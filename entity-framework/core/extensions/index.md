---
title: Narzędzia & rozszerzenia — EF Core
author: ErikEJ
ms.date: 04/11/2020
ms.assetid: 14fffb6c-a687-4881-a094-af4a1359a296
uid: core/extensions/index
ms.openlocfilehash: 3fa407ca36d32a33a6bc889cd54ff88537510e9c
ms.sourcegitcommit: 387cbd8109c0fc5ce6bdc85d0dec1aed72ad4c33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82103116"
---
# <a name="ef-core-tools--extensions"></a><span data-ttu-id="098df-102">Rozszerzenia narzędzi EF Core &</span><span class="sxs-lookup"><span data-stu-id="098df-102">EF Core Tools & Extensions</span></span>

<span data-ttu-id="098df-103">Te narzędzia i rozszerzenia zapewniają dodatkową funkcjonalność dla Entity Framework Core 2,1 i nowszych.</span><span class="sxs-lookup"><span data-stu-id="098df-103">These tools and extensions provide additional functionality for Entity Framework Core 2.1 and later.</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="098df-104">Rozszerzenia są tworzone przez różne źródła i nie są obsługiwane w ramach projektu Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="098df-104">Extensions are built by a variety of sources and aren't maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="098df-105">Biorąc pod uwagę rozszerzenie innej firmy, pamiętaj o ocenie jego jakości, licencjonowania, zgodności, wsparcia itp., aby upewnić się, że spełnia Twoje wymagania.</span><span class="sxs-lookup"><span data-stu-id="098df-105">When considering a third party extension, be sure to evaluate its quality, licensing, compatibility, support, etc. to ensure it meets your requirements.</span></span> <span data-ttu-id="098df-106">W szczególności rozszerzenie skompilowane dla starszej wersji EF Core może wymagać aktualizacji, zanim będzie działały z najnowszymi wersjami.</span><span class="sxs-lookup"><span data-stu-id="098df-106">In particular, an extension built for an older version of EF Core may need updating before it will work with the latest versions.</span></span>

## <a name="tools"></a><span data-ttu-id="098df-107">narzędzia</span><span class="sxs-lookup"><span data-stu-id="098df-107">Tools</span></span>

### <a name="llblgen-pro"></a><span data-ttu-id="098df-108">LLBLGen Pro</span><span class="sxs-lookup"><span data-stu-id="098df-108">LLBLGen Pro</span></span>

<span data-ttu-id="098df-109">LLBLGen Pro to rozwiązanie do modelowania jednostek z obsługą Entity Framework i Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="098df-109">LLBLGen Pro is an entity modeling solution with support for Entity Framework and Entity Framework Core.</span></span> <span data-ttu-id="098df-110">Umożliwia ona łatwe definiowanie modelu jednostki i mapowanie go do bazy danych przy użyciu najpierw pierwszej lub modelu bazy danych, dzięki czemu możesz od razu zacząć pisać zapytania.</span><span class="sxs-lookup"><span data-stu-id="098df-110">It lets you easily define your entity model and map it to your database, using database first or model first, so you can get started writing queries right away.</span></span> <span data-ttu-id="098df-111">Dla EF Core: 2, 3</span><span class="sxs-lookup"><span data-stu-id="098df-111">For EF Core: 2, 3</span></span>

