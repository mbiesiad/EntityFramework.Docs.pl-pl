---
title: Dokumentacja narzędzi EF Core Tools (konsola Menedżera pakietów) — EF Core
author: bricelam
ms.author: bricelam
ms.date: 09/18/2018
uid: core/miscellaneous/cli/powershell
ms.openlocfilehash: 0676475d46a8d21dee7bd10e25dd273a11e96ac3
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82538407"
---
# <a name="entity-framework-core-tools-reference---package-manager-console-in-visual-studio"></a>Dokumentacja narzędzi Entity Framework Core Tools — konsola Menedżera pakietów w programie Visual Studio

Narzędzia konsoli Menedżera pakietów (PMC) dla Entity Framework Core wykonują zadania deweloperskie czasu projektowania. Na przykład tworzą [migracje](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0), stosują migracje i generują kod dla modelu na podstawie istniejącej bazy danych. Polecenia są uruchamiane w programie Visual Studio przy użyciu [konsoli Menedżera pakietów](/nuget/tools/package-manager-console). Te narzędzia współpracują z projektami .NET Framework i .NET Core.

Jeśli nie korzystasz z programu Visual Studio, zamiast tego zalecamy używanie [EF Core narzędzi wiersza polecenia](dotnet.md) . Narzędzia interfejsu wiersza polecenia są na wielu platformach i uruchamiane w wierszu poleceń.

## <a name="installing-the-tools"></a>Instalowanie narzędzi

Procedury instalowania i aktualizowania narzędzi różnią się między wersjami ASP.NET Core 2.1 + i starszymi lub innymi typami projektów.

### <a name="aspnet-core-version-21-and-later"></a>ASP.NET Core wersja 2,1 lub nowsza

Narzędzia są automatycznie dołączane do ASP.NET Core 2.1 i projektu, ponieważ `Microsoft.EntityFrameworkCore.Tools` pakiet jest zawarty w [pakiecie Microsoft. AspNetCore. app](/aspnet/core/fundamentals/metapackage-app).

W związku z tym nie trzeba wykonywać żadnych czynności w celu zainstalowania narzędzi, ale trzeba:

* Przywróć pakiety przed użyciem narzędzi w nowym projekcie.
* Zainstaluj pakiet, aby zaktualizować narzędzia do nowszej wersji.

Aby upewnić się, że korzystasz z najnowszej wersji narzędzi, zalecamy również wykonanie następujących czynności:

* Edytuj plik *. csproj* i Dodaj wiersz określający najnowszą wersję pakietu [Microsoft. EntityFrameworkCore. Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/) . Na przykład plik *. csproj* może zawierać następujący komunikat `ItemGroup` :

  ```xml
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="2.1.3" />
    <PackageReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Design" Version="2.1.1" />
  </ItemGroup>
  ```

Zaktualizuj narzędzia, gdy zostanie wyświetlony komunikat podobny do następującego:

> Narzędzia EF Core w wersji "2.1.1-RTM-30846" są starsze niż środowisko uruchomieniowe "2.1.3-RTM-32065". Zaktualizuj narzędzia dla najnowszych funkcji i poprawek błędów.

Aby zaktualizować narzędzia:

* Zainstaluj najnowszą zestaw .NET Core SDK.
* Zaktualizuj program Visual Studio do najnowszej wersji.
* Edytuj plik *. csproj* , tak aby zawierał odwołanie do pakietu dla najnowszych narzędzi, jak pokazano wcześniej.

### <a name="other-versions-and-project-types"></a>Inne wersje i typy projektów

Zainstaluj narzędzia konsoli Menedżera pakietów, uruchamiając następujące polecenie w **konsoli Menedżera pakietów**:

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

Zaktualizuj narzędzia, uruchamiając następujące polecenie w **konsoli Menedżera pakietów**.

``` powershell
Update-Package Microsoft.EntityFrameworkCore.Tools
```

### <a name="verify-the-installation"></a>Weryfikacja instalacji

Sprawdź, czy narzędzia są zainstalowane, uruchamiając następujące polecenie:

``` powershell
Get-Help about_EntityFrameworkCore
```

Dane wyjściowe wyglądają następująco (nie informuje o jakiej wersji narzędzi, z których korzystasz):

```console

                     _/\__
               ---==/    \\
         ___  ___   |.    \|\
        | __|| __|  |  )   \\\
        | _| | _|   \_/ |  //|\\
        |___||_|       /   \\\/\\

TOPIC
    about_EntityFrameworkCore

SHORT DESCRIPTION
    Provides information about the Entity Framework Core Package Manager Console Tools.

<A list of available commands follows, omitted here.>
```

