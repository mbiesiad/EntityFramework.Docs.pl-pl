---
title: Co nowego w EF Core 5,0
description: Omówienie nowych funkcji w EF Core 5,0
author: ajcvickers
ms.date: 06/02/2020
uid: core/what-is-new/ef-core-5.0/whatsnew
ms.openlocfilehash: 304ed74fe344b43177525113c70b7be7bb0ac5ed
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238336"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="58f8e-103">Co nowego w EF Core 5,0</span><span class="sxs-lookup"><span data-stu-id="58f8e-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="58f8e-104">EF Core 5,0 jest obecnie w trakcie opracowywania.</span><span class="sxs-lookup"><span data-stu-id="58f8e-104">EF Core 5.0 is currently in development.</span></span> <span data-ttu-id="58f8e-105">Ta strona będzie zawierać przegląd interesujących zmian wprowadzonych w każdej wersji zapoznawczej.</span><span class="sxs-lookup"><span data-stu-id="58f8e-105">This page will contain an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="58f8e-106">Ta strona nie duplikuje [planu dla EF Core 5,0](xref:core/what-is-new/ef-core-5.0/plan).</span><span class="sxs-lookup"><span data-stu-id="58f8e-106">This page does not duplicate the [plan for EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan).</span></span> <span data-ttu-id="58f8e-107">Plan opisuje ogólne motywy dla EF Core 5,0, w tym wszystko, co planujemy uwzględnić przed wysyłką ostatecznej wersji.</span><span class="sxs-lookup"><span data-stu-id="58f8e-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

<span data-ttu-id="58f8e-108">Będziemy dodawać linki z tego miejsca do oficjalnej dokumentacji w trakcie jej publikacji.</span><span class="sxs-lookup"><span data-stu-id="58f8e-108">We will add links from here to the official documentation as it is published.</span></span>

## <a name="preview-6"></a><span data-ttu-id="58f8e-109">Wersja zapoznawcza 6</span><span class="sxs-lookup"><span data-stu-id="58f8e-109">Preview 6</span></span>

### <a name="split-queries-for-related-collections"></a><span data-ttu-id="58f8e-110">Podziel zapytania dla powiązanych kolekcji</span><span class="sxs-lookup"><span data-stu-id="58f8e-110">Split queries for related collections</span></span>

<span data-ttu-id="58f8e-111">Począwszy od EF Core 3,0, EF Core zawsze generuje pojedyncze zapytanie SQL dla każdej kwerendy LINQ.</span><span class="sxs-lookup"><span data-stu-id="58f8e-111">Starting with EF Core 3.0, EF Core always generates a single SQL query for each LINQ query.</span></span> <span data-ttu-id="58f8e-112">Zapewnia to spójność danych zwracanych w ramach ograniczeń trybu transakcji w użyciu.</span><span class="sxs-lookup"><span data-stu-id="58f8e-112">This ensures consistency of the data returned within the constraints of the transaction mode in use.</span></span> <span data-ttu-id="58f8e-113">Jednak może to potrwać bardzo wolno, gdy zapytanie używa `Include` lub projekcji, aby przywrócić wiele powiązanych kolekcji.</span><span class="sxs-lookup"><span data-stu-id="58f8e-113">However, this can become very slow when the query uses `Include` or a projection to bring back multiple related collections.</span></span>

<span data-ttu-id="58f8e-114">EF Core 5,0 umożliwia teraz pojedyncze zapytanie LINQ, w tym powiązane kolekcje, które mają być podzielone na wiele zapytań SQL.</span><span class="sxs-lookup"><span data-stu-id="58f8e-114">EF Core 5.0 now allows a single LINQ query including related collections to be split into multiple SQL queries.</span></span> <span data-ttu-id="58f8e-115">Może to znacząco poprawić wydajność, ale może spowodować niespójność wyników zwróconych w przypadku zmiany danych między dwoma zapytaniami.</span><span class="sxs-lookup"><span data-stu-id="58f8e-115">This can significantly improve performance, but can result in inconsistency in the results returned if the data changes between the two queries.</span></span> <span data-ttu-id="58f8e-116">Można użyć transakcji do serializacji lub migawek, aby ograniczyć to i zapewnić spójność z zapytaniami z podziałem, ale może to spowodować inne koszty wydajności i różnice w zachowaniu.</span><span class="sxs-lookup"><span data-stu-id="58f8e-116">Serializable or snapshot transactions can be used to mitigate this and achieve consistency with split queries, but that may bring other performance costs and behavioral difference.</span></span>

#### <a name="split-queries-with-include"></a><span data-ttu-id="58f8e-117">Rozdziel zapytania za pomocą dyrektywy include</span><span class="sxs-lookup"><span data-stu-id="58f8e-117">Split queries with Include</span></span>

<span data-ttu-id="58f8e-118">Rozważmy na przykład zapytanie, które pobiera dwa poziomy pokrewnych kolekcji przy użyciu `Include` :</span><span class="sxs-lookup"><span data-stu-id="58f8e-118">For example, consider a query that pulls in two levels of related collections using `Include`:</span></span>

```CSharp
var artists = context.Artists
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="58f8e-119">Domyślnie EF Core będzie generować następujące SQL podczas korzystania z dostawcy oprogramowania SQLite:</span><span class="sxs-lookup"><span data-stu-id="58f8e-119">By default, EF Core will generate the following SQL when using the SQLite provider:</span></span>

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

<span data-ttu-id="58f8e-120">`AsSplitQuery`Aby zmienić to zachowanie, można użyć nowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="58f8e-120">The new `AsSplitQuery` API can be used to change this behavior.</span></span> <span data-ttu-id="58f8e-121">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="58f8e-121">For example:</span></span>

```CSharp
var artists = context.Artists
    .AsSplitQuery()
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="58f8e-122">AsSplitQuery jest dostępny dla wszystkich dostawców relacyjnych baz danych i można go używać w dowolnym miejscu zapytania, podobnie jak AsNoTracking.</span><span class="sxs-lookup"><span data-stu-id="58f8e-122">AsSplitQuery is available for all relational database providers and can be used anywhere in the query, just like AsNoTracking.</span></span> <span data-ttu-id="58f8e-123">EF Core będzie teraz generować następujące trzy zapytania SQL:</span><span class="sxs-lookup"><span data-stu-id="58f8e-123">EF Core will now generate the following three SQL queries:</span></span>

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

<span data-ttu-id="58f8e-124">Wszystkie operacje w katalogu głównym zapytania są obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="58f8e-124">All operations on the query root are supported.</span></span> <span data-ttu-id="58f8e-125">Obejmuje to operacje OrderBy/Skip/Take, join, FirstOrDefault i podobne pojedyncze wyniki wybierające operacje.</span><span class="sxs-lookup"><span data-stu-id="58f8e-125">This includes OrderBy/Skip/Take, Join operations, FirstOrDefault and similar single result selecting operations.</span></span>

<span data-ttu-id="58f8e-126">Zwróć uwagę, że filtrowane elementy include z OrderBy/Skip/Take nie są obsługiwane w wersji zapoznawczej 6, ale są dostępne w codziennych kompilacjach i zostaną uwzględnione w wersji zapoznawczej 7.</span><span class="sxs-lookup"><span data-stu-id="58f8e-126">Note that filtered Includes with OrderBy/Skip/Take are not supported in preview 6, but are available in the daily builds and will be included in preview 7.</span></span>

#### <a name="split-queries-with-collection-projections"></a><span data-ttu-id="58f8e-127">Podziel zapytania z projekcjami kolekcji</span><span class="sxs-lookup"><span data-stu-id="58f8e-127">Split queries with collection projections</span></span>

<span data-ttu-id="58f8e-128">`AsSplitQuery`można go również użyć, gdy kolekcje są ładowane do projekcji.</span><span class="sxs-lookup"><span data-stu-id="58f8e-128">`AsSplitQuery` can also be used when collections are loaded in projections.</span></span> <span data-ttu-id="58f8e-129">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="58f8e-129">For example:</span></span>

```CSharp
context.Artists
    .AsSplitQuery()
    .Select(e => new
    {
        Artist = e,
        Albums = e.Albums,
    }).ToList();
```

