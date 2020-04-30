---
title: Przykład testowania EF Core — EF Core
description: Przykład pokazujący, jak testować aplikacje, które używają EF Core
author: ajcvickers
ms.date: 04/22/2020
uid: core/miscellaneous/testing/testing-sample
no-loc:
- Item
- Tag
- Items
- Tags
- items
- tags
ms.openlocfilehash: dda7191df7646aa06aab51d8d7891bd0ba155674
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82564288"
---
# <a name="ef-core-testing-sample"></a>Przykład testowania EF Core

> [!TIP]
> Kod w tym dokumencie można znaleźć w witrynie GitHub jako [przykład możliwy do uruchomienia](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/).
> Należy zauważyć, że niektóre z tych testów **mogą się nie powieść**. Poniższe przyczyny zostały opisane poniżej. 

W tym dokumencie przedstawiono przykład testowania kodu, który używa EF Core.

## <a name="the-application"></a>Aplikacja

[Przykład](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) zawiera dwa projekty:
- ItemsWebApi: bardzo prosty [internetowy interfejs API,](/aspnet/core/tutorials/first-web-api?view=aspnetcore-3.1&tabs=visual-studio) który jest obsługiwany przez ASP.NET Core z jednym kontrolerem
- Testy: projekt testu [XUnit](https://xunit.net/) do testowania kontrolera

### <a name="the-model-and-business-rules"></a>Model i reguły biznesowe

Model obsługujący ten interfejs API ma dwa typy jednostek Items : Tagsi.

* Itemsma nazwę uwzględniającą wielkość liter i kolekcję Tags.
* Każda Tag z nich ma etykietę i liczbę reprezentującą liczbę przypadków, gdy została ona zastosowana do Item.
* Każdy Item z nich powinien mieć Tag tylko jedną z podaną etykietą.
  * Jeśli element jest oznakowany za pomocą tej samej etykiety więcej niż raz, wówczas liczba w istniejącym tagu z tą etykietą jest zwiększana zamiast nowego tworzonego tagu. 
* Item Usunięcie wszystkich skojarzonych Tags.

#### <a name="the-item-entity-type"></a>Typ Item jednostki

Typ `Item` jednostki:

[!code-csharp[ItemEntityType](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Item.cs?name=ItemEntityType)]

I jego konfiguracja w `DbContext.OnModelCreating`programie:

[!code-csharp[ConfigureItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/ItemsContext.cs?name=ConfigureItem)]

Zwróć uwagę, że typ jednostki ogranicza sposób, w jaki może być używany w celu odzwierciedlenia modelu domeny i reguł firmy. W szczególności:
- Klucz podstawowy jest mapowany bezpośrednio do `_id` pola i nie jest ujawniany publicznie
  - EF wykrywa i używa konstruktora prywatnego akceptującego wartość i nazwę klucza podstawowego.
- `Name` Właściwość jest tylko do odczytu i ustawiana tylko w konstruktorze. 
- Tagssą ujawniane jako `IReadOnlyList<Tag>` a, aby zapobiec dowolnej modyfikacji.
  - EF kojarzy `Tags` właściwość z polem `_tags` zapasowym, dopasowując ich nazwy. 
  - `AddTag` Metoda przyjmuje etykietę tagu i implementuje regułę biznesową opisaną powyżej.
    Oznacza to, że tag jest dodawany tylko dla nowych etykiet.
    W przeciwnym razie liczba w istniejącej etykiecie jest zwiększana.
- Właściwość `Tags` nawigacji jest skonfigurowana dla relacji wiele-do-jednego
  - Nie ma potrzeby dla właściwości nawigacji z Tag elementu do Item, dlatego nie jest on uwzględniony.
  - Ponadto program Tag nie definiuje właściwości klucza obcego.
    Zamiast tego, EF utworzy właściwość w stanie Shadow i zarządza nią.

#### <a name="the-tag-entity-type"></a>Typ Tag jednostki

Typ `Tag` jednostki:

[!code-csharp[TagEntityType](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Tag.cs?name=TagEntityType)]

I jego konfiguracja w `DbContext.OnModelCreating`programie:

[!code-csharp[ConfigureTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/ItemsContext.cs?name=ConfigureTag)]

Podobnie jak Item, Tag ukrywa swój klucz podstawowy i ustawia właściwość `Label` tylko do odczytu.

### <a name="the-itemscontroller"></a>ItemsController

Kontroler interfejsu API sieci Web jest dość podstawowy.
Pobiera `DbContext` z kontenera iniekcji zależności za pośrednictwem iniekcji konstruktora:

[!code-csharp[Constructor](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=Constructor)]

Ma metody do pobrania wszystkie Items lub Item o podaną nazwę:

[!code-csharp[Get](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=Get)]

Ma ona metodę dodawania nowego Item:

[!code-csharp[PostItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=PostItem)]

Metoda Item zwracająca etykietę z etykietą:

[!code-csharp[PostTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=PostTag)]

I metodę usuwania Item wszystkich skojarzonych Tags:

[!code-csharp[DeleteItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=DeleteItem)]

Większość walidacji i obsługa błędów została usunięta w celu zmniejszenia bałaganu.

## <a name="the-tests"></a>Testy

Testy są zorganizowane do uruchamiania z wieloma konfiguracjami dostawcy bazy danych:
* Dostawca SQL Server, który jest dostawcą używanym przez aplikację
* Dostawca oprogramowania SQLite
* Dostawca oprogramowania SQLite korzystający z baz danych programu SQLite w pamięci
* Dostawca bazy danych EF w pamięci

Jest to osiągane przez umieszczenie wszystkich testów w klasie bazowej, a następnie dziedziczenie z tej metody w celu przetestowania przy użyciu każdego dostawcy.

> [!TIP]
> Należy zmienić parametry połączenia SQL Server, jeśli nie używasz LocalDB.

> [!TIP]
> Zobacz [testowanie przy użyciu oprogramowania SQLite](xref:core/miscellaneous/testing/sqlite) , aby uzyskać wskazówki dotyczące używania oprogramowania SQLite do testowania w pamięci. 

Następujące dwa testy są oczekiwane niepowodzeniem:
* `Can_remove_item_and_all_associated_tags`w przypadku korzystania z dostawcy bazy danych EF w pamięci
* `Can_add_item_differing_only_by_case`podczas uruchamiania z dostawcą SQL Server

Zostało to omówione bardziej szczegółowo poniżej.

### <a name="setting-up-and-seeding-the-database"></a>Konfigurowanie i wypełnianie bazy danych

XUnit, jak większość platform testowych, utworzy nowe wystąpienie klasy testowej dla każdego przebiegu testu.
Ponadto XUnit nie będzie uruchamiać testów w ramach danej klasy testowej równolegle. Oznacza to, że możemy zainstalować i skonfigurować bazę danych w konstruktorze testów i będzie on znajdował się w dobrze znanym stanie dla każdego testu.

> [!TIP]
> Ten przykład odtworzy bazę danych dla każdego testu.
> Jest to dobre rozwiązanie w przypadku testowania baz danych oprogramowania SQLite i EF w pamięci, ale może to wiązać się z innymi systemami baz danych, w tym SQL Server.
> Metody zmniejszania nakładu pracy dotyczą [udostępniania baz danych między testami](xref:core/miscellaneous/testing/sharing-databases).

Po uruchomieniu każdego testu:
* DbContextOptions są skonfigurowane dla dostawcy w użyciu i przechodzą do konstruktora klasy bazowej
  * Te opcje są przechowywane we właściwości i używane w ramach testów do tworzenia wystąpień DbContext
* Metoda inicjatora jest wywoływana w celu utworzenia i wypełniania bazy danych
  * Metoda inicjatora gwarantuje, że baza danych jest czyszczona, usuwając ją, a następnie ponownie tworząc.
  * Niektóre dobrze znane jednostki testowe są tworzone i zapisywane w bazie danych

[!code-csharp[Seeding](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=Seeding)]

Każda konkretna Klasa testowa dziedziczy po tym.
Przykład:

[!code-csharp[SqliteItemsControllerTest](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/SqliteItemsControllerTest.cs?name=SqliteItemsControllerTest)]

### <a name="test-structure"></a>Struktura testu

Mimo że aplikacja używa iniekcji zależności, testy nie są.
W tym miejscu warto użyć iniekcji zależności, ale dodatkowy kod, którego wymaga, ma niewiele wartości.
Zamiast tego, DbContext jest tworzony przy `new` użyciu, a następnie bezpośrednio przekazywać jako zależność do kontrolera.

Każdy test wykonuje następnie metodę testową na kontrolerze i potwierdza, że wyniki są zgodnie z oczekiwaniami.
Przykład:

[!code-csharp[CanGetItems](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanGetItems)]

Zwróć uwagę, że różne wystąpienia DbContext są używane do wypełniania bazy danych i uruchamiania testów. Gwarantuje to, że test nie będzie używać jednostek, które są śledzone przez kontekst podczas umieszczania.
Lepiej pasuje do tego, co się dzieje w aplikacjach i usługach sieci Web.

Testy, które mutacją bazy danych tworzą drugie wystąpienie DbContext w testach z przyczyn podobnych.
Oznacza to utworzenie nowego, czystego i kontekstowego, a następnie przeczytanie go z bazy danych, aby upewnić się, że zmiany rzeczywiście zostały zapisane w bazie danych. Przykład:

[!code-csharp[CanAddItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddItem)]

Dwa nieco bardziej objęte testy obejmują logikę biznesową wokół dodawania tagów.

[!code-csharp[CanAddTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddTag)]

[!code-csharp[CanUpTagCount](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanUpTagCount)]

## <a name="issues-using-different-database-providers"></a>Problemy korzystające z różnych dostawców baz danych

Testowanie przy użyciu innego systemu bazy danych niż używany w aplikacji produkcyjnej może prowadzić do problemów.
Są one objęte poziomem koncepcyjnym w [testowaniu kodu, który używa EF Core](xref:core/miscellaneous/testing/index).  
Poniższe sekcje obejmują dwa przykłady takich problemów, które przedstawiono w testach w tym przykładzie.

### <a name="test-passes-when-application-is-broken"></a>Test kończy się powodzeniem, gdy aplikacja jest uszkodzona

Jednym z wymagań aplikacji jest to, że "Items ma nazwę uwzględniającą wielkość liter i kolekcję". Tags
Jest to bardzo proste do przetestowania:

[!code-csharp[CanAddItemCaseInsensitive](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddItemCaseInsensitive)]

Uruchomienie tego testu względem bazy danych EF w pamięci oznacza, że wszystko jest odpowiednie.
Wszystko nadal wygląda dobrze w przypadku korzystania z oprogramowania SQLite.
Ale test kończy się niepowodzeniem, gdy przebiega z SQL Server!

```
System.InvalidOperationException : Sequence contains more than one element
   at System.Linq.ThrowHelper.ThrowMoreThanOneElementException()
   at System.Linq.Enumerable.Single[TSource](IEnumerable`1 source)
   at Microsoft.EntityFrameworkCore.Query.Internal.QueryCompiler.Execute[TResult](Expression query)
   at Microsoft.EntityFrameworkCore.Query.Internal.EntityQueryProvider.Execute[TResult](Expression expression)
   at System.Linq.Queryable.Single[TSource](IQueryable`1 source, Expression`1 predicate)
   at Tests.ItemsControllerTest.Can_add_item_differing_only_by_case()
