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
# <a name="loading-related-data"></a>Ładowanie powiązanych danych

Entity Framework Core umożliwia użycie właściwości nawigacji w modelu do ładowania powiązanych jednostek. Istnieją trzy typowe wzorce O/RM używane do ładowania powiązanych danych.

* **Wczesne ładowanie** oznacza, że powiązane dane są ładowane z bazy danych jako część kwerendy początkowej.
* **Jawne ładowanie** oznacza, że powiązane dane są jawnie ładowane z bazy danych w późniejszym czasie.
* **Powolne ładowanie** oznacza, że powiązane dane są ładowane w sposób przezroczysty z bazy danych, gdy dostęp do właściwości nawigacji.

> [!TIP]  
> Możesz wyświetlić [ten](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) przykład artykułu na GitHub.

## <a name="eager-loading"></a>Gorliwy załadunek

Za pomocą `Include` tej metody można określić powiązane dane, które mają zostać uwzględnione w wynikach kwerendy. W poniższym przykładzie blogi, które są zwracane w wynikach będą miały ich `Posts` właściwości wypełnione powiązanych wpisów.

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleInclude)]

> [!TIP]  
> Entity Framework Core automatycznie naprawi właściwości nawigacji do innych jednostek, które zostały wcześniej załadowane do wystąpienia kontekstu. Więc nawet jeśli nie jawnie dołączyć dane dla właściwości nawigacji, właściwość może nadal być wypełniona, jeśli niektóre lub wszystkie powiązane jednostki zostały wcześniej załadowane.

W jednym zapytaniu można uwzględnić powiązane dane z wielu relacji.

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleIncludes)]

### <a name="including-multiple-levels"></a>W tym wiele poziomów

Można przejść do szczegółów relacji, aby uwzględnić `ThenInclude` wiele poziomów powiązanych danych przy użyciu metody. Poniższy przykład ładuje wszystkie blogi, powiązane z nimi posty i autora każdego posta.

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleThenInclude)]

Można wysuń wiele wywołań, aby `ThenInclude` kontynuować, w tym dalsze poziomy powiązanych danych.

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleThenIncludes)]

Można połączyć to wszystko, aby uwzględnić powiązane dane z wielu poziomów i wiele katalogów głównych w tej samej kwerendzie.

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#IncludeTree)]

Można dołączyć wiele powiązanych jednostek dla jednej z jednostek, która jest uwzględniona. Na przykład podczas `Blogs`wykonywania zapytań `Posts` dołącza się, `Author` a `Tags` następnie `Posts`chcesz dołączyć zarówno plik . Aby to zrobić, należy określić każdą ścieżkę dołączania, zaczynając od katalogu głównego. Na przykład `Blog -> Posts -> Author` `Blog -> Posts -> Tags`i . Nie oznacza to, że otrzymasz zbędne sprzężenia; w większości przypadków EF skonsoliduje sprzężenia podczas generowania języka SQL.

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludes)]

> [!CAUTION]
> Od wersji 3.0.0, każdy `Include` spowoduje dodatkowe JOIN do dodania do zapytań SQL produkowanych przez dostawców relacyjnych, podczas gdy poprzednie wersje generowane dodatkowe zapytania SQL. Może to znacznie zmienić wydajność zapytań, na lepsze lub gorsze. W szczególności zapytania LINQ z niezwykle dużą `Include` liczbą operatorów może być konieczne w podziale na wiele oddzielnych zapytań LINQ w celu uniknięcia problemu rozłąki kartezjańskiej.

### <a name="filtered-include"></a>Filtrowane include

> [!NOTE]
> Ta funkcja jest wprowadzana w EF Core 5.0.

Podczas stosowania Include do ładowania powiązanych danych, można zastosować niektóre operacje wyliczalne na dołączonej nawigacji kolekcji, co pozwala na filtrowanie i sortowanie wyników.

Obsługiwane `Where`operacje to: `OrderBy` `OrderByDescending`, `ThenBy` `ThenByDescending`, `Skip`, `Take`, , , i .

Takie operacje powinny być stosowane na nawigacji kolekcji w lambda przekazywane do Include metody, jak pokazano w poniższym przykładzie:

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#FilteredInclude)]

Każda dołączona nawigacja umożliwia tylko jeden unikatowy zestaw operacji filtrowania. W przypadkach, gdy wiele operacji Include są`blog.Posts` stosowane dla danej nawigacji kolekcji (w poniższych przykładach), operacje filtrowania można określić tylko na jednym z nich: 

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered1)]

Alternatywnie, identyczne operacje mogą być stosowane dla każdej nawigacji, która jest uwzględniona wiele razy:

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered2)]

### <a name="include-on-derived-types"></a>Uwzględnij typy pochodne

Można uwzględnić powiązane dane z nawigacji zdefiniowanych `Include` tylko `ThenInclude`dla typu pochodnego przy użyciu i .

Biorąc pod uwagę następujący model:

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