<span data-ttu-id="58f8e-130">Ta kwerenda LINQ generuje następujące dwa zapytania SQL, gdy jest używany dostawca oprogramowania SQLite:</span><span class="sxs-lookup"><span data-stu-id="58f8e-130">This LINQ query generates the following two SQL queries when using the SQLite provider:</span></span>

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id"
```

<span data-ttu-id="58f8e-131">Należy zauważyć, że obsługiwana jest tylko materializację kolekcji.</span><span class="sxs-lookup"><span data-stu-id="58f8e-131">Note that only materialization of the collection is supported.</span></span> <span data-ttu-id="58f8e-132">Wszelkie kompozycje po `e.Albums` powyższym przypadku nie spowodują przetworzenia zapytania podzielonego.</span><span class="sxs-lookup"><span data-stu-id="58f8e-132">Any composition after `e.Albums` in above case won't result in a split query.</span></span> <span data-ttu-id="58f8e-133">Ulepszenia w tym obszarze są śledzone przez [#21234](https://github.com/dotnet/efcore/issues/21234).</span><span class="sxs-lookup"><span data-stu-id="58f8e-133">Improvements in this area are tracked by [#21234](https://github.com/dotnet/efcore/issues/21234).</span></span>

### <a name="indexattribute"></a><span data-ttu-id="58f8e-134">Indexattribute</span><span class="sxs-lookup"><span data-stu-id="58f8e-134">IndexAttribute</span></span>

<span data-ttu-id="58f8e-135">Nowy element Indexattribute może być umieszczony w typie jednostki, aby określić indeks dla pojedynczej kolumny.</span><span class="sxs-lookup"><span data-stu-id="58f8e-135">The new IndexAttribute can be placed on an entity type to specify an index for a single column.</span></span> <span data-ttu-id="58f8e-136">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="58f8e-136">For example:</span></span>

```CSharp
[Index(nameof(FullName), IsUnique = true)]
public class User
{
    public int Id { get; set; }
    
    [MaxLength(128)]
    public string FullName { get; set; }
}
```

<span data-ttu-id="58f8e-137">W przypadku SQL Server migracja będzie generować następujące SQL:</span><span class="sxs-lookup"><span data-stu-id="58f8e-137">For SQL Server, Migrations will then generate the following SQL:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FullName]
    ON [Users] ([FullName])
    WHERE [FullName] IS NOT NULL;
```

<span data-ttu-id="58f8e-138">W celu określenia indeksu obejmującego wiele kolumn można także użyć elementu indexattribute.</span><span class="sxs-lookup"><span data-stu-id="58f8e-138">IndexAttribute can also be used to specify an index spanning multiple columns.</span></span> <span data-ttu-id="58f8e-139">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="58f8e-139">For example:</span></span>

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

<span data-ttu-id="58f8e-140">W przypadku SQL Server powoduje to:</span><span class="sxs-lookup"><span data-stu-id="58f8e-140">For SQL Server, this results in:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FirstName_LastName]
    ON [Users] ([FirstName], [LastName])
    WHERE [FirstName] IS NOT NULL AND [LastName] IS NOT NULL;
```

<span data-ttu-id="58f8e-141">Dokumentacja jest śledzona przez [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407)problemu.</span><span class="sxs-lookup"><span data-stu-id="58f8e-141">Documentation is tracked by issue [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span></span>

### <a name="improved-query-translation-exceptions"></a><span data-ttu-id="58f8e-142">Ulepszone wyjątki translacji zapytań</span><span class="sxs-lookup"><span data-stu-id="58f8e-142">Improved query translation exceptions</span></span>

<span data-ttu-id="58f8e-143">Kontynuujemy ulepszanie komunikatów o wyjątkach generowanych w przypadku niepowodzenia tłumaczenia zapytań.</span><span class="sxs-lookup"><span data-stu-id="58f8e-143">We are continuing to improve the exception messages generated when query translation fails.</span></span> <span data-ttu-id="58f8e-144">Na przykład to zapytanie używa niezamapowanej właściwości `IsSigned` :</span><span class="sxs-lookup"><span data-stu-id="58f8e-144">For example, this query uses the unmapped property `IsSigned`:</span></span>

```CSharp
var artists = context.Artists.Where(e => e.IsSigned).ToList();
```

<span data-ttu-id="58f8e-145">EF Core zgłosi następujący wyjątek wskazujący, że tłumaczenie nie powiodło się, ponieważ `IsSigned` nie jest zamapowany:</span><span class="sxs-lookup"><span data-stu-id="58f8e-145">EF Core will throw the following exception indicating that translation failed because `IsSigned` is not mapped:</span></span>

> <span data-ttu-id="58f8e-146">Nieobsługiwany wyjątek.</span><span class="sxs-lookup"><span data-stu-id="58f8e-146">Unhandled exception.</span></span> <span data-ttu-id="58f8e-147">System. InvalidOperationException: wyrażenie LINQ "Nieogólnymi <Artist> (). Gdzie nie można przetłumaczyć (a => a. IsSigned) '.</span><span class="sxs-lookup"><span data-stu-id="58f8e-147">System.InvalidOperationException: The LINQ expression 'DbSet<Artist>() .Where(a => a.IsSigned)' could not be translated.</span></span> <span data-ttu-id="58f8e-148">Informacje dodatkowe: Tłumaczenie elementu członkowskiego "IsSigned" w typie jednostki "artyści" nie powiodło się.</span><span class="sxs-lookup"><span data-stu-id="58f8e-148">Additional information: Translation of member 'IsSigned' on entity type 'Artist' failed.</span></span> <span data-ttu-id="58f8e-149">Prawdopodobnie określony element członkowski nie jest zamapowany.</span><span class="sxs-lookup"><span data-stu-id="58f8e-149">Possibly the specified member is not mapped.</span></span> <span data-ttu-id="58f8e-150">Ponownie Napisz zapytanie w formularzu, który można przetłumaczyć, lub Przełącz się na ocenę klienta jawnie, wstawiając wywołanie do AsEnumerable (), AsAsyncEnumerable (), ToList — () lub ToListAsync ().</span><span class="sxs-lookup"><span data-stu-id="58f8e-150">Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().</span></span> <span data-ttu-id="58f8e-151">Aby uzyskać więcej informacji, zobacz https://go.microsoft.com/fwlink/?linkid=2101038.</span><span class="sxs-lookup"><span data-stu-id="58f8e-151">See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.</span></span>

<span data-ttu-id="58f8e-152">Podobnie w przypadku próby przetłumaczenia porównania ciągów z semantyką zależną od kultury są teraz generowane lepsze komunikaty o wyjątkach.</span><span class="sxs-lookup"><span data-stu-id="58f8e-152">Similarly, better exception messages are now generated when attempting to translate string comparisons with culture-dependent semantics.</span></span> <span data-ttu-id="58f8e-153">Na przykład ta kwerenda próbuje użyć `StringComparison.CurrentCulture` :</span><span class="sxs-lookup"><span data-stu-id="58f8e-153">For example, this query attempts to use `StringComparison.CurrentCulture`:</span></span>

```CSharp
var artists = context.Artists
    .Where(e => e.Name.Equals("The Unicorns", StringComparison.CurrentCulture))
    .ToList();
