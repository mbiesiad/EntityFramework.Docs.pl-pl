---
title: Ładowanie powiązanych danych — EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: f9fb64e2-6699-4d70-a773-592918c04c19
uid: core/querying/related-data
ms.openlocfilehash: bfd6e161ed7f7bf96e61946f94c8eeadd24a72f5
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434191"
---
# <a name="loading-related-data"></a><span data-ttu-id="ed119-102">Ładowanie powiązanych danych</span><span class="sxs-lookup"><span data-stu-id="ed119-102">Loading Related Data</span></span>

<span data-ttu-id="ed119-103">Entity Framework Core umożliwia użycie właściwości nawigacji w modelu do ładowania powiązanych jednostek.</span><span class="sxs-lookup"><span data-stu-id="ed119-103">Entity Framework Core allows you to use the navigation properties in your model to load related entities.</span></span> <span data-ttu-id="ed119-104">Istnieją trzy typowe wzorce O/RM używane do ładowania powiązanych danych.</span><span class="sxs-lookup"><span data-stu-id="ed119-104">There are three common O/RM patterns used to load related data.</span></span>

* <span data-ttu-id="ed119-105">**Wczesne ładowanie** oznacza, że powiązane dane są ładowane z bazy danych jako część kwerendy początkowej.</span><span class="sxs-lookup"><span data-stu-id="ed119-105">**Eager loading** means that the related data is loaded from the database as part of the initial query.</span></span>
* <span data-ttu-id="ed119-106">**Jawne ładowanie** oznacza, że powiązane dane są jawnie ładowane z bazy danych w późniejszym czasie.</span><span class="sxs-lookup"><span data-stu-id="ed119-106">**Explicit loading** means that the related data is explicitly loaded from the database at a later time.</span></span>
* <span data-ttu-id="ed119-107">**Powolne ładowanie** oznacza, że powiązane dane są ładowane w sposób przezroczysty z bazy danych, gdy dostęp do właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="ed119-107">**Lazy loading** means that the related data is transparently loaded from the database when the navigation property is accessed.</span></span>

> [!TIP]  
> <span data-ttu-id="ed119-108">Możesz wyświetlić [ten](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) przykład artykułu na GitHub.</span><span class="sxs-lookup"><span data-stu-id="ed119-108">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="eager-loading"></a><span data-ttu-id="ed119-109">Gorliwy załadunek</span><span class="sxs-lookup"><span data-stu-id="ed119-109">Eager loading</span></span>

<span data-ttu-id="ed119-110">Za pomocą `Include` tej metody można określić powiązane dane, które mają zostać uwzględnione w wynikach kwerendy.</span><span class="sxs-lookup"><span data-stu-id="ed119-110">You can use the `Include` method to specify related data to be included in query results.</span></span> <span data-ttu-id="ed119-111">W poniższym przykładzie blogi, które są zwracane w wynikach będą miały ich `Posts` właściwości wypełnione powiązanych wpisów.</span><span class="sxs-lookup"><span data-stu-id="ed119-111">In the following example, the blogs that are returned in the results will have their `Posts` property populated with the related posts.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleInclude)]

> [!TIP]  
> <span data-ttu-id="ed119-112">Entity Framework Core automatycznie naprawi właściwości nawigacji do innych jednostek, które zostały wcześniej załadowane do wystąpienia kontekstu.</span><span class="sxs-lookup"><span data-stu-id="ed119-112">Entity Framework Core will automatically fix-up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="ed119-113">Więc nawet jeśli nie jawnie dołączyć dane dla właściwości nawigacji, właściwość może nadal być wypełniona, jeśli niektóre lub wszystkie powiązane jednostki zostały wcześniej załadowane.</span><span class="sxs-lookup"><span data-stu-id="ed119-113">So even if you don't explicitly include the data for a navigation property, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

<span data-ttu-id="ed119-114">W jednym zapytaniu można uwzględnić powiązane dane z wielu relacji.</span><span class="sxs-lookup"><span data-stu-id="ed119-114">You can include related data from multiple relationships in a single query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleIncludes)]

