---
title: Filtry zapytania globalnego — EF Core
author: anpete
ms.date: 11/03/2017
uid: core/querying/filters
ms.openlocfilehash: 233289b0c50e15f4555b342bc654211ce04c24d3
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370464"
---
# <a name="global-query-filters"></a><span data-ttu-id="94c45-102">Filtry zapytań globalnych</span><span class="sxs-lookup"><span data-stu-id="94c45-102">Global Query Filters</span></span>

> [!NOTE]
> <span data-ttu-id="94c45-103">Ta funkcja została wprowadzona w EF Core 2,0.</span><span class="sxs-lookup"><span data-stu-id="94c45-103">This feature was introduced in EF Core 2.0.</span></span>

<span data-ttu-id="94c45-104">Globalne filtry zapytań to predykaty zapytań LINQ (wyrażenie logiczne zwykle przenoszone do składnika LINQ *WHERE* operator zapytań) zastosowane do typów jednostek w modelu metadanych (zwykle w *OnModelCreating*).</span><span class="sxs-lookup"><span data-stu-id="94c45-104">Global query filters are LINQ query predicates (a boolean expression typically passed to the LINQ *Where* query operator) applied to Entity Types in the metadata model (usually in *OnModelCreating*).</span></span> <span data-ttu-id="94c45-105">Takie filtry są automatycznie stosowane do dowolnych zapytań LINQ obejmujących te typy jednostek, w tym typy jednostek, do których odwołuje się pośrednio, na przykład przy użyciu odwołań do właściwości dołączania lub nawigacji bezpośredniej.</span><span class="sxs-lookup"><span data-stu-id="94c45-105">Such filters are automatically applied to any LINQ queries involving those Entity Types, including Entity Types referenced indirectly, such as through the use of Include or direct navigation property references.</span></span> <span data-ttu-id="94c45-106">Niektóre typowe aplikacje tej funkcji to:</span><span class="sxs-lookup"><span data-stu-id="94c45-106">Some common applications of this feature are:</span></span>

* <span data-ttu-id="94c45-107">**Usuwanie nietrwałe** — typ jednostki definiuje Właściwość *IsDeleted* .</span><span class="sxs-lookup"><span data-stu-id="94c45-107">**Soft delete** - An Entity Type defines an *IsDeleted* property.</span></span>
* <span data-ttu-id="94c45-108">**Wielodostępność** — typ jednostki definiuje Właściwość *TenantId* .</span><span class="sxs-lookup"><span data-stu-id="94c45-108">**Multi-tenancy** - An Entity Type defines a *TenantId* property.</span></span>

## <a name="example"></a><span data-ttu-id="94c45-109">Przykład</span><span class="sxs-lookup"><span data-stu-id="94c45-109">Example</span></span>

<span data-ttu-id="94c45-110">Poniższy przykład pokazuje, jak używać globalnych filtrów zapytań do implementacji nietrwałego i wielodostępnych zachowań zapytań w prostym modelu blogów.</span><span class="sxs-lookup"><span data-stu-id="94c45-110">The following example shows how to use Global Query Filters to implement soft-delete and multi-tenancy query behaviors in a simple blogging model.</span></span>

> [!TIP]
> <span data-ttu-id="94c45-111">Możesz wyświetlić przykład z [wielodostępną](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters) i [przykłady przy użyciu nawigacji](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFiltersNavigations) w witrynie GitHub.</span><span class="sxs-lookup"><span data-stu-id="94c45-111">You can view a [multi-tenancy sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters) and [samples using navigations](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFiltersNavigations) on GitHub.</span></span> 

<span data-ttu-id="94c45-112">Najpierw Zdefiniuj jednostki:</span><span class="sxs-lookup"><span data-stu-id="94c45-112">First, define the entities:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Entities)]

