---
title: Migracje — EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/05/2018
uid: core/managing-schemas/migrations/index
ms.openlocfilehash: 99bb420d95cb86443b63ba05ce9e6b4ab838eff9
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82538455"
---
# <a name="migrations"></a>Migracje

Model danych zmienia się podczas opracowywania i nie jest synchronizowany z bazą danych. Możesz porzucić bazę danych i pozwolić, aby EF utworzyły nową, zgodną z modelem, ale ta procedura powoduje utratę danych. Funkcja migracji w EF Core zapewnia sposób stopniowego aktualizowania schematu bazy danych, aby zachować synchronizację z modelem danych aplikacji przy zachowaniu istniejących danych w bazie danych.

Migracje obejmują narzędzia wiersza polecenia i interfejsy API, które ułatwiają wykonywanie następujących zadań:

* [Tworzenie migracji](#create-a-migration). Generuj kod, który może aktualizować bazę danych w celu zsynchronizowania jej z zestawem zmian modelu.
* [Zaktualizuj bazę danych](#update-the-database). Zastosuj oczekujące migracje, aby zaktualizować schemat bazy danych.
* [Dostosuj kod migracji](#customize-migration-code). Czasami wygenerowany kod musi być modyfikowany lub uzupełniany.
* [Usuń migrację](#remove-a-migration). Usuń wygenerowany kod.
* [Przywrócenie migracji](#revert-a-migration). Cofnij zmiany w bazie danych.
* [Generuj skrypty SQL](#generate-sql-scripts). Może być potrzebny skrypt do zaktualizowania produkcyjnej bazy danych lub rozwiązywania problemów z kodem migracji.
* [Zastosuj migracje w czasie wykonywania](#apply-migrations-at-runtime). W przypadku aktualizacji i uruchamiania skryptów w czasie projektowania nie są najlepszym rozwiązaniem, należy `Migrate()` wywołać metodę.

> [!TIP]
> Jeśli `DbContext` znajduje się w innym zestawie niż projekt startowy, można jawnie określić projekty docelowe i uruchomieniowe w [narzędziu Konsola Menedżera pakietów](xref:core/miscellaneous/cli/powershell#target-and-startup-project) lub [Narzędzia interfejs wiersza polecenia platformy .NET Core](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project).

## <a name="install-the-tools"></a>Instalowanie narzędzi

Zainstaluj [narzędzia wiersza polecenia](xref:core/miscellaneous/cli/index):

* W przypadku programu Visual Studio zalecamy korzystanie z [narzędzi konsoli Menedżera pakietów](xref:core/miscellaneous/cli/powershell).
* W przypadku innych środowisk programistycznych wybierz [narzędzia interfejs wiersza polecenia platformy .NET Core](xref:core/miscellaneous/cli/dotnet).

## <a name="create-a-migration"></a>Tworzenie migracji

Po [zdefiniowaniu modelu początkowego](xref:core/modeling/index)należy utworzyć bazę danych. Aby dodać początkową migrację, uruchom następujące polecenie.

### <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Add-Migration InitialCreate
```

***

Do projektu są dodawane trzy pliki w katalogu **migracji** :

* **XXXXXXXXXXXXXX_InitialCreate. cs**— główny plik migracji. Zawiera operacje niezbędne do zastosowania migracji (w programie `Up()`) i przywrócenia jej (w programie `Down()`).
* **XXXXXXXXXXXXXX_InitialCreate. Designer. cs**— plik metadanych migracji. Zawiera informacje używane przez EF.
* **MyContextModelSnapshot.cs**— migawka bieżącego modelu. Służy do określania, co zmieniło się podczas dodawania następnej migracji.

Sygnatura czasowa w nazwie pliku pomaga zachować ich uporządkowane chronologicznie, aby zobaczyć postęp zmian.

> [!TIP]
> Możesz przenieść pliki migracji i ręcznie zmienić ich przestrzeń nazw. Nowe migracje są tworzone jako elementy równorzędne ostatniej migracji.
> 
> Alternatywnie możesz użyć `-Namespace` (konsola Menedżera pakietów) lub `--namespace` (interfejs wiersza polecenia platformy .NET Core), aby określić przestrzeń nazw podczas generowania.
> ### <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/dotnet-core-cli)
> 
> ```dotnetcli
> dotnet ef migrations add InitialCreate --namespace Your.Namespace
> ```
> 
> ### <a name="visual-studio"></a>[Visual Studio](#tab/vs)
> 
> ``` powershell
> Add-Migration InitialCreate -Namespace Your.Namespace
> ```

## <a name="update-the-database"></a>Aktualizowanie bazy danych

Następnie Zastosuj migrację do bazy danych, aby utworzyć schemat.

### <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Update-Database
```

***

## <a name="customize-migration-code"></a>Dostosowywanie kodu migracji

Po wprowadzeniu zmian w modelu EF Core schemat bazy danych może nie być zsynchronizowany. Aby zapewnić aktualność, Dodaj kolejną migrację. Nazwa migracji może być używana jak komunikat zatwierdzenia w systemie kontroli wersji. Na przykład można wybrać nazwę, np. *AddProductReviews* , jeśli zmiana jest nową klasą jednostek do przeglądu.

### <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddProductReviews
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Add-Migration AddProductReviews
```

***

Po utworzeniu szkieletowej migracji (kod wygenerowany dla niego), Przejrzyj kod pod kątem dokładności i Dodaj, Usuń lub zmodyfikuj wszystkie operacje wymagane do poprawnego zastosowania.

Na przykład migracja może zawierać następujące operacje:

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

Chociaż te operacje sprawiają, że schemat bazy danych jest zgodny, nie zachowuje istniejących nazw klientów. Aby go ulepszyć, napisz ponownie w następujący sposób.

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

> [!TIP]
> Proces tworzenia szkieletu migracji ostrzega, gdy operacja może spowodować utratę danych (np. upuszczenie kolumny). Jeśli widzisz to ostrzeżenie, pamiętaj o tym, aby sprawdzić poprawność kodu migracji.

Zastosuj migrację do bazy danych przy użyciu odpowiedniego polecenia.

### <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Update-Database
```

***

### <a name="empty-migrations"></a>Puste migracje

Czasami warto dodać migrację bez wprowadzania żadnych zmian modelu. W takim przypadku dodanie nowej migracji powoduje utworzenie plików kodu z pustymi klasami. Można dostosować tę migrację do wykonywania operacji, które nie są bezpośrednio powiązane z modelem EF Core. W ten sposób możesz chcieć zarządzać tymi elementami:

* Wyszukiwanie pełnotekstowe
* Funkcje
* Procedury składowane
* Wyzwalacze
* Widoki

## <a name="remove-a-migration"></a>Usuń migrację

Czasami należy dodać migrację i zdawać sobie sprawę, że należy wprowadzić dodatkowe zmiany w modelu EF Core przed ich zastosowaniem. Aby usunąć ostatnią migrację, użyj tego polecenia.

### <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations remove
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Remove-Migration
```

***

Po usunięciu migracji możesz wprowadzić dodatkowe zmiany modelu i dodać je ponownie.

## <a name="revert-a-migration"></a>Przywracanie migracji

Jeśli migracja (lub kilka migracji) została już zastosowana do bazy danych, ale konieczne jest jej przywrócenie, można użyć tego samego polecenia do zastosowania migracji, ale określić nazwę migracji, do której chcesz przeprowadzić przywracanie.

### <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update LastGoodMigration
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Update-Database LastGoodMigration
```

***

## <a name="generate-sql-scripts"></a>Generuj skrypty SQL

Podczas debugowania migracji lub wdrażania ich w produkcyjnej bazie danych warto wygenerować skrypt SQL. Skrypt może następnie być ponownie przeglądany pod kątem dokładności i dostrojony, aby dopasować potrzeby produkcyjnej bazy danych. Skrypt może być również używany w połączeniu z technologią wdrażania. Podstawowe polecenie jest następujące.

### <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/dotnet-core-cli)

#### <a name="basic-usage"></a>Podstawowe użycie
```dotnetcli
dotnet ef migrations script
```

#### <a name="with-from-to-implied"></a>Z opcją od (do implikowanego)
Spowoduje to wygenerowanie skryptu SQL z tej migracji do najnowszej migracji.
```dotnetcli
dotnet ef migrations script 20190725054716_Add_new_tables
```

#### <a name="with-from-and-to"></a>Z i do
Spowoduje to wygenerowanie skryptu SQL z `from` migracji do określonej `to` migracji.
```dotnetcli
dotnet ef migrations script 20190725054716_Add_new_tables 20190829031257_Add_audit_table
```
Możesz użyć `from` , która jest nowsza niż `to` w celu wygenerowania skryptu wycofywania. *Weź pod uwagę potencjalne scenariusze utraty danych.*

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

#### <a name="basic-usage"></a>Podstawowe użycie
``` powershell
Script-Migration
```

#### <a name="with-from-to-implied"></a>Z opcją od (do implikowanego)
Spowoduje to wygenerowanie skryptu SQL z tej migracji do najnowszej migracji.
```powershell
Script-Migration 20190725054716_Add_new_tables
```

#### <a name="with-from-and-to"></a>Z i do
Spowoduje to wygenerowanie skryptu SQL z `from` migracji do określonej `to` migracji.
```powershell
Script-Migration 20190725054716_Add_new_tables 20190829031257_Add_audit_table
```
Możesz użyć `from` , która jest nowsza niż `to` w celu wygenerowania skryptu wycofywania. *Weź pod uwagę potencjalne scenariusze utraty danych.*

***

Istnieje kilka opcji tego polecenia.

Migracja **z** migracji powinna być ostatnią zastosowana do bazy danych przed uruchomieniem skryptu. Jeśli nie zastosowano żadnych migracji, określ `0` (jest to ustawienie domyślne).

Migracja **do** migracji to Ostatnia migracja, która zostanie zastosowana do bazy danych po uruchomieniu skryptu. Ta wartość domyślna to Ostatnia migracja w projekcie.

Opcjonalnie można wygenerować skrypt **idempotentne** . Ten skrypt stosuje tylko migracje, jeśli nie zostały one jeszcze zastosowane do bazy danych. Jest to przydatne, jeśli nie masz dokładnej znajomości ostatniej migracji zastosowanej do bazy danych lub Jeśli wdrażasz ją w wielu bazach danych, które mogą znajdować się w innej migracji.

## <a name="apply-migrations-at-runtime"></a>Zastosuj migracje w czasie wykonywania

Niektóre aplikacje mogą chcieć zastosować migracje w czasie wykonywania podczas uruchamiania lub pierwszego uruchomienia. Zrób to przy użyciu `Migrate()` metody.

Ta metoda jest oparta na `IMigrator` usłudze, która może być używana w bardziej zaawansowanych scenariuszach. Użyj `myDbContext.GetInfrastructure().GetService<IMigrator>()` , aby uzyskać do niej dostęp.

``` csharp
myDbContext.Database.Migrate();
```

> [!WARNING]
>
> * Takie podejście nie jest przeznaczone dla wszystkich użytkowników. Chociaż jest to doskonałe rozwiązanie w przypadku aplikacji z lokalną bazą danych, większość aplikacji będzie wymagała bardziej niezawodnej strategii wdrażania, takiej jak generowanie skryptów SQL.
> * Nie wywołuj `EnsureCreated()` przed `Migrate()`. `EnsureCreated()`pomija migracje, aby utworzyć schemat, co powoduje `Migrate()` niepowodzenie.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz <xref:core/miscellaneous/cli/index>.
