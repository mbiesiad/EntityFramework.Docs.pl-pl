---
title: Stosowanie migracji — EF Core
author: bricelam
ms.author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/applying
ms.openlocfilehash: f83a014651fdf2262a603fb91da7984672690197
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238760"
---
# <a name="applying-migrations"></a><span data-ttu-id="a2861-102">Stosowanie migracji</span><span class="sxs-lookup"><span data-stu-id="a2861-102">Applying Migrations</span></span>

<span data-ttu-id="a2861-103">Po dodaniu migracji należy je wdrożyć i zastosować do baz danych.</span><span class="sxs-lookup"><span data-stu-id="a2861-103">Once your migrations have been added, they need to be deployed and applied to your databases.</span></span> <span data-ttu-id="a2861-104">Istnieją różne strategie, które są bardziej odpowiednie dla środowisk produkcyjnych i innych dla cyklu życia.</span><span class="sxs-lookup"><span data-stu-id="a2861-104">There are various strategies for doing this, with some being more appropriate for production environments, and others for the development lifecycle.</span></span>

> [!NOTE]
> <span data-ttu-id="a2861-105">Bez względu na strategię wdrażania, należy zawsze sprawdzać wygenerowane migracje i testować je przed zastosowaniem do produkcyjnej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="a2861-105">Whatever your deployment strategy, always inspect the generated migrations and test them before applying to a production database.</span></span> <span data-ttu-id="a2861-106">Migracja może porzucić kolumnę, gdy zamiarem było zmienić jej nazwę lub może się nie powieść z różnych powodów w przypadku zastosowania do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="a2861-106">A migration may drop a column when the intent was to rename it, or may fail for various reasons when applied to a database.</span></span>

## <a name="sql-scripts"></a><span data-ttu-id="a2861-107">Skrypty SQL</span><span class="sxs-lookup"><span data-stu-id="a2861-107">SQL scripts</span></span>

<span data-ttu-id="a2861-108">Zalecanym sposobem wdrożenia migracji do produkcyjnej bazy danych jest wygenerowanie skryptów SQL.</span><span class="sxs-lookup"><span data-stu-id="a2861-108">The recommended way to deploy migrations to a production database is by generating SQL scripts.</span></span> <span data-ttu-id="a2861-109">Zalety tej strategii są następujące:</span><span class="sxs-lookup"><span data-stu-id="a2861-109">The advantages of this strategy include the following:</span></span>

* <span data-ttu-id="a2861-110">Skrypty SQL można sprawdzić pod kątem dokładności; jest to ważne, ponieważ stosowanie zmian schematu do produkcyjnych baz danych jest potencjalnie niebezpieczną operacją, która może wiązać się z utratą danych.</span><span class="sxs-lookup"><span data-stu-id="a2861-110">SQL scripts can be reviewed for accuracy; this is important since applying schema changes to production databases is a potentially dangerous operation that could involve data loss.</span></span>
* <span data-ttu-id="a2861-111">W niektórych przypadkach skrypty można dostrajać w celu dopasowania do konkretnych potrzeb produkcyjnej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="a2861-111">In some cases, the scripts can be tuned to fit the specific needs of a production database.</span></span>
* <span data-ttu-id="a2861-112">Skrypty SQL mogą być używane w połączeniu z technologią wdrażania i mogą być nawet generowane jako część procesu CI.</span><span class="sxs-lookup"><span data-stu-id="a2861-112">SQL scripts can be used in conjunction with a deployment technology, and can even be generated as part of your CI process.</span></span>
* <span data-ttu-id="a2861-113">Skrypty SQL mogą być udostępniane ADMINISTRATORom i mogą być zarządzane i archiwizowane osobno.</span><span class="sxs-lookup"><span data-stu-id="a2861-113">SQL scripts can be provided to a DBA, and can be managed and archived separately.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="a2861-114">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="a2861-114">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

#### <a name="basic-usage"></a><span data-ttu-id="a2861-115">Podstawowe użycie</span><span class="sxs-lookup"><span data-stu-id="a2861-115">Basic Usage</span></span>

<span data-ttu-id="a2861-116">Następujące generuje skrypt SQL z pustej bazy danych do najnowszej migracji:</span><span class="sxs-lookup"><span data-stu-id="a2861-116">The following generates a SQL script from a blank database to the latest migration:</span></span>

```dotnetcli
dotnet ef migrations script
```

