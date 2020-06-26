---
title: Planowanie EF Core wydania
author: ajcvickers
ms.date: 01/28/2020
uid: core/what-is-new/release_planning
ms.openlocfilehash: df933ac2462fcc18c53f49d862836fd2d6a4dd99
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370191"
---
# <a name="release-planning-process"></a>Proces planowania wersji

Często otrzymujemy pytania dotyczące sposobu wybierania określonych funkcji, aby przejść do określonej wersji.
W tym dokumencie opisano używany przez nas proces.
Proces ciągle się zmienia, gdy znajdziesz lepsze metody do zaplanowania, ale ogólne koncepcje pozostają bez zmian.

## <a name="different-kinds-of-releases"></a>Różne rodzaje wydań

Różne rodzaje wersji zawierają różne rodzaje zmian.
To z kolei oznacza, że planowanie wydania jest różne dla różnych rodzajów wydania.

### <a name="patch-releases"></a>Wersje poprawek

Wersje poprawek zmieniają tylko część "Poprawka" wersji.
Na przykład EF Core 3,1. **1** to wersja, która zawiera poprawki dotyczące problemów występujących w EF Core 3,1. **0**.

Wersje poprawek mają na celu rozwiązanie krytycznych usterek klientów.
Oznacza to, że wersje poprawek nie zawierają nowych funkcji.
Zmiany interfejsu API są niedozwolone w wersjach poprawek, z wyjątkiem sytuacji szczególnych.

Wprowadzenie zmian w wersji poprawki jest bardzo duże.
Wynika to z faktu, że wersje poprawek nie wprowadzają nowych błędów.
W związku z tym proces decyzyjny wyróżnia wysoką wartość i niskie ryzyko.

Więcej informacji o problemie można poprawić, jeśli:
  * Ma to wpływ na wielu klientów
  * Jest to regresja z poprzedniej wersji
  * Awaria powoduje uszkodzenie danych

W przypadku, gdy:
  * Istnieją uzasadnione obejścia
  * Poprawka ma duże ryzyko związane z rozdzieleniem czegoś innego
  * Usterka jest w przypadku rogu

Ten pasek stopniowo rośnie przez okres istnienia [długoterminowej pomocy technicznej (LTS)](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) . Wynika to z faktu, że wydania LTS podkreślają stabilność.

Ostateczna decyzja dotycząca tego, czy w firmie Microsoft ma być podejmowana poprawka, czy nie.

### <a name="minor-releases"></a>Wersje pomocnicze

Wersje pomocnicze zmieniają tylko część "pomocnicze" wersji.
Na przykład EF Core 3. **1**0 to wersja, która usprawnia EF Core 3. **0**. 0.

Wersje pomocnicze:
* Mają na celu poprawę jakości i funkcji poprzedniej wersji
* Zwykle zawierają poprawki błędów i nowe funkcje
* Nie uwzględniaj zamierzonych zmian
* Zawiera kilka wersji wstępnych programu do dystrybucji wypychanych do narzędzia NuGet

### <a name="major-releases"></a>Wersje główne

Wersje główne zmieniają "główny" numer wersji.
Na przykład EF Core **3**. 0,0 to główna wersja, która umożliwia przekazanie dużego kroku do EF Core 2.2. x.

Wersje główne:
* Mają na celu poprawę jakości i funkcji poprzedniej wersji
* Zwykle zawierają poprawki błędów i nowe funkcje
  * Niektóre nowe funkcje mogą być fundamentalnymi zmianami w sposób, w jaki EF Core działa
* Zwykle obejmują zamierzone istotne zmiany
  * Istotne zmiany są niezbędne części rozwijających się EF Core w miarę zdobywania
  * Należy jednak pamiętać o wprowadzeniu wszelkich istotnych zmian z powodu potencjalnego wpływu na klienta. Firma Microsoft mogła być zbyt agresywna dzięki nieprzerwanym zmianom. W przyszłości będziemy dążyć do zminimalizowania zmian, które powodują przerwanie stosowania aplikacji, i zmniejszenia zmian, które powodują przerwanie dostawców i rozszerzeń bazy danych.
* Wiele podglądów wersji wstępnej wypychanych do narzędzia NuGet

## <a name="planning-for-majorminor-releases"></a>Planowanie wersji głównych/pomocniczych

### <a name="github-issue-tracking"></a>Śledzenie problemów GitHub

