---
title: Ładowanie powiązanych danych — EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: f9fb64e2-6699-4d70-a773-592918c04c19
uid: core/querying/related-data
ms.openlocfilehash: d3a1810599771befb451715d93454fff63949771
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238310"
---
# <a name="loading-related-data"></a><span data-ttu-id="5c427-102">Ładowanie powiązanych danych</span><span class="sxs-lookup"><span data-stu-id="5c427-102">Loading Related Data</span></span>

<span data-ttu-id="5c427-103">Entity Framework Core umożliwia używanie właściwości nawigacji w modelu do ładowania powiązanych jednostek.</span><span class="sxs-lookup"><span data-stu-id="5c427-103">Entity Framework Core allows you to use the navigation properties in your model to load related entities.</span></span> <span data-ttu-id="5c427-104">Istnieją trzy typowe wzorce O/RM używane do ładowania powiązanych danych.</span><span class="sxs-lookup"><span data-stu-id="5c427-104">There are three common O/RM patterns used to load related data.</span></span>

* <span data-ttu-id="5c427-105">**Ładowanie eager** oznacza, że powiązane dane są ładowane z bazy danych w ramach wstępnego zapytania.</span><span class="sxs-lookup"><span data-stu-id="5c427-105">**Eager loading** means that the related data is loaded from the database as part of the initial query.</span></span>
* <span data-ttu-id="5c427-106">**Jawne ładowanie** oznacza, że powiązane dane są jawnie ładowane z bazy danych w późniejszym czasie.</span><span class="sxs-lookup"><span data-stu-id="5c427-106">**Explicit loading** means that the related data is explicitly loaded from the database at a later time.</span></span>
* <span data-ttu-id="5c427-107">**Ładowanie z opóźnieniem** oznacza, że powiązane dane są w sposób niewidoczny do załadowania z bazy danych podczas uzyskiwania dostępu do właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="5c427-107">**Lazy loading** means that the related data is transparently loaded from the database when the navigation property is accessed.</span></span>

> [!TIP]  
> <span data-ttu-id="5c427-108">[Przykład](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) tego artykułu można wyświetlić w witrynie GitHub.</span><span class="sxs-lookup"><span data-stu-id="5c427-108">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="eager-loading"></a><span data-ttu-id="5c427-109">Ładowanie eager</span><span class="sxs-lookup"><span data-stu-id="5c427-109">Eager loading</span></span>

<span data-ttu-id="5c427-110">Możesz użyć metody, `Include` Aby określić powiązane dane, które mają być uwzględnione w wynikach zapytania.</span><span class="sxs-lookup"><span data-stu-id="5c427-110">You can use the `Include` method to specify related data to be included in query results.</span></span> <span data-ttu-id="5c427-111">W poniższym przykładzie w blogach, które są zwracane w wynikach, zostanie `Posts` wypełniona ich właściwość wraz z powiązanymi wpisami.</span><span class="sxs-lookup"><span data-stu-id="5c427-111">In the following example, the blogs that are returned in the results will have their `Posts` property populated with the related posts.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleInclude)]

> [!TIP]  
> <span data-ttu-id="5c427-112">Entity Framework Core automatycznie naprawia właściwości nawigacji do wszystkich innych jednostek, które zostały wcześniej załadowane do wystąpienia kontekstu.</span><span class="sxs-lookup"><span data-stu-id="5c427-112">Entity Framework Core will automatically fix-up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="5c427-113">Dlatego nawet jeśli nie dołączysz jawnie danych dla właściwości nawigacji, właściwość może nadal zostać wypełniona, jeśli niektóre lub wszystkie powiązane jednostki zostały wcześniej załadowane.</span><span class="sxs-lookup"><span data-stu-id="5c427-113">So even if you don't explicitly include the data for a navigation property, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

<span data-ttu-id="5c427-114">Można uwzględnić powiązane dane z wielu relacji w pojedynczym zapytaniu.</span><span class="sxs-lookup"><span data-stu-id="5c427-114">You can include related data from multiple relationships in a single query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleIncludes)]

### <a name="including-multiple-levels"></a><span data-ttu-id="5c427-115">Uwzględnienie wielu poziomów</span><span class="sxs-lookup"><span data-stu-id="5c427-115">Including multiple levels</span></span>

