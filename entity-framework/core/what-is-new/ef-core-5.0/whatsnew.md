---
title: Co nowego w EF Core 5,0
description: Omówienie nowych funkcji w EF Core 5,0
author: ajcvickers
ms.date: 06/02/2020
uid: core/what-is-new/ef-core-5.0/whatsnew.md
ms.openlocfilehash: 45d851a4b08a26dda0c24e20c79f42964fa4fae4
ms.sourcegitcommit: 1f0f93c66b2b50e03fcbed90260e94faa0279c46
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2020
ms.locfileid: "84418947"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="ef092-103">Co nowego w EF Core 5,0</span><span class="sxs-lookup"><span data-stu-id="ef092-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="ef092-104">EF Core 5,0 jest obecnie w trakcie opracowywania.</span><span class="sxs-lookup"><span data-stu-id="ef092-104">EF Core 5.0 is currently in development.</span></span>
<span data-ttu-id="ef092-105">Ta strona będzie zawierać przegląd interesujących zmian wprowadzonych w każdej wersji zapoznawczej.</span><span class="sxs-lookup"><span data-stu-id="ef092-105">This page will contain an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="ef092-106">Ta strona nie duplikuje [planu dla EF Core 5,0](plan.md).</span><span class="sxs-lookup"><span data-stu-id="ef092-106">This page does not duplicate the [plan for EF Core 5.0](plan.md).</span></span>
<span data-ttu-id="ef092-107">Plan opisuje ogólne motywy dla EF Core 5,0, w tym wszystko, co planujemy uwzględnić przed wysyłką ostatecznej wersji.</span><span class="sxs-lookup"><span data-stu-id="ef092-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

<span data-ttu-id="ef092-108">Będziemy dodawać linki z tego miejsca do oficjalnej dokumentacji w trakcie jej publikacji.</span><span class="sxs-lookup"><span data-stu-id="ef092-108">We will add links from here to the official documentation as it is published.</span></span>

## <a name="preview-5"></a><span data-ttu-id="ef092-109">Wersja zapoznawcza 5</span><span class="sxs-lookup"><span data-stu-id="ef092-109">Preview 5</span></span>

### <a name="database-collations"></a><span data-ttu-id="ef092-110">Sortowanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="ef092-110">Database collations</span></span>

<span data-ttu-id="ef092-111">Domyślne sortowanie dla bazy danych można teraz określić w modelu EF.</span><span class="sxs-lookup"><span data-stu-id="ef092-111">The default collation for a database can now be specified in the EF model.</span></span>
<span data-ttu-id="ef092-112">Spowoduje to przetworzenie przetworzonych migracji w celu ustawienia sortowania podczas tworzenia bazy danych.</span><span class="sxs-lookup"><span data-stu-id="ef092-112">This will flow through to generated migrations to set the collation when the database is created.</span></span>
<span data-ttu-id="ef092-113">Przykład:</span><span class="sxs-lookup"><span data-stu-id="ef092-113">For example:</span></span>

```CSharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="ef092-114">Następnie migracja generuje następujące elementy, aby utworzyć bazę danych na SQL Server:</span><span class="sxs-lookup"><span data-stu-id="ef092-114">Migrations then generates the following to create the database on SQL Server:</span></span>

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

<span data-ttu-id="ef092-115">Można również określić sortowanie, które ma być używane dla określonych kolumn bazy danych.</span><span class="sxs-lookup"><span data-stu-id="ef092-115">The collation to use for specific database columns can also be specified.</span></span>
<span data-ttu-id="ef092-116">Przykład:</span><span class="sxs-lookup"><span data-stu-id="ef092-116">For example:</span></span>

```CSharp
 modelBuilder
     .Entity<User>()
     .Property(e => e.Name)
     .UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="ef092-117">Dla tych, które nie korzystają z migracji, sortowania są teraz odtwarzane z bazy danych podczas tworzenia szkieletu DbContext.</span><span class="sxs-lookup"><span data-stu-id="ef092-117">For those not using migrations, collations are now reverse-engineered from the database when scaffolding a DbContext.</span></span>

<span data-ttu-id="ef092-118">Na koniec `EF.Functions.Collate()` zezwala na kwerendy ad hoc przy użyciu różnych ustawień sortowania.</span><span class="sxs-lookup"><span data-stu-id="ef092-118">Finally, the `EF.Functions.Collate()` allows for ad-hoc queries using different collations.</span></span>
<span data-ttu-id="ef092-119">Przykład:</span><span class="sxs-lookup"><span data-stu-id="ef092-119">For example:</span></span>

```CSharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

<span data-ttu-id="ef092-120">Spowoduje to wygenerowanie następującej kwerendy dla SQL Server:</span><span class="sxs-lookup"><span data-stu-id="ef092-120">This will generate the following query for SQL Server:</span></span>

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

<span data-ttu-id="ef092-121">Należy pamiętać, że sortowania ad-hoc należy używać z ostrożnością, ponieważ mogą one mieć negatywny wpływ na wydajność bazy danych.</span><span class="sxs-lookup"><span data-stu-id="ef092-121">Note that ad-hoc collations should be used with care as they can negatively impact database performance.</span></span>

<span data-ttu-id="ef092-122">Dokumentacja jest śledzona przez [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273)problemu.</span><span class="sxs-lookup"><span data-stu-id="ef092-122">Documentation is tracked by issue [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span></span>

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a><span data-ttu-id="ef092-123">Argumenty przepływu w IDesignTimeDbContextFactory</span><span class="sxs-lookup"><span data-stu-id="ef092-123">Flow arguments into IDesignTimeDbContextFactory</span></span>

<span data-ttu-id="ef092-124">Argumenty są teraz przepływane z wiersza polecenia do `CreateDbContext` metody [IDesignTimeDbContextFactory](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1).</span><span class="sxs-lookup"><span data-stu-id="ef092-124">Arguments are now flowed from the command line into the `CreateDbContext` method of [IDesignTimeDbContextFactory](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1).</span></span> <span data-ttu-id="ef092-125">Na przykład, aby wskazać, że jest to kompilacja dev, argument niestandardowy (np. `dev` ) może zostać zakończony w wierszu polecenia:</span><span class="sxs-lookup"><span data-stu-id="ef092-125">For example, to indicate this is a dev build, a custom argument (e.g. `dev`) can passed on the command line:</span></span>

```
dotnet ef migrations add two --verbose --dev
``` 

<span data-ttu-id="ef092-126">Ten argument będzie przepływał do fabryki, gdzie może służyć do kontrolowania sposobu tworzenia i inicjowania kontekstu.</span><span class="sxs-lookup"><span data-stu-id="ef092-126">This argument will then flow into the factory, where it can be used to control how the context is created and initialized.</span></span>
<span data-ttu-id="ef092-127">Przykład:</span><span class="sxs-lookup"><span data-stu-id="ef092-127">For example:</span></span>

```CSharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args) 
        => new SomeDbContext(args.Contains("--dev"));
}
```

<span data-ttu-id="ef092-128">Dokumentacja jest śledzona przez [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419)problemu.</span><span class="sxs-lookup"><span data-stu-id="ef092-128">Documentation is tracked by issue [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span></span>

### <a name="no-tracking-queries-with-identity-resolution"></a><span data-ttu-id="ef092-129">Nie śledzij zapytań z rozpoznawaniem tożsamości</span><span class="sxs-lookup"><span data-stu-id="ef092-129">No-tracking queries with identity resolution</span></span>

<span data-ttu-id="ef092-130">Nie można teraz skonfigurować zapytań śledzenia, aby przeprowadzić rozpoznawanie tożsamości.</span><span class="sxs-lookup"><span data-stu-id="ef092-130">No-tracking queries can now be configured to perform identity resolution.</span></span>
<span data-ttu-id="ef092-131">Na przykład następujące zapytanie utworzy nowe wystąpienie blogu dla każdego wpisu, nawet jeśli każdy blog ma ten sam klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="ef092-131">For example, the following query will create a new Blog instance for each Post, even if each Blog has the same primary key.</span></span> 

```CSharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

<span data-ttu-id="ef092-132">Jednak koszt zwykle jest nieco wolniejszy i zawsze korzysta z większej ilości pamięci, to zapytanie można zmienić, aby upewnić się, że tworzone jest tylko jedno wystąpienie blogu:</span><span class="sxs-lookup"><span data-stu-id="ef092-132">However, at the expense of usually being slightly slower and always using more memory, this query can be changed to ensure only a single Blog instance is created:</span></span>

```CSharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

<span data-ttu-id="ef092-133">Należy zauważyć, że jest to przydatne tylko w przypadku zapytań bez śledzenia, ponieważ wszystkie zapytania śledzenia już wykazują takie zachowanie.</span><span class="sxs-lookup"><span data-stu-id="ef092-133">Note that this is only useful for no-tracking queries since all tracking queries already exhibit this behavior.</span></span> <span data-ttu-id="ef092-134">Ponadto, po przeglądzie interfejsu API, `PerformIdentityResolution` składnia zostanie zmieniona.</span><span class="sxs-lookup"><span data-stu-id="ef092-134">Also, following API review, the `PerformIdentityResolution` syntax will be changed.</span></span>
<span data-ttu-id="ef092-135">Zobacz [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span><span class="sxs-lookup"><span data-stu-id="ef092-135">See [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span></span>

<span data-ttu-id="ef092-136">Dokumentacja jest śledzona przez [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895)problemu.</span><span class="sxs-lookup"><span data-stu-id="ef092-136">Documentation is tracked by issue [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span></span>

### <a name="stored-persisted-computed-columns"></a><span data-ttu-id="ef092-137">Przechowywane (utrwalone) kolumny obliczane</span><span class="sxs-lookup"><span data-stu-id="ef092-137">Stored (persisted) computed columns</span></span>

<span data-ttu-id="ef092-138">Większość baz danych umożliwia przechowywanie wartości kolumn obliczanych po obliczeniach.</span><span class="sxs-lookup"><span data-stu-id="ef092-138">Most databases allow computed column values to be stored after computation.</span></span>
<span data-ttu-id="ef092-139">Gdy to zajmuje miejsce na dysku, kolumna obliczana jest obliczana tylko raz podczas aktualizacji, a nie za każdym razem, gdy jej wartość zostanie pobrana.</span><span class="sxs-lookup"><span data-stu-id="ef092-139">While this takes up disk space, the computed column is calculated only once on update, instead of each time its value is retrieved.</span></span>
<span data-ttu-id="ef092-140">Pozwala to również na indeksowanie kolumny dla niektórych baz danych.</span><span class="sxs-lookup"><span data-stu-id="ef092-140">This also allows the column to be indexed for some databases.</span></span>

<span data-ttu-id="ef092-141">EF Core 5,0 umożliwia skonfigurowanie kolumn obliczanych jako przechowywanych.</span><span class="sxs-lookup"><span data-stu-id="ef092-141">EF Core 5.0 allows computed columns to be configured as stored.</span></span>
<span data-ttu-id="ef092-142">Przykład:</span><span class="sxs-lookup"><span data-stu-id="ef092-142">For example:</span></span>
 
```CSharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a><span data-ttu-id="ef092-143">Kolumny obliczane przez SQLite</span><span class="sxs-lookup"><span data-stu-id="ef092-143">SQLite computed columns</span></span>