### <a name="including-multiple-levels"></a><span data-ttu-id="ed119-115">W tym wiele poziomów</span><span class="sxs-lookup"><span data-stu-id="ed119-115">Including multiple levels</span></span>

<span data-ttu-id="ed119-116">Można przejść do szczegółów relacji, aby uwzględnić `ThenInclude` wiele poziomów powiązanych danych przy użyciu metody.</span><span class="sxs-lookup"><span data-stu-id="ed119-116">You can drill down through relationships to include multiple levels of related data using the `ThenInclude` method.</span></span> <span data-ttu-id="ed119-117">Poniższy przykład ładuje wszystkie blogi, powiązane z nimi posty i autora każdego posta.</span><span class="sxs-lookup"><span data-stu-id="ed119-117">The following example loads all blogs, their related posts, and the author of each post.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleThenInclude)]

<span data-ttu-id="ed119-118">Można wysuń wiele wywołań, aby `ThenInclude` kontynuować, w tym dalsze poziomy powiązanych danych.</span><span class="sxs-lookup"><span data-stu-id="ed119-118">You can chain multiple calls to `ThenInclude` to continue including further levels of related data.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleThenIncludes)]

<span data-ttu-id="ed119-119">Można połączyć to wszystko, aby uwzględnić powiązane dane z wielu poziomów i wiele katalogów głównych w tej samej kwerendzie.</span><span class="sxs-lookup"><span data-stu-id="ed119-119">You can combine all of this to include related data from multiple levels and multiple roots in the same query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#IncludeTree)]

<span data-ttu-id="ed119-120">Można dołączyć wiele powiązanych jednostek dla jednej z jednostek, która jest uwzględniona.</span><span class="sxs-lookup"><span data-stu-id="ed119-120">You may want to include multiple related entities for one of the entities that is being included.</span></span> <span data-ttu-id="ed119-121">Na przykład podczas `Blogs`wykonywania zapytań `Posts` dołącza się, `Author` a `Tags` następnie `Posts`chcesz dołączyć zarówno plik .</span><span class="sxs-lookup"><span data-stu-id="ed119-121">For example, when querying `Blogs`, you include `Posts` and then want to include both the `Author` and `Tags` of the `Posts`.</span></span> <span data-ttu-id="ed119-122">Aby to zrobić, należy określić każdą ścieżkę dołączania, zaczynając od katalogu głównego.</span><span class="sxs-lookup"><span data-stu-id="ed119-122">To do this, you need to specify each include path starting at the root.</span></span> <span data-ttu-id="ed119-123">Na przykład `Blog -> Posts -> Author` `Blog -> Posts -> Tags`i .</span><span class="sxs-lookup"><span data-stu-id="ed119-123">For example, `Blog -> Posts -> Author` and `Blog -> Posts -> Tags`.</span></span> <span data-ttu-id="ed119-124">Nie oznacza to, że otrzymasz zbędne sprzężenia; w większości przypadków EF skonsoliduje sprzężenia podczas generowania języka SQL.</span><span class="sxs-lookup"><span data-stu-id="ed119-124">This does not mean you will get redundant joins; in most cases, EF will consolidate the joins when generating SQL.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludes)]

> [!CAUTION]
> <span data-ttu-id="ed119-125">Od wersji 3.0.0, każdy `Include` spowoduje dodatkowe JOIN do dodania do zapytań SQL produkowanych przez dostawców relacyjnych, podczas gdy poprzednie wersje generowane dodatkowe zapytania SQL.</span><span class="sxs-lookup"><span data-stu-id="ed119-125">Since version 3.0.0, each `Include` will cause an additional JOIN to be added to SQL queries produced by relational providers, whereas previous versions generated additional SQL queries.</span></span> <span data-ttu-id="ed119-126">Może to znacznie zmienić wydajność zapytań, na lepsze lub gorsze.</span><span class="sxs-lookup"><span data-stu-id="ed119-126">This can significantly change the performance of your queries, for better or worse.</span></span> <span data-ttu-id="ed119-127">W szczególności zapytania LINQ z niezwykle dużą `Include` liczbą operatorów może być konieczne w podziale na wiele oddzielnych zapytań LINQ w celu uniknięcia problemu rozłąki kartezjańskiej.</span><span class="sxs-lookup"><span data-stu-id="ed119-127">In particular, LINQ queries with an exceedingly high number of `Include` operators may need to be broken down into multiple separate LINQ queries in order to avoid the cartesian explosion problem.</span></span>

