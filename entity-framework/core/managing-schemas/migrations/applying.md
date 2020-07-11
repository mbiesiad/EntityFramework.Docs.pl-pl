---
title: Stosowanie migracji — EF Core
author: bricelam
ms.author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/applying
ms.openlocfilehash: f83a014651fdf2262a603fb91da7984672690197
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238760"
---
# <a name="applying-migrations"></a>Stosowanie migracji

Po dodaniu migracji należy je wdrożyć i zastosować do baz danych. Istnieją różne strategie, które są bardziej odpowiednie dla środowisk produkcyjnych i innych dla cyklu życia.

> [!NOTE]
> Bez względu na strategię wdrażania, należy zawsze sprawdzać wygenerowane migracje i testować je przed zastosowaniem do produkcyjnej bazy danych. Migracja może porzucić kolumnę, gdy zamiarem było zmienić jej nazwę lub może się nie powieść z różnych powodów w przypadku zastosowania do bazy danych.

## <a name="sql-scripts"></a>Skrypty SQL

Zalecanym sposobem wdrożenia migracji do produkcyjnej bazy danych jest wygenerowanie skryptów SQL. Zalety tej strategii są następujące:

* Skrypty SQL można sprawdzić pod kątem dokładności; jest to ważne, ponieważ stosowanie zmian schematu do produkcyjnych baz danych jest potencjalnie niebezpieczną operacją, która może wiązać się z utratą danych.
* W niektórych przypadkach skrypty można dostrajać w celu dopasowania do konkretnych potrzeb produkcyjnej bazy danych.
* Skrypty SQL mogą być używane w połączeniu z technologią wdrażania i mogą być nawet generowane jako część procesu CI.
* Skrypty SQL mogą być udostępniane ADMINISTRATORom i mogą być zarządzane i archiwizowane osobno.

### <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/dotnet-core-cli)

#### <a name="basic-usage"></a>Podstawowe użycie

Następujące generuje skrypt SQL z pustej bazy danych do najnowszej migracji:

```dotnetcli
dotnet ef migrations script
```

#### <a name="with-from-to-implied"></a>Z opcją od (do implikowanego)

Poniższy proces generuje skrypt SQL z danej migracji do najnowszej migracji.

```dotnetcli
dotnet ef migrations script AddNewTables
```

#### <a name="with-from-and-to"></a>Z i do

Poniższy proces generuje skrypt SQL z określonej `from` migracji do określonej `to` migracji.

```dotnetcli
dotnet ef migrations script AddNewTables AddAuditTable
```

Możesz użyć `from` , która jest nowsza niż `to` w celu wygenerowania skryptu wycofywania.

> [!WARNING]
> Weź pod uwagę potencjalne scenariusze utraty danych.

### <a name="visual-studio"></a>[Program Visual Studio](#tab/vs)

#### <a name="basic-usage"></a>Podstawowe użycie

Następujące generuje skrypt SQL z pustej bazy danych do najnowszej migracji:

``` powershell
Script-Migration
```

#### <a name="with-from-to-implied"></a>Z opcją od (do implikowanego)

Poniższy proces generuje skrypt SQL z danej migracji do najnowszej migracji.

```powershell
Script-Migration AddNewTables
```

#### <a name="with-from-and-to"></a>Z i do

Poniższy proces generuje skrypt SQL z określonej `from` migracji do określonej `to` migracji.

```powershell
Script-Migration AddNewTables AddAuditTable
```
Możesz użyć `from` , która jest nowsza niż `to` w celu wygenerowania skryptu wycofywania. *Weź pod uwagę potencjalne scenariusze utraty danych.*

***

Generowanie skryptu akceptuje następujące dwa argumenty w polu wskaż, który zakres migracji powinien zostać wygenerowany:

* Migracja **z** migracji powinna być ostatnią zastosowana do bazy danych przed uruchomieniem skryptu. Jeśli nie zastosowano żadnych migracji, określ `0` (jest to ustawienie domyślne).
* Migracja **do** migracji to Ostatnia migracja, która zostanie zastosowana do bazy danych po uruchomieniu skryptu. Ta wartość domyślna to Ostatnia migracja w projekcie.

## <a name="idempotent-sql-scripts"></a>Idempotentne skrypty SQL

Utworzone powyżej skrypty SQL mogą być stosowane tylko w celu zmiany schematu z jednej migracji na inną; odpowiedzialność za odpowiednie zastosowanie skryptu i tylko do bazy danych w poprawnym stanie migracji. EF Core obsługuje również generowanie skryptów **idempotentne** , które wewnętrznie sprawdzają, które migracje zostały już zastosowane (za pośrednictwem tabeli historii migracji) i mają zastosowanie tylko brakujące. Jest to przydatne, jeśli nie wiesz dokładnie, jak Ostatnia migracja została zastosowana do bazy danych, lub Jeśli wdrażasz w wielu bazach danych, które mogą znajdować się w innej migracji.