<span data-ttu-id="ef092-144">EF Core teraz obsługuje kolumny obliczane w bazach danych oprogramowania SQLite.</span><span class="sxs-lookup"><span data-stu-id="ef092-144">EF Core now supports computed columns in SQLite databases.</span></span>

## <a name="preview-4"></a><span data-ttu-id="ef092-145">Wersja zapoznawcza 4</span><span class="sxs-lookup"><span data-stu-id="ef092-145">Preview 4</span></span>

### <a name="configure-database-precisionscale-in-model"></a><span data-ttu-id="ef092-146">Konfiguruj precyzję i skalowanie bazy danych w modelu</span><span class="sxs-lookup"><span data-stu-id="ef092-146">Configure database precision/scale in model</span></span>

<span data-ttu-id="ef092-147">Precyzja i skala właściwości można teraz określić przy użyciu konstruktora modeli.</span><span class="sxs-lookup"><span data-stu-id="ef092-147">Precision and scale for a property can now be specified using the model builder.</span></span>
<span data-ttu-id="ef092-148">Przykład:</span><span class="sxs-lookup"><span data-stu-id="ef092-148">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

<span data-ttu-id="ef092-149">Precyzja i skala można nadal ustawiać za pośrednictwem pełnego typu bazy danych, na przykład "Decimal (16, 4)".</span><span class="sxs-lookup"><span data-stu-id="ef092-149">Precision and scale can still be set via the full database type, such as "decimal(16,4)".</span></span> 

