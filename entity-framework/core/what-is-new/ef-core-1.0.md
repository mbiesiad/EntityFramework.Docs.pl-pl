---
title: Co to jest nowa w programie EF 1.0 Core - EF Core
author: divega
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 20A25111-AEBE-4BC2-83A5-3F651952DF72
ms.technology: entity-framework-core
uid: core/what-is-new/ef-core-1.0
ms.openlocfilehash: e5b9e57a01ff302b1d7bd0fc5419aa5b8213865e
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2017
---
# <a name="features-included-in-ef-core-10"></a><span data-ttu-id="8fe81-102">Funkcje uwzględnione w wersji 1.0 Core EF</span><span class="sxs-lookup"><span data-stu-id="8fe81-102">Features included in EF Core 1.0</span></span>

## <a name="platforms"></a><span data-ttu-id="8fe81-103">Platformy</span><span class="sxs-lookup"><span data-stu-id="8fe81-103">Platforms</span></span>
### <a name="net-framework-451"></a><span data-ttu-id="8fe81-104">.NET Framework 4.5.1</span><span class="sxs-lookup"><span data-stu-id="8fe81-104">.NET Framework 4.5.1</span></span>
<span data-ttu-id="8fe81-105">Obejmuje konsoli WPF, WinForms, platformy ASP.NET 4, itp.</span><span class="sxs-lookup"><span data-stu-id="8fe81-105">Includes Console, WPF, WinForms, ASP.NET 4, etc.</span></span>
### <a name="net-standard-13"></a><span data-ttu-id="8fe81-106">.NET standard 1.3</span><span class="sxs-lookup"><span data-stu-id="8fe81-106">.NET Standard 1.3</span></span>
<span data-ttu-id="8fe81-107">W tym platformy ASP.NET Core przeznaczonych dla zarówno .NET Framework i .NET Core systemu Windows, OS x i Linux.</span><span class="sxs-lookup"><span data-stu-id="8fe81-107">Including ASP.NET Core targeting both .NET Framework and .NET Core on Windows, OSX, and Linux.</span></span>

