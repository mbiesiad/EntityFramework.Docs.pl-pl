---
title: Co nowego w EF Core 5,0
description: Omówienie nowych funkcji w EF Core 5,0
author: ajcvickers
ms.date: 03/30/2020
uid: core/what-is-new/ef-core-5.0/whatsnew.md
ms.openlocfilehash: c902988920e3b1a6039808fe0658fc19dee2728a
ms.sourcegitcommit: 387cbd8109c0fc5ce6bdc85d0dec1aed72ad4c33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82103077"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="dc41f-103">Co nowego w EF Core 5,0</span><span class="sxs-lookup"><span data-stu-id="dc41f-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="dc41f-104">EF Core 5,0 jest obecnie w trakcie opracowywania.</span><span class="sxs-lookup"><span data-stu-id="dc41f-104">EF Core 5.0 is currently in development.</span></span>
<span data-ttu-id="dc41f-105">Ta strona będzie zawierać przegląd interesujących zmian wprowadzonych w każdej wersji zapoznawczej.</span><span class="sxs-lookup"><span data-stu-id="dc41f-105">This page will contain an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="dc41f-106">Ta strona nie duplikuje [planu dla EF Core 5,0](plan.md).</span><span class="sxs-lookup"><span data-stu-id="dc41f-106">This page does not duplicate the [plan for EF Core 5.0](plan.md).</span></span>
<span data-ttu-id="dc41f-107">Plan opisuje ogólne motywy dla EF Core 5,0, w tym wszystko, co planujemy uwzględnić przed wysyłką ostatecznej wersji.</span><span class="sxs-lookup"><span data-stu-id="dc41f-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

<span data-ttu-id="dc41f-108">Będziemy dodawać linki z tego miejsca do oficjalnej dokumentacji w trakcie jej publikacji.</span><span class="sxs-lookup"><span data-stu-id="dc41f-108">We will add links from here to the official documentation as it is published.</span></span>

## <a name="preview-3"></a><span data-ttu-id="dc41f-109">Wersja zapoznawcza 3</span><span class="sxs-lookup"><span data-stu-id="dc41f-109">Preview 3</span></span>

### <a name="filtered-include"></a><span data-ttu-id="dc41f-110">Filtr obejmujący</span><span class="sxs-lookup"><span data-stu-id="dc41f-110">Filtered Include</span></span>

<span data-ttu-id="dc41f-111">Metoda include obsługuje teraz filtrowanie uwzględnionych jednostek.</span><span class="sxs-lookup"><span data-stu-id="dc41f-111">The Include method now supports filtering of the entities included.</span></span>
<span data-ttu-id="dc41f-112">Przykład:</span><span class="sxs-lookup"><span data-stu-id="dc41f-112">For example:</span></span>

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="dc41f-113">To zapytanie będzie zwracać Blogi razem z poszczególnymi wpisami skojarzonymi, ale tylko wtedy, gdy tytuł wpisu zawiera "ser".</span><span class="sxs-lookup"><span data-stu-id="dc41f-113">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="dc41f-114">Pomiń i zrób można także użyć, aby zmniejszyć liczbę uwzględnionych jednostek.</span><span class="sxs-lookup"><span data-stu-id="dc41f-114">Skip and Take can also be used to reduce the number of included entities.</span></span>
<span data-ttu-id="dc41f-115">Przykład:</span><span class="sxs-lookup"><span data-stu-id="dc41f-115">For example:</span></span>
 