## <a name="using-the-tools"></a>Korzystanie z narzędzi

Przed rozpoczęciem korzystania z narzędzi:

* Zapoznaj się z różnicą między projektem docelowym i początkowym.
* Dowiedz się, jak używać narzędzi z bibliotekami klas .NET Standard.
* W przypadku projektów ASP.NET Core Ustaw środowisko.

### <a name="target-and-startup-project"></a>Projekt docelowy i startowy

Polecenia odnoszą się do *projektu* i *projektu startowego*.

* *Projekt* jest również znany jako *projekt docelowy* , ponieważ jest to miejsce, w którym polecenia dodają lub usuwają pliki. Domyślnie **domyślnym projektem** wybranym w **konsoli Menedżera pakietów** jest projekt docelowy. Możesz określić inny projekt jako projekt docelowy przy użyciu <nobr>`--project`</nobr> opcji.

* *Projekt startowy* jest tym, że narzędzia kompilują i uruchamiają. Narzędzia muszą wykonywać kod aplikacji w czasie projektowania, aby uzyskać informacje o projekcie, takie jak parametry połączenia bazy danych i Konfiguracja modelu. Domyślnie **projekt startowy** w **Eksplorator rozwiązań** jest projektem startowym. Możesz określić inny projekt jako projekt startowy przy użyciu <nobr>`--startup-project`</nobr> opcji.

Projekt startowy i projekt docelowy są często tymi samymi projektami. Typowy scenariusz, w którym są oddzielnymi projektami, to:

* EF Core kontekstu i klasy jednostek znajdują się w bibliotece klas .NET Core.
* Aplikacja konsolowa platformy .NET Core lub aplikacja internetowa odwołuje się do biblioteki klas.

Możliwe jest również [umieszczenie kodu migracji w bibliotece klas odrębnie od kontekstu EF Core](xref:core/managing-schemas/migrations/projects).

### <a name="other-target-frameworks"></a>Inne platformy docelowe

Narzędzia konsoli Menedżera pakietów współpracują z projektami .NET Core lub .NET Framework. Aplikacje, które mają model EF Core w bibliotece klas .NET Standard mogą nie mieć projektu .NET Core lub .NET Framework. Na przykład jest to prawdziwe w aplikacjach Xamarin i platforma uniwersalna systemu Windows. W takich przypadkach można utworzyć projekt aplikacji konsolowej .NET Core lub .NET Framework, którego jedynym celem jest działanie jako projekt startowy dla narzędzi. Projekt może być fikcyjnym projektem bez rzeczywistego kodu &mdash; , który jest wymagany tylko do udostępnienia obiektu docelowego dla narzędzi.

Dlaczego jest wymagany projekt fikcyjny? Jak wspomniano wcześniej, narzędzia muszą wykonać kod aplikacji w czasie projektowania. W tym celu należy użyć środowiska uruchomieniowego .NET Core lub .NET Framework. Gdy model EF Core znajduje się w projekcie, który jest przeznaczony dla programu .NET Core lub .NET Framework, narzędzia EF Core zażyczą sobie środowisko uruchomieniowe z projektu. Nie można tego zrobić, jeśli model EF Core znajduje się w .NET Standardej bibliotece klas. .NET Standard nie jest rzeczywistą implementacją platformy .NET; jest to specyfikacja zestawu interfejsów API, które muszą być obsługiwane przez implementacje platformy .NET. W związku z tym .NET Standard nie są wystarczające dla narzędzi EF Core do wykonywania kodu aplikacji. Projekt fikcyjny tworzony do użycia jako projekt startowy zapewnia konkretną platformę docelową, do której narzędzia mogą ładować .NET Standard biblioteki klas.

### <a name="aspnet-core-environment"></a>Środowisko ASP.NET Core

Aby określić środowisko dla projektów ASP.NET Core, ustaw **env: ASPNETCORE_ENVIRONMENT** przed uruchomionymi poleceniami.

## <a name="common-parameters"></a>Parametry wspólne

W poniższej tabeli przedstawiono parametry wspólne dla wszystkich poleceń EF Core:

