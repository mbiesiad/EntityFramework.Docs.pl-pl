---
title: Testowanie kodu, który używa EF Core-EF Core
description: Różne podejścia do testowania aplikacji korzystających EF Core
author: ajcvickers
ms.date: 04/22/2020
uid: core/miscellaneous/testing/index
ms.openlocfilehash: 7929c284c2794b2fcc95235ae413d56895ebb6e2
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526813"
---
# <a name="testing-code-that-uses-ef-core"></a><span data-ttu-id="efd43-103">Testowanie kodu korzystającego ze środowiska EF Core</span><span class="sxs-lookup"><span data-stu-id="efd43-103">Testing code that uses EF Core</span></span>

<span data-ttu-id="efd43-104">Testowanie kodu, który uzyskuje dostęp do bazy danych, wymaga:</span><span class="sxs-lookup"><span data-stu-id="efd43-104">Testing code that accesses a database requires either:</span></span>
* <span data-ttu-id="efd43-105">Uruchamianie zapytań i aktualizacji w ramach tego samego systemu bazy danych używanego w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="efd43-105">Running queries and updates against the same database system used in production.</span></span>
* <span data-ttu-id="efd43-106">Uruchamianie zapytań i aktualizacji w celu łatwiejszego zarządzania systemem bazy danych.</span><span class="sxs-lookup"><span data-stu-id="efd43-106">Running queries and updates against some other easier to manage database system.</span></span>
* <span data-ttu-id="efd43-107">Używanie podwójnego rozliczania testów lub innego mechanizmu, aby uniknąć używania bazy danych w ogóle.</span><span class="sxs-lookup"><span data-stu-id="efd43-107">Using test doubles or some other mechanism to avoid using a database at all.</span></span>

<span data-ttu-id="efd43-108">Ten dokument zawiera informacje o zaletach związanych z poszczególnymi opcjami oraz sposób, w jaki EF Core mogą być używane z każdym podejściem.</span><span class="sxs-lookup"><span data-stu-id="efd43-108">This document outlines the trade-offs involved in each of these choices and shows how EF Core can be used with each approach.</span></span>  

> [!TIP]
> <span data-ttu-id="efd43-109">Zobacz [przykład testowania EF Core](xref:core/miscellaneous/testing/testing-sample) , aby uzyskać kod pokazujący koncepcje wprowadzone w tym miejscu.</span><span class="sxs-lookup"><span data-stu-id="efd43-109">See [EF Core testing sample](xref:core/miscellaneous/testing/testing-sample) for code demonstrating the concepts introduced here.</span></span> 

## <a name="all-database-providers-are-not-equal"></a><span data-ttu-id="efd43-110">Wszyscy dostawcy bazy danych nie są równe</span><span class="sxs-lookup"><span data-stu-id="efd43-110">All database providers are not equal</span></span>

<span data-ttu-id="efd43-111">Bardzo ważne jest, aby zrozumieć, że EF Core nie jest zaprojektowana do abstrakcyjnych wszystkich aspektów bazowego systemu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="efd43-111">It is very important to understand that EF Core is not designed to abstract every aspect of the underlying database system.</span></span>
<span data-ttu-id="efd43-112">Zamiast tego EF Core jest wspólnym zestawem wzorców i koncepcji, które mogą być używane z dowolnym systemem bazy danych.</span><span class="sxs-lookup"><span data-stu-id="efd43-112">Instead, EF Core is a common set of patterns and concepts that can be used with any database system.</span></span>
<span data-ttu-id="efd43-113">EF Core dostawców baz danych, a następnie zachowanie specyficzne dla bazy danych i funkcjonalności w ramach tego wspólnego środowiska.</span><span class="sxs-lookup"><span data-stu-id="efd43-113">EF Core database providers then layer database-specific behavior and functionality over this common framework.</span></span>
<span data-ttu-id="efd43-114">Dzięki temu każdy system bazy danych może wykonać to działanie najlepiej, zachowując zgodność z innymi systemami baz danych.</span><span class="sxs-lookup"><span data-stu-id="efd43-114">This allows each database system to do what it does best while still maintaining commonality, where appropriate, with other database systems.</span></span> 

