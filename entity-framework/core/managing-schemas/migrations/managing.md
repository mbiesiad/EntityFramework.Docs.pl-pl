---
title: Zarządzanie migracjami — EF Core
author: bricelam
ms.author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/managing
ms.openlocfilehash: 2097d3cc9232d448191dbebbe3d14d86e80b91fe
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526436"
---
# <a name="managing-migrations"></a><span data-ttu-id="63e74-102">Zarządzanie migracjami</span><span class="sxs-lookup"><span data-stu-id="63e74-102">Managing Migrations</span></span>

<span data-ttu-id="63e74-103">W miarę zmiany modelu migracje są dodawane i usuwane w ramach normalnego opracowywania, a pliki migracji są sprawdzane w kontroli źródła projektu.</span><span class="sxs-lookup"><span data-stu-id="63e74-103">As your model changes, migrations are added and removed as part of normal development, and the migration files are checked into your project's source control.</span></span> <span data-ttu-id="63e74-104">Aby zarządzać migracjami, należy najpierw zainstalować [EF Core narzędzia wiersza polecenia](xref:core/miscellaneous/cli/index).</span><span class="sxs-lookup"><span data-stu-id="63e74-104">To manage migrations, you must first install the [EF Core command-line tools](xref:core/miscellaneous/cli/index).</span></span>

