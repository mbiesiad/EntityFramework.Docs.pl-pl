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
# <a name="testability-and-entity-framework-40"></a>Testowanie i struktura jednostek 4.0
Scott Allen

Opublikowano: maj 2010 r.

## <a name="introduction"></a>Wprowadzenie

W tym opracowali oficjalny dokument opisano i pokazano, jak napisać testowalny kod za pomocą ADO.NET Entity Framework 4.0 i Visual Studio 2010. W tym dokumencie nie należy skupiać się na konkretnej metodologii testowania, takiej jak projektowanie oparte na testach (TDD) lub projektowanie oparte na zachowaniu (BDD). Zamiast tego ten dokument będzie koncentrować się na jak napisać kod, który używa ADO.NET Entity Framework jeszcze pozostaje łatwe do wyizolowania i testowania w sposób zautomatyzowany. Przyjrzymy się typowym wzorcom projektowym, które ułatwiają testowanie w scenariuszach dostępu do danych i zobaczymy, jak zastosować te wzorce podczas korzystania z tej struktury. Przyjrzymy się również określonym funkcjom struktury, aby zobaczyć, jak te funkcje mogą działać w sprawdzalnym kodzie.

## <a name="what-is-testable-code"></a>Co to jest testowalny kod?

Możliwość weryfikacji oprogramowania za pomocą zautomatyzowanych testów jednostkowych oferuje wiele pożądanych korzyści. Każdy wie, że dobre testy zmniejszą liczbę wad oprogramowania w aplikacji i podniosą jakość aplikacji - ale posiadanie testów jednostkowych wykracza daleko poza znalezienie błędów.

Dobry zestaw testów jednostkowych pozwala zespołowi programistów zaoszczędzić czas i zachować kontrolę nad utworzonym przez nie oprogramowaniem. Zespół może wprowadzać zmiany w istniejącym kodzie, refaktoryzatorze, przeprojektowywanie i restrukturyzacji oprogramowania w celu spełnienia nowych wymagań i dodawać nowe składniki do aplikacji, wiedząc, że zestaw testów może zweryfikować zachowanie aplikacji. Testy jednostkowe są częścią cyklu szybkiego sprzężenia zwrotnego w celu ułatwienia zmian i zachowania możliwości konserwacji oprogramowania w miarę zwiększania złożoności.

Testy jednostkowe mają jednak swoją cenę. Zespół musi poświęcić czas na tworzenie i utrzymywanie testów jednostkowych. Nakład pracy wymagany do utworzenia tych testów jest bezpośrednio związany z **możliwością testowania** oprogramowania źródłowego. Jak łatwe jest oprogramowanie do przetestowania? Zespół projektujący oprogramowanie z myślą o testowalności stworzy skuteczne testy szybciej niż zespół pracujący z niesprawnym oprogramowaniem.

Firma Microsoft zaprojektowała ADO.NET Entity Framework 4.0 (EF4) z myślą o testowalności. Nie oznacza to, że deweloperzy będą pisać testy jednostkowe względem samego kodu framework. Zamiast tego cele testowalności dla EF4 ułatwiają tworzenie sprawdzanego kodu, który tworzy na szczycie struktury. Zanim przyjrzymy się konkretnym przykładom, warto zrozumieć cechy kodu sprawdzanego.

### <a name="the-qualities-of-testable-code"></a>Cechy sprawdzanego kodu

Kod, który jest łatwy do przetestowania, zawsze będzie wykazywał co najmniej dwie cechy. Po pierwsze, sprawdzalny kod jest łatwy do **zaobserwowania.** Biorąc pod uwagę niektóre zestaw danych wejściowych, powinno być łatwe do obserwacji danych wyjściowych kodu. Na przykład testowanie następującej metody jest łatwe, ponieważ metoda bezpośrednio zwraca wynik obliczeń.

``` csharp
    public int Add(int x, int y) {
        return x + y;
    }
```

Testowanie metody jest trudne, jeśli metoda zapisuje obliczoną wartość w gnieździe sieciowym, tabeli bazy danych lub pliku, takiego jak poniższy kod. Test musi wykonać dodatkową pracę, aby pobrać wartość.

``` csharp
    public void AddAndSaveToFile(int x, int y) {
         var results = string.Format("The answer is {0}", x + y);
         File.WriteAllText("results.txt", results);
    }
```

Po drugie, sprawdzalny kod jest łatwy do **wyizolowania**. Użyjmy następującego pseudo-kodu jako zły przykład sprawdzalny kod.

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

Metoda jest łatwa do zaobserwowania – możemy przekazać polisę ubezpieczeniową i sprawdzić, czy wartość zwrotu odpowiada oczekiwanemu wynikowi. Jednak aby przetestować metodę, musimy mieć zainstalowaną bazę danych z poprawnym schematem i skonfigurować serwer SMTP w przypadku, gdy metoda próbuje wysłać wiadomość e-mail.

Test jednostkowy chce zweryfikować tylko logikę obliczeń wewnątrz metody, ale test może zakończyć się niepowodzeniem, ponieważ serwer poczty e-mail jest w trybie offline lub ponieważ serwer bazy danych został przeniesiony. Oba te błędy nie są związane z zachowaniem, które test chce zweryfikować. Zachowanie jest trudne do wyizolowania.

Deweloperzy oprogramowania, którzy starają się napisać testowalny kod często starają się zachować separacji problemów w kodzie, który piszą. Powyższa metoda powinna koncentrować się na obliczeniach biznesowych i delegować szczegóły implementacji bazy danych i wiadomości e-mail do innych składników. Robert C. Martin nazywa to zasadą jednolitej odpowiedzialności. Obiekt powinien hermetyzować pojedynczą, wąską odpowiedzialność, na przykład obliczanie wartości zasad. Wszystkie inne bazy danych i powiadomień pracy powinny być odpowiedzialne za inny obiekt. Kod napisany w ten sposób jest łatwiejszy do wyizolowania, ponieważ koncentruje się na jednym zadaniu.

W .NET mamy abstrakcje musimy przestrzegać zasady jednolitej odpowiedzialności i osiągnąć izolację. Możemy użyć definicji interfejsu i wymusić kod do użycia abstrakcji interfejsu zamiast typu konkretnego. W dalszej części tego artykułu zobaczymy, jak metoda taka jak zły przykład przedstawiony powyżej może pracować z interfejsami, które *wyglądają,* jakby będą rozmawiać z bazą danych. W czasie testowania możemy jednak zastąpić implementację manekina, która nie rozmawia z bazą danych, ale zamiast tego przechowuje dane w pamięci. Ta implementacja manekina wyizoluje kod od niepowiązanych problemów w kodzie dostępu do danych lub konfiguracji bazy danych.

Istnieją dodatkowe korzyści dla izolacji. Obliczenia biznesowe w ostatniej metodzie powinny potrwać tylko kilka milisekund do wykonania, ale sam test może działać przez kilka sekund, ponieważ kod przeskakuje wokół sieci i rozmawia z różnymi serwerami. Testy jednostkowe powinny działać szybko, aby ułatwić małe zmiany. Testy jednostkowe powinny być również powtarzalne i nie zawodzą, ponieważ składnik niezwiązany z testem ma problem. Pisanie kodu, który jest łatwy do zaobserwowania i wyizolowania oznacza, że deweloperzy będą mieli łatwiejszy czas pisania testów dla kodu, spędzają mniej czasu na czekaniu na testy do wykonania, a co ważniejsze, spędzają mniej czasu na śledzeniu błędów, które nie istnieją.

Mam nadzieję, że można docenić korzyści z testowania i zrozumieć cechy, które testowalne eksponatów kodu. Mamy zamiar zająć się, jak napisać kod, który współpracuje z EF4, aby zapisać dane w bazie danych, pozostając obserwowalne i łatwe do wyizolowania, ale najpierw zawęzimy naszą uwagę, aby omówić testowalne projekty dostępu do danych.