### <a name="filtered-include"></a><span data-ttu-id="ed119-128">Filtrowane include</span><span class="sxs-lookup"><span data-stu-id="ed119-128">Filtered include</span></span>

> [!NOTE]
> <span data-ttu-id="ed119-129">Ta funkcja jest wprowadzana w EF Core 5.0.</span><span class="sxs-lookup"><span data-stu-id="ed119-129">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="ed119-130">Podczas stosowania Include do ładowania powiązanych danych, można zastosować niektóre operacje wyliczalne na dołączonej nawigacji kolekcji, co pozwala na filtrowanie i sortowanie wyników.</span><span class="sxs-lookup"><span data-stu-id="ed119-130">When applying Include to load related data, you can apply certain enumerable operations on the included collection navigation, which allows for filtering and sorting of the results.</span></span>

<span data-ttu-id="ed119-131">Obsługiwane `Where`operacje to: `OrderBy` `OrderByDescending`, `ThenBy` `ThenByDescending`, `Skip`, `Take`, , , i .</span><span class="sxs-lookup"><span data-stu-id="ed119-131">Supported operations are: `Where`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `Skip`, and `Take`.</span></span>

<span data-ttu-id="ed119-132">Takie operacje powinny być stosowane na nawigacji kolekcji w lambda przekazywane do Include metody, jak pokazano w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="ed119-132">Such operations should be applied on the collection navigation in the lambda passed to the Include method, as shown in example below:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#FilteredInclude)]

<span data-ttu-id="ed119-133">Każda dołączona nawigacja umożliwia tylko jeden unikatowy zestaw operacji filtrowania.</span><span class="sxs-lookup"><span data-stu-id="ed119-133">Each included navigation allows only one unique set of filter operations.</span></span> <span data-ttu-id="ed119-134">W przypadkach, gdy wiele operacji Include są`blog.Posts` stosowane dla danej nawigacji kolekcji (w poniższych przykładach), operacje filtrowania można określić tylko na jednym z nich:</span><span class="sxs-lookup"><span data-stu-id="ed119-134">In cases where multiple Include operations are applied for a given collection navigation (`blog.Posts` in the examples below), filter operations can only be specified on one of them:</span></span> 

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered1)]

<span data-ttu-id="ed119-135">Alternatywnie, identyczne operacje mogą być stosowane dla każdej nawigacji, która jest uwzględniona wiele razy:</span><span class="sxs-lookup"><span data-stu-id="ed119-135">Alternatively, identical operations can be applied for each navigation that is included multiple times:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered2)]

### <a name="include-on-derived-types"></a><span data-ttu-id="ed119-136">Uwzględnij typy pochodne</span><span class="sxs-lookup"><span data-stu-id="ed119-136">Include on derived types</span></span>

<span data-ttu-id="ed119-137">Można uwzględnić powiązane dane z nawigacji zdefiniowanych `Include` tylko `ThenInclude`dla typu pochodnego przy użyciu i .</span><span class="sxs-lookup"><span data-stu-id="ed119-137">You can include related data from navigations defined only on a derived type using `Include` and `ThenInclude`.</span></span>

<span data-ttu-id="ed119-138">Biorąc pod uwagę następujący model:</span><span class="sxs-lookup"><span data-stu-id="ed119-138">Given the following model:</span></span>

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

