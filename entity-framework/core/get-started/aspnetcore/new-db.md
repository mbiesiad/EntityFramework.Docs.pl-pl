---
title: Wprowadzenie do platformy ASP.NET Core — Nowa baza danych — EF Core
author: rick-anderson
ms.author: riande
ms.date: 08/03/2018
ms.assetid: e153627f-f132-4c11-b13c-6c9a607addce
uid: core/get-started/aspnetcore/new-db
ms.openlocfilehash: 25e5a683acf4bbed0b978cc6a80f1b50a0b64ca1
ms.sourcegitcommit: eb8359b7ab3b0a1a08522faf67b703a00ecdcefd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/21/2019
ms.locfileid: "58319182"
---
# <a name="getting-started-with-ef-core-on-aspnet-core-with-a-new-database"></a><span data-ttu-id="4c581-102">Wprowadzenie do programu EF Core programu ASP.NET Core przy użyciu nowej bazy danych</span><span class="sxs-lookup"><span data-stu-id="4c581-102">Getting Started with EF Core on ASP.NET Core with a New database</span></span>

<span data-ttu-id="4c581-103">W tym samouczku utworzysz aplikację ASP.NET Core MVC, który wykonuje podstawowe dane dostępu przy użyciu platformy Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="4c581-103">In this tutorial, you build an ASP.NET Core MVC application that performs basic data access using Entity Framework Core.</span></span> <span data-ttu-id="4c581-104">W samouczku migracje utworzyć bazę danych z modelu danych.</span><span class="sxs-lookup"><span data-stu-id="4c581-104">The tutorial uses migrations to create the database from the data model.</span></span>

<span data-ttu-id="4c581-105">Za pomocą programu Visual Studio 2017 na Windows lub za pomocą interfejsu wiersza polecenia platformy .NET Core w systemie Windows, macOS lub Linux, można wykonać kroki samouczka.</span><span class="sxs-lookup"><span data-stu-id="4c581-105">You can follow the tutorial by using Visual Studio 2017 on Windows, or by using the .NET Core CLI on Windows, macOS, or Linux.</span></span>

