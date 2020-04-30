---
title: Migracje — EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/05/2018
uid: core/managing-schemas/migrations/index
ms.openlocfilehash: 99bb420d95cb86443b63ba05ce9e6b4ab838eff9
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82538455"
---
# <a name="migrations"></a><span data-ttu-id="add5d-102">Migracje</span><span class="sxs-lookup"><span data-stu-id="add5d-102">Migrations</span></span>

<span data-ttu-id="add5d-103">Model danych zmienia się podczas opracowywania i nie jest synchronizowany z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="add5d-103">A data model changes during development and gets out of sync with the database.</span></span> <span data-ttu-id="add5d-104">Możesz porzucić bazę danych i pozwolić, aby EF utworzyły nową, zgodną z modelem, ale ta procedura powoduje utratę danych.</span><span class="sxs-lookup"><span data-stu-id="add5d-104">You can drop the database and let EF create a new one that matches the model, but this procedure results in the loss of data.</span></span> <span data-ttu-id="add5d-105">Funkcja migracji w EF Core zapewnia sposób stopniowego aktualizowania schematu bazy danych, aby zachować synchronizację z modelem danych aplikacji przy zachowaniu istniejących danych w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="add5d-105">The migrations feature in EF Core provides a way to incrementally update the database schema to keep it in sync with the application's data model while preserving existing data in the database.</span></span>

<span data-ttu-id="add5d-106">Migracje obejmują narzędzia wiersza polecenia i interfejsy API, które ułatwiają wykonywanie następujących zadań:</span><span class="sxs-lookup"><span data-stu-id="add5d-106">Migrations includes command-line tools and APIs that help with the following tasks:</span></span>

