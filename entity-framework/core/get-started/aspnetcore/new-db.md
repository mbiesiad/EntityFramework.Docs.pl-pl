---
title: Wprowadzenie do platformy ASP.NET Core — Nowa baza danych — EF Core
author: rick-anderson
ms.author: riande
ms.date: 08/03/2018
ms.assetid: e153627f-f132-4c11-b13c-6c9a607addce
uid: core/get-started/aspnetcore/new-db
ms.openlocfilehash: 4734586adc89e9c1d866a1b4accd8b5e51fe2bb0
ms.sourcegitcommit: ebf661025d2ad2b62466fa7bf0e0772a7811cbe7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2019
ms.locfileid: "54211169"
---
# <a name="getting-started-with-ef-core-on-aspnet-core-with-a-new-database"></a><span data-ttu-id="a5a26-102">Wprowadzenie do programu EF Core programu ASP.NET Core przy użyciu nowej bazy danych</span><span class="sxs-lookup"><span data-stu-id="a5a26-102">Getting Started with EF Core on ASP.NET Core with a New database</span></span>

<span data-ttu-id="a5a26-103">W tym samouczku utworzysz aplikację ASP.NET Core MVC, który wykonuje podstawowe dane dostępu przy użyciu platformy Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="a5a26-103">In this tutorial, you build an ASP.NET Core MVC application that performs basic data access using Entity Framework Core.</span></span> <span data-ttu-id="a5a26-104">W samouczku migracje utworzyć bazę danych z modelu danych.</span><span class="sxs-lookup"><span data-stu-id="a5a26-104">The tutorial uses migrations to create the database from the data model.</span></span>

<span data-ttu-id="a5a26-105">Za pomocą programu Visual Studio 2017 na Windows lub za pomocą interfejsu wiersza polecenia platformy .NET Core w systemie Windows, macOS lub Linux, można wykonać kroki samouczka.</span><span class="sxs-lookup"><span data-stu-id="a5a26-105">You can follow the tutorial by using Visual Studio 2017 on Windows, or by using the .NET Core CLI on Windows, macOS, or Linux.</span></span>

