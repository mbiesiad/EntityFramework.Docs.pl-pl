---
title: Testowanie przy użyciu oprogramowania SQLite-EF Core
description: Testowanie aplikacji EF Core przy użyciu oprogramowania SQLite
author: ajcvickers
ms.date: 04/24/2020
uid: core/miscellaneous/testing/sqlite
ms.openlocfilehash: 327fdc230df2a3b4094accf93fffa81f92e0a931
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82538285"
---
# <a name="using-sqlite-to-test-an-ef-core-application"></a>Testowanie aplikacji EF Core przy użyciu oprogramowania SQLite

> [!WARNING]
> Korzystanie z oprogramowania SQLite może być skutecznym sposobem przetestowania aplikacji EF Core.
> Problemy mogą jednak wystąpić w sytuacji, gdy program SQLite działa inaczej niż w przypadku innych systemów baz danych. Zobacz [testowanie kodu, który używa EF Core](xref:core/miscellaneous/testing/index) do omówienia problemów i zalet.  

Ten dokument zawiera informacje dotyczące pojęć wprowadzonych w [przykładzie pokazujący sposób testowania aplikacji, które używają EF Core](xref:core/miscellaneous/testing/testing-sample).
Przykłady kodu przedstawione w tym miejscu pochodzą z tego przykładu.

## <a name="using-sqlite-in-memory-databases"></a>Korzystanie z baz danych programu SQLite w pamięci

Zwykle Program SQLite tworzy bazy danych jako proste pliki i uzyskuje dostęp do pliku w procesie przy użyciu aplikacji.
Jest to bardzo szybkie, szczególnie w przypadku korzystania z szybkiego dysku [SSD](https://en.wikipedia.org/wiki/Solid-state_drive). 

Program SQLite może również używać baz danych utworzonych w pamięci podręcznej.
Jest to łatwe w użyciu z EF Core, o ile zrozumiesz okres istnienia bazy danych w pamięci:
* Baza danych jest tworzona, gdy zostanie otwarte połączenie
* Baza danych jest usuwana po zamknięciu połączenia

EF Core użyje już otwartego połączenia, gdy zostanie on określony, i nigdy nie spróbuje go zamknąć.
Dlatego klucz używany EF Core z bazą danych programu SQLite w pamięci polega na otwarciu połączenia przed przekazaniem go do EF.  

[Przykład](xref:core/miscellaneous/testing/testing-sample) osiąga ten kod przy użyciu następującego kodu:

[!code-csharp[SqliteInMemory](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/SqliteInMemoryItemsControllerTest.cs?name=SqliteInMemory)]

Wzory
* `CreateInMemoryDatabase` Metoda tworzy bazę danych w pamięci programu SQLite i otwiera połączenie z nim.
* Utworzony `DbConnection` został wyodrębniony z `ContextOptions` i zapisany.
* Połączenie jest usuwane, gdy test zostanie usunięty, aby zasoby nie były wyciekami. 

> [!NOTE]
> [#16103 problemu](https://github.com/dotnet/efcore/issues/16103) śledzi sposoby ułatwienia tego zarządzania połączeniami. 
