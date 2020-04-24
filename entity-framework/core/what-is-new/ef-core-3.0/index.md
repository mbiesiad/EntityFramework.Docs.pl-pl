---
title: Nowe funkcje w Entity Framework Core 3,0 — EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: 2EBE2CCC-E52D-483F-834C-8877F5EB0C0C
uid: core/what-is-new/ef-core-3.0/index
ms.openlocfilehash: 39684cdcc17e3baa4b77cf29d54b626294771332
ms.sourcegitcommit: 387cbd8109c0fc5ce6bdc85d0dec1aed72ad4c33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82103142"
---
# <a name="new-features-in-entity-framework-core-30"></a>Nowe funkcje w Entity Framework Core 3,0

Poniższa lista zawiera najważniejsze nowe funkcje w EF Core 3,0.

W wersji głównej EF Core 3,0 również zawiera kilka znaczących [zmian](xref:core/what-is-new/ef-core-3.0/breaking-changes), które są ULEPSZENIAMI interfejsu API, które mogą mieć negatywny wpływ na istniejące aplikacje.  

## <a name="linq-overhaul"></a>Remonty LINQ

LINQ umożliwia pisanie zapytań bazy danych przy użyciu wybranego języka .NET, wykorzystując informacje o typie rozbudowanym do oferowania funkcji IntelliSense i sprawdzania typu w czasie kompilacji.
Jednak LINQ pozwala także pisać nieograniczoną liczbę skomplikowanych kwerend zawierających dowolne wyrażenia (wywołania metod lub operacje).
Jak obsługiwać wszystkie te kombinacje, jest głównym wyzwaniem dla dostawców LINQ.

W EF Core 3,0 został ponownie opracowany przez nas dostawca LINQ, aby umożliwić tłumaczenie większej liczby wzorców zapytań na SQL, generowanie wydajnych zapytań w większej liczbie przypadków i zapobieganie wykryciu nieefektywnych zapytań. Nowy dostawca LINQ jest podstawą, w której będziemy mogli oferować nowe możliwości zapytania i ulepszenia wydajności w przyszłych wersjach, bez przerywania istniejących aplikacji i dostawców danych.

### <a name="restricted-client-evaluation"></a>Obliczenia klientów z ograniczeniami

Najważniejszym zmianą projektu jest to, jak obsługujemy wyrażenia LINQ, które nie mogą być konwertowane na parametry lub tłumaczone na SQL.

W poprzednich wersjach EF Core zidentyfikować, jakie fragmenty zapytania można przetłumaczyć na SQL i wykonać pozostałą część zapytania na kliencie.
Ten typ wykonywania po stronie klienta jest pożądany w niektórych sytuacjach, ale w wielu innych przypadkach może to spowodować niewydajne zapytania.

Na przykład jeśli EF Core 2,2 nie może przetłumaczyć predykatu `Where()` w wywołaniu, wykonał instrukcję SQL bez filtru, przesłał wszystkie wiersze z bazy danych, a następnie przefiltrowanych je w pamięci:

``` csharp
var specialCustomers = context.Customers
    .Where(c => c.Name.StartsWith(n) && IsSpecialCustomer(c));
```

To może być akceptowalne, jeśli baza danych zawiera niewielką liczbę wierszy, ale może powodować znaczne problemy z wydajnością lub nawet niepowodzenie aplikacji, jeśli baza danych zawiera dużą liczbę wierszy.

W EF Core 3,0 ograniczenie oceny klienta ma miejsce tylko w projekcji najwyższego poziomu (zasadniczo jest to ostatnie wywołanie do `Select()`).
Gdy EF Core 3,0 wykrywa wyrażenia, które nie mogą być przetłumaczone w innym miejscu zapytania, zgłasza wyjątek czasu wykonania.

Aby ocenić warunek predykatu na kliencie, jak w poprzednim przykładzie, deweloperzy muszą jawnie przełączać ocenę zapytania do LINQ to Objects:

``` csharp
var specialCustomers = context.Customers
    .Where(c => c.Name.StartsWith(n))
    .AsEnumerable() // switches to LINQ to Objects
    .Where(c => IsSpecialCustomer(c));
```

