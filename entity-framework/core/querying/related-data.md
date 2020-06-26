---
title: Ładowanie powiązanych danych — EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: f9fb64e2-6699-4d70-a773-592918c04c19
uid: core/querying/related-data
ms.openlocfilehash: 86b9d08377ea8295b746e5f0217a408edcfe1517
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370476"
---
# <a name="loading-related-data"></a>Ładowanie powiązanych danych

Entity Framework Core umożliwia używanie właściwości nawigacji w modelu do ładowania powiązanych jednostek. Istnieją trzy typowe wzorce O/RM używane do ładowania powiązanych danych.

* **Ładowanie eager** oznacza, że powiązane dane są ładowane z bazy danych w ramach wstępnego zapytania.
* **Jawne ładowanie** oznacza, że powiązane dane są jawnie ładowane z bazy danych w późniejszym czasie.
* **Ładowanie z opóźnieniem** oznacza, że powiązane dane są w sposób niewidoczny do załadowania z bazy danych podczas uzyskiwania dostępu do właściwości nawigacji.

> [!TIP]  
> [Przykład](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) tego artykułu można wyświetlić w witrynie GitHub.

## <a name="eager-loading"></a>Ładowanie eager

Możesz użyć metody, `Include` Aby określić powiązane dane, które mają być uwzględnione w wynikach zapytania. W poniższym przykładzie w blogach, które są zwracane w wynikach, zostanie `Posts` wypełniona ich właściwość wraz z powiązanymi wpisami.

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleInclude)]

> [!TIP]  
> Entity Framework Core automatycznie naprawia właściwości nawigacji do wszystkich innych jednostek, które zostały wcześniej załadowane do wystąpienia kontekstu. Dlatego nawet jeśli nie dołączysz jawnie danych dla właściwości nawigacji, właściwość może nadal zostać wypełniona, jeśli niektóre lub wszystkie powiązane jednostki zostały wcześniej załadowane.

Można uwzględnić powiązane dane z wielu relacji w pojedynczym zapytaniu.

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleIncludes)]

### <a name="including-multiple-levels"></a>Uwzględnienie wielu poziomów

Możesz przejść do szczegółów relacji, aby dołączyć wiele poziomów powiązanych danych przy użyciu `ThenInclude` metody. Poniższy przykład ładuje wszystkie blogi, ich powiązane wpisy i autora każdego wpisu.

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleThenInclude)]

Można połączyć wiele wywołań, aby `ThenInclude` kontynuować, włączając dalsze poziomy pokrewnych danych.

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleThenIncludes)]

Możesz połączyć wszystkie te elementy, aby uwzględnić powiązane dane z wielu poziomów i wiele elementów głównych w tym samym zapytaniu.

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#IncludeTree)]

Możesz chcieć dołączyć wiele powiązanych jednostek dla jednej z dołączanych jednostek. Na przykład, podczas wykonywania zapytania `Blogs` , należy dołączyć `Posts` zarówno, jak `Author` i `Tags` `Posts` . W tym celu należy określić wszystkie ścieżki dołączania, zaczynając od elementu głównego. Na przykład `Blog -> Posts -> Author` i `Blog -> Posts -> Tags` . Nie oznacza to, że nastąpi nadmiarowe sprzężenia; w większości przypadków program Dr konsoliduje sprzężenia podczas generowania bazy danych SQL.

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludes)]

> [!CAUTION]
> Od wersji 3.0.0 każda z nich spowoduje `Include` dodanie dodatkowego sprzężenia do zapytań SQL generowanych przez dostawców relacyjnych, podczas gdy poprzednie wersje wygenerowały dodatkowe zapytania SQL. Może to znacząco zmienić wydajność zapytań, aby lepiej lub gorszyć. W szczególności zapytania LINQ o przekroczeniu dużej liczbie `Include` operatorów mogą wymagać podzielenia na wiele oddzielnych zapytań LINQ w celu uniknięcia problemu z wybuchem kartezjańskiego.

### <a name="filtered-include"></a>Filtr obejmujący

> [!NOTE]
> Ta funkcja jest wprowadzana w EF Core 5,0.

W przypadku zastosowania dyrektywy include do załadowania powiązanych danych można zastosować pewne wyliczalne operacje na nawigacji kolekcji, która umożliwia filtrowanie i sortowanie wyników.

Obsługiwane są następujące operacje:,,,,, `Where` `OrderBy` `OrderByDescending` `ThenBy` `ThenByDescending` `Skip` , i `Take` .

Takie operacje powinny być stosowane w nawigacji kolekcji w wyrażeniu lambda przekazanym do metody include, jak pokazano w przykładzie poniżej:

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#FilteredInclude)]

Każda dołączona Nawigacja zezwala na tylko jeden unikatowy zestaw operacji filtrowania. W przypadkach, gdy do danej kolekcji są stosowane wiele operacji dołączania ( `blog.Posts` w poniższych przykładach), operacje filtrowania można określić tylko dla jednego z nich: 

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered1)]

Alternatywnie można zastosować identyczne operacje dla każdej nawigacji, która jest dołączana wiele razy:

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered2)]

> [!CAUTION]
> W przypadku zapytań śledzenia wyniki odfiltrowanych instrukcji include mogą być nieoczekiwane ze względu na naprawę [nawigacji](tracking.md). Wszystkie odpowiednie jednostki, które zostały querried wcześniej i zostały zapisane w module śledzącym zmiany, będą obecne w wynikach filtrowanego zapytania include, nawet jeśli nie spełniają wymagań filtru. Rozważ użycie `NoTracking` zapytań lub Utwórz ponownie DbContext, gdy w tych sytuacjach użyto funkcji Filtered include.

