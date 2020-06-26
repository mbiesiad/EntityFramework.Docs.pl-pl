---
title: EF Core wersje i planowanie
description: Bieżące wersje EF Core oraz szczegóły harmonogramu/planowania dla przyszłych wersji
author: ajcvickers
ms.date: 06/11/2020
uid: core/what-is-new/index
ms.openlocfilehash: 7d4f37c227feec0fad9f828999680f834ddd2d4e
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370243"
---
# <a name="ef-core-releases-and-planning"></a>EF Core wersje i planowanie

## <a name="stable-releases"></a>Stabilne wersje

| Wydanie | Platforma docelowa | Obsługiwane do | Linki
|:--------|------------------|-----------------|------
| [EF Core 3,1](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore) | .NET Standard 2,0 | 3 grudnia 2022 (LTS) | [Instruktaż](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-3-1-and-entity-framework-6-4/)
| ~~[EF Core 3.0](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/3.0.3)~~ | .NET Standard 2,1 | Wygasła 3 marca, 2020 | [Anons](https://devblogs.microsoft.com/dotnet/announcing-ef-core-3-0-and-ef-6-3-general-availability/)  /  Istotne [zmiany](ef-core-3.0/breaking-changes.md)
| ~~[EF Core 2.2](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/2.2.6)~~ | .NET Standard 2,0 | Wygasł 23 grudnia, 2019 | [Instruktaż](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-2-2/)
| [EF Core 2.1](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/2.1.14) | .NET Standard 2,0 | 21 sierpnia 2021 (LTS) | [Instruktaż](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-2-1/)
| ~~[EF Core 2.0](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/2.0.3)~~ | .NET Standard 2,0 | Wygasła 1 października 2018 | [Instruktaż](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-2-0/)
| ~~[EF Core 1.1](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/1.1.6)~~ | .NET Standard 1,3 | Wygasła Czerwiec 27 2019 | [Instruktaż](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-1-1/)
| ~~[EF Core 1.0](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/1.0.6)~~ | .NET Standard 1,3 | Wygasła Czerwiec 27 2019 | [Instruktaż](https://devblogs.microsoft.com/dotnet/entity-framework-core-1-0-0-available/)

Zapoznaj się z [obsługiwanymi platformami](../platforms/index.md) , aby uzyskać informacje o określonych platformach obsługiwanych przez poszczególne EF Core wydania.

Zobacz [zasady pomocy technicznej platformy .NET](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) , aby uzyskać informacje o pomocy technicznej dotyczącej wygasania i długoterminowych wersji (LTS).

## <a name="guidance-on-updating-to-new-releases"></a>Wskazówki dotyczące aktualizacji do nowych wersji

* Obsługiwane wersje są poprawione dla bezpieczeństwa i innych krytycznych usterek. Zawsze używaj najnowszej poprawki danej wersji. Na przykład dla EF Core 2,1 Użyj 2.1.14.
* Aktualizacje wersji głównej (na przykład z EF Core 2 do EF Core 3) często mają krytyczne zmiany. W przypadku aktualizacji w wersjach głównych zaleca się dokładne testowanie. Skorzystaj z powyższych linków zmiany, aby uzyskać wskazówki dotyczące rozwiązywania istotnych zmian.
* Aktualizacje wersji pomocniczej zwykle nie zawierają istotnych zmian. Jednak testy dokładne są nadal zalecane, ponieważ nowe funkcje mogą wprowadzać regresje.

## <a name="release-planning-and-schedules"></a>Planowanie i harmonogramy wydania

EF Core wersje są wyrównane z [harmonogramem wysyłki platformy .NET Core](https://github.com/dotnet/core/blob/master/roadmap.md).

Wersje poprawek zwykle wysyłają co miesiąc, ale mają długi czas realizacji.
Pracujemy nad tym, aby udoskonalić ten produkt.

Aby uzyskać więcej informacji na temat tego, co należy dostarczyć w poszczególnych wersjach, zobacz [proces planowania zlecenia](release-planning.md) .
Zwykle nie wykonujemy szczegółowego planowania niż w przypadku następnej wersji głównej lub pomocniczej.

## <a name="ef-core-50"></a>EF Core 5.0

Następne planowane, stabilne wydanie to **EF Core 5,0**, zaplanowane dla listopada 2020.

[Plan wysokiego poziomu dla EF Core 5,0](xref:core/what-is-new/ef-core-5.0/plan) został utworzony przez następujący [proces planowania wydania](release-planning.md).

Twoja opinia na temat planowania jest ważna.
Najlepszym sposobem na wskazanie znaczenia problemu jest zagłosowanie (kciuki 👍 ) dla tego problemu w serwisie GitHub.
Te dane zostaną następnie przetworzone do procesu planowania dla kolejnej wersji.

### <a name="get-it-now"></a>Pobierz teraz!

Pakiety EF Core 5,0 są **teraz dostępne** jako

* [Codzienne kompilacje](https://github.com/dotnet/aspnetcore/blob/master/docs/DailyBuilds.md)
  * Wszystkie najnowsze funkcje i poprawki błędów. Ogólnie bardzo stabilne; 57000 + testy są uruchamiane dla każdej kompilacji.
* [Wersje zapoznawcze dotyczące narzędzia NuGet](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
  * Opóźnienie za kompilacje codzienne, ale są testowane do pracy z odpowiednimi wersjami zapoznawczymi ASP.NET Core i .NET Core.

Korzystanie z wersji zapoznawczych lub codziennych kompilacji jest doskonałym sposobem znajdowania problemów i przesyłania opinii tak szybko, jak to możliwe.
Wkrótce otrzymamy taką opinię, tym bardziej prawdopodobnie będzie to możliwe przed następną oficjalną wersją.
