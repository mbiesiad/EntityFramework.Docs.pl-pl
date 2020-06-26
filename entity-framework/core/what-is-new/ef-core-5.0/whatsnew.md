---
title: Co nowego w EF Core 5,0
description: Omówienie nowych funkcji w EF Core 5,0
author: ajcvickers
ms.date: 06/02/2020
uid: core/what-is-new/ef-core-5.0/whatsnew
ms.openlocfilehash: 0a2ba5b804cc6636b321edcc48feeb76ad60560b
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370373"
---
# <a name="whats-new-in-ef-core-50"></a>Co nowego w EF Core 5,0

EF Core 5,0 jest obecnie w trakcie opracowywania. Ta strona będzie zawierać przegląd interesujących zmian wprowadzonych w każdej wersji zapoznawczej.

Ta strona nie duplikuje [planu dla EF Core 5,0](xref:core/what-is-new/ef-core-5.0/plan). Plan opisuje ogólne motywy dla EF Core 5,0, w tym wszystko, co planujemy uwzględnić przed wysyłką ostatecznej wersji.

Będziemy dodawać linki z tego miejsca do oficjalnej dokumentacji w trakcie jej publikacji.

## <a name="preview-6"></a>Wersja zapoznawcza 6

### <a name="split-queries-for-related-collections"></a>Podziel zapytania dla powiązanych kolekcji

Począwszy od EF Core 3,0, EF Core zawsze generuje pojedyncze zapytanie SQL dla każdej kwerendy LINQ. Zapewnia to spójność danych zwracanych w ramach ograniczeń trybu transakcji w użyciu. Jednak może to potrwać bardzo wolno, gdy zapytanie używa `Include` lub projekcji, aby przywrócić wiele powiązanych kolekcji.

EF Core 5,0 umożliwia teraz pojedyncze zapytanie LINQ, w tym powiązane kolekcje, które mają być podzielone na wiele zapytań SQL. Może to znacząco poprawić wydajność, ale może spowodować niespójność wyników zwróconych w przypadku zmiany danych między dwoma zapytaniami. Można użyć transakcji do serializacji lub migawek, aby ograniczyć to i zapewnić spójność z zapytaniami z podziałem, ale może to spowodować inne koszty wydajności i różnice w zachowaniu.

#### <a name="split-queries-with-include"></a>Rozdziel zapytania za pomocą dyrektywy include

Rozważmy na przykład zapytanie, które pobiera dwa poziomy pokrewnych kolekcji przy użyciu `Include` :

```CSharp
var artists = context.Artists
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

Domyślnie EF Core będzie generować następujące SQL podczas korzystania z dostawcy oprogramowania SQLite:

```sql
SELECT "a"."Id", "a"."Name", "t0"."Id", "t0"."ArtistId", "t0"."Title", "t0"."Id0", "t0"."AlbumId", "t0"."Name"
FROM "Artists" AS "a"
LEFT JOIN (
    SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "t"."Id" AS "Id0", "t"."AlbumId", "t"."Name"
    FROM "Album" AS "a0"
    LEFT JOIN "Tag" AS "t" ON "a0"."Id" = "t"."AlbumId"
) AS "t0" ON "a"."Id" = "t0"."ArtistId"
ORDER BY "a"."Id", "t0"."Id", "t0"."Id0"
```

`AsSplitQuery`Aby zmienić to zachowanie, można użyć nowego interfejsu API. Na przykład:

```CSharp
var artists = context.Artists
    .AsSplitQuery()
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

AsSplitQuery jest dostępny dla wszystkich dostawców relacyjnych baz danych i można go używać w dowolnym miejscu zapytania, podobnie jak AsNoTracking. EF Core będzie teraz generować następujące trzy zapytania SQL:

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id", "a0"."Id"