<span data-ttu-id="4c581-106">Wyświetl przykład w tym artykule w witrynie GitHub:</span><span class="sxs-lookup"><span data-stu-id="4c581-106">View this article's sample on GitHub:</span></span>
* [<span data-ttu-id="4c581-107">Program Visual Studio 2017 z programem SQL Server</span><span class="sxs-lookup"><span data-stu-id="4c581-107">Visual Studio 2017 with SQL Server</span></span>](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb)
* <span data-ttu-id="4c581-108">[.NET core interfejsu wiersza polecenia za pomocą SQLite](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite).</span><span class="sxs-lookup"><span data-stu-id="4c581-108">[.NET Core CLI with SQLite](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4c581-109">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="4c581-109">Prerequisites</span></span>

<span data-ttu-id="4c581-110">Zainstaluj następujące oprogramowanie:</span><span class="sxs-lookup"><span data-stu-id="4c581-110">Install the following software:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4c581-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4c581-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4c581-112">[Visual Studio 2017 w wersji 15.7 lub nowszej](https://www.visualstudio.com/downloads/) za pomocą tych obciążeniach:</span><span class="sxs-lookup"><span data-stu-id="4c581-112">[Visual Studio 2017 version 15.7 or later](https://www.visualstudio.com/downloads/) with these workloads:</span></span>
  * <span data-ttu-id="4c581-113">**ASP.NET i tworzenie aplikacji internetowych** (w obszarze **sieć Web i chmura**)</span><span class="sxs-lookup"><span data-stu-id="4c581-113">**ASP.NET and web development** (under **Web & Cloud**)</span></span>
  * <span data-ttu-id="4c581-114">**Programowanie dla wielu platform .NET core** (w obszarze **inne zestawy narzędzi**)</span><span class="sxs-lookup"><span data-stu-id="4c581-114">**.NET Core cross-platform development** (under **Other Toolsets**)</span></span>
* <span data-ttu-id="4c581-115">[.NET core 2.1 SDK](https://www.microsoft.com/net/download/core).</span><span class="sxs-lookup"><span data-stu-id="4c581-115">[.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core).</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="4c581-116">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4c581-116">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="4c581-117">[.NET core 2.1 SDK](https://www.microsoft.com/net/download/core).</span><span class="sxs-lookup"><span data-stu-id="4c581-117">[.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core).</span></span>

---

## <a name="create-a-new-project"></a><span data-ttu-id="4c581-118">Tworzenie nowego projektu</span><span class="sxs-lookup"><span data-stu-id="4c581-118">Create a new project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4c581-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4c581-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4c581-120">Open Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="4c581-120">Open Visual Studio 2017</span></span>
* <span data-ttu-id="4c581-121">**Plik > Nowy > Projekt**</span><span class="sxs-lookup"><span data-stu-id="4c581-121">**File > New > Project**</span></span>
* <span data-ttu-id="4c581-122">Z menu po lewej stronie wybierz **zainstalowane > Visual C# > .NET Core**.</span><span class="sxs-lookup"><span data-stu-id="4c581-122">From the left menu, select **Installed > Visual C# > .NET Core**.</span></span>
* <span data-ttu-id="4c581-123">Wybierz **aplikacji sieci Web platformy ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="4c581-123">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="4c581-124">Wprowadź **EFGetStarted.AspNetCore.NewDb** nazwę i kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="4c581-124">Enter **EFGetStarted.AspNetCore.NewDb** for the name and click **OK**.</span></span>
* <span data-ttu-id="4c581-125">W **Nowa aplikacja internetowa ASP.NET Core** okno dialogowe:</span><span class="sxs-lookup"><span data-stu-id="4c581-125">In the **New ASP.NET Core Web Application** dialog:</span></span>
  * <span data-ttu-id="4c581-126">Upewnij się, że **platformy .NET Core** i **platformy ASP.NET Core 2.1** wybranych z listy rozwijanej</span><span class="sxs-lookup"><span data-stu-id="4c581-126">Make sure that **.NET Core** and **ASP.NET Core 2.1** are selected in the drop-down lists</span></span>
  * <span data-ttu-id="4c581-127">Wybierz **aplikacji sieci Web (Model-View-Controller)** szablonu projektu</span><span class="sxs-lookup"><span data-stu-id="4c581-127">Select the **Web Application (Model-View-Controller)** project template</span></span>
  * <span data-ttu-id="4c581-128">Upewnij się, że **uwierzytelniania** ustawiono **bez uwierzytelniania**</span><span class="sxs-lookup"><span data-stu-id="4c581-128">Make sure that **Authentication** is set to **No Authentication**</span></span>
  * <span data-ttu-id="4c581-129">Kliknij przycisk **OK**</span><span class="sxs-lookup"><span data-stu-id="4c581-129">Click **OK**</span></span>

<span data-ttu-id="4c581-130">Ostrzeżenie: Jeśli używasz **indywidualne konta użytkowników** zamiast **Brak** dla **uwierzytelniania** , a następnie na model Entity Framework Core zostanie dodany do projektu w `Models\IdentityModel.cs`.</span><span class="sxs-lookup"><span data-stu-id="4c581-130">Warning: If you use **Individual User Accounts** instead of **None** for **Authentication** then an Entity Framework Core model will be added to the project in `Models\IdentityModel.cs`.</span></span> <span data-ttu-id="4c581-131">Przy użyciu technik, z którego dowiesz się, w tym samouczku, można dodać drugi model lub rozszerzyć ten istniejący model zawiera Twoje klas jednostek.</span><span class="sxs-lookup"><span data-stu-id="4c581-131">Using the techniques you learn in this tutorial, you can choose to add a second model, or extend this existing model to contain your entity classes.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="4c581-132">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4c581-132">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="4c581-133">Uruchom następujące polecenie, aby utworzyć projekt programu MVC:</span><span class="sxs-lookup"><span data-stu-id="4c581-133">Run the following command to create an MVC project:</span></span>

   ```cli
   dotnet new mvc -n EFGetStarted.AspNetCore.NewDb
   ```
* <span data-ttu-id="4c581-134">Przejdź do katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="4c581-134">Change to the project directory.</span></span> <span data-ttu-id="4c581-135">Kolejne polecenia, które możesz wprowadzić należy uruchamiać dla nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="4c581-135">The next commands you enter need to run against the new project.</span></span>

   ```cli
   cd EFGetStarted.AspNetCore.NewDb
   ```
---

## <a name="install-entity-framework-core"></a><span data-ttu-id="4c581-136">Instalowanie platformy Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="4c581-136">Install Entity Framework Core</span></span>

<span data-ttu-id="4c581-137">Aby zainstalować programu EF Core, należy zainstalować pakiet dla dostawców bazy danych programu EF Core, który ma pod kątem.</span><span class="sxs-lookup"><span data-stu-id="4c581-137">To install EF Core, you install the package for the EF Core database provider(s) you want to target.</span></span> <span data-ttu-id="4c581-138">Aby uzyskać listę dostępnych dostawców, zobacz [dostawcy baz danych](../../providers/index.md).</span><span class="sxs-lookup"><span data-stu-id="4c581-138">For a list of available providers, see [Database Providers](../../providers/index.md).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4c581-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4c581-139">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4c581-140">Na potrzeby tego samouczka nie trzeba zainstalować pakiet dostawcy, ponieważ w tym samouczku użyto programu SQL Server.</span><span class="sxs-lookup"><span data-stu-id="4c581-140">For this tutorial, you don't have to install a provider package because the tutorial uses SQL Server.</span></span> <span data-ttu-id="4c581-141">Pakiet dostawcy programu SQL Server znajduje się w [meta Microsoft.aspnetcore.all Microsoft.AspnetCore.App](https://docs.microsoft.com/aspnet/core/fundamentals/metapackage-app?view=aspnetcore-2.1).</span><span class="sxs-lookup"><span data-stu-id="4c581-141">The SQL Server provider package is included in the [Microsoft.AspnetCore.App metapackage](https://docs.microsoft.com/aspnet/core/fundamentals/metapackage-app?view=aspnetcore-2.1).</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="4c581-142">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4c581-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="4c581-143">Ten samouczek używa bazy danych SQLite, ponieważ jest uruchamiana na wszystkich platformach, które obsługuje platformy .NET Core.</span><span class="sxs-lookup"><span data-stu-id="4c581-143">This tutorial uses SQLite because it runs on all platforms that .NET Core supports.</span></span>

* <span data-ttu-id="4c581-144">Uruchom następujące polecenie, aby zainstalować dostawcę bazy danych SQLite:</span><span class="sxs-lookup"><span data-stu-id="4c581-144">Run the following command to install the SQLite provider:</span></span>

   ```cli
   dotnet add package Microsoft.EntityFrameworkCore.Sqlite
   ```

---

## <a name="create-the-model"></a><span data-ttu-id="4c581-145">Tworzenie modelu</span><span class="sxs-lookup"><span data-stu-id="4c581-145">Create the model</span></span>

<span data-ttu-id="4c581-146">Zdefiniuj klasę kontekstu i klas jednostek, które tworzą model.</span><span class="sxs-lookup"><span data-stu-id="4c581-146">Define a context class and entity classes that make up the model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4c581-147">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4c581-147">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4c581-148">Kliknij prawym przyciskiem myszy **modeli** i wybierz polecenie **Dodaj > klasa**.</span><span class="sxs-lookup"><span data-stu-id="4c581-148">Right-click on the **Models** folder and select **Add > Class**.</span></span>
* <span data-ttu-id="4c581-149">Wprowadź **Model.cs** jako nazwę i kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="4c581-149">Enter **Model.cs** as the name and click **OK**.</span></span>
* <span data-ttu-id="4c581-150">Zastąp zawartość pliku następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="4c581-150">Replace the contents of the file with the following code:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Models/Model.cs)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="4c581-151">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4c581-151">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="4c581-152">W **modeli** Utwórz folder **Model.cs** następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="4c581-152">In the **Models** folder create **Model.cs** with the following code:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite/Models/Model.cs)]

---

<span data-ttu-id="4c581-153">Zazwyczaj jest aplikacji produkcyjnej przełączyć każda klasa w oddzielnym pliku.</span><span class="sxs-lookup"><span data-stu-id="4c581-153">A production app would typically put each class in a separate file.</span></span> <span data-ttu-id="4c581-154">Dla uproszczenia ten samouczek umieszcza w ramach tych zajęć w jednym pliku.</span><span class="sxs-lookup"><span data-stu-id="4c581-154">For the sake of simplicity, this tutorial puts these classes in one file.</span></span>

## <a name="register-the-context-with-dependency-injection"></a><span data-ttu-id="4c581-155">Zarejestrowanie kontekście wstrzykiwanie zależności</span><span class="sxs-lookup"><span data-stu-id="4c581-155">Register the context with dependency injection</span></span>

<span data-ttu-id="4c581-156">Aby `BloggingContext` dostępne dla kontrolerów MVC, zarejestruj go jako usługę w `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="4c581-156">To make `BloggingContext` available to MVC controllers, register it as a service in `Startup.cs`.</span></span>

<span data-ttu-id="4c581-157">Usługi (takie jak `BloggingContext`) zostały zarejestrowane przy użyciu [wstrzykiwanie zależności](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html) podczas uruchamiania aplikacji, aby dostarczony automatycznie do składników korzystających z usług (na przykład kontrolerów MVC) przy użyciu konstruktora Parametry i właściwości.</span><span class="sxs-lookup"><span data-stu-id="4c581-157">Services (such as `BloggingContext`) are registered with [dependency injection](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html) during application startup so that they can be provided automatically to components that consume services (such as MVC controllers) via constructor parameters and properties.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4c581-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4c581-158">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4c581-159">W **Startup.cs** Dodaj następujący kod `using` instrukcji:</span><span class="sxs-lookup"><span data-stu-id="4c581-159">In **Startup.cs** add the following `using` statements:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Startup.cs#AddedUsings)]

* <span data-ttu-id="4c581-160">Dodaj następujący wyróżniony kod do `ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="4c581-160">Add the following highlighted code to the `ConfigureServices` method:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Startup.cs?name=ConfigureServices&highlight=12-14)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="4c581-161">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4c581-161">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="4c581-162">W **Startup.cs** Dodaj następujący kod `using` instrukcji:</span><span class="sxs-lookup"><span data-stu-id="4c581-162">In **Startup.cs** add the following `using` statements:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite/Startup.cs#AddedUsings)]

