---
title: Zagadnienia dotyczące wydajności dla EF4, EF5 i EF6 — EF6
author: divega
ms.date: 10/23/2016
ms.assetid: d6d5a465-6434-45fa-855d-5eb48c61a2ea
ms.openlocfilehash: 0ece383bb5083b41c7a2636c009473333af6d3e2
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434342"
---
# <a name="performance-considerations-for-ef-4-5-and-6"></a>Zagadnienia dotyczące wydajności dla EF 4, 5 i 6
Autorstwa Davida Obando, Erica Dettingera i innych

Opublikowano: kwiecień 2012 r.

Ostatnia aktualizacja: maj 2014 r.

------------------------------------------------------------------------

## <a name="1-introduction"></a>1. Wprowadzenie

Struktury mapowania obiektowo-relacyjnego są wygodnym sposobem zapewnienia abstrakcji dostępu do danych w aplikacji obiektowej. W przypadku aplikacji platformy .NET zalecana o/rm firmy Microsoft to entity framework. Z każdej abstrakcji jednak, wydajność może stać się problemem.

Ten oficjalny dokument został napisany w celu wyświetlenia zagadnień dotyczących wydajności podczas tworzenia aplikacji przy użyciu entity framework, aby dać deweloperom pomysł entity framework wewnętrznych algorytmów, które mogą mieć wpływ na wydajność i aby zapewnić wskazówki dotyczące badania i poprawy wydajności w swoich aplikacjach, które używają entity framework. Istnieje wiele dobrych tematów dotyczących wydajności już dostępnych w internecie, a my również próbowaliśmy wskazać te zasoby tam, gdzie to możliwe.

Wydajność to trudny temat. Ten oficjalny dokument jest przeznaczony jako zasób ułatwiające podejmowanie decyzji związanych z wydajnością dla aplikacji korzystających z entity framework. Uwzględniliśmy niektóre metryki testu, aby zademonstrować wydajność, ale te metryki nie są przeznaczone jako bezwzględne wskaźniki wydajności, które zostaną wyświetleni w aplikacji.

Dla praktycznych celów ten dokument zakłada Entity Framework 4 jest uruchamiany w obszarze .NET 4.0 i Entity Framework 5 i 6 są uruchamiane w ramach .NET 4.5. Wiele ulepszeń wydajności wprowadzonych dla entity framework 5 znajduje się w podstawowych komponentach dostarczanych z programem .NET 4.5.

Entity Framework 6 jest wydaniem poza pasmem i nie zależy od składników entity framework, które są dostarczane z .NET. Entity Framework 6 działa zarówno na .NET 4.0 i .NET 4.5 i może zaoferować duże korzyści wydajności dla tych, którzy nie uaktualniony z .NET 4.0, ale chcą najnowsze bity entity framework w aplikacji. Gdy ten dokument wspomina Entity Framework 6, odnosi się do najnowszej wersji dostępnej w momencie pisania tego tekstu: wersja 6.1.0.

## <a name="2-cold-vs-warm-query-execution"></a>2. Zimne i ciepłe wykonywanie zapytań

Po raz pierwszy wszelkie kwerendy jest wykonany w stosunku do danego modelu, Entity Framework wykonuje wiele pracy za kulisami, aby załadować i sprawdzić poprawność modelu. Często nazywamy to pierwsze zapytanie kwerendą "zimną" kwerendą.Dalsze zapytania dotyczące już załadowanego modelu są znane jako "ciepłe" zapytania i są znacznie szybsze.

Przyjrzyjmy się widok wysokiego poziomu, gdzie czas spędza się podczas wykonywania kwerendy przy użyciu entity framework i zobacz, gdzie rzeczy są ulepszane w entity framework 6.

**Pierwsze wykonanie kwerendy — zimne zapytanie**

| Zapisy użytkownika kodu                                                                                     | Akcja                    | Wpływ na wydajność EF4                                                                                                                                                                                                                                                                                                                                                                                                        | Wpływ na wydajność EF5                                                                                                                                                                                                                                                                                                                                                                                                                                                    | Wpływ na wydajność EF6                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | Tworzenie kontekstu          | Medium                                                                                                                                                                                                                                                                                                                                                                                                                        | Medium                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | Małe                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | Tworzenie wyrażenia kwerendy | Małe                                                                                                                                                                                                                                                                                                                                                                                                                           | Małe                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Małe                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var c1 = q1.First();`                                                                             | Wykonanie kwerendy LINQ      | - Ładowanie metadanych: Wysokie, ale buforowane <br/> - Wyświetl generację: Potencjalnie bardzo wysoka, ale buforowana <br/> - Ocena parametrów: Średnia <br/> - Tłumaczenie zapytania: Medium <br/> - Generowanie Materializer: Średnie, ale buforowane <br/> - Wykonywanie kwerendy bazy danych: Potencjalnie wysokie <br/> + Connection.Otwórz <br/> + Command.ExecuteReader <br/> + DataReader.Read <br/> Materializacja obiektów: Średnia <br/> - Wyszukiwanie tożsamości: Medium | - Ładowanie metadanych: Wysokie, ale buforowane <br/> - Wyświetl generację: Potencjalnie bardzo wysoka, ale buforowana <br/> - Ocena parametrów: Niska <br/> - Tłumaczenie zapytania: Średnie, ale buforowane <br/> - Generowanie Materializer: Średnie, ale buforowane <br/> - Wykonywanie zapytań bazy danych: Potencjalnie wysokie (Lepsze zapytania w niektórych sytuacjach) <br/> + Connection.Otwórz <br/> + Command.ExecuteReader <br/> + DataReader.Read <br/> Materializacja obiektów: Średnia <br/> - Wyszukiwanie tożsamości: Medium | - Ładowanie metadanych: Wysokie, ale buforowane <br/> - Wyświetl generację: Średni, ale buforowany <br/> - Ocena parametrów: Niska <br/> - Tłumaczenie zapytania: Średnie, ale buforowane <br/> - Generowanie Materializer: Średnie, ale buforowane <br/> - Wykonywanie zapytań bazy danych: Potencjalnie wysokie (Lepsze zapytania w niektórych sytuacjach) <br/> + Connection.Otwórz <br/> + Command.ExecuteReader <br/> + DataReader.Read <br/> Materializacja obiektów: Średnia (szybsza niż EF5) <br/> - Wyszukiwanie tożsamości: Medium |
| `}`                                                                                                  | Connection.Close          | Małe                                                                                                                                                                                                                                                                                                                                                                                                                           | Małe                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Małe                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |


**Drugie wykonanie zapytania – ciepłe zapytanie**

| Zapisy użytkownika kodu                                                                                     | Akcja                    | Wpływ na wydajność EF4                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | Wpływ na wydajność EF5                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Wpływ na wydajność EF6                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | Tworzenie kontekstu          | Medium                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | Medium                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Małe                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | Tworzenie wyrażenia kwerendy | Małe                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | Małe                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | Małe                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var c1 = q1.First();`                                                                             | Wykonanie kwerendy LINQ      | - Wyszukiwanie ~~ładowania~~ metadanych: ~~Wysokie, ale buforowane~~ Niskie <br/> - Zobacz wyszukiwanie ~~generacji:~~ ~~Potencjalnie bardzo wysoki, ale buforowane~~ Niskie <br/> - Ocena parametrów: Średnia <br/> - Wyszukiwanie ~~tłumaczenia~~ zapytań: Medium <br/> - Wyszukiwanie ~~generowania~~ Materializer: ~~Średnie, ale buforowane~~ Niskie <br/> - Wykonywanie kwerendy bazy danych: Potencjalnie wysokie <br/> + Connection.Otwórz <br/> + Command.ExecuteReader <br/> + DataReader.Read <br/> Materializacja obiektów: Średnia <br/> - Wyszukiwanie tożsamości: Medium | - Wyszukiwanie ~~ładowania~~ metadanych: ~~Wysokie, ale buforowane~~ Niskie <br/> - Zobacz wyszukiwanie ~~generacji:~~ ~~Potencjalnie bardzo wysoki, ale buforowane~~ Niskie <br/> - Ocena parametrów: Niska <br/> - Wyszukiwanie ~~tłumaczenia~~ zapytań: ~~Średni, ale buforowany~~ niski <br/> - Wyszukiwanie ~~generowania~~ Materializer: ~~Średnie, ale buforowane~~ Niskie <br/> - Wykonywanie zapytań bazy danych: Potencjalnie wysokie (Lepsze zapytania w niektórych sytuacjach) <br/> + Connection.Otwórz <br/> + Command.ExecuteReader <br/> + DataReader.Read <br/> Materializacja obiektów: Średnia <br/> - Wyszukiwanie tożsamości: Medium | - Wyszukiwanie ~~ładowania~~ metadanych: ~~Wysokie, ale buforowane~~ Niskie <br/> - Zobacz wyszukiwanie ~~generacji:~~ ~~Średni, ale buforowany~~ niski <br/> - Ocena parametrów: Niska <br/> - Wyszukiwanie ~~tłumaczenia~~ zapytań: ~~Średni, ale buforowany~~ niski <br/> - Wyszukiwanie ~~generowania~~ Materializer: ~~Średnie, ale buforowane~~ Niskie <br/> - Wykonywanie zapytań bazy danych: Potencjalnie wysokie (Lepsze zapytania w niektórych sytuacjach) <br/> + Connection.Otwórz <br/> + Command.ExecuteReader <br/> + DataReader.Read <br/> Materializacja obiektów: Średnia (szybsza niż EF5) <br/> - Wyszukiwanie tożsamości: Medium |
| `}`                                                                                                  | Connection.Close          | Małe                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | Małe                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | Małe                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |


Istnieje kilka sposobów, aby zmniejszyć koszt wydajności zarówno zimnych, jak i ciepłych zapytań, a my przyjrzymy się tym w poniższej sekcji. W szczególności przyjrzymy się zmniejszeniu kosztów ładowania modelu w zimnych zapytaniach przy użyciu wstępnie wygenerowanych widoków, co powinno pomóc złagodzić bóle wydajności doświadczane podczas generowania widoku. W przypadku ciepłych zapytań omówimy buforowanie planu kwerend, brak zapytań śledzenia i różne opcje wykonywania kwerend.

### <a name="21-what-is-view-generation"></a>2.1 Co to jest generowanie widoku?

Aby zrozumieć, czym jest generowanie widoku, musimy najpierw zrozumieć, czym są "Widoki mapowania". Widoki mapowania są wykonywalnymi reprezentacjami przekształceń określonych w mapowaniu dla każdego zestawu jednostek i skojarzenia. Wewnętrznie te widoki mapowania przybierają kształt CQTs (kanonicznych drzew zapytań). Istnieją dwa typy widoków mapowania:

-   Widoki kwerendy: reprezentują one transformację niezbędną do przejścia ze schematu bazy danych do modelu koncepcyjnego.
-   Widoki aktualizacji: reprezentują one transformację niezbędną do przejścia z modelu koncepcyjnego do schematu bazy danych.

Należy pamiętać, że model koncepcyjny może się różnić od schematu bazy danych na różne sposoby. Na przykład jedna tabela może służyć do przechowywania danych dla dwóch różnych typów jednostek. Dziedziczenie i nietrywialne mapowania odgrywają rolę w złożoności widoków mapowania.

Proces obliczania tych widoków na podstawie specyfikacji mapowania jest tym, co nazywamy generowaniem widoku. Generowanie widoku może odbywać się dynamicznie po załadowaniu modelu lub w czasie kompilacji przy użyciu "wstępnie wygenerowanych widoków"; te ostatnie są serializowane w formie instrukcji\# Entity SQL do pliku C lub VB.

Podczas generowania widoków są one również sprawdzane. Z punktu widzenia wydajności zdecydowana większość generowania widoku jest faktycznie sprawdzanie poprawności widoków, co zapewnia, że połączenia między jednostkami mają sens i mają poprawną kardynalność dla wszystkich obsługiwanych operacji.

Po wykonaniu kwerendy za pośrednictwem zestawu jednostek kwerenda jest łączona z odpowiednim widokiem kwerendy, a wynik tej kompozycji jest uruchamiany za pośrednictwem kompilatora planu w celu utworzenia reprezentacji kwerendy, którą magazyn zapasowy może zrozumieć. W przypadku programu SQL Server końcowym wynikiem tej kompilacji będzie instrukcja T-SQL SELECT. Przy pierwszej aktualizacji za pośrednictwem zestawu jednostek jest uruchamiany przez podobny proces, aby przekształcić go w instrukcje DML dla docelowej bazy danych.

### <a name="22-factors-that-affect-view-generation-performance"></a>2.2 Czynniki wpływające na wydajność generowania widoku

Wydajność kroku generowania widoku zależy nie tylko od rozmiaru modelu, ale także od tego, jak połączony jest model. Jeśli dwa jednostki są połączone za pośrednictwem łańcucha dziedziczenia lub skojarzenia, są one uważane za połączone. Podobnie, jeśli dwie tabele są połączone za pomocą klucza obcego, są one połączone. Wraz ze wzrostem liczby połączonych jednostek i tabel w schematach zwiększa się koszt generowania widoku.

Algorytm, którego używamy do generowania i sprawdzania poprawności widoków jest wykładniczy w najgorszym przypadku, chociaż używamy niektórych optymalizacji, aby to poprawić. Największe czynniki, które wydają się negatywnie wpływać na wydajność są:

-   Rozmiar modelu, odnosząc się do liczby jednostek i liczby skojarzeń między tymi jednostkami.
-   Złożoność modelu, w szczególności dziedziczenie obejmujące dużą liczbę typów.
-   Korzystanie z niezależnych skojarzeń zamiast skojarzeń kluczy zagranicznych.

W przypadku małych, prostych modeli koszt może być wystarczająco mały, aby nie przejmować się korzystaniem z wstępnie wygenerowanych widoków. Wraz ze wzrostem rozmiaru i złożoności modelu dostępnych jest kilka opcji zmniejszających koszt generowania i sprawdzania poprawności widoku.

### <a name="23-using-pre-generated-views-to-decrease-model-load-time"></a>2.3 Wykorzystanie wstępnie wygenerowanych widoków do skrócenia czasu ładowania modelu

Szczegółowe informacje na temat korzystania z wstępnie wygenerowanych widoków w programie Entity Framework 6 można znaleźć na stronie [Wstępnie wygenerowane widoki mapowania](~/ef6/fundamentals/performance/pre-generated-views.md)

#### <a name="231-pre-generated-views-using-the-entity-framework-power-tools-community-edition"></a>2.3.1 Wstępnie wygenerowane widoki przy użyciu wersji community entity framework power tools

Za pomocą [programu Entity Framework 6 Power Tools Community Edition](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) można generować widoki modeli EDMX i Code First, klikając prawym przyciskiem myszy plik klasy modelu i korzystając z menu Entity Framework, aby wybrać opcję "Generowanie widoków". Entity Framework Power Tools Community Edition działa tylko w kontekstach pochodzących z DbContext.

#### <a name="232-how-to-use-pre-generated-views-with-a-model-created-by-edmgen"></a>2.3.2 Jak korzystać z wstępnie wygenerowanych widoków z modelem stworzonym przez EDMGen

EDMGen jest narzędziem, które jest dostarczane z .NET i współpracuje z Entity Framework 4 i 5, ale nie z Entity Framework 6. EDMGen umożliwia generowanie pliku modelu, warstwy obiektu i widoków z wiersza polecenia. Jednym z wyjść będzie plik Widoków w wybranym języku,\#VB lub C . Jest to plik kodu zawierający fragmenty kodu ENCJI SQL dla każdego zestawu jednostek. Aby włączyć wstępnie wygenerowane widoki, wystarczy dołączyć plik do projektu.

Jeśli ręcznie wniesiesz zmiany do plików schematu dla modelu, musisz ponownie wygenerować plik widoków. Można to zrobić, uruchamiając EDMGen z **/mode:ViewGeneration** flagi.

#### <a name="233-how-to-use-pre-generated-views-with-an-edmx-file"></a>2.3.3 Jak korzystać z wstępnie wygenerowanych widoków z plikiem EDMX

Można również użyć EDMGen do generowania widoków dla pliku EDMX - wcześniej przywoływany temat MSDN opisuje, jak dodać zdarzenie przed kompilacją, aby to zrobić — ale jest to skomplikowane i są przypadki, w których nie jest to możliwe. Zazwyczaj łatwiej jest użyć szablonu T4 do generowania widoków, gdy model znajduje się w pliku edmx.