SELECT "t"."Id", "t"."AlbumId", "t"."Name", "a"."Id", "a0"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
INNER JOIN "Tag" AS "t" ON "a0"."Id" = "t"."AlbumId"
ORDER BY "a"."Id", "a0"."Id"
```

Wszystkie operacje w katalogu głównym zapytania są obsługiwane. Obejmuje to operacje OrderBy/Skip/Take, join, FirstOrDefault i podobne pojedyncze wyniki wybierające operacje.

Zwróć uwagę, że filtrowane elementy include z OrderBy/Skip/Take nie są obsługiwane w wersji zapoznawczej 6, ale są dostępne w codziennych kompilacjach i zostaną uwzględnione w wersji zapoznawczej 7.

#### <a name="split-queries-with-collection-projections"></a>Podziel zapytania z projekcjami kolekcji

`AsSplitQuery`można go również użyć, gdy kolekcje są ładowane do projekcji. Na przykład:

```CSharp
context.Artists
    .AsSplitQuery()
    .Select(e => new
    {
        Artist = e,
        Albums = e.Albums,
    }).ToList();
```

Ta kwerenda LINQ generuje następujące dwa zapytania SQL, gdy jest używany dostawca oprogramowania SQLite:

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id"
```

Należy zauważyć, że obsługiwana jest tylko materializację kolekcji. Wszelkie kompozycje po `e.Albums` powyższym przypadku nie spowodują przetworzenia zapytania podzielonego. Ulepszenia w tym obszarze są śledzone przez [#21234](https://github.com/dotnet/efcore/issues/21234).

### <a name="indexattribute"></a>Indexattribute

Nowy element Indexattribute może być umieszczony w typie jednostki, aby określić indeks dla pojedynczej kolumny. Na przykład:

```CSharp
[Index(nameof(FullName), IsUnique = true)]
public class User
{
    public int Id { get; set; }
    
    [MaxLength(128)]
    public string FullName { get; set; }
}
```

W przypadku SQL Server migracja będzie generować następujące SQL:

```sql
CREATE UNIQUE INDEX [IX_Users_FullName]
    ON [Users] ([FullName])
    WHERE [FullName] IS NOT NULL;
```

W celu określenia indeksu obejmującego wiele kolumn można także użyć elementu indexattribute. Na przykład:

```CSharp
[Index(nameof(FirstName), nameof(LastName), IsUnique = true)]
public class User
{
    public int Id { get; set; }
    
    [MaxLength(64)]
    public string FirstName { get; set; }

    [MaxLength(64)]
    public string LastName { get; set; }
}
```

W przypadku SQL Server powoduje to:

```sql
CREATE UNIQUE INDEX [IX_Users_FirstName_LastName]
    ON [Users] ([FirstName], [LastName])
    WHERE [FirstName] IS NOT NULL AND [LastName] IS NOT NULL;
```

Dokumentacja jest śledzona przez [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407)problemu.

### <a name="improved-query-translation-exceptions"></a>Ulepszone wyjątki translacji zapytań

Kontynuujemy ulepszanie komunikatów o wyjątkach generowanych w przypadku niepowodzenia tłumaczenia zapytań. Na przykład to zapytanie używa niezamapowanej właściwości `IsSigned` :

```CSharp
var artists = context.Artists.Where(e => e.IsSigned).ToList();
```

EF Core zgłosi następujący wyjątek wskazujący, że tłumaczenie nie powiodło się, ponieważ `IsSigned` nie jest zamapowany:

> Nieobsługiwany wyjątek. System. InvalidOperationException: wyrażenie LINQ "Nieogólnymi <Artist> (). Gdzie nie można przetłumaczyć (a => a. IsSigned) '. Informacje dodatkowe: Tłumaczenie elementu członkowskiego "IsSigned" w typie jednostki "artyści" nie powiodło się. Prawdopodobnie określony element członkowski nie jest zamapowany. Ponownie Napisz zapytanie w formularzu, który można przetłumaczyć, lub Przełącz się na ocenę klienta jawnie, wstawiając wywołanie do AsEnumerable (), AsAsyncEnumerable (), ToList — () lub ToListAsync (). Aby uzyskać więcej informacji, zobacz https://go.microsoft.com/fwlink/?linkid=2101038.

Podobnie w przypadku próby przetłumaczenia porównania ciągów z semantyką zależną od kultury są teraz generowane lepsze komunikaty o wyjątkach. Na przykład ta kwerenda próbuje użyć `StringComparison.CurrentCulture` :

```CSharp
var artists = context.Artists
    .Where(e => e.Name.Equals("The Unicorns", StringComparison.CurrentCulture))
    .ToList();
```

EF Core będzie teraz zgłosić następujący wyjątek:

> Nieobsługiwany wyjątek. System. InvalidOperationException: wyrażenie LINQ "Nieogólnymi <Artist> (). Gdzie (a => a. Name. Equals (wartość: "różowo", comparisonType: CurrentCulture)) "nie można przetłumaczyć. Informacje dodatkowe: tłumaczenie ciągu. Metoda Equals, która pobiera argument "StringComparison", nie jest obsługiwana. Aby uzyskać więcej informacji, zobacz https://go.microsoft.com/fwlink/?linkid=2129535. Ponownie Napisz zapytanie w formularzu, który można przetłumaczyć, lub Przełącz się na ocenę klienta jawnie, wstawiając wywołanie do AsEnumerable (), AsAsyncEnumerable (), ToList — () lub ToListAsync (). Aby uzyskać więcej informacji, zobacz https://go.microsoft.com/fwlink/?linkid=2101038.

### <a name="specify-transaction-id"></a>Określ identyfikator transakcji

Ta funkcja została pobrana ze społeczności przez program [@Marusyk](https://github.com/Marusyk) . Wiele Dziękujemy za udział!

EF Core uwidacznia identyfikator transakcji dla korelacji transakcji w wywołaniach. Ten identyfikator jest zazwyczaj ustawiany przez EF Core po rozpoczęciu transakcji. Jeśli zamiast tego aplikacja uruchamia transakcję, ta funkcja umożliwia aplikacji jawne ustawienie identyfikatora transakcji, dzięki czemu jest on skorelowany prawidłowo wszędzie tam, gdzie jest używana. Na przykład:

```CSharp
using (context.Database.UseTransaction(myTransaction, myId))
{
   ...
}
```

### <a name="ipaddress-mapping"></a>Mapowanie adresu IP

Ta funkcja została pobrana ze społeczności przez program [@ralmsdeveloper](https://github.com/ralmsdeveloper) . Wiele Dziękujemy za udział!

Standardowa [Klasa adresu IP](/dotnet/api/system.net.ipaddress) platformy .NET jest teraz automatycznie mapowana na kolumnę ciągów dla baz danych, które nie mają jeszcze natywnej obsługi. Rozważmy na przykład mapowanie tego typu jednostki:

```CSharp
public class Host
{
    public int Id { get; set; }
    public IPAddress Address { get; set; }
}
```

W przypadku SQL Server spowoduje to migrację, tworząc następującą tabelę:

```sql
CREATE TABLE [Host] (
    [Id] int NOT NULL,
    [Address] nvarchar(45) NULL,
    CONSTRAINT [PK_Host] PRIMARY KEY ([Id]));
``` 

Jednostki można następnie dodać w zwykły sposób:

```CSharp
context.AddRange(
    new Host { Address = IPAddress.Parse("127.0.0.1")},
    new Host { Address = IPAddress.Parse("0000:0000:0000:0000:0000:0000:0000:0001")});
``` 

A w efekcie w programie SQL Server zostanie wstawiony znormalizowany adres IPv4 lub IPv6:

```sql
Executed DbCommand (14ms) [Parameters=[@p0='1', @p1='127.0.0.1' (Size = 45), @p2='2', @p3='::1' (Size = 45)], CommandType='Text', CommandTimeout='30']
      SET NOCOUNT ON;
      INSERT INTO [Host] ([Id], [Address])
      VALUES (@p0, @p1), (@p2, @p3);
```

### <a name="exclude-onconfiguring-when-scaffolding"></a>Wyklucz onkonfigurowany podczas tworzenia szkieletów

Gdy DbContext jest szkieletem z istniejącej bazy danych, EF Core domyślnie tworzy Przeciążenie przy użyciu parametrów połączenia, dzięki czemu kontekst jest natychmiast użyteczny. Nie jest to jednak przydatne, jeśli masz już klasę częściową z funkcją onconfiguring lub w przypadku konfigurowania kontekstu w inny sposób.

Aby rozwiązać ten potrzeba, można teraz wydać polecenia tworzenia szkieletu, aby pominąć generowanie elementu onconfiguring. Na przykład:

```
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer --no-onconfiguring
```

Lub w konsoli Menedżera pakietów:

```
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer -NoOnConfiguring 
``` 

Należy pamiętać, że zalecamy użycie [nazwanego ciągu połączenia i bezpiecznego magazynu, takiego jak wpisy tajne użytkownika](/core/managing-schemas/scaffolding?tabs=vs#configuration-and-user-secrets).

### <a name="translations-for-firstordefault-on-strings"></a>Tłumaczenia dla FirstOrDefault na ciągach

Ta funkcja została pobrana ze społeczności przez program [@dvoreckyaa](https://github.com/dvoreckyaa) . Wiele Dziękujemy za udział!

FirstOrDefault i podobne operatory dla znaków w ciągach są teraz tłumaczone. Na przykład ta kwerenda LINQ:

```CSharp
context.Customers.Where(c => c.ContactName.FirstOrDefault() == 'A').ToList();
```

Zostaną przetłumaczone na następujące SQL podczas korzystania z SQL Server:

```sql
SELECT [c].[Id], [c].[ContactName]
FROM [Customer] AS [c]
WHERE SUBSTRING([c].[ContactName], 1, 1) = N'A'
```

### <a name="simplify-case-blocks"></a>Uprość bloki wielkości liter

EF Core teraz generuje lepsze zapytania z blokami przypadków. Na przykład ta kwerenda LINQ: 

```CSharp
context.Weapons
    .OrderBy(w => w.Name.CompareTo("Marcus' Lancer") == 0)
    .ThenBy(w => w.Id)
```

Był włączony SQL Server przetłumaczony na:

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN (CASE
        WHEN [w].[Name] = N'Marcus'' Lancer' THEN 0
        WHEN [w].[Name] > N'Marcus'' Lancer' THEN 1
        WHEN [w].[Name] < N'Marcus'' Lancer' THEN -1
    END = 0) AND CASE
        WHEN [w].[Name] = N'Marcus'' Lancer' THEN 0
        WHEN [w].[Name] > N'Marcus'' Lancer' THEN 1
        WHEN [w].[Name] < N'Marcus'' Lancer' THEN -1
    END IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
``` 

Jest teraz tłumaczone na:

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN ([w].[Name] = N'Marcus'' Lancer') AND [w].[Name] IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
``` 

## <a name="preview-5"></a>Wersja zapoznawcza 5

### <a name="database-collations"></a>Sortowanie bazy danych

Domyślne sortowanie dla bazy danych można teraz określić w modelu EF. Spowoduje to przetworzenie przetworzonych migracji w celu ustawienia sortowania podczas tworzenia bazy danych. Na przykład:

```CSharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

Następnie migracja generuje następujące elementy, aby utworzyć bazę danych na SQL Server:

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

Można również określić sortowanie, które ma być używane dla określonych kolumn bazy danych. Na przykład:

```CSharp
 modelBuilder
     .Entity<User>()
     .Property(e => e.Name)
     .UseCollation("German_PhoneBook_CI_AS");
```

Dla tych, które nie korzystają z migracji, sortowania są teraz odtwarzane z bazy danych podczas tworzenia szkieletu DbContext.

Na koniec `EF.Functions.Collate()` zezwala na kwerendy ad hoc przy użyciu różnych ustawień sortowania. Na przykład:

```CSharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

Spowoduje to wygenerowanie następującej kwerendy dla SQL Server:

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

Należy pamiętać, że sortowania ad-hoc należy używać z ostrożnością, ponieważ mogą one mieć negatywny wpływ na wydajność bazy danych.

Dokumentacja jest śledzona przez [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273)problemu.

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a>Argumenty przepływu w IDesignTimeDbContextFactory

Argumenty są teraz przepływane z wiersza polecenia do `CreateDbContext` metody [IDesignTimeDbContextFactory](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1). Na przykład, aby wskazać, że jest to kompilacja dev, argument niestandardowy (np. `dev` ) może zostać zakończony w wierszu polecenia:

```
dotnet ef migrations add two --verbose --dev
``` 

Ten argument będzie przepływał do fabryki, gdzie może służyć do kontrolowania sposobu tworzenia i inicjowania kontekstu. Na przykład:

```CSharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args) 
        => new SomeDbContext(args.Contains("--dev"));
}
```

Dokumentacja jest śledzona przez [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419)problemu.

### <a name="no-tracking-queries-with-identity-resolution"></a>Nie śledzij zapytań z rozpoznawaniem tożsamości

Nie można teraz skonfigurować zapytań śledzenia, aby przeprowadzić rozpoznawanie tożsamości. Na przykład następujące zapytanie utworzy nowe wystąpienie blogu dla każdego wpisu, nawet jeśli każdy blog ma ten sam klucz podstawowy. 

```CSharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

Jednak koszt zwykle jest nieco wolniejszy i zawsze korzysta z większej ilości pamięci, to zapytanie można zmienić, aby upewnić się, że tworzone jest tylko jedno wystąpienie blogu:

```CSharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

Należy zauważyć, że jest to przydatne tylko w przypadku zapytań bez śledzenia, ponieważ wszystkie zapytania śledzenia już wykazują takie zachowanie. Ponadto, po przeglądzie interfejsu API, `PerformIdentityResolution` składnia zostanie zmieniona. Zobacz [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).

Dokumentacja jest śledzona przez [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895)problemu.

### <a name="stored-persisted-computed-columns"></a>Przechowywane (utrwalone) kolumny obliczane

Większość baz danych umożliwia przechowywanie wartości kolumn obliczanych po obliczeniach. Gdy to zajmuje miejsce na dysku, kolumna obliczana jest obliczana tylko raz podczas aktualizacji, a nie za każdym razem, gdy jej wartość zostanie pobrana. Pozwala to również na indeksowanie kolumny dla niektórych baz danych.

EF Core 5,0 umożliwia skonfigurowanie kolumn obliczanych jako przechowywanych. Na przykład:
 
```CSharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a>Kolumny obliczane przez SQLite

EF Core teraz obsługuje kolumny obliczane w bazach danych oprogramowania SQLite.

## <a name="preview-4"></a>Wersja zapoznawcza 4

### <a name="configure-database-precisionscale-in-model"></a>Konfiguruj precyzję i skalowanie bazy danych w modelu

Precyzja i skala właściwości można teraz określić przy użyciu konstruktora modeli. Na przykład:

```CSharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

Precyzja i skala można nadal ustawiać za pośrednictwem pełnego typu bazy danych, na przykład "Decimal (16, 4)". 

Dokumentacja jest śledzona przez [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527)problemu.

### <a name="specify-sql-server-index-fill-factor"></a>Określ współczynnik wypełnienia indeksu SQL Server

Współczynnik wypełniania można teraz określić podczas tworzenia indeksu na SQL Server. Na przykład:

```CSharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a>Wersja zapoznawcza 3

### <a name="filtered-include"></a>Filtr obejmujący

Metoda include obsługuje teraz filtrowanie uwzględnionych jednostek. Na przykład:

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

To zapytanie będzie zwracać Blogi razem z poszczególnymi wpisami skojarzonymi, ale tylko wtedy, gdy tytuł wpisu zawiera "ser".

Pomiń i zrób można także użyć, aby zmniejszyć liczbę uwzględnionych jednostek. Na przykład:
 
```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
To zapytanie będzie zwracać blogi z co najwyżej pięcioma wpisami zawartymi w każdym blogu.

Szczegółowe informacje znajdują się w [dokumentacji dołączanej](xref:core/querying/related-data#filtered-include) .

### <a name="new-modelbuilder-api-for-navigation-properties"></a>Nowy interfejs API element modelbuilder dla właściwości nawigacji

Właściwości nawigacji są przede wszystkim konfigurowane podczas [definiowania relacji](xref:core/modeling/relationships). Jednak nowa `Navigation` Metoda może być używana w przypadkach, w których właściwości nawigacji wymagają dodatkowej konfiguracji. Na przykład, aby ustawić pole zapasowe dla nawigacji, gdy pole nie zostanie znalezione według Konwencji:

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

Należy pamiętać, że `Navigation` interfejs API nie zastępuje konfiguracji relacji. Zamiast tego umożliwia dodatkową konfigurację właściwości nawigacji w już odnalezionych lub zdefiniowanych relacjach.

Zapoznaj się z [dokumentacją dotyczącą konfigurowania właściwości nawigacji](xref:core/modeling/relationships#configuring-navigation-properties).

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a>Nowe parametry wiersza polecenia dla przestrzeni nazw i parametrów połączenia 

Migracje i tworzenie szkieletów umożliwiają teraz określenie przestrzeni nazw w wierszu polecenia. Na przykład w celu odtworzenia bazy danych, w której są umieszczane klasy kontekstu i modelu w różnych przestrzeniach nazw: 

```
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

Szczegółowe informacje znajdują się [w dokumentacji](xref:core/managing-schemas/scaffolding#directories-and-namespaces) dotyczącej [migracji](xref:core/managing-schemas/migrations/index#namespaces) i odtwarzania.

---
Ponadto parametry połączenia można teraz przekazywać do `database-update` polecenia:

```
dotnet ef database update --connection "connection string"
```

Szczegółowe informacje znajdują się w [dokumentacji narzędzi](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) .

Do poleceń programu PowerShell, które są używane w konsoli Menedżera pakietów programu VS, dodano również równoważne parametry.

### <a name="enabledetailederrors-has-returned"></a>EnableDetailedErrors

Ze względu na wydajność EF nie wykonuje dodatkowych kontroli wartości null podczas odczytywania wartości z bazy danych. Może to spowodować, że wyjątki są trudne do wylogowania, gdy zostanie napotkany nieoczekiwany element null.

Korzystanie z programu `EnableDetailedErrors` spowoduje dodanie dodatkowych kontroli wartości null do zapytań, takich jak w przypadku małego obciążenia wydajności, te błędy są łatwiejsze do śledzenia z przyczyn głównych.  

Na przykład:
```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors 
        .UseSqlServer(Your.SqlServerConnectionString);
```

Dokumentacja jest śledzona przez [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955)problemu.

### <a name="cosmos-partition-keys"></a>Klucze partycji Cosmos

Klucz partycji, który ma być używany dla danego zapytania, można teraz określić w zapytaniu. Na przykład:

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

Dokumentacja jest śledzona przez [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199)problemu.

### <a name="support-for-the-sql-server-datalength-function"></a>Obsługa funkcji SQL Server DATALENGTH

Dostęp do niego można uzyskać za pomocą nowej `EF.Functions.DataLength` metody. Na przykład:
```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
``` 

## <a name="preview-2"></a>Preview 2

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a>Użyj atrybutu języka C#, aby określić pole zapasowe właściwości

Atrybut języka C# może być teraz używany do określania pola zapasowego dla właściwości. Ten atrybut pozwala EF Core nadal pisać i odczytywać dane z pola zapasowego, tak jak zwykle, nawet jeśli nie można automatycznie znaleźć pola zapasowego. Na przykład:

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

Dokumentacja jest śledzona przez [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230)problemu.

### <a name="complete-discriminator-mapping"></a>Kompletne mapowanie rozróżniacza

EF Core używa kolumny rozróżniacza dla [mapowania TPH hierarchii dziedziczenia](/ef/core/modeling/inheritance). Niektóre ulepszenia wydajności są możliwe tak długo, jak EF Core wie wszystkie możliwe wartości dla rozróżniacza. EF Core 5,0 implementuje teraz te ulepszenia.

Na przykład poprzednie wersje programu EF Core zawsze generują te dane SQL dla zapytania zwracającego wszystkie typy w hierarchii:

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

EF Core 5,0 będzie teraz generować następujące elementy, gdy zostanie skonfigurowane kompletne mapowanie rozróżniacza:

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

Będzie to domyślne zachowanie rozpoczynające się od wersji zapoznawczej 3.

### <a name="performance-improvements-in-microsoftdatasqlite"></a>Ulepszenia wydajności w programie Microsoft. Data. sqlite

Wprowadziliśmy dwie ulepszenia wydajności dla oprogramowania SQLIte:

* Pobieranie danych binarnych i ciągów za pomocą metody GetBytes, GetChars i GetTextReader jest teraz wydajniejsze dzięki użyciu SqliteBlob i strumieni.
* Inicjalizacja SqliteConnection jest teraz opóźniona.

Te udoskonalenia znajdują się w dostawcy ADO.NET Microsoft. Data. SQLite, a tym samym zwiększyć wydajność poza EF Core.

## <a name="preview-1"></a>Wersja zapoznawcza 1

### <a name="simple-logging"></a>Rejestrowanie proste

Ta funkcja dodaje funkcje podobne do `Database.Log` programu w programie Ef6. Oznacza to, że zapewnia prosty sposób pobierania dzienników z EF Core bez konieczności konfigurowania żadnego rodzaju zewnętrznej platformy rejestrowania.

Wstępna dokumentacja jest zawarta w [statusie tygodnia Ef 5 grudnia 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).

Dodatkowa dokumentacja jest śledzona przez [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085)problemu.

### <a name="simple-way-to-get-generated-sql"></a>Prosty sposób uzyskiwania wygenerowanego kodu SQL

EF Core 5,0 wprowadza `ToQueryString` metodę rozszerzenia, która zwróci kod SQL, który EF Core zostanie wygenerowany podczas wykonywania zapytania LINQ.

Wstępna dokumentacja jest uwzględniona w [statusie tygodniowym EF dla 9 stycznia 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).

Dodatkowa dokumentacja jest śledzona przez [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331)problemu.

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a>Użyj atrybutu języka C#, aby wskazać, że jednostka nie ma klucza

Typ jednostki można teraz skonfigurować jako bez klucza przy użyciu nowego elementu `KeylessAttribute` . Na przykład:

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

Dokumentacja jest śledzona przez [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186)problemu.

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a>Parametry Connection lub Connection można zmienić w zainicjowanym kontekście DbContext

Teraz łatwiej jest utworzyć wystąpienie DbContext bez żadnego połączenia lub parametrów połączenia. Ponadto parametry połączenia lub połączenia można teraz przystąpić do wystąpienia kontekstu. Ta funkcja pozwala temu samemu wystąpieniu kontekstu na dynamiczne łączenie się z różnymi bazami danych.

Dokumentacja jest śledzona przez [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075)problemu.

### <a name="change-tracking-proxies"></a>Serwery proxy śledzenia zmian

EF Core mogą teraz generować serwery proxy środowiska uruchomieniowego, które automatycznie implementują [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) i [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1). Następnie te zmiany są raportowane w oparciu o właściwości jednostki bezpośrednio do EF Core, unikając konieczności skanowania pod kątem zmian. Jednak serwery proxy są dostarczane z własnym zestawem ograniczeń, więc nie są przeznaczone dla wszystkich użytkowników.

Dokumentacja jest śledzona przez [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076)problemu.

### <a name="enhanced-debug-views"></a>Udoskonalone widoki debugowania

Widoki debugowania to prosty sposób na zajrzeć do wewnętrznych EF Core w przypadku problemów z debugowaniem. Widok debugowania dla modelu został zaimplementowany jakiś czas temu. W przypadku EF Core 5,0 ten widok modelu jest łatwiejszy do odczytania i dodania nowego widoku debugowania dla śledzonych jednostek w Menedżerze stanu.

Wstępna dokumentacja jest uwzględniona w [Stanach tygodnia EF 12 grudnia 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).

Dodatkowa dokumentacja jest śledzona przez [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086)problemu.

### <a name="improved-handling-of-database-null-semantics"></a>Ulepszona obsługa semantyki o wartości null bazy danych

Relacyjne bazy danych zazwyczaj traktują wartości NULL jako nieznaną wartość i w związku z tym nie są równe żadnym innym NULL. Chociaż język C# traktuje wartość null w postaci wartości zdefiniowanej, co porównuje ją z innymi wartościami null. EF Core domyślnie tłumaczy zapytania tak, aby korzystały z semantyki wartości null języka C#. EF Core 5,0 znacznie poprawia wydajność tych tłumaczeń.

Dokumentacja jest śledzona przez [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612)problemu.

### <a name="indexer-properties"></a>Właściwości indeksatora

EF Core 5,0 obsługuje mapowanie właściwości indeksatora języka C#. Te właściwości umożliwiają jednostkom działanie jako zbiory właściwości, w których kolumny są mapowane na nazwane właściwości w zbiorze.

Dokumentacja jest śledzona przez [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018)problemu.

### <a name="generation-of-check-constraints-for-enum-mappings"></a>Generowanie ograniczeń check dla mapowań wyliczenia

Migracje EF Core 5,0 mogą teraz generować ograniczenia CHECK dla mapowań właściwości enum. Na przykład:

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

Dokumentacja jest śledzona przez [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082)problemu.

### <a name="isrelational"></a>Isrelacyjne

Dodano nową `IsRelational` metodę oprócz istniejących `IsSqlServer` , `IsSqlite` i `IsInMemory` . Tej metody można użyć do sprawdzenia, czy DbContext używa dowolnego dostawcy relacyjnej bazy danych. Na przykład:

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

Dokumentacja jest śledzona przez [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185)problemu.

### <a name="cosmos-optimistic-concurrency-with-etags"></a>Cosmos optymistyczne współbieżność za pomocą elementów ETag

Dostawca bazy danych Azure Cosmos DB obsługuje teraz optymistyczne współbieżność za pomocą elementów ETag. Użyj konstruktora modeli w OnModelCreating, aby skonfigurować element ETag:

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

Metody SaveChanges następnie zgłosi `DbUpdateConcurrencyException` konflikt współbieżności, który [można obsłużyć](https://docs.microsoft.com/ef/core/saving/concurrency) w celu zaimplementowania ponownych prób itd.

Dokumentacja jest śledzona przez [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099)problemu.

### <a name="query-translations-for-more-datetime-constructs"></a>Tłumaczenie zapytań dla większej liczby konstrukcji DateTime

Zapytania zawierające nową konstrukcję DateTime są teraz tłumaczone.

Ponadto następujące funkcje SQL Server są teraz mapowane:

* DateDiffWeek
* DateFromParts

Na przykład:

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

Dokumentacja jest śledzona przez [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)problemu.

### <a name="query-translations-for-more-byte-array-constructs"></a>Tłumaczenie zapytania dla większej liczby konstrukcji tablicy

Zapytania używające właściwości Contains, Length, SequenceEqual itp. on-Byte [] są teraz tłumaczone na SQL.

Wstępna dokumentacja jest zawarta w [statusie tygodnia Ef 5 grudnia 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).

Dodatkowa dokumentacja jest śledzona przez [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)problemu.

### <a name="query-translation-for-reverse"></a>Tłumaczenie zapytania do tyłu

Zapytania z użyciem `Reverse` są teraz tłumaczone. Na przykład:

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

Dokumentacja jest śledzona przez [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)problemu.

### <a name="query-translation-for-bitwise-operators"></a>Tłumaczenie zapytania dla operatorów bitowych

Zapytania wykorzystujące operatory bitowe są teraz tłumaczone na przykład w większej liczbie przypadków:

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

Dokumentacja jest śledzona przez [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)problemu.

### <a name="query-translation-for-strings-on-cosmos"></a>Tłumaczenie zapytania dla ciągów w Cosmos

Zapytania korzystające z metod String zawierają, StartsWith i EndsWith są teraz tłumaczone przy użyciu dostawcy Azure Cosmos DB.

Dokumentacja jest śledzona przez [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)problemu.
