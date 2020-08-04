---
title: Wprowadzenie do platformy WPF — EF Core
author: jeremylikness
ms.author: jeliknes
ms.date: 07/24/2020
uid: core/get-started/wpf
ms.openlocfilehash: 958fc579a72a9bf3c97c5551d55777df6c32293f
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87535649"
---
# <a name="getting-started-with-wpf"></a>Wprowadzenie do korzystania z platformy WPF

W tym przewodniku krok po kroku pokazano, jak powiązać typy POCO z kontrolkami WPF w formularzu "Main-szczegóły". Aplikacja używa Entity Framework interfejsów API do wypełniania obiektów danymi z bazy danych, śledzenia zmian i utrwalania danych w bazie danych.

Model definiuje dwa typy, które uczestniczą w relacji jeden do wielu: **kategorii** (głównej główna \\ ) i **produktu** (szczegóły zależne \\ ). Struktura powiązań danych WPF umożliwia nawigowanie między obiektami pokrewnymi: wybranie wierszy w widoku wzorca powoduje aktualizację widoku szczegółów z odpowiednimi danymi podrzędnymi.

Zrzuty ekranu i listy kodu w tym instruktażu są pobierane z programu Visual Studio 2019 16.6.5.

> [!TIP]
> Przykład tego artykułu można wyświetlić [w witrynie GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/WPF).

## <a name="pre-requisites"></a>Wymagania wstępne

* Aby ukończyć ten przewodnik, musisz mieć zainstalowany program Visual Studio 2019 16,3 lub nowszy z wybranym **obciążeniem programu .NET Desktop** .
    
    Aby uzyskać więcej informacji na temat instalowania najnowszej wersji programu Visual Studio, zobacz [Instalowanie programu Visual Studio](/visualstudio/install/install-visual-studio).

## <a name="create-the-application"></a>Tworzenie aplikacji

1. Otwórz program Visual Studio.
2. W oknie uruchamiania wybierz pozycję **Utwórz nowy projekt**.
3. Wyszukaj ciąg "WPF", wybierz pozycję **Aplikacja WPF (.NET Core)** , a następnie wybierz przycisk **dalej**.
4. Na następnym ekranie Nadaj projektowi nazwę, na przykład **GetStartedWPF**, i wybierz pozycję **Utwórz.**

## <a name="install-the-entity-framework-nuget-packages"></a>Zainstaluj Entity Framework pakiety NuGet

1. Kliknij prawym przyciskiem myszy rozwiązanie i wybierz pozycję **Zarządzaj pakietami NuGet dla rozwiązania...**

    ![Zarządzanie pakietami NuGet](_static/wpf-tutorial-nuget.jpg)

1. Wpisz `entityframeworkcore.sqlite` w polu wyszukiwania.
1. Wybierz pakiet **Microsoft. EntityFrameworkCore. sqlite** .
1. Sprawdź projekt w okienku po prawej stronie, a następnie kliknij przycisk **Instaluj** .

    ![Pakiet oprogramowania SQLite](_static/wpf-tutorial-sqlite.jpg)

1. Powtórz kroki, aby wyszukać `entityframeworkcore.proxies` i zainstalować **Microsoft. EntityFrameworkCore. proxy**.

> [!NOTE]
> Podczas instalacji pakietu SQLite automatycznie pobierany jest powiązany pakiet podstawowy **Microsoft. EntityFrameworkCore** . Pakiet **Microsoft. EntityFrameworkCore. proxy** zapewnia obsługę danych "ładowanie z opóźnieniem". Oznacza to, że w przypadku obiektów z jednostkami podrzędnymi pobierane są tylko elementy nadrzędne w początkowym obciążeniu. Serwery proxy wykrywają, kiedy nastąpi próba uzyskania dostępu do jednostek podrzędnych i ładuje je automatycznie na żądanie. 

## <a name="define-a-model"></a>Zdefiniuj model

W tym instruktażu zostanie wdrożony model przy użyciu "Code First". Oznacza to, że EF Core utworzy tabele bazy danych i schemat na podstawie zdefiniowanych klas języka C#.

Dodaj nową klasę. Nadaj jej nazwę: `Product.cs` i wypełnij ją następująco:

**`Product.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/Product.cs)]

Następnie Dodaj klasę o nazwie `Category.cs` i wypełnij ją następującym kodem:

**`Category.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/Category.cs)]

Właściwości **Products** klasy **Category** i **Category** klasy **Product** są właściwościami nawigacji. W Entity Framework właściwości nawigacji umożliwiają nawigowanie po relacjach między dwoma typami jednostek.

Oprócz definiowania jednostek należy zdefiniować klasę, która dziedziczy z DbContext i uwidacznia &lt; &gt; Właściwości nieogólnymi. Właściwości Nieogólnymiości &lt; &gt; pozwalają określić, które typy mają być uwzględnione w modelu.

