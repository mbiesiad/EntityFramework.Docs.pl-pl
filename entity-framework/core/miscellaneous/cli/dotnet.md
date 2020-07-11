---
title: Dokumentacja narzędzi EF Core Tools (interfejs wiersza polecenia platformy .NET) — EF Core
author: bricelam
ms.author: bricelam
ms.date: 07/11/2019
uid: core/miscellaneous/cli/dotnet
ms.openlocfilehash: 8ff2f3481c7f0c255def3272ca53370faba33e95
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238180"
---
# <a name="entity-framework-core-tools-reference---net-core-cli"></a>Dokumentacja narzędzi Entity Framework Core Tools — interfejs wiersza polecenia platformy .NET Core

Narzędzia interfejsu wiersza polecenia (CLI) dla Entity Framework Core wykonują zadania deweloperskie czasu projektowania. Na przykład tworzą [migracje](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0), stosują migracje i generują kod dla modelu na podstawie istniejącej bazy danych. Polecenia są rozszerzeniem dla międzyplatformowego polecenia [dotnet](/dotnet/core/tools) , które jest częścią [zestaw .NET Core SDK](https://www.microsoft.com/net/core). Te narzędzia współpracują z projektami .NET Core.

Jeśli używasz programu Visual Studio, zalecamy użycie [narzędzi konsoli Menedżera pakietów](powershell.md) :

* Automatycznie pracują z bieżącym projektem wybranym w **konsoli Menedżera pakietów** bez konieczności ręcznego przełączania katalogów.
* Automatycznie otwierają pliki wygenerowane przez polecenie po zakończeniu polecenia.

## <a name="installing-the-tools"></a>Instalowanie narzędzi

Procedura instalacji zależy od typu projektu i wersji:

* EF Core 3. x
* ASP.NET Core wersja 2,1 lub nowsza
* EF Core 2. x
* EF Core 1. x

### <a name="ef-core-3x"></a>EF Core 3. x

* `dotnet ef`musi być zainstalowany jako narzędzie globalne lub lokalne. Większość deweloperów zostanie zainstalowana `dotnet ef` jako narzędzie globalne przy użyciu następującego polecenia:

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

  Można również użyć programu `dotnet ef` jako lokalnego narzędzia. Aby użyć go jako narzędzia lokalnego, Przywróć zależności projektu, które deklaruje jako zależność narzędzia przy użyciu [pliku manifestu narzędzia](https://github.com/dotnet/cli/issues/10288).

* Zainstaluj [zestaw .NET Core SDK](https://www.microsoft.com/net/download/core).

* Zainstaluj najnowszy `Microsoft.EntityFrameworkCore.Design` pakiet.

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.Design
  ```

### <a name="aspnet-core-21"></a>ASP.NET Core 2.1 +

* Zainstaluj bieżącą [zestaw .NET Core SDK](https://www.microsoft.com/net/download/core). Zestaw SDK należy zainstalować nawet w przypadku najnowszej wersji programu Visual Studio 2017.

  Jest to wszystkie niezbędne dla ASP.NET Core 2.1 +, ponieważ `Microsoft.EntityFrameworkCore.Design` pakiet jest zawarty w [pakiecie Microsoft. AspNetCore. app](/aspnet/core/fundamentals/metapackage-app).

### <a name="ef-core-2x-not-aspnet-core"></a>EF Core 2. x (nie ASP.NET Core)

`dotnet ef`Polecenia są zawarte w zestaw .NET Core SDK, ale w celu włączenia poleceń koniecznych do zainstalowania `Microsoft.EntityFrameworkCore.Design` pakietu.

* Zainstaluj bieżącą [zestaw .NET Core SDK](https://www.microsoft.com/net/download/core). Zestaw SDK należy zainstalować nawet w przypadku najnowszej wersji programu Visual Studio.

* Zainstaluj najnowszy stabilny `Microsoft.EntityFrameworkCore.Design` pakiet.

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.Design
  ```

### <a name="ef-core-1x"></a>EF Core 1. x

* Zainstaluj zestaw .NET Core SDK w wersji 2.1.200. Nowsze wersje nie są zgodne z narzędziami interfejsu wiersza polecenia dla EF Core 1,0 i 1,1.

* Skonfiguruj aplikację tak, aby korzystała z wersji zestawu SDK 2.1.200, modyfikując jej [global.jsw](/dotnet/core/tools/global-json) pliku. Ten plik jest zwykle zawarty w katalogu rozwiązania (jeden powyżej projektu).

* Edytuj plik projektu i Dodaj `Microsoft.EntityFrameworkCore.Tools.DotNet` jako `DotNetCliToolReference` element. Określ wersję najnowszej wersji 1. x, na przykład: 1.1.6. Zobacz przykład pliku projektu na końcu tej sekcji.

* Zainstaluj najnowszą wersję 1. x `Microsoft.EntityFrameworkCore.Design` pakietu, na przykład:

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 1.1.6
  ```

  Po dodaniu obu odwołań do pakietu plik projektu wygląda następująco:

  ``` xml
  <Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
      <OutputType>Exe</OutputType>
      <TargetFramework>netcoreapp1.1</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
      <PackageReference Include="Microsoft.EntityFrameworkCore.Design"
                        Version="1.1.6"
                         PrivateAssets="All" />
    </ItemGroup>
    <ItemGroup>
       <DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet"
                              Version="1.1.6" />
    </ItemGroup>
  </Project>
  ```

  Odwołanie do pakietu z `PrivateAssets="All"` nieujawnione dla projektów, które odwołują się do tego projektu. To ograniczenie jest szczególnie przydatne w przypadku pakietów, które są zwykle używane tylko podczas projektowania.

### <a name="verify-installation"></a>Weryfikuj instalację

Uruchom następujące polecenia, aby upewnić się, że narzędzia interfejsu wiersza polecenia EF Core są prawidłowo zainstalowane:

  ```dotnetcli
  dotnet restore
  dotnet ef
  ```

Dane wyjściowe polecenia określają wersję narzędzi w użyciu:

```console

                     _/\__
               ---==/    \\
         ___  ___   |.    \|\
        | __|| __|  |  )   \\\
        | _| | _|   \_/ |  //|\\
        |___||_|       /   \\\/\\

Entity Framework Core .NET Command-line Tools 2.1.3-rtm-32065

<Usage documentation follows, not shown.>
```

## <a name="updating-the-tools"></a>Aktualizowanie narzędzi

Użyj `dotnet tool update --global dotnet-ef` , aby zaktualizować globalne narzędzia do najnowszej dostępnej wersji, jeśli masz narzędzia zainstalowane lokalnie w używanym projekcie `dotnet tool update dotnet-ef` . Zainstaluj określoną wersję, dołączając `--version <VERSION>` do polecenia. Aby uzyskać więcej informacji, zobacz sekcję [Aktualizacja](/dotnet/core/tools/dotnet-tool-update) w dokumentacji narzędzia dotnet.

## <a name="using-the-tools"></a>Korzystanie z narzędzi

Przed rozpoczęciem korzystania z narzędzi, może być konieczne utworzenie projektu startowego lub ustawienie środowiska.

### <a name="target-project-and-startup-project"></a>Projekt docelowy i projekt startowy

Polecenia odnoszą się do *projektu* i *projektu startowego*.

* *Projekt* jest również znany jako *projekt docelowy* , ponieważ jest to miejsce, w którym polecenia dodają lub usuwają pliki. Domyślnie projekt w bieżącym katalogu jest projektem docelowym. Możesz określić inny projekt jako projekt docelowy przy użyciu <nobr>`--project`</nobr> opcji.

* *Projekt startowy* jest tym, że narzędzia kompilują i uruchamiają. Narzędzia muszą wykonywać kod aplikacji w czasie projektowania, aby uzyskać informacje o projekcie, takie jak parametry połączenia bazy danych i Konfiguracja modelu. Domyślnie projekt w bieżącym katalogu jest projektem startowym. Możesz określić inny projekt jako projekt startowy przy użyciu <nobr>`--startup-project`</nobr> opcji.

Projekt startowy i projekt docelowy są często tymi samymi projektami. Typowy scenariusz, w którym są oddzielnymi projektami, to:

* EF Core kontekstu i klasy jednostek znajdują się w bibliotece klas .NET Core.
* Aplikacja konsolowa platformy .NET Core lub aplikacja internetowa odwołuje się do biblioteki klas.

Możliwe jest również [umieszczenie kodu migracji w bibliotece klas odrębnie od kontekstu EF Core](xref:core/managing-schemas/migrations/projects).

### <a name="other-target-frameworks"></a>Inne platformy docelowe

Narzędzia interfejsu wiersza polecenia współpracują z projektami .NET Core i projektami .NET Framework. Aplikacje, które mają model EF Core w bibliotece klas .NET Standard mogą nie mieć projektu .NET Core lub .NET Framework. Na przykład jest to prawdziwe w aplikacjach Xamarin i platforma uniwersalna systemu Windows. W takich przypadkach można utworzyć projekt aplikacji konsolowej .NET Core, którego jedynym celem jest działanie jako projekt startowy dla narzędzi. Projekt może być fikcyjnym projektem bez rzeczywistego kodu, który &mdash; jest wymagany tylko do udostępnienia obiektu docelowego dla narzędzi.

Dlaczego jest wymagany projekt fikcyjny? Jak wspomniano wcześniej, narzędzia muszą wykonać kod aplikacji w czasie projektowania. W tym celu należy użyć środowiska uruchomieniowego platformy .NET Core. Gdy model EF Core znajduje się w projekcie, który jest przeznaczony dla programu .NET Core lub .NET Framework, narzędzia EF Core zażyczą sobie środowisko uruchomieniowe z projektu. Nie można tego zrobić, jeśli model EF Core znajduje się w .NET Standardej bibliotece klas. .NET Standard nie jest rzeczywistą implementacją platformy .NET; jest to specyfikacja zestawu interfejsów API, które muszą być obsługiwane przez implementacje platformy .NET. W związku z tym .NET Standard nie są wystarczające dla narzędzi EF Core do wykonywania kodu aplikacji. Projekt fikcyjny tworzony do użycia jako projekt startowy zapewnia konkretną platformę docelową, do której narzędzia mogą ładować .NET Standard biblioteki klas.

### <a name="aspnet-core-environment"></a>Środowisko ASP.NET Core

Aby określić środowisko dla projektów ASP.NET Core, należy ustawić zmienną środowiskową **ASPNETCORE_ENVIRONMENT** przed uruchomieniem poleceń.

## <a name="common-options"></a>Typowe opcje

| Opcja                                         | Wybierak             | Opis                                                                                                                                                                                                                                                   |
|:-----------------------------------------------|:------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `--json`                                       |                   | Pokaż dane wyjściowe JSON.                                                                                                                                                                                                                                             |
| `--context <DBCONTEXT>`                        | <nobr>`-c`</nobr> | `DbContext`Klasa, która ma zostać użyta. Nazwa klasy lub w pełni kwalifikowana z przestrzeniami nazw.  Jeśli ta opcja zostanie pominięta, EF Core odnajdzie klasę kontekstową. Jeśli istnieje wiele klas kontekstu, ta opcja jest wymagana.                                            |
| `--project <PROJECT>`                          | `-p`              | Ścieżka względna do folderu projektu projektu docelowego.  Wartość domyślna to bieżący folder.                                                                                                                                                              |
| `--startup-project <PROJECT>`                  | `-s`              | Ścieżka względna do folderu projektu projektu startowego. Wartość domyślna to bieżący folder.                                                                                                                                                              |
| `--framework <FRAMEWORK>`                      |                   | [Moniker platformy docelowej](/dotnet/standard/frameworks#supported-target-framework-versions) dla [platformy docelowej](/dotnet/standard/frameworks).  Użyj, gdy plik projektu określa wiele platform docelowych i chcesz wybrać jedną z nich. |
| <nobr>`--configuration <CONFIGURATION>`</nobr> |                   | Konfiguracja kompilacji, na przykład: `Debug` lub `Release` .                                                                                                                                                                                                   |
| `--runtime <IDENTIFIER>`                       |                   | Identyfikator docelowego środowiska uruchomieniowego, dla którego mają zostać przywrócone pakiety. Aby uzyskać listę identyfikatorów środowiska uruchomieniowego (RID), zobacz [wykaz identyfikatorów RID](/dotnet/core/rid-catalog).                                                                                                      |
| `--help`                                       | `-h`              | Pokaż informacje pomocy.                                                                                                                                                                                                                                        |
| `--verbose`                                    | `-v`              | Pokaż pełne dane wyjściowe.                                                                                                                                                                                                                                          |
| `--no-color`                                   |                   | Nie Koloruj danych wyjściowych.                                                                                                                                                                                                                                        |
| `--prefix-output`                              |                   | Przedrostka danych wyjściowych z poziomem.                                                                                                                                                                                                                                     |

## <a name="dotnet-ef-database-drop"></a>porzucanie bazy danych programu dotnet EF

Odrzuca bazę danych.

Opcje:

| Opcja                   | Wybierak             | Opis                                              |
|:-------------------------|:------------------|:---------------------------------------------------------|
| `--force`                | <nobr>`-f`</nobr> | Nie potwierdzaj.                                           |
| <nobr>`--dry-run`</nobr> |                   | Pokazuje, która baza danych zostanie porzucona, ale nie Porzuć jej. |

## <a name="dotnet-ef-database-update"></a>Aktualizacja bazy danych dotnet EF

Aktualizuje bazę danych do ostatniej migracji lub do określonej migracji.

Argumentu

| Argument                   | Opis                                                                                                                                                                                                                                                     |
|:---------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>`<MIGRATION>`</nobr> | Migracja docelowa. Migracje mogą być identyfikowane według nazwy lub identyfikatora. Liczba 0 jest szczególnym przypadkiem *przed pierwszą migracją* i powoduje przywrócenie wszystkich migracji. Jeśli migracja nie zostanie określona, polecenie domyślnie przestanie być ostatnią migracją. |

Opcje:

| Opcja                                    | Opis                                                                                                                                        |
|:------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------|
|  <nobr>`--connection <CONNECTION>`</nobr> | Parametry połączenia z bazą danych. Wartość domyślna to określona w `AddDbContext` lub `OnConfiguring` . (Dostępne w EF Core 5.0.0 lub nowszym). |

Poniższe przykłady umożliwiają zaktualizowanie bazy danych do określonej migracji. Pierwsza z nich używa nazwy migracji, a drugi używa identyfikatora migracji i określonego połączenia:

```dotnetcli
dotnet ef database update InitialCreate
dotnet ef database update 20180904195021_InitialCreate --connection your_connection_string
```

## <a name="dotnet-ef-dbcontext-info"></a>informacje dotyczące kontekstu dotnet EF

Pobiera informacje o `DbContext` typie.

## <a name="dotnet-ef-dbcontext-list"></a>Lista kontekstowa dotnet EF

Wyświetla listę dostępnych `DbContext` typów.

## <a name="dotnet-ef-dbcontext-scaffold"></a>Szkielet EF DbContext

Generuje kod dla `DbContext` typów jednostek i dla bazy danych. Aby to polecenie generowało typ jednostki, tabela bazy danych musi mieć klucz podstawowy.

Argumentu

| Argument                    | Opis                                                                                                                                                                                                             |
|:----------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>`<CONNECTION>`</nobr> | Parametry połączenia z bazą danych. W przypadku projektów ASP.NET Core 2. x wartością może być *nazwa = \<name of connection string> *. W takim przypadku nazwa pochodzi ze źródeł konfiguracji skonfigurowanych dla projektu. |
| `<PROVIDER>`                | Dostawca do użycia. Zazwyczaj jest to nazwa pakietu NuGet, na przykład: `Microsoft.EntityFrameworkCore.SqlServer` .                                                                                           |

Opcje:

| Opcja                                   | Wybierak             | Opis                                                                                                                                                                    |
|:-----------------------------------------|:------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `--data-annotations`                     | <nobr>`-d`</nobr> | Użyj atrybutów, aby skonfigurować model (tam, gdzie to możliwe). Jeśli ta opcja zostanie pominięta, używany jest tylko interfejs API Fluent.                                                                |
| `--context <NAME>`                       | `-c`              | Nazwa `DbContext` klasy do wygenerowania.                                                                                                                                 |
| `--context-dir <PATH>`                   |                   | Katalog, w którym ma zostać umieszczony `DbContext` plik klasy. Ścieżki są względne dla katalogu projektu. Przestrzenie nazw są wyprowadzane z nazw folderów.                                 |
| `--context-namespace <NAMESPACE>`        |                   | Przestrzeń nazw, która ma być używana dla wygenerowanej `DbContext` klasy. Uwaga: zastąpienia `--namespace` . (Dostępne z EFCore 5.0.0.)                                                |
| `--force`                                | `-f`              | Zastąp istniejące pliki.                                                                                                                                                      |
| `--output-dir <PATH>`                    | `-o`              | Katalog, w którym mają zostać umieszczone pliki klas jednostek. Ścieżki są względne dla katalogu projektu.                                                                                       |
| `--namespace <NAMESPACE>`                | `-n`              | Przestrzeń nazw, która ma być używana dla wszystkich wygenerowanych klas. Wartość domyślna jest generowana z głównej przestrzeni nazw i katalogu wyjściowego. (Dostępne z EFCore 5.0.0.)                 |
| <nobr>`--schema <SCHEMA_NAME>...`</nobr> |                   | Schematy tabel, dla których mają zostać wygenerowane typy jednostek. Aby określić wiele schematów, powtórz `--schema` dla każdej z nich. W przypadku pominięcia tej opcji są uwzględniane wszystkie schematy.          |
| `--table <TABLE_NAME>`...                | `-t`              | Tabele, dla których mają zostać wygenerowane typy jednostek. Aby określić wiele tabel, powtórz `-t` lub `--table` dla każdej z nich. W przypadku pominięcia tej opcji są uwzględniane wszystkie tabele.                |
| `--use-database-names`                   |                   | Nazwy tabel i kolumn należy używać dokładnie tak, jak pojawiają się one w bazie danych. Jeśli ta opcja zostanie pominięta, nazwy baz danych są zmieniane na bardziej ściśle zgodne z konwencjami stylu nazwy języka C#. |
| `--no-onconfiguring`                     |                   | Pomija generowanie `OnConfiguring` metody w generowanej `DbContext` klasie. (Dostępne z EFCore 5.0.0.)                                                 |

Poniższy przykład szkieletuje wszystkie schematy i tabele i umieszcza nowe pliki w folderze *models* .

```dotnetcli
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -o Models
```

Poniższy przykład szkieletuje tylko wybrane tabele i tworzy kontekst w osobnym folderze z określoną nazwą i przestrzenią nazw:

```dotnetcli
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -o Models -t Blog -t Post --context-dir Context -c BlogContext --context-namespace New.Namespace
```

## <a name="dotnet-ef-migrations-add"></a>Dodawanie migracji dotnet EF

Dodaje nową migrację.

Argumentu

| Argument              | Opis                |
|:----------------------|:---------------------------|
| <nobr>`<NAME>`</nobr> | Nazwa migracji. |

Opcje:

| Opcja                                 | Wybierak             | Opis                                                                                                                             |
|:---------------------------------------|:------------------|:----------------------------------------------------------------------------------------------------------------------------------------|
| `--output-dir <PATH>`                  | <nobr>`-o`</nobr> | Katalog używany do wyprowadzania plików. Ścieżki są względne dla docelowego katalogu projektu. Wartość domyślna to "migracje".                    |
| <nobr>`--namespace <NAMESPACE>`</nobr> | `-n`              | Przestrzeń nazw, która ma być używana dla wygenerowanych klas. Wartość domyślna jest generowana z katalogu wyjściowego. (Dostępne z EFCore 5.0.0.) |

## <a name="dotnet-ef-migrations-list"></a>Lista migracji dotnet

Wyświetla listę dostępnych migracji.

## <a name="dotnet-ef-migrations-remove"></a>Usuwanie migracji dotnet

Usuwa ostatnią migrację (przywraca zmiany kodu, które zostały wykonane podczas migracji).

Opcje:

| Opcja                 | Wybierak             | Opis                                                                     |
|:-----------------------|:------------------|:--------------------------------------------------------------------------------|
| <nobr>`--force`</nobr> | <nobr>`-f`</nobr> | Przywróć migrację (Wycofaj zmiany, które zostały zastosowane do bazy danych). |

## <a name="dotnet-ef-migrations-script"></a>skrypt migracji dotnet

Generuje skrypt SQL z migracji.

Argumentu

| Argument              | Opis                                                                                                                                                   |
|:----------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>`<FROM>`</nobr> | Rozpoczynanie migracji. Migracje mogą być identyfikowane według nazwy lub identyfikatora. Liczba 0 to specjalny przypadek, który oznacza *przed pierwszą migracją*. Wartość domyślna to 0. |
| `<TO>`                | Kończenie migracji. Wartość domyślna to Ostatnia migracja.                                                                                                         |

Opcje:

| Opcja                         | Wybierak             | Opis                                                        |
|:-------------------------------|:------------------|:-------------------------------------------------------------------|
| <nobr>`--output <FILE>`</nobr> | <nobr>`-o`</nobr> | Plik, w którym ma zostać zapisany skrypt.                                   |
| `--idempotent`                 | `-i`              | Generuj skrypt, którego można użyć w bazie danych w dowolnej migracji. |

Poniższy przykład tworzy skrypt migracji InitialCreate:

```dotnetcli
dotnet ef migrations script 0 InitialCreate
```

Poniższy przykład tworzy skrypt dla wszystkich migracji po migracji InitialCreate.

```dotnetcli
dotnet ef migrations script 20180904195021_InitialCreate
```

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Migracje](xref:core/managing-schemas/migrations/index)
* [Odwrócenie inżynierii](xref:core/managing-schemas/scaffolding)
