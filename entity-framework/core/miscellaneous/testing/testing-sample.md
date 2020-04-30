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
# <a name="ef-core-testing-sample"></a><span data-ttu-id="106eb-103">Przykład testowania EF Core</span><span class="sxs-lookup"><span data-stu-id="106eb-103">EF Core testing sample</span></span>

> [!TIP]
> <span data-ttu-id="106eb-104">Kod w tym dokumencie można znaleźć w witrynie GitHub jako [przykład możliwy do uruchomienia](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/).</span><span class="sxs-lookup"><span data-stu-id="106eb-104">The code in this document can be found on GitHub as a [runnable sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/).</span></span>
> <span data-ttu-id="106eb-105">Należy zauważyć, że niektóre z tych testów **mogą się nie powieść**.</span><span class="sxs-lookup"><span data-stu-id="106eb-105">Note that some of these tests **are expected to fail**.</span></span> <span data-ttu-id="106eb-106">Poniższe przyczyny zostały opisane poniżej.</span><span class="sxs-lookup"><span data-stu-id="106eb-106">The reasons for this are explained below.</span></span> 

<span data-ttu-id="106eb-107">W tym dokumencie przedstawiono przykład testowania kodu, który używa EF Core.</span><span class="sxs-lookup"><span data-stu-id="106eb-107">This doc walks through a sample for testing code that uses EF Core.</span></span>

## <a name="the-application"></a><span data-ttu-id="106eb-108">Aplikacja</span><span class="sxs-lookup"><span data-stu-id="106eb-108">The application</span></span>