<span data-ttu-id="5c427-116">Możesz przejść do szczegółów relacji, aby dołączyć wiele poziomów powiązanych danych przy użyciu `ThenInclude` metody.</span><span class="sxs-lookup"><span data-stu-id="5c427-116">You can drill down through relationships to include multiple levels of related data using the `ThenInclude` method.</span></span> <span data-ttu-id="5c427-117">Poniższy przykład ładuje wszystkie blogi, ich powiązane wpisy i autora każdego wpisu.</span><span class="sxs-lookup"><span data-stu-id="5c427-117">The following example loads all blogs, their related posts, and the author of each post.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleThenInclude)]

<span data-ttu-id="5c427-118">Można połączyć wiele wywołań, aby `ThenInclude` kontynuować, włączając dalsze poziomy pokrewnych danych.</span><span class="sxs-lookup"><span data-stu-id="5c427-118">You can chain multiple calls to `ThenInclude` to continue including further levels of related data.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleThenIncludes)]

<span data-ttu-id="5c427-119">Możesz połączyć wszystkie te elementy, aby uwzględnić powiązane dane z wielu poziomów i wiele elementów głównych w tym samym zapytaniu.</span><span class="sxs-lookup"><span data-stu-id="5c427-119">You can combine all of this to include related data from multiple levels and multiple roots in the same query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#IncludeTree)]

<span data-ttu-id="5c427-120">Możesz chcieć dołączyć wiele powiązanych jednostek dla jednej z dołączanych jednostek.</span><span class="sxs-lookup"><span data-stu-id="5c427-120">You may want to include multiple related entities for one of the entities that is being included.</span></span> <span data-ttu-id="5c427-121">Na przykład, podczas wykonywania zapytania `Blogs` , należy dołączyć `Posts` zarówno, jak `Author` i `Tags` `Posts` .</span><span class="sxs-lookup"><span data-stu-id="5c427-121">For example, when querying `Blogs`, you include `Posts` and then want to include both the `Author` and `Tags` of the `Posts`.</span></span> <span data-ttu-id="5c427-122">W tym celu należy określić wszystkie ścieżki dołączania, zaczynając od elementu głównego.</span><span class="sxs-lookup"><span data-stu-id="5c427-122">To do this, you need to specify each include path starting at the root.</span></span> <span data-ttu-id="5c427-123">Na przykład `Blog -> Posts -> Author` i `Blog -> Posts -> Tags`.</span><span class="sxs-lookup"><span data-stu-id="5c427-123">For example, `Blog -> Posts -> Author` and `Blog -> Posts -> Tags`.</span></span> <span data-ttu-id="5c427-124">Nie oznacza to, że nastąpi nadmiarowe sprzężenia; w większości przypadków program Dr konsoliduje sprzężenia podczas generowania bazy danych SQL.</span><span class="sxs-lookup"><span data-stu-id="5c427-124">This does not mean you will get redundant joins; in most cases, EF will consolidate the joins when generating SQL.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludes)]

### <a name="single-and-split-queries"></a><span data-ttu-id="5c427-125">Pojedyncze i podzielone zapytania</span><span class="sxs-lookup"><span data-stu-id="5c427-125">Single and split queries</span></span>

> [!NOTE]
> <span data-ttu-id="5c427-126">Ta funkcja jest wprowadzana w EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="5c427-126">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="5c427-127">W relacyjnych bazach danych wszystkie powiązane jednostki są domyślnie ładowane przez wprowadzenie sprzężeń:</span><span class="sxs-lookup"><span data-stu-id="5c427-127">In relational databases, all related entities are by default loaded by introducing JOINs:</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

<span data-ttu-id="5c427-128">Jeśli typowy blog ma wiele powiązanych wpisów, wiersze dla tych wpisów pomogą zduplikować informacje o blogu, prowadząc do problemu "kartezjańskiego eksplozji".</span><span class="sxs-lookup"><span data-stu-id="5c427-128">If a typical blog has multiple related posts, rows for these posts will duplicate the blog's information, leading to the so-called "cartesian explosion" problem.</span></span> <span data-ttu-id="5c427-129">W miarę ładowania większej liczby relacji jeden-do-wielu, ilość zduplikowanych danych może wzrosnąć i niekorzystnie wpłynąć na wydajność aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5c427-129">As more one-to-many relationships are loaded, the amount of duplicated data may grow and adversely affect the performance of your application.</span></span>

