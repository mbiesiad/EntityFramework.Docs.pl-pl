---
title: Testowalność i struktura jednostek 4.0 — EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9430e2ab-261c-4e8e-8545-2ebc52d7a247
ms.openlocfilehash: 96b6b2791f12b7d60a233f7e6dc77e5a8579fb66
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434316"
---
# <a name="testability-and-entity-framework-40"></a><span data-ttu-id="7a2e2-102">Testowanie i struktura jednostek 4.0</span><span class="sxs-lookup"><span data-stu-id="7a2e2-102">Testability and Entity Framework 4.0</span></span>
<span data-ttu-id="7a2e2-103">Scott Allen</span><span class="sxs-lookup"><span data-stu-id="7a2e2-103">Scott Allen</span></span>

<span data-ttu-id="7a2e2-104">Opublikowano: maj 2010 r.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-104">Published: May 2010</span></span>

## <a name="introduction"></a><span data-ttu-id="7a2e2-105">Wprowadzenie</span><span class="sxs-lookup"><span data-stu-id="7a2e2-105">Introduction</span></span>

<span data-ttu-id="7a2e2-106">W tym opracowali oficjalny dokument opisano i pokazano, jak napisać testowalny kod za pomocą ADO.NET Entity Framework 4.0 i Visual Studio 2010.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-106">This white paper describes and demonstrates how to write testable code with the ADO.NET Entity Framework 4.0 and Visual Studio 2010.</span></span> <span data-ttu-id="7a2e2-107">W tym dokumencie nie należy skupiać się na konkretnej metodologii testowania, takiej jak projektowanie oparte na testach (TDD) lub projektowanie oparte na zachowaniu (BDD).</span><span class="sxs-lookup"><span data-stu-id="7a2e2-107">This paper does not try to focus on a specific testing methodology, like test-driven design (TDD) or behavior-driven design (BDD).</span></span> <span data-ttu-id="7a2e2-108">Zamiast tego ten dokument będzie koncentrować się na jak napisać kod, który używa ADO.NET Entity Framework jeszcze pozostaje łatwe do wyizolowania i testowania w sposób zautomatyzowany.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-108">Instead this paper will focus on how to write code that uses the ADO.NET Entity Framework yet remains easy to isolate and test in an automated fashion.</span></span> <span data-ttu-id="7a2e2-109">Przyjrzymy się typowym wzorcom projektowym, które ułatwiają testowanie w scenariuszach dostępu do danych i zobaczymy, jak zastosować te wzorce podczas korzystania z tej struktury.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-109">We’ll look at common design patterns that facilitate testing in data access scenarios and see how to apply those patterns when using the framework.</span></span> <span data-ttu-id="7a2e2-110">Przyjrzymy się również określonym funkcjom struktury, aby zobaczyć, jak te funkcje mogą działać w sprawdzalnym kodzie.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-110">We’ll also look at specific features of the framework to see how those features can work in testable code.</span></span>

## <a name="what-is-testable-code"></a><span data-ttu-id="7a2e2-111">Co to jest testowalny kod?</span><span class="sxs-lookup"><span data-stu-id="7a2e2-111">What Is Testable Code?</span></span>

<span data-ttu-id="7a2e2-112">Możliwość weryfikacji oprogramowania za pomocą zautomatyzowanych testów jednostkowych oferuje wiele pożądanych korzyści.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-112">The ability to verify a piece of software using automated unit tests offers many desirable benefits.</span></span> <span data-ttu-id="7a2e2-113">Każdy wie, że dobre testy zmniejszą liczbę wad oprogramowania w aplikacji i podniosą jakość aplikacji - ale posiadanie testów jednostkowych wykracza daleko poza znalezienie błędów.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-113">Everyone knows that good tests will reduce the number of software defects in an application and increase the application’s quality - but having unit tests in place goes far beyond just finding bugs.</span></span>

<span data-ttu-id="7a2e2-114">Dobry zestaw testów jednostkowych pozwala zespołowi programistów zaoszczędzić czas i zachować kontrolę nad utworzonym przez nie oprogramowaniem.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-114">A good unit test suite allows a development team to save time and remain in control of the software they create.</span></span> <span data-ttu-id="7a2e2-115">Zespół może wprowadzać zmiany w istniejącym kodzie, refaktoryzatorze, przeprojektowywanie i restrukturyzacji oprogramowania w celu spełnienia nowych wymagań i dodawać nowe składniki do aplikacji, wiedząc, że zestaw testów może zweryfikować zachowanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-115">A team can make changes to existing code, refactor, redesign, and restructure software to meet new requirements, and add new components into an application all while knowing the test suite can verify the application’s behavior.</span></span> <span data-ttu-id="7a2e2-116">Testy jednostkowe są częścią cyklu szybkiego sprzężenia zwrotnego w celu ułatwienia zmian i zachowania możliwości konserwacji oprogramowania w miarę zwiększania złożoności.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-116">Unit tests are part of a quick feedback cycle to facilitate change and preserve the maintainability of software as complexity increases.</span></span>

<span data-ttu-id="7a2e2-117">Testy jednostkowe mają jednak swoją cenę.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-117">Unit testing comes with a price, however.</span></span> <span data-ttu-id="7a2e2-118">Zespół musi poświęcić czas na tworzenie i utrzymywanie testów jednostkowych.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-118">A team has to invest the time to create and maintain unit tests.</span></span> <span data-ttu-id="7a2e2-119">Nakład pracy wymagany do utworzenia tych testów jest bezpośrednio związany z **możliwością testowania** oprogramowania źródłowego.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-119">The amount of effort required to create these tests is directly related to the **testability** of the underlying software.</span></span> <span data-ttu-id="7a2e2-120">Jak łatwe jest oprogramowanie do przetestowania?</span><span class="sxs-lookup"><span data-stu-id="7a2e2-120">How easy is the software to test?</span></span> <span data-ttu-id="7a2e2-121">Zespół projektujący oprogramowanie z myślą o testowalności stworzy skuteczne testy szybciej niż zespół pracujący z niesprawnym oprogramowaniem.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-121">A team designing software with testability in mind will create effective tests faster than the team working with un-testable software.</span></span>

<span data-ttu-id="7a2e2-122">Firma Microsoft zaprojektowała ADO.NET Entity Framework 4.0 (EF4) z myślą o testowalności.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-122">Microsoft designed the ADO.NET Entity Framework 4.0 (EF4) with testability in mind.</span></span> <span data-ttu-id="7a2e2-123">Nie oznacza to, że deweloperzy będą pisać testy jednostkowe względem samego kodu framework.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-123">This doesn’t mean developers will be writing unit tests against framework code itself.</span></span> <span data-ttu-id="7a2e2-124">Zamiast tego cele testowalności dla EF4 ułatwiają tworzenie sprawdzanego kodu, który tworzy na szczycie struktury.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-124">Instead, the testability goals for EF4 make it easy to create testable code that builds on top of the framework.</span></span> <span data-ttu-id="7a2e2-125">Zanim przyjrzymy się konkretnym przykładom, warto zrozumieć cechy kodu sprawdzanego.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-125">Before we look at specific examples, it’s worthwhile to understand the qualities of testable code.</span></span>

### <a name="the-qualities-of-testable-code"></a><span data-ttu-id="7a2e2-126">Cechy sprawdzanego kodu</span><span class="sxs-lookup"><span data-stu-id="7a2e2-126">The Qualities of Testable Code</span></span>

<span data-ttu-id="7a2e2-127">Kod, który jest łatwy do przetestowania, zawsze będzie wykazywał co najmniej dwie cechy.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-127">Code that is easy to test will always exhibit at least two traits.</span></span> <span data-ttu-id="7a2e2-128">Po pierwsze, sprawdzalny kod jest łatwy do **zaobserwowania.**</span><span class="sxs-lookup"><span data-stu-id="7a2e2-128">First, testable code is easy to **observe**.</span></span> <span data-ttu-id="7a2e2-129">Biorąc pod uwagę niektóre zestaw danych wejściowych, powinno być łatwe do obserwacji danych wyjściowych kodu.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-129">Given some set of inputs, it should be easy to observe the output of the code.</span></span> <span data-ttu-id="7a2e2-130">Na przykład testowanie następującej metody jest łatwe, ponieważ metoda bezpośrednio zwraca wynik obliczeń.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-130">For example, testing the following method is easy because the method directly returns the result of a calculation.</span></span>

``` csharp
    public int Add(int x, int y) {
        return x + y;
    }
```

<span data-ttu-id="7a2e2-131">Testowanie metody jest trudne, jeśli metoda zapisuje obliczoną wartość w gnieździe sieciowym, tabeli bazy danych lub pliku, takiego jak poniższy kod.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-131">Testing a method is difficult if the method writes the computed value into a network socket, a database table, or a file like the following code.</span></span> <span data-ttu-id="7a2e2-132">Test musi wykonać dodatkową pracę, aby pobrać wartość.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-132">The test has to perform additional work to retrieve the value.</span></span>

``` csharp
    public void AddAndSaveToFile(int x, int y) {
         var results = string.Format("The answer is {0}", x + y);
         File.WriteAllText("results.txt", results);
    }
```

<span data-ttu-id="7a2e2-133">Po drugie, sprawdzalny kod jest łatwy do **wyizolowania**.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-133">Secondly, testable code is easy to **isolate**.</span></span> <span data-ttu-id="7a2e2-134">Użyjmy następującego pseudo-kodu jako zły przykład sprawdzalny kod.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-134">Let’s use the following pseudo-code as a bad example of testable code.</span></span>

``` csharp
    public int ComputePolicyValue(InsurancePolicy policy) {
        using (var connection = new SqlConnection("dbConnection"))
        using (var command = new SqlCommand(query, connection)) {

            // business calculations omitted ...               

            if (totalValue > notificationThreshold) {
                var message = new MailMessage();
                message.Subject = "Warning!";
                var client = new SmtpClient();
                client.Send(message);
            }
        }
        return totalValue;
    }
```

<span data-ttu-id="7a2e2-135">Metoda jest łatwa do zaobserwowania – możemy przekazać polisę ubezpieczeniową i sprawdzić, czy wartość zwrotu odpowiada oczekiwanemu wynikowi.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-135">The method is easy to observe – we can pass in an insurance policy and verify the return value matches an expected result.</span></span> <span data-ttu-id="7a2e2-136">Jednak aby przetestować metodę, musimy mieć zainstalowaną bazę danych z poprawnym schematem i skonfigurować serwer SMTP w przypadku, gdy metoda próbuje wysłać wiadomość e-mail.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-136">However, to test the method we’ll need to have a database installed with the correct schema, and configure the SMTP server in case the method tries to send an email.</span></span>

<span data-ttu-id="7a2e2-137">Test jednostkowy chce zweryfikować tylko logikę obliczeń wewnątrz metody, ale test może zakończyć się niepowodzeniem, ponieważ serwer poczty e-mail jest w trybie offline lub ponieważ serwer bazy danych został przeniesiony.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-137">The unit test only wants to verify the calculation logic inside the method, but the test might fail because the email server is offline, or because the database server moved.</span></span> <span data-ttu-id="7a2e2-138">Oba te błędy nie są związane z zachowaniem, które test chce zweryfikować.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-138">Both of these failures are unrelated to the behavior the test wants to verify.</span></span> <span data-ttu-id="7a2e2-139">Zachowanie jest trudne do wyizolowania.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-139">The behavior is difficult to isolate.</span></span>

<span data-ttu-id="7a2e2-140">Deweloperzy oprogramowania, którzy starają się napisać testowalny kod często starają się zachować separacji problemów w kodzie, który piszą.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-140">Software developers who strive to write testable code often strive to maintain a separation of concerns in the code they write.</span></span> <span data-ttu-id="7a2e2-141">Powyższa metoda powinna koncentrować się na obliczeniach biznesowych i delegować szczegóły implementacji bazy danych i wiadomości e-mail do innych składników.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-141">The above method should focus on the business calculations and delegate the database and email implementation details to other components.</span></span> <span data-ttu-id="7a2e2-142">Robert C. Martin nazywa to zasadą jednolitej odpowiedzialności.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-142">Robert C. Martin calls this the Single Responsibility Principle.</span></span> <span data-ttu-id="7a2e2-143">Obiekt powinien hermetyzować pojedynczą, wąską odpowiedzialność, na przykład obliczanie wartości zasad.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-143">An object should encapsulate a single, narrow responsibility, like calculating the value of a policy.</span></span> <span data-ttu-id="7a2e2-144">Wszystkie inne bazy danych i powiadomień pracy powinny być odpowiedzialne za inny obiekt.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-144">All other database and notification work should be the responsibility of some other object.</span></span> <span data-ttu-id="7a2e2-145">Kod napisany w ten sposób jest łatwiejszy do wyizolowania, ponieważ koncentruje się na jednym zadaniu.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-145">Code written in this fashion is easier to isolate because it is focused on a single task.</span></span>

<span data-ttu-id="7a2e2-146">W .NET mamy abstrakcje musimy przestrzegać zasady jednolitej odpowiedzialności i osiągnąć izolację.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-146">In .NET we have the abstractions we need to follow the Single Responsibility Principle and achieve isolation.</span></span> <span data-ttu-id="7a2e2-147">Możemy użyć definicji interfejsu i wymusić kod do użycia abstrakcji interfejsu zamiast typu konkretnego.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-147">We can use interface definitions and force the code to use the interface abstraction instead of a concrete type.</span></span> <span data-ttu-id="7a2e2-148">W dalszej części tego artykułu zobaczymy, jak metoda taka jak zły przykład przedstawiony powyżej może pracować z interfejsami, które *wyglądają,* jakby będą rozmawiać z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-148">Later in this paper we’ll see how a method like the bad example presented above can work with interfaces that *look* like they will talk to the database.</span></span> <span data-ttu-id="7a2e2-149">W czasie testowania możemy jednak zastąpić implementację manekina, która nie rozmawia z bazą danych, ale zamiast tego przechowuje dane w pamięci.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-149">At test time, however, we can substitute a dummy implementation that doesn’t talk to the database but instead holds data in memory.</span></span> <span data-ttu-id="7a2e2-150">Ta implementacja manekina wyizoluje kod od niepowiązanych problemów w kodzie dostępu do danych lub konfiguracji bazy danych.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-150">This dummy implementation will isolate the code from unrelated problems in the data access code or database configuration.</span></span>

<span data-ttu-id="7a2e2-151">Istnieją dodatkowe korzyści dla izolacji.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-151">There are additional benefits to isolation.</span></span> <span data-ttu-id="7a2e2-152">Obliczenia biznesowe w ostatniej metodzie powinny potrwać tylko kilka milisekund do wykonania, ale sam test może działać przez kilka sekund, ponieważ kod przeskakuje wokół sieci i rozmawia z różnymi serwerami.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-152">The business calculation in the last method should only take a few milliseconds to execute, but the test itself might run for several seconds as the code hops around the network and talks to various servers.</span></span> <span data-ttu-id="7a2e2-153">Testy jednostkowe powinny działać szybko, aby ułatwić małe zmiany.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-153">Unit tests should run fast to facilitate small changes.</span></span> <span data-ttu-id="7a2e2-154">Testy jednostkowe powinny być również powtarzalne i nie zawodzą, ponieważ składnik niezwiązany z testem ma problem.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-154">Unit tests should also be repeatable and not fail because a component unrelated to the test has a problem.</span></span> <span data-ttu-id="7a2e2-155">Pisanie kodu, który jest łatwy do zaobserwowania i wyizolowania oznacza, że deweloperzy będą mieli łatwiejszy czas pisania testów dla kodu, spędzają mniej czasu na czekaniu na testy do wykonania, a co ważniejsze, spędzają mniej czasu na śledzeniu błędów, które nie istnieją.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-155">Writing code that is easy to observe and to isolate means developers will have an easier time writing tests for the code, spend less time waiting for tests to execute, and more importantly, spend less time tracking down bugs that do not exist.</span></span>

<span data-ttu-id="7a2e2-156">Mam nadzieję, że można docenić korzyści z testowania i zrozumieć cechy, które testowalne eksponatów kodu.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-156">Hopefully you can appreciate the benefits of testing and understand the qualities that testable code exhibits.</span></span> <span data-ttu-id="7a2e2-157">Mamy zamiar zająć się, jak napisać kod, który współpracuje z EF4, aby zapisać dane w bazie danych, pozostając obserwowalne i łatwe do wyizolowania, ale najpierw zawęzimy naszą uwagę, aby omówić testowalne projekty dostępu do danych.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-157">We are about to address how to write code that works with EF4 to save data into a database while remaining observable and easy to isolate, but first we’ll narrow our focus to discuss testable designs for data access.</span></span>

## <a name="design-patterns-for-data-persistence"></a><span data-ttu-id="7a2e2-158">Wzorce projektowe dla trwałości danych</span><span class="sxs-lookup"><span data-stu-id="7a2e2-158">Design Patterns for Data Persistence</span></span>

<span data-ttu-id="7a2e2-159">Oba złe przykłady przedstawione wcześniej miały zbyt wiele obowiązków.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-159">Both of the bad examples presented earlier had too many responsibilities.</span></span> <span data-ttu-id="7a2e2-160">Pierwszy zły przykład musiał wykonać obliczenia *i* zapisać do pliku.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-160">The first bad example had to perform a calculation *and* write to a file.</span></span> <span data-ttu-id="7a2e2-161">Drugi zły przykład musiał odczytać dane z bazy danych *i* wykonać obliczenia biznesowe *i* wysłać wiadomość e-mail.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-161">The second bad example had to read data from a database *and* perform a business calculation *and* send email.</span></span> <span data-ttu-id="7a2e2-162">Projektując mniejsze metody, które oddzielają obawy i delegują odpowiedzialność do innych składników, poczynisz duże postępy w kierunku pisania kodu testowalny.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-162">By designing smaller methods that separate concerns and delegate responsibility to other components you’ll make great strides towards writing testable code.</span></span> <span data-ttu-id="7a2e2-163">Celem jest tworzenie funkcjonalności przez komponowanie akcji z małych i skupionych abstrakcji.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-163">The goal is to build functionality by composing actions from small and focused abstractions.</span></span>

