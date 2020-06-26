---
title: Planowanie dla Entity Framework Core 5,0
author: ajcvickers
ms.date: 06/11/2020
uid: core/what-is-new/ef-core-5.0/plan
ms.openlocfilehash: 249560bc14f72fd524be91bb1670dbaf78ae6b60
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370581"
---
# <a name="plan-for-entity-framework-core-50"></a><span data-ttu-id="cc12f-102">Planowanie dla Entity Framework Core 5,0</span><span class="sxs-lookup"><span data-stu-id="cc12f-102">Plan for Entity Framework Core 5.0</span></span>

<span data-ttu-id="cc12f-103">Zgodnie z opisem w [procesie planowania](xref:core/what-is-new/release_planning)dane wejściowe od uczestników projektu zostały zebrane w ramach wstępnego planu dla wydania EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="cc12f-103">As described in the [planning process](xref:core/what-is-new/release_planning), we have gathered input from stakeholders into a tentative plan for the EF Core 5.0 release.</span></span>

> [!IMPORTANT] 
> <span data-ttu-id="cc12f-104">Ten plan jest nadal wykonywany w toku.</span><span class="sxs-lookup"><span data-stu-id="cc12f-104">This plan is still a work-in-progress.</span></span> <span data-ttu-id="cc12f-105">Nic tutaj jest zobowiązaniem.</span><span class="sxs-lookup"><span data-stu-id="cc12f-105">Nothing here is a commitment.</span></span> <span data-ttu-id="cc12f-106">Ten plan jest punktem początkowym, który będzie się rozwijać, gdy douczymy się więcej.</span><span class="sxs-lookup"><span data-stu-id="cc12f-106">This plan is a starting point that will evolve as we learn more.</span></span> <span data-ttu-id="cc12f-107">Niektóre elementy, które nie są obecnie planowane dla 5,0, mogą zostać pobrane.</span><span class="sxs-lookup"><span data-stu-id="cc12f-107">Some things not currently planned for 5.0 may get pulled in.</span></span> <span data-ttu-id="cc12f-108">Niektóre elementy aktualnie planowane dla 5,0 mogą zostać punted.</span><span class="sxs-lookup"><span data-stu-id="cc12f-108">Some things currently planned for 5.0 may get punted out.</span></span>

### <a name="version-number-and-release-date"></a><span data-ttu-id="cc12f-109">Numer wersji i Data wydania.</span><span class="sxs-lookup"><span data-stu-id="cc12f-109">Version number and release date.</span></span>