<span data-ttu-id="ef092-150">Dokumentacja jest śledzona przez [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527)problemu.</span><span class="sxs-lookup"><span data-stu-id="ef092-150">Documentation is tracked by issue [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span></span>

### <a name="specify-sql-server-index-fill-factor"></a><span data-ttu-id="ef092-151">Określ współczynnik wypełnienia indeksu SQL Server</span><span class="sxs-lookup"><span data-stu-id="ef092-151">Specify SQL Server index fill factor</span></span>

<span data-ttu-id="ef092-152">Współczynnik wypełniania można teraz określić podczas tworzenia indeksu na SQL Server.</span><span class="sxs-lookup"><span data-stu-id="ef092-152">The fill factor can now be specified when creating an index on SQL Server.</span></span>
<span data-ttu-id="ef092-153">Przykład:</span><span class="sxs-lookup"><span data-stu-id="ef092-153">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a><span data-ttu-id="ef092-154">Wersja zapoznawcza 3</span><span class="sxs-lookup"><span data-stu-id="ef092-154">Preview 3</span></span>

### <a name="filtered-include"></a><span data-ttu-id="ef092-155">Filtr obejmujący</span><span class="sxs-lookup"><span data-stu-id="ef092-155">Filtered Include</span></span>

<span data-ttu-id="ef092-156">Metoda include obsługuje teraz filtrowanie uwzględnionych jednostek.</span><span class="sxs-lookup"><span data-stu-id="ef092-156">The Include method now supports filtering of the entities included.</span></span>
<span data-ttu-id="ef092-157">Przykład:</span><span class="sxs-lookup"><span data-stu-id="ef092-157">For example:</span></span>

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="ef092-158">To zapytanie będzie zwracać Blogi razem z poszczególnymi wpisami skojarzonymi, ale tylko wtedy, gdy tytuł wpisu zawiera "ser".</span><span class="sxs-lookup"><span data-stu-id="ef092-158">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="ef092-159">Pomiń i zrób można także użyć, aby zmniejszyć liczbę uwzględnionych jednostek.</span><span class="sxs-lookup"><span data-stu-id="ef092-159">Skip and Take can also be used to reduce the number of included entities.</span></span>
<span data-ttu-id="ef092-160">Przykład:</span><span class="sxs-lookup"><span data-stu-id="ef092-160">For example:</span></span>
 
```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
<span data-ttu-id="ef092-161">To zapytanie będzie zwracać blogi z co najwyżej pięcioma wpisami zawartymi w każdym blogu.</span><span class="sxs-lookup"><span data-stu-id="ef092-161">This query will return blogs with at most five posts included on each blog.</span></span>

<span data-ttu-id="ef092-162">Szczegółowe informacje znajdują się w [dokumentacji dołączanej](xref:core/querying/related-data#filtered-include) .</span><span class="sxs-lookup"><span data-stu-id="ef092-162">See the [Include documentation](xref:core/querying/related-data#filtered-include) for full details.</span></span>

### <a name="new-modelbuilder-api-for-navigation-properties"></a><span data-ttu-id="ef092-163">Nowy interfejs API element modelbuilder dla właściwości nawigacji</span><span class="sxs-lookup"><span data-stu-id="ef092-163">New ModelBuilder API for navigation properties</span></span>

<span data-ttu-id="ef092-164">Właściwości nawigacji są przede wszystkim konfigurowane podczas [definiowania relacji](xref:core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="ef092-164">Navigation properties are primarily configured when [defining relationships](xref:core/modeling/relationships).</span></span>
<span data-ttu-id="ef092-165">Jednak nowa `Navigation` Metoda może być używana w przypadkach, w których właściwości nawigacji wymagają dodatkowej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="ef092-165">However, the new `Navigation` method can be used in the cases where navigation properties need additional configuration.</span></span>
<span data-ttu-id="ef092-166">Na przykład, aby ustawić pole zapasowe dla nawigacji, gdy pole nie zostanie znalezione według Konwencji:</span><span class="sxs-lookup"><span data-stu-id="ef092-166">For example, to set a backing field for the navigation when the field would not be found by convention:</span></span>

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

<span data-ttu-id="ef092-167">Należy pamiętać, że `Navigation` interfejs API nie zastępuje konfiguracji relacji.</span><span class="sxs-lookup"><span data-stu-id="ef092-167">Note that the `Navigation` API does not replace relationship configuration.</span></span>
<span data-ttu-id="ef092-168">Zamiast tego umożliwia dodatkową konfigurację właściwości nawigacji w już odnalezionych lub zdefiniowanych relacjach.</span><span class="sxs-lookup"><span data-stu-id="ef092-168">Instead it allows additional configuration of navigation properties in already discovered or defined relationships.</span></span>

<span data-ttu-id="ef092-169">Zapoznaj się z [dokumentacją dotyczącą konfigurowania właściwości nawigacji](xref:core/modeling/relationships#configuring-navigation-properties).</span><span class="sxs-lookup"><span data-stu-id="ef092-169">See the [Configuring Navigation Properties documentation](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a><span data-ttu-id="ef092-170">Nowe parametry wiersza polecenia dla przestrzeni nazw i parametrów połączenia</span><span class="sxs-lookup"><span data-stu-id="ef092-170">New command-line parameters for namespaces and connection strings</span></span> 

<span data-ttu-id="ef092-171">Migracje i tworzenie szkieletów umożliwiają teraz określenie przestrzeni nazw w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="ef092-171">Migrations and scaffolding now allow namespaces to be specified on the command line.</span></span>
<span data-ttu-id="ef092-172">Na przykład w celu odtworzenia bazy danych, w której są umieszczane klasy kontekstu i modelu w różnych przestrzeniach nazw:</span><span class="sxs-lookup"><span data-stu-id="ef092-172">For example, to reverse engineer a database putting the context and model classes in different namespaces:</span></span> 

```
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

<span data-ttu-id="ef092-173">Szczegółowe informacje znajdują się [w dokumentacji](xref:core/managing-schemas/scaffolding#directories-and-namespaces) dotyczącej [migracji](xref:core/managing-schemas/migrations/index#namespaces) i odtwarzania.</span><span class="sxs-lookup"><span data-stu-id="ef092-173">See the [Migrations](xref:core/managing-schemas/migrations/index#namespaces) and [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces) documentation for full details.</span></span>

---
<span data-ttu-id="ef092-174">Ponadto parametry połączenia można teraz przekazywać do `database-update` polecenia:</span><span class="sxs-lookup"><span data-stu-id="ef092-174">Also, a connection string can now be passed to the `database-update` command:</span></span>

```
dotnet ef database update --connection "connection string"
```

<span data-ttu-id="ef092-175">Szczegółowe informacje znajdują się w [dokumentacji narzędzi](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) .</span><span class="sxs-lookup"><span data-stu-id="ef092-175">See the [Tools documentation](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) for full details.</span></span>

<span data-ttu-id="ef092-176">Do poleceń programu PowerShell, które są używane w konsoli Menedżera pakietów programu VS, dodano również równoważne parametry.</span><span class="sxs-lookup"><span data-stu-id="ef092-176">Equivalent parameters have also been added to the PowerShell commands used in the VS Package Manager Console.</span></span>

### <a name="enabledetailederrors-has-returned"></a><span data-ttu-id="ef092-177">EnableDetailedErrors</span><span class="sxs-lookup"><span data-stu-id="ef092-177">EnableDetailedErrors has returned</span></span>

<span data-ttu-id="ef092-178">Ze względu na wydajność EF nie wykonuje dodatkowych kontroli wartości null podczas odczytywania wartości z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="ef092-178">For performance reasons, EF doesn't do additional null-checks when reading values from the database.</span></span>
<span data-ttu-id="ef092-179">Może to spowodować, że wyjątki są trudne do wylogowania, gdy zostanie napotkany nieoczekiwany element null.</span><span class="sxs-lookup"><span data-stu-id="ef092-179">This can result in exceptions that are hard to root-cause when an unexpected null is encountered.</span></span>

<span data-ttu-id="ef092-180">Korzystanie z programu `EnableDetailedErrors` spowoduje dodanie dodatkowych kontroli wartości null do zapytań, takich jak w przypadku małego obciążenia wydajności, te błędy są łatwiejsze do śledzenia z przyczyn głównych.</span><span class="sxs-lookup"><span data-stu-id="ef092-180">Using `EnableDetailedErrors` will add extra null checking to queries such that, for a small performance overhead, these errors are easier to trace back to a root cause.</span></span>  

<span data-ttu-id="ef092-181">Przykład:</span><span class="sxs-lookup"><span data-stu-id="ef092-181">For example:</span></span>
```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors 
        .UseSqlServer(Your.SqlServerConnectionString);
```

<span data-ttu-id="ef092-182">Dokumentacja jest śledzona przez [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955)problemu.</span><span class="sxs-lookup"><span data-stu-id="ef092-182">Documentation is tracked by issue [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="ef092-183">Klucze partycji Cosmos</span><span class="sxs-lookup"><span data-stu-id="ef092-183">Cosmos partition keys</span></span>

<span data-ttu-id="ef092-184">Klucz partycji, który ma być używany dla danego zapytania, można teraz określić w zapytaniu.</span><span class="sxs-lookup"><span data-stu-id="ef092-184">The partition key to use for a given query can now be specified in the query.</span></span>
<span data-ttu-id="ef092-185">Przykład:</span><span class="sxs-lookup"><span data-stu-id="ef092-185">For example:</span></span>

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

<span data-ttu-id="ef092-186">Dokumentacja jest śledzona przez [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199)problemu.</span><span class="sxs-lookup"><span data-stu-id="ef092-186">Documentation is tracked by issue [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span></span>

### <a name="support-for-the-sql-server-datalength-function"></a><span data-ttu-id="ef092-187">Obsługa funkcji SQL Server DATALENGTH</span><span class="sxs-lookup"><span data-stu-id="ef092-187">Support for the SQL Server DATALENGTH function</span></span>

<span data-ttu-id="ef092-188">Dostęp do niego można uzyskać za pomocą nowej `EF.Functions.DataLength` metody.</span><span class="sxs-lookup"><span data-stu-id="ef092-188">This can be accessed using the new `EF.Functions.DataLength` method.</span></span>
<span data-ttu-id="ef092-189">Przykład:</span><span class="sxs-lookup"><span data-stu-id="ef092-189">For example:</span></span>
```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
``` 

## <a name="preview-2"></a><span data-ttu-id="ef092-190">Preview 2</span><span class="sxs-lookup"><span data-stu-id="ef092-190">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="ef092-191">Użyj atrybutu języka C#, aby określić pole zapasowe właściwości</span><span class="sxs-lookup"><span data-stu-id="ef092-191">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="ef092-192">Atrybut języka C# może być teraz używany do określania pola zapasowego dla właściwości.</span><span class="sxs-lookup"><span data-stu-id="ef092-192">A C# attribute can now be used to specify the backing field for a property.</span></span>
<span data-ttu-id="ef092-193">Ten atrybut pozwala EF Core nadal pisać i odczytywać dane z pola zapasowego, tak jak zwykle, nawet jeśli nie można automatycznie znaleźć pola zapasowego.</span><span class="sxs-lookup"><span data-stu-id="ef092-193">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span>
<span data-ttu-id="ef092-194">Przykład:</span><span class="sxs-lookup"><span data-stu-id="ef092-194">For example:</span></span>

```CSharp
public class Blog
{
    private string _mainTitle;

    public int Id { get; set; }

    [BackingField(nameof(_mainTitle))]
    public string Title
    {
        get => _mainTitle;
        set => _mainTitle = value;
    }
}
```

<span data-ttu-id="ef092-195">Dokumentacja jest śledzona przez [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230)problemu.</span><span class="sxs-lookup"><span data-stu-id="ef092-195">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="ef092-196">Kompletne mapowanie rozróżniacza</span><span class="sxs-lookup"><span data-stu-id="ef092-196">Complete discriminator mapping</span></span>

<span data-ttu-id="ef092-197">EF Core używa kolumny rozróżniacza dla [mapowania TPH hierarchii dziedziczenia](/ef/core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="ef092-197">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](/ef/core/modeling/inheritance).</span></span>
<span data-ttu-id="ef092-198">Niektóre ulepszenia wydajności są możliwe tak długo, jak EF Core wie wszystkie możliwe wartości dla rozróżniacza.</span><span class="sxs-lookup"><span data-stu-id="ef092-198">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span>
<span data-ttu-id="ef092-199">EF Core 5,0 implementuje teraz te ulepszenia.</span><span class="sxs-lookup"><span data-stu-id="ef092-199">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="ef092-200">Na przykład poprzednie wersje programu EF Core zawsze generują te dane SQL dla zapytania zwracającego wszystkie typy w hierarchii:</span><span class="sxs-lookup"><span data-stu-id="ef092-200">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="ef092-201">EF Core 5,0 będzie teraz generować następujące elementy, gdy zostanie skonfigurowane kompletne mapowanie rozróżniacza:</span><span class="sxs-lookup"><span data-stu-id="ef092-201">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="ef092-202">Będzie to domyślne zachowanie rozpoczynające się od wersji zapoznawczej 3.</span><span class="sxs-lookup"><span data-stu-id="ef092-202">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="ef092-203">Ulepszenia wydajności w programie Microsoft. Data. sqlite</span><span class="sxs-lookup"><span data-stu-id="ef092-203">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="ef092-204">Wprowadziliśmy dwie ulepszenia wydajności dla oprogramowania SQLIte:</span><span class="sxs-lookup"><span data-stu-id="ef092-204">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="ef092-205">Pobieranie danych binarnych i ciągów za pomocą metody GetBytes, GetChars i GetTextReader jest teraz wydajniejsze dzięki użyciu SqliteBlob i strumieni.</span><span class="sxs-lookup"><span data-stu-id="ef092-205">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="ef092-206">Inicjalizacja SqliteConnection jest teraz opóźniona.</span><span class="sxs-lookup"><span data-stu-id="ef092-206">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="ef092-207">Te udoskonalenia znajdują się w dostawcy ADO.NET Microsoft. Data. SQLite, a tym samym zwiększyć wydajność poza EF Core.</span><span class="sxs-lookup"><span data-stu-id="ef092-207">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="ef092-208">Wersja zapoznawcza 1</span><span class="sxs-lookup"><span data-stu-id="ef092-208">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="ef092-209">Rejestrowanie proste</span><span class="sxs-lookup"><span data-stu-id="ef092-209">Simple logging</span></span>

<span data-ttu-id="ef092-210">Ta funkcja dodaje funkcje podobne do `Database.Log` programu w programie Ef6.</span><span class="sxs-lookup"><span data-stu-id="ef092-210">This feature adds functionality similar to `Database.Log` in EF6.</span></span>
<span data-ttu-id="ef092-211">Oznacza to, że zapewnia prosty sposób pobierania dzienników z EF Core bez konieczności konfigurowania żadnego rodzaju zewnętrznej platformy rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="ef092-211">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="ef092-212">Wstępna dokumentacja jest zawarta w [statusie tygodnia Ef 5 grudnia 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span><span class="sxs-lookup"><span data-stu-id="ef092-212">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="ef092-213">Dodatkowa dokumentacja jest śledzona przez [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085)problemu.</span><span class="sxs-lookup"><span data-stu-id="ef092-213">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="ef092-214">Prosty sposób uzyskiwania wygenerowanego kodu SQL</span><span class="sxs-lookup"><span data-stu-id="ef092-214">Simple way to get generated SQL</span></span>

<span data-ttu-id="ef092-215">EF Core 5,0 wprowadza `ToQueryString` metodę rozszerzenia, która zwróci kod SQL, który EF Core zostanie wygenerowany podczas wykonywania zapytania LINQ.</span><span class="sxs-lookup"><span data-stu-id="ef092-215">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="ef092-216">Wstępna dokumentacja jest uwzględniona w [statusie tygodniowym EF dla 9 stycznia 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span><span class="sxs-lookup"><span data-stu-id="ef092-216">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="ef092-217">Dodatkowa dokumentacja jest śledzona przez [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331)problemu.</span><span class="sxs-lookup"><span data-stu-id="ef092-217">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="ef092-218">Użyj atrybutu języka C#, aby wskazać, że jednostka nie ma klucza</span><span class="sxs-lookup"><span data-stu-id="ef092-218">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="ef092-219">Typ jednostki można teraz skonfigurować jako bez klucza przy użyciu nowego elementu `KeylessAttribute` .</span><span class="sxs-lookup"><span data-stu-id="ef092-219">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span>
<span data-ttu-id="ef092-220">Przykład:</span><span class="sxs-lookup"><span data-stu-id="ef092-220">For example:</span></span>

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="ef092-221">Dokumentacja jest śledzona przez [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186)problemu.</span><span class="sxs-lookup"><span data-stu-id="ef092-221">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="ef092-222">Parametry Connection lub Connection można zmienić w zainicjowanym kontekście DbContext</span><span class="sxs-lookup"><span data-stu-id="ef092-222">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="ef092-223">Teraz łatwiej jest utworzyć wystąpienie DbContext bez żadnego połączenia lub parametrów połączenia.</span><span class="sxs-lookup"><span data-stu-id="ef092-223">It is now easier to create a DbContext instance without any connection or connection string.</span></span>
<span data-ttu-id="ef092-224">Ponadto parametry połączenia lub połączenia można teraz przystąpić do wystąpienia kontekstu.</span><span class="sxs-lookup"><span data-stu-id="ef092-224">Also, the connection or connection string can now be mutated on the context instance.</span></span>
<span data-ttu-id="ef092-225">Ta funkcja pozwala temu samemu wystąpieniu kontekstu na dynamiczne łączenie się z różnymi bazami danych.</span><span class="sxs-lookup"><span data-stu-id="ef092-225">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="ef092-226">Dokumentacja jest śledzona przez [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075)problemu.</span><span class="sxs-lookup"><span data-stu-id="ef092-226">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="ef092-227">Serwery proxy śledzenia zmian</span><span class="sxs-lookup"><span data-stu-id="ef092-227">Change-tracking proxies</span></span>

<span data-ttu-id="ef092-228">EF Core mogą teraz generować serwery proxy środowiska uruchomieniowego, które automatycznie implementują [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) i [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span><span class="sxs-lookup"><span data-stu-id="ef092-228">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) and [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span></span>
<span data-ttu-id="ef092-229">Następnie te zmiany są raportowane w oparciu o właściwości jednostki bezpośrednio do EF Core, unikając konieczności skanowania pod kątem zmian.</span><span class="sxs-lookup"><span data-stu-id="ef092-229">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span>
<span data-ttu-id="ef092-230">Jednak serwery proxy są dostarczane z własnym zestawem ograniczeń, więc nie są przeznaczone dla wszystkich użytkowników.</span><span class="sxs-lookup"><span data-stu-id="ef092-230">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="ef092-231">Dokumentacja jest śledzona przez [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076)problemu.</span><span class="sxs-lookup"><span data-stu-id="ef092-231">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="ef092-232">Udoskonalone widoki debugowania</span><span class="sxs-lookup"><span data-stu-id="ef092-232">Enhanced debug views</span></span>

<span data-ttu-id="ef092-233">Widoki debugowania to prosty sposób na zajrzeć do wewnętrznych EF Core w przypadku problemów z debugowaniem.</span><span class="sxs-lookup"><span data-stu-id="ef092-233">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span>
<span data-ttu-id="ef092-234">Widok debugowania dla modelu został zaimplementowany jakiś czas temu.</span><span class="sxs-lookup"><span data-stu-id="ef092-234">A debug view for the Model was implemented some time ago.</span></span>
<span data-ttu-id="ef092-235">W przypadku EF Core 5,0 ten widok modelu jest łatwiejszy do odczytania i dodania nowego widoku debugowania dla śledzonych jednostek w Menedżerze stanu.</span><span class="sxs-lookup"><span data-stu-id="ef092-235">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="ef092-236">Wstępna dokumentacja jest uwzględniona w [Stanach tygodnia EF 12 grudnia 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span><span class="sxs-lookup"><span data-stu-id="ef092-236">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="ef092-237">Dodatkowa dokumentacja jest śledzona przez [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086)problemu.</span><span class="sxs-lookup"><span data-stu-id="ef092-237">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="ef092-238">Ulepszona obsługa semantyki o wartości null bazy danych</span><span class="sxs-lookup"><span data-stu-id="ef092-238">Improved handling of database null semantics</span></span>

<span data-ttu-id="ef092-239">Relacyjne bazy danych zazwyczaj traktują wartości NULL jako nieznaną wartość i w związku z tym nie są równe żadnym innym NULL.</span><span class="sxs-lookup"><span data-stu-id="ef092-239">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span>
<span data-ttu-id="ef092-240">Chociaż język C# traktuje wartość null w postaci wartości zdefiniowanej, co porównuje ją z innymi wartościami null.</span><span class="sxs-lookup"><span data-stu-id="ef092-240">While C# treats null as a defined value, which compares equal to any other null.</span></span>
<span data-ttu-id="ef092-241">EF Core domyślnie tłumaczy zapytania tak, aby korzystały z semantyki wartości null języka C#.</span><span class="sxs-lookup"><span data-stu-id="ef092-241">EF Core by default translates queries so that they use C# null semantics.</span></span>
<span data-ttu-id="ef092-242">EF Core 5,0 znacznie poprawia wydajność tych tłumaczeń.</span><span class="sxs-lookup"><span data-stu-id="ef092-242">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="ef092-243">Dokumentacja jest śledzona przez [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612)problemu.</span><span class="sxs-lookup"><span data-stu-id="ef092-243">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="ef092-244">Właściwości indeksatora</span><span class="sxs-lookup"><span data-stu-id="ef092-244">Indexer properties</span></span>

<span data-ttu-id="ef092-245">EF Core 5,0 obsługuje mapowanie właściwości indeksatora języka C#.</span><span class="sxs-lookup"><span data-stu-id="ef092-245">EF Core 5.0 supports mapping of C# indexer properties.</span></span>
<span data-ttu-id="ef092-246">Te właściwości umożliwiają jednostkom działanie jako zbiory właściwości, w których kolumny są mapowane na nazwane właściwości w zbiorze.</span><span class="sxs-lookup"><span data-stu-id="ef092-246">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="ef092-247">Dokumentacja jest śledzona przez [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018)problemu.</span><span class="sxs-lookup"><span data-stu-id="ef092-247">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="ef092-248">Generowanie ograniczeń check dla mapowań wyliczenia</span><span class="sxs-lookup"><span data-stu-id="ef092-248">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="ef092-249">Migracje EF Core 5,0 mogą teraz generować ograniczenia CHECK dla mapowań właściwości enum.</span><span class="sxs-lookup"><span data-stu-id="ef092-249">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span>
<span data-ttu-id="ef092-250">Przykład:</span><span class="sxs-lookup"><span data-stu-id="ef092-250">For example:</span></span>

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="ef092-251">Dokumentacja jest śledzona przez [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082)problemu.</span><span class="sxs-lookup"><span data-stu-id="ef092-251">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="ef092-252">Isrelacyjne</span><span class="sxs-lookup"><span data-stu-id="ef092-252">IsRelational</span></span>

<span data-ttu-id="ef092-253">Dodano nową `IsRelational` metodę oprócz istniejących `IsSqlServer` , `IsSqlite` i `IsInMemory` .</span><span class="sxs-lookup"><span data-stu-id="ef092-253">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span>
<span data-ttu-id="ef092-254">Tej metody można użyć do sprawdzenia, czy DbContext używa dowolnego dostawcy relacyjnej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="ef092-254">This method can be used to test if the DbContext is using any relational database provider.</span></span>
<span data-ttu-id="ef092-255">Przykład:</span><span class="sxs-lookup"><span data-stu-id="ef092-255">For example:</span></span>

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="ef092-256">Dokumentacja jest śledzona przez [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185)problemu.</span><span class="sxs-lookup"><span data-stu-id="ef092-256">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="ef092-257">Cosmos optymistyczne współbieżność za pomocą elementów ETag</span><span class="sxs-lookup"><span data-stu-id="ef092-257">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="ef092-258">Dostawca bazy danych Azure Cosmos DB obsługuje teraz optymistyczne współbieżność za pomocą elementów ETag.</span><span class="sxs-lookup"><span data-stu-id="ef092-258">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span>
<span data-ttu-id="ef092-259">Użyj konstruktora modeli w OnModelCreating, aby skonfigurować element ETag:</span><span class="sxs-lookup"><span data-stu-id="ef092-259">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="ef092-260">Metody SaveChanges następnie zgłosi `DbUpdateConcurrencyException` konflikt współbieżności, który [można obsłużyć](https://docs.microsoft.com/ef/core/saving/concurrency) w celu zaimplementowania ponownych prób itd.</span><span class="sxs-lookup"><span data-stu-id="ef092-260">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](https://docs.microsoft.com/ef/core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="ef092-261">Dokumentacja jest śledzona przez [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099)problemu.</span><span class="sxs-lookup"><span data-stu-id="ef092-261">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="ef092-262">Tłumaczenie zapytań dla większej liczby konstrukcji DateTime</span><span class="sxs-lookup"><span data-stu-id="ef092-262">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="ef092-263">Zapytania zawierające nową konstrukcję DateTime są teraz tłumaczone.</span><span class="sxs-lookup"><span data-stu-id="ef092-263">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="ef092-264">Ponadto następujące funkcje SQL Server są teraz mapowane:</span><span class="sxs-lookup"><span data-stu-id="ef092-264">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="ef092-265">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="ef092-265">DateDiffWeek</span></span>
* <span data-ttu-id="ef092-266">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="ef092-266">DateFromParts</span></span>

<span data-ttu-id="ef092-267">Przykład:</span><span class="sxs-lookup"><span data-stu-id="ef092-267">For example:</span></span>

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="ef092-268">Dokumentacja jest śledzona przez [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)problemu.</span><span class="sxs-lookup"><span data-stu-id="ef092-268">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="ef092-269">Tłumaczenie zapytania dla większej liczby konstrukcji tablicy</span><span class="sxs-lookup"><span data-stu-id="ef092-269">Query translations for more byte array constructs</span></span>

<span data-ttu-id="ef092-270">Zapytania używające właściwości Contains, Length, SequenceEqual itp. on-Byte [] są teraz tłumaczone na SQL.</span><span class="sxs-lookup"><span data-stu-id="ef092-270">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="ef092-271">Wstępna dokumentacja jest zawarta w [statusie tygodnia Ef 5 grudnia 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span><span class="sxs-lookup"><span data-stu-id="ef092-271">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="ef092-272">Dodatkowa dokumentacja jest śledzona przez [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)problemu.</span><span class="sxs-lookup"><span data-stu-id="ef092-272">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="ef092-273">Tłumaczenie zapytania do tyłu</span><span class="sxs-lookup"><span data-stu-id="ef092-273">Query translation for Reverse</span></span>

<span data-ttu-id="ef092-274">Zapytania z użyciem `Reverse` są teraz tłumaczone.</span><span class="sxs-lookup"><span data-stu-id="ef092-274">Queries using `Reverse` are now translated.</span></span>
<span data-ttu-id="ef092-275">Przykład:</span><span class="sxs-lookup"><span data-stu-id="ef092-275">For example:</span></span>

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="ef092-276">Dokumentacja jest śledzona przez [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)problemu.</span><span class="sxs-lookup"><span data-stu-id="ef092-276">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="ef092-277">Tłumaczenie zapytania dla operatorów bitowych</span><span class="sxs-lookup"><span data-stu-id="ef092-277">Query translation for bitwise operators</span></span>

<span data-ttu-id="ef092-278">Zapytania wykorzystujące operatory bitowe są teraz tłumaczone na przykład w większej liczbie przypadków:</span><span class="sxs-lookup"><span data-stu-id="ef092-278">Queries using bitwise operators are now translated in more cases For example:</span></span>

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="ef092-279">Dokumentacja jest śledzona przez [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)problemu.</span><span class="sxs-lookup"><span data-stu-id="ef092-279">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="ef092-280">Tłumaczenie zapytania dla ciągów w Cosmos</span><span class="sxs-lookup"><span data-stu-id="ef092-280">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="ef092-281">Zapytania korzystające z metod String zawierają, StartsWith i EndsWith są teraz tłumaczone przy użyciu dostawcy Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="ef092-281">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="ef092-282">Dokumentacja jest śledzona przez [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)problemu.</span><span class="sxs-lookup"><span data-stu-id="ef092-282">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