Następujące generowanie idempotentne migracji:

#### <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations script --idempotent
```

#### <a name="visual-studio"></a>[Program Visual Studio](#tab/vs)

``` powershell
Script-Migration -Idempotent
```

***

## <a name="command-line-tools"></a>Narzędzia wiersza polecenia

Narzędzia wiersza polecenia EF mogą służyć do zastosowania migracji do bazy danych programu. Podczas pracy w przypadku lokalnego tworzenia i testowania migracji takie podejście nie jest idealnym rozwiązaniem do zarządzania produkcyjnymi bazami danych:

* Polecenia SQL są stosowane bezpośrednio przez narzędzie, bez nadawania deweloperowi zmiany w celu ich sprawdzenia lub zmodyfikowania. Może to być niebezpieczne w środowisku produkcyjnym.
* Zestaw .NET SDK i narzędzie EF muszą być zainstalowane na serwerach produkcyjnych.

### <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/dotnet-core-cli)

Następująca aktualizacja bazy danych do najnowszej migracji:

```dotnetcli
dotnet ef database update
```

Następujące czynności umożliwiają zaktualizowanie bazy danych do danej migracji:

```dotnetcli
dotnet ef database update AddNewTables
```

Należy zauważyć, że może to służyć do przywrócenia wcześniejszej migracji.

> [!WARNING]
> Weź pod uwagę potencjalne scenariusze utraty danych.

### <a name="visual-studio"></a>[Program Visual Studio](#tab/vs)

Następująca aktualizacja bazy danych do najnowszej migracji:

``` powershell
Update-Database
```

Następujące czynności umożliwiają zaktualizowanie bazy danych do danej migracji:

``` powershell
Update-Database AddNewTables
```

Należy zauważyć, że może to służyć do przywrócenia wcześniejszej migracji.

> [!WARNING]
> Weź pod uwagę potencjalne scenariusze utraty danych.

***

Aby uzyskać więcej informacji na temat stosowania migracji za pomocą narzędzi wiersza polecenia, zobacz [informacje dotyczące narzędzi EF Core Tools](xref:core/miscellaneous/cli/index).

## <a name="apply-migrations-at-runtime"></a>Zastosuj migracje w czasie wykonywania

Istnieje możliwość programistycznego zastosowania migracji przez samą aplikację, zazwyczaj podczas uruchamiania. Podczas pracy w przypadku lokalnego tworzenia i testowania migracji takie podejście jest nieodpowiednie do zarządzania produkcyjnymi bazami danych, z następujących powodów:

* W przypadku uruchomienia wielu wystąpień aplikacji obie aplikacje mogą próbować zastosować migrację współbieżnie i niepowodzeniem (lub gorszą, spowodować uszkodzenie danych).
* Podobnie, jeśli aplikacja uzyskuje dostęp do bazy danych podczas migrowania jej przez inną aplikację, może to spowodować poważne problemy.
* Aplikacja musi mieć podwyższony poziom dostępu, aby modyfikować schemat bazy danych. Zwykle dobrym sposobem jest ograniczenie uprawnień bazy danych aplikacji w środowisku produkcyjnym.
* Ważne jest, aby móc wycofać zastosowana migracja w przypadku problemu. Inne strategie zapewniają to łatwo i z nich.
* Polecenia SQL są stosowane bezpośrednio przez program bez nadawania deweloperowi zmiany w celu ich sprawdzenia lub zmodyfikowania. Może to być niebezpieczne w środowisku produkcyjnym.

Aby programowo zastosować migracje, wywołaj polecenie `context.Database.Migrate()` . Na przykład typowa aplikacja ASP.NET może wykonać następujące czynności:

```c#
public static void Main(string[] args)
{
    var host = CreateHostBuilder(args).Build();

    using (var scope = host.Services.CreateScope())
    {
        var db = scope.ServiceProvider.GetRequiredService<ApplicationDbContext>();
        db.Database.Migrate();
    }

    host.Run();
}
```

Należy pamiętać, że `Migrate()` kompilacja jest oparta na `IMigrator` usłudze, która może być używana w bardziej zaawansowanych scenariuszach. Użyj `myDbContext.GetInfrastructure().GetService<IMigrator>()` , aby uzyskać do niej dostęp.

> [!WARNING]
>
> * Należy uważnie rozważyć przed rozpoczęciem korzystania z tej metody w środowisku produkcyjnym. Środowisko pokazuje, że prostota tej strategii wdrażania jest przeważona przez tworzone przez niego problemy. Zamiast tego Rozważ użycie skryptów SQL.
> * Nie wywołuj `EnsureCreated()` przed `Migrate()` . `EnsureCreated()`pomija migracje, aby utworzyć schemat, co powoduje `Migrate()` Niepowodzenie.
