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
# <a name="sharing-databases-between-tests"></a><span data-ttu-id="61a41-103">Udostępnianie baz danych między testami</span><span class="sxs-lookup"><span data-stu-id="61a41-103">Sharing databases between tests</span></span>

<span data-ttu-id="61a41-104">[Przykład testowania EF Core](xref:core/miscellaneous/testing/testing-sample) pokazano, jak testować aplikacje dla różnych systemów baz danych.</span><span class="sxs-lookup"><span data-stu-id="61a41-104">The [EF Core testing sample](xref:core/miscellaneous/testing/testing-sample) showed how to test applications against different database systems.</span></span>
<span data-ttu-id="61a41-105">Dla tego przykładu każdy test utworzył nową bazę danych.</span><span class="sxs-lookup"><span data-stu-id="61a41-105">For that sample, each test created a new database.</span></span>
<span data-ttu-id="61a41-106">Jest to dobry wzorzec w przypadku korzystania z oprogramowania SQLite lub EF bazy danych w pamięci, ale może to pociągnąć za sobą znaczne obciążenie w przypadku korzystania z innych systemów baz danych.</span><span class="sxs-lookup"><span data-stu-id="61a41-106">This is a good pattern when using SQLite or the EF in-memory database, but it can involve significant overhead when using other database systems.</span></span>

<span data-ttu-id="61a41-107">Ten przykład kompiluje się w poprzednim przykładzie poprzez przeniesienie tworzenia bazy danych do osprzętu testowego.</span><span class="sxs-lookup"><span data-stu-id="61a41-107">This sample builds on the previous sample by moving database creation into a test fixture.</span></span>
<span data-ttu-id="61a41-108">Dzięki temu można utworzyć pojedynczą bazę danych SQL Server i rozmieścić ją tylko raz dla wszystkich testów.</span><span class="sxs-lookup"><span data-stu-id="61a41-108">This allows a single SQL Server database to be created and seeded only once for all tests.</span></span>

> [!TIP]
> <span data-ttu-id="61a41-109">Przed kontynuowaniem upewnij się, że pracujesz na [próbce EF Core testowej](xref:core/miscellaneous/testing/testing-sample) .</span><span class="sxs-lookup"><span data-stu-id="61a41-109">Make sure to work through the [EF Core testing sample](xref:core/miscellaneous/testing/testing-sample) before continuing here.</span></span>

<span data-ttu-id="61a41-110">Pisanie wielu testów w tej samej bazie danych nie jest trudne.</span><span class="sxs-lookup"><span data-stu-id="61a41-110">It's not difficult to write multiple tests against the same database.</span></span>
<span data-ttu-id="61a41-111">Lewę odbywa się to w taki sposób, aby testy nie były przenoszone na siebie w miarę ich działania.</span><span class="sxs-lookup"><span data-stu-id="61a41-111">The trick is doing it in a way that the tests don't trip over each other as they run.</span></span>
<span data-ttu-id="61a41-112">Wymaga to zrozumienie:</span><span class="sxs-lookup"><span data-stu-id="61a41-112">This requires understanding:</span></span>
* <span data-ttu-id="61a41-113">Jak bezpiecznie udostępniać obiekty między testami</span><span class="sxs-lookup"><span data-stu-id="61a41-113">How to safely share objects between tests</span></span>
* <span data-ttu-id="61a41-114">Gdy środowisko testowe uruchamia testy równolegle</span><span class="sxs-lookup"><span data-stu-id="61a41-114">When the test framework runs tests in parallel</span></span>
* <span data-ttu-id="61a41-115">Jak zachować czysty stan bazy danych dla każdego testu</span><span class="sxs-lookup"><span data-stu-id="61a41-115">How to keep the database in a clean state for every test</span></span>  

## <a name="the-fixture"></a><span data-ttu-id="61a41-116">Armatura</span><span class="sxs-lookup"><span data-stu-id="61a41-116">The fixture</span></span>