<span data-ttu-id="efd43-115">W związku z tym oznacza to, że przełączenie dostawcy bazy danych zmieni zachowanie EF Core i aplikacja nie będzie mogła działać prawidłowo, chyba że jawnie rozwiąże się z wszelkimi różnicami w działaniu.</span><span class="sxs-lookup"><span data-stu-id="efd43-115">Fundamentally, this means that switching out the database provider will change EF Core behavior and the application can't be expected to function correctly unless it explicitly accounts for any differences in behavior.</span></span>
<span data-ttu-id="efd43-116">W wielu przypadkach ten stan będzie działał, ponieważ istnieje wysoki stopień zgodności między relacyjnymi bazami danych.</span><span class="sxs-lookup"><span data-stu-id="efd43-116">That being said, in many cases doing this will work because there is a high degree of commonality amongst relational databases.</span></span>
<span data-ttu-id="efd43-117">Jest to dobre i złe.</span><span class="sxs-lookup"><span data-stu-id="efd43-117">This is good and bad.</span></span>
<span data-ttu-id="efd43-118">Dobre, ponieważ przechodzenie między systemami bazy danych może być stosunkowo proste.</span><span class="sxs-lookup"><span data-stu-id="efd43-118">Good because moving between database systems can be relatively easy.</span></span>
<span data-ttu-id="efd43-119">Zła, ponieważ może dać fałszywe znaczenie zabezpieczeń, jeśli aplikacja nie została w pełni przetestowana z nowym systemem bazy danych.</span><span class="sxs-lookup"><span data-stu-id="efd43-119">Bad because it can give a false sense of security if the application is not fully tested against the new database system.</span></span>  

## <a name="approach-1-production-database-system"></a><span data-ttu-id="efd43-120">Podejście 1: system produkcyjnej bazy danych</span><span class="sxs-lookup"><span data-stu-id="efd43-120">Approach 1: Production database system</span></span>

<span data-ttu-id="efd43-121">Zgodnie z opisem w poprzedniej sekcji, jedynym sposobem, aby upewnić się, że testy wykonywane w środowisku produkcyjnym korzystają z tego samego systemu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="efd43-121">As described in the previous section, the only way to be sure you are testing what runs in production is to use the same database system.</span></span>
<span data-ttu-id="efd43-122">Na przykład jeśli wdrożona aplikacja korzysta z platformy SQL Azure, testowanie powinno również odbywać się w odniesieniu do usługi SQL Azure.</span><span class="sxs-lookup"><span data-stu-id="efd43-122">For example, if the deployed application uses SQL Azure, then testing should also be done against SQL Azure.</span></span>

<span data-ttu-id="efd43-123">Jednak każdy deweloper uruchamia testy na platformie SQL Azure, podczas gdy aktywnie pracujemy nad kodem, może być powolny i kosztowny.</span><span class="sxs-lookup"><span data-stu-id="efd43-123">However, having every developer run tests against SQL Azure while actively working on the code would be both slow and expensive.</span></span>
<span data-ttu-id="efd43-124">Ilustruje to główne działania w ramach tych metod: Kiedy jest to odpowiednie do odróżnienia od systemu produkcyjnej bazy danych, aby zwiększyć efektywność testu?</span><span class="sxs-lookup"><span data-stu-id="efd43-124">This illustrates the main trade-off involved throughout these approaches: when is it appropriate to deviate from the production database system so as to improve test efficiency?</span></span>