* <span data-ttu-id="4c581-163">Dodaj następujący wyróżniony kod do `ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="4c581-163">Add the following highlighted code to the `ConfigureServices` method:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite/Startup.cs?name=ConfigureServices&highlight=12-14)]

---

<span data-ttu-id="4c581-164">Aplikacji produkcyjnej zazwyczaj umieścić ciąg połączenia w zmiennej środowisku lub pliku konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="4c581-164">A production app would typically put the connection string in a configuration file or environment variable.</span></span> <span data-ttu-id="4c581-165">Dla uproszczenia w tym samouczku zdefiniowano go w kodzie.</span><span class="sxs-lookup"><span data-stu-id="4c581-165">For the sake of simplicity, this tutorial defines it in code.</span></span> <span data-ttu-id="4c581-166">Zobacz [parametry połączenia](../../miscellaneous/connection-strings.md) Aby uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="4c581-166">See [Connection Strings](../../miscellaneous/connection-strings.md) for more information.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="4c581-167">Tworzenie bazy danych</span><span class="sxs-lookup"><span data-stu-id="4c581-167">Create the database</span></span>

<span data-ttu-id="4c581-168">Następujące kroki użycia [migracje](xref:core/managing-schemas/migrations/index) utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="4c581-168">The following steps use [migrations](xref:core/managing-schemas/migrations/index) to create a database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4c581-169">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4c581-169">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4c581-170">**Narzędzia > Menedżer pakietów NuGet > Konsola Menedżera pakietów**</span><span class="sxs-lookup"><span data-stu-id="4c581-170">**Tools > NuGet Package Manager > Package Manager Console**</span></span>
* <span data-ttu-id="4c581-171">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="4c581-171">Run the following commands:</span></span>

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

  <span data-ttu-id="4c581-172">Jeśli zostanie wyświetlony błąd wskazujący `The term 'add-migration' is not recognized as the name of a cmdlet`, zamknij i otwórz ponownie program Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="4c581-172">If you get an error stating `The term 'add-migration' is not recognized as the name of a cmdlet`, close and reopen Visual Studio.</span></span>

  <span data-ttu-id="4c581-173">`Add-Migration` Polecenia scaffolds migracji, aby utworzyć początkowy zestaw tabel dla modelu.</span><span class="sxs-lookup"><span data-stu-id="4c581-173">The `Add-Migration` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="4c581-174">`Update-Database` Polecenie tworzy bazę danych i dotyczy nowej migracji.</span><span class="sxs-lookup"><span data-stu-id="4c581-174">The `Update-Database` command creates the database and applies the new migration to it.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="4c581-175">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4c581-175">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="4c581-176">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="4c581-176">Run the following commands:</span></span>

  ```cli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

  <span data-ttu-id="4c581-177">`migrations` Polecenia scaffolds migracji, aby utworzyć początkowy zestaw tabel dla modelu.</span><span class="sxs-lookup"><span data-stu-id="4c581-177">The `migrations` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="4c581-178">`database update` Polecenie tworzy bazę danych i dotyczy nowej migracji.</span><span class="sxs-lookup"><span data-stu-id="4c581-178">The `database update` command creates the database and applies the new migration to it.</span></span>