Wystąpienie typu pochodnego DbContext zarządza obiektami obiektów w czasie wykonywania, co obejmuje wypełnianie obiektów danymi z bazy danych, śledzenie zmian i utrwalanie danych w bazie danych.

Dodaj nową `ProductContext.cs` klasę do projektu z następującą definicją:

**`ProductContext.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/ProductContext.cs)]

* Informuje o tym, `DbSet` EF Core jakie jednostki języka C# mają być mapowane do bazy danych.
* Istnieją różne sposoby konfigurowania EF Core `DbContext` . Możesz przeczytać o nich w: [Konfigurowanie DbContext](/ef/core/miscellaneous/configuring-dbcontext).
* W tym przykładzie zastosowano `OnConfiguring` zastąpienie, aby określić plik danych programu SQLite.
* `UseLazyLoadingProxies`Wywołanie instruuje EF Core, aby zaimplementować ładowanie z opóźnieniem, więc jednostki podrzędne są automatycznie ładowane podczas uzyskiwania dostępu z elementu nadrzędnego.

Naciśnij **klawisze CTRL + SHIFT + B** lub przejdź do **kompilacji Kompiluj &gt; rozwiązanie** , aby skompilować projekt.

> [!TIP]
> Zapoznaj się z informacjami na temat innych sposobów, aby zachować synchronizację baz danych i modeli EF Core: [Zarządzanie schematami bazy danych](/ef/core/managing-schemas).

## <a name="lazy-loading"></a>Ładowanie z opóźnieniem

Właściwości **Products** klasy **Category** i **Category** klasy **Product** są właściwościami nawigacji. W Entity Framework Core właściwości nawigacji umożliwiają nawigowanie po relacjach między dwoma typami jednostek.

EF Core zapewnia opcję ładowania powiązanych jednostek z bazy danych automatycznie przy pierwszym dostępie do właściwości nawigacji. W przypadku tego typu ładowania (nazywanego ładowaniem opóźnionym) należy pamiętać, że podczas pierwszego uzyskiwania dostępu do każdej właściwości nawigacji oddzielne zapytanie zostanie wykonane względem bazy danych, jeśli zawartość nie jest jeszcze w kontekście.

W przypadku używania "zwykłego starego typu obiektu języka C#" (POCO), EF Core osiąga opóźnienie ładowania przez utworzenie wystąpień pochodnych typów proxy podczas wykonywania, a następnie Zastępowanie właściwości wirtualnych w klasach w celu dodania punktu zaczepienia ładowania. Aby uzyskać opóźnione ładowanie powiązanych obiektów, należy zadeklarować metody do pobierania właściwości nawigacji jako **publiczne** i **wirtualne** (Zastąp w Visual Basic), a Klasa nie może być **zapieczętowana** **(** **NotOverridable** w Visual Basic). W przypadku korzystania z Database First właściwości nawigacji są automatycznie wprowadzane do wirtualnego, aby umożliwić ładowanie z opóźnieniem. 

## <a name="bind-object-to-controls"></a>Powiąż obiekt z kontrolkami

Dodaj klasy, które są zdefiniowane w modelu jako źródła danych dla tej aplikacji WPF.

1. Kliknij dwukrotnie pozycję **MainWindow. XAML** w Eksplorator rozwiązań, aby otworzyć formularz główny.
1. Wybierz kartę **XAML** , aby edytować kod XAML.
1. Zaraz po tagu otwierającym `Window` Dodaj następujące źródła, aby połączyć się z jednostkami EF Core.

    [!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=1-13&highlight=9-13)]

1. Powoduje to skonfigurowanie źródła dla kategorii "nadrzędny" i drugie źródło dla produktów "Szczegóły".
1. Następnie Dodaj następujący znacznik do kodu XAML po tagu zamykającym `Window.Resources` .

    [!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=15-26)]

1. Należy pamiętać, że `CategoryId` jest ustawiona na, `ReadOnly` ponieważ jest przypisana przez bazę danych i nie można jej zmienić.

## <a name="adding-a-details-grid"></a>Dodawanie siatki szczegółów

Teraz, gdy siatka istnieje do wyświetlania kategorii, można dodać siatkę szczegółów, aby pokazać produkty.

**`MainWindow.xaml`**

[!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=27-40)]

Na koniec Dodaj `Save` przycisk i przewód w zdarzeniu kliknięcia do `Button_Click` .

[!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=41-42)]

Widok projektu powinien wyglądać następująco:

![Zrzut ekranu projektanta WPF](_static/wpf-tutorial-designer.jpg)

## <a name="add-code-that-handles-data-interaction"></a>Dodawanie kodu, który obsługuje interakcję z danymi

Czas na dodanie obsługi zdarzeń do okna głównego.

1. W oknie XAML kliknij element ** &lt; okna &gt; ** , aby wybrać okno główne.
1. W oknie **Właściwości** wybierz pozycję **zdarzenia** w prawym górnym rogu, a następnie kliknij dwukrotnie pole tekstowe po prawej stronie **załadowanej** etykiety.

    ![Właściwości okna głównego](_static/wpf-tutorial-loaded.jpg)

Spowoduje to przeprowadzenie pracy z kodem związanym z formularzem. teraz edytujemy kod w celu korzystania z programu w `ProductContext` celu zapewnienia dostępu do danych. Zaktualizuj kod, jak pokazano poniżej.

Kod deklaruje długotrwałe wystąpienie `ProductContext` . `ProductContext`Obiekt jest używany do wykonywania zapytań i zapisywania danych w bazie danych. `Dispose()`Metoda na tym `ProductContext` wystąpieniu jest następnie wywoływana z przesłoniętej `OnClosing` metody.Komentarze do kodu wyjaśniają, co robi każdy krok.

**`MainWindow.xaml.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml.cs)]

