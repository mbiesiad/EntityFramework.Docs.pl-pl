---
title: Nowe funkcje w Entity Framework Core 3,0 — EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: 2EBE2CCC-E52D-483F-834C-8877F5EB0C0C
uid: core/what-is-new/ef-core-3.0/index
ms.openlocfilehash: ccfb8259c70cf8706a06eb3b22b9541224c3b9bb
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182075"
---
# <a name="new-features-in-entity-framework-core-30"></a><span data-ttu-id="54875-102">Nowe funkcje w Entity Framework Core 3,0</span><span class="sxs-lookup"><span data-stu-id="54875-102">New features in Entity Framework Core 3.0</span></span>

<span data-ttu-id="54875-103">Poniższa lista zawiera najważniejsze nowe funkcje w EF Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="54875-103">The following list includes the major new features in EF Core 3.0.</span></span>

<span data-ttu-id="54875-104">W wersji głównej EF Core 3,0 również zawiera kilka znaczących [zmian](xref:core/what-is-new/ef-core-3.0/breaking-changes), które są ULEPSZENIAMI interfejsu API, które mogą mieć negatywny wpływ na istniejące aplikacje.</span><span class="sxs-lookup"><span data-stu-id="54875-104">As a major release, EF Core 3.0 also contains several [breaking changes](xref:core/what-is-new/ef-core-3.0/breaking-changes), which are API improvements that may have negative impact on existing applications.</span></span>  

## <a name="linq-overhaul"></a><span data-ttu-id="54875-105">Remonty LINQ</span><span class="sxs-lookup"><span data-stu-id="54875-105">LINQ overhaul</span></span> 

<span data-ttu-id="54875-106">LINQ umożliwia pisanie zapytań bazy danych przy użyciu wybranego języka .NET, wykorzystując informacje o typie rozbudowanym do oferowania funkcji IntelliSense i sprawdzania typu w czasie kompilacji.</span><span class="sxs-lookup"><span data-stu-id="54875-106">LINQ enables you to write database queries using your .NET language of choice, taking advantage of rich type information to offer IntelliSense and compile-time type checking.</span></span>
<span data-ttu-id="54875-107">Jednak LINQ pozwala także pisać nieograniczoną liczbę skomplikowanych kwerend zawierających dowolne wyrażenia (wywołania metod lub operacje).</span><span class="sxs-lookup"><span data-stu-id="54875-107">But LINQ also enables you to write an unlimited number of complicated queries containing arbitrary expressions (method calls or operations).</span></span>
<span data-ttu-id="54875-108">Jak obsługiwać wszystkie te kombinacje, jest głównym wyzwaniem dla dostawców LINQ.</span><span class="sxs-lookup"><span data-stu-id="54875-108">How to handle all those combinations is the main challenge for LINQ providers.</span></span>

<span data-ttu-id="54875-109">W EF Core 3,0 został ponownie opracowany przez nas dostawca LINQ, aby umożliwić tłumaczenie większej liczby wzorców zapytań na SQL, generowanie wydajnych zapytań w większej liczbie przypadków i zapobieganie wykryciu nieefektywnych zapytań.</span><span class="sxs-lookup"><span data-stu-id="54875-109">In EF Core 3.0, we rearchitected our LINQ provider to enable translating more query patterns into SQL, generating efficient queries in more cases, and preventing inefficient queries from going undetected.</span></span> <span data-ttu-id="54875-110">Nowy dostawca LINQ jest podstawą, w której będziemy mogli oferować nowe możliwości zapytania i ulepszenia wydajności w przyszłych wersjach, bez przerywania istniejących aplikacji i dostawców danych.</span><span class="sxs-lookup"><span data-stu-id="54875-110">The new LINQ provider is the foundation over which we'll be able to offer new query capabilities and performance improvements in future releases, without breaking existing applications and data providers.</span></span>

### <a name="restricted-client-evaluation"></a><span data-ttu-id="54875-111">Obliczenia klientów z ograniczeniami</span><span class="sxs-lookup"><span data-stu-id="54875-111">Restricted client evaluation</span></span>
<span data-ttu-id="54875-112">Najważniejszym zmianą projektu jest to, jak obsługujemy wyrażenia LINQ, które nie mogą być konwertowane na parametry lub tłumaczone na SQL.</span><span class="sxs-lookup"><span data-stu-id="54875-112">The most important design change has to do with how we handle LINQ expressions that cannot be converted to parameters or translated to SQL.</span></span>