Zapoznaj się z dokumentacją dotyczącą istotnych [zmian](xref:core/what-is-new/ef-core-3.0/breaking-changes#linq-queries-are-no-longer-evaluated-on-the-client) , aby uzyskać więcej informacji o tym, jak może to wpływać na istniejące aplikacje.

### <a name="single-sql-statement-per-linq-query"></a>Pojedyncza instrukcja SQL na zapytanie LINQ

Innym aspektem projektu, który został znacząco zmieniony w 3,0, jest zawsze generowanie pojedynczej instrukcji SQL na zapytanie LINQ. W poprzednich wersjach użyto do wygenerowania w niektórych przypadkach wielu instrukcji SQL, przetłumaczonych `Include()` wywołań właściwości nawigacji kolekcji i przetłumaczonych zapytań, które miały pewne wzorce z podzapytaniami. Mimo że było to w niektórych przypadkach wygodne, a `Include()` w przypadku, gdy nawet pomaga uniknąć wysyłania nadmiarowych danych przez sieć, implementacja była złożona i powodowała pewne niezwykle niewydajne zachowania (zapytania o N + 1). Istniały sytuacje, w których dane zwrócone przez wiele zapytań były potencjalnie niespójne.

Podobnie jak w przypadku oceny klienta, jeśli EF Core 3,0 nie można przetłumaczyć zapytania LINQ na pojedynczą instrukcję SQL, zgłasza wyjątek czasu wykonania. Ale wprowadziliśmy EF Core możliwości tłumaczenia wielu wspólnych wzorców, które były używane do generowania wielu zapytań do pojedynczego zapytania z sprzężeniami.

## <a name="cosmos-db-support"></a>Obsługa Cosmos DB

Dostawca Cosmos DB dla EF Core umożliwia deweloperom znającym model programu EF programowanie, aby łatwo kierować Azure Cosmos DB jako bazę danych aplikacji. Celem jest, aby niektóre zalety Cosmos DB, takich jak dystrybucja globalna, "zawsze włączone" dostępność, elastyczna skalowalność i małe opóźnienia, jeszcze bardziej dostępne dla deweloperów platformy .NET. Dostawca włącza większość funkcji EF Core, takich jak automatyczne śledzenie zmian, LINQ i konwersje wartości, względem interfejsu API SQL w programie Cosmos DB.

Aby uzyskać więcej informacji, zobacz [dokumentację dostawcy Cosmos DB](xref:core/providers/cosmos/index) .

## <a name="c-80-support"></a>Obsługa języka C# 8,0

EF Core 3,0 wykorzystuje kilka [nowych funkcji w języku C# 8,0](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-8):

### <a name="asynchronous-streams"></a>Strumienie asynchroniczne

Asynchroniczne wyniki zapytania są teraz udostępniane przy użyciu nowego interfejsu `IAsyncEnumerable<T>` standardowego i mogą być używane przy `await foreach`użyciu programu.

``` csharp
var orders =
    from o in context.Orders
    where o.Status == OrderStatus.Pending
    select o;

await foreach(var o in orders.AsAsyncEnumerable())
{
    Process(o);
}
```

Aby uzyskać więcej informacji, zobacz [strumienie asynchroniczne w dokumentacji języka C#](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-8#asynchronous-streams) .

### <a name="nullable-reference-types"></a>Typy referencyjne dopuszczające wartość null

Gdy ta nowa funkcja jest włączona w kodzie, EF Core bada wartości null właściwości typu odwołania i stosuje je do odpowiednich kolumn i relacji w bazie danych: właściwości typów odwołań niedopuszczających wartości null są traktowane tak, jakby miały atrybut adnotacji `[Required]` danych.

Na przykład w poniższej klasie właściwości oznaczone jako typu `string?` zostaną skonfigurowane jako opcjonalne, a `string` program zostanie skonfigurowany zgodnie z wymaganiami:

``` csharp
public class Customer
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string? MiddleName { get; set; }
}
```

Aby uzyskać więcej informacji, zobacz [Praca z typami odwołań dopuszczającymi wartość null](xref:core/miscellaneous/nullable-reference-types) w dokumentacji EF Core.

## <a name="interception-of-database-operations"></a>Przechwycenie operacji bazy danych

Nowy interfejs API przechwycenia w EF Core 3,0 umożliwia automatyczne wywoływanie logiki niestandardowej za każdym razem, gdy w normalnej operacji EF Core wystąpią operacje bazy danych niskiego poziomu. Na przykład podczas otwierania połączeń, zatwierdzania transakcji lub wykonywania poleceń.

Podobnie jak w przypadku funkcji przechwycenia, które istniały w EF 6, Interceptory umożliwiają przechwycenie operacji przed lub po ich wystąpieniu. Gdy przechwytuje je przed ich przystąpieniem, można wykonywać wykonywanie i dostarczać alternatywne wyniki z logiki przechwycenia.

Na przykład, aby manipulować tekstem poleceń, można utworzyć `DbCommandInterceptor`:

``` csharp
public class HintCommandInterceptor : DbCommandInterceptor
{
    public override InterceptionResult<DbDataReader> ReaderExecuting(
        DbCommand command,
        CommandEventData eventData,
        InterceptionResult<DbDataReader> result)
    {
        // Manipulate the command text, etc. here...
        command.CommandText += " OPTION (OPTIMIZE FOR UNKNOWN)";
        return result;
    }
}
```

I zarejestruj go w `DbContext`:

``` csharp
services.AddDbContext(b => b
    .UseSqlServer(connectionString)
    .AddInterceptors(new HintCommandInterceptor()));
```

## <a name="reverse-engineering-of-database-views"></a>Odtwarzanie widoków bazy danych

Typy zapytań, które reprezentują dane, które mogą zostać odczytane z bazy danych, ale nie zostały zaktualizowane, zostały zmienione pod kątem [typów jednostek](xref:core/modeling/keyless-entity-types).
Ponieważ zapewniają one doskonałą obsługę mapowania widoków bazy danych w większości scenariuszy, EF Core teraz automatycznie tworzy typy jednostek bez użycia w przypadku odtwarzania widoków bazy danych.

Na przykład przy użyciu [narzędzia wiersza polecenia dotnet EF](xref:core/miscellaneous/cli/dotnet) można wpisać:

```dotnetcli
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

Narzędzie będzie teraz automatycznie szkieletować typy dla widoków i tabel bez kluczy:

``` csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Names>(entity =>
    {
        entity.HasNoKey();
        entity.ToView("Names");
    });

    modelBuilder.Entity<Things>(entity =>
    {
        entity.HasNoKey();
    });
}
```

## <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a>Jednostki zależne współużytkujące tabelę z podmiotem zabezpieczeń są teraz opcjonalne

Począwszy od EF Core 3,0, jeśli `OrderDetails` jest własnością `Order` lub jawnie zmapowana do tej samej tabeli, będzie możliwe dodanie `Order` bez `OrderDetails` i wszystkich `OrderDetails` właściwości, z wyjątkiem tego, że klucz podstawowy zostanie zmapowany na kolumny dopuszczające wartość null.

Podczas wykonywania zapytania, EF Core zostanie ustawiona `OrderDetails` na `null` , Jeśli którakolwiek z wymaganych właściwości nie ma wartości lub jeśli nie ma żadnych wymaganych właściwości poza kluczem podstawowym i wszystkie właściwości są `null`.

``` csharp
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public OrderDetails Details { get; set; }
}

[Owned]
public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}
```

## <a name="ef-63-on-net-core"></a>Dr 6,3 na platformie .NET Core

To nie jest w rzeczywistości funkcją EF Core 3,0, ale uważamy, że jest ona ważna dla naszych bieżących klientów.

Firma Microsoft zdaje sobie sprawę, że w wielu istniejących aplikacjach są używane poprzednie wersje EF, a ich przenoszenie do EF Core tylko w celu wykorzystania platformy .NET Core może wymagać znacznego wysiłku.
Z tego powodu postanowiono przenieść najnowszą wersję programu EF 6 do uruchamiania na platformie .NET Core 3,0.

Aby uzyskać więcej informacji, zobacz [co nowego w programie EF 6](xref:ef6/what-is-new/index).

## <a name="postponed-features"></a>Funkcje odroczone

Niektóre funkcje początkowo planowane dla EF Core 3,0 zostały odroczone do przyszłych wersji:

- Możliwość ignorowania części modelu w migracjach, śledzonych jako [#2725](https://github.com/aspnet/EntityFrameworkCore/issues/2725).
- Jednostki zbioru właściwości, śledzone jako dwa oddzielne problemy: [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914) informacji o jednostkach typu udostępnionego i [#13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610) o obsłudze mapowania właściwości indeksowanych.
