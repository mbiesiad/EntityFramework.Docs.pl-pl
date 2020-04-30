---
title: Pisanie dostawcy bazy danych — EF Core
author: anmiller
ms.date: 10/27/2016
ms.assetid: 1165e2ec-e421-43fc-92ab-d92f9ab3c494
uid: core/providers/writing-a-provider
ms.openlocfilehash: 662c7e386bbdf0ff1e4e5051349d6a5c56a3df7b
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82538471"
---
# <a name="writing-a-database-provider"></a>Tworzenie dostawcy bazy danych

Aby uzyskać informacje na temat pisania dostawcy bazy danych Entity Framework Core, zobacz, [czy chcesz napisać dostawcę EF Core](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/) za pomocą [Arthur Vickers](https://github.com/ajcvickers).

> [!NOTE]
> Te wpisy nie zostały zaktualizowane od momentu EF Core 1,1 i wprowadzono znaczące zmiany od tego czasu.  
[Problem 681](https://github.com/dotnet/EntityFramework.Docs/issues/681) służy do śledzenia aktualizacji tej dokumentacji.

Baza kodu EF Core jest typu open source i zawiera kilku dostawców baz danych, których można użyć jako odwołania. Kod źródłowy można znaleźć pod adresem <https://github.com/aspnet/EntityFrameworkCore>. Pomocne może być również przeszukanie kodu dla często używanych dostawców innych firm, takich jak [Npgsql](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL), [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql)i [SQL Server Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact). W szczególności te projekty są skonfigurowane do rozszerania i uruchamiania testów funkcjonalnych publikowanych na platformie NuGet. Ten rodzaj instalacji jest zdecydowanie zalecany.

## <a name="keeping-up-to-date-with-provider-changes"></a>Utrzymywanie Aktualności przy użyciu zmian dostawcy

Począwszy od wersji 2,1, został utworzony [Dziennik zmian](provider-log.md) , które mogą wymagać odpowiednich zmian w kodzie dostawcy. Jest to przydatne w przypadku aktualizowania istniejącego dostawcy do pracy z nową wersją EF Core.

Przed 2,1, używamy etykiet [`providers-beware`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware) i [`providers-fyi`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi) w naszych problemach z usługą GitHub i żądaniami ściągnięcia w podobny sposób. Continiue się na korzystanie z tych poszukiwaniu w przypadku problemów, aby wskazać, które elementy robocze w danej wersji mogą również wymagać pracy w ramach dostawców. `providers-beware` Etykieta zazwyczaj oznacza, że implementacja elementu pracy może przerwać dostawców, natomiast `providers-fyi` etykieta zazwyczaj oznacza, że dostawcy nie będą przerywane, ale może być konieczne zmodyfikowanie kodu, na przykład w celu włączenia nowych funkcji.

## <a name="suggested-naming-of-third-party-providers"></a>Sugerowane nazewnictwo dostawców innych firm

Zalecamy używanie następujących nazw dla pakietów NuGet. Jest to zgodne z nazwami pakietów dostarczonych przez zespół EF Core.

`<Optional project/company name>.EntityFrameworkCore.<Database engine name>`

Przykład:

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Npgsql.EntityFrameworkCore.PostgreSQL`
* `EntityFrameworkCore.SqlServerCompact40`