<span data-ttu-id="efd43-125">Na szczęście, w tym przypadku odpowiedź jest bardzo łatwa: Użyj lokalnego lub SQL Server lokalnie do testowania dla deweloperów.</span><span class="sxs-lookup"><span data-stu-id="efd43-125">Luckily, in this case the answer is quite easy: use local or on-premises SQL Server for developer testing.</span></span>
<span data-ttu-id="efd43-126">Usługi SQL Azure i SQL Server są bardzo podobne, dlatego testowanie w odniesieniu do SQL Server jest zwykle racjonalnie uzasadnione.</span><span class="sxs-lookup"><span data-stu-id="efd43-126">SQL Azure and SQL Server are extremely similar, so testing against SQL Server is usually a reasonable trade-off.</span></span>
<span data-ttu-id="efd43-127">W takim przypadku nadal można uruchomić testy na platformie SQL Azure przed przejściem do środowiska produkcyjnego.</span><span class="sxs-lookup"><span data-stu-id="efd43-127">That being said, it is still wise to run tests against SQL Azure itself before going into production.</span></span>
 
### <a name="localdb"></a><span data-ttu-id="efd43-128">LocalDB</span><span class="sxs-lookup"><span data-stu-id="efd43-128">LocalDB</span></span> 

<span data-ttu-id="efd43-129">Wszystkie główne systemy baz danych mają pewną postać "Developer Edition" na potrzeby testowania lokalnego.</span><span class="sxs-lookup"><span data-stu-id="efd43-129">All the major database systems have some form of "Developer Edition" for local testing.</span></span>
<span data-ttu-id="efd43-130">SQL Server ma również funkcję o nazwie [LocalDB](/sql/database-engine/configure-windows/sql-server-express-localdb?view=sql-server-ver15).</span><span class="sxs-lookup"><span data-stu-id="efd43-130">SQL Server also has a feature called [LocalDB](/sql/database-engine/configure-windows/sql-server-express-localdb?view=sql-server-ver15).</span></span>
<span data-ttu-id="efd43-131">Główną zaletą LocalDB jest to, że powoduje to wystąpienie bazy danych na żądanie.</span><span class="sxs-lookup"><span data-stu-id="efd43-131">The primary advantage of LocalDB is that it spins up the database instance on demand.</span></span>
<span data-ttu-id="efd43-132">Pozwala to uniknąć uruchamiania na maszynie usługi bazy danych, nawet jeśli nie są uruchomione testy.</span><span class="sxs-lookup"><span data-stu-id="efd43-132">This avoids having a database service running on your machine even when you're not running tests.</span></span>

<span data-ttu-id="efd43-133">LocalDB nie ma żadnych problemów:</span><span class="sxs-lookup"><span data-stu-id="efd43-133">LocalDB is not without its issues:</span></span>
* <span data-ttu-id="efd43-134">Nie obsługuje ona wszystkich elementów, które [SQL Server Developer Edition](/sql/sql-server/editions-and-components-of-sql-server-2016?view=sql-server-ver15) .</span><span class="sxs-lookup"><span data-stu-id="efd43-134">It doesn't support everything that [SQL Server Developer Edition](/sql/sql-server/editions-and-components-of-sql-server-2016?view=sql-server-ver15) does.</span></span>
* <span data-ttu-id="efd43-135">Nie jest on dostępny w systemie Linux.</span><span class="sxs-lookup"><span data-stu-id="efd43-135">It isn't available on Linux.</span></span>
* <span data-ttu-id="efd43-136">Może to spowodować opóźnienie pierwszego przebiegu testu, gdy usługa jest w stanie Spuninst.</span><span class="sxs-lookup"><span data-stu-id="efd43-136">It can cause lag on first test run as the service is spun up.</span></span>

<span data-ttu-id="efd43-137">Na komputerze deweloperskim nigdy nie udało Ci się znaleźć problemu związanego z uruchomioną usługą bazy danych.</span><span class="sxs-lookup"><span data-stu-id="efd43-137">Personally, I've never found it a problem having a database service running on my dev machine and I would generally recommend using Developer Edition instead.</span></span>
<span data-ttu-id="efd43-138">LocalDB mogą jednak być odpowiednie dla niektórych osób, szczególnie w mniej wydajnych maszynach deweloperskich.</span><span class="sxs-lookup"><span data-stu-id="efd43-138">However, LocalDB may be appropriate for some people, especially on less powerful dev machines.</span></span>