<span data-ttu-id="a5a26-106">Wyświetl przykład w tym artykule w witrynie GitHub:</span><span class="sxs-lookup"><span data-stu-id="a5a26-106">View this article's sample on GitHub:</span></span>
* [<span data-ttu-id="a5a26-107">Program Visual Studio 2017 z programem SQL Server</span><span class="sxs-lookup"><span data-stu-id="a5a26-107">Visual Studio 2017 with SQL Server</span></span>](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb)
* <span data-ttu-id="a5a26-108">[.NET core interfejsu wiersza polecenia za pomocą SQLite](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite).</span><span class="sxs-lookup"><span data-stu-id="a5a26-108">[.NET Core CLI with SQLite](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a5a26-109">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="a5a26-109">Prerequisites</span></span>

<span data-ttu-id="a5a26-110">Zainstaluj następujące oprogramowanie:</span><span class="sxs-lookup"><span data-stu-id="a5a26-110">Install the following software:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a5a26-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a5a26-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a5a26-112">[Visual Studio 2017 w wersji 15.7 lub nowszej](https://www.visualstudio.com/downloads/) za pomocą tych obciążeniach:</span><span class="sxs-lookup"><span data-stu-id="a5a26-112">[Visual Studio 2017 version 15.7 or later](https://www.visualstudio.com/downloads/) with these workloads:</span></span>
  * <span data-ttu-id="a5a26-113">**ASP.NET i tworzenie aplikacji internetowych** (w obszarze **sieć Web i chmura**)</span><span class="sxs-lookup"><span data-stu-id="a5a26-113">**ASP.NET and web development** (under **Web & Cloud**)</span></span>
  * <span data-ttu-id="a5a26-114">**Programowanie dla wielu platform .NET core** (w obszarze **inne zestawy narzędzi**)</span><span class="sxs-lookup"><span data-stu-id="a5a26-114">**.NET Core cross-platform development** (under **Other Toolsets**)</span></span>
* <span data-ttu-id="a5a26-115">[.NET core 2.1 SDK](https://www.microsoft.com/net/download/core).</span><span class="sxs-lookup"><span data-stu-id="a5a26-115">[.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core).</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="a5a26-116">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a5a26-116">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="a5a26-117">[.NET core 2.1 SDK](https://www.microsoft.com/net/download/core).</span><span class="sxs-lookup"><span data-stu-id="a5a26-117">[.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core).</span></span>

---

## <a name="create-a-new-project"></a><span data-ttu-id="a5a26-118">Tworzenie nowego projektu</span><span class="sxs-lookup"><span data-stu-id="a5a26-118">Create a new project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a5a26-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a5a26-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a5a26-120">Otwórz program Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="a5a26-120">Open Visual Studio 2017</span></span>
* <span data-ttu-id="a5a26-121">**Plik > Nowy > Projekt**</span><span class="sxs-lookup"><span data-stu-id="a5a26-121">**File > New > Project**</span></span>
* <span data-ttu-id="a5a26-122">Z menu po lewej stronie wybierz **zainstalowane > Visual C# > .NET Core**.</span><span class="sxs-lookup"><span data-stu-id="a5a26-122">From the left menu, select **Installed > Visual C# > .NET Core**.</span></span>
* <span data-ttu-id="a5a26-123">Wybierz **aplikacji sieci Web platformy ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="a5a26-123">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="a5a26-124">Wprowadź **EFGetStarted.AspNetCore.NewDb** nazwę i kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="a5a26-124">Enter **EFGetStarted.AspNetCore.NewDb** for the name and click **OK**.</span></span>
* <span data-ttu-id="a5a26-125">W **Nowa aplikacja internetowa ASP.NET Core** okno dialogowe:</span><span class="sxs-lookup"><span data-stu-id="a5a26-125">In the **New ASP.NET Core Web Application** dialog:</span></span>
  * <span data-ttu-id="a5a26-126">Upewnij się, że **platformy .NET Core** i **platformy ASP.NET Core 2.1** wybranych z listy rozwijanej</span><span class="sxs-lookup"><span data-stu-id="a5a26-126">Make sure that **.NET Core** and **ASP.NET Core 2.1** are selected in the drop-down lists</span></span>
  * <span data-ttu-id="a5a26-127">Wybierz **aplikacji sieci Web (Model-View-Controller)** szablonu projektu</span><span class="sxs-lookup"><span data-stu-id="a5a26-127">Select the **Web Application (Model-View-Controller)** project template</span></span>
  * <span data-ttu-id="a5a26-128">Upewnij się, że **uwierzytelniania** ustawiono **bez uwierzytelniania**</span><span class="sxs-lookup"><span data-stu-id="a5a26-128">Make sure that **Authentication** is set to **No Authentication**</span></span>
  * <span data-ttu-id="a5a26-129">Kliknij przycisk **OK**</span><span class="sxs-lookup"><span data-stu-id="a5a26-129">Click **OK**</span></span>

<span data-ttu-id="a5a26-130">Ostrzeżenie: Jeśli używasz **indywidualne konta użytkowników** zamiast **Brak** dla **uwierzytelniania** , a następnie na model Entity Framework Core zostanie dodany do projektu w `Models\IdentityModel.cs`.</span><span class="sxs-lookup"><span data-stu-id="a5a26-130">Warning: If you use **Individual User Accounts** instead of **None** for **Authentication** then an Entity Framework Core model will be added to the project in `Models\IdentityModel.cs`.</span></span> <span data-ttu-id="a5a26-131">Przy użyciu technik, z którego dowiesz się, w tym samouczku, można dodać drugi model lub rozszerzyć ten istniejący model zawiera Twoje klas jednostek.</span><span class="sxs-lookup"><span data-stu-id="a5a26-131">Using the techniques you learn in this tutorial, you can choose to add a second model, or extend this existing model to contain your entity classes.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="a5a26-132">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a5a26-132">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="a5a26-133">Uruchom następujące polecenie, aby utworzyć projekt programu MVC:</span><span class="sxs-lookup"><span data-stu-id="a5a26-133">Run the following command to create an MVC project:</span></span>

   ```cli
   dotnet new mvc -n EFGetStarted.AspNetCore.NewDb
   ```
* <span data-ttu-id="a5a26-134">Przejdź do katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="a5a26-134">Change to the project directory.</span></span> <span data-ttu-id="a5a26-135">Kolejne polecenia, które możesz wprowadzić należy uruchamiać dla nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="a5a26-135">The next commands you enter need to run against the new project.</span></span>

   ```cli
   cd EFGetStarted.AspNetCore.NewDb
   ```
---

## <a name="install-entity-framework-core"></a><span data-ttu-id="a5a26-136">Instalowanie platformy Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="a5a26-136">Install Entity Framework Core</span></span>

<span data-ttu-id="a5a26-137">Aby zainstalować programu EF Core, należy zainstalować pakiet dla dostawców bazy danych programu EF Core, który ma pod kątem.</span><span class="sxs-lookup"><span data-stu-id="a5a26-137">To install EF Core, you install the package for the EF Core database provider(s) you want to target.</span></span> <span data-ttu-id="a5a26-138">Aby uzyskać listę dostępnych dostawców, zobacz [dostawcy baz danych](../../providers/index.md).</span><span class="sxs-lookup"><span data-stu-id="a5a26-138">For a list of available providers, see [Database Providers](../../providers/index.md).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a5a26-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a5a26-139">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a5a26-140">Na potrzeby tego samouczka nie trzeba zainstalować pakiet dostawcy, ponieważ w tym samouczku użyto programu SQL Server.</span><span class="sxs-lookup"><span data-stu-id="a5a26-140">For this tutorial, you don't have to install a provider package because the tutorial uses SQL Server.</span></span> <span data-ttu-id="a5a26-141">Pakiet dostawcy programu SQL Server znajduje się w [meta Microsoft.aspnetcore.all Microsoft.AspnetCore.App](https://docs.microsoft.com/aspnet/core/fundamentals/metapackage-app?view=aspnetcore-2.1).</span><span class="sxs-lookup"><span data-stu-id="a5a26-141">The SQL Server provider package is included in the [Microsoft.AspnetCore.App metapackage](https://docs.microsoft.com/aspnet/core/fundamentals/metapackage-app?view=aspnetcore-2.1).</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="a5a26-142">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a5a26-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="a5a26-143">Ten samouczek używa bazy danych SQLite, ponieważ jest uruchamiana na wszystkich platformach, które obsługuje platformy .NET Core.</span><span class="sxs-lookup"><span data-stu-id="a5a26-143">This tutorial uses SQLite because it runs on all platforms that .NET Core supports.</span></span>

* <span data-ttu-id="a5a26-144">Uruchom następujące polecenie, aby zainstalować dostawcę bazy danych SQLite:</span><span class="sxs-lookup"><span data-stu-id="a5a26-144">Run the following command to install the SQLite provider:</span></span>

   ```cli
   dotnet add package Microsoft.EntityFrameworkCore.Sqlite
   ```

---

## <a name="create-the-model"></a><span data-ttu-id="a5a26-145">Tworzenie modelu</span><span class="sxs-lookup"><span data-stu-id="a5a26-145">Create the model</span></span>

<span data-ttu-id="a5a26-146">Zdefiniuj klasę kontekstu i klas jednostek, które tworzą model.</span><span class="sxs-lookup"><span data-stu-id="a5a26-146">Define a context class and entity classes that make up the model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a5a26-147">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a5a26-147">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a5a26-148">Kliknij prawym przyciskiem myszy **modeli** i wybierz polecenie **Dodaj > klasa**.</span><span class="sxs-lookup"><span data-stu-id="a5a26-148">Right-click on the **Models** folder and select **Add > Class**.</span></span>
* <span data-ttu-id="a5a26-149">Wprowadź **Model.cs** jako nazwę i kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="a5a26-149">Enter **Model.cs** as the name and click **OK**.</span></span>
* <span data-ttu-id="a5a26-150">Zastąp zawartość pliku następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="a5a26-150">Replace the contents of the file with the following code:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Models/Model.cs)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="a5a26-151">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a5a26-151">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="a5a26-152">W **modeli** Utwórz folder **Model.cs** następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="a5a26-152">In the **Models** folder create **Model.cs** with the following code:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite/Models/Model.cs)]

---

<span data-ttu-id="a5a26-153">Zazwyczaj jest aplikacji produkcyjnej przełączyć każda klasa w oddzielnym pliku.</span><span class="sxs-lookup"><span data-stu-id="a5a26-153">A production app would typically put each class in a separate file.</span></span> <span data-ttu-id="a5a26-154">Dla uproszczenia ten samouczek umieszcza w ramach tych zajęć w jednym pliku.</span><span class="sxs-lookup"><span data-stu-id="a5a26-154">For the sake of simplicity, this tutorial puts these classes in one file.</span></span>

## <a name="register-the-context-with-dependency-injection"></a><span data-ttu-id="a5a26-155">Zarejestrowanie kontekście wstrzykiwanie zależności</span><span class="sxs-lookup"><span data-stu-id="a5a26-155">Register the context with dependency injection</span></span>

<span data-ttu-id="a5a26-156">Usługi (takie jak `BloggingContext`) zostały zarejestrowane przy użyciu [wstrzykiwanie zależności](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html) podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a5a26-156">Services (such as `BloggingContext`) are registered with [dependency injection](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html) during application startup.</span></span> <span data-ttu-id="a5a26-157">Składniki, które wymagają tych usług, (na przykład kontrolerów MVC) znajdują się tych usług za pomocą właściwości lub parametry konstruktora.</span><span class="sxs-lookup"><span data-stu-id="a5a26-157">Components that require these services (such as MVC controllers) are provided these services via constructor parameters or properties.</span></span>

<span data-ttu-id="a5a26-158">Zapewnienie `BloggingContext` dostępne dla kontrolerów MVC, zarejestruj go jako usługę.</span><span class="sxs-lookup"><span data-stu-id="a5a26-158">To make `BloggingContext` available to MVC controllers, register it as a service.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a5a26-159">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a5a26-159">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a5a26-160">W **Startup.cs** Dodaj następujący kod `using` instrukcji:</span><span class="sxs-lookup"><span data-stu-id="a5a26-160">In **Startup.cs** add the following `using` statements:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Startup.cs#AddedUsings)]

* <span data-ttu-id="a5a26-161">Dodaj następujący wyróżniony kod do `ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="a5a26-161">Add the following highlighted code to the `ConfigureServices` method:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Startup.cs?name=ConfigureServices&highlight=12-14)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="a5a26-162">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a5a26-162">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="a5a26-163">W **Startup.cs** Dodaj następujący kod `using` instrukcji:</span><span class="sxs-lookup"><span data-stu-id="a5a26-163">In **Startup.cs** add the following `using` statements:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite/Startup.cs#AddedUsings)]

* <span data-ttu-id="a5a26-164">Dodaj następujący wyróżniony kod do `ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="a5a26-164">Add the following highlighted code to the `ConfigureServices` method:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite/Startup.cs?name=ConfigureServices&highlight=12-14)]