ADO.NET blogu zespołu zawiera post, który opisuje, jak używać szablonu \< https://docs.microsoft.com/archive/blogs/adonet/how-to-use-a-t4-template-for-view-generation>)T4 do generowania widoku ( . Ten post zawiera szablon, który można pobrać i dodać do projektu. Szablon został napisany dla pierwszej wersji entity framework, więc nie są one gwarantowane do pracy z najnowszymi wersjami entity framework. Można jednak pobrać bardziej aktualny zestaw szablonów generowania widoku dla programu Entity Framework 4 i 5 z galerii programu Visual Studio:

-   VB.NET:\<http://visualstudiogallery.msdn.microsoft.com/118b44f2-1b91-4de2-a584-7a680418941d>
-   C\#:\<http://visualstudiogallery.msdn.microsoft.com/ae7730ce-ddab-470f-8456-1b313cd2c44d>

Jeśli używasz entity framework 6 można uzyskać szablony T4 generacji \< http://visualstudiogallery.msdn.microsoft.com/18a7db90-6705-4d19-9dd1-0a6c23d0751f>widoku z Galerii programu Visual Studio w .

### <a name="24-reducing-the-cost-of-view-generation"></a>2.4 Obniżenie kosztów generowania widoku

Za pomocą wstępnie wygenerowanych widoków przenosi koszt generowania widoku z ładowania modelu (czas wykonywania) do czasu projektowania. Chociaż poprawia to wydajność uruchamiania w czasie wykonywania, nadal będzie odczuwać ból generowania widoku podczas tworzenia. Istnieje kilka dodatkowych sztuczek, które mogą pomóc zmniejszyć koszt generowania widoku, zarówno w czasie kompilacji, jak i czasie wykonywania.

#### <a name="241-using-foreign-key-associations-to-reduce-view-generation-cost"></a>2.4.1 Korzystanie ze stowarzyszeń kluczy obcych w celu zmniejszenia kosztów generowania widoku

Widzieliśmy wiele przypadków, w których zmiana skojarzeń w modelu z niezależnych stowarzyszeń na stowarzyszenia kluczy zagranicznych znacznie poprawiła czas spędzony w generowaniu widoków.

Aby zademonstrować to ulepszenie, wygenerowaliśmy dwie wersje modelu Navision przy użyciu EDMGen. *Uwaga: opis modelu systemu Navision można znaleźć w załączniku C.* Model Navision jest interesujący dla tego ćwiczenia ze względu na bardzo dużą ilość jednostek i relacji między nimi.

Jedna wersja tego bardzo dużego modelu została wygenerowana za pomocą stowarzyszeń kluczy zagranicznych, a druga została wygenerowana za pomocą niezależnych skojarzeń. Następnie czas czasu, jak długo zajęło wygenerowanie widoków dla każdego modelu. Entity Framework 5 test używany GenerateViews() metoda z klasy EntityViewGenerator do generowania widoków, podczas gdy entity framework 6 test używany GenerateViews() metoda z klasy StorageMappingItemCollection. To z powodu restrukturyzacji kodu, które miały miejsce w entity framework 6 codebase.

Za pomocą entity framework 5, generowanie widoku dla modelu z kluczami obcymi trwało 65 minut na komputerze laboratoryjnym. Nie wiadomo, jak długo zajęłoby wygenerowanie widoków dla modelu, który używał niezależnych skojarzeń. Pozostawiliśmy test uruchomiony przez ponad miesiąc, zanim komputer został ponownie uruchomiony w naszym laboratorium, aby zainstalować miesięczne aktualizacje.

Za pomocą entity framework 6, generowanie widoku dla modelu z kluczami obcymi trwało 28 sekund na tej samej maszynie laboratoryjnych. Wyświetl generowanie dla modelu, który używa niezależnych skojarzeń trwało 58 sekund. Ulepszenia wprowadzone do entity framework 6 na jego kod generowania widoku oznacza, że wiele projektów nie będzie potrzebować wstępnie wygenerowanych widoków, aby uzyskać szybsze czasy uruchamiania.

Należy zauważyć, że wstępnie generujące widoki w entity framework 4 i 5 można wykonać za pomocą EDMGen lub entity framework power tools. Dla entity framework 6 generowania widoku można wykonać za pośrednictwem entity framework power tools lub programowo, jak opisano w [wstępnie wygenerowanych widoków mapowania](~/ef6/fundamentals/performance/pre-generated-views.md).

##### <a name="2411-how-to-use-foreign-keys-instead-of-independent-associations"></a>2.4.1.1 Jak używać kluczy zagranicznych zamiast niezależnych stowarzyszeń

Podczas korzystania z EDMGen lub Projektanta jednostek w programie Visual Studio, można uzyskać FKs domyślnie i trwa tylko jedno pole wyboru lub flagi wiersza polecenia, aby przełączyć się między FKs i IAs.

Jeśli masz duży model Code First, przy użyciu niezależnych skojarzeń będzie mieć taki sam wpływ na generowanie widoku. Można uniknąć tego wpływu, dołączając właściwości klucza obcego na klasach dla obiektów zależnych, chociaż niektórzy deweloperzy uznają to za zanieczyszczające ich model obiektu. Więcej informacji na ten temat \< http://blog.oneunicorn.com/2011/12/11/whats-the-deal-with-mapping-foreign-keys-using-the-entity-framework/>można znaleźć w programie .