<span data-ttu-id="7a2e2-164">Jeśli chodzi o trwałość danych małe i skoncentrowane abstrakcje, których szukamy są tak powszechne, że zostały udokumentowane jako wzorce projektowe.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-164">When it comes to data persistence the small and focused abstractions we are looking for are so common they’ve been documented as design patterns.</span></span> <span data-ttu-id="7a2e2-165">Książka Martina Fowlera Patterns of Enterprise Application Architecture była pierwszą pracą opisującą te wzorce w druku.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-165">Martin Fowler’s book Patterns of Enterprise Application Architecture was the first work to describe these patterns in print.</span></span> <span data-ttu-id="7a2e2-166">Firma We'll provide a brief description of these patterns in the following sections before we show how these ADO.NET Entity Framework implements and works with these patterns.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-166">We’ll provide a brief description of these patterns in the following sections before we show how these ADO.NET Entity Framework implements and works with these patterns.</span></span>

### <a name="the-repository-pattern"></a><span data-ttu-id="7a2e2-167">Wzorzec repozytorium</span><span class="sxs-lookup"><span data-stu-id="7a2e2-167">The Repository Pattern</span></span>

<span data-ttu-id="7a2e2-168">Fowler mówi repozytorium "pośredniczy między warstwami mapowania domeny i danych za pomocą interfejsu przypominającego kolekcję do uzyskiwania dostępu do obiektów domeny".</span><span class="sxs-lookup"><span data-stu-id="7a2e2-168">Fowler says a repository “mediates between the domain and data mapping layers using a collection-like interface for accessing domain objects”.</span></span> <span data-ttu-id="7a2e2-169">Celem wzorca repozytorium jest wyizolowanie kodu od minutiae dostępu do danych, a jak widzieliśmy wcześniej izolacji jest wymagana cecha dla sprawdzalności.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-169">The goal of the repository pattern is to isolate code from the minutiae of data access, and as we saw earlier isolation is a required trait for testability.</span></span>

<span data-ttu-id="7a2e2-170">Kluczem do izolacji jest sposób, w jaki repozytorium udostępnia obiekty przy użyciu interfejsu podobnego do kolekcji.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-170">The key to the isolation is how the repository exposes objects using a collection-like interface.</span></span> <span data-ttu-id="7a2e2-171">Logika, którą piszesz do korzystania z repozytorium nie ma pojęcia, jak repozytorium zmaterializuje żądane obiekty.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-171">The logic you write to use the repository has no idea how the repository will materialize the objects you request.</span></span> <span data-ttu-id="7a2e2-172">Repozytorium może rozmawiać z bazą danych lub może po prostu zwrócić obiekty z kolekcji w pamięci.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-172">The repository might talk to a database, or it might just return objects from an in-memory collection.</span></span> <span data-ttu-id="7a2e2-173">Cały kod musi wiedzieć, jest to, że repozytorium wydaje się obsługiwać kolekcji i można pobrać, dodać i usunąć obiekty z kolekcji.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-173">All your code needs to know is that the repository appears to maintain the collection, and you can retrieve, add, and delete objects from the collection.</span></span>

<span data-ttu-id="7a2e2-174">W istniejących aplikacjach .NET repozytorium betonu często dziedziczy z ogólnego interfejsu, takiego jak:</span><span class="sxs-lookup"><span data-stu-id="7a2e2-174">In existing .NET applications a concrete repository often inherits from a generic interface like the following:</span></span>

``` csharp
    public interface IRepository<T> {       
        IEnumerable<T> FindAll();
        IEnumerable<T> FindBy(Expression<Func\<T, bool>> predicate);
        T FindById(int id);
        void Add(T newEntity);
        void Remove(T entity);
    }
```

<span data-ttu-id="7a2e2-175">Firma We'll make a few changes to the interface definition when we provide an implementation for EF4, but the basic concept remains the same.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-175">We’ll make a few changes to the interface definition when we provide an implementation for EF4, but the basic concept remains the same.</span></span> <span data-ttu-id="7a2e2-176">Kod można użyć konkretnego repozytorium implementujące ten interfejs, aby pobrać jednostkę według jego wartości klucza podstawowego, aby pobrać kolekcję jednostek na podstawie oceny predykatu lub po prostu pobrać wszystkie dostępne jednostki.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-176">Code can use a concrete repository implementing this interface to retrieve an entity by its primary key value, to retrieve a collection of entities based on the evaluation of a predicate, or simply retrieve all available entities.</span></span> <span data-ttu-id="7a2e2-177">Kod można również dodawać i usuwać jednostki za pośrednictwem interfejsu repozytorium.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-177">The code can also add and remove entities through the repository interface.</span></span>

<span data-ttu-id="7a2e2-178">Biorąc pod uwagę IRepository employee obiektów, kod można wykonać następujące operacje.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-178">Given an IRepository of Employee objects, code can perform the following operations.</span></span>

``` csharp
    var employeesNamedScott =
        repository
            .FindBy(e => e.Name == "Scott")
            .OrderBy(e => e.HireDate);
    var firstEmployee = repository.FindById(1);
    var newEmployee = new Employee() {/*... */};
    repository.Add(newEmployee);
```

<span data-ttu-id="7a2e2-179">Ponieważ kod jest przy użyciu interfejsu (IRepository pracownika), możemy dostarczyć kod z różnych implementacji interfejsu.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-179">Since the code is using an interface (IRepository of Employee), we can provide the code with different implementations of the interface.</span></span> <span data-ttu-id="7a2e2-180">Jedną z implementacji może być implementacja wspierana przez EF4 i utrwalanie obiektów w bazie danych programu Microsoft SQL Server.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-180">One implementation might be an implementation backed by EF4 and persisting objects into a Microsoft SQL Server database.</span></span> <span data-ttu-id="7a2e2-181">Inna implementacja (używana podczas testowania) może być poparta przez listę pracowników w pamięci obiektów.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-181">A different implementation (one we use during testing) might be backed by an in-memory List of Employee objects.</span></span> <span data-ttu-id="7a2e2-182">Interfejs pomoże osiągnąć izolację w kodzie.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-182">The interface will help to achieve isolation in the code.</span></span>

<span data-ttu-id="7a2e2-183">Zwróć uwagę, że&lt;interfejs&gt; IRepository T nie udostępnia operacji Zapisywania.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-183">Notice the IRepository&lt;T&gt; interface does not expose a Save operation.</span></span> <span data-ttu-id="7a2e2-184">Jak możemy zaktualizować istniejące obiekty?</span><span class="sxs-lookup"><span data-stu-id="7a2e2-184">How do we update existing objects?</span></span> <span data-ttu-id="7a2e2-185">Można natknąć się na definicje IRepository, które zawierają Save operacji i implementacje tych repozytoriów będzie musiał natychmiast utrwalić obiekt do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-185">You might come across IRepository definitions that do include the Save operation, and implementations of these repositories will need to immediately persist an object into the database.</span></span> <span data-ttu-id="7a2e2-186">Jednak w wielu aplikacjach nie chcemy zachowywać obiektów indywidualnie.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-186">However, in many applications we don’t want to persist objects individually.</span></span> <span data-ttu-id="7a2e2-187">Zamiast tego chcemy ożywić obiekty, być może z różnych repozytoriów, zmodyfikować te obiekty jako część działania biznesowego, a następnie utrwalić wszystkie obiekty w ramach pojedynczej operacji niepodzielnej.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-187">Instead, we want to bring objects to life, perhaps from different repositories, modify those objects as part of a business activity, and then persist all the objects as part of a single, atomic operation.</span></span> <span data-ttu-id="7a2e2-188">Na szczęście istnieje wzorzec, aby umożliwić tego typu zachowanie.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-188">Fortunately, there is a pattern to allow this type of behavior.</span></span>

### <a name="the-unit-of-work-pattern"></a><span data-ttu-id="7a2e2-189">Jednostka wzorca pracy</span><span class="sxs-lookup"><span data-stu-id="7a2e2-189">The Unit of Work Pattern</span></span>

<span data-ttu-id="7a2e2-190">Fowler mówi, że jednostka pracy będzie "prowadzić listę obiektów dotkniętych transakcją biznesową i koordynuje wypisanie zmian i rozwiązywanie problemów współbieżności".</span><span class="sxs-lookup"><span data-stu-id="7a2e2-190">Fowler says a unit of work will “maintain a list of objects affected by a business transaction and coordinates the writing out of changes and the resolution of concurrency problems”.</span></span> <span data-ttu-id="7a2e2-191">Obowiązkiem jednostki pracy jest śledzenie zmian w obiektach, które wprowadzamy do życia z repozytorium i utrwalanie wszelkich zmian, które wprowadziliśmy w obiektach, gdy mówimy jednostce pracy o zatwierdzeniu zmian.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-191">It is the responsibility of the unit of work to track changes to the objects we bring to life from a repository and persist any changes we’ve made to the objects when we tell the unit of work to commit the changes.</span></span> <span data-ttu-id="7a2e2-192">Jest to również odpowiedzialność jednostki pracy do podjęcia nowych obiektów dodaliśmy do wszystkich repozytoriów i wstawić obiekty do bazy danych, a także usuwanie mange.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-192">It’s also the responsibility of the unit of work to take the new objects we’ve added to all repositories and insert the objects into a database, and also mange deletion.</span></span>

<span data-ttu-id="7a2e2-193">Jeśli kiedykolwiek wykonałeś jakąkolwiek pracę z ADO.NET DataSets, będziesz już zaznajomiony z wzorcem pracy jednostki.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-193">If you’ve ever done any work with ADO.NET DataSets then you’ll already be familiar with the unit of work pattern.</span></span> <span data-ttu-id="7a2e2-194">ADO.NET DataSets miał możliwość śledzenia naszych aktualizacji, usunięcia i wstawiania obiektów DataRow i może (za pomocą TableAdapter) uzgodnić wszystkie nasze zmiany w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-194">ADO.NET DataSets had the ability to track our updates, deletions, and insertion of DataRow objects and could (with the help of a TableAdapter) reconcile all our changes to a database.</span></span> <span data-ttu-id="7a2e2-195">Jednak obiekty DataSet modelu rozłączony podzbiór podstawowej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-195">However, DataSet objects model a disconnected subset of the underlying database.</span></span> <span data-ttu-id="7a2e2-196">Jednostka wzorca pracy wykazuje to samo zachowanie, ale współpracuje z obiektów biznesowych i obiektów domeny, które są odizolowane od kodu dostępu do danych i nieświadomych bazy danych.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-196">The unit of work pattern exhibits the same behavior, but works with business objects and domain objects that are isolated from data access code and unaware of the database.</span></span>

<span data-ttu-id="7a2e2-197">Abstrakcja do modelowania jednostki pracy w kodzie .NET może wyglądać następująco:</span><span class="sxs-lookup"><span data-stu-id="7a2e2-197">An abstraction to model the unit of work in .NET code might look like the following:</span></span>

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<Order> Orders { get; }
        IRepository<Customer> Customers { get; }
        void Commit();
    }
```

<span data-ttu-id="7a2e2-198">Ujawniając referencje repozytorium z jednostki pracy możemy zapewnić, że jedna jednostka obiektu pracy ma możliwość śledzenia wszystkich jednostek zmaterializowanych podczas transakcji biznesowej.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-198">By exposing repository references from the unit of work we can ensure a single unit of work object has the ability to track all entities materialized during a business transaction.</span></span> <span data-ttu-id="7a2e2-199">Implementacja Commit metody dla rzeczywistej jednostki pracy jest, gdzie wszystkie magii dzieje się pogodzić zmiany w pamięci z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-199">The implementation of the Commit method for a real unit of work is where all the magic happens to reconcile in-memory changes with the database.</span></span> 

<span data-ttu-id="7a2e2-200">Biorąc pod uwagę odwołanie IUnitOfWork, kod można wprowadzać zmiany w obiektach biznesowych pobranych z jednego lub więcej repozytoriów i zapisać wszystkie zmiany przy użyciu operacji atomic Commit.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-200">Given an IUnitOfWork reference, code can make changes to business objects retrieved from one or more repositories and save all the changes using the atomic Commit operation.</span></span>

``` csharp
    var firstEmployee = unitofWork.Employees.FindById(1);
    var firstCustomer = unitofWork.Customers.FindById(1);
    firstEmployee.Name = "Alex";
    firstCustomer.Name = "Christopher";
    unitofWork.Commit();
```

### <a name="the-lazy-load-pattern"></a><span data-ttu-id="7a2e2-201">Wzorzec obciążenia z opóźnieniem</span><span class="sxs-lookup"><span data-stu-id="7a2e2-201">The Lazy Load Pattern</span></span>

<span data-ttu-id="7a2e2-202">Fowler używa nazwy lagzy load do opisania "obiektu, który nie zawiera wszystkich danych, których potrzebujesz, ale wie, jak je uzyskać".</span><span class="sxs-lookup"><span data-stu-id="7a2e2-202">Fowler uses the name lazy load to describe “an object that doesn’t contain all of the data you need but knows how to get it”.</span></span> <span data-ttu-id="7a2e2-203">Przezroczyste ładowanie z opóźnieniem jest ważną funkcją, która ma podczas pisania sprawdzanego kodu biznesowego i pracy z relacyjnej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-203">Transparent lazy loading is an important feature to have when writing testable business code and working with a relational database.</span></span> <span data-ttu-id="7a2e2-204">Jako przykład należy wziąć pod uwagę następujący kod.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-204">As an example, consider the following code.</span></span>

``` csharp
    var employee = repository.FindById(id);
    // ... and later ...
    foreach(var timeCard in employee.TimeCards) {
        // .. manipulate the timeCard
    }
```

<span data-ttu-id="7a2e2-205">Jak wypełnia się kolekcję TimeCards?</span><span class="sxs-lookup"><span data-stu-id="7a2e2-205">How is the TimeCards collection populated?</span></span> <span data-ttu-id="7a2e2-206">Istnieją dwie możliwe odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-206">There are two possible answers.</span></span> <span data-ttu-id="7a2e2-207">Jedną z odpowiedzi jest to, że repozytorium pracownika, gdy zostanie poproszony o pobranie pracownika, wystawia zapytanie w celu pobrania zarówno pracownika, jak i skojarzonych informacji o karcie czasu pracownika.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-207">One answer is that the employee repository, when asked to fetch an employee, issues a query to retrieve both the employee along with the employee’s associated time card information.</span></span> <span data-ttu-id="7a2e2-208">W relacyjnych baz danych zazwyczaj wymaga to kwerendy z klauzulą JOIN i może spowodować pobranie większej ilości informacji niż wymaga to aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-208">In relational databases this generally requires a query with a JOIN clause and may result in retrieving more information than an application needs.</span></span> <span data-ttu-id="7a2e2-209">Co zrobić, jeśli aplikacja nigdy nie musi dotykać timecards właściwości?</span><span class="sxs-lookup"><span data-stu-id="7a2e2-209">What if the application never needs to touch the TimeCards property?</span></span>

<span data-ttu-id="7a2e2-210">Drugą odpowiedzią jest załadowanie właściwości TimeCards "na żądanie".</span><span class="sxs-lookup"><span data-stu-id="7a2e2-210">A second answer is to load the TimeCards property “on demand”.</span></span> <span data-ttu-id="7a2e2-211">To ładowanie z opóźnieniem jest niejawne i przezroczyste dla logiki biznesowej, ponieważ kod nie wywołuje specjalnych interfejsów API w celu pobrania informacji o karcie czasu.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-211">This lazy loading is implicit and transparent to the business logic because the code does not invoke special APIs to retrieve time card information.</span></span> <span data-ttu-id="7a2e2-212">Kod zakłada, że informacje o karcie czasu są obecne w razie potrzeby.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-212">The code assumes the time card information is present when needed.</span></span> <span data-ttu-id="7a2e2-213">Istnieje kilka magii związane z ładowaniem z opóźnieniem, który zazwyczaj obejmuje przechwytywanie środowiska uruchomieniowego wywołań metody.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-213">There is some magic involved with lazy loading that generally involves runtime interception of method invocations.</span></span> <span data-ttu-id="7a2e2-214">Kod przechwytujący jest odpowiedzialny za rozmowy z bazą danych i pobieranie informacji o karcie czasu, pozostawiając logikę biznesową za darmo logiki biznesowej.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-214">The intercepting code is responsible for talking to the database and retrieving time card information while leaving the business logic free to be business logic.</span></span> <span data-ttu-id="7a2e2-215">Ta magia obciążenia z opóźnieniem umożliwia kod biznesowy izolować się od operacji pobierania danych i powoduje, że kod jest bardziej sprawdzalny.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-215">This lazy load magic allows the business code to isolate itself from data retrieval operations and results in more testable code.</span></span>

<span data-ttu-id="7a2e2-216">Wadą z opóźnieniem obciążenia jest to, że gdy aplikacja *potrzebuje* informacji o karcie czasu kod wykona dodatkowe zapytanie.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-216">The drawback to a lazy load is that when an application *does* need the time card information the code will execute an additional query.</span></span> <span data-ttu-id="7a2e2-217">Nie jest to problemem dla wielu aplikacji, ale dla aplikacji zależnych od wydajności lub aplikacji zapętlania przez szereg obiektów pracownika i wykonywania kwerendy do pobierania kart czasu podczas każdej iteracji pętli (problem często określane jako problem kwerendy N + 1), ładowanie z opóźnieniem jest przeciąganie.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-217">This isn’t a concern for many applications, but for performance sensitive applications or applications looping through a number of employee objects and executing a query to retrieve time cards during each iteration of the loop (a problem often referred to as the N+1 query problem), lazy loading is a drag.</span></span> <span data-ttu-id="7a2e2-218">W tych scenariuszach aplikacja może chcieć chętnie załadować informacje o karcie czasu w najbardziej efektywny sposób.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-218">In these scenarios an application might want to eagerly load time card information in the most efficient manner possible.</span></span>

<span data-ttu-id="7a2e2-219">Na szczęście zobaczymy, jak EF4 obsługuje zarówno niejawne obciążenia leniwe i wydajne obciążenia chętnych, jak przenieść się do następnej sekcji i zaimplementować te wzorce.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-219">Fortunately, we’ll see how EF4 supports both implicit lazy loads and efficient eager loads as we move into the next section and implement these patterns.</span></span>

## <a name="implementing-patterns-with-the-entity-framework"></a><span data-ttu-id="7a2e2-220">Wdrażanie wzorców z platformą encji</span><span class="sxs-lookup"><span data-stu-id="7a2e2-220">Implementing Patterns with the Entity Framework</span></span>

<span data-ttu-id="7a2e2-221">Dobrą wiadomością jest to, że wszystkie wzorce projektowe, które opisaliśmy w ostatniej sekcji, są proste do zaimplementowania za pomocą EF4.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-221">The good news is that all of the design patterns we described in the last section are straightforward to implement with EF4.</span></span> <span data-ttu-id="7a2e2-222">Aby zademonstrować, zamierzamy użyć prostej aplikacji ASP.NET MVC do edycji i wyświetlania pracowników i powiązanych z nimi informacji o karcie czasu.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-222">To demonstrate we are going to use a simple ASP.NET MVC application to edit and display Employees and their associated time card information.</span></span> <span data-ttu-id="7a2e2-223">Zaczniemy od użycia następujących "zwykłych starych obiektów CLR" (POCO).</span><span class="sxs-lookup"><span data-stu-id="7a2e2-223">We’ll start by using the following “plain old CLR objects” (POCOs).</span></span> 

``` csharp
    public class Employee {
        public int Id { get; set; }
        public string Name { get; set; }
        public DateTime HireDate { get; set; }
        public ICollection<TimeCard> TimeCards { get; set; }
    }

    public class TimeCard {
        public int Id { get; set; }
        public int Hours { get; set; }
        public DateTime EffectiveDate { get; set; }
    }