<span data-ttu-id="ed119-139">Zawartość `School` nawigacji wszystkich osób, które są Studentami, może być chętnie ładowana za pomocą wielu wzorców:</span><span class="sxs-lookup"><span data-stu-id="ed119-139">Contents of `School` navigation of all People who are Students can be eagerly loaded using a number of patterns:</span></span>

* <span data-ttu-id="ed119-140">za pomocą odlewana</span><span class="sxs-lookup"><span data-stu-id="ed119-140">using cast</span></span>

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* <span data-ttu-id="ed119-141">korzystanie `as` z operatora</span><span class="sxs-lookup"><span data-stu-id="ed119-141">using `as` operator</span></span>

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* <span data-ttu-id="ed119-142">za pomocą `Include` przeciążenia, że trwa parametr typu`string`</span><span class="sxs-lookup"><span data-stu-id="ed119-142">using overload of `Include` that takes parameter of type `string`</span></span>

  ```csharp
  context.People.Include("School").ToList()
  ```

## <a name="explicit-loading"></a><span data-ttu-id="ed119-143">Jawne ładowanie</span><span class="sxs-lookup"><span data-stu-id="ed119-143">Explicit loading</span></span>

<span data-ttu-id="ed119-144">Można jawnie załadować właściwość `DbContext.Entry(...)` nawigacji za pośrednictwem interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="ed119-144">You can explicitly load a navigation property via the `DbContext.Entry(...)` API.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#Eager)]

<span data-ttu-id="ed119-145">Można również jawnie załadować właściwość nawigacji, wykonując oddzielne zapytanie, które zwraca encje pokrewne.</span><span class="sxs-lookup"><span data-stu-id="ed119-145">You can also explicitly load a navigation property by executing a separate query that returns the related entities.</span></span> <span data-ttu-id="ed119-146">Jeśli śledzenie zmian jest włączone, a następnie podczas ładowania jednostki EF Core automatycznie ustawi właściwości nawigacji nowo załadowanej jednostki, aby odwoływać się do wszystkich jednostek już załadowanych i ustawić właściwości nawigacji już załadowanych jednostek, aby odwołać się do nowo załadowanej jednostki.</span><span class="sxs-lookup"><span data-stu-id="ed119-146">If change tracking is enabled, then when loading an entity, EF Core will automatically set the navigation properties of the newly-loaded entitiy to refer to any entities already loaded, and set the navigation properties of the already-loaded entities to refer to the newly-loaded entity.</span></span>

### <a name="querying-related-entities"></a><span data-ttu-id="ed119-147">Wykonywanie zapytań o encje pokrewne</span><span class="sxs-lookup"><span data-stu-id="ed119-147">Querying related entities</span></span>

<span data-ttu-id="ed119-148">Można również uzyskać kwerendę LINQ, która reprezentuje zawartość właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="ed119-148">You can also get a LINQ query that represents the contents of a navigation property.</span></span>

<span data-ttu-id="ed119-149">Dzięki temu można wykonać takie czynności, jak uruchamianie operatora agregacji za sprawą powiązanych jednostek bez ładowania ich do pamięci.</span><span class="sxs-lookup"><span data-stu-id="ed119-149">This allows you to do things such as running an aggregate operator over the related entities without loading them into memory.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

<span data-ttu-id="ed119-150">Można również filtrować, które powiązane jednostki są ładowane do pamięci.</span><span class="sxs-lookup"><span data-stu-id="ed119-150">You can also filter which related entities are loaded into memory.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryFiltered)]

## <a name="lazy-loading"></a><span data-ttu-id="ed119-151">Z opóźnieniem załadunku</span><span class="sxs-lookup"><span data-stu-id="ed119-151">Lazy loading</span></span>

<span data-ttu-id="ed119-152">Najprostszym sposobem korzystania z ładowania z opóźnieniem jest zainstalowanie pakietu [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) i włączenie go za pomocą połączenia z `UseLazyLoadingProxies`programem .</span><span class="sxs-lookup"><span data-stu-id="ed119-152">The simplest way to use lazy-loading is by installing the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package and enabling it with a call to `UseLazyLoadingProxies`.</span></span> <span data-ttu-id="ed119-153">Przykład:</span><span class="sxs-lookup"><span data-stu-id="ed119-153">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseLazyLoadingProxies()
        .UseSqlServer(myConnectionString);
