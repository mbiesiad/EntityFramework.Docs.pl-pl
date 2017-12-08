---
title: "Porównaj EF Core & EF6"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: a6b9cd22-6803-4c6c-a4d4-21147c0a81cb
uid: efcore-and-ef6/index
ms.openlocfilehash: 4442e6931327f6a07d98aee47211ddbeed51a467
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2017
---
# <a name="compare-ef-core--ef6"></a><span data-ttu-id="cffc8-102">Porównaj EF Core & EF6</span><span class="sxs-lookup"><span data-stu-id="cffc8-102">Compare EF Core & EF6</span></span>

<span data-ttu-id="cffc8-103">Istnieją dwie wersje programu Entity Framework, Entity Framework Core oraz Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="cffc8-103">There are two versions of Entity Framework, Entity Framework Core and Entity Framework 6.</span></span>

## <a name="entity-framework-6"></a><span data-ttu-id="cffc8-104">Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="cffc8-104">Entity Framework 6</span></span>

<span data-ttu-id="cffc8-105">Entity Framework 6 (EF6) to technologia dostępu próby sklonowania i przetestowany danych z wielu lat funkcji i stabilizacji.</span><span class="sxs-lookup"><span data-stu-id="cffc8-105">Entity Framework 6 (EF6) is a tried and tested data access technology with many years of features and stabilization.</span></span> <span data-ttu-id="cffc8-106">On opublikowany po raz pierwszy w 2008 jako część programu .NET Framework 3.5 z dodatkiem SP1 i programu Visual Studio 2008 z dodatkiem SP1.</span><span class="sxs-lookup"><span data-stu-id="cffc8-106">It first released in 2008, as part of .NET Framework 3.5 SP1 and Visual Studio 2008 SP1.</span></span> <span data-ttu-id="cffc8-107">Począwszy od wersji EF4.1 została wysłana jako [pakietu EntityFramework NuGet](https://www.nuget.org/packages/EntityFramework/) — obecnie najpopularniejsze pakietu na NuGet.org.</span><span class="sxs-lookup"><span data-stu-id="cffc8-107">Starting with the EF4.1 release it has shipped as the [EntityFramework NuGet package](https://www.nuget.org/packages/EntityFramework/) - currently the most popular package on NuGet.org.</span></span>

<span data-ttu-id="cffc8-108">EF6 jest nadal obsługiwane produktu i będzie wyświetlana poprawek i drobne ulepszenia przez pewien czas do trybu.</span><span class="sxs-lookup"><span data-stu-id="cffc8-108">EF6 continues to be a supported product, and will continue to see bug fixes and minor improvements for some time to come.</span></span>

## <a name="entity-framework-core"></a><span data-ttu-id="cffc8-109">Program Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="cffc8-109">Entity Framework Core</span></span>

<span data-ttu-id="cffc8-110">Entity Framework Core (EF Core) to lekkie, rozszerzalne i wieloplatformowych wersji programu Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="cffc8-110">Entity Framework Core (EF Core) is a lightweight, extensible, and cross-platform version of Entity Framework.</span></span> <span data-ttu-id="cffc8-111">Podstawowe EF wprowadzono wiele ulepszeń i nowe funkcje w porównaniu z EF6.</span><span class="sxs-lookup"><span data-stu-id="cffc8-111">EF Core introduces many improvements and new features when compared with EF6.</span></span> <span data-ttu-id="cffc8-112">W tym samym czasie EF Core jest kod podstawowy, a nie jako dojrzałe jako EF6.</span><span class="sxs-lookup"><span data-stu-id="cffc8-112">At the same time, EF Core is a new code base and not as mature as EF6.</span></span>

<span data-ttu-id="cffc8-113">Podstawowe EF temu środowisko dewelopera z EF6 i większość najwyższego poziomu interfejsów API pozostają takie same, EF rdzenie będą możesz bardzo dostosowanym do pracowników, którzy użyli EF6.</span><span class="sxs-lookup"><span data-stu-id="cffc8-113">EF Core keeps the developer experience from EF6, and most of the top-level APIs remain the same too, so EF Core will feel very familiar to folks who have used EF6.</span></span> <span data-ttu-id="cffc8-114">W tym samym czasie EF Core jest wbudowany w zupełnie nowy zestaw składników podstawowych.</span><span class="sxs-lookup"><span data-stu-id="cffc8-114">At the same time, EF Core is built over a completely new set of core components.</span></span> <span data-ttu-id="cffc8-115">Oznacza to, że EF Core automatycznie nie dziedziczy z EF6 wszystkich funkcji.</span><span class="sxs-lookup"><span data-stu-id="cffc8-115">This means EF Core doesn't automatically inherit all the features from EF6.</span></span> <span data-ttu-id="cffc8-116">Niektóre z tych funkcji będą widoczne w przyszłych wersjach (na przykład opóźnionego ładowania i połączenia odporności), inne rzadko używane się, że nie będzie zaimplementowana funkcji EF główną.</span><span class="sxs-lookup"><span data-stu-id="cffc8-116">Some of these features will show up in future releases (such as lazy loading and connection resiliency), other less commonly used features will not be implemented in EF Core.</span></span>

<span data-ttu-id="cffc8-117">Nowe, rozszerzalne i lightweight rdzeni również zezwolił nam dodać niektóre funkcje do Core EF, który nie będzie zaimplementowana w EF6 (takie jak klucze alternatywne i oceny mieszanych/bazy danych klienta w zapytaniach LINQ).</span><span class="sxs-lookup"><span data-stu-id="cffc8-117">The new, extensible, and lightweight core has also allowed us to add some features to EF Core that will not be implemented in EF6 (such as alternate keys and mixed client/database evaluation in LINQ queries).</span></span>