```

<span data-ttu-id="7a2e2-224">Te definicje klas zmieni się nieco, jak możemy zbadać różne podejścia i funkcje EF4, ale celem jest utrzymanie tych klas jako trwałości ignorantów (PI), jak to możliwe.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-224">These class definitions will change slightly as we explore different approaches and features of EF4, but the intent is to keep these classes as persistence ignorant (PI) as possible.</span></span> <span data-ttu-id="7a2e2-225">Obiekt PI nie wie, *jak*, a nawet *jeśli*, stan posiada mieszka wewnątrz bazy danych.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-225">A PI object doesn’t know *how*, or even *if*, the state it holds lives inside a database.</span></span> <span data-ttu-id="7a2e2-226">PI i POCO idą w parze z testowalnym oprogramowaniem.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-226">PI and POCOs go hand in hand with testable software.</span></span> <span data-ttu-id="7a2e2-227">Obiekty przy użyciu metody POCO są mniej ograniczone, bardziej elastyczne i łatwiejsze do testowania, ponieważ mogą działać bez bazy danych.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-227">Objects using a POCO approach are less constrained, more flexible, and easier to test because they can operate without a database present.</span></span>

<span data-ttu-id="7a2e2-228">Z POCO w miejscu możemy utworzyć model danych jednostki (EDM) w programie Visual Studio (patrz rysunek 1).</span><span class="sxs-lookup"><span data-stu-id="7a2e2-228">With the POCOs in place we can create an Entity Data Model (EDM) in Visual Studio (see figure 1).</span></span> <span data-ttu-id="7a2e2-229">Nie będziemy używać EDM do generowania kodu dla naszych podmiotów.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-229">We will not use the EDM to generate code for our entities.</span></span> <span data-ttu-id="7a2e2-230">Zamiast tego chcemy korzystać z jednostek, które pięknie twórzmy ręcznie.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-230">Instead, we want to use the entities we lovingly craft by hand.</span></span> <span data-ttu-id="7a2e2-231">Będziemy używać EDM tylko do generowania naszego schematu bazy danych i podać metadane EF4 musi mapować obiekty do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-231">We will only use the EDM to generate our database schema and provide the metadata EF4 needs to map objects into the database.</span></span>

![ef test_01](~/ef6/media/eftest-01.jpg)

<span data-ttu-id="7a2e2-233">**Rysunek 1.**</span><span class="sxs-lookup"><span data-stu-id="7a2e2-233">**Figure 1**</span></span>

<span data-ttu-id="7a2e2-234">Uwaga: jeśli chcesz najpierw opracować model EDM, możliwe jest wygenerowanie czystego kodu POCO z EDM.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-234">Note: if you want to develop the EDM model first, it is possible to generate clean, POCO code from the EDM.</span></span> <span data-ttu-id="7a2e2-235">Można to zrobić za pomocą rozszerzenia programu Visual Studio 2010 dostarczonego przez zespół programowalności danych.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-235">You can do this with a Visual Studio 2010 extension provided by the Data Programmability team.</span></span> <span data-ttu-id="7a2e2-236">Aby pobrać rozszerzenie, uruchom Menedżera rozszerzeń z menu Narzędzia w programie Visual Studio i wyszukaj w galerii online szablonów dla "POCO" (zobacz rysunek 2).</span><span class="sxs-lookup"><span data-stu-id="7a2e2-236">To download the extension, launch the Extension Manager from the Tools menu in Visual Studio and search the online gallery of templates for “POCO” (See figure 2).</span></span> <span data-ttu-id="7a2e2-237">Istnieje kilka szablonów POCO dostępnych dla EF.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-237">There are several POCO templates available for EF.</span></span> <span data-ttu-id="7a2e2-238">Aby uzyskać więcej informacji na temat korzystania z szablonu, zobacz " [Instruktaż: Szablon POCO dla entity framework](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-poco-template-for-the-entity-framework)".</span><span class="sxs-lookup"><span data-stu-id="7a2e2-238">For more information on using the template, see “ [Walkthrough: POCO Template for the Entity Framework](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-poco-template-for-the-entity-framework)”.</span></span>

![ef test_02](~/ef6/media/eftest-02.png)

<span data-ttu-id="7a2e2-240">**Rysunek 2**</span><span class="sxs-lookup"><span data-stu-id="7a2e2-240">**Figure 2**</span></span>

<span data-ttu-id="7a2e2-241">Z tego punktu wyjścia POCO zbadamy dwa różne podejścia do kodu testowalny.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-241">From this POCO starting point we will explore two different approaches to testable code.</span></span> <span data-ttu-id="7a2e2-242">Pierwsze podejście, które nazywam podejściem EF, ponieważ wykorzystuje abstrakcje z interfejsu API programu Entity Framework do implementacji jednostek pracy i repozytoriów.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-242">The first approach I call the EF approach because it leverages abstractions from the Entity Framework API to implement units of work and repositories.</span></span> <span data-ttu-id="7a2e2-243">W drugim podejściu utworzymy własne niestandardowe abstrakcje repozytorium, a następnie zobaczymy zalety i wady każdego podejścia.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-243">In the second approach we will create our own custom repository abstractions and then see the advantages and disadvantages of each approach.</span></span> <span data-ttu-id="7a2e2-244">Zaczniemy od zbadania podejścia EF.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-244">We’ll start by exploring the EF approach.</span></span>  

### <a name="an-ef-centric-implementation"></a><span data-ttu-id="7a2e2-245">Wdrożenie ef centric</span><span class="sxs-lookup"><span data-stu-id="7a2e2-245">An EF Centric Implementation</span></span>

<span data-ttu-id="7a2e2-246">Należy wziąć pod uwagę następujące działania kontrolera z ASP.NET projektu MVC.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-246">Consider the following controller action from an ASP.NET MVC project.</span></span> <span data-ttu-id="7a2e2-247">Akcja pobiera Employee obiektu i zwraca wynik, aby wyświetlić szczegółowy widok pracownika.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-247">The action retrieves an Employee object and returns a result to display a detailed view of the employee.</span></span>

``` csharp
    public ViewResult Details(int id) {
        var employee = _unitOfWork.Employees
                                  .Single(e => e.Id == id);
        return View(employee);
    }
```

<span data-ttu-id="7a2e2-248">Czy kod jest sprawdzalny?</span><span class="sxs-lookup"><span data-stu-id="7a2e2-248">Is the code testable?</span></span> <span data-ttu-id="7a2e2-249">Istnieją co najmniej dwa testy, które musimy zweryfikować zachowanie akcji.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-249">There are at least two tests we’d need to verify the action’s behavior.</span></span> <span data-ttu-id="7a2e2-250">Najpierw chcielibyśmy sprawdzić, czy akcja zwraca poprawny widok – łatwy test.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-250">First, we’d like to verify the action returns the correct view – an easy test.</span></span> <span data-ttu-id="7a2e2-251">Chcielibyśmy również napisać test, aby sprawdzić, czy akcja pobiera poprawnego pracownika i chcielibyśmy to zrobić bez wykonywania kodu do kwerendy bazy danych.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-251">We’d also want to write a test to verify the action retrieves the correct employee, and we’d like to do this without executing code to query the database.</span></span> <span data-ttu-id="7a2e2-252">Pamiętaj, że chcemy wyizolować testowany kod.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-252">Remember we want to isolate the code under test.</span></span> <span data-ttu-id="7a2e2-253">Izolacja zapewni, że test nie zakończy się niepowodzeniem z powodu błędu w kodzie dostępu do danych lub konfiguracji bazy danych.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-253">Isolation will ensure the test doesn’t fail because of a bug in the data access code or database configuration.</span></span> <span data-ttu-id="7a2e2-254">Jeśli test zakończy się niepowodzeniem, będziemy wiedzieć, że mamy błąd w logice kontrolera, a nie w niektórych składnikach systemu niższego poziomu.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-254">If the test fails, we will know we have a bug in the controller logic, and not in some lower level system component.</span></span>

<span data-ttu-id="7a2e2-255">Aby osiągnąć izolację, potrzebujemy kilka abstrakcji, takich jak interfejsy, które przedstawiliśmy wcześniej dla repozytoriów i jednostek pracy.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-255">To achieve isolation we’ll need some abstractions like the interfaces we presented earlier for repositories and units of work.</span></span> <span data-ttu-id="7a2e2-256">Pamiętaj, że wzorzec repozytorium jest przeznaczony do pośredniczenia między obiektami domeny a warstwą mapowania danych.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-256">Remember the repository pattern is designed to mediate between domain objects and the data mapping layer.</span></span> <span data-ttu-id="7a2e2-257">W tym scenariuszu EF4 *jest* warstwą mapowania danych i już zapewnia abstrakcję&lt;&gt; podobną do repozytorium o nazwie IObjectSet T (z obszaru nazw System.Data.Objects).</span><span class="sxs-lookup"><span data-stu-id="7a2e2-257">In this scenario EF4 *is* the data mapping layer, and already provides a repository-like abstraction named IObjectSet&lt;T&gt; (from the System.Data.Objects namespace).</span></span> <span data-ttu-id="7a2e2-258">Definicja interfejsu wygląda następująco.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-258">The interface definition looks like the following.</span></span>

``` csharp
    public interface IObjectSet<TEntity> :
                     IQueryable<TEntity>,
                     IEnumerable<TEntity>,
                     IQueryable,
                     IEnumerable
                     where TEntity : class
    {
        void AddObject(TEntity entity);
        void Attach(TEntity entity);
        void DeleteObject(TEntity entity);
        void Detach(TEntity entity);
    }
```

<span data-ttu-id="7a2e2-259">IObjectSet&lt;&gt; T spełnia wymagania dla repozytorium, ponieważ przypomina kolekcję obiektów (za&lt;&gt;pośrednictwem IEnumerable T) i udostępnia metody dodawania i usuwania obiektów z symulowanej kolekcji.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-259">IObjectSet&lt;T&gt; meets the requirements for a repository because it resembles a collection of objects (via IEnumerable&lt;T&gt;) and provides methods to add and remove objects from the simulated collection.</span></span> <span data-ttu-id="7a2e2-260">Metody Dołączania i odłączania udostępniają dodatkowe możliwości interfejsu API EF4.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-260">The Attach and Detach methods expose additional capabilities of the EF4 API.</span></span> <span data-ttu-id="7a2e2-261">Aby użyć IObjectSet&lt;T&gt; jako interfejsu dla repozytoriów potrzebujemy jednostki abstrakcji pracy do powiązania repozytoriów razem.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-261">To use IObjectSet&lt;T&gt; as the interface for repositories we need a unit of work abstraction to bind repositories together.</span></span>

``` csharp
    public interface IUnitOfWork {
        IObjectSet<Employee> Employees { get; }
        IObjectSet<TimeCard> TimeCards { get; }
        void Commit();
    }
```

<span data-ttu-id="7a2e2-262">Jedna konkretna implementacja tego interfejsu będzie rozmawiać z sql server i jest łatwe do utworzenia przy użyciu ObjectContext klasy z EF4.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-262">One concrete implementation of this interface will talk to SQL Server and is easy to create using the ObjectContext class from EF4.</span></span> <span data-ttu-id="7a2e2-263">ObjectContext Klasa jest rzeczywistą jednostką pracy w interfejsie API EF4.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-263">The ObjectContext class is the real unit of work in the EF4 API.</span></span>

``` csharp
    public class SqlUnitOfWork : IUnitOfWork {
        public SqlUnitOfWork() {
            var connectionString =
                ConfigurationManager
                    .ConnectionStrings[ConnectionStringName]
                    .ConnectionString;
            _context = new ObjectContext(connectionString);
        }

        public IObjectSet<Employee> Employees {
            get { return _context.CreateObjectSet<Employee>(); }
        }

        public IObjectSet<TimeCard> TimeCards {
            get { return _context.CreateObjectSet<TimeCard>(); }
        }

        public void Commit() {
            _context.SaveChanges();
        }

        readonly ObjectContext _context;
        const string ConnectionStringName = "EmployeeDataModelContainer";
    }
```

<span data-ttu-id="7a2e2-264">Wprowadzenie IObjectSet&lt;&gt; T do życia jest tak proste, jak wywoływanie CreateObjectSet metody ObjectContext obiektu.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-264">Bringing an IObjectSet&lt;T&gt; to life is as easy as invoking the CreateObjectSet method of the ObjectContext object.</span></span> <span data-ttu-id="7a2e2-265">Za kulisami ramach będzie używać metadanych, które pod warunkiem w&lt;&gt;EDM do produkcji konkretnego ObjectSet T .</span><span class="sxs-lookup"><span data-stu-id="7a2e2-265">Behind the scenes the framework will use the metadata we provided in the EDM to produce a concrete ObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="7a2e2-266">Będziemy trzymać się zwracania interfejsu IObjectSet&lt;T,&gt; ponieważ pomoże zachować możliwości testowania w kodzie klienta.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-266">We’ll stick with returning the IObjectSet&lt;T&gt; interface because it will help preserve testability in client code.</span></span>

<span data-ttu-id="7a2e2-267">Ta konkretna implementacja jest przydatna w produkcji, ale musimy skupić się na tym, jak będziemy używać naszej abstrakcji IUnitOfWork w celu ułatwienia testowania.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-267">This concrete implementation is useful in production, but we need to focus on how we’ll use our IUnitOfWork abstraction to facilitate testing.</span></span>

### <a name="the-test-doubles"></a><span data-ttu-id="7a2e2-268">Test podwaja</span><span class="sxs-lookup"><span data-stu-id="7a2e2-268">The Test Doubles</span></span>

<span data-ttu-id="7a2e2-269">Aby wyizolować akcję kontrolera, potrzebujemy możliwości przełączania się między rzeczywistą jednostką pracy (popartą przez ObjectContext) a testową podwójną lub "fałszywą" jednostką pracy (wykonywanie operacji w pamięci).</span><span class="sxs-lookup"><span data-stu-id="7a2e2-269">To isolate the controller action we’ll need the ability to switch between the real unit of work (backed by an ObjectContext) and a test double or “fake” unit of work (performing in-memory operations).</span></span> <span data-ttu-id="7a2e2-270">Typowym podejściem do wykonywania tego typu przełączania jest nie pozwolić kontrolerowi MVC na tworzenie wystąpienia jednostki pracy, ale zamiast tego przekazać jednostkę pracy do kontrolera jako parametr konstruktora.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-270">The common approach to perform this type of switching is to not let the MVC controller instantiate a unit of work, but instead pass the unit of work into the controller as a constructor parameter.</span></span>

``` csharp
    class EmployeeController : Controller {
      publicEmployeeController(IUnitOfWork unitOfWork)  {
          _unitOfWork = unitOfWork;
      }
      ...
    }
