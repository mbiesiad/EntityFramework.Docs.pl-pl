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
# <a name="connection-strings"></a>Parametry połączenia

Większość dostawców baz danych wymaga pewnego rodzaju parametrów połączenia w celu nawiązania połączenia z bazą danych. Czasami te parametry połączenia zawierają poufne informacje, które muszą być chronione. Może być również konieczna zmiana parametrów połączenia podczas przenoszenia aplikacji między środowiskami, takich jak programowanie, testowanie i środowisko produkcyjne.

## <a name="winforms--wpf-applications"></a>WinForms & aplikacje WPF

Aplikacje WinForms, WPF i ASP.NET 4 mają wypróbowany i przetestowany wzorzec parametrów połączenia. Parametry połączenia należy dodać do pliku App.config aplikacji (Web.config, jeśli używasz ASP.NET). Jeśli parametry połączenia zawierają informacje poufne, takie jak nazwa użytkownika i hasło, można chronić zawartość pliku konfiguracji za pomocą [Narzędzia do zarządzania kluczami tajnymi](/aspnet/core/security/app-secrets#secret-manager).

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
> To `providerName` ustawienie nie jest wymagane dla EF Core parametrów połączenia przechowywanych w App.config, ponieważ dostawca bazy danych jest konfigurowany za pośrednictwem kodu.

Następnie można odczytać parametry połączenia przy użyciu `ConfigurationManager` interfejsu API w `OnConfiguring` metodzie kontekstu. Aby można było korzystać z tego interfejsu API, może być konieczne dodanie odwołania do `System.Configuration` zestawu struktury.

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

## <a name="universal-windows-platform-uwp"></a>Platforma uniwersalna systemu Windows (UWP)

Parametry połączenia w aplikacji platformy UWP są zazwyczaj połączeniem z systemem SQLite, które tylko określa lokalną nazwę pliku. Zazwyczaj nie zawierają one informacji poufnych i nie trzeba ich zmieniać w miarę wdrażania aplikacji. W związku z tym te parametry połączenia są zwykle bardziej dokładne w kodzie, jak pokazano poniżej. Jeśli chcesz przenieść je poza kod, platformy UWP obsługuje koncepcje ustawień, zobacz [sekcję Ustawienia aplikacji w dokumentacji platformy UWP](/windows/uwp/app-settings/store-and-retrieve-app-data) , aby uzyskać szczegółowe informacje.

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

## <a name="aspnet-core"></a>ASP.NET Core

W ASP.NET Core system konfiguracji jest bardzo elastyczny, a parametry połączenia mogą być przechowywane w `appsettings.json` , zmiennej środowiskowej, magazynie kluczy tajnych użytkownika lub w innym źródle konfiguracji. Aby uzyskać więcej informacji, zobacz [sekcję Konfiguracja w dokumentacji ASP.NET Core](/aspnet/core/fundamentals/configuration) .

Na przykład można użyć [Narzędzia tajnego Menedżera](/aspnet/core/security/app-secrets#secret-manager) do przechowywania hasła bazy danych, a następnie w przypadku tworzenia szkieletów Użyj parametrów połączenia, które składają się z `Name=<database-alias>` .

```dotnetcli
dotnet user-secrets set ConnectionStrings.YourDatabaseAlias "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=YourDatabase"
dotnet ef dbcontext scaffold Name=ConnectionStrings.YourDatabaseAlias Microsoft.EntityFrameworkCore.SqlServer
```

Lub Poniższy przykład pokazuje parametry połączenia przechowywane w `appsettings.json` .

``` json
{
  "ConnectionStrings": {
    "BloggingDatabase": "Server=(localdb)\\mssqllocaldb;Database=EFGetStarted.ConsoleApp.NewDb;Trusted_Connection=True;"
  },
}
```

Następnie kontekst jest zazwyczaj skonfigurowany `Startup.cs` przy użyciu parametrów połączenia, które są odczytywane z konfiguracji. Zwróć uwagę na to, że `GetConnectionString()` Metoda poszukuje wartości konfiguracji, której kluczem jest `ConnectionStrings:<connection string name>` . Aby użyć tej metody rozszerzenia, należy zaimportować przestrzeń nazw [Microsoft.Extensions.Configwersja](/dotnet/api/microsoft.extensions.configuration) .

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("BloggingDatabase")));
}
```