<span data-ttu-id="efd43-139">[Uruchomienie SQL Server](/sql/linux/quickstart-install-connect-docker?view=sql-server-ver15) (lub dowolnego innego systemu bazy danych) w kontenerze platformy Docker (lub podobny) jest innym sposobem, aby uniknąć uruchamiania systemu bazy danych bezpośrednio na komputerze deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="efd43-139">[Running SQL Server](/sql/linux/quickstart-install-connect-docker?view=sql-server-ver15) (or any other database system) in a Docker container (or similar) is another way to avoid running the database system directly on your development machine.</span></span>  

## <a name="approach-2-sqlite"></a><span data-ttu-id="efd43-140">Podejście 2: SQLite</span><span class="sxs-lookup"><span data-stu-id="efd43-140">Approach 2: SQLite</span></span>

<span data-ttu-id="efd43-141">EF Core testuje dostawcę SQL Server przede wszystkim przez uruchomienie go w odniesieniu do lokalnego wystąpienia SQL Server.</span><span class="sxs-lookup"><span data-stu-id="efd43-141">EF Core tests the SQL Server provider primarily by running it against a local SQL Server instance.</span></span>
<span data-ttu-id="efd43-142">Te testy uruchamiają dziesiątki tysięcy zapytań w kilka minut na szybkiej maszynie.</span><span class="sxs-lookup"><span data-stu-id="efd43-142">These tests run tens of thousands of queries in a couple of minutes on a fast machine.</span></span>
<span data-ttu-id="efd43-143">Ilustruje to, że korzystanie z rzeczywistego systemu bazy danych może być rozwiązaniem wydajnym.</span><span class="sxs-lookup"><span data-stu-id="efd43-143">This illustrates that using the real database system can be a performant solution.</span></span>
<span data-ttu-id="efd43-144">Jest to omówienia, który używa niektórych jaśniejszych baz danych, jest jedynym sposobem na szybkie uruchamianie testów.</span><span class="sxs-lookup"><span data-stu-id="efd43-144">It is a myth that using some lighter-weight database is the only way to run tests quickly.</span></span>