```

<span data-ttu-id="7a2e2-271">Powyższy kod jest przykładem iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-271">The above code is an example of dependency injection.</span></span> <span data-ttu-id="7a2e2-272">Nie zezwalamy kontrolerowi na tworzenie zależności (jednostki pracy), ale wstrzykujemy zależność do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-272">We don’t allow the controller to create it’s dependency (the unit of work) but inject the dependency into the controller.</span></span> <span data-ttu-id="7a2e2-273">W projekcie MVC jest wspólne użycie fabryki kontrolera niestandardowego w połączeniu z kontenerem inwersji kontroli (IoC) do automatyzacji iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-273">In an MVC project it is common to use a custom controller factory in combination with an inversion of control (IoC) container to automate dependency injection.</span></span> <span data-ttu-id="7a2e2-274">Te tematy wykraczają poza zakres tego artykułu, ale możesz przeczytać więcej, wykonując odwołania na końcu tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-274">These topics are beyond the scope of this article, but you can read more by following the references at the end of this article.</span></span>

<span data-ttu-id="7a2e2-275">Fałszywa jednostka implementacji pracy, której możemy użyć do testowania, może wyglądać następująco.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-275">A fake unit of work implementation that we can use for testing might look like the following.</span></span>

``` csharp
    public class InMemoryUnitOfWork : IUnitOfWork {
        public InMemoryUnitOfWork() {
            Committed = false;
        }
        public IObjectSet<Employee> Employees {
            get;
            set;
        }

        public IObjectSet<TimeCard> TimeCards {
            get;
            set;
        }

        public bool Committed { get; set; }
        public void Commit() {
            Committed = true;
        }
    }
```

<span data-ttu-id="7a2e2-276">Zwróć uwagę, że fałszywa jednostka pracy udostępnia właściwość Commited.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-276">Notice the fake unit of work exposes a Commited property.</span></span> <span data-ttu-id="7a2e2-277">Czasami warto dodać funkcje do fałszywej klasy, które ułatwiają testowanie.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-277">It’s sometimes useful to add features to a fake class that facilitate testing.</span></span> <span data-ttu-id="7a2e2-278">W takim przypadku jest łatwe do zaobserwowania, jeśli kod zatwierdza jednostkę pracy, sprawdzając Commited właściwości.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-278">In this case it is easy to observe if code commits a unit of work by checking the Commited property.</span></span>

<span data-ttu-id="7a2e2-279">Będziemy również potrzebować fałszywego IObjectSet&lt;T&gt; do przechowywania obiektów employee i timecard w pamięci.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-279">We’ll also need a fake IObjectSet&lt;T&gt; to hold Employee and TimeCard objects in memory.</span></span> <span data-ttu-id="7a2e2-280">Możemy zapewnić pojedynczą implementację przy użyciu generycznych.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-280">We can provide a single implementation using generics.</span></span>

``` csharp
    public class InMemoryObjectSet<T> : IObjectSet<T> where T : class
        public InMemoryObjectSet()
            : this(Enumerable.Empty<T>()) {
        }
        public InMemoryObjectSet(IEnumerable<T> entities) {
            _set = new HashSet<T>();
            foreach (var entity in entities) {
                _set.Add(entity);
            }
            _queryableSet = _set.AsQueryable();
        }
        public void AddObject(T entity) {
            _set.Add(entity);
        }
        public void Attach(T entity) {
            _set.Add(entity);
        }
        public void DeleteObject(T entity) {
            _set.Remove(entity);
        }
        public void Detach(T entity) {
            _set.Remove(entity);
        }
        public Type ElementType {
            get { return _queryableSet.ElementType; }
        }
        public Expression Expression {
            get { return _queryableSet.Expression; }
        }
        public IQueryProvider Provider {
            get { return _queryableSet.Provider; }
        }
        public IEnumerator<T> GetEnumerator() {
            return _set.GetEnumerator();
        }
        IEnumerator IEnumerable.GetEnumerator() {
            return GetEnumerator();
        }

        readonly HashSet<T> _set;
        readonly IQueryable<T> _queryableSet;
    }
```

<span data-ttu-id="7a2e2-281">Ten test dwukrotnie deleguje większość swojej pracy&lt;&gt; do podstawowej HashSet T obiektu.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-281">This test double delegates most of its work to an underlying HashSet&lt;T&gt; object.</span></span> <span data-ttu-id="7a2e2-282">Należy zauważyć, że&lt;IObjectSet&gt; T wymaga ogólnego ograniczenia wymuszającego T jako klasę (typ odwołania), a także zmusza nas do zaimplementowania IQueryable&lt;T&gt;.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-282">Note that IObjectSet&lt;T&gt; requires a generic constraint enforcing T as a class (a reference type), and also forces us to implement IQueryable&lt;T&gt;.</span></span> <span data-ttu-id="7a2e2-283">Łatwo jest dokonać kolekcji w pamięci pojawiają się&lt;jako&gt; IQueryable T przy użyciu standardowego operatora LINQ AsQueryable.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-283">It is easy to make an in-memory collection appear as an IQueryable&lt;T&gt; using the standard LINQ operator AsQueryable.</span></span>

### <a name="the-tests"></a><span data-ttu-id="7a2e2-284">Testy</span><span class="sxs-lookup"><span data-stu-id="7a2e2-284">The Tests</span></span>

<span data-ttu-id="7a2e2-285">Tradycyjne testy jednostkowe użyją jednej klasy testowej do przechowywania wszystkich testów dla wszystkich akcji w jednym kontrolerze MVC.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-285">Traditional unit tests will use a single test class to hold all of the tests for all of the actions in a single MVC controller.</span></span> <span data-ttu-id="7a2e2-286">Możemy napisać te testy lub dowolny rodzaj testu jednostkowego, używając sfałszowanych w pamięci, które stworzyliśmy.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-286">We can write these tests, or any type of unit test, using the in memory fakes we’ve built.</span></span> <span data-ttu-id="7a2e2-287">Jednak w tym artykule unikniemy monolitycznego podejścia klasy testowej i zamiast tego zgrupujemy nasze testy, aby skupić się na określonej funkcji.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-287">However, for this article we will avoid the monolithic test class approach and instead group our tests to focus on a specific piece of functionality.</span></span><span data-ttu-id="7a2e2-288">Na przykład "utwórz nowego pracownika" może być funkcje, które chcemy przetestować, więc użyjemy jednej klasy testowej, aby zweryfikować akcję pojedynczego kontrolera odpowiedzialną za tworzenie nowego pracownika.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-288">  For example, “create new employee” might be the functionality we want to test, so we will use a single test class to verify the single controller action responsible for creating a new employee.</span></span>

<span data-ttu-id="7a2e2-289">Istnieje kilka typowych kodu konfiguracji, którego potrzebujemy dla wszystkich tych klas testów drobnoziarnistych.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-289">There is some common setup code we need for all these fine grained test classes.</span></span> <span data-ttu-id="7a2e2-290">Na przykład zawsze musimy tworzyć nasze repozytoria w pamięci i fałszywe jednostki pracy.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-290">For example, we always need to create our in-memory repositories and fake unit of work.</span></span> <span data-ttu-id="7a2e2-291">Potrzebujemy również wystąpienia kontrolera pracownika z fałszywą jednostką pracy wstrzykniętą.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-291">We also need an instance of the employee controller with the fake unit of work injected.</span></span> <span data-ttu-id="7a2e2-292">Udostępnimy ten wspólny kod konfiguracji w klasach testowych przy użyciu klasy podstawowej.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-292">We’ll share this common setup code across test classes by using a base class.</span></span>

``` csharp
    public class EmployeeControllerTestBase {
        public EmployeeControllerTestBase() {
            _employeeData = EmployeeObjectMother.CreateEmployees()
                                                .ToList();
            _repository = new InMemoryObjectSet<Employee>(_employeeData);
            _unitOfWork = new InMemoryUnitOfWork();
            _unitOfWork.Employees = _repository;
            _controller = new EmployeeController(_unitOfWork);
        }

        protected IList<Employee> _employeeData;
        protected EmployeeController _controller;
        protected InMemoryObjectSet<Employee> _repository;
        protected InMemoryUnitOfWork _unitOfWork;
    }
```

<span data-ttu-id="7a2e2-293">"Matka obiektu" używamy w klasie podstawowej jest jeden wspólny wzorzec do tworzenia danych testowych.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-293">The “object mother” we use in the base class is one common pattern for creating test data.</span></span> <span data-ttu-id="7a2e2-294">Macierzysta obiekt zawiera metody fabryczne do tworzenia wystąpienia jednostek testowych do użycia w wielu urządzeniach testowych.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-294">An object mother contains factory methods to instantiate test entities for use across multiple test fixtures.</span></span>

``` csharp
    public static class EmployeeObjectMother {
        public static IEnumerable<Employee> CreateEmployees() {
            yield return new Employee() {
                Id = 1, Name = "Scott", HireDate=new DateTime(2002, 1, 1)
            };
            yield return new Employee() {
                Id = 2, Name = "Poonam", HireDate=new DateTime(2001, 1, 1)
            };
            yield return new Employee() {
                Id = 3, Name = "Simon", HireDate=new DateTime(2008, 1, 1)
            };
        }
        // ... more fake data for different scenarios
    }
```

<span data-ttu-id="7a2e2-295">Możemy użyć EmployeeControllerTestBase jako klasy podstawowej dla wielu urządzeń testowych (patrz rysunek 3).</span><span class="sxs-lookup"><span data-stu-id="7a2e2-295">We can use the EmployeeControllerTestBase as the base class for a number of test fixtures (see figure 3).</span></span> <span data-ttu-id="7a2e2-296">Każde urządzenie testowe przetestuje określoną akcję kontrolera.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-296">Each test fixture will test a specific controller action.</span></span> <span data-ttu-id="7a2e2-297">Na przykład jeden element testu skupi się na testowaniu akcji Utwórz używanej podczas żądania HTTP GET (aby wyświetlić widok do tworzenia pracownika), a inne urządzenie skoncentruje się na akcji Utwórz używanej w żądaniu HTTP POST (aby uzyskać informacje przesłane przez użytkownika w celu utworzenia pracownika).</span><span class="sxs-lookup"><span data-stu-id="7a2e2-297">For example, one test fixture will focus on testing the Create action used during an HTTP GET request (to display the view for creating an employee), and a different fixture will focus on the Create action used in an HTTP POST request (to take information submitted by the user to create an employee).</span></span> <span data-ttu-id="7a2e2-298">Każda klasa pochodna jest odpowiedzialny tylko za konfigurację potrzebne w określonym kontekście i do zapewnienia potwierdzeń potrzebnych do weryfikacji wyników dla jego kontekstu określonego testu.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-298">Each derived class is only responsible for the setup needed in its specific context, and to provide the assertions needed to verify the outcomes for its specific test context.</span></span>

![ef test_03](~/ef6/media/eftest-03.png)

<span data-ttu-id="7a2e2-300">**Rysunek 3**</span><span class="sxs-lookup"><span data-stu-id="7a2e2-300">**Figure 3**</span></span>

<span data-ttu-id="7a2e2-301">Konwencja nazewnictwa i styl testu przedstawione w tym miejscu nie jest wymagane dla kodu sprawdzalne — to tylko jedno podejście.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-301">The naming convention and test style presented here isn’t required for testable code – it’s just one approach.</span></span> <span data-ttu-id="7a2e2-302">Rysunek 4 przedstawia testy uruchomione w wtyczce jet brains resharper test runner dla programu Visual Studio 2010.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-302">Figure 4 shows the tests running in the Jet Brains Resharper test runner plugin for Visual Studio 2010.</span></span>

![ef test_04](~/ef6/media/eftest-04.png)

<span data-ttu-id="7a2e2-304">**Rysunek 4**</span><span class="sxs-lookup"><span data-stu-id="7a2e2-304">**Figure 4**</span></span>

<span data-ttu-id="7a2e2-305">Z klasy podstawowej do obsługi udostępnionego kodu konfiguracji, testy jednostkowe dla każdej akcji kontrolera są małe i łatwe do zapisu.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-305">With a base class to handle the shared setup code, the unit tests for each controller action are small and easy to write.</span></span> <span data-ttu-id="7a2e2-306">Testy będą wykonywane szybko (ponieważ wykonujemy operacje w pamięci) i nie powinny zakończyć się niepowodzeniem z powodu niepowiązanych problemów z infrastrukturą lub środowiskiem (ponieważ wyizolowaliśmy testową jednostkę).</span><span class="sxs-lookup"><span data-stu-id="7a2e2-306">The tests will execute quickly (since we are performing in-memory operations), and shouldn’t fail because of unrelated infrastructure or environmental concerns (because we’ve isolated the unit under test).</span></span>

``` csharp
    [TestClass]
    public class EmployeeControllerCreateActionPostTests
               : EmployeeControllerTestBase {
        [TestMethod]
        public void ShouldAddNewEmployeeToRepository() {
            _controller.Create(_newEmployee);
            Assert.IsTrue(_repository.Contains(_newEmployee));
        }
        [TestMethod]
        public void ShouldCommitUnitOfWork() {
            _controller.Create(_newEmployee);
            Assert.IsTrue(_unitOfWork.Committed);
        }
        // ... more tests

        Employee _newEmployee = new Employee() {
            Name = "NEW EMPLOYEE",
            HireDate = new System.DateTime(2010, 1, 1)
        };
    }
```

<span data-ttu-id="7a2e2-307">W tych testach klasa podstawowa wykonuje większość pracy instalatora.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-307">In these tests, the base class does most of the setup work.</span></span> <span data-ttu-id="7a2e2-308">Pamiętaj konstruktor klasy podstawowej tworzy repozytorium w pamięci, fałszywe jednostki pracy i wystąpienie EmployeeController klasy.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-308">Remember the base class constructor creates the in-memory repository, a fake unit of work, and an instance of the EmployeeController class.</span></span> <span data-ttu-id="7a2e2-309">Klasa testowa pochodzi z tej klasy podstawowej i koncentruje się na specyfiki testowania Create metody.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-309">The test class derives from this base class and focuses on the specifics of testing the Create method.</span></span> <span data-ttu-id="7a2e2-310">W takim przypadku szczegóły sprowadzają się do kroków "rozmieszczaj, działaj i assertuj", które zobaczysz w dowolnej procedurze testowania jednostkowego:</span><span class="sxs-lookup"><span data-stu-id="7a2e2-310">In this case the specifics boil down to the “arrange, act, and assert” steps you’ll see in any unit testing procedure:</span></span>

-   <span data-ttu-id="7a2e2-311">Utwórz nowyObażyć obiekt do symulacji przychodzących danych.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-311">Create a newEmployee object to simulate incoming data.</span></span>
-   <span data-ttu-id="7a2e2-312">Wywołaj Create akcji EmployeeController i przekazać w newEmployee.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-312">Invoke the Create action of the EmployeeController and pass in the newEmployee.</span></span>
-   <span data-ttu-id="7a2e2-313">Sprawdź, czy akcja Utwórz daje oczekiwane wyniki (pracownik pojawia się w repozytorium).</span><span class="sxs-lookup"><span data-stu-id="7a2e2-313">Verify the Create action produces the expected results (the employee appears in the repository).</span></span>

<span data-ttu-id="7a2e2-314">To, co zbudowaliśmy, pozwala nam przetestować dowolną z akcji EmployeeController.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-314">What we’ve built allows us to test any of the EmployeeController actions.</span></span> <span data-ttu-id="7a2e2-315">Na przykład podczas pisania testów dla akcji Indeks kontrolera employee możemy dziedziczyć z klasy podstawowej testu, aby ustanowić tę samą konfigurację podstawową dla naszych testów.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-315">For example, when we write tests for the Index action of the Employee controller we can inherit from the test base class to establish the same base setup for our tests.</span></span> <span data-ttu-id="7a2e2-316">Ponownie klasa podstawowa utworzy repozytorium w pamięci, fałszywą jednostkę pracy i wystąpienie EmployeeController.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-316">Again the base class will create the in-memory repository, the fake unit of work, and an instance of the EmployeeController.</span></span> <span data-ttu-id="7a2e2-317">Testy dla akcji Indeks wystarczy skupić się na wywoływaniu akcji Indeks i testowania jakości modelu akcja zwraca.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-317">The tests for the Index action only need to focus on invoking the Index action and testing the qualities of the model the action returns.</span></span>

``` csharp
    [TestClass]
    public class EmployeeControllerIndexActionTests
               : EmployeeControllerTestBase {
        [TestMethod]
        public void ShouldBuildModelWithAllEmployees() {
            var result = _controller.Index();
            var model = result.ViewData.Model
                          as IEnumerable<Employee>;
            Assert.IsTrue(model.Count() == _employeeData.Count);
        }
        [TestMethod]
        public void ShouldOrderModelByHiredateAscending() {
            var result = _controller.Index();
            var model = result.ViewData.Model
                         as IEnumerable<Employee>;
            Assert.IsTrue(model.SequenceEqual(
                           _employeeData.OrderBy(e => e.HireDate)));
        }
        // ...
    }
```

<span data-ttu-id="7a2e2-318">Testy, które tworzymy z podróbkami w pamięci, są ukierunkowane na testowanie *stanu* oprogramowania.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-318">The tests we are creating with in-memory fakes are oriented towards testing the *state* of the software.</span></span> <span data-ttu-id="7a2e2-319">Na przykład podczas testowania Create akcji chcemy sprawdzić stan repozytorium po wykonaniu akcji tworzenia — czy repozytorium przechowuje nowego pracownika?</span><span class="sxs-lookup"><span data-stu-id="7a2e2-319">For example, when testing the Create action we want to inspect the state of the repository after the create action executes – does the repository hold the new employee?</span></span>

``` csharp
    [TestMethod]
    public void ShouldAddNewEmployeeToRepository() {
        _controller.Create(_newEmployee);
        Assert.IsTrue(_repository.Contains(_newEmployee));
    }