<span data-ttu-id="61a41-117">Będziemy używać armatury testowej do udostępniania obiektów między testami.</span><span class="sxs-lookup"><span data-stu-id="61a41-117">We will use a test fixture for sharing objects between tests.</span></span>
<span data-ttu-id="61a41-118">[Dokumentacja XUnit](https://xunit.net/docs/shared-context.html) określa, że Armatura powinna być używana, gdy chcesz utworzyć pojedynczy kontekst testu i udostępnić go między wszystkimi testami w klasie, i wyczyścić po zakończeniu wszystkich testów w klasie. "</span><span class="sxs-lookup"><span data-stu-id="61a41-118">The [XUnit documentation](https://xunit.net/docs/shared-context.html) states that a fixture should be used "when you want to create a single test context and share it among all the tests in the class, and have it cleaned up after all the tests in the class have finished."</span></span>

> [!TIP]
> <span data-ttu-id="61a41-119">Ten przykład używa [XUnit](https://xunit.net/), ale podobne koncepcje istnieją w innych strukturach testowania, w tym [nunit](https://nunit.org/).</span><span class="sxs-lookup"><span data-stu-id="61a41-119">This sample uses [XUnit](https://xunit.net/), but similar concepts exist in other testing frameworks, including [NUnit](https://nunit.org/).</span></span>

<span data-ttu-id="61a41-120">Oznacza to, że musimy przenieść Tworzenie bazy danych i wypełnianie jej do klasy armatury.</span><span class="sxs-lookup"><span data-stu-id="61a41-120">This means that we need to move database creation and seeding to a fixture class.</span></span>
<span data-ttu-id="61a41-121">Oto jak wygląda:</span><span class="sxs-lookup"><span data-stu-id="61a41-121">Here's what it looks like:</span></span>

[!code-csharp[SharedDatabaseFixture](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseFixture.cs?name=SharedDatabaseFixture)]

<span data-ttu-id="61a41-122">Teraz Zwróć uwagę na to, jak Konstruktor:</span><span class="sxs-lookup"><span data-stu-id="61a41-122">For now, notice how the constructor:</span></span>
* <span data-ttu-id="61a41-123">Tworzy jedno połączenie z bazą danych na potrzeby okresu istnienia armatury</span><span class="sxs-lookup"><span data-stu-id="61a41-123">Creates a single database connection for the lifetime of the fixture</span></span>
* <span data-ttu-id="61a41-124">Tworzy i nasiona tej bazy danych przez wywołanie `Seed` metody</span><span class="sxs-lookup"><span data-stu-id="61a41-124">Creates and seeds that database by calling the `Seed` method</span></span> 

<span data-ttu-id="61a41-125">Ignoruj blokowanie teraz; będziemy wrócić do niego później.</span><span class="sxs-lookup"><span data-stu-id="61a41-125">Ignore the locking for now; we will come back to it later.</span></span>

> [!TIP]
> <span data-ttu-id="61a41-126">Tworzenie i wypełnianie kodu nie musi być asynchroniczne.</span><span class="sxs-lookup"><span data-stu-id="61a41-126">The creation and seeding code does not need to be async.</span></span>
> <span data-ttu-id="61a41-127">Uczynienie go asynchronicznie komplikuje kod i nie poprawi wydajności ani przepływności testów.</span><span class="sxs-lookup"><span data-stu-id="61a41-127">Making it async will complicate the code and will not improve performance or throughput of tests.</span></span>

<span data-ttu-id="61a41-128">Baza danych jest tworzona przez usunięcie istniejącej bazy danych, a następnie utworzenie nowej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="61a41-128">The database is created by first deleting any existing database and then creating a new database.</span></span>
<span data-ttu-id="61a41-129">Gwarantuje to, że baza danych będzie zgodna z bieżącym modelem EF, nawet jeśli została zmieniona od ostatniego uruchomienia testu.</span><span class="sxs-lookup"><span data-stu-id="61a41-129">This ensures that the database matches the current EF model even if it has been changed since the last test run.</span></span>

> [!TIP]
> <span data-ttu-id="61a41-130">Jest to szybsze od "czyszczenia" istniejącej bazy danych przy użyciu podobnej do operacji ponownego [duplikowania](https://jimmybogard.com/tag/respawn/) , a nie tworzenia ich za każdym razem.</span><span class="sxs-lookup"><span data-stu-id="61a41-130">It can be faster to "clean" the existing database using something like [respawn](https://jimmybogard.com/tag/respawn/) rather than re-create it each time.</span></span>
> <span data-ttu-id="61a41-131">Należy jednak zwrócić uwagę, aby upewnić się, że schemat bazy danych jest aktualny przy użyciu modelu EF.</span><span class="sxs-lookup"><span data-stu-id="61a41-131">However, care must be taken to ensure that the database schema is up-to-date with the EF model when doing this.</span></span>

<span data-ttu-id="61a41-132">Połączenie z bazą danych zostanie usunięte po usunięciu armatury.</span><span class="sxs-lookup"><span data-stu-id="61a41-132">The database connection is disposed when the fixture is disposed.</span></span>
<span data-ttu-id="61a41-133">W tym momencie można również rozważyć usunięcie testowej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="61a41-133">You may also consider deleting the test database at this point.</span></span>
<span data-ttu-id="61a41-134">Jednak wymaga to dodatkowego blokowania i zliczania odwołań, jeśli Armatura jest udostępniana przez wiele klas testowych.</span><span class="sxs-lookup"><span data-stu-id="61a41-134">However, this will require additional locking and reference counting if the fixture is being shared by multiple test classes.</span></span>
<span data-ttu-id="61a41-135">Ponadto często przydatne jest, aby baza danych testowa była nadal dostępna do debugowania testów zakończonych niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="61a41-135">Also, it is often useful to have the test database still available for debugging failed tests.</span></span>  

## <a name="using-the-fixture"></a><span data-ttu-id="61a41-136">Korzystanie z osprzętu</span><span class="sxs-lookup"><span data-stu-id="61a41-136">Using the fixture</span></span>

<span data-ttu-id="61a41-137">XUnit ma wspólny wzorzec do kojarzenia armatury testów z klasą testów:</span><span class="sxs-lookup"><span data-stu-id="61a41-137">XUnit has a common pattern for associating a test fixture with a class of tests:</span></span>

[!code-csharp[UsingTheFixture](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseTest.cs?name=UsingTheFixture)]

<span data-ttu-id="61a41-138">XUnit utworzy teraz pojedyncze wystąpienie elementu osprzętu i przekaże je do każdego wystąpienia klasy testowej.</span><span class="sxs-lookup"><span data-stu-id="61a41-138">XUnit will now create a single fixture instance and pass it to each instance of the test class.</span></span>
<span data-ttu-id="61a41-139">(Należy pamiętać, że z pierwszego [przykładu testowego](xref:core/miscellaneous/testing/testing-sample) , który XUnit tworzy nowe wystąpienie klasy testowej przy każdym uruchomieniu testu). Oznacza to, że baza danych zostanie utworzona i zostanie przetworzona raz, a następnie każdy test użyje tej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="61a41-139">(Remember from the first [testing sample](xref:core/miscellaneous/testing/testing-sample) that XUnit creates a new test class instance every time it runs a test.) This means that the database will be created and seeded once and then each test will use this database.</span></span>

<span data-ttu-id="61a41-140">Należy zauważyć, że testy w ramach pojedynczej klasy nie będą uruchamiane równolegle.</span><span class="sxs-lookup"><span data-stu-id="61a41-140">Note that tests within a single class will not be run in parallel.</span></span>
<span data-ttu-id="61a41-141">Oznacza to, że jest bezpieczny dla każdego testu, aby używać tego samego połączenia z bazą danych `DbConnection` , nawet jeśli obiekt nie jest bezpieczny dla wątków.</span><span class="sxs-lookup"><span data-stu-id="61a41-141">This means it is safe for each test to use the same database connection, even though the `DbConnection` object is not thread-safe.</span></span>

## <a name="maintaining-database-state"></a><span data-ttu-id="61a41-142">Obsługa stanu bazy danych</span><span class="sxs-lookup"><span data-stu-id="61a41-142">Maintaining database state</span></span>

<span data-ttu-id="61a41-143">Testy często wymagają mutacji danych testowych przy użyciu operacji wstawiania, aktualizacji i usuwania.</span><span class="sxs-lookup"><span data-stu-id="61a41-143">Tests often need to mutate the test data with inserts, updates, and deletes.</span></span>
<span data-ttu-id="61a41-144">Jednak te zmiany będą mieć wpływ na inne testy, które oczekują czystej, nasiennej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="61a41-144">But these changes will then impact other tests which are expecting a clean, seeded database.</span></span>

<span data-ttu-id="61a41-145">Można to zrobić, uruchamiając mutację testów wewnątrz transakcji.</span><span class="sxs-lookup"><span data-stu-id="61a41-145">This can be dealt with by running mutating tests inside a transaction.</span></span>
<span data-ttu-id="61a41-146">Przykład:</span><span class="sxs-lookup"><span data-stu-id="61a41-146">For example:</span></span>

[!code-csharp[CanAddItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseTest.cs?name=CanAddItem)]

<span data-ttu-id="61a41-147">Należy zauważyć, że transakcja jest tworzona, gdy test jest uruchamiany i usuwany po zakończeniu.</span><span class="sxs-lookup"><span data-stu-id="61a41-147">Notice that the transaction is created as the test starts and disposed when it is finished.</span></span>
<span data-ttu-id="61a41-148">Likwidacja transakcji powoduje jej wycofanie, więc żadna zmiana nie będzie widoczna w innych testach.</span><span class="sxs-lookup"><span data-stu-id="61a41-148">Disposing the transaction causes it to be rolled back, so none of the changes will be seen by other tests.</span></span>

<span data-ttu-id="61a41-149">Metoda pomocnika służąca do tworzenia kontekstu (Zobacz kod armatury powyżej) akceptuje tę transakcję i umożliwia użycie kontekstu DbContext do korzystania z niego.</span><span class="sxs-lookup"><span data-stu-id="61a41-149">The helper method for creating a context (see the fixture code above) accepts this transaction and opts the DbContext into using it.</span></span> 

## <a name="sharing-the-fixture"></a><span data-ttu-id="61a41-150">Udostępnianie armatury</span><span class="sxs-lookup"><span data-stu-id="61a41-150">Sharing the fixture</span></span>

<span data-ttu-id="61a41-151">Można zauważyć, że zablokowanie kodu wokół tworzenia bazy danych i wypełnianie.</span><span class="sxs-lookup"><span data-stu-id="61a41-151">You may have noticed locking code around database creation and seeding.</span></span>
<span data-ttu-id="61a41-152">Nie jest to potrzebne w przypadku tego przykładu, ponieważ tylko jedna Klasa testów używa armatury, więc tworzone jest tylko jedno wystąpienie elementu osprzętu.</span><span class="sxs-lookup"><span data-stu-id="61a41-152">This is not needed for this sample since only one class of tests use the fixture, so only a single fixture instance is created.</span></span>

<span data-ttu-id="61a41-153">Jednak można użyć tego samego elementu osprzętu z wieloma klasami testów.</span><span class="sxs-lookup"><span data-stu-id="61a41-153">However, you may want to use the same fixture with multiple classes of tests.</span></span>
<span data-ttu-id="61a41-154">XUnit utworzy jedno wystąpienie elementu osprzętu dla każdej z tych klas.</span><span class="sxs-lookup"><span data-stu-id="61a41-154">XUnit will create one fixture instance for each of these classes.</span></span>
<span data-ttu-id="61a41-155">Mogą one być używane przez różne wątki z uruchomionymi testami równoległymi.</span><span class="sxs-lookup"><span data-stu-id="61a41-155">These may be used by different threads running tests in parallel.</span></span>
<span data-ttu-id="61a41-156">W związku z tym ważne jest, aby mieć odpowiednie blokady, aby upewnić się, że tylko jeden wątek wykonuje tworzenie i umieszczanie baz danych.</span><span class="sxs-lookup"><span data-stu-id="61a41-156">Therefore, it is important to have appropriate locking to ensure only one thread does the database creation and seeding.</span></span>

> [!TIP]
> <span data-ttu-id="61a41-157">Prosta `lock` jest tutaj dokładniej.</span><span class="sxs-lookup"><span data-stu-id="61a41-157">A simple `lock` is fine here.</span></span>
> <span data-ttu-id="61a41-158">Nie ma potrzeby podejmowania większej liczby skomplikowanych elementów, takich jak wzorce bez blokady.</span><span class="sxs-lookup"><span data-stu-id="61a41-158">There is no need to attempt anything more complex, such as any lock-free patterns.</span></span>