* <span data-ttu-id="add5d-107">[Tworzenie migracji](#create-a-migration).</span><span class="sxs-lookup"><span data-stu-id="add5d-107">[Create a migration](#create-a-migration).</span></span> <span data-ttu-id="add5d-108">Generuj kod, który może aktualizować bazę danych w celu zsynchronizowania jej z zestawem zmian modelu.</span><span class="sxs-lookup"><span data-stu-id="add5d-108">Generate code that can update the database to sync it with a set of model changes.</span></span>
* <span data-ttu-id="add5d-109">[Zaktualizuj bazę danych](#update-the-database).</span><span class="sxs-lookup"><span data-stu-id="add5d-109">[Update the database](#update-the-database).</span></span> <span data-ttu-id="add5d-110">Zastosuj oczekujące migracje, aby zaktualizować schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="add5d-110">Apply pending migrations to update the database schema.</span></span>
* <span data-ttu-id="add5d-111">[Dostosuj kod migracji](#customize-migration-code).</span><span class="sxs-lookup"><span data-stu-id="add5d-111">[Customize migration code](#customize-migration-code).</span></span> <span data-ttu-id="add5d-112">Czasami wygenerowany kod musi być modyfikowany lub uzupełniany.</span><span class="sxs-lookup"><span data-stu-id="add5d-112">Sometimes the generated code needs to be modified or supplemented.</span></span>
* <span data-ttu-id="add5d-113">[Usuń migrację](#remove-a-migration).</span><span class="sxs-lookup"><span data-stu-id="add5d-113">[Remove a migration](#remove-a-migration).</span></span> <span data-ttu-id="add5d-114">Usuń wygenerowany kod.</span><span class="sxs-lookup"><span data-stu-id="add5d-114">Delete the generated code.</span></span>
* <span data-ttu-id="add5d-115">[Przywrócenie migracji](#revert-a-migration).</span><span class="sxs-lookup"><span data-stu-id="add5d-115">[Revert a migration](#revert-a-migration).</span></span> <span data-ttu-id="add5d-116">Cofnij zmiany w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="add5d-116">Undo the database changes.</span></span>
* <span data-ttu-id="add5d-117">[Generuj skrypty SQL](#generate-sql-scripts).</span><span class="sxs-lookup"><span data-stu-id="add5d-117">[Generate SQL scripts](#generate-sql-scripts).</span></span> <span data-ttu-id="add5d-118">Może być potrzebny skrypt do zaktualizowania produkcyjnej bazy danych lub rozwiązywania problemów z kodem migracji.</span><span class="sxs-lookup"><span data-stu-id="add5d-118">You might need a script to update a production database or to troubleshoot migration code.</span></span>
* <span data-ttu-id="add5d-119">[Zastosuj migracje w czasie wykonywania](#apply-migrations-at-runtime).</span><span class="sxs-lookup"><span data-stu-id="add5d-119">[Apply migrations at runtime](#apply-migrations-at-runtime).</span></span> <span data-ttu-id="add5d-120">W przypadku aktualizacji i uruchamiania skryptów w czasie projektowania nie są najlepszym rozwiązaniem, należy `Migrate()` wywołać metodę.</span><span class="sxs-lookup"><span data-stu-id="add5d-120">When design-time updates and running scripts aren't the best options, call the `Migrate()` method.</span></span>

> [!TIP]
> <span data-ttu-id="add5d-121">Jeśli `DbContext` znajduje się w innym zestawie niż projekt startowy, można jawnie określić projekty docelowe i uruchomieniowe w [narzędziu Konsola Menedżera pakietów](xref:core/miscellaneous/cli/powershell#target-and-startup-project) lub [Narzędzia interfejs wiersza polecenia platformy .NET Core](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project).</span><span class="sxs-lookup"><span data-stu-id="add5d-121">If the `DbContext` is in a different assembly than the startup project, you can explicitly specify the target and startup projects in either the [Package Manager Console tools](xref:core/miscellaneous/cli/powershell#target-and-startup-project) or the [.NET Core CLI tools](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project).</span></span>

## <a name="install-the-tools"></a><span data-ttu-id="add5d-122">Instalowanie narzędzi</span><span class="sxs-lookup"><span data-stu-id="add5d-122">Install the tools</span></span>

<span data-ttu-id="add5d-123">Zainstaluj [narzędzia wiersza polecenia](xref:core/miscellaneous/cli/index):</span><span class="sxs-lookup"><span data-stu-id="add5d-123">Install the [command-line tools](xref:core/miscellaneous/cli/index):</span></span>

* <span data-ttu-id="add5d-124">W przypadku programu Visual Studio zalecamy korzystanie z [narzędzi konsoli Menedżera pakietów](xref:core/miscellaneous/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="add5d-124">For Visual Studio, we recommend the [Package Manager Console tools](xref:core/miscellaneous/cli/powershell).</span></span>
* <span data-ttu-id="add5d-125">W przypadku innych środowisk programistycznych wybierz [narzędzia interfejs wiersza polecenia platformy .NET Core](xref:core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="add5d-125">For other development environments, choose the [.NET Core CLI tools](xref:core/miscellaneous/cli/dotnet).</span></span>

## <a name="create-a-migration"></a><span data-ttu-id="add5d-126">Tworzenie migracji</span><span class="sxs-lookup"><span data-stu-id="add5d-126">Create a migration</span></span>

<span data-ttu-id="add5d-127">Po [zdefiniowaniu modelu początkowego](xref:core/modeling/index)należy utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="add5d-127">After you've [defined your initial model](xref:core/modeling/index), it's time to create the database.</span></span> <span data-ttu-id="add5d-128">Aby dodać początkową migrację, uruchom następujące polecenie.</span><span class="sxs-lookup"><span data-stu-id="add5d-128">To add an initial migration, run the following command.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="add5d-129">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="add5d-129">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate
```

### <a name="visual-studio"></a>[<span data-ttu-id="add5d-130">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="add5d-130">Visual Studio</span></span>](#tab/vs)

``` powershell
Add-Migration InitialCreate
```

***

<span data-ttu-id="add5d-131">Do projektu są dodawane trzy pliki w katalogu **migracji** :</span><span class="sxs-lookup"><span data-stu-id="add5d-131">Three files are added to your project under the **Migrations** directory:</span></span>

* <span data-ttu-id="add5d-132">**XXXXXXXXXXXXXX_InitialCreate. cs**— główny plik migracji.</span><span class="sxs-lookup"><span data-stu-id="add5d-132">**XXXXXXXXXXXXXX_InitialCreate.cs**--The main migrations file.</span></span> <span data-ttu-id="add5d-133">Zawiera operacje niezbędne do zastosowania migracji (w programie `Up()`) i przywrócenia jej (w programie `Down()`).</span><span class="sxs-lookup"><span data-stu-id="add5d-133">Contains the operations necessary to apply the migration (in `Up()`) and to revert it (in `Down()`).</span></span>
* <span data-ttu-id="add5d-134">**XXXXXXXXXXXXXX_InitialCreate. Designer. cs**— plik metadanych migracji.</span><span class="sxs-lookup"><span data-stu-id="add5d-134">**XXXXXXXXXXXXXX_InitialCreate.Designer.cs**--The migrations metadata file.</span></span> <span data-ttu-id="add5d-135">Zawiera informacje używane przez EF.</span><span class="sxs-lookup"><span data-stu-id="add5d-135">Contains information used by EF.</span></span>
* <span data-ttu-id="add5d-136">**MyContextModelSnapshot.cs**— migawka bieżącego modelu.</span><span class="sxs-lookup"><span data-stu-id="add5d-136">**MyContextModelSnapshot.cs**--A snapshot of your current model.</span></span> <span data-ttu-id="add5d-137">Służy do określania, co zmieniło się podczas dodawania następnej migracji.</span><span class="sxs-lookup"><span data-stu-id="add5d-137">Used to determine what changed when adding the next migration.</span></span>

<span data-ttu-id="add5d-138">Sygnatura czasowa w nazwie pliku pomaga zachować ich uporządkowane chronologicznie, aby zobaczyć postęp zmian.</span><span class="sxs-lookup"><span data-stu-id="add5d-138">The timestamp in the filename helps keep them ordered chronologically so you can see the progression of changes.</span></span>

> [!TIP]
> <span data-ttu-id="add5d-139">Możesz przenieść pliki migracji i ręcznie zmienić ich przestrzeń nazw.</span><span class="sxs-lookup"><span data-stu-id="add5d-139">You are free to move Migrations files and change their namespace manually.</span></span> <span data-ttu-id="add5d-140">Nowe migracje są tworzone jako elementy równorzędne ostatniej migracji.</span><span class="sxs-lookup"><span data-stu-id="add5d-140">New migrations are created as siblings of the last migration.</span></span>
> 
> <span data-ttu-id="add5d-141">Alternatywnie możesz użyć `-Namespace` (konsola Menedżera pakietów) lub `--namespace` (interfejs wiersza polecenia platformy .NET Core), aby określić przestrzeń nazw podczas generowania.</span><span class="sxs-lookup"><span data-stu-id="add5d-141">Alternatively you can use `-Namespace` (Package Manager Console) or `--namespace` (.NET Core CLI) to specify the namespace at generation time.</span></span>
> ### <a name="net-core-cli"></a>[<span data-ttu-id="add5d-142">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="add5d-142">.NET Core CLI</span></span>](#tab/dotnet-core-cli)
> 
> ```dotnetcli
> dotnet ef migrations add InitialCreate --namespace Your.Namespace
> ```
> 
> ### <a name="visual-studio"></a>[<span data-ttu-id="add5d-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="add5d-143">Visual Studio</span></span>](#tab/vs)
> 
> ``` powershell
> Add-Migration InitialCreate -Namespace Your.Namespace
> ```

## <a name="update-the-database"></a><span data-ttu-id="add5d-144">Aktualizowanie bazy danych</span><span class="sxs-lookup"><span data-stu-id="add5d-144">Update the database</span></span>

<span data-ttu-id="add5d-145">Następnie Zastosuj migrację do bazy danych, aby utworzyć schemat.</span><span class="sxs-lookup"><span data-stu-id="add5d-145">Next, apply the migration to the database to create the schema.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="add5d-146">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="add5d-146">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```

### <a name="visual-studio"></a>[<span data-ttu-id="add5d-147">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="add5d-147">Visual Studio</span></span>](#tab/vs)

``` powershell
Update-Database
```

***

## <a name="customize-migration-code"></a><span data-ttu-id="add5d-148">Dostosowywanie kodu migracji</span><span class="sxs-lookup"><span data-stu-id="add5d-148">Customize migration code</span></span>

<span data-ttu-id="add5d-149">Po wprowadzeniu zmian w modelu EF Core schemat bazy danych może nie być zsynchronizowany. Aby zapewnić aktualność, Dodaj kolejną migrację.</span><span class="sxs-lookup"><span data-stu-id="add5d-149">After making changes to your EF Core model, the database schema might be out of sync. To bring it up to date, add another migration.</span></span> <span data-ttu-id="add5d-150">Nazwa migracji może być używana jak komunikat zatwierdzenia w systemie kontroli wersji.</span><span class="sxs-lookup"><span data-stu-id="add5d-150">The migration name can be used like a commit message in a version control system.</span></span> <span data-ttu-id="add5d-151">Na przykład można wybrać nazwę, np. *AddProductReviews* , jeśli zmiana jest nową klasą jednostek do przeglądu.</span><span class="sxs-lookup"><span data-stu-id="add5d-151">For example, you might choose a name like *AddProductReviews* if the change is a new entity class for reviews.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="add5d-152">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="add5d-152">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddProductReviews
```

### <a name="visual-studio"></a>[<span data-ttu-id="add5d-153">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="add5d-153">Visual Studio</span></span>](#tab/vs)

``` powershell
Add-Migration AddProductReviews
```

***

<span data-ttu-id="add5d-154">Po utworzeniu szkieletowej migracji (kod wygenerowany dla niego), Przejrzyj kod pod kątem dokładności i Dodaj, Usuń lub zmodyfikuj wszystkie operacje wymagane do poprawnego zastosowania.</span><span class="sxs-lookup"><span data-stu-id="add5d-154">Once the migration is scaffolded (code generated for it), review the code for accuracy and add, remove or modify any operations required to apply it correctly.</span></span>

<span data-ttu-id="add5d-155">Na przykład migracja może zawierać następujące operacje:</span><span class="sxs-lookup"><span data-stu-id="add5d-155">For example, a migration might contain the following operations:</span></span>

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

<span data-ttu-id="add5d-156">Chociaż te operacje sprawiają, że schemat bazy danych jest zgodny, nie zachowuje istniejących nazw klientów.</span><span class="sxs-lookup"><span data-stu-id="add5d-156">While these operations make the database schema compatible, they don't preserve the existing customer names.</span></span> <span data-ttu-id="add5d-157">Aby go ulepszyć, napisz ponownie w następujący sposób.</span><span class="sxs-lookup"><span data-stu-id="add5d-157">To make it better, rewrite it as follows.</span></span>

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
> <span data-ttu-id="add5d-158">Proces tworzenia szkieletu migracji ostrzega, gdy operacja może spowodować utratę danych (np. upuszczenie kolumny).</span><span class="sxs-lookup"><span data-stu-id="add5d-158">The migration scaffolding process warns when an operation might result in data loss (like dropping a column).</span></span> <span data-ttu-id="add5d-159">Jeśli widzisz to ostrzeżenie, pamiętaj o tym, aby sprawdzić poprawność kodu migracji.</span><span class="sxs-lookup"><span data-stu-id="add5d-159">If you see that warning, be especially sure to review the migrations code for accuracy.</span></span>

<span data-ttu-id="add5d-160">Zastosuj migrację do bazy danych przy użyciu odpowiedniego polecenia.</span><span class="sxs-lookup"><span data-stu-id="add5d-160">Apply the migration to the database using the appropriate command.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="add5d-161">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="add5d-161">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```

### <a name="visual-studio"></a>[<span data-ttu-id="add5d-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="add5d-162">Visual Studio</span></span>](#tab/vs)

``` powershell
Update-Database
```

***

### <a name="empty-migrations"></a><span data-ttu-id="add5d-163">Puste migracje</span><span class="sxs-lookup"><span data-stu-id="add5d-163">Empty migrations</span></span>

<span data-ttu-id="add5d-164">Czasami warto dodać migrację bez wprowadzania żadnych zmian modelu.</span><span class="sxs-lookup"><span data-stu-id="add5d-164">Sometimes it's useful to add a migration without making any model changes.</span></span> <span data-ttu-id="add5d-165">W takim przypadku dodanie nowej migracji powoduje utworzenie plików kodu z pustymi klasami.</span><span class="sxs-lookup"><span data-stu-id="add5d-165">In this case, adding a new migration creates code files with empty classes.</span></span> <span data-ttu-id="add5d-166">Można dostosować tę migrację do wykonywania operacji, które nie są bezpośrednio powiązane z modelem EF Core.</span><span class="sxs-lookup"><span data-stu-id="add5d-166">You can customize this migration to perform operations that don't directly relate to the EF Core model.</span></span> <span data-ttu-id="add5d-167">W ten sposób możesz chcieć zarządzać tymi elementami:</span><span class="sxs-lookup"><span data-stu-id="add5d-167">Some things you might want to manage this way are:</span></span>

* <span data-ttu-id="add5d-168">Wyszukiwanie pełnotekstowe</span><span class="sxs-lookup"><span data-stu-id="add5d-168">Full-Text Search</span></span>
* <span data-ttu-id="add5d-169">Funkcje</span><span class="sxs-lookup"><span data-stu-id="add5d-169">Functions</span></span>
* <span data-ttu-id="add5d-170">Procedury składowane</span><span class="sxs-lookup"><span data-stu-id="add5d-170">Stored procedures</span></span>
* <span data-ttu-id="add5d-171">Wyzwalacze</span><span class="sxs-lookup"><span data-stu-id="add5d-171">Triggers</span></span>
* <span data-ttu-id="add5d-172">Widoki</span><span class="sxs-lookup"><span data-stu-id="add5d-172">Views</span></span>

## <a name="remove-a-migration"></a><span data-ttu-id="add5d-173">Usuń migrację</span><span class="sxs-lookup"><span data-stu-id="add5d-173">Remove a migration</span></span>

<span data-ttu-id="add5d-174">Czasami należy dodać migrację i zdawać sobie sprawę, że należy wprowadzić dodatkowe zmiany w modelu EF Core przed ich zastosowaniem.</span><span class="sxs-lookup"><span data-stu-id="add5d-174">Sometimes you add a migration and realize you need to make additional changes to your EF Core model before applying it.</span></span> <span data-ttu-id="add5d-175">Aby usunąć ostatnią migrację, użyj tego polecenia.</span><span class="sxs-lookup"><span data-stu-id="add5d-175">To remove the last migration, use this command.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="add5d-176">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="add5d-176">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations remove
```

### <a name="visual-studio"></a>[<span data-ttu-id="add5d-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="add5d-177">Visual Studio</span></span>](#tab/vs)

``` powershell
Remove-Migration
```

***

<span data-ttu-id="add5d-178">Po usunięciu migracji możesz wprowadzić dodatkowe zmiany modelu i dodać je ponownie.</span><span class="sxs-lookup"><span data-stu-id="add5d-178">After removing the migration, you can make the additional model changes and add it again.</span></span>

## <a name="revert-a-migration"></a><span data-ttu-id="add5d-179">Przywracanie migracji</span><span class="sxs-lookup"><span data-stu-id="add5d-179">Revert a migration</span></span>

<span data-ttu-id="add5d-180">Jeśli migracja (lub kilka migracji) została już zastosowana do bazy danych, ale konieczne jest jej przywrócenie, można użyć tego samego polecenia do zastosowania migracji, ale określić nazwę migracji, do której chcesz przeprowadzić przywracanie.</span><span class="sxs-lookup"><span data-stu-id="add5d-180">If you already applied a migration (or several migrations) to the database but need to revert it, you can use the same command to apply migrations, but specify the name of the migration you want to roll back to.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="add5d-181">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="add5d-181">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update LastGoodMigration
```

### <a name="visual-studio"></a>[<span data-ttu-id="add5d-182">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="add5d-182">Visual Studio</span></span>](#tab/vs)

``` powershell
Update-Database LastGoodMigration
```

***

## <a name="generate-sql-scripts"></a><span data-ttu-id="add5d-183">Generuj skrypty SQL</span><span class="sxs-lookup"><span data-stu-id="add5d-183">Generate SQL scripts</span></span>

<span data-ttu-id="add5d-184">Podczas debugowania migracji lub wdrażania ich w produkcyjnej bazie danych warto wygenerować skrypt SQL.</span><span class="sxs-lookup"><span data-stu-id="add5d-184">When debugging your migrations or deploying them to a production database, it's useful to generate a SQL script.</span></span> <span data-ttu-id="add5d-185">Skrypt może następnie być ponownie przeglądany pod kątem dokładności i dostrojony, aby dopasować potrzeby produkcyjnej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="add5d-185">The script can then be further reviewed for accuracy and tuned to fit the needs of a production database.</span></span> <span data-ttu-id="add5d-186">Skrypt może być również używany w połączeniu z technologią wdrażania.</span><span class="sxs-lookup"><span data-stu-id="add5d-186">The script can also be used in conjunction with a deployment technology.</span></span> <span data-ttu-id="add5d-187">Podstawowe polecenie jest następujące.</span><span class="sxs-lookup"><span data-stu-id="add5d-187">The basic command is as follows.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="add5d-188">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="add5d-188">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

#### <a name="basic-usage"></a><span data-ttu-id="add5d-189">Podstawowe użycie</span><span class="sxs-lookup"><span data-stu-id="add5d-189">Basic Usage</span></span>
```dotnetcli
dotnet ef migrations script
```

#### <a name="with-from-to-implied"></a><span data-ttu-id="add5d-190">Z opcją od (do implikowanego)</span><span class="sxs-lookup"><span data-stu-id="add5d-190">With From (to implied)</span></span>
<span data-ttu-id="add5d-191">Spowoduje to wygenerowanie skryptu SQL z tej migracji do najnowszej migracji.</span><span class="sxs-lookup"><span data-stu-id="add5d-191">This will generate a SQL script from this migration to the latest migration.</span></span>
```dotnetcli
dotnet ef migrations script 20190725054716_Add_new_tables
```

#### <a name="with-from-and-to"></a><span data-ttu-id="add5d-192">Z i do</span><span class="sxs-lookup"><span data-stu-id="add5d-192">With From and To</span></span>
<span data-ttu-id="add5d-193">Spowoduje to wygenerowanie skryptu SQL z `from` migracji do określonej `to` migracji.</span><span class="sxs-lookup"><span data-stu-id="add5d-193">This will generate a SQL script from the `from` migration to the specified `to` migration.</span></span>
```dotnetcli
dotnet ef migrations script 20190725054716_Add_new_tables 20190829031257_Add_audit_table
```
<span data-ttu-id="add5d-194">Możesz użyć `from` , która jest nowsza niż `to` w celu wygenerowania skryptu wycofywania.</span><span class="sxs-lookup"><span data-stu-id="add5d-194">You can use a `from` that is newer than the `to` in order to generate a rollback script.</span></span> <span data-ttu-id="add5d-195">*Weź pod uwagę potencjalne scenariusze utraty danych.*</span><span class="sxs-lookup"><span data-stu-id="add5d-195">*Please take note of potential data loss scenarios.*</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="add5d-196">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="add5d-196">Visual Studio</span></span>](#tab/vs)

#### <a name="basic-usage"></a><span data-ttu-id="add5d-197">Podstawowe użycie</span><span class="sxs-lookup"><span data-stu-id="add5d-197">Basic Usage</span></span>
``` powershell
Script-Migration
```

#### <a name="with-from-to-implied"></a><span data-ttu-id="add5d-198">Z opcją od (do implikowanego)</span><span class="sxs-lookup"><span data-stu-id="add5d-198">With From (to implied)</span></span>
<span data-ttu-id="add5d-199">Spowoduje to wygenerowanie skryptu SQL z tej migracji do najnowszej migracji.</span><span class="sxs-lookup"><span data-stu-id="add5d-199">This will generate a SQL script from this migration to the latest migration.</span></span>
```powershell
Script-Migration 20190725054716_Add_new_tables
```

#### <a name="with-from-and-to"></a><span data-ttu-id="add5d-200">Z i do</span><span class="sxs-lookup"><span data-stu-id="add5d-200">With From and To</span></span>
<span data-ttu-id="add5d-201">Spowoduje to wygenerowanie skryptu SQL z `from` migracji do określonej `to` migracji.</span><span class="sxs-lookup"><span data-stu-id="add5d-201">This will generate a SQL script from the `from` migration to the specified `to` migration.</span></span>
```powershell
Script-Migration 20190725054716_Add_new_tables 20190829031257_Add_audit_table
```
<span data-ttu-id="add5d-202">Możesz użyć `from` , która jest nowsza niż `to` w celu wygenerowania skryptu wycofywania.</span><span class="sxs-lookup"><span data-stu-id="add5d-202">You can use a `from` that is newer than the `to` in order to generate a rollback script.</span></span> <span data-ttu-id="add5d-203">*Weź pod uwagę potencjalne scenariusze utraty danych.*</span><span class="sxs-lookup"><span data-stu-id="add5d-203">*Please take note of potential data loss scenarios.*</span></span>

***

<span data-ttu-id="add5d-204">Istnieje kilka opcji tego polecenia.</span><span class="sxs-lookup"><span data-stu-id="add5d-204">There are several options to this command.</span></span>

<span data-ttu-id="add5d-205">Migracja **z** migracji powinna być ostatnią zastosowana do bazy danych przed uruchomieniem skryptu.</span><span class="sxs-lookup"><span data-stu-id="add5d-205">The **from** migration should be the last migration applied to the database before running the script.</span></span> <span data-ttu-id="add5d-206">Jeśli nie zastosowano żadnych migracji, określ `0` (jest to ustawienie domyślne).</span><span class="sxs-lookup"><span data-stu-id="add5d-206">If no migrations have been applied, specify `0` (this is the default).</span></span>

<span data-ttu-id="add5d-207">Migracja **do** migracji to Ostatnia migracja, która zostanie zastosowana do bazy danych po uruchomieniu skryptu.</span><span class="sxs-lookup"><span data-stu-id="add5d-207">The **to** migration is the last migration that will be applied to the database after running the script.</span></span> <span data-ttu-id="add5d-208">Ta wartość domyślna to Ostatnia migracja w projekcie.</span><span class="sxs-lookup"><span data-stu-id="add5d-208">This defaults to the last migration in your project.</span></span>

<span data-ttu-id="add5d-209">Opcjonalnie można wygenerować skrypt **idempotentne** .</span><span class="sxs-lookup"><span data-stu-id="add5d-209">An **idempotent** script can optionally be generated.</span></span> <span data-ttu-id="add5d-210">Ten skrypt stosuje tylko migracje, jeśli nie zostały one jeszcze zastosowane do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="add5d-210">This script only applies migrations if they haven't already been applied to the database.</span></span> <span data-ttu-id="add5d-211">Jest to przydatne, jeśli nie masz dokładnej znajomości ostatniej migracji zastosowanej do bazy danych lub Jeśli wdrażasz ją w wielu bazach danych, które mogą znajdować się w innej migracji.</span><span class="sxs-lookup"><span data-stu-id="add5d-211">This is useful if you don't exactly know what the last migration applied to the database was or if you are deploying to multiple databases that may each be at a different migration.</span></span>

## <a name="apply-migrations-at-runtime"></a><span data-ttu-id="add5d-212">Zastosuj migracje w czasie wykonywania</span><span class="sxs-lookup"><span data-stu-id="add5d-212">Apply migrations at runtime</span></span>

<span data-ttu-id="add5d-213">Niektóre aplikacje mogą chcieć zastosować migracje w czasie wykonywania podczas uruchamiania lub pierwszego uruchomienia.</span><span class="sxs-lookup"><span data-stu-id="add5d-213">Some apps may want to apply migrations at runtime during startup or first run.</span></span> <span data-ttu-id="add5d-214">Zrób to przy użyciu `Migrate()` metody.</span><span class="sxs-lookup"><span data-stu-id="add5d-214">Do this using the `Migrate()` method.</span></span>

<span data-ttu-id="add5d-215">Ta metoda jest oparta na `IMigrator` usłudze, która może być używana w bardziej zaawansowanych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="add5d-215">This method builds on top of the `IMigrator` service, which can be used for more advanced scenarios.</span></span> <span data-ttu-id="add5d-216">Użyj `myDbContext.GetInfrastructure().GetService<IMigrator>()` , aby uzyskać do niej dostęp.</span><span class="sxs-lookup"><span data-stu-id="add5d-216">Use `myDbContext.GetInfrastructure().GetService<IMigrator>()` to access it.</span></span>

``` csharp
myDbContext.Database.Migrate();
```

> [!WARNING]
>
> * <span data-ttu-id="add5d-217">Takie podejście nie jest przeznaczone dla wszystkich użytkowników.</span><span class="sxs-lookup"><span data-stu-id="add5d-217">This approach isn't for everyone.</span></span> <span data-ttu-id="add5d-218">Chociaż jest to doskonałe rozwiązanie w przypadku aplikacji z lokalną bazą danych, większość aplikacji będzie wymagała bardziej niezawodnej strategii wdrażania, takiej jak generowanie skryptów SQL.</span><span class="sxs-lookup"><span data-stu-id="add5d-218">While it's great for apps with a local database, most applications will require more robust deployment strategy like generating SQL scripts.</span></span>
> * <span data-ttu-id="add5d-219">Nie wywołuj `EnsureCreated()` przed `Migrate()`.</span><span class="sxs-lookup"><span data-stu-id="add5d-219">Don't call `EnsureCreated()` before `Migrate()`.</span></span> <span data-ttu-id="add5d-220">`EnsureCreated()`pomija migracje, aby utworzyć schemat, co powoduje `Migrate()` niepowodzenie.</span><span class="sxs-lookup"><span data-stu-id="add5d-220">`EnsureCreated()` bypasses Migrations to create the schema, which causes `Migrate()` to fail.</span></span>

## <a name="next-steps"></a><span data-ttu-id="add5d-221">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="add5d-221">Next steps</span></span>

<span data-ttu-id="add5d-222">Aby uzyskać więcej informacji, zobacz <xref:core/miscellaneous/cli/index>.</span><span class="sxs-lookup"><span data-stu-id="add5d-222">For more information, see <xref:core/miscellaneous/cli/index>.</span></span>