---

## <a name="create-a-controller"></a><span data-ttu-id="4c581-179">Tworzenie kontrolera</span><span class="sxs-lookup"><span data-stu-id="4c581-179">Create a controller</span></span>

<span data-ttu-id="4c581-180">Tworzenia szkieletu kontrolera i widoki dla `Blog` jednostki.</span><span class="sxs-lookup"><span data-stu-id="4c581-180">Scaffold a controller and views for the `Blog` entity.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4c581-181">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4c581-181">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4c581-182">Kliknij prawym przyciskiem myszy **kontrolerów** folderu w **Eksploratora rozwiązań** i wybierz **Dodaj > kontrolera**.</span><span class="sxs-lookup"><span data-stu-id="4c581-182">Right-click on the **Controllers** folder in **Solution Explorer** and select **Add > Controller**.</span></span>
* <span data-ttu-id="4c581-183">Wybierz **kontroler MVC z widokami używający narzędzia Entity Framework** i kliknij przycisk **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="4c581-183">Select **MVC Controller with views, using Entity Framework** and click **Add**.</span></span>
* <span data-ttu-id="4c581-184">Ustaw **klasa modelu** do **Blog** i **klasa kontekstu danych** do **BloggingContext**.</span><span class="sxs-lookup"><span data-stu-id="4c581-184">Set **Model class** to **Blog** and **Data context class** to **BloggingContext**.</span></span>
* <span data-ttu-id="4c581-185">Kliknij przycisk **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="4c581-185">Click **Add**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="4c581-186">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4c581-186">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="4c581-187">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="4c581-187">Run the following commands:</span></span>

  ```cli
  dotnet tool install -g dotnet-aspnet-codegenerator
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet restore
  dotnet aspnet-codegenerator controller -name BlogsController -m Blog -dc BloggingContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  <span data-ttu-id="4c581-188">`tool install` i `add package` polecenia instalacji narzędzia, które można tworzenia szkieletu, widoków i kontrolerów.</span><span class="sxs-lookup"><span data-stu-id="4c581-188">The `tool install` and `add package` commands install the tooling that can scaffold controllers and views.</span></span> <span data-ttu-id="4c581-189">`restore` Polecenia zapewnia, że wszystkie pakiety projektu zostaną pobrane, a `aspnet-codegenerator` polecenie powoduje szkieletu.</span><span class="sxs-lookup"><span data-stu-id="4c581-189">The `restore` command ensures that all of the project's packages are downloaded, and the `aspnet-codegenerator` command does the scaffolding.</span></span>
---

<span data-ttu-id="4c581-190">Aparat tworzenia szkieletów tworzy następujące pliki:</span><span class="sxs-lookup"><span data-stu-id="4c581-190">The scaffolding engine creates the following files:</span></span>

* <span data-ttu-id="4c581-191">Kontroler (*Controllers/BlogsController.cs*)</span><span class="sxs-lookup"><span data-stu-id="4c581-191">A controller (*Controllers/BlogsController.cs*)</span></span>
* <span data-ttu-id="4c581-192">Widokami razor dla stron Create, Delete, szczegółowe informacje, edycji i indeksu (_Views/Blogs/\*.cshtml_)</span><span class="sxs-lookup"><span data-stu-id="4c581-192">Razor views for Create, Delete, Details, Edit, and Index pages (_Views/Blogs/\*.cshtml_)</span></span>

## <a name="run-the-application"></a><span data-ttu-id="4c581-193">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="4c581-193">Run the application</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4c581-194">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4c581-194">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4c581-195">**Debugowanie** > **Uruchom bez debugowania**</span><span class="sxs-lookup"><span data-stu-id="4c581-195">**Debug** > **Start Without Debugging**</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="4c581-196">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4c581-196">.NET Core CLI</span></span>](#tab/netcore-cli)

```cli
dotnet run
```
---

* <span data-ttu-id="4c581-197">Przejdź do `/Blogs`</span><span class="sxs-lookup"><span data-stu-id="4c581-197">Navigate to `/Blogs`</span></span>

* <span data-ttu-id="4c581-198">Użyj **Utwórz nowy** link, aby utworzyć wpisy na blogu.</span><span class="sxs-lookup"><span data-stu-id="4c581-198">Use the **Create New** link to create some blog entries.</span></span>

  ![Tworzenie strony](_static/create.png)

* <span data-ttu-id="4c581-200">Test **szczegóły**, **Edytuj**, i **Usuń** łącza.</span><span class="sxs-lookup"><span data-stu-id="4c581-200">Test the **Details**, **Edit**, and **Delete** links.</span></span>

  ![Strona indeksu](_static/index-new-db.png)

## <a name="additional-resources"></a><span data-ttu-id="4c581-202">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="4c581-202">Additional Resources</span></span>

* [<span data-ttu-id="4c581-203">Samouczek: Rozpoczynanie pracy z programem EF Core na platformie .NET Core za pomocą nowej bazy danych przy użyciu systemu SQLite</span><span class="sxs-lookup"><span data-stu-id="4c581-203">Tutorial: Get started with EF Core on .NET Core with a new database using SQLite</span></span>](xref:core/get-started/netcore/new-db-sqlite)
* [<span data-ttu-id="4c581-204">Samouczek: Rozpoczynanie pracy ze stronami Razor w programie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4c581-204">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="4c581-205">Samouczek: Strony razor za pomocą platformy Entity Framework Core w programie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4c581-205">Tutorial: Razor Pages with Entity Framework Core in ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/data/ef-rp/intro)
