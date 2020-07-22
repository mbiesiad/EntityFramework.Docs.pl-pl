---
title: Co nowego w EF Core 5,0
description: Omówienie nowych funkcji w EF Core 5,0
author: ajcvickers
ms.date: 07/20/2020
uid: core/what-is-new/ef-core-5.0/whatsnew
ms.openlocfilehash: d42b2811d07516e9febedbc51fcb206000d38371
ms.sourcegitcommit: 51148929e3889c48227d96c95c4e310d53a3d2c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/21/2020
ms.locfileid: "86873386"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="f4ba7-103">Co nowego w EF Core 5,0</span><span class="sxs-lookup"><span data-stu-id="f4ba7-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="f4ba7-104">EF Core 5,0 jest obecnie w trakcie opracowywania.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-104">EF Core 5.0 is currently in development.</span></span> <span data-ttu-id="f4ba7-105">Ta strona będzie zawierać przegląd interesujących zmian wprowadzonych w każdej wersji zapoznawczej.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-105">This page will contain an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="f4ba7-106">Ta strona nie duplikuje [planu dla EF Core 5,0](xref:core/what-is-new/ef-core-5.0/plan).</span><span class="sxs-lookup"><span data-stu-id="f4ba7-106">This page does not duplicate the [plan for EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan).</span></span> <span data-ttu-id="f4ba7-107">Plan opisuje ogólne motywy dla EF Core 5,0, w tym wszystko, co planujemy uwzględnić przed wysyłką ostatecznej wersji.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

<span data-ttu-id="f4ba7-108">Będziemy dodawać linki z tego miejsca do oficjalnej dokumentacji w trakcie jej publikacji.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-108">We will add links from here to the official documentation as it is published.</span></span>

## <a name="preview-7"></a><span data-ttu-id="f4ba7-109">Wersja zapoznawcza 7</span><span class="sxs-lookup"><span data-stu-id="f4ba7-109">Preview 7</span></span>

### <a name="dbcontextfactory"></a><span data-ttu-id="f4ba7-110">DbContextFactory</span><span class="sxs-lookup"><span data-stu-id="f4ba7-110">DbContextFactory</span></span>

<span data-ttu-id="f4ba7-111">EF Core 5,0 wprowadza `AddDbContextFactory` i `AddPooledDbContextFactory` rejestruje fabrykę do tworzenia wystąpień DbContext w kontenerze iniekcji zależności aplikacji (D.I.).</span><span class="sxs-lookup"><span data-stu-id="f4ba7-111">EF Core 5.0 introduces `AddDbContextFactory` and `AddPooledDbContextFactory` to register a factory for creating DbContext instances in the application's dependency injection (D.I.) container.</span></span> <span data-ttu-id="f4ba7-112">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-112">For example:</span></span>

```csharp
services.AddDbContextFactory<SomeDbContext>(b =>
    b.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
```

<span data-ttu-id="f4ba7-113">Usługi aplikacji, takie jak kontrolery ASP.NET Core, mogą następnie zależeć od `IDbContextFactory<TContext>` konstruktora usług.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-113">Application services such as ASP.NET Core controllers can then depend on `IDbContextFactory<TContext>` in the service constructor.</span></span> <span data-ttu-id="f4ba7-114">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-114">For example:</span></span>

```csharp
public class MyController
{
    private readonly IDbContextFactory<SomeDbContext> _contextFactory;

    public MyController(IDbContextFactory<SomeDbContext> contextFactory)
    {
        _contextFactory = contextFactory;
    }
}
```

<span data-ttu-id="f4ba7-115">Wystąpienia DbContext można następnie utworzyć i użyć w razie konieczności.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-115">DbContext instances can then be created and used as needed.</span></span> <span data-ttu-id="f4ba7-116">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-116">For example:</span></span>

```csharp
public void DoSomehing()
{
    using (var context = _contextFactory.CreateDbContext())
    {
        // ...            
    }
}
```

<span data-ttu-id="f4ba7-117">Należy zauważyć, że wystąpienia DbContext utworzone w ten sposób _nie_ są zarządzane przez dostawcę usług aplikacji i dlatego muszą zostać zlikwidowane przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-117">Note that the DbContext instances created in this way are _not_ managed by the application's service provider and therefore must be disposed by the application.</span></span> <span data-ttu-id="f4ba7-118">To oddzielenie jest bardzo przydatne w przypadku aplikacji Blazor, gdzie using `IDbContextFactory` jest zalecane, ale mogą być również przydatne w innych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-118">This decoupling is very useful for Blazor applications, where using `IDbContextFactory` is recommended, but may also be useful in other scenarios.</span></span>

<span data-ttu-id="f4ba7-119">Wystąpienia DbContext mogą być buforowane przez wywołanie `AddPooledDbContextFactory` .</span><span class="sxs-lookup"><span data-stu-id="f4ba7-119">DbContext instances can be pooled by calling `AddPooledDbContextFactory`.</span></span> <span data-ttu-id="f4ba7-120">Ta Pula działa tak samo jak w przypadku programu `AddDbContextPool` , a także ma takie same ograniczenia.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-120">This pooling works the same way as for `AddDbContextPool`, and also has the same limitations.</span></span>