#### <a name="with-from-to-implied"></a><span data-ttu-id="a2861-117">Z opcją od (do implikowanego)</span><span class="sxs-lookup"><span data-stu-id="a2861-117">With From (to implied)</span></span>

<span data-ttu-id="a2861-118">Poniższy proces generuje skrypt SQL z danej migracji do najnowszej migracji.</span><span class="sxs-lookup"><span data-stu-id="a2861-118">The following generates a SQL script from the given migration to the latest migration.</span></span>

```dotnetcli
dotnet ef migrations script AddNewTables
```

#### <a name="with-from-and-to"></a><span data-ttu-id="a2861-119">Z i do</span><span class="sxs-lookup"><span data-stu-id="a2861-119">With From and To</span></span>

<span data-ttu-id="a2861-120">Poniższy proces generuje skrypt SQL z określonej `from` migracji do określonej `to` migracji.</span><span class="sxs-lookup"><span data-stu-id="a2861-120">The following generates a SQL script from the specified `from` migration to the specified `to` migration.</span></span>

```dotnetcli
dotnet ef migrations script AddNewTables AddAuditTable
```

<span data-ttu-id="a2861-121">Możesz użyć `from` , która jest nowsza niż `to` w celu wygenerowania skryptu wycofywania.</span><span class="sxs-lookup"><span data-stu-id="a2861-121">You can use a `from` that is newer than the `to` in order to generate a rollback script.</span></span>

> [!WARNING]
> <span data-ttu-id="a2861-122">Weź pod uwagę potencjalne scenariusze utraty danych.</span><span class="sxs-lookup"><span data-stu-id="a2861-122">Please take note of potential data loss scenarios.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="a2861-123">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a2861-123">Visual Studio</span></span>](#tab/vs)

#### <a name="basic-usage"></a><span data-ttu-id="a2861-124">Podstawowe użycie</span><span class="sxs-lookup"><span data-stu-id="a2861-124">Basic Usage</span></span>

<span data-ttu-id="a2861-125">Następujące generuje skrypt SQL z pustej bazy danych do najnowszej migracji:</span><span class="sxs-lookup"><span data-stu-id="a2861-125">The following generates a SQL script from a blank database to the latest migration:</span></span>

``` powershell
Script-Migration
```

#### <a name="with-from-to-implied"></a><span data-ttu-id="a2861-126">Z opcją od (do implikowanego)</span><span class="sxs-lookup"><span data-stu-id="a2861-126">With From (to implied)</span></span>

<span data-ttu-id="a2861-127">Poniższy proces generuje skrypt SQL z danej migracji do najnowszej migracji.</span><span class="sxs-lookup"><span data-stu-id="a2861-127">The following generates a SQL script from the given migration to the latest migration.</span></span>

```powershell
Script-Migration AddNewTables
```

#### <a name="with-from-and-to"></a><span data-ttu-id="a2861-128">Z i do</span><span class="sxs-lookup"><span data-stu-id="a2861-128">With From and To</span></span>

<span data-ttu-id="a2861-129">Poniższy proces generuje skrypt SQL z określonej `from` migracji do określonej `to` migracji.</span><span class="sxs-lookup"><span data-stu-id="a2861-129">The following generates a SQL script from the specified `from` migration to the specified `to` migration.</span></span>

```powershell
Script-Migration AddNewTables AddAuditTable
```
<span data-ttu-id="a2861-130">Możesz użyć `from` , która jest nowsza niż `to` w celu wygenerowania skryptu wycofywania.</span><span class="sxs-lookup"><span data-stu-id="a2861-130">You can use a `from` that is newer than the `to` in order to generate a rollback script.</span></span> <span data-ttu-id="a2861-131">*Weź pod uwagę potencjalne scenariusze utraty danych.*</span><span class="sxs-lookup"><span data-stu-id="a2861-131">*Please take note of potential data loss scenarios.*</span></span>

***

<span data-ttu-id="a2861-132">Generowanie skryptu akceptuje następujące dwa argumenty w polu wskaż, który zakres migracji powinien zostać wygenerowany:</span><span class="sxs-lookup"><span data-stu-id="a2861-132">Script generation accepts the following two arguments in indicate which range of migrations should be generated:</span></span>