---

<span data-ttu-id="a5a26-165">Aplikacji produkcyjnej zazwyczaj umieścić ciąg połączenia w zmiennej środowisku lub pliku konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="a5a26-165">A production app would typically put the connection string in a configuration file or environment variable.</span></span> <span data-ttu-id="a5a26-166">Dla uproszczenia w tym samouczku zdefiniowano go w kodzie.</span><span class="sxs-lookup"><span data-stu-id="a5a26-166">For the sake of simplicity, this tutorial defines it in code.</span></span> <span data-ttu-id="a5a26-167">Zobacz [parametry połączenia](../../miscellaneous/connection-strings.md) Aby uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="a5a26-167">See [Connection Strings](../../miscellaneous/connection-strings.md) for more information.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="a5a26-168">Tworzenie bazy danych</span><span class="sxs-lookup"><span data-stu-id="a5a26-168">Create the database</span></span>

<span data-ttu-id="a5a26-169">Następujące kroki użycia [migracje](xref:core/managing-schemas/migrations/index) utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="a5a26-169">The following steps use [migrations](xref:core/managing-schemas/migrations/index) to create a database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a5a26-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a5a26-170">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a5a26-171">**Narzędzia > Menedżer pakietów NuGet > Konsola Menedżera pakietów**</span><span class="sxs-lookup"><span data-stu-id="a5a26-171">**Tools > NuGet Package Manager > Package Manager Console**</span></span>
* <span data-ttu-id="a5a26-172">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="a5a26-172">Run the following commands:</span></span>

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

  <span data-ttu-id="a5a26-173">Jeśli zostanie wyświetlony błąd wskazujący `The term 'add-migration' is not recognized as the name of a cmdlet`, zamknij i otwórz ponownie program Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a5a26-173">If you get an error stating `The term 'add-migration' is not recognized as the name of a cmdlet`, close and reopen Visual Studio.</span></span>

  <span data-ttu-id="a5a26-174">`Add-Migration` Polecenia scaffolds migracji, aby utworzyć początkowy zestaw tabel dla modelu.</span><span class="sxs-lookup"><span data-stu-id="a5a26-174">The `Add-Migration` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="a5a26-175">`Update-Database` Polecenie tworzy bazę danych i dotyczy nowej migracji.</span><span class="sxs-lookup"><span data-stu-id="a5a26-175">The `Update-Database` command creates the database and applies the new migration to it.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="a5a26-176">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a5a26-176">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="a5a26-177">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="a5a26-177">Run the following commands:</span></span>

  ```cli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

  <span data-ttu-id="a5a26-178">`migrations` Polecenia scaffolds migracji, aby utworzyć początkowy zestaw tabel dla modelu.</span><span class="sxs-lookup"><span data-stu-id="a5a26-178">The `migrations` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="a5a26-179">`database update` Polecenie tworzy bazę danych i dotyczy nowej migracji.</span><span class="sxs-lookup"><span data-stu-id="a5a26-179">The `database update` command creates the database and applies the new migration to it.</span></span>

---

## <a name="create-a-controller"></a><span data-ttu-id="a5a26-180">Tworzenie kontrolera</span><span class="sxs-lookup"><span data-stu-id="a5a26-180">Create a controller</span></span>

<span data-ttu-id="a5a26-181">Tworzenia szkieletu kontrolera i widoki dla `Blog` jednostki.</span><span class="sxs-lookup"><span data-stu-id="a5a26-181">Scaffold a controller and views for the `Blog` entity.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a5a26-182">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a5a26-182">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a5a26-183">Kliknij prawym przyciskiem myszy **kontrolerów** folderu w **Eksploratora rozwiązań** i wybierz **Dodaj > kontrolera**.</span><span class="sxs-lookup"><span data-stu-id="a5a26-183">Right-click on the **Controllers** folder in **Solution Explorer** and select **Add > Controller**.</span></span>
* <span data-ttu-id="a5a26-184">Wybierz **kontroler MVC z widokami używający narzędzia Entity Framework** i kliknij przycisk **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="a5a26-184">Select **MVC Controller with views, using Entity Framework** and click **Add**.</span></span>
* <span data-ttu-id="a5a26-185">Ustaw **klasa modelu** do **Blog** i **klasa kontekstu danych** do **BloggingContext**.</span><span class="sxs-lookup"><span data-stu-id="a5a26-185">Set **Model class** to **Blog** and **Data context class** to **BloggingContext**.</span></span>
* <span data-ttu-id="a5a26-186">Kliknij przycisk **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="a5a26-186">Click **Add**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="a5a26-187">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a5a26-187">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="a5a26-188">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="a5a26-188">Run the following commands:</span></span>

  ```cli
  dotnet tool install -g dotnet-aspnet-codegenerator
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet restore
  dotnet aspnet-codegenerator controller -name BlogsController -m Blog -dc BloggingContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  <span data-ttu-id="a5a26-189">`tool install` i `add package` polecenia instalacji narzędzia, które można tworzenia szkieletu, widoków i kontrolerów.</span><span class="sxs-lookup"><span data-stu-id="a5a26-189">The `tool install` and `add package` commands install the tooling that can scaffold controllers and views.</span></span> <span data-ttu-id="a5a26-190">`restore` Polecenia zapewnia, że wszystkie pakiety projektu zostaną pobrane, a `aspnet-codegenerator` polecenie powoduje szkieletu.</span><span class="sxs-lookup"><span data-stu-id="a5a26-190">The `restore` command ensures that all of the project's packages are downloaded, and the `aspnet-codegenerator` command does the scaffolding.</span></span>
