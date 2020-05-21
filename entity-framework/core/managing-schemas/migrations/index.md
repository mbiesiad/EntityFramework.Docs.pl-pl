---
title: Migracje — EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/05/2018
uid: core/managing-schemas/migrations/index
ms.openlocfilehash: c87864b3430d3cd42729c13ddde33c0cd9de9308
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672986"
---
# <a name="migrations"></a><span data-ttu-id="fb4d1-102">Migracje</span><span class="sxs-lookup"><span data-stu-id="fb4d1-102">Migrations</span></span>

<span data-ttu-id="fb4d1-103">Model danych zmienia się podczas opracowywania i nie jest synchronizowany z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-103">A data model changes during development and gets out of sync with the database.</span></span> <span data-ttu-id="fb4d1-104">Możesz porzucić bazę danych i pozwolić, aby EF utworzyły nową, zgodną z modelem, ale ta procedura powoduje utratę danych.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-104">You can drop the database and let EF create a new one that matches the model, but this procedure results in the loss of data.</span></span> <span data-ttu-id="fb4d1-105">Funkcja migracji w EF Core zapewnia sposób stopniowego aktualizowania schematu bazy danych, aby zachować synchronizację z modelem danych aplikacji przy zachowaniu istniejących danych w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-105">The migrations feature in EF Core provides a way to incrementally update the database schema to keep it in sync with the application's data model while preserving existing data in the database.</span></span>

<span data-ttu-id="fb4d1-106">Migracje obejmują narzędzia wiersza polecenia i interfejsy API, które ułatwiają wykonywanie następujących zadań:</span><span class="sxs-lookup"><span data-stu-id="fb4d1-106">Migrations includes command-line tools and APIs that help with the following tasks:</span></span>