## <a name="modelling"></a><span data-ttu-id="8fe81-108">Modelowanie</span><span class="sxs-lookup"><span data-stu-id="8fe81-108">Modelling</span></span>
### <a name="basic-modelling"></a><span data-ttu-id="8fe81-109">Podstawowe modelowania</span><span class="sxs-lookup"><span data-stu-id="8fe81-109">Basic modelling</span></span>
<span data-ttu-id="8fe81-110">Oparte na jednostki POCO z pobierania/ustawiania właściwości popularnych typów skalarnych (`int`, `string`itp.).</span><span class="sxs-lookup"><span data-stu-id="8fe81-110">Based on POCO entities with get/set properties of common scalar types (`int`, `string`, etc.).</span></span>
### <a name="relationships-and-navigation-properties"></a><span data-ttu-id="8fe81-111">Relacje i właściwości nawigacji</span><span class="sxs-lookup"><span data-stu-id="8fe81-111">Relationships and navigation properties</span></span>
<span data-ttu-id="8fe81-112">Można określić jeden do wielu i relacje jeden do zero lub jeden w modelu, na podstawie klucza obcego.</span><span class="sxs-lookup"><span data-stu-id="8fe81-112">One-to-many and One-to-zero-or-one relationships can be specified in the model based on a foreign key.</span></span> <span data-ttu-id="8fe81-113">Właściwości nawigacji prostego typu Kolekcja lub odwołanie mogą być skojarzone z tych relacji.</span><span class="sxs-lookup"><span data-stu-id="8fe81-113">Navigation properties of simple collection or reference types can be associated with these relationships.</span></span>
### <a name="built-in-conventions"></a><span data-ttu-id="8fe81-114">Konwencje wbudowane</span><span class="sxs-lookup"><span data-stu-id="8fe81-114">Built-in conventions</span></span>
<span data-ttu-id="8fe81-115">Te kompilacji początkowej model oparty na kształt klas jednostek.</span><span class="sxs-lookup"><span data-stu-id="8fe81-115">These build an initial model based on the shape of the entity classes.</span></span>
### <a name="fluent-api"></a><span data-ttu-id="8fe81-116">Interfejsu API Fluent</span><span class="sxs-lookup"><span data-stu-id="8fe81-116">Fluent API</span></span>
<span data-ttu-id="8fe81-117">Pozwala zastąpić `OnModelCreating` metody dla kontekstu można dodatkowo skonfigurować modelu, która została wykryta przez Konwencję.</span><span class="sxs-lookup"><span data-stu-id="8fe81-117">Allows you to override the `OnModelCreating` method on your context to further configure the model that was discovered by convention.</span></span>
### <a name="data-annotations"></a><span data-ttu-id="8fe81-118">Adnotacje danych</span><span class="sxs-lookup"><span data-stu-id="8fe81-118">Data annotations</span></span>
<span data-ttu-id="8fe81-119">Są to atrybuty, które mogą zostać dodane do właściwości klasy jednostki i będzie mieć wpływ modelu EF (tj. dodając EF wiedzieć, że wymagana jest właściwość let będzie [wymagane]).</span><span class="sxs-lookup"><span data-stu-id="8fe81-119">Are attributes that can be added to your entity classes/properties and will influence the EF model (i.e. adding [Required] will let EF know that a property is required).</span></span>
### <a name="relational-table-mapping"></a><span data-ttu-id="8fe81-120">Relacyjne mapowania tabeli</span><span class="sxs-lookup"><span data-stu-id="8fe81-120">Relational Table mapping</span></span>
<span data-ttu-id="8fe81-121">Umożliwia jednostek można mapować na tabele/kolumny.</span><span class="sxs-lookup"><span data-stu-id="8fe81-121">Allows entities to be mapped to tables/columns.</span></span>
### <a name="key-value-generation"></a><span data-ttu-id="8fe81-122">Generowanie wartości klucza</span><span class="sxs-lookup"><span data-stu-id="8fe81-122">Key value generation</span></span>
<span data-ttu-id="8fe81-123">W tym generacji po stronie klienta i bazy danych.</span><span class="sxs-lookup"><span data-stu-id="8fe81-123">Including client-side generation and database generation.</span></span>
### <a name="database-generated-values"></a><span data-ttu-id="8fe81-124">Baza danych wygenerowała wartości</span><span class="sxs-lookup"><span data-stu-id="8fe81-124">Database generated values</span></span>
<span data-ttu-id="8fe81-125">Zezwala na wartości, które ma być generowany przez bazę danych przy wstawianiu (wartości domyślne) lub aktualizacji (kolumn obliczanych).</span><span class="sxs-lookup"><span data-stu-id="8fe81-125">Allows for values to be generated by the database on insert (default values) or update (computed columns).</span></span>
### <a name="sequences-in-sql-server"></a><span data-ttu-id="8fe81-126">Sekwencje w programie SQL Server</span><span class="sxs-lookup"><span data-stu-id="8fe81-126">Sequences in SQL Server</span></span>
<span data-ttu-id="8fe81-127">Zezwala na obiekty sekwencji ma zostać zdefiniowana w modelu.</span><span class="sxs-lookup"><span data-stu-id="8fe81-127">Allows for sequence objects to be defined in the model.</span></span>
### <a name="unique-constraints"></a><span data-ttu-id="8fe81-128">Ograniczenia unikalne</span><span class="sxs-lookup"><span data-stu-id="8fe81-128">Unique constraints</span></span>
<span data-ttu-id="8fe81-129">Umożliwia definicji klucze alternatywne i możliwość definiowania relacji obiektu docelowego tego klucza.</span><span class="sxs-lookup"><span data-stu-id="8fe81-129">Allows for the definition of alternate keys and the ability to define relationships that target that key.</span></span>
### <a name="indexes"></a><span data-ttu-id="8fe81-130">Indeksy</span><span class="sxs-lookup"><span data-stu-id="8fe81-130">Indexes</span></span>
<span data-ttu-id="8fe81-131">Definiowanie indeksów w modelu automatycznie wprowadza indeksy w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="8fe81-131">Defining indexes in the model automatically introduces indexes in the database.</span></span> <span data-ttu-id="8fe81-132">Unikatowe indeksy są również obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="8fe81-132">Unique indexes are also supported.</span></span>
### <a name="shadow-state-properties"></a><span data-ttu-id="8fe81-133">Właściwości stanu w tle</span><span class="sxs-lookup"><span data-stu-id="8fe81-133">Shadow state properties</span></span>
<span data-ttu-id="8fe81-134">Umożliwia właściwości ma zostać zdefiniowana w modelu, które nie został zadeklarowany i nie są przechowywane w klasie .NET, ale można śledzić i zaktualizowane przez EF Core.</span><span class="sxs-lookup"><span data-stu-id="8fe81-134">Allows for properties to be defined in the model that are not declared and are not stored in the .NET class but can be tracked and updated by EF Core.</span></span> <span data-ttu-id="8fe81-135">Powszechnie używane dla właściwości klucza obcego, gdy udostępnianie w obiekt nie jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="8fe81-135">Commonly used for foreign key properties when exposing these in the object is not desired.</span></span>
### <a name="table-per-hierarchy-inheritance-pattern"></a><span data-ttu-id="8fe81-136">Wzorzec dziedziczenia tabeli na hierarchii</span><span class="sxs-lookup"><span data-stu-id="8fe81-136">Table-Per-Hierarchy inheritance pattern</span></span>
<span data-ttu-id="8fe81-137">Umożliwia jednostek w hierarchii dziedziczenia do zapisania pojedynczej tabeli, aby zidentyfikować ich typ jednostki dla danego rekordu w bazie danych przy użyciu kolumny rozróżniacza.</span><span class="sxs-lookup"><span data-stu-id="8fe81-137">Allows entities in an inheritance hierarchy to be saved to a single table using a discriminator column to identify they entity type for a given record in the database.</span></span>
### <a name="model-validation"></a><span data-ttu-id="8fe81-138">Weryfikacja modelu</span><span class="sxs-lookup"><span data-stu-id="8fe81-138">Model validation</span></span>
<span data-ttu-id="8fe81-139">Wykrywa nieprawidłowa wzorce w modelu i zawiera przydatne komunikaty.</span><span class="sxs-lookup"><span data-stu-id="8fe81-139">Detects invalid patterns in the model and provides helpful error messages.</span></span>