---

<span data-ttu-id="a5a26-191">Aparat tworzenia szkieletów tworzy następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="a5a26-191">The scaffolding engine creates the following files:</span></span>

* <span data-ttu-id="a5a26-192">Kontroler (*Controllers/BlogsController.cs*)</span><span class="sxs-lookup"><span data-stu-id="a5a26-192">A controller (*Controllers/BlogsController.cs*)</span></span>
* <span data-ttu-id="a5a26-193">Widokami razor dla stron Create, Delete, szczegółowe informacje, edycji i indeksu (_Views/Blogs/\*.cshtml_)</span><span class="sxs-lookup"><span data-stu-id="a5a26-193">Razor views for Create, Delete, Details, Edit, and Index pages (_Views/Blogs/\*.cshtml_)</span></span>

## <a name="run-the-application"></a><span data-ttu-id="a5a26-194">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="a5a26-194">Run the application</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a5a26-195">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a5a26-195">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a5a26-196">**Debugowanie** > **Uruchom bez debugowania**</span><span class="sxs-lookup"><span data-stu-id="a5a26-196">**Debug** > **Start Without Debugging**</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="a5a26-197">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a5a26-197">.NET Core CLI</span></span>](#tab/netcore-cli)

```cli
dotnet run
```
---

* <span data-ttu-id="a5a26-198">Przejdź do `/Blogs`</span><span class="sxs-lookup"><span data-stu-id="a5a26-198">Navigate to `/Blogs`</span></span>

