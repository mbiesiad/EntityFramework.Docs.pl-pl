---
title: Migracje Code First w środowiskach zespołów — EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 4c2d9a95-de6f-4e97-9738-c1f8043eff69
ms.openlocfilehash: 46c22c997b8db53bb381d0b528c65733f1766849
ms.sourcegitcommit: 387cbd8109c0fc5ce6bdc85d0dec1aed72ad4c33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82103168"
---
# <a name="code-first-migrations-in-team-environments"></a>Migracje Code First w środowiskach zespołu
> [!NOTE]
> W tym artykule założono, że wiesz, jak używać Migracje Code First w podstawowych scenariuszach. Jeśli tego nie zrobisz, musisz przeczytać [migracje Code First](~/ef6/modeling/code-first/migrations/index.md) przed kontynuowaniem.

## <a name="grab-a-coffee-you-need-to-read-this-whole-article"></a>Weź pod nich kawę, musisz przeczytać ten cały artykuł

Problemy w środowiskach zespołu polegają głównie na scalaniu migracji, gdy dwaj deweloperzy wygenerowały migracje w lokalnej bazie kodu. Chociaż kroki, które należy rozwiązać, są bardzo proste, wymagają one pełnego poznania sposobu działania migracji. Nie przeskoczę do końca — Poświęć chwilę na zapoznanie się z całym artykułem, aby upewnić się, że powiodło się.

## <a name="some-general-guidelines"></a>Niektóre ogólne wytyczne

Zanim Dig się na zarządzanie scalanymi migracjami wygenerowanymi przez wielu deweloperów, poniżej przedstawiono niektóre ogólne wskazówki dotyczące konfigurowania sukcesu.

### <a name="each-team-member-should-have-a-local-development-database"></a>Każdy członek zespołu powinien mieć lokalną bazę danych programistycznych

Migracja korzysta ** \_ \_** z tabeli MigrationsHistory do przechowywania migracji, które zostały zastosowane do bazy danych programu. Jeśli masz wielu deweloperów generujących różne migracje przy próbie docelowej tej samej bazy danych (i w ** \_ \_** ten sposób udostępnianie tabeli MigrationsHistory), migracje są bardzo mylone.

Oczywiście, jeśli masz członków zespołu, którzy nie generują migracji, nie ma żadnego problemu, aby udostępnić centralną bazę danych programistycznych.

### <a name="avoid-automatic-migrations"></a>Unikaj automatycznych migracji

Dolna linia polega na pierwszym wyszukiwaniu automatycznych migracji w środowiskach zespołu, ale w rzeczywistości nie działają. Jeśli chcesz dowiedzieć się, dlaczego należy czytać — Jeśli nie, możesz przejść do następnej sekcji.

Automatyczne migracje umożliwiają zaktualizowanie schematu bazy danych w taki sposób, aby był zgodny z bieżącym modelem bez konieczności generowania plików kodu (migracje oparte na kodzie). Migracje automatyczne działają bardzo dobrze w środowisku zespołu, jeśli kiedykolwiek były używane, i nigdy nie generują żadnych migracji opartych na kodzie. Problem polega na tym, że automatyczne migracje są ograniczone i nie obsługują wielu operacji — zmiana nazw właściwości/kolumn, przenoszenie danych do innej tabeli itp. Aby obsłużyć te scenariusze, można wykonać generowanie migracji opartych na kodzie (i edytowanie kodu szkieletowego), które są mieszane między zmianami, które są obsługiwane przez migracje automatyczne. Dzięki temu nie można scalić zmian, gdy dwaj deweloperzy zaewidencjonują migracje.

## <a name="screencasts"></a>Screencasts

Jeśli wolisz obejrzeć zrzut ekranu przedstawiający niż odczytanie tego artykułu, następujące dwa wideo obejmują tę samą zawartość co ten artykuł.

### <a name="video-one-migrations---under-the-hood"></a>Wideo one: "migracje — pod okapem"