```

<span data-ttu-id="58f8e-154">EF Core będzie teraz zgłosić następujący wyjątek:</span><span class="sxs-lookup"><span data-stu-id="58f8e-154">EF Core will now throw the following exception:</span></span>

> <span data-ttu-id="58f8e-155">Nieobsługiwany wyjątek.</span><span class="sxs-lookup"><span data-stu-id="58f8e-155">Unhandled exception.</span></span> <span data-ttu-id="58f8e-156">System. InvalidOperationException: wyrażenie LINQ "Nieogólnymi <Artist> (). Gdzie (a => a. Name. Equals (wartość: "różowo", comparisonType: CurrentCulture)) "nie można przetłumaczyć.</span><span class="sxs-lookup"><span data-stu-id="58f8e-156">System.InvalidOperationException: The LINQ expression 'DbSet<Artist>() .Where(a => a.Name.Equals( value: "The Unicorns", comparisonType: CurrentCulture))' could not be translated.</span></span> <span data-ttu-id="58f8e-157">Informacje dodatkowe: tłumaczenie ciągu. Metoda Equals, która pobiera argument "StringComparison", nie jest obsługiwana.</span><span class="sxs-lookup"><span data-stu-id="58f8e-157">Additional information: Translation of 'string.Equals' method which takes 'StringComparison' argument is not supported.</span></span> <span data-ttu-id="58f8e-158">Aby uzyskać więcej informacji, zobacz https://go.microsoft.com/fwlink/?linkid=2129535.</span><span class="sxs-lookup"><span data-stu-id="58f8e-158">See https://go.microsoft.com/fwlink/?linkid=2129535 for more information.</span></span> <span data-ttu-id="58f8e-159">Ponownie Napisz zapytanie w formularzu, który można przetłumaczyć, lub Przełącz się na ocenę klienta jawnie, wstawiając wywołanie do AsEnumerable (), AsAsyncEnumerable (), ToList — () lub ToListAsync ().</span><span class="sxs-lookup"><span data-stu-id="58f8e-159">Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().</span></span> <span data-ttu-id="58f8e-160">Aby uzyskać więcej informacji, zobacz https://go.microsoft.com/fwlink/?linkid=2101038.</span><span class="sxs-lookup"><span data-stu-id="58f8e-160">See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.</span></span>

### <a name="specify-transaction-id"></a><span data-ttu-id="58f8e-161">Określ identyfikator transakcji</span><span class="sxs-lookup"><span data-stu-id="58f8e-161">Specify transaction ID</span></span>

<span data-ttu-id="58f8e-162">Ta funkcja została pobrana ze społeczności przez program [@Marusyk](https://github.com/Marusyk) .</span><span class="sxs-lookup"><span data-stu-id="58f8e-162">This feature was contributed from the community by [@Marusyk](https://github.com/Marusyk).</span></span> <span data-ttu-id="58f8e-163">Wiele Dziękujemy za udział!</span><span class="sxs-lookup"><span data-stu-id="58f8e-163">Many thanks for the contribution!</span></span>

<span data-ttu-id="58f8e-164">EF Core uwidacznia identyfikator transakcji dla korelacji transakcji w wywołaniach.</span><span class="sxs-lookup"><span data-stu-id="58f8e-164">EF Core exposes a transaction ID for correlation of transactions across calls.</span></span> <span data-ttu-id="58f8e-165">Ten identyfikator jest zazwyczaj ustawiany przez EF Core po rozpoczęciu transakcji.</span><span class="sxs-lookup"><span data-stu-id="58f8e-165">This ID typically set by EF Core when a transaction is started.</span></span> <span data-ttu-id="58f8e-166">Jeśli zamiast tego aplikacja uruchamia transakcję, ta funkcja umożliwia aplikacji jawne ustawienie identyfikatora transakcji, dzięki czemu jest on skorelowany prawidłowo wszędzie tam, gdzie jest używana.</span><span class="sxs-lookup"><span data-stu-id="58f8e-166">If the application starts the transaction instead, then this feature allows the application to explicitly set the transaction ID so it is correlated correctly everywhere it is used.</span></span> <span data-ttu-id="58f8e-167">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="58f8e-167">For example:</span></span>

```CSharp
using (context.Database.UseTransaction(myTransaction, myId))
{
   ...
}
```

### <a name="ipaddress-mapping"></a><span data-ttu-id="58f8e-168">Mapowanie adresu IP</span><span class="sxs-lookup"><span data-stu-id="58f8e-168">IPAddress mapping</span></span>

<span data-ttu-id="58f8e-169">Ta funkcja została pobrana ze społeczności przez program [@ralmsdeveloper](https://github.com/ralmsdeveloper) .</span><span class="sxs-lookup"><span data-stu-id="58f8e-169">This feature was contributed from the community by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span> <span data-ttu-id="58f8e-170">Wiele Dziękujemy za udział!</span><span class="sxs-lookup"><span data-stu-id="58f8e-170">Many thanks for the contribution!</span></span>

<span data-ttu-id="58f8e-171">Standardowa [Klasa adresu IP](/dotnet/api/system.net.ipaddress) platformy .NET jest teraz automatycznie mapowana na kolumnę ciągów dla baz danych, które nie mają jeszcze natywnej obsługi.</span><span class="sxs-lookup"><span data-stu-id="58f8e-171">The standard .NET [IPAddress class](/dotnet/api/system.net.ipaddress) is now automatically mapped to a string column for databases that do not already have native support.</span></span> <span data-ttu-id="58f8e-172">Rozważmy na przykład mapowanie tego typu jednostki:</span><span class="sxs-lookup"><span data-stu-id="58f8e-172">For example, consider mapping this entity type:</span></span>

```CSharp
public class Host
{
    public int Id { get; set; }
    public IPAddress Address { get; set; }
}
```

<span data-ttu-id="58f8e-173">W przypadku SQL Server spowoduje to migrację, tworząc następującą tabelę:</span><span class="sxs-lookup"><span data-stu-id="58f8e-173">On SQL Server, this will result in Migrations creating the following table:</span></span>

```sql
CREATE TABLE [Host] (
    [Id] int NOT NULL,
    [Address] nvarchar(45) NULL,
    CONSTRAINT [PK_Host] PRIMARY KEY ([Id]));
``` 

<span data-ttu-id="58f8e-174">Jednostki można następnie dodać w zwykły sposób:</span><span class="sxs-lookup"><span data-stu-id="58f8e-174">Entities can then be added in the normal way:</span></span>

```CSharp
context.AddRange(
    new Host { Address = IPAddress.Parse("127.0.0.1")},
    new Host { Address = IPAddress.Parse("0000:0000:0000:0000:0000:0000:0000:0001")});
``` 

<span data-ttu-id="58f8e-175">A w efekcie w programie SQL Server zostanie wstawiony znormalizowany adres IPv4 lub IPv6:</span><span class="sxs-lookup"><span data-stu-id="58f8e-175">And the resulting SQL will insert the normalized IPv4 or IPv6 address:</span></span>

```sql
Executed DbCommand (14ms) [Parameters=[@p0='1', @p1='127.0.0.1' (Size = 45), @p2='2', @p3='::1' (Size = 45)], CommandType='Text', CommandTimeout='30']
      SET NOCOUNT ON;
      INSERT INTO [Host] ([Id], [Address])
      VALUES (@p0, @p1), (@p2, @p3);
```

### <a name="exclude-onconfiguring-when-scaffolding"></a><span data-ttu-id="58f8e-176">Wyklucz onkonfigurowany podczas tworzenia szkieletów</span><span class="sxs-lookup"><span data-stu-id="58f8e-176">Exclude OnConfiguring when scaffolding</span></span>

<span data-ttu-id="58f8e-177">Gdy DbContext jest szkieletem z istniejącej bazy danych, EF Core domyślnie tworzy Przeciążenie przy użyciu parametrów połączenia, dzięki czemu kontekst jest natychmiast użyteczny.</span><span class="sxs-lookup"><span data-stu-id="58f8e-177">When a DbContext is scaffolded from an existing database, EF Core by default creates an OnConfiguring overload with a connection string so that the context is immediately usable.</span></span> <span data-ttu-id="58f8e-178">Nie jest to jednak przydatne, jeśli masz już klasę częściową z funkcją onconfiguring lub w przypadku konfigurowania kontekstu w inny sposób.</span><span class="sxs-lookup"><span data-stu-id="58f8e-178">However, this is not useful if you already have a partial class with OnConfiguring, or if you are configuring the context some other way.</span></span>

<span data-ttu-id="58f8e-179">Aby rozwiązać ten potrzeba, można teraz wydać polecenia tworzenia szkieletu, aby pominąć generowanie elementu onconfiguring.</span><span class="sxs-lookup"><span data-stu-id="58f8e-179">To address this, the scaffolding commands can now be instructed to omit generation of OnConfiguring.</span></span> <span data-ttu-id="58f8e-180">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="58f8e-180">For example:</span></span>

```
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer --no-onconfiguring
```

<span data-ttu-id="58f8e-181">Lub w konsoli Menedżera pakietów:</span><span class="sxs-lookup"><span data-stu-id="58f8e-181">Or in the Package Manager Console:</span></span>

```
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer -NoOnConfiguring 
``` 

<span data-ttu-id="58f8e-182">Należy pamiętać, że zalecamy użycie [nazwanego ciągu połączenia i bezpiecznego magazynu, takiego jak wpisy tajne użytkownika](/core/managing-schemas/scaffolding?tabs=vs#configuration-and-user-secrets).</span><span class="sxs-lookup"><span data-stu-id="58f8e-182">Note that we recommend using [a named connection string and secure storage like User Secrets](/core/managing-schemas/scaffolding?tabs=vs#configuration-and-user-secrets).</span></span>

### <a name="translations-for-firstordefault-on-strings"></a><span data-ttu-id="58f8e-183">Tłumaczenia dla FirstOrDefault na ciągach</span><span class="sxs-lookup"><span data-stu-id="58f8e-183">Translations for FirstOrDefault on strings</span></span>

<span data-ttu-id="58f8e-184">Ta funkcja została pobrana ze społeczności przez program [@dvoreckyaa](https://github.com/dvoreckyaa) .</span><span class="sxs-lookup"><span data-stu-id="58f8e-184">This feature was contributed from the community by [@dvoreckyaa](https://github.com/dvoreckyaa).</span></span> <span data-ttu-id="58f8e-185">Wiele Dziękujemy za udział!</span><span class="sxs-lookup"><span data-stu-id="58f8e-185">Many thanks for the contribution!</span></span>

<span data-ttu-id="58f8e-186">FirstOrDefault i podobne operatory dla znaków w ciągach są teraz tłumaczone.</span><span class="sxs-lookup"><span data-stu-id="58f8e-186">FirstOrDefault and similar operators for characters in strings are now translated.</span></span> <span data-ttu-id="58f8e-187">Na przykład ta kwerenda LINQ:</span><span class="sxs-lookup"><span data-stu-id="58f8e-187">For example, this LINQ query:</span></span>

```CSharp
context.Customers.Where(c => c.ContactName.FirstOrDefault() == 'A').ToList();
```

<span data-ttu-id="58f8e-188">Zostaną przetłumaczone na następujące SQL podczas korzystania z SQL Server:</span><span class="sxs-lookup"><span data-stu-id="58f8e-188">Will be translated to the following SQL when using SQL Server:</span></span>

```sql
SELECT [c].[Id], [c].[ContactName]
FROM [Customer] AS [c]
WHERE SUBSTRING([c].[ContactName], 1, 1) = N'A'
```

### <a name="simplify-case-blocks"></a><span data-ttu-id="58f8e-189">Uprość bloki wielkości liter</span><span class="sxs-lookup"><span data-stu-id="58f8e-189">Simplify case blocks</span></span>

<span data-ttu-id="58f8e-190">EF Core teraz generuje lepsze zapytania z blokami przypadków.</span><span class="sxs-lookup"><span data-stu-id="58f8e-190">EF Core now generates better queries with CASE blocks.</span></span> <span data-ttu-id="58f8e-191">Na przykład ta kwerenda LINQ:</span><span class="sxs-lookup"><span data-stu-id="58f8e-191">For example, this LINQ query:</span></span> 

```CSharp
context.Weapons
    .OrderBy(w => w.Name.CompareTo("Marcus' Lancer") == 0)
    .ThenBy(w => w.Id)