<span data-ttu-id="106eb-109">[Przykład](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) zawiera dwa projekty:</span><span class="sxs-lookup"><span data-stu-id="106eb-109">The [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) contains two projects:</span></span>
- <span data-ttu-id="106eb-110">ItemsWebApi: bardzo prosty [internetowy interfejs API,](/aspnet/core/tutorials/first-web-api?view=aspnetcore-3.1&tabs=visual-studio) który jest obsługiwany przez ASP.NET Core z jednym kontrolerem</span><span class="sxs-lookup"><span data-stu-id="106eb-110">ItemsWebApi: A very simple [Web API backed by ASP.NET Core](/aspnet/core/tutorials/first-web-api?view=aspnetcore-3.1&tabs=visual-studio) with a single controller</span></span>
- <span data-ttu-id="106eb-111">Testy: projekt testu [XUnit](https://xunit.net/) do testowania kontrolera</span><span class="sxs-lookup"><span data-stu-id="106eb-111">Tests: An [XUnit](https://xunit.net/) test project to test the controller</span></span>

### <a name="the-model-and-business-rules"></a><span data-ttu-id="106eb-112">Model i reguły biznesowe</span><span class="sxs-lookup"><span data-stu-id="106eb-112">The model and business rules</span></span>

<span data-ttu-id="106eb-113">Model obsługujący ten interfejs API ma dwa typy jednostek Items : Tagsi.</span><span class="sxs-lookup"><span data-stu-id="106eb-113">The model backing this API has two entity types: Items and Tags.</span></span>

* Items<span data-ttu-id="106eb-114">ma nazwę uwzględniającą wielkość liter i kolekcję Tags.</span><span class="sxs-lookup"><span data-stu-id="106eb-114"> have a case-sensitive name and a collection of Tags.</span></span>
* <span data-ttu-id="106eb-115">Każda Tag z nich ma etykietę i liczbę reprezentującą liczbę przypadków, gdy została ona zastosowana do Item.</span><span class="sxs-lookup"><span data-stu-id="106eb-115">Each Tag has a label and a count representing the number of times it has been applied to the Item.</span></span>
* <span data-ttu-id="106eb-116">Każdy Item z nich powinien mieć Tag tylko jedną z podaną etykietą.</span><span class="sxs-lookup"><span data-stu-id="106eb-116">Each Item should only have one Tag with with a given label.</span></span>
  * <span data-ttu-id="106eb-117">Jeśli element jest oznakowany za pomocą tej samej etykiety więcej niż raz, wówczas liczba w istniejącym tagu z tą etykietą jest zwiększana zamiast nowego tworzonego tagu.</span><span class="sxs-lookup"><span data-stu-id="106eb-117">If an item is tagged with the same label more than once, then the count on the existing tag with that label is incremented instead of a new tag being created.</span></span> 
* <span data-ttu-id="106eb-118">Item Usunięcie wszystkich skojarzonych Tags.</span><span class="sxs-lookup"><span data-stu-id="106eb-118">Deleting an Item should delete all associated Tags.</span></span>

#### <a name="the-item-entity-type"></a><span data-ttu-id="106eb-119">Typ Item jednostki</span><span class="sxs-lookup"><span data-stu-id="106eb-119">The Item entity type</span></span>

<span data-ttu-id="106eb-120">Typ `Item` jednostki:</span><span class="sxs-lookup"><span data-stu-id="106eb-120">The `Item` entity type:</span></span>

[!code-csharp[ItemEntityType](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Item.cs?name=ItemEntityType)]

<span data-ttu-id="106eb-121">I jego konfiguracja w `DbContext.OnModelCreating`programie:</span><span class="sxs-lookup"><span data-stu-id="106eb-121">And its configuration in `DbContext.OnModelCreating`:</span></span>

[!code-csharp[ConfigureItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/ItemsContext.cs?name=ConfigureItem)]

<span data-ttu-id="106eb-122">Zwróć uwagę, że typ jednostki ogranicza sposób, w jaki może być używany w celu odzwierciedlenia modelu domeny i reguł firmy.</span><span class="sxs-lookup"><span data-stu-id="106eb-122">Notice that entity type constrains the way it can be used to reflect the domain model and business rules.</span></span> <span data-ttu-id="106eb-123">W szczególności:</span><span class="sxs-lookup"><span data-stu-id="106eb-123">In particular:</span></span>
- <span data-ttu-id="106eb-124">Klucz podstawowy jest mapowany bezpośrednio do `_id` pola i nie jest ujawniany publicznie</span><span class="sxs-lookup"><span data-stu-id="106eb-124">The primary key is mapped directly to the `_id` field and not exposed publicly</span></span>
  - <span data-ttu-id="106eb-125">EF wykrywa i używa konstruktora prywatnego akceptującego wartość i nazwę klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="106eb-125">EF detects and uses the private constructor accepting the primary key value and name.</span></span>
- <span data-ttu-id="106eb-126">`Name` Właściwość jest tylko do odczytu i ustawiana tylko w konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="106eb-126">The `Name` property is read-only and set only in the constructor.</span></span> 
- Tags<span data-ttu-id="106eb-127">są ujawniane jako `IReadOnlyList<Tag>` a, aby zapobiec dowolnej modyfikacji.</span><span class="sxs-lookup"><span data-stu-id="106eb-127"> are exposed as a `IReadOnlyList<Tag>` to prevent arbitrary modification.</span></span>
  - <span data-ttu-id="106eb-128">EF kojarzy `Tags` właściwość z polem `_tags` zapasowym, dopasowując ich nazwy.</span><span class="sxs-lookup"><span data-stu-id="106eb-128">EF associates the `Tags` property with the `_tags` backing field by matching their names.</span></span> 
  - <span data-ttu-id="106eb-129">`AddTag` Metoda przyjmuje etykietę tagu i implementuje regułę biznesową opisaną powyżej.</span><span class="sxs-lookup"><span data-stu-id="106eb-129">The `AddTag` method takes a tag label and implements the business rule described above.</span></span>
    <span data-ttu-id="106eb-130">Oznacza to, że tag jest dodawany tylko dla nowych etykiet.</span><span class="sxs-lookup"><span data-stu-id="106eb-130">That is, a tag is only added for new labels.</span></span>
    <span data-ttu-id="106eb-131">W przeciwnym razie liczba w istniejącej etykiecie jest zwiększana.</span><span class="sxs-lookup"><span data-stu-id="106eb-131">Otherwise the count on an existing label is incremented.</span></span>
- <span data-ttu-id="106eb-132">Właściwość `Tags` nawigacji jest skonfigurowana dla relacji wiele-do-jednego</span><span class="sxs-lookup"><span data-stu-id="106eb-132">The `Tags` navigation property is configured for a many-to-one relationship</span></span>
  - <span data-ttu-id="106eb-133">Nie ma potrzeby dla właściwości nawigacji z Tag elementu do Item, dlatego nie jest on uwzględniony.</span><span class="sxs-lookup"><span data-stu-id="106eb-133">There is no need for a navigation property from Tag to Item, so it is not included.</span></span>
  - <span data-ttu-id="106eb-134">Ponadto program Tag nie definiuje właściwości klucza obcego.</span><span class="sxs-lookup"><span data-stu-id="106eb-134">Also, Tag does not define a foreign key property.</span></span>
    <span data-ttu-id="106eb-135">Zamiast tego, EF utworzy właściwość w stanie Shadow i zarządza nią.</span><span class="sxs-lookup"><span data-stu-id="106eb-135">Instead, EF will create and manage a property in shadow-state.</span></span>

#### <a name="the-tag-entity-type"></a><span data-ttu-id="106eb-136">Typ Tag jednostki</span><span class="sxs-lookup"><span data-stu-id="106eb-136">The Tag entity type</span></span>

<span data-ttu-id="106eb-137">Typ `Tag` jednostki:</span><span class="sxs-lookup"><span data-stu-id="106eb-137">The `Tag` entity type:</span></span>

[!code-csharp[TagEntityType](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Tag.cs?name=TagEntityType)]

<span data-ttu-id="106eb-138">I jego konfiguracja w `DbContext.OnModelCreating`programie:</span><span class="sxs-lookup"><span data-stu-id="106eb-138">And its configuration in `DbContext.OnModelCreating`:</span></span>

[!code-csharp[ConfigureTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/ItemsContext.cs?name=ConfigureTag)]

<span data-ttu-id="106eb-139">Podobnie jak Item, Tag ukrywa swój klucz podstawowy i ustawia właściwość `Label` tylko do odczytu.</span><span class="sxs-lookup"><span data-stu-id="106eb-139">Similarly to Item, Tag hides its primary key and makes the `Label` property read-only.</span></span>

### <a name="the-itemscontroller"></a><span data-ttu-id="106eb-140">ItemsController</span><span class="sxs-lookup"><span data-stu-id="106eb-140">The ItemsController</span></span>

<span data-ttu-id="106eb-141">Kontroler interfejsu API sieci Web jest dość podstawowy.</span><span class="sxs-lookup"><span data-stu-id="106eb-141">The Web API controller is pretty basic.</span></span>
<span data-ttu-id="106eb-142">Pobiera `DbContext` z kontenera iniekcji zależności za pośrednictwem iniekcji konstruktora:</span><span class="sxs-lookup"><span data-stu-id="106eb-142">It gets a `DbContext` from the dependency injection container through constructor injection:</span></span>

[!code-csharp[Constructor](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=Constructor)]

<span data-ttu-id="106eb-143">Ma metody do pobrania wszystkie Items lub Item o podaną nazwę:</span><span class="sxs-lookup"><span data-stu-id="106eb-143">It has methods to get all Items or an Item with a given name:</span></span>

[!code-csharp[Get](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=Get)]

<span data-ttu-id="106eb-144">Ma ona metodę dodawania nowego Item:</span><span class="sxs-lookup"><span data-stu-id="106eb-144">It has a method to add a new Item:</span></span>

[!code-csharp[PostItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=PostItem)]

<span data-ttu-id="106eb-145">Metoda Item zwracająca etykietę z etykietą:</span><span class="sxs-lookup"><span data-stu-id="106eb-145">A method to tag an Item with a label:</span></span>

[!code-csharp[PostTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=PostTag)]

<span data-ttu-id="106eb-146">I metodę usuwania Item wszystkich skojarzonych Tags:</span><span class="sxs-lookup"><span data-stu-id="106eb-146">And a method to delete an Item and all associated Tags:</span></span>

[!code-csharp[DeleteItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=DeleteItem)]

<span data-ttu-id="106eb-147">Większość walidacji i obsługa błędów została usunięta w celu zmniejszenia bałaganu.</span><span class="sxs-lookup"><span data-stu-id="106eb-147">Most validation and error handling has been removed to reduce clutter.</span></span>

## <a name="the-tests"></a><span data-ttu-id="106eb-148">Testy</span><span class="sxs-lookup"><span data-stu-id="106eb-148">The Tests</span></span>

<span data-ttu-id="106eb-149">Testy są zorganizowane do uruchamiania z wieloma konfiguracjami dostawcy bazy danych:</span><span class="sxs-lookup"><span data-stu-id="106eb-149">The tests are organized to run with multiple database provider configurations:</span></span>
* <span data-ttu-id="106eb-150">Dostawca SQL Server, który jest dostawcą używanym przez aplikację</span><span class="sxs-lookup"><span data-stu-id="106eb-150">The SQL Server provider, which is the provider used by the application</span></span>
* <span data-ttu-id="106eb-151">Dostawca oprogramowania SQLite</span><span class="sxs-lookup"><span data-stu-id="106eb-151">The SQLite provider</span></span>
* <span data-ttu-id="106eb-152">Dostawca oprogramowania SQLite korzystający z baz danych programu SQLite w pamięci</span><span class="sxs-lookup"><span data-stu-id="106eb-152">The SQLite provider using in-memory SQLite databases</span></span>
* <span data-ttu-id="106eb-153">Dostawca bazy danych EF w pamięci</span><span class="sxs-lookup"><span data-stu-id="106eb-153">The EF in-memory database provider</span></span>

<span data-ttu-id="106eb-154">Jest to osiągane przez umieszczenie wszystkich testów w klasie bazowej, a następnie dziedziczenie z tej metody w celu przetestowania przy użyciu każdego dostawcy.</span><span class="sxs-lookup"><span data-stu-id="106eb-154">This is achieved by putting all the tests in a base class, then inheriting from this to test with each provider.</span></span>

> [!TIP]
> <span data-ttu-id="106eb-155">Należy zmienić parametry połączenia SQL Server, jeśli nie używasz LocalDB.</span><span class="sxs-lookup"><span data-stu-id="106eb-155">You will need to change the SQL Server connection string if you're not using LocalDB.</span></span>

> [!TIP]
> <span data-ttu-id="106eb-156">Zobacz [testowanie przy użyciu oprogramowania SQLite](xref:core/miscellaneous/testing/sqlite) , aby uzyskać wskazówki dotyczące używania oprogramowania SQLite do testowania w pamięci.</span><span class="sxs-lookup"><span data-stu-id="106eb-156">See [Testing with SQLite](xref:core/miscellaneous/testing/sqlite) for guidance on using SQLite for in-memory testing.</span></span> 

<span data-ttu-id="106eb-157">Następujące dwa testy są oczekiwane niepowodzeniem:</span><span class="sxs-lookup"><span data-stu-id="106eb-157">The following two tests are expected to fail:</span></span>
* <span data-ttu-id="106eb-158">`Can_remove_item_and_all_associated_tags`w przypadku korzystania z dostawcy bazy danych EF w pamięci</span><span class="sxs-lookup"><span data-stu-id="106eb-158">`Can_remove_item_and_all_associated_tags` when running with the EF in-memory database provider</span></span>
* <span data-ttu-id="106eb-159">`Can_add_item_differing_only_by_case`podczas uruchamiania z dostawcą SQL Server</span><span class="sxs-lookup"><span data-stu-id="106eb-159">`Can_add_item_differing_only_by_case` when running with the SQL Server provider</span></span>

<span data-ttu-id="106eb-160">Zostało to omówione bardziej szczegółowo poniżej.</span><span class="sxs-lookup"><span data-stu-id="106eb-160">This is covered in more detail below.</span></span>

### <a name="setting-up-and-seeding-the-database"></a><span data-ttu-id="106eb-161">Konfigurowanie i wypełnianie bazy danych</span><span class="sxs-lookup"><span data-stu-id="106eb-161">Setting up and seeding the database</span></span>

<span data-ttu-id="106eb-162">XUnit, jak większość platform testowych, utworzy nowe wystąpienie klasy testowej dla każdego przebiegu testu.</span><span class="sxs-lookup"><span data-stu-id="106eb-162">XUnit, like most testing frameworks, will create a new test class instance for each test run.</span></span>
<span data-ttu-id="106eb-163">Ponadto XUnit nie będzie uruchamiać testów w ramach danej klasy testowej równolegle.</span><span class="sxs-lookup"><span data-stu-id="106eb-163">Also, XUnit will not run tests within a given test class in parallel.</span></span> <span data-ttu-id="106eb-164">Oznacza to, że możemy zainstalować i skonfigurować bazę danych w konstruktorze testów i będzie on znajdował się w dobrze znanym stanie dla każdego testu.</span><span class="sxs-lookup"><span data-stu-id="106eb-164">This means that we can setup and configure the database in the test constructor and it will be in a well-known state for each test.</span></span>

> [!TIP]
> <span data-ttu-id="106eb-165">Ten przykład odtworzy bazę danych dla każdego testu.</span><span class="sxs-lookup"><span data-stu-id="106eb-165">This sample recreates the database for each test.</span></span>
> <span data-ttu-id="106eb-166">Jest to dobre rozwiązanie w przypadku testowania baz danych oprogramowania SQLite i EF w pamięci, ale może to wiązać się z innymi systemami baz danych, w tym SQL Server.</span><span class="sxs-lookup"><span data-stu-id="106eb-166">This works well for SQLite and EF in-memory database testing, but can involve significant overhead with other database systems, including SQL Server.</span></span>
> <span data-ttu-id="106eb-167">Metody zmniejszania nakładu pracy dotyczą [udostępniania baz danych między testami](xref:core/miscellaneous/testing/sharing-databases).</span><span class="sxs-lookup"><span data-stu-id="106eb-167">Approaches for reducing this overhead are covered in [Sharing databases across tests](xref:core/miscellaneous/testing/sharing-databases).</span></span>

<span data-ttu-id="106eb-168">Po uruchomieniu każdego testu:</span><span class="sxs-lookup"><span data-stu-id="106eb-168">When each test is run:</span></span>
* <span data-ttu-id="106eb-169">DbContextOptions są skonfigurowane dla dostawcy w użyciu i przechodzą do konstruktora klasy bazowej</span><span class="sxs-lookup"><span data-stu-id="106eb-169">DbContextOptions are configured for the provider in use and passed to the base class constructor</span></span>
  * <span data-ttu-id="106eb-170">Te opcje są przechowywane we właściwości i używane w ramach testów do tworzenia wystąpień DbContext</span><span class="sxs-lookup"><span data-stu-id="106eb-170">These options are stored in a property and used throughout the tests for creating DbContext instances</span></span>
* <span data-ttu-id="106eb-171">Metoda inicjatora jest wywoływana w celu utworzenia i wypełniania bazy danych</span><span class="sxs-lookup"><span data-stu-id="106eb-171">A Seed method is called to create and seed the database</span></span>
  * <span data-ttu-id="106eb-172">Metoda inicjatora gwarantuje, że baza danych jest czyszczona, usuwając ją, a następnie ponownie tworząc.</span><span class="sxs-lookup"><span data-stu-id="106eb-172">The Seed method ensures the database is clean by deleting it and then re-creating it</span></span>
  * <span data-ttu-id="106eb-173">Niektóre dobrze znane jednostki testowe są tworzone i zapisywane w bazie danych</span><span class="sxs-lookup"><span data-stu-id="106eb-173">Some well-known test entities are created and saved to the database</span></span>

[!code-csharp[Seeding](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=Seeding)]

<span data-ttu-id="106eb-174">Każda konkretna Klasa testowa dziedziczy po tym.</span><span class="sxs-lookup"><span data-stu-id="106eb-174">Each concrete test class then inherits from this.</span></span>
<span data-ttu-id="106eb-175">Przykład:</span><span class="sxs-lookup"><span data-stu-id="106eb-175">For example:</span></span>

[!code-csharp[SqliteItemsControllerTest](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/SqliteItemsControllerTest.cs?name=SqliteItemsControllerTest)]

### <a name="test-structure"></a><span data-ttu-id="106eb-176">Struktura testu</span><span class="sxs-lookup"><span data-stu-id="106eb-176">Test structure</span></span>

<span data-ttu-id="106eb-177">Mimo że aplikacja używa iniekcji zależności, testy nie są.</span><span class="sxs-lookup"><span data-stu-id="106eb-177">Even though the application uses dependency injection, the tests do not.</span></span>
<span data-ttu-id="106eb-178">W tym miejscu warto użyć iniekcji zależności, ale dodatkowy kod, którego wymaga, ma niewiele wartości.</span><span class="sxs-lookup"><span data-stu-id="106eb-178">It would be fine to use dependency injection here, but the additional code it requires has little value.</span></span>
<span data-ttu-id="106eb-179">Zamiast tego, DbContext jest tworzony przy `new` użyciu, a następnie bezpośrednio przekazywać jako zależność do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="106eb-179">Instead, a DbContext is created using `new` and then directly passed as the dependency to the controller.</span></span>

<span data-ttu-id="106eb-180">Każdy test wykonuje następnie metodę testową na kontrolerze i potwierdza, że wyniki są zgodnie z oczekiwaniami.</span><span class="sxs-lookup"><span data-stu-id="106eb-180">Each test then executes the method under test on the controller and asserts the results are as expected.</span></span>
<span data-ttu-id="106eb-181">Przykład:</span><span class="sxs-lookup"><span data-stu-id="106eb-181">For example:</span></span>

[!code-csharp[CanGetItems](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanGetItems)]

<span data-ttu-id="106eb-182">Zwróć uwagę, że różne wystąpienia DbContext są używane do wypełniania bazy danych i uruchamiania testów.</span><span class="sxs-lookup"><span data-stu-id="106eb-182">Notice that different DbContext instances are used to seed the database and run the tests.</span></span> <span data-ttu-id="106eb-183">Gwarantuje to, że test nie będzie używać jednostek, które są śledzone przez kontekst podczas umieszczania.</span><span class="sxs-lookup"><span data-stu-id="106eb-183">This ensures that test is not using (or tripping over) entities tracked by the context when seeding.</span></span>
<span data-ttu-id="106eb-184">Lepiej pasuje do tego, co się dzieje w aplikacjach i usługach sieci Web.</span><span class="sxs-lookup"><span data-stu-id="106eb-184">It also better matches what happens in web apps and services.</span></span>

<span data-ttu-id="106eb-185">Testy, które mutacją bazy danych tworzą drugie wystąpienie DbContext w testach z przyczyn podobnych.</span><span class="sxs-lookup"><span data-stu-id="106eb-185">Tests that mutate the database create a second DbContext instance in the test for similar reasons.</span></span>
<span data-ttu-id="106eb-186">Oznacza to utworzenie nowego, czystego i kontekstowego, a następnie przeczytanie go z bazy danych, aby upewnić się, że zmiany rzeczywiście zostały zapisane w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="106eb-186">That is, creating a new, clean, context and then reading into it from the database to ensure that the changes really were saved to the database.</span></span> <span data-ttu-id="106eb-187">Przykład:</span><span class="sxs-lookup"><span data-stu-id="106eb-187">For example:</span></span>

[!code-csharp[CanAddItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddItem)]

<span data-ttu-id="106eb-188">Dwa nieco bardziej objęte testy obejmują logikę biznesową wokół dodawania tagów.</span><span class="sxs-lookup"><span data-stu-id="106eb-188">Two slightly more involved tests cover the business logic around adding tags.</span></span>

[!code-csharp[CanAddTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddTag)]

[!code-csharp[CanUpTagCount](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanUpTagCount)]

## <a name="issues-using-different-database-providers"></a><span data-ttu-id="106eb-189">Problemy korzystające z różnych dostawców baz danych</span><span class="sxs-lookup"><span data-stu-id="106eb-189">Issues using different database providers</span></span>

<span data-ttu-id="106eb-190">Testowanie przy użyciu innego systemu bazy danych niż używany w aplikacji produkcyjnej może prowadzić do problemów.</span><span class="sxs-lookup"><span data-stu-id="106eb-190">Testing with a different database system than is used in the production application can lead to problems.</span></span>
<span data-ttu-id="106eb-191">Są one objęte poziomem koncepcyjnym w [testowaniu kodu, który używa EF Core](xref:core/miscellaneous/testing/index).</span><span class="sxs-lookup"><span data-stu-id="106eb-191">These are covered at the conceptual level in [Testing code that uses EF Core](xref:core/miscellaneous/testing/index).</span></span>  
<span data-ttu-id="106eb-192">Poniższe sekcje obejmują dwa przykłady takich problemów, które przedstawiono w testach w tym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="106eb-192">The sections below cover two examples of such issues demonstrated by the tests in this sample.</span></span>

### <a name="test-passes-when-application-is-broken"></a><span data-ttu-id="106eb-193">Test kończy się powodzeniem, gdy aplikacja jest uszkodzona</span><span class="sxs-lookup"><span data-stu-id="106eb-193">Test passes when application is broken</span></span>

<span data-ttu-id="106eb-194">Jednym z wymagań aplikacji jest to, że "Items ma nazwę uwzględniającą wielkość liter i kolekcję". Tags</span><span class="sxs-lookup"><span data-stu-id="106eb-194">One of the requirements for our application is that, "Items have a case-sensitive name and a collection of Tags."</span></span>
<span data-ttu-id="106eb-195">Jest to bardzo proste do przetestowania:</span><span class="sxs-lookup"><span data-stu-id="106eb-195">This is pretty simple to test:</span></span>

[!code-csharp[CanAddItemCaseInsensitive](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddItemCaseInsensitive)]

<span data-ttu-id="106eb-196">Uruchomienie tego testu względem bazy danych EF w pamięci oznacza, że wszystko jest odpowiednie.</span><span class="sxs-lookup"><span data-stu-id="106eb-196">Running this test against the EF in-memory database indicates that everything is fine.</span></span>
<span data-ttu-id="106eb-197">Wszystko nadal wygląda dobrze w przypadku korzystania z oprogramowania SQLite.</span><span class="sxs-lookup"><span data-stu-id="106eb-197">Everything still looks fine when using SQLite.</span></span>
<span data-ttu-id="106eb-198">Ale test kończy się niepowodzeniem, gdy przebiega z SQL Server!</span><span class="sxs-lookup"><span data-stu-id="106eb-198">But the test fails when run against SQL Server!</span></span>

```
System.InvalidOperationException : Sequence contains more than one element
   at System.Linq.ThrowHelper.ThrowMoreThanOneElementException()
   at System.Linq.Enumerable.Single[TSource](IEnumerable`1 source)
   at Microsoft.EntityFrameworkCore.Query.Internal.QueryCompiler.Execute[TResult](Expression query)
   at Microsoft.EntityFrameworkCore.Query.Internal.EntityQueryProvider.Execute[TResult](Expression expression)
   at System.Linq.Queryable.Single[TSource](IQueryable`1 source, Expression`1 predicate)
   at Tests.ItemsControllerTest.Can_add_item_differing_only_by_case()
