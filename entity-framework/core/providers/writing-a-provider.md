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
# <a name="writing-a-database-provider"></a><span data-ttu-id="1cb55-102">Tworzenie dostawcy bazy danych</span><span class="sxs-lookup"><span data-stu-id="1cb55-102">Writing a Database Provider</span></span>

<span data-ttu-id="1cb55-103">Aby uzyskać informacje na temat pisania dostawcy bazy danych Entity Framework Core, zobacz, [czy chcesz napisać dostawcę EF Core](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/) za pomocą [Arthur Vickers](https://github.com/ajcvickers).</span><span class="sxs-lookup"><span data-stu-id="1cb55-103">For information about writing an Entity Framework Core database provider, see [So you want to write an EF Core provider](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/) by [Arthur Vickers](https://github.com/ajcvickers).</span></span>

> [!NOTE]
> <span data-ttu-id="1cb55-104">Te wpisy nie zostały zaktualizowane od momentu EF Core 1,1 i wprowadzono znaczące zmiany od tego czasu.</span><span class="sxs-lookup"><span data-stu-id="1cb55-104">These posts have not been updated since EF Core 1.1 and there have been significant changes since that time.</span></span>  
<span data-ttu-id="1cb55-105">[Problem 681](https://github.com/dotnet/EntityFramework.Docs/issues/681) służy do śledzenia aktualizacji tej dokumentacji.</span><span class="sxs-lookup"><span data-stu-id="1cb55-105">[Issue 681](https://github.com/dotnet/EntityFramework.Docs/issues/681) is tracking updates to this documentation.</span></span>

<span data-ttu-id="1cb55-106">Baza kodu EF Core jest typu open source i zawiera kilku dostawców baz danych, których można użyć jako odwołania.</span><span class="sxs-lookup"><span data-stu-id="1cb55-106">The EF Core codebase is open source and contains several database providers that can be used as a reference.</span></span> <span data-ttu-id="1cb55-107">Kod źródłowy można znaleźć pod adresem <https://github.com/aspnet/EntityFrameworkCore>.</span><span class="sxs-lookup"><span data-stu-id="1cb55-107">You can find the source code at <https://github.com/aspnet/EntityFrameworkCore>.</span></span> <span data-ttu-id="1cb55-108">Pomocne może być również przeszukanie kodu dla często używanych dostawców innych firm, takich jak [Npgsql](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL), [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql)i [SQL Server Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact).</span><span class="sxs-lookup"><span data-stu-id="1cb55-108">It may also be helpful to look at the code for commonly used third-party providers, such as [Npgsql](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL), [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql), and [SQL Server Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact).</span></span> <span data-ttu-id="1cb55-109">W szczególności te projekty są skonfigurowane do rozszerania i uruchamiania testów funkcjonalnych publikowanych na platformie NuGet.</span><span class="sxs-lookup"><span data-stu-id="1cb55-109">In particular, these projects are setup to extend from and run functional tests that we publish on NuGet.</span></span> <span data-ttu-id="1cb55-110">Ten rodzaj instalacji jest zdecydowanie zalecany.</span><span class="sxs-lookup"><span data-stu-id="1cb55-110">This kind of setup is strongly recommended.</span></span>

## <a name="keeping-up-to-date-with-provider-changes"></a><span data-ttu-id="1cb55-111">Utrzymywanie Aktualności przy użyciu zmian dostawcy</span><span class="sxs-lookup"><span data-stu-id="1cb55-111">Keeping up-to-date with provider changes</span></span>

<span data-ttu-id="1cb55-112">Począwszy od wersji 2,1, został utworzony [Dziennik zmian](provider-log.md) , które mogą wymagać odpowiednich zmian w kodzie dostawcy.</span><span class="sxs-lookup"><span data-stu-id="1cb55-112">Starting with work after the 2.1 release, we have created a [log of changes](provider-log.md) that may need corresponding changes in provider code.</span></span> <span data-ttu-id="1cb55-113">Jest to przydatne w przypadku aktualizowania istniejącego dostawcy do pracy z nową wersją EF Core.</span><span class="sxs-lookup"><span data-stu-id="1cb55-113">This is intended to help when updating an existing provider to work with a new version of EF Core.</span></span>

<span data-ttu-id="1cb55-114">Przed 2,1, używamy etykiet [`providers-beware`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware) i [`providers-fyi`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi) w naszych problemach z usługą GitHub i żądaniami ściągnięcia w podobny sposób.</span><span class="sxs-lookup"><span data-stu-id="1cb55-114">Prior to 2.1, we used the [`providers-beware`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware) and [`providers-fyi`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi) labels on our GitHub issues and pull requests for a similar purpose.</span></span> <span data-ttu-id="1cb55-115">Continiue się na korzystanie z tych poszukiwaniu w przypadku problemów, aby wskazać, które elementy robocze w danej wersji mogą również wymagać pracy w ramach dostawców.</span><span class="sxs-lookup"><span data-stu-id="1cb55-115">We will continiue to use these lables on issues to give an indication which work items in a given release may also require work to be done in providers.</span></span> <span data-ttu-id="1cb55-116">`providers-beware` Etykieta zazwyczaj oznacza, że implementacja elementu pracy może przerwać dostawców, natomiast `providers-fyi` etykieta zazwyczaj oznacza, że dostawcy nie będą przerywane, ale może być konieczne zmodyfikowanie kodu, na przykład w celu włączenia nowych funkcji.</span><span class="sxs-lookup"><span data-stu-id="1cb55-116">A `providers-beware` label typically means that the implementation of an work item may break providers, while a `providers-fyi` label typically means that providers will not be broken, but code may need to be changed anyway, for example, to enable new functionality.</span></span>

## <a name="suggested-naming-of-third-party-providers"></a><span data-ttu-id="1cb55-117">Sugerowane nazewnictwo dostawców innych firm</span><span class="sxs-lookup"><span data-stu-id="1cb55-117">Suggested naming of third party providers</span></span>

<span data-ttu-id="1cb55-118">Zalecamy używanie następujących nazw dla pakietów NuGet.</span><span class="sxs-lookup"><span data-stu-id="1cb55-118">We suggest using the following naming for NuGet packages.</span></span> <span data-ttu-id="1cb55-119">Jest to zgodne z nazwami pakietów dostarczonych przez zespół EF Core.</span><span class="sxs-lookup"><span data-stu-id="1cb55-119">This is consistent with the names of packages delivered by the EF Core team.</span></span>

`<Optional project/company name>.EntityFrameworkCore.<Database engine name>`

<span data-ttu-id="1cb55-120">Przykład:</span><span class="sxs-lookup"><span data-stu-id="1cb55-120">For example:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Npgsql.EntityFrameworkCore.PostgreSQL`
* `EntityFrameworkCore.SqlServerCompact40`