```

<span data-ttu-id="7a2e2-320">Później przyjrzymy się testom opartym na interakcji.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-320">Later we’ll look at interaction based testing.</span></span> <span data-ttu-id="7a2e2-321">Testowanie oparte na interakcji zapyta, czy testowany kod wywołał odpowiednie metody na naszych obiektach i przeszedł poprawne parametry.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-321">Interaction based testing will ask if the code under test invoked the proper methods on our objects and passed the correct parameters.</span></span> <span data-ttu-id="7a2e2-322">Na razie przeniesiemy na okładkę inny wzór projektowy – leniwy ładunek.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-322">For now we’ll move on the cover another design pattern – the lazy load.</span></span>

## <a name="eager-loading-and-lazy-loading"></a><span data-ttu-id="7a2e2-323">Gorliwy załadunek i leniwy ładowanie</span><span class="sxs-lookup"><span data-stu-id="7a2e2-323">Eager Loading and Lazy Loading</span></span>

<span data-ttu-id="7a2e2-324">W pewnym momencie w ASP.NET aplikacji sieci web MVC możemy chcieć wyświetlić informacje o pracowniku i dołączyć skojarzone z nim karty czasowe pracownika.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-324">At some point in the ASP.NET  MVC web application we might wish to display an employee’s information and include the employee’s associated time cards.</span></span> <span data-ttu-id="7a2e2-325">Na przykład możemy mieć wyświetlanie podsumowania karty czasu, które pokazuje imię i nazwisko pracownika oraz całkowitą liczbę kart czasu w systemie.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-325">For example, we might have a time card summary display that shows the employee’s name and the total number of time cards in the system.</span></span> <span data-ttu-id="7a2e2-326">Istnieje kilka podejść, które możemy podjąć, aby zaimplementować tę funkcję.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-326">There are several approaches we can take to implement this feature.</span></span>

### <a name="projection"></a><span data-ttu-id="7a2e2-327">Projekcja</span><span class="sxs-lookup"><span data-stu-id="7a2e2-327">Projection</span></span>

<span data-ttu-id="7a2e2-328">Jednym z łatwych podejść do tworzenia podsumowania jest skonstruowanie modelu dedykowanego do informacji, które chcemy wyświetlić w widoku.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-328">One easy approach to create the summary is to construct a model dedicated to the information we want to display in the view.</span></span> <span data-ttu-id="7a2e2-329">W tym scenariuszu model może wyglądać następująco.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-329">In this scenario the model might look like the following.</span></span>

``` csharp
    public class EmployeeSummaryViewModel {
        public string Name { get; set; }
        public int TotalTimeCards { get; set; }
    }
```

<span data-ttu-id="7a2e2-330">Należy zauważyć, że EmployeeSummaryViewModel nie jest jednostką — innymi słowy nie jest to coś, co chcemy utrzymać w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-330">Note that the EmployeeSummaryViewModel is not an entity – in other words it is not something we want to persist in the database.</span></span> <span data-ttu-id="7a2e2-331">Będziemy używać tej klasy tylko do tasowania danych do widoku w sposób silnie typizowany.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-331">We are only going to use this class to shuffle data into the view in a strongly typed manner.</span></span> <span data-ttu-id="7a2e2-332">Model widoku jest jak obiekt transferu danych (DTO), ponieważ nie zawiera żadnych zachowań (bez metod) — tylko właściwości.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-332">The view model is like a data transfer object (DTO) because it contains no behavior (no methods) – only properties.</span></span> <span data-ttu-id="7a2e2-333">Właściwości będą zawierać dane, które musimy przenieść.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-333">The properties will hold the data we need to move.</span></span> <span data-ttu-id="7a2e2-334">Tworzenie wystąpienia tego modelu widoku jest łatwe przy użyciu standardowego operatora projekcji LINQ — operatora Select.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-334">It is easy to instantiate this view model using LINQ’s standard projection operator – the Select operator.</span></span>

``` csharp
    public ViewResult Summary(int id) {
        var model = _unitOfWork.Employees
                               .Where(e => e.Id == id)
                               .Select(e => new EmployeeSummaryViewModel
                                  {
                                    Name = e.Name,
                                    TotalTimeCards = e.TimeCards.Count()
                                  })
                               .Single();
        return View(model);
    }
```

<span data-ttu-id="7a2e2-335">Istnieją dwie godne uwagi funkcje powyższego kodu.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-335">There are two notable features to the above code.</span></span> <span data-ttu-id="7a2e2-336">Po pierwsze – kod jest łatwy do przetestowania, ponieważ nadal jest łatwy do zaobserwowania i wyizolowania.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-336">First – the code is easy to test because it is still easy to observe and isolate.</span></span> <span data-ttu-id="7a2e2-337">Operator Select działa równie dobrze przeciwko naszym podróbom w pamięci, jak i przeciwko prawdziwej jednostce pracy.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-337">The Select operator works just as well against our in-memory fakes as it does against the real unit of work.</span></span>

``` csharp
    [TestClass]
    public class EmployeeControllerSummaryActionTests
               : EmployeeControllerTestBase {
        [TestMethod]
        public void ShouldBuildModelWithCorrectEmployeeSummary() {
            var id = 1;
            var result = _controller.Summary(id);
            var model = result.ViewData.Model as EmployeeSummaryViewModel;
            Assert.IsTrue(model.TotalTimeCards == 3);
        }
        // ...
    }
```

<span data-ttu-id="7a2e2-338">Drugą godną uwagi funkcją jest sposób, w jaki kod umożliwia EF4 wygenerowanie pojedynczego, wydajnego zapytania w celu zebrania informacji o pracownikach i kartach czasu.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-338">The second notable feature is how the code allows EF4 to generate a single, efficient query to assemble employee and time card information together.</span></span> <span data-ttu-id="7a2e2-339">Załadowaliśmy informacje o pracownikach i informacje o karcie czasu do tego samego obiektu bez użycia specjalnych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-339">We’ve loaded employee information and time card information into the same object without using any special APIs.</span></span> <span data-ttu-id="7a2e2-340">Kod tylko wyrażone informacje, które wymaga przy użyciu standardowych operatorów LINQ, które działają z w pamięci źródeł danych, jak również zdalnych źródeł danych.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-340">The code merely expressed the information it requires using standard LINQ operators that work against in-memory data sources as well as remote data sources.</span></span> <span data-ttu-id="7a2e2-341">EF4 był w stanie przetłumaczyć drzewa wyrażeń generowane przez kwerendę LINQ i kompilator C\# na pojedyncze i wydajne zapytanie T-SQL.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-341">EF4 was able to translate the expression trees generated by the LINQ query and C\# compiler into a single and efficient T-SQL query.</span></span>

``` SQL
    SELECT
    [Limit1].[Id] AS [Id],
    [Limit1].[Name] AS [Name],
    [Limit1].[C1] AS [C1]
    FROM (SELECT TOP (2)
      [Project1].[Id] AS [Id],
      [Project1].[Name] AS [Name],
      [Project1].[C1] AS [C1]
      FROM (SELECT
        [Extent1].[Id] AS [Id],
        [Extent1].[Name] AS [Name],
        (SELECT COUNT(1) AS [A1]
         FROM [dbo].[TimeCards] AS [Extent2]
         WHERE [Extent1].[Id] =
               [Extent2].[EmployeeTimeCard_TimeCard_Id]) AS [C1]
              FROM [dbo].[Employees] AS [Extent1]
               WHERE [Extent1].[Id] = @p__linq__0
         )  AS [Project1]
    )  AS [Limit1]
```

<span data-ttu-id="7a2e2-342">Istnieją inne czasy, gdy nie chcemy pracować z modelem widoku lub obiektem DTO, ale z rzeczywistymi jednostkami.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-342">There are other times when we don’t want to work with a view model or DTO object, but with real entities.</span></span> <span data-ttu-id="7a2e2-343">Kiedy wiemy, że potrzebujemy pracownika *i* kart czasowych pracownika, możemy chętnie załadować powiązane dane w dyskretny i skuteczny sposób.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-343">When we know we need an employee *and* the employee’s time cards, we can eagerly load the related data in an unobtrusive and efficient manner.</span></span>

### <a name="explicit-eager-loading"></a><span data-ttu-id="7a2e2-344">Jawne wczesne ładowanie</span><span class="sxs-lookup"><span data-stu-id="7a2e2-344">Explicit Eager Loading</span></span>

<span data-ttu-id="7a2e2-345">Gdy chcemy chętnie załadować informacje o encji pokrewnych, potrzebujemy pewnego mechanizmu logiki biznesowej (lub w tym scenariuszu logiki akcji kontrolera), aby wyrazić swoje pragnienie repozytorium.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-345">When we want to eagerly load related entity information we need some mechanism for business logic (or in this scenario, controller action logic) to express its desire to the repository.</span></span> <span data-ttu-id="7a2e2-346">Klasa EF4 ObjectQuery&lt;T&gt; definiuje metodę Include, aby określić powiązane obiekty do pobrania podczas kwerendy.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-346">The EF4 ObjectQuery&lt;T&gt; class defines an Include method to specify the related objects to retrieve during a query.</span></span> <span data-ttu-id="7a2e2-347">Zapamiętaj EF4 ObjectContext udostępnia jednostki za&lt;&gt; pośrednictwem klasy ObjectSet T, która dziedziczy z ObjectQuery&lt;T&gt;.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-347">Remember the EF4 ObjectContext exposes entities via the concrete ObjectSet&lt;T&gt; class which inherits from ObjectQuery&lt;T&gt;.</span></span><span data-ttu-id="7a2e2-348">Gdybyśmy używali odwołań ObjectSet&lt;T&gt; w naszej akcji kontrolera, moglibyśmy napisać następujący kod, aby określić chętny ładunek informacji o karcie czasu dla każdego pracownika.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-348">  If we were using ObjectSet&lt;T&gt; references in our controller action we could write the following code to specify an eager load of time card information for each employee.</span></span>

``` csharp
    _employees.Include("TimeCards")
              .Where(e => e.HireDate.Year > 2009);
```

<span data-ttu-id="7a2e2-349">Jednak ponieważ staramy się zachować nasz kod testowalne&lt;nie&gt; są narażając ObjectSet T spoza rzeczywistej jednostki klasy roboczej.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-349">However, since we are trying to keep our code testable we are not exposing ObjectSet&lt;T&gt; from outside the real unit of work class.</span></span> <span data-ttu-id="7a2e2-350">Zamiast tego polegamy na interfejsie&lt;IObjectSet T,&gt; który jest łatwiejszy&lt;do&gt; sfałszowania, ale IObjectSet T nie definiuje metody Include.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-350">Instead, we rely on the IObjectSet&lt;T&gt; interface which is easier to fake, but IObjectSet&lt;T&gt; does not define an Include method.</span></span> <span data-ttu-id="7a2e2-351">Piękno LINQ polega na tym, że możemy stworzyć własny operator Include.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-351">The beauty of LINQ is that we can create our own Include operator.</span></span>

``` csharp
    public static class QueryableExtensions {
        public static IQueryable<T> Include<T>
                (this IQueryable<T> sequence, string path) {
            var objectQuery = sequence as ObjectQuery<T>;
            if(objectQuery != null)
            {
                return objectQuery.Include(path);
            }
            return sequence;
        }
    }