## <a name="change-tracking"></a><span data-ttu-id="8fe81-140">Śledzenie zmian</span><span class="sxs-lookup"><span data-stu-id="8fe81-140">Change tracking</span></span>
### <a name="snapshot-change-tracking"></a><span data-ttu-id="8fe81-141">Śledzenie zmian migawki</span><span class="sxs-lookup"><span data-stu-id="8fe81-141">Snapshot change tracking</span></span>
<span data-ttu-id="8fe81-142">Pozwalają na zmiany w obiektach, aby można było wykryć automatycznie na podstawie porównania ilości bieżący stan kopii (migawki) pierwotnego stanu.</span><span class="sxs-lookup"><span data-stu-id="8fe81-142">Allows changes in entities to be detected automatically by comparing current state against a copy (snapshot) of the original state.</span></span>
### <a name="notification-change-tracking"></a><span data-ttu-id="8fe81-143">Śledzenie zmian powiadomień</span><span class="sxs-lookup"><span data-stu-id="8fe81-143">Notification change tracking</span></span>
<span data-ttu-id="8fe81-144">Umożliwia jednostki do powiadamiania śledzący zmiany wartości właściwości są modyfikowane.</span><span class="sxs-lookup"><span data-stu-id="8fe81-144">Allows your entities to notify the change tracker when property values are modified.</span></span>
### <a name="accessing-tracked-state"></a><span data-ttu-id="8fe81-145">Uzyskiwanie dostępu do śledzonych stanu</span><span class="sxs-lookup"><span data-stu-id="8fe81-145">Accessing tracked state</span></span>
<span data-ttu-id="8fe81-146">Za pomocą `DbContext.Entry` i `DbContext.ChangeTracker`.</span><span class="sxs-lookup"><span data-stu-id="8fe81-146">Via `DbContext.Entry` and `DbContext.ChangeTracker`.</span></span>
### <a name="attaching-detached-entitiesgraphs"></a><span data-ttu-id="8fe81-147">Dołączanie jednostki odłączyć/wykresy</span><span class="sxs-lookup"><span data-stu-id="8fe81-147">Attaching detached entities/graphs</span></span>
<span data-ttu-id="8fe81-148">Nowe `DbContext.AttachGraph` interfejsu API pomaga ponownie dołączyć jednostek do kontekstu, aby zapisać nowych/zmodyfikowanych jednostek.</span><span class="sxs-lookup"><span data-stu-id="8fe81-148">The new `DbContext.AttachGraph` API helps re-attach entities to a context in order to save new/modified entities.</span></span>