| Parametr                 | Opis                                                                                                                                                                                                          |
|:--------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| -> \<ciągu kontekstu        | Klasa `DbContext` , która ma zostać użyta. Nazwa klasy lub w pełni kwalifikowana z przestrzeniami nazw.  Jeśli ten parametr zostanie pominięty, EF Core odnajdzie klasę kontekstową. Jeśli istnieje wiele klas kontekstu, ten parametr jest wymagany. |
| -> \<ciągu projektu        | Projekt docelowy. Jeśli ten parametr zostanie pominięty, **domyślny projekt** **konsoli Menedżera pakietów** jest używany jako projekt docelowy.                                                                             |
| <nobr>-StartupProject</nobr> \<ciąg> | Projekt startowy. Jeśli ten parametr zostanie pominięty, **projekt startowy** we **właściwościach rozwiązania** jest używany jako projekt docelowy.                                                                                 |
| -Verbose                  | Pokaż pełne dane wyjściowe.                                                                                                                                                                                                 |

Aby wyświetlić informacje pomocy dotyczące polecenia, należy użyć `Get-Help` polecenia programu PowerShell.

> [!TIP]
> Parametry Context, Project i StartupProject obsługują rozszerzanie kart.

## <a name="add-migration"></a>Dodawanie-migracja

Dodaje nową migrację.

Parametry:

| Parametr                         | Opis                                                                                                             |
|:----------------------------------|:------------------------------------------------------------------------------------------------------------------------|
| <nobr>-Nazwa \<ciąg><nobr>       | Nazwa migracji. Jest to parametr pozycyjny i jest wymagany.                                              |
| <nobr>-OutputDir \<ciąg></nobr> | Katalog używany do wyprowadzania plików. Ścieżki są względne dla docelowego katalogu projektu. Wartość domyślna to "migracje". |
| <nobr>->\<ciągu przestrzeni nazw</nobr> | Przestrzeń nazw, która ma być używana dla wygenerowanych klas. Wartość domyślna jest generowana z katalogu wyjściowego. |

## <a name="drop-database"></a>Usuń bazę danych

Odrzuca bazę danych.

Parametry:

| Parametr | Opis                                              |
|:----------|:---------------------------------------------------------|
| -WhatIf   | Pokazuje, która baza danych zostanie porzucona, ale nie Porzuć jej. |

## <a name="get-dbcontext"></a>Kontekst Get-

Pobiera informacje o `DbContext` typie.

## <a name="remove-migration"></a>Usuń migrację

Usuwa ostatnią migrację (przywraca zmiany kodu, które zostały wykonane podczas migracji).

Parametry:

| Parametr | Opis                                                                     |
|:----------|:--------------------------------------------------------------------------------|
| -Force    | Przywróć migrację (Wycofaj zmiany, które zostały zastosowane do bazy danych). |

## <a name="scaffold-dbcontext"></a>Szkielet — DbContext

Generuje kod dla typów `DbContext` jednostek i dla bazy danych. `Scaffold-DbContext` Aby można było wygenerować typ jednostki, tabela bazy danych musi mieć klucz podstawowy.

Parametry:

| Parametr                          | Opis                                                                                                                                                                                                                                                             |
|:-----------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>-Parametry \<połączenia></nobr> | Parametry połączenia z bazą danych. W przypadku projektów ASP.NET Core 2. x wartością może być *nazwa =\<nazwa>parametrów połączenia *. W takim przypadku nazwa pochodzi ze źródeł konfiguracji skonfigurowanych dla projektu. Jest to parametr pozycyjny i jest wymagany. |
| <nobr>->\<ciągu dostawcy</nobr>   | Dostawca do użycia. Zazwyczaj jest to nazwa pakietu NuGet, na przykład: `Microsoft.EntityFrameworkCore.SqlServer`. Jest to parametr pozycyjny i jest wymagany.                                                                                           |
| -OutputDir \<ciąg>               | Katalog, w którym mają zostać umieszczone pliki. Ścieżki są względne dla katalogu projektu.                                                                                                                                                                                             |
| -ContextDir \<ciąg>              | Katalog, w którym ma `DbContext` zostać umieszczony plik. Ścieżki są względne dla katalogu projektu.                                                                                                                                                               |
| -> \<ciągu przestrzeni nazw               | Przestrzeń nazw, która ma być używana dla wszystkich wygenerowanych klas. Wartość domyślna jest generowana z głównej przestrzeni nazw i katalogu wyjściowego.                                                                                                                                                                                             |
| -Atrybut contextnamespace \<ciąg>        | Przestrzeń nazw, która ma być używana `DbContext` dla wygenerowanej klasy. Uwaga: zastąpienia `-Namespace`.                                                                                                                                                                              |
| -> \<ciągu kontekstu                 | Nazwa `DbContext` klasy do wygenerowania.                                                                                                                                                                                                                          |
| -Schemat \<ciąg [] >               | Schematy tabel, dla których mają zostać wygenerowane typy jednostek. Jeśli ten parametr zostanie pominięty, zostaną uwzględnione wszystkie schematy.                                                                                                                                                             |
| — Ciąg \<tabel [] >                | Tabele, dla których mają zostać wygenerowane typy jednostek. Jeśli ten parametr zostanie pominięty, zostaną uwzględnione wszystkie tabele.                                                                                                                                                                         |
| — Adnotacje                   | Użyj atrybutów, aby skonfigurować model (tam, gdzie to możliwe). Jeśli ten parametr zostanie pominięty, zostanie użyty tylko interfejs API Fluent.                                                                                                                                                      |
| -UseDatabaseNames                  | Nazwy tabel i kolumn należy używać dokładnie tak, jak pojawiają się one w bazie danych. Jeśli ten parametr zostanie pominięty, nazwy baz danych są zmieniane na bardziej ściśle zgodne z konwencjami stylu nazwy języka C#.                                                                                       |
| -Force                             | Zastąp istniejące pliki.                                                                                                                                                                                                                                               |