* <span data-ttu-id="fb4d1-107">[Tworzenie migracji](#create-a-migration).</span><span class="sxs-lookup"><span data-stu-id="fb4d1-107">[Create a migration](#create-a-migration).</span></span> <span data-ttu-id="fb4d1-108">Generuj kod, który może aktualizować bazę danych w celu zsynchronizowania jej z zestawem zmian modelu.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-108">Generate code that can update the database to sync it with a set of model changes.</span></span>
* <span data-ttu-id="fb4d1-109">[Zaktualizuj bazę danych](#update-the-database).</span><span class="sxs-lookup"><span data-stu-id="fb4d1-109">[Update the database](#update-the-database).</span></span> <span data-ttu-id="fb4d1-110">Zastosuj oczekujące migracje, aby zaktualizować schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-110">Apply pending migrations to update the database schema.</span></span>
* <span data-ttu-id="fb4d1-111">[Dostosuj kod migracji](#customize-migration-code).</span><span class="sxs-lookup"><span data-stu-id="fb4d1-111">[Customize migration code](#customize-migration-code).</span></span> <span data-ttu-id="fb4d1-112">Czasami wygenerowany kod musi być modyfikowany lub uzupełniany.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-112">Sometimes the generated code needs to be modified or supplemented.</span></span>
* <span data-ttu-id="fb4d1-113">[Usuń migrację](#remove-a-migration).</span><span class="sxs-lookup"><span data-stu-id="fb4d1-113">[Remove a migration](#remove-a-migration).</span></span> <span data-ttu-id="fb4d1-114">Usuń wygenerowany kod.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-114">Delete the generated code.</span></span>
* <span data-ttu-id="fb4d1-115">[Przywrócenie migracji](#revert-a-migration).</span><span class="sxs-lookup"><span data-stu-id="fb4d1-115">[Revert a migration](#revert-a-migration).</span></span> <span data-ttu-id="fb4d1-116">Cofnij zmiany w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-116">Undo the database changes.</span></span>
* <span data-ttu-id="fb4d1-117">[Generuj skrypty SQL](#generate-sql-scripts).</span><span class="sxs-lookup"><span data-stu-id="fb4d1-117">[Generate SQL scripts](#generate-sql-scripts).</span></span> <span data-ttu-id="fb4d1-118">Może być potrzebny skrypt do zaktualizowania produkcyjnej bazy danych lub rozwiązywania problemów z kodem migracji.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-118">You might need a script to update a production database or to troubleshoot migration code.</span></span>
* <span data-ttu-id="fb4d1-119">[Zastosuj migracje w czasie wykonywania](#apply-migrations-at-runtime).</span><span class="sxs-lookup"><span data-stu-id="fb4d1-119">[Apply migrations at runtime](#apply-migrations-at-runtime).</span></span> <span data-ttu-id="fb4d1-120">W przypadku aktualizacji i uruchamiania skryptów w czasie projektowania nie są najlepszym rozwiązaniem, należy wywołać `Migrate()` metodę.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-120">When design-time updates and running scripts aren't the best options, call the `Migrate()` method.</span></span>

> [!TIP]
> <span data-ttu-id="fb4d1-121">Jeśli `DbContext` znajduje się w innym zestawie niż projekt startowy, można jawnie określić projekty docelowe i uruchomieniowe w [narzędziu Konsola Menedżera pakietów](xref:core/miscellaneous/cli/powershell#target-and-startup-project) lub [Narzędzia interfejs wiersza polecenia platformy .NET Core](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project).</span><span class="sxs-lookup"><span data-stu-id="fb4d1-121">If the `DbContext` is in a different assembly than the startup project, you can explicitly specify the target and startup projects in either the [Package Manager Console tools](xref:core/miscellaneous/cli/powershell#target-and-startup-project) or the [.NET Core CLI tools](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project).</span></span>

## <a name="install-the-tools"></a><span data-ttu-id="fb4d1-122">Instalowanie narzędzi</span><span class="sxs-lookup"><span data-stu-id="fb4d1-122">Install the tools</span></span>

<span data-ttu-id="fb4d1-123">Zainstaluj [narzędzia wiersza polecenia](xref:core/miscellaneous/cli/index):</span><span class="sxs-lookup"><span data-stu-id="fb4d1-123">Install the [command-line tools](xref:core/miscellaneous/cli/index):</span></span>

* <span data-ttu-id="fb4d1-124">W przypadku programu Visual Studio zalecamy korzystanie z [narzędzi konsoli Menedżera pakietów](xref:core/miscellaneous/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="fb4d1-124">For Visual Studio, we recommend the [Package Manager Console tools](xref:core/miscellaneous/cli/powershell).</span></span>
* <span data-ttu-id="fb4d1-125">W przypadku innych środowisk programistycznych wybierz [narzędzia interfejs wiersza polecenia platformy .NET Core](xref:core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="fb4d1-125">For other development environments, choose the [.NET Core CLI tools](xref:core/miscellaneous/cli/dotnet).</span></span>

## <a name="create-a-migration"></a><span data-ttu-id="fb4d1-126">Tworzenie migracji</span><span class="sxs-lookup"><span data-stu-id="fb4d1-126">Create a migration</span></span>

<span data-ttu-id="fb4d1-127">Po [zdefiniowaniu modelu początkowego](xref:core/modeling/index)należy utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-127">After you've [defined your initial model](xref:core/modeling/index), it's time to create the database.</span></span> <span data-ttu-id="fb4d1-128">Aby dodać początkową migrację, uruchom następujące polecenie.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-128">To add an initial migration, run the following command.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="fb4d1-129">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="fb4d1-129">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate
```

### <a name="visual-studio"></a>[<span data-ttu-id="fb4d1-130">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fb4d1-130">Visual Studio</span></span>](#tab/vs)

``` powershell
Add-Migration InitialCreate
```

***

<span data-ttu-id="fb4d1-131">Do projektu są dodawane trzy pliki w katalogu **migracji** :</span><span class="sxs-lookup"><span data-stu-id="fb4d1-131">Three files are added to your project under the **Migrations** directory:</span></span>

* <span data-ttu-id="fb4d1-132">**XXXXXXXXXXXXXX_InitialCreate. cs**— główny plik migracji.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-132">**XXXXXXXXXXXXXX_InitialCreate.cs**--The main migrations file.</span></span> <span data-ttu-id="fb4d1-133">Zawiera operacje niezbędne do zastosowania migracji (w programie `Up()` ) i przywrócenia jej (w programie `Down()` ).</span><span class="sxs-lookup"><span data-stu-id="fb4d1-133">Contains the operations necessary to apply the migration (in `Up()`) and to revert it (in `Down()`).</span></span>
* <span data-ttu-id="fb4d1-134">**XXXXXXXXXXXXXX_InitialCreate. Designer. cs**— plik metadanych migracji.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-134">**XXXXXXXXXXXXXX_InitialCreate.Designer.cs**--The migrations metadata file.</span></span> <span data-ttu-id="fb4d1-135">Zawiera informacje używane przez EF.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-135">Contains information used by EF.</span></span>
* <span data-ttu-id="fb4d1-136">**MyContextModelSnapshot.cs**— migawka bieżącego modelu.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-136">**MyContextModelSnapshot.cs**--A snapshot of your current model.</span></span> <span data-ttu-id="fb4d1-137">Służy do określania, co zmieniło się podczas dodawania następnej migracji.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-137">Used to determine what changed when adding the next migration.</span></span>

<span data-ttu-id="fb4d1-138">Sygnatura czasowa w nazwie pliku pomaga zachować ich uporządkowane chronologicznie, aby zobaczyć postęp zmian.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-138">The timestamp in the filename helps keep them ordered chronologically so you can see the progression of changes.</span></span>

### <a name="namespaces"></a><span data-ttu-id="fb4d1-139">Przestrzenie nazw</span><span class="sxs-lookup"><span data-stu-id="fb4d1-139">Namespaces</span></span>

<span data-ttu-id="fb4d1-140">Możesz przenieść pliki migracji i ręcznie zmienić ich przestrzeń nazw.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-140">You are free to move Migrations files and change their namespace manually.</span></span> <span data-ttu-id="fb4d1-141">Nowe migracje są tworzone jako elementy równorzędne ostatniej migracji.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-141">New migrations are created as siblings of the last migration.</span></span>

<span data-ttu-id="fb4d1-142">Alternatywnie możesz użyć `-Namespace` (konsola Menedżera pakietów) lub `--namespace` (interfejs wiersza polecenia platformy .NET Core), aby określić przestrzeń nazw podczas generowania.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-142">Alternatively you can use `-Namespace` (Package Manager Console) or `--namespace` (.NET Core CLI) to specify the namespace at generation time.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="fb4d1-143">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="fb4d1-143">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --namespace Your.Namespace
```

### <a name="visual-studio"></a>[<span data-ttu-id="fb4d1-144">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fb4d1-144">Visual Studio</span></span>](#tab/vs)

``` powershell
Add-Migration InitialCreate -Namespace Your.Namespace
```

***

## <a name="update-the-database"></a><span data-ttu-id="fb4d1-145">Aktualizowanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="fb4d1-145">Update the database</span></span>

<span data-ttu-id="fb4d1-146">Następnie Zastosuj migrację do bazy danych, aby utworzyć schemat.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-146">Next, apply the migration to the database to create the schema.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="fb4d1-147">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="fb4d1-147">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```

### <a name="visual-studio"></a>[<span data-ttu-id="fb4d1-148">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fb4d1-148">Visual Studio</span></span>](#tab/vs)

``` powershell
Update-Database
```

***

## <a name="customize-migration-code"></a><span data-ttu-id="fb4d1-149">Dostosowywanie kodu migracji</span><span class="sxs-lookup"><span data-stu-id="fb4d1-149">Customize migration code</span></span>

<span data-ttu-id="fb4d1-150">Po wprowadzeniu zmian w modelu EF Core schemat bazy danych może nie być zsynchronizowany. Aby zapewnić aktualność, Dodaj kolejną migrację.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-150">After making changes to your EF Core model, the database schema might be out of sync. To bring it up to date, add another migration.</span></span> <span data-ttu-id="fb4d1-151">Nazwa migracji może być używana jak komunikat zatwierdzenia w systemie kontroli wersji.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-151">The migration name can be used like a commit message in a version control system.</span></span> <span data-ttu-id="fb4d1-152">Na przykład można wybrać nazwę, np. *AddProductReviews* , jeśli zmiana jest nową klasą jednostek do przeglądu.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-152">For example, you might choose a name like *AddProductReviews* if the change is a new entity class for reviews.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="fb4d1-153">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="fb4d1-153">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddProductReviews
```

### <a name="visual-studio"></a>[<span data-ttu-id="fb4d1-154">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fb4d1-154">Visual Studio</span></span>](#tab/vs)

``` powershell
Add-Migration AddProductReviews
```

***

<span data-ttu-id="fb4d1-155">Po utworzeniu szkieletowej migracji (kod wygenerowany dla niego), Przejrzyj kod pod kątem dokładności i Dodaj, Usuń lub zmodyfikuj wszystkie operacje wymagane do poprawnego zastosowania.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-155">Once the migration is scaffolded (code generated for it), review the code for accuracy and add, remove or modify any operations required to apply it correctly.</span></span>

<span data-ttu-id="fb4d1-156">Na przykład migracja może zawierać następujące operacje:</span><span class="sxs-lookup"><span data-stu-id="fb4d1-156">For example, a migration might contain the following operations:</span></span>

``` csharp
migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");

migrationBuilder.AddColumn<string>(
    name: "Name",
    table: "Customer",
    nullable: true);
```

<span data-ttu-id="fb4d1-157">Chociaż te operacje sprawiają, że schemat bazy danych jest zgodny, nie zachowuje istniejących nazw klientów.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-157">While these operations make the database schema compatible, they don't preserve the existing customer names.</span></span> <span data-ttu-id="fb4d1-158">Aby go ulepszyć, napisz ponownie w następujący sposób.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-158">To make it better, rewrite it as follows.</span></span>

``` csharp
migrationBuilder.AddColumn<string>(
    name: "Name",
    table: "Customer",
    nullable: true);

migrationBuilder.Sql(
@"
    UPDATE Customer
    SET Name = FirstName + ' ' + LastName;
");

migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");
```

> [!TIP]
> <span data-ttu-id="fb4d1-159">Proces tworzenia szkieletu migracji ostrzega, gdy operacja może spowodować utratę danych (np. upuszczenie kolumny).</span><span class="sxs-lookup"><span data-stu-id="fb4d1-159">The migration scaffolding process warns when an operation might result in data loss (like dropping a column).</span></span> <span data-ttu-id="fb4d1-160">Jeśli widzisz to ostrzeżenie, pamiętaj o tym, aby sprawdzić poprawność kodu migracji.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-160">If you see that warning, be especially sure to review the migrations code for accuracy.</span></span>

<span data-ttu-id="fb4d1-161">Zastosuj migrację do bazy danych przy użyciu odpowiedniego polecenia.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-161">Apply the migration to the database using the appropriate command.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="fb4d1-162">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="fb4d1-162">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```

### <a name="visual-studio"></a>[<span data-ttu-id="fb4d1-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fb4d1-163">Visual Studio</span></span>](#tab/vs)

``` powershell
Update-Database
```

***

### <a name="empty-migrations"></a><span data-ttu-id="fb4d1-164">Puste migracje</span><span class="sxs-lookup"><span data-stu-id="fb4d1-164">Empty migrations</span></span>

<span data-ttu-id="fb4d1-165">Czasami warto dodać migrację bez wprowadzania żadnych zmian modelu.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-165">Sometimes it's useful to add a migration without making any model changes.</span></span> <span data-ttu-id="fb4d1-166">W takim przypadku dodanie nowej migracji powoduje utworzenie plików kodu z pustymi klasami.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-166">In this case, adding a new migration creates code files with empty classes.</span></span> <span data-ttu-id="fb4d1-167">Można dostosować tę migrację do wykonywania operacji, które nie są bezpośrednio powiązane z modelem EF Core.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-167">You can customize this migration to perform operations that don't directly relate to the EF Core model.</span></span> <span data-ttu-id="fb4d1-168">W ten sposób możesz chcieć zarządzać tymi elementami:</span><span class="sxs-lookup"><span data-stu-id="fb4d1-168">Some things you might want to manage this way are:</span></span>

* <span data-ttu-id="fb4d1-169">Wyszukiwanie pełnotekstowe</span><span class="sxs-lookup"><span data-stu-id="fb4d1-169">Full-Text Search</span></span>
* <span data-ttu-id="fb4d1-170">Funkcje</span><span class="sxs-lookup"><span data-stu-id="fb4d1-170">Functions</span></span>
* <span data-ttu-id="fb4d1-171">Procedury składowane</span><span class="sxs-lookup"><span data-stu-id="fb4d1-171">Stored procedures</span></span>
* <span data-ttu-id="fb4d1-172">Wyzwalacze</span><span class="sxs-lookup"><span data-stu-id="fb4d1-172">Triggers</span></span>
* <span data-ttu-id="fb4d1-173">Widoki</span><span class="sxs-lookup"><span data-stu-id="fb4d1-173">Views</span></span>

## <a name="remove-a-migration"></a><span data-ttu-id="fb4d1-174">Usuń migrację</span><span class="sxs-lookup"><span data-stu-id="fb4d1-174">Remove a migration</span></span>

<span data-ttu-id="fb4d1-175">Czasami należy dodać migrację i zdawać sobie sprawę, że należy wprowadzić dodatkowe zmiany w modelu EF Core przed ich zastosowaniem.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-175">Sometimes you add a migration and realize you need to make additional changes to your EF Core model before applying it.</span></span> <span data-ttu-id="fb4d1-176">Aby usunąć ostatnią migrację, użyj tego polecenia.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-176">To remove the last migration, use this command.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="fb4d1-177">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="fb4d1-177">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations remove
```

### <a name="visual-studio"></a>[<span data-ttu-id="fb4d1-178">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fb4d1-178">Visual Studio</span></span>](#tab/vs)

``` powershell
Remove-Migration
```

***

<span data-ttu-id="fb4d1-179">Po usunięciu migracji możesz wprowadzić dodatkowe zmiany modelu i dodać je ponownie.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-179">After removing the migration, you can make the additional model changes and add it again.</span></span>

## <a name="revert-a-migration"></a><span data-ttu-id="fb4d1-180">Przywracanie migracji</span><span class="sxs-lookup"><span data-stu-id="fb4d1-180">Revert a migration</span></span>

<span data-ttu-id="fb4d1-181">Jeśli migracja (lub kilka migracji) została już zastosowana do bazy danych, ale konieczne jest jej przywrócenie, można użyć tego samego polecenia do zastosowania migracji, ale określić nazwę migracji, do której chcesz przeprowadzić przywracanie.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-181">If you already applied a migration (or several migrations) to the database but need to revert it, you can use the same command to apply migrations, but specify the name of the migration you want to roll back to.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="fb4d1-182">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="fb4d1-182">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update LastGoodMigration
```

### <a name="visual-studio"></a>[<span data-ttu-id="fb4d1-183">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fb4d1-183">Visual Studio</span></span>](#tab/vs)

``` powershell
Update-Database LastGoodMigration
```

***

## <a name="generate-sql-scripts"></a><span data-ttu-id="fb4d1-184">Generuj skrypty SQL</span><span class="sxs-lookup"><span data-stu-id="fb4d1-184">Generate SQL scripts</span></span>

<span data-ttu-id="fb4d1-185">Podczas debugowania migracji lub wdrażania ich w produkcyjnej bazie danych warto wygenerować skrypt SQL.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-185">When debugging your migrations or deploying them to a production database, it's useful to generate a SQL script.</span></span> <span data-ttu-id="fb4d1-186">Skrypt może następnie być ponownie przeglądany pod kątem dokładności i dostrojony, aby dopasować potrzeby produkcyjnej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-186">The script can then be further reviewed for accuracy and tuned to fit the needs of a production database.</span></span> <span data-ttu-id="fb4d1-187">Skrypt może być również używany w połączeniu z technologią wdrażania.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-187">The script can also be used in conjunction with a deployment technology.</span></span> <span data-ttu-id="fb4d1-188">Podstawowe polecenie jest następujące.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-188">The basic command is as follows.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="fb4d1-189">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="fb4d1-189">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

#### <a name="basic-usage"></a><span data-ttu-id="fb4d1-190">Podstawowe użycie</span><span class="sxs-lookup"><span data-stu-id="fb4d1-190">Basic Usage</span></span>
```dotnetcli
dotnet ef migrations script
```

#### <a name="with-from-to-implied"></a><span data-ttu-id="fb4d1-191">Z opcją od (do implikowanego)</span><span class="sxs-lookup"><span data-stu-id="fb4d1-191">With From (to implied)</span></span>
<span data-ttu-id="fb4d1-192">Spowoduje to wygenerowanie skryptu SQL z tej migracji do najnowszej migracji.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-192">This will generate a SQL script from this migration to the latest migration.</span></span>
```dotnetcli
dotnet ef migrations script 20190725054716_Add_new_tables
```

#### <a name="with-from-and-to"></a><span data-ttu-id="fb4d1-193">Z i do</span><span class="sxs-lookup"><span data-stu-id="fb4d1-193">With From and To</span></span>
<span data-ttu-id="fb4d1-194">Spowoduje to wygenerowanie skryptu SQL z `from` migracji do określonej `to` migracji.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-194">This will generate a SQL script from the `from` migration to the specified `to` migration.</span></span>
```dotnetcli
dotnet ef migrations script 20190725054716_Add_new_tables 20190829031257_Add_audit_table
```
<span data-ttu-id="fb4d1-195">Możesz użyć `from` , która jest nowsza niż `to` w celu wygenerowania skryptu wycofywania.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-195">You can use a `from` that is newer than the `to` in order to generate a rollback script.</span></span> <span data-ttu-id="fb4d1-196">*Weź pod uwagę potencjalne scenariusze utraty danych.*</span><span class="sxs-lookup"><span data-stu-id="fb4d1-196">*Please take note of potential data loss scenarios.*</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="fb4d1-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fb4d1-197">Visual Studio</span></span>](#tab/vs)

#### <a name="basic-usage"></a><span data-ttu-id="fb4d1-198">Podstawowe użycie</span><span class="sxs-lookup"><span data-stu-id="fb4d1-198">Basic Usage</span></span>
``` powershell
Script-Migration
```

#### <a name="with-from-to-implied"></a><span data-ttu-id="fb4d1-199">Z opcją od (do implikowanego)</span><span class="sxs-lookup"><span data-stu-id="fb4d1-199">With From (to implied)</span></span>
<span data-ttu-id="fb4d1-200">Spowoduje to wygenerowanie skryptu SQL z tej migracji do najnowszej migracji.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-200">This will generate a SQL script from this migration to the latest migration.</span></span>
```powershell
Script-Migration 20190725054716_Add_new_tables
```

#### <a name="with-from-and-to"></a><span data-ttu-id="fb4d1-201">Z i do</span><span class="sxs-lookup"><span data-stu-id="fb4d1-201">With From and To</span></span>
<span data-ttu-id="fb4d1-202">Spowoduje to wygenerowanie skryptu SQL z `from` migracji do określonej `to` migracji.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-202">This will generate a SQL script from the `from` migration to the specified `to` migration.</span></span>
```powershell
Script-Migration 20190725054716_Add_new_tables 20190829031257_Add_audit_table
```
<span data-ttu-id="fb4d1-203">Możesz użyć `from` , która jest nowsza niż `to` w celu wygenerowania skryptu wycofywania.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-203">You can use a `from` that is newer than the `to` in order to generate a rollback script.</span></span> <span data-ttu-id="fb4d1-204">*Weź pod uwagę potencjalne scenariusze utraty danych.*</span><span class="sxs-lookup"><span data-stu-id="fb4d1-204">*Please take note of potential data loss scenarios.*</span></span>

***

<span data-ttu-id="fb4d1-205">Istnieje kilka opcji tego polecenia.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-205">There are several options to this command.</span></span>

<span data-ttu-id="fb4d1-206">Migracja **z** migracji powinna być ostatnią zastosowana do bazy danych przed uruchomieniem skryptu.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-206">The **from** migration should be the last migration applied to the database before running the script.</span></span> <span data-ttu-id="fb4d1-207">Jeśli nie zastosowano żadnych migracji, określ `0` (jest to ustawienie domyślne).</span><span class="sxs-lookup"><span data-stu-id="fb4d1-207">If no migrations have been applied, specify `0` (this is the default).</span></span>

<span data-ttu-id="fb4d1-208">Migracja **do** migracji to Ostatnia migracja, która zostanie zastosowana do bazy danych po uruchomieniu skryptu.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-208">The **to** migration is the last migration that will be applied to the database after running the script.</span></span> <span data-ttu-id="fb4d1-209">Ta wartość domyślna to Ostatnia migracja w projekcie.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-209">This defaults to the last migration in your project.</span></span>

<span data-ttu-id="fb4d1-210">Opcjonalnie można wygenerować skrypt **idempotentne** .</span><span class="sxs-lookup"><span data-stu-id="fb4d1-210">An **idempotent** script can optionally be generated.</span></span> <span data-ttu-id="fb4d1-211">Ten skrypt stosuje tylko migracje, jeśli nie zostały one jeszcze zastosowane do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-211">This script only applies migrations if they haven't already been applied to the database.</span></span> <span data-ttu-id="fb4d1-212">Jest to przydatne, jeśli nie masz dokładnej znajomości ostatniej migracji zastosowanej do bazy danych lub Jeśli wdrażasz ją w wielu bazach danych, które mogą znajdować się w innej migracji.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-212">This is useful if you don't exactly know what the last migration applied to the database was or if you are deploying to multiple databases that may each be at a different migration.</span></span>

## <a name="apply-migrations-at-runtime"></a><span data-ttu-id="fb4d1-213">Zastosuj migracje w czasie wykonywania</span><span class="sxs-lookup"><span data-stu-id="fb4d1-213">Apply migrations at runtime</span></span>

<span data-ttu-id="fb4d1-214">Niektóre aplikacje mogą chcieć zastosować migracje w czasie wykonywania podczas uruchamiania lub pierwszego uruchomienia.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-214">Some apps may want to apply migrations at runtime during startup or first run.</span></span> <span data-ttu-id="fb4d1-215">Zrób to przy użyciu `Migrate()` metody.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-215">Do this using the `Migrate()` method.</span></span>

<span data-ttu-id="fb4d1-216">Ta metoda jest oparta na `IMigrator` usłudze, która może być używana w bardziej zaawansowanych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-216">This method builds on top of the `IMigrator` service, which can be used for more advanced scenarios.</span></span> <span data-ttu-id="fb4d1-217">Użyj `myDbContext.GetInfrastructure().GetService<IMigrator>()` , aby uzyskać do niej dostęp.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-217">Use `myDbContext.GetInfrastructure().GetService<IMigrator>()` to access it.</span></span>

``` csharp
myDbContext.Database.Migrate();
```

> [!WARNING]
>
> * <span data-ttu-id="fb4d1-218">Takie podejście nie jest przeznaczone dla wszystkich użytkowników.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-218">This approach isn't for everyone.</span></span> <span data-ttu-id="fb4d1-219">Chociaż jest to doskonałe rozwiązanie w przypadku aplikacji z lokalną bazą danych, większość aplikacji będzie wymagała bardziej niezawodnej strategii wdrażania, takiej jak generowanie skryptów SQL.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-219">While it's great for apps with a local database, most applications will require more robust deployment strategy like generating SQL scripts.</span></span>
> * <span data-ttu-id="fb4d1-220">Nie wywołuj `EnsureCreated()` przed `Migrate()` .</span><span class="sxs-lookup"><span data-stu-id="fb4d1-220">Don't call `EnsureCreated()` before `Migrate()`.</span></span> <span data-ttu-id="fb4d1-221">`EnsureCreated()`pomija migracje, aby utworzyć schemat, co powoduje `Migrate()` Niepowodzenie.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-221">`EnsureCreated()` bypasses Migrations to create the schema, which causes `Migrate()` to fail.</span></span>

## <a name="next-steps"></a><span data-ttu-id="fb4d1-222">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="fb4d1-222">Next steps</span></span>

<span data-ttu-id="fb4d1-223">Aby uzyskać więcej informacji, zobacz <xref:core/miscellaneous/cli/index>.</span><span class="sxs-lookup"><span data-stu-id="fb4d1-223">For more information, see <xref:core/miscellaneous/cli/index>.</span></span>