```

Wynika to z faktu, że zarówno w bazie danych EF w pamięci, jak i w bazie danych programu SQLite jest rozróżniana wielkość liter.
SQL Server, z drugiej strony, nie uwzględnia wielkości liter. 

EF Core, zgodnie z projektem, nie zmienia tych zachowań, ponieważ wymusza zmianę wielkości liter może mieć wpływ na dużą wydajność.

Gdy wiemy, że jest to problem, możemy naprawić aplikację i zrekompensować testy.
Jednak w tym miejscu można pominąć tę usterkę, jeśli tylko testuje się za pomocą programu EF w pamięci podręcznej lub dostawcy oprogramowania SQLite.

### <a name="test-fails-when-application-is-correct"></a>Test kończy się niepowodzeniem, gdy aplikacja jest poprawna 

Inne wymagania dotyczące naszej aplikacji to "Usuwanie Item wszystkich skojarzonych. Tags
Ponownie, łatwa do przetestowania:

[!code-csharp[DeleteItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=DeleteItem)]

Ten test kończy się w przypadku SQL Server i oprogramowania SQLite, ale nie powiedzie się w bazie danych EF w pamięci.

```
Assert.False() Failure
Expected: False
Actual:   True
   at Tests.ItemsControllerTest.Can_remove_item_and_all_associated_tags()
```

W takim przypadku aplikacja działa poprawnie, ponieważ SQL Server obsługuje [usuwanie kaskadowe](xref:core/saving/cascade-delete). Program SQLite obsługuje także usuwanie kaskadowe, tak jak w przypadku większości relacyjnych baz danych, co pozwala na ich testowanie w programie SQLite.
Z drugiej strony baza danych EF w pamięci nie [obsługuje usuwania kaskadowego](https://github.com/dotnet/efcore/issues/3924).
Oznacza to, że ta część aplikacji nie może zostać przetestowana z dostawcą bazy danych EF w pamięci.
