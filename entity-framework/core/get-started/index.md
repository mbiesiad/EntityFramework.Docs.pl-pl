---
title: Wprowadzenie — EF Core
author: rick-anderson
ms.date: 09/17/2019
ms.assetid: 3c88427c-20c6-42ec-a736-22d3eccd5071
uid: core/get-started/index
ms.openlocfilehash: b45a7e5010dd3ef37ea952aaf483847ae95ea4ef
ms.sourcegitcommit: 1f0f93c66b2b50e03fcbed90260e94faa0279c46
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2020
ms.locfileid: "84418950"
---
# <a name="getting-started-with-ef-core"></a><span data-ttu-id="408f4-102">Wprowadzenie z EF Core</span><span class="sxs-lookup"><span data-stu-id="408f4-102">Getting Started with EF Core</span></span>

<span data-ttu-id="408f4-103">W tym samouczku utworzysz aplikację konsolową .NET Core, która zapewnia dostęp do danych w bazie danych programu SQLite przy użyciu Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="408f4-103">In this tutorial, you create a .NET Core console app that performs data access against a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="408f4-104">Możesz wykonać czynności opisane w samouczku za pomocą programu Visual Studio w systemie Windows lub za pomocą interfejs wiersza polecenia platformy .NET Core w systemie Windows, macOS lub Linux.</span><span class="sxs-lookup"><span data-stu-id="408f4-104">You can follow the tutorial by using Visual Studio on Windows, or by using the .NET Core CLI on Windows, macOS, or Linux.</span></span>