> [!TIP]
> <span data-ttu-id="63e74-105">Jeśli `DbContext` znajduje się w innym zestawie niż projekt startowy, można jawnie określić projekty docelowe i uruchomieniowe w [narzędziu Konsola Menedżera pakietów](xref:core/miscellaneous/cli/powershell#target-and-startup-project) lub [Narzędzia interfejs wiersza polecenia platformy .NET Core](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project).</span><span class="sxs-lookup"><span data-stu-id="63e74-105">If the `DbContext` is in a different assembly than the startup project, you can explicitly specify the target and startup projects in either the [Package Manager Console tools](xref:core/miscellaneous/cli/powershell#target-and-startup-project) or the [.NET Core CLI tools](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project).</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="63e74-106">Dodawanie migracji</span><span class="sxs-lookup"><span data-stu-id="63e74-106">Add a migration</span></span>

<span data-ttu-id="63e74-107">Po zmianie modelu można dodać migrację dla tej zmiany:</span><span class="sxs-lookup"><span data-stu-id="63e74-107">After your model has been changed, you can add a migration for that change:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="63e74-108">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="63e74-108">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddBlogCreatedTimestamp
```

### <a name="visual-studio"></a>[<span data-ttu-id="63e74-109">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="63e74-109">Visual Studio</span></span>](#tab/vs)

``` powershell
Add-Migration AddBlogCreatedTimestamp
```

***

<span data-ttu-id="63e74-110">Nazwa migracji może być używana jak komunikat zatwierdzenia w systemie kontroli wersji.</span><span class="sxs-lookup"><span data-stu-id="63e74-110">The migration name can be used like a commit message in a version control system.</span></span> <span data-ttu-id="63e74-111">Na przykład można wybrać nazwę, np. *AddBlogCreatedTimestamp* , jeśli zmiana jest nową `CreatedTimestamp` właściwością w `Blog` jednostce.</span><span class="sxs-lookup"><span data-stu-id="63e74-111">For example, you might choose a name like *AddBlogCreatedTimestamp* if the change is a new `CreatedTimestamp` property on your `Blog` entity.</span></span>

<span data-ttu-id="63e74-112">Do projektu są dodawane trzy pliki w katalogu **migracji** :</span><span class="sxs-lookup"><span data-stu-id="63e74-112">Three files are added to your project under the **Migrations** directory:</span></span>

* <span data-ttu-id="63e74-113">**XXXXXXXXXXXXXX_AddCreatedTimestamp. cs**— główny plik migracji.</span><span class="sxs-lookup"><span data-stu-id="63e74-113">**XXXXXXXXXXXXXX_AddCreatedTimestamp.cs**--The main migrations file.</span></span> <span data-ttu-id="63e74-114">Zawiera operacje niezbędne do zastosowania migracji (w programie `Up` ) i przywrócenia jej (w programie `Down` ).</span><span class="sxs-lookup"><span data-stu-id="63e74-114">Contains the operations necessary to apply the migration (in `Up`) and to revert it (in `Down`).</span></span>
* <span data-ttu-id="63e74-115">**XXXXXXXXXXXXXX_AddCreatedTimestamp. Designer. cs**— plik metadanych migracji.</span><span class="sxs-lookup"><span data-stu-id="63e74-115">**XXXXXXXXXXXXXX_AddCreatedTimestamp.Designer.cs**--The migrations metadata file.</span></span> <span data-ttu-id="63e74-116">Zawiera informacje używane przez EF.</span><span class="sxs-lookup"><span data-stu-id="63e74-116">Contains information used by EF.</span></span>
* <span data-ttu-id="63e74-117">**MyContextModelSnapshot.cs**— migawka bieżącego modelu.</span><span class="sxs-lookup"><span data-stu-id="63e74-117">**MyContextModelSnapshot.cs**--A snapshot of your current model.</span></span> <span data-ttu-id="63e74-118">Służy do określania, co zmieniło się podczas dodawania następnej migracji.</span><span class="sxs-lookup"><span data-stu-id="63e74-118">Used to determine what changed when adding the next migration.</span></span>

<span data-ttu-id="63e74-119">Sygnatura czasowa w nazwie pliku pomaga zachować ich uporządkowane chronologicznie, aby zobaczyć postęp zmian.</span><span class="sxs-lookup"><span data-stu-id="63e74-119">The timestamp in the filename helps keep them ordered chronologically so you can see the progression of changes.</span></span>

### <a name="namespaces"></a><span data-ttu-id="63e74-120">Przestrzenie nazw</span><span class="sxs-lookup"><span data-stu-id="63e74-120">Namespaces</span></span>

<span data-ttu-id="63e74-121">Możesz przenieść pliki migracji i ręcznie zmienić ich przestrzeń nazw.</span><span class="sxs-lookup"><span data-stu-id="63e74-121">You are free to move Migrations files and change their namespace manually.</span></span> <span data-ttu-id="63e74-122">Nowe migracje są tworzone jako elementy równorzędne ostatniej migracji.</span><span class="sxs-lookup"><span data-stu-id="63e74-122">New migrations are created as siblings of the last migration.</span></span> <span data-ttu-id="63e74-123">Alternatywnie można określić przestrzeń nazw podczas generowania w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="63e74-123">Alternatively, you can specify the namespace at generation time as follows:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="63e74-124">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="63e74-124">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --namespace Your.Namespace
```

### <a name="visual-studio"></a>[<span data-ttu-id="63e74-125">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="63e74-125">Visual Studio</span></span>](#tab/vs)

``` powershell
Add-Migration InitialCreate -Namespace Your.Namespace
```

***

## <a name="customize-migration-code"></a><span data-ttu-id="63e74-126">Dostosowywanie kodu migracji</span><span class="sxs-lookup"><span data-stu-id="63e74-126">Customize migration code</span></span>

<span data-ttu-id="63e74-127">Mimo że EF Core zwykle tworzy dokładne migracje, należy zawsze sprawdzić kod i upewnić się, że odpowiada żądanej zmianie; w niektórych przypadkach jest to nawet konieczne.</span><span class="sxs-lookup"><span data-stu-id="63e74-127">While EF Core generally creates accurate migrations, you should always review the code and make sure it corresponds to the desired change; in some cases, it is even necessary to do so.</span></span>

### <a name="column-renames"></a><span data-ttu-id="63e74-128">Nazwy kolumn</span><span class="sxs-lookup"><span data-stu-id="63e74-128">Column renames</span></span>

<span data-ttu-id="63e74-129">Jednym z przykładów, w których wymagane jest dostosowanie migracji, jest zmiana nazwy właściwości.</span><span class="sxs-lookup"><span data-stu-id="63e74-129">One notable example where customizing migrations is required is when renaming a property.</span></span> <span data-ttu-id="63e74-130">Na przykład jeśli zmienisz nazwę właściwości z `Name` na `FullName` , EF Core będzie generować następującą migrację:</span><span class="sxs-lookup"><span data-stu-id="63e74-130">For example, if you rename a property from `Name` to `FullName`, EF Core will generate the following migration:</span></span>

```c#
migrationBuilder.DropColumn(
    name: "Name",
    table: "Customers");

migrationBuilder.AddColumn<string>(
    name: "FullName",
    table: "Customers",
    nullable: true);
```

<span data-ttu-id="63e74-131">EF Core zazwyczaj nie jest w stanie wiedzieć, kiedy zamiarem jest Porzucenie kolumny i utworzenie nowej (dwóch oddzielnych zmian) oraz zmiana nazwy kolumny.</span><span class="sxs-lookup"><span data-stu-id="63e74-131">EF Core is generally unable to know when the intention is to drop a column and create a new one (two separate changes), and when a column should be renamed.</span></span> <span data-ttu-id="63e74-132">Jeśli powyższa migracja zostanie zastosowana w taki sposób, wszystkie nazwy klientów zostaną utracone.</span><span class="sxs-lookup"><span data-stu-id="63e74-132">If the above migration is applied as-is, all your customer names will be lost.</span></span> <span data-ttu-id="63e74-133">Aby zmienić nazwę kolumny, Zastąp powyższą wytworzoną migrację, wykonując następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="63e74-133">To rename a column, replace the above generated migration with the following:</span></span>

```c#
migrationBuilder.RenameColumn(
    name: "Name",
    table: "Customers",
    newName: "FullName");
```

> [!TIP]
> <span data-ttu-id="63e74-134">Proces tworzenia szkieletu migracji ostrzega, gdy operacja może spowodować utratę danych (np. upuszczenie kolumny).</span><span class="sxs-lookup"><span data-stu-id="63e74-134">The migration scaffolding process warns when an operation might result in data loss (like dropping a column).</span></span> <span data-ttu-id="63e74-135">Jeśli widzisz to ostrzeżenie, pamiętaj o tym, aby sprawdzić poprawność kodu migracji.</span><span class="sxs-lookup"><span data-stu-id="63e74-135">If you see that warning, be especially sure to review the migrations code for accuracy.</span></span>

### <a name="adding-raw-sql"></a><span data-ttu-id="63e74-136">Dodawanie RAW SQL</span><span class="sxs-lookup"><span data-stu-id="63e74-136">Adding raw SQL</span></span>

<span data-ttu-id="63e74-137">Podczas zmieniania nazwy kolumny można uzyskać za pomocą wbudowanego interfejsu API, w wielu przypadkach nie jest to możliwe.</span><span class="sxs-lookup"><span data-stu-id="63e74-137">While renaming a column can be achieved via a built-in API, in many cases that is not possible.</span></span> <span data-ttu-id="63e74-138">Na przykład możemy chcieć zamienić istniejące `FirstName` i właściwości na `LastName` jedną, nową `FullName` Właściwość.</span><span class="sxs-lookup"><span data-stu-id="63e74-138">For example, we may want to replace existing `FirstName` and `LastName` properties with a single, new `FullName` property.</span></span> <span data-ttu-id="63e74-139">Migracja wygenerowana przez EF Core będzie następująca:</span><span class="sxs-lookup"><span data-stu-id="63e74-139">The migration generated by EF Core will be the following:</span></span>

``` csharp
migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");

migrationBuilder.AddColumn<string>(
    name: "FullName",
    table: "Customer",
    nullable: true);
```

<span data-ttu-id="63e74-140">Tak jak wcześniej, spowoduje to niepożądane utratę danych.</span><span class="sxs-lookup"><span data-stu-id="63e74-140">As before, this would cause unwanted data loss.</span></span> <span data-ttu-id="63e74-141">Aby przetransferować dane ze starych kolumn, należy zmienić układ migracji i wprowadzić nieprzetworzoną operację SQL w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="63e74-141">To transfer the data from the old columns, we rearrange the migrations and introduce a raw SQL operation as follows:</span></span>

``` csharp
migrationBuilder.AddColumn<string>(
    name: "FullName",
    table: "Customer",
    nullable: true);

migrationBuilder.Sql(
@"
    UPDATE Customer
    SET FullName = FirstName + ' ' + LastName;
");

migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");
```

### <a name="arbitrary-changes-via-raw-sql"></a><span data-ttu-id="63e74-142">Dowolne zmiany za pośrednictwem bazy danych SQL RAW</span><span class="sxs-lookup"><span data-stu-id="63e74-142">Arbitrary changes via raw SQL</span></span>

<span data-ttu-id="63e74-143">Do zarządzania obiektami bazy danych, do których EF Core nie ma informacji, można również użyć surowego SQL.</span><span class="sxs-lookup"><span data-stu-id="63e74-143">Raw SQL can also be used to manage database objects that EF Core isn't aware of.</span></span> <span data-ttu-id="63e74-144">W tym celu należy dodać migrację bez wprowadzania żadnych zmian modelu; zostanie wygenerowana pusta migracja, którą można następnie wypełnić przy użyciu nieprzetworzonych operacji SQL.</span><span class="sxs-lookup"><span data-stu-id="63e74-144">To do this, add a migration without making any model change; an empty migration will be generated, which you can then populate with raw SQL operations.</span></span>

<span data-ttu-id="63e74-145">Na przykład następująca migracja tworzy SQL Server procedury składowanej:</span><span class="sxs-lookup"><span data-stu-id="63e74-145">For example, the following migration creates a SQL Server stored procedure:</span></span>

```c#
migrationBuilder.Sql(
@"
    EXEC ('CREATE PROCEDURE getFullName
        @LastName nvarchar(50),
        @FirstName nvarchar(50)
    AS
        RETURN @LastName + @FirstName;')");
```

<span data-ttu-id="63e74-146">Może to służyć do zarządzania dowolnym aspektem bazy danych, w tym:</span><span class="sxs-lookup"><span data-stu-id="63e74-146">This can be used to manage any aspect of your database, including:</span></span>

* <span data-ttu-id="63e74-147">Procedury składowane</span><span class="sxs-lookup"><span data-stu-id="63e74-147">Stored procedures</span></span>
* <span data-ttu-id="63e74-148">Wyszukiwanie pełnotekstowe</span><span class="sxs-lookup"><span data-stu-id="63e74-148">Full-Text Search</span></span>
* <span data-ttu-id="63e74-149">Funkcje</span><span class="sxs-lookup"><span data-stu-id="63e74-149">Functions</span></span>
* <span data-ttu-id="63e74-150">Wyzwalacze</span><span class="sxs-lookup"><span data-stu-id="63e74-150">Triggers</span></span>
* <span data-ttu-id="63e74-151">Widoki</span><span class="sxs-lookup"><span data-stu-id="63e74-151">Views</span></span>

<span data-ttu-id="63e74-152">W większości przypadków EF Core automatycznie zawija każdą migrację we własnej transakcji podczas stosowania migracji.</span><span class="sxs-lookup"><span data-stu-id="63e74-152">In most cases, EF Core will automatically wrap each migration in its own transaction when applying migrations.</span></span> <span data-ttu-id="63e74-153">Niestety, nie można wykonać niektórych operacji migracji w ramach transakcji w niektórych bazach danych; w takich przypadkach możesz zrezygnować z transakcji, przekazując `suppressTransaction: true` do `migrationBuilder.Sql` .</span><span class="sxs-lookup"><span data-stu-id="63e74-153">Unfortunately, some migrations operations cannot be performed within a transaction in some databases; for these cases, you may opt out of the transaction by passing `suppressTransaction: true` to `migrationBuilder.Sql`.</span></span>

<span data-ttu-id="63e74-154">Jeśli `DbContext` znajduje się w innym zestawie niż projekt startowy, można jawnie określić projekty docelowe i uruchomieniowe w [narzędziu Konsola Menedżera pakietów](xref:core/miscellaneous/cli/powershell#target-and-startup-project) lub [Narzędzia interfejs wiersza polecenia platformy .NET Core](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project).</span><span class="sxs-lookup"><span data-stu-id="63e74-154">If the `DbContext` is in a different assembly than the startup project, you can explicitly specify the target and startup projects in either the [Package Manager Console tools](xref:core/miscellaneous/cli/powershell#target-and-startup-project) or the [.NET Core CLI tools](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project).</span></span>

## <a name="remove-a-migration"></a><span data-ttu-id="63e74-155">Usuń migrację</span><span class="sxs-lookup"><span data-stu-id="63e74-155">Remove a migration</span></span>

<span data-ttu-id="63e74-156">Czasami należy dodać migrację i zdawać sobie sprawę, że należy wprowadzić dodatkowe zmiany w modelu EF Core przed ich zastosowaniem.</span><span class="sxs-lookup"><span data-stu-id="63e74-156">Sometimes you add a migration and realize you need to make additional changes to your EF Core model before applying it.</span></span> <span data-ttu-id="63e74-157">Aby usunąć ostatnią migrację, użyj tego polecenia.</span><span class="sxs-lookup"><span data-stu-id="63e74-157">To remove the last migration, use this command.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="63e74-158">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="63e74-158">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations remove
```

### <a name="visual-studio"></a>[<span data-ttu-id="63e74-159">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="63e74-159">Visual Studio</span></span>](#tab/vs)

``` powershell
Remove-Migration
```

***

<span data-ttu-id="63e74-160">Po usunięciu migracji możesz wprowadzić dodatkowe zmiany modelu i dodać je ponownie.</span><span class="sxs-lookup"><span data-stu-id="63e74-160">After removing the migration, you can make the additional model changes and add it again.</span></span>

> [!WARNING]
> <span data-ttu-id="63e74-161">Pamiętaj, aby nie usuwać żadnych migracji, które zostały już zastosowane do produkcyjnych baz danych.</span><span class="sxs-lookup"><span data-stu-id="63e74-161">Take care not to remove any migrations which are already applied to production databases.</span></span> <span data-ttu-id="63e74-162">Nie pozwoli to na przywrócenie tego stanu i może spowodować przerwanie założeń dokonanych przez kolejne migracje.</span><span class="sxs-lookup"><span data-stu-id="63e74-162">Not doing so will prevent you from being able to revert it, and may break the assumptions made by subsequent migrations.</span></span>

## <a name="listing-migrations"></a><span data-ttu-id="63e74-163">Wyświetlanie listy migracji</span><span class="sxs-lookup"><span data-stu-id="63e74-163">Listing migrations</span></span>

<span data-ttu-id="63e74-164">Można wyświetlić listę wszystkich istniejących migracji w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="63e74-164">You can list all existing migrations as follows:</span></span>

```dotnetcli
dotnet ef migrations list
```

## <a name="resetting-all-migrations"></a><span data-ttu-id="63e74-165">Resetowanie wszystkich migracji</span><span class="sxs-lookup"><span data-stu-id="63e74-165">Resetting all migrations</span></span>

<span data-ttu-id="63e74-166">W niektórych sytuacjach może być konieczne usunięcie wszystkich migracji i rozpoczęcie od nowa.</span><span class="sxs-lookup"><span data-stu-id="63e74-166">In some extreme cases, it may be necessary to remove all migrations and start over.</span></span> <span data-ttu-id="63e74-167">Można to łatwo zrobić, usuwając folder **migracji** i upuszczając swoją bazę danych. w tym momencie można utworzyć nową migrację początkową, która będzie zawierać cały bieżący schemat.</span><span class="sxs-lookup"><span data-stu-id="63e74-167">This can be easily done by deleting your **Migrations** folder and dropping your database; at that point you can create a new initial migration, which will contain you entire current schema.</span></span>

<span data-ttu-id="63e74-168">Istnieje również możliwość zresetowania wszystkich migracji i utworzenia jednego z nich bez utraty danych.</span><span class="sxs-lookup"><span data-stu-id="63e74-168">It's also possible to reset all migrations and create a single one without losing your data.</span></span> <span data-ttu-id="63e74-169">Jest to czasami nazywane "zgniatanie" i obejmuje kilka czynności ręcznych:</span><span class="sxs-lookup"><span data-stu-id="63e74-169">This is sometimes called "squashing", and involves some manual work:</span></span>

* <span data-ttu-id="63e74-170">Usuwanie folderu **migracji**</span><span class="sxs-lookup"><span data-stu-id="63e74-170">Delete your **Migrations** folder</span></span>
* <span data-ttu-id="63e74-171">Utwórz nową migrację i Wygeneruj skrypt SQL dla tego programu</span><span class="sxs-lookup"><span data-stu-id="63e74-171">Create a new migration and generate a SQL script for it</span></span>
* <span data-ttu-id="63e74-172">W bazie danych Usuń wszystkie wiersze z tabeli historii migracji</span><span class="sxs-lookup"><span data-stu-id="63e74-172">In your database, delete all rows from the migrations history table</span></span>
* <span data-ttu-id="63e74-173">Wstaw pojedynczy wiersz do historii migracji, aby zarejestrować, że pierwsza migracja została już zastosowana, ponieważ tabele już istnieją.</span><span class="sxs-lookup"><span data-stu-id="63e74-173">Insert a single row into the migrations history, to record that the first migration has already been applied, since your tables are already there.</span></span> <span data-ttu-id="63e74-174">Insert SEQL to Ostatnia operacja w skrypcie SQL wygenerowany powyżej.</span><span class="sxs-lookup"><span data-stu-id="63e74-174">The insert SEQL is the last operation in the SQL script generated above.</span></span>