```

<span data-ttu-id="58f8e-192">Był włączony SQL Server przetłumaczony na:</span><span class="sxs-lookup"><span data-stu-id="58f8e-192">Was on SQL Server formally translated to:</span></span>

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

<span data-ttu-id="58f8e-193">Jest teraz tłumaczone na:</span><span class="sxs-lookup"><span data-stu-id="58f8e-193">But is now translated to:</span></span>

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN ([w].[Name] = N'Marcus'' Lancer') AND [w].[Name] IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
``` 

## <a name="preview-5"></a><span data-ttu-id="58f8e-194">Wersja zapoznawcza 5</span><span class="sxs-lookup"><span data-stu-id="58f8e-194">Preview 5</span></span>

### <a name="database-collations"></a><span data-ttu-id="58f8e-195">Sortowanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="58f8e-195">Database collations</span></span>

<span data-ttu-id="58f8e-196">Domyślne sortowanie dla bazy danych można teraz określić w modelu EF.</span><span class="sxs-lookup"><span data-stu-id="58f8e-196">The default collation for a database can now be specified in the EF model.</span></span> <span data-ttu-id="58f8e-197">Spowoduje to przetworzenie przetworzonych migracji w celu ustawienia sortowania podczas tworzenia bazy danych.</span><span class="sxs-lookup"><span data-stu-id="58f8e-197">This will flow through to generated migrations to set the collation when the database is created.</span></span> <span data-ttu-id="58f8e-198">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="58f8e-198">For example:</span></span>

```CSharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="58f8e-199">Następnie migracja generuje następujące elementy, aby utworzyć bazę danych na SQL Server:</span><span class="sxs-lookup"><span data-stu-id="58f8e-199">Migrations then generates the following to create the database on SQL Server:</span></span>

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

<span data-ttu-id="58f8e-200">Można również określić sortowanie, które ma być używane dla określonych kolumn bazy danych.</span><span class="sxs-lookup"><span data-stu-id="58f8e-200">The collation to use for specific database columns can also be specified.</span></span> <span data-ttu-id="58f8e-201">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="58f8e-201">For example:</span></span>

```CSharp
 modelBuilder
     .Entity<User>()
     .Property(e => e.Name)
     .UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="58f8e-202">Dla tych, które nie korzystają z migracji, sortowania są teraz odtwarzane z bazy danych podczas tworzenia szkieletu DbContext.</span><span class="sxs-lookup"><span data-stu-id="58f8e-202">For those not using migrations, collations are now reverse-engineered from the database when scaffolding a DbContext.</span></span>

<span data-ttu-id="58f8e-203">Na koniec `EF.Functions.Collate()` zezwala na kwerendy ad hoc przy użyciu różnych ustawień sortowania.</span><span class="sxs-lookup"><span data-stu-id="58f8e-203">Finally, the `EF.Functions.Collate()` allows for ad-hoc queries using different collations.</span></span> <span data-ttu-id="58f8e-204">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="58f8e-204">For example:</span></span>

```CSharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

<span data-ttu-id="58f8e-205">Spowoduje to wygenerowanie następującej kwerendy dla SQL Server:</span><span class="sxs-lookup"><span data-stu-id="58f8e-205">This will generate the following query for SQL Server:</span></span>

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

<span data-ttu-id="58f8e-206">Należy pamiętać, że sortowania ad-hoc należy używać z ostrożnością, ponieważ mogą one mieć negatywny wpływ na wydajność bazy danych.</span><span class="sxs-lookup"><span data-stu-id="58f8e-206">Note that ad-hoc collations should be used with care as they can negatively impact database performance.</span></span>

<span data-ttu-id="58f8e-207">Dokumentacja jest śledzona przez [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273)problemu.</span><span class="sxs-lookup"><span data-stu-id="58f8e-207">Documentation is tracked by issue [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span></span>

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a><span data-ttu-id="58f8e-208">Argumenty przepływu w IDesignTimeDbContextFactory</span><span class="sxs-lookup"><span data-stu-id="58f8e-208">Flow arguments into IDesignTimeDbContextFactory</span></span>

<span data-ttu-id="58f8e-209">Argumenty są teraz przepływane z wiersza polecenia do `CreateDbContext` metody [IDesignTimeDbContextFactory](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1).</span><span class="sxs-lookup"><span data-stu-id="58f8e-209">Arguments are now flowed from the command line into the `CreateDbContext` method of [IDesignTimeDbContextFactory](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1).</span></span> <span data-ttu-id="58f8e-210">Na przykład, aby wskazać, że jest to kompilacja dev, argument niestandardowy (np. `dev` ) można przesłać w wierszu polecenia:</span><span class="sxs-lookup"><span data-stu-id="58f8e-210">For example, to indicate this is a dev build, a custom argument (e.g. `dev`) can be passed on the command line:</span></span>

```
dotnet ef migrations add two --verbose --dev
``` 

<span data-ttu-id="58f8e-211">Ten argument będzie przepływał do fabryki, gdzie może służyć do kontrolowania sposobu tworzenia i inicjowania kontekstu.</span><span class="sxs-lookup"><span data-stu-id="58f8e-211">This argument will then flow into the factory, where it can be used to control how the context is created and initialized.</span></span> <span data-ttu-id="58f8e-212">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="58f8e-212">For example:</span></span>

```CSharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args) 
        => new SomeDbContext(args.Contains("--dev"));
}
```

<span data-ttu-id="58f8e-213">Dokumentacja jest śledzona przez [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419)problemu.</span><span class="sxs-lookup"><span data-stu-id="58f8e-213">Documentation is tracked by issue [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span></span>

### <a name="no-tracking-queries-with-identity-resolution"></a><span data-ttu-id="58f8e-214">Nie śledzij zapytań z rozpoznawaniem tożsamości</span><span class="sxs-lookup"><span data-stu-id="58f8e-214">No-tracking queries with identity resolution</span></span>

<span data-ttu-id="58f8e-215">Nie można teraz skonfigurować zapytań śledzenia, aby przeprowadzić rozpoznawanie tożsamości.</span><span class="sxs-lookup"><span data-stu-id="58f8e-215">No-tracking queries can now be configured to perform identity resolution.</span></span> <span data-ttu-id="58f8e-216">Na przykład następujące zapytanie utworzy nowe wystąpienie blogu dla każdego wpisu, nawet jeśli każdy blog ma ten sam klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="58f8e-216">For example, the following query will create a new Blog instance for each Post, even if each Blog has the same primary key.</span></span> 

```CSharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