<span data-ttu-id="5c427-130">EF pozwala określić, że dana kwerenda LINQ ma być *podzielona* na wiele zapytań SQL.</span><span class="sxs-lookup"><span data-stu-id="5c427-130">EF allows you to specify that a given LINQ query should be *split* into multiple SQL queries.</span></span> <span data-ttu-id="5c427-131">Zamiast sprzężeń, podzielone zapytania wykonują dodatkowe zapytania SQL dla każdej dołączonej nawigacji "jeden do wielu":</span><span class="sxs-lookup"><span data-stu-id="5c427-131">Instead of JOINs, split queries perform an additional SQL query for each included one-to-many navigation:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs?name=AsSplitQuery&highlight=5)]

<span data-ttu-id="5c427-132">Spowoduje to wygenerowanie następującego kodu SQL:</span><span class="sxs-lookup"><span data-stu-id="5c427-132">This will produce the following SQL:</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
ORDER BY [b].[BlogId]

SELECT [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title], [b].[BlogId]
FROM [Blogs] AS [b]
INNER JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId]
```

<span data-ttu-id="5c427-133">Pozwala to uniknąć problemów z wydajnością skojarzonych z przyłączami i rozłożeniem kartezjańskiego, ale również ma pewne wady:</span><span class="sxs-lookup"><span data-stu-id="5c427-133">While this avoids the performance issues associated with JOINs and cartesian explosion, it also has some drawbacks:</span></span>

* <span data-ttu-id="5c427-134">Chociaż większość baz danych gwarantuje spójność danych dla pojedynczych zapytań, nie istnieją takie gwarancje dla wielu zapytań.</span><span class="sxs-lookup"><span data-stu-id="5c427-134">While most databases guarantee data consistency for single queries, no such guarantees exist for multiple queries.</span></span> <span data-ttu-id="5c427-135">Oznacza to, że jeśli baza danych jest aktualizowana współbieżnie, gdy wykonywane są zapytania, dane wyniki mogą nie być spójne.</span><span class="sxs-lookup"><span data-stu-id="5c427-135">This means that if the database is being updated concurrently as your queries are being executed, resulting data may not be consistent.</span></span> <span data-ttu-id="5c427-136">Można to zmniejszyć przez zapakowanie zapytań w transakcji możliwej do serializacji lub migawki, chociaż może to spowodować problemy z wydajnością.</span><span class="sxs-lookup"><span data-stu-id="5c427-136">This may be mitigated by wrapping the queries in a serializable or snapshot transaction, although this may create performance issues of its own.</span></span> <span data-ttu-id="5c427-137">Aby uzyskać więcej informacji, zapoznaj się z dokumentacją bazy danych.</span><span class="sxs-lookup"><span data-stu-id="5c427-137">Consult your database's documentation for more details.</span></span>
* <span data-ttu-id="5c427-138">Każde zapytanie oznacza obecnie dodatkową sieć w sieci do bazy danych programu; może to obniżyć wydajność, zwłaszcza w przypadku, gdy opóźnienie do bazy danych jest wysokie (na przykład usługi w chmurze).</span><span class="sxs-lookup"><span data-stu-id="5c427-138">Each query currently implies an additional network roundtrip to your database; this can degrade performance, especially where latency to the database is high (e.g. cloud services).</span></span> <span data-ttu-id="5c427-139">EF Core poprawi to w przyszłości, tworząc zbiorczo zapytania w ramach jednej roundtrip.</span><span class="sxs-lookup"><span data-stu-id="5c427-139">EF Core will improve this in the future by batching the queries into a single roundtrip.</span></span>
* <span data-ttu-id="5c427-140">Niektóre bazy danych umożliwiają zużywanie wyników wielu zapytań w tym samym czasie (SQL Server z usługami MARS i SQLite), co pozwala na aktywne tylko pojedyncze zapytanie w danym punkcie.</span><span class="sxs-lookup"><span data-stu-id="5c427-140">While some databases allow consuming the results of multiple queries at the same time (SQL Server with MARS, Sqlite), most allow only a single query to be active at any given point.</span></span> <span data-ttu-id="5c427-141">Oznacza to, że wszystkie wyniki z wcześniejszych zapytań muszą być buforowane w pamięci aplikacji przed wykonaniem późniejszych zapytań, co znacznie zwiększa wymagania dotyczące pamięci.</span><span class="sxs-lookup"><span data-stu-id="5c427-141">This means that all results from earlier queries must be buffered in your application's memory before executing later queries, increasing your memory requirements in a potentially significant way.</span></span>

<span data-ttu-id="5c427-142">Niestety, nie ma żadnej strategii na potrzeby ładowania powiązanych jednostek, które pasują do wszystkich scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="5c427-142">Unfortunately, there isn't one strategy for loading related entities that fits all scenarios.</span></span> <span data-ttu-id="5c427-143">Należy uważnie rozważyć zalety i wady pojedynczych i podzielnych zapytań, a następnie wybrać ten, który odpowiada Twoim potrzebom.</span><span class="sxs-lookup"><span data-stu-id="5c427-143">Carefully consider the advantages and disadvantages of single and split queries, and select the one that fits your needs.</span></span>

> [!NOTE]
> <span data-ttu-id="5c427-144">Jednostki powiązane jeden-do-jednego są zawsze ładowane za pośrednictwem sprzężeń, ponieważ nie ma to wpływu na wydajność.</span><span class="sxs-lookup"><span data-stu-id="5c427-144">One-to-one related entities are always loaded via JOINs, as this has no performance impact.</span></span>
>
> <span data-ttu-id="5c427-145">W tej chwili użycie dzielenia zapytania na SQL Server wymaga ustawień `MultipleActiveResultSets=true` w parametrach połączenia.</span><span class="sxs-lookup"><span data-stu-id="5c427-145">At the moment, use of query splitting on SQL Server requires settings `MultipleActiveResultSets=true` in your connection string.</span></span> <span data-ttu-id="5c427-146">To wymaganie zostanie usunięte w przyszłej wersji zapoznawczej.</span><span class="sxs-lookup"><span data-stu-id="5c427-146">This requirement will be removed in a future preview.</span></span>
>
> <span data-ttu-id="5c427-147">Przyszłe wersje zapoznawcze EF Core 5,0 umożliwiają określanie dzielenia zapytania jako domyślnego dla kontekstu.</span><span class="sxs-lookup"><span data-stu-id="5c427-147">Future previews of EF Core 5.0 will allow specifying query splitting as the default for your context.</span></span>

### <a name="filtered-include"></a><span data-ttu-id="5c427-148">Filtr obejmujący</span><span class="sxs-lookup"><span data-stu-id="5c427-148">Filtered include</span></span>

> [!NOTE]
> <span data-ttu-id="5c427-149">Ta funkcja jest wprowadzana w EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="5c427-149">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="5c427-150">W przypadku zastosowania dyrektywy include do załadowania powiązanych danych można zastosować pewne wyliczalne operacje na nawigacji kolekcji, która umożliwia filtrowanie i sortowanie wyników.</span><span class="sxs-lookup"><span data-stu-id="5c427-150">When applying Include to load related data, you can apply certain enumerable operations on the included collection navigation, which allows for filtering and sorting of the results.</span></span>

<span data-ttu-id="5c427-151">Obsługiwane są następujące operacje:,,,,, `Where` `OrderBy` `OrderByDescending` `ThenBy` `ThenByDescending` `Skip` , i `Take` .</span><span class="sxs-lookup"><span data-stu-id="5c427-151">Supported operations are: `Where`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `Skip`, and `Take`.</span></span>

<span data-ttu-id="5c427-152">Takie operacje powinny być stosowane w nawigacji kolekcji w wyrażeniu lambda przekazanym do metody include, jak pokazano w przykładzie poniżej:</span><span class="sxs-lookup"><span data-stu-id="5c427-152">Such operations should be applied on the collection navigation in the lambda passed to the Include method, as shown in example below:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#FilteredInclude)]

<span data-ttu-id="5c427-153">Każda dołączona Nawigacja zezwala na tylko jeden unikatowy zestaw operacji filtrowania.</span><span class="sxs-lookup"><span data-stu-id="5c427-153">Each included navigation allows only one unique set of filter operations.</span></span> <span data-ttu-id="5c427-154">W przypadkach, gdy do danej kolekcji są stosowane wiele operacji dołączania ( `blog.Posts` w poniższych przykładach), operacje filtrowania można określić tylko dla jednego z nich:</span><span class="sxs-lookup"><span data-stu-id="5c427-154">In cases where multiple Include operations are applied for a given collection navigation (`blog.Posts` in the examples below), filter operations can only be specified on one of them:</span></span> 

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered1)]

<span data-ttu-id="5c427-155">Alternatywnie można zastosować identyczne operacje dla każdej nawigacji, która jest dołączana wiele razy:</span><span class="sxs-lookup"><span data-stu-id="5c427-155">Alternatively, identical operations can be applied for each navigation that is included multiple times:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered2)]

> [!CAUTION]
> <span data-ttu-id="5c427-156">W przypadku zapytań śledzenia wyniki odfiltrowanych instrukcji include mogą być nieoczekiwane ze względu na naprawę [nawigacji](tracking.md).</span><span class="sxs-lookup"><span data-stu-id="5c427-156">In case of tracking queries, results of Filtered Include may be unexpected due to [navigation fixup](tracking.md).</span></span> <span data-ttu-id="5c427-157">Wszystkie odpowiednie jednostki, które zostały querried wcześniej i zostały zapisane w module śledzącym zmiany, będą obecne w wynikach filtrowanego zapytania include, nawet jeśli nie spełniają wymagań filtru.</span><span class="sxs-lookup"><span data-stu-id="5c427-157">All relevant entities that have been querried for previously and have been stored in the Change Tracker will be present in the results of Filtered Include query, even if they don't meet the requirements of the filter.</span></span> <span data-ttu-id="5c427-158">Rozważ użycie `NoTracking` zapytań lub Utwórz ponownie DbContext, gdy w tych sytuacjach użyto funkcji Filtered include.</span><span class="sxs-lookup"><span data-stu-id="5c427-158">Consider using `NoTracking` queries or re-create the DbContext when using Filtered Include in those situations.</span></span>

<span data-ttu-id="5c427-159">Przykład:</span><span class="sxs-lookup"><span data-stu-id="5c427-159">Example:</span></span>

```csharp
var orders = context.Orders.Where(o => o.Id > 1000).ToList();