```

<span data-ttu-id="7a2e2-352">Należy zauważyć, że to Włącz operator jest&lt;zdefiniowany jako metoda rozszerzenia dla IQueryable T&gt; zamiast IObjectSet&lt;T&gt;.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-352">Notice this Include operator is defined as an extension method for IQueryable&lt;T&gt; instead of IObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="7a2e2-353">Daje nam to możliwość korzystania z metody z szerszym zakresem&lt;możliwych&gt;typów, w&lt;tym&gt;IQueryable T , IObjectSet T ,&lt;ObjectQuery T&gt;i ObjectSet&lt;T&gt;.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-353">This gives us the ability to use the method with a wider range of possible types, including IQueryable&lt;T&gt;, IObjectSet&lt;T&gt;, ObjectQuery&lt;T&gt;, and ObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="7a2e2-354">W przypadku, gdy podstawowa sekwencja nie&lt;jest&gt;oryginalna EF4 ObjectQuery T , to nie ma żadnych szkód zrobić i Include operator jest no-op.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-354">In the event the underlying sequence is not a genuine EF4 ObjectQuery&lt;T&gt;, then there is no harm done and the Include operator is a no-op.</span></span> <span data-ttu-id="7a2e2-355">Jeśli podstawową *is* sekwencją jest&lt;&gt; ObjectQuery T (lub&lt;&gt;pochodzące z ObjectQuery T), a następnie EF4 zobaczy nasze wymagania dotyczące dodatkowych danych i sformułować odpowiednie zapytanie SQL.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-355">If the underlying sequence *is* an ObjectQuery&lt;T&gt; (or derived from ObjectQuery&lt;T&gt;), then EF4 will see our requirement for additional data and formulate the proper SQL query.</span></span>

<span data-ttu-id="7a2e2-356">Z tego nowego operatora w miejscu możemy jawnie zażądać obciążenia informacji karty czasu z repozytorium.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-356">With this new operator in place we can explicitly request an eager load of time card information from the repository.</span></span>

``` csharp
    public ViewResult Index() {
        var model = _unitOfWork.Employees
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

<span data-ttu-id="7a2e2-357">Po uruchomieniu z rzeczywistym ObjectContext, kod tworzy następujące pojedyncze zapytanie.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-357">When run against a real ObjectContext, the code produces the following single query.</span></span> <span data-ttu-id="7a2e2-358">Kwerenda zbiera wystarczającą ilość informacji z bazy danych w jednej podróży, aby zmaterializować obiekty pracownika i w pełni wypełnić ich timecards właściwości.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-358">The query gathers enough information from the database in one trip to materialize the employee objects and fully populate their TimeCards property.</span></span>

``` SQL
    SELECT
    [Project1].[Id] AS [Id],
    [Project1].[Name] AS [Name],
    [Project1].[HireDate] AS [HireDate],
    [Project1].[C1] AS [C1],
    [Project1].[Id1] AS [Id1],
    [Project1].[Hours] AS [Hours],
    [Project1].[EffectiveDate] AS [EffectiveDate],
    [Project1].[EmployeeTimeCard_TimeCard_Id] AS [EmployeeTimeCard_TimeCard_Id]
    FROM ( SELECT
         [Extent1].[Id] AS [Id],
         [Extent1].[Name] AS [Name],
         [Extent1].[HireDate] AS [HireDate],
         [Extent2].[Id] AS [Id1],
         [Extent2].[Hours] AS [Hours],
         [Extent2].[EffectiveDate] AS [EffectiveDate],
         [Extent2].[EmployeeTimeCard_TimeCard_Id] AS
                    [EmployeeTimeCard_TimeCard_Id],
         CASE WHEN ([Extent2].[Id] IS NULL) THEN CAST(NULL AS int)
         ELSE 1 END AS [C1]
         FROM  [dbo].[Employees] AS [Extent1]
         LEFT OUTER JOIN [dbo].[TimeCards] AS [Extent2] ON [Extent1].[Id] = [Extent2].[EmployeeTimeCard_TimeCard_Id]
    )  AS [Project1]
    ORDER BY [Project1].[HireDate] ASC,
             [Project1].[Id] ASC, [Project1].[C1] ASC
```

<span data-ttu-id="7a2e2-359">Świetną wiadomością jest kod wewnątrz metody akcji pozostaje w pełni sprawdzalny.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-359">The great news is the code inside the action method remains fully testable.</span></span> <span data-ttu-id="7a2e2-360">Nie musimy udostępniać żadnych dodatkowych funkcji dla naszych podróbek, aby wspierać operatora Include.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-360">We don’t need to provide any additional features for our fakes to support the Include operator.</span></span> <span data-ttu-id="7a2e2-361">Złą wiadomością jest to, że musieliśmy użyć Include operator wewnątrz kodu chcieliśmy zachować wytrwałość ignorantów.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-361">The bad news is we had to use the Include operator inside of the code we wanted to keep persistence ignorant.</span></span> <span data-ttu-id="7a2e2-362">Jest to doskonały przykład typu kompromisów, które należy ocenić podczas tworzenia kodu testowalny.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-362">This is a prime example of the type of tradeoffs you’ll need to evaluate when building testable code.</span></span> <span data-ttu-id="7a2e2-363">Istnieją chwile, kiedy trzeba pozwolić trwałość dotyczy przeciek poza abstrakcji repozytorium, aby osiągnąć cele wydajności.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-363">There are times when you need to let persistence concerns leak outside the repository abstraction to meet performance goals.</span></span>

<span data-ttu-id="7a2e2-364">Alternatywą dla zaauwania chętnie jest ładowanie z opóźnieniem.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-364">The alternative to eager loading is lazy loading.</span></span> <span data-ttu-id="7a2e2-365">Z opóźnieniem ładowania oznacza, że *nie* potrzebujemy naszego kodu biznesowego, aby jawnie ogłosić wymóg dla skojarzonych danych.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-365">Lazy loading means we do *not* need our business code to explicitly announce the requirement for associated data.</span></span> <span data-ttu-id="7a2e2-366">Zamiast tego używamy naszych jednostek w aplikacji i jeśli potrzebne są dodatkowe dane Entity Framework załaduje dane na żądanie.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-366">Instead, we use our entities in the application and if additional data is needed Entity Framework will load the data on demand.</span></span>

### <a name="lazy-loading"></a><span data-ttu-id="7a2e2-367">Z opóźnieniem</span><span class="sxs-lookup"><span data-stu-id="7a2e2-367">Lazy Loading</span></span>

<span data-ttu-id="7a2e2-368">Łatwo wyobrazić sobie scenariusz, w którym nie wiemy, jakich danych będzie potrzebowała logika biznesowa.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-368">It’s easy to imagine a scenario where we don’t know what data a piece of business logic will need.</span></span> <span data-ttu-id="7a2e2-369">Możemy wiedzieć, że logika potrzebuje obiektu pracownika, ale możemy rozgałęzić się na różne ścieżki wykonywania, gdzie niektóre z tych ścieżek wymagają informacji o karcie czasu od pracownika, a niektóre nie.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-369">We might know the logic needs an employee object, but we may branch into different execution paths where some of those paths require time card information from the employee, and some do not.</span></span> <span data-ttu-id="7a2e2-370">Scenariusze takie jak ten są idealne do niejawnego ładowania z opóźnieniem, ponieważ dane magicznie pojawia się na podstawie zgodnie z potrzebami.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-370">Scenarios like this are perfect for implicit lazy loading because data magically appears on an as-needed basis.</span></span>

<span data-ttu-id="7a2e2-371">Z opóźnieniem ładowania, znany również jako odroczone ładowanie, umieszcza pewne wymagania na naszych obiektów jednostki.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-371">Lazy loading, also known as deferred loading, does place some requirements on our entity objects.</span></span> <span data-ttu-id="7a2e2-372">POCO z prawdziwą ignorancją trwałości nie napotka żadnych wymagań z warstwy trwałości, ale prawdziwa ignorancja trwałości jest praktycznie niemożliwa do osiągnięcia.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-372">POCOs with true persistence ignorance would not face any requirements from the persistence layer, but true persistence ignorance is practically impossible to achieve.</span></span><span data-ttu-id="7a2e2-373">Zamiast tego mierzymy ignorancję trwałości w stopniach względnych.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-373">  Instead we measure persistence ignorance in relative degrees.</span></span> <span data-ttu-id="7a2e2-374">Byłoby niefortunne, gdybyśmy musieli dziedziczyć z klasy podstawowej zorientowanej na trwałość lub użyć specjalistycznej kolekcji, aby osiągnąć opóźnienie ładowania w POCO.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-374">It would be unfortunate if we needed to inherit from a persistence oriented base class or use a specialized collection to achieve lazy loading in POCOs.</span></span> <span data-ttu-id="7a2e2-375">Na szczęście EF4 ma mniej inwazyjne rozwiązanie.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-375">Fortunately, EF4 has a less intrusive solution.</span></span>

### <a name="virtually-undetectable"></a><span data-ttu-id="7a2e2-376">Praktycznie niewykrywalne</span><span class="sxs-lookup"><span data-stu-id="7a2e2-376">Virtually Undetectable</span></span>

<span data-ttu-id="7a2e2-377">Podczas korzystania z obiektów POCO, EF4 można dynamicznie generować serwery proxy środowiska uruchomieniowego dla jednostek.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-377">When using POCO objects, EF4 can dynamically generate runtime proxies for entities.</span></span> <span data-ttu-id="7a2e2-378">Te serwery proxy niewidocznie zawijają zmaterializowane POCO i zapewniają dodatkowe usługi, przechwytując każdą właściwość get and set operation w celu wykonania dodatkowej pracy.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-378">These proxies invisibly wrap the materialized POCOs and provide additional services by intercepting each property get and set operation to perform additional work.</span></span> <span data-ttu-id="7a2e2-379">Jedną z takich usług jest leniwa funkcja ładowania, którego szukamy.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-379">One such service is the lazy loading feature we are looking for.</span></span> <span data-ttu-id="7a2e2-380">Inna usługa to mechanizm śledzenia efektywnych zmian, który może rejestrować, gdy program zmienia wartości właściwości jednostki.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-380">Another service is an efficient change tracking mechanism which can record when the program changes the property values of an entity.</span></span> <span data-ttu-id="7a2e2-381">Lista zmian jest używana przez ObjectContext podczas SaveChanges metody do utrwalania wszelkich zmodyfikowanych jednostek przy użyciu polecenia UPDATE.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-381">The list of changes is used by the ObjectContext during the SaveChanges method to persist any modified entities using UPDATE commands.</span></span>

<span data-ttu-id="7a2e2-382">Dla tych serwerów proxy do pracy, jednak potrzebują sposobu, aby podłączyć do właściwości dostać i ustawić operacje na jednostki, a serwery proxy osiągnąć ten cel przez zastąpienie wirtualnych elementów członkowskich.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-382">For these proxies to work, however, they need a way to hook into property get and set operations on an entity, and the proxies achieve this goal by overriding virtual members.</span></span> <span data-ttu-id="7a2e2-383">W związku z tym jeśli chcemy mieć niejawne ładowanie z opóźnieniem i efektywne śledzenie zmian, musimy wrócić do naszych definicji klas POCO i oznaczyć właściwości jako wirtualne.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-383">Thus, if we want to have implicit lazy loading and efficient change tracking we need to go back to our POCO class definitions and mark properties as virtual.</span></span>

``` csharp
    public class Employee {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }
        public virtual DateTime HireDate { get; set; }
        public virtual ICollection<TimeCard> TimeCards { get; set; }
    }
```

<span data-ttu-id="7a2e2-384">Nadal możemy powiedzieć, że jednostka Pracownik jest w większości niewiedzą w zachowaniu.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-384">We can still say the Employee entity is mostly persistence ignorant.</span></span> <span data-ttu-id="7a2e2-385">Jedynym wymaganiem jest użycie elementów członkowskich wirtualnych i nie ma to wpływu na sprawdzalność kodu.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-385">The only requirement is to use virtual members and this does not impact the testability of the code.</span></span> <span data-ttu-id="7a2e2-386">Nie musimy czerpać z żadnej specjalnej klasy podstawowej, a nawet używać specjalnej kolekcji poświęconej leniwemu załadunkowi.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-386">We don’t need to derive from any special base class, or even use a special collection dedicated to lazy loading.</span></span> <span data-ttu-id="7a2e2-387">Jak pokazuje kod, każda klasa implementująca&lt;&gt; ICollection T jest dostępna do przechowywania jednostek pokrewnych.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-387">As the code demonstrates, any class implementing ICollection&lt;T&gt; is available to hold related entities.</span></span>

<span data-ttu-id="7a2e2-388">Jest też jedna drobna zmiana, którą musimy wprowadzić w naszej jednostce pracy.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-388">There is also one minor change we need to make inside our unit of work.</span></span> <span data-ttu-id="7a2e2-389">Ładowanie z opóźnieniem jest domyślnie *wyłączone* podczas pracy bezpośrednio z ObjectContext obiektu.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-389">Lazy loading is *off* by default when working directly with an ObjectContext object.</span></span> <span data-ttu-id="7a2e2-390">Istnieje właściwość, którą możemy ustawić na ContextOptions właściwości, aby włączyć odroczone ładowanie i możemy ustawić tę właściwość wewnątrz naszej rzeczywistej jednostki pracy, jeśli chcemy włączyć ładowanie leniwe wszędzie.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-390">There is a property we can set on the ContextOptions property to enable deferred loading, and we can set this property inside our real unit of work if we want to enable lazy loading everywhere.</span></span>

``` csharp
    public class SqlUnitOfWork : IUnitOfWork {
         public SqlUnitOfWork() {
             // ...
             _context = new ObjectContext(connectionString);
             _context.ContextOptions.LazyLoadingEnabled = true;
         }    
         // ...
     }
```

<span data-ttu-id="7a2e2-391">Z niejawnego ładowania z opóźnieniem włączone, kod aplikacji można użyć pracownika i pracownika skojarzone karty czasu, pozostając błogo nieświadomy pracy wymaganej dla EF załadować dodatkowe dane.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-391">With implicit lazy loading enabled, application code can use an employee and the employee’s associated time cards while remaining blissfully unaware of the work required for EF to load the extra data.</span></span>

``` csharp
    var employee = _unitOfWork.Employees
                              .Single(e => e.Id == id);
    foreach (var card in employee.TimeCards) {
        // ...
    }
```

<span data-ttu-id="7a2e2-392">Powolne ładowanie sprawia, że kod aplikacji łatwiejsze do zapisu, a z magii proxy kod pozostaje całkowicie sprawdzalny.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-392">Lazy loading makes the application code easier to write, and with the proxy magic the code remains completely testable.</span></span> <span data-ttu-id="7a2e2-393">Podróbki w pamięci jednostki pracy można po prostu wstępnie załadować fałszywe jednostki z powiązanych danych, gdy jest to potrzebne podczas testu.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-393">In-memory fakes of the unit of work can simply preload fake entities with associated data when needed during a test.</span></span>

<span data-ttu-id="7a2e2-394">W tym momencie zwrócimy naszą uwagę z tworzenia repozytoriów przy użyciu IObjectSet&lt;T&gt; i spojrzeć na abstrakcje, aby ukryć wszystkie oznaki struktury trwałości.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-394">At this point we’ll turn our attention from building repositories using IObjectSet&lt;T&gt; and look at abstractions to hide all signs of the persistence framework.</span></span>

## <a name="custom-repositories"></a><span data-ttu-id="7a2e2-395">Repozytoria niestandardowe</span><span class="sxs-lookup"><span data-stu-id="7a2e2-395">Custom Repositories</span></span>

<span data-ttu-id="7a2e2-396">Kiedy po raz pierwszy zaprezentowaliśmy wzorzec projektowania jednostki pracy w tym artykule, podaliśmy przykładowy kod, jak może wyglądać jednostka pracy.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-396">When we first presented the unit of work design pattern in this article we provided some sample code for what the unit of work might look like.</span></span> <span data-ttu-id="7a2e2-397">Zaprezentujmy ponownie ten oryginalny pomysł przy użyciu scenariusza karty czasu pracownika i pracownika, z którym pracowaliśmy.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-397">Let’s re-present this original idea using the employee and employee time card scenario we’ve been working with.</span></span>

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<TimeCard> TimeCards { get;  }
        void Commit();
    }
```

<span data-ttu-id="7a2e2-398">Podstawową różnicą między tą jednostką pracy a jednostką pracy, którą utworzyliśmy w ostatniej sekcji, jest to, jak ta&lt;jednostka&gt;pracy nie używa żadnych abstrakcji z struktury EF4 (nie ma IObjectSet T ).</span><span class="sxs-lookup"><span data-stu-id="7a2e2-398">The primary difference between this unit of work and the unit of work we created in the last section is how this unit of work does not use any abstractions from the EF4 framework (there is no IObjectSet&lt;T&gt;).</span></span> <span data-ttu-id="7a2e2-399">IObjectSet&lt;&gt; T działa dobrze jako interfejs repozytorium, ale interfejs API, który udostępnia może nie idealnie dostosować się do potrzeb naszej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-399">IObjectSet&lt;T&gt; works well as a repository interface, but the API it exposes might not perfectly align with our application’s needs.</span></span> <span data-ttu-id="7a2e2-400">W tym nadchodzącym podejściu będziemy reprezentować repozytoria przy&lt;&gt; użyciu niestandardowej abstrakcji IRepository T.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-400">In this upcoming approach we will represent repositories using a custom IRepository&lt;T&gt; abstraction.</span></span>

<span data-ttu-id="7a2e2-401">Wielu deweloperów, którzy postępują zgodnie z projektem opartym na testach, projektowaniem opartym na zachowaniu i projektowaniem metodologii opartych na domenie, preferuje podejście IRepository&lt;T&gt; z kilku powodów.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-401">Many developers who follow test-driven design, behavior-driven design, and domain driven methodologies design prefer the IRepository&lt;T&gt; approach for several reasons.</span></span> <span data-ttu-id="7a2e2-402">Po pierwsze interfejs IRepository&lt;T&gt; reprezentuje warstwę "antykorupcyjną".</span><span class="sxs-lookup"><span data-stu-id="7a2e2-402">First, the IRepository&lt;T&gt; interface represents an “anti-corruption” layer.</span></span> <span data-ttu-id="7a2e2-403">Zgodnie z opisem eric Evans w jego domain driven design książki warstwy antykorupcyjnej utrzymuje kod domeny z dala od interfejsów API infrastruktury, jak interfejs API trwałości.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-403">As described by Eric Evans in his Domain Driven Design book an anti-corruption layer keeps your domain code away from infrastructure APIs, like a persistence API.</span></span> <span data-ttu-id="7a2e2-404">Po drugie deweloperzy mogą tworzyć metody do repozytorium, które spełniają dokładne potrzeby aplikacji (odnalezione podczas pisania testów).</span><span class="sxs-lookup"><span data-stu-id="7a2e2-404">Secondly, developers can build methods into the repository that meet the exact needs of an application (as discovered while writing tests).</span></span> <span data-ttu-id="7a2e2-405">Na przykład często może być konieczne zlokalizowanie pojedynczej jednostki przy użyciu wartości identyfikatora, dzięki czemu możemy dodać metodę FindById do interfejsu repozytorium.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-405">For example, we might frequently need to locate a single entity using an ID value, so we can add a FindById method to the repository interface.</span></span><span data-ttu-id="7a2e2-406">Nasza definicja IRepository&lt;T&gt; będzie wyglądać następująco.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-406">  Our IRepository&lt;T&gt; definition will look like the following.</span></span>

``` csharp
    public interface IRepository<T>
                    where T : class, IEntity {
        IQueryable<T> FindAll();
        IQueryable<T> FindWhere(Expression<Func\<T, bool>> predicate);
        T FindById(int id);       
        void Add(T newEntity);
        void Remove(T entity);
    }
```

<span data-ttu-id="7a2e2-407">Zwróć uwagę, że powrócimy do korzystania&lt;&gt; z interfejsu IQueryable T, aby udostępnić kolekcje jednostek.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-407">Notice we’ll drop back to using an IQueryable&lt;T&gt; interface to expose entity collections.</span></span> <span data-ttu-id="7a2e2-408">IQueryable&lt;&gt; T umożliwia linq wyrażeń drzewa przepływu do dostawcy EF4 i dać dostawcy całościowy widok kwerendy.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-408">IQueryable&lt;T&gt; allows LINQ expression trees to flow into the EF4 provider and give the provider a holistic view of the query.</span></span> <span data-ttu-id="7a2e2-409">Drugą opcją byłoby zwrócenie IEnumerable&lt;T&gt;, co oznacza, że dostawca EF4 LINQ zobaczy tylko wyrażenia wbudowane wewnątrz repozytorium.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-409">A second option would be to return IEnumerable&lt;T&gt;, which means the EF4 LINQ provider will only see the expressions built inside of the repository.</span></span> <span data-ttu-id="7a2e2-410">Wszelkie grupowanie, kolejność i projekcja wykonywane poza repozytorium nie będą składać się do polecenia SQL wysyłanego do bazy danych, co może zaszkodzić wydajności.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-410">Any grouping, ordering, and projection done outside of the repository will not be composed into the SQL command sent to the database, which can hurt performance.</span></span> <span data-ttu-id="7a2e2-411">Z drugiej strony repozytorium zwracające tylko wyniki&lt;IEnumerable T&gt; nigdy nie zaskoczy Cię nowym poleceniem SQL.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-411">On the other hand, a repository returning only IEnumerable&lt;T&gt; results will never surprise you with a new SQL command.</span></span> <span data-ttu-id="7a2e2-412">Oba podejścia będą działać, a oba podejścia pozostają sprawdzalne.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-412">Both approaches will work, and both approaches remain testable.</span></span>

<span data-ttu-id="7a2e2-413">Jest to proste, aby zapewnić pojedynczą implementację&lt;&gt; interfejsu IRepository T przy użyciu generics i EF4 ObjectContext INTERFEJSU API.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-413">It’s straightforward to provide a single implementation of the IRepository&lt;T&gt; interface using generics and the EF4 ObjectContext API.</span></span>

``` csharp
    public class SqlRepository<T> : IRepository<T>
                                    where T : class, IEntity {
        public SqlRepository(ObjectContext context) {
            _objectSet = context.CreateObjectSet<T>();
        }
        public IQueryable<T> FindAll() {
            return _objectSet;
        }
        public IQueryable<T> FindWhere(
                               Expression<Func\<T, bool>> predicate) {
            return _objectSet.Where(predicate);
        }
        public T FindById(int id) {
            return _objectSet.Single(o => o.Id == id);
        }
        public void Add(T newEntity) {
            _objectSet.AddObject(newEntity);
        }
        public void Remove(T entity) {
            _objectSet.DeleteObject(entity);
        }
        protected ObjectSet<T> _objectSet;
    }
```

<span data-ttu-id="7a2e2-414">Podejście&lt;IRepository&gt; T daje nam dodatkową kontrolę nad naszymi zapytaniami, ponieważ klient musi wywołać metodę, aby uzyskać kontakt z jednostką.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-414">The IRepository&lt;T&gt; approach gives us some additional control over our queries because a client has to invoke a method to get to an entity.</span></span> <span data-ttu-id="7a2e2-415">Wewnątrz metody możemy zapewnić dodatkowe kontrole i operatorów LINQ do wymuszania ograniczeń aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-415">Inside the method we could provide additional checks and LINQ operators to enforce application constraints.</span></span> <span data-ttu-id="7a2e2-416">Zwróć uwagę, że interfejs ma dwa ograniczenia na parametr typu ogólnego.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-416">Notice the interface has two constraints on the generic type parameter.</span></span> <span data-ttu-id="7a2e2-417">Pierwsze ograniczenie jest klasa cons taint wymagane&lt;&gt;przez ObjectSet T , a drugie ograniczenie wymusza nasze jednostki do zaimplementowania IEntity - abstrakcji utworzone dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-417">The first constraint is the class cons taint required by ObjectSet&lt;T&gt;, and the second constraint forces our entities to implement IEntity – an abstraction created for the application.</span></span> <span data-ttu-id="7a2e2-418">Interfejs IEntity wymusza jednostki, aby mieć czytelną właściwość identyfikatora, a następnie możemy użyć tej właściwości w FindById metody.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-418">The IEntity interface forces entities to have a readable Id property, and we can then use this property in the FindById method.</span></span> <span data-ttu-id="7a2e2-419">IEntity jest zdefiniowany za pomocą następującego kodu.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-419">IEntity is defined with the following code.</span></span>

``` csharp
    public interface IEntity {
        int Id { get; }
    }