```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
<span data-ttu-id="dc41f-116">To zapytanie będzie zwracać blogi z co najwyżej pięcioma wpisami zawartymi w każdym blogu.</span><span class="sxs-lookup"><span data-stu-id="dc41f-116">This query will return blogs with at most five posts included on each blog.</span></span>

<span data-ttu-id="dc41f-117">Szczegółowe informacje znajdują się w [dokumentacji dołączanej](xref:core/querying/related-data#filtered-include) .</span><span class="sxs-lookup"><span data-stu-id="dc41f-117">See the [Include documentation](xref:core/querying/related-data#filtered-include) for full details.</span></span>

### <a name="new-modelbuilder-api-for-navigation-properties"></a><span data-ttu-id="dc41f-118">Nowy interfejs API element modelbuilder dla właściwości nawigacji</span><span class="sxs-lookup"><span data-stu-id="dc41f-118">New ModelBuilder API for navigation properties</span></span>

<span data-ttu-id="dc41f-119">Właściwości nawigacji są przede wszystkim konfigurowane podczas [definiowania relacji](xref:core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="dc41f-119">Navigation properties are primarily configured when [defining relationships](xref:core/modeling/relationships).</span></span>
<span data-ttu-id="dc41f-120">Jednak nowa `Navigation` Metoda może być używana w przypadkach, w których właściwości nawigacji wymagają dodatkowej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="dc41f-120">However, the new `Navigation` method can be used in the cases where navigation properties need additional configuration.</span></span>
<span data-ttu-id="dc41f-121">Na przykład, aby ustawić pole zapasowe dla nawigacji, gdy pole nie zostanie znalezione według Konwencji:</span><span class="sxs-lookup"><span data-stu-id="dc41f-121">For example, to set a backing field for the navigation when the field would not be found by convention:</span></span>

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

<span data-ttu-id="dc41f-122">Należy pamiętać, `Navigation` że interfejs API nie zastępuje konfiguracji relacji.</span><span class="sxs-lookup"><span data-stu-id="dc41f-122">Note that the `Navigation` API does not replace relationship configuration.</span></span>
<span data-ttu-id="dc41f-123">Zamiast tego umożliwia dodatkową konfigurację właściwości nawigacji w już odnalezionych lub zdefiniowanych relacjach.</span><span class="sxs-lookup"><span data-stu-id="dc41f-123">Instead it allows additional configuration of navigation properties in already discovered or defined relationships.</span></span>

<span data-ttu-id="dc41f-124">Dokumentacja jest śledzona przez [#2302](https://github.com/dotnet/EntityFramework.Docs/issues/2302)problemu.</span><span class="sxs-lookup"><span data-stu-id="dc41f-124">Documentation is tracked by issue [#2302](https://github.com/dotnet/EntityFramework.Docs/issues/2302).</span></span>

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a><span data-ttu-id="dc41f-125">Nowe parametry wiersza polecenia dla przestrzeni nazw i parametrów połączenia</span><span class="sxs-lookup"><span data-stu-id="dc41f-125">New command-line parameters for namespaces and connection strings</span></span> 

<span data-ttu-id="dc41f-126">Migracje i tworzenie szkieletów umożliwiają teraz określenie przestrzeni nazw w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="dc41f-126">Migrations and scaffolding now allow namespaces to be specified on the command line.</span></span>
<span data-ttu-id="dc41f-127">Na przykład w celu odtworzenia bazy danych, w której są umieszczane klasy kontekstu i modelu w różnych przestrzeniach nazw:</span><span class="sxs-lookup"><span data-stu-id="dc41f-127">For example, to reverse engineer a database putting the context and model classes in different namespaces:</span></span> 

```
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

<span data-ttu-id="dc41f-128">Ponadto parametry połączenia można teraz przekazywać do `database-update` polecenia:</span><span class="sxs-lookup"><span data-stu-id="dc41f-128">Also, a connection string can now be passed to the `database-update` command:</span></span>

```
dotnet ef database update --connection "connection string"
```

<span data-ttu-id="dc41f-129">Do poleceń programu PowerShell, które są używane w konsoli Menedżera pakietów programu VS, dodano również równoważne parametry.</span><span class="sxs-lookup"><span data-stu-id="dc41f-129">Equivalent parameters have also been added to the PowerShell commands used in the VS Package Manager Console.</span></span>