```

<span data-ttu-id="ed119-154">Lub podczas korzystania z AddDbContext:</span><span class="sxs-lookup"><span data-stu-id="ed119-154">Or when using AddDbContext:</span></span>

```csharp
.AddDbContext<BloggingContext>(
    b => b.UseLazyLoadingProxies()
          .UseSqlServer(myConnectionString));
```

<span data-ttu-id="ed119-155">EF Core włączy powolne ładowanie dla dowolnej właściwości nawigacji, która może zostać `virtual` zastąpiona — oznacza, że musi być i na klasę, która może być dziedziczona.</span><span class="sxs-lookup"><span data-stu-id="ed119-155">EF Core will then enable lazy loading for any navigation property that can be overridden--that is, it must be `virtual` and on a class that can be inherited from.</span></span> <span data-ttu-id="ed119-156">Na przykład w następujących jednostkach `Post.Blog` `Blog.Posts` właściwości i nawigacji będą ładowane z opóźnieniem.</span><span class="sxs-lookup"><span data-stu-id="ed119-156">For example, in the following entities, the `Post.Blog` and `Blog.Posts` navigation properties will be lazy-loaded.</span></span>

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

### <a name="lazy-loading-without-proxies"></a><span data-ttu-id="ed119-157">Leniwe ładowanie bez serwerów proxy</span><span class="sxs-lookup"><span data-stu-id="ed119-157">Lazy loading without proxies</span></span>

<span data-ttu-id="ed119-158">Serwery proxy ładowania z opóźnieniem `ILazyLoader` działają przez wstrzyknięcie usługi do jednostki, zgodnie z opisem w [konstruktorach typu jednostki](../modeling/constructors.md).</span><span class="sxs-lookup"><span data-stu-id="ed119-158">Lazy-loading proxies work by injecting the `ILazyLoader` service into an entity, as described in [Entity Type Constructors](../modeling/constructors.md).</span></span> <span data-ttu-id="ed119-159">Przykład:</span><span class="sxs-lookup"><span data-stu-id="ed119-159">For example:</span></span>

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

<span data-ttu-id="ed119-160">Nie wymaga to, aby typy jednostek były dziedziczone z lub właściwości nawigacji, aby być wirtualne i umożliwia wystąpienia jednostek utworzone z `new` opóźnieniem ładowania po dołączeniu do kontekstu.</span><span class="sxs-lookup"><span data-stu-id="ed119-160">This doesn't require entity types to be inherited from or navigation properties to be virtual, and allows entity instances created with `new` to lazy-load once attached to a context.</span></span> <span data-ttu-id="ed119-161">Jednak wymaga odwołania do `ILazyLoader` usługi, która jest zdefiniowana w pakiecie [Microsoft.EntityFrameworkCore.Abstractions.](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/)</span><span class="sxs-lookup"><span data-stu-id="ed119-161">However, it requires a reference to the `ILazyLoader` service, which is defined in the [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/) package.</span></span> <span data-ttu-id="ed119-162">Ten pakiet zawiera minimalny zestaw typów, dzięki czemu jest bardzo mały wpływ w zależności od niego.</span><span class="sxs-lookup"><span data-stu-id="ed119-162">This package contains a minimal set of types so that there is very little impact in depending on it.</span></span> <span data-ttu-id="ed119-163">Jednak aby całkowicie uniknąć w zależności od wszystkich pakietów EF Core `ILazyLoader.Load` w typach jednostek, jest możliwe, aby wstrzyknąć metodę jako delegata.</span><span class="sxs-lookup"><span data-stu-id="ed119-163">However, to completely avoid depending on any EF Core packages in the entity types, it is possible to inject the `ILazyLoader.Load` method as a delegate.</span></span> <span data-ttu-id="ed119-164">Przykład:</span><span class="sxs-lookup"><span data-stu-id="ed119-164">For example:</span></span>

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

<span data-ttu-id="ed119-165">Powyższy kod używa `Load` metody rozszerzenia, aby przy użyciu delegata nieco czystsze:</span><span class="sxs-lookup"><span data-stu-id="ed119-165">The code above uses a `Load` extension method to make using the delegate a bit cleaner:</span></span>

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
> <span data-ttu-id="ed119-166">Parametr konstruktora dla delegata z opóźnieniem musi być nazywany "lazyLoader".</span><span class="sxs-lookup"><span data-stu-id="ed119-166">The constructor parameter for the lazy-loading delegate must be called "lazyLoader".</span></span> <span data-ttu-id="ed119-167">Konfiguracja, aby użyć innej nazwy niż to jest planowane dla przyszłej wersji.</span><span class="sxs-lookup"><span data-stu-id="ed119-167">Configuration to use a different name than this is planned for a future release.</span></span>

## <a name="related-data-and-serialization"></a><span data-ttu-id="ed119-168">Powiązane dane i serializacja</span><span class="sxs-lookup"><span data-stu-id="ed119-168">Related data and serialization</span></span>

<span data-ttu-id="ed119-169">Ponieważ EF Core automatycznie naprawi właściwości nawigacji, można skończyć z cykli na wykresie obiektu.</span><span class="sxs-lookup"><span data-stu-id="ed119-169">Because EF Core will automatically fix-up navigation properties, you can end up with cycles in your object graph.</span></span> <span data-ttu-id="ed119-170">Na przykład załadowanie bloga i powiązanych z nim wpisów spowoduje, że obiekt blogu odwołuje się do kolekcji wpisów.</span><span class="sxs-lookup"><span data-stu-id="ed119-170">For example, loading a blog and its related posts will result in a blog object that references a collection of posts.</span></span> <span data-ttu-id="ed119-171">Każdy z tych postów będzie miał odniesienie z powrotem do bloga.</span><span class="sxs-lookup"><span data-stu-id="ed119-171">Each of those posts will have a reference back to the blog.</span></span>

<span data-ttu-id="ed119-172">Niektóre struktury serializacji nie zezwalają na takie cykle.</span><span class="sxs-lookup"><span data-stu-id="ed119-172">Some serialization frameworks do not allow such cycles.</span></span> <span data-ttu-id="ed119-173">Na przykład Json.NET zda następujący wyjątek, jeśli wystąpi cykl.</span><span class="sxs-lookup"><span data-stu-id="ed119-173">For example, Json.NET will throw the following exception if a cycle is encountered.</span></span>

> <span data-ttu-id="ed119-174">Newtonsoft.Json.JsonSerializationException: Self referenceing pętli wykryte dla właściwości "Blog" z typem "MyApplication.Models.Blog".</span><span class="sxs-lookup"><span data-stu-id="ed119-174">Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'.</span></span>

<span data-ttu-id="ed119-175">Jeśli używasz ASP.NET Core, można skonfigurować Json.NET, aby ignorować cykle, które znajduje się na wykresie obiektu.</span><span class="sxs-lookup"><span data-stu-id="ed119-175">If you are using ASP.NET Core, you can configure Json.NET to ignore cycles that it finds in the object graph.</span></span> <span data-ttu-id="ed119-176">Odbywa się to `ConfigureServices(...)` w `Startup.cs`metodzie w .</span><span class="sxs-lookup"><span data-stu-id="ed119-176">This is done in the `ConfigureServices(...)` method in `Startup.cs`.</span></span>

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

<span data-ttu-id="ed119-177">Inną alternatywą jest ozdobać jedną z `[JsonIgnore]` właściwości nawigacji z atrybutem, który nakazuje Json.NET, aby nie przechodzić przez tę właściwość nawigacji podczas serializacji.</span><span class="sxs-lookup"><span data-stu-id="ed119-177">Another alternative is to decorate one of the navigation properties with the `[JsonIgnore]` attribute, which instructs Json.NET to not traverse that navigation property while serializing.</span></span>
