---
title: Narzędzia & rozszerzenia — EF Core
author: ErikEJ
ms.date: 04/11/2020
ms.assetid: 14fffb6c-a687-4881-a094-af4a1359a296
uid: core/extensions/index
ms.openlocfilehash: 5b7945bd24ac19a82b08f4d5709555cf53485f4c
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238258"
---
# <a name="ef-core-tools--extensions"></a>Rozszerzenia narzędzi EF Core &

Te narzędzia i rozszerzenia zapewniają dodatkową funkcjonalność dla Entity Framework Core 2,1 i nowszych.

> [!IMPORTANT]  
> Rozszerzenia są tworzone przez różne źródła i nie są obsługiwane w ramach projektu Entity Framework Core. Biorąc pod uwagę rozszerzenie innej firmy, pamiętaj o ocenie jego jakości, licencjonowania, zgodności, wsparcia itp., aby upewnić się, że spełnia Twoje wymagania. W szczególności rozszerzenie skompilowane dla starszej wersji EF Core może wymagać aktualizacji, zanim będzie działały z najnowszymi wersjami.

## <a name="tools"></a>Narzędzia

### <a name="llblgen-pro"></a>LLBLGen Pro

LLBLGen Pro to rozwiązanie do modelowania jednostek z obsługą Entity Framework i Entity Framework Core. Umożliwia ona łatwe definiowanie modelu jednostki i mapowanie go do bazy danych przy użyciu najpierw pierwszej lub modelu bazy danych, dzięki czemu możesz od razu zacząć pisać zapytania. Dla EF Core: 2, 3

