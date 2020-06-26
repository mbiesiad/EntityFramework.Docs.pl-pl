---
title: Wprowadzenie — EF Core
author: rick-anderson
ms.date: 09/17/2019
ms.assetid: 3c88427c-20c6-42ec-a736-22d3eccd5071
uid: core/get-started/index
ms.openlocfilehash: 7181df6ee73b09f196940178ffed38d96b075258
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370321"
---
# <a name="getting-started-with-ef-core"></a>Wprowadzenie z EF Core

W tym samouczku utworzysz aplikację konsolową .NET Core, która zapewnia dostęp do danych w bazie danych programu SQLite przy użyciu Entity Framework Core.

Możesz wykonać czynności opisane w samouczku za pomocą programu Visual Studio w systemie Windows lub za pomocą interfejs wiersza polecenia platformy .NET Core w systemie Windows, macOS lub Linux.

[Zapoznaj się z przykładem tego artykułu w witrynie GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/GetStarted).

## <a name="prerequisites"></a>Wymagania wstępne

Zainstaluj następujące oprogramowanie:

### <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli)

* [Zestaw .NET Core SDK](https://www.microsoft.com/net/download/core).

### <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* [Program Visual Studio 2019 w wersji 16,3 lub nowszej](https://www.visualstudio.com/downloads/) z tym obciążeniem:
  * **Programowanie dla wielu platform w środowisku .NET Core** (w innych zestawach **narzędzi**)

---

## <a name="create-a-new-project"></a>Tworzenie nowego projektu

### <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new console -o EFGetStarted
cd EFGetStarted
```

### <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Otwórz program Visual Studio.
* Kliknij pozycję **Utwórz nowy projekt**
* Wybierz pozycję **aplikacja konsoli (.NET Core)** ze znacznikiem **C#** , a następnie kliknij przycisk **dalej** .
* Wprowadź **EFGetStarted** dla nazwy i kliknij przycisk **Utwórz** .

---

## <a name="install-entity-framework-core"></a>Zainstaluj Entity Framework Core

Aby zainstalować EF Core, należy zainstalować pakiet dla dostawców usługi EF Core Database, które mają być przeznaczone do celów. W tym samouczku jest używane oprogramowanie SQLite, ponieważ jest ono wykonywane na wszystkich platformach obsługiwanych przez platformę .NET Core. Aby uzyskać listę dostępnych dostawców, zobacz [dostawcy bazy danych](../providers/index.md).

### <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* **Narzędzia > Menedżera pakietów NuGet > konsoli Menedżera pakietów**
* Uruchom następujące polecenia:

  ``` PowerShell
  Install-Package Microsoft.EntityFrameworkCore.Sqlite
  ```

Porada: Możesz także zainstalować pakiety, klikając prawym przyciskiem myszy projekt i wybierając pozycję **Zarządzaj pakietami NuGet** .

---

## <a name="create-the-model"></a>Tworzenie modelu

Zdefiniuj klasę kontekstu i klasy jednostek, które tworzą model.

### <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli)

* W katalogu projektu Utwórz **model.cs** z następującym kodem

### <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Dodaj klasy >**
* Wprowadź **model.cs** jako nazwę, a następnie kliknij przycisk **Dodaj** .
* Zastąp zawartość pliku następującym kodem

---

[!code-csharp[Main](../../../samples/core/GetStarted/Model.cs)]

EF Core [może również odtworzyć](../managing-schemas/scaffolding.md) model z istniejącej bazy danych.

Porada: Ta aplikacja zacelowo upraszcza przejrzystość. [Parametry połączenia](../miscellaneous/connection-strings.md) nie powinny być przechowywane w kodzie dla aplikacji produkcyjnych. Można również podzielić każdą klasę języka C# na własny plik.

## <a name="create-the-database"></a>Tworzenie bazy danych

Poniższe kroki służą do tworzenia bazy [danych programu.](xref:core/managing-schemas/migrations/index)

### <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli)

* Uruchom następujące polecenia:

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

  Spowoduje to zainstalowanie programu [dotnet EF](../miscellaneous/cli/dotnet.md) i pakietu projektowego, który jest wymagany do uruchomienia polecenia w projekcie. `migrations`Polecenie tworzy szkielet migracji w celu utworzenia początkowego zestawu tabel dla modelu. `database update`Polecenie tworzy bazę danych i stosuje do niej nową migrację.

### <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Uruchom następujące polecenia w **konsoli Menedżera pakietów (PMC)**

  ``` PowerShell
  Install-Package Microsoft.EntityFrameworkCore.Tools
  Add-Migration InitialCreate
  Update-Database
  ```

  Spowoduje to zainstalowanie [narzędzi PMC dla EF Core](../miscellaneous/cli/powershell.md). `Add-Migration`Polecenie tworzy szkielet migracji w celu utworzenia początkowego zestawu tabel dla modelu. `Update-Database`Polecenie tworzy bazę danych i stosuje do niej nową migrację.

---

## <a name="create-read-update--delete"></a>Tworzenie, odczytywanie, aktualizowanie & usuwanie

* Otwórz *program.cs* i Zastąp zawartość następującym kodem:

  [!code-csharp[Main](../../../samples/core/GetStarted/Program.cs)]

## <a name="run-the-app"></a>Uruchomienie aplikacji

### <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet run
```

### <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Program Visual Studio używa niespójnego katalogu roboczego podczas uruchamiania aplikacji konsolowych platformy .NET Core. (zobacz [dotnet/Project-System # 3619](https://github.com/dotnet/project-system/issues/3619)) Powoduje to zgłaszanie wyjątku: *nie ma takiej tabeli: blogi*. Aby zaktualizować katalog roboczy:

* Kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Edytuj plik projektu**
* Po prostu poniżej właściwości *TargetFramework* Dodaj następujące elementy:

  ``` XML
  <StartWorkingDirectory>$(MSBuildProjectDirectory)</StartWorkingDirectory>
  ```

* Zapisz plik.

Teraz możesz uruchomić aplikację:

* **Debugowanie > uruchamiane bez debugowania**

---

## <a name="next-steps"></a>Następne kroki

* Postępuj zgodnie z [samouczkiem ASP.NET Core](/aspnet/core/data/ef-rp/intro) , aby użyć EF Core w aplikacji sieci Web
* Dowiedz się więcej o [wyrażeniach zapytań LINQ](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)
* [Skonfiguruj model](xref:core/modeling/index) , aby określić elementy, takie jak [wymagana](xref:core/modeling/entity-properties#required-and-optional-properties) i [Maksymalna długość](xref:core/modeling/entity-properties#maximum-length)
* Użyj [migracji](xref:core/managing-schemas/migrations/index) , aby zaktualizować schemat bazy danych po zmianie modelu
