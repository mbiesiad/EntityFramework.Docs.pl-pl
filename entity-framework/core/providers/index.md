---
title: Dostawcy baz danych — EF Core
author: ajcvickers
ms.date: 12/17/2019
uid: core/providers/index
ms.openlocfilehash: 695f8c311eb33f134bed053b1550612040da6fae
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672866"
---
# <a name="database-providers"></a>Dostawcy baz danych

Entity Framework Core mogą uzyskać dostęp do wielu różnych baz danych za za poorednictwem bibliotek wtyczek nazywanych dostawcami baz danych.

## <a name="current-providers"></a>Bieżący dostawcy

> [!IMPORTANT]  
> Dostawcy EF Core są kompilowane przy użyciu różnych źródeł. Nie wszyscy dostawcy są obsługiwani jako część [projektu Entity Framework Core](https://github.com/aspnet/EntityFrameworkCore). Rozważając dostawcę, należy oszacować jakość, licencjonowanie, pomoc techniczną itp., aby upewnić się, że spełniają one wymagania. Aby uzyskać szczegółowe informacje o zgodności wersji, należy również zapoznać się z dokumentacją każdego dostawcy.

> [!IMPORTANT]  
> Dostawcy EF Core zwykle pracują w różnych wersjach pomocniczych, ale nie w wersjach głównych. Na przykład dostawca opublikowany dla EF Core 2,1 powinien współpracować z EF Core 2,2, ale nie będzie działał z EF Core 3,0. 

| Pakiet NuGet                                                                                                        | Obsługiwane aparaty bazy danych | Element utrzymujący/dostawca                                                           | Uwagi/wymagania | Skompilowano dla wersji | Przydatne łącza                                                                                                                                                                                       |
|:---------------------------------------------------------------------------------------------------------------------|:---------------------------|:------------------------------------------------------------------------------|:---------------------|:------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Microsoft. EntityFrameworkCore. SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer)    | SQL Server 2012 do wewnątrz    | [Projekt EF Core](https://github.com/aspnet/EntityFrameworkCore/) (Microsoft) |                      | 3,1               | [docs](xref:core/providers/sql-server/index)                                                                                                                                                       |
| [Microsoft. EntityFrameworkCore. sqlite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite)          | SQLite 3,7         | [Projekt EF Core](https://github.com/aspnet/EntityFrameworkCore/) (Microsoft) |                      | 3,1               | [docs](xref:core/providers/sqlite/index)                                                                                                                                                           |
| [Microsoft. EntityFrameworkCore. inMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)      | EF Core bazę danych w pamięci | [Projekt EF Core](https://github.com/aspnet/EntityFrameworkCore/) (Microsoft) | [Ograniczenia](xref:core/miscellaneous/testing/in-memory)                 | 3,1               | [docs](xref:core/providers/in-memory/index)                                                                                                                                                        |
| [Microsoft. EntityFrameworkCore. Cosmos](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos)          | Azure Cosmos DB — interfejs SQL API    | [Projekt EF Core](https://github.com/aspnet/EntityFrameworkCore/) (Microsoft) |                      | 3,1               | [docs](xref:core/providers/cosmos/index)                                                                                                                                                           |
| [Npgsql. EntityFrameworkCore. PostgreSQL](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL)        | PostgreSQL                 | [Zespół deweloperów Npgsql](https://github.com/npgsql)                          |                      | 3,1               | [docs](https://www.npgsql.org/efcore/index.html)                                                                                                                                                   |
| [Pomelo. EntityFrameworkCore. MySql](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql)                  | MySQL, MariaDB             | [Projekt pomelo Foundation](https://github.com/PomeloFoundation)              |                      | 3,1               | [Readme](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql/blob/master/README.md)                                                                                               |
| [Devart. Data. MySql. EFCore](https://www.nuget.org/packages/Devart.Data.MySql.EFCore/)                                 | Baza danych MySQL 5            | [DevArt](https://www.devart.com/)                                             | Święcona                 | 3.0               | [docs](https://www.devart.com/dotconnect/mysql/docs/)                                                                                                                                              |
| [Devart. Data. Oracle. EFCore](https://www.nuget.org/packages/Devart.Data.Oracle.EFCore/)                               | Oracle DB 9.2.0.4  | [DevArt](https://www.devart.com/)                                             | Święcona                 | 3.0               | [docs](https://www.devart.com/dotconnect/oracle/docs/)                                                                                                                                             |
| [Devart. Data. PostgreSql. EFCore](https://www.nuget.org/packages/Devart.Data.PostgreSql.EFCore/)                       | PostgreSQL 8,0 do wewnątrz     | [DevArt](https://www.devart.com/)                                             | Święcona                 | 3.0               | [docs](https://www.devart.com/dotconnect/postgresql/docs/)                                                                                                                                         |
| [Devart. Data. SQLite. EFCore](https://www.nuget.org/packages/Devart.Data.SQLite.EFCore/)                               | SQLite 3 lub nowszy           | [DevArt](https://www.devart.com/)                                             | Święcona                 | 3.0               | [docs](https://www.devart.com/dotconnect/sqlite/docs/)                                                                                                                                             |
| [FileContextCore](https://www.nuget.org/packages/FileContextCore/)                                                   | Przechowuje dane w plikach       | [Morris Janatzek](https://github.com/morrisjdev)                              | Do celów deweloperskich | 3.0               | [Readme](https://github.com/morrisjdev/FileContextCore/blob/master/README.md)                                                                                                                                              |
| [EntityFrameworkCore. Jet](https://www.nuget.org/packages/EntityFrameworkCore.Jet/)                                   | Pliki programu Microsoft Access     | [Bubi](https://github.com/bubibubi)                                           | .NET Framework       | 2,2               | [Readme](https://github.com/bubibubi/EntityFrameworkCore.Jet/blob/master/docs/README.md)                                                                                                           |
| [EntityFrameworkCore.SqlServerCompact35](https://www.nuget.org/packages/EntityFrameworkCore.SqlServerCompact35)      | SQL Server Compact 3.5     | [Erik Ejlskov Jensen](https://github.com/ErikEJ/)                             | .NET Framework       | 2,2               | [witryny](https://github.com/ErikEJ/EntityFramework.SqlServerCompact/wiki/Using-EF-Core-with-SQL-Server-Compact-in-Traditional-.NET-Applications)                                                     |
| [EntityFrameworkCore.SqlServerCompact40](https://www.nuget.org/packages/EntityFrameworkCore.SqlServerCompact40)      | SQL Server Compact 4.0     | [Erik Ejlskov Jensen](https://github.com/ErikEJ/)                             | .NET Framework       | 2,2               | [witryny](https://github.com/ErikEJ/EntityFramework.SqlServerCompact/wiki/Using-EF-Core-with-SQL-Server-Compact-in-Traditional-.NET-Applications)                                                     |
| [FirebirdSql.EntityFrameworkCore.Firebird](https://www.nuget.org/packages/FirebirdSql.EntityFrameworkCore.Firebird/) | Firebird 2,5 i 3. x       | [Jiří Činčura](https://github.com/cincuranet)                                 |                      | 3,1               | [docs](https://github.com/cincuranet/FirebirdSql.Data.FirebirdClient/blob/master/Provider/docs/entity-framework-core.md)                                                                           |
| [Teradata. EntityFrameworkCore](https://www.nuget.org/packages/Teradata.EntityFrameworkCore/)                         | Baza danych programu Teradata 16,10 | [Teradata](https://downloads.teradata.com/download/connectivity/net-data-provider-for-teradata) | | 2,2               |[producenta](https://www.nuget.org/packages/Teradata.EntityFrameworkCore/)                                                                                                                            |
| [EntityFrameworkCore.FirebirdSql](https://www.nuget.org/packages/EntityFrameworkCore.FirebirdSql/)                   | Firebird 2,5 i 3. x       | [Rafael Almeida](https://github.com/ralmsdeveloper)                           |                      | 2.1               | [witryny](https://github.com/ralmsdeveloper/EntityFrameworkCore.FirebirdSQL/wiki)                                                                                                                     |
| [EntityFrameworkCore.OpenEdge](https://www.nuget.org/packages/EntityFrameworkCore.OpenEdge/)                         | Progress OpenEdge          | [Alex Wiese](https://github.com/alexwiese)                                    |                      | 2.1               | [Readme](https://github.com/alexwiese/EntityFrameworkCore.OpenEdge/blob/master/README.md)                                                                                                          |
| [MySql. Data. EntityFrameworkCore](https://www.nuget.org/packages/MySql.Data.EntityFrameworkCore)                      | MySQL                      | [Projekt MySQL](https://dev.mysql.com) (Oracle)                               |                      | 2.1               | [docs](https://dev.mysql.com/doc/connector-net/en/connector-net-entityframework-core.html)                                                                                                         |
| [Oracle. EntityFrameworkCore](https://www.nuget.org/packages/Oracle.EntityFrameworkCore/)                             | Oracle DB 11,2 do wewnątrz     | [Oracle](https://www.oracle.com/technetwork/topics/dotnet/)                   |                      | 2.1               | [producenta](https://www.oracle.com/technetwork/topics/dotnet/)                                                                                                                                       |
| [Usługę. EntityFrameworkCore](https://www.nuget.org/packages/IBM.EntityFrameworkCore)                                    | DB2, Informix              | [IBM](https://ibm.com)                                                        | Wersja systemu Windows      | 2.0               | [wpisów](https://www.ibm.com/developerworks/community/blogs/96960515-2ea1-4391-8170-b0515d08e4da/entry/Creating_Entity_Data_Model_using_IBM_Data_Server_providers_for_Entity_Framework_Core?lang=en) |
| [Usługę. EntityFrameworkCore-lnx](https://www.nuget.org/packages/IBM.EntityFrameworkCore-lnx)                            | DB2, Informix              | [IBM](https://ibm.com)                                                        | Wersja systemu Linux        | 2.0               | [wpisów](https://www.ibm.com/developerworks/community/blogs/96960515-2ea1-4391-8170-b0515d08e4da/entry/Creating_Entity_Data_Model_using_IBM_Data_Server_providers_for_Entity_Framework_Core?lang=en) |
| [Usługę. EntityFrameworkCore — OSX](https://www.nuget.org/packages/IBM.EntityFrameworkCore-osx)                            | DB2, Informix              | [IBM](https://ibm.com)                                                        | wersja macOS        | 2.0               | [wpisów](https://www.ibm.com/developerworks/community/blogs/96960515-2ea1-4391-8170-b0515d08e4da/entry/Creating_Entity_Data_Model_using_IBM_Data_Server_providers_for_Entity_Framework_Core?lang=en) |
| [Pomelo.EntityFrameworkCore.MyCat](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MyCat)                  | Serwer MyCAT               | [Projekt pomelo Foundation](https://github.com/PomeloFoundation)              | Tylko wersja wstępna      | 1.1               | [Readme](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MyCat/blob/master/README.md)                                                                                               |

## <a name="adding-a-database-provider-to-your-application"></a>Dodawanie dostawcy bazy danych do aplikacji

Większość dostawców baz danych dla EF Core są dystrybuowane jako pakiety NuGet i można ją zainstalować w następujący sposób:

## <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package provider_package_name
```

## <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
install-package provider_package_name
```

***

Po zainstalowaniu dostawca zostanie skonfigurowany w `DbContext` `OnConfiguring` metodzie lub w `AddDbContext` metodzie, jeśli używasz kontenera iniekcji zależności.
Na przykład poniższy wiersz konfiguruje dostawcę SQL Server z przekazywaniem parametrów połączenia:

``` csharp
optionsBuilder.UseSqlServer(
    "Server=(localdb)\mssqllocaldb;Database=MyDatabase;Trusted_Connection=True;");
```  

Dostawcy bazy danych mogą rozciągnąć EF Core, aby umożliwić dostęp do funkcji unikatowych dla konkretnych baz danych.
Niektóre koncepcje są wspólne dla większości baz danych i znajdują się w głównych składnikach EF Core.
Takie koncepcje obejmują ekspresowe zapytania w LINQ, Transactions i śledzenie zmian w obiektach po ich załadowaniu z bazy danych.
Niektóre koncepcje są specyficzne dla określonego dostawcy.
Na przykład dostawca SQL Server umożliwia [skonfigurowanie tabel zoptymalizowanych pod kątem pamięci](xref:core/providers/sql-server/memory-optimized-tables) (funkcji specyficznych dla SQL Server).
Inne koncepcje są specyficzne dla klasy dostawców.
Na przykład EF Core dostawców dla relacyjnych baz danych kompilują się na wspólną `Microsoft.EntityFrameworkCore.Relational` bibliotekę, która udostępnia interfejsy API do konfigurowania mapowań tabel i kolumn, ograniczenia FOREIGN KEY itp. Dostawcy są zwykle dystrybuowane jako pakiety NuGet.

> [!IMPORTANT]  
> Gdy zostanie wydana nowa wersja poprawki EF Core, często zawiera ona aktualizacje `Microsoft.EntityFrameworkCore.Relational` pakietu.
> Po dodaniu dostawcy relacyjnej bazy danych pakiet ten zostanie przechodnią zależnością aplikacji.
> Ale wielu dostawców jest wydawanych niezależnie od EF Core i może nie zostać zaktualizowany w zależności od nowszej wersji poprawki tego pakietu.
> Aby upewnić się, że zostaną wyświetlone wszystkie poprawki błędów, zaleca się dodanie wersji poprawki programu `Microsoft.EntityFrameworkCore.Relational` jako bezpośredniej zależności aplikacji.