| Podczas korzystania z      | Zrób to                                                                                                                                                                                                                                                                                                                              |
|:----------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Projektant encji | Po dodaniu skojarzenia między dwoma encjami upewnij się, że masz ograniczenie referencyjne. Ograniczenia referencyjne informują entity framework o użyciu kluczy obcych zamiast niezależnych skojarzeń. Aby uzyskać \< https://docs.microsoft.com/archive/blogs/efdesign/foreign-keys-in-the-entity-framework>więcej informacji odwiedź . |
| EDMGen ( EDMGen )          | Podczas korzystania z EDMGen do generowania plików z bazy danych, klucze obce będą przestrzegane i dodawane do modelu jako takie. Aby uzyskać więcej informacji na temat różnych [http://msdn.microsoft.com/library/bb387165.aspx](https://msdn.microsoft.com/library/bb387165.aspx)opcji oferowanych przez EDMGen wizyta .                           |
| Najpierw kod      | Zobacz sekcję "Konwencja relacji" w temacie [Konwencje code first,](~/ef6/modeling/code-first/conventions/built-in.md) aby uzyskać informacje na temat dołączania właściwości klucza obcego do obiektów zależnych podczas korzystania z code first.                                                                                              |

#### <a name="242-moving-your-model-to-a-separate-assembly"></a>2.4.2 Przenoszenie modelu do oddzielnego zespołu

Gdy model jest uwzględniony bezpośrednio w projekcie aplikacji i generujesz widoki za pośrednictwem zdarzenia przed kompilacji lub szablonu T4, generowanie i sprawdzanie poprawności widoku odbędzie się za każdym razem, gdy projekt zostanie przebudowany, nawet jeśli model nie został zmieniony. Jeśli przeniesiesz model do oddzielnego zestawu i odwołujesz się do niego z projektu aplikacji, można wprowadzić inne zmiany w aplikacji bez konieczności odbudowywania projektu zawierającego model.

*Uwaga:*  podczas przenoszenia modelu do oddzielnych zestawów należy pamiętać, aby skopiować parametry połączenia dla modelu do pliku konfiguracji aplikacji projektu klienta.

#### <a name="243-disable-validation-of-an-edmx-based-model"></a>2.4.3 Wyłącz sprawdzanie poprawności modelu opartego na edmx

Modele EDMX są sprawdzane w czasie kompilacji, nawet jeśli model pozostaje niezmieniony. Jeśli model został już zweryfikowany, można pominąć sprawdzanie poprawności w czasie kompilacji, ustawiając właściwość "Sprawdź poprawność na kompilacji" na false w oknie właściwości. Po zmianie mapowania lub modelu można tymczasowo ponownie włączyć sprawdzanie poprawności, aby zweryfikować zmiany.

Należy zauważyć, że wprowadzono ulepszenia wydajności do projektanta struktury jednostek dla entity framework 6, a koszt "Sprawdź poprawność na kompilacji" jest znacznie niższa niż w poprzednich wersjach projektanta.

## <a name="3-caching-in-the-entity-framework"></a>3 Buforowanie w ramach jednostki

Entity Framework ma wbudowane następujące formy buforowania:

1.  Buforowanie obiektów — ObjectStateManager wbudowany w wystąpienie ObjectContext przechowuje śledzenie w pamięci obiektów, które zostały pobrane przy użyciu tego wystąpienia. Jest to również znane jako pamięci podręcznej pierwszego poziomu.
2.  Buforowanie planu kwerendy — ponowne ponowne połączenie wygenerowanego polecenia magazynu, gdy kwerenda jest wykonywana więcej niż jeden raz.
3.  Buforowanie metadanych — udostępnianie metadanych dla modelu w różnych połączeniach z tym samym modelem.

Oprócz pamięci podręcznej, które EF zapewnia po wyjęciu z pudełka, specjalny rodzaj dostawcy danych ADO.NET znany jako dostawca zawijania może również służyć do rozszerzenia entity framework z pamięci podręcznej dla wyników pobranych z bazy danych, znany również jako buforowania drugiego poziomu.

### <a name="31-object-caching"></a>3.1 Buforowanie obiektów

Domyślnie, gdy jednostka jest zwracana w wynikach kwerendy, tuż przed EF materializes go ObjectContext sprawdzi, czy jednostka z tym samym kluczem została już załadowana do objectStateManager. Jeśli jednostka z tymi samymi kluczami jest już obecny EF będzie zawierać go w wynikach kwerendy. Mimo że EF będzie nadal wystawiać kwerendę do bazy danych, to zachowanie można pominąć wiele kosztów materializacji jednostki wiele razy.

#### <a name="311-getting-entities-from-the-object-cache-using-dbcontext-find"></a>3.1.1 Pozyskiwanie jednostek z pamięci podręcznej obiektów przy użyciu funkcji DbContext Find

W przeciwieństwie do zwykłej kwerendy Find metody w DbSet (interfejsy API zawarte po raz pierwszy w EF 4.1) wykona wyszukiwanie w pamięci przed nawet wydawania kwerendy w bazie danych. Należy pamiętać, że dwa różne ObjectContext wystąpień będzie miał dwa różne ObjectStateManager wystąpień, co oznacza, że mają oddzielne pamięci podręczne obiektów.

Funkcja Znajdź używa wartości klucza podstawowego do znalezienia jednostki śledzonej przez kontekst. Jeśli jednostka nie znajduje się w kontekście, kwerenda zostanie wykonana i oceniona względem bazy danych, a wartość null zostanie zwrócona, jeśli jednostka nie zostanie znaleziona w kontekście lub w bazie danych. Należy zauważyć, że funkcja Znajdź zwraca również jednostki, które zostały dodane do kontekstu, ale nie zostały jeszcze zapisane w bazie danych.

Podczas korzystania z funkcji Znajdź należy wziąć pod uwagę wydajność. Wywołania tej metody domyślnie wyzwoli sprawdzanie poprawności pamięci podręcznej obiektów w celu wykrycia zmian, które są nadal oczekujące zatwierdzić do bazy danych. Ten proces może być bardzo kosztowne, jeśli istnieje bardzo duża liczba obiektów w pamięci podręcznej obiektów lub na wykresie dużych obiektów dodawanych do pamięci podręcznej obiektów, ale można go również wyłączyć. W niektórych przypadkach można dostrzec ponad rząd wielkości różnicy w wywoływaniu Find metody po wyłączeniu automatycznego wykrywania zmian. Jednak drugi rząd wielkości jest postrzegany, gdy obiekt faktycznie znajduje się w pamięci podręcznej, a gdy obiekt musi zostać pobrany z bazy danych. Oto przykładowy wykres z pomiarami wykonanymi przy użyciu niektórych naszych mikrobenchmarków, wyrażonych w milisekundach, z obciążeniem 5000 jednostek:

![.NET 4.5 skala logarytmiczna](~/ef6/media/net45logscale.png ".NET 4.5 - skala logarytmiczna")

Przykład funkcji Znajdź z wyłączonymi automatycznymi wykrywaniami:

``` csharp
    context.Configuration.AutoDetectChangesEnabled = false;
    var product = context.Products.Find(productId);
    context.Configuration.AutoDetectChangesEnabled = true;
    ...
```

Co należy wziąć pod uwagę podczas korzystania z Find metody jest:

1.  Jeśli obiekt nie znajduje się w pamięci podręcznej korzyści find są negowane, ale składnia jest nadal prostsza niż kwerenda według klucza.
2.  Jeśli automatyczne wykrywanie zmian jest włączone koszt Find metody może wzrosnąć o jeden rząd wielkości lub nawet więcej w zależności od złożoności modelu i ilość jednostek w pamięci podręcznej obiektów.

Należy również pamiętać, że funkcja Znajdź zwraca tylko jednostkę, której szukasz, i nie ładuje automatycznie skojarzonych z nią jednostek, jeśli nie znajdują się one jeszcze w pamięci podręcznej obiektów. Jeśli chcesz pobrać skojarzone jednostki, można użyć kwerendy według klucza z wczytywaniem. Aby uzyskać więcej informacji, zobacz **8.1 Lazy Loading vs. Eager Loading**.

#### <a name="312-performance-issues-when-the-object-cache-has-many-entities"></a>3.1.2 Problemy z wydajnością, gdy pamięć podręczna obiektów ma wiele jednostek

Pamięć podręczna obiektów pomaga zwiększyć ogólną responsywność entity framework. Jednak gdy pamięć podręczna obiektów ma bardzo dużą ilość jednostek załadowanych może mieć wpływ na niektóre operacje, takie jak Dodaj, Usuń, Znajdź, Wpis, SaveChanges i więcej. W szczególności operacje, które wyzwalają wywołanie DetectChanges będą miały negatywny wpływ na bardzo dużych pamięci podręcznych obiektów. DetectChanges synchronizuje wykres obiektu z menedżerem stanu obiektu, a jego wydajność zależy bezpośrednio od rozmiaru wykresu obiektu. Aby uzyskać więcej informacji na temat wykrywania zmian, zobacz [Śledzenie zmian w jednostkach POCO](https://msdn.microsoft.com/library/dd456848.aspx).

Podczas korzystania z entity framework 6, deweloperzy są w stanie wywołać AddRange i RemoveRange bezpośrednio na DbSet, zamiast iteracji w kolekcji i wywołanie Dodaj raz na wystąpienie. Zaletą korzystania z metod zakresu jest to, że koszt DetectChanges jest wypłacany tylko raz dla całego zestawu jednostek, w przeciwieństwie do raz na każdą dodaną jednostkę.

### <a name="32-query-plan-caching"></a>3.2 Buforowanie planu zapytań

Przy pierwszym wykonaniu kwerendy przechodzi przez kompilator planu wewnętrznego, aby przetłumaczyć zapytanie koncepcyjne do polecenia magazynu (na przykład T-SQL, który jest wykonywany podczas uruchamiania z programem SQL Server).Jeśli buforowanie planu kwerend jest włączone, przy następnym wykonaniu kwerendy polecenie magazynu jest pobierane bezpośrednio z pamięci podręcznej planu kwerendy do wykonania, pomijając kompilator planu.

Pamięć podręczna planu kwerend jest współużytkowana przez wystąpienia ObjectContext w ramach tej samej biblioteki appdomain. Nie trzeba trzymać na ObjectContext wystąpienia, aby korzystać z buforowania planu kwerendy.

#### <a name="321-some-notes-about-query-plan-caching"></a>3.2.1 Niektóre uwagi dotyczące buforowania planu zapytań

-   Pamięć podręczna planu kwerend jest współużytkowana dla wszystkich typów zapytań: Entity SQL, LINQ do jednostek i CompiledQuery obiektów.
-   Domyślnie buforowanie planu kwerend jest włączone dla zapytań SQL encji, niezależnie od tego, czy są wykonywane za pośrednictwem narzędzia EntityCommand, czy za pośrednictwem zapytania objectQuery. Jest również domyślnie włączona dla LINQ do zapytań jednostek w ramach encji w programie .NET 4.5 i entity Framework 6
    -   Buforowanie planu kwerend można wyłączyć, ustawiając właściwość EnablePlanCaching (na EntityCommand lub ObjectQuery) na false. Przykład:
``` csharp
                    var query = from customer in context.Customer
                                where customer.CustomerId == id
                                select new
                                {
                                    customer.CustomerId,
                                    customer.Name
                                };
                    ObjectQuery oQuery = query as ObjectQuery;
                    oQuery.EnablePlanCaching = false;
```
-   W przypadku zapytań sparametryzowanych zmiana wartości parametru będzie nadal uderzać w kwerendę buforowaną. Ale zmiana aspektów parametru (na przykład rozmiar, precyzja lub skala) spowoduje przejście innego wpisu w pamięci podręcznej.
-   Podczas korzystania z encji SQL ciąg zapytania jest częścią klucza. Zmiana kwerendy w ogóle spowoduje różne wpisy pamięci podręcznej, nawet jeśli kwerendy są funkcjonalnie równoważne. Obejmuje to zmiany w wielkości liter lub odstępach.
-   Podczas korzystania z LINQ kwerenda jest przetwarzana w celu wygenerowania części klucza. Zmiana wyrażenia LINQ spowoduje wygenerować inny klucz.
-   Mogą obowiązywać inne ograniczenia techniczne; Aby uzyskać więcej informacji, zobacz Kwerendy z automatyczną kompilacją.

#### <a name="322-cache-eviction-algorithm"></a>3.2.2 Algorytm eksmisji pamięci podręcznej

Zrozumienie, jak działa wewnętrzny algorytm pomoże Ci dowiedzieć się, kiedy włączyć lub wyłączyć buforowanie planu kwerend. Algorytm oczyszczania jest następujący:

1.  Gdy pamięć podręczna zawiera określoną liczbę wpisów (800), uruchamiamy czasomierz, który okresowo (raz na minutę) przeciera pamięć podręczną.
2.  Podczas przeciągów pamięci podręcznej wpisy są usuwane z pamięci podręcznej na podstawie LFRU (najmniej często — ostatnio używane). Algorytm ten bierze pod uwagę zarówno liczbę trafień, jak i wiek przy podejmowaniu decyzji, które wpisy są wyrzucane.
3.  Na końcu każdego wyciągnięcia po ścieżce pamięci podręcznej pamięć podręczna ponownie zawiera 800 wpisów.

Wszystkie wpisy pamięci podręcznej są traktowane jednakowo podczas określania, które wpisy do eksmisji. Oznacza to, że polecenie magazynu dla CompiledQuery ma taką samą szansę eksmisji jak polecenie magazynu dla kwerendy SQL jednostki.

Należy zauważyć, że czasomierz eksmisji pamięci podręcznej jest uruchamiany, gdy w pamięci podręcznej znajduje się 800 jednostek, ale pamięć podręczna jest przeciągnięta tylko 60 sekund po uruchomieniu tego czasomierza. Oznacza to, że przez maksymalnie 60 sekund pamięć podręczna może rosnąć do dość dużej.

#### <a name="323-test-metrics-demonstrating-query-plan-caching-performance"></a>3.2.3 Dane testowe demonstrujące wydajność buforowania planu kwerend

Aby zademonstrować wpływ buforowania planu kwerend na wydajność aplikacji, przeprowadziliśmy test, w którym wykonaliśmy wiele zapytań sql jednostki względem modelu systemu Navision. Zobacz dodatek opis modelu systemu Navision i typów zapytań, które zostały wykonane. W tym teście najpierw iterować za pośrednictwem listy zapytań i wykonać każdy z nich raz, aby dodać je do pamięci podręcznej (jeśli buforowanie jest włączone). Ten krok jest nierozerwiony. Następnie śpimy w głównym wątku przez ponad 60 sekund, aby umożliwić zamiatanie pamięci podręcznej; na koniec iterujemy listę po raz drugi, aby wykonać zapytania w pamięci podręcznej. Ponadto pamięć podręczna planu programu SQL Server jest opróżniana przed wykonaniem każdego zestawu zapytań, tak aby czasy, które uzyskujemy, dokładnie odzwierciedlały korzyści wynikające z pamięci podręcznej planu kwerend.

##### <a name="3231-test-results"></a>3.2.3.1 Wyniki badań

| Testowanie                                                                   | EF5 brak pamięci podręcznej | EF5 w pamięci podręcznej | EF6 brak pamięci podręcznej | EF6 w pamięci podręcznej |
|:-----------------------------------------------------------------------|:-------------|:-----------|:-------------|:-----------|
| Wyliczanie wszystkich zapytań 18723                                          | 124          | 125.4      | 124.3        | 125.3      |
| Unikanie zamiatania (tylko pierwsze 800 zapytań, niezależnie od złożoności)  | 41.7         | 5.5        | 40.5         | 5.4        |
| Tylko zapytania AggregatingSubtotals (łącznie 178 — co pozwala uniknąć wyciągnięcia po ścieżce) | 39.5         | 4.5        | 38.1         | 4.6        |

*Wszystkie czasy w sekundach.*

Moral — podczas wykonywania wielu różnych zapytań (na przykład dynamicznie tworzone kwerendy), buforowanie nie pomaga, a wynikowe opróżnianie pamięci podręcznej może zachować zapytania, które najbardziej skorzystają z buforowania planu z faktycznie go używać.

Zapytania AggregatingSubtotals są najbardziej złożone z zapytań, które testowane z. Zgodnie z oczekiwaniami, im bardziej złożona jest kwerenda, tym więcej korzyści zobaczysz z buforowania planu kwerend.

Ponieważ CompiledQuery jest naprawdę linq kwerendy z jego planu w pamięci podręcznej, porównanie CompiledQuery w porównaniu do równoważnej jednostki kwerendy SQL powinny mieć podobne wyniki. W rzeczywistości jeśli aplikacja ma wiele dynamicznych zapytań SQL jednostki, wypełnianie pamięci podręcznej z kwerendami będzie również skutecznie powodować CompiledQueries do "dekompilowania", gdy są one opróżniane z pamięci podręcznej. W tym scenariuszu wydajność może zostać zwiększona przez wyłączenie buforowania w zapytaniach dynamicznych, aby nadać priorytet CompiledQueries. Jeszcze lepiej, oczywiście, byłoby przepisać aplikację do korzystania z sparametryzowanych zapytań zamiast zapytań dynamicznych.

### <a name="33-using-compiledquery-to-improve-performance-with-linq-queries"></a>3.3 Korzystanie z CompiledQuery w celu zwiększenia wydajności za pomocą zapytań LINQ

Nasze testy wskazują, że za pomocą CompiledQuery może przynieść korzyści 7% w stosunku do automatycznie kompilowanych zapytań LINQ; oznacza to, że spędzisz 7% mniej czasu na wykonywaniu kodu ze stosu Entity Framework; nie oznacza to, że twoja aplikacja będzie o 7% szybsza. Ogólnie rzecz biorąc, koszt pisania i utrzymywania CompiledQuery obiektów w EF 5.0 nie może być warte kłopotów w porównaniu do korzyści. Przebieg może się różnić, więc wystają ci, jeśli projekt wymaga dodatkowego naciśnięcia. Należy zauważyć, że CompiledQueries są zgodne tylko z modelami pochodzącymi objectContext i nie są zgodne z modelami pochodnymi DbContext.

Aby uzyskać więcej informacji na temat tworzenia i wywoływania CompiledQuery, zobacz [Skompilowane zapytania (LINQ do jednostek)](https://msdn.microsoft.com/library/bb896297.aspx).

Istnieją dwie kwestie, które należy podjąć podczas korzystania z CompiledQuery, a mianowicie wymóg używania wystąpień statycznych i problemy, które mają z możliwością komponowania. Poniżej przedstawiono szczegółowe wyjaśnienie tych dwóch rozważań.

#### <a name="331-use-static-compiledquery-instances"></a>3.3.1 Używanie statycznych wystąpień CompiledQuery

Ponieważ kompilowanie zapytania LINQ jest procesem czasochłonnym, nie chcemy tego robić za każdym razem, gdy musimy pobrać dane z bazy danych. CompiledQuery wystąpień pozwalają skompilować raz i uruchomić wiele razy, ale trzeba być ostrożnym i zaopatrywać się ponownie użyć tego samego wystąpienia CompiledQuery za każdym razem zamiast kompilować go w kółko. Użycie statycznych elementów członkowskich do przechowywania CompiledQuery wystąpień staje się konieczne; w przeciwnym razie nie zobaczysz żadnych korzyści.

Załóżmy na przykład, że strona ma następującą treść metody do obsługi wyświetlania produktów dla wybranej kategorii:

``` csharp
    // Warning: this is the wrong way of using CompiledQuery
    using (NorthwindEntities context = new NorthwindEntities())
    {
        string selectedCategory = this.categoriesList.SelectedValue;

        var productsForCategory = CompiledQuery.Compile<NorthwindEntities, string, IQueryable<Product>>(
            (NorthwindEntities nwnd, string category) =>
                nwnd.Products.Where(p => p.Category.CategoryName == category)
        );

        this.productsGrid.DataSource = productsForCategory.Invoke(context, selectedCategory).ToList();
        this.productsGrid.DataBind();
    }

    this.productsGrid.Visible = true;
```

W takim przypadku utworzysz nowe wystąpienie CompiledQuery na bieżąco za każdym razem, gdy metoda jest wywoływana. Zamiast widzieć korzyści wydajności, pobierając polecenie magazynu z pamięci podręcznej planu kwerend, CompiledQuery przejdzie przez kompilator planu za każdym razem, gdy zostanie utworzone nowe wystąpienie. W rzeczywistości będzie zanieczyszczanie pamięci podręcznej planu kwerendy z nowym compiledquery wpis za każdym razem, gdy metoda jest wywoływana.

Zamiast tego chcesz utworzyć statyczne wystąpienie skompilowanego zapytania, więc wywołujesz tę samą skompilowaną kwerendę za każdym razem, gdy metoda jest wywoływana. Jednym ze sposobów, aby tak jest dodanie CompiledQuery wystąpienie jako element członkowski kontekstu obiektu.Następnie można zrobić rzeczy trochę czystsze, uzyskując dostęp do CompiledQuery za pomocą metody pomocnika:

``` csharp
    public partial class NorthwindEntities : ObjectContext
    {
        private static readonly Func<NorthwindEntities, string, IEnumerable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
            (NorthwindEntities context, string categoryName) =>
                context.Products.Where(p => p.Category.CategoryName == categoryName)
            );

        public IEnumerable<Product> GetProductsForCategory(string categoryName)
        {
            return productsForCategoryCQ.Invoke(this, categoryName).ToList();
        }
```

Ta metoda pomocnika będzie wywoływana w następujący sposób:

``` csharp
    this.productsGrid.DataSource = context.GetProductsForCategory(selectedCategory);
```

#### <a name="332-composing-over-a-compiledquery"></a>3.3.2 Komponowanie na CompiledQuery

Możliwość komponowania nad dowolnym zapytaniem LINQ jest niezwykle przydatna; aby to zrobić, wystarczy wywołać metodę po IQueryable, takich jak *Skip()* lub *Count()*. Jednak w ten sposób zasadniczo zwraca nowy IQueryable obiektu. Chociaż nie ma nic, aby powstrzymać cię technicznie od komponowania przez CompiledQuery, spowoduje to generowanie nowego obiektu IQueryable, który wymaga ponownego przejścia przez kompilator planu.

Niektóre składniki będą korzystać z składanych obiektów IQueryable, aby włączyć zaawansowane funkcje. Na przykład ASP. Net GridView może być powiązany z danymi do obiektu IQueryable za pośrednictwem SelectMethod właściwości. GridView będzie następnie komponować nad tym IQueryable obiektu, aby umożliwić sortowanie i stronicowanie za pomocą modelu danych. Jak widać, za pomocą CompiledQuery dla GridView nie trafi skompilowane zapytanie, ale wygeneruje nowe zapytanie autokompilowane.

Jednym z miejsc, w których można napotkać to podczas dodawania filtrów progresywnych do kwerendy. Załóżmy na przykład, że masz stronę Klienci z kilkoma listami rozwijaną dla filtrów opcjonalnych (na przykład Kraj i Liczba zamówień). Można skomponować te filtry za pośrednictwem IQueryable wyniki CompiledQuery, ale spowoduje to nowe zapytanie przechodzi przez kompilator planu za każdym razem, gdy go wykonać.

``` csharp
    using (NorthwindEntities context = new NorthwindEntities())
    {
        IQueryable<Customer> myCustomers = context.InvokeCustomersForEmployee();

        if (this.orderCountFilterList.SelectedItem.Value != defaultFilterText)
        {
            int orderCount = int.Parse(orderCountFilterList.SelectedValue);
            myCustomers = myCustomers.Where(c => c.Orders.Count > orderCount);
        }

        if (this.countryFilterList.SelectedItem.Value != defaultFilterText)
        {
            myCustomers = myCustomers.Where(c => c.Address.Country == countryFilterList.SelectedValue);
        }

        this.customersGrid.DataSource = myCustomers;
        this.customersGrid.DataBind();
    }
```

 Aby uniknąć tej ponownej kompilacji, można przepisać CompiledQuery wziąć pod uwagę możliwe filtry:

``` csharp
    private static readonly Func<NorthwindEntities, int, int?, string, IQueryable<Customer>> customersForEmployeeWithFiltersCQ = CompiledQuery.Compile(
        (NorthwindEntities context, int empId, int? countFilter, string countryFilter) =>
            context.Customers.Where(c => c.Orders.Any(o => o.EmployeeID == empId))
            .Where(c => countFilter.HasValue == false || c.Orders.Count > countFilter)
            .Where(c => countryFilter == null || c.Address.Country == countryFilter)
        );
```

Który byłby wywoływany w interfejsie użytkownika, takich jak:

``` csharp
    using (NorthwindEntities context = new NorthwindEntities())
    {
        int? countFilter = (this.orderCountFilterList.SelectedIndex == 0) ?
            (int?)null :
            int.Parse(this.orderCountFilterList.SelectedValue);

        string countryFilter = (this.countryFilterList.SelectedIndex == 0) ?
            null :
            this.countryFilterList.SelectedValue;

        IQueryable<Customer> myCustomers = context.InvokeCustomersForEmployeeWithFilters(
                countFilter, countryFilter);

        this.customersGrid.DataSource = myCustomers;
        this.customersGrid.DataBind();
    }
```

 Kompromisem w tym miejscu jest wygenerowane polecenie magazynu zawsze będzie miał filtry z kontroli null, ale powinny one być dość proste dla serwera bazy danych do optymalizacji:

``` SQL
...
WHERE ((0 = (CASE WHEN (@p__linq__1 IS NOT NULL) THEN cast(1 as bit) WHEN (@p__linq__1 IS NULL) THEN cast(0 as bit) END)) OR ([Project3].[C2] > @p__linq__2)) AND (@p__linq__3 IS NULL OR [Project3].[Country] = @p__linq__4)
```

### <a name="34-metadata-caching"></a>3.4 Buforowanie metadanych

Entity Framework obsługuje również buforowanie metadanych. Jest to zasadniczo buforowanie informacji o typie i informacji mapowania typu do bazy danych w różnych połączeniach z tym samym modelem. Pamięć podręczna metadanych jest unikatowa dla domen aplikacji.

#### <a name="341-metadata-caching-algorithm"></a>3.4.1 Algorytm buforowania metadanych

1.  Informacje o metadanych dla modelu są przechowywane w ItemCollection dla każdego entityconnection.
    -   Na marginesie istnieją różne ItemCollection obiektów dla różnych części modelu. Na przykład StoreItemCollections zawiera informacje o modelu bazy danych; ObjectItemCollection zawiera informacje o modelu danych; EdmItemCollection zawiera informacje o modelu koncepcyjnym.

2.  Jeśli dwa połączenia używają tego samego ciągu połączenia, będą współużytkować to samo wystąpienie ItemCollection.
3.  Funkcjonalnie równoważne, ale tekstowo różne parametry połączenia może spowodować różne pamięci podręczne metadanych. Robimy tokenizacji ciągów połączeń, więc po prostu zmiana kolejności tokenów powinna spowodować udostępnione metadane. Ale dwa parametry połączenia, które wydają się funkcjonalnie takie same nie mogą być oceniane jako identyczne po tokenizacji.
4.  ItemCollection jest okresowo sprawdzany pod kątem użycia. Jeśli zostanie ustalone, że obszar roboczy nie został ostatnio osiągnięty, zostanie oznaczony do czyszczenia podczas następnego wyciągnięcia po ścieżce pamięci podręcznej.
5.  Samo utworzenie entityconnection spowoduje pamięć podręczną metadanych do utworzenia (chociaż kolekcje elementów w nim nie zostaną zainicjowane, dopóki połączenie nie zostanie otwarte). Ten obszar roboczy pozostanie w pamięci, dopóki algorytm buforowania nie stwierdzi, że nie jest "w użyciu".

Zespół Doradztwa Klienta napisał wpis w blogu, który opisuje trzymanie odwołania do ItemCollection w celu \< https://docs.microsoft.com/archive/blogs/appfabriccat/holding-a-reference-to-the-ef-metadataworkspace-for-wcf-services>uniknięcia "deprecation" podczas korzystania z dużych modeli: .

#### <a name="342-the-relationship-between-metadata-caching-and-query-plan-caching"></a>3.4.2 Relacja między buforowaniem metadanych a buforem planu zapytań

Wystąpienie pamięci podręcznej planu kwerendy znajduje się w itemcollection metadataWorkspace typów magazynu. Oznacza to, że polecenia magazynu w pamięci podręcznej będą używane dla zapytań względem dowolnego kontekstu wystąpienia przy użyciu danego obszaru MetadataWorkspace. Oznacza to również, że jeśli masz dwa połączenia ciągi, które są nieco inne i nie pasują do siebie po tokenizacji, będziesz miał różne wystąpienia pamięci podręcznej planu kwerendy.

### <a name="35-results-caching"></a>3.5 Buforowanie wyników

Za pomocą buforowania wyników (znanego również jako "buforowanie drugiego poziomu) wyniki zapytań są przechowywane w lokalnej pamięci podręcznej. Podczas wystawiania kwerendy, najpierw sprawdzić, czy wyniki są dostępne lokalnie przed kwerendą w sklepie. Podczas buforowania wyników nie jest bezpośrednio obsługiwane przez entity framework, jest możliwe, aby dodać pamięć podręczną drugiego poziomu przy użyciu dostawcy zawijania. Przykładowym dostawcą zawijania z pamięcią podręczną drugiego poziomu jest [pamięć podręczna drugiego poziomu Alachisoft Entity Framework oparta na NCache.](https://www.alachisoft.com/ncache/entity-framework.html)

Ta implementacja buforowania drugiego poziomu jest funkcją wstrzyknięte, która ma miejsce po linq wyrażenie zostało ocenione (i funcletized) i plan wykonywania kwerendy jest obliczana lub pobierana z pamięci podręcznej pierwszego poziomu. Pamięć podręczna drugiego poziomu będzie następnie przechowywać tylko wyniki nieprzetworzonej bazy danych, więc potok materializacji nadal jest wykonywany później.

#### <a name="351-additional-references-for-results-caching-with-the-wrapping-provider"></a>3.5.1 Dodatkowe referencje dotyczące buforowania wyników z dostawcą opakowań jednostkowych

-   Julie Lerman napisała artykuł MSDN "Drugi poziom buforowania w ramach jednostki i systemu Windows Azure", który zawiera sposób aktualizowania przykładowego dostawcy zawijania w celu korzystania z buforowania aplikacji Windows Server AppFabric:[https://msdn.microsoft.com/magazine/hh394143.aspx](https://msdn.microsoft.com/magazine/hh394143.aspx)
-   Jeśli pracujesz z Entity Framework 5, blog zespołu zawiera wpis, który opisuje, jak uzyskać działanie \< https://docs.microsoft.com/archive/blogs/adonet/ef-caching-with-jarek-kowalskis-provider>rzeczy z dostawcą buforowania dla entity framework 5: . Zawiera również szablon T4, który ułatwia dodawanie buforowania drugiego poziomu do projektu.

## <a name="4-autocompiled-queries"></a>4 Kwerendy z automatyczną kompilacją

Gdy kwerenda jest wystawiana w bazie danych przy użyciu entity framework, musi przejść przez szereg kroków przed faktycznie materializacji wyników; jednym z takich kroków jest kompilacja zapytań. Zapytania SQL jednostki były znane mają dobrą wydajność, ponieważ są one automatycznie buforowane, więc po raz drugi lub trzeci wykonać tę samą kwerendę można pominąć kompilator planu i zamiast tego użyć buforowanego planu.

Entity Framework 5 wprowadzono automatyczne buforowanie linq do jednostek kwerend, jak również. W poprzednich wersjach Entity Framework tworzenia CompiledQuery, aby przyspieszyć wydajność była powszechną praktyką, ponieważ to sprawi, że LINQ do jednostek kwerendy cacheable. Ponieważ buforowanie odbywa się teraz automatycznie bez użycia CompiledQuery, nazywamy tę funkcję "zapytaniami autokompilowanymi". Aby uzyskać więcej informacji na temat pamięci podręcznej planu kwerend i jej mechaniki, zobacz Buforowanie planu kwerend.

Entity Framework wykrywa, gdy kwerenda wymaga ponownej kompilacji i robi to, gdy kwerenda jest wywoływana, nawet jeśli została skompilowana wcześniej. Typowe warunki, które powodują ponowne skompilowane zapytanie, to:

-   Zmiana opcji scalania skojarzonej z kwerendą. Kwerenda buforowana nie będzie używana, zamiast tego kompilator planu zostanie uruchomiony ponownie, a nowo utworzony plan zostanie buforowany.
-   Zmiana wartości ContextOptions.UseCSharpNullComparisonBehavior. Otrzymujesz taki sam efekt jak zmiana opcji Scalania.

Inne warunki mogą uniemożliwić kwerendy przy użyciu pamięci podręcznej. Typowe przykłady:

-   Korzystanie z IEnumerable&lt;T&gt;. Zawiera&lt;&gt;(wartość T).
-   Korzystanie z funkcji, które generują zapytania ze stałymi.
-   Korzystanie z właściwości obiektu niezamapowane.
-   Łączenie kwerendy z inną kwerendą, która wymaga ponownej kompilacji.

### <a name="41-using-ienumerablelttgtcontainslttgtt-value"></a>4.1 Korzystanie z IEnumerable&lt;T&gt;. Zawiera&lt;&gt;wartość T (wartość T)

Entity Framework nie buforuje zapytań, które&lt;wywołują IEnumerable T&gt;. Zawiera&lt;&gt;T (T wartość) względem kolekcji w pamięci, ponieważ wartości kolekcji są uważane za volatile. Następująca przykładowa kwerenda nie będzie buforowana, więc zawsze będzie przetwarzana przez kompilator planu:

``` csharp
int[] ids = new int[10000];
...
using (var context = new MyContext())
{
    var query = context.MyEntities
                    .Where(entity => ids.Contains(entity.Id));

    var results = query.ToList();
    ...
}
```

Należy zauważyć, że rozmiar IEnumerable względem którego zawiera jest wykonywany określa, jak szybko lub jak wolno kwerendy jest kompilowany. Wydajność może znacznie ucierpieć podczas korzystania z dużych kolekcji, takich jak pokazano w powyższym przykładzie.

Entity Framework 6 zawiera optymalizacje sposobu IEnumerable&lt;T&gt;. Zawiera&lt;&gt;wartość T (T) działa podczas wykonywania kwerend. Kod SQL, który jest generowany jest znacznie szybsze do produkcji i bardziej czytelny, a w większości przypadków również wykonuje szybciej na serwerze.

### <a name="42-using-functions-that-produce-queries-with-constants"></a>4.2 Korzystanie z funkcji, które generują zapytania ze stałymi

Operatory SKIP(), Take(), Contains() i DefautIfEmpty() LINQ nie generują zapytań SQL z parametrami, lecz umieszczają wartości przekazywane do nich jako stałe. Z tego powodu kwerendy, które w przeciwnym razie mogą być identyczne, zanieczyszczają pamięć podręczną planu kwerend, zarówno na stosie EF, jak i na serwerze bazy danych i nie są ponownie przyliczane, chyba że te same stałe są używane w kolejnym wykonaniu kwerendy. Przykład:

``` csharp
var id = 10;
...
using (var context = new MyContext())
{
    var query = context.MyEntities.Select(entity => entity.Id).Contains(id);

    var results = query.ToList();
    ...
}
```

W tym przykładzie za każdym razem, gdy ta kwerenda jest wykonywana z inną wartością dla identyfikatora, kwerenda zostanie skompilowana do nowego planu.

W szczególności należy zwrócić uwagę na korzystanie z Skip i Take podczas wykonywania stronicowania. W EF6 te metody mają przeciążenie lambda, które skutecznie sprawia, że buforowany plan kwerendy wielokrotnego pożytek, ponieważ EF można przechwycić zmienne przekazywane do tych metod i przetłumaczyć je do SQLparameters. Pomaga to również zachować czystsze pamięci podręcznej, ponieważ w przeciwnym razie każde zapytanie z inną stałą dla Skip i Take otrzyma własny wpis pamięci podręcznej planu kwerend.

Należy wziąć pod uwagę następujący kod, który jest nieoptymalny, ale jest przeznaczony tylko do przykładu tej klasy zapytań:

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i < count; ++i)
{
    var currentCustomer = customers.Skip(i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

Szybsza wersja tego samego kodu wymagałaby wywołania Skip z lambda:

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i < count; ++i)
{
    var currentCustomer = customers.Skip(() => i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

Drugi fragment kodu może działać do 11% szybciej, ponieważ ten sam plan kwerendy jest używany za każdym razem, gdy kwerenda jest uruchamiana, co oszczędza czas procesora CPU i pozwala uniknąć zanieczyszczania pamięci podręcznej zapytań. Ponadto, ponieważ parametr skip znajduje się w zamknięciu kod może równie dobrze wyglądać teraz:

``` csharp
var i = 0;
var skippyCustomers = context.Customers.OrderBy(c => c.LastName).Skip(() => i);
for (; i < count; ++i)
{
    var currentCustomer = skippyCustomers.FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

### <a name="43-using-the-properties-of-a-non-mapped-object"></a>4.3 Korzystanie z właściwości obiektu niezamapowanego

Gdy kwerenda używa właściwości typu obiektu niemapowanego jako parametru, kwerenda nie zostanie buforowana. Przykład:

``` csharp
using (var context = new MyContext())
{
    var myObject = new NonMappedType();

    var query = from entity in context.MyEntities
                where entity.Name.StartsWith(myObject.MyProperty)
                select entity;

   var results = query.ToList();
    ...
}
```

W tym przykładzie załóżmy, że klasa NonMappedType nie jest częścią modelu jednostki. Tę kwerendę można łatwo zmienić, aby nie używać typu niemapowane i zamiast tego użyć zmiennej lokalnej jako parametru do kwerendy:

``` csharp
using (var context = new MyContext())
{
    var myObject = new NonMappedType();
    var myValue = myObject.MyProperty;
    var query = from entity in context.MyEntities
                where entity.Name.StartsWith(myValue)
                select entity;

    var results = query.ToList();
    ...
}
```

W takim przypadku kwerenda będzie mogła uzyskać buforowane i będzie korzystać z pamięci podręcznej planu kwerendy.

### <a name="44-linking-to-queries-that-require-recompiling"></a>4.4 Łączenie z zapytaniami wymagającymi ponownego komppilowania

Zgodnie z tym samym przykładem, jak powyżej, jeśli masz drugie zapytanie, które opiera się na kwerendzie, która musi zostać ponownie skompilowana, całe drugie zapytanie również zostanie ponownie skompilowane. Oto przykład ilustrowania tego scenariusza:

``` csharp
int[] ids = new int[10000];
...
using (var context = new MyContext())
{
    var firstQuery = from entity in context.MyEntities
                        where ids.Contains(entity.Id)
                        select entity;

    var secondQuery = from entity in context.MyEntities
                        where firstQuery.Any(otherEntity => otherEntity.Id == entity.Id)
                        select entity;

    var results = secondQuery.ToList();
    ...
}
```

Przykład jest ogólny, ale ilustruje, jak łączenie z firstQuery powoduje secondQuery nie być w stanie uzyskać buforowane. Jeśli firstQuery nie było kwerendą, która wymaga ponownego kompelowania, następnie secondQuery byłby buforowany.

## <a name="5-notracking-queries"></a>5 Zapytania notracking

### <a name="51-disabling-change-tracking-to-reduce-state-management-overhead"></a>5.1 Wyłączenie śledzenia zmian w celu zmniejszenia kosztów zarządzania stanem

Jeśli jesteś w scenariuszu tylko do odczytu i chcesz uniknąć narzutu ładowania obiektów do ObjectStateManager, można wystawić kwerendy "Brak śledzenia".Śledzenie zmian można wyłączyć na poziomie zapytania.

Należy jednak pamiętać, że wyłączając śledzenie zmian skutecznie wyłączasz pamięć podręczną obiektów. Podczas kwerendy dla jednostki, nie możemy pominąć materializacji, pobierając wcześniej zmaterializowane wyniki kwerendy z ObjectStateManager. Jeśli są wielokrotnie kwerendy dla tych samych jednostek w tym samym kontekście, może rzeczywiście zobaczyć korzyści wydajności z włączania śledzenia zmian.

Podczas wykonywania zapytań przy użyciu ObjectContext, ObjectQuery i ObjectSet wystąpień zapamięta MergeOption raz jest ustawiony, a kwerendy, które są na nich składa odziedziczy skuteczne MergeOption kwerendy nadrzędnej. Podczas korzystania z DbContext, śledzenie można wyłączyć, wywołując AsNoTracking() modyfikator na DbSet.

#### <a name="511-disabling-change-tracking-for-a-query-when-using-dbcontext"></a>5.1.1 Wyłączanie śledzenia zmian dla kwerendy podczas korzystania z DbContext

Tryb kwerendy można przełączyć na NoTracking, łącząc wywołanie metody AsNoTracking() w kwerendzie. W przeciwieństwie do ObjectQuery DbSet i DbQuery klasy w interfejsie API DbContext nie mają właściwości modyfikowalne dla MergeOption.

``` csharp
    var productsForCategory = from p in context.Products.AsNoTracking()
                                where p.Category.CategoryName == selectedCategory
                                select p;


```

#### <a name="512-disabling-change-tracking-at-the-query-level-using-objectcontext"></a>5.1.2 Wyłączanie śledzenia zmian na poziomie zapytania przy użyciu objectcontext

``` csharp
    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;

    ((ObjectQuery)productsForCategory).MergeOption = MergeOption.NoTracking;
```

#### <a name="513-disabling-change-tracking-for-an-entire-entity-set-using-objectcontext"></a>5.1.3 Wyłączanie śledzenia zmian dla całego zestawu jednostek przy użyciu objectcontext

``` csharp
    context.Products.MergeOption = MergeOption.NoTracking;

    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;
```

### <a name="52test-metrics-demonstrating-the-performance-benefit-of-notracking-queries"></a>5.2 Metryki testów demonstrujące korzyści z wydajności zapytań NoTracking

W tym teście przyjrzymy się kosztowi wypełnienia ObjectStateManager, porównując śledzenie do zapytań NoTracking dla modelu Systemu Navision. Zobacz dodatek opis modelu systemu Navision i typów zapytań, które zostały wykonane. W tym teście możemy iterować za pośrednictwem listy zapytań i wykonać każdy z nich raz. Uruchomiliśmy dwie odmiany testu, raz z zapytaniami NoTracking i raz z domyślną opcją scalania "AppendOnly". Uruchomiliśmy każdą odmianę 3 razy i przyjmujemy średnią wartość przebiegów. Między testami czyścimy pamięć podręczną zapytań na serwerze SQL Server i zmniejszamy tempdb, uruchamiając następujące polecenia:

1.  DBCC DROPCLEANBUFFERS
2.  DBCC FREEPROCCACHE
3.  DBCC SHRINKDATABASE (tempdb, 0)

Wyniki badań, mediana ponad 3 przebiegów:

|                        | BRAK ŚLEDZENIA – ZESTAW ROBOCZY | BRAK ŚLEDZENIA – CZAS | TYLKO DOŁĄCZ — ZESTAW ROBOCZY | TYLKO DOŁĄCZ — CZAS |
|:-----------------------|:--------------------------|:-------------------|:--------------------------|:-------------------|
| **Struktura jednostek 5** | 460361728                 | 1163536 ms         | 596545536                 | 1273042 ms         |
| **Entity Framework 6** | 647127040                 | 190228 ms          | 832798720                 | 195521 ms          |

Entity Framework 5 będzie miał mniejszy rozmiar pamięci na końcu przebiegu niż Entity Framework 6 nie. Dodatkowa pamięć zużywana przez entity framework 6 jest wynikiem dodatkowych struktur pamięci i kodu, które umożliwiają nowe funkcje i lepszą wydajność.

Istnieje również wyraźna różnica w pamięci footprint podczas korzystania ObjectStateManager. Entity Framework 5 zwiększył swój ślad o 30% podczas śledzenia wszystkich jednostek, które zmaterializowaliśmy się z bazy danych. Entity Framework 6 zwiększyła swój ślad o 28% w tym celu.

Pod względem czasu entity framework 6 przewyższa entity framework 5 w tym teście przez duży margines. Entity Framework 6 zakończył test w około 16% czasu zużywanego przez entity framework 5. Ponadto Entity Framework 5 zajmuje 9% więcej czasu, aby zakończyć, gdy ObjectStateManager jest używany. Dla porównania Entity Framework 6 używa 3% więcej czasu podczas korzystania ObjectStateManager.

## <a name="6-query-execution-options"></a>6 Opcje wykonywania kwerend

Entity Framework oferuje kilka różnych sposobów wykonywania zapytań. Przyjrzymy się następującym opcjom, porównamy zalety i minusy każdego z nich i zbadamy ich charakterystykę wydajności:

-   LINQ do jednostek.
-   Brak śledzenia LINQ do jednostek.
-   Entity SQL przez ObjectQuery.
-   Entity SQL za pośrednictwem entityCommand.
-   ExecuteStoreQuery.
-   SqlQuery.
-   CompiledQuery.

### <a name="61-linq-to-entities-queries"></a>6.1 LINQ do jednostek kwerendy

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

**Zalety**

-   Nadaje się do operacji CUD.
-   W pełni zmaterializowane obiekty.
-   Najprostsze do zapisu ze składnią wbudowaną w język programowania.
-   Dobra wydajność.

**Wady**

-   Pewne ograniczenia techniczne, takie jak:
    -   Wzorce przy użyciu DefaultIfEmpty dla kwerend SPRZĘŻENIA ZEWNĘTRZNEGO spowodować bardziej złożone kwerendy niż proste zewnętrzne join instrukcji w entity SQL.
    -   Nadal nie można używać LIKE z ogólnego dopasowania wzorca.

### <a name="62-no-tracking-linq-to-entities-queries"></a>6.2 Brak śledzenia LINQ do zapytań jednostek

Gdy kontekst wywodzi ObjectContext:

``` csharp
context.Products.MergeOption = MergeOption.NoTracking;
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

Gdy kontekst wywodzi DbContext:

``` csharp
var q = context.Products.AsNoTracking()
                        .Where(p => p.Category.CategoryName == "Beverages");
```

**Zalety**

-   Zwiększona wydajność w stosunku do zwykłych zapytań LINQ.
-   W pełni zmaterializowane obiekty.
-   Najprostsze do zapisu ze składnią wbudowaną w język programowania.

**Wady**

-   Nie nadaje się do operacji CUD.
-   Pewne ograniczenia techniczne, takie jak:
    -   Wzorce przy użyciu DefaultIfEmpty dla kwerend SPRZĘŻENIA ZEWNĘTRZNEGO spowodować bardziej złożone kwerendy niż proste zewnętrzne join instrukcji w entity SQL.
    -   Nadal nie można używać LIKE z ogólnego dopasowania wzorca.

Należy zauważyć, że kwerendy, które projekt właściwości skalarne nie są śledzone, nawet jeśli NoTracking nie jest określony. Przykład:

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages").Select(p => new { p.ProductName });
```

Ta określona kwerenda nie jawnie określić, że NoTracking, ale ponieważ nie materializing typu, który jest znany menedżerowi stanu obiektu następnie zmaterializowany wynik nie jest śledzony.

### <a name="63-entity-sql-over-an-objectquery"></a>6.3 Entity SQL za pośrednictwem zapytania obiektów

``` csharp
ObjectQuery<Product> products = context.Products.Where("it.Category.CategoryName = 'Beverages'");
```

**Zalety**

-   Nadaje się do operacji CUD.
-   W pełni zmaterializowane obiekty.
-   Obsługuje buforowanie planu kwerend.

**Wady**

-   Obejmuje tekstowych ciągów zapytań, które są bardziej podatne na błąd użytkownika niż konstrukcje zapytań wbudowane w język.

### <a name="64-entity-sql-over-an-entity-command"></a>6.4 Sql jednostki za pośrednictwem polecenia jednostki

``` csharp
EntityCommand cmd = eConn.CreateCommand();
cmd.CommandText = "Select p From NorthwindEntities.Products As p Where p.Category.CategoryName = 'Beverages'";

using (EntityDataReader reader = cmd.ExecuteReader(CommandBehavior.SequentialAccess))
{
    while (reader.Read())
    {
        // manually 'materialize' the product
    }
}
```

**Zalety**

-   Obsługuje buforowanie planu kwerend w programie .NET 4.0 (buforowanie planu jest obsługiwane przez wszystkie inne typy zapytań w programie .NET 4.5).

**Wady**

-   Obejmuje tekstowych ciągów zapytań, które są bardziej podatne na błąd użytkownika niż konstrukcje zapytań wbudowane w język.
-   Nie nadaje się do operacji CUD.
-   Wyniki nie są automatycznie materializowane i muszą być odczytywane z czytnika danych.

### <a name="65-sqlquery-and-executestorequery"></a>6.5 Zapytania sqlquery i executestorequery

SqlQuery w bazie danych:

``` csharp
// use this to obtain entities and not track them
var q1 = context.Database.SqlQuery<Product>("select * from products");
```

SqlQuery na DbSet:

``` csharp
// use this to obtain entities and have them tracked
var q2 = context.Products.SqlQuery("select * from products");
```

ExecyteStoreQuery:

``` csharp
var beverages = context.ExecuteStoreQuery<Product>(
@"     SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued, P.DiscontinuedDate
       FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
       WHERE        (C.CategoryName = 'Beverages')"
);
```

**Zalety**

-   Ogólnie najszybsza wydajność, ponieważ kompilator planu jest pomijany.
-   W pełni zmaterializowane obiekty.
-   Nadaje się do operacji CUD, gdy jest używany z zestawu DbSet.

**Wady**

-   Zapytanie jest tekstowe i podatne na błędy.
-   Kwerenda jest powiązana z określonym zapleczem przy użyciu semantyki magazynu zamiast semantyki koncepcyjnej.
-   Gdy dziedziczenie jest obecny, ręcznie kwerendy musi uwzględnić warunki mapowania dla żądanego typu.

### <a name="66-compiledquery"></a>6.6 CompiledQuery

``` csharp
private static readonly Func<NorthwindEntities, string, IQueryable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
    (NorthwindEntities context, string categoryName) =>
        context.Products.Where(p => p.Category.CategoryName == categoryName)
        );
…
var q = context.InvokeProductsForCategoryCQ("Beverages");
```

**Zalety**

-   Zapewnia do 7% poprawę wydajności w porównaniu do zwykłych zapytań LINQ.
-   W pełni zmaterializowane obiekty.
-   Nadaje się do operacji CUD.

**Wady**

-   Zwiększona złożoność i obciążenie programistyczne.
-   Poprawa wydajności jest tracona podczas tworzenia na szczycie skompilowanego zapytania.
-   Niektóre zapytania LINQ nie mogą być zapisywane jako CompiledQuery — na przykład projekcje typów anonimowych.

### <a name="67-performance-comparison-of-different-query-options"></a>6.7 Porównanie wydajności różnych opcji zapytań

Proste mikrobenchmarki, w których tworzenie kontekstu nie zostało wystawione, zostały wystawione na próbę. Zmierzyliśmy kwerendy 5000 razy dla zestawu jednostek nie buforowanych w kontrolowanym środowisku. Liczby te należy traktować z ostrzeżeniem: nie odzwierciedlają rzeczywistych liczb produkowanych przez aplikację, ale zamiast tego są bardzo dokładnym pomiarem, ile różnicy w wydajności występuje, gdy różne opcje zapytań są porównywane z jabłkami do jabłek, z wyłączeniem kosztów utworzenia nowego kontekstu.

| Ef  | Testowanie                                 | Czas (ms) | Memory (Pamięć)   |
|:----|:-------------------------------------|:----------|:---------|
| EF5 | ObiektContext ESQL                   | 2414      | 38801408 |
| EF5 | Zapytanie Linq ocontexta obiektu             | 2692      | 38277120 |
| EF5 | DbContext Linq Kwerenda nie śledzenie     | 2818      | 41840640 |
| EF5 | Kwerenda Linq DbContext                 | 2930      | 41771008 |
| EF5 | ObjectContext Linq Kwerenda bez śledzenia | 3013      | 38412288 |
|     |                                      |           |          |
| EF6 | ObiektContext ESQL                   | 2059      | 46039040 |
| EF6 | Zapytanie Linq ocontexta obiektu             | 3074      | 45248512 |
| EF6 | DbContext Linq Kwerenda nie śledzenie     | 3125      | 47575040 |
| EF6 | Kwerenda Linq DbContext                 | 3420      | 47652864 |
| EF6 | ObjectContext Linq Kwerenda bez śledzenia | 3593      | 45260800 |

![Mikroprocesory EF5, 5000 ciepłych iteracji](~/ef6/media/ef5micro5000warm.png)

![Mikroprocesory EF6, 5000 ciepłych iteracji](~/ef6/media/ef6micro5000warm.png)

Microbenchmarks są bardzo wrażliwe na małe zmiany w kodzie. W tym przypadku różnica między kosztami entity Framework 5 i Entity Framework 6 wynika z dodania [przechwycenia](~/ef6/fundamentals/logging-and-interception.md) i [ulepszeń transakcyjnych.](~/ef6/saving/transactions.md) Te liczby microbenchmarks, jednak są wzmocnione wizji w bardzo mały fragment tego, co Entity Framework nie. Rzeczywistych scenariuszy ciepłych zapytań nie powinny widzieć regresji wydajności podczas uaktualniania z entity framework 5 do entity framework 6.

Aby porównać rzeczywistą wydajność różnych opcji zapytań, utworzyliśmy 5 oddzielnych odmian testowych, w których używamy innej opcji zapytania, aby wybrać wszystkie produkty, których nazwa kategorii to "Napoje". Każda iteracja zawiera koszt utworzenia kontekstu i koszt materializacji wszystkich zwróconych jednostek. 10 iteracji są uruchamiane bezterminowe przed podjęciem sumy 1000 iteracji czasowych. Przedstawione wyniki to mediana przebiegu pobrana z 5 przebiegów każdego testu. Aby uzyskać więcej informacji, zobacz dodatek B, który zawiera kod testu.

| Ef  | Testowanie                                        | Czas (ms) | Memory (Pamięć)   |
|:----|:--------------------------------------------|:----------|:---------|
| EF5 | Polecenie Encja ObjectContext                | 621       | 39350272 |
| EF5 | DbContext Sql Query w bazie danych             | 825       | 37519360 |
| EF5 | Zapytanie magazynu ObjectContext                   | 878       | 39460864 |
| EF5 | ObjectContext Linq Kwerenda bez śledzenia        | 969       | 38293504 |
| EF5 | ObjectContext Entity Sql przy użyciu kwerendy obiektu | 1089      | 38981632 |
| EF5 | Zapytanie skompilowane w programie ObjectContext                | 1099      | 38682624 |
| EF5 | Zapytanie Linq ocontexta obiektu                    | 1152      | 38178816 |
| EF5 | DbContext Linq Kwerenda nie śledzenie            | 1208      | 41803776 |
| EF5 | DbContext Sql Query na dbset                | 1414      | 37982208 |
| EF5 | Kwerenda Linq DbContext                        | 1574      | 41738240 |
|     |                                             |           |          |
| EF6 | Polecenie Encja ObjectContext                | 480       | 47247360 |
| EF6 | Zapytanie magazynu ObjectContext                   | 493       | 46739456 |
| EF6 | DbContext Sql Query w bazie danych             | 614       | 41607168 |
| EF6 | ObjectContext Linq Kwerenda bez śledzenia        | 684       | 46333952 |
| EF6 | ObjectContext Entity Sql przy użyciu kwerendy obiektu | 767       | 48865280 |
| EF6 | Zapytanie skompilowane w programie ObjectContext                | 788       | 48467968 |
| EF6 | DbContext Linq Kwerenda nie śledzenie            | 878       | 47554560 |
| EF6 | Zapytanie Linq ocontexta obiektu                    | 953       | 47632384 |
| EF6 | DbContext Sql Query na dbset                | 1023      | 41992192 |
| EF6 | Kwerenda Linq DbContext                        | 1290      | 47529984 |


![EF5 ciepłe zapytanie 1000 iteracji](~/ef6/media/ef5warmquery1000.png)

![EF6 ciepłe zapytanie 1000 iteracji](~/ef6/media/ef6warmquery1000.png)

> [!NOTE]
> Aby uzyskać kompletność, uwzględniliśmy odmianę, w której wykonujemy kwerendę SQL jednostki na entityCommand. Jednak ponieważ wyniki nie są zmaterializowane dla takich zapytań, porównanie nie jest koniecznie jabłka do jabłek. Test zawiera bliskie przybliżenie do materializacji, aby spróbować uczynić porównanie bardziej sprawiedliwym.

W tym przypadku end-to-end Entity Framework 6 przewyższa Entity Framework 5 ze względu na poprawę wydajności na kilka części stosu, w&lt;&gt; tym znacznie lżejsze inicjowanie DbContext i szybsze wyszukiwania MetadataCollection T.

## <a name="7-design-time-performance-considerations"></a>7 Zagadnienia dotyczące wydajności czasu projektowania

### <a name="71-inheritance-strategies"></a>7.1 Strategie dziedziczenia

Inną kwestią wydajności podczas korzystania z entity framework jest strategia dziedziczenia, którego używasz. Entity Framework obsługuje 3 podstawowe typy dziedziczenia i ich kombinacje:

-   Tabela według hierarchii (TPH) — gdzie każdy zestaw dziedziczenia jest mapowany do tabeli z kolumną dyskryminującą, aby wskazać, który typ w hierarchii jest reprezentowany w wierszu.
-   Tabela na typ (TPT) — gdzie każdy typ ma własną tabelę w bazie danych; tabele podrzędne definiują tylko kolumny, których tabela nadrzędna nie zawiera.
-   Tabela na klasę (TPC) — gdzie każdy typ ma własną pełną tabelę w bazie danych; tabele podrzędne definiują wszystkie ich pola, w tym te zdefiniowane w typach nadrzędnych.

Jeśli model używa dziedziczenia TPT, kwerendy, które są generowane będą bardziej złożone niż te, które są generowane z innymi strategiami dziedziczenia, co może spowodować dłuższe czasy wykonywania w magazynie.Zazwyczaj trwa dłużej, aby wygenerować zapytania za w modelu TPT i zmaterializować wynikowe obiekty.

Zobacz wpis w blogu \< https://docs.microsoft.com/archive/blogs/adonet/performance-considerations-when-using-tpt-table-per-type-inheritance-in-the-entity-framework>MSDN "Zagadnienia dotyczące wydajności podczas korzystania z dziedziczenia TPT (tabela na typ) w ramach encji: .

#### <a name="711-avoiding-tpt-in-model-first-or-code-first-applications"></a>7.1.1 Unikanie TPT w aplikacjach Model First lub Code First

Podczas tworzenia modelu za cie za ną bazę danych, która ma schemat TPT, nie masz wielu opcji. Ale podczas tworzenia aplikacji przy użyciu modelu pierwszy lub code first, należy unikać dziedziczenia TPT dla problemów z wydajnością.

Korzystając z modelu najpierw w Kreatorze projektanta jednostek, otrzymasz TPT dla każdego dziedziczenia w modelu. Jeśli chcesz przełączyć się na strategię dziedziczenia TPH za pomocą modelu First, możesz użyć dodatku Power Pack "Power Pack generowania bazy danych projektanta jednostek" dostępnego w galerii programu Visual Studio ( \< http://visualstudiogallery.msdn.microsoft.com/df3541c3-d833-4b65-b942-989e7ec74c87/>).

Podczas korzystania z code first skonfigurować mapowanie modelu z dziedziczenia, EF będzie używać TPH domyślnie, w związku z tym wszystkie jednostki w hierarchii dziedziczenia będą mapowane do tej samej tabeli. Zobacz sekcję "Mapowanie za pomocą interfejsu API Fluent" w artykule "Code First [http://msdn.microsoft.com/magazine/hh126815.aspx](https://msdn.microsoft.com/magazine/hh126815.aspx)in Entity Framework4.1" w MSDN Magazine ( ) aby uzyskać więcej informacji.

### <a name="72-upgrading-from-ef4-to-improve-model-generation-time"></a>7.2 Aktualizacja z EF4 w celu skrócenia czasu generowania modelu

Ulepszenie specyficzne dla programu SQL Server do algorytmu, który generuje warstwę magazynu (SSDL) modelu jest dostępny w entity framework 5 i 6 i jako aktualizacja do entity framework 4 po zainstalowaniu programu Visual Studio 2010 SP1. Poniższe wyniki testów pokazują poprawę podczas generowania bardzo dużego modelu, w tym przypadku modelu systemu Navision. Zobacz dodatek C, aby uzyskać więcej informacji na ten temat.

Model zawiera 1005 zestawów jednostek i 4227 zestawów skojarzeń.

| Konfigurowanie                              | Podział czasochłonnego                                                                                                                                               |
|:-------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Visual Studio 2010, entity Framework 4     | Generacja SSDL: 2 godz. <br/> Generowanie mapowania: 1 sekunda <br/> Generowanie CSDL: 1 sekunda <br/> Generowanie warstwy obiektu: 1 sekunda <br/> Wyświetl generację: 2 h 14 min |
| Visual Studio 2010 z dodatkem SP1, entity Framework 4 | Generacja SSDL: 1 sekunda <br/> Generowanie mapowania: 1 sekunda <br/> Generowanie CSDL: 1 sekunda <br/> Generowanie warstwy obiektu: 1 sekunda <br/> Wyświetl generację: 1 godz.   |
| Visual Studio 2013, entity Framework 5     | Generacja SSDL: 1 sekunda <br/> Generowanie mapowania: 1 sekunda <br/> Generowanie CSDL: 1 sekunda <br/> Generowanie warstwy obiektu: 1 sekunda <br/> Wyświetl generację: 65 minut    |
| Visual Studio 2013, entity Framework 6     | Generacja SSDL: 1 sekunda <br/> Generowanie mapowania: 1 sekunda <br/> Generowanie CSDL: 1 sekunda <br/> Generowanie warstwy obiektu: 1 sekunda <br/> Wyświetl generację: 28 sekund.   |


Warto zauważyć, że podczas generowania SSDL, obciążenie jest prawie w całości wydane na SQL Server, podczas gdy komputer deweloperujący klienta czeka bezczynnie na wyniki wrócić z serwera. PbA powinny szczególnie docenić tę poprawę. Warto również zauważyć, że zasadniczo cały koszt generowania modelu odbywa się teraz w View Generation.

### <a name="73-splitting-large-models-with-database-first-and-model-first"></a>7.3 Dzielenie dużych modeli z bazą danych i pierwszym modelem

Wraz ze wzrostem rozmiaru modelu powierzchnia projektanta staje się zaśmiecona i trudna w użyciu. Zazwyczaj uważamy, że model z więcej niż 300 jednostek jest zbyt duży, aby skutecznie używać projektanta. W poniższym wpisie w blogu opisano \< https://docs.microsoft.com/archive/blogs/adonet/working-with-large-models-in-entity-framework-part-2>kilka opcji dzielenia dużych modeli: .

Post został napisany dla pierwszej wersji entity framework, ale kroki nadal mają zastosowanie.

### <a name="74-performance-considerations-with-the-entity-data-source-control"></a>7.4 Zagadnienia dotyczące wydajności związane z kontrolą źródła danych jednostki

Widzieliśmy przypadki w wielowątkowych testów wydajności i warunków skrajnych, w których wydajność aplikacji sieci web przy użyciu EntityDataSource Control znacznie się pogarsza. Podstawową przyczyną jest to, że EntityDataSource wielokrotnie wywołuje MetadataWorkspace.LoadFromAssembly w zestawach, do których odwołuje się aplikacja sieci Web, aby odnajdować typy, które mają być używane jako jednostki.

Rozwiązaniem jest ustawienie ContextTypeName entityDataSource na nazwę typu pochodnej ObjectContext klasy. Spowoduje to wyłączenie mechanizmu, który skanuje wszystkie zestawy, do których istnieje odwołanie, w poszukiwaniu typów encji.

Ustawienie ContextTypeName pole zapobiega również problem funkcjonalny, gdzie EntityDataSource w .NET 4.0 zgłasza ReflectionTypeLoadException, gdy nie można załadować typu z zestawu za pomocą odbicia. Ten problem został rozwiązany w .NET 4.5.

### <a name="75-poco-entities-and-change-tracking-proxies"></a>7.5 Podmioty POCO i serwery proxy śledzenia zmian

Entity Framework umożliwia korzystanie z niestandardowych klas danych wraz z modelu danych bez wprowadzania żadnych zmian do samych klas danych. Oznacza to, że można użyć "zwykły stary" obiektów CLR (POCO), takich jak istniejące obiekty domeny, z modelu danych. Te klasy danych POCO (znane również jako obiekty ignorujące trwałość), które są mapowane na jednostki zdefiniowane w modelu danych, obsługują większość tych samych zachowań kwerendy, wstawiania, aktualizacji i usuwania jako typy jednostek, które są generowane przez narzędzia modelu danych jednostki.

Entity Framework można również utworzyć klasy proxy pochodzące z typów POCO, które są używane, gdy chcesz włączyć funkcje, takie jak ładowanie z opóźnieniem i automatyczne śledzenie zmian na jednostkach POCO. Klasy POCO muszą spełniać określone wymagania, aby umożliwić platformie Entity [http://msdn.microsoft.com/library/dd468057.aspx](https://msdn.microsoft.com/library/dd468057.aspx)Framework używanie serwerów proxy, zgodnie z opisem w tym miejscu: .

Serwery proxy śledzenia szansy powiadomi menedżera stanu obiektu za każdym razem, gdy jakakolwiek z właściwości jednostek ma swoją wartość zmienioną, więc entity framework zna rzeczywisty stan jednostek przez cały czas. Odbywa się to przez dodanie zdarzeń powiadomień do treści metody ustawiacza właściwości i o menedżera stanu obiektu przetwarzania takich zdarzeń. Należy zauważyć, że tworzenie jednostki serwera proxy będzie zazwyczaj droższe niż tworzenie jednostki POCO bez serwera proxy ze względu na dodatkowy zestaw zdarzeń utworzonych przez entity framework.

Gdy jednostka POCO nie ma serwera proxy śledzenia zmian, zmiany są znajdowanie przez porównanie zawartości encji z kopią poprzedniego zapisanego stanu. To głębokie porównanie stanie się procesem długotrwałym, gdy masz wiele jednostek w kontekście lub gdy jednostki mają bardzo dużą ilość właściwości, nawet jeśli żadna z nich nie zmieniła się od czasu ostatniego porównania.

Podsumowując: zapłacisz za trafienie wydajności podczas tworzenia serwera proxy śledzenia zmian, ale śledzenie zmian pomoże przyspieszyć proces wykrywania zmian, gdy jednostki mają wiele właściwości lub gdy masz wiele jednostek w modelu. Dla jednostek z niewielką liczbą właściwości, gdzie ilość jednostek nie rośnie zbyt wiele, o zmiany śledzenia serwerów proxy może nie być bardzo korzystne.

## <a name="8-loading-related-entities"></a>8 Ładowanie powiązanych jednostek

### <a name="81-lazy-loading-vs-eager-loading"></a>8.1 Z opóźnieniem ładowanie vs. Zagosc.

Entity Framework oferuje kilka różnych sposobów ładowania jednostek, które są powiązane z jednostką docelową. Na przykład podczas kwerendy dla produktów, istnieją różne sposoby, że powiązane zamówienia zostaną załadowane do Menedżera stanu obiektu. Z punktu widzenia wydajności największym pytaniem do rozważenia podczas ładowania powiązanych jednostek będzie użycie ładowania z opóźnieniem lub ładowanie chętni.

Podczas korzystania z funkcji Wczytywanie eager, powiązane jednostki są ładowane wraz z zestawem encji docelowej. Instrukcja Include w kwerendzie służy do wskazywania powiązanych encji, które chcesz wprowadzić.

Podczas korzystania z opóźnieniem ładowania, początkowe zapytanie przynosi tylko w zestawie jednostek docelowych. Ale za każdym razem, gdy uzyskujesz dostęp do właściwości nawigacji, inna kwerenda jest wystawiana dla magazynu, aby załadować jednostkę pokrewną.

Po załadowaniu jednostki wszelkie dalsze zapytania dla jednostki załadują ją bezpośrednio z Menedżera stanu obiektu, niezależnie od tego, czy używasz ładowania z opóźnieniem, czy też jest to wczesne ładowanie.

### <a name="82-how-to-choose-between-lazy-loading-and-eager-loading"></a>8.2 Jak wybrać między leniwym ładowaniem a załadunkiem

Ważną rzeczą jest to, że rozumiesz różnicę między ładowaniem z opóźnieniem i zapałem ładowania, dzięki czemu można dokonać właściwego wyboru dla aplikacji. Pomoże to ocenić kompromis między wieloma żądaniami w bazie danych w porównaniu z pojedynczym żądaniem, które może zawierać duży ładunek. Może być właściwe użycie zapału ładowania w niektórych częściach aplikacji i powolne ładowanie w innych częściach.

Jako przykład tego, co dzieje się pod maską, załóżmy, że chcesz zapytać klientów, którzy mieszkają w Wielkiej Brytanii i ich liczba zamówień.

**Korzystanie z zaaskliwajnia**

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    var ukCustomers = context.Customers.Include(c => c.Orders).Where(c => c.Address.Country == "UK");
    var chosenCustomer = AskUserToPickCustomer(ukCustomers);
    Console.WriteLine("Customer Id: {0} has {1} orders", customer.CustomerID, customer.Orders.Count);
}
```

**Korzystanie z z opóźnieniem ładowania**

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    context.ContextOptions.LazyLoadingEnabled = true;

    //Notice that the Include method call is missing in the query
    var ukCustomers = context.Customers.Where(c => c.Address.Country == "UK");

    var chosenCustomer = AskUserToPickCustomer(ukCustomers);
    Console.WriteLine("Customer Id: {0} has {1} orders", customer.CustomerID, customer.Orders.Count);
}
```

Podczas korzystania z wczytywania z dużym zadowoleniem, wystawisz pojedynczą kwerendę, która zwraca wszystkich klientów i wszystkie zamówienia. Polecenie sklepu wygląda następująco:

``` SQL
SELECT
[Project1].[C1] AS [C1],
[Project1].[CustomerID] AS [CustomerID],
[Project1].[CompanyName] AS [CompanyName],
[Project1].[ContactName] AS [ContactName],
[Project1].[ContactTitle] AS [ContactTitle],
[Project1].[Address] AS [Address],
[Project1].[City] AS [City],
[Project1].[Region] AS [Region],
[Project1].[PostalCode] AS [PostalCode],
[Project1].[Country] AS [Country],
[Project1].[Phone] AS [Phone],
[Project1].[Fax] AS [Fax],
[Project1].[C2] AS [C2],
[Project1].[OrderID] AS [OrderID],
[Project1].[CustomerID1] AS [CustomerID1],
[Project1].[EmployeeID] AS [EmployeeID],
[Project1].[OrderDate] AS [OrderDate],
[Project1].[RequiredDate] AS [RequiredDate],
[Project1].[ShippedDate] AS [ShippedDate],
[Project1].[ShipVia] AS [ShipVia],
[Project1].[Freight] AS [Freight],
[Project1].[ShipName] AS [ShipName],
[Project1].[ShipAddress] AS [ShipAddress],
[Project1].[ShipCity] AS [ShipCity],
[Project1].[ShipRegion] AS [ShipRegion],
[Project1].[ShipPostalCode] AS [ShipPostalCode],
[Project1].[ShipCountry] AS [ShipCountry]
FROM ( SELECT
      [Extent1].[CustomerID] AS [CustomerID],
       [Extent1].[CompanyName] AS [CompanyName],
       [Extent1].[ContactName] AS [ContactName],
       [Extent1].[ContactTitle] AS [ContactTitle],
       [Extent1].[Address] AS [Address],
       [Extent1].[City] AS [City],
       [Extent1].[Region] AS [Region],
       [Extent1].[PostalCode] AS [PostalCode],
       [Extent1].[Country] AS [Country],
       [Extent1].[Phone] AS [Phone],
       [Extent1].[Fax] AS [Fax],
      1 AS [C1],
       [Extent2].[OrderID] AS [OrderID],
       [Extent2].[CustomerID] AS [CustomerID1],
       [Extent2].[EmployeeID] AS [EmployeeID],
       [Extent2].[OrderDate] AS [OrderDate],
       [Extent2].[RequiredDate] AS [RequiredDate],
       [Extent2].[ShippedDate] AS [ShippedDate],
       [Extent2].[ShipVia] AS [ShipVia],
       [Extent2].[Freight] AS [Freight],
       [Extent2].[ShipName] AS [ShipName],
       [Extent2].[ShipAddress] AS [ShipAddress],
       [Extent2].[ShipCity] AS [ShipCity],
       [Extent2].[ShipRegion] AS [ShipRegion],
       [Extent2].[ShipPostalCode] AS [ShipPostalCode],
       [Extent2].[ShipCountry] AS [ShipCountry],
      CASE WHEN ([Extent2].[OrderID] IS NULL) THEN CAST(NULL AS int) ELSE 1 END AS [C2]
      FROM  [dbo].[Customers] AS [Extent1]
      LEFT OUTER JOIN [dbo].[Orders] AS [Extent2] ON [Extent1].[CustomerID] = [Extent2].[CustomerID]
      WHERE N'UK' = [Extent1].[Country]
)  AS [Project1]
ORDER BY [Project1].[CustomerID] ASC, [Project1].[C2] ASC
```

Podczas korzystania z ładowania z opóźnieniem, najpierw wydasz następującą kwerendę:

``` SQL
SELECT
[Extent1].[CustomerID] AS [CustomerID],
[Extent1].[CompanyName] AS [CompanyName],
[Extent1].[ContactName] AS [ContactName],
[Extent1].[ContactTitle] AS [ContactTitle],
[Extent1].[Address] AS [Address],
[Extent1].[City] AS [City],
[Extent1].[Region] AS [Region],
[Extent1].[PostalCode] AS [PostalCode],
[Extent1].[Country] AS [Country],
[Extent1].[Phone] AS [Phone],
[Extent1].[Fax] AS [Fax]
FROM [dbo].[Customers] AS [Extent1]
WHERE N'UK' = [Extent1].[Country]
```

I za każdym razem, gdy uzyskujesz dostęp do właściwości nawigacji Zamówienia klienta, w sklepie jest wydawane kolejne zapytanie, podobne do następujących czynności:

``` SQL
exec sp_executesql N'SELECT
[Extent1].[OrderID] AS [OrderID],
[Extent1].[CustomerID] AS [CustomerID],
[Extent1].[EmployeeID] AS [EmployeeID],
[Extent1].[OrderDate] AS [OrderDate],
[Extent1].[RequiredDate] AS [RequiredDate],
[Extent1].[ShippedDate] AS [ShippedDate],
[Extent1].[ShipVia] AS [ShipVia],
[Extent1].[Freight] AS [Freight],
[Extent1].[ShipName] AS [ShipName],
[Extent1].[ShipAddress] AS [ShipAddress],
[Extent1].[ShipCity] AS [ShipCity],
[Extent1].[ShipRegion] AS [ShipRegion],
[Extent1].[ShipPostalCode] AS [ShipPostalCode],
[Extent1].[ShipCountry] AS [ShipCountry]
FROM [dbo].[Orders] AS [Extent1]
WHERE [Extent1].[CustomerID] = @EntityKeyValue1',N'@EntityKeyValue1 nchar(5)',@EntityKeyValue1=N'AROUT'
```

Aby uzyskać więcej informacji, zobacz [ładowanie powiązanych obiektów](https://msdn.microsoft.com/library/bb896272.aspx).

#### <a name="821-lazy-loading-versus-eager-loading-cheat-sheet"></a>8.2.1 Lazy Loading kontra Eager Loading ściągawki

Nie ma czegoś takiego jak jeden rozmiar dla wszystkich do wyboru gorliwy ładowania w porównaniu do ładowania leniwy. Spróbuj najpierw zrozumieć różnice między obiema strategiami, dzięki czemu można zrobić dobrze świadomą decyzję; należy również rozważyć, czy kod pasuje do dowolnego z następujących scenariuszy:

| Scenariusz                                                                    | Nasza sugestia                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
|:----------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Czy trzeba uzyskać dostęp do wielu właściwości nawigacji z pobranych jednostek? | **Nie** - Obie opcje prawdopodobnie zrobią. Jednak jeśli ładunek kwerendy przynosi nie jest zbyt duży, może wystąpić korzyści wydajności przy użyciu eager ładowania, ponieważ będzie wymagać mniej sieci rund, aby zmaterializować swoje obiekty. <br/> <br/> **Tak** — Jeśli chcesz uzyskać dostęp do wielu właściwości nawigacji z jednostek, można to zrobić przy użyciu wielu instrukcji include w zapytaniu z wczytywanie eager. Im więcej jednostek zostanie uwzględnione, tym większa ładunek, który zwróci zapytanie. Po dołączeniu trzech lub więcej jednostek do kwerendy, należy rozważyć przełączenie do ładowania z opóźnieniem. |
| Czy wiesz dokładnie, jakie dane będą potrzebne w czasie wykonywania?                   | **Nie** - Leniwy ładowanie będzie dla Ciebie lepsze. W przeciwnym razie może skończyć się kwerendy dla danych, które nie będą potrzebne. <br/> <br/> **Tak** - Chętne ładowanie jest chyba najlepszym rozwiązaniem ; pomoże to szybciej ładować całe zestawy. Jeśli zapytanie wymaga pobierania bardzo dużej ilości danych, a to staje się zbyt powolne, spróbuj leniwe ładowanie zamiast tego.                                                                                                                                                                                                                                                       |
| Czy kod jest wykonywany z dala od bazy danych? (zwiększone opóźnienie sieci)  | **Nie** — gdy opóźnienie sieci nie jest problemem, przy użyciu ładowania z opóźnieniem może uprościć kod. Należy pamiętać, że topologia aplikacji może ulec zmianie, więc nie należy przyjmować bliskości bazy danych za pewnik. <br/> <br/> **Tak** — gdy sieć jest problemem, tylko ty możesz zdecydować, co pasuje lepiej do twojego scenariusza. Zazwyczaj wczesne ładowanie będzie lepsze, ponieważ wymaga mniejszej liczby rund.                                                                                                                                                                                                      |


#### <a name="822-performance-concerns-with-multiple-includes"></a>8.2.2 Problemy z wydajnością z wieloma

Gdy słyszymy pytania dotyczące wydajności, które dotyczą problemów z czasem odpowiedzi serwera, źródłem problemu są często zapytania z wieloma instrukcjami Include. Chociaż włączenie powiązanych jednostek w kwerendzie jest zaawansowane, ważne jest, aby zrozumieć, co dzieje się w ramach okładek.

Zajmuje stosunkowo dużo czasu dla kwerendy z wielu Include instrukcji w nim, aby przejść przez nasz kompilator planu wewnętrznego do produkcji polecenia magazynu. Większość tego czasu spędzana próbuje zoptymalizować wynikowe zapytanie. Wygenerowane polecenie magazynu będzie zawierać sprzężenie zewnętrzne lub Union dla każdego Include, w zależności od mapowania. Zapytania takie jak ten przyniesie w dużych połączonych wykresów z bazy danych w jednym ładunku, który będzie acerbate wszelkie problemy z przepustowością, zwłaszcza, gdy istnieje wiele nadmiarowości w ładunku (na przykład, gdy wiele poziomów Include są używane do przechodzenia skojarzeń w kierunku jeden do wielu).

Można sprawdzić przypadki, w których kwerendy zwracają zbyt duże ładunki, uzyskując dostęp do podstawowej tsql dla kwerendy za pomocą ToTraceString i wykonywania polecenia magazynu w programie SQL Server Management Studio, aby zobaczyć rozmiar ładunku. W takich przypadkach można spróbować zmniejszyć liczbę Include instrukcji w zapytaniu, aby po prostu przynieść w danych, których potrzebujesz. Lub może być w stanie podzielić zapytanie na mniejszą sekwencję podquerii, na przykład:

**Przed przerwaniem kwerendy:**

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    var customers = from c in context.Customers.Include(c => c.Orders)
                    where c.LastName.StartsWith(lastNameParameter)
                    select c;

    foreach (Customer customer in customers)
    {
        ...
    }
}
```

**Po zerwaniu kwerendy:**

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    var orders = from o in context.Orders
                 where o.Customer.LastName.StartsWith(lastNameParameter)
                 select o;

    orders.Load();

    var customers = from c in context.Customers
                    where c.LastName.StartsWith(lastNameParameter)
                    select c;

    foreach (Customer customer in customers)
    {
        ...
    }
}
```

Będzie to działać tylko w przypadku śledzonych zapytań, ponieważ korzystamy z możliwości, którą kontekst musi automatycznie wykonywać rozpoznawanie tożsamości i rozwiązywanie skojarzeń.

Podobnie jak w przypadku ładowania z opóźnieniem, kompromis będzie więcej zapytań dla mniejszych ładunków. Można również użyć projekcji poszczególnych właściwości, aby jawnie wybrać tylko dane potrzebne z każdej jednostki, ale nie będzie ładowanie jednostek w tym przypadku i aktualizacje nie będą obsługiwane.

#### <a name="823-workaround-to-get-lazy-loading-of-properties"></a>8.2.3 Obejście, aby uzyskać leniwe ładowanie właściwości

Entity Framework obecnie nie obsługuje powolne ładowanie właściwości skalarnych lub złożonych. Jednak w przypadkach, gdy masz tabelę, która zawiera duży obiekt, taki jak blob, można użyć podziału tabeli, aby oddzielić duże właściwości na oddzielną jednostkę. Załóżmy na przykład, że masz tabelę Produkt, która zawiera kolumnę zdjęć varbinary. Jeśli nie często trzeba uzyskać dostęp do tej właściwości w kwerendach, można użyć dzielenia tabel, aby przynieść tylko części jednostki, które zwykle potrzebne. Encja reprezentująca zdjęcie produktu zostanie załadowana tylko wtedy, gdy jest wyraźnie potrzebna.

Dobrym zasobem, który pokazuje, jak włączyć dzielenie tabeli jest gil Fink \< http://blogs.microsoft.co.il/blogs/gilf/archive/2009/10/13/table-splitting-in-entity-framework.aspx>"Dzielenie tabel w entity framework" blogu: .

## <a name="9-other-considerations"></a>9 Inne rozważania

### <a name="91-server-garbage-collection"></a>9.1 Wyrzucanie elementów bezużytecznych serwera

Niektórzy użytkownicy mogą wystąpić rywalizacji zasobów, która ogranicza równoległości oczekują, gdy moduł zbierający elementy bezużyteczne nie jest poprawnie skonfigurowany. Ilekroć EF jest używany w scenariuszu wielowątkowym lub w dowolnej aplikacji, która przypomina system po stronie serwera, upewnij się, aby włączyć wyrzucanie elementów bezużytecznych serwera. Odbywa się to za pomocą prostego ustawienia w pliku konfiguracyjnym aplikacji:

``` xml
<?xmlversion="1.0" encoding="utf-8" ?>
<configuration>
        <runtime>
               <gcServer enabled="true" />
        </runtime>
</configuration>
```

Powinno to zmniejszyć rywalizację wątku i zwiększyć przepływność nawet o 30% w scenariuszach nasyconych procesorem CPU. Ogólnie rzecz biorąc należy zawsze przetestować, jak aplikacja zachowuje się przy użyciu klasycznego wyrzucania elementów bezużytecznych (który jest lepiej dostrojony dla scenariuszy po stronie interfejsu użytkownika i klienta), a także wyrzucania elementów bezużytecznych serwera.

### <a name="92-autodetectchanges"></a>9.2 Zmiany autodetectchanges

Jak wspomniano wcześniej, Entity Framework może pokazać problemy z wydajnością, gdy pamięć podręczna obiektów ma wiele jednostek. Niektóre operacje, takie jak Dodaj, Usuń, Znajdź, Wpis i SaveChanges, wyzwalają wywołania DetectChanges, które mogą zużywać dużą ilość procesora CPU na podstawie tego, jak duża stała się pamięć podręczna obiektów. Powodem tego jest, że pamięć podręczna obiektów i menedżer stanu obiektu starają się pozostać jak najbardziej zsynchronizowane w każdej operacji wykonywanej w kontekście, tak aby dane produkowane są gwarantowane być poprawne w szerokiej tablicy scenariuszy.

Ogólnie jest dobrą praktyką, aby pozostawić automatyczne wykrywanie zmian entity framework włączone przez cały okres użytkowania aplikacji. Jeśli scenariusz jest negatywnie wpływa na wysokie użycie procesora CPU i profile wskazują, że winowajcą jest wywołanie DetectChanges, należy rozważyć tymczasowe wyłączenie AutoDetectChanges w poufnej części kodu:

``` csharp
try
{
    context.Configuration.AutoDetectChangesEnabled = false;
    var product = context.Products.Find(productId);
    ...
}
finally
{
    context.Configuration.AutoDetectChangesEnabled = true;
}
```

Przed wyłączeniem AutoDetectChanges, dobrze jest zrozumieć, że może to spowodować entity framework utracić zdolność do śledzenia niektórych informacji o zmianach, które mają miejsce w jednostkach. Jeśli obsługiwane niepoprawnie, może to spowodować niespójność danych w aplikacji. Aby uzyskać więcej informacji na temat wyłączania \< http://blog.oneunicorn.com/2012/03/12/secrets-of-detectchanges-part-3-switching-off-automatic-detectchanges/>automatycznych zmian w programie AutoDetectChanges, przeczytaj .

### <a name="93-context-per-request"></a>9.3 Kontekst na żądanie

Konteksty entity framework są przeznaczone do użycia jako wystąpienia krótkotrwałe w celu zapewnienia najbardziej optymalne środowisko wydajności. Oczekuje się, że konteksty będą krótkotrwałe i odrzucone, a jako takie zostały zaimplementowane jako bardzo lekkie i ponownie przystosowują metadane, gdy tylko jest to możliwe. W scenariuszach sieci web ważne jest, aby mieć to na uwadze i nie mają kontekstu dla więcej niż czas trwania pojedynczego żądania. Podobnie w scenariuszach innych niż web kontekst powinien zostać odrzucony na podstawie zrozumienia różnych poziomów buforowania w ramach encji. Ogólnie rzecz biorąc, należy unikać wystąpienia kontekstu przez cały okres użytkowania aplikacji, a także kontekstów na wątek i kontekstów statycznych.

### <a name="94-database-null-semantics"></a>9.4 Semantyka null bazy danych

Entity Framework domyślnie wygeneruje\# kod SQL, który ma semantyka porównania c null. Należy wziąć pod uwagę następującą przykładowa kwerenda:

``` csharp
            int? categoryId = 7;
            int? supplierId = 8;
            decimal? unitPrice = 0;
            short? unitsInStock = 100;
            short? unitsOnOrder = 20;
            short? reorderLevel = null;

            var q = from p incontext.Products
                    wherep.Category.CategoryName == "Beverages"
                          || (p.CategoryID == categoryId
                                || p.SupplierID == supplierId
                                || p.UnitPrice == unitPrice
                                || p.UnitsInStock == unitsInStock
                                || p.UnitsOnOrder == unitsOnOrder
                                || p.ReorderLevel == reorderLevel)
                    select p;

            var r = q.ToList();
```

W tym przykładzie porównujemy liczbę zmiennych powodujących wartość nullable z właściwościami powodującymi anulowanie w jednostce, takimi jak SupplierID i UnitPrice. Wygenerowany SQL dla tej kwerendy zapyta, czy wartość parametru jest taka sama jak wartość kolumny lub jeśli zarówno parametr, jak i wartości kolumny mają wartość null. Spowoduje to ukrycie sposobu, w jaki serwer bazy\# danych obsługuje wartości null i zapewni spójne środowisko c null w różnych dostawcach bazy danych. Z drugiej strony wygenerowany kod jest nieco zawiłe i może nie działać dobrze, gdy ilość porównań w where instrukcji kwerendy rośnie do dużej liczby.

Jednym ze sposobów radzenia sobie z tą sytuacją jest przy użyciu bazy danych null semantyki. Należy zauważyć, że może to\# potencjalnie zachowywać się inaczej niż semantyka null C, ponieważ teraz Entity Framework wygeneruje prostszy SQL, który udostępnia sposób aparat bazy danych obsługuje wartości null. Semantyka null bazy danych może być aktywowana na kontekst za pomocą jednej linii konfiguracji względem konfiguracji kontekstu:

``` csharp
                context.Configuration.UseDatabaseNullSemantics = true;
```

Małe i średnie zapytania nie będą wyświetlać zauważalną poprawę wydajności podczas korzystania z semantyki null bazy danych, ale różnica stanie się bardziej zauważalna w kwerendach z dużą liczbą potencjalnych porównań null.

W powyższej kwerendzie przykładowej różnica w wydajności była mniejsza niż 2% w mikrobenchmarku działającym w kontrolowanym środowisku.

### <a name="95-async"></a>9.5 Asynchronik

Entity Framework 6 wprowadzono obsługę operacji asynchroniowych podczas uruchamiania w programie .NET 4.5 lub nowszym. W przeważającej części aplikacje, które mają rywalizacji związane z we/wy skorzystają najbardziej z używania asynchronicznej kwerendy i zapisać operacje. Jeśli aplikacja nie cierpi z powodu rywalizacji we/wy, użycie asynchronicznego będzie w najlepszych przypadkach działać synchronicznie i zwracać wynik w takiej samej ilości czasu jak wywołanie synchroniczne lub w najgorszym przypadku, po prostu odroczyć wykonanie zadania asynchronicznego i dodać dodatkowy czas do zakończenia scenariusza.

Aby uzyskać informacje na temat pracy programowania asynchroniowego, które pomogą Ci [http://msdn.microsoft.com/library/hh191443.aspx](https://msdn.microsoft.com/library/hh191443.aspx)zdecydować, czy asynchiza poprawi wydajność twojej wizyty w aplikacji . Aby uzyskać więcej informacji na temat używania operacji asynchronizowych w entity framework, zobacz [Async Query i Save](~/ef6/fundamentals/async.md
).

### <a name="96-ngen"></a>9.6 NGEN

Entity Framework 6 nie jest w domyślnej instalacji platformy .NET framework. W związku z tym zestawy framework jednostek nie są NGEN'd domyślnie, co oznacza, że cały kod entity framework podlega tym samym kosztom JIT'ing, jak każdy inny zestaw MSIL. Może to pogorszyć doświadczenie F5 podczas tworzenia, a także zimne uruchamianie aplikacji w środowiskach produkcyjnych. W celu zmniejszenia kosztów procesora i pamięci JIT'ing wskazane jest, aby NGEN entity framework obrazów, w stosownych przypadkach. Aby uzyskać więcej informacji na temat poprawy wydajności uruchamiania programu Entity Framework 6 za pomocą NGEN, zobacz [Poprawianie wydajności uruchamiania za pomocą NGen](~/ef6/fundamentals/performance/ngen.md).

### <a name="97-code-first-versus-edmx"></a>9.7 Code First w porównaniu z EDMX

Entity Framework przyczyny dotyczące problemu niezgodności impedancji między programowaniem obiektowym i relacyjnych baz danych, mając w pamięci reprezentacji modelu koncepcyjnego (obiektów), schemat magazynu (bazy danych) i mapowanie między nimi. Te metadane są nazywane modelem danych jednostki lub w skrócie EDM. Z tego EDM Entity Framework będzie pochodzić widoki do danych w obie strony z obiektów w pamięci do bazy danych i z powrotem.

Gdy entity framework jest używany z plikiem EDMX, który formalnie określa model koncepcyjny, schemat magazynu i mapowanie, a następnie etap ładowania modelu musi tylko sprawdzić, czy EDM jest poprawny (na przykład upewnij się, że nie brakuje mapowań), a następnie wygeneruj widoki, a następnie sprawdź poprawność widoków i przygotuj te metadane do użycia. Tylko wtedy można wykonać kwerendę lub zapisać nowe dane w magazynie danych.

Podejście Code First jest w swoim sercu zaawansowanym generatorem danych jednostki. Entity Framework musi produkować EDM z dostarczonego kodu; robi to analizując klasy zaangażowane w modelu, stosując konwencje i konfigurując model za pośrednictwem interfejsu API Fluent. Po zbudowaniu EDM entity Framework zasadniczo zachowuje się tak samo, jak gdyby plik EDMX był obecny w projekcie. W związku z tym tworzenie modelu z Code First dodaje dodatkową złożoność, która przekłada się na wolniejszy czas uruchamiania dla entity framework w porównaniu do posiadania EDMX. Koszt jest całkowicie zależny od rozmiaru i złożoności budowanego modelu.

Decydując się na użycie EDMX versus Code First, należy wiedzieć, że elastyczność wprowadzona przez Code First zwiększa koszt tworzenia modelu po raz pierwszy. Jeśli aplikacja może wytrzymać koszt tego obciążenia po raz pierwszy, zazwyczaj Code First będzie preferowanym sposobem.

## <a name="10-investigating-performance"></a>10 Badanie wydajności

### <a name="101-using-the-visual-studio-profiler"></a>10.1 Korzystanie z programu Visual Studio Profiler

Jeśli masz problemy z wydajnością z entity framework, można użyć profilera, takiego jak ten wbudowany w programie Visual Studio, aby zobaczyć, gdzie aplikacja spędza swój czas. Jest to narzędzie, którego używaliśmy do generowania wykresów kołowych w blogu "Exploring the Performance \< https://docs.microsoft.com/archive/blogs/adonet/exploring-the-performance-of-the-ado-net-entity-framework-part-1>) of the ADO.NET Entity Framework - Part 1" (które pokazują, gdzie entity framework spędza swój czas podczas zimnych i ciepłych zapytań.

Wpis w blogu "Profilowanie entity framework przy użyciu programu Visual Studio 2010 Profiler" napisany przez zespół doradczy ds.\<https://docs.microsoft.com/archive/blogs/dmcat/profiling-entity-framework-using-the-visual-studio-2010-profiler>. Ten post został napisany dla aplikacji systemu Windows. Jeśli chcesz profilować aplikację sieci web, narzędzia WPR (Performance Recorder) i Windows Performance Analyzer (WPA) mogą działać lepiej niż praca z programem Visual Studio. WPR i WPA są częścią zestawu narzędzi wydajności systemu Windows, który [http://www.microsoft.com/download/details.aspx?id=39982](https://www.microsoft.com/download/details.aspx?id=39982)jest dołączony do zestawu oceny i wdrażania systemu Windows ( ).

### <a name="102-applicationdatabase-profiling"></a>10.2 Profilowanie aplikacji/bazy danych

Narzędzia, takie jak profiler wbudowany w programie Visual Studio, informują, gdzie aplikacja spędza czas.Dostępny jest inny typ profilera, który wykonuje dynamiczną analizę uruchomionej aplikacji w produkcji lub przedprodukcji w zależności od potrzeb i wyszukuje typowe pułapki i wzorce anty-wzorce dostępu do bazy danych.

Dwa dostępne na rynku profilerzy to \< http://efprof.com>) Entity Framework Profiler \< http://ormprofiler.com>)( i ORMProfiler ( .

Jeśli aplikacja jest aplikacją MVC przy użyciu code first, można użyć MiniProfiler StackExchange.If your application is an MVC application using Code First, you can use StackExchange's MiniProfiler. Scott Hanselman opisuje to narzędzie \< http://www.hanselman.com/blog/NuGetPackageOfTheWeek9ASPNETMiniProfilerFromStackExchangeRocksYourWorld.aspx>w swoim blogu na: .

Aby uzyskać więcej informacji na temat profilowania aktywności aplikacji w bazie danych, zobacz artykuł MSDN Magazine Julie Lerman zatytułowany [Profilowanie aktywności bazy danych w ramach entity framework](https://msdn.microsoft.com/magazine/gg490349.aspx).

### <a name="103-database-logger"></a>10.3 Rejestrator bazy danych

Jeśli używasz Entity Framework 6 należy również rozważyć użycie wbudowanej funkcji rejestrowania. Właściwość bazy danych kontekstu może być poinstruowana, aby rejestrować jego działanie za pomocą prostej konfiguracji jednowierszowej:

``` csharp
    using (var context = newQueryComparison.DbC.NorthwindEntities())
    {
        context.Database.Log = Console.WriteLine;
        var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
        q.ToList();
    }
```

W tym przykładzie działanie bazy danych będą rejestrowane w konsoli, ale Log&lt;właściwości&gt; można skonfigurować do wywołania dowolnego delegata ciągu akcji.

Jeśli chcesz włączyć rejestrowanie bazy danych bez ponownej kompilacji i używasz entity framework 6.1 lub nowszego, można to zrobić, dodając interceptor w pliku web.config lub app.config aplikacji.

``` xml
  <interceptors>
    <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
      <parameters>
        <parameter value="C:\Path\To\My\LogOutput.txt"/>
      </parameters>
    </interceptor>
  </interceptors>
```

Aby uzyskać więcej informacji na temat dodawania rejestrowania bez ponownego komppilowania, przejdź do \< http://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/>pliku .

## <a name="11-appendix"></a>11 Dodatek

### <a name="111-a-test-environment"></a>11.1 A. Środowisko testowe

To środowisko używa konfiguracji 2-komputer z bazą danych na oddzielnym komputerze z aplikacji klienckiej. Maszyny znajdują się w tym samym stelażu, więc opóźnienie sieci jest stosunkowo niskie, ale bardziej realistyczne niż środowisko jednoobrobowe.

#### <a name="1111-app-server"></a>11.1.1 Serwer aplikacji

##### <a name="11111-software-environment"></a>11.1.1.1 Środowisko programowe

-   Środowisko oprogramowania Entity Framework 4
    -   Nazwa systemu operacyjnego: Dodatek SP1 dla systemu Windows Server 2008 R2 Enterprise.
    -   Visual Studio 2010 – Ultimate.
    -   Visual Studio 2010 z dodatku SP1 (tylko dla niektórych porównań).
-   Środowisko oprogramowania Entity Framework 5 i 6
    -   Nazwa systemu operacyjnego: Windows 8.1 Enterprise
    -   Visual Studio 2013 – Ultimate.

##### <a name="11112-hardware-environment"></a>11.1.1.2 Środowisko sprzętowe

-   Dwuprocesor: Procesor Intel(R) Xeon(R) L5520 W3530 @ 2.27GHz, 2261 Mhz8 GHz, 4 Core(s), 84 Procesory logiczne(-y).
-   2412 GB RamRAM.
-   Dysk 136 GB SCSI250GB SATA 7200 obr./s 3 GB/s dzieli się na 4 partycje.

#### <a name="1112-db-server"></a>11.1.2 Serwer DB

##### <a name="11121-software-environment"></a>11.1.2.1 Środowisko programowe

-   Nazwa systemu operacyjnego: Dodatek SP1 dla systemu Windows Server 2008 R28.1 Enterprise.
-   SQL Server 2008 R22012.

##### <a name="11122-hardware-environment"></a>11.1.2.2 Środowisko sprzętowe

-   Pojedynczy procesor: Procesor Intel(R) Xeon(R) L5520 @ 2.27GHz, 2261 MhzES-1620 0 @ 3.60GHz, 4 Core(s), 8 Procesory logiczne(-y).
-   824 GB RamRAM.
-   465 GB dysk SATA 7200 obr./s 7200 gb/s 465 GB dzieli się na 4 partycje.

### <a name="112-b-query-performance-comparison-tests"></a>11.2 B. Testy porównawcze wydajności zapytań

Model Northwind został użyty do wykonania tych testów. Został wygenerowany z bazy danych przy użyciu projektanta entity framework. Następnie użyto następującego kodu do porównania wydajności opcji wykonywania kwerendy:

``` csharp
using System;
using System.Collections.Generic;
using System.Data;
using System.Data.Common;
using System.Data.Entity.Infrastructure;
using System.Data.EntityClient;
using System.Data.Objects;
using System.Linq;

namespace QueryComparison
{
    public partial class NorthwindEntities : ObjectContext
    {
        private static readonly Func<NorthwindEntities, string, IQueryable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
            (NorthwindEntities context, string categoryName) =>
                context.Products.Where(p => p.Category.CategoryName == categoryName)
                );

        public IQueryable<Product> InvokeProductsForCategoryCQ(string categoryName)
        {
            return productsForCategoryCQ(this, categoryName);
        }
    }

    public class QueryTypePerfComparison
    {
        private static string entityConnectionStr = @"metadata=res://*/Northwind.csdl|res://*/Northwind.ssdl|res://*/Northwind.msl;provider=System.Data.SqlClient;provider connection string='data source=.;initial catalog=Northwind;integrated security=True;multipleactiveresultsets=True;App=EntityFramework'";

        public void LINQIncludingContextCreation()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {                 
                var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
                q.ToList();
            }
        }

        public void LINQNoTracking()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {
                context.Products.MergeOption = MergeOption.NoTracking;

                var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
                q.ToList();
            }
        }

        public void CompiledQuery()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {
                var q = context.InvokeProductsForCategoryCQ("Beverages");
                q.ToList();
            }
        }

        public void ObjectQuery()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {
                ObjectQuery<Product> products = context.Products.Where("it.Category.CategoryName = 'Beverages'");
                products.ToList();
            }
        }

        public void EntityCommand()
        {
            using (EntityConnection eConn = new EntityConnection(entityConnectionStr))
            {
                eConn.Open();
                EntityCommand cmd = eConn.CreateCommand();
                cmd.CommandText = "Select p From NorthwindEntities.Products As p Where p.Category.CategoryName = 'Beverages'";

                using (EntityDataReader reader = cmd.ExecuteReader(CommandBehavior.SequentialAccess))
                {
                    List<Product> productsList = new List<Product>();
                    while (reader.Read())
                    {
                        DbDataRecord record = (DbDataRecord)reader.GetValue(0);

                        // 'materialize' the product by accessing each field and value. Because we are materializing products, we won't have any nested data readers or records.
                        int fieldCount = record.FieldCount;

                        // Treat all products as Product, even if they are the subtype DiscontinuedProduct.
                        Product product = new Product();  

                        product.ProductID = record.GetInt32(0);
                        product.ProductName = record.GetString(1);
                        product.SupplierID = record.GetInt32(2);
                        product.CategoryID = record.GetInt32(3);
                        product.QuantityPerUnit = record.GetString(4);
                        product.UnitPrice = record.GetDecimal(5);
                        product.UnitsInStock = record.GetInt16(6);
                        product.UnitsOnOrder = record.GetInt16(7);
                        product.ReorderLevel = record.GetInt16(8);
                        product.Discontinued = record.GetBoolean(9);

                        productsList.Add(product);
                    }
                }
            }
        }

        public void ExecuteStoreQuery()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {
                ObjectResult<Product> beverages = context.ExecuteStoreQuery<Product>(
@"    SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued
    FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
    WHERE        (C.CategoryName = 'Beverages')"
);
                beverages.ToList();
            }
        }

        public void ExecuteStoreQueryDbContext()
        {
            using (var context = new QueryComparison.DbC.NorthwindEntities())
            {
                var beverages = context.Database.SqlQuery\<QueryComparison.DbC.Product>(
@"    SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued
    FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
    WHERE        (C.CategoryName = 'Beverages')"
);
                beverages.ToList();
            }
        }

        public void ExecuteStoreQueryDbSet()
        {
            using (var context = new QueryComparison.DbC.NorthwindEntities())
            {
                var beverages = context.Products.SqlQuery(
@"    SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued
    FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
    WHERE        (C.CategoryName = 'Beverages')"
);
                beverages.ToList();
            }
        }

        public void LINQIncludingContextCreationDbContext()
        {
            using (var context = new QueryComparison.DbC.NorthwindEntities())
            {                 
                var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
                q.ToList();
            }
        }

        public void LINQNoTrackingDbContext()
        {
            using (var context = new QueryComparison.DbC.NorthwindEntities())
            {
                var q = context.Products.AsNoTracking().Where(p => p.Category.CategoryName == "Beverages");
                q.ToList();
            }
        }
    }
}
```

### <a name="113-c-navision-model"></a>11.3 C. Model systemu Navision

Baza danych systemu Navision jest dużą bazą danych używaną do demonstracji systemu Microsoft Dynamics — NAV. Wygenerowany model koncepcyjny zawiera 1005 zestawów jednostek i 4227 zestawów skojarzeń. Model używany w teście jest "płaski" — nie dodano do niego dziedziczenia.

#### <a name="1131-queries-used-for-navision-tests"></a>11.3.1 Zapytania używane do testów systemu Navision

Lista zapytań używana z modelem systemu Navision zawiera 3 kategorie zapytań SQL encji:

##### <a name="11311-lookup"></a>11.3.1.1 Odnośnika

Prosta kwerenda odnośnika bez agregacji

-   Liczba: 16232
-   Przykład:

``` xml
  <Query complexity="Lookup">
    <CommandText>Select value distinct top(4) e.Idle_Time From NavisionFKContext.Session as e</CommandText>
  </Query>