[Witryna internetowa](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a>Devart Entity Developer

Deweloper jednostki jest zaawansowanym projektantem ORM dla ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access i LINQ to SQL. Obsługuje ona projektowanie EF Core modeli wizualnie, przy użyciu pierwszej metody modelu lub pierwszej podejścia do bazy danych, a także generowania kodu w języku C# lub Visual Basic. Dla EF Core: 1, 2, 3, 5.

[Witryna internetowa](https://www.devart.com/entitydeveloper/)

### <a name="nhydrate-orm-for-entity-framework"></a>nHydrate ORM dla Entity Framework

Obiekt ORM, który tworzy klasy o jednoznacznie określonym typie, rozszerzalny dla Entity Framework. Wygenerowany kod jest Entity Framework Core. Nie ma żadnych różnic. Nie jest to zamiennik dla EF lub niestandardowej ORM. Jest to Wizualizacja warstwa modelowania, która umożliwia zespołowi zarządzanie złożonymi schematami bazy danych. Dobrze sprawdza się w przypadku oprogramowania SCM, takiego jak Git, umożliwiając dostęp dla użytkowników do modelu z minimalnymi konfliktami. Instalator śledzi zmiany modelu i tworzy skrypty uaktualniania. Dla EF Core: 3.

[Witryna usługi GitHub](https://github.com/nHydrate/nHydrate)

### <a name="ef-core-power-tools"></a>EF Core narzędzia do zarządzania

EF Core PowerShell to rozszerzenie programu Visual Studio, które uwidacznia różne EF Core zadania czasu projektowania w prostym interfejsie użytkownika. Obejmuje ona odtwarzanie klas DbContext i Entity Classes z istniejących baz danych, a [SQL Server DACPACs](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications), zarządzanie migracjami baz danych i wizualizacje modeli. Dla EF Core: 2, 3.

[Witryna typu wiki usługi GitHub](https://github.com/ErikEJ/EFCorePowerTools/wiki)

### <a name="entity-framework-visual-editor"></a>Entity Framework edytor wizualny

Entity Framework edytorem wizualnym jest rozszerzenie programu Visual Studio, które dodaje projektanta ORM do projektowania wizualizacji Dr 6 i klasy EF Core. Kod jest generowany przy użyciu szablonów T4, więc można go dostosować do własnych potrzeb. Obsługuje dziedziczenie, dwukierunkowe i dwukierunkowe skojarzenia, wyliczenia oraz możliwość kolorowania kodu klas i Dodawanie bloków tekstowych, aby wyjaśnić potencjalnie specjalne części projektu. Dla EF Core: 2.

[Rynek](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner)

### <a name="catfactory"></a>CatFactory

CatFactory to aparat tworzenia szkieletów dla platformy .NET Core, który umożliwia automatyzację generacji klas DbContext, jednostek, konfiguracji mapowania i klas repozytorium z bazy danych SQL Server. Dla EF Core: 2.

[Repozytorium GitHub](https://github.com/hherzl/CatFactory.EntityFrameworkCore)

### <a name="loresofts-entity-framework-core-generator"></a>Generator Entity Framework Core LoreSoft

Generator Entity Framework Core (EFG) to narzędzie interfejs wiersza polecenia platformy .NET Core, które może generować modele EF Core z istniejącej bazy danych, podobnie jak `dotnet ef dbcontext scaffold` , ale również zapewnia bezpieczną [regenerację](https://efg.loresoft.com/en/latest/regeneration/) kodu przez zastąpienie regionu lub przeanalizowanie plików mapowania. To narzędzie obsługuje generowanie modeli widoku, walidacji i kodu mapowania obiektów. Dla EF Core: 2.

[Samouczek](https://www.loresoft.com/Generate-ASP-NET-Web-API) 
 [Dokumentacja](https://efg.loresoft.com/en/latest/)

## <a name="extensions"></a>Rozszerzenia

### <a name="microsoftentityframeworkcoreautohistory"></a>Microsoft.EntityFrameworkCore.AutoHistory

Biblioteka wtyczek, która umożliwia automatyczne rejestrowanie zmian danych wykonywanych przez EF Core w tabeli historii. Dla EF Core: 2.

[Repozytorium GitHub](https://github.com/Arch/AutoHistory/)

### <a name="efcoresecondlevelcacheinterceptor"></a>EFCoreSecondLevelCacheInterceptor

Buforowanie drugiego poziomu jest pamięcią podręczną zapytań. Wyniki poleceń Dr są przechowywane w pamięci podręcznej, dzięki czemu te same polecenia EF pobierają dane z pamięci podręcznej, a nie ponownie wykonywane w odniesieniu do bazy danych. Dla EF Core: 3.

[Repozytorium GitHub](https://github.com/VahidN/EFCoreSecondLevelCacheInterceptor)

### <a name="geco"></a>Geco

Geco (konsola generatora) to prosty generator kodu oparty na projekcie konsoli, który działa na platformie .NET Core i używa interpolowanych ciągów języka C# do generowania kodu. Geco obejmuje generator modelu Odwróć dla EF Core z obsługą szablonów pluralizacja, singularization i edytowalnych. Udostępnia również Generator skryptów danych inicjatora, moduł uruchamiający skrypty i oczyszczarkę bazy danych. Dla EF Core: 2.

[Repozytorium GitHub](https://github.com/iQuarc/Geco)

### <a name="entityframeworkcorescaffoldinghandlebars"></a>EntityFrameworkCore. Tworzenie szkieletów. kierownicy 

Umożliwia dostosowanie klas odtworzonych z istniejącej bazy danych przy użyciu Entity Framework Core łańcucha narzędzi z szablonami kierownicy. Dla EF Core: 2, 3.

[Repozytorium GitHub](https://github.com/TrackableEntities/EntityFrameworkCore.Scaffolding.Handlebars)

### <a name="neinlinqentityframeworkcore"></a>NeinLinq.EntityFrameworkCore 

NeinLinq rozszerza dostawców LINQ, takich jak Entity Framework, aby włączyć ponowne używanie funkcji, zapisywania zapytań i kompilowania zapytań dynamicznych przy użyciu predykatów z możliwością tłumaczenia i selektorów. Dla EF Core: 2, 3.

[Repozytorium GitHub](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a>Microsoft.EntityFrameworkCore.UnitOfWork

Wtyczka dla elementu Microsoft. EntityFrameworkCore do obsługi repozytorium, wzorców jednostek roboczych i wielu baz danych z obsługiwaną transakcją rozproszoną. Dla EF Core: 2.

[Repozytorium GitHub](https://github.com/Arch/UnitOfWork/)

### <a name="efcorebulkextensions"></a>EFCore.BulkExtensions

Rozszerzenia EF Core dla operacji zbiorczych (INSERT, Update i Delete). Dla EF Core: 2, 3.

[Repozytorium GitHub](https://github.com/borisdj/EFCore.BulkExtensions)

### <a name="bricelamentityframeworkcorepluralizer"></a>Bricelam.EntityFrameworkCore.Pluralizer

Dodaje pluralizacja czasu projektowania. Dla EF Core: 2.

[Repozytorium GitHub](https://github.com/bricelam/EFCore.Pluralizer)

### <a name="toolbeltentityframeworkcoreindexattribute"></a>Toolbelt. EntityFrameworkCore. Indexattribute

Revival [index] atrybut (z rozszerzeniem dla kompilowania modelu). Dla EF Core: 2, 3.

[Repozytorium GitHub](https://github.com/jsakamoto/EntityFrameworkCore.IndexAttribute)

### <a name="efcoreinmemoryhelpers"></a>EfCore.InMemoryHelpers

Zawiera otokę wokół EF Core dostawcy bazy danych w pamięci. Sprawia, że działa tak samo jak dostawca relacyjny. Dla EF Core: 2.

[Repozytorium GitHub](https://github.com/SimonCropp/EfCore.InMemoryHelpers)

### <a name="efcoretemporalsupport"></a>EFCore.TemporalSupport

Implementacja obsługi danych czasowych. Dla EF Core: 2.

[Repozytorium GitHub](https://github.com/cpoDesign/EFCore.TemporalSupport)

### <a name="efcoretemporaltable"></a>EfCoreTemporalTable

Łatwe wykonywanie zapytań czasowych w ulubionej bazie danych przy użyciu wprowadzonych metod rozszerzających: `AsTemporalAll()` , `AsTemporalAsOf(date)` ,, `AsTemporalFrom(startDate, endDate)` `AsTemporalBetween(startDate, endDate)` `AsTemporalContained(startDate, endDate)` . Dla EF Core: 3.

[Repozytorium GitHub](https://github.com/glautrou/EfCoreTemporalTable)

### <a name="efcoretimetraveler"></a>EFCore.TimeTraveler

Zezwalaj na w pełni funkcjonalne zapytania Entity Framework Core w odniesieniu do [SQL Server historii](/sql/relational-databases/tables/temporal-table-usage-scenarios#point-in-time-analysis-time-travel) danych czasowych przy użyciu zdefiniowanego w EF Core kodu, jednostek i mapowań.  Poruszaj się po czasie, zawijając kod w `using (TemporalQuery.AsOf(targetDateTime)) {...}` . Dla EF Core: 3.

[Repozytorium GitHub](https://github.com/VantageSoftware/EFCore.TimeTraveler)


### <a name="entityframeworkcoretemporaltables"></a>EntityFrameworkCore.TemporalTables

Biblioteka rozszerzeń dla Entity Framework Core, która umożliwia deweloperom, którzy używają SQL Server do łatwego korzystania z tabel danych czasowych. Dla EF Core: 2.

[Repozytorium GitHub](https://github.com/findulov/EntityFrameworkCore.TemporalTables)


### <a name="entityframeworkcorecacheable"></a>EntityFrameworkCore. buforowanie

Pamięć podręczna zapytań o wysokiej wydajności. Dla EF Core: 2.

[Repozytorium GitHub](https://github.com/SteffenMangold/EntityFrameworkCore.Cacheable)

### <a name="entityframeworkcorencache"></a>EntityFrameworkCore.NCache

NCache Entity Framework Core jest dostawcą rozproszonej pamięci podręcznej drugiego poziomu na potrzeby buforowania wyników zapytania. Rozproszona architektura NCache sprawia, że jest bardziej skalowalna i wysoka dostępność. Dla EF Core 2.

[Witryna internetowa](https://www.alachisoft.com/ncache/ef-core-cache.html)

### <a name="entity-framework-plus"></a>Entity Framework Plus

Rozszerza kontekst DbContext z funkcjami takimi jak: Filter include, Audit, buforowanie, Future Query, Batch Delete, Batch Update i innych. Dla EF Core: 2, 3.

[Witryna sieci Web](https://entityframework-plus.net/) 
 [Repozytorium GitHub](https://github.com/zzzprojects/EntityFramework-Plus)

### <a name="entity-framework-extensions"></a>Rozszerzenia Entity Framework

Rozszerza swój kontekst dbwith operacji zbiorczych o wysokiej wydajności: BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge i inne. Dla EF Core: 2, 3.

[Witryna internetowa](https://entityframework-extensions.net/)

### <a name="expressionify"></a>Expressionify

Dodano obsługę wywoływania metod rozszerzających w składniku LINQ lambda. Dla EF Core: 3.

[Repozytorium GitHub](https://github.com/ClaveConsulting/Expressionify)

### <a name="xlinq"></a>XLinq

Technologia Integrated Language Query (LINQ) dla relacyjnych baz danych. Pozwala na używanie języka C# do pisania kwerend o jednoznacznie określonym typie. Dla EF Core: 3.

- Pełna obsługa języka C# na potrzeby tworzenia zapytań: wiele instrukcji wewnątrz wyrażenia lambda, zmienne, funkcje itd.
- Brak przerwy semantycznej z SQL. XLinq deklaruje instrukcje języka SQL (takie jak `SELECT` , `FROM` , `WHERE` ) jako metody pierwszej klasy języka C#, łącząc znaną składnię z technologią IntelliSense, bezpieczeństwo typów i refaktoryzację.

W związku z tym, w wyniku tego, w programie SQL Server, "inna" Biblioteka klas uwidacznia interfejs API lokalnie, dosłownie *"język zintegrowany języka SQL"*.

[Witryna internetowa](http://xlinq.live/)

### <a name="ramses"></a>Ramses

Punkty zaczepienia cyklu życia (dla metody SaveChanges). Dla EF Core: 2, 3.

[Repozytorium GitHub](https://github.com/JValck/Ramses)

### <a name="efcorenamingconventions"></a>EFCore.NamingConventions

Spowoduje to automatyczne wprowadzenie wszystkich nazw tabel i kolumn, które mają snake_case, wielkie lub małe litery. Dla EF Core: 3.

[Repozytorium GitHub](https://github.com/efcore/EFCore.NamingConventions)

### <a name="simplersoftwareentityframeworkcoresqlservernodatime"></a>SimplerSoftware. EntityFrameworkCore. SqlServer. NodaTime

Dodaje natywną obsługę EntityFrameworkCore SQL Server dla typów NodaTime. Dla EF Core: 3.

[Repozytorium GitHub](https://github.com/StevenRasmussen/EFCore.SqlServer.NodaTime)

### <a name="dabbleentityframeworkcoretemporalquery"></a>Dabble. EntityFrameworkCore. czasowo. Query

Rozszerzenia LINQ do Entity Framework Core 3,1 do obsługi Microsoft SQL Server danych czasowych w tabelach. Dla EF Core: 3.

[Repozytorium GitHub](https://github.com/Adam-Langley/efcore-temporal-query)

### <a name="entityframeworkcoresqlserverhierarchyid"></a>EntityFrameworkCore. SqlServer. HierarchyId

Dodaje obsługę hierarchyid do dostawcy EF Core SQL Server. Dla EF Core: 3.

[Repozytorium GitHub](https://github.com/efcore/EFCore.SqlServer.HierarchyId)

### <a name="linq2dbentityframeworkcore"></a>linq2db. EntityFrameworkCore

Alternatywny translator zapytań LINQ do wyrażeń SQL. Dla EF Core: 3.

Obejmuje obsługę zaawansowanych funkcji SQL, takich jak cyklicznych, kopia zbiorcza, wskazówki tabeli, funkcje okienkowe, tabele tymczasowe oraz operacje tworzenia/aktualizowania/usuwania po stronie bazy danych.

[Repozytorium GitHub](https://github.com/linq2db/linq2db.EntityFrameworkCore)

### <a name="efcoresoftdelete"></a>EFCore.SoftDelete

Implementacja nietrwałego usuwania jednostek. Dla EF Core: 3.

[NuGet](https://www.nuget.org/packages/EFCore.SoftDelete)
