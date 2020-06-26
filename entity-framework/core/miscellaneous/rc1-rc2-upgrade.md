---
title: Uaktualnianie z wersji EF Core 1,0 RC1 do RC2 — EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 6d75b229-cc79-4d08-88cd-3a1c1b24d88f
uid: core/miscellaneous/rc1-rc2-upgrade
ms.openlocfilehash: 5500addc98a0c0eb314c4d9f9401fa301ce5c6f6
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370529"
---
# <a name="upgrading-from-ef-core-10-rc1-to-10-rc2"></a>Uaktualnianie z wersji EF Core 1,0 RC1 do 1,0 RC2

Ten artykuł zawiera wskazówki dotyczące przesuwania aplikacji skompilowanej z pakietami RC1 do RC2.

## <a name="package-names-and-versions"></a>Nazwy i wersje pakietów

Między RC1 a RC2 zmieniono z "Entity Framework 7" na "Entity Framework Core". Więcej informacji na temat przyczyn zmiany w [tym wpisie można znaleźć Scott Hanselman](https://www.hanselman.com/blog/ASPNET5IsDeadIntroducingASPNETCore10AndNETCore10.aspx). Ze względu na tę zmianę nazwy pakietów uległy zmianie w stosunku `EntityFramework.*` do `Microsoft.EntityFrameworkCore.*` i naszych wersji z systemu `7.0.0-rc1-final` do `1.0.0-rc2-final` (lub `1.0.0-preview1-final` narzędzi).

**Konieczne będzie całkowite usunięcie pakietów RC1, a następnie zainstalowanie programu RC2.** Oto mapowanie niektórych typowych pakietów.

| Pakiet RC1                                               | Równoważność RC2                                                       |
|:----------------------------------------------------------|:---------------------------------------------------------------------|
| EntityFramework. MicrosoftSqlServer 7.0.0-RC1 — wersja finalna | Microsoft. EntityFrameworkCore. SqlServer 1.0.0-RC2 — wersja końcowa      |
| EntityFramework. SQLite 7.0.0-RC1 — wersja finalna | Microsoft. EntityFrameworkCore. sqlite 1.0.0-RC2 — wersja końcowa      |
| EntityFramework7. Npgsql 3.1.0-RC1-3     | NpgSql. EntityFrameworkCore. Postgres<to be advised>      |
| EntityFramework. SqlServerCompact35 7.0.0-RC1 — wersja finalna | EntityFrameworkCore. SqlServerCompact35 1.0.0-RC2 — wersja finalna      |
| EntityFramework. SqlServerCompact40 7.0.0-RC1 — wersja finalna | EntityFrameworkCore. SqlServerCompact40 1.0.0-RC2 — wersja finalna      |
| EntityFramework. inMemory 7.0.0-RC1 — wersja końcowa | Microsoft. EntityFrameworkCore. inMemory 1.0.0-RC2 — wersja końcowa      |
| EntityFramework. IBMDataServer 7.0.0-beta1     | Jeszcze niedostępne dla wersji RC2                                            |
| EntityFramework. Commands 7.0.0-RC1 — Final | Microsoft. EntityFrameworkCore. Tools 1.0.0-zestawu — wersja finalna |
| EntityFramework. MicrosoftSqlServer. Design 7.0.0-RC1 — wersja końcowa | Microsoft. EntityFrameworkCore. SqlServer. Design 1.0.0-RC2 — wersja końcowa      |

## <a name="namespaces"></a>Namespaces

Wraz z nazwami pakietów przestrzenie nazw zmieniają `Microsoft.Data.Entity.*` się z na `Microsoft.EntityFrameworkCore.*` . Tę zmianę można obsłużyć za pomocą Znajdź/Zamień `using Microsoft.Data.Entity` z `using Microsoft.EntityFrameworkCore` .

## <a name="table-naming-convention-changes"></a>Zmiany konwencji nazewnictwa tabel

Istotna zmiana funkcjonalna w wersji RC2 była używana `DbSet<TEntity>` jako nazwa właściwości danej jednostki, która jest mapowana na nazwę tabeli, a nie tylko nazwę klasy. Więcej informacji na temat tej zmiany można znaleźć w [powiązanym problemie związanym z ogłoszeniem](https://github.com/aspnet/Announcements/issues/167).

W przypadku istniejących aplikacji w wersji RC1 zalecamy dodanie następującego kodu do początku `OnModelCreating` metody, aby zachować strategię nazewnictwa RC1:

``` csharp
foreach (var entity in modelBuilder.Model.GetEntityTypes())
{
    entity.Relational().TableName = entity.DisplayName();
}
```

Jeśli chcesz przyjąć nową strategię nazewnictwa, zalecamy pomyślne zakończenie pozostałej części kroków uaktualnienia, a następnie usunięcie kodu i utworzenie migracji w celu zastosowania zmian nazwy tabeli.

## <a name="adddbcontext--startupcs-changes-aspnet-core-projects-only"></a>AddDbContext/Startup.cs zmiany (tylko projekty ASP.NET Core)

W wersji RC1 należy dodać usługi Entity Framework do dostawcy usługi aplikacji — w programie `Startup.ConfigureServices(...)` :

``` csharp
services.AddEntityFramework()
  .AddSqlServer()
  .AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"]));
```

W wersji RC2 można usunąć wywołania do `AddEntityFramework()` , `AddSqlServer()` itp.:

``` csharp
services.AddDbContext<ApplicationDbContext>(options =>
  options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"]));
```

Należy również dodać konstruktora do kontekstu pochodnego, który przyjmuje opcje kontekstu i przekazuje je do konstruktora podstawowego. Jest to potrzebne, ponieważ usunęliśmy część Scary Magic, która snuck je w tle:

``` csharp
public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
    : base(options)
{
}
```

## <a name="passing-in-an-iserviceprovider"></a>Przekazywanie w IServiceProvider

Jeśli masz kod w wersji RC1, który przekazuje `IServiceProvider` do kontekstu, ten element jest teraz przenoszony do `DbContextOptions` , a nie jako osobny parametr konstruktora. Użyj `DbContextOptionsBuilder.UseInternalServiceProvider(...)` , aby ustawić dostawcę usług.

### <a name="testing"></a>Testowanie

Najbardziej typowym scenariuszem tego jest kontrolowanie zakresu bazy danych inMemory podczas testowania. Zapoznaj się z zaktualizowanym artykułem dotyczącym [testowania](testing/index.md) , aby zapoznać się z przykładem w wersji RC2.

### <a name="resolving-internal-services-from-application-service-provider-aspnet-core-projects-only"></a>Rozpoznawanie wewnętrznych usług z poziomu dostawcy usługi aplikacji (tylko projekty ASP.NET Core)

Jeśli masz aplikację ASP.NET Core i chcesz, aby program Dr mógł rozpoznać usługi wewnętrzne od dostawcy usług aplikacji, istnieje Przeciążenie `AddDbContext` , które umożliwia skonfigurowanie:

``` csharp
services.AddEntityFrameworkSqlServer()
  .AddDbContext<ApplicationDbContext>((serviceProvider, options) =>
    options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"])
           .UseInternalServiceProvider(serviceProvider));
```

> [!WARNING]  
> Zalecamy umożliwienie wewnętrznie zarządzania własnymi usługami przez program EF, chyba że masz powód, aby połączyć wewnętrzne usługi EF z dostawcą usług aplikacji. Głównym powodem może być użycie dostawcy usług aplikacji w celu zastąpienia usług używanych wewnętrznie przez program Dr

## <a name="dnx-commands--net-core-cli-aspnet-core-projects-only"></a>Polecenia środowiska DNX => interfejs wiersza polecenia platformy .NET Core (tylko projekty ASP.NET Core)

Jeśli wcześniej były używane `dnx ef` polecenia dla projektów programu ASP.NET 5, zostały one przeniesione do `dotnet ef` poleceń. Nadal stosuje się tę samą składnię polecenia. Można użyć `dotnet ef --help` do informacji o składni.

Sposób rejestrowania poleceń został zmieniony w wersji RC2, ponieważ środowiska DNX jest zastępowany przez interfejs wiersza polecenia platformy .NET Core. Polecenia są teraz zarejestrowane w `tools` sekcji w `project.json` :

``` json
"tools": {
  "Microsoft.EntityFrameworkCore.Tools": {
    "version": "1.0.0-preview1-final",
    "imports": [
      "portable-net45+win8+dnxcore50",
      "portable-net45+win8"
    ]
  }
}
```

> [!TIP]  
> W przypadku korzystania z programu Visual Studio można teraz używać konsoli Menedżera pakietów do uruchamiania poleceń EF dla projektów ASP.NET Core (nie jest to obsługiwane w wersji RC1). W tym celu należy zarejestrować polecenia w `tools` sekcji `project.json` .

## <a name="package-manager-commands-require-powershell-5"></a>Polecenia Menedżera pakietów wymagają programu PowerShell 5

Jeśli używasz poleceń Entity Framework w konsoli Menedżera pakietów w programie Visual Studio, musisz upewnić się, że jest zainstalowany program PowerShell 5. Jest to tymczasowy wymóg, który zostanie usunięty w następnej wersji (zobacz [problem #5327](https://github.com/aspnet/EntityFramework/issues/5327) , aby uzyskać więcej informacji).

## <a name="using-imports-in-projectjson"></a>Używanie "Imports" w project.jsna

Niektóre zależności EF Core nie obsługują jeszcze .NET Standard. EF Core w projektach .NET Standard i .NET Core mogą wymagać dodania elementu "Imports", aby project.jsjako tymczasowe obejście.

Po dodaniu EF, w wyniku przywracania NuGet zostanie wyświetlony następujący komunikat o błędzie:

``` Console
Package Ix-Async 1.2.5 is not compatible with netcoreapp1.0 (.NETCoreApp,Version=v1.0). Package Ix-Async 1.2.5 supports:
  - net40 (.NETFramework,Version=v4.0)
  - net45 (.NETFramework,Version=v4.5)
  - portable-net45+win8+wp8 (.NETPortable,Version=v0.0,Profile=Profile78)
Package Remotion.Linq 2.0.2 is not compatible with netcoreapp1.0 (.NETCoreApp,Version=v1.0). Package Remotion.Linq 2.0.2 supports:
  - net35 (.NETFramework,Version=v3.5)
  - net40 (.NETFramework,Version=v4.0)
  - net45 (.NETFramework,Version=v4.5)
  - portable-net45+win8+wp8+wpa81 (.NETPortable,Version=v0.0,Profile=Profile259)
```

Obejście polega na ręcznym zaimportowaniu przenośnego profilu "Portable-net451 + Win8". Wymusza to, aby pakiet NuGet traktował te dane binarne, które pasują do tej funkcji jako zgodnej platformy z .NET Standard, nawet jeśli nie są. Chociaż polecenie "Portable-net451 + Win8" nie jest zgodne 100%, jest zgodne .NET Standard ze zbyt dużą ilością dla przejścia z PCL do .NET Standard. Importy mogą zostać usunięte, gdy zależności EF ostatecznie uaktualnią do .NET Standard.

Do "Imports" można dodać wiele struktur w składni tablicy. Inne Importy mogą być konieczne w przypadku dodania do projektu dodatkowych bibliotek.

``` json
{
  "frameworks": {
    "netcoreapp1.0": {
      "imports": ["dnxcore50", "portable-net451+win8"]
    }
  }
}
```

Zobacz [#5176 problemu](https://github.com/aspnet/EntityFramework/issues/5176).
