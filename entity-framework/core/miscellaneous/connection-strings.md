---
title: Parametry połączenia — EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: aeb0f5f8-b212-4f89-ae83-c642a5190ba0
uid: core/miscellaneous/connection-strings
ms.openlocfilehash: e955e93723fc371170641b0b3209cca014ef1c26
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238154"
---
# <a name="connection-strings"></a><span data-ttu-id="3c73d-102">Parametry połączenia</span><span class="sxs-lookup"><span data-stu-id="3c73d-102">Connection Strings</span></span>

<span data-ttu-id="3c73d-103">Większość dostawców baz danych wymaga pewnego rodzaju parametrów połączenia w celu nawiązania połączenia z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="3c73d-103">Most database providers require some form of connection string to connect to the database.</span></span> <span data-ttu-id="3c73d-104">Czasami te parametry połączenia zawierają poufne informacje, które muszą być chronione.</span><span class="sxs-lookup"><span data-stu-id="3c73d-104">Sometimes this connection string contains sensitive information that needs to be protected.</span></span> <span data-ttu-id="3c73d-105">Może być również konieczna zmiana parametrów połączenia podczas przenoszenia aplikacji między środowiskami, takich jak programowanie, testowanie i środowisko produkcyjne.</span><span class="sxs-lookup"><span data-stu-id="3c73d-105">You may also need to change the connection string as you move your application between environments, such as development, testing, and production.</span></span>

## <a name="winforms--wpf-applications"></a><span data-ttu-id="3c73d-106">WinForms & aplikacje WPF</span><span class="sxs-lookup"><span data-stu-id="3c73d-106">WinForms & WPF Applications</span></span>

<span data-ttu-id="3c73d-107">Aplikacje WinForms, WPF i ASP.NET 4 mają wypróbowany i przetestowany wzorzec parametrów połączenia.</span><span class="sxs-lookup"><span data-stu-id="3c73d-107">WinForms, WPF, and ASP.NET 4 applications have a tried and tested connection string pattern.</span></span> <span data-ttu-id="3c73d-108">Parametry połączenia należy dodać do pliku App.config aplikacji (Web.config, jeśli używasz ASP.NET).</span><span class="sxs-lookup"><span data-stu-id="3c73d-108">The connection string should be added to your application's App.config file (Web.config if you are using ASP.NET).</span></span> <span data-ttu-id="3c73d-109">Jeśli parametry połączenia zawierają informacje poufne, takie jak nazwa użytkownika i hasło, można chronić zawartość pliku konfiguracji za pomocą [Narzędzia do zarządzania kluczami tajnymi](/aspnet/core/security/app-secrets#secret-manager).</span><span class="sxs-lookup"><span data-stu-id="3c73d-109">If your connection string contains sensitive information, such as username and password, you can protect the contents of the configuration file using the [Secret Manager tool](/aspnet/core/security/app-secrets#secret-manager).</span></span>

``` xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>

  <connectionStrings>
    <add name="BloggingDatabase"
         connectionString="Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" />
  </connectionStrings>
</configuration>
```

> [!TIP]  
> <span data-ttu-id="3c73d-110">To `providerName` ustawienie nie jest wymagane dla EF Core parametrów połączenia przechowywanych w App.config, ponieważ dostawca bazy danych jest konfigurowany za pośrednictwem kodu.</span><span class="sxs-lookup"><span data-stu-id="3c73d-110">The `providerName` setting is not required on EF Core connection strings stored in App.config because the database provider is configured via code.</span></span>

<span data-ttu-id="3c73d-111">Następnie można odczytać parametry połączenia przy użyciu `ConfigurationManager` interfejsu API w `OnConfiguring` metodzie kontekstu.</span><span class="sxs-lookup"><span data-stu-id="3c73d-111">You can then read the connection string using the `ConfigurationManager` API in your context's `OnConfiguring` method.</span></span> <span data-ttu-id="3c73d-112">Aby można było korzystać z tego interfejsu API, może być konieczne dodanie odwołania do `System.Configuration` zestawu struktury.</span><span class="sxs-lookup"><span data-stu-id="3c73d-112">You may need to add a reference to the `System.Configuration` framework assembly to be able to use this API.</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
      optionsBuilder.UseSqlServer(ConfigurationManager.ConnectionStrings["BloggingDatabase"].ConnectionString);
    }
}
```

## <a name="universal-windows-platform-uwp"></a><span data-ttu-id="3c73d-113">Platforma uniwersalna systemu Windows (UWP)</span><span class="sxs-lookup"><span data-stu-id="3c73d-113">Universal Windows Platform (UWP)</span></span>

<span data-ttu-id="3c73d-114">Parametry połączenia w aplikacji platformy UWP są zazwyczaj połączeniem z systemem SQLite, które tylko określa lokalną nazwę pliku.</span><span class="sxs-lookup"><span data-stu-id="3c73d-114">Connection strings in a UWP application are typically a SQLite connection that just specifies a local filename.</span></span> <span data-ttu-id="3c73d-115">Zazwyczaj nie zawierają one informacji poufnych i nie trzeba ich zmieniać w miarę wdrażania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3c73d-115">They typically do not contain sensitive information, and do not need to be changed as an application is deployed.</span></span> <span data-ttu-id="3c73d-116">W związku z tym te parametry połączenia są zwykle bardziej dokładne w kodzie, jak pokazano poniżej.</span><span class="sxs-lookup"><span data-stu-id="3c73d-116">As such, these connection strings are usually fine to be left in code, as shown below.</span></span> <span data-ttu-id="3c73d-117">Jeśli chcesz przenieść je poza kod, platformy UWP obsługuje koncepcje ustawień, zobacz [sekcję Ustawienia aplikacji w dokumentacji platformy UWP](/windows/uwp/app-settings/store-and-retrieve-app-data) , aby uzyskać szczegółowe informacje.</span><span class="sxs-lookup"><span data-stu-id="3c73d-117">If you wish to move them out of code then UWP supports the concept of settings, see the [App Settings section of the UWP documentation](/windows/uwp/app-settings/store-and-retrieve-app-data) for details.</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
            optionsBuilder.UseSqlite("Data Source=blogging.db");
    }
}
```