[<span data-ttu-id="098df-112">witryna sieci web</span><span class="sxs-lookup"><span data-stu-id="098df-112">Website</span></span>](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a><span data-ttu-id="098df-113">Devart Entity Developer</span><span class="sxs-lookup"><span data-stu-id="098df-113">Devart Entity Developer</span></span>

<span data-ttu-id="098df-114">Deweloper jednostki jest zaawansowanym projektantem ORM dla ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access i LINQ to SQL.</span><span class="sxs-lookup"><span data-stu-id="098df-114">Entity Developer is a powerful ORM designer for ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access, and LINQ to SQL.</span></span> <span data-ttu-id="098df-115">Obsługuje ona projektowanie EF Core modeli wizualnie, przy użyciu pierwszej metody modelu lub pierwszej podejścia do bazy danych, a także generowania kodu w języku C# lub Visual Basic.</span><span class="sxs-lookup"><span data-stu-id="098df-115">It supports designing EF Core models visually, using model first or database first approaches, and C# or Visual Basic code generation.</span></span> <span data-ttu-id="098df-116">Dla EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="098df-116">For EF Core: 2.</span></span>

[<span data-ttu-id="098df-117">witryna sieci web</span><span class="sxs-lookup"><span data-stu-id="098df-117">Website</span></span>](https://www.devart.com/entitydeveloper/)

### <a name="nhydrate-orm-for-entity-framework"></a><span data-ttu-id="098df-118">nHydrate ORM dla Entity Framework</span><span class="sxs-lookup"><span data-stu-id="098df-118">nHydrate ORM for Entity Framework</span></span>

<span data-ttu-id="098df-119">Obiekt ORM, który tworzy klasy o jednoznacznie określonym typie, rozszerzalny dla Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="098df-119">An ORM that creates strongly-typed, extendable classes for Entity Framework.</span></span> <span data-ttu-id="098df-120">Wygenerowany kod jest Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="098df-120">The generated code is Entity Framework Core.</span></span> <span data-ttu-id="098df-121">Nie ma żadnej różnicy.</span><span class="sxs-lookup"><span data-stu-id="098df-121">There is no difference.</span></span> <span data-ttu-id="098df-122">Nie jest to zamiennik dla EF lub niestandardowej ORM.</span><span class="sxs-lookup"><span data-stu-id="098df-122">This is not a replacement for EF or a custom ORM.</span></span> <span data-ttu-id="098df-123">Jest to Wizualizacja warstwa modelowania, która umożliwia zespołowi zarządzanie złożonymi schematami bazy danych.</span><span class="sxs-lookup"><span data-stu-id="098df-123">It is a visual, modeling layer that allows a team to manage complex database schemas.</span></span> <span data-ttu-id="098df-124">Dobrze sprawdza się w przypadku oprogramowania SCM, takiego jak Git, umożliwiając dostęp dla użytkowników do modelu z minimalnymi konfliktami.</span><span class="sxs-lookup"><span data-stu-id="098df-124">It works well with SCM software like Git, allowing multi-user access to your model with minimal conflicts.</span></span> <span data-ttu-id="098df-125">Instalator śledzi zmiany modelu i tworzy skrypty uaktualniania.</span><span class="sxs-lookup"><span data-stu-id="098df-125">The installer tracks model changes and creates upgrade scripts.</span></span> <span data-ttu-id="098df-126">Dla EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="098df-126">For EF Core: 3.</span></span>

[<span data-ttu-id="098df-127">Witryna usługi GitHub</span><span class="sxs-lookup"><span data-stu-id="098df-127">Github site</span></span>](https://github.com/nHydrate/nHydrate)

### <a name="ef-core-power-tools"></a><span data-ttu-id="098df-128">EF Core narzędzia do zarządzania</span><span class="sxs-lookup"><span data-stu-id="098df-128">EF Core Power Tools</span></span>

<span data-ttu-id="098df-129">EF Core PowerShell to rozszerzenie programu Visual Studio, które uwidacznia różne EF Core zadania czasu projektowania w prostym interfejsie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="098df-129">EF Core Power Tools is a Visual Studio extension that exposes various EF Core design-time tasks in a simple user interface.</span></span> <span data-ttu-id="098df-130">Obejmuje ona odtwarzanie klas DbContext i Entity Classes z istniejących baz danych, a [SQL Server DACPACs](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications), zarządzanie migracjami baz danych i wizualizacje modeli.</span><span class="sxs-lookup"><span data-stu-id="098df-130">It includes reverse engineering of DbContext and entity classes from existing databases and [SQL Server DACPACs](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications), management of database migrations, and model visualizations.</span></span> <span data-ttu-id="098df-131">Dla EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="098df-131">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="098df-132">Witryna typu wiki usługi GitHub</span><span class="sxs-lookup"><span data-stu-id="098df-132">GitHub wiki</span></span>](https://github.com/ErikEJ/EFCorePowerTools/wiki)

### <a name="entity-framework-visual-editor"></a><span data-ttu-id="098df-133">Entity Framework edytor wizualny</span><span class="sxs-lookup"><span data-stu-id="098df-133">Entity Framework Visual Editor</span></span>

<span data-ttu-id="098df-134">Entity Framework edytorem wizualnym jest rozszerzenie programu Visual Studio, które dodaje projektanta ORM do projektowania wizualizacji Dr 6 i klasy EF Core.</span><span class="sxs-lookup"><span data-stu-id="098df-134">Entity Framework Visual Editor is a Visual Studio extension that adds an ORM designer for visual design of EF 6, and EF Core classes.</span></span> <span data-ttu-id="098df-135">Kod jest generowany przy użyciu szablonów T4, więc można go dostosować do własnych potrzeb.</span><span class="sxs-lookup"><span data-stu-id="098df-135">Code is generated using T4 templates so can be customized to suit any needs.</span></span> <span data-ttu-id="098df-136">Obsługuje dziedziczenie, dwukierunkowe i dwukierunkowe skojarzenia, wyliczenia oraz możliwość kolorowania kodu klas i Dodawanie bloków tekstowych, aby wyjaśnić potencjalnie specjalne części projektu.</span><span class="sxs-lookup"><span data-stu-id="098df-136">It supports inheritance, unidirectional and bidirectional associations, enumerations, and the ability to color-code your classes and add text blocks to explain potentially arcane parts of your design.</span></span> <span data-ttu-id="098df-137">Dla EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="098df-137">For EF Core: 2.</span></span>

[<span data-ttu-id="098df-138">Rynek</span><span class="sxs-lookup"><span data-stu-id="098df-138">Marketplace</span></span>](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner)

### <a name="catfactory"></a><span data-ttu-id="098df-139">CatFactory</span><span class="sxs-lookup"><span data-stu-id="098df-139">CatFactory</span></span>

<span data-ttu-id="098df-140">CatFactory to aparat tworzenia szkieletów dla platformy .NET Core, który umożliwia automatyzację generacji klas DbContext, jednostek, konfiguracji mapowania i klas repozytorium z bazy danych SQL Server.</span><span class="sxs-lookup"><span data-stu-id="098df-140">CatFactory is a scaffolding engine for .NET Core that can automate the generation of DbContext classes, entities, mapping configurations, and repository classes from a SQL Server database.</span></span> <span data-ttu-id="098df-141">Dla EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="098df-141">For EF Core: 2.</span></span>

[<span data-ttu-id="098df-142">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="098df-142">GitHub repository</span></span>](https://github.com/hherzl/CatFactory.EntityFrameworkCore)

### <a name="loresofts-entity-framework-core-generator"></a><span data-ttu-id="098df-143">Generator Entity Framework Core LoreSoft</span><span class="sxs-lookup"><span data-stu-id="098df-143">LoreSoft's Entity Framework Core Generator</span></span>

<span data-ttu-id="098df-144">Generator Entity Framework Core (EFG) to narzędzie interfejs wiersza polecenia platformy .NET Core, które może generować modele EF Core z istniejącej bazy danych, podobnie jak `dotnet ef dbcontext scaffold`, ale również zapewnia bezpieczną [regenerację](https://efg.loresoft.com/en/latest/regeneration/) kodu przez zastąpienie regionu lub przeanalizowanie plików mapowania.</span><span class="sxs-lookup"><span data-stu-id="098df-144">Entity Framework Core Generator (efg) is a .NET Core CLI tool that can generate EF Core models from an existing database, much like `dotnet ef dbcontext scaffold`, but it also supports safe code [regeneration](https://efg.loresoft.com/en/latest/regeneration/) via region replacement or by parsing mapping files.</span></span> <span data-ttu-id="098df-145">To narzędzie obsługuje generowanie modeli widoku, walidacji i kodu mapowania obiektów.</span><span class="sxs-lookup"><span data-stu-id="098df-145">This tool supports generating view models, validation, and object mapper code.</span></span> <span data-ttu-id="098df-146">Dla EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="098df-146">For EF Core: 2.</span></span>

<span data-ttu-id="098df-147">[Dokumentacja samouczka](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[Documentation](https://efg.loresoft.com/en/latest/)</span><span class="sxs-lookup"><span data-stu-id="098df-147">[Tutorial](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[Documentation](https://efg.loresoft.com/en/latest/)</span></span>

## <a name="extensions"></a><span data-ttu-id="098df-148">Rozszerzenia</span><span class="sxs-lookup"><span data-stu-id="098df-148">Extensions</span></span>

### <a name="microsoftentityframeworkcoreautohistory"></a><span data-ttu-id="098df-149">Microsoft.EntityFrameworkCore.AutoHistory</span><span class="sxs-lookup"><span data-stu-id="098df-149">Microsoft.EntityFrameworkCore.AutoHistory</span></span>

<span data-ttu-id="098df-150">Biblioteka wtyczek, która umożliwia automatyczne rejestrowanie zmian danych wykonywanych przez EF Core w tabeli historii.</span><span class="sxs-lookup"><span data-stu-id="098df-150">A plugin library that enables automatically recording the data changes performed by EF Core into a history table.</span></span> <span data-ttu-id="098df-151">Dla EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="098df-151">For EF Core: 2.</span></span>

[<span data-ttu-id="098df-152">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="098df-152">GitHub repository</span></span>](https://github.com/Arch/AutoHistory/)

### <a name="efcoresecondlevelcacheinterceptor"></a><span data-ttu-id="098df-153">EFCoreSecondLevelCacheInterceptor</span><span class="sxs-lookup"><span data-stu-id="098df-153">EFCoreSecondLevelCacheInterceptor</span></span>

<span data-ttu-id="098df-154">Buforowanie drugiego poziomu jest pamięcią podręczną zapytań.</span><span class="sxs-lookup"><span data-stu-id="098df-154">Second level caching is a query cache.</span></span> <span data-ttu-id="098df-155">Wyniki poleceń Dr są przechowywane w pamięci podręcznej, dzięki czemu te same polecenia EF pobierają dane z pamięci podręcznej, a nie ponownie wykonywane w odniesieniu do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="098df-155">The results of EF commands will be stored in the cache, so that the same EF commands will retrieve their data from the cache rather than executing them against the database again.</span></span> <span data-ttu-id="098df-156">Dla EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="098df-156">For EF Core: 3.</span></span>

[<span data-ttu-id="098df-157">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="098df-157">GitHub repository</span></span>](https://github.com/VahidN/EFCoreSecondLevelCacheInterceptor)

### <a name="geco"></a><span data-ttu-id="098df-158">Geco</span><span class="sxs-lookup"><span data-stu-id="098df-158">Geco</span></span>

<span data-ttu-id="098df-159">Geco (konsola generatora) to prosty generator kodu oparty na projekcie konsoli, który działa na platformie .NET Core i używa interpolowanych ciągów języka C# do generowania kodu.</span><span class="sxs-lookup"><span data-stu-id="098df-159">Geco (Generator Console) is a simple code generator based on a console project, that runs on .NET Core and uses C# interpolated strings for code generation.</span></span> <span data-ttu-id="098df-160">Geco obejmuje generator modelu Odwróć dla EF Core z obsługą szablonów pluralizacja, singularization i edytowalnych.</span><span class="sxs-lookup"><span data-stu-id="098df-160">Geco includes a reverse model generator for EF Core with support for pluralization, singularization, and editable templates.</span></span> <span data-ttu-id="098df-161">Udostępnia również Generator skryptów danych inicjatora, moduł uruchamiający skrypty i oczyszczarkę bazy danych.</span><span class="sxs-lookup"><span data-stu-id="098df-161">It also provides a seed data script generator, a script runner, and a database cleaner.</span></span> <span data-ttu-id="098df-162">Dla EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="098df-162">For EF Core: 2.</span></span>

[<span data-ttu-id="098df-163">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="098df-163">GitHub repository</span></span>](https://github.com/iQuarc/Geco)

### <a name="entityframeworkcorescaffoldinghandlebars"></a><span data-ttu-id="098df-164">EntityFrameworkCore. Tworzenie szkieletów. kierownicy</span><span class="sxs-lookup"><span data-stu-id="098df-164">EntityFrameworkCore.Scaffolding.Handlebars</span></span> 

<span data-ttu-id="098df-165">Umożliwia dostosowanie klas odtworzonych z istniejącej bazy danych przy użyciu Entity Framework Core łańcucha narzędzi z szablonami kierownicy.</span><span class="sxs-lookup"><span data-stu-id="098df-165">Allows customization of classes reverse engineered from an existing database using the Entity Framework Core toolchain with Handlebars templates.</span></span> <span data-ttu-id="098df-166">Dla EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="098df-166">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="098df-167">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="098df-167">GitHub repository</span></span>](https://github.com/TrackableEntities/EntityFrameworkCore.Scaffolding.Handlebars)

### <a name="neinlinqentityframeworkcore"></a><span data-ttu-id="098df-168">NeinLinq.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="098df-168">NeinLinq.EntityFrameworkCore</span></span> 

<span data-ttu-id="098df-169">NeinLinq rozszerza dostawców LINQ, takich jak Entity Framework, aby włączyć ponowne używanie funkcji, zapisywania zapytań i kompilowania zapytań dynamicznych przy użyciu predykatów z możliwością tłumaczenia i selektorów.</span><span class="sxs-lookup"><span data-stu-id="098df-169">NeinLinq extends LINQ providers such as Entity Framework to enable reusing functions, rewriting queries, and building dynamic queries using translatable predicates and selectors.</span></span> <span data-ttu-id="098df-170">Dla EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="098df-170">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="098df-171">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="098df-171">GitHub repository</span></span>](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a><span data-ttu-id="098df-172">Microsoft.EntityFrameworkCore.UnitOfWork</span><span class="sxs-lookup"><span data-stu-id="098df-172">Microsoft.EntityFrameworkCore.UnitOfWork</span></span>

<span data-ttu-id="098df-173">Wtyczka dla elementu Microsoft. EntityFrameworkCore do obsługi repozytorium, wzorców jednostek roboczych i wielu baz danych z obsługiwaną transakcją rozproszoną.</span><span class="sxs-lookup"><span data-stu-id="098df-173">A plugin for Microsoft.EntityFrameworkCore to support repository, unit of work patterns, and multiple databases with distributed transaction supported.</span></span> <span data-ttu-id="098df-174">Dla EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="098df-174">For EF Core: 2.</span></span>

[<span data-ttu-id="098df-175">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="098df-175">GitHub repository</span></span>](https://github.com/Arch/UnitOfWork/)

### <a name="efcorebulkextensions"></a><span data-ttu-id="098df-176">EFCore.BulkExtensions</span><span class="sxs-lookup"><span data-stu-id="098df-176">EFCore.BulkExtensions</span></span>

<span data-ttu-id="098df-177">Rozszerzenia EF Core dla operacji zbiorczych (INSERT, Update i Delete).</span><span class="sxs-lookup"><span data-stu-id="098df-177">EF Core extensions for Bulk operations (Insert, Update, Delete).</span></span> <span data-ttu-id="098df-178">Dla EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="098df-178">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="098df-179">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="098df-179">GitHub repository</span></span>](https://github.com/borisdj/EFCore.BulkExtensions)

### <a name="bricelamentityframeworkcorepluralizer"></a><span data-ttu-id="098df-180">Bricelam.EntityFrameworkCore.Pluralizer</span><span class="sxs-lookup"><span data-stu-id="098df-180">Bricelam.EntityFrameworkCore.Pluralizer</span></span>

<span data-ttu-id="098df-181">Dodaje pluralizacja czasu projektowania.</span><span class="sxs-lookup"><span data-stu-id="098df-181">Adds design-time pluralization.</span></span> <span data-ttu-id="098df-182">Dla EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="098df-182">For EF Core: 2.</span></span>

[<span data-ttu-id="098df-183">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="098df-183">GitHub repository</span></span>](https://github.com/bricelam/EFCore.Pluralizer)

### <a name="toolbeltentityframeworkcoreindexattribute"></a><span data-ttu-id="098df-184">Toolbelt. EntityFrameworkCore. Indexattribute</span><span class="sxs-lookup"><span data-stu-id="098df-184">Toolbelt.EntityFrameworkCore.IndexAttribute</span></span>

<span data-ttu-id="098df-185">Revival [index] atrybut (z rozszerzeniem dla kompilowania modelu).</span><span class="sxs-lookup"><span data-stu-id="098df-185">Revival of [Index] attribute (with extension for model building).</span></span> <span data-ttu-id="098df-186">Dla EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="098df-186">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="098df-187">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="098df-187">GitHub repository</span></span>](https://github.com/jsakamoto/EntityFrameworkCore.IndexAttribute)

### <a name="efcoreinmemoryhelpers"></a><span data-ttu-id="098df-188">EfCore.InMemoryHelpers</span><span class="sxs-lookup"><span data-stu-id="098df-188">EfCore.InMemoryHelpers</span></span>

<span data-ttu-id="098df-189">Zawiera otokę wokół EF Core dostawcy bazy danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="098df-189">Provides a wrapper around the EF Core In-Memory Database Provider.</span></span> <span data-ttu-id="098df-190">Sprawia, że działa tak samo jak dostawca relacyjny.</span><span class="sxs-lookup"><span data-stu-id="098df-190">Makes it act more like a relational provider.</span></span> <span data-ttu-id="098df-191">Dla EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="098df-191">For EF Core: 2.</span></span>

[<span data-ttu-id="098df-192">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="098df-192">GitHub repository</span></span>](https://github.com/SimonCropp/EfCore.InMemoryHelpers)

### <a name="efcoretemporalsupport"></a><span data-ttu-id="098df-193">EFCore.TemporalSupport</span><span class="sxs-lookup"><span data-stu-id="098df-193">EFCore.TemporalSupport</span></span>

<span data-ttu-id="098df-194">Implementacja obsługi danych czasowych.</span><span class="sxs-lookup"><span data-stu-id="098df-194">An implementation of temporal support.</span></span> <span data-ttu-id="098df-195">Dla EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="098df-195">For EF Core: 2.</span></span>

[<span data-ttu-id="098df-196">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="098df-196">GitHub repository</span></span>](https://github.com/cpoDesign/EFCore.TemporalSupport)

### <a name="efcoretemporaltable"></a><span data-ttu-id="098df-197">EfCoreTemporalTable</span><span class="sxs-lookup"><span data-stu-id="098df-197">EfCoreTemporalTable</span></span>

<span data-ttu-id="098df-198">Łatwe wykonywanie zapytań czasowych w ulubionej bazie danych przy użyciu wprowadzonych `AsTemporalAsOf(date)`metod `AsTemporalFrom(startDate, endDate)`rozszerzających `AsTemporalContained(startDate, endDate)`: `AsTemporalAll()`,, `AsTemporalBetween(startDate, endDate)`,.</span><span class="sxs-lookup"><span data-stu-id="098df-198">Easily perform temporal queries on your favourite database using introduced extension methods: `AsTemporalAll()`, `AsTemporalAsOf(date)`, `AsTemporalFrom(startDate, endDate)`, `AsTemporalBetween(startDate, endDate)`, `AsTemporalContained(startDate, endDate)`.</span></span> <span data-ttu-id="098df-199">Dla EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="098df-199">For EF Core: 3.</span></span>

[<span data-ttu-id="098df-200">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="098df-200">GitHub repository</span></span>](https://github.com/glautrou/EfCoreTemporalTable)

### <a name="efcoretimetraveler"></a><span data-ttu-id="098df-201">EFCore.TimeTraveler</span><span class="sxs-lookup"><span data-stu-id="098df-201">EFCore.TimeTraveler</span></span>

<span data-ttu-id="098df-202">Zezwalaj na w pełni funkcjonalne zapytania Entity Framework Core w odniesieniu do [SQL Server historii](/sql/relational-databases/tables/temporal-table-usage-scenarios#point-in-time-analysis-time-travel) danych czasowych przy użyciu zdefiniowanego w EF Core kodu, jednostek i mapowań.</span><span class="sxs-lookup"><span data-stu-id="098df-202">Allow full-featured Entity Framework Core queries against [SQL Server Temporal History](/sql/relational-databases/tables/temporal-table-usage-scenarios#point-in-time-analysis-time-travel) using the EF Core code, entities, and mappings you already have defined.</span></span>  <span data-ttu-id="098df-203">Poruszaj się po czasie, zawijając `using (TemporalQuery.AsOf(targetDateTime)) {...}`kod w.</span><span class="sxs-lookup"><span data-stu-id="098df-203">Travel through time by wrapping your code in `using (TemporalQuery.AsOf(targetDateTime)) {...}`.</span></span> <span data-ttu-id="098df-204">Dla EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="098df-204">For EF Core: 3.</span></span>

[<span data-ttu-id="098df-205">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="098df-205">GitHub repository</span></span>](https://github.com/VantageSoftware/EFCore.TimeTraveler)


### <a name="entityframeworkcoretemporaltables"></a><span data-ttu-id="098df-206">EntityFrameworkCore.TemporalTables</span><span class="sxs-lookup"><span data-stu-id="098df-206">EntityFrameworkCore.TemporalTables</span></span>

<span data-ttu-id="098df-207">Biblioteka rozszerzeń dla Entity Framework Core, która umożliwia deweloperom, którzy używają SQL Server do łatwego korzystania z tabel danych czasowych.</span><span class="sxs-lookup"><span data-stu-id="098df-207">Extension library for Entity Framework Core which allows developers who use SQL Server to easily use temporal tables.</span></span> <span data-ttu-id="098df-208">Dla EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="098df-208">For EF Core: 2.</span></span>

[<span data-ttu-id="098df-209">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="098df-209">GitHub repository</span></span>](https://github.com/findulov/EntityFrameworkCore.TemporalTables)


### <a name="entityframeworkcorecacheable"></a><span data-ttu-id="098df-210">EntityFrameworkCore. buforowanie</span><span class="sxs-lookup"><span data-stu-id="098df-210">EntityFrameworkCore.Cacheable</span></span>

<span data-ttu-id="098df-211">Pamięć podręczna zapytań o wysokiej wydajności.</span><span class="sxs-lookup"><span data-stu-id="098df-211">A high-performance second-level query cache.</span></span> <span data-ttu-id="098df-212">Dla EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="098df-212">For EF Core: 2.</span></span>

[<span data-ttu-id="098df-213">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="098df-213">GitHub repository</span></span>](https://github.com/SteffenMangold/EntityFrameworkCore.Cacheable)

### <a name="entity-framework-plus"></a><span data-ttu-id="098df-214">Entity Framework Plus</span><span class="sxs-lookup"><span data-stu-id="098df-214">Entity Framework Plus</span></span>

<span data-ttu-id="098df-215">Rozszerza kontekst DbContext z funkcjami takimi jak: Filter include, Audit, buforowanie, Future Query, Batch Delete, Batch Update i innych.</span><span class="sxs-lookup"><span data-stu-id="098df-215">Extends your DbContext with features such as: Include Filter, Auditing, Caching, Query Future, Batch Delete, Batch Update, and more.</span></span> <span data-ttu-id="098df-216">Dla EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="098df-216">For EF Core: 2, 3.</span></span>

<span data-ttu-id="098df-217">[Website](https://entityframework-plus.net/)
[Repozytorium GitHub](https://github.com/zzzprojects/EntityFramework-Plus) witryny sieci Web</span><span class="sxs-lookup"><span data-stu-id="098df-217">[Website](https://entityframework-plus.net/)
[GitHub repository](https://github.com/zzzprojects/EntityFramework-Plus)</span></span>

### <a name="entity-framework-extensions"></a><span data-ttu-id="098df-218">Rozszerzenia Entity Framework</span><span class="sxs-lookup"><span data-stu-id="098df-218">Entity Framework Extensions</span></span>

<span data-ttu-id="098df-219">Rozszerza swój kontekst dbwith operacji zbiorczych o wysokiej wydajności: BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge i inne.</span><span class="sxs-lookup"><span data-stu-id="098df-219">Extends your DbContext with high-performance bulk operations: BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge, and more.</span></span> <span data-ttu-id="098df-220">Dla EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="098df-220">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="098df-221">witryna sieci web</span><span class="sxs-lookup"><span data-stu-id="098df-221">Website</span></span>](https://entityframework-extensions.net/)

### <a name="expressionify"></a><span data-ttu-id="098df-222">Expressionify</span><span class="sxs-lookup"><span data-stu-id="098df-222">Expressionify</span></span>

<span data-ttu-id="098df-223">Dodano obsługę wywoływania metod rozszerzających w składniku LINQ lambda.</span><span class="sxs-lookup"><span data-stu-id="098df-223">Add support for calling extension methods in LINQ lambdas.</span></span> <span data-ttu-id="098df-224">Dla EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="098df-224">For EF Core: 3.</span></span>

[<span data-ttu-id="098df-225">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="098df-225">GitHub repository</span></span>](https://github.com/ClaveConsulting/Expressionify)

### <a name="xlinq"></a><span data-ttu-id="098df-226">XLinq</span><span class="sxs-lookup"><span data-stu-id="098df-226">XLinq</span></span>

<span data-ttu-id="098df-227">Technologia Integrated Language Query (LINQ) dla relacyjnych baz danych.</span><span class="sxs-lookup"><span data-stu-id="098df-227">Language Integrated Query (LINQ) technology for relational databases.</span></span> <span data-ttu-id="098df-228">Pozwala na używanie języka C# do pisania kwerend o jednoznacznie określonym typie.</span><span class="sxs-lookup"><span data-stu-id="098df-228">It allows you to use C# to write strongly typed queries.</span></span> <span data-ttu-id="098df-229">Dla EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="098df-229">For EF Core: 3.</span></span>

- <span data-ttu-id="098df-230">Pełna obsługa języka C# na potrzeby tworzenia zapytań: wiele instrukcji wewnątrz wyrażenia lambda, zmienne, funkcje itd.</span><span class="sxs-lookup"><span data-stu-id="098df-230">Full C# support for query creation: multiple statements inside lambda, variables, functions, etc.</span></span>
- <span data-ttu-id="098df-231">Brak przerwy semantycznej z SQL.</span><span class="sxs-lookup"><span data-stu-id="098df-231">No semantic gap with SQL.</span></span> <span data-ttu-id="098df-232">XLinq deklaruje instrukcje języka SQL `SELECT`( `FROM`takie `WHERE`jak,,) jako metody pierwszej klasy języka C#, łącząc znaną składnię z technologią IntelliSense, bezpieczeństwo typów i refaktoryzację.</span><span class="sxs-lookup"><span data-stu-id="098df-232">XLinq declares SQL statements (like `SELECT`, `FROM`, `WHERE`) as first class C# methods, combining familiar syntax with intellisense, type safety and refactoring.</span></span>

<span data-ttu-id="098df-233">W związku z tym, w wyniku tego, w programie SQL Server, "inna" Biblioteka klas uwidacznia interfejs API lokalnie, dosłownie *"język zintegrowany języka SQL"*.</span><span class="sxs-lookup"><span data-stu-id="098df-233">As a result SQL becomes just "another" class library exposing its API locally, literally *"Language Integrated SQL"*.</span></span>

[<span data-ttu-id="098df-234">witryna sieci web</span><span class="sxs-lookup"><span data-stu-id="098df-234">Website</span></span>](http://xlinq.live/)

### <a name="ramses"></a><span data-ttu-id="098df-235">Ramses</span><span class="sxs-lookup"><span data-stu-id="098df-235">Ramses</span></span>

<span data-ttu-id="098df-236">Punkty zaczepienia cyklu życia (dla metody SaveChanges).</span><span class="sxs-lookup"><span data-stu-id="098df-236">Lifecycle hooks (for SaveChanges).</span></span> <span data-ttu-id="098df-237">Dla EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="098df-237">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="098df-238">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="098df-238">GitHub repository</span></span>](https://github.com/JValck/Ramses)

### <a name="efcorenamingconventions"></a><span data-ttu-id="098df-239">EFCore.NamingConventions</span><span class="sxs-lookup"><span data-stu-id="098df-239">EFCore.NamingConventions</span></span>

<span data-ttu-id="098df-240">Spowoduje to automatyczne wprowadzenie wszystkich nazw tabel i kolumn, które mają snake_case, wielkie lub małe litery.</span><span class="sxs-lookup"><span data-stu-id="098df-240">This will automatically make all your table and column names have snake_case, all UPPER or all lower case naming.</span></span> <span data-ttu-id="098df-241">Dla EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="098df-241">For EF Core: 3.</span></span>

[<span data-ttu-id="098df-242">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="098df-242">GitHub repository</span></span>](https://github.com/efcore/EFCore.NamingConventions)

### <a name="simplersoftwareentityframeworkcoresqlservernodatime"></a><span data-ttu-id="098df-243">SimplerSoftware. EntityFrameworkCore. SqlServer. NodaTime</span><span class="sxs-lookup"><span data-stu-id="098df-243">SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime</span></span>

<span data-ttu-id="098df-244">Dodaje natywną obsługę EntityFrameworkCore SQL Server dla typów NodaTime.</span><span class="sxs-lookup"><span data-stu-id="098df-244">Adds native support to EntityFrameworkCore for SQL Server for the NodaTime types.</span></span> <span data-ttu-id="098df-245">Dla EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="098df-245">For EF Core: 3.</span></span>

[<span data-ttu-id="098df-246">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="098df-246">GitHub repository</span></span>](https://github.com/StevenRasmussen/EFCore.SqlServer.NodaTime)

### <a name="dabbleentityframeworkcoretemporalquery"></a><span data-ttu-id="098df-247">Dabble. EntityFrameworkCore. czasowo. Query</span><span class="sxs-lookup"><span data-stu-id="098df-247">Dabble.EntityFrameworkCore.Temporal.Query</span></span>

<span data-ttu-id="098df-248">Rozszerzenia LINQ do Entity Framework Core 3,1 do obsługi Microsoft SQL Server danych czasowych w tabelach.</span><span class="sxs-lookup"><span data-stu-id="098df-248">LINQ extensions to Entity Framework Core 3.1 to support Microsoft SQL Server Temporal Table Querying.</span></span> <span data-ttu-id="098df-249">Dla EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="098df-249">For EF Core: 3.</span></span>

[<span data-ttu-id="098df-250">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="098df-250">GitHub repository</span></span>](https://github.com/Adam-Langley/efcore-temporal-query)