// customer entities will have references to all orders where Id > 1000, rathat than > 5000
var filtered = context.Customers.Include(c => c.Orders.Where(o => o.Id > 5000)).ToList();
```

### <a name="include-on-derived-types"></a><span data-ttu-id="5c427-160">Uwzględnij w typach pochodnych</span><span class="sxs-lookup"><span data-stu-id="5c427-160">Include on derived types</span></span>

<span data-ttu-id="5c427-161">Można dołączyć powiązane dane z nawigacji zdefiniowanych tylko dla typu pochodnego przy użyciu `Include` i `ThenInclude` .</span><span class="sxs-lookup"><span data-stu-id="5c427-161">You can include related data from navigations defined only on a derived type using `Include` and `ThenInclude`.</span></span>

<span data-ttu-id="5c427-162">Uwzględniając następujący model:</span><span class="sxs-lookup"><span data-stu-id="5c427-162">Given the following model:</span></span>

```csharp
public class SchoolContext : DbContext
{
    public DbSet<Person> People { get; set; }
    public DbSet<School> Schools { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<School>().HasMany(s => s.Students).WithOne(s => s.School);
    }
}

public class Person
{
    public int Id { get; set; }
    public string Name { get; set; }
}

public class Student : Person
{
    public School School { get; set; }
}