<span data-ttu-id="efd43-145">Co się dzieje, co zrobić, jeśli z jakiegoś powodu nie można uruchomić testów w pobliżu systemu produkcyjnej bazy danych?</span><span class="sxs-lookup"><span data-stu-id="efd43-145">That being said, what if for whatever reason you can't run tests against something close to your production database system?</span></span>
<span data-ttu-id="efd43-146">Następnym najlepszym wyborem jest użycie czegoś z podobną funkcjonalnością.</span><span class="sxs-lookup"><span data-stu-id="efd43-146">The next best choice is to use something with similar functionality.</span></span>
<span data-ttu-id="efd43-147">Zwykle oznacza to inną relacyjną bazę danych, dla której program [SQLite](https://sqlite.org/index.html) jest oczywisty.</span><span class="sxs-lookup"><span data-stu-id="efd43-147">This usually means another relational database, for which [SQLite](https://sqlite.org/index.html) is the obvious choice.</span></span>

<span data-ttu-id="efd43-148">Program SQLite jest dobrym rozwiązaniem, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="efd43-148">SQLite is a good choice because:</span></span>
* <span data-ttu-id="efd43-149">Jest ona uruchamiana w procesie z aplikacją i dlatego ma niewielkie obciążenie.</span><span class="sxs-lookup"><span data-stu-id="efd43-149">It runs in-process with your application and so has low overhead.</span></span>
* <span data-ttu-id="efd43-150">Używa on prostych, automatycznie tworzonych plików dla baz danych i dlatego nie wymaga zarządzania bazami danych.</span><span class="sxs-lookup"><span data-stu-id="efd43-150">It uses simple, automatically created files for databases, and so doesn't require database management.</span></span>
* <span data-ttu-id="efd43-151">Ma tryb w pamięci, który pozwala uniknąć nawet tworzenia pliku.</span><span class="sxs-lookup"><span data-stu-id="efd43-151">It has an in-memory mode that avoids even the file creation.</span></span>

<span data-ttu-id="efd43-152">Należy jednak pamiętać, że:</span><span class="sxs-lookup"><span data-stu-id="efd43-152">However, remember that:</span></span>
* <span data-ttu-id="efd43-153">Program SQLite inevitability nie obsługuje wszystkich elementów produkcyjnych systemu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="efd43-153">SQLite inevitability doesn't support everything that your production database system does.</span></span>
* <span data-ttu-id="efd43-154">Program SQLite będzie zachowywać się inaczej niż system produkcyjnej bazy danych dla niektórych zapytań.</span><span class="sxs-lookup"><span data-stu-id="efd43-154">SQLite will behave differently than your production database system for some queries.</span></span>

<span data-ttu-id="efd43-155">Dlatego jeśli w przypadku niektórych testów używasz oprogramowania SQLite, pamiętaj również o przetestowaniu względem rzeczywistego systemu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="efd43-155">So if you do use SQLite for some testing, make sure to also test against your real database system.</span></span>

<span data-ttu-id="efd43-156">Aby EF Core uzyskać szczegółowe wskazówki, zobacz [testowanie przy użyciu oprogramowania SQLite](xref:core/miscellaneous/testing/sqlite) .</span><span class="sxs-lookup"><span data-stu-id="efd43-156">See [Testing with SQLite](xref:core/miscellaneous/testing/sqlite) for EF Core specific guidance.</span></span> 

## <a name="approach-3-the-ef-core-in-memory-database"></a><span data-ttu-id="efd43-157">Podejście 3: EF Core bazy danych w pamięci</span><span class="sxs-lookup"><span data-stu-id="efd43-157">Approach 3: The EF Core in-memory database</span></span>

<span data-ttu-id="efd43-158">EF Core zawiera bazę danych w pamięci, która jest używana do wewnętrznego testowania EF Core samego siebie.</span><span class="sxs-lookup"><span data-stu-id="efd43-158">EF Core comes with an in-memory database that we use for internal testing of EF Core itself.</span></span>
<span data-ttu-id="efd43-159">Ta baza danych jest ogólnie **nieodpowiednia do testowania aplikacji, które używają EF Core**.</span><span class="sxs-lookup"><span data-stu-id="efd43-159">This database is in general **not suitable for testing applications that use EF Core**.</span></span> <span data-ttu-id="efd43-160">W szczególności:</span><span class="sxs-lookup"><span data-stu-id="efd43-160">Specifically:</span></span>

* <span data-ttu-id="efd43-161">Nie jest to relacyjna baza danych.</span><span class="sxs-lookup"><span data-stu-id="efd43-161">It is not a relational database.</span></span>
* <span data-ttu-id="efd43-162">Nie obsługuje transakcji.</span><span class="sxs-lookup"><span data-stu-id="efd43-162">It doesn't support transactions.</span></span>
* <span data-ttu-id="efd43-163">Nie można uruchamiać nieprzetworzonych zapytań SQL.</span><span class="sxs-lookup"><span data-stu-id="efd43-163">It cannot run raw SQL queries.</span></span>
* <span data-ttu-id="efd43-164">Nie jest zoptymalizowany pod kątem wydajności.</span><span class="sxs-lookup"><span data-stu-id="efd43-164">It is not optimized for performance.</span></span>

<span data-ttu-id="efd43-165">Żadna z tych elementów nie jest bardzo ważna podczas testowania EF Core wewnętrznych, ponieważ jest ona używana szczególnie w przypadku, gdy baza danych nie ma znaczenia dla testu.</span><span class="sxs-lookup"><span data-stu-id="efd43-165">None of this is very important when testing EF Core internals because we use it specifically where the database is irrelevant to the test.</span></span>
<span data-ttu-id="efd43-166">Z drugiej strony te rzeczy są bardzo ważne podczas testowania aplikacji, która używa EF Core.</span><span class="sxs-lookup"><span data-stu-id="efd43-166">On the other hand, these things tend to be very important when testing an application that uses EF Core.</span></span>

## <a name="unit-testing"></a><span data-ttu-id="efd43-167">Testowanie jednostek</span><span class="sxs-lookup"><span data-stu-id="efd43-167">Unit testing</span></span>

<span data-ttu-id="efd43-168">Należy rozważyć testowanie części logiki biznesowej, która może wymagać użycia niektórych danych z bazy danych, ale nie testowanie interakcji z bazami danych.</span><span class="sxs-lookup"><span data-stu-id="efd43-168">Consider testing a piece of business logic that might need to use some data from a database, but is not inherently testing the database interactions.</span></span>
<span data-ttu-id="efd43-169">Jedną z opcji jest użycie [testu podwójnego](https://en.wikipedia.org/wiki/Test_double) , takiego jak makieta lub fałszywe.</span><span class="sxs-lookup"><span data-stu-id="efd43-169">One option is to use a [test double](https://en.wikipedia.org/wiki/Test_double) such as a mock or fake.</span></span>

<span data-ttu-id="efd43-170">Używamy podwójnej próby do wewnętrznego testowania EF Core.</span><span class="sxs-lookup"><span data-stu-id="efd43-170">We use test doubles for internal testing of EF Core.</span></span>
<span data-ttu-id="efd43-171">Jednak nigdy nie próbujemy zasymulować kontekstu DbContext lub IQueryable.</span><span class="sxs-lookup"><span data-stu-id="efd43-171">However, we never try to mock DbContext or IQueryable.</span></span>
<span data-ttu-id="efd43-172">Takie działanie jest trudne, uciążliwe i delikatne.</span><span class="sxs-lookup"><span data-stu-id="efd43-172">Doing so is difficult, cumbersome, and fragile.</span></span>
<span data-ttu-id="efd43-173">**Nie rób tego.**</span><span class="sxs-lookup"><span data-stu-id="efd43-173">**Don't do it.**</span></span>

<span data-ttu-id="efd43-174">Zamiast tego używamy bazy danych EF w pamięci podczas testowania jednostkowego, który używa DbContext.</span><span class="sxs-lookup"><span data-stu-id="efd43-174">Instead we use the EF in-memory database when unit testing something that uses DbContext.</span></span>
<span data-ttu-id="efd43-175">W takim przypadku użycie EF bazy danych w pamięci jest odpowiednie, ponieważ test nie zależy od zachowania bazy danych.</span><span class="sxs-lookup"><span data-stu-id="efd43-175">In this case using the EF in-memory database is appropriate because the test is not dependent on database behavior.</span></span>
<span data-ttu-id="efd43-176">Nie wykonuj tej czynności, aby przetestować rzeczywiste zapytania lub aktualizacje bazy danych.</span><span class="sxs-lookup"><span data-stu-id="efd43-176">Just don't do this to test actual database queries or updates.</span></span>   

<span data-ttu-id="efd43-177">[Przykład testowania EF Core](xref:core/miscellaneous/testing/testing-sample) ilustruje testy korzystające z bazy danych EF w pamięci, a także SQL Server i oprogramowania SQLite.</span><span class="sxs-lookup"><span data-stu-id="efd43-177">The [EF Core testing sample](xref:core/miscellaneous/testing/testing-sample) demonstrates tests using the EF in-memory database, as well as SQL Server and SQLite.</span></span> 