```

<span data-ttu-id="7a2e2-420">IEntity można uznać za małe naruszenie trwałości ignorancji, ponieważ nasze jednostki są wymagane do zaimplementowania tego interfejsu.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-420">IEntity could be considered a small violation of persistence ignorance since our entities are required to implement this interface.</span></span> <span data-ttu-id="7a2e2-421">Pamiętaj, że ignorancja trwałości dotyczy kompromisów, a dla wielu funkcja FindById przeważy nad ograniczeniem narzuconym przez interfejs.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-421">Remember persistence ignorance is about tradeoffs, and for many the FindById functionality will outweigh the constraint imposed by the interface.</span></span> <span data-ttu-id="7a2e2-422">Interfejs nie ma wpływu na testowalność.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-422">The interface has no impact on testability.</span></span>

<span data-ttu-id="7a2e2-423">Tworzenie wystąpienia na żywo IRepository&lt;T&gt; wymaga EF4 ObjectContext, więc implementacji konkretnej jednostki pracy należy zarządzać wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-423">Instantiating a live IRepository&lt;T&gt; requires an EF4 ObjectContext, so a concrete unit of work implementation should manage the instantiation.</span></span>

``` csharp
    public class SqlUnitOfWork : IUnitOfWork {
        public SqlUnitOfWork() {
            var connectionString =
                ConfigurationManager
                    .ConnectionStrings[ConnectionStringName]
                    .ConnectionString;

            _context = new ObjectContext(connectionString);
            _context.ContextOptions.LazyLoadingEnabled = true;
        }

        public IRepository<Employee> Employees {
            get {
                if (_employees == null) {
                    _employees = new SqlRepository<Employee>(_context);
                }
                return _employees;
            }
        }

        public IRepository<TimeCard> TimeCards {
            get {
                if (_timeCards == null) {
                    _timeCards = new SqlRepository<TimeCard>(_context);
                }
                return _timeCards;
            }
        }

        public void Commit() {
            _context.SaveChanges();
        }

        SqlRepository<Employee> _employees = null;
        SqlRepository<TimeCard> _timeCards = null;
        readonly ObjectContext _context;
        const string ConnectionStringName = "EmployeeDataModelContainer";
    }
