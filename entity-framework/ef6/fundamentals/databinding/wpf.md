---
title: Wiązanie danych przy użyciu WPF-EF6
author: divega
ms.date: 05/19/2020
ms.assetid: e90d48e6-bea7785-47ef-b756-7b89cce4daf0
ms.openlocfilehash: baeb75b1ee386ca58013048bcc31ea4074604673
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526852"
---
# <a name="databinding-with-wpf"></a>Wiązanie danych z WPF

> [!IMPORTANT]
> **Ten dokument jest prawidłowy dla WPF tylko na .NET Framework**
>
> W tym dokumencie opisano powiązanie DataBinding dla WPF na .NET Framework. W przypadku nowych projektów platformy .NET Core zalecamy używanie [EF Core](/ef/core) zamiast Entity Framework 6. Dokumentacja dotycząca wiązania danych w EF Core jest tutaj: [wprowadzenie z WPF](/ef/core/get-started/wpf).

W tym przewodniku krok po kroku pokazano, jak powiązać typy POCO z kontrolkami WPF w formularzu "wzorzec-szczegóły". Aplikacja używa Entity Framework interfejsów API do wypełniania obiektów danymi z bazy danych, śledzenia zmian i utrwalania danych w bazie danych.

Model definiuje dwa typy, które uczestniczą w relacji jeden do wielu: **Kategoria** (główny podmiot zabezpieczeń \\ ) i **produkt** (szczegóły zależne \\ ). Następnie narzędzia programu Visual Studio są używane do powiązania typów zdefiniowanych w modelu z kontrolkami WPF. Struktura powiązań danych WPF umożliwia nawigowanie między obiektami pokrewnymi: wybranie wierszy w widoku wzorca powoduje aktualizację widoku szczegółów z odpowiednimi danymi podrzędnymi.

Zrzuty ekranu i listy kodu w tym instruktażu są pobierane z Visual Studio 2013 ale można wykonać ten Instruktaż w programie Visual Studio 2012 lub Visual Studio 2010.

## <a name="use-the-object-option-for-creating-wpf-data-sources"></a>Użyj opcji "Object" do tworzenia źródeł danych WPF

W przypadku wcześniejszej wersji Entity Framework zalecamy użycie opcji **bazy danych** podczas tworzenia nowego źródła danych na podstawie modelu utworzonego za pomocą narzędzia Dr Designer. To dlatego, że projektant wygeneruje kontekst pochodzący z klas ObjectContext i Entity Classes, które pochodzą z obiektu EntityObject. Użycie opcji baza danych pomoże Ci napisać najlepszy kod do współdziałania z tą powierzchnią interfejsu API.

Projektanci programu EF dla programu Visual Studio 2012 i Visual Studio 2013 generują kontekst, który pochodzi od DbContext wraz z prostymi klasami jednostek POCO. W programie Visual Studio 2010 zalecamy zamianę na szablon generowania kodu, który używa DbContext, zgodnie z opisem w dalszej części tego instruktażu.

Korzystając z powierzchni interfejsu API DbContext, należy użyć opcji **Object** podczas tworzenia nowego źródła danych, jak pokazano w tym instruktażu.

W razie potrzeby można [wrócić do generowania kodu opartego na obiekcie ObjectContext](~/ef6/modeling/designer/codegen/legacy-objectcontext.md) dla modeli utworzonych za pomocą programu EF Designer.

## <a name="pre-requisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, musisz mieć zainstalowany Visual Studio 2013, Visual Studio 2012 lub Visual Studio 2010.