<span data-ttu-id="58f8e-217">Jednak koszt zwykle jest nieco wolniejszy i zawsze korzysta z większej ilości pamięci, to zapytanie można zmienić, aby upewnić się, że tworzone jest tylko jedno wystąpienie blogu:</span><span class="sxs-lookup"><span data-stu-id="58f8e-217">However, at the expense of usually being slightly slower and always using more memory, this query can be changed to ensure only a single Blog instance is created:</span></span>

```CSharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

<span data-ttu-id="58f8e-218">Należy zauważyć, że jest to przydatne tylko w przypadku zapytań bez śledzenia, ponieważ wszystkie zapytania śledzenia już wykazują takie zachowanie.</span><span class="sxs-lookup"><span data-stu-id="58f8e-218">Note that this is only useful for no-tracking queries since all tracking queries already exhibit this behavior.</span></span> <span data-ttu-id="58f8e-219">Ponadto, po przeglądzie interfejsu API, `PerformIdentityResolution` składnia zostanie zmieniona.</span><span class="sxs-lookup"><span data-stu-id="58f8e-219">Also, following API review, the `PerformIdentityResolution` syntax will be changed.</span></span> <span data-ttu-id="58f8e-220">Zobacz [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span><span class="sxs-lookup"><span data-stu-id="58f8e-220">See [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span></span>

<span data-ttu-id="58f8e-221">Dokumentacja jest śledzona przez [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895)problemu.</span><span class="sxs-lookup"><span data-stu-id="58f8e-221">Documentation is tracked by issue [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span></span>

### <a name="stored-persisted-computed-columns"></a><span data-ttu-id="58f8e-222">Przechowywane (utrwalone) kolumny obliczane</span><span class="sxs-lookup"><span data-stu-id="58f8e-222">Stored (persisted) computed columns</span></span>

<span data-ttu-id="58f8e-223">Większość baz danych umożliwia przechowywanie wartości kolumn obliczanych po obliczeniach.</span><span class="sxs-lookup"><span data-stu-id="58f8e-223">Most databases allow computed column values to be stored after computation.</span></span> <span data-ttu-id="58f8e-224">Gdy to zajmuje miejsce na dysku, kolumna obliczana jest obliczana tylko raz podczas aktualizacji, a nie za każdym razem, gdy jej wartość zostanie pobrana.</span><span class="sxs-lookup"><span data-stu-id="58f8e-224">While this takes up disk space, the computed column is calculated only once on update, instead of each time its value is retrieved.</span></span> <span data-ttu-id="58f8e-225">Pozwala to również na indeksowanie kolumny dla niektórych baz danych.</span><span class="sxs-lookup"><span data-stu-id="58f8e-225">This also allows the column to be indexed for some databases.</span></span>

<span data-ttu-id="58f8e-226">EF Core 5,0 umożliwia skonfigurowanie kolumn obliczanych jako przechowywanych.</span><span class="sxs-lookup"><span data-stu-id="58f8e-226">EF Core 5.0 allows computed columns to be configured as stored.</span></span> <span data-ttu-id="58f8e-227">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="58f8e-227">For example:</span></span>
 
```CSharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a><span data-ttu-id="58f8e-228">Kolumny obliczane przez SQLite</span><span class="sxs-lookup"><span data-stu-id="58f8e-228">SQLite computed columns</span></span>

<span data-ttu-id="58f8e-229">EF Core teraz obsługuje kolumny obliczane w bazach danych oprogramowania SQLite.</span><span class="sxs-lookup"><span data-stu-id="58f8e-229">EF Core now supports computed columns in SQLite databases.</span></span>

## <a name="preview-4"></a><span data-ttu-id="58f8e-230">Wersja zapoznawcza 4</span><span class="sxs-lookup"><span data-stu-id="58f8e-230">Preview 4</span></span>

### <a name="configure-database-precisionscale-in-model"></a><span data-ttu-id="58f8e-231">Konfiguruj precyzję i skalowanie bazy danych w modelu</span><span class="sxs-lookup"><span data-stu-id="58f8e-231">Configure database precision/scale in model</span></span>

<span data-ttu-id="58f8e-232">Precyzja i skala właściwości można teraz określić przy użyciu konstruktora modeli.</span><span class="sxs-lookup"><span data-stu-id="58f8e-232">Precision and scale for a property can now be specified using the model builder.</span></span> <span data-ttu-id="58f8e-233">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="58f8e-233">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

<span data-ttu-id="58f8e-234">Precyzja i skala można nadal ustawiać za pośrednictwem pełnego typu bazy danych, na przykład "Decimal (16, 4)".</span><span class="sxs-lookup"><span data-stu-id="58f8e-234">Precision and scale can still be set via the full database type, such as "decimal(16,4)".</span></span> 