## <a name="design-patterns-for-data-persistence"></a>Wzorce projektowe dla trwałości danych

Oba złe przykłady przedstawione wcześniej miały zbyt wiele obowiązków. Pierwszy zły przykład musiał wykonać obliczenia *i* zapisać do pliku. Drugi zły przykład musiał odczytać dane z bazy danych *i* wykonać obliczenia biznesowe *i* wysłać wiadomość e-mail. Projektując mniejsze metody, które oddzielają obawy i delegują odpowiedzialność do innych składników, poczynisz duże postępy w kierunku pisania kodu testowalny. Celem jest tworzenie funkcjonalności przez komponowanie akcji z małych i skupionych abstrakcji.

Jeśli chodzi o trwałość danych małe i skoncentrowane abstrakcje, których szukamy są tak powszechne, że zostały udokumentowane jako wzorce projektowe. Książka Martina Fowlera Patterns of Enterprise Application Architecture była pierwszą pracą opisującą te wzorce w druku. Firma We'll provide a brief description of these patterns in the following sections before we show how these ADO.NET Entity Framework implements and works with these patterns.

### <a name="the-repository-pattern"></a>Wzorzec repozytorium

Fowler mówi repozytorium "pośredniczy między warstwami mapowania domeny i danych za pomocą interfejsu przypominającego kolekcję do uzyskiwania dostępu do obiektów domeny". Celem wzorca repozytorium jest wyizolowanie kodu od minutiae dostępu do danych, a jak widzieliśmy wcześniej izolacji jest wymagana cecha dla sprawdzalności.

Kluczem do izolacji jest sposób, w jaki repozytorium udostępnia obiekty przy użyciu interfejsu podobnego do kolekcji. Logika, którą piszesz do korzystania z repozytorium nie ma pojęcia, jak repozytorium zmaterializuje żądane obiekty. Repozytorium może rozmawiać z bazą danych lub może po prostu zwrócić obiekty z kolekcji w pamięci. Cały kod musi wiedzieć, jest to, że repozytorium wydaje się obsługiwać kolekcji i można pobrać, dodać i usunąć obiekty z kolekcji.

W istniejących aplikacjach .NET repozytorium betonu często dziedziczy z ogólnego interfejsu, takiego jak:

``` csharp
    public interface IRepository<T> {       
        IEnumerable<T> FindAll();
        IEnumerable<T> FindBy(Expression<Func\<T, bool>> predicate);
        T FindById(int id);
        void Add(T newEntity);
        void Remove(T entity);
    }
```

Firma We'll make a few changes to the interface definition when we provide an implementation for EF4, but the basic concept remains the same. Kod można użyć konkretnego repozytorium implementujące ten interfejs, aby pobrać jednostkę według jego wartości klucza podstawowego, aby pobrać kolekcję jednostek na podstawie oceny predykatu lub po prostu pobrać wszystkie dostępne jednostki. Kod można również dodawać i usuwać jednostki za pośrednictwem interfejsu repozytorium.

Biorąc pod uwagę IRepository employee obiektów, kod można wykonać następujące operacje.

``` csharp
    var employeesNamedScott =
        repository
            .FindBy(e => e.Name == "Scott")
            .OrderBy(e => e.HireDate);
    var firstEmployee = repository.FindById(1);
    var newEmployee = new Employee() {/*... */};
    repository.Add(newEmployee);
```

Ponieważ kod jest przy użyciu interfejsu (IRepository pracownika), możemy dostarczyć kod z różnych implementacji interfejsu. Jedną z implementacji może być implementacja wspierana przez EF4 i utrwalanie obiektów w bazie danych programu Microsoft SQL Server. Inna implementacja (używana podczas testowania) może być poparta przez listę pracowników w pamięci obiektów. Interfejs pomoże osiągnąć izolację w kodzie.

Zwróć uwagę, że&lt;interfejs&gt; IRepository T nie udostępnia operacji Zapisywania. Jak możemy zaktualizować istniejące obiekty? Można natknąć się na definicje IRepository, które zawierają Save operacji i implementacje tych repozytoriów będzie musiał natychmiast utrwalić obiekt do bazy danych. Jednak w wielu aplikacjach nie chcemy zachowywać obiektów indywidualnie. Zamiast tego chcemy ożywić obiekty, być może z różnych repozytoriów, zmodyfikować te obiekty jako część działania biznesowego, a następnie utrwalić wszystkie obiekty w ramach pojedynczej operacji niepodzielnej. Na szczęście istnieje wzorzec, aby umożliwić tego typu zachowanie.

### <a name="the-unit-of-work-pattern"></a>Jednostka wzorca pracy

Fowler mówi, że jednostka pracy będzie "prowadzić listę obiektów dotkniętych transakcją biznesową i koordynuje wypisanie zmian i rozwiązywanie problemów współbieżności". Obowiązkiem jednostki pracy jest śledzenie zmian w obiektach, które wprowadzamy do życia z repozytorium i utrwalanie wszelkich zmian, które wprowadziliśmy w obiektach, gdy mówimy jednostce pracy o zatwierdzeniu zmian. Jest to również odpowiedzialność jednostki pracy do podjęcia nowych obiektów dodaliśmy do wszystkich repozytoriów i wstawić obiekty do bazy danych, a także usuwanie mange.

Jeśli kiedykolwiek wykonałeś jakąkolwiek pracę z ADO.NET DataSets, będziesz już zaznajomiony z wzorcem pracy jednostki. ADO.NET DataSets miał możliwość śledzenia naszych aktualizacji, usunięcia i wstawiania obiektów DataRow i może (za pomocą TableAdapter) uzgodnić wszystkie nasze zmiany w bazie danych. Jednak obiekty DataSet modelu rozłączony podzbiór podstawowej bazy danych. Jednostka wzorca pracy wykazuje to samo zachowanie, ale współpracuje z obiektów biznesowych i obiektów domeny, które są odizolowane od kodu dostępu do danych i nieświadomych bazy danych.

Abstrakcja do modelowania jednostki pracy w kodzie .NET może wyglądać następująco:

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<Order> Orders { get; }
        IRepository<Customer> Customers { get; }
        void Commit();
    }
```

Ujawniając referencje repozytorium z jednostki pracy możemy zapewnić, że jedna jednostka obiektu pracy ma możliwość śledzenia wszystkich jednostek zmaterializowanych podczas transakcji biznesowej. Implementacja Commit metody dla rzeczywistej jednostki pracy jest, gdzie wszystkie magii dzieje się pogodzić zmiany w pamięci z bazą danych. 

Biorąc pod uwagę odwołanie IUnitOfWork, kod można wprowadzać zmiany w obiektach biznesowych pobranych z jednego lub więcej repozytoriów i zapisać wszystkie zmiany przy użyciu operacji atomic Commit.

``` csharp
    var firstEmployee = unitofWork.Employees.FindById(1);
    var firstCustomer = unitofWork.Customers.FindById(1);
    firstEmployee.Name = "Alex";
    firstCustomer.Name = "Christopher";
    unitofWork.Commit();
```

### <a name="the-lazy-load-pattern"></a>Wzorzec obciążenia z opóźnieniem

Fowler używa nazwy lagzy load do opisania "obiektu, który nie zawiera wszystkich danych, których potrzebujesz, ale wie, jak je uzyskać". Przezroczyste ładowanie z opóźnieniem jest ważną funkcją, która ma podczas pisania sprawdzanego kodu biznesowego i pracy z relacyjnej bazy danych. Jako przykład należy wziąć pod uwagę następujący kod.

``` csharp
    var employee = repository.FindById(id);
    // ... and later ...
    foreach(var timeCard in employee.TimeCards) {
        // .. manipulate the timeCard
    }
