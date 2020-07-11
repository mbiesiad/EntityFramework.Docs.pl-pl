---
title: Przegląd migracji — EF Core
author: bricelam
ms.author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/index
ms.openlocfilehash: 8539a8da6f0051d3737efc583f0adfaf05fb2d3d
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238232"
---
# <a name="migrations-overview"></a>Przegląd migracji

W realnych projektach modele danych zmieniają się wraz z zaimplementowanymi funkcjami: nowe jednostki lub właściwości są dodawane i usuwane, a schematy bazy danych muszą być odpowiednio zmieniane, aby były utrzymywane w synchronizacji z aplikacją. Funkcja migracji w EF Core zapewnia sposób stopniowego aktualizowania schematu bazy danych, aby zachować synchronizację z modelem danych aplikacji przy zachowaniu istniejących danych w bazie danych.

Na wysokim poziomie migracja działa w następujący sposób:

* Gdy zostanie wprowadzona zmiana modelu danych, programista korzysta z EF Core narzędzi, aby dodać odpowiednią migrację opisującą aktualizacje niezbędne do synchronizowania schematu bazy danych. EF Core porównuje bieżący model z migawką starego modelu w celu określenia różnic i generuje pliki źródłowe migracji; pliki mogą być śledzone w kontroli źródła projektu, podobnie jak każdy inny plik źródłowy.
* Po wygenerowaniu nowej migracji można ją zastosować do bazy danych na różne sposoby. EF Core rejestruje wszystkie zastosowane migracje w specjalnej tabeli historii, dzięki czemu może ona wiedzieć, które migracje zostały zastosowane, a które nie.

Pozostała część tej strony to przewodnik początkującego krok po kroku dotyczący korzystania z migracji. Aby uzyskać szczegółowe informacje, zapoznaj się z innymi stronami w tej sekcji.

## <a name="getting-started"></a>Wprowadzenie

Załóżmy, że po prostu zakończysz pierwszą aplikację EF Core, która zawiera następujący prosty model:

```c#
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
}
```

Podczas opracowywania możesz użyć [interfejsów API tworzenia i upuszczania](xref:core/managing-schemas/ensure-created) , aby szybko wykonywać iteracje, zmieniając model w miarę potrzeb; Jednak teraz, gdy aplikacja przechodzi do środowiska produkcyjnego, musisz bezpiecznie rozwijać schemat bez porzucania całej bazy danych.

### <a name="install-the-tools"></a>Instalowanie narzędzi

Najpierw należy zainstalować [EF Core narzędzia wiersza polecenia](xref:core/miscellaneous/cli/index):

* Ogólnie zalecamy korzystanie z [interfejs wiersza polecenia platformy .NET Core narzędzi](xref:core/miscellaneous/cli/dotnet), które działają na wszystkich platformach.
* Jeśli nie masz doświadczenia w pracy w programie Visual Studio lub masz doświadczenie w korzystaniu z migracji EF6, możesz również użyć [narzędzi konsoli Menedżera pakietów](xref:core/miscellaneous/cli/powershell).

### <a name="create-your-first-migration"></a>Tworzenie pierwszej migracji

Teraz można przystąpić do dodawania pierwszej migracji. Poinstruuj EF Core, aby utworzyć migrację o nazwie **InitialCreate**:

#### <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate
```

#### <a name="visual-studio"></a>[Program Visual Studio](#tab/vs)

``` powershell
Add-Migration InitialCreate
```

***

EF Core utworzy katalog o nazwie **migrations** w projekcie i wygeneruje niektóre pliki. Dobrym pomysłem jest zbadanie dokładnie wygenerowanego EF Core i jego zmianę, ale powrócimy teraz.

### <a name="create-your-database-and-schema"></a>Tworzenie bazy danych i schematu

W tym momencie można utworzyć bazę danych programu EF i utworzyć schemat z migracji. Można to zrobić, wykonując następujące czynności:

#### <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```
#### <a name="visual-studio"></a>[Program Visual Studio](#tab/vs)

``` powershell
Update-Database
```

***

To wszystko — aplikacja jest gotowa do uruchamiania w nowej bazie danych i nie trzeba pisać pojedynczego wiersza SQL. Należy pamiętać, że ten sposób stosowania migracji jest idealnym rozwiązaniem do lokalnego rozwoju, ale jest mniej odpowiedni dla środowisk produkcyjnych — Aby uzyskać więcej informacji, zobacz [stronę stosowanie migracji](xref:core/managing-schemas/migrations/applying) .

### <a name="evolving-your-model"></a>Rozwój modelu

Przeszedł kilka dni i zostanie wyświetlony monit o dodanie sygnatury czasowej tworzenia do blogów. Wprowadzono niezbędne zmiany w aplikacji, a model wygląda teraz następująco:

```c#
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
    public DateTime CreatedTimestamp { get; set; }
}
```

Model i produkcyjna baza danych nie są teraz zsynchronizowane — należy dodać nową kolumnę do schematu bazy danych. Utwórzmy nową migrację:

#### <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddBlogCreatedTimestamp
```

#### <a name="visual-studio"></a>[Program Visual Studio](#tab/vs)

``` powershell
Add-Migration AddBlogCreatedTimestamp
```

***

Należy pamiętać, że firma Microsoft przekazuje nazwę opisową, aby ułatwić zrozumienie historii projektu później.

Ponieważ nie jest to pierwsza migracja projektu, EF Core teraz porównuje zaktualizowany model względem migawki starego modelu, zanim kolumna zostanie dodana; migawka modelu jest jednym z plików generowanych przez EF Core podczas dodawania migracji i jest sprawdzany w kontroli źródła. Na podstawie tego porównania EF Core wykrywa, że dodano kolumnę i dodaje odpowiednią migrację.

Teraz możesz zastosować migrację tak jak wcześniej:

#### <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```
#### <a name="visual-studio"></a>[Program Visual Studio](#tab/vs)

``` powershell
Update-Database
```

***

Należy pamiętać, że ten czas EF wykrywa, czy baza danych już istnieje. Ponadto, gdy pierwsza migracja została zastosowana powyżej, ten fakt został zarejestrowany w specjalnej tabeli historii migracji w bazie danych programu; Umożliwia to programowi EF automatyczne stosowanie tylko nowej migracji.

### <a name="next-steps"></a>Następne kroki

Powyższe dane były tylko krótkim wprowadzeniem do migracji. Zapoznaj się z innymi stronami dokumentacji, aby dowiedzieć się więcej na temat [zarządzania migracjami](xref:core/managing-schemas/migrations/managing), [stosowania ich](xref:core/managing-schemas/migrations/applying)i innych aspektów. [Dokumentacja narzędzia interfejs wiersza polecenia platformy .NET Core](xref:core/miscellaneous/cli/index) zawiera również przydatne informacje dotyczące różnych poleceń