[Ten zrzut ekranu przedstawiający](https://channel9.msdn.com/blogs/ef/migrations-under-the-hood) dotyczy sposobu, w jaki migracja śledzi i używa informacji o modelu do wykrywania zmian modelu.

### <a name="video-two-migrations---team-environments"></a>Wideo dwa: "migracje-środowiska zespołu"

Na podstawie koncepcji z poprzedniego wideo [Ten zrzut ekranu przedstawiający](https://channel9.msdn.com/blogs/ef/migrations-team-environments) obejmuje problemy, które pojawiają się w środowisku zespołowym i sposoby ich rozwiązywania.

## <a name="understanding-how-migrations-works"></a>Zrozumienie, jak działa migracja

Kluczem do pomyślnego użycia migracji w środowisku zespołowym jest podstawowe zrozumienie, jak migracja śledzi i używa informacji o modelu do wykrywania zmian modelu.

### <a name="the-first-migration"></a>Pierwsza migracja

Po dodaniu pierwszej migracji do projektu, w konsoli Menedżera pakietów jest uruchamiany element, taki jak **Dodaj migrację** . Poniżej przedstawiono kroki wysokiego poziomu wykonywane przez to polecenie.

![Pierwsza migracja](~/ef6/media/firstmigration.png)

Bieżący model jest obliczany na podstawie kodu (1). Wymagane obiekty bazy danych są następnie obliczane przez model różnią się (2) — ponieważ jest to pierwsza migracja, model różni się tylko za pomocą pustego modelu do porównania. Wymagane zmiany są przesyłane do generatora kodu w celu utworzenia wymaganego kodu migracji (3), który jest następnie dodawany do rozwiązania programu Visual Studio (4).

Oprócz faktycznego kodu migracji, który jest przechowywany w pliku kodu głównego, migracja również generuje kilka dodatkowych plików powiązanych z kodem. Te pliki są metadanymi, które są używane przez migracje i nie są coś, co należy edytować. Jeden z tych plików jest plikiem zasobów (. resx) zawierającym migawkę modelu w momencie wygenerowania migracji. Zobaczysz, jak to jest używane w następnym kroku.

W tym momencie prawdopodobnie chcesz uruchomić polecenie **Update-Database** , aby zastosować zmiany do bazy danych, a następnie przejdź do implementacji innych obszarów aplikacji.

### <a name="subsequent-migrations"></a>Kolejne migracje

Później powrócisz i wprowadzisz pewne zmiany do modelu — w naszym przykładzie dodamy do **blogu**Właściwość **adresu URL** . Następnie można wydać polecenie, takie jak **dodanie AddUrl migracji** , aby przeprowadzić szkielet migracji w celu zastosowania odpowiednich zmian w bazie danych. Poniżej przedstawiono kroki wysokiego poziomu wykonywane przez to polecenie.

![Druga migracja](~/ef6/media/secondmigration.png)

Podobnie jak w przypadku ostatniego czasu bieżący model jest obliczany na podstawie kodu (1). Jednak w tej chwili istnieją migracje, dlatego poprzedni model zostanie pobrany z najnowszej migracji (2). Te dwa modele są różnicowane w celu znalezienia wymaganych zmian w bazie danych (3), a następnie proces kończy się tak jak wcześniej.

Ten sam proces jest używany w przypadku wszelkich dalszych migracji dodawanych do projektu.

### <a name="why-bother-with-the-model-snapshot"></a>Dlaczego bother z migawką modelu?

Może być zastanawiasz się, dlaczego EF obydwa z migawek modelu — dlaczego nie tylko Przyjrzyj się bazie danych. Jeśli tak, przeczytaj. Jeśli nie jesteś zainteresowany, możesz pominąć tę sekcję.

Istnieje wiele powodów, w których zachowanie migawek modelu jest zachowywane:

-   Umożliwia ona przedryfowanie bazy danych z modelu EF. Te zmiany można wprowadzać bezpośrednio w bazie danych programu lub można zmienić kod szkieletowy w migracjach, aby wprowadzić zmiany. Poniżej przedstawiono kilka przykładów tego zalecenia:
    -   Chcesz dodać wstawioną i zaktualizowaną kolumnę do co najmniej jednej tabeli, ale nie chcesz uwzględniać tych kolumn w modelu EF. Jeśli podczas migracji oglądana jest baza danych, będzie ona stale próbować porzucić te kolumny przy każdym utworzeniu szkieletu migracji. Przy użyciu migawki modelu EF będzie wykrywać tylko te same zmiany w modelu.
    -   Należy zmienić treść procedury składowanej używanej do aktualizacji w celu uwzględnienia niektórych dzienników. Jeśli migracje przeszukiwane w ramach tej procedury składowanej pochodzą z bazy danych, będzie ona stale próbować i resetować ją z powrotem do definicji, która jest oczekiwana przez EF. Przy użyciu migawki modelu EF tylko kiedykolwiek kod szkieletu, aby zmienić procedurę składowaną w przypadku zmiany kształtu procedury w modelu EF.
    -   Te same zasady mają zastosowanie do dodawania dodatkowych indeksów, w tym dodatkowych tabel w bazie danych, mapowanie EF do widoku bazy danych, który znajduje się na tabeli itd.
-   Model EF zawiera więcej niż tylko kształt bazy danych. Cały model umożliwia migrowanie informacji o właściwościach i klasach w modelu oraz sposobie ich mapowania do kolumn i tabel. Te informacje umożliwiają bardziej inteligentne migracje w kodzie, który szkieletuje. Na przykład jeśli zmienisz nazwę kolumny, którą Właściwość mapuje na migracje, może wykryć zmianę nazwy, sprawdzając, czy jest to taka sama Właściwość — co nie można zrobić, jeśli masz tylko schemat bazy danych. 

## <a name="what-causes-issues-in-team-environments"></a>Co powoduje problemy w środowiskach zespołu

Przepływ pracy objęty poprzednią sekcją działa dobrze, gdy jesteś pojedynczym deweloperem pracującym nad aplikacją. Dobrze działa również w środowisku zespołu, jeśli jesteś jedyną osobą wprowadzającą zmiany w modelu. W tym scenariuszu można wprowadzać zmiany modelu, generować migracje i przesyłać je do kontroli źródła. Inni deweloperzy mogą synchronizować zmiany i uruchamiać **aktualizacje-Database** , aby zmiany schematu zostały zastosowane.

Problemy zaczynają się w sytuacji, gdy wielu deweloperów wprowadza zmiany w modelu EF i jednocześnie przesyła do kontroli źródła. Brak elementów EF to pierwszy sposób scalania migracji lokalnych z migracjami, które inny deweloper przesłał do kontroli źródła od momentu ostatniej synchronizacji.

## <a name="an-example-of-a-merge-conflict"></a>Przykład konfliktu scalania

Najpierw przyjrzyjmy się konkretnemu przykładowi konfliktu scalania. Będziemy dalej korzystać z przykładu, który oglądamy wcześniej. Jako punkt początkowy Załóżmy, że zmiany z poprzedniej sekcji zostały zaewidencjonowane przez oryginalnego dewelopera. Będziemy śledzić dwóch deweloperów w miarę wprowadzania zmian w bazie kodu.

Będziemy śledzić model EF i migracje przez wiele zmian. W przypadku punktu początkowego obydwie deweloperzy zostały zsynchronizowane z repozytorium kontroli źródła, jak przedstawiono na poniższej ilustracji.

![Punkt początkowy](~/ef6/media/startingpoint.png)

Deweloperzy \#1 i Developer \#2 wprowadzają teraz pewne zmiany w modelu EF w ich lokalnej bazie kodu. Deweloper \#1 dodaje do **blogu** Właściwość **oceny** — i generuje migrację **addrating** , aby zastosować zmiany do bazy danych. Deweloper \#2 dodaje właściwość **czytelnicy** do **blogu** — i generuje odpowiednie migracje **addreader** . Obaj deweloperzy uruchamiają **aktualizację bazy danych**, aby zastosować zmiany do ich lokalnych baz danych, a następnie kontynuować opracowywanie aplikacji.

> [!NOTE]
> Migracje są poprzedzone sygnaturą czasową, więc nasza ilustracja przedstawia, że migracja addreader z \#dewelopera 2 jest dostępna po migracji addrating \#z dewelopera 1. Niezależnie od \#tego, \#czy deweloper 1 lub 2 wygenerował proces migracji, nie ma żadnego wpływu na problemy związane z pracą w zespole ani procesu scalania, który przeprowadzimy w następnej sekcji.

![Zmiany lokalne](~/ef6/media/localchanges.png)

Jest to cieszymy dzień dla deweloperów \#1, gdy nastąpiła najpierw przesłanie zmian. Ponieważ nikt inny nie zaewidencjonuje się, ponieważ synchronizuje swoje repozytorium, może po prostu przesłać zmiany bez wykonywania scalania.

![Submit](~/ef6/media/submit.png)

Teraz czas na przesłanie dewelopera \#2. Nie cieszymy. Ponieważ ktoś inny przesłał zmiany od czasu ich synchronizacji, będzie musiał ściągnąć zmiany i scalić. System kontroli źródła prawdopodobnie będzie mógł automatycznie scalić zmiany na poziomie kodu, ponieważ są one bardzo proste. Stan repozytorium lokalnego dewelopera \#2 po synchronizacji przedstawiono na poniższej ilustracji. 

![Ściąganie](~/ef6/media/pull.png)

Na tym etapie deweloper \#2 może uruchomić program **Update-Database** , który wykryje nową migrację **addrating** (która nie została zastosowana do bazy danych dewelopera \#2) i zastosuje ją. Teraz kolumna **Rating** zostanie dodana do tabeli **Blogi** , a baza danych jest zsynchronizowana z modelem.

Istnieje kilka problemów, chociaż:

1.  Mimo że **Aktualizacja bazy danych** zostanie zastosowana do migracji **addrating** , zostanie również zgłoszone ostrzeżenie: *nie można zaktualizować bazy danych tak, aby była zgodna z bieżącym modelem, ponieważ istnieją oczekujące zmiany i automatyczna migracja jest wyłączona...*
    Problem polega na tym, że migawka modelu przechowywana w ostatniej migracji (**Addreader**) nie zawiera właściwości **Rating** w **blogu** (ponieważ nie była częścią modelu podczas generowania migracji). Code First wykryje, że model w ostatniej migracji nie jest zgodny z bieżącym modelem i generuje ostrzeżenie.
2.  Uruchomienie aplikacji spowoduje, że zostanie InvalidOperationException, że "*model z kopią zapasową kontekstu" BloggingContext "został zmieniony od czasu utworzenia bazy danych. Rozważ użycie Migracje Code First do zaktualizowania bazy danych... "*
    Problem polega na tym, że migawka modelu przechowywana w ostatniej migracji nie jest zgodna z bieżącym modelem.
3.  Na koniec oczekujemy, że operacja **Add-Migration** spowoduje wygenerowanie pustej migracji (ponieważ nie ma zmian do zastosowania do bazy danych). Jednak ponieważ migracja porównuje bieżący model z tą z ostatniej migracji (w której brakuje właściwości **Rating** ), w rzeczywistości będzie szkieletować inne wywołanie **addColumn** do dodania w kolumnie **Rating** . Oczywiście ta migracja nie powiedzie się podczas **aktualizacji bazy danych** , ponieważ kolumna **klasyfikacji** już istnieje.

## <a name="resolving-the-merge-conflict"></a>Rozwiązywanie konfliktu scalania

Dobrą wiedzą, że nie jest zbyt trudne do rozdzielenia z scalaniem ręcznie — pod warunkiem, że wiesz, jak działa migracja. Dlatego jeśli pominięto w tej sekcji... Niestety, musisz najpierw wrócić do pozostałej części artykułu.

Dostępne są dwie opcje, najłatwiej jest wygenerować pustą migrację, która ma prawidłowy bieżący model jako migawkę. Druga opcja polega na aktualizacji migawki w ostatniej migracji w celu uzyskania poprawnej migawki modelu. Druga opcja jest nieco trudniejsza i nie może być używana w każdym scenariuszu, ale jest również przejrzysta, ponieważ nie obejmuje dodawania dodatkowej migracji.

### <a name="option-1-add-a-blank-merge-migration"></a>Opcja 1: Dodanie pustej migracji "merge"

W tej opcji wygenerujemy pustą migrację wyłącznie na potrzeby upewnienia się, że w ramach najnowszej migracji Zapisano poprawną migawkę modelu.

Tej opcji można użyć niezależnie od tego, kto wygenerował ostatnią migrację. W przykładzie, który został uruchomiony przez dewelopera \#2, zajmiemy się scalaniem i wystąpiły one w celu wygenerowania ostatniej migracji. Jednak te same kroki mogą być używane, jeśli \#deweloper 1 wygenerował ostatnią migrację. Te kroki mają zastosowanie również w przypadku istnienia wielu migracji, w celu ich prostego przeprowadzenia.

Następujący proces może służyć do tego podejścia, od momentu wprowadzenia zmian, które muszą zostać zsynchronizowane z kontroli źródła.

1.  Upewnij się, że wszystkie oczekujące zmiany modelu w lokalnej bazie kodu zostały zapisaną do migracji. Ten krok gwarantuje, że nie zostaną pominięte żadne uzasadnione zmiany, gdy nadejdzie czas na wygenerowanie pustej migracji.
2.  Synchronizuj z kontrolą źródła.
3.  Uruchom opcję **Update-Database** , aby zastosować nowe migracje, które zostały zaewidencjonowane przez innych deweloperów.
    **_Uwaga:_** *Jeśli nie otrzymasz żadnych ostrzeżeń z polecenia Update-Database, nie było żadnych nowych migracji od innych deweloperów i nie ma potrzeby wykonywania dalszych scalania.*
4.  Uruchom polecenie **Add- &lt;Migration\_wybierz\_nazwę&gt; – IgnoreChanges** (na przykład polecenie **Add-Migration Merge – IgnoreChanges**). Spowoduje to wygenerowanie migracji ze wszystkimi metadanymi (łącznie z migawką bieżącego modelu), ale zignoruje wszelkie zmiany, które wykryje podczas porównywania bieżącego modelu z migawką w ostatniej migracji (co oznacza, że można uzyskać pustą metodę w **górę** i **w dół** ).
5.  Uruchom **aktualizację-Database** , aby ponownie zastosować najnowszą migrację ze zaktualizowanymi metadanymi.
6.  Kontynuuj opracowywanie lub Prześlij do kontroli źródła (po uruchomieniu testów jednostkowych kursu).

Poniżej znajduje się stan lokalnej bazy \#kodu dewelopera 2 po użyciu tego podejścia.

![Scalanie migracji](~/ef6/media/mergemigration.png)

### <a name="option-2-update-the-model-snapshot-in-the-last-migration"></a>Opcja 2: aktualizowanie migawki modelu w ostatniej migracji

Ta opcja jest bardzo podobna do opcji 1, ale eliminuje dodatkową migrację pustą — ponieważ będzie ona zależeć od tego, kto chce uzyskać dodatkowe pliki kodu w rozwiązaniu.

**Takie podejście jest możliwe tylko wtedy, gdy Najnowsza migracja istnieje tylko w bazie kodu lokalnego i nie została jeszcze przesłana do kontroli źródła (na przykład jeśli Ostatnia migracja została wygenerowana przez użytkownika wykonującego scalanie)**. Edytowanie metadanych migracji, które mogą już być stosowane przez innych deweloperów, lub nawet gorszenia zastosowania do produkcyjnej bazy danych — może spowodować nieoczekiwane skutki uboczne. Podczas procesu wycofywanie ostatniej migracji z lokalnej bazy danych i ponowne zastosowanie jej przy użyciu zaktualizowanych metadanych.

Podczas gdy Ostatnia migracja musi znajdować się w bazie kodu lokalnego, nie ma żadnych ograniczeń dotyczących liczby lub kolejności migracji, które go przechodzą. Może istnieć wiele migracji od wielu różnych deweloperów i te same kroki — przeszukamy dwie, aby zachować prostotę.

Następujący proces może służyć do tego podejścia, od momentu wprowadzenia zmian, które muszą zostać zsynchronizowane z kontroli źródła.

1.  Upewnij się, że wszystkie oczekujące zmiany modelu w lokalnej bazie kodu zostały zapisaną do migracji. Ten krok gwarantuje, że nie zostaną pominięte żadne uzasadnione zmiany, gdy nadejdzie czas na wygenerowanie pustej migracji.
2.  Synchronizuj z kontrolą źródła.
3.  Uruchom opcję **Update-Database** , aby zastosować nowe migracje, które zostały zaewidencjonowane przez innych deweloperów.
    **_Uwaga:_** *Jeśli nie otrzymasz żadnych ostrzeżeń z polecenia Update-Database, nie było żadnych nowych migracji od innych deweloperów i nie ma potrzeby wykonywania dalszych scalania.*
4.  Uruchom **Update-Database – TargetMigration &lt;sekundę\_Ostatnia\_migracja&gt; ** (w przykładzie poniżej zostałaby to **Aktualizacja-Database – TargetMigration addrating**). Powoduje to, że baza danych jest przywracana do stanu drugiej ostatniej migracji — w praktyce "cofnięto stosowanie" ostatniej migracji z bazy danych.
    **_Uwaga:_** *ten krok jest wymagany, aby można było bezpiecznie edytować metadane migracji, ponieważ metadane są również przechowywane w \_ \_MigrationsHistoryTable bazy danych. Dlatego należy używać tej opcji tylko wtedy, gdy Ostatnia migracja jest tylko w lokalnej bazie kodu. Jeśli podczas ostatniej zastosowanej migracji istnieją inne bazy danych, należy również ponownie je wycofać i zastosować ostatniej migracji w celu zaktualizowania metadanych.* 
5.  Uruchom **pełną &lt;\_nazwę\_migracji z uwzględnieniem\_sygnatury czasowej\_\_ostatniej\_migracji** &gt; (w przykładzie, w którym został on wykonany, będzie coś takiego jak **Add\_-Migration 201311062215252 addreader**).
    **_Uwaga:_** należy *dołączyć sygnaturę czasową, aby migracja wiedziała, że chcesz edytować istniejącą migrację, a nie utworzyć szkieletu nowej.*
    Spowoduje to zaktualizowanie metadanych ostatniej migracji w celu dopasowania do bieżącego modelu. Po zakończeniu wykonywania polecenia otrzymasz następujące ostrzeżenie, ale to dokładnie to, czego potrzebujesz. "*Został utworzony ponowny szkielet" tylko kod\_projektanta dla migracji "201311062215252 addreader". Aby przeprowadzić ponowną próbę przetworzenia szkieletu całej migracji, użyj parametru-Force.*
6.  Uruchom **aktualizację-Database** , aby ponownie zastosować najnowszą migrację ze zaktualizowanymi metadanymi.
7.  Kontynuuj opracowywanie lub Prześlij do kontroli źródła (po uruchomieniu testów jednostkowych kursu).

Poniżej znajduje się stan lokalnej bazy \#kodu dewelopera 2 po użyciu tego podejścia.

![Zaktualizowane metadane](~/ef6/media/updatedmetadata.png)

## <a name="summary"></a>Podsumowanie

Korzystanie z Migracje Code First w środowisku zespołu ma pewne problemy. Jednak podstawowe informacje dotyczące sposobu działania migracji oraz kilka prostych metod rozwiązywania konfliktów scalania ułatwiają pokonanie tych wyzwań.

Podstawowy problem dotyczy nieprawidłowych metadanych przechowywanych w najnowszej migracji. Powoduje to, że Code First nieprawidłowo wykryć, że bieżący model i schemat bazy danych nie są zgodne, i aby przeprowadzić szkielet niepoprawnego kodu podczas następnej migracji. Tę sytuację można rozwiązać przez wygenerowanie pustej migracji z odpowiednim modelem lub zaktualizowanie metadanych w najnowszej migracji.