<span data-ttu-id="cc12f-110">EF Core 5,0 jest obecnie zaplanowane do wydania w ramach [programu .net 5,0](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span><span class="sxs-lookup"><span data-stu-id="cc12f-110">EF Core 5.0 is currently scheduled for release at [the same time as .NET 5.0](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span></span> <span data-ttu-id="cc12f-111">Wybrano wersję "5,0" do dopasowania z platformą .NET 5,0.</span><span class="sxs-lookup"><span data-stu-id="cc12f-111">The version "5.0" was chosen to align with .NET 5.0.</span></span>

### <a name="supported-platforms"></a><span data-ttu-id="cc12f-112">Obsługiwane platformy</span><span class="sxs-lookup"><span data-stu-id="cc12f-112">Supported platforms</span></span>

<span data-ttu-id="cc12f-113">EF Core 5,0 jest planowane do uruchamiania na dowolnej platformie .NET 5,0 w oparciu o [zbieżność tych platform w programie .NET Core](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span><span class="sxs-lookup"><span data-stu-id="cc12f-113">EF Core 5.0 is planned to run on any .NET 5.0 platform based on the [convergence of these platforms to .NET Core](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span></span> <span data-ttu-id="cc12f-114">Co to oznacza .NET Standard i rzeczywiste użycie TFM nadal będzie możliwe do ustalenia.</span><span class="sxs-lookup"><span data-stu-id="cc12f-114">What this means in terms of .NET Standard and the actual TFM used is still TBD.</span></span>

<span data-ttu-id="cc12f-115">EF Core 5,0 nie będzie działać na .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="cc12f-115">EF Core 5.0 will not run on .NET Framework.</span></span>

### <a name="breaking-changes"></a><span data-ttu-id="cc12f-116">Fundamentalne zmiany</span><span class="sxs-lookup"><span data-stu-id="cc12f-116">Breaking changes</span></span>

<span data-ttu-id="cc12f-117">EF Core 5,0 będzie zawierać pewne istotne [zmiany](xref:core/what-is-new/ef-core-5.0/breaking-changes), ale będą znacznie mniej surowe niż w przypadku EF Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="cc12f-117">EF Core 5.0 will contain some [breaking changes](xref:core/what-is-new/ef-core-5.0/breaking-changes), but these will be much less severe than was the case for EF Core 3.0.</span></span> <span data-ttu-id="cc12f-118">Naszym celem jest umożliwienie nieprzerwanego aktualizowania większości aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cc12f-118">Our goal is to allow the vast majority of applications to update without breaking.</span></span>

<span data-ttu-id="cc12f-119">Oczekuje się, że istnieją istotne zmiany dotyczące dostawców baz danych, szczególnie w przypadku pomocy technicznej TPT.</span><span class="sxs-lookup"><span data-stu-id="cc12f-119">It is expected that there will be some breaking changes for database providers, especially around TPT support.</span></span> <span data-ttu-id="cc12f-120">Jednak oczekujemy, że aktualizacja dostawcy na 5,0 będzie mniejsza niż wymagana do zaktualizowania dla 3,0.</span><span class="sxs-lookup"><span data-stu-id="cc12f-120">However, we expect the work to update a provider for 5.0 will be less than was required to update for 3.0.</span></span>

## <a name="themes"></a><span data-ttu-id="cc12f-121">Motywy</span><span class="sxs-lookup"><span data-stu-id="cc12f-121">Themes</span></span>

<span data-ttu-id="cc12f-122">Wyodrębnimy kilka głównych obszarów lub motywów, które będą stanowić podstawę dużych inwestycji w EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="cc12f-122">We have extracted a few major areas or themes which will form the basis for the large investments in EF Core 5.0.</span></span>

## <a name="fully-transparent-many-to-many-mapping-by-convention"></a><span data-ttu-id="cc12f-123">Pełne przezroczyste mapowanie wiele-do-wielu według Konwencji</span><span class="sxs-lookup"><span data-stu-id="cc12f-123">Fully transparent many-to-many mapping by convention</span></span>

<span data-ttu-id="cc12f-124">Potencjalni deweloperzy: @smitpatel , @AndriySvyryd , i@lajones</span><span class="sxs-lookup"><span data-stu-id="cc12f-124">Lead developers: @smitpatel, @AndriySvyryd, and @lajones</span></span>

<span data-ttu-id="cc12f-125">Śledzone przez [#10508](https://github.com/aspnet/EntityFrameworkCore/issues/10508)</span><span class="sxs-lookup"><span data-stu-id="cc12f-125">Tracked by [#10508](https://github.com/aspnet/EntityFrameworkCore/issues/10508)</span></span>

<span data-ttu-id="cc12f-126">Rozmiar koszulki: L</span><span class="sxs-lookup"><span data-stu-id="cc12f-126">T-shirt size: L</span></span>

<span data-ttu-id="cc12f-127">Stan: w toku</span><span class="sxs-lookup"><span data-stu-id="cc12f-127">Status: In-progress</span></span>

<span data-ttu-id="cc12f-128">Funkcja wiele-do-wielu jest [najbardziej żądaną funkcją](https://github.com/aspnet/EntityFrameworkCore/issues/1368) (~ 506 głosów) w zaległości usługi GitHub.</span><span class="sxs-lookup"><span data-stu-id="cc12f-128">Many-to-many is the [most requested feature](https://github.com/aspnet/EntityFrameworkCore/issues/1368) (~506 votes) on the GitHub backlog.</span></span>

<span data-ttu-id="cc12f-129">Obsługa relacji wiele-do-wielu można podzielić na trzy główne obszary:</span><span class="sxs-lookup"><span data-stu-id="cc12f-129">Support for many-to-many relationships can be broken down into three major areas:</span></span>

* <span data-ttu-id="cc12f-130">Pomiń właściwości nawigacji — zakryte przez następny motyw.</span><span class="sxs-lookup"><span data-stu-id="cc12f-130">Skip navigation properties--covered by the next theme.</span></span>
* <span data-ttu-id="cc12f-131">Typy jednostek zbioru właściwości.</span><span class="sxs-lookup"><span data-stu-id="cc12f-131">Property-bag entity types.</span></span> <span data-ttu-id="cc12f-132">Umożliwiają one użycie standardowego typu CLR (np. `Dictionary` ) dla wystąpień jednostek, w taki sposób, że jawny typ CLR nie jest wymagany dla każdego typu jednostki.</span><span class="sxs-lookup"><span data-stu-id="cc12f-132">These allow a standard CLR type (e.g. `Dictionary`) to be used for entity instances such that an explicit CLR type is not needed for each entity type.</span></span> <span data-ttu-id="cc12f-133">Śledzone przez [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914).</span><span class="sxs-lookup"><span data-stu-id="cc12f-133">Tracked by [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914).</span></span>
* <span data-ttu-id="cc12f-134">Cukier pozwala na łatwą konfigurację relacji wiele-do-wielu.</span><span class="sxs-lookup"><span data-stu-id="cc12f-134">Sugar for easy configuration of many-to-many relationships.</span></span>

<span data-ttu-id="cc12f-135">Oprócz pomocy technicznej dotyczącej pomijania nawigacji teraz pobieramy te inne obszary z wielu do wielu do EF Core 5,0, aby zapewnić pełne środowisko pracy.</span><span class="sxs-lookup"><span data-stu-id="cc12f-135">In addition to the skip navigation support, we are now pulling these other areas of many-to-many into EF Core 5.0 so as to provide a complete experience.</span></span>

## <a name="many-to-many-navigation-properties-aka-skip-navigations"></a><span data-ttu-id="cc12f-136">Właściwości nawigacji wiele-do-wielu (a. k. a "pomijanie nawigacji")</span><span class="sxs-lookup"><span data-stu-id="cc12f-136">Many-to-many navigation properties (a.k.a "skip navigations")</span></span>

<span data-ttu-id="cc12f-137">Potencjalni deweloperzy: @smitpatel i@AndriySvyryd</span><span class="sxs-lookup"><span data-stu-id="cc12f-137">Lead developers: @smitpatel and @AndriySvyryd</span></span>

<span data-ttu-id="cc12f-138">Śledzone przez [#19003](https://github.com/aspnet/EntityFrameworkCore/issues/19003)</span><span class="sxs-lookup"><span data-stu-id="cc12f-138">Tracked by [#19003](https://github.com/aspnet/EntityFrameworkCore/issues/19003)</span></span>

<span data-ttu-id="cc12f-139">Rozmiar koszulki: L</span><span class="sxs-lookup"><span data-stu-id="cc12f-139">T-shirt size: L</span></span>

<span data-ttu-id="cc12f-140">Stan: w toku</span><span class="sxs-lookup"><span data-stu-id="cc12f-140">Status: In-progress</span></span>

<span data-ttu-id="cc12f-141">Zgodnie z opisem w pierwszym motywie obsługa wiele-do-wielu ma wiele aspektów.</span><span class="sxs-lookup"><span data-stu-id="cc12f-141">As described in the first theme, many-to-many support has multiple aspects.</span></span>
<span data-ttu-id="cc12f-142">Ten motyw służy do śledzenia użycia nawigacji pomijania.</span><span class="sxs-lookup"><span data-stu-id="cc12f-142">This theme specifically tracks use of skip navigations.</span></span>
<span data-ttu-id="cc12f-143">Uważamy, że najbardziej znaczący blok dla tych, które chcą uzyskać pomoc techniczną wiele-do-wielu, nie ma możliwości używania "naturalnych" relacji bez odwołujących się do tabeli sprzężenia w logice biznesowej, takiej jak zapytania.</span><span class="sxs-lookup"><span data-stu-id="cc12f-143">We believe that the most significant blocker for those wanting many-to-many support is not being able to use the "natural" relationships, without referring to the join table, in business logic such as queries.</span></span>
<span data-ttu-id="cc12f-144">Typ jednostki połączonej tabeli może nadal istnieć, ale nie powinien się on znajdować w sposób logiki biznesowej.</span><span class="sxs-lookup"><span data-stu-id="cc12f-144">The join table entity type may still exist, but it should not get in the way of business logic.</span></span>

## <a name="table-per-type-tpt-inheritance-mapping"></a><span data-ttu-id="cc12f-145">Mapowanie dziedziczenia według typu tabeli (TPT)</span><span class="sxs-lookup"><span data-stu-id="cc12f-145">Table-per-type (TPT) inheritance mapping</span></span>

<span data-ttu-id="cc12f-146">Deweloper potencjalnego klienta:@AndriySvyryd</span><span class="sxs-lookup"><span data-stu-id="cc12f-146">Lead developer: @AndriySvyryd</span></span>

<span data-ttu-id="cc12f-147">Śledzone przez [#2266](https://github.com/aspnet/EntityFrameworkCore/issues/2266)</span><span class="sxs-lookup"><span data-stu-id="cc12f-147">Tracked by [#2266](https://github.com/aspnet/EntityFrameworkCore/issues/2266)</span></span>

<span data-ttu-id="cc12f-148">Rozmiar koszulki: XL</span><span class="sxs-lookup"><span data-stu-id="cc12f-148">T-shirt size: XL</span></span>

<span data-ttu-id="cc12f-149">Stan: w toku</span><span class="sxs-lookup"><span data-stu-id="cc12f-149">Status: In-progress</span></span>

<span data-ttu-id="cc12f-150">Wykonujemy TPT, ponieważ jest to zarówno wysoce żądana funkcja (~ 289 głosów; trzecia ogólna), ponieważ wymaga ona pewnych zmian niskiego poziomu, które są odpowiednie dla ogólnego charakteru planu .NET 5.</span><span class="sxs-lookup"><span data-stu-id="cc12f-150">We're doing TPT because it is both a highly requested feature (~289 votes; 3rd overall) and because it requires some low-level changes that we feel are appropriate for the foundational nature of the overall .NET 5 plan.</span></span> <span data-ttu-id="cc12f-151">Oczekujemy, że spowoduje to powstanie istotnych zmian dla dostawców baz danych, chociaż powinny one być znacznie mniej surowe niż zmiany wymagane przez 3,0.</span><span class="sxs-lookup"><span data-stu-id="cc12f-151">We expect this to result in breaking changes for database providers, although these should be much less severe than the changes required for 3.0.</span></span>

## <a name="filtered-include"></a><span data-ttu-id="cc12f-152">Filtr obejmujący</span><span class="sxs-lookup"><span data-stu-id="cc12f-152">Filtered Include</span></span>

<span data-ttu-id="cc12f-153">Deweloper potencjalnego klienta:@maumar</span><span class="sxs-lookup"><span data-stu-id="cc12f-153">Lead developer: @maumar</span></span>

<span data-ttu-id="cc12f-154">Śledzone przez [#1833](https://github.com/aspnet/EntityFrameworkCore/issues/1833)</span><span class="sxs-lookup"><span data-stu-id="cc12f-154">Tracked by [#1833](https://github.com/aspnet/EntityFrameworkCore/issues/1833)</span></span>

<span data-ttu-id="cc12f-155">Rozmiar koszulki: M</span><span class="sxs-lookup"><span data-stu-id="cc12f-155">T-shirt size: M</span></span>

<span data-ttu-id="cc12f-156">Stan: w toku</span><span class="sxs-lookup"><span data-stu-id="cc12f-156">Status: In-progress</span></span>

<span data-ttu-id="cc12f-157">Filtrowanie include to wysoce żądana funkcja (~ 376 głosów; druga ogólna), która nie jest ogromną ilością pracy, i że firma Microsoft uważa, że nie będzie można zablokować lub ułatwić wielu scenariuszy, które obecnie wymagają filtrów na poziomie modelu lub bardziej złożonych zapytań.</span><span class="sxs-lookup"><span data-stu-id="cc12f-157">Filtered Include is a highly-requested feature (~376 votes; 2nd overall) that isn't a huge amount of work, and that we believe will unblock or make easier many scenarios that currently require model-level filters or more complex queries.</span></span>

## <a name="split-include"></a><span data-ttu-id="cc12f-158">Podziel dołączenie</span><span class="sxs-lookup"><span data-stu-id="cc12f-158">Split Include</span></span>

<span data-ttu-id="cc12f-159">Deweloper potencjalnego klienta:@smitpatel</span><span class="sxs-lookup"><span data-stu-id="cc12f-159">Lead developer: @smitpatel</span></span>

<span data-ttu-id="cc12f-160">Śledzone przez [#20892](https://github.com/dotnet/efcore/issues/20892)</span><span class="sxs-lookup"><span data-stu-id="cc12f-160">Tracked by [#20892](https://github.com/dotnet/efcore/issues/20892)</span></span>

<span data-ttu-id="cc12f-161">Rozmiar koszulki: L</span><span class="sxs-lookup"><span data-stu-id="cc12f-161">T-shirt size: L</span></span>

<span data-ttu-id="cc12f-162">Stan: w toku</span><span class="sxs-lookup"><span data-stu-id="cc12f-162">Status: In-progress</span></span>

<span data-ttu-id="cc12f-163">EF Core 3,0 zmieniono zachowanie domyślne w celu utworzenia pojedynczego zapytania SQL dla danego zapytania LINQ.</span><span class="sxs-lookup"><span data-stu-id="cc12f-163">EF Core 3.0 changed the default behavior to create a single SQL query for a given LINQ query.</span></span>
<span data-ttu-id="cc12f-164">Spowodowało to duże regresje wydajności dla zapytań, które używają elementu include dla wielu kolekcji.</span><span class="sxs-lookup"><span data-stu-id="cc12f-164">This caused large performance regressions for queries that use Include for multiple collections.</span></span>

<span data-ttu-id="cc12f-165">W EF Core 5,0 zachowywane jest nowe zachowanie domyślne.</span><span class="sxs-lookup"><span data-stu-id="cc12f-165">In EF Core 5.0, we are retaining the new default behavior.</span></span>
<span data-ttu-id="cc12f-166">Jednak EF Core 5,0 zezwoli teraz na generowanie wielu zapytań dla kolekcji, gdzie istnieje pojedyncze zapytanie powodujące niską wydajność.</span><span class="sxs-lookup"><span data-stu-id="cc12f-166">However, EF Core 5.0 will now allow generation of multiple queries for collection Includes where having a single query is causing bad performance..</span></span> 

## <a name="rationalize-totable-toquery-toview-fromsql-etc"></a><span data-ttu-id="cc12f-167">Racjonalizacja ToTable, ToQuery, ToView, Z tabel itp.</span><span class="sxs-lookup"><span data-stu-id="cc12f-167">Rationalize ToTable, ToQuery, ToView, FromSql, etc.</span></span>

<span data-ttu-id="cc12f-168">Potencjalni deweloperzy: @maumar i@smitpatel</span><span class="sxs-lookup"><span data-stu-id="cc12f-168">Lead developers: @maumar and @smitpatel</span></span>

<span data-ttu-id="cc12f-169">Śledzone przez [#17270](https://github.com/aspnet/EntityFrameworkCore/issues/17270)</span><span class="sxs-lookup"><span data-stu-id="cc12f-169">Tracked by [#17270](https://github.com/aspnet/EntityFrameworkCore/issues/17270)</span></span>

<span data-ttu-id="cc12f-170">Rozmiar koszulki: L</span><span class="sxs-lookup"><span data-stu-id="cc12f-170">T-shirt size: L</span></span>

<span data-ttu-id="cc12f-171">Stan: w toku</span><span class="sxs-lookup"><span data-stu-id="cc12f-171">Status: In-progress</span></span>

<span data-ttu-id="cc12f-172">Wprowadziliśmy postępy we wcześniejszych wersjach na potrzeby obsługi nieprzetworzonych, typów i niezwiązanych z programem SQL.</span><span class="sxs-lookup"><span data-stu-id="cc12f-172">We have made progress in previous releases towards supporting raw SQL, keyless types, and related areas.</span></span> <span data-ttu-id="cc12f-173">Istnieją jednak zarówno luki, jak i niespójności w sposób, w jaki wszystko działa razem jako całość.</span><span class="sxs-lookup"><span data-stu-id="cc12f-173">However, there are both gaps and inconsistencies in the way everything works together as a whole.</span></span> <span data-ttu-id="cc12f-174">Celem 5,0 jest rozwiązanie tego problemu i utworzenie dobrego środowiska do definiowania, migrowania i korzystania z różnych typów jednostek oraz skojarzonych z nimi zapytań i artefaktów baz danych.</span><span class="sxs-lookup"><span data-stu-id="cc12f-174">The goal for 5.0 is to fix these and create a good experience for defining, migrating, and using different types of entities and their associated queries and database artifacts.</span></span> <span data-ttu-id="cc12f-175">Może to również dotyczyć aktualizacji skompilowanego interfejsu API zapytań.</span><span class="sxs-lookup"><span data-stu-id="cc12f-175">This may also involve updates to the compiled query API.</span></span>

<span data-ttu-id="cc12f-176">Należy zauważyć, że ten element może spowodować pewne zmiany na poziomie aplikacji, ponieważ niektóre funkcje obecnie są zbyt ograniczane, dzięki czemu mogą szybko prowadzić do Pits awarii.</span><span class="sxs-lookup"><span data-stu-id="cc12f-176">Note that this item may result in some application-level breaking changes since some of the functionality we currently have is too permissive such that it can quickly lead people into pits of failure.</span></span> <span data-ttu-id="cc12f-177">Prawdopodobnie zakończy się blokowanie niektórych z tych funkcji wraz ze wskazówkami dotyczącymi tego, co należy zrobić.</span><span class="sxs-lookup"><span data-stu-id="cc12f-177">We will likely end up blocking some of this functionality together with guidance on what to do instead.</span></span>

## <a name="general-query-enhancements"></a><span data-ttu-id="cc12f-178">Ogólne ulepszenia zapytań</span><span class="sxs-lookup"><span data-stu-id="cc12f-178">General query enhancements</span></span>

<span data-ttu-id="cc12f-179">Potencjalni deweloperzy: @smitpatel i@maumar</span><span class="sxs-lookup"><span data-stu-id="cc12f-179">Lead developers: @smitpatel and @maumar</span></span>

<span data-ttu-id="cc12f-180">Śledzone przez [problemy oznaczone za pomocą `area-query` w obszarze kontrolnym 5,0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)</span><span class="sxs-lookup"><span data-stu-id="cc12f-180">Tracked by [issues labeled with `area-query` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="cc12f-181">Rozmiar koszulki: XL</span><span class="sxs-lookup"><span data-stu-id="cc12f-181">T-shirt size: XL</span></span>

<span data-ttu-id="cc12f-182">Stan: w toku</span><span class="sxs-lookup"><span data-stu-id="cc12f-182">Status: In-progress</span></span>

<span data-ttu-id="cc12f-183">Kod tłumaczenia zapytania został rozbudowany na EF Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="cc12f-183">The query translation code was extensively rewritten for EF Core 3.0.</span></span> <span data-ttu-id="cc12f-184">W związku z tym kod zapytania jest zazwyczaj bardziej niezawodny.</span><span class="sxs-lookup"><span data-stu-id="cc12f-184">The query code is generally in a much more robust state because of this.</span></span> <span data-ttu-id="cc12f-185">W przypadku 5,0 nie planuje się wprowadzania istotnych zmian zapytania poza tymi, które są potrzebne do obsługi TPT i pomijania właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="cc12f-185">For 5.0 we aren't planning on making major query changes, outside those needed to support TPT and skip navigation properties.</span></span> <span data-ttu-id="cc12f-186">Jednak nadal istnieje znacząca konieczność naprawienia długu technicznego pozostałego w porównaniu z 3,0.</span><span class="sxs-lookup"><span data-stu-id="cc12f-186">However, there is still significant work needed to fix some technical debt left over from the 3.0 overhaul.</span></span> <span data-ttu-id="cc12f-187">Planuje również Rozwiązywanie problemów z wieloma usterkami i zaimplementowanie małych ulepszeń w celu dalszej poprawy ogólnego środowiska zapytań.</span><span class="sxs-lookup"><span data-stu-id="cc12f-187">We also plan to fix many bugs and implement small enhancements to further improve the overall query experience.</span></span>

## <a name="migrations-and-deployment-experience"></a><span data-ttu-id="cc12f-188">Migracje i środowisko wdrażania</span><span class="sxs-lookup"><span data-stu-id="cc12f-188">Migrations and deployment experience</span></span>

<span data-ttu-id="cc12f-189">Potencjalni deweloperzy:@bricelam</span><span class="sxs-lookup"><span data-stu-id="cc12f-189">Lead developers: @bricelam</span></span>

<span data-ttu-id="cc12f-190">Śledzone przez [#19587](https://github.com/dotnet/efcore/issues/19587)</span><span class="sxs-lookup"><span data-stu-id="cc12f-190">Tracked by [#19587](https://github.com/dotnet/efcore/issues/19587)</span></span>

<span data-ttu-id="cc12f-191">Rozmiar koszulki: L</span><span class="sxs-lookup"><span data-stu-id="cc12f-191">T-shirt size: L</span></span>

<span data-ttu-id="cc12f-192">Stan: w toku</span><span class="sxs-lookup"><span data-stu-id="cc12f-192">Status: In-progress</span></span>

<span data-ttu-id="cc12f-193">Obecnie wielu deweloperów migruje swoje bazy danych podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cc12f-193">Currently, many developers migrate their databases at application startup time.</span></span> <span data-ttu-id="cc12f-194">Jest to proste, ale nie jest zalecane, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="cc12f-194">This is easy but is not recommended because:</span></span>

* <span data-ttu-id="cc12f-195">Wiele wątków/procesów/serwerów może próbować przeprowadzić migrację bazy danych współbieżnie</span><span class="sxs-lookup"><span data-stu-id="cc12f-195">Multiple threads/processes/servers may attempt to migrate the database concurrently</span></span>
* <span data-ttu-id="cc12f-196">Aplikacje mogą próbować uzyskać dostęp do niespójnego stanu, gdy jest to wykonywane</span><span class="sxs-lookup"><span data-stu-id="cc12f-196">Applications may try to access inconsistent state while this is happening</span></span>
* <span data-ttu-id="cc12f-197">Zwykle uprawnienia bazy danych do modyfikacji schematu nie należy przyznawać do wykonania aplikacji</span><span class="sxs-lookup"><span data-stu-id="cc12f-197">Usually the database permissions to modify the schema should not be granted for application execution</span></span>
* <span data-ttu-id="cc12f-198">Jeśli coś się nie stanie, trudno wrócić do stanu czystego</span><span class="sxs-lookup"><span data-stu-id="cc12f-198">It's hard to revert back to a clean state if something goes wrong</span></span>

<span data-ttu-id="cc12f-199">Chcemy zapewnić lepsze środowisko w tym miejscu, które pozwala na łatwe Migrowanie bazy danych w czasie wdrażania.</span><span class="sxs-lookup"><span data-stu-id="cc12f-199">We want to deliver a better experience here that allows an easy way to migrate the database at deployment time.</span></span> <span data-ttu-id="cc12f-200">Powinno to być:</span><span class="sxs-lookup"><span data-stu-id="cc12f-200">This should:</span></span>

* <span data-ttu-id="cc12f-201">Pracuj w systemach Linux, Mac i Windows</span><span class="sxs-lookup"><span data-stu-id="cc12f-201">Work on Linux, Mac, and Windows</span></span>
* <span data-ttu-id="cc12f-202">Być dobrym doświadczeniem w wierszu polecenia</span><span class="sxs-lookup"><span data-stu-id="cc12f-202">Be a good experience on the command line</span></span>
* <span data-ttu-id="cc12f-203">Scenariusze pomocy technicznej z kontenerami</span><span class="sxs-lookup"><span data-stu-id="cc12f-203">Support scenarios with containers</span></span>
* <span data-ttu-id="cc12f-204">Pracuj z powszechnie używanymi rzeczywistymi narzędziami/przepływami wdrażania</span><span class="sxs-lookup"><span data-stu-id="cc12f-204">Work with commonly used real-world deployment tools/flows</span></span>
* <span data-ttu-id="cc12f-205">Integruj do co najmniej programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc12f-205">Integrate into at least Visual Studio</span></span>

<span data-ttu-id="cc12f-206">Może to być wiele małych ulepszeń w EF Core (na przykład lepszych migracji przy użyciu oprogramowania SQLite), a także wskazówki i średniookresowe współpracę z innymi zespołami w celu ulepszania kompleksowych środowisk, które wykraczają poza prawie Dr.</span><span class="sxs-lookup"><span data-stu-id="cc12f-206">The result is likely to be many small improvements in EF Core (for example, better Migrations on SQLite), together with guidance and longer-term collaborations with other teams to improve end-to-end experiences that go beyond just EF.</span></span>

## <a name="ef-core-platforms-experience"></a><span data-ttu-id="cc12f-207">Środowisko EF Core platform</span><span class="sxs-lookup"><span data-stu-id="cc12f-207">EF Core platforms experience</span></span> 

<span data-ttu-id="cc12f-208">Potencjalni deweloperzy: @roji i@bricelam</span><span class="sxs-lookup"><span data-stu-id="cc12f-208">Lead developers: @roji and @bricelam</span></span>

<span data-ttu-id="cc12f-209">Śledzone przez [#19588](https://github.com/dotnet/efcore/issues/19588)</span><span class="sxs-lookup"><span data-stu-id="cc12f-209">Tracked by [#19588](https://github.com/dotnet/efcore/issues/19588)</span></span>

<span data-ttu-id="cc12f-210">Rozmiar koszulki: L</span><span class="sxs-lookup"><span data-stu-id="cc12f-210">T-shirt size: L</span></span>

<span data-ttu-id="cc12f-211">Stan: nie uruchomiono</span><span class="sxs-lookup"><span data-stu-id="cc12f-211">Status: Not started</span></span>

<span data-ttu-id="cc12f-212">Mamy dobre wskazówki dotyczące używania EF Core w tradycyjnych aplikacjach internetowych opartych na standardzie MVC.</span><span class="sxs-lookup"><span data-stu-id="cc12f-212">We have good guidance for using EF Core in traditional MVC-like web applications.</span></span> <span data-ttu-id="cc12f-213">Brak wskazówek dotyczących innych platform i modeli aplikacji albo są one nieaktualne.</span><span class="sxs-lookup"><span data-stu-id="cc12f-213">Guidance for other platforms and application models is either missing or out-of-date.</span></span> <span data-ttu-id="cc12f-214">W przypadku EF Core 5,0 planujemy zbadać, ulepszyć i udokumentować środowisko korzystania z EF Core z:</span><span class="sxs-lookup"><span data-stu-id="cc12f-214">For EF Core 5.0, we plan to investigate, improve, and document the experience of using EF Core with:</span></span>

* <span data-ttu-id="cc12f-215">Blazor</span><span class="sxs-lookup"><span data-stu-id="cc12f-215">Blazor</span></span>
* <span data-ttu-id="cc12f-216">Środowisko Xamarin, w tym używanie scenariusza AOT/konsolidatora</span><span class="sxs-lookup"><span data-stu-id="cc12f-216">Xamarin, including using the AOT/linker story</span></span>
* <span data-ttu-id="cc12f-217">WinForms/WPF/WinUI i prawdopodobnie inne U.I.</span><span class="sxs-lookup"><span data-stu-id="cc12f-217">WinForms/WPF/WinUI and possibly other U.I.</span></span> <span data-ttu-id="cc12f-218">platform</span><span class="sxs-lookup"><span data-stu-id="cc12f-218">frameworks</span></span>

<span data-ttu-id="cc12f-219">Jest to prawdopodobnie wiele małych ulepszeń w EF Core, wraz ze wskazówkami i dłuższymi współpracy z innymi zespołami, aby ulepszyć kompleksowe środowiska, które wykraczają poza prawie Dr.</span><span class="sxs-lookup"><span data-stu-id="cc12f-219">This is likely to be many small improvements in EF Core, together with guidance and longer-term collaborations with other teams to improve end-to-end experiences that go beyond just EF.</span></span>

<span data-ttu-id="cc12f-220">Określone obszary, które planujemy sprawdzić, to:</span><span class="sxs-lookup"><span data-stu-id="cc12f-220">Specific areas we plan to look at are:</span></span>

* <span data-ttu-id="cc12f-221">Wdrożenie, w tym środowisko do korzystania z narzędzi EF, takich jak migracja</span><span class="sxs-lookup"><span data-stu-id="cc12f-221">Deployment, including the experience for using EF tooling such as for Migrations</span></span>
* <span data-ttu-id="cc12f-222">Modele aplikacji, w tym Xamarin i Blazor, a prawdopodobnie inne</span><span class="sxs-lookup"><span data-stu-id="cc12f-222">Application models, including Xamarin and Blazor, and probably others</span></span>
* <span data-ttu-id="cc12f-223">Środowiska oprogramowania SQLite, w tym środowisko przestrzenne i ponowne kompilacje tabel</span><span class="sxs-lookup"><span data-stu-id="cc12f-223">SQLite experiences, including the spatial experience and table rebuilds</span></span>
* <span data-ttu-id="cc12f-224">AOT i łącza — środowisko</span><span class="sxs-lookup"><span data-stu-id="cc12f-224">AOT and linking experiences</span></span>
* <span data-ttu-id="cc12f-225">Integracja diagnostyki, w tym liczników wydajności</span><span class="sxs-lookup"><span data-stu-id="cc12f-225">Diagnostics integration, including perf counters</span></span>

## <a name="performance"></a><span data-ttu-id="cc12f-226">Wydajność</span><span class="sxs-lookup"><span data-stu-id="cc12f-226">Performance</span></span>

<span data-ttu-id="cc12f-227">Deweloper potencjalnego klienta:@roji</span><span class="sxs-lookup"><span data-stu-id="cc12f-227">Lead developer: @roji</span></span>

<span data-ttu-id="cc12f-228">Śledzone przez [problemy oznaczone za pomocą `area-perf` w obszarze kontrolnym 5,0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)</span><span class="sxs-lookup"><span data-stu-id="cc12f-228">Tracked by [issues labeled with `area-perf` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="cc12f-229">Rozmiar koszulki: L</span><span class="sxs-lookup"><span data-stu-id="cc12f-229">T-shirt size: L</span></span>

<span data-ttu-id="cc12f-230">Stan: w toku</span><span class="sxs-lookup"><span data-stu-id="cc12f-230">Status: In-progress</span></span>

<span data-ttu-id="cc12f-231">W przypadku EF Core planujemy udoskonalić nasz pakiet testów wydajnościowych i wprowadzić ukierunkowane ulepszenia wydajności środowiska uruchomieniowego.</span><span class="sxs-lookup"><span data-stu-id="cc12f-231">For EF Core, we plan to improve our suite of performance benchmarks and make directed performance improvements to the runtime.</span></span> <span data-ttu-id="cc12f-232">Ponadto planujemy ukończenie nowego interfejsu API tworzenia wsadowego ADO.NET, który został utworzony w ramach cyklu wydania 3,0.</span><span class="sxs-lookup"><span data-stu-id="cc12f-232">In addition, we plan to complete the new ADO.NET batching API which was prototyped during the 3.0 release cycle.</span></span> <span data-ttu-id="cc12f-233">Ponadto w warstwie ADO.NET planujemy dodatkowe ulepszenia wydajności dla dostawcy Npgsql.</span><span class="sxs-lookup"><span data-stu-id="cc12f-233">Also at the ADO.NET layer, we plan additional performance improvements to the Npgsql provider.</span></span>

<span data-ttu-id="cc12f-234">W ramach tej pracy zaplanowano również dodanie ADO.NET/EF podstawowych liczników wydajności i innych elementów diagnostycznych odpowiednio do potrzeb.</span><span class="sxs-lookup"><span data-stu-id="cc12f-234">As part of this work we also plan to add ADO.NET/EF Core performance counters and other diagnostics as appropriate.</span></span>

## <a name="architecturalcontributor-documentation"></a><span data-ttu-id="cc12f-235">Dokumentacja architektury/współautora</span><span class="sxs-lookup"><span data-stu-id="cc12f-235">Architectural/contributor documentation</span></span>

<span data-ttu-id="cc12f-236">Dokument potencjalnego klienta:@ajcvickers</span><span class="sxs-lookup"><span data-stu-id="cc12f-236">Lead documenter: @ajcvickers</span></span>

<span data-ttu-id="cc12f-237">Śledzone przez [#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920)</span><span class="sxs-lookup"><span data-stu-id="cc12f-237">Tracked by [#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920)</span></span>

<span data-ttu-id="cc12f-238">Rozmiar koszulki: L</span><span class="sxs-lookup"><span data-stu-id="cc12f-238">T-shirt size: L</span></span>

<span data-ttu-id="cc12f-239">Stan: Wytnij</span><span class="sxs-lookup"><span data-stu-id="cc12f-239">Status: Cut</span></span>

<span data-ttu-id="cc12f-240">Tutaj warto ułatwić zrozumienie, co się dzieje w wewnętrznych EF Core.</span><span class="sxs-lookup"><span data-stu-id="cc12f-240">The idea here is to make it easier to understand what is going on in the internals of EF Core.</span></span> <span data-ttu-id="cc12f-241">Może to być przydatne dla każdej osoby korzystającej z EF Core, ale podstawową motywacją jest ułatwienie użytkownikom zewnętrznym:</span><span class="sxs-lookup"><span data-stu-id="cc12f-241">This can be useful to anyone using EF Core, but the primary motivation is to make it easier for external people to:</span></span>

* <span data-ttu-id="cc12f-242">Współtworzenie kodu EF Core</span><span class="sxs-lookup"><span data-stu-id="cc12f-242">Contribute to the EF Core code</span></span>
* <span data-ttu-id="cc12f-243">Tworzenie dostawców baz danych</span><span class="sxs-lookup"><span data-stu-id="cc12f-243">Create database providers</span></span>
* <span data-ttu-id="cc12f-244">Kompiluj inne rozszerzenia</span><span class="sxs-lookup"><span data-stu-id="cc12f-244">Build other extensions</span></span>

<span data-ttu-id="cc12f-245">Aktualizacja: Niestety, ten plan był zbyt ambitne podejście.</span><span class="sxs-lookup"><span data-stu-id="cc12f-245">Update: Unfortunately, this plan was too ambitious.</span></span>
<span data-ttu-id="cc12f-246">Nadal uważamy, że jest to ważne, ale nie będzie to miało wpływu na EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="cc12f-246">We still believe this is important, but unfortunately it won't land with EF Core 5.0.</span></span>

## <a name="microsoftdatasqlite-documentation"></a><span data-ttu-id="cc12f-247">Dokumentacja Microsoft. Data. sqlite</span><span class="sxs-lookup"><span data-stu-id="cc12f-247">Microsoft.Data.Sqlite documentation</span></span>

<span data-ttu-id="cc12f-248">Dokument potencjalnego klienta:@bricelam</span><span class="sxs-lookup"><span data-stu-id="cc12f-248">Lead documenter: @bricelam</span></span>

<span data-ttu-id="cc12f-249">Śledzone przez [#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675)</span><span class="sxs-lookup"><span data-stu-id="cc12f-249">Tracked by [#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675)</span></span>

<span data-ttu-id="cc12f-250">Rozmiar koszulki: M</span><span class="sxs-lookup"><span data-stu-id="cc12f-250">T-shirt size: M</span></span>

<span data-ttu-id="cc12f-251">Stan: ukończono.</span><span class="sxs-lookup"><span data-stu-id="cc12f-251">Status: Completed.</span></span> <span data-ttu-id="cc12f-252">Nowa dokumentacja znajduje się [na żywo w witrynie Microsoft docs](https://docs.microsoft.com/dotnet/standard/data/sqlite/?tabs=netcore-cli).</span><span class="sxs-lookup"><span data-stu-id="cc12f-252">The new documentation is [live on the Microsoft docs site](https://docs.microsoft.com/dotnet/standard/data/sqlite/?tabs=netcore-cli).</span></span>

<span data-ttu-id="cc12f-253">Zespół EF również jest właścicielem dostawcy ADO.NET Microsoft. Data. sqlite.</span><span class="sxs-lookup"><span data-stu-id="cc12f-253">The EF Team also owns the Microsoft.Data.Sqlite ADO.NET provider.</span></span> <span data-ttu-id="cc12f-254">Planujemy w pełni udokumentować tego dostawcę w wersji 5,0.</span><span class="sxs-lookup"><span data-stu-id="cc12f-254">We plan to fully document this provider as part of the 5.0 release.</span></span>

## <a name="general-documentation"></a><span data-ttu-id="cc12f-255">Ogólna dokumentacja</span><span class="sxs-lookup"><span data-stu-id="cc12f-255">General documentation</span></span>

<span data-ttu-id="cc12f-256">Dokument potencjalnego klienta:@ajcvickers</span><span class="sxs-lookup"><span data-stu-id="cc12f-256">Lead documenter: @ajcvickers</span></span>

<span data-ttu-id="cc12f-257">Śledzone przez [problemy w repozytorium docs w obszarze kontrolnym 5,0](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)</span><span class="sxs-lookup"><span data-stu-id="cc12f-257">Tracked by [issues in the docs repo in the 5.0 milestone](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="cc12f-258">Rozmiar koszulki: L</span><span class="sxs-lookup"><span data-stu-id="cc12f-258">T-shirt size: L</span></span>

<span data-ttu-id="cc12f-259">Stan: w toku</span><span class="sxs-lookup"><span data-stu-id="cc12f-259">Status: In-progress</span></span>

<span data-ttu-id="cc12f-260">Już trwa proces aktualizacji dokumentacji dla wersji 3,0 i 3,1.</span><span class="sxs-lookup"><span data-stu-id="cc12f-260">We are already in the process of updating documentation for the 3.0 and 3.1 releases.</span></span> <span data-ttu-id="cc12f-261">Pracujemy również nad:</span><span class="sxs-lookup"><span data-stu-id="cc12f-261">We are also working on:</span></span>
  * <span data-ttu-id="cc12f-262">Remontowanie dokumentów wprowadzających wprowadzenie, aby zwiększyć ich podejście/łatwiejsze w obserwowanie</span><span class="sxs-lookup"><span data-stu-id="cc12f-262">An overhaul of the getting started docs to make them more approachable/easier to follow</span></span>
  * <span data-ttu-id="cc12f-263">Reorganizacja dokumentów, aby ułatwić znajdowanie i Dodawanie odsyłaczy</span><span class="sxs-lookup"><span data-stu-id="cc12f-263">Reorganization of docs to make things easier to find and to add cross-references</span></span>
  * <span data-ttu-id="cc12f-264">Dodawanie dalszych szczegółów i wyjaśnień do istniejących dokumentów</span><span class="sxs-lookup"><span data-stu-id="cc12f-264">Adding more details and clarifications to existing docs</span></span>
  * <span data-ttu-id="cc12f-265">Aktualizowanie przykładów i Dodawanie kolejnych przykładów</span><span class="sxs-lookup"><span data-stu-id="cc12f-265">Updating the samples and adding more examples</span></span>

## <a name="fixing-bugs"></a><span data-ttu-id="cc12f-266">Naprawianie usterek</span><span class="sxs-lookup"><span data-stu-id="cc12f-266">Fixing bugs</span></span>

<span data-ttu-id="cc12f-267">Śledzone przez [problemy oznaczone za pomocą `type-bug` w obszarze kontrolnym 5,0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)</span><span class="sxs-lookup"><span data-stu-id="cc12f-267">Tracked by [issues labeled with `type-bug` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)</span></span>

<span data-ttu-id="cc12f-268">Deweloperzy: @roji , @maumar ,,, @bricelam @smitpatel @AndriySvyryd ,@ajcvickers</span><span class="sxs-lookup"><span data-stu-id="cc12f-268">Developers: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd, @ajcvickers</span></span>

<span data-ttu-id="cc12f-269">Rozmiar koszulki: L</span><span class="sxs-lookup"><span data-stu-id="cc12f-269">T-shirt size: L</span></span>

<span data-ttu-id="cc12f-270">Stan: w toku</span><span class="sxs-lookup"><span data-stu-id="cc12f-270">Status: In-progress</span></span>

<span data-ttu-id="cc12f-271">W momencie pisania mamy 135 błędów zaklasyfikowany w wersji 5,0 (z 62 już Naprawiono), ale istnieje duże nakładanie się na sekcję _Ogólne ulepszenia zapytania_ .</span><span class="sxs-lookup"><span data-stu-id="cc12f-271">At the time of writing, we have 135 bugs triaged to be fixed in the 5.0 release (with 62 already fixed), but there is significant overlap with the _General query enhancements_ section above.</span></span>

<span data-ttu-id="cc12f-272">Szybkość odbierania (problemy, które kończą się w trakcie pracy w kontrolce), dotyczyła 23 problemów miesięcznie w trakcie wydania 3,0.</span><span class="sxs-lookup"><span data-stu-id="cc12f-272">The incoming rate (issues that end up as work in a milestone) was about 23 issues per month over the course of the 3.0 release.</span></span> <span data-ttu-id="cc12f-273">Nie wszystkie z nich będą musiały zostać naprawione w 5,0.</span><span class="sxs-lookup"><span data-stu-id="cc12f-273">Not all of these will need to be fixed in 5.0.</span></span> <span data-ttu-id="cc12f-274">Jako przybliżony szacunek planujemy rozwiązać dodatkowe 150 problemów w przedziale czasowym 5,0.</span><span class="sxs-lookup"><span data-stu-id="cc12f-274">As a rough estimate we plan to fix an additional 150 issues in the 5.0 time frame.</span></span>

## <a name="small-enhancements"></a><span data-ttu-id="cc12f-275">Małe ulepszenia</span><span class="sxs-lookup"><span data-stu-id="cc12f-275">Small enhancements</span></span>

<span data-ttu-id="cc12f-276">Śledzone przez [problemy oznaczone za pomocą `type-enhancement` w obszarze kontrolnym 5,0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)</span><span class="sxs-lookup"><span data-stu-id="cc12f-276">Tracked by [issues labeled with `type-enhancement` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)</span></span>

<span data-ttu-id="cc12f-277">Deweloperzy: @roji , @maumar ,,, @bricelam @smitpatel @AndriySvyryd ,@ajcvickers</span><span class="sxs-lookup"><span data-stu-id="cc12f-277">Developers: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd, @ajcvickers</span></span>

<span data-ttu-id="cc12f-278">Rozmiar koszulki: L</span><span class="sxs-lookup"><span data-stu-id="cc12f-278">T-shirt size: L</span></span>

<span data-ttu-id="cc12f-279">Stan: w toku</span><span class="sxs-lookup"><span data-stu-id="cc12f-279">Status: In-progress</span></span>

<span data-ttu-id="cc12f-280">Oprócz większych funkcji opisanych powyżej, firma Microsoft oferuje również wiele mniejszych ulepszeń zaplanowanych na 5,0, aby naprawić "kawałki papieru".</span><span class="sxs-lookup"><span data-stu-id="cc12f-280">In addition to the bigger features outlined above, we also have many smaller improvements scheduled for 5.0 to fix "paper-cuts".</span></span> <span data-ttu-id="cc12f-281">Należy zauważyć, że wiele z tych ulepszeń jest również objętych bardziej ogólnymi motywami opisanymi powyżej.</span><span class="sxs-lookup"><span data-stu-id="cc12f-281">Note that many of these enhancements are also covered by the more general themes outlined above.</span></span>

## <a name="below-the-line"></a><span data-ttu-id="cc12f-282">Poniżej — wiersz</span><span class="sxs-lookup"><span data-stu-id="cc12f-282">Below-the-line</span></span>

<span data-ttu-id="cc12f-283">Śledzone przez [problemy oznaczone przy `consider-for-next-release` użyciu](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)</span><span class="sxs-lookup"><span data-stu-id="cc12f-283">Tracked by [issues labeled with `consider-for-next-release`](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)</span></span>

<span data-ttu-id="cc12f-284">Są to poprawki i udoskonalenia błędów, które **nie** są obecnie planowane dla wydania 5,0, ale będziemy przebiegać zgodnie z przeznaczeniem do rozciągnięcia w zależności od postępu wykonywanego powyżej.</span><span class="sxs-lookup"><span data-stu-id="cc12f-284">These are bug fixes and enhancements that are **not** currently scheduled for the 5.0 release, but we will look at as stretch goals depending on the progress made on the work above.</span></span>

<span data-ttu-id="cc12f-285">Ponadto zawsze należy wziąć pod uwagę [najczęstsze problemy związane](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) z planowaniem.</span><span class="sxs-lookup"><span data-stu-id="cc12f-285">In addition, we always consider the [most voted issues](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) when planning.</span></span> <span data-ttu-id="cc12f-286">Wszystkie te problemy w wersji są zawsze bolesnym, ale potrzebujemy realistycznego planu dla zasobów, które mamy.</span><span class="sxs-lookup"><span data-stu-id="cc12f-286">Cutting any of these issues from a release is always painful, but we do need a realistic plan for the resources we have.</span></span>

## <a name="feedback"></a><span data-ttu-id="cc12f-287">Opinia</span><span class="sxs-lookup"><span data-stu-id="cc12f-287">Feedback</span></span>

<span data-ttu-id="cc12f-288">Twoja opinia na temat planowania jest ważna.</span><span class="sxs-lookup"><span data-stu-id="cc12f-288">Your feedback on planning is important.</span></span> <span data-ttu-id="cc12f-289">Najlepszym sposobem na wskazanie znaczenia problemu jest zagłosowanie (kciuki) dla tego problemu w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="cc12f-289">The best way to indicate the importance of an issue is to vote (thumbs-up) for that issue on GitHub.</span></span> <span data-ttu-id="cc12f-290">Te dane zostaną następnie [przetworzone do procesu planowania](xref:core/what-is-new/release_planning) dla kolejnej wersji.</span><span class="sxs-lookup"><span data-stu-id="cc12f-290">This data will then feed into the [planning process](xref:core/what-is-new/release_planning) for the next release.</span></span>