public class School
{
    public int Id { get; set; }
    public string Name { get; set; }

    public List<Student> Students { get; set; }
}
```

<span data-ttu-id="5c427-163">Zawartość `School` nawigacji dla wszystkich osób, które są uczniami, może być eagerly załadowana przy użyciu wielu wzorców:</span><span class="sxs-lookup"><span data-stu-id="5c427-163">Contents of `School` navigation of all People who are Students can be eagerly loaded using a number of patterns:</span></span>

* <span data-ttu-id="5c427-164">Używanie rzutowania</span><span class="sxs-lookup"><span data-stu-id="5c427-164">using cast</span></span>

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* <span data-ttu-id="5c427-165">`as`operator using</span><span class="sxs-lookup"><span data-stu-id="5c427-165">using `as` operator</span></span>

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* <span data-ttu-id="5c427-166">Użycie przeciążenia `Include` , które pobiera parametr typu`string`</span><span class="sxs-lookup"><span data-stu-id="5c427-166">using overload of `Include` that takes parameter of type `string`</span></span>

  ```csharp
  context.People.Include("School").ToList()
  ```

## <a name="explicit-loading"></a><span data-ttu-id="5c427-167">Jawne ładowanie</span><span class="sxs-lookup"><span data-stu-id="5c427-167">Explicit loading</span></span>

<span data-ttu-id="5c427-168">Można jawnie załadować właściwość nawigacji za pośrednictwem `DbContext.Entry(...)` interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="5c427-168">You can explicitly load a navigation property via the `DbContext.Entry(...)` API.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#Eager)]

<span data-ttu-id="5c427-169">Możesz również jawnie załadować właściwość nawigacji przez wykonanie oddzielnego zapytania, które zwraca powiązane jednostki.</span><span class="sxs-lookup"><span data-stu-id="5c427-169">You can also explicitly load a navigation property by executing a separate query that returns the related entities.</span></span> <span data-ttu-id="5c427-170">Jeśli funkcja śledzenia zmian jest włączona, podczas ładowania jednostki EF Core automatycznie ustawił właściwości nawigacji nowo załadowanego jednostkę, aby odwołać się do wszystkich jednostek, które zostały już załadowane, i ustawić właściwości nawigacji już załadowanych jednostek, aby odwołać się do nowo załadowanego obiektu.</span><span class="sxs-lookup"><span data-stu-id="5c427-170">If change tracking is enabled, then when loading an entity, EF Core will automatically set the navigation properties of the newly-loaded entitiy to refer to any entities already loaded, and set the navigation properties of the already-loaded entities to refer to the newly-loaded entity.</span></span>

### <a name="querying-related-entities"></a><span data-ttu-id="5c427-171">Wykonywanie zapytania dotyczącego powiązanych jednostek</span><span class="sxs-lookup"><span data-stu-id="5c427-171">Querying related entities</span></span>

<span data-ttu-id="5c427-172">Możesz również uzyskać zapytanie LINQ, które reprezentuje zawartość właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="5c427-172">You can also get a LINQ query that represents the contents of a navigation property.</span></span>

<span data-ttu-id="5c427-173">Umożliwia to wykonywanie takich czynności, jak uruchamianie agregacji operatora na powiązanych jednostkach bez ładowania ich do pamięci.</span><span class="sxs-lookup"><span data-stu-id="5c427-173">This allows you to do things such as running an aggregate operator over the related entities without loading them into memory.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

<span data-ttu-id="5c427-174">Można również filtrować powiązane jednostki, które są ładowane do pamięci.</span><span class="sxs-lookup"><span data-stu-id="5c427-174">You can also filter which related entities are loaded into memory.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryFiltered)]

## <a name="lazy-loading"></a><span data-ttu-id="5c427-175">Ładowanie z opóźnieniem</span><span class="sxs-lookup"><span data-stu-id="5c427-175">Lazy loading</span></span>

<span data-ttu-id="5c427-176">Najprostszym sposobem użycia ładowania z opóźnieniem jest zainstalowanie pakietu [Microsoft. EntityFrameworkCore. proxy](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) i włączenie go z wywołaniem do `UseLazyLoadingProxies` .</span><span class="sxs-lookup"><span data-stu-id="5c427-176">The simplest way to use lazy-loading is by installing the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package and enabling it with a call to `UseLazyLoadingProxies`.</span></span> <span data-ttu-id="5c427-177">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="5c427-177">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseLazyLoadingProxies()
        .UseSqlServer(myConnectionString);
```