```

<span data-ttu-id="106eb-199">Wynika to z faktu, że zarówno w bazie danych EF w pamięci, jak i w bazie danych programu SQLite jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="106eb-199">This is because both the EF in-memory database and the SQLite database are case-sensitive by default.</span></span>
<span data-ttu-id="106eb-200">SQL Server, z drugiej strony, nie uwzględnia wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="106eb-200">SQL Server, on the other hand, is case-insensitive!</span></span> 

<span data-ttu-id="106eb-201">EF Core, zgodnie z projektem, nie zmienia tych zachowań, ponieważ wymusza zmianę wielkości liter może mieć wpływ na dużą wydajność.</span><span class="sxs-lookup"><span data-stu-id="106eb-201">EF Core, by design, does not change these behaviors because forcing a change in case-sensitivity can have big performance impact.</span></span>

<span data-ttu-id="106eb-202">Gdy wiemy, że jest to problem, możemy naprawić aplikację i zrekompensować testy.</span><span class="sxs-lookup"><span data-stu-id="106eb-202">Once we know this is a problem we can fix the application and compensate in tests.</span></span>
<span data-ttu-id="106eb-203">Jednak w tym miejscu można pominąć tę usterkę, jeśli tylko testuje się za pomocą programu EF w pamięci podręcznej lub dostawcy oprogramowania SQLite.</span><span class="sxs-lookup"><span data-stu-id="106eb-203">However, the point here is that this bug could be missed if only testing with the EF in-memory database or SQLite providers.</span></span>

### <a name="test-fails-when-application-is-correct"></a><span data-ttu-id="106eb-204">Test kończy się niepowodzeniem, gdy aplikacja jest poprawna</span><span class="sxs-lookup"><span data-stu-id="106eb-204">Test fails when application is correct</span></span> 

<span data-ttu-id="106eb-205">Inne wymagania dotyczące naszej aplikacji to "Usuwanie Item wszystkich skojarzonych. Tags</span><span class="sxs-lookup"><span data-stu-id="106eb-205">Another of the requirements for our application is that, "deleting an Item should delete all associated Tags."</span></span>
<span data-ttu-id="106eb-206">Ponownie, łatwa do przetestowania:</span><span class="sxs-lookup"><span data-stu-id="106eb-206">Again, easy to test:</span></span>

[!code-csharp[DeleteItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=DeleteItem)]

<span data-ttu-id="106eb-207">Ten test kończy się w przypadku SQL Server i oprogramowania SQLite, ale nie powiedzie się w bazie danych EF w pamięci.</span><span class="sxs-lookup"><span data-stu-id="106eb-207">This test passes on SQL Server and SQLite, but fails with the EF in-memory database!</span></span>

```
Assert.False() Failure
Expected: False
Actual:   True
   at Tests.ItemsControllerTest.Can_remove_item_and_all_associated_tags()