```

Jak wypełnia się kolekcję TimeCards? Istnieją dwie możliwe odpowiedzi. Jedną z odpowiedzi jest to, że repozytorium pracownika, gdy zostanie poproszony o pobranie pracownika, wystawia zapytanie w celu pobrania zarówno pracownika, jak i skojarzonych informacji o karcie czasu pracownika. W relacyjnych baz danych zazwyczaj wymaga to kwerendy z klauzulą JOIN i może spowodować pobranie większej ilości informacji niż wymaga to aplikacji. Co zrobić, jeśli aplikacja nigdy nie musi dotykać timecards właściwości?

Drugą odpowiedzią jest załadowanie właściwości TimeCards "na żądanie". To ładowanie z opóźnieniem jest niejawne i przezroczyste dla logiki biznesowej, ponieważ kod nie wywołuje specjalnych interfejsów API w celu pobrania informacji o karcie czasu. Kod zakłada, że informacje o karcie czasu są obecne w razie potrzeby. Istnieje kilka magii związane z ładowaniem z opóźnieniem, który zazwyczaj obejmuje przechwytywanie środowiska uruchomieniowego wywołań metody. Kod przechwytujący jest odpowiedzialny za rozmowy z bazą danych i pobieranie informacji o karcie czasu, pozostawiając logikę biznesową za darmo logiki biznesowej. Ta magia obciążenia z opóźnieniem umożliwia kod biznesowy izolować się od operacji pobierania danych i powoduje, że kod jest bardziej sprawdzalny.

Wadą z opóźnieniem obciążenia jest to, że gdy aplikacja *potrzebuje* informacji o karcie czasu kod wykona dodatkowe zapytanie. Nie jest to problemem dla wielu aplikacji, ale dla aplikacji zależnych od wydajności lub aplikacji zapętlania przez szereg obiektów pracownika i wykonywania kwerendy do pobierania kart czasu podczas każdej iteracji pętli (problem często określane jako problem kwerendy N + 1), ładowanie z opóźnieniem jest przeciąganie. W tych scenariuszach aplikacja może chcieć chętnie załadować informacje o karcie czasu w najbardziej efektywny sposób.

Na szczęście zobaczymy, jak EF4 obsługuje zarówno niejawne obciążenia leniwe i wydajne obciążenia chętnych, jak przenieść się do następnej sekcji i zaimplementować te wzorce.

## <a name="implementing-patterns-with-the-entity-framework"></a>Wdrażanie wzorców z platformą encji

Dobrą wiadomością jest to, że wszystkie wzorce projektowe, które opisaliśmy w ostatniej sekcji, są proste do zaimplementowania za pomocą EF4. Aby zademonstrować, zamierzamy użyć prostej aplikacji ASP.NET MVC do edycji i wyświetlania pracowników i powiązanych z nimi informacji o karcie czasu. Zaczniemy od użycia następujących "zwykłych starych obiektów CLR" (POCO). 

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

Te definicje klas zmieni się nieco, jak możemy zbadać różne podejścia i funkcje EF4, ale celem jest utrzymanie tych klas jako trwałości ignorantów (PI), jak to możliwe. Obiekt PI nie wie, *jak*, a nawet *jeśli*, stan posiada mieszka wewnątrz bazy danych. PI i POCO idą w parze z testowalnym oprogramowaniem. Obiekty przy użyciu metody POCO są mniej ograniczone, bardziej elastyczne i łatwiejsze do testowania, ponieważ mogą działać bez bazy danych.

Z POCO w miejscu możemy utworzyć model danych jednostki (EDM) w programie Visual Studio (patrz rysunek 1). Nie będziemy używać EDM do generowania kodu dla naszych podmiotów. Zamiast tego chcemy korzystać z jednostek, które pięknie twórzmy ręcznie. Będziemy używać EDM tylko do generowania naszego schematu bazy danych i podać metadane EF4 musi mapować obiekty do bazy danych.

![ef test_01](~/ef6/media/eftest-01.jpg)

**Rysunek 1.**

Uwaga: jeśli chcesz najpierw opracować model EDM, możliwe jest wygenerowanie czystego kodu POCO z EDM. Można to zrobić za pomocą rozszerzenia programu Visual Studio 2010 dostarczonego przez zespół programowalności danych. Aby pobrać rozszerzenie, uruchom Menedżera rozszerzeń z menu Narzędzia w programie Visual Studio i wyszukaj w galerii online szablonów dla "POCO" (zobacz rysunek 2). Istnieje kilka szablonów POCO dostępnych dla EF. Aby uzyskać więcej informacji na temat korzystania z szablonu, zobacz " [Instruktaż: Szablon POCO dla entity framework](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-poco-template-for-the-entity-framework)".

![ef test_02](~/ef6/media/eftest-02.png)

**Rysunek 2**

Z tego punktu wyjścia POCO zbadamy dwa różne podejścia do kodu testowalny. Pierwsze podejście, które nazywam podejściem EF, ponieważ wykorzystuje abstrakcje z interfejsu API programu Entity Framework do implementacji jednostek pracy i repozytoriów. W drugim podejściu utworzymy własne niestandardowe abstrakcje repozytorium, a następnie zobaczymy zalety i wady każdego podejścia. Zaczniemy od zbadania podejścia EF.  

### <a name="an-ef-centric-implementation"></a>Wdrożenie ef centric

Należy wziąć pod uwagę następujące działania kontrolera z ASP.NET projektu MVC. Akcja pobiera Employee obiektu i zwraca wynik, aby wyświetlić szczegółowy widok pracownika.

``` csharp
    public ViewResult Details(int id) {
        var employee = _unitOfWork.Employees
                                  .Single(e => e.Id == id);
        return View(employee);
    }
```

Czy kod jest sprawdzalny? Istnieją co najmniej dwa testy, które musimy zweryfikować zachowanie akcji. Najpierw chcielibyśmy sprawdzić, czy akcja zwraca poprawny widok – łatwy test. Chcielibyśmy również napisać test, aby sprawdzić, czy akcja pobiera poprawnego pracownika i chcielibyśmy to zrobić bez wykonywania kodu do kwerendy bazy danych. Pamiętaj, że chcemy wyizolować testowany kod. Izolacja zapewni, że test nie zakończy się niepowodzeniem z powodu błędu w kodzie dostępu do danych lub konfiguracji bazy danych. Jeśli test zakończy się niepowodzeniem, będziemy wiedzieć, że mamy błąd w logice kontrolera, a nie w niektórych składnikach systemu niższego poziomu.

Aby osiągnąć izolację, potrzebujemy kilka abstrakcji, takich jak interfejsy, które przedstawiliśmy wcześniej dla repozytoriów i jednostek pracy. Pamiętaj, że wzorzec repozytorium jest przeznaczony do pośredniczenia między obiektami domeny a warstwą mapowania danych. W tym scenariuszu EF4 *jest* warstwą mapowania danych i już zapewnia abstrakcję&lt;&gt; podobną do repozytorium o nazwie IObjectSet T (z obszaru nazw System.Data.Objects). Definicja interfejsu wygląda następująco.

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

IObjectSet&lt;&gt; T spełnia wymagania dla repozytorium, ponieważ przypomina kolekcję obiektów (za&lt;&gt;pośrednictwem IEnumerable T) i udostępnia metody dodawania i usuwania obiektów z symulowanej kolekcji. Metody Dołączania i odłączania udostępniają dodatkowe możliwości interfejsu API EF4. Aby użyć IObjectSet&lt;T&gt; jako interfejsu dla repozytoriów potrzebujemy jednostki abstrakcji pracy do powiązania repozytoriów razem.

``` csharp
    public interface IUnitOfWork {
        IObjectSet<Employee> Employees { get; }
        IObjectSet<TimeCard> TimeCards { get; }
        void Commit();
    }