* <span data-ttu-id="a2861-133">Migracja **z** migracji powinna być ostatnią zastosowana do bazy danych przed uruchomieniem skryptu.</span><span class="sxs-lookup"><span data-stu-id="a2861-133">The **from** migration should be the last migration applied to the database before running the script.</span></span> <span data-ttu-id="a2861-134">Jeśli nie zastosowano żadnych migracji, określ `0` (jest to ustawienie domyślne).</span><span class="sxs-lookup"><span data-stu-id="a2861-134">If no migrations have been applied, specify `0` (this is the default).</span></span>
* <span data-ttu-id="a2861-135">Migracja **do** migracji to Ostatnia migracja, która zostanie zastosowana do bazy danych po uruchomieniu skryptu.</span><span class="sxs-lookup"><span data-stu-id="a2861-135">The **to** migration is the last migration that will be applied to the database after running the script.</span></span> <span data-ttu-id="a2861-136">Ta wartość domyślna to Ostatnia migracja w projekcie.</span><span class="sxs-lookup"><span data-stu-id="a2861-136">This defaults to the last migration in your project.</span></span>

## <a name="idempotent-sql-scripts"></a><span data-ttu-id="a2861-137">Idempotentne skrypty SQL</span><span class="sxs-lookup"><span data-stu-id="a2861-137">Idempotent SQL scripts</span></span>

<span data-ttu-id="a2861-138">Utworzone powyżej skrypty SQL mogą być stosowane tylko w celu zmiany schematu z jednej migracji na inną; odpowiedzialność za odpowiednie zastosowanie skryptu i tylko do bazy danych w poprawnym stanie migracji.</span><span class="sxs-lookup"><span data-stu-id="a2861-138">The SQL scripts generated above can only be applied to change your schema from one migration to another; it is your responsibility to apply the script appropriately, and only to database in the correct migration state.</span></span> <span data-ttu-id="a2861-139">EF Core obsługuje również generowanie skryptów **idempotentne** , które wewnętrznie sprawdzają, które migracje zostały już zastosowane (za pośrednictwem tabeli historii migracji) i mają zastosowanie tylko brakujące.</span><span class="sxs-lookup"><span data-stu-id="a2861-139">EF Core also supports generating **idempotent** scripts, which internally check which migrations have already been applied (via the migrations history table), and only apply missing ones.</span></span> <span data-ttu-id="a2861-140">Jest to przydatne, jeśli nie wiesz dokładnie, jak Ostatnia migracja została zastosowana do bazy danych, lub Jeśli wdrażasz w wielu bazach danych, które mogą znajdować się w innej migracji.</span><span class="sxs-lookup"><span data-stu-id="a2861-140">This is useful if you don't exactly know what the last migration applied to the database was, or if you are deploying to multiple databases that may each be at a different migration.</span></span>