<span data-ttu-id="5c427-178">Lub w przypadku korzystania z AddDbContext:</span><span class="sxs-lookup"><span data-stu-id="5c427-178">Or when using AddDbContext:</span></span>

```csharp
.AddDbContext<BloggingContext>(
    b => b.UseLazyLoadingProxies()
          .UseSqlServer(myConnectionString));
```

<span data-ttu-id="5c427-179">EF Core następnie włącza ładowanie z opóźnieniem dla każdej właściwości nawigacji, która może zostać przesłonięta — to oznacza, że musi być `virtual` i na klasie, z której można dziedziczyć.</span><span class="sxs-lookup"><span data-stu-id="5c427-179">EF Core will then enable lazy loading for any navigation property that can be overridden--that is, it must be `virtual` and on a class that can be inherited from.</span></span> <span data-ttu-id="5c427-180">Na przykład w następujących jednostkach `Post.Blog` `Blog.Posts` właściwości i nawigacji zostaną załadowane z opóźnieniem.</span><span class="sxs-lookup"><span data-stu-id="5c427-180">For example, in the following entities, the `Post.Blog` and `Blog.Posts` navigation properties will be lazy-loaded.</span></span>

```csharp
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }

    public virtual ICollection<Post> Posts { get; set; }
}

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public virtual Blog Blog { get; set; }
}
```