<span data-ttu-id="408f4-105">[Zapoznaj się z przykładem tego artykułu w witrynie GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/GetStarted).</span><span class="sxs-lookup"><span data-stu-id="408f4-105">[View this article's sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/GetStarted).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="408f4-106">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="408f4-106">Prerequisites</span></span>

<span data-ttu-id="408f4-107">Zainstaluj następujące oprogramowanie:</span><span class="sxs-lookup"><span data-stu-id="408f4-107">Install the following software:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="408f4-108">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="408f4-108">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="408f4-109">[Zestaw .NET Core SDK](https://www.microsoft.com/net/download/core).</span><span class="sxs-lookup"><span data-stu-id="408f4-109">[.NET Core SDK](https://www.microsoft.com/net/download/core).</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="408f4-110">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="408f4-110">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="408f4-111">[Program Visual Studio 2019 w wersji 16,3 lub nowszej](https://www.visualstudio.com/downloads/) z tym obciążeniem:</span><span class="sxs-lookup"><span data-stu-id="408f4-111">[Visual Studio 2019 version 16.3 or later](https://www.visualstudio.com/downloads/) with this  workload:</span></span>
  * <span data-ttu-id="408f4-112">**Programowanie dla wielu platform w środowisku .NET Core** (w innych zestawach **narzędzi**)</span><span class="sxs-lookup"><span data-stu-id="408f4-112">**.NET Core cross-platform development** (under **Other Toolsets**)</span></span>

---

## <a name="create-a-new-project"></a><span data-ttu-id="408f4-113">Tworzenie nowego projektu</span><span class="sxs-lookup"><span data-stu-id="408f4-113">Create a new project</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="408f4-114">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="408f4-114">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new console -o EFGetStarted
cd EFGetStarted
```

### <a name="visual-studio"></a>[<span data-ttu-id="408f4-115">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="408f4-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="408f4-116">Otwórz program Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="408f4-116">Open Visual Studio</span></span>
* <span data-ttu-id="408f4-117">Kliknij pozycję **Utwórz nowy projekt**</span><span class="sxs-lookup"><span data-stu-id="408f4-117">Click **Create a new project**</span></span>
* <span data-ttu-id="408f4-118">Wybierz pozycję **aplikacja konsoli (.NET Core)** ze znacznikiem **C#** , a następnie kliknij przycisk **dalej** .</span><span class="sxs-lookup"><span data-stu-id="408f4-118">Select **Console App (.NET Core)** with the **C#** tag and click **Next**</span></span>
* <span data-ttu-id="408f4-119">Wprowadź **EFGetStarted** dla nazwy i kliknij przycisk **Utwórz** .</span><span class="sxs-lookup"><span data-stu-id="408f4-119">Enter **EFGetStarted** for the name and click **Create**</span></span>

---

## <a name="install-entity-framework-core"></a><span data-ttu-id="408f4-120">Zainstaluj Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="408f4-120">Install Entity Framework Core</span></span>

<span data-ttu-id="408f4-121">Aby zainstalować EF Core, należy zainstalować pakiet dla dostawców usługi EF Core Database, które mają być przeznaczone do celów.</span><span class="sxs-lookup"><span data-stu-id="408f4-121">To install EF Core, you install the package for the EF Core database provider(s) you want to target.</span></span> <span data-ttu-id="408f4-122">W tym samouczku jest używane oprogramowanie SQLite, ponieważ jest ono wykonywane na wszystkich platformach obsługiwanych przez platformę .NET Core.</span><span class="sxs-lookup"><span data-stu-id="408f4-122">This tutorial uses SQLite because it runs on all platforms that .NET Core supports.</span></span> <span data-ttu-id="408f4-123">Aby uzyskać listę dostępnych dostawców, zobacz [dostawcy bazy danych](../providers/index.md).</span><span class="sxs-lookup"><span data-stu-id="408f4-123">For a list of available providers, see [Database Providers](../providers/index.md).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="408f4-124">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="408f4-124">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[<span data-ttu-id="408f4-125">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="408f4-125">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="408f4-126">**Narzędzia > Menedżera pakietów NuGet > konsoli Menedżera pakietów**</span><span class="sxs-lookup"><span data-stu-id="408f4-126">**Tools > NuGet Package Manager > Package Manager Console**</span></span>
* <span data-ttu-id="408f4-127">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="408f4-127">Run the following commands:</span></span>

  ``` PowerShell
  Install-Package Microsoft.EntityFrameworkCore.Sqlite
  ```

<span data-ttu-id="408f4-128">Porada: Możesz także zainstalować pakiety, klikając prawym przyciskiem myszy projekt i wybierając pozycję **Zarządzaj pakietami NuGet** .</span><span class="sxs-lookup"><span data-stu-id="408f4-128">Tip: You can also install packages by right-clicking on the project and selecting **Manage NuGet Packages**</span></span>

---

## <a name="create-the-model"></a><span data-ttu-id="408f4-129">Tworzenie modelu</span><span class="sxs-lookup"><span data-stu-id="408f4-129">Create the model</span></span>

<span data-ttu-id="408f4-130">Zdefiniuj klasę kontekstu i klasy jednostek, które tworzą model.</span><span class="sxs-lookup"><span data-stu-id="408f4-130">Define a context class and entity classes that make up the model.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="408f4-131">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="408f4-131">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="408f4-132">W katalogu projektu Utwórz **model.cs** z następującym kodem</span><span class="sxs-lookup"><span data-stu-id="408f4-132">In the project directory, create **Model.cs** with the following code</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="408f4-133">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="408f4-133">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="408f4-134">Kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Dodaj klasy >**</span><span class="sxs-lookup"><span data-stu-id="408f4-134">Right-click on the project and select **Add > Class**</span></span>
* <span data-ttu-id="408f4-135">Wprowadź **model.cs** jako nazwę, a następnie kliknij przycisk **Dodaj** .</span><span class="sxs-lookup"><span data-stu-id="408f4-135">Enter **Model.cs** as the name and click **Add**</span></span>
* <span data-ttu-id="408f4-136">Zastąp zawartość pliku następującym kodem</span><span class="sxs-lookup"><span data-stu-id="408f4-136">Replace the contents of the file with the following code</span></span>

---

[!code-csharp[Main](../../../samples/core/GetStarted/Model.cs)]

<span data-ttu-id="408f4-137">EF Core [może również odtworzyć](../managing-schemas/scaffolding.md) model z istniejącej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="408f4-137">EF Core can also [reverse engineer](../managing-schemas/scaffolding.md) a model from an existing database.</span></span>

<span data-ttu-id="408f4-138">Porada: Ta aplikacja zacelowo upraszcza przejrzystość.</span><span class="sxs-lookup"><span data-stu-id="408f4-138">Tip: This application intentionally keeps things simple for clarity.</span></span> <span data-ttu-id="408f4-139">[Parametry połączenia](../miscellaneous/connection-strings.md) nie powinny być przechowywane w kodzie dla aplikacji produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="408f4-139">[Connection strings](../miscellaneous/connection-strings.md) should not be stored in the code for production applications.</span></span> <span data-ttu-id="408f4-140">Można również podzielić każdą klasę języka C# na własny plik.</span><span class="sxs-lookup"><span data-stu-id="408f4-140">You may also want to split each C# class into its own file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="408f4-141">Tworzenie bazy danych</span><span class="sxs-lookup"><span data-stu-id="408f4-141">Create the database</span></span>

<span data-ttu-id="408f4-142">Poniższe kroki służą do tworzenia bazy [danych programu.](xref:core/managing-schemas/migrations/index)</span><span class="sxs-lookup"><span data-stu-id="408f4-142">The following steps use [migrations](xref:core/managing-schemas/migrations/index) to create a database.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="408f4-143">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="408f4-143">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="408f4-144">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="408f4-144">Run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

  <span data-ttu-id="408f4-145">Spowoduje to zainstalowanie programu [dotnet EF](../miscellaneous/cli/dotnet.md) i pakietu projektowego, który jest wymagany do uruchomienia polecenia w projekcie.</span><span class="sxs-lookup"><span data-stu-id="408f4-145">This installs [dotnet ef](../miscellaneous/cli/dotnet.md) and the design package which is required to run the command on a project.</span></span> <span data-ttu-id="408f4-146">`migrations`Polecenie tworzy szkielet migracji w celu utworzenia początkowego zestawu tabel dla modelu.</span><span class="sxs-lookup"><span data-stu-id="408f4-146">The `migrations` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="408f4-147">`database update`Polecenie tworzy bazę danych i stosuje do niej nową migrację.</span><span class="sxs-lookup"><span data-stu-id="408f4-147">The `database update` command creates the database and applies the new migration to it.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="408f4-148">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="408f4-148">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="408f4-149">Uruchom następujące polecenia w **konsoli Menedżera pakietów**</span><span class="sxs-lookup"><span data-stu-id="408f4-149">Run the following commands in **Package Manager Console**</span></span>

  ``` PowerShell
  Install-Package Microsoft.EntityFrameworkCore.Tools
  Add-Migration InitialCreate
  Update-Database
  ```

  <span data-ttu-id="408f4-150">Spowoduje to zainstalowanie [narzędzi PMC dla EF Core](../miscellaneous/cli/powershell.md).</span><span class="sxs-lookup"><span data-stu-id="408f4-150">This installs the [PMC tools for EF Core](../miscellaneous/cli/powershell.md).</span></span> <span data-ttu-id="408f4-151">`Add-Migration`Polecenie tworzy szkielet migracji w celu utworzenia początkowego zestawu tabel dla modelu.</span><span class="sxs-lookup"><span data-stu-id="408f4-151">The `Add-Migration` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="408f4-152">`Update-Database`Polecenie tworzy bazę danych i stosuje do niej nową migrację.</span><span class="sxs-lookup"><span data-stu-id="408f4-152">The `Update-Database` command creates the database and applies the new migration to it.</span></span>

---

## <a name="create-read-update--delete"></a><span data-ttu-id="408f4-153">Tworzenie, odczytywanie, aktualizowanie & usuwanie</span><span class="sxs-lookup"><span data-stu-id="408f4-153">Create, read, update & delete</span></span>

* <span data-ttu-id="408f4-154">Otwórz *program.cs* i Zastąp zawartość następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="408f4-154">Open *Program.cs* and replace the contents with the following code:</span></span>

  [!code-csharp[Main](../../../samples/core/GetStarted/Program.cs)]

## <a name="run-the-app"></a><span data-ttu-id="408f4-155">Uruchomienie aplikacji</span><span class="sxs-lookup"><span data-stu-id="408f4-155">Run the app</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="408f4-156">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="408f4-156">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet run
```

### <a name="visual-studio"></a>[<span data-ttu-id="408f4-157">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="408f4-157">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="408f4-158">Program Visual Studio używa niespójnego katalogu roboczego podczas uruchamiania aplikacji konsolowych platformy .NET Core.</span><span class="sxs-lookup"><span data-stu-id="408f4-158">Visual Studio uses an inconsistent working directory when running .NET Core console apps.</span></span> <span data-ttu-id="408f4-159">(zobacz [dotnet/Project-System # 3619](https://github.com/dotnet/project-system/issues/3619)) Powoduje to zgłaszanie wyjątku: *nie ma takiej tabeli: blogi*.</span><span class="sxs-lookup"><span data-stu-id="408f4-159">(see [dotnet/project-system#3619](https://github.com/dotnet/project-system/issues/3619)) This results in an exception being thrown: *no such table: Blogs*.</span></span> <span data-ttu-id="408f4-160">Aby zaktualizować katalog roboczy:</span><span class="sxs-lookup"><span data-stu-id="408f4-160">To update the working directory:</span></span>

* <span data-ttu-id="408f4-161">Kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Edytuj plik projektu**</span><span class="sxs-lookup"><span data-stu-id="408f4-161">Right-click on the project and select **Edit Project File**</span></span>
* <span data-ttu-id="408f4-162">Po prostu poniżej właściwości *TargetFramework* Dodaj następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="408f4-162">Just below the *TargetFramework* property, add the following:</span></span>

  ``` XML
  <StartWorkingDirectory>$(MSBuildProjectDirectory)</StartWorkingDirectory>
  ```

* <span data-ttu-id="408f4-163">Zapisz plik.</span><span class="sxs-lookup"><span data-stu-id="408f4-163">Save the file</span></span>

<span data-ttu-id="408f4-164">Teraz możesz uruchomić aplikację:</span><span class="sxs-lookup"><span data-stu-id="408f4-164">Now you can run the app:</span></span>

* <span data-ttu-id="408f4-165">**Debugowanie > uruchamiane bez debugowania**</span><span class="sxs-lookup"><span data-stu-id="408f4-165">**Debug > Start Without Debugging**</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="408f4-166">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="408f4-166">Next steps</span></span>

* <span data-ttu-id="408f4-167">Postępuj zgodnie z [samouczkiem ASP.NET Core](/aspnet/core/data/ef-rp/intro) , aby użyć EF Core w aplikacji sieci Web</span><span class="sxs-lookup"><span data-stu-id="408f4-167">Follow the [ASP.NET Core Tutorial](/aspnet/core/data/ef-rp/intro) to use EF Core in a web app</span></span>
* <span data-ttu-id="408f4-168">Dowiedz się więcej o [wyrażeniach zapytań LINQ](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span><span class="sxs-lookup"><span data-stu-id="408f4-168">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
* <span data-ttu-id="408f4-169">[Skonfiguruj model](xref:core/modeling/index) , aby określić elementy, takie jak [wymagana](xref:core/modeling/entity-properties#required-and-optional-properties) i [Maksymalna długość](xref:core/modeling/entity-properties#maximum-length)</span><span class="sxs-lookup"><span data-stu-id="408f4-169">[Configure your model](xref:core/modeling/index) to specify things like [required](xref:core/modeling/entity-properties#required-and-optional-properties) and [maximum length](xref:core/modeling/entity-properties#maximum-length)</span></span>
* <span data-ttu-id="408f4-170">Użyj [migracji](xref:core/managing-schemas/migrations/index) , aby zaktualizować schemat bazy danych po zmianie modelu</span><span class="sxs-lookup"><span data-stu-id="408f4-170">Use [Migrations](xref:core/managing-schemas/migrations/index) to update the database schema after changing your model</span></span>