W przypadku korzystania z programu Visual Studio 2010 należy również zainstalować pakiet NuGet. Aby uzyskać więcej informacji, zobacz [Instalowanie programu NuGet](https://docs.microsoft.com/nuget/install-nuget-client-tools).  

## <a name="create-the-application"></a>Tworzenie aplikacji

-   Otwórz program Visual Studio.
-   **Plik — &gt; Nowy — &gt; projekt...**
-   Wybierz pozycję **Windows**   w lewym okienku i **WPFApplication** w okienku po prawej stronie
-   Wprowadź **WPFwithEFSample**   jako nazwę
-   Wybierz **przycisk OK**

## <a name="install-the-entity-framework-nuget-package"></a>Zainstaluj pakiet NuGet Entity Framework

-   W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt **WinFormswithEFSample**
-   Wybierz pozycję **Zarządzaj pakietami NuGet...**
-   W oknie dialogowym Zarządzanie pakietami NuGet wybierz kartę **online** i wybierz pakiet **EntityFramework**
-   Kliknij przycisk **Instaluj**  
    >[!NOTE]
    > Oprócz zestawu EntityFramework dodawane jest również odwołanie do elementu System. ComponentModel. DataAnnotations. Jeśli projekt zawiera odwołanie do System. Data. Entity, zostanie usunięte po zainstalowaniu pakietu EntityFramework. Zestaw system. Data. Entity nie jest już używany w przypadku aplikacji Entity Framework 6.

## <a name="define-a-model"></a>Zdefiniuj model

W tym instruktażu możesz wybrać wdrożenie modelu przy użyciu Code First lub programu Dr Designer. Wykonaj jedną z dwóch poniższych sekcji.

### <a name="option-1-define-a-model-using-code-first"></a>Opcja 1: definiowanie modelu przy użyciu Code First

W tej sekcji pokazano, jak utworzyć model i skojarzoną z nim bazę danych przy użyciu Code First. Przejdź do następnej sekcji (**Opcja 2: zdefiniuj model przy użyciu Database First)** , jeśli wolisz używać Database First do odtwarzania modelu z bazy danych przy użyciu narzędzia Dr Designer

W przypadku korzystania z Code First projektowania zwykle zaczynasz od pisania klas .NET Framework, które definiują model koncepcyjny (domeny).

-   Dodaj nową klasę do **WPFwithEFSample:**
    -   Kliknij prawym przyciskiem myszy nazwę projektu
    -   Wybierz pozycję **Dodaj**, a następnie pozycję **nowy element**
    -   Wybierz **klasę** i wprowadź **produkt**   dla nazwy klasy
-   Zastąp **Product**   definicję klasy produktu następującym kodem:

``` csharp
    namespace WPFwithEFSample
    {
        public class Product
        {
            public int ProductId { get; set; }
            public string Name { get; set; }

            public int CategoryId { get; set; }
            public virtual Category Category { get; set; }
        }
    }

-   Add a **Category** class with the following definition:

    using System.Collections.ObjectModel;

    namespace WPFwithEFSample
    {
        public class Category
        {
            public Category()
            {
                this.Products = new ObservableCollection<Product>();
            }

            public int CategoryId { get; set; }
            public string Name { get; set; }

            public virtual ObservableCollection<Product> Products { get; private set; }
        }
    }
```

Właściwości **Products** klasy **Category** i **Category** klasy **Product** są właściwościami nawigacji. W Entity Framework właściwości nawigacji umożliwiają nawigowanie po relacjach między dwoma typami jednostek.

Oprócz definiowania jednostek należy zdefiniować klasę, która dziedziczy z DbContext i uwidacznia &lt; &gt; Właściwości nieogólnymi. Właściwości Nieogólnymiości &lt; &gt; pozwalają określić, które typy mają być uwzględnione w modelu.

Wystąpienie typu pochodnego DbContext zarządza obiektami obiektów w czasie wykonywania, co obejmuje wypełnianie obiektów danymi z bazy danych, śledzenie zmian i utrwalanie danych w bazie danych.

-   Dodaj nową klasę **ProductContext** do projektu z następującą definicją:

``` csharp
    using System.Data.Entity;

    namespace WPFwithEFSample
    {
        public class ProductContext : DbContext
        {
            public DbSet<Category> Categories { get; set; }
            public DbSet<Product> Products { get; set; }
        }
    }
```

Kompiluj projekt.

### <a name="option-2-define-a-model-using-database-first"></a>Opcja 2: definiowanie modelu przy użyciu Database First

W tej sekcji pokazano, jak używać programu Database First, aby odtworzyć model z bazy danych przy użyciu narzędzia Dr Designer. Jeśli poprzednia sekcja została ukończona (**Opcja 1: zdefiniuj model przy użyciu Code First)**, Pomiń tę sekcję i przejdź bezpośrednio do sekcji **ładowanie z opóźnieniem** .

#### <a name="create-an-existing-database"></a>Tworzenie istniejącej bazy danych

Zwykle w przypadku określania docelowej istniejącej bazy danych zostanie ona już utworzona, ale w tym instruktażu musimy utworzyć bazę danych do uzyskania dostępu.

Serwer bazy danych zainstalowany przy użyciu programu Visual Studio różni się w zależności od zainstalowanej wersji programu Visual Studio:

-   Jeśli używasz programu Visual Studio 2010, tworzysz bazę danych SQL Express.
-   Jeśli używasz programu Visual Studio 2012, zostanie utworzona baza danych [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx) .

Przyjrzyjmy się i wygenerujemy bazę danych.

-   **Widok- &gt; Eksplorator serwera**
-   Kliknij prawym przyciskiem myszy pozycję **połączenia danych — &gt; Dodaj połączenie...**
-   Jeśli nie masz połączenia z bazą danych Eksplorator serwera przed wybraniem Microsoft SQL Server jako źródła danych

    ![Zmień źródło danych](~/ef6/media/changedatasource.png)

-   Połącz się z usługą LocalDB lub SQL Express, w zależności od tego, który został zainstalowany, i wprowadź **produkty** jako nazwę bazy danych

    ![Dodaj LocalDB połączenia](~/ef6/media/addconnectionlocaldb.png)

    ![Dodaj połączenie ekspresowe](~/ef6/media/addconnectionexpress.png)

-   Wybierz pozycję **OK** . pojawi się monit z prośbą o utworzenie nowej bazy danych, a następnie wybierz pozycję **tak** .

    ![Create Database](~/ef6/media/createdatabase.png)

-   Nowa baza danych będzie teraz wyświetlana w Eksplorator serwera, kliknij ją prawym przyciskiem myszy, a następnie wybierz pozycję **nowe zapytanie** .
-   Skopiuj następujące SQL do nowego zapytania, a następnie kliknij prawym przyciskiem myszy zapytanie i wybierz polecenie **Execute (wykonaj** ).

``` SQL
    CREATE TABLE [dbo].[Categories] (
        [CategoryId] [int] NOT NULL IDENTITY,
        [Name] [nvarchar](max),
        CONSTRAINT [PK_dbo.Categories] PRIMARY KEY ([CategoryId])
    )

    CREATE TABLE [dbo].[Products] (
        [ProductId] [int] NOT NULL IDENTITY,
        [Name] [nvarchar](max),
        [CategoryId] [int] NOT NULL,
        CONSTRAINT [PK_dbo.Products] PRIMARY KEY ([ProductId])
    )

    CREATE INDEX [IX_CategoryId] ON [dbo].[Products]([CategoryId])

    ALTER TABLE [dbo].[Products] ADD CONSTRAINT [FK_dbo.Products_dbo.Categories_CategoryId] FOREIGN KEY ([CategoryId]) REFERENCES [dbo].[Categories] ([CategoryId]) ON DELETE CASCADE
```

#### <a name="reverse-engineer-model"></a>Odtwórz Model

Będziemy używać Entity Framework Designer, które są dołączone jako część programu Visual Studio, aby utworzyć nasz model.

-   **Projekt — &gt; Dodaj nowy element...**
-   Wybierz pozycję **dane** z menu po lewej stronie, a następnie **ADO.NET Entity Data Model**
-   Wprowadź **ProductModel** jako nazwę, a następnie kliknij przycisk **OK** .
-   Spowoduje to uruchomienie **kreatora Entity Data Model**
-   Wybierz pozycję **Generuj z bazy danych** i kliknij przycisk **dalej** .

    ![Wybierz zawartość modelu](~/ef6/media/choosemodelcontents.png)

-   Wybierz połączenie z bazą danych utworzoną w pierwszej sekcji, wprowadź **ProductContext** jako nazwę parametrów połączenia i kliknij przycisk **dalej** .

    ![Wybierz połączenie](~/ef6/media/chooseyourconnection.png)

-   Kliknij pole wyboru obok pozycji "tabele", aby zaimportować wszystkie tabele, a następnie kliknij przycisk Zakończ.

    ![Wybieranie obiektów](~/ef6/media/chooseyourobjects.png)

Po zakończeniu procesu odtwarzania nowy model zostanie dodany do projektu i otwarty do wyświetlania w Entity Framework Designer. Plik App.config został również dodany do projektu i zawiera szczegółowe informacje o połączeniu dla bazy danych.

#### <a name="additional-steps-in-visual-studio-2010"></a>Dodatkowe kroki w programie Visual Studio 2010

Jeśli pracujesz w programie Visual Studio 2010, musisz zaktualizować projektanta EF, aby korzystał z generowania kodu EF6.

-   Kliknij prawym przyciskiem myszy pusty punkt w modelu w programie Dr Designer i wybierz polecenie **Dodaj element generowania kodu...**
-   Wybierz pozycję **Szablony online** z menu po lewej stronie i Wyszukaj w usłudze **DbContext**
-   Wybierz pozycję **Dr 6. x DbContext generator dla języka C \# ,** wprowadź **ProductsModel** jako nazwę i kliknij przycisk Dodaj.

#### <a name="updating-code-generation-for-data-binding"></a>Aktualizowanie generowania kodu dla powiązania danych

EF generuje kod z modelu przy użyciu szablonów T4. Szablony dostarczane z programem Visual Studio lub pobrane z galerii programu Visual Studio są przeznaczone do ogólnego użycia. Oznacza to, że jednostki wygenerowane na podstawie tych szablonów mają &lt; proste &gt; właściwości ICollection T. Jednak podczas tworzenia powiązań danych przy użyciu platformy WPF pożądane jest użycie **ObservableCollection** dla właściwości kolekcji, dzięki czemu WPF może śledzić zmiany wprowadzone do kolekcji. W tym celu będziemy modyfikować szablony, aby używać ObservableCollection.

-   Otwórz **Eksplorator rozwiązań** i Znajdź plik **ProductModel. edmx**
-   Znajdź plik **ProductModel.tt** , który zostanie zagnieżdżony w pliku ProductModel. edmx

    ![Szablon modelu produktu WPF](~/ef6/media/wpfproductmodeltemplate.png)

-   Kliknij dwukrotnie plik ProductModel.tt, aby otworzyć go w edytorze programu Visual Studio.
-   Znajdź i Zamień dwa wystąpienia elementu "**ICollection**" na "**ObservableCollection**". Znajdują się one w przybliżeniu w wierszach 296 i 484.
-   Znajdź i Zamień pierwsze wystąpienie elementu "**HashSet —**" na "**ObservableCollection**". To wystąpienie znajduje się około w wierszu 50. **Nie** zamieniaj drugiego wystąpienia HashSet — znalezionego w dalszej części kodu.
-   Znajdź i Zamień jedynym wystąpieniem elementu "**System. Collections. Generic**" na "**System. Collections. ObjectModel**". Ta lokalizacja znajduje się około w wierszu 424.
-   Zapisz plik ProductModel.tt. Powinno to spowodować, że kod dla jednostek zostanie ponownie wygenerowany. Jeśli kod nie zostanie wygenerowany automatycznie, kliknij prawym przyciskiem myszy pozycję ProductModel.tt i wybierz polecenie "Uruchom narzędzie niestandardowe".

Jeśli teraz otworzysz plik Category.cs (który jest zagnieżdżony w obszarze ProductModel.tt), powinna zostać wyświetlona, że kolekcja Products zawiera ** &lt; &gt; produkt typu ObservableCollection**.

Kompiluj projekt.

## <a name="lazy-loading"></a>Ładowanie z opóźnieniem

Właściwości **Products** klasy **Category** i **Category** klasy **Product** są właściwościami nawigacji. W Entity Framework właściwości nawigacji umożliwiają nawigowanie po relacjach między dwoma typami jednostek.

EF oferuje opcję ładowania powiązanych jednostek z bazy danych automatycznie przy pierwszym dostępie do właściwości nawigacji. W przypadku tego typu ładowania (nazywanego ładowaniem opóźnionym) należy pamiętać, że podczas pierwszego uzyskiwania dostępu do każdej właściwości nawigacji oddzielne zapytanie zostanie wykonane względem bazy danych, jeśli zawartość nie jest jeszcze w kontekście.

W przypadku korzystania z typów jednostek POCO EF osiąga opóźnione ładowanie przez utworzenie wystąpień pochodnych typów proxy podczas wykonywania, a następnie Zastępowanie właściwości wirtualnych w klasach, aby dodać punkt zaczepienia ładowania. Aby uzyskać opóźnione ładowanie pokrewnych obiektów, należy zadeklarować metody do pobierania właściwości nawigacji jako **publiczne** i **wirtualne** (Zastąp w Visual Basic), a Klasa nie może być **zapieczętowana** **(** **NotOverridable** w Visual Basic). Przy użyciu Database First właściwości nawigacji są automatycznie wprowadzane do wirtualnego, aby umożliwić ładowanie z opóźnieniem. W sekcji Code First wybrano, aby właściwości nawigacji były wirtualne z tego samego powodu

## <a name="bind-object-to-controls"></a>Powiąż obiekt z kontrolkami

Dodaj klasy, które są zdefiniowane w modelu jako źródła danych dla tej aplikacji WPF.

-   Kliknij dwukrotnie pozycję **MainWindow. XAML** w Eksplorator rozwiązań, aby otworzyć formularz główny.
-   Z menu głównego wybierz **projekt — &gt; Dodaj nowe źródło danych...**
    (w programie Visual Studio 2010 musisz wybrać pozycję **dane &gt; Dodaj nowe źródło danych...**)
-   W oknie Wybieranie źródła danych Typewindow wybierz **obiekt** , a następnie kliknij przycisk **dalej** .
-   W oknie dialogowym Wybieranie obiektów danych unfold **WPFwithEFSample**   dwa razy i wybierz **kategorię**  
    *Nie ma potrzeby wybierania źródła danych **produktu** , ponieważ zostanie on przejdziemy do niego za pomocą właściwości **produktu**w źródle danych **kategorii** .*  

    ![Wybierz obiekty danych](~/ef6/media/selectdataobjects.png)

-   Kliknij przycisk **Zakończ.**
-   Okno źródła danych jest otwierane obok okna MainWindow. XAML *, jeśli okno źródła danych nie jest wyświetlane, wybierz pozycję **Widok — &gt; inne okna — &gt; źródła danych** *
-   Naciśnij ikonę pinezki, aby okno źródła danych nie było ukrywane. Może być konieczne kliknięcie przycisku Odśwież, jeśli okno było już widoczne.

    ![Źródła danych](~/ef6/media/datasources.png)

-   Wybierz **kategorię** źródło danych i przeciągnij ją na formularz.

Podczas przeciągania tego źródła wystąpił następujący:

-   Dodano zasób **categoryViewSource** i kontrolkę **categoryDataGrid** do języka XAML 
-   Właściwość DataContext elementu nadrzędnej siatki ma ustawioną wartość "{StaticResource **categoryViewSource** }".Zasób **categoryViewSource** służy jako źródło powiązań dla zewnętrznego \\ elementu siatki nadrzędnej. Elementy wewnętrznej siatki dziedziczą wartość DataContext z siatki nadrzędnej (Właściwość ItemsSource categoryDataGrid jest ustawiona na "{Binding}")

``` xml
    <Window.Resources>
        <CollectionViewSource x:Key="categoryViewSource"
                                d:DesignSource="{d:DesignInstance {x:Type local:Category}, CreateList=True}"/>
    </Window.Resources>
    <Grid DataContext="{StaticResource categoryViewSource}">
        <DataGrid x:Name="categoryDataGrid" AutoGenerateColumns="False" EnableRowVirtualization="True"
                    ItemsSource="{Binding}" Margin="13,13,43,191"
                    RowDetailsVisibilityMode="VisibleWhenSelected">
            <DataGrid.Columns>
                <DataGridTextColumn x:Name="categoryIdColumn" Binding="{Binding CategoryId}"
                                    Header="Category Id" Width="SizeToHeader"/>
                <DataGridTextColumn x:Name="nameColumn" Binding="{Binding Name}"
                                    Header="Name" Width="SizeToHeader"/>
            </DataGrid.Columns>
        </DataGrid>
    </Grid>
```

## <a name="adding-a-details-grid"></a>Dodawanie siatki szczegółów

Teraz, gdy mamy siatkę do wyświetlania kategorii dodajemy siatkę szczegółów, aby wyświetlić skojarzone produkty.

-   Wybierz właściwość **Products (produkty** ) w obszarze Źródło danych **kategorii** i przeciągnij ją na formularz.
    -   Zasób **categoryProductsViewSource** i siatka **productDataGrid** są dodawane do języka XAML
    -   Ścieżka powiązania dla tego zasobu jest ustawiona na produkty
    -   Struktura powiązań danych WPF zapewnia, że tylko produkty powiązane z wybraną kategorią będą widoczne w **productDataGrid**
-   Z przybornika przeciągnij **przycisk** do formularza. Ustaw właściwość **name** na **buttonSave** oraz Właściwość **Content** , która ma zostać **zapisana**.

Formularz powinien wyglądać podobnie do tego:

![Projektant](~/ef6/media/designer.png) 

## <a name="add-code-that-handles-data-interaction"></a>Dodawanie kodu, który obsługuje interakcję z danymi

Czas na dodanie obsługi zdarzeń do okna głównego.

-   W oknie XAML kliknij element ** &lt; window** , zaznaczając Okno główne
-   W oknie **Właściwości** wybierz pozycję **zdarzenia** w prawym górnym rogu, a następnie kliknij dwukrotnie pole tekstowe po prawej stronie **załadowanej** etykiety.

    ![Właściwości okna głównego](~/ef6/media/mainwindowproperties.png)

-   Dodaj również zdarzenie **kliknięcia** dla przycisku **Zapisz** przez dwukrotne kliknięcie przycisku Zapisz w projektancie. 

Spowoduje to przeprowadzenie pracy z kodem związanym z formularzem. teraz edytujemy kod, aby użyć ProductContext do zapewnienia dostępu do danych. Zaktualizuj kod dla MainWindow, jak pokazano poniżej.

Kod deklaruje długotrwałe wystąpienie **ProductContext**. Obiekt **ProductContext** jest używany do wykonywania zapytań i zapisywania danych w bazie danych. Metoda **Dispose ()** w wystąpieniu **ProductContext** jest następnie wywoływana z przesłoniętej metody **onzamykającej** .Komentarze do kodu zawierają szczegółowe informacje o tym, co robi kod.

``` csharp
    using System.Data.Entity;
    using System.Linq;
    using System.Windows;

    namespace WPFwithEFSample
    {
        public partial class MainWindow : Window
        {
            private ProductContext _context = new ProductContext();
            public MainWindow()
            {
                InitializeComponent();
            }

            private void Window_Loaded(object sender, RoutedEventArgs e)
            {
                System.Windows.Data.CollectionViewSource categoryViewSource =
                    ((System.Windows.Data.CollectionViewSource)(this.FindResource("categoryViewSource")));

                // Load is an extension method on IQueryable,
                // defined in the System.Data.Entity namespace.
                // This method enumerates the results of the query,
                // similar to ToList but without creating a list.
                // When used with Linq to Entities this method
                // creates entity objects and adds them to the context.
                _context.Categories.Load();

                // After the data is loaded call the DbSet<T>.Local property
                // to use the DbSet<T> as a binding source.
                categoryViewSource.Source = _context.Categories.Local;
            }

            private void buttonSave_Click(object sender, RoutedEventArgs e)
            {
                // When you delete an object from the related entities collection
                // (in this case Products), the Entity Framework doesn’t mark
                // these child entities as deleted.
                // Instead, it removes the relationship between the parent and the child
                // by setting the parent reference to null.
                // So we manually have to delete the products
                // that have a Category reference set to null.

                // The following code uses LINQ to Objects
                // against the Local collection of Products.
                // The ToList call is required because otherwise the collection will be modified
                // by the Remove call while it is being enumerated.
                // In most other situations you can use LINQ to Objects directly
                // against the Local property without using ToList first.
                foreach (var product in _context.Products.Local.ToList())
                {
                    if (product.Category == null)
                    {
                        _context.Products.Remove(product);
                    }
                }

                _context.SaveChanges();
                // Refresh the grids so the database generated values show up.
                this.categoryDataGrid.Items.Refresh();
                this.productsDataGrid.Items.Refresh();
            }

            protected override void OnClosing(System.ComponentModel.CancelEventArgs e)
            {
                base.OnClosing(e);
                this._context.Dispose();
            }
        }

    }
```

## <a name="test-the-wpf-application"></a>Testowanie aplikacji WPF

-   Skompiluj i uruchom aplikację. Jeśli użyto Code First, zostanie wyświetlona baza danych **WPFwithEFSample. ProductContext** .
-   Wprowadź nazwę kategorii w górnej siatce i nazwy produktów w dolnej siatce *nie wprowadzaj niczego w kolumnach identyfikatorów, ponieważ klucz podstawowy jest generowany przez bazę danych* .

    ![Okno główne z nowymi kategoriami i produktami](~/ef6/media/screen1.png)

-   Naciśnij przycisk **Save (Zapisz** ), aby zapisać dane w bazie danych

Po wywołaniu elementu DbContext **metody SaveChanges ()** identyfikatory są wypełniane wartościami wygenerowanymi przez bazę danych. Ze względu na to, że wywołano **odświeżanie ()** po **metody SaveChanges ()** , formanty **DataGrid** są aktualizowane również przy użyciu nowych wartości.

![Okno główne z wypełnionymi identyfikatorami](~/ef6/media/screen2.png)

## <a name="additional-resources"></a>Dodatkowe zasoby

Aby dowiedzieć się więcej na temat powiązania danych z kolekcjami przy użyciu platformy WPF, zobacz [ten temat](https://docs.microsoft.com/dotnet/framework/wpf/data/data-binding-overview#binding-to-collections) w dokumentacji WPF.  
