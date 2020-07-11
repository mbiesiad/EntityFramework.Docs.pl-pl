---
title: Zarządzanie migracjami — EF Core
author: bricelam
ms.author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/managing
ms.openlocfilehash: e52d3680360a1e83e05f04650c735c5a67680094
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238763"
---
# <a name="managing-migrations"></a>Zarządzanie migracjami

W miarę zmiany modelu migracje są dodawane i usuwane w ramach normalnego opracowywania, a pliki migracji są sprawdzane w kontroli źródła projektu. Aby zarządzać migracjami, należy najpierw zainstalować [EF Core narzędzia wiersza polecenia](xref:core/miscellaneous/cli/index).

> [!TIP]
> Jeśli `DbContext` znajduje się w innym zestawie niż projekt startowy, można jawnie określić projekty docelowe i uruchomieniowe w [narzędziu Konsola Menedżera pakietów](xref:core/miscellaneous/cli/powershell#target-and-startup-project) lub [Narzędzia interfejs wiersza polecenia platformy .NET Core](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project).

## <a name="add-a-migration"></a>Dodawanie migracji

Po zmianie modelu można dodać migrację dla tej zmiany:

### <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddBlogCreatedTimestamp
```

### <a name="visual-studio"></a>[Program Visual Studio](#tab/vs)

``` powershell
Add-Migration AddBlogCreatedTimestamp
```

***

Nazwa migracji może być używana jak komunikat zatwierdzenia w systemie kontroli wersji. Na przykład można wybrać nazwę, np. *AddBlogCreatedTimestamp* , jeśli zmiana jest nową `CreatedTimestamp` właściwością w `Blog` jednostce.

Do projektu są dodawane trzy pliki w katalogu **migracji** :

* **XXXXXXXXXXXXXX_AddCreatedTimestamp. cs**— główny plik migracji. Zawiera operacje niezbędne do zastosowania migracji (w programie `Up` ) i przywrócenia jej (w programie `Down` ).
* **XXXXXXXXXXXXXX_AddCreatedTimestamp. Designer. cs**— plik metadanych migracji. Zawiera informacje używane przez EF.
* **MyContextModelSnapshot.cs**— migawka bieżącego modelu. Służy do określania, co zmieniło się podczas dodawania następnej migracji.

Sygnatura czasowa w nazwie pliku pomaga zachować ich uporządkowane chronologicznie, aby zobaczyć postęp zmian.

### <a name="namespaces"></a>Przestrzenie nazw

Możesz przenieść pliki migracji i ręcznie zmienić ich przestrzeń nazw. Nowe migracje są tworzone jako elementy równorzędne ostatniej migracji. Alternatywnie można określić przestrzeń nazw podczas generowania w następujący sposób:

### <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --namespace Your.Namespace
```

### <a name="visual-studio"></a>[Program Visual Studio](#tab/vs)

``` powershell
Add-Migration InitialCreate -Namespace Your.Namespace
```

***

## <a name="customize-migration-code"></a>Dostosowywanie kodu migracji

Mimo że EF Core zwykle tworzy dokładne migracje, należy zawsze sprawdzić kod i upewnić się, że odpowiada żądanej zmianie; w niektórych przypadkach jest to nawet konieczne.

### <a name="column-renames"></a>Nazwy kolumn

Jednym z przykładów, w których wymagane jest dostosowanie migracji, jest zmiana nazwy właściwości. Na przykład jeśli zmienisz nazwę właściwości z `Name` na `FullName` , EF Core będzie generować następującą migrację:

```c#
migrationBuilder.DropColumn(
    name: "Name",
    table: "Customers");

migrationBuilder.AddColumn<string>(
    name: "FullName",
    table: "Customers",
    nullable: true);
```

EF Core zazwyczaj nie jest w stanie wiedzieć, kiedy zamiarem jest Porzucenie kolumny i utworzenie nowej (dwóch oddzielnych zmian) oraz zmiana nazwy kolumny. Jeśli powyższa migracja zostanie zastosowana w taki sposób, wszystkie nazwy klientów zostaną utracone. Aby zmienić nazwę kolumny, Zastąp powyższą wytworzoną migrację, wykonując następujące czynności:

```c#
migrationBuilder.RenameColumn(
    name: "Name",
    table: "Customers",
    newName: "FullName");
```

> [!TIP]
> Proces tworzenia szkieletu migracji ostrzega, gdy operacja może spowodować utratę danych (np. upuszczenie kolumny). Jeśli widzisz to ostrzeżenie, pamiętaj o tym, aby sprawdzić poprawność kodu migracji.

### <a name="adding-raw-sql"></a>Dodawanie RAW SQL

Podczas zmieniania nazwy kolumny można uzyskać za pomocą wbudowanego interfejsu API, w wielu przypadkach nie jest to możliwe. Na przykład możemy chcieć zamienić istniejące `FirstName` i właściwości na `LastColumn` jedną, nową `FullName` Właściwość. Migracja wygenerowana przez EF Core będzie następująca:

``` csharp
migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");

migrationBuilder.AddColumn<string>(
    name: "Name",
    table: "Customer",
    nullable: true);
```

Tak jak wcześniej, spowoduje to niepożądane utratę danych. Aby przetransferować dane ze starych kolumn, należy zmienić układ migracji i wprowadzić nieprzetworzoną operację SQL w następujący sposób:

``` csharp
migrationBuilder.AddColumn<string>(
    name: "Name",
    table: "Customer",
    nullable: true);

migrationBuilder.Sql(
@"
    UPDATE Customer
    SET Name = FirstName + ' ' + LastName;
");

migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");
```

### <a name="arbitrary-changes-via-raw-sql"></a>Dowolne zmiany za pośrednictwem bazy danych SQL RAW

Do zarządzania obiektami bazy danych, do których EF Core nie ma informacji, można również użyć surowego SQL. W tym celu należy dodać migrację bez wprowadzania żadnych zmian modelu; zostanie wygenerowana pusta migracja, którą można następnie wypełnić przy użyciu nieprzetworzonych operacji SQL.

Na przykład następująca migracja tworzy SQL Server procedury składowanej:

```c#
migrationBuilder.Sql(
@"
    EXEC ('CREATE PROCEDURE getFullName
        @LastName nvarchar(50),
        @FirstName nvarchar(50)
    AS
        RETURN @LastName + @FirstName;')");
```

Może to służyć do zarządzania dowolnym aspektem bazy danych, w tym:

* Procedury składowane
* Wyszukiwanie pełnotekstowe
* Funkcje
* Wyzwalacze
* Widoki

W większości przypadków EF Core automatycznie zawija każdą migrację we własnej transakcji podczas stosowania migracji. Niestety, nie można wykonać niektórych operacji migracji w ramach transakcji w niektórych bazach danych; w takich przypadkach możesz zrezygnować z transakcji, przekazując `suppressTransaction: true` do `migrationBuilder.Sql` .

Jeśli `DbContext` znajduje się w innym zestawie niż projekt startowy, można jawnie określić projekty docelowe i uruchomieniowe w [narzędziu Konsola Menedżera pakietów](xref:core/miscellaneous/cli/powershell#target-and-startup-project) lub [Narzędzia interfejs wiersza polecenia platformy .NET Core](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project).

## <a name="remove-a-migration"></a>Usuń migrację

Czasami należy dodać migrację i zdawać sobie sprawę, że należy wprowadzić dodatkowe zmiany w modelu EF Core przed ich zastosowaniem. Aby usunąć ostatnią migrację, użyj tego polecenia.

### <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations remove
```

### <a name="visual-studio"></a>[Program Visual Studio](#tab/vs)

``` powershell
Remove-Migration
```

***

Po usunięciu migracji możesz wprowadzić dodatkowe zmiany modelu i dodać je ponownie.

> [!WARNING]
> Pamiętaj, aby nie usuwać żadnych migracji, które zostały już zastosowane do produkcyjnych baz danych. Nie pozwoli to na przywrócenie tego stanu i może spowodować przerwanie założeń dokonanych przez kolejne migracje.

## <a name="listing-migrations"></a>Wyświetlanie listy migracji

Można wyświetlić listę wszystkich istniejących migracji w następujący sposób:

```dotnetcli
dotnet ef migrations list
```

## <a name="resetting-all-migrations"></a>Resetowanie wszystkich migracji

W niektórych sytuacjach może być konieczne usunięcie wszystkich migracji i rozpoczęcie od nowa. Można to łatwo zrobić, usuwając folder **migracji** i upuszczając swoją bazę danych. w tym momencie można utworzyć nową migrację początkową, która będzie zawierać cały bieżący schemat.

Istnieje również możliwość zresetowania wszystkich migracji i utworzenia jednego z nich bez utraty danych. Jest to czasami nazywane "zgniatanie" i obejmuje kilka czynności ręcznych:

* Usuwanie folderu **migracji**
* Utwórz nową migrację i Wygeneruj skrypt SQL dla tego programu
* W bazie danych Usuń wszystkie wiersze z tabeli historii migracji
* Wstaw pojedynczy wiersz do historii migracji, aby zarejestrować, że pierwsza migracja została już zastosowana, ponieważ tabele już istnieją. Insert SEQL to Ostatnia operacja w skrypcie SQL wygenerowany powyżej.
