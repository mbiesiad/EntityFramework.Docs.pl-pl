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
# <a name="breaking-changes-in-ef-core-50"></a>Istotne zmiany w EF Core 5,0

Poniższe zmiany dotyczące interfejsu API i zachowania mogą spowodować przerwanie aktualizowania istniejących aplikacji do EF Core 5.0.0.

## <a name="summary"></a>Podsumowanie

| **Zmiana podziału**                                                                                                               | **Wpływ** |
|:----------------------------------------------------------------------------------------------------------------------------------|------------|
| [Usunięto metodę HasGeometricDimension z rozszerzenia SQLite NKTY przerwania](#removed-hasgeometricdimension-method-from-sqlite-nts-extension) | Małe        |

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a>Usunięto metodę HasGeometricDimension z rozszerzenia SQLite NKTY przerwania

[Śledzenie problemu #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

**Stare zachowanie**

HasGeometricDimension został użyty do włączenia dodatkowych wymiarów (Z i M) w kolumnach geometrycznych. Jednak kiedykolwiek dotyczy to tylko tworzenia bazy danych. Nie można go określić do przeszukiwania wartości z dodatkowymi wymiarami. Ponadto nie działała poprawnie podczas wstawiania lub aktualizowania wartości z dodatkowymi wymiarami ([zobacz #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).

**Nowe zachowanie**

Aby włączyć Wstawianie i aktualizowanie wartości geometrii z dodatkowymi wymiarami (Z i M), wymiar należy określić jako część nazwy typu kolumny. Jest to ściśle zgodne z podstawowym zachowaniem funkcji AddGeometryColumn SpatiaLite.

**Zalet**

Użycie HasGeometricDimension po określeniu wymiaru w typie kolumny jest zbędne i nadmiarowe, dlatego całkowicie usunięto HasGeometricDimension.

**Środki zaradcze**

Użyj, `HasColumnType` Aby określić wymiar:

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