<span data-ttu-id="f4ba7-121">Dokumentacja jest śledzona przez [#2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523)problemu.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-121">Documentation is tracked by issue [#2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523).</span></span>

### <a name="reset-dbcontext-state"></a><span data-ttu-id="f4ba7-122">Zresetuj stan DbContext</span><span class="sxs-lookup"><span data-stu-id="f4ba7-122">Reset DbContext state</span></span>

<span data-ttu-id="f4ba7-123">EF Core 5,0 wprowadza `ChangeTracker.Clear()` , który czyści DbContext wszystkich śledzonych jednostek.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-123">EF Core 5.0 introduces `ChangeTracker.Clear()` which clears the DbContext of all tracked entities.</span></span> <span data-ttu-id="f4ba7-124">Zwykle nie jest to zalecane, gdy jest stosowane najlepsze rozwiązanie do tworzenia nowego, krótkoterminowego wystąpienia kontekstu dla każdej jednostki pracy.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-124">This should usually not be needed when using the best practice of creating a new, short-lived context instance for each unit-of-work.</span></span> <span data-ttu-id="f4ba7-125">Jeśli jednak istnieje potrzeba zresetowania stanu wystąpienia DbContext, użycie nowej `Clear()` metody jest bardziej wydajne i niezawodne niż w przypadku odłączania wszystkich jednostek.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-125">However, if there is a need to reset the state of a DbContext instance, then using the new `Clear()` method is more performant and robust than mass-detaching all entities.</span></span>  

<span data-ttu-id="f4ba7-126">Dokumentacja jest śledzona przez [#2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524)problemu.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-126">Documentation is tracked by issue [#2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524).</span></span>

### <a name="new-pattern-for-store-generated-defaults"></a><span data-ttu-id="f4ba7-127">Nowy wzorzec dla ustawień domyślnych generowanych przez magazyn</span><span class="sxs-lookup"><span data-stu-id="f4ba7-127">New pattern for store-generated defaults</span></span>

<span data-ttu-id="f4ba7-128">EF Core pozwala ustawić jawną wartość dla kolumny, która może również mieć ograniczenie wartości domyślnej.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-128">EF Core allows an explicit value to be set for a column that may also have default value constraint.</span></span> <span data-ttu-id="f4ba7-129">EF Core używa domyślnego środowiska CLR typu właściwości typu jako wskaźnikowego dla tego elementu. Jeśli wartość nie jest wartością domyślną środowiska CLR, zostanie ona wstawiona, w przeciwnym razie używana jest domyślna baza danych.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-129">EF Core uses the CLR default of type property type as a sentinel for this; if the value is not the CLR default, then it is inserted, otherwise the database default is used.</span></span>

<span data-ttu-id="f4ba7-130">Powoduje to utworzenie problemów dotyczących typów, w przypadku których wartość domyślna środowiska CLR nie jest dobrym wskaźnikiem, w szczególności `bool` właściwości.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-130">This creates problems for types where the CLR default is not a good sentinel--most notably, `bool` properties.</span></span> <span data-ttu-id="f4ba7-131">EF Core 5,0 umożliwia teraz pole zapasowe dopuszczające wartość null w przypadku takich przypadków.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-131">EF Core 5.0 now allows the backing field to be nullable for cases like this.</span></span> <span data-ttu-id="f4ba7-132">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-132">For example:</span></span>

```csharp
public class Blog
{
    private bool? _isValid;

    public bool IsValid
    {
        get => _isValid ?? false;
        set => _isValid = value;
    }
}
```

<span data-ttu-id="f4ba7-133">Zwróć uwagę, że pole zapasowe dopuszcza wartość null, ale publiczna właściwość nie jest.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-133">Note that the backing field is nullable, but the publicly exposed property is not.</span></span> <span data-ttu-id="f4ba7-134">Pozwala to na to, aby wartość wskaźnikowa była `null` bez wpływu na publiczną powierzchnię typu jednostki.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-134">This allows the sentinel value to be `null` without impacting the public surface of the entity type.</span></span> <span data-ttu-id="f4ba7-135">W takim przypadku, jeśli `IsValid` nigdy nie jest ustawiona, zostanie użyta domyślna baza danych, ponieważ pole zapasowe pozostanie puste.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-135">In this case, if the `IsValid` is never set, then the database default will be used since the backing field remains null.</span></span> <span data-ttu-id="f4ba7-136">Jeśli `true` lub `false` są ustawione, ta wartość jest jawnie zapisywana w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-136">If either `true` or `false` are set, then this value is saved explicitly to the database.</span></span>

<span data-ttu-id="f4ba7-137">Dokumentacja jest śledzona przez [#2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525)problemu.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-137">Documentation is tracked by issue [#2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="f4ba7-138">Klucze partycji Cosmos</span><span class="sxs-lookup"><span data-stu-id="f4ba7-138">Cosmos partition keys</span></span>

<span data-ttu-id="f4ba7-139">EF Core umożliwia dołączenie klucza partycji Cosmos do modelu EF.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-139">EF Core allows the Cosmos partition key is included in the EF model.</span></span> <span data-ttu-id="f4ba7-140">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-140">For example:</span></span>

```csharp
modelBuilder.Entity<Customer>().HasPartitionKey(b => b.AlternateKey)
```

<span data-ttu-id="f4ba7-141">Począwszy od wersji zapoznawczej 7, klucz partycji jest uwzględniany w kluczu PK typu jednostki i służy do zwiększenia wydajności w niektórych zapytaniach.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-141">Starting with preview 7, the partition key is included in the entity type's PK and is used to improved performance in some queries.</span></span>

<span data-ttu-id="f4ba7-142">Dokumentacja jest śledzona przez [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471)problemu.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-142">Documentation is tracked by issue [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span></span>

### <a name="cosmos-configuration"></a><span data-ttu-id="f4ba7-143">Konfiguracja Cosmos</span><span class="sxs-lookup"><span data-stu-id="f4ba7-143">Cosmos configuration</span></span>

<span data-ttu-id="f4ba7-144">EF Core 5,0 zwiększa konfigurację połączeń Cosmos i Cosmos.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-144">EF Core 5.0 improves configuration of Cosmos and Cosmos connections.</span></span>

<span data-ttu-id="f4ba7-145">Wcześniej EF Core wymagać określenia punktu końcowego i klucza, aby można było jawnie określić połączenie z bazą danych Cosmos.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-145">Previously, EF Core required the end-point and key to be specified explicitly when connecting to a Cosmos database.</span></span> <span data-ttu-id="f4ba7-146">EF Core 5,0 pozwala zamiast tego użyć parametrów połączenia.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-146">EF Core 5.0 allows use of a connection string instead.</span></span> <span data-ttu-id="f4ba7-147">Ponadto EF Core 5,0 umożliwia jawne ustawienie wystąpienia obiektu WebProxy.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-147">In addition, EF Core 5.0 allows the WebProxy instance to be explicitly set.</span></span> <span data-ttu-id="f4ba7-148">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-148">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.WebProxy(myProxyInstance);
            });
```

<span data-ttu-id="f4ba7-149">Można również skonfigurować wiele innych wartości limitów czasu, limitów itp.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-149">Many other timeout values, limits, etc. can now also be configured.</span></span> <span data-ttu-id="f4ba7-150">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-150">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.LimitToEndpoint();
                cosmosOptionsBuilder.RequestTimeout(requestTimeout);
                cosmosOptionsBuilder.OpenTcpConnectionTimeout(timeout);
                cosmosOptionsBuilder.IdleTcpConnectionTimeout(timeout);
                cosmosOptionsBuilder.GatewayModeMaxConnectionLimit(connectionLimit);
                cosmosOptionsBuilder.MaxTcpConnectionsPerEndpoint(connectionLimit);
                cosmosOptionsBuilder.MaxRequestsPerTcpConnection(requestLimit);
            });
```

<span data-ttu-id="f4ba7-151">Na koniec domyślnym trybem połączenia jest teraz `ConnectionMode.Gateway` , który jest zazwyczaj bardziej zgodny.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-151">Finally, the default connection mode is now `ConnectionMode.Gateway`, which is generally more compatible.</span></span>

<span data-ttu-id="f4ba7-152">Dokumentacja jest śledzona przez [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471)problemu.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-152">Documentation is tracked by issue [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span></span>

### <a name="scaffold-dbcontext-now-singularizes"></a><span data-ttu-id="f4ba7-153">Szkielet — DbContext teraz singularizes</span><span class="sxs-lookup"><span data-stu-id="f4ba7-153">Scaffold-DbContext now singularizes</span></span>

<span data-ttu-id="f4ba7-154">Wcześniej podczas tworzenia szkieletu DbContext z istniejącej bazy danych EF Core utworzy nazwy typu jednostki, które pasują do nazw tabel w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-154">Previously when scaffolding a DbContext from an existing database, EF Core will create entity type names that match the table names in the database.</span></span> <span data-ttu-id="f4ba7-155">Na przykład tabele `People` i `Addresses` w wyniku typów jednostek o nazwach `People` i `Addresses` .</span><span class="sxs-lookup"><span data-stu-id="f4ba7-155">For example, tables `People` and `Addresses` resulted in entity types named `People` and `Addresses`.</span></span>

<span data-ttu-id="f4ba7-156">W poprzednich wersjach to zachowanie zostało konfigurowalne za pomocą rejestracji usługi pluralizacja.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-156">In previous releases, this behavior was configurable through registration of a pluralization service.</span></span> <span data-ttu-id="f4ba7-157">Teraz w EF Core 5,0 pakiet [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) jest używany jako domyślna usługa pluralizacja.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-157">Now in EF Core 5.0, the [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) package is used as a default pluralization service.</span></span> <span data-ttu-id="f4ba7-158">Oznacza to, że tabele są teraz odtwarzane `People` `Addresses` w typach jednostek o nazwach `Person` i `Address` .</span><span class="sxs-lookup"><span data-stu-id="f4ba7-158">This means tables `People` and `Addresses` will now be reverse engineered to entity types named `Person` and `Address`.</span></span>

### <a name="savepoints"></a><span data-ttu-id="f4ba7-159">Punktów zapisu</span><span class="sxs-lookup"><span data-stu-id="f4ba7-159">Savepoints</span></span>

<span data-ttu-id="f4ba7-160">EF Core teraz obsługuje [punktów zapisu](/SQL/t-sql/language-elements/save-transaction-transact-sql?view=sql-server-ver15#remarks) , aby uzyskać większą kontrolę nad transakcjami, które wykonują wiele operacji.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-160">EF Core now supports [savepoints](/SQL/t-sql/language-elements/save-transaction-transact-sql?view=sql-server-ver15#remarks) for greater control over transactions that execute multiple operations.</span></span>

<span data-ttu-id="f4ba7-161">Punktów zapisu można ręcznie utworzyć, zwolnić i wycofać.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-161">Savepoints can be manually created, released, and rolled back.</span></span> <span data-ttu-id="f4ba7-162">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-162">For example:</span></span>

```csharp
context.Database.CreateSavepoint("MySavePoint"); 
```

<span data-ttu-id="f4ba7-163">Ponadto EF Core będzie teraz przywracane do ostatniego punktu zapisu, gdy wykonywanie `SaveChanges` nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-163">In addition, EF Core will now roll back to the last savepoint when executing `SaveChanges` fails.</span></span> <span data-ttu-id="f4ba7-164">Dzięki temu metody SaveChanges można ponowić próbę bez ponawiania próby całej transakcji.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-164">This allows SaveChanges to be re-tried without re-trying the entire transaction.</span></span>

<span data-ttu-id="f4ba7-165">Dokumentacja jest śledzona przez [#2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429)problemu.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-165">Documentation is tracked by issue [#2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429).</span></span>

## <a name="preview-6"></a><span data-ttu-id="f4ba7-166">Wersja zapoznawcza 6</span><span class="sxs-lookup"><span data-stu-id="f4ba7-166">Preview 6</span></span>

### <a name="split-queries-for-related-collections"></a><span data-ttu-id="f4ba7-167">Podziel zapytania dla powiązanych kolekcji</span><span class="sxs-lookup"><span data-stu-id="f4ba7-167">Split queries for related collections</span></span>

<span data-ttu-id="f4ba7-168">Począwszy od EF Core 3,0, EF Core zawsze generuje pojedyncze zapytanie SQL dla każdej kwerendy LINQ.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-168">Starting with EF Core 3.0, EF Core always generates a single SQL query for each LINQ query.</span></span> <span data-ttu-id="f4ba7-169">Zapewnia to spójność danych zwracanych w ramach ograniczeń trybu transakcji w użyciu.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-169">This ensures consistency of the data returned within the constraints of the transaction mode in use.</span></span> <span data-ttu-id="f4ba7-170">Jednak może to potrwać bardzo wolno, gdy zapytanie używa `Include` lub projekcji, aby przywrócić wiele powiązanych kolekcji.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-170">However, this can become very slow when the query uses `Include` or a projection to bring back multiple related collections.</span></span>

<span data-ttu-id="f4ba7-171">EF Core 5,0 umożliwia teraz pojedyncze zapytanie LINQ, w tym powiązane kolekcje, które mają być podzielone na wiele zapytań SQL.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-171">EF Core 5.0 now allows a single LINQ query including related collections to be split into multiple SQL queries.</span></span> <span data-ttu-id="f4ba7-172">Może to znacząco poprawić wydajność, ale może spowodować niespójność wyników zwróconych w przypadku zmiany danych między dwoma zapytaniami.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-172">This can significantly improve performance, but can result in inconsistency in the results returned if the data changes between the two queries.</span></span> <span data-ttu-id="f4ba7-173">Można użyć transakcji do serializacji lub migawek, aby ograniczyć to i zapewnić spójność z zapytaniami z podziałem, ale może to spowodować inne koszty wydajności i różnice w zachowaniu.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-173">Serializable or snapshot transactions can be used to mitigate this and achieve consistency with split queries, but that may bring other performance costs and behavioral difference.</span></span>

#### <a name="split-queries-with-include"></a><span data-ttu-id="f4ba7-174">Rozdziel zapytania za pomocą dyrektywy include</span><span class="sxs-lookup"><span data-stu-id="f4ba7-174">Split queries with Include</span></span>

<span data-ttu-id="f4ba7-175">Rozważmy na przykład zapytanie, które pobiera dwa poziomy pokrewnych kolekcji przy użyciu `Include` :</span><span class="sxs-lookup"><span data-stu-id="f4ba7-175">For example, consider a query that pulls in two levels of related collections using `Include`:</span></span>

```CSharp
var artists = context.Artists
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="f4ba7-176">Domyślnie EF Core będzie generować następujące SQL podczas korzystania z dostawcy oprogramowania SQLite:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-176">By default, EF Core will generate the following SQL when using the SQLite provider:</span></span>

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

<span data-ttu-id="f4ba7-177">`AsSplitQuery`Aby zmienić to zachowanie, można użyć nowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-177">The new `AsSplitQuery` API can be used to change this behavior.</span></span> <span data-ttu-id="f4ba7-178">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-178">For example:</span></span>

```CSharp
var artists = context.Artists
    .AsSplitQuery()
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="f4ba7-179">AsSplitQuery jest dostępny dla wszystkich dostawców relacyjnych baz danych i można go używać w dowolnym miejscu zapytania, podobnie jak AsNoTracking.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-179">AsSplitQuery is available for all relational database providers and can be used anywhere in the query, just like AsNoTracking.</span></span> <span data-ttu-id="f4ba7-180">EF Core będzie teraz generować następujące trzy zapytania SQL:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-180">EF Core will now generate the following three SQL queries:</span></span>

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

<span data-ttu-id="f4ba7-181">Wszystkie operacje w katalogu głównym zapytania są obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-181">All operations on the query root are supported.</span></span> <span data-ttu-id="f4ba7-182">Obejmuje to operacje OrderBy/Skip/Take, join, FirstOrDefault i podobne pojedyncze wyniki wybierające operacje.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-182">This includes OrderBy/Skip/Take, Join operations, FirstOrDefault and similar single result selecting operations.</span></span>

<span data-ttu-id="f4ba7-183">Zwróć uwagę, że filtrowane elementy include z OrderBy/Skip/Take nie są obsługiwane w wersji zapoznawczej 6, ale są dostępne w codziennych kompilacjach i zostaną uwzględnione w wersji zapoznawczej 7.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-183">Note that filtered Includes with OrderBy/Skip/Take are not supported in preview 6, but are available in the daily builds and will be included in preview 7.</span></span>

#### <a name="split-queries-with-collection-projections"></a><span data-ttu-id="f4ba7-184">Podziel zapytania z projekcjami kolekcji</span><span class="sxs-lookup"><span data-stu-id="f4ba7-184">Split queries with collection projections</span></span>

<span data-ttu-id="f4ba7-185">`AsSplitQuery`można go również użyć, gdy kolekcje są ładowane do projekcji.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-185">`AsSplitQuery` can also be used when collections are loaded in projections.</span></span> <span data-ttu-id="f4ba7-186">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-186">For example:</span></span>

```CSharp
context.Artists
    .AsSplitQuery()
    .Select(e => new
    {
        Artist = e,
        Albums = e.Albums,
    }).ToList();
```

<span data-ttu-id="f4ba7-187">Ta kwerenda LINQ generuje następujące dwa zapytania SQL, gdy jest używany dostawca oprogramowania SQLite:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-187">This LINQ query generates the following two SQL queries when using the SQLite provider:</span></span>

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id"
```

<span data-ttu-id="f4ba7-188">Należy zauważyć, że obsługiwana jest tylko materializację kolekcji.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-188">Note that only materialization of the collection is supported.</span></span> <span data-ttu-id="f4ba7-189">Wszelkie kompozycje po `e.Albums` powyższym przypadku nie spowodują przetworzenia zapytania podzielonego.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-189">Any composition after `e.Albums` in above case won't result in a split query.</span></span> <span data-ttu-id="f4ba7-190">Ulepszenia w tym obszarze są śledzone przez [#21234](https://github.com/dotnet/efcore/issues/21234).</span><span class="sxs-lookup"><span data-stu-id="f4ba7-190">Improvements in this area are tracked by [#21234](https://github.com/dotnet/efcore/issues/21234).</span></span>

### <a name="indexattribute"></a><span data-ttu-id="f4ba7-191">Indexattribute</span><span class="sxs-lookup"><span data-stu-id="f4ba7-191">IndexAttribute</span></span>

<span data-ttu-id="f4ba7-192">Nowy element Indexattribute może być umieszczony w typie jednostki, aby określić indeks dla pojedynczej kolumny.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-192">The new IndexAttribute can be placed on an entity type to specify an index for a single column.</span></span> <span data-ttu-id="f4ba7-193">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-193">For example:</span></span>

```CSharp
[Index(nameof(FullName), IsUnique = true)]
public class User
{
    public int Id { get; set; }
    
    [MaxLength(128)]
    public string FullName { get; set; }
}
```

<span data-ttu-id="f4ba7-194">W przypadku SQL Server migracja będzie generować następujące SQL:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-194">For SQL Server, Migrations will then generate the following SQL:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FullName]
    ON [Users] ([FullName])
    WHERE [FullName] IS NOT NULL;
```

<span data-ttu-id="f4ba7-195">W celu określenia indeksu obejmującego wiele kolumn można także użyć elementu indexattribute.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-195">IndexAttribute can also be used to specify an index spanning multiple columns.</span></span> <span data-ttu-id="f4ba7-196">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-196">For example:</span></span>

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

<span data-ttu-id="f4ba7-197">W przypadku SQL Server powoduje to:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-197">For SQL Server, this results in:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FirstName_LastName]
    ON [Users] ([FirstName], [LastName])
    WHERE [FirstName] IS NOT NULL AND [LastName] IS NOT NULL;
```

<span data-ttu-id="f4ba7-198">Dokumentacja jest śledzona przez [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407)problemu.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-198">Documentation is tracked by issue [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span></span>

### <a name="improved-query-translation-exceptions"></a><span data-ttu-id="f4ba7-199">Ulepszone wyjątki translacji zapytań</span><span class="sxs-lookup"><span data-stu-id="f4ba7-199">Improved query translation exceptions</span></span>

<span data-ttu-id="f4ba7-200">Kontynuujemy ulepszanie komunikatów o wyjątkach generowanych w przypadku niepowodzenia tłumaczenia zapytań.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-200">We are continuing to improve the exception messages generated when query translation fails.</span></span> <span data-ttu-id="f4ba7-201">Na przykład to zapytanie używa niezamapowanej właściwości `IsSigned` :</span><span class="sxs-lookup"><span data-stu-id="f4ba7-201">For example, this query uses the unmapped property `IsSigned`:</span></span>

```CSharp
var artists = context.Artists.Where(e => e.IsSigned).ToList();
```

<span data-ttu-id="f4ba7-202">EF Core zgłosi następujący wyjątek wskazujący, że tłumaczenie nie powiodło się, ponieważ `IsSigned` nie jest zamapowany:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-202">EF Core will throw the following exception indicating that translation failed because `IsSigned` is not mapped:</span></span>

> <span data-ttu-id="f4ba7-203">Nieobsługiwany wyjątek.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-203">Unhandled exception.</span></span> <span data-ttu-id="f4ba7-204">System. InvalidOperationException: wyrażenie LINQ "Nieogólnymi <Artist> (). Gdzie nie można przetłumaczyć (a => a. IsSigned) '.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-204">System.InvalidOperationException: The LINQ expression 'DbSet<Artist>() .Where(a => a.IsSigned)' could not be translated.</span></span> <span data-ttu-id="f4ba7-205">Informacje dodatkowe: Tłumaczenie elementu członkowskiego "IsSigned" w typie jednostki "artyści" nie powiodło się.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-205">Additional information: Translation of member 'IsSigned' on entity type 'Artist' failed.</span></span> <span data-ttu-id="f4ba7-206">Prawdopodobnie określony element członkowski nie jest zamapowany.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-206">Possibly the specified member is not mapped.</span></span> <span data-ttu-id="f4ba7-207">Ponownie Napisz zapytanie w formularzu, który można przetłumaczyć, lub Przełącz się na ocenę klienta jawnie, wstawiając wywołanie do AsEnumerable (), AsAsyncEnumerable (), ToList — () lub ToListAsync ().</span><span class="sxs-lookup"><span data-stu-id="f4ba7-207">Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().</span></span> <span data-ttu-id="f4ba7-208">Aby uzyskać więcej informacji, zobacz https://go.microsoft.com/fwlink/?linkid=2101038.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-208">See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.</span></span>

<span data-ttu-id="f4ba7-209">Podobnie w przypadku próby przetłumaczenia porównania ciągów z semantyką zależną od kultury są teraz generowane lepsze komunikaty o wyjątkach.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-209">Similarly, better exception messages are now generated when attempting to translate string comparisons with culture-dependent semantics.</span></span> <span data-ttu-id="f4ba7-210">Na przykład ta kwerenda próbuje użyć `StringComparison.CurrentCulture` :</span><span class="sxs-lookup"><span data-stu-id="f4ba7-210">For example, this query attempts to use `StringComparison.CurrentCulture`:</span></span>

```CSharp
var artists = context.Artists
    .Where(e => e.Name.Equals("The Unicorns", StringComparison.CurrentCulture))
    .ToList();
```

<span data-ttu-id="f4ba7-211">EF Core będzie teraz zgłosić następujący wyjątek:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-211">EF Core will now throw the following exception:</span></span>

> <span data-ttu-id="f4ba7-212">Nieobsługiwany wyjątek.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-212">Unhandled exception.</span></span> <span data-ttu-id="f4ba7-213">System. InvalidOperationException: wyrażenie LINQ "Nieogólnymi <Artist> (). Gdzie (a => a. Name. Equals (wartość: "różowo", comparisonType: CurrentCulture)) "nie można przetłumaczyć.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-213">System.InvalidOperationException: The LINQ expression 'DbSet<Artist>() .Where(a => a.Name.Equals( value: "The Unicorns", comparisonType: CurrentCulture))' could not be translated.</span></span> <span data-ttu-id="f4ba7-214">Informacje dodatkowe: tłumaczenie ciągu. Metoda Equals, która pobiera argument "StringComparison", nie jest obsługiwana.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-214">Additional information: Translation of 'string.Equals' method which takes 'StringComparison' argument is not supported.</span></span> <span data-ttu-id="f4ba7-215">Aby uzyskać więcej informacji, zobacz https://go.microsoft.com/fwlink/?linkid=2129535.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-215">See https://go.microsoft.com/fwlink/?linkid=2129535 for more information.</span></span> <span data-ttu-id="f4ba7-216">Ponownie Napisz zapytanie w formularzu, który można przetłumaczyć, lub Przełącz się na ocenę klienta jawnie, wstawiając wywołanie do AsEnumerable (), AsAsyncEnumerable (), ToList — () lub ToListAsync ().</span><span class="sxs-lookup"><span data-stu-id="f4ba7-216">Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().</span></span> <span data-ttu-id="f4ba7-217">Aby uzyskać więcej informacji, zobacz https://go.microsoft.com/fwlink/?linkid=2101038.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-217">See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.</span></span>

### <a name="specify-transaction-id"></a><span data-ttu-id="f4ba7-218">Określ identyfikator transakcji</span><span class="sxs-lookup"><span data-stu-id="f4ba7-218">Specify transaction ID</span></span>

<span data-ttu-id="f4ba7-219">Ta funkcja została pobrana ze społeczności przez program [@Marusyk](https://github.com/Marusyk) .</span><span class="sxs-lookup"><span data-stu-id="f4ba7-219">This feature was contributed from the community by [@Marusyk](https://github.com/Marusyk).</span></span> <span data-ttu-id="f4ba7-220">Wiele Dziękujemy za udział!</span><span class="sxs-lookup"><span data-stu-id="f4ba7-220">Many thanks for the contribution!</span></span>

<span data-ttu-id="f4ba7-221">EF Core uwidacznia identyfikator transakcji dla korelacji transakcji w wywołaniach.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-221">EF Core exposes a transaction ID for correlation of transactions across calls.</span></span> <span data-ttu-id="f4ba7-222">Ten identyfikator jest zazwyczaj ustawiany przez EF Core po rozpoczęciu transakcji.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-222">This ID typically set by EF Core when a transaction is started.</span></span> <span data-ttu-id="f4ba7-223">Jeśli zamiast tego aplikacja uruchamia transakcję, ta funkcja umożliwia aplikacji jawne ustawienie identyfikatora transakcji, dzięki czemu jest on skorelowany prawidłowo wszędzie tam, gdzie jest używana.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-223">If the application starts the transaction instead, then this feature allows the application to explicitly set the transaction ID so it is correlated correctly everywhere it is used.</span></span> <span data-ttu-id="f4ba7-224">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-224">For example:</span></span>

```CSharp
using (context.Database.UseTransaction(myTransaction, myId))
{
   ...
}
```

### <a name="ipaddress-mapping"></a><span data-ttu-id="f4ba7-225">Mapowanie adresu IP</span><span class="sxs-lookup"><span data-stu-id="f4ba7-225">IPAddress mapping</span></span>

<span data-ttu-id="f4ba7-226">Ta funkcja została pobrana ze społeczności przez program [@ralmsdeveloper](https://github.com/ralmsdeveloper) .</span><span class="sxs-lookup"><span data-stu-id="f4ba7-226">This feature was contributed from the community by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span> <span data-ttu-id="f4ba7-227">Wiele Dziękujemy za udział!</span><span class="sxs-lookup"><span data-stu-id="f4ba7-227">Many thanks for the contribution!</span></span>

<span data-ttu-id="f4ba7-228">Standardowa [Klasa adresu IP](/dotnet/api/system.net.ipaddress) platformy .NET jest teraz automatycznie mapowana na kolumnę ciągów dla baz danych, które nie mają jeszcze natywnej obsługi.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-228">The standard .NET [IPAddress class](/dotnet/api/system.net.ipaddress) is now automatically mapped to a string column for databases that do not already have native support.</span></span> <span data-ttu-id="f4ba7-229">Rozważmy na przykład mapowanie tego typu jednostki:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-229">For example, consider mapping this entity type:</span></span>

```CSharp
public class Host
{
    public int Id { get; set; }
    public IPAddress Address { get; set; }
}
```

<span data-ttu-id="f4ba7-230">W przypadku SQL Server spowoduje to migrację, tworząc następującą tabelę:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-230">On SQL Server, this will result in Migrations creating the following table:</span></span>

```sql
CREATE TABLE [Host] (
    [Id] int NOT NULL,
    [Address] nvarchar(45) NULL,
    CONSTRAINT [PK_Host] PRIMARY KEY ([Id]));
``` 

<span data-ttu-id="f4ba7-231">Jednostki można następnie dodać w zwykły sposób:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-231">Entities can then be added in the normal way:</span></span>

```CSharp
context.AddRange(
    new Host { Address = IPAddress.Parse("127.0.0.1")},
    new Host { Address = IPAddress.Parse("0000:0000:0000:0000:0000:0000:0000:0001")});
``` 

<span data-ttu-id="f4ba7-232">A w efekcie w programie SQL Server zostanie wstawiony znormalizowany adres IPv4 lub IPv6:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-232">And the resulting SQL will insert the normalized IPv4 or IPv6 address:</span></span>

```sql
Executed DbCommand (14ms) [Parameters=[@p0='1', @p1='127.0.0.1' (Size = 45), @p2='2', @p3='::1' (Size = 45)], CommandType='Text', CommandTimeout='30']
      SET NOCOUNT ON;
      INSERT INTO [Host] ([Id], [Address])
      VALUES (@p0, @p1), (@p2, @p3);
```

### <a name="exclude-onconfiguring-when-scaffolding"></a><span data-ttu-id="f4ba7-233">Wyklucz onkonfigurowany podczas tworzenia szkieletów</span><span class="sxs-lookup"><span data-stu-id="f4ba7-233">Exclude OnConfiguring when scaffolding</span></span>

<span data-ttu-id="f4ba7-234">Gdy DbContext jest szkieletem z istniejącej bazy danych, EF Core domyślnie tworzy Przeciążenie przy użyciu parametrów połączenia, dzięki czemu kontekst jest natychmiast użyteczny.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-234">When a DbContext is scaffolded from an existing database, EF Core by default creates an OnConfiguring overload with a connection string so that the context is immediately usable.</span></span> <span data-ttu-id="f4ba7-235">Nie jest to jednak przydatne, jeśli masz już klasę częściową z funkcją onconfiguring lub w przypadku konfigurowania kontekstu w inny sposób.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-235">However, this is not useful if you already have a partial class with OnConfiguring, or if you are configuring the context some other way.</span></span>

<span data-ttu-id="f4ba7-236">Aby rozwiązać ten potrzeba, można teraz wydać polecenia tworzenia szkieletu, aby pominąć generowanie elementu onconfiguring.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-236">To address this, the scaffolding commands can now be instructed to omit generation of OnConfiguring.</span></span> <span data-ttu-id="f4ba7-237">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-237">For example:</span></span>

```
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer --no-onconfiguring
```

<span data-ttu-id="f4ba7-238">Lub w konsoli Menedżera pakietów:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-238">Or in the Package Manager Console:</span></span>

```
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer -NoOnConfiguring 
``` 

<span data-ttu-id="f4ba7-239">Należy pamiętać, że zalecamy użycie [nazwanego ciągu połączenia i bezpiecznego magazynu, takiego jak wpisy tajne użytkownika](/core/managing-schemas/scaffolding?tabs=vs#configuration-and-user-secrets).</span><span class="sxs-lookup"><span data-stu-id="f4ba7-239">Note that we recommend using [a named connection string and secure storage like User Secrets](/core/managing-schemas/scaffolding?tabs=vs#configuration-and-user-secrets).</span></span>

### <a name="translations-for-firstordefault-on-strings"></a><span data-ttu-id="f4ba7-240">Tłumaczenia dla FirstOrDefault na ciągach</span><span class="sxs-lookup"><span data-stu-id="f4ba7-240">Translations for FirstOrDefault on strings</span></span>

<span data-ttu-id="f4ba7-241">Ta funkcja została pobrana ze społeczności przez program [@dvoreckyaa](https://github.com/dvoreckyaa) .</span><span class="sxs-lookup"><span data-stu-id="f4ba7-241">This feature was contributed from the community by [@dvoreckyaa](https://github.com/dvoreckyaa).</span></span> <span data-ttu-id="f4ba7-242">Wiele Dziękujemy za udział!</span><span class="sxs-lookup"><span data-stu-id="f4ba7-242">Many thanks for the contribution!</span></span>

<span data-ttu-id="f4ba7-243">FirstOrDefault i podobne operatory dla znaków w ciągach są teraz tłumaczone.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-243">FirstOrDefault and similar operators for characters in strings are now translated.</span></span> <span data-ttu-id="f4ba7-244">Na przykład ta kwerenda LINQ:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-244">For example, this LINQ query:</span></span>

```CSharp
context.Customers.Where(c => c.ContactName.FirstOrDefault() == 'A').ToList();
```

<span data-ttu-id="f4ba7-245">Zostaną przetłumaczone na następujące SQL podczas korzystania z SQL Server:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-245">Will be translated to the following SQL when using SQL Server:</span></span>

```sql
SELECT [c].[Id], [c].[ContactName]
FROM [Customer] AS [c]
WHERE SUBSTRING([c].[ContactName], 1, 1) = N'A'
```

### <a name="simplify-case-blocks"></a><span data-ttu-id="f4ba7-246">Uprość bloki wielkości liter</span><span class="sxs-lookup"><span data-stu-id="f4ba7-246">Simplify case blocks</span></span>

<span data-ttu-id="f4ba7-247">EF Core teraz generuje lepsze zapytania z blokami przypadków.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-247">EF Core now generates better queries with CASE blocks.</span></span> <span data-ttu-id="f4ba7-248">Na przykład ta kwerenda LINQ:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-248">For example, this LINQ query:</span></span> 

```CSharp
context.Weapons
    .OrderBy(w => w.Name.CompareTo("Marcus' Lancer") == 0)
    .ThenBy(w => w.Id)
```

<span data-ttu-id="f4ba7-249">Był włączony SQL Server przetłumaczony na:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-249">Was on SQL Server formally translated to:</span></span>

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

<span data-ttu-id="f4ba7-250">Jest teraz tłumaczone na:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-250">But is now translated to:</span></span>

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN ([w].[Name] = N'Marcus'' Lancer') AND [w].[Name] IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
``` 

## <a name="preview-5"></a><span data-ttu-id="f4ba7-251">Wersja zapoznawcza 5</span><span class="sxs-lookup"><span data-stu-id="f4ba7-251">Preview 5</span></span>

### <a name="database-collations"></a><span data-ttu-id="f4ba7-252">Sortowanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="f4ba7-252">Database collations</span></span>

<span data-ttu-id="f4ba7-253">Domyślne sortowanie dla bazy danych można teraz określić w modelu EF.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-253">The default collation for a database can now be specified in the EF model.</span></span> <span data-ttu-id="f4ba7-254">Spowoduje to przetworzenie przetworzonych migracji w celu ustawienia sortowania podczas tworzenia bazy danych.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-254">This will flow through to generated migrations to set the collation when the database is created.</span></span> <span data-ttu-id="f4ba7-255">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-255">For example:</span></span>

```CSharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="f4ba7-256">Następnie migracja generuje następujące elementy, aby utworzyć bazę danych na SQL Server:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-256">Migrations then generates the following to create the database on SQL Server:</span></span>

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

<span data-ttu-id="f4ba7-257">Można również określić sortowanie, które ma być używane dla określonych kolumn bazy danych.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-257">The collation to use for specific database columns can also be specified.</span></span> <span data-ttu-id="f4ba7-258">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-258">For example:</span></span>

```CSharp
 modelBuilder
     .Entity<User>()
     .Property(e => e.Name)
     .UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="f4ba7-259">Dla tych, które nie korzystają z migracji, sortowania są teraz odtwarzane z bazy danych podczas tworzenia szkieletu DbContext.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-259">For those not using migrations, collations are now reverse-engineered from the database when scaffolding a DbContext.</span></span>

<span data-ttu-id="f4ba7-260">Na koniec `EF.Functions.Collate()` zezwala na kwerendy ad hoc przy użyciu różnych ustawień sortowania.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-260">Finally, the `EF.Functions.Collate()` allows for ad-hoc queries using different collations.</span></span> <span data-ttu-id="f4ba7-261">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-261">For example:</span></span>

```CSharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

<span data-ttu-id="f4ba7-262">Spowoduje to wygenerowanie następującej kwerendy dla SQL Server:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-262">This will generate the following query for SQL Server:</span></span>

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

<span data-ttu-id="f4ba7-263">Należy pamiętać, że sortowania ad-hoc należy używać z ostrożnością, ponieważ mogą one mieć negatywny wpływ na wydajność bazy danych.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-263">Note that ad-hoc collations should be used with care as they can negatively impact database performance.</span></span>

<span data-ttu-id="f4ba7-264">Dokumentacja jest śledzona przez [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273)problemu.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-264">Documentation is tracked by issue [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span></span>

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a><span data-ttu-id="f4ba7-265">Argumenty przepływu w IDesignTimeDbContextFactory</span><span class="sxs-lookup"><span data-stu-id="f4ba7-265">Flow arguments into IDesignTimeDbContextFactory</span></span>

<span data-ttu-id="f4ba7-266">Argumenty są teraz przepływane z wiersza polecenia do `CreateDbContext` metody [IDesignTimeDbContextFactory](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1).</span><span class="sxs-lookup"><span data-stu-id="f4ba7-266">Arguments are now flowed from the command line into the `CreateDbContext` method of [IDesignTimeDbContextFactory](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1).</span></span> <span data-ttu-id="f4ba7-267">Na przykład, aby wskazać, że jest to kompilacja dev, argument niestandardowy (np. `dev` ) można przesłać w wierszu polecenia:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-267">For example, to indicate this is a dev build, a custom argument (e.g. `dev`) can be passed on the command line:</span></span>

```
dotnet ef migrations add two --verbose --dev
``` 

<span data-ttu-id="f4ba7-268">Ten argument będzie przepływał do fabryki, gdzie może służyć do kontrolowania sposobu tworzenia i inicjowania kontekstu.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-268">This argument will then flow into the factory, where it can be used to control how the context is created and initialized.</span></span> <span data-ttu-id="f4ba7-269">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-269">For example:</span></span>

```CSharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args) 
        => new SomeDbContext(args.Contains("--dev"));
}
```

<span data-ttu-id="f4ba7-270">Dokumentacja jest śledzona przez [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419)problemu.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-270">Documentation is tracked by issue [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span></span>

### <a name="no-tracking-queries-with-identity-resolution"></a><span data-ttu-id="f4ba7-271">Nie śledzij zapytań z rozpoznawaniem tożsamości</span><span class="sxs-lookup"><span data-stu-id="f4ba7-271">No-tracking queries with identity resolution</span></span>

<span data-ttu-id="f4ba7-272">Nie można teraz skonfigurować zapytań śledzenia, aby przeprowadzić rozpoznawanie tożsamości.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-272">No-tracking queries can now be configured to perform identity resolution.</span></span> <span data-ttu-id="f4ba7-273">Na przykład następujące zapytanie utworzy nowe wystąpienie blogu dla każdego wpisu, nawet jeśli każdy blog ma ten sam klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-273">For example, the following query will create a new Blog instance for each Post, even if each Blog has the same primary key.</span></span> 

```CSharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

<span data-ttu-id="f4ba7-274">Jednak koszt zwykle jest nieco wolniejszy i zawsze korzysta z większej ilości pamięci, to zapytanie można zmienić, aby upewnić się, że tworzone jest tylko jedno wystąpienie blogu:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-274">However, at the expense of usually being slightly slower and always using more memory, this query can be changed to ensure only a single Blog instance is created:</span></span>

```CSharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

<span data-ttu-id="f4ba7-275">Należy zauważyć, że jest to przydatne tylko w przypadku zapytań bez śledzenia, ponieważ wszystkie zapytania śledzenia już wykazują takie zachowanie.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-275">Note that this is only useful for no-tracking queries since all tracking queries already exhibit this behavior.</span></span> <span data-ttu-id="f4ba7-276">Ponadto, po przeglądzie interfejsu API, `PerformIdentityResolution` składnia zostanie zmieniona.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-276">Also, following API review, the `PerformIdentityResolution` syntax will be changed.</span></span> <span data-ttu-id="f4ba7-277">Zobacz [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span><span class="sxs-lookup"><span data-stu-id="f4ba7-277">See [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span></span>

<span data-ttu-id="f4ba7-278">Dokumentacja jest śledzona przez [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895)problemu.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-278">Documentation is tracked by issue [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span></span>

### <a name="stored-persisted-computed-columns"></a><span data-ttu-id="f4ba7-279">Przechowywane (utrwalone) kolumny obliczane</span><span class="sxs-lookup"><span data-stu-id="f4ba7-279">Stored (persisted) computed columns</span></span>

<span data-ttu-id="f4ba7-280">Większość baz danych umożliwia przechowywanie wartości kolumn obliczanych po obliczeniach.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-280">Most databases allow computed column values to be stored after computation.</span></span> <span data-ttu-id="f4ba7-281">Gdy to zajmuje miejsce na dysku, kolumna obliczana jest obliczana tylko raz podczas aktualizacji, a nie za każdym razem, gdy jej wartość zostanie pobrana.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-281">While this takes up disk space, the computed column is calculated only once on update, instead of each time its value is retrieved.</span></span> <span data-ttu-id="f4ba7-282">Pozwala to również na indeksowanie kolumny dla niektórych baz danych.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-282">This also allows the column to be indexed for some databases.</span></span>

<span data-ttu-id="f4ba7-283">EF Core 5,0 umożliwia skonfigurowanie kolumn obliczanych jako przechowywanych.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-283">EF Core 5.0 allows computed columns to be configured as stored.</span></span> <span data-ttu-id="f4ba7-284">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-284">For example:</span></span>
 
```CSharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a><span data-ttu-id="f4ba7-285">Kolumny obliczane przez SQLite</span><span class="sxs-lookup"><span data-stu-id="f4ba7-285">SQLite computed columns</span></span>

<span data-ttu-id="f4ba7-286">EF Core teraz obsługuje kolumny obliczane w bazach danych oprogramowania SQLite.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-286">EF Core now supports computed columns in SQLite databases.</span></span>

## <a name="preview-4"></a><span data-ttu-id="f4ba7-287">Wersja zapoznawcza 4</span><span class="sxs-lookup"><span data-stu-id="f4ba7-287">Preview 4</span></span>

### <a name="configure-database-precisionscale-in-model"></a><span data-ttu-id="f4ba7-288">Konfiguruj precyzję i skalowanie bazy danych w modelu</span><span class="sxs-lookup"><span data-stu-id="f4ba7-288">Configure database precision/scale in model</span></span>

<span data-ttu-id="f4ba7-289">Precyzja i skala właściwości można teraz określić przy użyciu konstruktora modeli.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-289">Precision and scale for a property can now be specified using the model builder.</span></span> <span data-ttu-id="f4ba7-290">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-290">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

<span data-ttu-id="f4ba7-291">Precyzja i skala można nadal ustawiać za pośrednictwem pełnego typu bazy danych, na przykład "Decimal (16, 4)".</span><span class="sxs-lookup"><span data-stu-id="f4ba7-291">Precision and scale can still be set via the full database type, such as "decimal(16,4)".</span></span> 

<span data-ttu-id="f4ba7-292">Dokumentacja jest śledzona przez [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527)problemu.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-292">Documentation is tracked by issue [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span></span>

### <a name="specify-sql-server-index-fill-factor"></a><span data-ttu-id="f4ba7-293">Określ współczynnik wypełnienia indeksu SQL Server</span><span class="sxs-lookup"><span data-stu-id="f4ba7-293">Specify SQL Server index fill factor</span></span>

<span data-ttu-id="f4ba7-294">Współczynnik wypełniania można teraz określić podczas tworzenia indeksu na SQL Server.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-294">The fill factor can now be specified when creating an index on SQL Server.</span></span> <span data-ttu-id="f4ba7-295">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-295">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a><span data-ttu-id="f4ba7-296">Wersja zapoznawcza 3</span><span class="sxs-lookup"><span data-stu-id="f4ba7-296">Preview 3</span></span>

### <a name="filtered-include"></a><span data-ttu-id="f4ba7-297">Filtr obejmujący</span><span class="sxs-lookup"><span data-stu-id="f4ba7-297">Filtered Include</span></span>

<span data-ttu-id="f4ba7-298">Metoda include obsługuje teraz filtrowanie uwzględnionych jednostek.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-298">The Include method now supports filtering of the entities included.</span></span> <span data-ttu-id="f4ba7-299">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-299">For example:</span></span>

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="f4ba7-300">To zapytanie będzie zwracać Blogi razem z poszczególnymi wpisami skojarzonymi, ale tylko wtedy, gdy tytuł wpisu zawiera "ser".</span><span class="sxs-lookup"><span data-stu-id="f4ba7-300">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="f4ba7-301">Pomiń i zrób można także użyć, aby zmniejszyć liczbę uwzględnionych jednostek.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-301">Skip and Take can also be used to reduce the number of included entities.</span></span> <span data-ttu-id="f4ba7-302">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-302">For example:</span></span>
 
```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
<span data-ttu-id="f4ba7-303">To zapytanie będzie zwracać blogi z co najwyżej pięcioma wpisami zawartymi w każdym blogu.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-303">This query will return blogs with at most five posts included on each blog.</span></span>

<span data-ttu-id="f4ba7-304">Szczegółowe informacje znajdują się w [dokumentacji dołączanej](xref:core/querying/related-data#filtered-include) .</span><span class="sxs-lookup"><span data-stu-id="f4ba7-304">See the [Include documentation](xref:core/querying/related-data#filtered-include) for full details.</span></span>

### <a name="new-modelbuilder-api-for-navigation-properties"></a><span data-ttu-id="f4ba7-305">Nowy interfejs API element modelbuilder dla właściwości nawigacji</span><span class="sxs-lookup"><span data-stu-id="f4ba7-305">New ModelBuilder API for navigation properties</span></span>

<span data-ttu-id="f4ba7-306">Właściwości nawigacji są przede wszystkim konfigurowane podczas [definiowania relacji](xref:core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="f4ba7-306">Navigation properties are primarily configured when [defining relationships](xref:core/modeling/relationships).</span></span> <span data-ttu-id="f4ba7-307">Jednak nowa `Navigation` Metoda może być używana w przypadkach, w których właściwości nawigacji wymagają dodatkowej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-307">However, the new `Navigation` method can be used in the cases where navigation properties need additional configuration.</span></span> <span data-ttu-id="f4ba7-308">Na przykład, aby ustawić pole zapasowe dla nawigacji, gdy pole nie zostanie znalezione według Konwencji:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-308">For example, to set a backing field for the navigation when the field would not be found by convention:</span></span>

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

<span data-ttu-id="f4ba7-309">Należy pamiętać, że `Navigation` interfejs API nie zastępuje konfiguracji relacji.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-309">Note that the `Navigation` API does not replace relationship configuration.</span></span> <span data-ttu-id="f4ba7-310">Zamiast tego umożliwia dodatkową konfigurację właściwości nawigacji w już odnalezionych lub zdefiniowanych relacjach.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-310">Instead it allows additional configuration of navigation properties in already discovered or defined relationships.</span></span>

<span data-ttu-id="f4ba7-311">Zapoznaj się z [dokumentacją dotyczącą konfigurowania właściwości nawigacji](xref:core/modeling/relationships#configuring-navigation-properties).</span><span class="sxs-lookup"><span data-stu-id="f4ba7-311">See the [Configuring Navigation Properties documentation](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a><span data-ttu-id="f4ba7-312">Nowe parametry wiersza polecenia dla przestrzeni nazw i parametrów połączenia</span><span class="sxs-lookup"><span data-stu-id="f4ba7-312">New command-line parameters for namespaces and connection strings</span></span> 

<span data-ttu-id="f4ba7-313">Migracje i tworzenie szkieletów umożliwiają teraz określenie przestrzeni nazw w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-313">Migrations and scaffolding now allow namespaces to be specified on the command line.</span></span> <span data-ttu-id="f4ba7-314">Na przykład w celu odtworzenia bazy danych, w której są umieszczane klasy kontekstu i modelu w różnych przestrzeniach nazw:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-314">For example, to reverse engineer a database putting the context and model classes in different namespaces:</span></span> 

```
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

<span data-ttu-id="f4ba7-315">Szczegółowe informacje znajdują się [w dokumentacji](xref:core/managing-schemas/scaffolding#directories-and-namespaces) dotyczącej [migracji](xref:core/managing-schemas/migrations/index#namespaces) i odtwarzania.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-315">See the [Migrations](xref:core/managing-schemas/migrations/index#namespaces) and [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces) documentation for full details.</span></span>

---
<span data-ttu-id="f4ba7-316">Ponadto parametry połączenia można teraz przekazywać do `database-update` polecenia:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-316">Also, a connection string can now be passed to the `database-update` command:</span></span>

```
dotnet ef database update --connection "connection string"
```

<span data-ttu-id="f4ba7-317">Szczegółowe informacje znajdują się w [dokumentacji narzędzi](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) .</span><span class="sxs-lookup"><span data-stu-id="f4ba7-317">See the [Tools documentation](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) for full details.</span></span>

<span data-ttu-id="f4ba7-318">Do poleceń programu PowerShell, które są używane w konsoli Menedżera pakietów programu VS, dodano również równoważne parametry.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-318">Equivalent parameters have also been added to the PowerShell commands used in the VS Package Manager Console.</span></span>

### <a name="enabledetailederrors-has-returned"></a><span data-ttu-id="f4ba7-319">EnableDetailedErrors</span><span class="sxs-lookup"><span data-stu-id="f4ba7-319">EnableDetailedErrors has returned</span></span>

<span data-ttu-id="f4ba7-320">Ze względu na wydajność EF nie wykonuje dodatkowych kontroli wartości null podczas odczytywania wartości z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-320">For performance reasons, EF doesn't do additional null-checks when reading values from the database.</span></span> <span data-ttu-id="f4ba7-321">Może to spowodować, że wyjątki są trudne do wylogowania, gdy zostanie napotkany nieoczekiwany element null.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-321">This can result in exceptions that are hard to root-cause when an unexpected null is encountered.</span></span>

<span data-ttu-id="f4ba7-322">Korzystanie z programu `EnableDetailedErrors` spowoduje dodanie dodatkowych kontroli wartości null do zapytań, takich jak w przypadku małego obciążenia wydajności, te błędy są łatwiejsze do śledzenia z przyczyn głównych.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-322">Using `EnableDetailedErrors` will add extra null checking to queries such that, for a small performance overhead, these errors are easier to trace back to a root cause.</span></span>  

<span data-ttu-id="f4ba7-323">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-323">For example:</span></span>
```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors 
        .UseSqlServer(Your.SqlServerConnectionString);
```

<span data-ttu-id="f4ba7-324">Dokumentacja jest śledzona przez [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955)problemu.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-324">Documentation is tracked by issue [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="f4ba7-325">Klucze partycji Cosmos</span><span class="sxs-lookup"><span data-stu-id="f4ba7-325">Cosmos partition keys</span></span>

<span data-ttu-id="f4ba7-326">Klucz partycji, który ma być używany dla danego zapytania, można teraz określić w zapytaniu.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-326">The partition key to use for a given query can now be specified in the query.</span></span> <span data-ttu-id="f4ba7-327">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-327">For example:</span></span>

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

<span data-ttu-id="f4ba7-328">Dokumentacja jest śledzona przez [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199)problemu.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-328">Documentation is tracked by issue [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span></span>

### <a name="support-for-the-sql-server-datalength-function"></a><span data-ttu-id="f4ba7-329">Obsługa funkcji SQL Server DATALENGTH</span><span class="sxs-lookup"><span data-stu-id="f4ba7-329">Support for the SQL Server DATALENGTH function</span></span>

<span data-ttu-id="f4ba7-330">Dostęp do niego można uzyskać za pomocą nowej `EF.Functions.DataLength` metody.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-330">This can be accessed using the new `EF.Functions.DataLength` method.</span></span> <span data-ttu-id="f4ba7-331">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-331">For example:</span></span>
```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
``` 

## <a name="preview-2"></a><span data-ttu-id="f4ba7-332">Preview 2</span><span class="sxs-lookup"><span data-stu-id="f4ba7-332">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="f4ba7-333">Użyj atrybutu języka C#, aby określić pole zapasowe właściwości</span><span class="sxs-lookup"><span data-stu-id="f4ba7-333">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="f4ba7-334">Atrybut języka C# może być teraz używany do określania pola zapasowego dla właściwości.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-334">A C# attribute can now be used to specify the backing field for a property.</span></span> <span data-ttu-id="f4ba7-335">Ten atrybut pozwala EF Core nadal pisać i odczytywać dane z pola zapasowego, tak jak zwykle, nawet jeśli nie można automatycznie znaleźć pola zapasowego.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-335">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span> <span data-ttu-id="f4ba7-336">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-336">For example:</span></span>

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

<span data-ttu-id="f4ba7-337">Dokumentacja jest śledzona przez [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230)problemu.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-337">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="f4ba7-338">Kompletne mapowanie rozróżniacza</span><span class="sxs-lookup"><span data-stu-id="f4ba7-338">Complete discriminator mapping</span></span>

<span data-ttu-id="f4ba7-339">EF Core używa kolumny rozróżniacza dla [mapowania TPH hierarchii dziedziczenia](/ef/core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="f4ba7-339">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](/ef/core/modeling/inheritance).</span></span> <span data-ttu-id="f4ba7-340">Niektóre ulepszenia wydajności są możliwe tak długo, jak EF Core wie wszystkie możliwe wartości dla rozróżniacza.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-340">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span> <span data-ttu-id="f4ba7-341">EF Core 5,0 implementuje teraz te ulepszenia.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-341">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="f4ba7-342">Na przykład poprzednie wersje programu EF Core zawsze generują te dane SQL dla zapytania zwracającego wszystkie typy w hierarchii:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-342">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="f4ba7-343">EF Core 5,0 będzie teraz generować następujące elementy, gdy zostanie skonfigurowane kompletne mapowanie rozróżniacza:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-343">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="f4ba7-344">Będzie to domyślne zachowanie rozpoczynające się od wersji zapoznawczej 3.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-344">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="f4ba7-345">Ulepszenia wydajności w programie Microsoft. Data. sqlite</span><span class="sxs-lookup"><span data-stu-id="f4ba7-345">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="f4ba7-346">Wprowadziliśmy dwie ulepszenia wydajności dla oprogramowania SQLIte:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-346">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="f4ba7-347">Pobieranie danych binarnych i ciągów za pomocą metody GetBytes, GetChars i GetTextReader jest teraz wydajniejsze dzięki użyciu SqliteBlob i strumieni.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-347">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="f4ba7-348">Inicjalizacja SqliteConnection jest teraz opóźniona.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-348">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="f4ba7-349">Te udoskonalenia znajdują się w dostawcy ADO.NET Microsoft. Data. SQLite, a tym samym zwiększyć wydajność poza EF Core.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-349">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="f4ba7-350">Wersja zapoznawcza 1</span><span class="sxs-lookup"><span data-stu-id="f4ba7-350">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="f4ba7-351">Rejestrowanie proste</span><span class="sxs-lookup"><span data-stu-id="f4ba7-351">Simple logging</span></span>

<span data-ttu-id="f4ba7-352">Ta funkcja dodaje funkcje podobne do `Database.Log` programu w programie Ef6.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-352">This feature adds functionality similar to `Database.Log` in EF6.</span></span> <span data-ttu-id="f4ba7-353">Oznacza to, że zapewnia prosty sposób pobierania dzienników z EF Core bez konieczności konfigurowania żadnego rodzaju zewnętrznej platformy rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-353">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="f4ba7-354">Wstępna dokumentacja jest zawarta w [statusie tygodnia Ef 5 grudnia 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span><span class="sxs-lookup"><span data-stu-id="f4ba7-354">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="f4ba7-355">Dodatkowa dokumentacja jest śledzona przez [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085)problemu.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-355">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="f4ba7-356">Prosty sposób uzyskiwania wygenerowanego kodu SQL</span><span class="sxs-lookup"><span data-stu-id="f4ba7-356">Simple way to get generated SQL</span></span>

<span data-ttu-id="f4ba7-357">EF Core 5,0 wprowadza `ToQueryString` metodę rozszerzenia, która zwróci kod SQL, który EF Core zostanie wygenerowany podczas wykonywania zapytania LINQ.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-357">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="f4ba7-358">Wstępna dokumentacja jest uwzględniona w [statusie tygodniowym EF dla 9 stycznia 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span><span class="sxs-lookup"><span data-stu-id="f4ba7-358">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="f4ba7-359">Dodatkowa dokumentacja jest śledzona przez [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331)problemu.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-359">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="f4ba7-360">Użyj atrybutu języka C#, aby wskazać, że jednostka nie ma klucza</span><span class="sxs-lookup"><span data-stu-id="f4ba7-360">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="f4ba7-361">Typ jednostki można teraz skonfigurować jako bez klucza przy użyciu nowego elementu `KeylessAttribute` .</span><span class="sxs-lookup"><span data-stu-id="f4ba7-361">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span> <span data-ttu-id="f4ba7-362">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-362">For example:</span></span>

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="f4ba7-363">Dokumentacja jest śledzona przez [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186)problemu.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-363">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="f4ba7-364">Parametry Connection lub Connection można zmienić w zainicjowanym kontekście DbContext</span><span class="sxs-lookup"><span data-stu-id="f4ba7-364">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="f4ba7-365">Teraz łatwiej jest utworzyć wystąpienie DbContext bez żadnego połączenia lub parametrów połączenia.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-365">It is now easier to create a DbContext instance without any connection or connection string.</span></span> <span data-ttu-id="f4ba7-366">Ponadto parametry połączenia lub połączenia można teraz przystąpić do wystąpienia kontekstu.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-366">Also, the connection or connection string can now be mutated on the context instance.</span></span> <span data-ttu-id="f4ba7-367">Ta funkcja pozwala temu samemu wystąpieniu kontekstu na dynamiczne łączenie się z różnymi bazami danych.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-367">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="f4ba7-368">Dokumentacja jest śledzona przez [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075)problemu.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-368">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="f4ba7-369">Serwery proxy śledzenia zmian</span><span class="sxs-lookup"><span data-stu-id="f4ba7-369">Change-tracking proxies</span></span>

<span data-ttu-id="f4ba7-370">EF Core mogą teraz generować serwery proxy środowiska uruchomieniowego, które automatycznie implementują [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) i [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span><span class="sxs-lookup"><span data-stu-id="f4ba7-370">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) and [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span></span> <span data-ttu-id="f4ba7-371">Następnie te zmiany są raportowane w oparciu o właściwości jednostki bezpośrednio do EF Core, unikając konieczności skanowania pod kątem zmian.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-371">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span> <span data-ttu-id="f4ba7-372">Jednak serwery proxy są dostarczane z własnym zestawem ograniczeń, więc nie są przeznaczone dla wszystkich użytkowników.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-372">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="f4ba7-373">Dokumentacja jest śledzona przez [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076)problemu.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-373">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="f4ba7-374">Udoskonalone widoki debugowania</span><span class="sxs-lookup"><span data-stu-id="f4ba7-374">Enhanced debug views</span></span>

<span data-ttu-id="f4ba7-375">Widoki debugowania to prosty sposób na zajrzeć do wewnętrznych EF Core w przypadku problemów z debugowaniem.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-375">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span> <span data-ttu-id="f4ba7-376">Widok debugowania dla modelu został zaimplementowany jakiś czas temu.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-376">A debug view for the Model was implemented some time ago.</span></span> <span data-ttu-id="f4ba7-377">W przypadku EF Core 5,0 ten widok modelu jest łatwiejszy do odczytania i dodania nowego widoku debugowania dla śledzonych jednostek w Menedżerze stanu.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-377">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="f4ba7-378">Wstępna dokumentacja jest uwzględniona w [Stanach tygodnia EF 12 grudnia 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span><span class="sxs-lookup"><span data-stu-id="f4ba7-378">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="f4ba7-379">Dodatkowa dokumentacja jest śledzona przez [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086)problemu.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-379">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="f4ba7-380">Ulepszona obsługa semantyki o wartości null bazy danych</span><span class="sxs-lookup"><span data-stu-id="f4ba7-380">Improved handling of database null semantics</span></span>

<span data-ttu-id="f4ba7-381">Relacyjne bazy danych zazwyczaj traktują wartości NULL jako nieznaną wartość i w związku z tym nie są równe żadnym innym NULL.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-381">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span> <span data-ttu-id="f4ba7-382">Chociaż język C# traktuje wartość null w postaci wartości zdefiniowanej, co porównuje ją z innymi wartościami null.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-382">While C# treats null as a defined value, which compares equal to any other null.</span></span> <span data-ttu-id="f4ba7-383">EF Core domyślnie tłumaczy zapytania tak, aby korzystały z semantyki wartości null języka C#.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-383">EF Core by default translates queries so that they use C# null semantics.</span></span> <span data-ttu-id="f4ba7-384">EF Core 5,0 znacznie poprawia wydajność tych tłumaczeń.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-384">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="f4ba7-385">Dokumentacja jest śledzona przez [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612)problemu.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-385">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="f4ba7-386">Właściwości indeksatora</span><span class="sxs-lookup"><span data-stu-id="f4ba7-386">Indexer properties</span></span>

<span data-ttu-id="f4ba7-387">EF Core 5,0 obsługuje mapowanie właściwości indeksatora języka C#.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-387">EF Core 5.0 supports mapping of C# indexer properties.</span></span> <span data-ttu-id="f4ba7-388">Te właściwości umożliwiają jednostkom działanie jako zbiory właściwości, w których kolumny są mapowane na nazwane właściwości w zbiorze.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-388">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="f4ba7-389">Dokumentacja jest śledzona przez [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018)problemu.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-389">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="f4ba7-390">Generowanie ograniczeń check dla mapowań wyliczenia</span><span class="sxs-lookup"><span data-stu-id="f4ba7-390">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="f4ba7-391">Migracje EF Core 5,0 mogą teraz generować ograniczenia CHECK dla mapowań właściwości enum.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-391">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span> <span data-ttu-id="f4ba7-392">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-392">For example:</span></span>

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="f4ba7-393">Dokumentacja jest śledzona przez [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082)problemu.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-393">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="f4ba7-394">Isrelacyjne</span><span class="sxs-lookup"><span data-stu-id="f4ba7-394">IsRelational</span></span>

<span data-ttu-id="f4ba7-395">Dodano nową `IsRelational` metodę oprócz istniejących `IsSqlServer` , `IsSqlite` i `IsInMemory` .</span><span class="sxs-lookup"><span data-stu-id="f4ba7-395">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span> <span data-ttu-id="f4ba7-396">Tej metody można użyć do sprawdzenia, czy DbContext używa dowolnego dostawcy relacyjnej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-396">This method can be used to test if the DbContext is using any relational database provider.</span></span> <span data-ttu-id="f4ba7-397">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-397">For example:</span></span>

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="f4ba7-398">Dokumentacja jest śledzona przez [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185)problemu.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-398">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="f4ba7-399">Cosmos optymistyczne współbieżność za pomocą elementów ETag</span><span class="sxs-lookup"><span data-stu-id="f4ba7-399">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="f4ba7-400">Dostawca bazy danych Azure Cosmos DB obsługuje teraz optymistyczne współbieżność za pomocą elementów ETag.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-400">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span> <span data-ttu-id="f4ba7-401">Użyj konstruktora modeli w OnModelCreating, aby skonfigurować element ETag:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-401">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="f4ba7-402">Metody SaveChanges następnie zgłosi `DbUpdateConcurrencyException` konflikt współbieżności, który [można obsłużyć](https://docs.microsoft.com/ef/core/saving/concurrency) w celu zaimplementowania ponownych prób itd.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-402">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](https://docs.microsoft.com/ef/core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="f4ba7-403">Dokumentacja jest śledzona przez [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099)problemu.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-403">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="f4ba7-404">Tłumaczenie zapytań dla większej liczby konstrukcji DateTime</span><span class="sxs-lookup"><span data-stu-id="f4ba7-404">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="f4ba7-405">Zapytania zawierające nową konstrukcję DateTime są teraz tłumaczone.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-405">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="f4ba7-406">Ponadto następujące funkcje SQL Server są teraz mapowane:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-406">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="f4ba7-407">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="f4ba7-407">DateDiffWeek</span></span>
* <span data-ttu-id="f4ba7-408">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="f4ba7-408">DateFromParts</span></span>

<span data-ttu-id="f4ba7-409">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-409">For example:</span></span>

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="f4ba7-410">Dokumentacja jest śledzona przez [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)problemu.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-410">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="f4ba7-411">Tłumaczenie zapytania dla większej liczby konstrukcji tablicy</span><span class="sxs-lookup"><span data-stu-id="f4ba7-411">Query translations for more byte array constructs</span></span>

<span data-ttu-id="f4ba7-412">Zapytania używające właściwości Contains, Length, SequenceEqual itp. on-Byte [] są teraz tłumaczone na SQL.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-412">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="f4ba7-413">Wstępna dokumentacja jest zawarta w [statusie tygodnia Ef 5 grudnia 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span><span class="sxs-lookup"><span data-stu-id="f4ba7-413">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="f4ba7-414">Dodatkowa dokumentacja jest śledzona przez [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)problemu.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-414">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="f4ba7-415">Tłumaczenie zapytania do tyłu</span><span class="sxs-lookup"><span data-stu-id="f4ba7-415">Query translation for Reverse</span></span>

<span data-ttu-id="f4ba7-416">Zapytania z użyciem `Reverse` są teraz tłumaczone.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-416">Queries using `Reverse` are now translated.</span></span> <span data-ttu-id="f4ba7-417">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-417">For example:</span></span>

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="f4ba7-418">Dokumentacja jest śledzona przez [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)problemu.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-418">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="f4ba7-419">Tłumaczenie zapytania dla operatorów bitowych</span><span class="sxs-lookup"><span data-stu-id="f4ba7-419">Query translation for bitwise operators</span></span>

<span data-ttu-id="f4ba7-420">Zapytania wykorzystujące operatory bitowe są teraz tłumaczone na przykład w większej liczbie przypadków:</span><span class="sxs-lookup"><span data-stu-id="f4ba7-420">Queries using bitwise operators are now translated in more cases For example:</span></span>

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="f4ba7-421">Dokumentacja jest śledzona przez [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)problemu.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-421">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="f4ba7-422">Tłumaczenie zapytania dla ciągów w Cosmos</span><span class="sxs-lookup"><span data-stu-id="f4ba7-422">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="f4ba7-423">Zapytania korzystające z metod String zawierają, StartsWith i EndsWith są teraz tłumaczone przy użyciu dostawcy Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-423">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="f4ba7-424">Dokumentacja jest śledzona przez [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079)problemu.</span><span class="sxs-lookup"><span data-stu-id="f4ba7-424">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