```

### <a name="using-the-custom-repository"></a><span data-ttu-id="7a2e2-424">Korzystanie z repozytorium niestandardowego</span><span class="sxs-lookup"><span data-stu-id="7a2e2-424">Using the Custom Repository</span></span>

<span data-ttu-id="7a2e2-425">Korzystanie z naszego niestandardowego repozytorium nie różni się znacząco od korzystania&lt;&gt;z repozytorium opartego na IObjectSet T .</span><span class="sxs-lookup"><span data-stu-id="7a2e2-425">Using our custom repository is not significantly different from using the repository based on IObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="7a2e2-426">Zamiast stosowania operatorów LINQ bezpośrednio do właściwości, najpierw musimy wywołać jedną z metod repozytorium, aby&lt;pobrać&gt; odwołanie IQueryable T.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-426">Instead of applying LINQ operators directly to a property, we’ll first need to invoke one the repository’s methods to grab an IQueryable&lt;T&gt; reference.</span></span>

``` csharp
    public ViewResult Index() {
        var model = _repository.FindAll()
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

<span data-ttu-id="7a2e2-427">Zwróć uwagę, że niestandardowy operator Include, który zaimplementowaliśmy wcześniej, będzie działał bez zmian.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-427">Notice the custom Include operator we implemented previously will work without change.</span></span> <span data-ttu-id="7a2e2-428">Metoda FindById repozytorium usuwa zduplikowaną logikę z akcji próbujących pobrać pojedynczą jednostkę.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-428">The repository’s FindById method removes duplicated logic from actions trying to retrieve a single entity.</span></span>

``` csharp
    public ViewResult Details(int id) {
        var model = _repository.FindById(id);
        return View(model);
    }
```

<span data-ttu-id="7a2e2-429">Nie ma znaczącej różnicy w sprawdzalności dwóch metod, które zbadaliśmy.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-429">There is no significant difference in the testability of the two approaches we’ve examined.</span></span> <span data-ttu-id="7a2e2-430">Możemy zapewnić fałszywe&lt;implementacje IRepository T,&gt; budując&lt;konkretne&gt; klasy wspierane przez pracownika HashSet - podobnie jak to, co zrobiliśmy w ostatniej sekcji.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-430">We could provide fake implementations of IRepository&lt;T&gt; by building concrete classes backed by HashSet&lt;Employee&gt; - just like what we did in the last section.</span></span> <span data-ttu-id="7a2e2-431">Jednak niektórzy deweloperzy wolą używać makiety obiektów i makiety struktur obiektów zamiast tworzenia podróbek.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-431">However, some developers prefer to use mock objects and mock object frameworks instead of building fakes.</span></span> <span data-ttu-id="7a2e2-432">Przyjrzymy się za pomocą makiety, aby przetestować naszą implementację i omówić różnice między makietami i podróbkami w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-432">We’ll look at using mocks to test our implementation and discuss the differences between mocks and fakes in the next section.</span></span>

### <a name="testing-with-mocks"></a><span data-ttu-id="7a2e2-433">Testowanie za pomocą makiet</span><span class="sxs-lookup"><span data-stu-id="7a2e2-433">Testing with Mocks</span></span>

<span data-ttu-id="7a2e2-434">Istnieją różne podejścia do budowania tego, co Martin Fowler nazywa "podwójnym testem".</span><span class="sxs-lookup"><span data-stu-id="7a2e2-434">There are different approaches to building what Martin Fowler calls a “test double”.</span></span> <span data-ttu-id="7a2e2-435">Test double (jak film stunt double) jest obiektem, który budujesz, aby "stać" dla rzeczywistych obiektów produkcyjnych podczas testów.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-435">A test double (like a movie stunt double) is an object you build to “stand in” for real, production objects during tests.</span></span> <span data-ttu-id="7a2e2-436">Repozytoria w pamięci, które utworzyliśmy, są testami podwaja dla repozytoriów, które rozmawiają z programem SQL Server.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-436">The in-memory repositories we created are test doubles for the repositories that talk to SQL Server.</span></span> <span data-ttu-id="7a2e2-437">Widzieliśmy, jak używać tych testów podwaja podczas testów jednostkowych do izolowania kodu i zachować testy działa szybko.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-437">We’ve seen how to use these test-doubles during the unit tests to isolate code and keep tests running fast.</span></span>

<span data-ttu-id="7a2e2-438">Test podwaja stworzyliśmy mają prawdziwe, działające implementacje.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-438">The test doubles we’ve built have real, working implementations.</span></span> <span data-ttu-id="7a2e2-439">Za kulisami każdy z nich przechowuje betonową kolekcję obiektów, a oni dodać i usunąć obiekty z tej kolekcji, jak manipulować repozytorium podczas testu.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-439">Behind the scenes each one stores a concrete collection of objects, and they will add and remove objects from this collection as we manipulate the repository during a test.</span></span> <span data-ttu-id="7a2e2-440">Niektórzy deweloperzy lubią tworzyć swoje testy podwaja w ten sposób — z prawdziwym kodem i implementacji pracy.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-440">Some developers like to build their test doubles this way – with real code and working implementations.</span></span><span data-ttu-id="7a2e2-441">Te podwójne badania są to, co *nazywamy podróbki*.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-441">  These test doubles are what we call *fakes*.</span></span> <span data-ttu-id="7a2e2-442">Mają działające implementacje, ale nie są wystarczająco prawdziwe do użytku produkcyjnego.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-442">They have working implementations, but they aren’t real enough for production use.</span></span> <span data-ttu-id="7a2e2-443">Fałszywe repozytorium w rzeczywistości nie zapisuje się w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-443">The fake repository doesn’t actually write to the database.</span></span> <span data-ttu-id="7a2e2-444">Fałszywy serwer SMTP w rzeczywistości nie wysyła wiadomości e-mail przez sieć.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-444">The fake SMTP server doesn’t actually send an email message over the network.</span></span>

### <a name="mocks-versus-fakes"></a><span data-ttu-id="7a2e2-445">Szyki kontra podróbki</span><span class="sxs-lookup"><span data-stu-id="7a2e2-445">Mocks versus Fakes</span></span>

<span data-ttu-id="7a2e2-446">Istnieje inny typ testu dwukrotnie znany jako *mock*.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-446">There is another type of test double known as a *mock*.</span></span> <span data-ttu-id="7a2e2-447">Podczas gdy podróbki mają działające implementacje, mocks pochodzą z braku implementacji.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-447">While fakes have working implementations, mocks come with no implementation.</span></span> <span data-ttu-id="7a2e2-448">Za pomocą struktury obiektu makiety konstruujemy te obiekty makiety w czasie wykonywania i używamy ich jako podwaja test.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-448">With the help of a mock object framework we construct these mock objects at run time and use them as test doubles.</span></span> <span data-ttu-id="7a2e2-449">W tej sekcji będziemy używać open source mocking framework Moq.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-449">In this section we’ll be using the open source mocking framework Moq.</span></span> <span data-ttu-id="7a2e2-450">Oto prosty przykład używania Moq do dynamicznego tworzenia podwójnego testu dla repozytorium pracowników.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-450">Here is a simple example of using Moq to dynamically create a test double for an employee repository.</span></span>

``` csharp
    Mock<IRepository<Employee>> mock =
        new Mock<IRepository<Employee>>();
    IRepository<Employee> repository = mock.Object;
    repository.Add(new Employee());
    var employee = repository.FindById(1);
```

<span data-ttu-id="7a2e2-451">Prosimy Moq o wdrożenie IRepository&lt;Employee&gt; i buduje jeden dynamicznie.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-451">We ask Moq for an IRepository&lt;Employee&gt; implementation and it builds one dynamically.</span></span> <span data-ttu-id="7a2e2-452">Możemy uzyskać dostęp do obiektu implementującego&lt;IRepository Employee,&gt; uzyskując dostęp do Object właściwości Mock&lt;T&gt; obiektu.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-452">We can get to the object implementing IRepository&lt;Employee&gt; by accessing the Object property of the Mock&lt;T&gt; object.</span></span> <span data-ttu-id="7a2e2-453">Jest to wewnętrzny obiekt, który możemy przekazać do naszych kontrolerów i nie będą wiedzieć, czy jest to test podwójny czy prawdziwe repozytorium.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-453">It is this inner object we can pass into our controllers, and they won’t know if this is a test double or the real repository.</span></span> <span data-ttu-id="7a2e2-454">Możemy wywołać metody na obiekcie, tak jak firma My wywołać metody na obiekcie z rzeczywistą implementacją.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-454">We can invoke methods on the object just like we would invoke methods on an object with a real implementation.</span></span>

<span data-ttu-id="7a2e2-455">Musisz się zastanawiać, co repozytorium makiety zrobi, gdy wywołamy Add metody.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-455">You must be wondering what the mock repository will do when we invoke the Add method.</span></span> <span data-ttu-id="7a2e2-456">Ponieważ nie ma implementacji za makiety obiektu, Add nie robi nic.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-456">Since there is no implementation behind the mock object, Add does nothing.</span></span> <span data-ttu-id="7a2e2-457">Nie ma betonowej kolekcji za kulisami, jak mieliśmy z podróbkami, które napisaliśmy, więc pracownik zostaje odrzucony.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-457">There is no concrete collection behind the scenes like we had with the fakes we wrote, so the employee is discarded.</span></span> <span data-ttu-id="7a2e2-458">Co z wartością zwracaną FindById?</span><span class="sxs-lookup"><span data-stu-id="7a2e2-458">What about the return value of FindById?</span></span> <span data-ttu-id="7a2e2-459">W tym przypadku obiekt makiety robi jedyną rzeczą, jaką może zrobić, czyli zwraca wartość domyślną.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-459">In this case the mock object does the only thing it can do, which is return a default value.</span></span> <span data-ttu-id="7a2e2-460">Ponieważ zwracamy typ odwołania (Pracownik), wartość zwracana jest wartością null.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-460">Since we are returning a reference type (an Employee), the return value is a null value.</span></span>

<span data-ttu-id="7a2e2-461">Kpiny mogą wydawać się bezwartościowe; Istnieją jednak jeszcze dwie cechy makiet, o których nie rozmawialiśmy.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-461">Mocks might sound worthless; however, there are two more features of mocks we haven’t talked about.</span></span> <span data-ttu-id="7a2e2-462">Po pierwsze moq framework rejestruje wszystkie wywołania wykonane na makiety obiektu.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-462">First, the Moq framework records all the calls made on the mock object.</span></span> <span data-ttu-id="7a2e2-463">W dalszej części kodu możemy zapytać Moq, czy ktoś wywołał Add metody lub jeśli ktoś wywołał FindById metody.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-463">Later in the code we can ask Moq if anyone invoked the Add method, or if anyone invoked the FindById method.</span></span> <span data-ttu-id="7a2e2-464">Zobaczymy później, jak możemy korzystać z tej funkcji nagrywania "czarnej skrzynki" w testach.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-464">We’ll see later how we can use this “black box” recording feature in tests.</span></span>

<span data-ttu-id="7a2e2-465">Drugą wspaniałą cechą jest to, jak możemy użyć Moq do zaprogramowania makiety obiektu z *oczekiwaniami*.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-465">The second great feature is how we can use Moq to program a mock object with *expectations*.</span></span> <span data-ttu-id="7a2e2-466">Oczekiwanie informuje obiekt makiety, jak reagować na danej interakcji.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-466">An expectation tells the mock object how to respond to any given interaction.</span></span> <span data-ttu-id="7a2e2-467">Na przykład możemy zaprogramować oczekiwania do naszego makiety i powiedzieć mu, aby zwrócić obiekt pracownika, gdy ktoś wywołuje FindById.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-467">For example, we can program an expectation into our mock and tell it to return an employee object when someone invokes FindById.</span></span> <span data-ttu-id="7a2e2-468">Struktura Moq używa interfejsu API instalatora i wyrażenia lambda do programowania tych oczekiwań.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-468">The Moq framework uses a Setup API and lambda expressions to program these expectations.</span></span>

``` csharp
    [TestMethod]
    public void MockSample() {
        Mock<IRepository<Employee>> mock =
            new Mock<IRepository<Employee>>();
        mock.Setup(m => m.FindById(5))
            .Returns(new Employee {Id = 5});
        IRepository<Employee> repository = mock.Object;
        var employee = repository.FindById(5);
        Assert.IsTrue(employee.Id == 5);
    }
```

<span data-ttu-id="7a2e2-469">W tym przykładzie prosimy Moq dynamicznie zbudować repozytorium, a następnie programować repozytorium z oczekiwaniami.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-469">In this sample we ask Moq to dynamically build a repository, and then we program the repository with an expectation.</span></span> <span data-ttu-id="7a2e2-470">Oczekiwanie mówi makiety obiektu do zwrócenia nowego obiektu pracownika o wartości identyfikatora 5, gdy ktoś wywołuje FindById metody przekazywania wartości 5.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-470">The expectation tells the mock object to return a new employee object with an Id value of 5 when someone invokes the FindById method passing a value of 5.</span></span> <span data-ttu-id="7a2e2-471">Ten test przechodzi, a my nie trzeba budować pełną implementację&lt;do&gt;fałszywych IRepository T .</span><span class="sxs-lookup"><span data-stu-id="7a2e2-471">This test passes, and we didn’t need to build a full implementation to fake IRepository&lt;T&gt;.</span></span>

<span data-ttu-id="7a2e2-472">Wróćmy do testów, które napisaliśmy wcześniej i przerobić je, aby użyć makiet zamiast podróbek.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-472">Let’s revisit the tests we wrote earlier and rework them to use mocks instead of fakes.</span></span> <span data-ttu-id="7a2e2-473">Podobnie jak poprzednio, użyjemy klasy podstawowej do skonfigurowania typowych elementów infrastruktury, których potrzebujemy do wszystkich testów kontrolera.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-473">Just like before, we’ll use a base class to setup the common pieces of infrastructure we need for all of the controller’s tests.</span></span>

``` csharp
    public class EmployeeControllerTestBase {
        public EmployeeControllerTestBase() {
            _employeeData = EmployeeObjectMother.CreateEmployees()
                                                .AsQueryable();
            _repository = new Mock<IRepository<Employee>>();
            _unitOfWork = new Mock<IUnitOfWork>();
            _unitOfWork.Setup(u => u.Employees)
                       .Returns(_repository.Object);
            _controller = new EmployeeController(_unitOfWork.Object);
        }

        protected IQueryable<Employee> _employeeData;
        protected Mock<IUnitOfWork> _unitOfWork;
        protected EmployeeController _controller;
        protected Mock<IRepository<Employee>> _repository;
    }
```

<span data-ttu-id="7a2e2-474">Kod konfiguracji pozostaje w większości taki sam.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-474">The setup code remains mostly the same.</span></span> <span data-ttu-id="7a2e2-475">Zamiast używać podróbek, użyjemy Moq do konstruowania makiet obiektów.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-475">Instead of using fakes, we’ll use Moq to construct mock objects.</span></span> <span data-ttu-id="7a2e2-476">Klasa podstawowa rozmieszcza makiety jednostki pracy, aby zwrócić repozytorium makiety, gdy kod wywołuje Employees właściwości.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-476">The base class arranges for the mock unit of work to return a mock repository when code invokes the Employees property.</span></span> <span data-ttu-id="7a2e2-477">Pozostała część konfiguracji makiety odbędzie się wewnątrz opraw testowych dedykowanych każdemu konkretnemu scenariuszowi.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-477">The rest of the mock setup will take place inside the test fixtures dedicated to each specific scenario.</span></span> <span data-ttu-id="7a2e2-478">Na przykład oprawa testowa dla akcji Indeks skonfiguruje makiety repozytorium, aby zwrócić listę pracowników, gdy akcja wywołuje FindAll metody repozytorium makiety.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-478">For example, the test fixture for the Index action will setup the mock repository to return a list of employees when the action invokes the FindAll method of the mock repository.</span></span>

``` csharp
    [TestClass]
    public class EmployeeControllerIndexActionTests
               : EmployeeControllerTestBase {
        public EmployeeControllerIndexActionTests() {
            _repository.Setup(r => r.FindAll())
                        .Returns(_employeeData);
        }
        // .. tests
        [TestMethod]
        public void ShouldBuildModelWithAllEmployees() {
            var result = _controller.Index();
            var model = result.ViewData.Model
                          as IEnumerable<Employee>;
            Assert.IsTrue(model.Count() == _employeeData.Count());
        }
        // .. and more tests
    }
```

<span data-ttu-id="7a2e2-479">Z wyjątkiem oczekiwań, nasze testy wyglądają podobnie do testów, które mieliśmy wcześniej.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-479">Except for the expectations, our tests look similar to the tests we had before.</span></span> <span data-ttu-id="7a2e2-480">Jednak dzięki możliwości rejestrowania makiety struktury możemy podejść do testowania pod innym kątem.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-480">However, with the recording ability of a mock framework we can approach testing from a different angle.</span></span> <span data-ttu-id="7a2e2-481">Przyjrzymy się tej nowej perspektywie w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-481">We’ll look at this new perspective in the next section.</span></span>

### <a name="state-versus-interaction-testing"></a><span data-ttu-id="7a2e2-482">Testowanie stanu a interakcji</span><span class="sxs-lookup"><span data-stu-id="7a2e2-482">State versus Interaction Testing</span></span>

<span data-ttu-id="7a2e2-483">Istnieją różne techniki, których można użyć do testowania oprogramowania z makietami obiektów.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-483">There are different techniques you can use to test software with mock objects.</span></span> <span data-ttu-id="7a2e2-484">Jednym z podejść jest wykorzystanie testów opartych na stanie, co do tej pory zrobiliśmy w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-484">One approach is to use state based testing, which is what we have done in this paper so far.</span></span> <span data-ttu-id="7a2e2-485">Testowanie oparte na stanie sprawia, że potwierdzenia dotyczące stanu oprogramowania.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-485">State based testing makes assertions about the state of the software.</span></span> <span data-ttu-id="7a2e2-486">W ostatnim teście wywołaliśmy metodę akcji na kontrolerze i dokonaliśmy potwierdzenia o modelu, który powinien skompilować.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-486">In the last test we invoked an action method on the controller and made an assertion about the model it should build.</span></span> <span data-ttu-id="7a2e2-487">Oto kilka innych przykładów stanu testowania:</span><span class="sxs-lookup"><span data-stu-id="7a2e2-487">Here are some other examples of testing state:</span></span>

-   <span data-ttu-id="7a2e2-488">Sprawdź, czy repozytorium zawiera nowy obiekt pracownika po wykonaniu create.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-488">Verify the repository contains the new employee object after Create executes.</span></span>
-   <span data-ttu-id="7a2e2-489">Sprawdź, czy model zawiera listę wszystkich pracowników po wykonaniu indeksu.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-489">Verify the model holds a list of all employees after Index executes.</span></span>
-   <span data-ttu-id="7a2e2-490">Sprawdź, czy repozytorium nie zawiera danego pracownika po wykonaniu delete.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-490">Verify the repository does not contain a given employee after Delete executes.</span></span>

<span data-ttu-id="7a2e2-491">Innym podejściem, które zobaczysz z makietami obiektów, jest weryfikowanie *interakcji.*</span><span class="sxs-lookup"><span data-stu-id="7a2e2-491">Another approach you’ll see with mock objects is to verify *interactions*.</span></span> <span data-ttu-id="7a2e2-492">Podczas testowania opartego na stanie sprawia, że potwierdzenia o stanie obiektów, testowanie oparte na interakcji sprawia, że potwierdzenia dotyczące interakcji obiektów.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-492">While state based testing makes assertions about the state of objects, interaction based testing makes assertions about how objects interact.</span></span> <span data-ttu-id="7a2e2-493">Przykład:</span><span class="sxs-lookup"><span data-stu-id="7a2e2-493">For example:</span></span>

-   <span data-ttu-id="7a2e2-494">Sprawdź kontroler wywołuje repozytorium Add metody podczas wykonywania Create.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-494">Verify the controller invokes the repository’s Add method when Create executes.</span></span>
-   <span data-ttu-id="7a2e2-495">Sprawdź kontroler wywołuje repozytorium FindAll metody podczas wykonywania indeksu.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-495">Verify the controller invokes the repository’s FindAll method when Index executes.</span></span>
-   <span data-ttu-id="7a2e2-496">Sprawdź, czy kontroler wywołuje jednostkę pracy Commit metody, aby zapisać zmiany podczas wykonywania edycji.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-496">Verify the controller invokes the unit of work’s Commit method to save changes when Edit executes.</span></span>

<span data-ttu-id="7a2e2-497">Testowanie interakcji często wymaga mniej danych testowych, ponieważ nie grzebie w kolekcjach i nie weryfikujemy liczby.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-497">Interaction testing often requires less test data, because we aren’t poking inside of collections and verifying counts.</span></span> <span data-ttu-id="7a2e2-498">Na przykład jeśli wiemy, szczegóły akcji wywołuje findbyid repozytorium metody z poprawną wartością - następnie akcja prawdopodobnie zachowuje się poprawnie.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-498">For example, if we know the Details action invokes a repository’s FindById method with the correct value - then the action is probably behaving correctly.</span></span> <span data-ttu-id="7a2e2-499">Możemy zweryfikować to zachowanie bez konfigurowania żadnych danych testowych do zwrócenia z FindById.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-499">We can verify this behavior without setting up any test data to return from FindById.</span></span>

``` csharp
    [TestClass]
    public class EmployeeControllerDetailsActionTests
               : EmployeeControllerTestBase {
         // ...
        [TestMethod]
        public void ShouldInvokeRepositoryToFindEmployee() {
            var result = _controller.Details(_detailsId);
            _repository.Verify(r => r.FindById(_detailsId));
        }
        int _detailsId = 1;
    }
```

<span data-ttu-id="7a2e2-500">Jedyną konfiguracją wymaganą w powyższym urządzeniu testowym jest konfiguracja zapewniana przez klasę podstawową.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-500">The only setup required in the above test fixture is the setup provided by the base class.</span></span> <span data-ttu-id="7a2e2-501">Gdy wywołamy akcję kontrolera, Moq będzie rejestrować interakcje z repozytorium makiety.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-501">When we invoke the controller action, Moq will record the interactions with the mock repository.</span></span> <span data-ttu-id="7a2e2-502">Korzystając z interfejsu API Verify moq, możemy zapytać Moq, jeśli kontroler wywołał FindById z właściwą wartością identyfikatora.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-502">Using the Verify API of Moq, we can ask Moq if the controller invoked FindById with the proper ID value.</span></span> <span data-ttu-id="7a2e2-503">Jeśli kontroler nie wywołał metody lub wywołał metodę z nieoczekiwaną wartością parametru, Verify metoda zda wyjątek i test zakończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-503">If the controller did not invoke the method, or invoked the method with an unexpected parameter value, the Verify method will throw an exception and the test will fail.</span></span>

<span data-ttu-id="7a2e2-504">Oto kolejny przykład, aby sprawdzić Create akcji wywołuje Commit na bieżącej jednostki pracy.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-504">Here is another example to verify the Create action invokes Commit on the current unit of work.</span></span>

``` csharp
    [TestMethod]
    public void ShouldCommitUnitOfWork() {
        _controller.Create(_newEmployee);
        _unitOfWork.Verify(u => u.Commit());
    }
```

<span data-ttu-id="7a2e2-505">Jednym z zagrożeń związanych z testowaniem interakcji jest tendencja do określania interakcji.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-505">One danger with interaction testing is the tendency to over specify interactions.</span></span> <span data-ttu-id="7a2e2-506">Możliwość obiektu makiety do rejestrowania i weryfikowania każdej interakcji z obiektem makiety nie oznacza, że test powinien próbować zweryfikować każdą interakcję.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-506">The ability of the mock object to record and verify every interaction with the mock object doesn’t mean the test should try to verify every interaction.</span></span> <span data-ttu-id="7a2e2-507">Niektóre interakcje są szczegóły implementacji i należy tylko sprawdzić interakcje *wymagane* do spełnienia bieżącego testu.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-507">Some interactions are implementation details and you should only verify the interactions *required* to satisfy the current test.</span></span>

<span data-ttu-id="7a2e2-508">Wybór między mocks lub podróbki w dużej mierze zależy od systemu, który testujesz i osobiste (lub zespół) preferencje.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-508">The choice between mocks or fakes largely depends on the system you are testing and your personal (or team) preferences.</span></span> <span data-ttu-id="7a2e2-509">Makiety obiektów można drastycznie zmniejszyć ilość kodu potrzebne do zaimplementowania testu podwaja, ale nie każdy jest wygodne oczekiwania programowania i weryfikacji interakcji.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-509">Mock objects can drastically reduce the amount of code you need to implement test doubles, but not everyone is comfortable programming expectations and verifying interactions.</span></span>

## <a name="conclusions"></a><span data-ttu-id="7a2e2-510">Wnioski</span><span class="sxs-lookup"><span data-stu-id="7a2e2-510">Conclusions</span></span>

<span data-ttu-id="7a2e2-511">W tym dokumencie wykazaliśmy kilka podejść do tworzenia kodu sprawdzalne podczas korzystania z ADO.NET entity framework dla trwałości danych.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-511">In this paper we’ve demonstrated several approaches to creating testable code while using the ADO.NET Entity Framework for data persistence.</span></span> <span data-ttu-id="7a2e2-512">Możemy wykorzystać wbudowane abstrakcje, takie&lt;&gt;jak IObjectSet T, lub&lt;tworzyć&gt;własne abstrakcje, takie jak IRepository T.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-512">We can leverage built in abstractions like IObjectSet&lt;T&gt;, or create our own abstractions like IRepository&lt;T&gt;.</span></span><span data-ttu-id="7a2e2-513">W obu przypadkach obsługa POCO w ADO.NET Entity Framework 4.0 umożliwia konsumentom tych abstrakcji zachować trwałe ignorantów i wysoce sprawdzalne.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-513">  In both cases, the POCO support in the ADO.NET Entity Framework 4.0 allows the consumers of these abstractions to remain persistent ignorant and highly testable.</span></span> <span data-ttu-id="7a2e2-514">Dodatkowe funkcje EF4, takie jak niejawne ładowanie z opóźnieniem umożliwia pracę kodu usługi biznesowej i aplikacji bez martwienia się o szczegóły relacyjnego magazynu danych.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-514">Additional EF4 features like implicit lazy loading allows business and application service code to work without worrying about the details of a relational data store.</span></span> <span data-ttu-id="7a2e2-515">Wreszcie, abstrakcje, które tworzymy są łatwe do wyśmiewania lub fałszywe wewnątrz testów jednostkowych, i możemy użyć tych testów podwaja, aby osiągnąć szybkie uruchamianie, wysoce izolowane i niezawodne testy.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-515">Finally, the abstractions we create are easy to mock or fake inside of unit tests, and we can use these test doubles to achieve fast running, highly isolated, and reliable tests.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="7a2e2-516">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="7a2e2-516">Additional Resources</span></span>

-   <span data-ttu-id="7a2e2-517">Robert C. Martin, " [Zasada jednej odpowiedzialności](https://www.objectmentor.com/resources/articles/srp.pdf)"</span><span class="sxs-lookup"><span data-stu-id="7a2e2-517">Robert C. Martin, “ [The Single Responsibility Principle](https://www.objectmentor.com/resources/articles/srp.pdf)”</span></span>
-   <span data-ttu-id="7a2e2-518">Martin Fowler, [Katalog wzorców](https://www.martinfowler.com/eaaCatalog/index.html) z *wzorców architektury aplikacji dla przedsiębiorstw*</span><span class="sxs-lookup"><span data-stu-id="7a2e2-518">Martin Fowler, [Catalog of Patterns](https://www.martinfowler.com/eaaCatalog/index.html) from *Patterns of Enterprise Application Architecture*</span></span>
-   <span data-ttu-id="7a2e2-519">Griffin Caprio, " [Zastrzyk zależności](https://msdn.microsoft.com/magazine/cc163739.aspx)"</span><span class="sxs-lookup"><span data-stu-id="7a2e2-519">Griffin Caprio, “ [Dependency Injection](https://msdn.microsoft.com/magazine/cc163739.aspx)”</span></span>
-   <span data-ttu-id="7a2e2-520">Blog programowalności danych, " [Przewodnik: Programować oparte na testach z entity framework 4.0](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-test-driven-development-with-the-entity-framework-4-0)".</span><span class="sxs-lookup"><span data-stu-id="7a2e2-520">Data Programmability Blog, “ [Walkthrough: Test Driven Development with the Entity Framework 4.0](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-test-driven-development-with-the-entity-framework-4-0)”.</span></span>
-   <span data-ttu-id="7a2e2-521">Blog programowalności danych" [Korzystanie z repozytorium i wzorców pracy z entity framework 4.0](https://docs.microsoft.com/archive/blogs/adonet/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0)"</span><span class="sxs-lookup"><span data-stu-id="7a2e2-521">Data Programmability Blog, “ [Using Repository and Unit of Work patterns with Entity Framework 4.0](https://docs.microsoft.com/archive/blogs/adonet/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0)”</span></span>
-   <span data-ttu-id="7a2e2-522">Aaron Jensen, " [Introducing Machine Specifications](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)"</span><span class="sxs-lookup"><span data-stu-id="7a2e2-522">Aaron Jensen, “ [Introducing Machine Specifications](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)”</span></span>
-   <span data-ttu-id="7a2e2-523">Eric Lee, " [BDD with MSTest](https://saintgimp.org/2009/01/20/bdd-with-mstest/)"</span><span class="sxs-lookup"><span data-stu-id="7a2e2-523">Eric Lee, “ [BDD with MSTest](https://saintgimp.org/2009/01/20/bdd-with-mstest/)”</span></span>
-   <span data-ttu-id="7a2e2-524">Eric Evans, " [Domain Driven Design](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)"</span><span class="sxs-lookup"><span data-stu-id="7a2e2-524">Eric Evans, “ [Domain Driven Design](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)”</span></span>
-   <span data-ttu-id="7a2e2-525">Martin Fowler, " [Mocks Aren't Stubs](https://martinfowler.com/articles/mocksArentStubs.html)"</span><span class="sxs-lookup"><span data-stu-id="7a2e2-525">Martin Fowler, “ [Mocks Aren’t Stubs](https://martinfowler.com/articles/mocksArentStubs.html)”</span></span>
-   <span data-ttu-id="7a2e2-526">Martin Fowler, " [Test Double](https://martinfowler.com/bliki/TestDouble.html)"</span><span class="sxs-lookup"><span data-stu-id="7a2e2-526">Martin Fowler, “ [Test Double](https://martinfowler.com/bliki/TestDouble.html)”</span></span>
-   [<span data-ttu-id="7a2e2-527">Moq</span><span class="sxs-lookup"><span data-stu-id="7a2e2-527">Moq</span></span>](https://code.google.com/p/moq/)

### <a name="biography"></a><span data-ttu-id="7a2e2-528">Biografia</span><span class="sxs-lookup"><span data-stu-id="7a2e2-528">Biography</span></span>

<span data-ttu-id="7a2e2-529">Scott Allen jest członkiem personelu technicznego w Pluralsight i założycielem OdeToCode.com.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-529">Scott Allen is a member of the technical staff at Pluralsight and the founder of OdeToCode.com.</span></span> <span data-ttu-id="7a2e2-530">W ciągu 15 lat rozwoju komercyjnego oprogramowania Scott pracował nad rozwiązaniami dla wszystkich urządzeń 8-bitowych, po wysoce skalowalne aplikacje internetowe ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="7a2e2-530">In 15 years of commercial software development, Scott has worked on solutions for everything from 8-bit embedded devices to highly scalable ASP.NET web applications.</span></span> <span data-ttu-id="7a2e2-531">Możesz dotrzeć do Scotta na swoim blogu w [https://twitter.com/OdeToCode](https://twitter.com/OdeToCode)OdeToCode lub na Twitterze pod adresem .</span><span class="sxs-lookup"><span data-stu-id="7a2e2-531">You can reach Scott on his blog at OdeToCode, or on Twitter at [https://twitter.com/OdeToCode](https://twitter.com/OdeToCode).</span></span>