Zawartość `School` nawigacji wszystkich osób, które są Studentami, może być chętnie ładowana za pomocą wielu wzorców:

* za pomocą odlewana

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* korzystanie `as` z operatora

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* za pomocą `Include` przeciążenia, że trwa parametr typu`string`

  ```csharp
  context.People.Include("School").ToList()
  ```

## <a name="explicit-loading"></a>Jawne ładowanie

Można jawnie załadować właściwość `DbContext.Entry(...)` nawigacji za pośrednictwem interfejsu API.

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#Eager)]

Można również jawnie załadować właściwość nawigacji, wykonując oddzielne zapytanie, które zwraca encje pokrewne. Jeśli śledzenie zmian jest włączone, a następnie podczas ładowania jednostki EF Core automatycznie ustawi właściwości nawigacji nowo załadowanej jednostki, aby odwoływać się do wszystkich jednostek już załadowanych i ustawić właściwości nawigacji już załadowanych jednostek, aby odwołać się do nowo załadowanej jednostki.

### <a name="querying-related-entities"></a>Wykonywanie zapytań o encje pokrewne

Można również uzyskać kwerendę LINQ, która reprezentuje zawartość właściwości nawigacji.

Dzięki temu można wykonać takie czynności, jak uruchamianie operatora agregacji za sprawą powiązanych jednostek bez ładowania ich do pamięci.

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

Można również filtrować, które powiązane jednostki są ładowane do pamięci.

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryFiltered)]

## <a name="lazy-loading"></a>Z opóźnieniem załadunku

Najprostszym sposobem korzystania z ładowania z opóźnieniem jest zainstalowanie pakietu [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) i włączenie go za pomocą połączenia z `UseLazyLoadingProxies`programem . Przykład:

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseLazyLoadingProxies()
        .UseSqlServer(myConnectionString);
```

Lub podczas korzystania z AddDbContext:

```csharp
.AddDbContext<BloggingContext>(
    b => b.UseLazyLoadingProxies()
          .UseSqlServer(myConnectionString));
```

EF Core włączy powolne ładowanie dla dowolnej właściwości nawigacji, która może zostać `virtual` zastąpiona — oznacza, że musi być i na klasę, która może być dziedziczona. Na przykład w następujących jednostkach `Post.Blog` `Blog.Posts` właściwości i nawigacji będą ładowane z opóźnieniem.

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

### <a name="lazy-loading-without-proxies"></a>Leniwe ładowanie bez serwerów proxy

Serwery proxy ładowania z opóźnieniem `ILazyLoader` działają przez wstrzyknięcie usługi do jednostki, zgodnie z opisem w [konstruktorach typu jednostki](../modeling/constructors.md). Przykład:

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

Nie wymaga to, aby typy jednostek były dziedziczone z lub właściwości nawigacji, aby być wirtualne i umożliwia wystąpienia jednostek utworzone z `new` opóźnieniem ładowania po dołączeniu do kontekstu. Jednak wymaga odwołania do `ILazyLoader` usługi, która jest zdefiniowana w pakiecie [Microsoft.EntityFrameworkCore.Abstractions.](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/) Ten pakiet zawiera minimalny zestaw typów, dzięki czemu jest bardzo mały wpływ w zależności od niego. Jednak aby całkowicie uniknąć w zależności od wszystkich pakietów EF Core `ILazyLoader.Load` w typach jednostek, jest możliwe, aby wstrzyknąć metodę jako delegata. Przykład:

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

Powyższy kod używa `Load` metody rozszerzenia, aby przy użyciu delegata nieco czystsze:

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
> Parametr konstruktora dla delegata z opóźnieniem musi być nazywany "lazyLoader". Konfiguracja, aby użyć innej nazwy niż to jest planowane dla przyszłej wersji.

## <a name="related-data-and-serialization"></a>Powiązane dane i serializacja

Ponieważ EF Core automatycznie naprawi właściwości nawigacji, można skończyć z cykli na wykresie obiektu. Na przykład załadowanie bloga i powiązanych z nim wpisów spowoduje, że obiekt blogu odwołuje się do kolekcji wpisów. Każdy z tych postów będzie miał odniesienie z powrotem do bloga.

Niektóre struktury serializacji nie zezwalają na takie cykle. Na przykład Json.NET zda następujący wyjątek, jeśli wystąpi cykl.

> Newtonsoft.Json.JsonSerializationException: Self referenceing pętli wykryte dla właściwości "Blog" z typem "MyApplication.Models.Blog".

Jeśli używasz ASP.NET Core, można skonfigurować Json.NET, aby ignorować cykle, które znajduje się na wykresie obiektu. Odbywa się to `ConfigureServices(...)` w `Startup.cs`metodzie w .

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

Inną alternatywą jest ozdobać jedną z `[JsonIgnore]` właściwości nawigacji z atrybutem, który nakazuje Json.NET, aby nie przechodzić przez tę właściwość nawigacji podczas serializacji.