<span data-ttu-id="94c45-113">Zanotuj deklarację pola _tenantId_ w jednostce _blogu_ .</span><span class="sxs-lookup"><span data-stu-id="94c45-113">Note the declaration of a _tenantId_ field on the _Blog_ entity.</span></span> <span data-ttu-id="94c45-114">Ta wartość zostanie użyta do skojarzenia każdego wystąpienia blogu z konkretną dzierżawą.</span><span class="sxs-lookup"><span data-stu-id="94c45-114">This will be used to associate each Blog instance with a specific tenant.</span></span> <span data-ttu-id="94c45-115">Zdefiniowana również jest właściwością _IsDeleted_ dla typu jednostki _post_ .</span><span class="sxs-lookup"><span data-stu-id="94c45-115">Also defined is an _IsDeleted_ property on the _Post_ entity type.</span></span> <span data-ttu-id="94c45-116">Służy do śledzenia, czy wystąpienie _post_ zostało usunięte z nietrwałego usuwania.</span><span class="sxs-lookup"><span data-stu-id="94c45-116">This is used to keep track of whether a _Post_ instance has been "soft-deleted".</span></span> <span data-ttu-id="94c45-117">Oznacza to, że wystąpienie jest oznaczone jako usunięte bez fizycznego usunięcia danych źródłowych.</span><span class="sxs-lookup"><span data-stu-id="94c45-117">That is, the instance is marked as deleted without physically removing the underlying data.</span></span>

<span data-ttu-id="94c45-118">Następnie skonfiguruj filtry zapytania w _OnModelCreating_ przy użyciu `HasQueryFilter` interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="94c45-118">Next, configure the query filters in _OnModelCreating_ using the `HasQueryFilter` API.</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Configuration)]

<span data-ttu-id="94c45-119">Wyrażenia predykatu przesłane do wywołań _HasQueryFilter_ będą teraz automatycznie stosowane do wszystkich zapytań LINQ dla tych typów.</span><span class="sxs-lookup"><span data-stu-id="94c45-119">The predicate expressions passed to the _HasQueryFilter_ calls will now automatically be applied to any LINQ queries for those types.</span></span>

> [!TIP]
> <span data-ttu-id="94c45-120">Zwróć uwagę na użycie pola poziomu wystąpienia DbContext: `_tenantId` służy do ustawiania bieżącej dzierżawy.</span><span class="sxs-lookup"><span data-stu-id="94c45-120">Note the use of a DbContext instance level field: `_tenantId` used to set the current tenant.</span></span> <span data-ttu-id="94c45-121">Filtry na poziomie modelu będą używać wartości z poprawnego wystąpienia kontekstu (czyli wystąpienia, które wykonuje zapytanie).</span><span class="sxs-lookup"><span data-stu-id="94c45-121">Model-level filters will use the value from the correct context instance (that is, the instance that is executing the query).</span></span>