Przykład:

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

Przykład, który szkieletuje tylko wybrane tabele i tworzy kontekst w osobnym folderze z określoną nazwą i przestrzenią nazw:

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Tables "Blog","Post" -ContextDir Context -Context BlogContext -ContextNamespace New.Namespace
```

## <a name="script-migration"></a>Skrypt — migracja

Generuje skrypt SQL, który stosuje wszystkie zmiany z jednej wybranej migracji do innej wybranej migracji.

Parametry:

| Parametr                | Opis                                                                                                                                                                                                                |
|:-------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| *-Z* \<> ciągu        | Rozpoczynanie migracji. Migracje mogą być identyfikowane według nazwy lub identyfikatora. Liczba 0 to specjalny przypadek, który oznacza *przed pierwszą migracją*. Wartość domyślna to 0.                                                              |
| *-Do* \<> ciągu          | Kończenie migracji. Wartość domyślna to Ostatnia migracja.                                                                                                                                                                      |
| <nobr>-Idempotentne</nobr> | Generuj skrypt, którego można użyć w bazie danych w dowolnej migracji.                                                                                                                                                         |
| -Ciąg \<wyjściowy>        | Plik, w którym ma zostać zapisany wynik. Jeśli ten parametr zostanie pominięty, plik zostanie utworzony przy użyciu wygenerowanej nazwy w tym samym folderze, w którym są tworzone pliki środowiska uruchomieniowego aplikacji, na przykład: */obj/Debug/netcoreapp2.1/ghbkztfz.SQL/*. |

> [!TIP]
> Parametry do, od i Output obsługują rozszerzanie tabulacji.

Poniższy przykład tworzy skrypt migracji InitialCreate przy użyciu nazwy migracji.

```powershell
Script-Migration -To InitialCreate
```

Poniższy przykład tworzy skrypt dla wszystkich migracji po migracji InitialCreate przy użyciu identyfikatora migracji.

```powershell
Script-Migration -From 20180904195021_InitialCreate
```

## <a name="update-database"></a>Update-Database

Aktualizuje bazę danych do ostatniej migracji lub do określonej migracji.

| Parametr                           | Opis                                                                                                                                                                                                                                                     |
|:------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>*-Ciąg migracji* \<></nobr> | Migracja docelowa. Migracje mogą być identyfikowane według nazwy lub identyfikatora. Liczba 0 jest szczególnym przypadkiem *przed pierwszą migracją* i powoduje przywrócenie wszystkich migracji. Jeśli migracja nie zostanie określona, polecenie domyślnie przestanie być ostatnią migracją. |
| <nobr>-Parametry \<połączenia></nobr>  | Parametry połączenia z bazą danych. Wartość domyślna to określona w `AddDbContext` lub. `OnConfiguring` |

> [!TIP]
> Parametr migracji obsługuje rozszerzanie tabulacji.

Poniższy przykład przywraca wszystkie migracje.

```powershell
Update-Database -Migration 0
```

Poniższe przykłady umożliwiają zaktualizowanie bazy danych do określonej migracji. Pierwsza z nich używa nazwy migracji, a drugi używa identyfikatora migracji i określonego połączenia:

```powershell
Update-Database -Migration InitialCreate
Update-Database -Migration 20180904195021_InitialCreate -Connection your_connection_string
```

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Migracje](xref:core/managing-schemas/migrations/index)
* [Odwrócenie inżynierii](xref:core/managing-schemas/scaffolding)