### <a name="lazy-loading-without-proxies"></a><span data-ttu-id="5c427-181">Ładowanie z opóźnieniem bez serwerów proxy</span><span class="sxs-lookup"><span data-stu-id="5c427-181">Lazy loading without proxies</span></span>

<span data-ttu-id="5c427-182">Ładowanie serwerów proxy z opóźnieniem, które działają przez wstrzyknięcie `ILazyLoader` usługi do jednostki, zgodnie z opisem w [konstruktorach typu jednostki](../modeling/constructors.md).</span><span class="sxs-lookup"><span data-stu-id="5c427-182">Lazy-loading proxies work by injecting the `ILazyLoader` service into an entity, as described in [Entity Type Constructors](../modeling/constructors.md).</span></span> <span data-ttu-id="5c427-183">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="5c427-183">For example:</span></span>

```csharp
public class Blog
{
    private ICollection<Post> _posts;

    public Blog()
    {
    }

    private Blog(ILazyLoader lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private ILazyLoader LazyLoader { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts
    {
        get => LazyLoader.Load(this, ref _posts);
        set => _posts = value;
    }
}

public class Post
{
    private Blog _blog;

    public Post()
    {
    }

    private Post(ILazyLoader lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private ILazyLoader LazyLoader { get; set; }

    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog
    {
        get => LazyLoader.Load(this, ref _blog);
        set => _blog = value;
    }
}
```

<span data-ttu-id="5c427-184">Nie wymaga to dziedziczenia typów jednostek ani właściwości nawigacji, które mają być wirtualne, i umożliwia wystąpienia jednostki utworzone za pomocą `new` do ładowania opóźnionego po dołączeniu do kontekstu.</span><span class="sxs-lookup"><span data-stu-id="5c427-184">This doesn't require entity types to be inherited from or navigation properties to be virtual, and allows entity instances created with `new` to lazy-load once attached to a context.</span></span> <span data-ttu-id="5c427-185">Jednak wymaga odwołania do `ILazyLoader` usługi, która jest zdefiniowana w pakiecie [Microsoft. EntityFrameworkCore. Abstracts](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/) .</span><span class="sxs-lookup"><span data-stu-id="5c427-185">However, it requires a reference to the `ILazyLoader` service, which is defined in the [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/) package.</span></span> <span data-ttu-id="5c427-186">Ten pakiet zawiera minimalny zestaw typów, dzięki czemu w zależności od tego ma być bardzo niewielki wpływ.</span><span class="sxs-lookup"><span data-stu-id="5c427-186">This package contains a minimal set of types so that there is very little impact in depending on it.</span></span> <span data-ttu-id="5c427-187">Jednak aby całkowicie uniknąć w zależności od dowolnego EF Core pakietów w typach jednostek, można wstrzyknąć `ILazyLoader.Load` metodę jako delegata.</span><span class="sxs-lookup"><span data-stu-id="5c427-187">However, to completely avoid depending on any EF Core packages in the entity types, it is possible to inject the `ILazyLoader.Load` method as a delegate.</span></span> <span data-ttu-id="5c427-188">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="5c427-188">For example:</span></span>

```csharp
public class Blog
{
    private ICollection<Post> _posts;

    public Blog()
    {
    }

    private Blog(Action<object, string> lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private Action<object, string> LazyLoader { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts
    {
        get => LazyLoader.Load(this, ref _posts);
        set => _posts = value;
    }
}

public class Post
{
    private Blog _blog;

    public Post()
    {
    }

    private Post(Action<object, string> lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private Action<object, string> LazyLoader { get; set; }

    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog
    {
        get => LazyLoader.Load(this, ref _blog);
        set => _blog = value;
    }
}
```

<span data-ttu-id="5c427-189">W powyższym kodzie użyto `Load` metody rozszerzenia, aby użyć delegata:</span><span class="sxs-lookup"><span data-stu-id="5c427-189">The code above uses a `Load` extension method to make using the delegate a bit cleaner:</span></span>