## <a name="aspnet-core"></a><span data-ttu-id="3c73d-118">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3c73d-118">ASP.NET Core</span></span>

<span data-ttu-id="3c73d-119">W ASP.NET Core system konfiguracji jest bardzo elastyczny, a parametry połączenia mogą być przechowywane w `appsettings.json` , zmiennej środowiskowej, magazynie kluczy tajnych użytkownika lub w innym źródle konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="3c73d-119">In ASP.NET Core the configuration system is very flexible, and the connection string could be stored in `appsettings.json`, an environment variable, the user secret store, or another configuration source.</span></span> <span data-ttu-id="3c73d-120">Aby uzyskać więcej informacji, zobacz [sekcję Konfiguracja w dokumentacji ASP.NET Core](/aspnet/core/fundamentals/configuration) .</span><span class="sxs-lookup"><span data-stu-id="3c73d-120">See the [Configuration section of the ASP.NET Core documentation](/aspnet/core/fundamentals/configuration) for more details.</span></span>

<span data-ttu-id="3c73d-121">Na przykład można użyć [Narzędzia tajnego Menedżera](/aspnet/core/security/app-secrets#secret-manager) do przechowywania hasła bazy danych, a następnie w przypadku tworzenia szkieletów Użyj parametrów połączenia, które składają się z `Name=<database-alias>` .</span><span class="sxs-lookup"><span data-stu-id="3c73d-121">For instance, you can use the [Secret Manager tool](/aspnet/core/security/app-secrets#secret-manager) to store your database password and then, in scaffolding, use a connection string that simply consists of `Name=<database-alias>`.</span></span>

```dotnetcli
dotnet user-secrets set ConnectionStrings.YourDatabaseAlias "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=YourDatabase"
dotnet ef dbcontext scaffold Name=ConnectionStrings.YourDatabaseAlias Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="3c73d-122">Lub Poniższy przykład pokazuje parametry połączenia przechowywane w `appsettings.json` .</span><span class="sxs-lookup"><span data-stu-id="3c73d-122">Or the following example shows the connection string stored in `appsettings.json`.</span></span>

``` json
{
  "ConnectionStrings": {
    "BloggingDatabase": "Server=(localdb)\\mssqllocaldb;Database=EFGetStarted.ConsoleApp.NewDb;Trusted_Connection=True;"
  },
}
```

<span data-ttu-id="3c73d-123">Następnie kontekst jest zazwyczaj skonfigurowany `Startup.cs` przy użyciu parametrów połączenia, które są odczytywane z konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="3c73d-123">Then the context is typically configured in `Startup.cs` with the connection string being read from configuration.</span></span> <span data-ttu-id="3c73d-124">Zwróć uwagę na to, że `GetConnectionString()` Metoda poszukuje wartości konfiguracji, której kluczem jest `ConnectionStrings:<connection string name>` .</span><span class="sxs-lookup"><span data-stu-id="3c73d-124">Note the `GetConnectionString()` method looks for a configuration value whose key is `ConnectionStrings:<connection string name>`.</span></span> <span data-ttu-id="3c73d-125">Aby użyć tej metody rozszerzenia, należy zaimportować przestrzeń nazw [Microsoft.Extensions.Configwersja](/dotnet/api/microsoft.extensions.configuration) .</span><span class="sxs-lookup"><span data-stu-id="3c73d-125">You need to import the [Microsoft.Extensions.Configuration](/dotnet/api/microsoft.extensions.configuration) namespace to use this extension method.</span></span>

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("BloggingDatabase")));
}
```