```

Jedna konkretna implementacja tego interfejsu będzie rozmawiać z sql server i jest łatwe do utworzenia przy użyciu ObjectContext klasy z EF4. ObjectContext Klasa jest rzeczywistą jednostką pracy w interfejsie API EF4.

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

Wprowadzenie IObjectSet&lt;&gt; T do życia jest tak proste, jak wywoływanie CreateObjectSet metody ObjectContext obiektu. Za kulisami ramach będzie używać metadanych, które pod warunkiem w&lt;&gt;EDM do produkcji konkretnego ObjectSet T . Będziemy trzymać się zwracania interfejsu IObjectSet&lt;T,&gt; ponieważ pomoże zachować możliwości testowania w kodzie klienta.

Ta konkretna implementacja jest przydatna w produkcji, ale musimy skupić się na tym, jak będziemy używać naszej abstrakcji IUnitOfWork w celu ułatwienia testowania.

### <a name="the-test-doubles"></a>Test podwaja

Aby wyizolować akcję kontrolera, potrzebujemy możliwości przełączania się między rzeczywistą jednostką pracy (popartą przez ObjectContext) a testową podwójną lub "fałszywą" jednostką pracy (wykonywanie operacji w pamięci). Typowym podejściem do wykonywania tego typu przełączania jest nie pozwolić kontrolerowi MVC na tworzenie wystąpienia jednostki pracy, ale zamiast tego przekazać jednostkę pracy do kontrolera jako parametr konstruktora.

``` csharp
    class EmployeeController : Controller {
      publicEmployeeController(IUnitOfWork unitOfWork)  {
          _unitOfWork = unitOfWork;
      }
      ...
    }
```

Powyższy kod jest przykładem iniekcji zależności. Nie zezwalamy kontrolerowi na tworzenie zależności (jednostki pracy), ale wstrzykujemy zależność do kontrolera. W projekcie MVC jest wspólne użycie fabryki kontrolera niestandardowego w połączeniu z kontenerem inwersji kontroli (IoC) do automatyzacji iniekcji zależności. Te tematy wykraczają poza zakres tego artykułu, ale możesz przeczytać więcej, wykonując odwołania na końcu tego artykułu.

Fałszywa jednostka implementacji pracy, której możemy użyć do testowania, może wyglądać następująco.

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

Zwróć uwagę, że fałszywa jednostka pracy udostępnia właściwość Commited. Czasami warto dodać funkcje do fałszywej klasy, które ułatwiają testowanie. W takim przypadku jest łatwe do zaobserwowania, jeśli kod zatwierdza jednostkę pracy, sprawdzając Commited właściwości.

Będziemy również potrzebować fałszywego IObjectSet&lt;T&gt; do przechowywania obiektów employee i timecard w pamięci. Możemy zapewnić pojedynczą implementację przy użyciu generycznych.

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

Ten test dwukrotnie deleguje większość swojej pracy&lt;&gt; do podstawowej HashSet T obiektu. Należy zauważyć, że&lt;IObjectSet&gt; T wymaga ogólnego ograniczenia wymuszającego T jako klasę (typ odwołania), a także zmusza nas do zaimplementowania IQueryable&lt;T&gt;. Łatwo jest dokonać kolekcji w pamięci pojawiają się&lt;jako&gt; IQueryable T przy użyciu standardowego operatora LINQ AsQueryable.

### <a name="the-tests"></a>Testy

Tradycyjne testy jednostkowe użyją jednej klasy testowej do przechowywania wszystkich testów dla wszystkich akcji w jednym kontrolerze MVC. Możemy napisać te testy lub dowolny rodzaj testu jednostkowego, używając sfałszowanych w pamięci, które stworzyliśmy. Jednak w tym artykule unikniemy monolitycznego podejścia klasy testowej i zamiast tego zgrupujemy nasze testy, aby skupić się na określonej funkcji.Na przykład "utwórz nowego pracownika" może być funkcje, które chcemy przetestować, więc użyjemy jednej klasy testowej, aby zweryfikować akcję pojedynczego kontrolera odpowiedzialną za tworzenie nowego pracownika.

Istnieje kilka typowych kodu konfiguracji, którego potrzebujemy dla wszystkich tych klas testów drobnoziarnistych. Na przykład zawsze musimy tworzyć nasze repozytoria w pamięci i fałszywe jednostki pracy. Potrzebujemy również wystąpienia kontrolera pracownika z fałszywą jednostką pracy wstrzykniętą. Udostępnimy ten wspólny kod konfiguracji w klasach testowych przy użyciu klasy podstawowej.

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

"Matka obiektu" używamy w klasie podstawowej jest jeden wspólny wzorzec do tworzenia danych testowych. Macierzysta obiekt zawiera metody fabryczne do tworzenia wystąpienia jednostek testowych do użycia w wielu urządzeniach testowych.

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

Możemy użyć EmployeeControllerTestBase jako klasy podstawowej dla wielu urządzeń testowych (patrz rysunek 3). Każde urządzenie testowe przetestuje określoną akcję kontrolera. Na przykład jeden element testu skupi się na testowaniu akcji Utwórz używanej podczas żądania HTTP GET (aby wyświetlić widok do tworzenia pracownika), a inne urządzenie skoncentruje się na akcji Utwórz używanej w żądaniu HTTP POST (aby uzyskać informacje przesłane przez użytkownika w celu utworzenia pracownika). Każda klasa pochodna jest odpowiedzialny tylko za konfigurację potrzebne w określonym kontekście i do zapewnienia potwierdzeń potrzebnych do weryfikacji wyników dla jego kontekstu określonego testu.

![ef test_03](~/ef6/media/eftest-03.png)

**Rysunek 3**

Konwencja nazewnictwa i styl testu przedstawione w tym miejscu nie jest wymagane dla kodu sprawdzalne — to tylko jedno podejście. Rysunek 4 przedstawia testy uruchomione w wtyczce jet brains resharper test runner dla programu Visual Studio 2010.

![ef test_04](~/ef6/media/eftest-04.png)

**Rysunek 4**

Z klasy podstawowej do obsługi udostępnionego kodu konfiguracji, testy jednostkowe dla każdej akcji kontrolera są małe i łatwe do zapisu. Testy będą wykonywane szybko (ponieważ wykonujemy operacje w pamięci) i nie powinny zakończyć się niepowodzeniem z powodu niepowiązanych problemów z infrastrukturą lub środowiskiem (ponieważ wyizolowaliśmy testową jednostkę).

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

W tych testach klasa podstawowa wykonuje większość pracy instalatora. Pamiętaj konstruktor klasy podstawowej tworzy repozytorium w pamięci, fałszywe jednostki pracy i wystąpienie EmployeeController klasy. Klasa testowa pochodzi z tej klasy podstawowej i koncentruje się na specyfiki testowania Create metody. W takim przypadku szczegóły sprowadzają się do kroków "rozmieszczaj, działaj i assertuj", które zobaczysz w dowolnej procedurze testowania jednostkowego:

-   Utwórz nowyObażyć obiekt do symulacji przychodzących danych.
-   Wywołaj Create akcji EmployeeController i przekazać w newEmployee.
-   Sprawdź, czy akcja Utwórz daje oczekiwane wyniki (pracownik pojawia się w repozytorium).

To, co zbudowaliśmy, pozwala nam przetestować dowolną z akcji EmployeeController. Na przykład podczas pisania testów dla akcji Indeks kontrolera employee możemy dziedziczyć z klasy podstawowej testu, aby ustanowić tę samą konfigurację podstawową dla naszych testów. Ponownie klasa podstawowa utworzy repozytorium w pamięci, fałszywą jednostkę pracy i wystąpienie EmployeeController. Testy dla akcji Indeks wystarczy skupić się na wywoływaniu akcji Indeks i testowania jakości modelu akcja zwraca.

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

Testy, które tworzymy z podróbkami w pamięci, są ukierunkowane na testowanie *stanu* oprogramowania. Na przykład podczas testowania Create akcji chcemy sprawdzić stan repozytorium po wykonaniu akcji tworzenia — czy repozytorium przechowuje nowego pracownika?

``` csharp
    [TestMethod]
    public void ShouldAddNewEmployeeToRepository() {
        _controller.Create(_newEmployee);
        Assert.IsTrue(_repository.Contains(_newEmployee));
    }
