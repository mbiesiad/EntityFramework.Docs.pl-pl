---
title: Wprowadzenie - EF Core
author: rick-anderson
ms.date: 09/17/2019
ms.assetid: 3c88427c-20c6-42ec-a736-22d3eccd5071
uid: core/get-started/index
ms.openlocfilehash: 7ace80bf326395d3b68f3e745100cd45356d7973
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434100"
---
# <a name="getting-started-with-ef-core"></a><span data-ttu-id="0e6e6-102">Wprowadzenie do EF Core</span><span class="sxs-lookup"><span data-stu-id="0e6e6-102">Getting Started with EF Core</span></span>

<span data-ttu-id="0e6e6-103">W tym samouczku utworzysz aplikację konsoli .NET Core, która wykonuje dostęp do danych w bazie danych SQLite przy użyciu programu Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="0e6e6-103">In this tutorial, you create a .NET Core console app that performs data access against a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="0e6e6-104">Możesz wykonać samouczek przy użyciu programu Visual Studio w systemie Windows lub przy użyciu interfejsu wiersza polecenia .NET Core w systemie Windows, macOS lub Linux.</span><span class="sxs-lookup"><span data-stu-id="0e6e6-104">You can follow the tutorial by using Visual Studio on Windows, or by using the .NET Core CLI on Windows, macOS, or Linux.</span></span>

