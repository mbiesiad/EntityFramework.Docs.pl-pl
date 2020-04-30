---
title: Udostępnianie baz danych między testami — EF Core
description: Przykład pokazujący, jak udostępnić bazę danych między wieloma testami
author: ajcvickers
ms.date: 04/25/2020
uid: core/miscellaneous/testing/sharing-databases
ms.openlocfilehash: 96216fb8afd6916402637de3ffab04b79d37e11c
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82564281"
---
# <a name="sharing-databases-between-tests"></a>Udostępnianie baz danych między testami

[Przykład testowania EF Core](xref:core/miscellaneous/testing/testing-sample) pokazano, jak testować aplikacje dla różnych systemów baz danych.
Dla tego przykładu każdy test utworzył nową bazę danych.
Jest to dobry wzorzec w przypadku korzystania z oprogramowania SQLite lub EF bazy danych w pamięci, ale może to pociągnąć za sobą znaczne obciążenie w przypadku korzystania z innych systemów baz danych.

Ten przykład kompiluje się w poprzednim przykładzie poprzez przeniesienie tworzenia bazy danych do osprzętu testowego.
Dzięki temu można utworzyć pojedynczą bazę danych SQL Server i rozmieścić ją tylko raz dla wszystkich testów.

> [!TIP]
> Przed kontynuowaniem upewnij się, że pracujesz na [próbce EF Core testowej](xref:core/miscellaneous/testing/testing-sample) .

Pisanie wielu testów w tej samej bazie danych nie jest trudne.
Lewę odbywa się to w taki sposób, aby testy nie były przenoszone na siebie w miarę ich działania.
Wymaga to zrozumienie:
* Jak bezpiecznie udostępniać obiekty między testami
* Gdy środowisko testowe uruchamia testy równolegle
* Jak zachować czysty stan bazy danych dla każdego testu  

## <a name="the-fixture"></a>Armatura

Będziemy używać armatury testowej do udostępniania obiektów między testami.
[Dokumentacja XUnit](https://xunit.net/docs/shared-context.html) określa, że Armatura powinna być używana, gdy chcesz utworzyć pojedynczy kontekst testu i udostępnić go między wszystkimi testami w klasie, i wyczyścić po zakończeniu wszystkich testów w klasie. "

> [!TIP]
> Ten przykład używa [XUnit](https://xunit.net/), ale podobne koncepcje istnieją w innych strukturach testowania, w tym [nunit](https://nunit.org/).

Oznacza to, że musimy przenieść Tworzenie bazy danych i wypełnianie jej do klasy armatury.
Oto jak wygląda:

[!code-csharp[SharedDatabaseFixture](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseFixture.cs?name=SharedDatabaseFixture)]

Teraz Zwróć uwagę na to, jak Konstruktor:
* Tworzy jedno połączenie z bazą danych na potrzeby okresu istnienia armatury
* Tworzy i nasiona tej bazy danych przez wywołanie `Seed` metody 

Ignoruj blokowanie teraz; będziemy wrócić do niego później.

> [!TIP]
> Tworzenie i wypełnianie kodu nie musi być asynchroniczne.
> Uczynienie go asynchronicznie komplikuje kod i nie poprawi wydajności ani przepływności testów.

Baza danych jest tworzona przez usunięcie istniejącej bazy danych, a następnie utworzenie nowej bazy danych.
Gwarantuje to, że baza danych będzie zgodna z bieżącym modelem EF, nawet jeśli została zmieniona od ostatniego uruchomienia testu.

> [!TIP]
> Jest to szybsze od "czyszczenia" istniejącej bazy danych przy użyciu podobnej do operacji ponownego [duplikowania](https://jimmybogard.com/tag/respawn/) , a nie tworzenia ich za każdym razem.
> Należy jednak zwrócić uwagę, aby upewnić się, że schemat bazy danych jest aktualny przy użyciu modelu EF.

Połączenie z bazą danych zostanie usunięte po usunięciu armatury.
W tym momencie można również rozważyć usunięcie testowej bazy danych.
Jednak wymaga to dodatkowego blokowania i zliczania odwołań, jeśli Armatura jest udostępniana przez wiele klas testowych.
Ponadto często przydatne jest, aby baza danych testowa była nadal dostępna do debugowania testów zakończonych niepowodzeniem.  

## <a name="using-the-fixture"></a>Korzystanie z osprzętu

XUnit ma wspólny wzorzec do kojarzenia armatury testów z klasą testów:

[!code-csharp[UsingTheFixture](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseTest.cs?name=UsingTheFixture)]

XUnit utworzy teraz pojedyncze wystąpienie elementu osprzętu i przekaże je do każdego wystąpienia klasy testowej.
(Należy pamiętać, że z pierwszego [przykładu testowego](xref:core/miscellaneous/testing/testing-sample) , który XUnit tworzy nowe wystąpienie klasy testowej przy każdym uruchomieniu testu). Oznacza to, że baza danych zostanie utworzona i zostanie przetworzona raz, a następnie każdy test użyje tej bazy danych.

Należy zauważyć, że testy w ramach pojedynczej klasy nie będą uruchamiane równolegle.
Oznacza to, że jest bezpieczny dla każdego testu, aby używać tego samego połączenia z bazą danych `DbConnection` , nawet jeśli obiekt nie jest bezpieczny dla wątków.

## <a name="maintaining-database-state"></a>Obsługa stanu bazy danych

Testy często wymagają mutacji danych testowych przy użyciu operacji wstawiania, aktualizacji i usuwania.
Jednak te zmiany będą mieć wpływ na inne testy, które oczekują czystej, nasiennej bazy danych.

Można to zrobić, uruchamiając mutację testów wewnątrz transakcji.
Przykład:

[!code-csharp[CanAddItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseTest.cs?name=CanAddItem)]

Należy zauważyć, że transakcja jest tworzona, gdy test jest uruchamiany i usuwany po zakończeniu.
Likwidacja transakcji powoduje jej wycofanie, więc żadna zmiana nie będzie widoczna w innych testach.

Metoda pomocnika służąca do tworzenia kontekstu (Zobacz kod armatury powyżej) akceptuje tę transakcję i umożliwia użycie kontekstu DbContext do korzystania z niego. 

## <a name="sharing-the-fixture"></a>Udostępnianie armatury

Można zauważyć, że zablokowanie kodu wokół tworzenia bazy danych i wypełnianie.
Nie jest to potrzebne w przypadku tego przykładu, ponieważ tylko jedna Klasa testów używa armatury, więc tworzone jest tylko jedno wystąpienie elementu osprzętu.

Jednak można użyć tego samego elementu osprzętu z wieloma klasami testów.
XUnit utworzy jedno wystąpienie elementu osprzętu dla każdej z tych klas.
Mogą one być używane przez różne wątki z uruchomionymi testami równoległymi.
W związku z tym ważne jest, aby mieć odpowiednie blokady, aby upewnić się, że tylko jeden wątek wykonuje tworzenie i umieszczanie baz danych.

> [!TIP]
> Prosta `lock` jest tutaj dokładniej.
> Nie ma potrzeby podejmowania większej liczby skomplikowanych elementów, takich jak wzorce bez blokady.
