---
title: Narzędzia & rozszerzenia - EF Core
author: ErikEJ
ms.date: 04/11/2020
ms.assetid: 14fffb6c-a687-4881-a094-af4a1359a296
uid: core/extensions/index
ms.openlocfilehash: 7727229fc50a4bfd39e05481399e392037402396
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434113"
---
# <a name="ef-core-tools--extensions"></a><span data-ttu-id="38388-102">EF Core Tools & rozszerzenia</span><span class="sxs-lookup"><span data-stu-id="38388-102">EF Core Tools & Extensions</span></span>

<span data-ttu-id="38388-103">Te narzędzia i rozszerzenia zapewniają dodatkowe funkcje dla entity framework core 2.1 i nowsze.</span><span class="sxs-lookup"><span data-stu-id="38388-103">These tools and extensions provide additional functionality for Entity Framework Core 2.1 and later.</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="38388-104">Rozszerzenia są tworzone przez różne źródła i nie są obsługiwane w ramach projektu Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="38388-104">Extensions are built by a variety of sources and aren't maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="38388-105">Rozważając rozszerzenie innej firmy, pamiętaj, aby ocenić jego jakość, licencjonowanie, kompatybilność, wsparcie itp., aby upewnić się, że spełnia Twoje wymagania.</span><span class="sxs-lookup"><span data-stu-id="38388-105">When considering a third party extension, be sure to evaluate its quality, licensing, compatibility, support, etc. to ensure it meets your requirements.</span></span> <span data-ttu-id="38388-106">W szczególności rozszerzenie utworzone dla starszej wersji ef core może wymagać aktualizacji, zanim będzie działać z najnowszymi wersjami.</span><span class="sxs-lookup"><span data-stu-id="38388-106">In particular, an extension built for an older version of EF Core may need updating before it will work with the latest versions.</span></span>

## <a name="tools"></a><span data-ttu-id="38388-107">Narzędzia</span><span class="sxs-lookup"><span data-stu-id="38388-107">Tools</span></span>

### <a name="llblgen-pro"></a><span data-ttu-id="38388-108">LLBLGen Pro</span><span class="sxs-lookup"><span data-stu-id="38388-108">LLBLGen Pro</span></span>

<span data-ttu-id="38388-109">LLBLGen Pro to rozwiązanie do modelowania jednostek z obsługą entity framework i Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="38388-109">LLBLGen Pro is an entity modeling solution with support for Entity Framework and Entity Framework Core.</span></span> <span data-ttu-id="38388-110">Umożliwia łatwe definiowanie modelu jednostki i mapowanie go do bazy danych, najpierw przy użyciu bazy danych lub modelu, dzięki czemu można rozpocząć pisanie zapytań od razu.</span><span class="sxs-lookup"><span data-stu-id="38388-110">It lets you easily define your entity model and map it to your database, using database first or model first, so you can get started writing queries right away.</span></span> <span data-ttu-id="38388-111">Dla EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="38388-111">For EF Core: 2.</span></span>

