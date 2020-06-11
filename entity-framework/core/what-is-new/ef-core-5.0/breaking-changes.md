---
title: Istotne zmiany w EF Core 5,0 — EF Core
author: bricelam
ms.date: 06/05/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: 0ffe62d9b21dd901c2cdaf0ea5d416d14938497f
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84666196"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="16ce8-102">Istotne zmiany w EF Core 5,0</span><span class="sxs-lookup"><span data-stu-id="16ce8-102">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="16ce8-103">Poniższe zmiany dotyczące interfejsu API i zachowania mogą spowodować przerwanie aktualizowania istniejących aplikacji do EF Core 5.0.0.</span><span class="sxs-lookup"><span data-stu-id="16ce8-103">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="16ce8-104">Podsumowanie</span><span class="sxs-lookup"><span data-stu-id="16ce8-104">Summary</span></span>

| <span data-ttu-id="16ce8-105">**Zmiana podziału**</span><span class="sxs-lookup"><span data-stu-id="16ce8-105">**Breaking change**</span></span>                                                                                                               | <span data-ttu-id="16ce8-106">**Wpływ**</span><span class="sxs-lookup"><span data-stu-id="16ce8-106">**Impact**</span></span> |
|:----------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="16ce8-107">Usunięto metodę HasGeometricDimension z rozszerzenia SQLite NKTY przerwania</span><span class="sxs-lookup"><span data-stu-id="16ce8-107">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#removed-hasgeometricdimension-method-from-sqlite-nts-extension) | <span data-ttu-id="16ce8-108">Małe</span><span class="sxs-lookup"><span data-stu-id="16ce8-108">Low</span></span>        |

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="16ce8-109">Usunięto metodę HasGeometricDimension z rozszerzenia SQLite NKTY przerwania</span><span class="sxs-lookup"><span data-stu-id="16ce8-109">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="16ce8-110">Śledzenie problemu #14257</span><span class="sxs-lookup"><span data-stu-id="16ce8-110">Tracking Issue #14257</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

<span data-ttu-id="16ce8-111">**Stare zachowanie**</span><span class="sxs-lookup"><span data-stu-id="16ce8-111">**Old behavior**</span></span>

<span data-ttu-id="16ce8-112">HasGeometricDimension został użyty do włączenia dodatkowych wymiarów (Z i M) w kolumnach geometrycznych.</span><span class="sxs-lookup"><span data-stu-id="16ce8-112">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="16ce8-113">Jednak kiedykolwiek dotyczy to tylko tworzenia bazy danych.</span><span class="sxs-lookup"><span data-stu-id="16ce8-113">However, it only ever affected database creation.</span></span> <span data-ttu-id="16ce8-114">Nie można go określić do przeszukiwania wartości z dodatkowymi wymiarami.</span><span class="sxs-lookup"><span data-stu-id="16ce8-114">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="16ce8-115">Ponadto nie działała poprawnie podczas wstawiania lub aktualizowania wartości z dodatkowymi wymiarami ([zobacz #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span><span class="sxs-lookup"><span data-stu-id="16ce8-115">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span></span>

<span data-ttu-id="16ce8-116">**Nowe zachowanie**</span><span class="sxs-lookup"><span data-stu-id="16ce8-116">**New behavior**</span></span>

<span data-ttu-id="16ce8-117">Aby włączyć Wstawianie i aktualizowanie wartości geometrii z dodatkowymi wymiarami (Z i M), wymiar należy określić jako część nazwy typu kolumny.</span><span class="sxs-lookup"><span data-stu-id="16ce8-117">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="16ce8-118">Jest to ściśle zgodne z podstawowym zachowaniem funkcji AddGeometryColumn SpatiaLite.</span><span class="sxs-lookup"><span data-stu-id="16ce8-118">This more closely matches the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

<span data-ttu-id="16ce8-119">**Zalet**</span><span class="sxs-lookup"><span data-stu-id="16ce8-119">**Why**</span></span>

<span data-ttu-id="16ce8-120">Użycie HasGeometricDimension po określeniu wymiaru w typie kolumny jest zbędne i nadmiarowe, dlatego całkowicie usunięto HasGeometricDimension.</span><span class="sxs-lookup"><span data-stu-id="16ce8-120">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

<span data-ttu-id="16ce8-121">**Środki zaradcze**</span><span class="sxs-lookup"><span data-stu-id="16ce8-121">**Mitigations**</span></span>

<span data-ttu-id="16ce8-122">Użyj, `HasColumnType` Aby określić wymiar:</span><span class="sxs-lookup"><span data-stu-id="16ce8-122">Use `HasColumnType` to specify the dimension:</span></span>

```cs
modelBuilder.Entity<GeoEntity>(
    x =>
    {
        // Allow any GEOMETRY value with optional Z and M values
        x.Property(e => e.Geometry).HasColumnType("GEOMETRYZM");

        // Allow only POINT values with an optional Z value
        x.Property(e => e.Point).HasColumnType("POINTZ");
    });
```
