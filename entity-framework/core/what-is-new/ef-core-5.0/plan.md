---
title: Planowanie dla Entity Framework Core 5,0
author: ajcvickers
ms.date: 06/11/2020
uid: core/what-is-new/ef-core-5.0/plan
ms.openlocfilehash: 4abb6f500dce320dd0c32f8f3bf5c529b59fb28b
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526891"
---
# <a name="plan-for-entity-framework-core-50"></a>Planowanie dla Entity Framework Core 5,0

Zgodnie z opisem w [procesie planowania](xref:core/what-is-new/release_planning)dane wejściowe od uczestników projektu zostały zebrane w ramach wstępnego planu dla wydania EF Core 5,0.

> [!IMPORTANT]
> Ten plan jest nadal wykonywany w toku. Nic tutaj jest zobowiązaniem. Ten plan jest punktem początkowym, który będzie się rozwijać, gdy douczymy się więcej. Niektóre elementy, które nie są obecnie planowane dla 5,0, mogą zostać pobrane. Niektóre elementy aktualnie planowane dla 5,0 mogą zostać punted.

## <a name="general-information"></a>Informacje ogólne

### <a name="version-number-and-release-date"></a>Numer wersji i Data wydania

EF Core 5,0 jest obecnie zaplanowane do wydania w ramach [programu .net 5,0](https://devblogs.microsoft.com/dotnet/introducing-net-5/). Wybrano wersję "5,0" do dopasowania z platformą .NET 5,0.

### <a name="supported-platforms"></a>Obsługiwane platformy

EF Core 5,0 jest planowane do uruchamiania na dowolnej platformie .NET 5,0 w oparciu o [zbieżność tych platform w programie .NET Core](https://devblogs.microsoft.com/dotnet/introducing-net-5/). Co to oznacza .NET Standard i rzeczywiste użycie TFM nadal będzie możliwe do ustalenia.

EF Core 5,0 nie będzie działać na .NET Framework.

### <a name="breaking-changes"></a>Zmiany powodujące niezgodność

EF Core 5,0 będzie zawierać pewne istotne [zmiany](xref:core/what-is-new/ef-core-5.0/breaking-changes), ale będą znacznie mniej surowe niż w przypadku EF Core 3,0. Naszym celem jest umożliwienie nieprzerwanego aktualizowania większości aplikacji.

Oczekuje się, że istnieją istotne zmiany dotyczące dostawców baz danych, szczególnie w przypadku pomocy technicznej TPT. Jednak oczekujemy, że aktualizacja dostawcy na 5,0 będzie mniejsza niż wymagana do zaktualizowania dla 3,0.

## <a name="themes"></a>Motywy

Wyodrębnimy kilka głównych obszarów lub motywów, które będą stanowić podstawę dużych inwestycji w EF Core 5,0.

## <a name="fully-transparent-many-to-many-mapping-by-convention"></a>Pełne przezroczyste mapowanie wiele-do-wielu według Konwencji

Potencjalni deweloperzy: @smitpatel , @AndriySvyryd , i@lajones

Śledzone przez [#10508](https://github.com/aspnet/EntityFrameworkCore/issues/10508)

Rozmiar koszulki: L

Stan: w toku

Funkcja wiele-do-wielu jest [najbardziej żądaną funkcją](https://github.com/aspnet/EntityFrameworkCore/issues/1368) (~ 506 głosów) w zaległości usługi GitHub.

Obsługa relacji wiele-do-wielu można podzielić na trzy główne obszary:

* Pomiń właściwości nawigacji — zakryte przez następny motyw.
* Typy jednostek zbioru właściwości. Umożliwiają one użycie standardowego typu CLR (np. `Dictionary` ) dla wystąpień jednostek, w taki sposób, że jawny typ CLR nie jest wymagany dla każdego typu jednostki. Śledzone przez [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914).
* Cukier pozwala na łatwą konfigurację relacji wiele-do-wielu.

Oprócz pomocy technicznej dotyczącej pomijania nawigacji teraz pobieramy te inne obszary z wielu do wielu do EF Core 5,0, aby zapewnić pełne środowisko pracy.

## <a name="many-to-many-navigation-properties-aka-skip-navigations"></a>Właściwości nawigacji wiele-do-wielu (a. k. a "pomijanie nawigacji")

Potencjalni deweloperzy: @smitpatel i@AndriySvyryd

Śledzone przez [#19003](https://github.com/aspnet/EntityFrameworkCore/issues/19003)

Rozmiar koszulki: L

Stan: w toku

Zgodnie z opisem w pierwszym motywie obsługa wiele-do-wielu ma wiele aspektów.
Ten motyw służy do śledzenia użycia nawigacji pomijania.
Uważamy, że najbardziej znaczący blok dla tych, które chcą uzyskać pomoc techniczną wiele-do-wielu, nie ma możliwości używania "naturalnych" relacji bez odwołujących się do tabeli sprzężenia w logice biznesowej, takiej jak zapytania.
Typ jednostki połączonej tabeli może nadal istnieć, ale nie powinien się on znajdować w sposób logiki biznesowej.

## <a name="table-per-type-tpt-inheritance-mapping"></a>Mapowanie dziedziczenia według typu tabeli (TPT)

Deweloper potencjalnego klienta:@AndriySvyryd

Śledzone przez [#2266](https://github.com/aspnet/EntityFrameworkCore/issues/2266)

Rozmiar koszulki: XL

Stan: w toku

Wykonujemy TPT, ponieważ jest to zarówno wysoce żądana funkcja (~ 289 głosów; trzecia ogólna), ponieważ wymaga ona pewnych zmian niskiego poziomu, które są odpowiednie dla ogólnego charakteru planu .NET 5. Oczekujemy, że spowoduje to powstanie istotnych zmian dla dostawców baz danych, chociaż powinny one być znacznie mniej surowe niż zmiany wymagane przez 3,0.

## <a name="filtered-include"></a>Filtr obejmujący

Deweloper potencjalnego klienta:@maumar

Śledzone przez [#1833](https://github.com/aspnet/EntityFrameworkCore/issues/1833)

Rozmiar koszulki: M

Stan: w toku

Filtrowanie include to wysoce żądana funkcja (~ 376 głosów; druga ogólna), która nie jest ogromną ilością pracy, i że firma Microsoft uważa, że nie będzie można zablokować lub ułatwić wielu scenariuszy, które obecnie wymagają filtrów na poziomie modelu lub bardziej złożonych zapytań.

## <a name="split-include"></a>Podziel dołączenie

Deweloper potencjalnego klienta:@smitpatel

Śledzone przez [#20892](https://github.com/dotnet/efcore/issues/20892)

Rozmiar koszulki: L

Stan: w toku

EF Core 3,0 zmieniono zachowanie domyślne w celu utworzenia pojedynczego zapytania SQL dla danego zapytania LINQ.
Spowodowało to duże regresje wydajności dla zapytań, które używają elementu include dla wielu kolekcji.

W EF Core 5,0 zachowywane jest nowe zachowanie domyślne.
Jednak EF Core 5,0 zezwoli teraz na generowanie wielu zapytań dla kolekcji, gdzie istnieje pojedyncze zapytanie powodujące niską wydajność.

## <a name="rationalize-totable-toquery-toview-fromsql-etc"></a>Racjonalizacja ToTable, ToQuery, ToView, Z tabel itp.

Potencjalni deweloperzy: @maumar i@smitpatel

Śledzone przez [#17270](https://github.com/aspnet/EntityFrameworkCore/issues/17270)

Rozmiar koszulki: L

Stan: w toku

Wprowadziliśmy postępy we wcześniejszych wersjach na potrzeby obsługi nieprzetworzonych, typów i niezwiązanych z programem SQL. Istnieją jednak zarówno luki, jak i niespójności w sposób, w jaki wszystko działa razem jako całość. Celem 5,0 jest rozwiązanie tego problemu i utworzenie dobrego środowiska do definiowania, migrowania i korzystania z różnych typów jednostek oraz skojarzonych z nimi zapytań i artefaktów baz danych. Może to również dotyczyć aktualizacji skompilowanego interfejsu API zapytań.

Należy zauważyć, że ten element może spowodować pewne zmiany na poziomie aplikacji, ponieważ niektóre funkcje obecnie są zbyt ograniczane, dzięki czemu mogą szybko prowadzić do Pits awarii. Prawdopodobnie zakończy się blokowanie niektórych z tych funkcji wraz ze wskazówkami dotyczącymi tego, co należy zrobić.

## <a name="general-query-enhancements"></a>Ogólne ulepszenia zapytań

Potencjalni deweloperzy: @smitpatel i@maumar

Śledzone przez [problemy oznaczone za pomocą `area-query` w obszarze kontrolnym 5,0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)

Rozmiar koszulki: XL

Stan: w toku

Kod tłumaczenia zapytania został rozbudowany na EF Core 3,0. W związku z tym kod zapytania jest zazwyczaj bardziej niezawodny. W przypadku 5,0 nie planuje się wprowadzania istotnych zmian zapytania poza tymi, które są potrzebne do obsługi TPT i pomijania właściwości nawigacji. Jednak nadal istnieje znacząca konieczność naprawienia długu technicznego pozostałego w porównaniu z 3,0. Planuje również Rozwiązywanie problemów z wieloma usterkami i zaimplementowanie małych ulepszeń w celu dalszej poprawy ogólnego środowiska zapytań.

## <a name="migrations-and-deployment-experience"></a>Migracje i środowisko wdrażania

Potencjalni deweloperzy:@bricelam

Śledzone przez [#19587](https://github.com/dotnet/efcore/issues/19587)

Rozmiar koszulki: L

Stan: w toku

Obecnie wielu deweloperów migruje swoje bazy danych podczas uruchamiania aplikacji. Jest to proste, ale nie jest zalecane, ponieważ:

* Wiele wątków/procesów/serwerów może próbować przeprowadzić migrację bazy danych współbieżnie
* Aplikacje mogą próbować uzyskać dostęp do niespójnego stanu, gdy jest to wykonywane
* Zwykle uprawnienia bazy danych do modyfikacji schematu nie należy przyznawać do wykonania aplikacji
* Jeśli coś się nie stanie, trudno wrócić do stanu czystego

Chcemy zapewnić lepsze środowisko w tym miejscu, które pozwala na łatwe Migrowanie bazy danych w czasie wdrażania. Powinno to być:

* Pracuj w systemach Linux, Mac i Windows
* Być dobrym doświadczeniem w wierszu polecenia
* Scenariusze pomocy technicznej z kontenerami
* Pracuj z powszechnie używanymi rzeczywistymi narzędziami/przepływami wdrażania
* Integruj do co najmniej programu Visual Studio

Może to być wiele małych ulepszeń w EF Core (na przykład lepszych migracji przy użyciu oprogramowania SQLite), a także wskazówki i średniookresowe współpracę z innymi zespołami w celu ulepszania kompleksowych środowisk, które wykraczają poza prawie Dr.

## <a name="ef-core-platforms-experience"></a>Środowisko EF Core platform

Potencjalni deweloperzy: @roji i@bricelam

Śledzone przez [#19588](https://github.com/dotnet/efcore/issues/19588)

Rozmiar koszulki: L

Stan: nie uruchomiono

Mamy dobre wskazówki dotyczące używania EF Core w tradycyjnych aplikacjach internetowych opartych na standardzie MVC. Brak wskazówek dotyczących innych platform i modeli aplikacji albo są one nieaktualne. W przypadku EF Core 5,0 planujemy zbadać, ulepszyć i udokumentować środowisko korzystania z EF Core z:

* Blazor
* Środowisko Xamarin, w tym używanie scenariusza AOT/konsolidatora
* WinForms/WPF/WinUI i prawdopodobnie inne U.I. platform

Jest to prawdopodobnie wiele małych ulepszeń w EF Core, wraz ze wskazówkami i dłuższymi współpracy z innymi zespołami, aby ulepszyć kompleksowe środowiska, które wykraczają poza prawie Dr.

Określone obszary, które planujemy sprawdzić, to:

* Wdrożenie, w tym środowisko do korzystania z narzędzi EF, takich jak migracja
* Modele aplikacji, w tym Xamarin i Blazor, a prawdopodobnie inne
* Środowiska oprogramowania SQLite, w tym środowisko przestrzenne i ponowne kompilacje tabel
* AOT i łącza — środowisko
* Integracja diagnostyki, w tym liczników wydajności

## <a name="performance"></a>Wydajność

Deweloper potencjalnego klienta:@roji

Śledzone przez [problemy oznaczone za pomocą `area-perf` w obszarze kontrolnym 5,0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)

Rozmiar koszulki: L

Stan: w toku

W przypadku EF Core planujemy udoskonalić nasz pakiet testów wydajnościowych i wprowadzić ukierunkowane ulepszenia wydajności środowiska uruchomieniowego. Ponadto planujemy ukończenie nowego interfejsu API tworzenia wsadowego ADO.NET, który został utworzony w ramach cyklu wydania 3,0. Ponadto w warstwie ADO.NET planujemy dodatkowe ulepszenia wydajności dla dostawcy Npgsql.

W ramach tej pracy zaplanowano również dodanie ADO.NET/EF podstawowych liczników wydajności i innych elementów diagnostycznych odpowiednio do potrzeb.

## <a name="architecturalcontributor-documentation"></a>Dokumentacja architektury/współautora

Dokument potencjalnego klienta:@ajcvickers

Śledzone przez [#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920)

Rozmiar koszulki: L

Stan: Wytnij

Tutaj warto ułatwić zrozumienie, co się dzieje w wewnętrznych EF Core. Może to być przydatne dla każdej osoby korzystającej z EF Core, ale podstawową motywacją jest ułatwienie użytkownikom zewnętrznym:

* Współtworzenie kodu EF Core
* Tworzenie dostawców baz danych
* Kompiluj inne rozszerzenia

Aktualizacja: Niestety, ten plan był zbyt ambitne podejście.
Nadal uważamy, że jest to ważne, ale nie będzie to miało wpływu na EF Core 5,0.

## <a name="microsoftdatasqlite-documentation"></a>Dokumentacja Microsoft. Data. sqlite

Dokument potencjalnego klienta:@bricelam

Śledzone przez [#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675)

Rozmiar koszulki: M

Stan: ukończono. Nowa dokumentacja znajduje się [na żywo w witrynie Microsoft docs](/dotnet/standard/data/sqlite/?tabs=netcore-cli).

Zespół EF również jest właścicielem dostawcy ADO.NET Microsoft. Data. sqlite. Planujemy w pełni udokumentować tego dostawcę w wersji 5,0.

## <a name="general-documentation"></a>Ogólna dokumentacja

Dokument potencjalnego klienta:@ajcvickers

Śledzone przez [problemy w repozytorium docs w obszarze kontrolnym 5,0](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)

Rozmiar koszulki: L

Stan: w toku

Już trwa proces aktualizacji dokumentacji dla wersji 3,0 i 3,1. Pracujemy również nad:

* Remontowanie dokumentów wprowadzających wprowadzenie, aby zwiększyć ich podejście/łatwiejsze w obserwowanie
* Reorganizacja dokumentów, aby ułatwić znajdowanie i Dodawanie odsyłaczy
* Dodawanie dalszych szczegółów i wyjaśnień do istniejących dokumentów
* Aktualizowanie przykładów i Dodawanie kolejnych przykładów

## <a name="fixing-bugs"></a>Naprawianie usterek

Śledzone przez [problemy oznaczone za pomocą `type-bug` w obszarze kontrolnym 5,0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)

Deweloperzy: @roji , @maumar ,,, @bricelam @smitpatel @AndriySvyryd ,@ajcvickers

Rozmiar koszulki: L

Stan: w toku

W momencie pisania mamy 135 błędów zaklasyfikowany w wersji 5,0 (z 62 już Naprawiono), ale istnieje duże nakładanie się na sekcję _Ogólne ulepszenia zapytania_ .

Szybkość odbierania (problemy, które kończą się w trakcie pracy w kontrolce), dotyczyła 23 problemów miesięcznie w trakcie wydania 3,0. Nie wszystkie z nich będą musiały zostać naprawione w 5,0. Jako przybliżony szacunek planujemy rozwiązać dodatkowe 150 problemów w przedziale czasowym 5,0.

## <a name="small-enhancements"></a>Małe ulepszenia

Śledzone przez [problemy oznaczone za pomocą `type-enhancement` w obszarze kontrolnym 5,0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)

Deweloperzy: @roji , @maumar ,,, @bricelam @smitpatel @AndriySvyryd ,@ajcvickers

Rozmiar koszulki: L

Stan: w toku

Oprócz większych funkcji opisanych powyżej, firma Microsoft oferuje również wiele mniejszych ulepszeń zaplanowanych na 5,0, aby naprawić "kawałki papieru". Należy zauważyć, że wiele z tych ulepszeń jest również objętych bardziej ogólnymi motywami opisanymi powyżej.

## <a name="below-the-line"></a>Poniżej — wiersz

Śledzone przez [problemy oznaczone przy `consider-for-next-release` użyciu](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)

Są to poprawki i udoskonalenia błędów, które **nie** są obecnie planowane dla wydania 5,0, ale będziemy przebiegać zgodnie z przeznaczeniem do rozciągnięcia w zależności od postępu wykonywanego powyżej.

Ponadto zawsze należy wziąć pod uwagę [najczęstsze problemy związane](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) z planowaniem. Wszystkie te problemy w wersji są zawsze bolesnym, ale potrzebujemy realistycznego planu dla zasobów, które mamy.

## <a name="suggestions"></a>Sugestie

Twoja opinia na temat planowania jest ważna. Najlepszym sposobem na wskazanie znaczenia problemu jest zagłosowanie (kciuki) dla tego problemu w serwisie GitHub. Te dane zostaną następnie [przetworzone do procesu planowania](xref:core/what-is-new/release_planning) dla kolejnej wersji.
