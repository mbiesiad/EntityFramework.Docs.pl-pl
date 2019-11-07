---
title: Co nowego w EF Core 2,1 — EF Core
author: divega
ms.date: 02/20/2018
ms.assetid: 585F90A3-4D5A-4DD1-92D8-5243B14E0FEC
uid: core/what-is-new/ef-core-2.1
ms.openlocfilehash: ba3a26bcd76cd0b9615b13f32456e7280afe533a
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73654845"
---
# <a name="new-features-in-ef-core-21"></a><span data-ttu-id="890d2-102">Nowe funkcje w EF Core 2,1</span><span class="sxs-lookup"><span data-stu-id="890d2-102">New features in EF Core 2.1</span></span>

<span data-ttu-id="890d2-103">Oprócz licznych poprawek i niewielkich ulepszeń funkcjonalnych i wydajności EF Core 2,1 obejmuje niektóre atrakcyjne nowe funkcje:</span><span class="sxs-lookup"><span data-stu-id="890d2-103">Besides numerous bug fixes and small functional and performance enhancements, EF Core 2.1 includes some compelling new features:</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="890d2-104">Ładowanie z opóźnieniem</span><span class="sxs-lookup"><span data-stu-id="890d2-104">Lazy loading</span></span>

<span data-ttu-id="890d2-105">EF Core teraz zawiera niezbędne bloki konstrukcyjne dla każdego do tworzenia klas jednostek, które mogą ładować właściwości nawigacji na żądanie.</span><span class="sxs-lookup"><span data-stu-id="890d2-105">EF Core now contains the necessary building blocks for anyone to author entity classes that can load their navigation properties on demand.</span></span> <span data-ttu-id="890d2-106">Utworzyliśmy również nowy pakiet Microsoft. EntityFrameworkCore. proxy, który korzysta z tych bloków konstrukcyjnych, aby utworzyć klasy proxy z opóźnieniem, oparte na minimalnych modyfikacjach klas jednostek (na przykład klasy z wirtualnymi właściwościami nawigacji).</span><span class="sxs-lookup"><span data-stu-id="890d2-106">We have also created a new package, Microsoft.EntityFrameworkCore.Proxies, that leverages those building blocks to produce lazy loading proxy classes based on minimally modified entity classes (for example, classes with virtual navigation properties).</span></span>