## <a name="saving-data"></a><span data-ttu-id="8fe81-149">Zapisywanie danych</span><span class="sxs-lookup"><span data-stu-id="8fe81-149">Saving data</span></span>
### <a name="basic-save-functionality"></a><span data-ttu-id="8fe81-150">Podstawowa funkcja zapisywania</span><span class="sxs-lookup"><span data-stu-id="8fe81-150">Basic save functionality</span></span>
<span data-ttu-id="8fe81-151">Zezwala na zmiany można instancji jednostek utrwalenia w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="8fe81-151">Allows changes to entity instances to be persisted to the database.</span></span>
### <a name="optimistic-concurrency"></a><span data-ttu-id="8fe81-152">Optymistycznej współbieżności</span><span class="sxs-lookup"><span data-stu-id="8fe81-152">Optimistic Concurrency</span></span>
<span data-ttu-id="8fe81-153">Chroni przed nadpisaniem zmian wprowadzonych przez innego użytkownika, ponieważ pobrano dane z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="8fe81-153">Protects against overwriting changes made by another user since data was fetched from the database.</span></span>
### <a name="async-savechanges"></a><span data-ttu-id="8fe81-154">Asynchroniczne metody SaveChanges</span><span class="sxs-lookup"><span data-stu-id="8fe81-154">Async SaveChanges</span></span>
<span data-ttu-id="8fe81-155">Można zwolnić bieżący wątek przetwarzania innych żądań, gdy baza danych przetwarza polecenia wystawiony na podstawie `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="8fe81-155">Can free up the current thread to process other requests while the database processes the commands issued from `SaveChanges`.</span></span>
### <a name="database-transactions"></a><span data-ttu-id="8fe81-156">Transakcji bazy danych</span><span class="sxs-lookup"><span data-stu-id="8fe81-156">Database Transactions</span></span>
<span data-ttu-id="8fe81-157">Oznacza, że `SaveChanges` jest zawsze niepodzielne (to znaczy albo całkowicie próba powiedzie się lub nie jest zmieniana w bazie danych).</span><span class="sxs-lookup"><span data-stu-id="8fe81-157">Means that `SaveChanges` is always atomic (meaning it either completely succeeds, or no changes are made to the database).</span></span> <span data-ttu-id="8fe81-158">Istnieją również transakcji powiązanych interfejsów API umożliwiają udostępnianie transakcji między wystąpieniami kontekstu itp.</span><span class="sxs-lookup"><span data-stu-id="8fe81-158">There are also transaction related APIs to allow sharing transactions between context instances etc.</span></span>
### <a name="relational-batching-of-statements"></a><span data-ttu-id="8fe81-159">Relacyjne: Przetwarzanie wsadowe instrukcji</span><span class="sxs-lookup"><span data-stu-id="8fe81-159">Relational: Batching of statements</span></span>
<span data-ttu-id="8fe81-160">Zapewnia lepszą wydajność dzięki przetwarzanie wsadowe zapasową wielu poleceń INSERT/UPDATE/DELETE na jednym obie strony w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="8fe81-160">Provides better performance by batching up multiple INSERT/UPDATE/DELETE commands into a single roundtrip to the database.</span></span>

## <a name="query"></a><span data-ttu-id="8fe81-161">Zapytanie</span><span class="sxs-lookup"><span data-stu-id="8fe81-161">Query</span></span>
### <a name="basic-linq-support"></a><span data-ttu-id="8fe81-162">Podstawowa pomoc techniczna LINQ</span><span class="sxs-lookup"><span data-stu-id="8fe81-162">Basic LINQ support</span></span>
<span data-ttu-id="8fe81-163">Zapewnia możliwość używania LINQ do pobierania danych z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="8fe81-163">Provides the ability to use LINQ to retrieve data from the database.</span></span>
### <a name="mixed-clientserver-evaluation"></a><span data-ttu-id="8fe81-164">Ocena mieszanych klient serwer</span><span class="sxs-lookup"><span data-stu-id="8fe81-164">Mixed client/server evaluation</span></span>
<span data-ttu-id="8fe81-165">Umożliwia zapytania zawiera logikę, która nie można obliczyć w bazie danych i w związku z tym należy ocenić po dane są pobierane do pamięci.</span><span class="sxs-lookup"><span data-stu-id="8fe81-165">Enables queries to contain logic that cannot be evaluated in the database, and must therefore be evaluated after the data is retrieved into memory.</span></span>
### <a name="notracking"></a><span data-ttu-id="8fe81-166">NoTracking</span><span class="sxs-lookup"><span data-stu-id="8fe81-166">NoTracking</span></span>
<span data-ttu-id="8fe81-167">Zapytania umożliwia szybsze wykonywanie zapytania, gdy kontekst nie jest konieczne monitorowanie zmian w wystąpień jednostek (tj. wyniki są tylko do odczytu).</span><span class="sxs-lookup"><span data-stu-id="8fe81-167">Queries enables quicker query execution when the context does not need to monitor for changes to the entity instances (i.e. the results are read-only).</span></span>
### <a name="eager-loading"></a><span data-ttu-id="8fe81-168">Ładowanie wczesny</span><span class="sxs-lookup"><span data-stu-id="8fe81-168">Eager loading</span></span>
<span data-ttu-id="8fe81-169">Udostępnia `Include` i `ThenInclude` metodami do identyfikowania powiązane dane, które mają być pobierane, również podczas wykonywania zapytania.</span><span class="sxs-lookup"><span data-stu-id="8fe81-169">Provides the `Include` and `ThenInclude` methods to identify related data that should also be fetched when querying.</span></span>
### <a name="async-query"></a><span data-ttu-id="8fe81-170">Zapytania asynchronicznego</span><span class="sxs-lookup"><span data-stu-id="8fe81-170">Async query</span></span>
<span data-ttu-id="8fe81-171">Można zwolnić bieżącego wątku (i jego skojarzonych zasobów) do przetwarzania żądań innych podczas bazy danych przetwarza zapytanie.</span><span class="sxs-lookup"><span data-stu-id="8fe81-171">Can free up the current thread (and it's associated resources) to process other requests while the database processes the query.</span></span>
### <a name="raw-sql-queries"></a><span data-ttu-id="8fe81-172">Nieprzetworzona zapytania SQL</span><span class="sxs-lookup"><span data-stu-id="8fe81-172">Raw SQL queries</span></span>
<span data-ttu-id="8fe81-173">Udostępnia `DbSet.FromSql` metodę raw SQL zapytań można pobrać danych.</span><span class="sxs-lookup"><span data-stu-id="8fe81-173">Provides the `DbSet.FromSql` method to use raw SQL queries to fetch data.</span></span> <span data-ttu-id="8fe81-174">Te zapytania mogą być składane również przy użyciu LINQ.</span><span class="sxs-lookup"><span data-stu-id="8fe81-174">These queries can also be composed on using LINQ.</span></span>

## <a name="database-schema-management"></a><span data-ttu-id="8fe81-175">Zarządzanie schematem bazy danych</span><span class="sxs-lookup"><span data-stu-id="8fe81-175">Database schema management</span></span>       
### <a name="database-creationdeletion-apis"></a><span data-ttu-id="8fe81-176">Interfejsy API tworzenia/usuwania bazy danych</span><span class="sxs-lookup"><span data-stu-id="8fe81-176">Database creation/deletion APIs</span></span>
<span data-ttu-id="8fe81-177">Przede wszystkim są przeznaczone do testowania, w którym ma zostać szybkie tworzenie/usuwanie bazy danych bez korzystania z migracji.</span><span class="sxs-lookup"><span data-stu-id="8fe81-177">Are mostly designed for testing where you want to quickly create/delete the database without using migrations.</span></span>
### <a name="relational-database-migrations"></a><span data-ttu-id="8fe81-178">Migracje relacyjnej bazy danych</span><span class="sxs-lookup"><span data-stu-id="8fe81-178">Relational database migrations</span></span>
<span data-ttu-id="8fe81-179">Zezwalaj na schemacie relacyjnej bazy danych podlegać ewolucji nadgodzinach zmiana modelu.</span><span class="sxs-lookup"><span data-stu-id="8fe81-179">Allow a relational database schema to evolve overtime as your model changes.</span></span>
### <a name="reverse-engineer-from-database"></a><span data-ttu-id="8fe81-180">Odtwarzanie z bazy danych</span><span class="sxs-lookup"><span data-stu-id="8fe81-180">Reverse engineer from database</span></span>
<span data-ttu-id="8fe81-181">Rusztowania EF model na podstawie schematu relacyjnej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="8fe81-181">Scaffolds an EF model based on an existing relational database schema.</span></span>

## <a name="database-providers"></a><span data-ttu-id="8fe81-182">Dostawcy bazy danych</span><span class="sxs-lookup"><span data-stu-id="8fe81-182">Database providers</span></span>
### <a name="sql-server"></a><span data-ttu-id="8fe81-183">SQL Server</span><span class="sxs-lookup"><span data-stu-id="8fe81-183">SQL Server</span></span>
<span data-ttu-id="8fe81-184">Łączy się z programu Microsoft SQL Server 2008 lub nowszym.</span><span class="sxs-lookup"><span data-stu-id="8fe81-184">Connects to Microsoft SQL Server 2008 onwards.</span></span>
### <a name="sqlite"></a><span data-ttu-id="8fe81-185">SQLite</span><span class="sxs-lookup"><span data-stu-id="8fe81-185">SQLite</span></span>
<span data-ttu-id="8fe81-186">Nawiązuje połączenie z bazą danych SQLite 3.</span><span class="sxs-lookup"><span data-stu-id="8fe81-186">Connects to a SQLite 3 database.</span></span>
### <a name="in-memory"></a><span data-ttu-id="8fe81-187">W pamięci</span><span class="sxs-lookup"><span data-stu-id="8fe81-187">In-Memory</span></span>
<span data-ttu-id="8fe81-188">Umożliwia łatwe testowanie bez nawiązywania połączenia z bazą danych rzeczywistych.</span><span class="sxs-lookup"><span data-stu-id="8fe81-188">Is designed to easily enable testing without connecting to a real database.</span></span>
### <a name="3rd-party-providers"></a><span data-ttu-id="8fe81-189">3 dostawców</span><span class="sxs-lookup"><span data-stu-id="8fe81-189">3rd party providers</span></span>
<span data-ttu-id="8fe81-190">Wielu dostawców są dostępne dla innych baz danych.</span><span class="sxs-lookup"><span data-stu-id="8fe81-190">Several providers are available for other database engines.</span></span> <span data-ttu-id="8fe81-191">Zobacz [dostawcy bazy danych](../providers/index.md) pełną listę.</span><span class="sxs-lookup"><span data-stu-id="8fe81-191">See [Database Providers](../providers/index.md) for a complete list.</span></span>