<span data-ttu-id="58f8e-235">Dokumentacja jest śledzona przez [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527)problemu.</span><span class="sxs-lookup"><span data-stu-id="58f8e-235">Documentation is tracked by issue [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span></span>

### <a name="specify-sql-server-index-fill-factor"></a><span data-ttu-id="58f8e-236">Określ współczynnik wypełnienia indeksu SQL Server</span><span class="sxs-lookup"><span data-stu-id="58f8e-236">Specify SQL Server index fill factor</span></span>

<span data-ttu-id="58f8e-237">Współczynnik wypełniania można teraz określić podczas tworzenia indeksu na SQL Server.</span><span class="sxs-lookup"><span data-stu-id="58f8e-237">The fill factor can now be specified when creating an index on SQL Server.</span></span> <span data-ttu-id="58f8e-238">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="58f8e-238">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a><span data-ttu-id="58f8e-239">Wersja zapoznawcza 3</span><span class="sxs-lookup"><span data-stu-id="58f8e-239">Preview 3</span></span>

### <a name="filtered-include"></a><span data-ttu-id="58f8e-240">Filtr obejmujący</span><span class="sxs-lookup"><span data-stu-id="58f8e-240">Filtered Include</span></span>

<span data-ttu-id="58f8e-241">Metoda include obsługuje teraz filtrowanie uwzględnionych jednostek.</span><span class="sxs-lookup"><span data-stu-id="58f8e-241">The Include method now supports filtering of the entities included.</span></span> <span data-ttu-id="58f8e-242">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="58f8e-242">For example:</span></span>

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="58f8e-243">To zapytanie będzie zwracać Blogi razem z poszczególnymi wpisami skojarzonymi, ale tylko wtedy, gdy tytuł wpisu zawiera "ser".</span><span class="sxs-lookup"><span data-stu-id="58f8e-243">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="58f8e-244">Pomiń i zrób można także użyć, aby zmniejszyć liczbę uwzględnionych jednostek.</span><span class="sxs-lookup"><span data-stu-id="58f8e-244">Skip and Take can also be used to reduce the number of included entities.</span></span> <span data-ttu-id="58f8e-245">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="58f8e-245">For example:</span></span>
 
```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
<span data-ttu-id="58f8e-246">To zapytanie będzie zwracać blogi z co najwyżej pięcioma wpisami zawartymi w każdym blogu.</span><span class="sxs-lookup"><span data-stu-id="58f8e-246">This query will return blogs with at most five posts included on each blog.</span></span>

<span data-ttu-id="58f8e-247">Szczegółowe informacje znajdują się w [dokumentacji dołączanej](xref:core/querying/related-data#filtered-include) .</span><span class="sxs-lookup"><span data-stu-id="58f8e-247">See the [Include documentation](xref:core/querying/related-data#filtered-include) for full details.</span></span>

### <a name="new-modelbuilder-api-for-navigation-properties"></a><span data-ttu-id="58f8e-248">Nowy interfejs API element modelbuilder dla właściwości nawigacji</span><span class="sxs-lookup"><span data-stu-id="58f8e-248">New ModelBuilder API for navigation properties</span></span>

<span data-ttu-id="58f8e-249">Właściwości nawigacji są przede wszystkim konfigurowane podczas [definiowania relacji](xref:core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="58f8e-249">Navigation properties are primarily configured when [defining relationships](xref:core/modeling/relationships).</span></span> <span data-ttu-id="58f8e-250">Jednak nowa `Navigation` Metoda może być używana w przypadkach, w których właściwości nawigacji wymagają dodatkowej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="58f8e-250">However, the new `Navigation` method can be used in the cases where navigation properties need additional configuration.</span></span> <span data-ttu-id="58f8e-251">Na przykład, aby ustawić pole zapasowe dla nawigacji, gdy pole nie zostanie znalezione według Konwencji:</span><span class="sxs-lookup"><span data-stu-id="58f8e-251">For example, to set a backing field for the navigation when the field would not be found by convention:</span></span>

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

<span data-ttu-id="58f8e-252">Należy pamiętać, że `Navigation` interfejs API nie zastępuje konfiguracji relacji.</span><span class="sxs-lookup"><span data-stu-id="58f8e-252">Note that the `Navigation` API does not replace relationship configuration.</span></span> <span data-ttu-id="58f8e-253">Zamiast tego umożliwia dodatkową konfigurację właściwości nawigacji w już odnalezionych lub zdefiniowanych relacjach.</span><span class="sxs-lookup"><span data-stu-id="58f8e-253">Instead it allows additional configuration of navigation properties in already discovered or defined relationships.</span></span>

<span data-ttu-id="58f8e-254">Zapoznaj się z [dokumentacją dotyczącą konfigurowania właściwości nawigacji](xref:core/modeling/relationships#configuring-navigation-properties).</span><span class="sxs-lookup"><span data-stu-id="58f8e-254">See the [Configuring Navigation Properties documentation](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a><span data-ttu-id="58f8e-255">Nowe parametry wiersza polecenia dla przestrzeni nazw i parametrów połączenia</span><span class="sxs-lookup"><span data-stu-id="58f8e-255">New command-line parameters for namespaces and connection strings</span></span> 

<span data-ttu-id="58f8e-256">Migracje i tworzenie szkieletów umożliwiają teraz określenie przestrzeni nazw w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="58f8e-256">Migrations and scaffolding now allow namespaces to be specified on the command line.</span></span> <span data-ttu-id="58f8e-257">Na przykład w celu odtworzenia bazy danych, w której są umieszczane klasy kontekstu i modelu w różnych przestrzeniach nazw:</span><span class="sxs-lookup"><span data-stu-id="58f8e-257">For example, to reverse engineer a database putting the context and model classes in different namespaces:</span></span> 

```
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

<span data-ttu-id="58f8e-258">Szczegółowe informacje znajdują się [w dokumentacji](xref:core/managing-schemas/scaffolding#directories-and-namespaces) dotyczącej [migracji](xref:core/managing-schemas/migrations/index#namespaces) i odtwarzania.</span><span class="sxs-lookup"><span data-stu-id="58f8e-258">See the [Migrations](xref:core/managing-schemas/migrations/index#namespaces) and [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces) documentation for full details.</span></span>

---
<span data-ttu-id="58f8e-259">Ponadto parametry połączenia można teraz przekazywać do `database-update` polecenia:</span><span class="sxs-lookup"><span data-stu-id="58f8e-259">Also, a connection string can now be passed to the `database-update` command:</span></span>

```
dotnet ef database update --connection "connection string"
```

<span data-ttu-id="58f8e-260">Szczegółowe informacje znajdują się w [dokumentacji narzędzi](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) .</span><span class="sxs-lookup"><span data-stu-id="58f8e-260">See the [Tools documentation](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) for full details.</span></span>

<span data-ttu-id="58f8e-261">Do poleceń programu PowerShell, które są używane w konsoli Menedżera pakietów programu VS, dodano również równoważne parametry.</span><span class="sxs-lookup"><span data-stu-id="58f8e-261">Equivalent parameters have also been added to the PowerShell commands used in the VS Package Manager Console.</span></span>

### <a name="enabledetailederrors-has-returned"></a><span data-ttu-id="58f8e-262">EnableDetailedErrors</span><span class="sxs-lookup"><span data-stu-id="58f8e-262">EnableDetailedErrors has returned</span></span>

<span data-ttu-id="58f8e-263">Ze względu na wydajność EF nie wykonuje dodatkowych kontroli wartości null podczas odczytywania wartości z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="58f8e-263">For performance reasons, EF doesn't do additional null-checks when reading values from the database.</span></span> <span data-ttu-id="58f8e-264">Może to spowodować, że wyjątki są trudne do wylogowania, gdy zostanie napotkany nieoczekiwany element null.</span><span class="sxs-lookup"><span data-stu-id="58f8e-264">This can result in exceptions that are hard to root-cause when an unexpected null is encountered.</span></span>

<span data-ttu-id="58f8e-265">Korzystanie z programu `EnableDetailedErrors` spowoduje dodanie dodatkowych kontroli wartości null do zapytań, takich jak w przypadku małego obciążenia wydajności, te błędy są łatwiejsze do śledzenia z przyczyn głównych.</span><span class="sxs-lookup"><span data-stu-id="58f8e-265">Using `EnableDetailedErrors` will add extra null checking to queries such that, for a small performance overhead, these errors are easier to trace back to a root cause.</span></span>  

<span data-ttu-id="58f8e-266">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="58f8e-266">For example:</span></span>
```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors 
        .UseSqlServer(Your.SqlServerConnectionString);
```

<span data-ttu-id="58f8e-267">Dokumentacja jest śledzona przez [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955)problemu.</span><span class="sxs-lookup"><span data-stu-id="58f8e-267">Documentation is tracked by issue [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="58f8e-268">Klucze partycji Cosmos</span><span class="sxs-lookup"><span data-stu-id="58f8e-268">Cosmos partition keys</span></span>

<span data-ttu-id="58f8e-269">Klucz partycji, który ma być używany dla danego zapytania, można teraz określić w zapytaniu.</span><span class="sxs-lookup"><span data-stu-id="58f8e-269">The partition key to use for a given query can now be specified in the query.</span></span> <span data-ttu-id="58f8e-270">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="58f8e-270">For example:</span></span>

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

<span data-ttu-id="58f8e-271">Dokumentacja jest śledzona przez [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199)problemu.</span><span class="sxs-lookup"><span data-stu-id="58f8e-271">Documentation is tracked by issue [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span></span>

### <a name="support-for-the-sql-server-datalength-function"></a><span data-ttu-id="58f8e-272">Obsługa funkcji SQL Server DATALENGTH</span><span class="sxs-lookup"><span data-stu-id="58f8e-272">Support for the SQL Server DATALENGTH function</span></span>

<span data-ttu-id="58f8e-273">Dostęp do niego można uzyskać za pomocą nowej `EF.Functions.DataLength` metody.</span><span class="sxs-lookup"><span data-stu-id="58f8e-273">This can be accessed using the new `EF.Functions.DataLength` method.</span></span> <span data-ttu-id="58f8e-274">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="58f8e-274">For example:</span></span>
```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
``` 

## <a name="preview-2"></a><span data-ttu-id="58f8e-275">Preview 2</span><span class="sxs-lookup"><span data-stu-id="58f8e-275">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="58f8e-276">Użyj atrybutu języka C#, aby określić pole zapasowe właściwości</span><span class="sxs-lookup"><span data-stu-id="58f8e-276">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="58f8e-277">Atrybut języka C# może być teraz używany do określania pola zapasowego dla właściwości.</span><span class="sxs-lookup"><span data-stu-id="58f8e-277">A C# attribute can now be used to specify the backing field for a property.</span></span> <span data-ttu-id="58f8e-278">Ten atrybut pozwala EF Core nadal pisać i odczytywać dane z pola zapasowego, tak jak zwykle, nawet jeśli nie można automatycznie znaleźć pola zapasowego.</span><span class="sxs-lookup"><span data-stu-id="58f8e-278">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span> <span data-ttu-id="58f8e-279">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="58f8e-279">For example:</span></span>

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

<span data-ttu-id="58f8e-280">Dokumentacja jest śledzona przez [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230)problemu.</span><span class="sxs-lookup"><span data-stu-id="58f8e-280">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="58f8e-281">Kompletne mapowanie rozróżniacza</span><span class="sxs-lookup"><span data-stu-id="58f8e-281">Complete discriminator mapping</span></span>

<span data-ttu-id="58f8e-282">EF Core używa kolumny rozróżniacza dla [mapowania TPH hierarchii dziedziczenia](/ef/core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="58f8e-282">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](/ef/core/modeling/inheritance).</span></span> <span data-ttu-id="58f8e-283">Niektóre ulepszenia wydajności są możliwe tak długo, jak EF Core wie wszystkie możliwe wartości dla rozróżniacza.</span><span class="sxs-lookup"><span data-stu-id="58f8e-283">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span> <span data-ttu-id="58f8e-284">EF Core 5,0 implementuje teraz te ulepszenia.</span><span class="sxs-lookup"><span data-stu-id="58f8e-284">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="58f8e-285">Na przykład poprzednie wersje programu EF Core zawsze generują te dane SQL dla zapytania zwracającego wszystkie typy w hierarchii:</span><span class="sxs-lookup"><span data-stu-id="58f8e-285">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="58f8e-286">EF Core 5,0 będzie teraz generować następujące elementy, gdy zostanie skonfigurowane kompletne mapowanie rozróżniacza:</span><span class="sxs-lookup"><span data-stu-id="58f8e-286">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="58f8e-287">Będzie to domyślne zachowanie rozpoczynające się od wersji zapoznawczej 3.</span><span class="sxs-lookup"><span data-stu-id="58f8e-287">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="58f8e-288">Ulepszenia wydajności w programie Microsoft. Data. sqlite</span><span class="sxs-lookup"><span data-stu-id="58f8e-288">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="58f8e-289">Wprowadziliśmy dwie ulepszenia wydajności dla oprogramowania SQLIte:</span><span class="sxs-lookup"><span data-stu-id="58f8e-289">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="58f8e-290">Pobieranie danych binarnych i ciągów za pomocą metody GetBytes, GetChars i GetTextReader jest teraz wydajniejsze dzięki użyciu SqliteBlob i strumieni.</span><span class="sxs-lookup"><span data-stu-id="58f8e-290">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="58f8e-291">Inicjalizacja SqliteConnection jest teraz opóźniona.</span><span class="sxs-lookup"><span data-stu-id="58f8e-291">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="58f8e-292">Te udoskonalenia znajdują się w dostawcy ADO.NET Microsoft. Data. SQLite, a tym samym zwiększyć wydajność poza EF Core.</span><span class="sxs-lookup"><span data-stu-id="58f8e-292">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="58f8e-293">Wersja zapoznawcza 1</span><span class="sxs-lookup"><span data-stu-id="58f8e-293">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="58f8e-294">Rejestrowanie proste</span><span class="sxs-lookup"><span data-stu-id="58f8e-294">Simple logging</span></span>

<span data-ttu-id="58f8e-295">Ta funkcja dodaje funkcje podobne do `Database.Log` programu w programie Ef6.</span><span class="sxs-lookup"><span data-stu-id="58f8e-295">This feature adds functionality similar to `Database.Log` in EF6.</span></span> <span data-ttu-id="58f8e-296">Oznacza to, że zapewnia prosty sposób pobierania dzienników z EF Core bez konieczności konfigurowania żadnego rodzaju zewnętrznej platformy rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="58f8e-296">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="58f8e-297">Wstępna dokumentacja jest zawarta w [statusie tygodnia Ef 5 grudnia 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span><span class="sxs-lookup"><span data-stu-id="58f8e-297">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="58f8e-298">Dodatkowa dokumentacja jest śledzona przez [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085)problemu.</span><span class="sxs-lookup"><span data-stu-id="58f8e-298">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="58f8e-299">Prosty sposób uzyskiwania wygenerowanego kodu SQL</span><span class="sxs-lookup"><span data-stu-id="58f8e-299">Simple way to get generated SQL</span></span>

<span data-ttu-id="58f8e-300">EF Core 5,0 wprowadza `ToQueryString` metodę rozszerzenia, która zwróci kod SQL, który EF Core zostanie wygenerowany podczas wykonywania zapytania LINQ.</span><span class="sxs-lookup"><span data-stu-id="58f8e-300">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="58f8e-301">Wstępna dokumentacja jest uwzględniona w [statusie tygodniowym EF dla 9 stycznia 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span><span class="sxs-lookup"><span data-stu-id="58f8e-301">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="58f8e-302">Dodatkowa dokumentacja jest śledzona przez [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331)problemu.</span><span class="sxs-lookup"><span data-stu-id="58f8e-302">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="58f8e-303">Użyj atrybutu języka C#, aby wskazać, że jednostka nie ma klucza</span><span class="sxs-lookup"><span data-stu-id="58f8e-303">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="58f8e-304">Typ jednostki można teraz skonfigurować jako bez klucza przy użyciu nowego elementu `KeylessAttribute` .</span><span class="sxs-lookup"><span data-stu-id="58f8e-304">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span> <span data-ttu-id="58f8e-305">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="58f8e-305">For example:</span></span>

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="58f8e-306">Dokumentacja jest śledzona przez [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186)problemu.</span><span class="sxs-lookup"><span data-stu-id="58f8e-306">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="58f8e-307">Parametry Connection lub Connection można zmienić w zainicjowanym kontekście DbContext</span><span class="sxs-lookup"><span data-stu-id="58f8e-307">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="58f8e-308">Teraz łatwiej jest utworzyć wystąpienie DbContext bez żadnego połączenia lub parametrów połączenia.</span><span class="sxs-lookup"><span data-stu-id="58f8e-308">It is now easier to create a DbContext instance without any connection or connection string.</span></span> <span data-ttu-id="58f8e-309">Ponadto parametry połączenia lub połączenia można teraz przystąpić do wystąpienia kontekstu.</span><span class="sxs-lookup"><span data-stu-id="58f8e-309">Also, the connection or connection string can now be mutated on the context instance.</span></span> <span data-ttu-id="58f8e-310">Ta funkcja pozwala temu samemu wystąpieniu kontekstu na dynamiczne łączenie się z różnymi bazami danych.</span><span class="sxs-lookup"><span data-stu-id="58f8e-310">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="58f8e-311">Dokumentacja jest śledzona przez [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075)problemu.</span><span class="sxs-lookup"><span data-stu-id="58f8e-311">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="58f8e-312">Serwery proxy śledzenia zmian</span><span class="sxs-lookup"><span data-stu-id="58f8e-312">Change-tracking proxies</span></span>

<span data-ttu-id="58f8e-313">EF Core mogą teraz generować serwery proxy środowiska uruchomieniowego, które automatycznie implementują [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) i [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span><span class="sxs-lookup"><span data-stu-id="58f8e-313">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) and [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span></span> <span data-ttu-id="58f8e-314">Następnie te zmiany są raportowane w oparciu o właściwości jednostki bezpośrednio do EF Core, unikając konieczności skanowania pod kątem zmian.</span><span class="sxs-lookup"><span data-stu-id="58f8e-314">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span> <span data-ttu-id="58f8e-315">Jednak serwery proxy są dostarczane z własnym zestawem ograniczeń, więc nie są przeznaczone dla wszystkich użytkowników.</span><span class="sxs-lookup"><span data-stu-id="58f8e-315">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="58f8e-316">Dokumentacja jest śledzona przez [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076)problemu.</span><span class="sxs-lookup"><span data-stu-id="58f8e-316">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="58f8e-317">Udoskonalone widoki debugowania</span><span class="sxs-lookup"><span data-stu-id="58f8e-317">Enhanced debug views</span></span>

<span data-ttu-id="58f8e-318">Widoki debugowania to prosty sposób na zajrzeć do wewnętrznych EF Core w przypadku problemów z debugowaniem.</span><span class="sxs-lookup"><span data-stu-id="58f8e-318">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span> <span data-ttu-id="58f8e-319">Widok debugowania dla modelu został zaimplementowany jakiś czas temu.</span><span class="sxs-lookup"><span data-stu-id="58f8e-319">A debug view for the Model was implemented some time ago.</span></span> <span data-ttu-id="58f8e-320">W przypadku EF Core 5,0 ten widok modelu jest łatwiejszy do odczytania i dodania nowego widoku debugowania dla śledzonych jednostek w Menedżerze stanu.</span><span class="sxs-lookup"><span data-stu-id="58f8e-320">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="58f8e-321">Wstępna dokumentacja jest uwzględniona w [Stanach tygodnia EF 12 grudnia 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span><span class="sxs-lookup"><span data-stu-id="58f8e-321">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="58f8e-322">Dodatkowa dokumentacja jest śledzona przez [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086)problemu.</span><span class="sxs-lookup"><span data-stu-id="58f8e-322">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="58f8e-323">Ulepszona obsługa semantyki o wartości null bazy danych</span><span class="sxs-lookup"><span data-stu-id="58f8e-323">Improved handling of database null semantics</span></span>

<span data-ttu-id="58f8e-324">Relacyjne bazy danych zazwyczaj traktują wartości NULL jako nieznaną wartość i w związku z tym nie są równe żadnym innym NULL.</span><span class="sxs-lookup"><span data-stu-id="58f8e-324">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span> <span data-ttu-id="58f8e-325">Chociaż język C# traktuje wartość null w postaci wartości zdefiniowanej, co porównuje ją z innymi wartościami null.</span><span class="sxs-lookup"><span data-stu-id="58f8e-325">While C# treats null as a defined value, which compares equal to any other null.</span></span> <span data-ttu-id="58f8e-326">EF Core domyślnie tłumaczy zapytania tak, aby korzystały z semantyki wartości null języka C#.</span><span class="sxs-lookup"><span data-stu-id="58f8e-326">EF Core by default translates queries so that they use C# null semantics.</span></span> <span data-ttu-id="58f8e-327">EF Core 5,0 znacznie poprawia wydajność tych tłumaczeń.</span><span class="sxs-lookup"><span data-stu-id="58f8e-327">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="58f8e-328">Dokumentacja jest śledzona przez [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612)problemu.</span><span class="sxs-lookup"><span data-stu-id="58f8e-328">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="58f8e-329">Właściwości indeksatora</span><span class="sxs-lookup"><span data-stu-id="58f8e-329">Indexer properties</span></span>

<span data-ttu-id="58f8e-330">EF Core 5,0 obsługuje mapowanie właściwości indeksatora języka C#.</span><span class="sxs-lookup"><span data-stu-id="58f8e-330">EF Core 5.0 supports mapping of C# indexer properties.</span></span> <span data-ttu-id="58f8e-331">Te właściwości umożliwiają jednostkom działanie jako zbiory właściwości, w których kolumny są mapowane na nazwane właściwości w zbiorze.</span><span class="sxs-lookup"><span data-stu-id="58f8e-331">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="58f8e-332">Dokumentacja jest śledzona przez [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018)problemu.</span><span class="sxs-lookup"><span data-stu-id="58f8e-332">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="58f8e-333">Generowanie ograniczeń check dla mapowań wyliczenia</span><span class="sxs-lookup"><span data-stu-id="58f8e-333">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="58f8e-334">Migracje EF Core 5,0 mogą teraz generować ograniczenia CHECK dla mapowań właściwości enum.</span><span class="sxs-lookup"><span data-stu-id="58f8e-334">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span> <span data-ttu-id="58f8e-335">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="58f8e-335">For example:</span></span>

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="58f8e-336">Dokumentacja jest śledzona przez [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082)problemu.</span><span class="sxs-lookup"><span data-stu-id="58f8e-336">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="58f8e-337">Isrelacyjne</span><span class="sxs-lookup"><span data-stu-id="58f8e-337">IsRelational</span></span>

<span data-ttu-id="58f8e-338">Dodano nową `IsRelational` metodę oprócz istniejących `IsSqlServer` , `IsSqlite` i `IsInMemory` .</span><span class="sxs-lookup"><span data-stu-id="58f8e-338">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span> <span data-ttu-id="58f8e-339">Tej metody można użyć do sprawdzenia, czy DbContext używa dowolnego dostawcy relacyjnej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="58f8e-339">This method can be used to test if the DbContext is using any relational database provider.</span></span> <span data-ttu-id="58f8e-340">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="58f8e-340">For example:</span></span>

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="58f8e-341">Dokumentacja jest śledzona przez [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185)problemu.</span><span class="sxs-lookup"><span data-stu-id="58f8e-341">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="58f8e-342">Cosmos optymistyczne współbieżność za pomocą elementów ETag</span><span class="sxs-lookup"><span data-stu-id="58f8e-342">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="58f8e-343">Dostawca bazy danych Azure Cosmos DB obsługuje teraz optymistyczne współbieżność za pomocą elementów ETag.</span><span class="sxs-lookup"><span data-stu-id="58f8e-343">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span> <span data-ttu-id="58f8e-344">Użyj konstruktora modeli w OnModelCreating, aby skonfigurować element ETag:</span><span class="sxs-lookup"><span data-stu-id="58f8e-344">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="58f8e-345">Metody SaveChanges następnie zgłosi `DbUpdateConcurrencyException` konflikt współbieżności, który [można obsłużyć](https://docs.microsoft.com/ef/core/saving/concurrency) w celu zaimplementowania ponownych prób itd.</span><span class="sxs-lookup"><span data-stu-id="58f8e-345">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](https://docs.microsoft.com/ef/core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="58f8e-346">Dokumentacja jest śledzona przez [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099)problemu.</span><span class="sxs-lookup"><span data-stu-id="58f8e-346">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="58f8e-347">Tłumaczenie zapytań dla większej liczby konstrukcji DateTime</span><span class="sxs-lookup"><span data-stu-id="58f8e-347">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="58f8e-348">Zapytania zawierające nową konstrukcję DateTime są teraz tłumaczone.</span><span class="sxs-lookup"><span data-stu-id="58f8e-348">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="58f8e-349">Ponadto następujące funkcje SQL Server są teraz mapowane:</span><span class="sxs-lookup"><span data-stu-id="58f8e-349">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="58f8e-350">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="58f8e-350">DateDiffWeek</span></span>
* <span data-ttu-id="58f8e-351">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="58f8e-351">DateFromParts</span></span>

<span data-ttu-id="58f8e-352">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="58f8e-352">For example:</span></span>

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="58f8e-353">Dokumentacja jest śledzona przez [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)problemu.</span><span class="sxs-lookup"><span data-stu-id="58f8e-353">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="58f8e-354">Tłumaczenie zapytania dla większej liczby konstrukcji tablicy</span><span class="sxs-lookup"><span data-stu-id="58f8e-354">Query translations for more byte array constructs</span></span>

<span data-ttu-id="58f8e-355">Zapytania używające właściwości Contains, Length, SequenceEqual itp. on-Byte [] są teraz tłumaczone na SQL.</span><span class="sxs-lookup"><span data-stu-id="58f8e-355">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="58f8e-356">Wstępna dokumentacja jest zawarta w [statusie tygodnia Ef 5 grudnia 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span><span class="sxs-lookup"><span data-stu-id="58f8e-356">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="58f8e-357">Dodatkowa dokumentacja jest śledzona przez [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)problemu.</span><span class="sxs-lookup"><span data-stu-id="58f8e-357">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="58f8e-358">Tłumaczenie zapytania do tyłu</span><span class="sxs-lookup"><span data-stu-id="58f8e-358">Query translation for Reverse</span></span>

<span data-ttu-id="58f8e-359">Zapytania z użyciem `Reverse` są teraz tłumaczone.</span><span class="sxs-lookup"><span data-stu-id="58f8e-359">Queries using `Reverse` are now translated.</span></span> <span data-ttu-id="58f8e-360">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="58f8e-360">For example:</span></span>

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="58f8e-361">Dokumentacja jest śledzona przez [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)problemu.</span><span class="sxs-lookup"><span data-stu-id="58f8e-361">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="58f8e-362">Tłumaczenie zapytania dla operatorów bitowych</span><span class="sxs-lookup"><span data-stu-id="58f8e-362">Query translation for bitwise operators</span></span>

<span data-ttu-id="58f8e-363">Zapytania wykorzystujące operatory bitowe są teraz tłumaczone na przykład w większej liczbie przypadków:</span><span class="sxs-lookup"><span data-stu-id="58f8e-363">Queries using bitwise operators are now translated in more cases For example:</span></span>

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="58f8e-364">Dokumentacja jest śledzona przez [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)problemu.</span><span class="sxs-lookup"><span data-stu-id="58f8e-364">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="58f8e-365">Tłumaczenie zapytania dla ciągów w Cosmos</span><span class="sxs-lookup"><span data-stu-id="58f8e-365">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="58f8e-366">Zapytania korzystające z metod String zawierają, StartsWith i EndsWith są teraz tłumaczone przy użyciu dostawcy Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="58f8e-366">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="58f8e-367">Dokumentacja jest śledzona przez [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)problemu.</span><span class="sxs-lookup"><span data-stu-id="58f8e-367">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