[<span data-ttu-id="38388-112">witryna sieci web</span><span class="sxs-lookup"><span data-stu-id="38388-112">Website</span></span>](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a><span data-ttu-id="38388-113">Devart Entity Developer</span><span class="sxs-lookup"><span data-stu-id="38388-113">Devart Entity Developer</span></span>

<span data-ttu-id="38388-114">Entity Developer jest zaawansowanym projektantem ORM dla ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access i LINQ do SQL.</span><span class="sxs-lookup"><span data-stu-id="38388-114">Entity Developer is a powerful ORM designer for ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access, and LINQ to SQL.</span></span> <span data-ttu-id="38388-115">Obsługuje projektowanie modeli EF Core wizualnie, przy użyciu modelu pierwszy lub bazy danych pierwsze podejścia i C# lub Visual Basic generowania kodu.</span><span class="sxs-lookup"><span data-stu-id="38388-115">It supports designing EF Core models visually, using model first or database first approaches, and C# or Visual Basic code generation.</span></span> <span data-ttu-id="38388-116">Dla EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="38388-116">For EF Core: 2.</span></span>

[<span data-ttu-id="38388-117">witryna sieci web</span><span class="sxs-lookup"><span data-stu-id="38388-117">Website</span></span>](https://www.devart.com/entitydeveloper/)

### <a name="nhydrate-orm-for-entity-framework"></a><span data-ttu-id="38388-118">nHydrate ORM for Entity Framework</span><span class="sxs-lookup"><span data-stu-id="38388-118">nHydrate ORM for Entity Framework</span></span>

<span data-ttu-id="38388-119">Orm, który tworzy silnie typizowane, rozszerzalne klasy dla entity framework.</span><span class="sxs-lookup"><span data-stu-id="38388-119">An ORM that creates strongly-typed, extendable classes for Entity Framework.</span></span> <span data-ttu-id="38388-120">Wygenerowany kod to Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="38388-120">The generated code is Entity Framework Core.</span></span> <span data-ttu-id="38388-121">Nie ma żadnej różnicy.</span><span class="sxs-lookup"><span data-stu-id="38388-121">There is no difference.</span></span> <span data-ttu-id="38388-122">Nie jest to zamiennik ef lub niestandardowego ORM.</span><span class="sxs-lookup"><span data-stu-id="38388-122">This is not a replacement for EF or a custom ORM.</span></span> <span data-ttu-id="38388-123">Jest to warstwa wizualna, modelowania, która umożliwia zespołowi zarządzanie złożonymi schematami bazy danych.</span><span class="sxs-lookup"><span data-stu-id="38388-123">It is a visual, modeling layer that allows a team to manage complex database schemas.</span></span> <span data-ttu-id="38388-124">Dobrze współpracuje z oprogramowaniem SCM, takim jak Git, umożliwiając dostęp wielu użytkowników do modelu przy minimalnych konfliktach.</span><span class="sxs-lookup"><span data-stu-id="38388-124">It works well with SCM software like Git, allowing multi-user access to your model with minimal conflicts.</span></span> <span data-ttu-id="38388-125">Instalator śledzi zmiany modelu i tworzy skrypty uaktualnienia.</span><span class="sxs-lookup"><span data-stu-id="38388-125">The installer tracks model changes and creates upgrade scripts.</span></span> <span data-ttu-id="38388-126">Dla EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="38388-126">For EF Core: 3.</span></span>

[<span data-ttu-id="38388-127">Strona Github</span><span class="sxs-lookup"><span data-stu-id="38388-127">Github site</span></span>](https://github.com/nHydrate/nHydrate)

### <a name="ef-core-power-tools"></a><span data-ttu-id="38388-128">EF Core Power Tools</span><span class="sxs-lookup"><span data-stu-id="38388-128">EF Core Power Tools</span></span>

<span data-ttu-id="38388-129">EF Core Power Tools to rozszerzenie programu Visual Studio, które udostępnia różne zadania ef core projektowania w prostym interfejsie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="38388-129">EF Core Power Tools is a Visual Studio extension that exposes various EF Core design-time tasks in a simple user interface.</span></span> <span data-ttu-id="38388-130">Obejmuje inżynierię odwrotną DbContext i klasy jednostek z istniejących baz danych i [DACPAc programu SQL Server,](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications)zarządzanie migracjami baz danych i wizualizacje modelu.</span><span class="sxs-lookup"><span data-stu-id="38388-130">It includes reverse engineering of DbContext and entity classes from existing databases and [SQL Server DACPACs](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications), management of database migrations, and model visualizations.</span></span> <span data-ttu-id="38388-131">Dla EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="38388-131">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="38388-132">Wiki GitHub</span><span class="sxs-lookup"><span data-stu-id="38388-132">GitHub wiki</span></span>](https://github.com/ErikEJ/EFCorePowerTools/wiki)

### <a name="entity-framework-visual-editor"></a><span data-ttu-id="38388-133">Edytor wizualny programu Entity Framework</span><span class="sxs-lookup"><span data-stu-id="38388-133">Entity Framework Visual Editor</span></span>

<span data-ttu-id="38388-134">Edytor wizualny programu Entity Framework to rozszerzenie programu Visual Studio, które dodaje projektanta ORM do projektowania wizualnego klas EF 6 i EF Core.</span><span class="sxs-lookup"><span data-stu-id="38388-134">Entity Framework Visual Editor is a Visual Studio extension that adds an ORM designer for visual design of EF 6, and EF Core classes.</span></span> <span data-ttu-id="38388-135">Kod jest generowany przy użyciu szablonów T4, dzięki czemu można dostosować do każdego celu.</span><span class="sxs-lookup"><span data-stu-id="38388-135">Code is generated using T4 templates so can be customized to suit any needs.</span></span> <span data-ttu-id="38388-136">Obsługuje dziedziczenie, jednokierunkowe i dwukierunkowe skojarzenia, wyliczenia i możliwość kolor kodowania klas i dodawać bloki tekstu, aby wyjaśnić potencjalnie tajemne części projektu.</span><span class="sxs-lookup"><span data-stu-id="38388-136">It supports inheritance, unidirectional and bidirectional associations, enumerations, and the ability to color-code your classes and add text blocks to explain potentially arcane parts of your design.</span></span> <span data-ttu-id="38388-137">Dla EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="38388-137">For EF Core: 2.</span></span>

[<span data-ttu-id="38388-138">Rynek</span><span class="sxs-lookup"><span data-stu-id="38388-138">Marketplace</span></span>](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner)

### <a name="catfactory"></a><span data-ttu-id="38388-139">Fabryka Kotów</span><span class="sxs-lookup"><span data-stu-id="38388-139">CatFactory</span></span>

<span data-ttu-id="38388-140">CatFactory to aparat szkieletów dla platformy .NET Core, który może zautomatyzować generowanie klas DbContext, jednostek, konfiguracji mapowania i klas repozytorium z bazy danych programu SQL Server.</span><span class="sxs-lookup"><span data-stu-id="38388-140">CatFactory is a scaffolding engine for .NET Core that can automate the generation of DbContext classes, entities, mapping configurations, and repository classes from a SQL Server database.</span></span> <span data-ttu-id="38388-141">Dla EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="38388-141">For EF Core: 2.</span></span>

[<span data-ttu-id="38388-142">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="38388-142">GitHub repository</span></span>](https://github.com/hherzl/CatFactory.EntityFrameworkCore)

### <a name="loresofts-entity-framework-core-generator"></a><span data-ttu-id="38388-143">Generator rdzenia entity Framework firmy LoreSoft</span><span class="sxs-lookup"><span data-stu-id="38388-143">LoreSoft's Entity Framework Core Generator</span></span>

<span data-ttu-id="38388-144">Entity Framework Core Generator (efg) to narzędzie .NET Core CLI, które może `dotnet ef dbcontext scaffold`generować modele EF Core z istniejącej bazy danych, podobnie jak , ale obsługuje również [bezpieczną regenerację](https://efg.loresoft.com/en/latest/regeneration/) kodu poprzez zastępowanie regionu lub analizowanie plików mapowania.</span><span class="sxs-lookup"><span data-stu-id="38388-144">Entity Framework Core Generator (efg) is a .NET Core CLI tool that can generate EF Core models from an existing database, much like `dotnet ef dbcontext scaffold`, but it also supports safe code [regeneration](https://efg.loresoft.com/en/latest/regeneration/) via region replacement or by parsing mapping files.</span></span> <span data-ttu-id="38388-145">To narzędzie obsługuje generowanie modeli widoku, sprawdzania poprawności i kodu mapera obiektów.</span><span class="sxs-lookup"><span data-stu-id="38388-145">This tool supports generating view models, validation, and object mapper code.</span></span> <span data-ttu-id="38388-146">Dla EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="38388-146">For EF Core: 2.</span></span>

<span data-ttu-id="38388-147">[Tutorial](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[Dokumentacja samouczka](https://efg.loresoft.com/en/latest/)</span><span class="sxs-lookup"><span data-stu-id="38388-147">[Tutorial](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[Documentation](https://efg.loresoft.com/en/latest/)</span></span>

## <a name="extensions"></a><span data-ttu-id="38388-148">Rozszerzenia</span><span class="sxs-lookup"><span data-stu-id="38388-148">Extensions</span></span>

### <a name="microsoftentityframeworkcoreautohistory"></a><span data-ttu-id="38388-149">Microsoft.EntityFrameworkCore.AutoHistory</span><span class="sxs-lookup"><span data-stu-id="38388-149">Microsoft.EntityFrameworkCore.AutoHistory</span></span>

<span data-ttu-id="38388-150">Biblioteka wtyczek, która umożliwia automatyczne rejestrowanie zmian danych wykonywanych przez EF Core w tabeli historii.</span><span class="sxs-lookup"><span data-stu-id="38388-150">A plugin library that enables automatically recording the data changes performed by EF Core into a history table.</span></span> <span data-ttu-id="38388-151">Dla EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="38388-151">For EF Core: 2.</span></span>

[<span data-ttu-id="38388-152">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="38388-152">GitHub repository</span></span>](https://github.com/Arch/AutoHistory/)

### <a name="efcoresecondlevelcacheinterceptor"></a><span data-ttu-id="38388-153">EFCoreSecondLevelCacheInterceptor</span><span class="sxs-lookup"><span data-stu-id="38388-153">EFCoreSecondLevelCacheInterceptor</span></span>

<span data-ttu-id="38388-154">Buforowanie drugiego poziomu jest pamięcią podręczną zapytań.</span><span class="sxs-lookup"><span data-stu-id="38388-154">Second level caching is a query cache.</span></span> <span data-ttu-id="38388-155">Wyniki poleceń EF będą przechowywane w pamięci podręcznej, dzięki czemu te same polecenia EF będą pobierać swoje dane z pamięci podręcznej, a nie wykonując je ponownie w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="38388-155">The results of EF commands will be stored in the cache, so that the same EF commands will retrieve their data from the cache rather than executing them against the database again.</span></span> <span data-ttu-id="38388-156">Dla EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="38388-156">For EF Core: 3.</span></span>

[<span data-ttu-id="38388-157">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="38388-157">GitHub repository</span></span>](https://github.com/VahidN/EFCoreSecondLevelCacheInterceptor)

### <a name="geco"></a><span data-ttu-id="38388-158">Geco</span><span class="sxs-lookup"><span data-stu-id="38388-158">Geco</span></span>

<span data-ttu-id="38388-159">Geco (Generator Console) to prosty generator kodu oparty na projekcie konsoli, który działa na .NET Core i używa interpolowanych ciągów C# do generowania kodu.</span><span class="sxs-lookup"><span data-stu-id="38388-159">Geco (Generator Console) is a simple code generator based on a console project, that runs on .NET Core and uses C# interpolated strings for code generation.</span></span> <span data-ttu-id="38388-160">Geco zawiera generator modelu wstecznego dla EF Core z obsługą pluralizacji, singularization i edytowalnych szablonów.</span><span class="sxs-lookup"><span data-stu-id="38388-160">Geco includes a reverse model generator for EF Core with support for pluralization, singularization, and editable templates.</span></span> <span data-ttu-id="38388-161">Zapewnia również generator skryptów danych źródłowych, moduł przesiewowy skryptów i czyszczenie bazy danych.</span><span class="sxs-lookup"><span data-stu-id="38388-161">It also provides a seed data script generator, a script runner, and a database cleaner.</span></span> <span data-ttu-id="38388-162">Dla EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="38388-162">For EF Core: 2.</span></span>

[<span data-ttu-id="38388-163">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="38388-163">GitHub repository</span></span>](https://github.com/iQuarc/Geco)

### <a name="entityframeworkcorescaffoldinghandlebars"></a><span data-ttu-id="38388-164">EntityFrameworkCore.Scaffolding.Kierownica</span><span class="sxs-lookup"><span data-stu-id="38388-164">EntityFrameworkCore.Scaffolding.Handlebars</span></span> 

<span data-ttu-id="38388-165">Umożliwia dostosowanie klas inżynierii odwrotnej z istniejącej bazy danych przy użyciu programu Entity Framework Core toolchain z szablonami kierownicy.</span><span class="sxs-lookup"><span data-stu-id="38388-165">Allows customization of classes reverse engineered from an existing database using the Entity Framework Core toolchain with Handlebars templates.</span></span> <span data-ttu-id="38388-166">Dla EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="38388-166">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="38388-167">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="38388-167">GitHub repository</span></span>](https://github.com/TrackableEntities/EntityFrameworkCore.Scaffolding.Handlebars)

### <a name="neinlinqentityframeworkcore"></a><span data-ttu-id="38388-168">NeinLinq.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="38388-168">NeinLinq.EntityFrameworkCore</span></span> 

<span data-ttu-id="38388-169">NeinLinq rozszerza dostawców LINQ, takich jak Entity Framework, aby umożliwić ponowne użycie funkcji, przepisywanie zapytań i tworzenie zapytań dynamicznych przy użyciu tłumaczonych predykatów i selektorów.</span><span class="sxs-lookup"><span data-stu-id="38388-169">NeinLinq extends LINQ providers such as Entity Framework to enable reusing functions, rewriting queries, and building dynamic queries using translatable predicates and selectors.</span></span> <span data-ttu-id="38388-170">Dla EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="38388-170">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="38388-171">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="38388-171">GitHub repository</span></span>](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a><span data-ttu-id="38388-172">Microsoft.EntityFrameworkCore.UnitOfWork</span><span class="sxs-lookup"><span data-stu-id="38388-172">Microsoft.EntityFrameworkCore.UnitOfWork</span></span>

<span data-ttu-id="38388-173">Wtyczka dla Microsoft.EntityFrameworkCore do obsługi repozytorium, jednostki wzorców pracy i wielu baz danych z transakcjami rozproszonymi obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="38388-173">A plugin for Microsoft.EntityFrameworkCore to support repository, unit of work patterns, and multiple databases with distributed transaction supported.</span></span> <span data-ttu-id="38388-174">Dla EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="38388-174">For EF Core: 2.</span></span>

[<span data-ttu-id="38388-175">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="38388-175">GitHub repository</span></span>](https://github.com/Arch/UnitOfWork/)

### <a name="efcorebulkextensions"></a><span data-ttu-id="38388-176">EFCore.BulkWybory</span><span class="sxs-lookup"><span data-stu-id="38388-176">EFCore.BulkExtensions</span></span>

<span data-ttu-id="38388-177">Rozszerzenia EF Core dla operacji zbiorczych (Wstaw, Aktualizacja, Usuń).</span><span class="sxs-lookup"><span data-stu-id="38388-177">EF Core extensions for Bulk operations (Insert, Update, Delete).</span></span> <span data-ttu-id="38388-178">Dla EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="38388-178">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="38388-179">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="38388-179">GitHub repository</span></span>](https://github.com/borisdj/EFCore.BulkExtensions)

### <a name="bricelamentityframeworkcorepluralizer"></a><span data-ttu-id="38388-180">Bricelam.EntityFrameworkCore.Pluralizer</span><span class="sxs-lookup"><span data-stu-id="38388-180">Bricelam.EntityFrameworkCore.Pluralizer</span></span>

<span data-ttu-id="38388-181">Dodaje pluralizm w czasie projektowania.</span><span class="sxs-lookup"><span data-stu-id="38388-181">Adds design-time pluralization.</span></span> <span data-ttu-id="38388-182">Dla EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="38388-182">For EF Core: 2.</span></span>

[<span data-ttu-id="38388-183">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="38388-183">GitHub repository</span></span>](https://github.com/bricelam/EFCore.Pluralizer)

### <a name="toolbeltentityframeworkcoreindexattribute"></a><span data-ttu-id="38388-184">Atrybut toolbelt.entityFrameworkCore.IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="38388-184">Toolbelt.EntityFrameworkCore.IndexAttribute</span></span>

<span data-ttu-id="38388-185">Odrodzenie atrybutu [Index] (z rozszerzeniem dla budynku modelu).</span><span class="sxs-lookup"><span data-stu-id="38388-185">Revival of [Index] attribute (with extension for model building).</span></span> <span data-ttu-id="38388-186">Dla EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="38388-186">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="38388-187">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="38388-187">GitHub repository</span></span>](https://github.com/jsakamoto/EntityFrameworkCore.IndexAttribute)

### <a name="efcoreinmemoryhelpers"></a><span data-ttu-id="38388-188">EfCore.InMemoryPomowacze</span><span class="sxs-lookup"><span data-stu-id="38388-188">EfCore.InMemoryHelpers</span></span>

<span data-ttu-id="38388-189">Udostępnia otokę wokół dostawcy bazy danych EF Core w pamięci.</span><span class="sxs-lookup"><span data-stu-id="38388-189">Provides a wrapper around the EF Core In-Memory Database Provider.</span></span> <span data-ttu-id="38388-190">Sprawia, że działa bardziej jak dostawca relacyjne.</span><span class="sxs-lookup"><span data-stu-id="38388-190">Makes it act more like a relational provider.</span></span> <span data-ttu-id="38388-191">Dla EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="38388-191">For EF Core: 2.</span></span>

[<span data-ttu-id="38388-192">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="38388-192">GitHub repository</span></span>](https://github.com/SimonCropp/EfCore.InMemoryHelpers)

### <a name="efcoretemporalsupport"></a><span data-ttu-id="38388-193">EFCore.TemporalSupport</span><span class="sxs-lookup"><span data-stu-id="38388-193">EFCore.TemporalSupport</span></span>

<span data-ttu-id="38388-194">Wdrożenie wsparcia czasowego.</span><span class="sxs-lookup"><span data-stu-id="38388-194">An implementation of temporal support.</span></span> <span data-ttu-id="38388-195">Dla EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="38388-195">For EF Core: 2.</span></span>

[<span data-ttu-id="38388-196">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="38388-196">GitHub repository</span></span>](https://github.com/cpoDesign/EFCore.TemporalSupport)

### <a name="efcoretemporaltable"></a><span data-ttu-id="38388-197">Tabela EfCoreTemporalTable</span><span class="sxs-lookup"><span data-stu-id="38388-197">EfCoreTemporalTable</span></span>

<span data-ttu-id="38388-198">Łatwe wykonywanie zapytań czasowych w ulubionej `AsTemporalAll()`bazie `AsTemporalAsOf(date)` `AsTemporalFrom(startDate, endDate)`danych `AsTemporalBetween(startDate, endDate)` `AsTemporalContained(startDate, endDate)`za pomocą wprowadzonych metod rozszerzenia: , , , .</span><span class="sxs-lookup"><span data-stu-id="38388-198">Easily perform temporal queries on your favourite database using introduced extension methods: `AsTemporalAll()`, `AsTemporalAsOf(date)`, `AsTemporalFrom(startDate, endDate)`, `AsTemporalBetween(startDate, endDate)`, `AsTemporalContained(startDate, endDate)`.</span></span> <span data-ttu-id="38388-199">Dla EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="38388-199">For EF Core: 3.</span></span>

[<span data-ttu-id="38388-200">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="38388-200">GitHub repository</span></span>](https://github.com/glautrou/EfCoreTemporalTable)

### <a name="efcoretimetraveler"></a><span data-ttu-id="38388-201">EfCore.TimeTraveler</span><span class="sxs-lookup"><span data-stu-id="38388-201">EFCore.TimeTraveler</span></span>

<span data-ttu-id="38388-202">Zezwalaj na w pełni funkcjonalne zapytania Entity Framework Core względem [historii czasowej programu SQL Server](/sql/relational-databases/tables/temporal-table-usage-scenarios#point-in-time-analysis-time-travel) przy użyciu kodu EF Core, jednostek i mapowań, które zostały już zdefiniowane.</span><span class="sxs-lookup"><span data-stu-id="38388-202">Allow full-featured Entity Framework Core queries against [SQL Server Temporal History](/sql/relational-databases/tables/temporal-table-usage-scenarios#point-in-time-analysis-time-travel) using the EF Core code, entities, and mappings you already have defined.</span></span>  <span data-ttu-id="38388-203">Podróżuj w czasie, `using (TemporalQuery.AsOf(targetDateTime)) {...}`zawijając kod w pliku .</span><span class="sxs-lookup"><span data-stu-id="38388-203">Travel through time by wrapping your code in `using (TemporalQuery.AsOf(targetDateTime)) {...}`.</span></span> <span data-ttu-id="38388-204">Dla EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="38388-204">For EF Core: 3.</span></span>

[<span data-ttu-id="38388-205">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="38388-205">GitHub repository</span></span>](https://github.com/VantageSoftware/EFCore.TimeTraveler)


### <a name="entityframeworkcoretemporaltables"></a><span data-ttu-id="38388-206">EntityFrameworkCore.TemporalTables</span><span class="sxs-lookup"><span data-stu-id="38388-206">EntityFrameworkCore.TemporalTables</span></span>

<span data-ttu-id="38388-207">Biblioteka rozszerzeń dla entity framework core, który umożliwia deweloperom, którzy używają programu SQL Server łatwo używać tabel czasowych.</span><span class="sxs-lookup"><span data-stu-id="38388-207">Extension library for Entity Framework Core which allows developers who use SQL Server to easily use temporal tables.</span></span> <span data-ttu-id="38388-208">Dla EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="38388-208">For EF Core: 2.</span></span>

[<span data-ttu-id="38388-209">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="38388-209">GitHub repository</span></span>](https://github.com/findulov/EntityFrameworkCore.TemporalTables)


### <a name="entityframeworkcorecacheable"></a><span data-ttu-id="38388-210">Element EntityFrameworkCore.Cacheable</span><span class="sxs-lookup"><span data-stu-id="38388-210">EntityFrameworkCore.Cacheable</span></span>

<span data-ttu-id="38388-211">Wysokowydajna pamięć podręczna zapytań drugiego poziomu.</span><span class="sxs-lookup"><span data-stu-id="38388-211">A high-performance second-level query cache.</span></span> <span data-ttu-id="38388-212">Dla EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="38388-212">For EF Core: 2.</span></span>

[<span data-ttu-id="38388-213">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="38388-213">GitHub repository</span></span>](https://github.com/SteffenMangold/EntityFrameworkCore.Cacheable)

### <a name="entity-framework-plus"></a><span data-ttu-id="38388-214">Entity Framework Plus</span><span class="sxs-lookup"><span data-stu-id="38388-214">Entity Framework Plus</span></span>

<span data-ttu-id="38388-215">Rozszerza dbContext o funkcje, takie jak: Filtr, Inspekcja, Buforowanie, Przyszłość kwerendy, Usuwanie partii, Aktualizacja wsadowa i inne.</span><span class="sxs-lookup"><span data-stu-id="38388-215">Extends your DbContext with features such as: Include Filter, Auditing, Caching, Query Future, Batch Delete, Batch Update, and more.</span></span> <span data-ttu-id="38388-216">Dla EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="38388-216">For EF Core: 2, 3.</span></span>

<span data-ttu-id="38388-217">[Website](https://entityframework-plus.net/)
[Repozytorium GitHub witryny](https://github.com/zzzprojects/EntityFramework-Plus) sieci Web</span><span class="sxs-lookup"><span data-stu-id="38388-217">[Website](https://entityframework-plus.net/)
[GitHub repository](https://github.com/zzzprojects/EntityFramework-Plus)</span></span>

### <a name="entity-framework-extensions"></a><span data-ttu-id="38388-218">Rozszerzenia struktury encji</span><span class="sxs-lookup"><span data-stu-id="38388-218">Entity Framework Extensions</span></span>

<span data-ttu-id="38388-219">Rozszerza DbContext o wysokiej wydajności operacji masowych: BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge i więcej.</span><span class="sxs-lookup"><span data-stu-id="38388-219">Extends your DbContext with high-performance bulk operations: BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge, and more.</span></span> <span data-ttu-id="38388-220">Dla EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="38388-220">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="38388-221">witryna sieci web</span><span class="sxs-lookup"><span data-stu-id="38388-221">Website</span></span>](https://entityframework-extensions.net/)

### <a name="expressionify"></a><span data-ttu-id="38388-222">Wyekspresyfikacja</span><span class="sxs-lookup"><span data-stu-id="38388-222">Expressionify</span></span>

<span data-ttu-id="38388-223">Dodaj obsługę wywoływania metod rozszerzenia w linq lambdas.</span><span class="sxs-lookup"><span data-stu-id="38388-223">Add support for calling extension methods in LINQ lambdas.</span></span> <span data-ttu-id="38388-224">Dla EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="38388-224">For EF Core: 3.</span></span>

[<span data-ttu-id="38388-225">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="38388-225">GitHub repository</span></span>](https://github.com/ClaveConsulting/Expressionify)

### <a name="xlinq"></a><span data-ttu-id="38388-226">XLinq (własno)</span><span class="sxs-lookup"><span data-stu-id="38388-226">XLinq</span></span>

<span data-ttu-id="38388-227">Technologia linq (Language Integrated Query) dla relacyjnych baz danych.</span><span class="sxs-lookup"><span data-stu-id="38388-227">Language Integrated Query (LINQ) technology for relational databases.</span></span> <span data-ttu-id="38388-228">Umożliwia użycie języka C# do pisania silnie wpisanych zapytań.</span><span class="sxs-lookup"><span data-stu-id="38388-228">It allows you to use C# to write strongly typed queries.</span></span> <span data-ttu-id="38388-229">Dla EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="38388-229">For EF Core: 3.</span></span>

- <span data-ttu-id="38388-230">Pełna obsługa języka C# dla tworzenia zapytań: wiele instrukcji wewnątrz lambda, zmienne, funkcje itp.</span><span class="sxs-lookup"><span data-stu-id="38388-230">Full C# support for query creation: multiple statements inside lambda, variables, functions, etc.</span></span>
- <span data-ttu-id="38388-231">Brak luki semantycznej z SQL.</span><span class="sxs-lookup"><span data-stu-id="38388-231">No semantic gap with SQL.</span></span> <span data-ttu-id="38388-232">XLinq deklaruje instrukcje `SELECT`SQL `FROM` `WHERE`(jak , , ) jako metody pierwszej klasy C#, łącząc znaną składnię z intellisense, bezpieczeństwa typu i refaktoryzacji.</span><span class="sxs-lookup"><span data-stu-id="38388-232">XLinq declares SQL statements (like `SELECT`, `FROM`, `WHERE`) as first class C# methods, combining familiar syntax with intellisense, type safety and refactoring.</span></span>

<span data-ttu-id="38388-233">W rezultacie SQL staje się po prostu "inną" biblioteką klas eksponującą swój interfejs API lokalnie, dosłownie *"Language Integrated SQL"*.</span><span class="sxs-lookup"><span data-stu-id="38388-233">As a result SQL becomes just "another" class library exposing its API locally, literally *"Language Integrated SQL"*.</span></span>

[<span data-ttu-id="38388-234">witryna sieci web</span><span class="sxs-lookup"><span data-stu-id="38388-234">Website</span></span>](http://xlinq.live/)

### <a name="ramses"></a><span data-ttu-id="38388-235">Ramses</span><span class="sxs-lookup"><span data-stu-id="38388-235">Ramses</span></span>

<span data-ttu-id="38388-236">Haki cyklu życia (dla SaveChanges).</span><span class="sxs-lookup"><span data-stu-id="38388-236">Lifecycle hooks (for SaveChanges).</span></span> <span data-ttu-id="38388-237">Dla EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="38388-237">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="38388-238">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="38388-238">GitHub repository</span></span>](https://github.com/JValck/Ramses)

### <a name="efcorenamingconventions"></a><span data-ttu-id="38388-239">EFCore.NamingKonwenty</span><span class="sxs-lookup"><span data-stu-id="38388-239">EFCore.NamingConventions</span></span>

<span data-ttu-id="38388-240">Spowoduje to automatyczne, że wszystkie nazwy tabel i kolumn mają snake_case, wszystkie nazewnictwo górne lub wszystkie małe litery.</span><span class="sxs-lookup"><span data-stu-id="38388-240">This will automatically make all your table and column names have snake_case, all UPPER or all lower case naming.</span></span> <span data-ttu-id="38388-241">Dla EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="38388-241">For EF Core: 3.</span></span>

[<span data-ttu-id="38388-242">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="38388-242">GitHub repository</span></span>](https://github.com/efcore/EFCore.NamingConventions)

### <a name="simplersoftwareentityframeworkcoresqlservernodatime"></a><span data-ttu-id="38388-243">ProstszySoftware.EntityFrameCore.SqlServer.NodaTime</span><span class="sxs-lookup"><span data-stu-id="38388-243">SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime</span></span>

<span data-ttu-id="38388-244">Dodaje natywną obsługę entityframeworkCore dla programu SQL Server dla typów NodaTime.</span><span class="sxs-lookup"><span data-stu-id="38388-244">Adds native support to EntityFrameworkCore for SQL Server for the NodaTime types.</span></span> <span data-ttu-id="38388-245">Dla EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="38388-245">For EF Core: 3.</span></span>

[<span data-ttu-id="38388-246">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="38388-246">GitHub repository</span></span>](https://github.com/StevenRasmussen/EFCore.SqlServer.NodaTime)

### <a name="dabbleentityframeworkcoretemporalquery"></a><span data-ttu-id="38388-247">Dabble.EntityFrameworkCore.Temporal.Query</span><span class="sxs-lookup"><span data-stu-id="38388-247">Dabble.EntityFrameworkCore.Temporal.Query</span></span>

<span data-ttu-id="38388-248">LINQ rozszerzenia do entity framework core 3.1 do obsługi microsoft SQL Server czasowe kwerendy tabeli.</span><span class="sxs-lookup"><span data-stu-id="38388-248">LINQ extensions to Entity Framework Core 3.1 to support Microsoft SQL Server Temporal Table Querying.</span></span> <span data-ttu-id="38388-249">Dla EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="38388-249">For EF Core: 3.</span></span>

[<span data-ttu-id="38388-250">Repozytorium GitHub</span><span class="sxs-lookup"><span data-stu-id="38388-250">GitHub repository</span></span>](https://github.com/Adam-Langley/efcore-temporal-query)