<span data-ttu-id="a2861-141">Następujące generowanie idempotentne migracji:</span><span class="sxs-lookup"><span data-stu-id="a2861-141">The following generates idempotent migrations:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="a2861-142">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="a2861-142">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations script --idempotent
```

#### <a name="visual-studio"></a>[<span data-ttu-id="a2861-143">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a2861-143">Visual Studio</span></span>](#tab/vs)

``` powershell
Script-Migration -Idempotent
```

***

## <a name="command-line-tools"></a><span data-ttu-id="a2861-144">Narzędzia wiersza polecenia</span><span class="sxs-lookup"><span data-stu-id="a2861-144">Command-line tools</span></span>

<span data-ttu-id="a2861-145">Narzędzia wiersza polecenia EF mogą służyć do zastosowania migracji do bazy danych programu.</span><span class="sxs-lookup"><span data-stu-id="a2861-145">The EF command-line tools can be used to apply migrations to a database.</span></span> <span data-ttu-id="a2861-146">Podczas pracy w przypadku lokalnego tworzenia i testowania migracji takie podejście nie jest idealnym rozwiązaniem do zarządzania produkcyjnymi bazami danych:</span><span class="sxs-lookup"><span data-stu-id="a2861-146">While productive for local development and testing of migrations, this approach isn't ideal for managing production databases:</span></span>

* <span data-ttu-id="a2861-147">Polecenia SQL są stosowane bezpośrednio przez narzędzie, bez nadawania deweloperowi zmiany w celu ich sprawdzenia lub zmodyfikowania.</span><span class="sxs-lookup"><span data-stu-id="a2861-147">The SQL commands are applied directly by the tool, without giving the developer a change to inspect or modify them.</span></span> <span data-ttu-id="a2861-148">Może to być niebezpieczne w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="a2861-148">This can be dangerous in a production environment.</span></span>
* <span data-ttu-id="a2861-149">Zestaw .NET SDK i narzędzie EF muszą być zainstalowane na serwerach produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="a2861-149">The .NET SDK and the EF tool must be installed on production servers.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="a2861-150">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="a2861-150">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

<span data-ttu-id="a2861-151">Następująca aktualizacja bazy danych do najnowszej migracji:</span><span class="sxs-lookup"><span data-stu-id="a2861-151">The following updates your database to the latest migration:</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="a2861-152">Następujące czynności umożliwiają zaktualizowanie bazy danych do danej migracji:</span><span class="sxs-lookup"><span data-stu-id="a2861-152">The following updates your database to a given migration:</span></span>

```dotnetcli
dotnet ef database update AddNewTables
```

<span data-ttu-id="a2861-153">Należy zauważyć, że może to służyć do przywrócenia wcześniejszej migracji.</span><span class="sxs-lookup"><span data-stu-id="a2861-153">Note that this can be used to roll back to an earlier migration as well.</span></span>

> [!WARNING]
> <span data-ttu-id="a2861-154">Weź pod uwagę potencjalne scenariusze utraty danych.</span><span class="sxs-lookup"><span data-stu-id="a2861-154">Please take note of potential data loss scenarios.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="a2861-155">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a2861-155">Visual Studio</span></span>](#tab/vs)

<span data-ttu-id="a2861-156">Następująca aktualizacja bazy danych do najnowszej migracji:</span><span class="sxs-lookup"><span data-stu-id="a2861-156">The following updates your database to the latest migration:</span></span>

``` powershell
Update-Database
```

<span data-ttu-id="a2861-157">Następujące czynności umożliwiają zaktualizowanie bazy danych do danej migracji:</span><span class="sxs-lookup"><span data-stu-id="a2861-157">The following updates your database to a given migration:</span></span>

``` powershell
Update-Database AddNewTables
```

<span data-ttu-id="a2861-158">Należy zauważyć, że może to służyć do przywrócenia wcześniejszej migracji.</span><span class="sxs-lookup"><span data-stu-id="a2861-158">Note that this can be used to roll back to an earlier migration as well.</span></span>

> [!WARNING]
> <span data-ttu-id="a2861-159">Weź pod uwagę potencjalne scenariusze utraty danych.</span><span class="sxs-lookup"><span data-stu-id="a2861-159">Please take note of potential data loss scenarios.</span></span>

***

<span data-ttu-id="a2861-160">Aby uzyskać więcej informacji na temat stosowania migracji za pomocą narzędzi wiersza polecenia, zobacz [informacje dotyczące narzędzi EF Core Tools](xref:core/miscellaneous/cli/index).</span><span class="sxs-lookup"><span data-stu-id="a2861-160">For more information on applying migrations via the command-line tools, see the [EF Core tools reference](xref:core/miscellaneous/cli/index).</span></span>

## <a name="apply-migrations-at-runtime"></a><span data-ttu-id="a2861-161">Zastosuj migracje w czasie wykonywania</span><span class="sxs-lookup"><span data-stu-id="a2861-161">Apply migrations at runtime</span></span>

<span data-ttu-id="a2861-162">Istnieje możliwość programistycznego zastosowania migracji przez samą aplikację, zazwyczaj podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="a2861-162">It's possible for the application itself to apply migrations programmatically, typically during startup.</span></span> <span data-ttu-id="a2861-163">Podczas pracy w przypadku lokalnego tworzenia i testowania migracji takie podejście jest nieodpowiednie do zarządzania produkcyjnymi bazami danych, z następujących powodów:</span><span class="sxs-lookup"><span data-stu-id="a2861-163">While productive for local development and testing of migrations, this approach is inappropriate for managing production databases, for the following reasons:</span></span>

* <span data-ttu-id="a2861-164">W przypadku uruchomienia wielu wystąpień aplikacji obie aplikacje mogą próbować zastosować migrację współbieżnie i niepowodzeniem (lub gorszą, spowodować uszkodzenie danych).</span><span class="sxs-lookup"><span data-stu-id="a2861-164">If multiple instances of your application are running, both applications could attempt to apply the migration concurrently and fail (or worse, cause data corruption).</span></span>
* <span data-ttu-id="a2861-165">Podobnie, jeśli aplikacja uzyskuje dostęp do bazy danych podczas migrowania jej przez inną aplikację, może to spowodować poważne problemy.</span><span class="sxs-lookup"><span data-stu-id="a2861-165">Similarly, if an application is accessing the database while another application migrates it, this can cause severe issues.</span></span>
* <span data-ttu-id="a2861-166">Aplikacja musi mieć podwyższony poziom dostępu, aby modyfikować schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="a2861-166">The application must have elevated access to modify the database schema.</span></span> <span data-ttu-id="a2861-167">Zwykle dobrym sposobem jest ograniczenie uprawnień bazy danych aplikacji w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="a2861-167">It's generally good practice to limit the application's database permissions in production.</span></span>
* <span data-ttu-id="a2861-168">Ważne jest, aby móc wycofać zastosowana migracja w przypadku problemu.</span><span class="sxs-lookup"><span data-stu-id="a2861-168">It's important to be able to roll back an applied migration in case of an issue.</span></span> <span data-ttu-id="a2861-169">Inne strategie zapewniają to łatwo i z nich.</span><span class="sxs-lookup"><span data-stu-id="a2861-169">The other strategies provide this easily and out of the box.</span></span>
* <span data-ttu-id="a2861-170">Polecenia SQL są stosowane bezpośrednio przez program bez nadawania deweloperowi zmiany w celu ich sprawdzenia lub zmodyfikowania.</span><span class="sxs-lookup"><span data-stu-id="a2861-170">The SQL commands are applied directly by the program, without giving the developer a change to inspect or modify them.</span></span> <span data-ttu-id="a2861-171">Może to być niebezpieczne w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="a2861-171">This can be dangerous in a production environment.</span></span>

<span data-ttu-id="a2861-172">Aby programowo zastosować migracje, wywołaj polecenie `context.Database.Migrate()` .</span><span class="sxs-lookup"><span data-stu-id="a2861-172">To apply migrations programmatically, call `context.Database.Migrate()`.</span></span> <span data-ttu-id="a2861-173">Na przykład typowa aplikacja ASP.NET może wykonać następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="a2861-173">For example, a typical ASP.NET application can do the following:</span></span>

```c#
public static void Main(string[] args)
{
    var host = CreateHostBuilder(args).Build();

    using (var scope = host.Services.CreateScope())
    {
        var db = scope.ServiceProvider.GetRequiredService<ApplicationDbContext>();
        db.Database.Migrate();
    }

    host.Run();
}
```

<span data-ttu-id="a2861-174">Należy pamiętać, że `Migrate()` kompilacja jest oparta na `IMigrator` usłudze, która może być używana w bardziej zaawansowanych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="a2861-174">Note that `Migrate()` builds on top of the `IMigrator` service, which can be used for more advanced scenarios.</span></span> <span data-ttu-id="a2861-175">Użyj `myDbContext.GetInfrastructure().GetService<IMigrator>()` , aby uzyskać do niej dostęp.</span><span class="sxs-lookup"><span data-stu-id="a2861-175">Use `myDbContext.GetInfrastructure().GetService<IMigrator>()` to access it.</span></span>

> [!WARNING]
>
> * <span data-ttu-id="a2861-176">Należy uważnie rozważyć przed rozpoczęciem korzystania z tej metody w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="a2861-176">Carefully consider before using this approach in production.</span></span> <span data-ttu-id="a2861-177">Środowisko pokazuje, że prostota tej strategii wdrażania jest przeważona przez tworzone przez niego problemy.</span><span class="sxs-lookup"><span data-stu-id="a2861-177">Experience has shown that the simplicity of this deployment strategy is outweighed by the issues it creates.</span></span> <span data-ttu-id="a2861-178">Zamiast tego Rozważ użycie skryptów SQL.</span><span class="sxs-lookup"><span data-stu-id="a2861-178">Consider using SQL scripts instead.</span></span>
> * <span data-ttu-id="a2861-179">Nie wywołuj `EnsureCreated()` przed `Migrate()` .</span><span class="sxs-lookup"><span data-stu-id="a2861-179">Don't call `EnsureCreated()` before `Migrate()`.</span></span> <span data-ttu-id="a2861-180">`EnsureCreated()`pomija migracje, aby utworzyć schemat, co powoduje `Migrate()` Niepowodzenie.</span><span class="sxs-lookup"><span data-stu-id="a2861-180">`EnsureCreated()` bypasses Migrations to create the schema, which causes `Migrate()` to fail.</span></span>