<span data-ttu-id="54875-113">W poprzednich wersjach EF Core zidentyfikować, jakie fragmenty zapytania można przetłumaczyć na SQL i wykonać pozostałą część zapytania na kliencie.</span><span class="sxs-lookup"><span data-stu-id="54875-113">In previous versions, EF Core identified what portions of a query could be translated to SQL, and executed the rest of the query on the client.</span></span>
<span data-ttu-id="54875-114">Ten typ wykonywania po stronie klienta jest pożądany w niektórych sytuacjach, ale w wielu innych przypadkach może to spowodować niewydajne zapytania.</span><span class="sxs-lookup"><span data-stu-id="54875-114">This type of client-side execution is desirable in some situations, but in many other cases it can result in inefficient queries.</span></span>

<span data-ttu-id="54875-115">Na przykład jeśli EF Core 2,2 nie może przetłumaczyć predykatu w wywołaniu `Where()`, wykonał instrukcję SQL bez filtru, przeniesiono wszystkie wiersze z bazy danych, a następnie przefiltrowane je w pamięci:</span><span class="sxs-lookup"><span data-stu-id="54875-115">For example, if EF Core 2.2 couldn't translate a predicate in a `Where()` call, it executed an SQL statement without a filter, transferred all the rows from the database, and then filtered them in-memory:</span></span>

``` csharp
var specialCustomers = 
  context.Customers
    .Where(c => c.Name.StartsWith(n) && IsSpecialCustomer(c));
```

<span data-ttu-id="54875-116">To może być akceptowalne, jeśli baza danych zawiera niewielką liczbę wierszy, ale może powodować znaczne problemy z wydajnością lub nawet niepowodzenie aplikacji, jeśli baza danych zawiera dużą liczbę lub kilka wierszy.</span><span class="sxs-lookup"><span data-stu-id="54875-116">That may be acceptable if the database contains a small number of rows but can result in significant performance issues or even application failure if the database contains a large number or rows.</span></span>

<span data-ttu-id="54875-117">W EF Core 3,0 ograniczenie oceny klienta ma miejsce tylko w projekcji najwyższego poziomu (zasadniczo jest to ostatnie wywołanie do `Select()`).</span><span class="sxs-lookup"><span data-stu-id="54875-117">In EF Core 3.0, we've restricted client evaluation to only happen on the top-level projection (essentially, the last call to `Select()`).</span></span>
<span data-ttu-id="54875-118">Gdy EF Core 3,0 wykrywa wyrażenia, które nie mogą być przetłumaczone w innym miejscu zapytania, zgłasza wyjątek czasu wykonania.</span><span class="sxs-lookup"><span data-stu-id="54875-118">When EF Core 3.0 detects expressions that can't be translated anywhere else in the query, it throws a runtime exception.</span></span>

<span data-ttu-id="54875-119">Aby ocenić warunek predykatu na kliencie, jak w poprzednim przykładzie, deweloperzy muszą jawnie przełączać ocenę zapytania do LINQ to Objects:</span><span class="sxs-lookup"><span data-stu-id="54875-119">To evaluate a predicate condition on the client as in the previous example, developers now need to explicitly switch evaluation of the query to LINQ to Objects:</span></span> 

``` csharp
var specialCustomers =
  context.Customers
    .Where(c => c.Name.StartsWith(n)) 
    .AsEnumerable() // switches to LINQ to Objects
    .Where(c => IsSpecialCustomer(c));
```