```

Później przyjrzymy się testom opartym na interakcji. Testowanie oparte na interakcji zapyta, czy testowany kod wywołał odpowiednie metody na naszych obiektach i przeszedł poprawne parametry. Na razie przeniesiemy na okładkę inny wzór projektowy – leniwy ładunek.

## <a name="eager-loading-and-lazy-loading"></a>Gorliwy załadunek i leniwy ładowanie

W pewnym momencie w ASP.NET aplikacji sieci web MVC możemy chcieć wyświetlić informacje o pracowniku i dołączyć skojarzone z nim karty czasowe pracownika. Na przykład możemy mieć wyświetlanie podsumowania karty czasu, które pokazuje imię i nazwisko pracownika oraz całkowitą liczbę kart czasu w systemie. Istnieje kilka podejść, które możemy podjąć, aby zaimplementować tę funkcję.

### <a name="projection"></a>Projekcja

Jednym z łatwych podejść do tworzenia podsumowania jest skonstruowanie modelu dedykowanego do informacji, które chcemy wyświetlić w widoku. W tym scenariuszu model może wyglądać następująco.

``` csharp
    public class EmployeeSummaryViewModel {
        public string Name { get; set; }
        public int TotalTimeCards { get; set; }
    }
```

Należy zauważyć, że EmployeeSummaryViewModel nie jest jednostką — innymi słowy nie jest to coś, co chcemy utrzymać w bazie danych. Będziemy używać tej klasy tylko do tasowania danych do widoku w sposób silnie typizowany. Model widoku jest jak obiekt transferu danych (DTO), ponieważ nie zawiera żadnych zachowań (bez metod) — tylko właściwości. Właściwości będą zawierać dane, które musimy przenieść. Tworzenie wystąpienia tego modelu widoku jest łatwe przy użyciu standardowego operatora projekcji LINQ — operatora Select.

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

Istnieją dwie godne uwagi funkcje powyższego kodu. Po pierwsze – kod jest łatwy do przetestowania, ponieważ nadal jest łatwy do zaobserwowania i wyizolowania. Operator Select działa równie dobrze przeciwko naszym podróbom w pamięci, jak i przeciwko prawdziwej jednostce pracy.

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

Drugą godną uwagi funkcją jest sposób, w jaki kod umożliwia EF4 wygenerowanie pojedynczego, wydajnego zapytania w celu zebrania informacji o pracownikach i kartach czasu. Załadowaliśmy informacje o pracownikach i informacje o karcie czasu do tego samego obiektu bez użycia specjalnych interfejsów API. Kod tylko wyrażone informacje, które wymaga przy użyciu standardowych operatorów LINQ, które działają z w pamięci źródeł danych, jak również zdalnych źródeł danych. EF4 był w stanie przetłumaczyć drzewa wyrażeń generowane przez kwerendę LINQ i kompilator C\# na pojedyncze i wydajne zapytanie T-SQL.

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

Istnieją inne czasy, gdy nie chcemy pracować z modelem widoku lub obiektem DTO, ale z rzeczywistymi jednostkami. Kiedy wiemy, że potrzebujemy pracownika *i* kart czasowych pracownika, możemy chętnie załadować powiązane dane w dyskretny i skuteczny sposób.

### <a name="explicit-eager-loading"></a>Jawne wczesne ładowanie

Gdy chcemy chętnie załadować informacje o encji pokrewnych, potrzebujemy pewnego mechanizmu logiki biznesowej (lub w tym scenariuszu logiki akcji kontrolera), aby wyrazić swoje pragnienie repozytorium. Klasa EF4 ObjectQuery&lt;T&gt; definiuje metodę Include, aby określić powiązane obiekty do pobrania podczas kwerendy. Zapamiętaj EF4 ObjectContext udostępnia jednostki za&lt;&gt; pośrednictwem klasy ObjectSet T, która dziedziczy z ObjectQuery&lt;T&gt;.Gdybyśmy używali odwołań ObjectSet&lt;T&gt; w naszej akcji kontrolera, moglibyśmy napisać następujący kod, aby określić chętny ładunek informacji o karcie czasu dla każdego pracownika.

``` csharp
    _employees.Include("TimeCards")
              .Where(e => e.HireDate.Year > 2009);