> [!NOTE]
> <span data-ttu-id="94c45-122">Obecnie nie jest możliwe zdefiniowanie wielu filtrów zapytania w tej samej jednostce — zostanie zastosowana tylko Ostatnia z nich.</span><span class="sxs-lookup"><span data-stu-id="94c45-122">It is currently not possible to define multiple query filters on the same entity - only the last one will be applied.</span></span> <span data-ttu-id="94c45-123">Można jednak zdefiniować pojedynczy filtr z wieloma warunkami przy użyciu operatora logicznego _and_ ([ `&&` w języku C#](https://docs.microsoft.com/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)).</span><span class="sxs-lookup"><span data-stu-id="94c45-123">However, you can define a single filter with multiple conditions using the logical _AND_ operator ([`&&` in C#](https://docs.microsoft.com/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)).</span></span>

## <a name="use-of-navigations"></a><span data-ttu-id="94c45-124">Korzystanie z nawigacji</span><span class="sxs-lookup"><span data-stu-id="94c45-124">Use of navigations</span></span>

<span data-ttu-id="94c45-125">Podczas definiowania filtrów zapytania globalnego można używać nawigacji.</span><span class="sxs-lookup"><span data-stu-id="94c45-125">Navigations can be used when defining global query filters.</span></span> <span data-ttu-id="94c45-126">Są one stosowane cyklicznie — gdy nawigowanie w filtrach zapytań jest tłumaczone, filtry zapytań zdefiniowane na jednostkach, do których istnieją odwołania, są również stosowane, potencjalnie dodając więcej nawigacji.</span><span class="sxs-lookup"><span data-stu-id="94c45-126">They are applied recursively - when navigations used in query filters are translated, query filters defined on referenced entities are also applied, potentially adding more navigations.</span></span>

> [!NOTE]
> <span data-ttu-id="94c45-127">Obecnie EF Core nie wykrywa cykli w definicjach filtrów globalnych, dlatego należy zachować ostrożność podczas definiowania ich.</span><span class="sxs-lookup"><span data-stu-id="94c45-127">Currently EF Core does not detect cycles in global query filter definitions, so you should be careful when defining them.</span></span> <span data-ttu-id="94c45-128">Jeśli jest określony nieprawidłowo, może to spowodować nieskończone pętle podczas translacji zapytań.</span><span class="sxs-lookup"><span data-stu-id="94c45-128">If specified incorrectly, this could lead to infinite loops during query translation.</span></span>

## <a name="accessing-entity-with-query-filter-using-required-navigation"></a><span data-ttu-id="94c45-129">Uzyskiwanie dostępu do jednostki z filtrem zapytania przy użyciu wymaganej nawigacji</span><span class="sxs-lookup"><span data-stu-id="94c45-129">Accessing entity with query filter using required navigation</span></span>

> [!CAUTION]
> <span data-ttu-id="94c45-130">Użycie wymaganej nawigacji do jednostki, która ma zdefiniowany filtr zapytania globalnego może prowadzić do nieoczekiwanych wyników.</span><span class="sxs-lookup"><span data-stu-id="94c45-130">Using required navigation to access entity which has global query filter defined may lead to unexpected results.</span></span> 

<span data-ttu-id="94c45-131">Wymagana Nawigacja oczekuje, że jednostka pokrewna będzie zawsze obecna.</span><span class="sxs-lookup"><span data-stu-id="94c45-131">Required navigation expects the related entity to always be present.</span></span> <span data-ttu-id="94c45-132">Jeśli wymagana powiązana jednostka jest odfiltrowana przez filtr zapytania, Jednostka nadrzędna może zakończyć się w nieoczekiwanym stanie.</span><span class="sxs-lookup"><span data-stu-id="94c45-132">If required related entity is filtered out by the query filter, the parent entity could end up in unexpected state.</span></span> <span data-ttu-id="94c45-133">Może to spowodować zwrócenie mniejszej liczby elementów niż oczekiwano.</span><span class="sxs-lookup"><span data-stu-id="94c45-133">This may result in returning fewer elements than expected.</span></span> 

<span data-ttu-id="94c45-134">Aby zilustrować ten problem, można użyć `Blog` `Post` określonych powyżej jednostek i następującej metody _OnModelCreating_ :</span><span class="sxs-lookup"><span data-stu-id="94c45-134">To illustrate the problem, we can use the `Blog` and `Post` entities specified above and the following _OnModelCreating_ method:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

<span data-ttu-id="94c45-135">Model można rozmieścić przy użyciu następujących danych:</span><span class="sxs-lookup"><span data-stu-id="94c45-135">The model can be seeded with the following data:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#SeedData)]

<span data-ttu-id="94c45-136">Problem można zaobserwować podczas wykonywania dwóch zapytań:</span><span class="sxs-lookup"><span data-stu-id="94c45-136">The problem can be observed when executing two queries:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#Queries)]

<span data-ttu-id="94c45-137">W przypadku tej konfiguracji pierwsze zapytanie zwraca wszystkie 6 `Post` s, ale drugie zapytanie zwraca tylko wartość 3.</span><span class="sxs-lookup"><span data-stu-id="94c45-137">With this setup, the first query returns all 6 `Post`s, however the second query only returns 3.</span></span> <span data-ttu-id="94c45-138">Dzieje się tak, ponieważ metoda _include_ w drugim zapytaniu ładuje powiązane `Blog` jednostki.</span><span class="sxs-lookup"><span data-stu-id="94c45-138">This happens because _Include_ method in the second query loads the related `Blog` entities.</span></span> <span data-ttu-id="94c45-139">Ponieważ nawigacja między `Blog` i `Post` jest wymagana, EF Core używa `INNER JOIN` podczas konstruowania zapytania:</span><span class="sxs-lookup"><span data-stu-id="94c45-139">Since the navigation between `Blog` and `Post` is required, EF Core uses `INNER JOIN` when constructing the query:</span></span>

```SQL
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[IsDeleted], [p].[Title], [t].[BlogId], [t].[Name], [t].[Url]
FROM [Post] AS [p]
INNER JOIN (
    SELECT [b].[BlogId], [b].[Name], [b].[Url]
    FROM [Blogs] AS [b]
    WHERE CHARINDEX(N'fish', [b].[Url]) > 0
) AS [t] ON [p].[BlogId] = [t].[BlogId]
```

<span data-ttu-id="94c45-140">Użycie `INNER JOIN` filtrów wszystkie `Post` s, których powiązane `Blog` elementy s zostały usunięte przez filtr zapytania globalnego.</span><span class="sxs-lookup"><span data-stu-id="94c45-140">Use of the `INNER JOIN` filters out all `Post`s whose related `Blog`s have been removed by a global query filter.</span></span> 

<span data-ttu-id="94c45-141">Można ją rozwiązać przy użyciu opcjonalnej nawigacji zamiast jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="94c45-141">It can be addressed by using optional navigation instead of required.</span></span> <span data-ttu-id="94c45-142">W ten sposób pierwsze zapytanie pozostaje tak samo jak poprzednio, ale drugie zapytanie będzie teraz generować `LEFT JOIN` i zwracać 6 wyników.</span><span class="sxs-lookup"><span data-stu-id="94c45-142">This way the first query stays the same as before, however the second query will now generate `LEFT JOIN` and return 6 results.</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired(false);
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

<span data-ttu-id="94c45-143">Alternatywna metoda polega na określeniu spójnych filtrów zarówno dla `Blog` obiektów, jak i `Post` .</span><span class="sxs-lookup"><span data-stu-id="94c45-143">Alternative approach is to specify consistent filters on both `Blog` and `Post` entities.</span></span>
<span data-ttu-id="94c45-144">W ten sposób dopasowania filtrów są stosowane do obu `Blog` i `Post` .</span><span class="sxs-lookup"><span data-stu-id="94c45-144">This way matching filters are applied to both `Blog` and `Post`.</span></span> <span data-ttu-id="94c45-145">`Post`elementy, które mogą zakończyć się w nieoczekiwanym stanie, są usuwane, a oba zapytania zwracają 3 wyniki.</span><span class="sxs-lookup"><span data-stu-id="94c45-145">`Post`s that could end up in unexpected state are removed and both queries return 3 results.</span></span> 

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
    modelBuilder.Entity<Post>().HasQueryFilter(p => p.Blog.Url.Contains("fish"));
}
```

## <a name="disabling-filters"></a><span data-ttu-id="94c45-146">Wyłączanie filtrów</span><span class="sxs-lookup"><span data-stu-id="94c45-146">Disabling Filters</span></span>

<span data-ttu-id="94c45-147">Filtry mogą być wyłączone dla poszczególnych zapytań LINQ przy użyciu `IgnoreQueryFilters()` operatora.</span><span class="sxs-lookup"><span data-stu-id="94c45-147">Filters may be disabled for individual LINQ queries by using the `IgnoreQueryFilters()` operator.</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a><span data-ttu-id="94c45-148">Ograniczenia</span><span class="sxs-lookup"><span data-stu-id="94c45-148">Limitations</span></span>

<span data-ttu-id="94c45-149">Globalne filtry zapytań mają następujące ograniczenia:</span><span class="sxs-lookup"><span data-stu-id="94c45-149">Global query filters have the following limitations:</span></span>

* <span data-ttu-id="94c45-150">Filtry można definiować tylko dla typu jednostki głównej w hierarchii dziedziczenia.</span><span class="sxs-lookup"><span data-stu-id="94c45-150">Filters can only be defined for the root Entity Type of an inheritance hierarchy.</span></span>