* <span data-ttu-id="a5a26-199">Użyj **Utwórz nowy** link, aby utworzyć wpisy na blogu.</span><span class="sxs-lookup"><span data-stu-id="a5a26-199">Use the **Create New** link to create some blog entries.</span></span>

  ![Tworzenie strony](_static/create.png)

* <span data-ttu-id="a5a26-201">Test **szczegóły**, **Edytuj**, i **Usuń** łącza.</span><span class="sxs-lookup"><span data-stu-id="a5a26-201">Test the **Details**, **Edit**, and **Delete** links.</span></span>

  ![Strona indeksu](_static/index-new-db.png)

## <a name="additional-resources"></a><span data-ttu-id="a5a26-203">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="a5a26-203">Additional Resources</span></span>

* [<span data-ttu-id="a5a26-204">Samouczek: Rozpoczynanie pracy z programem EF Core na platformie .NET Core za pomocą nowej bazy danych przy użyciu systemu SQLite</span><span class="sxs-lookup"><span data-stu-id="a5a26-204">Tutorial: Get started with EF Core on .NET Core with a new database using SQLite</span></span>](xref:core/get-started/netcore/new-db-sqlite)
* [<span data-ttu-id="a5a26-205">Samouczek: Rozpoczynanie pracy ze stronami Razor w programie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a5a26-205">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="a5a26-206">Samouczek: Strony razor za pomocą platformy Entity Framework Core w programie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a5a26-206">Tutorial: Razor Pages with Entity Framework Core in ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/data/ef-rp/intro)
