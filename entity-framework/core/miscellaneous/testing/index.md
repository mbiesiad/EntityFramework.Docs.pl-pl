---
title: Testowanie kodu, który używa EF Core-EF Core
description: Różne podejścia do testowania aplikacji korzystających EF Core
author: ajcvickers
ms.date: 04/22/2020
uid: core/miscellaneous/testing/index
ms.openlocfilehash: 7929c284c2794b2fcc95235ae413d56895ebb6e2
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526813"
---
# <a name="testing-code-that-uses-ef-core"></a>Testowanie kodu korzystającego ze środowiska EF Core

Testowanie kodu, który uzyskuje dostęp do bazy danych, wymaga:
* Uruchamianie zapytań i aktualizacji w ramach tego samego systemu bazy danych używanego w środowisku produkcyjnym.
* Uruchamianie zapytań i aktualizacji w celu łatwiejszego zarządzania systemem bazy danych.
* Używanie podwójnego rozliczania testów lub innego mechanizmu, aby uniknąć używania bazy danych w ogóle.

Ten dokument zawiera informacje o zaletach związanych z poszczególnymi opcjami oraz sposób, w jaki EF Core mogą być używane z każdym podejściem.  

> [!TIP]
> Zobacz [przykład testowania EF Core](xref:core/miscellaneous/testing/testing-sample) , aby uzyskać kod pokazujący koncepcje wprowadzone w tym miejscu. 

## <a name="all-database-providers-are-not-equal"></a>Wszyscy dostawcy bazy danych nie są równe

Bardzo ważne jest, aby zrozumieć, że EF Core nie jest zaprojektowana do abstrakcyjnych wszystkich aspektów bazowego systemu bazy danych.
Zamiast tego EF Core jest wspólnym zestawem wzorców i koncepcji, które mogą być używane z dowolnym systemem bazy danych.
EF Core dostawców baz danych, a następnie zachowanie specyficzne dla bazy danych i funkcjonalności w ramach tego wspólnego środowiska.
Dzięki temu każdy system bazy danych może wykonać to działanie najlepiej, zachowując zgodność z innymi systemami baz danych. 

W związku z tym oznacza to, że przełączenie dostawcy bazy danych zmieni zachowanie EF Core i aplikacja nie będzie mogła działać prawidłowo, chyba że jawnie rozwiąże się z wszelkimi różnicami w działaniu.
W wielu przypadkach ten stan będzie działał, ponieważ istnieje wysoki stopień zgodności między relacyjnymi bazami danych.
Jest to dobre i złe.
Dobre, ponieważ przechodzenie między systemami bazy danych może być stosunkowo proste.
Zła, ponieważ może dać fałszywe znaczenie zabezpieczeń, jeśli aplikacja nie została w pełni przetestowana z nowym systemem bazy danych.  

## <a name="approach-1-production-database-system"></a>Podejście 1: system produkcyjnej bazy danych

Zgodnie z opisem w poprzedniej sekcji, jedynym sposobem, aby upewnić się, że testy wykonywane w środowisku produkcyjnym korzystają z tego samego systemu bazy danych.
Na przykład jeśli wdrożona aplikacja korzysta z platformy SQL Azure, testowanie powinno również odbywać się w odniesieniu do usługi SQL Azure.

Jednak każdy deweloper uruchamia testy na platformie SQL Azure, podczas gdy aktywnie pracujemy nad kodem, może być powolny i kosztowny.
Ilustruje to główne działania w ramach tych metod: Kiedy jest to odpowiednie do odróżnienia od systemu produkcyjnej bazy danych, aby zwiększyć efektywność testu?

Na szczęście, w tym przypadku odpowiedź jest bardzo łatwa: Użyj lokalnego lub SQL Server lokalnie do testowania dla deweloperów.
Usługi SQL Azure i SQL Server są bardzo podobne, dlatego testowanie w odniesieniu do SQL Server jest zwykle racjonalnie uzasadnione.
W takim przypadku nadal można uruchomić testy na platformie SQL Azure przed przejściem do środowiska produkcyjnego.
 
### <a name="localdb"></a>LocalDB 

Wszystkie główne systemy baz danych mają pewną postać "Developer Edition" na potrzeby testowania lokalnego.
SQL Server ma również funkcję o nazwie [LocalDB](/sql/database-engine/configure-windows/sql-server-express-localdb?view=sql-server-ver15).
Główną zaletą LocalDB jest to, że powoduje to wystąpienie bazy danych na żądanie.
Pozwala to uniknąć uruchamiania na maszynie usługi bazy danych, nawet jeśli nie są uruchomione testy.

LocalDB nie ma żadnych problemów:
* Nie obsługuje ona wszystkich elementów, które [SQL Server Developer Edition](/sql/sql-server/editions-and-components-of-sql-server-2016?view=sql-server-ver15) .
* Nie jest on dostępny w systemie Linux.
* Może to spowodować opóźnienie pierwszego przebiegu testu, gdy usługa jest w stanie Spuninst.