<span data-ttu-id="54875-120">Zapoznaj się z dokumentacją dotyczącą istotnych [zmian](xref:core/what-is-new/ef-core-3.0/breaking-changes#linq-queries-are-no-longer-evaluated-on-the-client) , aby uzyskać więcej informacji o tym, jak może to wpływać na istniejące aplikacje.</span><span class="sxs-lookup"><span data-stu-id="54875-120">See the [breaking changes documentation](xref:core/what-is-new/ef-core-3.0/breaking-changes#linq-queries-are-no-longer-evaluated-on-the-client) for more details about how this can affect existing applications.</span></span>

### <a name="single-sql-statement-per-linq-query"></a><span data-ttu-id="54875-121">Pojedyncza instrukcja SQL na zapytanie LINQ</span><span class="sxs-lookup"><span data-stu-id="54875-121">Single SQL statement per LINQ query</span></span>

<span data-ttu-id="54875-122">Innym aspektem projektu, który został znacząco zmieniony w 3,0, jest zawsze generowanie pojedynczej instrukcji SQL na zapytanie LINQ.</span><span class="sxs-lookup"><span data-stu-id="54875-122">Another aspect of the design that changed significantly in 3.0 is that we now always generate a single SQL statement per LINQ query.</span></span> <span data-ttu-id="54875-123">W poprzednich wersjach użyto do wygenerowania wielu instrukcji SQL w niektórych przypadkach, takich jak translacja wywołań `Include()` na właściwościach nawigacji kolekcji i translacja zapytań, które miały pewne wzorce z podzapytaniami.</span><span class="sxs-lookup"><span data-stu-id="54875-123">In previous versions, we used to generate multiple SQL statements in certain cases, like to translate `Include()` calls on collection navigation properties and to translate queries that followed certain patterns with subqueries.</span></span> <span data-ttu-id="54875-124">Mimo że było to w niektórych przypadkach wygodne, a dla `Include()`, nawet aby uniknąć wysyłania nadmiarowych danych przez sieć, implementacja była złożona, co spowodowało pewne niezwykle niewydajne zachowania (liczba zapytań: N + 1), a także sytuacje, w których dane zwrócenie między wieloma zapytaniami może być niespójne.</span><span class="sxs-lookup"><span data-stu-id="54875-124">Although this was in some cases convenient, and for `Include()` it even helped avoid sending redundant data over the wire, the implementation was complex, it resulted in some extremely inefficient behaviors (N+1 queries), and there was situations in which the data returned across multiple queries could be inconsistent.</span></span>

<span data-ttu-id="54875-125">Podobnie jak w przypadku oceny klienta, jeśli EF Core 3,0 nie można przetłumaczyć zapytania LINQ na pojedynczą instrukcję SQL, zgłasza wyjątek czasu wykonania.</span><span class="sxs-lookup"><span data-stu-id="54875-125">Similarly to client evaluation, if EF Core 3.0 can't translate a LINQ query into a single SQL statement, it throws a runtime exception.</span></span> <span data-ttu-id="54875-126">Ale wprowadziliśmy EF Core możliwości tłumaczenia wielu wspólnych wzorców, które były używane do generowania wielu zapytań do pojedynczego zapytania z sprzężeniami.</span><span class="sxs-lookup"><span data-stu-id="54875-126">But we made EF Core capable of translating many of the common patterns that used to generate multiple queries to a single query with JOINs.</span></span>

## <a name="cosmos-db-support"></a><span data-ttu-id="54875-127">Obsługa Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="54875-127">Cosmos DB support</span></span> 

<span data-ttu-id="54875-128">Dostawca Cosmos DB dla EF Core umożliwia deweloperom znającym model programu EF programowanie, aby łatwo kierować Azure Cosmos DB jako bazę danych aplikacji.</span><span class="sxs-lookup"><span data-stu-id="54875-128">The Cosmos DB provider for EF Core enables developers familiar with the EF programing model to easily target Azure Cosmos DB as an application database.</span></span> <span data-ttu-id="54875-129">Celem jest, aby niektóre zalety Cosmos DB, takich jak dystrybucja globalna, "zawsze włączone" dostępność, elastyczna skalowalność i małe opóźnienia, jeszcze bardziej dostępne dla deweloperów platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="54875-129">The goal is to make some of the advantages of Cosmos DB, like global distribution, "always on" availability, elastic scalability, and low latency, even more accessible to .NET developers.</span></span> <span data-ttu-id="54875-130">Dostawca włącza większość funkcji EF Core, takich jak automatyczne śledzenie zmian, LINQ i konwersje wartości, względem interfejsu API SQL w programie Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="54875-130">The provider enables most EF Core features, like automatic change tracking, LINQ, and value conversions, against the SQL API in Cosmos DB.</span></span>

<span data-ttu-id="54875-131">Aby uzyskać więcej informacji, zobacz [dokumentację dostawcy Cosmos DB](xref:core/providers/cosmos/index) .</span><span class="sxs-lookup"><span data-stu-id="54875-131">See the [Cosmos DB provider documentation](xref:core/providers/cosmos/index) for more details.</span></span>

## <a name="c-80-support"></a><span data-ttu-id="54875-132">C#Obsługa 8,0</span><span class="sxs-lookup"><span data-stu-id="54875-132">C# 8.0 support</span></span>

<span data-ttu-id="54875-133">EF Core 3,0 wykorzystuje kilka [nowych funkcji w C# 8,0](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-8):</span><span class="sxs-lookup"><span data-stu-id="54875-133">EF Core 3.0 takes advantage of a couple of the [new features in C# 8.0](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-8):</span></span>

### <a name="asynchronous-streams"></a><span data-ttu-id="54875-134">Strumienie asynchroniczne</span><span class="sxs-lookup"><span data-stu-id="54875-134">Asynchronous streams</span></span>

<span data-ttu-id="54875-135">Asynchroniczne wyniki zapytania są teraz uwidaczniane przy użyciu nowego interfejsu `IAsyncEnumerable<T>` i mogą być używane przy użyciu `await foreach`.</span><span class="sxs-lookup"><span data-stu-id="54875-135">Asynchronous query results are now exposed using the new standard `IAsyncEnumerable<T>` interface and can be consumed using `await foreach`.</span></span>

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

<span data-ttu-id="54875-136">Aby uzyskać więcej informacji, zobacz [strumienie asynchroniczne C# w dokumentacji](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-8#asynchronous-streams) .</span><span class="sxs-lookup"><span data-stu-id="54875-136">See the [asynchronous streams in the C# documentation](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-8#asynchronous-streams) for more details.</span></span>

### <a name="nullable-reference-types"></a><span data-ttu-id="54875-137">Typy referencyjne dopuszczające wartość null</span><span class="sxs-lookup"><span data-stu-id="54875-137">Nullable reference types</span></span> 

<span data-ttu-id="54875-138">Gdy ta nowa funkcja jest włączona w kodzie, EF Core bada wartości null właściwości typu odwołania i stosuje je do odpowiednich kolumn i relacji w bazie danych: właściwości typów odwołań niedopuszczających wartości null są traktowane tak, jakby były @no__ atrybut adnotacji danych t-0.</span><span class="sxs-lookup"><span data-stu-id="54875-138">When this new feature is enabled in your code, EF Core examines the nullability of reference type properties and applies it to corresponding columns and relationships in the database: properties of non-nullable references types are treated as if they had the `[Required]` data annotation attribute.</span></span>

<span data-ttu-id="54875-139">Na przykład w poniższej klasie właściwości oznaczone jako typu `string?` zostaną skonfigurowane jako opcjonalne, natomiast `string` zostanie skonfigurowany zgodnie z wymaganiami:</span><span class="sxs-lookup"><span data-stu-id="54875-139">For example, in the following class, properties marked as of type `string?` will be configured as optional, whereas `string` will be configured as required:</span></span>

``` csharp
public class Customer
{
  public int Id { get; set; }
  public string FirstName { get; set; }
  public string LastName { get; set; }
  public string? MiddleName { get; set; }
}
```

<span data-ttu-id="54875-140">Aby uzyskać więcej informacji, zobacz [Praca z typami odwołań dopuszczającymi wartość null](xref:core/miscellaneous/nullable-reference-types) w dokumentacji EF Core.</span><span class="sxs-lookup"><span data-stu-id="54875-140">See [Working with nullable reference types](xref:core/miscellaneous/nullable-reference-types) in the EF Core documentation for more details.</span></span>

## <a name="interception-of-database-operations"></a><span data-ttu-id="54875-141">Przechwycenie operacji bazy danych</span><span class="sxs-lookup"><span data-stu-id="54875-141">Interception of database operations</span></span>

<span data-ttu-id="54875-142">Nowy interfejs API przechwycenia w EF Core 3,0 umożliwia automatyczne wywoływanie logiki niestandardowej za każdym razem, gdy w normalnej operacji EF Core wystąpią operacje bazy danych niskiego poziomu.</span><span class="sxs-lookup"><span data-stu-id="54875-142">The new interception API in EF Core 3.0 allows providing custom logic to be invoked automatically whenever low-level database operations occur as part of the normal operation of EF Core.</span></span> <span data-ttu-id="54875-143">Na przykład podczas otwierania połączeń, zatwierdzania transakcji lub wykonywania poleceń.</span><span class="sxs-lookup"><span data-stu-id="54875-143">For example, when opening connections, committing transactions, or executing commands.</span></span> 

<span data-ttu-id="54875-144">Podobnie jak w przypadku funkcji przechwycenia, które istniały w EF 6, Interceptory umożliwiają przechwycenie operacji przed lub po ich wystąpieniu.</span><span class="sxs-lookup"><span data-stu-id="54875-144">Similarly to the interception features that existed in EF 6, interceptors allow you to intercept operations before or after they happen.</span></span> <span data-ttu-id="54875-145">Gdy przechwytuje je przed ich przystąpieniem, można wykonywać wykonywanie i dostarczać alternatywne wyniki z logiki przechwycenia.</span><span class="sxs-lookup"><span data-stu-id="54875-145">When you intercept them before they happen, you are allowed to by-pass execution and supply alternate results from the interception logic.</span></span> 

<span data-ttu-id="54875-146">Na przykład, aby manipulować tekstem poleceń, można utworzyć `IDbCommandInterceptor`:</span><span class="sxs-lookup"><span data-stu-id="54875-146">For example, to manipulate command text, you can create an `IDbCommandInterceptor`:</span></span>

``` csharp 
public class HintCommandInterceptor : DbCommandInterceptor
{
  public override InterceptionResult ReaderExecuting(
    DbCommand command, 
    CommandEventData eventData, 
    InterceptionResult result)
  {
    // Manipulate the command text, etc. here...
    command.CommandText += " OPTION (OPTIMIZE FOR UNKNOWN)";
    return result;
  }
}
``` 

<span data-ttu-id="54875-147">I zarejestruj ją za pomocą @ no__t-0:</span><span class="sxs-lookup"><span data-stu-id="54875-147">And register it with your `DbContext`:</span></span>

``` csharp
services.AddDbContext(b => b
  .UseSqlServer(connectionString)
  .AddInterceptors(new HintCommandInterceptor()));
```

## <a name="reverse-engineering-of-database-views"></a><span data-ttu-id="54875-148">Odtwarzanie widoków bazy danych</span><span class="sxs-lookup"><span data-stu-id="54875-148">Reverse engineering of database views</span></span>

<span data-ttu-id="54875-149">Typy zapytań, które reprezentują dane, które mogą zostać odczytane z bazy danych, ale nie zostały zaktualizowane, zostały zmienione pod kątem [typów jednostek](xref:core/modeling/keyless-entity-types).</span><span class="sxs-lookup"><span data-stu-id="54875-149">Query types, which represent data that can be read from the database but not updated, have been renamed to [keyless entity types](xref:core/modeling/keyless-entity-types).</span></span> <span data-ttu-id="54875-150">Ponieważ zapewniają one doskonałą obsługę mapowania widoków bazy danych w większości scenariuszy, EF Core teraz automatycznie tworzy typy jednostek bez użycia w przypadku odtwarzania widoków bazy danych.</span><span class="sxs-lookup"><span data-stu-id="54875-150">As they are an excellent fit for mapping database views in most scenarios, EF Core now automatically creates keyless entity types when reverse engineering database views.</span></span>

<span data-ttu-id="54875-151">Na przykład przy użyciu [narzędzia wiersza polecenia dotnet EF](xref:core/miscellaneous/cli/dotnet) można wpisać:</span><span class="sxs-lookup"><span data-stu-id="54875-151">For example, using the [dotnet ef command-line tool](xref:core/miscellaneous/cli/dotnet) you can type:</span></span>

``` console
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="54875-152">Narzędzie będzie teraz automatycznie szkieletować typy dla widoków i tabel bez kluczy:</span><span class="sxs-lookup"><span data-stu-id="54875-152">And the tool will now automatically scaffold types for views and tables without keys:</span></span>

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

## <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a><span data-ttu-id="54875-153">Jednostki zależne współużytkujące tabelę z podmiotem zabezpieczeń są teraz opcjonalne</span><span class="sxs-lookup"><span data-stu-id="54875-153">Dependent entities sharing the table with the principal are now optional</span></span>

<span data-ttu-id="54875-154">Począwszy od EF Core 3,0, jeśli `OrderDetails` należy do `Order` lub jawnie mapowanych do tej samej tabeli, będzie możliwe dodanie `Order` bez `OrderDetails` i wszystkich właściwości `OrderDetails`, z wyjątkiem tego, że klucz podstawowy zostanie zmapowany na kolumny dopuszczające wartość null.</span><span class="sxs-lookup"><span data-stu-id="54875-154">Starting with EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table, it will be possible to add an `Order` without an `OrderDetails` and all of the `OrderDetails` properties, except the primary key will be mapped to nullable columns.</span></span>

<span data-ttu-id="54875-155">Podczas wykonywania zapytania EF Core ustawi `OrderDetails`, aby `null`, Jeśli którakolwiek z wymaganych właściwości nie ma wartości lub jeśli nie ma żadnych wymaganych właściwości poza kluczem podstawowym, a wszystkie właściwości są `null`.</span><span class="sxs-lookup"><span data-stu-id="54875-155">When querying, EF Core will set `OrderDetails` to `null` if any of its required properties doesn't have a value, or if it has no required properties besides the primary key and all properties are `null`.</span></span>

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

## <a name="ef-63-on-net-core"></a><span data-ttu-id="54875-156">Dr 6,3 na platformie .NET Core</span><span class="sxs-lookup"><span data-stu-id="54875-156">EF 6.3 on .NET Core</span></span>

<span data-ttu-id="54875-157">To nie jest w rzeczywistości funkcją EF Core 3,0, ale uważamy, że jest ona ważna dla naszych bieżących klientów.</span><span class="sxs-lookup"><span data-stu-id="54875-157">This isn't really an EF Core 3.0 feature, but we think it is important to many of our current customers.</span></span> 

<span data-ttu-id="54875-158">Firma Microsoft zdaje sobie sprawę, że w wielu istniejących aplikacjach są używane poprzednie wersje EF, a ich przenoszenie do EF Core tylko w celu wykorzystania platformy .NET Core może wymagać znacznego wysiłku.</span><span class="sxs-lookup"><span data-stu-id="54875-158">We understand that many existing applications use previous versions of EF, and that porting them to EF Core only to take advantage of .NET Core can require a significant effort.</span></span> <span data-ttu-id="54875-159">Z tego powodu postanowiono przenieść najnowszą wersję programu EF 6 do uruchamiania na platformie .NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="54875-159">For that reason, we decided to port the newest version of EF 6 to run on .NET Core 3.0.</span></span> 

<span data-ttu-id="54875-160">Aby uzyskać więcej informacji, zobacz [co nowego w programie EF 6](xref:ef6/what-is-new/index).</span><span class="sxs-lookup"><span data-stu-id="54875-160">For more details, see [what's new in EF 6](xref:ef6/what-is-new/index).</span></span>

## <a name="postponed-features"></a><span data-ttu-id="54875-161">Funkcje odroczone</span><span class="sxs-lookup"><span data-stu-id="54875-161">Postponed features</span></span>

<span data-ttu-id="54875-162">Niektóre funkcje początkowo planowane dla EF Core 3,0 zostały odroczone do przyszłych wersji:</span><span class="sxs-lookup"><span data-stu-id="54875-162">Some features originally planned for EF Core 3.0 were postponed to future releases:</span></span>

- <span data-ttu-id="54875-163">Możliwość ignorowania części modelu w migracjach, śledzonych jako [#2725](https://github.com/aspnet/EntityFrameworkCore/issues/2725).</span><span class="sxs-lookup"><span data-stu-id="54875-163">Ability to ignore parts of a model in migrations, tracked as [#2725](https://github.com/aspnet/EntityFrameworkCore/issues/2725).</span></span>
- <span data-ttu-id="54875-164">Jednostki zbioru właściwości, śledzone jako dwa oddzielne problemy: [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914) informacji o jednostkach typu udostępnionego i [#13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610) o obsłudze mapowania właściwości indeksowanych.</span><span class="sxs-lookup"><span data-stu-id="54875-164">Property bag entities, tracked as two separate issues: [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914) about shared-type entities and [#13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610) about indexed property mapping support.</span></span>