```

Jednak ponieważ staramy się zachować nasz kod testowalne&lt;nie&gt; są narażając ObjectSet T spoza rzeczywistej jednostki klasy roboczej. Zamiast tego polegamy na interfejsie&lt;IObjectSet T,&gt; który jest łatwiejszy&lt;do&gt; sfałszowania, ale IObjectSet T nie definiuje metody Include. Piękno LINQ polega na tym, że możemy stworzyć własny operator Include.

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

Należy zauważyć, że to Włącz operator jest&lt;zdefiniowany jako metoda rozszerzenia dla IQueryable T&gt; zamiast IObjectSet&lt;T&gt;. Daje nam to możliwość korzystania z metody z szerszym zakresem&lt;możliwych&gt;typów, w&lt;tym&gt;IQueryable T , IObjectSet T ,&lt;ObjectQuery T&gt;i ObjectSet&lt;T&gt;. W przypadku, gdy podstawowa sekwencja nie&lt;jest&gt;oryginalna EF4 ObjectQuery T , to nie ma żadnych szkód zrobić i Include operator jest no-op. Jeśli podstawową *is* sekwencją jest&lt;&gt; ObjectQuery T (lub&lt;&gt;pochodzące z ObjectQuery T), a następnie EF4 zobaczy nasze wymagania dotyczące dodatkowych danych i sformułować odpowiednie zapytanie SQL.

Z tego nowego operatora w miejscu możemy jawnie zażądać obciążenia informacji karty czasu z repozytorium.

``` csharp
    public ViewResult Index() {
        var model = _unitOfWork.Employees
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

Po uruchomieniu z rzeczywistym ObjectContext, kod tworzy następujące pojedyncze zapytanie. Kwerenda zbiera wystarczającą ilość informacji z bazy danych w jednej podróży, aby zmaterializować obiekty pracownika i w pełni wypełnić ich timecards właściwości.

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

Świetną wiadomością jest kod wewnątrz metody akcji pozostaje w pełni sprawdzalny. Nie musimy udostępniać żadnych dodatkowych funkcji dla naszych podróbek, aby wspierać operatora Include. Złą wiadomością jest to, że musieliśmy użyć Include operator wewnątrz kodu chcieliśmy zachować wytrwałość ignorantów. Jest to doskonały przykład typu kompromisów, które należy ocenić podczas tworzenia kodu testowalny. Istnieją chwile, kiedy trzeba pozwolić trwałość dotyczy przeciek poza abstrakcji repozytorium, aby osiągnąć cele wydajności.

Alternatywą dla zaauwania chętnie jest ładowanie z opóźnieniem. Z opóźnieniem ładowania oznacza, że *nie* potrzebujemy naszego kodu biznesowego, aby jawnie ogłosić wymóg dla skojarzonych danych. Zamiast tego używamy naszych jednostek w aplikacji i jeśli potrzebne są dodatkowe dane Entity Framework załaduje dane na żądanie.

### <a name="lazy-loading"></a>Z opóźnieniem

Łatwo wyobrazić sobie scenariusz, w którym nie wiemy, jakich danych będzie potrzebowała logika biznesowa. Możemy wiedzieć, że logika potrzebuje obiektu pracownika, ale możemy rozgałęzić się na różne ścieżki wykonywania, gdzie niektóre z tych ścieżek wymagają informacji o karcie czasu od pracownika, a niektóre nie. Scenariusze takie jak ten są idealne do niejawnego ładowania z opóźnieniem, ponieważ dane magicznie pojawia się na podstawie zgodnie z potrzebami.

Z opóźnieniem ładowania, znany również jako odroczone ładowanie, umieszcza pewne wymagania na naszych obiektów jednostki. POCO z prawdziwą ignorancją trwałości nie napotka żadnych wymagań z warstwy trwałości, ale prawdziwa ignorancja trwałości jest praktycznie niemożliwa do osiągnięcia.Zamiast tego mierzymy ignorancję trwałości w stopniach względnych. Byłoby niefortunne, gdybyśmy musieli dziedziczyć z klasy podstawowej zorientowanej na trwałość lub użyć specjalistycznej kolekcji, aby osiągnąć opóźnienie ładowania w POCO. Na szczęście EF4 ma mniej inwazyjne rozwiązanie.

### <a name="virtually-undetectable"></a>Praktycznie niewykrywalne

Podczas korzystania z obiektów POCO, EF4 można dynamicznie generować serwery proxy środowiska uruchomieniowego dla jednostek. Te serwery proxy niewidocznie zawijają zmaterializowane POCO i zapewniają dodatkowe usługi, przechwytując każdą właściwość get and set operation w celu wykonania dodatkowej pracy. Jedną z takich usług jest leniwa funkcja ładowania, którego szukamy. Inna usługa to mechanizm śledzenia efektywnych zmian, który może rejestrować, gdy program zmienia wartości właściwości jednostki. Lista zmian jest używana przez ObjectContext podczas SaveChanges metody do utrwalania wszelkich zmodyfikowanych jednostek przy użyciu polecenia UPDATE.

Dla tych serwerów proxy do pracy, jednak potrzebują sposobu, aby podłączyć do właściwości dostać i ustawić operacje na jednostki, a serwery proxy osiągnąć ten cel przez zastąpienie wirtualnych elementów członkowskich. W związku z tym jeśli chcemy mieć niejawne ładowanie z opóźnieniem i efektywne śledzenie zmian, musimy wrócić do naszych definicji klas POCO i oznaczyć właściwości jako wirtualne.

``` csharp
    public class Employee {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }
        public virtual DateTime HireDate { get; set; }
        public virtual ICollection<TimeCard> TimeCards { get; set; }
    }
```

Nadal możemy powiedzieć, że jednostka Pracownik jest w większości niewiedzą w zachowaniu. Jedynym wymaganiem jest użycie elementów członkowskich wirtualnych i nie ma to wpływu na sprawdzalność kodu. Nie musimy czerpać z żadnej specjalnej klasy podstawowej, a nawet używać specjalnej kolekcji poświęconej leniwemu załadunkowi. Jak pokazuje kod, każda klasa implementująca&lt;&gt; ICollection T jest dostępna do przechowywania jednostek pokrewnych.

Jest też jedna drobna zmiana, którą musimy wprowadzić w naszej jednostce pracy. Ładowanie z opóźnieniem jest domyślnie *wyłączone* podczas pracy bezpośrednio z ObjectContext obiektu. Istnieje właściwość, którą możemy ustawić na ContextOptions właściwości, aby włączyć odroczone ładowanie i możemy ustawić tę właściwość wewnątrz naszej rzeczywistej jednostki pracy, jeśli chcemy włączyć ładowanie leniwe wszędzie.

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

Z niejawnego ładowania z opóźnieniem włączone, kod aplikacji można użyć pracownika i pracownika skojarzone karty czasu, pozostając błogo nieświadomy pracy wymaganej dla EF załadować dodatkowe dane.

``` csharp
    var employee = _unitOfWork.Employees
                              .Single(e => e.Id == id);
    foreach (var card in employee.TimeCards) {
        // ...
    }
```

Powolne ładowanie sprawia, że kod aplikacji łatwiejsze do zapisu, a z magii proxy kod pozostaje całkowicie sprawdzalny. Podróbki w pamięci jednostki pracy można po prostu wstępnie załadować fałszywe jednostki z powiązanych danych, gdy jest to potrzebne podczas testu.

W tym momencie zwrócimy naszą uwagę z tworzenia repozytoriów przy użyciu IObjectSet&lt;T&gt; i spojrzeć na abstrakcje, aby ukryć wszystkie oznaki struktury trwałości.

## <a name="custom-repositories"></a>Repozytoria niestandardowe

Kiedy po raz pierwszy zaprezentowaliśmy wzorzec projektowania jednostki pracy w tym artykule, podaliśmy przykładowy kod, jak może wyglądać jednostka pracy. Zaprezentujmy ponownie ten oryginalny pomysł przy użyciu scenariusza karty czasu pracownika i pracownika, z którym pracowaliśmy.

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<TimeCard> TimeCards { get;  }
        void Commit();
    }
```

Podstawową różnicą między tą jednostką pracy a jednostką pracy, którą utworzyliśmy w ostatniej sekcji, jest to, jak ta&lt;jednostka&gt;pracy nie używa żadnych abstrakcji z struktury EF4 (nie ma IObjectSet T ). IObjectSet&lt;&gt; T działa dobrze jako interfejs repozytorium, ale interfejs API, który udostępnia może nie idealnie dostosować się do potrzeb naszej aplikacji. W tym nadchodzącym podejściu będziemy reprezentować repozytoria przy&lt;&gt; użyciu niestandardowej abstrakcji IRepository T.

Wielu deweloperów, którzy postępują zgodnie z projektem opartym na testach, projektowaniem opartym na zachowaniu i projektowaniem metodologii opartych na domenie, preferuje podejście IRepository&lt;T&gt; z kilku powodów. Po pierwsze interfejs IRepository&lt;T&gt; reprezentuje warstwę "antykorupcyjną". Zgodnie z opisem eric Evans w jego domain driven design książki warstwy antykorupcyjnej utrzymuje kod domeny z dala od interfejsów API infrastruktury, jak interfejs API trwałości. Po drugie deweloperzy mogą tworzyć metody do repozytorium, które spełniają dokładne potrzeby aplikacji (odnalezione podczas pisania testów). Na przykład często może być konieczne zlokalizowanie pojedynczej jednostki przy użyciu wartości identyfikatora, dzięki czemu możemy dodać metodę FindById do interfejsu repozytorium.Nasza definicja IRepository&lt;T&gt; będzie wyglądać następująco.

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

Zwróć uwagę, że powrócimy do korzystania&lt;&gt; z interfejsu IQueryable T, aby udostępnić kolekcje jednostek. IQueryable&lt;&gt; T umożliwia linq wyrażeń drzewa przepływu do dostawcy EF4 i dać dostawcy całościowy widok kwerendy. Drugą opcją byłoby zwrócenie IEnumerable&lt;T&gt;, co oznacza, że dostawca EF4 LINQ zobaczy tylko wyrażenia wbudowane wewnątrz repozytorium. Wszelkie grupowanie, kolejność i projekcja wykonywane poza repozytorium nie będą składać się do polecenia SQL wysyłanego do bazy danych, co może zaszkodzić wydajności. Z drugiej strony repozytorium zwracające tylko wyniki&lt;IEnumerable T&gt; nigdy nie zaskoczy Cię nowym poleceniem SQL. Oba podejścia będą działać, a oba podejścia pozostają sprawdzalne.

Jest to proste, aby zapewnić pojedynczą implementację&lt;&gt; interfejsu IRepository T przy użyciu generics i EF4 ObjectContext INTERFEJSU API.

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

Podejście&lt;IRepository&gt; T daje nam dodatkową kontrolę nad naszymi zapytaniami, ponieważ klient musi wywołać metodę, aby uzyskać kontakt z jednostką. Wewnątrz metody możemy zapewnić dodatkowe kontrole i operatorów LINQ do wymuszania ograniczeń aplikacji. Zwróć uwagę, że interfejs ma dwa ograniczenia na parametr typu ogólnego. Pierwsze ograniczenie jest klasa cons taint wymagane&lt;&gt;przez ObjectSet T , a drugie ograniczenie wymusza nasze jednostki do zaimplementowania IEntity - abstrakcji utworzone dla aplikacji. Interfejs IEntity wymusza jednostki, aby mieć czytelną właściwość identyfikatora, a następnie możemy użyć tej właściwości w FindById metody. IEntity jest zdefiniowany za pomocą następującego kodu.

``` csharp
    public interface IEntity {
        int Id { get; }
    }