```

##### <a name="11312singleaggregating"></a>11.3.1.2 Pojedyncza agregacja

Normalna kwerenda analizy biznesowej z wieloma agregacjami, ale bez sum częściowych (pojedyncza kwerenda)

-   Liczba: 2313
-   Przykład:

``` xml
  <Query complexity="SingleAggregating">
    <CommandText>NavisionFK.MDF_SessionLogin_Time_Max()</CommandText>
  </Query>
```

Gdzie MDF\_SessionLogin\_Time\_Max() jest zdefiniowany w modelu jako:

``` xml
  <Function Name="MDF_SessionLogin_Time_Max" ReturnType="Collection(DateTime)">
    <DefiningExpression>SELECT VALUE Edm.Min(E.Login_Time) FROM NavisionFKContext.Session as E</DefiningExpression>
  </Function>
```

##### <a name="11313aggregatingsubtotals"></a>11.3.1.3 AgregowanieSubtotals

Kwerenda BI z agregacjami i sumami częściowymi (za pośrednictwem wszystkich unii)

-   Liczba: 178
-   Przykład:

``` xml
  <Query complexity="AggregatingSubtotals">
    <CommandText>
using NavisionFK;
function AmountConsumed(entities Collection([CRONUS_International_Ltd__Zone])) as
(
    Edm.Sum(select value N.Block_Movement FROM entities as E, E.CRONUS_International_Ltd__Bin as N)
)
function AmountConsumed(P1 Edm.Int32) as
(
    AmountConsumed(select value e from NavisionFKContext.CRONUS_International_Ltd__Zone as e where e.Zone_Ranking = P1)
)
----------------------------------------------------------------------------------------------------------------------
(
    select top(10) Zone_Ranking, Cross_Dock_Bin_Zone, AmountConsumed(GroupPartition(E))
    from NavisionFKContext.CRONUS_International_Ltd__Zone as E
    where AmountConsumed(E.Zone_Ranking) > @MinAmountConsumed
    group by E.Zone_Ranking, E.Cross_Dock_Bin_Zone
)
union all
(
    select top(10) Zone_Ranking, Cast(null as Edm.Byte) as P2, AmountConsumed(GroupPartition(E))
    from NavisionFKContext.CRONUS_International_Ltd__Zone as E
    where AmountConsumed(E.Zone_Ranking) > @MinAmountConsumed
    group by E.Zone_Ranking
)
union all
{
    Row(Cast(null as Edm.Int32) as P1, Cast(null as Edm.Byte) as P2, AmountConsumed(select value E
                                                                         from NavisionFKContext.CRONUS_International_Ltd__Zone as E
                                                                         where AmountConsumed(E.Zone_Ranking) > @MinAmountConsumed))
}</CommandText>
    <Parameters>
      <Parameter Name="MinAmountConsumed" DbType="Int32" Value="10000" />
    </Parameters>
  </Query>
```
