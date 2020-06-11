---
title: Filtry zapytania globalnego — EF Core
author: anpete
ms.date: 11/03/2017
uid: core/querying/filters
ms.openlocfilehash: f6c59bcbab31edcbed22079a1320c060ce08c6f7
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664133"
---
# <a name="global-query-filters"></a>Filtry zapytań globalnych

> [!NOTE]
> Ta funkcja została wprowadzona w EF Core 2,0.

Globalne filtry zapytań to predykaty zapytań LINQ (wyrażenie logiczne zwykle przenoszone do składnika LINQ *WHERE* operator zapytań) zastosowane do typów jednostek w modelu metadanych (zwykle w *OnModelCreating*). Takie filtry są automatycznie stosowane do dowolnych zapytań LINQ obejmujących te typy jednostek, w tym typy jednostek, do których odwołuje się pośrednio, na przykład przy użyciu odwołań do właściwości dołączania lub nawigacji bezpośredniej. Niektóre typowe aplikacje tej funkcji to:

* **Usuwanie nietrwałe** — typ jednostki definiuje Właściwość *IsDeleted* .
* **Wielodostępność** — typ jednostki definiuje Właściwość *TenantId* .

## <a name="example"></a>Przykład

Poniższy przykład pokazuje, jak używać globalnych filtrów zapytań do implementacji nietrwałego i wielodostępnych zachowań zapytań w prostym modelu blogów.

> [!TIP]
> Możesz wyświetlić przykład z [wielodostępną](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters) i [przykłady przy użyciu nawigacji](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFiltersNavigations) w witrynie GitHub. 

Najpierw Zdefiniuj jednostki:

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Entities)]

Zanotuj deklarację pola _tenantId_ w jednostce _blogu_ . Ta wartość zostanie użyta do skojarzenia każdego wystąpienia blogu z konkretną dzierżawą. Zdefiniowana również jest właściwością _IsDeleted_ dla typu jednostki _post_ . Służy do śledzenia, czy wystąpienie _post_ zostało usunięte z nietrwałego usuwania. Oznacza to, że wystąpienie jest oznaczone jako usunięte bez fizycznego usunięcia danych źródłowych.

Następnie skonfiguruj filtry zapytania w _OnModelCreating_ przy użyciu `HasQueryFilter` interfejsu API.

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Configuration)]

Wyrażenia predykatu przesłane do wywołań _HasQueryFilter_ będą teraz automatycznie stosowane do wszystkich zapytań LINQ dla tych typów.

> [!TIP]
> Zwróć uwagę na użycie pola poziomu wystąpienia DbContext: `_tenantId` służy do ustawiania bieżącej dzierżawy. Filtry na poziomie modelu będą używać wartości z poprawnego wystąpienia kontekstu (czyli wystąpienia, które wykonuje zapytanie).

> [!NOTE]
> Obecnie nie jest możliwe zdefiniowanie wielu filtrów zapytania w tej samej jednostce — zostanie zastosowana tylko Ostatnia z nich. Można jednak zdefiniować pojedynczy filtr z wieloma warunkami przy użyciu operatora logicznego _and_ ([ `&&` w języku C#](https://docs.microsoft.com/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)).

## <a name="use-of-navigations"></a>Korzystanie z nawigacji

Podczas definiowania filtrów zapytania globalnego można używać nawigacji. Są one stosowane cyklicznie — gdy nawigowanie w filtrach zapytań jest tłumaczone, filtry zapytań zdefiniowane na jednostkach, do których istnieją odwołania, są również stosowane, potencjalnie dodając więcej nawigacji.

> [!NOTE]
> Obecnie EF Core nie wykrywa cykli w definicjach filtrów globalnych, dlatego należy zachować ostrożność podczas definiowania ich. Jeśli jest określony nieprawidłowo, może to spowodować nieskończone pętle podczas translacji zapytań.

## <a name="accessing-entity-with-query-filter-using-reqiured-navigation"></a>Uzyskiwanie dostępu do jednostki z filtrem zapytania przy użyciu nawigacji reqiured

> [!CAUTION]
> Użycie wymaganej nawigacji do jednostki, która ma zdefiniowany filtr zapytania globalnego może prowadzić do nieoczekiwanych wyników. 

Wymagana Nawigacja oczekuje, że jednostka pokrewna będzie zawsze obecna. Jeśli wymagana powiązana jednostka jest odfiltrowana przez filtr zapytania, Jednostka nadrzędna może zakończyć się w nieoczekiwanym stanie. Może to spowodować zwrócenie mniejszej liczby elementów niż oczekiwano. 

Aby zilustrować ten problem, można użyć `Blog` `Post` określonych powyżej jednostek i następującej metody _OnModelCreating_ :

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

Model można rozmieścić przy użyciu następujących danych:

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#SeedData)]

Problem można zaobserwować podczas wykonywania dwóch zapytań:

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#Queries)]

W przypadku tej konfiguracji pierwsze zapytanie zwraca wszystkie 6 `Post` s, ale drugie zapytanie zwraca tylko wartość 3. Dzieje się tak, ponieważ metoda _include_ w drugim zapytaniu ładuje powiązane `Blog` jednostki. Ponieważ nawigacja między `Blog` i `Post` jest wymagana, EF Core używa `INNER JOIN` podczas konstruowania zapytania:

```SQL
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[IsDeleted], [p].[Title], [t].[BlogId], [t].[Name], [t].[Url]
FROM [Post] AS [p]
INNER JOIN (
    SELECT [b].[BlogId], [b].[Name], [b].[Url]
    FROM [Blogs] AS [b]
    WHERE CHARINDEX(N'fish', [b].[Url]) > 0
) AS [t] ON [p].[BlogId] = [t].[BlogId]
```

Użycie `INNER JOIN` filtrów wszystkie `Post` s, których powiązane `Blog` elementy s zostały usunięte przez filtr zapytania globalnego. 

Można ją rozwiązać przy użyciu opcjonalnej nawigacji zamiast jest wymagane. W ten sposób pierwsze zapytanie pozostaje tak samo jak poprzednio, ale drugie zapytanie będzie teraz generować `LEFT JOIN` i zwracać 6 wyników.

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired(false);
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

Alternatywna metoda polega na określeniu spójnych filtrów zarówno dla `Blog` obiektów, jak i `Post` .
W ten sposób dopasowania filtrów są stosowane do obu `Blog` i `Post` . `Post`elementy, które mogą zakończyć się w nieoczekiwanym stanie, są usuwane, a oba zapytania zwracają 3 wyniki. 

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
    modelBuilder.Entity<Post>().HasQueryFilter(p => p.Blog.Url.Contains("fish"));
}
```

## <a name="disabling-filters"></a>Wyłączanie filtrów

Filtry mogą być wyłączone dla poszczególnych zapytań LINQ przy użyciu `IgnoreQueryFilters()` operatora.

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a>Ograniczenia

Globalne filtry zapytań mają następujące ograniczenia:

* Filtry można definiować tylko dla typu jednostki głównej w hierarchii dziedziczenia.