```

IEntity można uznać za małe naruszenie trwałości ignorancji, ponieważ nasze jednostki są wymagane do zaimplementowania tego interfejsu. Pamiętaj, że ignorancja trwałości dotyczy kompromisów, a dla wielu funkcja FindById przeważy nad ograniczeniem narzuconym przez interfejs. Interfejs nie ma wpływu na testowalność.

Tworzenie wystąpienia na żywo IRepository&lt;T&gt; wymaga EF4 ObjectContext, więc implementacji konkretnej jednostki pracy należy zarządzać wystąpienia.

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

### <a name="using-the-custom-repository"></a>Korzystanie z repozytorium niestandardowego

Korzystanie z naszego niestandardowego repozytorium nie różni się znacząco od korzystania&lt;&gt;z repozytorium opartego na IObjectSet T . Zamiast stosowania operatorów LINQ bezpośrednio do właściwości, najpierw musimy wywołać jedną z metod repozytorium, aby&lt;pobrać&gt; odwołanie IQueryable T.

``` csharp
    public ViewResult Index() {
        var model = _repository.FindAll()
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

Zwróć uwagę, że niestandardowy operator Include, który zaimplementowaliśmy wcześniej, będzie działał bez zmian. Metoda FindById repozytorium usuwa zduplikowaną logikę z akcji próbujących pobrać pojedynczą jednostkę.

``` csharp
    public ViewResult Details(int id) {
        var model = _repository.FindById(id);
        return View(model);
    }
```

Nie ma znaczącej różnicy w sprawdzalności dwóch metod, które zbadaliśmy. Możemy zapewnić fałszywe&lt;implementacje IRepository T,&gt; budując&lt;konkretne&gt; klasy wspierane przez pracownika HashSet - podobnie jak to, co zrobiliśmy w ostatniej sekcji. Jednak niektórzy deweloperzy wolą używać makiety obiektów i makiety struktur obiektów zamiast tworzenia podróbek. Przyjrzymy się za pomocą makiety, aby przetestować naszą implementację i omówić różnice między makietami i podróbkami w następnej sekcji.

### <a name="testing-with-mocks"></a>Testowanie za pomocą makiet

Istnieją różne podejścia do budowania tego, co Martin Fowler nazywa "podwójnym testem". Test double (jak film stunt double) jest obiektem, który budujesz, aby "stać" dla rzeczywistych obiektów produkcyjnych podczas testów. Repozytoria w pamięci, które utworzyliśmy, są testami podwaja dla repozytoriów, które rozmawiają z programem SQL Server. Widzieliśmy, jak używać tych testów podwaja podczas testów jednostkowych do izolowania kodu i zachować testy działa szybko.

Test podwaja stworzyliśmy mają prawdziwe, działające implementacje. Za kulisami każdy z nich przechowuje betonową kolekcję obiektów, a oni dodać i usunąć obiekty z tej kolekcji, jak manipulować repozytorium podczas testu. Niektórzy deweloperzy lubią tworzyć swoje testy podwaja w ten sposób — z prawdziwym kodem i implementacji pracy.Te podwójne badania są to, co *nazywamy podróbki*. Mają działające implementacje, ale nie są wystarczająco prawdziwe do użytku produkcyjnego. Fałszywe repozytorium w rzeczywistości nie zapisuje się w bazie danych. Fałszywy serwer SMTP w rzeczywistości nie wysyła wiadomości e-mail przez sieć.

### <a name="mocks-versus-fakes"></a>Szyki kontra podróbki

Istnieje inny typ testu dwukrotnie znany jako *mock*. Podczas gdy podróbki mają działające implementacje, mocks pochodzą z braku implementacji. Za pomocą struktury obiektu makiety konstruujemy te obiekty makiety w czasie wykonywania i używamy ich jako podwaja test. W tej sekcji będziemy używać open source mocking framework Moq. Oto prosty przykład używania Moq do dynamicznego tworzenia podwójnego testu dla repozytorium pracowników.

``` csharp
    Mock<IRepository<Employee>> mock =
        new Mock<IRepository<Employee>>();
    IRepository<Employee> repository = mock.Object;
    repository.Add(new Employee());
    var employee = repository.FindById(1);
```

Prosimy Moq o wdrożenie IRepository&lt;Employee&gt; i buduje jeden dynamicznie. Możemy uzyskać dostęp do obiektu implementującego&lt;IRepository Employee,&gt; uzyskując dostęp do Object właściwości Mock&lt;T&gt; obiektu. Jest to wewnętrzny obiekt, który możemy przekazać do naszych kontrolerów i nie będą wiedzieć, czy jest to test podwójny czy prawdziwe repozytorium. Możemy wywołać metody na obiekcie, tak jak firma My wywołać metody na obiekcie z rzeczywistą implementacją.

Musisz się zastanawiać, co repozytorium makiety zrobi, gdy wywołamy Add metody. Ponieważ nie ma implementacji za makiety obiektu, Add nie robi nic. Nie ma betonowej kolekcji za kulisami, jak mieliśmy z podróbkami, które napisaliśmy, więc pracownik zostaje odrzucony. Co z wartością zwracaną FindById? W tym przypadku obiekt makiety robi jedyną rzeczą, jaką może zrobić, czyli zwraca wartość domyślną. Ponieważ zwracamy typ odwołania (Pracownik), wartość zwracana jest wartością null.

Kpiny mogą wydawać się bezwartościowe; Istnieją jednak jeszcze dwie cechy makiet, o których nie rozmawialiśmy. Po pierwsze moq framework rejestruje wszystkie wywołania wykonane na makiety obiektu. W dalszej części kodu możemy zapytać Moq, czy ktoś wywołał Add metody lub jeśli ktoś wywołał FindById metody. Zobaczymy później, jak możemy korzystać z tej funkcji nagrywania "czarnej skrzynki" w testach.

Drugą wspaniałą cechą jest to, jak możemy użyć Moq do zaprogramowania makiety obiektu z *oczekiwaniami*. Oczekiwanie informuje obiekt makiety, jak reagować na danej interakcji. Na przykład możemy zaprogramować oczekiwania do naszego makiety i powiedzieć mu, aby zwrócić obiekt pracownika, gdy ktoś wywołuje FindById. Struktura Moq używa interfejsu API instalatora i wyrażenia lambda do programowania tych oczekiwań.

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

W tym przykładzie prosimy Moq dynamicznie zbudować repozytorium, a następnie programować repozytorium z oczekiwaniami. Oczekiwanie mówi makiety obiektu do zwrócenia nowego obiektu pracownika o wartości identyfikatora 5, gdy ktoś wywołuje FindById metody przekazywania wartości 5. Ten test przechodzi, a my nie trzeba budować pełną implementację&lt;do&gt;fałszywych IRepository T .

Wróćmy do testów, które napisaliśmy wcześniej i przerobić je, aby użyć makiet zamiast podróbek. Podobnie jak poprzednio, użyjemy klasy podstawowej do skonfigurowania typowych elementów infrastruktury, których potrzebujemy do wszystkich testów kontrolera.

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

Kod konfiguracji pozostaje w większości taki sam. Zamiast używać podróbek, użyjemy Moq do konstruowania makiet obiektów. Klasa podstawowa rozmieszcza makiety jednostki pracy, aby zwrócić repozytorium makiety, gdy kod wywołuje Employees właściwości. Pozostała część konfiguracji makiety odbędzie się wewnątrz opraw testowych dedykowanych każdemu konkretnemu scenariuszowi. Na przykład oprawa testowa dla akcji Indeks skonfiguruje makiety repozytorium, aby zwrócić listę pracowników, gdy akcja wywołuje FindAll metody repozytorium makiety.

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

Z wyjątkiem oczekiwań, nasze testy wyglądają podobnie do testów, które mieliśmy wcześniej. Jednak dzięki możliwości rejestrowania makiety struktury możemy podejść do testowania pod innym kątem. Przyjrzymy się tej nowej perspektywie w następnej sekcji.

### <a name="state-versus-interaction-testing"></a>Testowanie stanu a interakcji

Istnieją różne techniki, których można użyć do testowania oprogramowania z makietami obiektów. Jednym z podejść jest wykorzystanie testów opartych na stanie, co do tej pory zrobiliśmy w tym dokumencie. Testowanie oparte na stanie sprawia, że potwierdzenia dotyczące stanu oprogramowania. W ostatnim teście wywołaliśmy metodę akcji na kontrolerze i dokonaliśmy potwierdzenia o modelu, który powinien skompilować. Oto kilka innych przykładów stanu testowania:

-   Sprawdź, czy repozytorium zawiera nowy obiekt pracownika po wykonaniu create.
-   Sprawdź, czy model zawiera listę wszystkich pracowników po wykonaniu indeksu.
-   Sprawdź, czy repozytorium nie zawiera danego pracownika po wykonaniu delete.

Innym podejściem, które zobaczysz z makietami obiektów, jest weryfikowanie *interakcji.* Podczas testowania opartego na stanie sprawia, że potwierdzenia o stanie obiektów, testowanie oparte na interakcji sprawia, że potwierdzenia dotyczące interakcji obiektów. Przykład:

-   Sprawdź kontroler wywołuje repozytorium Add metody podczas wykonywania Create.
-   Sprawdź kontroler wywołuje repozytorium FindAll metody podczas wykonywania indeksu.
-   Sprawdź, czy kontroler wywołuje jednostkę pracy Commit metody, aby zapisać zmiany podczas wykonywania edycji.

Testowanie interakcji często wymaga mniej danych testowych, ponieważ nie grzebie w kolekcjach i nie weryfikujemy liczby. Na przykład jeśli wiemy, szczegóły akcji wywołuje findbyid repozytorium metody z poprawną wartością - następnie akcja prawdopodobnie zachowuje się poprawnie. Możemy zweryfikować to zachowanie bez konfigurowania żadnych danych testowych do zwrócenia z FindById.

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

Jedyną konfiguracją wymaganą w powyższym urządzeniu testowym jest konfiguracja zapewniana przez klasę podstawową. Gdy wywołamy akcję kontrolera, Moq będzie rejestrować interakcje z repozytorium makiety. Korzystając z interfejsu API Verify moq, możemy zapytać Moq, jeśli kontroler wywołał FindById z właściwą wartością identyfikatora. Jeśli kontroler nie wywołał metody lub wywołał metodę z nieoczekiwaną wartością parametru, Verify metoda zda wyjątek i test zakończy się niepowodzeniem.

Oto kolejny przykład, aby sprawdzić Create akcji wywołuje Commit na bieżącej jednostki pracy.

``` csharp
    [TestMethod]
    public void ShouldCommitUnitOfWork() {
        _controller.Create(_newEmployee);
        _unitOfWork.Verify(u => u.Commit());
    }
```

Jednym z zagrożeń związanych z testowaniem interakcji jest tendencja do określania interakcji. Możliwość obiektu makiety do rejestrowania i weryfikowania każdej interakcji z obiektem makiety nie oznacza, że test powinien próbować zweryfikować każdą interakcję. Niektóre interakcje są szczegóły implementacji i należy tylko sprawdzić interakcje *wymagane* do spełnienia bieżącego testu.

Wybór między mocks lub podróbki w dużej mierze zależy od systemu, który testujesz i osobiste (lub zespół) preferencje. Makiety obiektów można drastycznie zmniejszyć ilość kodu potrzebne do zaimplementowania testu podwaja, ale nie każdy jest wygodne oczekiwania programowania i weryfikacji interakcji.

## <a name="conclusions"></a>Wnioski

W tym dokumencie wykazaliśmy kilka podejść do tworzenia kodu sprawdzalne podczas korzystania z ADO.NET entity framework dla trwałości danych. Możemy wykorzystać wbudowane abstrakcje, takie&lt;&gt;jak IObjectSet T, lub&lt;tworzyć&gt;własne abstrakcje, takie jak IRepository T.W obu przypadkach obsługa POCO w ADO.NET Entity Framework 4.0 umożliwia konsumentom tych abstrakcji zachować trwałe ignorantów i wysoce sprawdzalne. Dodatkowe funkcje EF4, takie jak niejawne ładowanie z opóźnieniem umożliwia pracę kodu usługi biznesowej i aplikacji bez martwienia się o szczegóły relacyjnego magazynu danych. Wreszcie, abstrakcje, które tworzymy są łatwe do wyśmiewania lub fałszywe wewnątrz testów jednostkowych, i możemy użyć tych testów podwaja, aby osiągnąć szybkie uruchamianie, wysoce izolowane i niezawodne testy.

### <a name="additional-resources"></a>Dodatkowe zasoby

-   Robert C. Martin, " [Zasada jednej odpowiedzialności](https://www.objectmentor.com/resources/articles/srp.pdf)"
-   Martin Fowler, [Katalog wzorców](https://www.martinfowler.com/eaaCatalog/index.html) z *wzorców architektury aplikacji dla przedsiębiorstw*
-   Griffin Caprio, " [Zastrzyk zależności](https://msdn.microsoft.com/magazine/cc163739.aspx)"
-   Blog programowalności danych, " [Przewodnik: Programować oparte na testach z entity framework 4.0](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-test-driven-development-with-the-entity-framework-4-0)".
-   Blog programowalności danych" [Korzystanie z repozytorium i wzorców pracy z entity framework 4.0](https://docs.microsoft.com/archive/blogs/adonet/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0)"
-   Aaron Jensen, " [Introducing Machine Specifications](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)"
-   Eric Lee, " [BDD with MSTest](https://saintgimp.org/2009/01/20/bdd-with-mstest/)"
-   Eric Evans, " [Domain Driven Design](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)"
-   Martin Fowler, " [Mocks Aren't Stubs](https://martinfowler.com/articles/mocksArentStubs.html)"
-   Martin Fowler, " [Test Double](https://martinfowler.com/bliki/TestDouble.html)"
-   [Moq](https://code.google.com/p/moq/)

### <a name="biography"></a>Biografia

Scott Allen jest członkiem personelu technicznego w Pluralsight i założycielem OdeToCode.com. W ciągu 15 lat rozwoju komercyjnego oprogramowania Scott pracował nad rozwiązaniami dla wszystkich urządzeń 8-bitowych, po wysoce skalowalne aplikacje internetowe ASP.NET. Możesz dotrzeć do Scotta na swoim blogu w [https://twitter.com/OdeToCode](https://twitter.com/OdeToCode)OdeToCode lub na Twitterze pod adresem .
