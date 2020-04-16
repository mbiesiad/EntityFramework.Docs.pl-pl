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
# <a name="getting-started-with-ef-core"></a>Wprowadzenie do EF Core

W tym samouczku utworzysz aplikację konsoli .NET Core, która wykonuje dostęp do danych w bazie danych SQLite przy użyciu programu Entity Framework Core.

Możesz wykonać samouczek przy użyciu programu Visual Studio w systemie Windows lub przy użyciu interfejsu wiersza polecenia .NET Core w systemie Windows, macOS lub Linux.

[Zobacz przykład tego artykułu na GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/GetStarted).

## <a name="prerequisites"></a>Wymagania wstępne

Zainstaluj następujące oprogramowanie:

### <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli)

* [.NET Core SDK](https://www.microsoft.com/net/download/core).

### <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* [Visual Studio 2019 w wersji 16.3 lub nowszej](https://www.visualstudio.com/downloads/) z tym obciążeniem:
  * **.NET Core rozwoju między platformami** (w obszarze **Inne zestawy narzędzi)**

---

## <a name="create-a-new-project"></a>Tworzenie nowego projektu

### <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new console -o EFGetStarted
cd EFGetStarted
```

### <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Otwórz program Visual Studio.
* Kliknij **pozycję Utwórz nowy projekt**
* Wybierz **aplikację konsoli (.NET Core)** z tagiem **C#** i kliknij przycisk **Dalej**
* Wprowadź **EFGetStarted** dla nazwy i kliknij przycisk **Utwórz**

---

## <a name="install-entity-framework-core"></a>Instalowanie rdzenia struktury encji

Aby zainstalować EF Core, należy zainstalować pakiet dla dostawców baz danych EF Core, które mają być kierowane. Ten samouczek używa SQLite, ponieważ działa na wszystkich platformach, które obsługują program .NET Core. Aby uzyskać listę dostępnych dostawców, zobacz [Dostawcy baz danych](../providers/index.md).

### <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* **Narzędzia > Konsoli menedżera pakietów NuGet > pakietów**
* Uruchom następujące polecenia:

  ``` PowerShell
  Install-Package Microsoft.EntityFrameworkCore.Sqlite
  ```

Wskazówka: Możesz również zainstalować pakiety, klikając prawym przyciskiem myszy projekt i wybierając **pozycję Zarządzaj pakietami NuGet**

---

## <a name="create-the-model"></a>Tworzenie modelu

Zdefiniuj klasy kontekstu i klasy jednostek, które tworzą model.

### <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli)

* W katalogu projektu utwórz **Model.cs** z następującym kodem

### <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Kliknij prawym przyciskiem myszy projekt i wybierz pozycję **Dodaj klasę >**
* Wprowadź **Model.cs** jako nazwę i kliknij przycisk **Dodaj**
* Zastąp zawartość pliku następującym kodem

---

[!code-csharp[Main](../../../samples/core/GetStarted/Model.cs)]

EF Core można również [odtworzyć](../managing-schemas/scaffolding.md) modelu z istniejącej bazy danych.

Wskazówka: Ta aplikacja celowo utrzymuje rzeczy proste dla jasności. [Parametry połączenia](../miscellaneous/connection-strings.md) nie powinny być przechowywane w kodzie dla aplikacji produkcyjnych. Można również podzielić każdą klasę C# na własny plik.

## <a name="create-the-database"></a>Tworzenie bazy danych

Poniższe kroki używają [migracji](xref:core/managing-schemas/migrations/index) do tworzenia bazy danych.

### <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli)

* Uruchom następujące polecenia:

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

  Spowoduje to zainstalowanie [dotnet ef](../miscellaneous/cli/dotnet.md) i pakietu projektowego, który jest wymagany do uruchomienia polecenia w projekcie. Polecenie `migrations` tworzy szkielety migracji w celu utworzenia początkowego zestawu tabel dla modelu. Polecenie `database update` tworzy bazę danych i stosuje do niej nową migrację.

### <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Uruchamianie następujących poleceń w **konsoli Menedżera pakietów**

  ``` PowerShell
  Install-Package Microsoft.EntityFrameworkCore.Tools
  Add-Migration InitialCreate
  Update-Database
  ```

  Spowoduje to zainstalowanie [narzędzi PMC dla EF Core](../miscellaneous/cli/powershell.md). Polecenie `Add-Migration` tworzy szkielety migracji w celu utworzenia początkowego zestawu tabel dla modelu. Polecenie `Update-Database` tworzy bazę danych i stosuje do niej nową migrację.

---

## <a name="create-read-update--delete"></a>Tworzenie, czytanie, aktualizowanie & usuwanie

* Otwórz *Program.cs* i zastąp zawartość następującym kodem:

  [!code-csharp[Main](../../../samples/core/GetStarted/Program.cs)]

## <a name="run-the-app"></a>Uruchomienie aplikacji

### <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet run
```

### <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Visual Studio używa niespójnego katalogu roboczego podczas uruchamiania aplikacji konsoli .NET Core. (patrz [dotnet/project-system#3619)](https://github.com/dotnet/project-system/issues/3619) Powoduje to wyjątek jest zgłaszany: *nie ma takiej tabeli: Blogi*. Aby zaktualizować katalog roboczy:

* Kliknij prawym przyciskiem myszy projekt i wybierz pozycję **Edytuj plik projektu**
* Tuż poniżej *właściwości TargetFramework* dodaj następujące elementy:

  ``` XML
  <StartWorkingDirectory>$(MSBuildProjectDirectory)</StartWorkingDirectory>
  ```

* Zapisz plik.

Teraz możesz uruchomić aplikację:

* **> debugowania start bez debugowania**

---

## <a name="next-steps"></a>Następne kroki

* Postępuj zgodnie z [samouczkiem ASP.NET Core,](/aspnet/core/data/ef-rp/intro) aby używać ef core w aplikacji sieci web
* Dowiedz się więcej o [wyrażeniach zapytań LINQ](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)
* [Konfigurowanie modelu](xref:core/modeling/index) w celu określenia [1, najaśniku i](xref:core/modeling/entity-properties#required-and-optional-properties) [maksymalnej długości](xref:core/modeling/entity-properties#maximum-length)
* Aktualizowanie schematu bazy danych po zmianie modelu za pomocą [migracji](xref:core/managing-schemas/migrations/index)