<span data-ttu-id="0e6e6-105">[Zobacz przykład tego artykułu na GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/GetStarted).</span><span class="sxs-lookup"><span data-stu-id="0e6e6-105">[View this article's sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/GetStarted).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0e6e6-106">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="0e6e6-106">Prerequisites</span></span>

<span data-ttu-id="0e6e6-107">Zainstaluj następujące oprogramowanie:</span><span class="sxs-lookup"><span data-stu-id="0e6e6-107">Install the following software:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="0e6e6-108">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="0e6e6-108">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="0e6e6-109">[.NET Core SDK](https://www.microsoft.com/net/download/core).</span><span class="sxs-lookup"><span data-stu-id="0e6e6-109">[.NET Core SDK](https://www.microsoft.com/net/download/core).</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="0e6e6-110">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0e6e6-110">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0e6e6-111">[Visual Studio 2019 w wersji 16.3 lub nowszej](https://www.visualstudio.com/downloads/) z tym obciążeniem:</span><span class="sxs-lookup"><span data-stu-id="0e6e6-111">[Visual Studio 2019 version 16.3 or later](https://www.visualstudio.com/downloads/) with this  workload:</span></span>
  * <span data-ttu-id="0e6e6-112">**.NET Core rozwoju między platformami** (w obszarze **Inne zestawy narzędzi)**</span><span class="sxs-lookup"><span data-stu-id="0e6e6-112">**.NET Core cross-platform development** (under **Other Toolsets**)</span></span>

---

## <a name="create-a-new-project"></a><span data-ttu-id="0e6e6-113">Tworzenie nowego projektu</span><span class="sxs-lookup"><span data-stu-id="0e6e6-113">Create a new project</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="0e6e6-114">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="0e6e6-114">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new console -o EFGetStarted
cd EFGetStarted
```

### <a name="visual-studio"></a>[<span data-ttu-id="0e6e6-115">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0e6e6-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0e6e6-116">Otwórz program Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0e6e6-116">Open Visual Studio</span></span>
* <span data-ttu-id="0e6e6-117">Kliknij **pozycję Utwórz nowy projekt**</span><span class="sxs-lookup"><span data-stu-id="0e6e6-117">Click **Create a new project**</span></span>
* <span data-ttu-id="0e6e6-118">Wybierz **aplikację konsoli (.NET Core)** z tagiem **C#** i kliknij przycisk **Dalej**</span><span class="sxs-lookup"><span data-stu-id="0e6e6-118">Select **Console App (.NET Core)** with the **C#** tag and click **Next**</span></span>
* <span data-ttu-id="0e6e6-119">Wprowadź **EFGetStarted** dla nazwy i kliknij przycisk **Utwórz**</span><span class="sxs-lookup"><span data-stu-id="0e6e6-119">Enter **EFGetStarted** for the name and click **Create**</span></span>

---

## <a name="install-entity-framework-core"></a><span data-ttu-id="0e6e6-120">Instalowanie rdzenia struktury encji</span><span class="sxs-lookup"><span data-stu-id="0e6e6-120">Install Entity Framework Core</span></span>

<span data-ttu-id="0e6e6-121">Aby zainstalować EF Core, należy zainstalować pakiet dla dostawców baz danych EF Core, które mają być kierowane.</span><span class="sxs-lookup"><span data-stu-id="0e6e6-121">To install EF Core, you install the package for the EF Core database provider(s) you want to target.</span></span> <span data-ttu-id="0e6e6-122">Ten samouczek używa SQLite, ponieważ działa na wszystkich platformach, które obsługują program .NET Core.</span><span class="sxs-lookup"><span data-stu-id="0e6e6-122">This tutorial uses SQLite because it runs on all platforms that .NET Core supports.</span></span> <span data-ttu-id="0e6e6-123">Aby uzyskać listę dostępnych dostawców, zobacz [Dostawcy baz danych](../providers/index.md).</span><span class="sxs-lookup"><span data-stu-id="0e6e6-123">For a list of available providers, see [Database Providers](../providers/index.md).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="0e6e6-124">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="0e6e6-124">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[<span data-ttu-id="0e6e6-125">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0e6e6-125">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0e6e6-126">**Narzędzia > Konsoli menedżera pakietów NuGet > pakietów**</span><span class="sxs-lookup"><span data-stu-id="0e6e6-126">**Tools > NuGet Package Manager > Package Manager Console**</span></span>
* <span data-ttu-id="0e6e6-127">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="0e6e6-127">Run the following commands:</span></span>

  ``` PowerShell
  Install-Package Microsoft.EntityFrameworkCore.Sqlite
  ```

<span data-ttu-id="0e6e6-128">Wskazówka: Możesz również zainstalować pakiety, klikając prawym przyciskiem myszy projekt i wybierając **pozycję Zarządzaj pakietami NuGet**</span><span class="sxs-lookup"><span data-stu-id="0e6e6-128">Tip: You can also install packages by right-clicking on the project and selecting **Manage NuGet Packages**</span></span>

---

## <a name="create-the-model"></a><span data-ttu-id="0e6e6-129">Tworzenie modelu</span><span class="sxs-lookup"><span data-stu-id="0e6e6-129">Create the model</span></span>

<span data-ttu-id="0e6e6-130">Zdefiniuj klasy kontekstu i klasy jednostek, które tworzą model.</span><span class="sxs-lookup"><span data-stu-id="0e6e6-130">Define a context class and entity classes that make up the model.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="0e6e6-131">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="0e6e6-131">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="0e6e6-132">W katalogu projektu utwórz **Model.cs** z następującym kodem</span><span class="sxs-lookup"><span data-stu-id="0e6e6-132">In the project directory, create **Model.cs** with the following code</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="0e6e6-133">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0e6e6-133">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0e6e6-134">Kliknij prawym przyciskiem myszy projekt i wybierz pozycję **Dodaj klasę >**</span><span class="sxs-lookup"><span data-stu-id="0e6e6-134">Right-click on the project and select **Add > Class**</span></span>
* <span data-ttu-id="0e6e6-135">Wprowadź **Model.cs** jako nazwę i kliknij przycisk **Dodaj**</span><span class="sxs-lookup"><span data-stu-id="0e6e6-135">Enter **Model.cs** as the name and click **Add**</span></span>
* <span data-ttu-id="0e6e6-136">Zastąp zawartość pliku następującym kodem</span><span class="sxs-lookup"><span data-stu-id="0e6e6-136">Replace the contents of the file with the following code</span></span>

---

[!code-csharp[Main](../../../samples/core/GetStarted/Model.cs)]

<span data-ttu-id="0e6e6-137">EF Core można również [odtworzyć](../managing-schemas/scaffolding.md) modelu z istniejącej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="0e6e6-137">EF Core can also [reverse engineer](../managing-schemas/scaffolding.md) a model from an existing database.</span></span>

<span data-ttu-id="0e6e6-138">Wskazówka: Ta aplikacja celowo utrzymuje rzeczy proste dla jasności.</span><span class="sxs-lookup"><span data-stu-id="0e6e6-138">Tip: This application intentionally keeps things simple for clarity.</span></span> <span data-ttu-id="0e6e6-139">[Parametry połączenia](../miscellaneous/connection-strings.md) nie powinny być przechowywane w kodzie dla aplikacji produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="0e6e6-139">[Connection strings](../miscellaneous/connection-strings.md) should not be stored in the code for production applications.</span></span> <span data-ttu-id="0e6e6-140">Można również podzielić każdą klasę C# na własny plik.</span><span class="sxs-lookup"><span data-stu-id="0e6e6-140">You may also want to split each C# class into it's own file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="0e6e6-141">Tworzenie bazy danych</span><span class="sxs-lookup"><span data-stu-id="0e6e6-141">Create the database</span></span>

<span data-ttu-id="0e6e6-142">Poniższe kroki używają [migracji](xref:core/managing-schemas/migrations/index) do tworzenia bazy danych.</span><span class="sxs-lookup"><span data-stu-id="0e6e6-142">The following steps use [migrations](xref:core/managing-schemas/migrations/index) to create a database.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="0e6e6-143">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="0e6e6-143">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="0e6e6-144">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="0e6e6-144">Run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

  <span data-ttu-id="0e6e6-145">Spowoduje to zainstalowanie [dotnet ef](../miscellaneous/cli/dotnet.md) i pakietu projektowego, który jest wymagany do uruchomienia polecenia w projekcie.</span><span class="sxs-lookup"><span data-stu-id="0e6e6-145">This installs [dotnet ef](../miscellaneous/cli/dotnet.md) and the design package which is required to run the command on a project.</span></span> <span data-ttu-id="0e6e6-146">Polecenie `migrations` tworzy szkielety migracji w celu utworzenia początkowego zestawu tabel dla modelu.</span><span class="sxs-lookup"><span data-stu-id="0e6e6-146">The `migrations` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="0e6e6-147">Polecenie `database update` tworzy bazę danych i stosuje do niej nową migrację.</span><span class="sxs-lookup"><span data-stu-id="0e6e6-147">The `database update` command creates the database and applies the new migration to it.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="0e6e6-148">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0e6e6-148">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0e6e6-149">Uruchamianie następujących poleceń w **konsoli Menedżera pakietów**</span><span class="sxs-lookup"><span data-stu-id="0e6e6-149">Run the following commands in **Package Manager Console**</span></span>

  ``` PowerShell
  Install-Package Microsoft.EntityFrameworkCore.Tools
  Add-Migration InitialCreate
  Update-Database
  ```

  <span data-ttu-id="0e6e6-150">Spowoduje to zainstalowanie [narzędzi PMC dla EF Core](../miscellaneous/cli/powershell.md).</span><span class="sxs-lookup"><span data-stu-id="0e6e6-150">This installs the [PMC tools for EF Core](../miscellaneous/cli/powershell.md).</span></span> <span data-ttu-id="0e6e6-151">Polecenie `Add-Migration` tworzy szkielety migracji w celu utworzenia początkowego zestawu tabel dla modelu.</span><span class="sxs-lookup"><span data-stu-id="0e6e6-151">The `Add-Migration` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="0e6e6-152">Polecenie `Update-Database` tworzy bazę danych i stosuje do niej nową migrację.</span><span class="sxs-lookup"><span data-stu-id="0e6e6-152">The `Update-Database` command creates the database and applies the new migration to it.</span></span>

---

## <a name="create-read-update--delete"></a><span data-ttu-id="0e6e6-153">Tworzenie, czytanie, aktualizowanie & usuwanie</span><span class="sxs-lookup"><span data-stu-id="0e6e6-153">Create, read, update & delete</span></span>

* <span data-ttu-id="0e6e6-154">Otwórz *Program.cs* i zastąp zawartość następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="0e6e6-154">Open *Program.cs* and replace the contents with the following code:</span></span>

  [!code-csharp[Main](../../../samples/core/GetStarted/Program.cs)]

## <a name="run-the-app"></a><span data-ttu-id="0e6e6-155">Uruchomienie aplikacji</span><span class="sxs-lookup"><span data-stu-id="0e6e6-155">Run the app</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="0e6e6-156">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="0e6e6-156">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet run
```

### <a name="visual-studio"></a>[<span data-ttu-id="0e6e6-157">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0e6e6-157">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0e6e6-158">Visual Studio używa niespójnego katalogu roboczego podczas uruchamiania aplikacji konsoli .NET Core.</span><span class="sxs-lookup"><span data-stu-id="0e6e6-158">Visual Studio uses an inconsistent working directory when running .NET Core console apps.</span></span> <span data-ttu-id="0e6e6-159">(patrz [dotnet/project-system#3619)](https://github.com/dotnet/project-system/issues/3619) Powoduje to wyjątek jest zgłaszany: *nie ma takiej tabeli: Blogi*.</span><span class="sxs-lookup"><span data-stu-id="0e6e6-159">(see [dotnet/project-system#3619](https://github.com/dotnet/project-system/issues/3619)) This results in an exception being thrown: *no such table: Blogs*.</span></span> <span data-ttu-id="0e6e6-160">Aby zaktualizować katalog roboczy:</span><span class="sxs-lookup"><span data-stu-id="0e6e6-160">To update the working directory:</span></span>

* <span data-ttu-id="0e6e6-161">Kliknij prawym przyciskiem myszy projekt i wybierz pozycję **Edytuj plik projektu**</span><span class="sxs-lookup"><span data-stu-id="0e6e6-161">Right-click on the project and select **Edit Project File**</span></span>
* <span data-ttu-id="0e6e6-162">Tuż poniżej *właściwości TargetFramework* dodaj następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="0e6e6-162">Just below the *TargetFramework* property, add the following:</span></span>

  ``` XML
  <StartWorkingDirectory>$(MSBuildProjectDirectory)</StartWorkingDirectory>
  ```

* <span data-ttu-id="0e6e6-163">Zapisz plik.</span><span class="sxs-lookup"><span data-stu-id="0e6e6-163">Save the file</span></span>

<span data-ttu-id="0e6e6-164">Teraz możesz uruchomić aplikację:</span><span class="sxs-lookup"><span data-stu-id="0e6e6-164">Now you can run the app:</span></span>

* <span data-ttu-id="0e6e6-165">**> debugowania start bez debugowania**</span><span class="sxs-lookup"><span data-stu-id="0e6e6-165">**Debug > Start Without Debugging**</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="0e6e6-166">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="0e6e6-166">Next steps</span></span>

* <span data-ttu-id="0e6e6-167">Postępuj zgodnie z [samouczkiem ASP.NET Core,](/aspnet/core/data/ef-rp/intro) aby używać ef core w aplikacji sieci web</span><span class="sxs-lookup"><span data-stu-id="0e6e6-167">Follow the [ASP.NET Core Tutorial](/aspnet/core/data/ef-rp/intro) to use EF Core in a web app</span></span>
* <span data-ttu-id="0e6e6-168">Dowiedz się więcej o [wyrażeniach zapytań LINQ](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span><span class="sxs-lookup"><span data-stu-id="0e6e6-168">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
* <span data-ttu-id="0e6e6-169">[Konfigurowanie modelu](xref:core/modeling/index) w celu określenia [1, najaśniku i](xref:core/modeling/entity-properties#required-and-optional-properties) [maksymalnej długości](xref:core/modeling/entity-properties#maximum-length)</span><span class="sxs-lookup"><span data-stu-id="0e6e6-169">[Configure your model](xref:core/modeling/index) to specify things like [required](xref:core/modeling/entity-properties#required-and-optional-properties) and [maximum length](xref:core/modeling/entity-properties#maximum-length)</span></span>
* <span data-ttu-id="0e6e6-170">Aktualizowanie schematu bazy danych po zmianie modelu za pomocą [migracji](xref:core/managing-schemas/migrations/index)</span><span class="sxs-lookup"><span data-stu-id="0e6e6-170">Use [Migrations](xref:core/managing-schemas/migrations/index) to update the database schema after changing your model</span></span>