```

<span data-ttu-id="106eb-208">W takim przypadku aplikacja działa poprawnie, ponieważ SQL Server obsługuje [usuwanie kaskadowe](xref:core/saving/cascade-delete).</span><span class="sxs-lookup"><span data-stu-id="106eb-208">In this case the application is working correctly because SQL Server supports [cascade deletes](xref:core/saving/cascade-delete).</span></span> <span data-ttu-id="106eb-209">Program SQLite obsługuje także usuwanie kaskadowe, tak jak w przypadku większości relacyjnych baz danych, co pozwala na ich testowanie w programie SQLite.</span><span class="sxs-lookup"><span data-stu-id="106eb-209">SQLite also supports cascade deletes, as do most relational databases, so testing this on SQLite works.</span></span>
<span data-ttu-id="106eb-210">Z drugiej strony baza danych EF w pamięci nie [obsługuje usuwania kaskadowego](https://github.com/dotnet/efcore/issues/3924).</span><span class="sxs-lookup"><span data-stu-id="106eb-210">On the other hand, the EF in-memory database [does not support cascade deletes](https://github.com/dotnet/efcore/issues/3924).</span></span>
<span data-ttu-id="106eb-211">Oznacza to, że ta część aplikacji nie może zostać przetestowana z dostawcą bazy danych EF w pamięci.</span><span class="sxs-lookup"><span data-stu-id="106eb-211">This means that this part of the application cannot be tested with the EF in-memory database provider.</span></span>