```csharp
public static class PocoLoadingExtensions
{
    public static TRelated Load<TRelated>(
        this Action<object, string> loader,
        object entity,
        ref TRelated navigationField,
        [CallerMemberName] string navigationName = null)
        where TRelated : class
    {
        loader?.Invoke(entity, navigationName);

        return navigationField;
    }
}
```

> [!NOTE]  
> <span data-ttu-id="5c427-190">Parametr konstruktora dla delegata ładowania z opóźnieniem musi mieć nazwę "lazyLoader".</span><span class="sxs-lookup"><span data-stu-id="5c427-190">The constructor parameter for the lazy-loading delegate must be called "lazyLoader".</span></span> <span data-ttu-id="5c427-191">Konfiguracja służąca do używania innej nazwy niż jest planowana dla przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="5c427-191">Configuration to use a different name than this is planned for a future release.</span></span>

## <a name="related-data-and-serialization"></a><span data-ttu-id="5c427-192">Powiązane dane i Serializacja</span><span class="sxs-lookup"><span data-stu-id="5c427-192">Related data and serialization</span></span>

<span data-ttu-id="5c427-193">Ponieważ EF Core automatycznie naprawia właściwości nawigacji, można zakończyć z cyklami na grafie obiektów.</span><span class="sxs-lookup"><span data-stu-id="5c427-193">Because EF Core will automatically fix-up navigation properties, you can end up with cycles in your object graph.</span></span> <span data-ttu-id="5c427-194">Na przykład załadowanie blogu i jego powiązanych wpisów spowoduje powstanie obiektu blogu, który odwołuje się do kolekcji wpisów.</span><span class="sxs-lookup"><span data-stu-id="5c427-194">For example, loading a blog and its related posts will result in a blog object that references a collection of posts.</span></span> <span data-ttu-id="5c427-195">Każdy z tych wpisów będzie miał odwołanie z powrotem do blogu.</span><span class="sxs-lookup"><span data-stu-id="5c427-195">Each of those posts will have a reference back to the blog.</span></span>

<span data-ttu-id="5c427-196">Niektóre platformy serializacji nie zezwalają na takie cykle.</span><span class="sxs-lookup"><span data-stu-id="5c427-196">Some serialization frameworks do not allow such cycles.</span></span> <span data-ttu-id="5c427-197">Na przykład Json.NET zgłosi następujący wyjątek w przypadku napotkania cyklu.</span><span class="sxs-lookup"><span data-stu-id="5c427-197">For example, Json.NET will throw the following exception if a cycle is encountered.</span></span>

> <span data-ttu-id="5c427-198">Newtonsoft.Json.Jsonserializationexception: wykryto pętlę odwołującą się do właściwości "blog" z typem "WebApplication. MODELES. bloga".</span><span class="sxs-lookup"><span data-stu-id="5c427-198">Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'.</span></span>

<span data-ttu-id="5c427-199">Jeśli używasz ASP.NET Core, możesz skonfigurować Json.NET do ignorowania cykli znalezionych w grafie obiektów.</span><span class="sxs-lookup"><span data-stu-id="5c427-199">If you are using ASP.NET Core, you can configure Json.NET to ignore cycles that it finds in the object graph.</span></span> <span data-ttu-id="5c427-200">Jest to realizowane za pomocą `ConfigureServices(...)` metody w `Startup.cs` .</span><span class="sxs-lookup"><span data-stu-id="5c427-200">This is done in the `ConfigureServices(...)` method in `Startup.cs`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddMvc()
        .AddJsonOptions(
            options => options.SerializerSettings.ReferenceLoopHandling = Newtonsoft.Json.ReferenceLoopHandling.Ignore
        );

    ...
}
```

<span data-ttu-id="5c427-201">Kolejną alternatywą jest dekorować jednej z właściwości nawigacji z `[JsonIgnore]` atrybutem, który instruuje JSON.NET, aby nie przechodzą tej właściwości nawigacji podczas serializacji.</span><span class="sxs-lookup"><span data-stu-id="5c427-201">Another alternative is to decorate one of the navigation properties with the `[JsonIgnore]` attribute, which instructs Json.NET to not traverse that navigation property while serializing.</span></span>