Na komputerze deweloperskim nigdy nie udało Ci się znaleźć problemu związanego z uruchomioną usługą bazy danych.
LocalDB mogą jednak być odpowiednie dla niektórych osób, szczególnie w mniej wydajnych maszynach deweloperskich.

[Uruchomienie SQL Server](/sql/linux/quickstart-install-connect-docker?view=sql-server-ver15) (lub dowolnego innego systemu bazy danych) w kontenerze platformy Docker (lub podobny) jest innym sposobem, aby uniknąć uruchamiania systemu bazy danych bezpośrednio na komputerze deweloperskim.  

## <a name="approach-2-sqlite"></a>Podejście 2: SQLite

EF Core testuje dostawcę SQL Server przede wszystkim przez uruchomienie go w odniesieniu do lokalnego wystąpienia SQL Server.
Te testy uruchamiają dziesiątki tysięcy zapytań w kilka minut na szybkiej maszynie.
Ilustruje to, że korzystanie z rzeczywistego systemu bazy danych może być rozwiązaniem wydajnym.
Jest to omówienia, który używa niektórych jaśniejszych baz danych, jest jedynym sposobem na szybkie uruchamianie testów.

Co się dzieje, co zrobić, jeśli z jakiegoś powodu nie można uruchomić testów w pobliżu systemu produkcyjnej bazy danych?
Następnym najlepszym wyborem jest użycie czegoś z podobną funkcjonalnością.
Zwykle oznacza to inną relacyjną bazę danych, dla której program [SQLite](https://sqlite.org/index.html) jest oczywisty.

Program SQLite jest dobrym rozwiązaniem, ponieważ:
* Jest ona uruchamiana w procesie z aplikacją i dlatego ma niewielkie obciążenie.
* Używa on prostych, automatycznie tworzonych plików dla baz danych i dlatego nie wymaga zarządzania bazami danych.
* Ma tryb w pamięci, który pozwala uniknąć nawet tworzenia pliku.

Należy jednak pamiętać, że:
* Program SQLite inevitability nie obsługuje wszystkich elementów produkcyjnych systemu bazy danych.
* Program SQLite będzie zachowywać się inaczej niż system produkcyjnej bazy danych dla niektórych zapytań.

Dlatego jeśli w przypadku niektórych testów używasz oprogramowania SQLite, pamiętaj również o przetestowaniu względem rzeczywistego systemu bazy danych.

Aby EF Core uzyskać szczegółowe wskazówki, zobacz [testowanie przy użyciu oprogramowania SQLite](xref:core/miscellaneous/testing/sqlite) . 

## <a name="approach-3-the-ef-core-in-memory-database"></a>Podejście 3: EF Core bazy danych w pamięci

EF Core zawiera bazę danych w pamięci, która jest używana do wewnętrznego testowania EF Core samego siebie.
Ta baza danych jest ogólnie **nieodpowiednia do testowania aplikacji, które używają EF Core**. W szczególności:

* Nie jest to relacyjna baza danych.
* Nie obsługuje transakcji.
* Nie można uruchamiać nieprzetworzonych zapytań SQL.
* Nie jest zoptymalizowany pod kątem wydajności.

Żadna z tych elementów nie jest bardzo ważna podczas testowania EF Core wewnętrznych, ponieważ jest ona używana szczególnie w przypadku, gdy baza danych nie ma znaczenia dla testu.
Z drugiej strony te rzeczy są bardzo ważne podczas testowania aplikacji, która używa EF Core.

## <a name="unit-testing"></a>Testowanie jednostek

Należy rozważyć testowanie części logiki biznesowej, która może wymagać użycia niektórych danych z bazy danych, ale nie testowanie interakcji z bazami danych.
Jedną z opcji jest użycie [testu podwójnego](https://en.wikipedia.org/wiki/Test_double) , takiego jak makieta lub fałszywe.

Używamy podwójnej próby do wewnętrznego testowania EF Core.
Jednak nigdy nie próbujemy zasymulować kontekstu DbContext lub IQueryable.
Takie działanie jest trudne, uciążliwe i delikatne.
**Nie rób tego.**

Zamiast tego używamy bazy danych EF w pamięci podczas testowania jednostkowego, który używa DbContext.
W takim przypadku użycie EF bazy danych w pamięci jest odpowiednie, ponieważ test nie zależy od zachowania bazy danych.
Nie wykonuj tej czynności, aby przetestować rzeczywiste zapytania lub aktualizacje bazy danych.   

[Przykład testowania EF Core](xref:core/miscellaneous/testing/testing-sample) ilustruje testy korzystające z bazy danych EF w pamięci, a także SQL Server i oprogramowania SQLite. 