> [!NOTE]
> Kod używa wywołania do `EnsureCreated()` kompilowania bazy danych przy pierwszym uruchomieniu. Jest to akceptowalne dla pokazów, ale w aplikacjach produkcyjnych należy przeglądać [migracje](/ef/core/managing-schemas/migrations/) w celu zarządzania schematem. Kod jest również wykonywany synchronicznie, ponieważ używa lokalnej bazy danych programu SQLite. W przypadku scenariuszy produkcyjnych, które zwykle obejmują serwer zdalny, należy rozważyć użycie asynchronicznych wersji `Load` `SaveChanges` metod i.

## <a name="test-the-wpf-application"></a>Testowanie aplikacji WPF

Skompiluj i uruchom aplikację, naciskając klawisz **F5** lub wybierając pozycję **Debuguj &gt; Rozpocznij debugowanie**. Baza danych powinna zostać utworzona automatycznie przy użyciu pliku o nazwie `products.db` . Wprowadź nazwę kategorii i naciśnij klawisz ENTER, a następnie Dodaj produkty do dolnej siatki. Kliknij przycisk Zapisz i obejrzyj odświeżanie siatki przy użyciu dostarczonych identyfikatorów bazy danych. Zaznacz wiersz i naciśnij klawisz **delete** , aby usunąć wiersz. Jednostka zostanie usunięta po kliknięciu przycisku **Zapisz**.

![Uruchomiona aplikacja](_static/wpf-tutorial-app.jpg)

## <a name="property-change-notification"></a>Powiadomienie o zmianie właściwości

Ten przykład polega na czterech krokach w celu zsynchronizowania jednostek z interfejsem użytkownika. 

1. Początkowe wywołanie `_context.Categories.Load()` ładuje dane kategorii.
1. Ładowane z opóźnieniem serwery proxy ładują dane zależnych produktów.
1. Wbudowane śledzenie zmian EF Core wprowadza odpowiednie modyfikacje jednostek, w tym wstawienia i usunięcia, gdy `_context.SaveChanges()` jest wywoływana.
1. Wywołania, aby `DataGridView.Items.Refresh()` wymusić ponowne załadowanie z nowo wygenerowanymi identyfikatorami.

Działa to w przypadku przykładu wprowadzenie, ale może być potrzebny dodatkowy kod dla innych scenariuszy. Formanty WPF renderują interfejs użytkownika, odczytując pola i właściwości w jednostkach. W przypadku edycji wartości w interfejsie użytkownika (UI) ta wartość jest przesyłana do jednostki. Gdy zmieniasz wartość właściwości bezpośrednio w jednostce, na przykład ładując ją z bazy danych, WPF nie będzie natychmiast odzwierciedlać zmian wprowadzonych w interfejsie użytkownika. Aparat renderowania musi zostać powiadomiony o zmianach. Projekt został spowodowany przez ręczne wywołanie `Refresh()` . Łatwym sposobem automatyzacji tego powiadomienia jest zaimplementowanie interfejsu [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged) . Składniki WPF automatycznie wykrywają interfejs i rejestrują zdarzenia zmiany. Jednostka jest odpowiedzialna za wywoływanie tych zdarzeń. 

> [!TIP]
> Aby dowiedzieć się więcej na temat sposobu obsługi zmian, Przeczytaj: [jak zaimplementować powiadomienie o zmianie właściwości](/dotnet/framework/wpf/data/how-to-implement-property-change-notification).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [konfigurowaniu DbContext](/ef/core/miscellaneous/configuring-dbcontext).