<span data-ttu-id="dc41f-130">Dokumentacja jest śledzona przez [#2303](https://github.com/dotnet/EntityFramework.Docs/issues/2303)problemu.</span><span class="sxs-lookup"><span data-stu-id="dc41f-130">Documentation is tracked by issue [#2303](https://github.com/dotnet/EntityFramework.Docs/issues/2303).</span></span>

### <a name="enabledetailederrors-has-returned"></a><span data-ttu-id="dc41f-131">EnableDetailedErrors</span><span class="sxs-lookup"><span data-stu-id="dc41f-131">EnableDetailedErrors has returned</span></span>

<span data-ttu-id="dc41f-132">Ze względu na wydajność EF nie wykonuje dodatkowych kontroli wartości null podczas odczytywania wartości z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="dc41f-132">For performance reasons, EF doesn't do additional null-checks when reading values from the database.</span></span>
<span data-ttu-id="dc41f-133">Może to spowodować, że wyjątki są trudne do wylogowania, gdy zostanie napotkany nieoczekiwany element null.</span><span class="sxs-lookup"><span data-stu-id="dc41f-133">This can result in exceptions that are hard to root-cause when an unexpected null is encountered.</span></span>

<span data-ttu-id="dc41f-134">Korzystanie `EnableDetailedErrors` z programu spowoduje dodanie dodatkowych kontroli wartości null do zapytań, takich jak w przypadku małego obciążenia wydajności, te błędy są łatwiejsze do śledzenia z przyczyn głównych.</span><span class="sxs-lookup"><span data-stu-id="dc41f-134">Using `EnableDetailedErrors` will add extra null checking to queries such that, for a small performance overhead, these errors are easier to trace back to a root cause.</span></span>  

<span data-ttu-id="dc41f-135">Przykład:</span><span class="sxs-lookup"><span data-stu-id="dc41f-135">For example:</span></span>
```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors 
        .UseSqlServer(Your.SqlServerConnectionString);
```

<span data-ttu-id="dc41f-136">Dokumentacja jest śledzona przez [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955)problemu.</span><span class="sxs-lookup"><span data-stu-id="dc41f-136">Documentation is tracked by issue [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="dc41f-137">Klucze partycji Cosmos</span><span class="sxs-lookup"><span data-stu-id="dc41f-137">Cosmos partition keys</span></span>

<span data-ttu-id="dc41f-138">Klucz partycji, który ma być używany dla danego zapytania, można teraz określić w zapytaniu.</span><span class="sxs-lookup"><span data-stu-id="dc41f-138">The partition key to use for a given query can now be specified in the query.</span></span>
<span data-ttu-id="dc41f-139">Przykład:</span><span class="sxs-lookup"><span data-stu-id="dc41f-139">For example:</span></span>

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

<span data-ttu-id="dc41f-140">Dokumentacja jest śledzona przez [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199)problemu.</span><span class="sxs-lookup"><span data-stu-id="dc41f-140">Documentation is tracked by issue [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span></span>

### <a name="support-for-the-sql-server-datalength-function"></a><span data-ttu-id="dc41f-141">Obsługa funkcji SQL Server DATALENGTH</span><span class="sxs-lookup"><span data-stu-id="dc41f-141">Support for the SQL Server DATALENGTH function</span></span>

<span data-ttu-id="dc41f-142">Dostęp do niego można uzyskać za pomocą `EF.Functions.DataLength` nowej metody.</span><span class="sxs-lookup"><span data-stu-id="dc41f-142">This can be accessed using the new `EF.Functions.DataLength` method.</span></span>
<span data-ttu-id="dc41f-143">Przykład:</span><span class="sxs-lookup"><span data-stu-id="dc41f-143">For example:</span></span>
```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
``` 

## <a name="preview-2"></a><span data-ttu-id="dc41f-144">Preview 2</span><span class="sxs-lookup"><span data-stu-id="dc41f-144">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="dc41f-145">Użyj atrybutu języka C#, aby określić pole zapasowe właściwości</span><span class="sxs-lookup"><span data-stu-id="dc41f-145">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="dc41f-146">Atrybut języka C# może być teraz używany do określania pola zapasowego dla właściwości.</span><span class="sxs-lookup"><span data-stu-id="dc41f-146">A C# attribute can now be used to specify the backing field for a property.</span></span>
<span data-ttu-id="dc41f-147">Ten atrybut pozwala EF Core nadal pisać i odczytywać dane z pola zapasowego, tak jak zwykle, nawet jeśli nie można automatycznie znaleźć pola zapasowego.</span><span class="sxs-lookup"><span data-stu-id="dc41f-147">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span>
<span data-ttu-id="dc41f-148">Przykład:</span><span class="sxs-lookup"><span data-stu-id="dc41f-148">For example:</span></span>

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

<span data-ttu-id="dc41f-149">Dokumentacja jest śledzona przez [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230)problemu.</span><span class="sxs-lookup"><span data-stu-id="dc41f-149">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="dc41f-150">Kompletne mapowanie rozróżniacza</span><span class="sxs-lookup"><span data-stu-id="dc41f-150">Complete discriminator mapping</span></span>

<span data-ttu-id="dc41f-151">EF Core używa kolumny rozróżniacza dla [mapowania TPH hierarchii dziedziczenia](/ef/core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="dc41f-151">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](/ef/core/modeling/inheritance).</span></span>
<span data-ttu-id="dc41f-152">Niektóre ulepszenia wydajności są możliwe tak długo, jak EF Core wie wszystkie możliwe wartości dla rozróżniacza.</span><span class="sxs-lookup"><span data-stu-id="dc41f-152">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span>
<span data-ttu-id="dc41f-153">EF Core 5,0 implementuje teraz te ulepszenia.</span><span class="sxs-lookup"><span data-stu-id="dc41f-153">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="dc41f-154">Na przykład poprzednie wersje programu EF Core zawsze generują te dane SQL dla zapytania zwracającego wszystkie typy w hierarchii:</span><span class="sxs-lookup"><span data-stu-id="dc41f-154">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="dc41f-155">EF Core 5,0 będzie teraz generować następujące elementy, gdy zostanie skonfigurowane kompletne mapowanie rozróżniacza:</span><span class="sxs-lookup"><span data-stu-id="dc41f-155">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="dc41f-156">Będzie to domyślne zachowanie rozpoczynające się od wersji zapoznawczej 3.</span><span class="sxs-lookup"><span data-stu-id="dc41f-156">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="dc41f-157">Ulepszenia wydajności w programie Microsoft. Data. sqlite</span><span class="sxs-lookup"><span data-stu-id="dc41f-157">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="dc41f-158">Wprowadziliśmy dwie ulepszenia wydajności dla oprogramowania SQLIte:</span><span class="sxs-lookup"><span data-stu-id="dc41f-158">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="dc41f-159">Pobieranie danych binarnych i ciągów za pomocą metody GetBytes, GetChars i GetTextReader jest teraz wydajniejsze dzięki użyciu SqliteBlob i strumieni.</span><span class="sxs-lookup"><span data-stu-id="dc41f-159">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="dc41f-160">Inicjalizacja SqliteConnection jest teraz opóźniona.</span><span class="sxs-lookup"><span data-stu-id="dc41f-160">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="dc41f-161">Te udoskonalenia znajdują się w dostawcy ADO.NET Microsoft. Data. SQLite, a tym samym zwiększyć wydajność poza EF Core.</span><span class="sxs-lookup"><span data-stu-id="dc41f-161">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="dc41f-162">Wersja zapoznawcza 1</span><span class="sxs-lookup"><span data-stu-id="dc41f-162">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="dc41f-163">Rejestrowanie proste</span><span class="sxs-lookup"><span data-stu-id="dc41f-163">Simple logging</span></span>

<span data-ttu-id="dc41f-164">Ta funkcja dodaje funkcje podobne do `Database.Log` programu w programie Ef6.</span><span class="sxs-lookup"><span data-stu-id="dc41f-164">This feature adds functionality similar to `Database.Log` in EF6.</span></span>
<span data-ttu-id="dc41f-165">Oznacza to, że zapewnia prosty sposób pobierania dzienników z EF Core bez konieczności konfigurowania żadnego rodzaju zewnętrznej platformy rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="dc41f-165">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="dc41f-166">Wstępna dokumentacja jest zawarta w [statusie tygodnia Ef 5 grudnia 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span><span class="sxs-lookup"><span data-stu-id="dc41f-166">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="dc41f-167">Dodatkowa dokumentacja jest śledzona przez [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085)problemu.</span><span class="sxs-lookup"><span data-stu-id="dc41f-167">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="dc41f-168">Prosty sposób uzyskiwania wygenerowanego kodu SQL</span><span class="sxs-lookup"><span data-stu-id="dc41f-168">Simple way to get generated SQL</span></span>

<span data-ttu-id="dc41f-169">EF Core 5,0 wprowadza metodę `ToQueryString` rozszerzenia, która zwróci kod SQL, który EF Core zostanie wygenerowany podczas wykonywania zapytania LINQ.</span><span class="sxs-lookup"><span data-stu-id="dc41f-169">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="dc41f-170">Wstępna dokumentacja jest uwzględniona w [statusie tygodniowym EF dla 9 stycznia 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span><span class="sxs-lookup"><span data-stu-id="dc41f-170">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="dc41f-171">Dodatkowa dokumentacja jest śledzona przez [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331)problemu.</span><span class="sxs-lookup"><span data-stu-id="dc41f-171">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="dc41f-172">Użyj atrybutu języka C#, aby wskazać, że jednostka nie ma klucza</span><span class="sxs-lookup"><span data-stu-id="dc41f-172">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="dc41f-173">Typ jednostki można teraz skonfigurować jako bez klucza przy użyciu nowego `KeylessAttribute`elementu.</span><span class="sxs-lookup"><span data-stu-id="dc41f-173">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span>
<span data-ttu-id="dc41f-174">Przykład:</span><span class="sxs-lookup"><span data-stu-id="dc41f-174">For example:</span></span>

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="dc41f-175">Dokumentacja jest śledzona przez [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186)problemu.</span><span class="sxs-lookup"><span data-stu-id="dc41f-175">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="dc41f-176">Parametry Connection lub Connection można zmienić w zainicjowanym kontekście DbContext</span><span class="sxs-lookup"><span data-stu-id="dc41f-176">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="dc41f-177">Teraz łatwiej jest utworzyć wystąpienie DbContext bez żadnego połączenia lub parametrów połączenia.</span><span class="sxs-lookup"><span data-stu-id="dc41f-177">It is now easier to create a DbContext instance without any connection or connection string.</span></span>
<span data-ttu-id="dc41f-178">Ponadto parametry połączenia lub połączenia można teraz przystąpić do wystąpienia kontekstu.</span><span class="sxs-lookup"><span data-stu-id="dc41f-178">Also, the connection or connection string can now be mutated on the context instance.</span></span>
<span data-ttu-id="dc41f-179">Ta funkcja pozwala temu samemu wystąpieniu kontekstu na dynamiczne łączenie się z różnymi bazami danych.</span><span class="sxs-lookup"><span data-stu-id="dc41f-179">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="dc41f-180">Dokumentacja jest śledzona przez [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075)problemu.</span><span class="sxs-lookup"><span data-stu-id="dc41f-180">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="dc41f-181">Serwery proxy śledzenia zmian</span><span class="sxs-lookup"><span data-stu-id="dc41f-181">Change-tracking proxies</span></span>

<span data-ttu-id="dc41f-182">EF Core mogą teraz generować serwery proxy środowiska uruchomieniowego, które automatycznie implementują [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) i [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span><span class="sxs-lookup"><span data-stu-id="dc41f-182">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) and [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span></span>
<span data-ttu-id="dc41f-183">Następnie te zmiany są raportowane w oparciu o właściwości jednostki bezpośrednio do EF Core, unikając konieczności skanowania pod kątem zmian.</span><span class="sxs-lookup"><span data-stu-id="dc41f-183">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span>
<span data-ttu-id="dc41f-184">Jednak serwery proxy są dostarczane z własnym zestawem ograniczeń, więc nie są przeznaczone dla wszystkich użytkowników.</span><span class="sxs-lookup"><span data-stu-id="dc41f-184">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="dc41f-185">Dokumentacja jest śledzona przez [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076)problemu.</span><span class="sxs-lookup"><span data-stu-id="dc41f-185">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="dc41f-186">Udoskonalone widoki debugowania</span><span class="sxs-lookup"><span data-stu-id="dc41f-186">Enhanced debug views</span></span>

<span data-ttu-id="dc41f-187">Widoki debugowania to prosty sposób na zajrzeć do wewnętrznych EF Core w przypadku problemów z debugowaniem.</span><span class="sxs-lookup"><span data-stu-id="dc41f-187">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span>
<span data-ttu-id="dc41f-188">Widok debugowania dla modelu został zaimplementowany jakiś czas temu.</span><span class="sxs-lookup"><span data-stu-id="dc41f-188">A debug view for the Model was implemented some time ago.</span></span>
<span data-ttu-id="dc41f-189">W przypadku EF Core 5,0 ten widok modelu jest łatwiejszy do odczytania i dodania nowego widoku debugowania dla śledzonych jednostek w Menedżerze stanu.</span><span class="sxs-lookup"><span data-stu-id="dc41f-189">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="dc41f-190">Wstępna dokumentacja jest uwzględniona w [Stanach tygodnia EF 12 grudnia 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span><span class="sxs-lookup"><span data-stu-id="dc41f-190">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="dc41f-191">Dodatkowa dokumentacja jest śledzona przez [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086)problemu.</span><span class="sxs-lookup"><span data-stu-id="dc41f-191">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="dc41f-192">Ulepszona obsługa semantyki o wartości null bazy danych</span><span class="sxs-lookup"><span data-stu-id="dc41f-192">Improved handling of database null semantics</span></span>

<span data-ttu-id="dc41f-193">Relacyjne bazy danych zazwyczaj traktują wartości NULL jako nieznaną wartość i w związku z tym nie są równe żadnym innym NULL.</span><span class="sxs-lookup"><span data-stu-id="dc41f-193">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span>
<span data-ttu-id="dc41f-194">Chociaż język C# traktuje wartość null w postaci wartości zdefiniowanej, co porównuje ją z innymi wartościami null.</span><span class="sxs-lookup"><span data-stu-id="dc41f-194">While C# treats null as a defined value, which compares equal to any other null.</span></span>
<span data-ttu-id="dc41f-195">EF Core domyślnie tłumaczy zapytania tak, aby korzystały z semantyki wartości null języka C#.</span><span class="sxs-lookup"><span data-stu-id="dc41f-195">EF Core by default translates queries so that they use C# null semantics.</span></span>
<span data-ttu-id="dc41f-196">EF Core 5,0 znacznie poprawia wydajność tych tłumaczeń.</span><span class="sxs-lookup"><span data-stu-id="dc41f-196">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="dc41f-197">Dokumentacja jest śledzona przez [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612)problemu.</span><span class="sxs-lookup"><span data-stu-id="dc41f-197">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="dc41f-198">Właściwości indeksatora</span><span class="sxs-lookup"><span data-stu-id="dc41f-198">Indexer properties</span></span>

<span data-ttu-id="dc41f-199">EF Core 5,0 obsługuje mapowanie właściwości indeksatora języka C#.</span><span class="sxs-lookup"><span data-stu-id="dc41f-199">EF Core 5.0 supports mapping of C# indexer properties.</span></span>
<span data-ttu-id="dc41f-200">Te właściwości umożliwiają jednostkom działanie jako zbiory właściwości, w których kolumny są mapowane na nazwane właściwości w zbiorze.</span><span class="sxs-lookup"><span data-stu-id="dc41f-200">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="dc41f-201">Dokumentacja jest śledzona przez [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018)problemu.</span><span class="sxs-lookup"><span data-stu-id="dc41f-201">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="dc41f-202">Generowanie ograniczeń check dla mapowań wyliczenia</span><span class="sxs-lookup"><span data-stu-id="dc41f-202">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="dc41f-203">Migracje EF Core 5,0 mogą teraz generować ograniczenia CHECK dla mapowań właściwości enum.</span><span class="sxs-lookup"><span data-stu-id="dc41f-203">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span>
<span data-ttu-id="dc41f-204">Przykład:</span><span class="sxs-lookup"><span data-stu-id="dc41f-204">For example:</span></span>

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="dc41f-205">Dokumentacja jest śledzona przez [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082)problemu.</span><span class="sxs-lookup"><span data-stu-id="dc41f-205">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="dc41f-206">Isrelacyjne</span><span class="sxs-lookup"><span data-stu-id="dc41f-206">IsRelational</span></span>

<span data-ttu-id="dc41f-207">Dodano nową `IsRelational` metodę oprócz istniejących `IsSqlServer`, `IsSqlite`i. `IsInMemory`</span><span class="sxs-lookup"><span data-stu-id="dc41f-207">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span>
<span data-ttu-id="dc41f-208">Tej metody można użyć do sprawdzenia, czy DbContext używa dowolnego dostawcy relacyjnej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="dc41f-208">This method can be used to test if the DbContext is using any relational database provider.</span></span>
<span data-ttu-id="dc41f-209">Przykład:</span><span class="sxs-lookup"><span data-stu-id="dc41f-209">For example:</span></span>

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="dc41f-210">Dokumentacja jest śledzona przez [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185)problemu.</span><span class="sxs-lookup"><span data-stu-id="dc41f-210">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="dc41f-211">Cosmos optymistyczne współbieżność za pomocą elementów ETag</span><span class="sxs-lookup"><span data-stu-id="dc41f-211">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="dc41f-212">Dostawca bazy danych Azure Cosmos DB obsługuje teraz optymistyczne współbieżność za pomocą elementów ETag.</span><span class="sxs-lookup"><span data-stu-id="dc41f-212">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span>
<span data-ttu-id="dc41f-213">Użyj konstruktora modeli w OnModelCreating, aby skonfigurować element ETag:</span><span class="sxs-lookup"><span data-stu-id="dc41f-213">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="dc41f-214">Metody SaveChanges następnie `DbUpdateConcurrencyException` zgłosi konflikt współbieżności, który [można obsłużyć](https://docs.microsoft.com/ef/core/saving/concurrency) w celu zaimplementowania ponownych prób itd.</span><span class="sxs-lookup"><span data-stu-id="dc41f-214">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](https://docs.microsoft.com/ef/core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="dc41f-215">Dokumentacja jest śledzona przez [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099)problemu.</span><span class="sxs-lookup"><span data-stu-id="dc41f-215">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="dc41f-216">Tłumaczenie zapytań dla większej liczby konstrukcji DateTime</span><span class="sxs-lookup"><span data-stu-id="dc41f-216">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="dc41f-217">Zapytania zawierające nową konstrukcję DateTime są teraz tłumaczone.</span><span class="sxs-lookup"><span data-stu-id="dc41f-217">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="dc41f-218">Ponadto następujące funkcje SQL Server są teraz mapowane:</span><span class="sxs-lookup"><span data-stu-id="dc41f-218">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="dc41f-219">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="dc41f-219">DateDiffWeek</span></span>
* <span data-ttu-id="dc41f-220">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="dc41f-220">DateFromParts</span></span>

<span data-ttu-id="dc41f-221">Przykład:</span><span class="sxs-lookup"><span data-stu-id="dc41f-221">For example:</span></span>

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="dc41f-222">Dokumentacja jest śledzona przez [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)problemu.</span><span class="sxs-lookup"><span data-stu-id="dc41f-222">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="dc41f-223">Tłumaczenie zapytania dla większej liczby konstrukcji tablicy</span><span class="sxs-lookup"><span data-stu-id="dc41f-223">Query translations for more byte array constructs</span></span>

<span data-ttu-id="dc41f-224">Zapytania używające właściwości Contains, Length, SequenceEqual itp. on-Byte [] są teraz tłumaczone na SQL.</span><span class="sxs-lookup"><span data-stu-id="dc41f-224">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="dc41f-225">Wstępna dokumentacja jest zawarta w [statusie tygodnia Ef 5 grudnia 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span><span class="sxs-lookup"><span data-stu-id="dc41f-225">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="dc41f-226">Dodatkowa dokumentacja jest śledzona przez [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)problemu.</span><span class="sxs-lookup"><span data-stu-id="dc41f-226">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="dc41f-227">Tłumaczenie zapytania do tyłu</span><span class="sxs-lookup"><span data-stu-id="dc41f-227">Query translation for Reverse</span></span>

<span data-ttu-id="dc41f-228">Zapytania z `Reverse` użyciem są teraz tłumaczone.</span><span class="sxs-lookup"><span data-stu-id="dc41f-228">Queries using `Reverse` are now translated.</span></span>
<span data-ttu-id="dc41f-229">Przykład:</span><span class="sxs-lookup"><span data-stu-id="dc41f-229">For example:</span></span>

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="dc41f-230">Dokumentacja jest śledzona przez [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)problemu.</span><span class="sxs-lookup"><span data-stu-id="dc41f-230">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="dc41f-231">Tłumaczenie zapytania dla operatorów bitowych</span><span class="sxs-lookup"><span data-stu-id="dc41f-231">Query translation for bitwise operators</span></span>

<span data-ttu-id="dc41f-232">Zapytania wykorzystujące operatory bitowe są teraz tłumaczone na przykład w większej liczbie przypadków:</span><span class="sxs-lookup"><span data-stu-id="dc41f-232">Queries using bitwise operators are now translated in more cases For example:</span></span>

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="dc41f-233">Dokumentacja jest śledzona przez [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)problemu.</span><span class="sxs-lookup"><span data-stu-id="dc41f-233">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="dc41f-234">Tłumaczenie zapytania dla ciągów w Cosmos</span><span class="sxs-lookup"><span data-stu-id="dc41f-234">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="dc41f-235">Zapytania korzystające z metod String zawierają, StartsWith i EndsWith są teraz tłumaczone przy użyciu dostawcy Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="dc41f-235">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="dc41f-236">Dokumentacja jest śledzona przez [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)problemu.</span><span class="sxs-lookup"><span data-stu-id="dc41f-236">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