Przykład:

```csharp
var orders = context.Orders.Where(o => o.Id > 1000).ToList();

// customer entities will have references to all orders where Id > 1000, rathat than > 5000
var filtered = context.Customers.Include(c => c.Orders.Where(o => o.Id > 5000)).ToList();
```

### <a name="include-on-derived-types"></a>Uwzględnij w typach pochodnych

Można dołączyć powiązane dane z nawigacji zdefiniowanych tylko dla typu pochodnego przy użyciu `Include` i `ThenInclude` .

Uwzględniając następujący model:

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

Zawartość `School` nawigacji dla wszystkich osób, które są uczniami, może być eagerly załadowana przy użyciu wielu wzorców:

* Używanie rzutowania

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* `as`operator using

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* Użycie przeciążenia `Include` , które pobiera parametr typu`string`

  ```csharp
  context.People.Include("School").ToList()
  ```

## <a name="explicit-loading"></a>Jawne ładowanie

Można jawnie załadować właściwość nawigacji za pośrednictwem `DbContext.Entry(...)` interfejsu API.

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#Eager)]

Możesz również jawnie załadować właściwość nawigacji przez wykonanie oddzielnego zapytania, które zwraca powiązane jednostki. Jeśli funkcja śledzenia zmian jest włączona, podczas ładowania jednostki EF Core automatycznie ustawił właściwości nawigacji nowo załadowanego jednostkę, aby odwołać się do wszystkich jednostek, które zostały już załadowane, i ustawić właściwości nawigacji już załadowanych jednostek, aby odwołać się do nowo załadowanego obiektu.

### <a name="querying-related-entities"></a>Wykonywanie zapytania dotyczącego powiązanych jednostek

Możesz również uzyskać zapytanie LINQ, które reprezentuje zawartość właściwości nawigacji.

Umożliwia to wykonywanie takich czynności, jak uruchamianie agregacji operatora na powiązanych jednostkach bez ładowania ich do pamięci.

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

Można również filtrować powiązane jednostki, które są ładowane do pamięci.

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryFiltered)]

## <a name="lazy-loading"></a>Ładowanie z opóźnieniem

Najprostszym sposobem użycia ładowania z opóźnieniem jest zainstalowanie pakietu [Microsoft. EntityFrameworkCore. proxy](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) i włączenie go z wywołaniem do `UseLazyLoadingProxies` . Na przykład:

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseLazyLoadingProxies()
        .UseSqlServer(myConnectionString);
```

Lub w przypadku korzystania z AddDbContext:

```csharp
.AddDbContext<BloggingContext>(
    b => b.UseLazyLoadingProxies()
          .UseSqlServer(myConnectionString));
```

EF Core następnie włącza ładowanie z opóźnieniem dla każdej właściwości nawigacji, która może zostać przesłonięta — to oznacza, że musi być `virtual` i na klasie, z której można dziedziczyć. Na przykład w następujących jednostkach `Post.Blog` `Blog.Posts` właściwości i nawigacji zostaną załadowane z opóźnieniem.

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

### <a name="lazy-loading-without-proxies"></a>Ładowanie z opóźnieniem bez serwerów proxy

Ładowanie serwerów proxy z opóźnieniem, które działają przez wstrzyknięcie `ILazyLoader` usługi do jednostki, zgodnie z opisem w [konstruktorach typu jednostki](../modeling/constructors.md). Na przykład:

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

Nie wymaga to dziedziczenia typów jednostek ani właściwości nawigacji, które mają być wirtualne, i umożliwia wystąpienia jednostki utworzone za pomocą `new` do ładowania opóźnionego po dołączeniu do kontekstu. Jednak wymaga odwołania do `ILazyLoader` usługi, która jest zdefiniowana w pakiecie [Microsoft. EntityFrameworkCore. Abstracts](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/) . Ten pakiet zawiera minimalny zestaw typów, dzięki czemu w zależności od tego ma być bardzo niewielki wpływ. Jednak aby całkowicie uniknąć w zależności od dowolnego EF Core pakietów w typach jednostek, można wstrzyknąć `ILazyLoader.Load` metodę jako delegata. Na przykład:

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

W powyższym kodzie użyto `Load` metody rozszerzenia, aby użyć delegata:

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
> Parametr konstruktora dla delegata ładowania z opóźnieniem musi mieć nazwę "lazyLoader". Konfiguracja służąca do używania innej nazwy niż jest planowana dla przyszłej wersji.

## <a name="related-data-and-serialization"></a>Powiązane dane i Serializacja

Ponieważ EF Core automatycznie naprawia właściwości nawigacji, można zakończyć z cyklami na grafie obiektów. Na przykład załadowanie blogu i jego powiązanych wpisów spowoduje powstanie obiektu blogu, który odwołuje się do kolekcji wpisów. Każdy z tych wpisów będzie miał odwołanie z powrotem do blogu.

Niektóre platformy serializacji nie zezwalają na takie cykle. Na przykład Json.NET zgłosi następujący wyjątek w przypadku napotkania cyklu.

> Newtonsoft.Json.Jsonserializationexception: wykryto pętlę odwołującą się do właściwości "blog" z typem "WebApplication. MODELES. bloga".

Jeśli używasz ASP.NET Core, możesz skonfigurować Json.NET do ignorowania cykli znalezionych w grafie obiektów. Jest to realizowane za pomocą `ConfigureServices(...)` metody w `Startup.cs` .

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

Kolejną alternatywą jest dekorować jednej z właściwości nawigacji z `[JsonIgnore]` atrybutem, który instruuje JSON.NET, aby nie przechodzą tej właściwości nawigacji podczas serializacji.