<span data-ttu-id="890d2-107">Zapoznaj się z [sekcją dotyczącą ładowania z opóźnieniem](xref:core/querying/related-data#lazy-loading) , aby uzyskać więcej informacji na temat tego tematu.</span><span class="sxs-lookup"><span data-stu-id="890d2-107">Read the [section on lazy loading](xref:core/querying/related-data#lazy-loading) for more information about this topic.</span></span>

## <a name="parameters-in-entity-constructors"></a><span data-ttu-id="890d2-108">Parametry w konstruktorach jednostek</span><span class="sxs-lookup"><span data-stu-id="890d2-108">Parameters in entity constructors</span></span>

<span data-ttu-id="890d2-109">Jako jeden z wymaganych bloków konstrukcyjnych dla ładowania z opóźnieniem włączono tworzenie jednostek, które przyjmują parametry w konstruktorach.</span><span class="sxs-lookup"><span data-stu-id="890d2-109">As one of the required building blocks for lazy loading, we enabled the creation of entities that take parameters in their constructors.</span></span> <span data-ttu-id="890d2-110">Za pomocą parametrów można wstrzyknąć wartości właściwości, delegatów ładowania z opóźnieniem i usług.</span><span class="sxs-lookup"><span data-stu-id="890d2-110">You can use parameters to inject property values, lazy loading delegates, and services.</span></span>

<span data-ttu-id="890d2-111">Przeczytaj [sekcję w konstruktorze jednostek z parametrami](xref:core/modeling/constructors) , aby uzyskać więcej informacji na temat tego tematu.</span><span class="sxs-lookup"><span data-stu-id="890d2-111">Read the [section on entity constructor with parameters](xref:core/modeling/constructors) for more information about this topic.</span></span>

## <a name="value-conversions"></a><span data-ttu-id="890d2-112">Konwersje wartości</span><span class="sxs-lookup"><span data-stu-id="890d2-112">Value conversions</span></span>

<span data-ttu-id="890d2-113">Do tej pory EF Core mogą mapować tylko właściwości typów natywnie obsługiwanych przez bazowego dostawcę bazy danych.</span><span class="sxs-lookup"><span data-stu-id="890d2-113">Until now, EF Core could only map properties of types natively supported by the underlying database provider.</span></span> <span data-ttu-id="890d2-114">Wartości zostały skopiowane z powrotem między kolumnami i właściwościami bez żadnego przekształcenia.</span><span class="sxs-lookup"><span data-stu-id="890d2-114">Values were copied back and forth between columns and properties without any transformation.</span></span> <span data-ttu-id="890d2-115">Począwszy od EF Core 2,1, konwersje wartości można zastosować do przekształcenia wartości uzyskanych z kolumn przed ich zastosowaniem do właściwości i na odwrót.</span><span class="sxs-lookup"><span data-stu-id="890d2-115">Starting with EF Core 2.1, value conversions can be applied to transform the values obtained from columns before they are applied to properties, and vice versa.</span></span> <span data-ttu-id="890d2-116">Mamy kilka konwersji, które mogą być stosowane do Konwencji w razie potrzeby, a także jawny interfejs API konfiguracji, który umożliwia rejestrowanie konwersji niestandardowych między kolumnami i właściwościami.</span><span class="sxs-lookup"><span data-stu-id="890d2-116">We have a number of conversions that can be applied by convention as necessary, as well as an explicit configuration API that allows registering custom conversions between columns and properties.</span></span> <span data-ttu-id="890d2-117">Niektóre aplikacje tej funkcji są następujące:</span><span class="sxs-lookup"><span data-stu-id="890d2-117">Some of the application of this feature are:</span></span>

- <span data-ttu-id="890d2-118">Przechowywanie tekstów stałych jako ciągów</span><span class="sxs-lookup"><span data-stu-id="890d2-118">Storing enums as strings</span></span>
- <span data-ttu-id="890d2-119">Mapowanie liczb całkowitych bez znaku przy użyciu SQL Server</span><span class="sxs-lookup"><span data-stu-id="890d2-119">Mapping unsigned integers with SQL Server</span></span>
- <span data-ttu-id="890d2-120">Automatyczne szyfrowanie i odszyfrowywanie wartości właściwości</span><span class="sxs-lookup"><span data-stu-id="890d2-120">Automatic encryption and decryption of property values</span></span>

<span data-ttu-id="890d2-121">Zapoznaj się z [sekcją konwersje wartości](xref:core/modeling/value-conversions) , aby uzyskać więcej informacji na temat tego tematu.</span><span class="sxs-lookup"><span data-stu-id="890d2-121">Read the [section on value conversions](xref:core/modeling/value-conversions) for more information about this topic.</span></span>  

## <a name="linq-groupby-translation"></a><span data-ttu-id="890d2-122">Tłumaczenie LINQ GroupBy</span><span class="sxs-lookup"><span data-stu-id="890d2-122">LINQ GroupBy translation</span></span>

<span data-ttu-id="890d2-123">Przed wersjami 2,1, w EF Core operator GroupBy LINQ zawsze będzie oceniany w pamięci.</span><span class="sxs-lookup"><span data-stu-id="890d2-123">Before version 2.1, in EF Core the GroupBy LINQ operator would always be evaluated in memory.</span></span> <span data-ttu-id="890d2-124">Teraz obsługujemy tłumaczenie tego kodu do klauzuli GROUP BY w większości typowych przypadków.</span><span class="sxs-lookup"><span data-stu-id="890d2-124">We now support translating it to the SQL GROUP BY clause in most common cases.</span></span>

<span data-ttu-id="890d2-125">Ten przykład pokazuje zapytanie z atrybutem GroupBy używanym do obliczania różnych funkcji agregujących:</span><span class="sxs-lookup"><span data-stu-id="890d2-125">This example shows a query with GroupBy used to compute various aggregate functions:</span></span>

``` csharp
var query = context.Orders
    .GroupBy(o => new { o.CustomerId, o.EmployeeId })
    .Select(g => new
        {
          g.Key.CustomerId,
          g.Key.EmployeeId,
          Sum = g.Sum(o => o.Amount),
          Min = g.Min(o => o.Amount),
          Max = g.Max(o => o.Amount),
          Avg = g.Average(o => o.Amount)
        });
```

<span data-ttu-id="890d2-126">Odpowiednie tłumaczenie SQL wygląda następująco:</span><span class="sxs-lookup"><span data-stu-id="890d2-126">The corresponding SQL translation looks like this:</span></span>

``` SQL
SELECT [o].[CustomerId], [o].[EmployeeId],
    SUM([o].[Amount]), MIN([o].[Amount]), MAX([o].[Amount]), AVG([o].[Amount])
FROM [Orders] AS [o]
GROUP BY [o].[CustomerId], [o].[EmployeeId];
```

## <a name="data-seeding"></a><span data-ttu-id="890d2-127">Wstępne wypełnianie danych</span><span class="sxs-lookup"><span data-stu-id="890d2-127">Data Seeding</span></span>

<span data-ttu-id="890d2-128">Dzięki nowej wersji będzie możliwe dostarczenie początkowych danych w celu wypełniania bazy danych.</span><span class="sxs-lookup"><span data-stu-id="890d2-128">With the new release it will be possible to provide initial data to populate a database.</span></span> <span data-ttu-id="890d2-129">W przeciwieństwie do EF6, wypełnianie danych jest skojarzone z typem jednostki w ramach konfiguracji modelu.</span><span class="sxs-lookup"><span data-stu-id="890d2-129">Unlike in EF6, seeding data is associated to an entity type as part of the model configuration.</span></span> <span data-ttu-id="890d2-130">Następnie EF Core migracji mogą automatycznie obliczyć operacje wstawiania, aktualizowania lub usuwania, które należy zastosować podczas uaktualniania bazy danych do nowej wersji modelu.</span><span class="sxs-lookup"><span data-stu-id="890d2-130">Then EF Core migrations can automatically compute what insert, update or delete operations need to be applied when upgrading the database to a new version of the model.</span></span>

<span data-ttu-id="890d2-131">Przykładowo można użyć tego do skonfigurowania danych inicjatora dla wpisu w `OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="890d2-131">As an example, you can use this to configure seed data for a Post in `OnModelCreating`:</span></span>

``` csharp
modelBuilder.Entity<Post>().HasData(new Post{ Id = 1, Text = "Hello World!" });
```

<span data-ttu-id="890d2-132">Zapoznaj się z [sekcją dotyczącą](xref:core/modeling/data-seeding) umieszczania danych, aby uzyskać więcej informacji na temat tego tematu.</span><span class="sxs-lookup"><span data-stu-id="890d2-132">Read the [section on data seeding](xref:core/modeling/data-seeding) for more information about this topic.</span></span>  

## <a name="query-types"></a><span data-ttu-id="890d2-133">Typy zapytań</span><span class="sxs-lookup"><span data-stu-id="890d2-133">Query types</span></span>

<span data-ttu-id="890d2-134">Model EF Core może teraz zawierać typy zapytań.</span><span class="sxs-lookup"><span data-stu-id="890d2-134">An EF Core model can now include query types.</span></span> <span data-ttu-id="890d2-135">W przeciwieństwie do typów jednostek, typy zapytań nie mają zdefiniowanych kluczy i nie można ich wstawiać, usuwać ani aktualizować (oznacza to, że są tylko do odczytu), ale mogą być zwracane bezpośrednio przez zapytania.</span><span class="sxs-lookup"><span data-stu-id="890d2-135">Unlike entity types, query types do not have keys defined on them and cannot be inserted, deleted or updated (that is, they are read-only), but they can be returned directly by queries.</span></span> <span data-ttu-id="890d2-136">Niektóre scenariusze użycia dla typów zapytań są następujące:</span><span class="sxs-lookup"><span data-stu-id="890d2-136">Some of the usage scenarios for query types are:</span></span>

- <span data-ttu-id="890d2-137">Mapowanie do widoków bez kluczy podstawowych</span><span class="sxs-lookup"><span data-stu-id="890d2-137">Mapping to views without primary keys</span></span>
- <span data-ttu-id="890d2-138">Mapowanie do tabel bez kluczy podstawowych</span><span class="sxs-lookup"><span data-stu-id="890d2-138">Mapping to tables without primary keys</span></span>
- <span data-ttu-id="890d2-139">Mapowanie na zapytania zdefiniowane w modelu</span><span class="sxs-lookup"><span data-stu-id="890d2-139">Mapping to queries defined in the model</span></span>
- <span data-ttu-id="890d2-140">Obsługa jako typ zwracany dla zapytań `FromSql()`</span><span class="sxs-lookup"><span data-stu-id="890d2-140">Serving as the return type for `FromSql()` queries</span></span>

<span data-ttu-id="890d2-141">Aby uzyskać więcej informacji na temat tego tematu, zapoznaj się z [sekcją dotyczącą typów zapytań](xref:core/modeling/keyless-entity-types) .</span><span class="sxs-lookup"><span data-stu-id="890d2-141">Read the [section on query types](xref:core/modeling/keyless-entity-types) for more information about this topic.</span></span>

## <a name="include-for-derived-types"></a><span data-ttu-id="890d2-142">Uwzględnij dla typów pochodnych</span><span class="sxs-lookup"><span data-stu-id="890d2-142">Include for derived types</span></span>

<span data-ttu-id="890d2-143">Teraz można określić właściwości nawigacji zdefiniowane tylko w typach pochodnych podczas pisania wyrażeń dla metody `Include`.</span><span class="sxs-lookup"><span data-stu-id="890d2-143">It will be now possible to specify navigation properties only defined on derived types when writing expressions for the `Include` method.</span></span> <span data-ttu-id="890d2-144">W przypadku silnie wpisanej wersji `Include`obsługujemy użycie jawnego rzutowania lub operatora `as`.</span><span class="sxs-lookup"><span data-stu-id="890d2-144">For the strongly typed version of `Include`, we support using either an explicit cast or the `as` operator.</span></span> <span data-ttu-id="890d2-145">Teraz obsługujemy również odwoływanie się do nazw właściwości nawigacji zdefiniowanej w typach pochodnych w wersji ciągu `Include`:</span><span class="sxs-lookup"><span data-stu-id="890d2-145">We also now support referencing the names of navigation property defined on derived types in the string version of `Include`:</span></span>

``` csharp
var option1 = context.People.Include(p => ((Student)p).School);
var option2 = context.People.Include(p => (p as Student).School);
var option3 = context.People.Include("School");
```

<span data-ttu-id="890d2-146">Zapoznaj się z [sekcją zawiera typy pochodne](xref:core/querying/related-data#include-on-derived-types) , aby uzyskać więcej informacji na temat tego tematu.</span><span class="sxs-lookup"><span data-stu-id="890d2-146">Read the [section on Include with derived types](xref:core/querying/related-data#include-on-derived-types) for more information about this topic.</span></span>

## <a name="systemtransactions-support"></a><span data-ttu-id="890d2-147">Obsługa system. Transactions</span><span class="sxs-lookup"><span data-stu-id="890d2-147">System.Transactions support</span></span>

<span data-ttu-id="890d2-148">Dodaliśmy możliwość pracy z funkcjami system. Transactions, takimi jak element TransactionScope.</span><span class="sxs-lookup"><span data-stu-id="890d2-148">We have added the ability to work with System.Transactions features such as TransactionScope.</span></span> <span data-ttu-id="890d2-149">Będzie to możliwe w przypadku .NET Framework i .NET Core przy użyciu dostawców baz danych, które go obsługują.</span><span class="sxs-lookup"><span data-stu-id="890d2-149">This will work on both .NET Framework and .NET Core when using database providers that support it.</span></span>

<span data-ttu-id="890d2-150">Zapoznaj się z [sekcją system. Transactions](xref:core/saving/transactions#using-systemtransactions) , aby uzyskać więcej informacji na temat tego tematu.</span><span class="sxs-lookup"><span data-stu-id="890d2-150">Read the [section on System.Transactions](xref:core/saving/transactions#using-systemtransactions) for more information about this topic.</span></span>

## <a name="better-column-ordering-in-initial-migration"></a><span data-ttu-id="890d2-151">Lepsza kolejność kolumn podczas migracji początkowej</span><span class="sxs-lookup"><span data-stu-id="890d2-151">Better column ordering in initial migration</span></span>

<span data-ttu-id="890d2-152">Na podstawie opinii klientów Zaktualizowaliśmy migracje w celu wstępnego wygenerowania kolumn dla tabel w tej samej kolejności, w której właściwości są zadeklarowane w klasach.</span><span class="sxs-lookup"><span data-stu-id="890d2-152">Based on customer feedback, we have updated migrations to initially generate columns for tables in the same order as properties are declared in classes.</span></span> <span data-ttu-id="890d2-153">Należy pamiętać, że EF Core nie może zmienić kolejności po dodaniu nowych członków po początkowej operacji tworzenia tabeli.</span><span class="sxs-lookup"><span data-stu-id="890d2-153">Note that EF Core cannot change order when new members are added after the initial table creation.</span></span>

## <a name="optimization-of-correlated-subqueries"></a><span data-ttu-id="890d2-154">Optymalizacja skorelowanych podkwerend</span><span class="sxs-lookup"><span data-stu-id="890d2-154">Optimization of correlated subqueries</span></span>

<span data-ttu-id="890d2-155">Ulepszono nasze tłumaczenie zapytań, aby uniknąć wykonywania zapytań SQL "N + 1" w wielu typowych scenariuszach, w których użycie właściwości nawigacji w projekcji prowadzi do dołączania danych z zapytania głównego z danymi z skorelowanego podzapytania.</span><span class="sxs-lookup"><span data-stu-id="890d2-155">We have improved our query translation to avoid executing "N + 1" SQL queries in many common scenarios in which the usage of a navigation property in the projection leads to joining data from the root query with data from a correlated subquery.</span></span> <span data-ttu-id="890d2-156">Optymalizacja wymaga buforowania wyników z podzapytania i wymaga modyfikacji zapytania, aby wybrać nowe zachowanie.</span><span class="sxs-lookup"><span data-stu-id="890d2-156">The optimization requires buffering the results from the subquery, and we require that you modify the query to opt-in the new behavior.</span></span>

<span data-ttu-id="890d2-157">Na przykład następujące zapytanie jest zwykle tłumaczone na jedno zapytanie dla klientów i N (gdzie "N" jest liczbą zwracanych klientów) oddzielnymi zapytania dla zamówień:</span><span class="sxs-lookup"><span data-stu-id="890d2-157">As an example, the following query normally gets translated into one query for Customers, plus N (where "N" is the number of customers returned) separate queries for Orders:</span></span>

``` csharp
var query = context.Customers.Select(
    c => c.Orders.Where(o => o.Amount  > 100).Select(o => o.Amount));
```

<span data-ttu-id="890d2-158">Dołączając `ToList()` w odpowiednim miejscu, oznacza to, że buforowanie jest odpowiednie dla zamówień, które umożliwiają optymalizację:</span><span class="sxs-lookup"><span data-stu-id="890d2-158">By including `ToList()` in the right place, you indicate that buffering is appropriate for the Orders, which enable the optimization:</span></span>

``` csharp
var query = context.Customers.Select(
    c => c.Orders.Where(o => o.Amount  > 100).Select(o => o.Amount).ToList());
```

<span data-ttu-id="890d2-159">Należy zauważyć, że to zapytanie zostanie przetłumaczone tylko na dwie zapytania SQL: jeden dla klientów i następny jeden dla zamówień.</span><span class="sxs-lookup"><span data-stu-id="890d2-159">Note that this query will be translated to only two SQL queries: One for Customers and the next one for Orders.</span></span>

## <a name="owned-attribute"></a><span data-ttu-id="890d2-160">[Własność] — atrybut</span><span class="sxs-lookup"><span data-stu-id="890d2-160">[Owned] attribute</span></span>

<span data-ttu-id="890d2-161">Teraz można skonfigurować [własne typy jednostek](xref:core/modeling/owned-entities) poprzez po prostu adnotację typu z `[Owned]`, a następnie upewniając się, że jednostka właściciela została dodana do modelu:</span><span class="sxs-lookup"><span data-stu-id="890d2-161">It is now possible to configure [owned entity types](xref:core/modeling/owned-entities) by simply annotating the type with `[Owned]` and then making sure the owner entity is added to the model:</span></span>

``` csharp
[Owned]
public class StreetAddress
{
    public string Street { get; set; }
    public string City { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public StreetAddress ShippingAddress { get; set; }
}
```

## <a name="command-line-tool-dotnet-ef-included-in-net-core-sdk"></a><span data-ttu-id="890d2-162">Narzędzie wiersza polecenia dotnet-EF zawarte w zestaw .NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="890d2-162">Command-line tool dotnet-ef included in .NET Core SDK</span></span>

<span data-ttu-id="890d2-163">Polecenia _dotnet-EF_ są teraz częścią zestaw .NET Core SDK, dlatego nie trzeba już używać DotNetCliToolReference w projekcie, aby można było używać migracji lub do tworzenia szkieletu DbContext z istniejącej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="890d2-163">The _dotnet-ef_ commands are now part of the .NET Core SDK, therefore it will no longer be necessary to use DotNetCliToolReference in the project to be able to use migrations or to scaffold a DbContext from an existing database.</span></span>

<span data-ttu-id="890d2-164">Zapoznaj się z sekcją [Instalowanie narzędzi,](xref:core/miscellaneous/cli/dotnet#installing-the-tools) Aby uzyskać więcej informacji na temat włączania narzędzi wiersza polecenia dla różnych wersji zestaw .NET Core SDK i EF Core.</span><span class="sxs-lookup"><span data-stu-id="890d2-164">See the section on [installing the tools](xref:core/miscellaneous/cli/dotnet#installing-the-tools) for more details on how to enable command line tools for different versions of the .NET Core SDK and EF Core.</span></span>

## <a name="microsoftentityframeworkcoreabstractions-package"></a><span data-ttu-id="890d2-165">Pakiet Microsoft. EntityFrameworkCore. Abstracts</span><span class="sxs-lookup"><span data-stu-id="890d2-165">Microsoft.EntityFrameworkCore.Abstractions package</span></span>

<span data-ttu-id="890d2-166">Nowy pakiet zawiera atrybuty i interfejsy, których można używać w projektach, aby wyrównać funkcje EF Core bez konieczności EF Core jako całości.</span><span class="sxs-lookup"><span data-stu-id="890d2-166">The new package contains attributes and interfaces that you can use in your projects to light up EF Core features without taking a dependency on EF Core as a whole.</span></span> <span data-ttu-id="890d2-167">Na przykład, atrybut [własność] i interfejs ILazyLoader znajdują się tutaj.</span><span class="sxs-lookup"><span data-stu-id="890d2-167">For example, the [Owned] attribute and the ILazyLoader interface are located here.</span></span>

## <a name="state-change-events"></a><span data-ttu-id="890d2-168">Zdarzenia zmiany stanu</span><span class="sxs-lookup"><span data-stu-id="890d2-168">State change events</span></span>

<span data-ttu-id="890d2-169">Nowe `Tracked` i `StateChanged` zdarzenia w `ChangeTracker` mogą służyć do pisania logiki, która reaguje na jednostki wchodzące w obiekt DbContext lub zmieniając ich stan.</span><span class="sxs-lookup"><span data-stu-id="890d2-169">New `Tracked` And `StateChanged` events on `ChangeTracker` can be used to write logic that reacts to entities entering the DbContext or changing their state.</span></span>

## <a name="raw-sql-parameter-analyzer"></a><span data-ttu-id="890d2-170">Nieprzetworzony Analizator parametrów SQL</span><span class="sxs-lookup"><span data-stu-id="890d2-170">Raw SQL parameter analyzer</span></span>

<span data-ttu-id="890d2-171">Do EF Core jest dołączany nowy analizator kodu, który wykrywa potencjalnie niebezpieczne użycia interfejsów API RAW-SQL, takich jak `FromSql` lub `ExecuteSqlCommand`.</span><span class="sxs-lookup"><span data-stu-id="890d2-171">A new code analyzer is included with EF Core that detects potentially unsafe usages of our raw-SQL APIs, like `FromSql` or `ExecuteSqlCommand`.</span></span> <span data-ttu-id="890d2-172">Na przykład dla poniższego zapytania zobaczysz ostrzeżenie, ponieważ _minAge_ nie jest sparametryzowane:</span><span class="sxs-lookup"><span data-stu-id="890d2-172">For example, for the following query, you will see a warning because _minAge_ is not parameterized:</span></span>

``` csharp
var sql = $"SELECT * FROM People WHERE Age > {minAge}";
var query = context.People.FromSql(sql);
```

## <a name="database-provider-compatibility"></a><span data-ttu-id="890d2-173">Zgodność dostawcy bazy danych</span><span class="sxs-lookup"><span data-stu-id="890d2-173">Database provider compatibility</span></span>

<span data-ttu-id="890d2-174">Zaleca się używanie EF Core 2,1 z dostawcami, którzy zostali zaktualizowani lub co najmniej przetestowani do pracy z EF Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="890d2-174">It is recommended that you use EF Core 2.1 with providers that have been updated or at least tested to work with EF Core 2.1.</span></span>

> [!TIP]
> <span data-ttu-id="890d2-175">Jeśli okaże się, że wystąpiły nieoczekiwane niezgodność lub jakiekolwiek problemy w nowych funkcjach, lub jeśli masz opinię na ich temat, zgłoś je za pomocą [naszego narzędzia do śledzenia problemów](https://github.com/aspnet/EntityFrameworkCore/issues/new).</span><span class="sxs-lookup"><span data-stu-id="890d2-175">If you find any unexpected incompatibility or any issue in the new features, or if you have feedback on them, please report it using [our issue tracker](https://github.com/aspnet/EntityFrameworkCore/issues/new).</span></span>