GitHub ( [https://github.com/dotnet/efcore](https://github.com/dotnet/efcore) ) to źródło prawdy dla wszystkich EF Core planowania.

Problemy z usługą GitHub:

* Stan
  * Nie zostały rozwiązane [problemy.](https://github.com/dotnet/efcore/issues)
  * Rozwiązano [zamknięte](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aclosed) problemy.
    * Wszystkie problemy, które zostały naprawione, są [oznaczone jako zamknięte](https://github.com/dotnet/efcore/issues?q=is%3Aissue+label%3Aclosed-fixed+is%3Aclosed). Problem oznaczony jako zamknięty — został usunięty i scalony, ale mógł nie zostać opublikowany.
    * Inne `closed-` etykiety wskazują inne przyczyny zamknięcia problemu. Na przykład duplikaty są otagowane z zamkniętym duplikatem.
* Typ
  * [Usterki](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+label%3Atype-bug) reprezentują błędy.
  * [Ulepszenia](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+label%3Atype-enhancement) przedstawiają nowe funkcje lub lepszą funkcjonalność w istniejących funkcjach.
* Punkt kontrolny
  * [Problemy z punktem kontrolnym nie](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+no%3Amilestone) są rozpatrywane przez zespół. Decyzja o tym, co należy zrobić z problemem, nie została jeszcze zrealizowana lub decyzja o zmianie jest rozpatrywana.
  * [Problemy w punkcie kontrolnym zaległości](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3ABacklog) to elementy, które zespół EF rozważy nad pracą w przyszłej wersji
    * Problemy w zaległościach można [oznaczyć przy użyciu pozycji Rozważ-for-Next-Release](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+label%3Aconsider-for-next-release) , co oznacza, że ten element roboczy jest wysoki na liście dla kolejnej wersji.
  * Otwarte problemy w punkcie kontrolnym z wersją są elementami, w których zespół planuje prace nad tą wersją. Na przykład [są to problemy, nad którymi planujemy obejść EF Core 5,0](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A5.0.0).
  * Zamknięte problemy w punktów kontrolnych z uruchomioną wersją są problemami, które zostały ukończone dla tej wersji. Należy zauważyć, że wersja mogła jeszcze nie zostać wydana. Na przykład [są to problemy wykonane dla EF Core 3,0](https://github.com/dotnet/efcore/issues?q=is%3Aissue+milestone%3A3.0.0+is%3Aclosed).
* Dane!
  * Głosowanie to najlepszy sposób, aby wskazać, że problem jest dla Ciebie ważny.
  * Aby głosować, po prostu Dodaj "kciuka" 👍 do problemu. Na przykład [są to problemy z najważniejszym głosem](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc)
  * Prosimy również o podanie określonych powodów, dla których będzie potrzebna ta funkcja. Komentowanie "+ 1" lub podobne nie powoduje dodania wartości.

### <a name="the-planning-process"></a>Proces planowania

Proces planowania jest większy niż w przypadku przejmowania najbardziej żądanych funkcji z listy prac.
Wynika to z tego, że zbieramy Opinie z wielu uczestników na wiele sposobów.
Następnie kształtuje się wydanie w oparciu o:

* Dane wejściowe od klientów
* Dane wejściowe z innych uczestników projektu
* Kierunek strategiczny
* Dostępne zasoby
* Zaplanuj

Oto niektóre pytania:

1. **Ilu deweloperów będziemy używać tej funkcji i ile lepiej przeprowadzi swoje aplikacje lub środowisko?** Aby odpowiedzieć na to pytanie, zbieramy Opinie z wielu źródeł — Komentarze i głosy dotyczące problemów są jednym z tych źródeł. Określone zaangażowanie z ważnymi klientami jest inne.

2. **Jakie obejścia mogą być używane w przypadku, gdy ta funkcja nie została jeszcze wdrożona?** Na przykład wielu deweloperów może zamapować tabelę sprzężenia, aby obejść brak natywnej obsługi wiele-do-wielu. Oczywiście nie wszyscy deweloperzy chcą tego robić, ale wiele z nich może, a także liczyć jako czynnik w naszej decyzji.

3. **Czy zaimplementowanie tej funkcji spowoduje rozwój architektury EF Core w taki sposób, aby przeniesieł nam bliższe wdrożenie innych funkcji?** Chcemy preferować funkcje, które pełnią rolę bloków konstrukcyjnych dla innych funkcji. Na przykład jednostki zbioru właściwości mogą pomóc nam w przeniesieniu do pomocy technicznej wiele-do-wielu, a konstruktory jednostek włączyli obsługę ładowania z opóźnieniem.

4. **Czy funkcja jest punktem rozszerzalności?** Chcemy preferować punkty rozszerzalności w porównaniu z normalnymi funkcjami, ponieważ umożliwiają deweloperom Podłączanie własnych zachowań i kompensowanie wszelkich brakujących funkcji.

5. **Jaka jest synergia funkcji, gdy jest używana w połączeniu z innymi produktami?** Firma Microsoft oferuje funkcje, które umożliwiają lub znacząco ulepszają środowisko korzystania z EF Core z innymi produktami, takimi jak .NET Core, Najnowsza wersja programu Visual Studio, Microsoft Azure i tak dalej.

6. **Jakie są umiejętności osób, które mogą korzystać z funkcji i jak najlepiej wykorzystać te zasoby?** Każdy członek zespołu EF i współautorzy społeczności mają różne poziomy doświadczenia w różnych obszarach, dlatego musimy odpowiednio zaplanować. Nawet jeśli chcemy mieć "wszystkie ręce na pokładzie", aby działać na określonej funkcji, takiej jak tłumaczenia GroupBy lub wiele-do-wielu, które nie były praktyczne.
