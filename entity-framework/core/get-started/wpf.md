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
# <a name="getting-started-with-wpf"></a><span data-ttu-id="0c236-102">Wprowadzenie do korzystania z platformy WPF</span><span class="sxs-lookup"><span data-stu-id="0c236-102">Getting Started with WPF</span></span>

<span data-ttu-id="0c236-103">W tym przewodniku krok po kroku pokazano, jak powiązać typy POCO z kontrolkami WPF w formularzu "Main-szczegóły".</span><span class="sxs-lookup"><span data-stu-id="0c236-103">This step-by-step walkthrough shows how to bind POCO types to WPF controls in a "main-detail" form.</span></span> <span data-ttu-id="0c236-104">Aplikacja używa Entity Framework interfejsów API do wypełniania obiektów danymi z bazy danych, śledzenia zmian i utrwalania danych w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="0c236-104">The application uses the Entity Framework APIs to populate objects with data from the database, track changes, and persist data to the database.</span></span>

<span data-ttu-id="0c236-105">Model definiuje dwa typy, które uczestniczą w relacji jeden do wielu: **kategorii** (głównej główna \\ ) i **produktu** (szczegóły zależne \\ ).</span><span class="sxs-lookup"><span data-stu-id="0c236-105">The model defines two types that participate in one-to-many relationship: **Category** (principal\\main) and **Product** (dependent\\detail).</span></span> <span data-ttu-id="0c236-106">Struktura powiązań danych WPF umożliwia nawigowanie między obiektami pokrewnymi: wybranie wierszy w widoku wzorca powoduje aktualizację widoku szczegółów z odpowiednimi danymi podrzędnymi.</span><span class="sxs-lookup"><span data-stu-id="0c236-106">The WPF data-binding framework enables navigation between related objects: selecting rows in the master view causes the detail view to update with the corresponding child data.</span></span>

<span data-ttu-id="0c236-107">Zrzuty ekranu i listy kodu w tym instruktażu są pobierane z programu Visual Studio 2019 16.6.5.</span><span class="sxs-lookup"><span data-stu-id="0c236-107">The screen shots and code listings in this walkthrough are taken from Visual Studio 2019 16.6.5.</span></span>

> [!TIP]
> <span data-ttu-id="0c236-108">Przykład tego artykułu można wyświetlić [w witrynie GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/WPF).</span><span class="sxs-lookup"><span data-stu-id="0c236-108">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/WPF).</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="0c236-109">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="0c236-109">Pre-Requisites</span></span>

* <span data-ttu-id="0c236-110">Aby ukończyć ten przewodnik, musisz mieć zainstalowany program Visual Studio 2019 16,3 lub nowszy z wybranym **obciążeniem programu .NET Desktop** .</span><span class="sxs-lookup"><span data-stu-id="0c236-110">You need to have Visual Studio 2019 16.3 or later installed with the **.NET desktop workload** selected to complete this walkthrough.</span></span>
    
    <span data-ttu-id="0c236-111">Aby uzyskać więcej informacji na temat instalowania najnowszej wersji programu Visual Studio, zobacz [Instalowanie programu Visual Studio](/visualstudio/install/install-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="0c236-111">For more information about installing the latest version of Visual Studio, see [Install Visual Studio](/visualstudio/install/install-visual-studio).</span></span>

## <a name="create-the-application"></a><span data-ttu-id="0c236-112">Tworzenie aplikacji</span><span class="sxs-lookup"><span data-stu-id="0c236-112">Create the Application</span></span>

1. <span data-ttu-id="0c236-113">Otwórz program Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0c236-113">Open Visual Studio</span></span>
2. <span data-ttu-id="0c236-114">W oknie uruchamiania wybierz pozycję **Utwórz nowy projekt**.</span><span class="sxs-lookup"><span data-stu-id="0c236-114">On the start window, choose **Create new project**.</span></span>
3. <span data-ttu-id="0c236-115">Wyszukaj ciąg "WPF", wybierz pozycję **Aplikacja WPF (.NET Core)** , a następnie wybierz przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="0c236-115">Search for "WPF," choose **WPF App (.NET Core)** and then choose **Next**.</span></span>
4. <span data-ttu-id="0c236-116">Na następnym ekranie Nadaj projektowi nazwę, na przykład **GetStartedWPF**, i wybierz pozycję **Utwórz.**</span><span class="sxs-lookup"><span data-stu-id="0c236-116">At the next screen, give the project a name, for example, **GetStartedWPF**, and choose **Create.**</span></span>

## <a name="install-the-entity-framework-nuget-packages"></a><span data-ttu-id="0c236-117">Zainstaluj Entity Framework pakiety NuGet</span><span class="sxs-lookup"><span data-stu-id="0c236-117">Install the Entity Framework NuGet packages</span></span>

1. <span data-ttu-id="0c236-118">Kliknij prawym przyciskiem myszy rozwiązanie i wybierz pozycję **Zarządzaj pakietami NuGet dla rozwiązania...**</span><span class="sxs-lookup"><span data-stu-id="0c236-118">Right-click on the solution and choose **Manage NuGet Packages for Solution...**</span></span>

    ![Zarządzanie pakietami NuGet](_static/wpf-tutorial-nuget.jpg)

1. <span data-ttu-id="0c236-120">Wpisz `entityframeworkcore.sqlite` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="0c236-120">Type `entityframeworkcore.sqlite` in the search box.</span></span>
1. <span data-ttu-id="0c236-121">Wybierz pakiet **Microsoft. EntityFrameworkCore. sqlite** .</span><span class="sxs-lookup"><span data-stu-id="0c236-121">Select the **Microsoft.EntityFrameworkCore.Sqlite** package.</span></span>
1. <span data-ttu-id="0c236-122">Sprawdź projekt w okienku po prawej stronie, a następnie kliknij przycisk **Instaluj** .</span><span class="sxs-lookup"><span data-stu-id="0c236-122">Check the project in the right pane and click **Install**</span></span>

    ![Pakiet oprogramowania SQLite](_static/wpf-tutorial-sqlite.jpg)

1. <span data-ttu-id="0c236-124">Powtórz kroki, aby wyszukać `entityframeworkcore.proxies` i zainstalować **Microsoft. EntityFrameworkCore. proxy**.</span><span class="sxs-lookup"><span data-stu-id="0c236-124">Repeat the steps to search for `entityframeworkcore.proxies` and install **Microsoft.EntityFrameworkCore.Proxies**.</span></span>

> [!NOTE]
> <span data-ttu-id="0c236-125">Podczas instalacji pakietu SQLite automatycznie pobierany jest powiązany pakiet podstawowy **Microsoft. EntityFrameworkCore** .</span><span class="sxs-lookup"><span data-stu-id="0c236-125">When you installed the Sqlite package, it automatically pulled down the related **Microsoft.EntityFrameworkCore** base package.</span></span> <span data-ttu-id="0c236-126">Pakiet **Microsoft. EntityFrameworkCore. proxy** zapewnia obsługę danych "ładowanie z opóźnieniem".</span><span class="sxs-lookup"><span data-stu-id="0c236-126">The **Microsoft.EntityFrameworkCore.Proxies** package provides support for "lazy-loading" data.</span></span> <span data-ttu-id="0c236-127">Oznacza to, że w przypadku obiektów z jednostkami podrzędnymi pobierane są tylko elementy nadrzędne w początkowym obciążeniu.</span><span class="sxs-lookup"><span data-stu-id="0c236-127">This means when you have entities with child entities, only the parents are fetched on the initial load.</span></span> <span data-ttu-id="0c236-128">Serwery proxy wykrywają, kiedy nastąpi próba uzyskania dostępu do jednostek podrzędnych i ładuje je automatycznie na żądanie.</span><span class="sxs-lookup"><span data-stu-id="0c236-128">The proxies detect when an attempt to access the child entities is made and automatically loads them on demand.</span></span> 

## <a name="define-a-model"></a><span data-ttu-id="0c236-129">Zdefiniuj model</span><span class="sxs-lookup"><span data-stu-id="0c236-129">Define a Model</span></span>

<span data-ttu-id="0c236-130">W tym instruktażu zostanie wdrożony model przy użyciu "Code First".</span><span class="sxs-lookup"><span data-stu-id="0c236-130">In this walkthrough you will implement a model using "code first."</span></span> <span data-ttu-id="0c236-131">Oznacza to, że EF Core utworzy tabele bazy danych i schemat na podstawie zdefiniowanych klas języka C#.</span><span class="sxs-lookup"><span data-stu-id="0c236-131">This means that EF Core will create the database tables and schema based on the C# classes you define.</span></span>

<span data-ttu-id="0c236-132">Dodaj nową klasę.</span><span class="sxs-lookup"><span data-stu-id="0c236-132">Add a new class.</span></span> <span data-ttu-id="0c236-133">Nadaj jej nazwę: `Product.cs` i wypełnij ją następująco:</span><span class="sxs-lookup"><span data-stu-id="0c236-133">Give it the name: `Product.cs` and populate it like this:</span></span>

**`Product.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/Product.cs)]

<span data-ttu-id="0c236-134">Następnie Dodaj klasę o nazwie `Category.cs` i wypełnij ją następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="0c236-134">Next, add a class named `Category.cs` and populate it with the following code:</span></span>

**`Category.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/Category.cs)]

<span data-ttu-id="0c236-135">Właściwości **Products** klasy **Category** i **Category** klasy **Product** są właściwościami nawigacji.</span><span class="sxs-lookup"><span data-stu-id="0c236-135">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="0c236-136">W Entity Framework właściwości nawigacji umożliwiają nawigowanie po relacjach między dwoma typami jednostek.</span><span class="sxs-lookup"><span data-stu-id="0c236-136">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="0c236-137">Oprócz definiowania jednostek należy zdefiniować klasę, która dziedziczy z DbContext i uwidacznia &lt; &gt; Właściwości nieogólnymi.</span><span class="sxs-lookup"><span data-stu-id="0c236-137">In addition to defining entities, you need to define a class that derives from DbContext and exposes DbSet&lt;TEntity&gt; properties.</span></span> <span data-ttu-id="0c236-138">Właściwości Nieogólnymiości &lt; &gt; pozwalają określić, które typy mają być uwzględnione w modelu.</span><span class="sxs-lookup"><span data-stu-id="0c236-138">The DbSet&lt;TEntity&gt; properties let the context know which types you want to include in the model.</span></span>

<span data-ttu-id="0c236-139">Wystąpienie typu pochodnego DbContext zarządza obiektami obiektów w czasie wykonywania, co obejmuje wypełnianie obiektów danymi z bazy danych, śledzenie zmian i utrwalanie danych w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="0c236-139">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

<span data-ttu-id="0c236-140">Dodaj nową `ProductContext.cs` klasę do projektu z następującą definicją:</span><span class="sxs-lookup"><span data-stu-id="0c236-140">Add a new `ProductContext.cs` class to the project with the following definition:</span></span>

**`ProductContext.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/ProductContext.cs)]

* <span data-ttu-id="0c236-141">Informuje o tym, `DbSet` EF Core jakie jednostki języka C# mają być mapowane do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="0c236-141">The `DbSet` informs EF Core what C# entities should be mapped to the database.</span></span>
* <span data-ttu-id="0c236-142">Istnieją różne sposoby konfigurowania EF Core `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="0c236-142">There are a variety of ways to configure the EF Core `DbContext`.</span></span> <span data-ttu-id="0c236-143">Możesz przeczytać o nich w: [Konfigurowanie DbContext](/ef/core/miscellaneous/configuring-dbcontext).</span><span class="sxs-lookup"><span data-stu-id="0c236-143">You can read about them in: [Configuring a DbContext](/ef/core/miscellaneous/configuring-dbcontext).</span></span>
* <span data-ttu-id="0c236-144">W tym przykładzie zastosowano `OnConfiguring` zastąpienie, aby określić plik danych programu SQLite.</span><span class="sxs-lookup"><span data-stu-id="0c236-144">This example uses the `OnConfiguring` override to specify a Sqlite data file.</span></span>
* <span data-ttu-id="0c236-145">`UseLazyLoadingProxies`Wywołanie instruuje EF Core, aby zaimplementować ładowanie z opóźnieniem, więc jednostki podrzędne są automatycznie ładowane podczas uzyskiwania dostępu z elementu nadrzędnego.</span><span class="sxs-lookup"><span data-stu-id="0c236-145">The `UseLazyLoadingProxies` call tells EF Core to implement lazy-loading, so child entities are automatically loaded when accessed from the parent.</span></span>

<span data-ttu-id="0c236-146">Naciśnij **klawisze CTRL + SHIFT + B** lub przejdź do **kompilacji Kompiluj &gt; rozwiązanie** , aby skompilować projekt.</span><span class="sxs-lookup"><span data-stu-id="0c236-146">Press **CTRL+SHIFT+B** or navigate to **Build &gt; Build Solution** to compile the project.</span></span>

> [!TIP]
> <span data-ttu-id="0c236-147">Zapoznaj się z informacjami na temat innych sposobów, aby zachować synchronizację baz danych i modeli EF Core: [Zarządzanie schematami bazy danych](/ef/core/managing-schemas).</span><span class="sxs-lookup"><span data-stu-id="0c236-147">Learn about the different was to keep your database and EF Core models in sync: [Managing Database Schemas](/ef/core/managing-schemas).</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="0c236-148">Ładowanie z opóźnieniem</span><span class="sxs-lookup"><span data-stu-id="0c236-148">Lazy Loading</span></span>

<span data-ttu-id="0c236-149">Właściwości **Products** klasy **Category** i **Category** klasy **Product** są właściwościami nawigacji.</span><span class="sxs-lookup"><span data-stu-id="0c236-149">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="0c236-150">W Entity Framework Core właściwości nawigacji umożliwiają nawigowanie po relacjach między dwoma typami jednostek.</span><span class="sxs-lookup"><span data-stu-id="0c236-150">In Entity Framework Core, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="0c236-151">EF Core zapewnia opcję ładowania powiązanych jednostek z bazy danych automatycznie przy pierwszym dostępie do właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="0c236-151">EF Core gives you an option of loading related entities from the database automatically the first time you access the navigation property.</span></span> <span data-ttu-id="0c236-152">W przypadku tego typu ładowania (nazywanego ładowaniem opóźnionym) należy pamiętać, że podczas pierwszego uzyskiwania dostępu do każdej właściwości nawigacji oddzielne zapytanie zostanie wykonane względem bazy danych, jeśli zawartość nie jest jeszcze w kontekście.</span><span class="sxs-lookup"><span data-stu-id="0c236-152">With this type of loading (called lazy loading), be aware that the first time you access each navigation property a separate query will be executed against the database if the contents are not already in the context.</span></span>

<span data-ttu-id="0c236-153">W przypadku używania "zwykłego starego typu obiektu języka C#" (POCO), EF Core osiąga opóźnienie ładowania przez utworzenie wystąpień pochodnych typów proxy podczas wykonywania, a następnie Zastępowanie właściwości wirtualnych w klasach w celu dodania punktu zaczepienia ładowania.</span><span class="sxs-lookup"><span data-stu-id="0c236-153">When using "Plain Old C# Object" (POCO) entity types, EF Core achieves lazy loading by creating instances of derived proxy types during runtime and then overriding virtual properties in your classes to add the loading hook.</span></span> <span data-ttu-id="0c236-154">Aby uzyskać opóźnione ładowanie powiązanych obiektów, należy zadeklarować metody do pobierania właściwości nawigacji jako **publiczne** i **wirtualne** (Zastąp w Visual Basic), a Klasa nie może być **zapieczętowana** **(** **NotOverridable** w Visual Basic).</span><span class="sxs-lookup"><span data-stu-id="0c236-154">To get lazy loading of related objects, you must declare navigation property getters as **public** and **virtual** (**Overridable** in Visual Basic), and your class must not be **sealed** (**NotOverridable** in Visual Basic).</span></span> <span data-ttu-id="0c236-155">W przypadku korzystania z Database First właściwości nawigacji są automatycznie wprowadzane do wirtualnego, aby umożliwić ładowanie z opóźnieniem.</span><span class="sxs-lookup"><span data-stu-id="0c236-155">When using Database First, navigation properties are automatically made virtual to enable lazy loading.</span></span> 

## <a name="bind-object-to-controls"></a><span data-ttu-id="0c236-156">Powiąż obiekt z kontrolkami</span><span class="sxs-lookup"><span data-stu-id="0c236-156">Bind Object to Controls</span></span>

<span data-ttu-id="0c236-157">Dodaj klasy, które są zdefiniowane w modelu jako źródła danych dla tej aplikacji WPF.</span><span class="sxs-lookup"><span data-stu-id="0c236-157">Add the classes that are defined in the model as data sources for this WPF application.</span></span>

1. <span data-ttu-id="0c236-158">Kliknij dwukrotnie pozycję **MainWindow. XAML** w Eksplorator rozwiązań, aby otworzyć formularz główny.</span><span class="sxs-lookup"><span data-stu-id="0c236-158">Double-click **MainWindow.xaml** in Solution Explorer to open the main form</span></span>
1. <span data-ttu-id="0c236-159">Wybierz kartę **XAML** , aby edytować kod XAML.</span><span class="sxs-lookup"><span data-stu-id="0c236-159">Choose the **XAML** tab to edit the XAML.</span></span>
1. <span data-ttu-id="0c236-160">Zaraz po tagu otwierającym `Window` Dodaj następujące źródła, aby połączyć się z jednostkami EF Core.</span><span class="sxs-lookup"><span data-stu-id="0c236-160">Immediately after the opening `Window` tag, add the following sources to connect to the EF Core entities.</span></span>

    [!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=1-13&highlight=9-13)]

1. <span data-ttu-id="0c236-161">Powoduje to skonfigurowanie źródła dla kategorii "nadrzędny" i drugie źródło dla produktów "Szczegóły".</span><span class="sxs-lookup"><span data-stu-id="0c236-161">This sets up source for the "parent" categories, and second source for the "detail" products.</span></span>
1. <span data-ttu-id="0c236-162">Następnie Dodaj następujący znacznik do kodu XAML po tagu zamykającym `Window.Resources` .</span><span class="sxs-lookup"><span data-stu-id="0c236-162">Next, add the following markup to your XAML after the closing `Window.Resources` tag.</span></span>

    [!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=15-26)]

1. <span data-ttu-id="0c236-163">Należy pamiętać, że `CategoryId` jest ustawiona na, `ReadOnly` ponieważ jest przypisana przez bazę danych i nie można jej zmienić.</span><span class="sxs-lookup"><span data-stu-id="0c236-163">Note that the `CategoryId` is set to `ReadOnly` because it is assigned by the database and cannot be changed.</span></span>

## <a name="adding-a-details-grid"></a><span data-ttu-id="0c236-164">Dodawanie siatki szczegółów</span><span class="sxs-lookup"><span data-stu-id="0c236-164">Adding a Details Grid</span></span>

<span data-ttu-id="0c236-165">Teraz, gdy siatka istnieje do wyświetlania kategorii, można dodać siatkę szczegółów, aby pokazać produkty.</span><span class="sxs-lookup"><span data-stu-id="0c236-165">Now that the grid exists to display categories, the details grid can be added to show products.</span></span>

**`MainWindow.xaml`**

[!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=27-40)]

<span data-ttu-id="0c236-166">Na koniec Dodaj `Save` przycisk i przewód w zdarzeniu kliknięcia do `Button_Click` .</span><span class="sxs-lookup"><span data-stu-id="0c236-166">Finally, add a `Save` button and wire in the click event to `Button_Click`.</span></span>

[!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=41-42)]

<span data-ttu-id="0c236-167">Widok projektu powinien wyglądać następująco:</span><span class="sxs-lookup"><span data-stu-id="0c236-167">Your design view should look like this:</span></span>

![Zrzut ekranu projektanta WPF](_static/wpf-tutorial-designer.jpg)

## <a name="add-code-that-handles-data-interaction"></a><span data-ttu-id="0c236-169">Dodawanie kodu, który obsługuje interakcję z danymi</span><span class="sxs-lookup"><span data-stu-id="0c236-169">Add Code that Handles Data Interaction</span></span>

<span data-ttu-id="0c236-170">Czas na dodanie obsługi zdarzeń do okna głównego.</span><span class="sxs-lookup"><span data-stu-id="0c236-170">It's time to add some event handlers to the main window.</span></span>

1. <span data-ttu-id="0c236-171">W oknie XAML kliknij element \*\* &lt; okna &gt; \*\* , aby wybrać okno główne.</span><span class="sxs-lookup"><span data-stu-id="0c236-171">In the XAML window, click on the **&lt;Window&gt;** element, to select the main window.</span></span>
1. <span data-ttu-id="0c236-172">W oknie **Właściwości** wybierz pozycję **zdarzenia** w prawym górnym rogu, a następnie kliknij dwukrotnie pole tekstowe po prawej stronie **załadowanej** etykiety.</span><span class="sxs-lookup"><span data-stu-id="0c236-172">In the **Properties** window choose **Events** at the top right, then double-click the text box to right of the **Loaded** label.</span></span>

    ![Właściwości okna głównego](_static/wpf-tutorial-loaded.jpg)

<span data-ttu-id="0c236-174">Spowoduje to przeprowadzenie pracy z kodem związanym z formularzem. teraz edytujemy kod w celu korzystania z programu w `ProductContext` celu zapewnienia dostępu do danych.</span><span class="sxs-lookup"><span data-stu-id="0c236-174">This brings you to the code behind for the form, we'll now edit the code to use the `ProductContext` to perform data access.</span></span> <span data-ttu-id="0c236-175">Zaktualizuj kod, jak pokazano poniżej.</span><span class="sxs-lookup"><span data-stu-id="0c236-175">Update the code as shown below.</span></span>

<span data-ttu-id="0c236-176">Kod deklaruje długotrwałe wystąpienie `ProductContext` .</span><span class="sxs-lookup"><span data-stu-id="0c236-176">The code declares a long-running instance of `ProductContext`.</span></span> <span data-ttu-id="0c236-177">`ProductContext`Obiekt jest używany do wykonywania zapytań i zapisywania danych w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="0c236-177">The `ProductContext` object is used to query and save data to the database.</span></span> <span data-ttu-id="0c236-178">`Dispose()`Metoda na tym `ProductContext` wystąpieniu jest następnie wywoływana z przesłoniętej `OnClosing` metody.</span><span class="sxs-lookup"><span data-stu-id="0c236-178">The `Dispose()` method on the `ProductContext` instance is then called from the overridden `OnClosing` method.</span></span><span data-ttu-id="0c236-179">Komentarze do kodu wyjaśniają, co robi każdy krok.</span><span class="sxs-lookup"><span data-stu-id="0c236-179"> The code comments explain what each step does.</span></span>

**`MainWindow.xaml.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml.cs)]

> [!NOTE]
> <span data-ttu-id="0c236-180">Kod używa wywołania do `EnsureCreated()` kompilowania bazy danych przy pierwszym uruchomieniu.</span><span class="sxs-lookup"><span data-stu-id="0c236-180">The code uses a call to `EnsureCreated()` to build the database on the first run.</span></span> <span data-ttu-id="0c236-181">Jest to akceptowalne dla pokazów, ale w aplikacjach produkcyjnych należy przeglądać [migracje](/ef/core/managing-schemas/migrations/) w celu zarządzania schematem.</span><span class="sxs-lookup"><span data-stu-id="0c236-181">This is acceptable for demos, but in production apps you should look at [migrations](/ef/core/managing-schemas/migrations/) to manage your schema.</span></span> <span data-ttu-id="0c236-182">Kod jest również wykonywany synchronicznie, ponieważ używa lokalnej bazy danych programu SQLite.</span><span class="sxs-lookup"><span data-stu-id="0c236-182">The code also executes synchronously because it uses a local SQLite database.</span></span> <span data-ttu-id="0c236-183">W przypadku scenariuszy produkcyjnych, które zwykle obejmują serwer zdalny, należy rozważyć użycie asynchronicznych wersji `Load` `SaveChanges` metod i.</span><span class="sxs-lookup"><span data-stu-id="0c236-183">For production scenarios that typically involve a remote server, consider using the asynchronous versions of the `Load` and `SaveChanges` methods.</span></span>

## <a name="test-the-wpf-application"></a><span data-ttu-id="0c236-184">Testowanie aplikacji WPF</span><span class="sxs-lookup"><span data-stu-id="0c236-184">Test the WPF Application</span></span>

<span data-ttu-id="0c236-185">Skompiluj i uruchom aplikację, naciskając klawisz **F5** lub wybierając pozycję **Debuguj &gt; Rozpocznij debugowanie**.</span><span class="sxs-lookup"><span data-stu-id="0c236-185">Compile and run the application by pressing **F5** or choosing **Debug &gt; Start Debugging**.</span></span> <span data-ttu-id="0c236-186">Baza danych powinna zostać utworzona automatycznie przy użyciu pliku o nazwie `products.db` .</span><span class="sxs-lookup"><span data-stu-id="0c236-186">The database should be automatically created with a file named `products.db`.</span></span> <span data-ttu-id="0c236-187">Wprowadź nazwę kategorii i naciśnij klawisz ENTER, a następnie Dodaj produkty do dolnej siatki.</span><span class="sxs-lookup"><span data-stu-id="0c236-187">Enter a category name and hit enter, then add products to the lower grid.</span></span> <span data-ttu-id="0c236-188">Kliknij przycisk Zapisz i obejrzyj odświeżanie siatki przy użyciu dostarczonych identyfikatorów bazy danych.</span><span class="sxs-lookup"><span data-stu-id="0c236-188">Click save and watch the grid refresh with the database provided ids.</span></span> <span data-ttu-id="0c236-189">Zaznacz wiersz i naciśnij klawisz **delete** , aby usunąć wiersz.</span><span class="sxs-lookup"><span data-stu-id="0c236-189">Highlight a row and hit **Delete** to remove the row.</span></span> <span data-ttu-id="0c236-190">Jednostka zostanie usunięta po kliknięciu przycisku **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="0c236-190">The entity will be deleted when you click **Save**.</span></span>

![Uruchomiona aplikacja](_static/wpf-tutorial-app.jpg)

## <a name="property-change-notification"></a><span data-ttu-id="0c236-192">Powiadomienie o zmianie właściwości</span><span class="sxs-lookup"><span data-stu-id="0c236-192">Property Change Notification</span></span>

<span data-ttu-id="0c236-193">Ten przykład polega na czterech krokach w celu zsynchronizowania jednostek z interfejsem użytkownika.</span><span class="sxs-lookup"><span data-stu-id="0c236-193">This example relies on four steps to synchronize the entities with the UI.</span></span> 

1. <span data-ttu-id="0c236-194">Początkowe wywołanie `_context.Categories.Load()` ładuje dane kategorii.</span><span class="sxs-lookup"><span data-stu-id="0c236-194">The initial call `_context.Categories.Load()` loads the categories data.</span></span>
1. <span data-ttu-id="0c236-195">Ładowane z opóźnieniem serwery proxy ładują dane zależnych produktów.</span><span class="sxs-lookup"><span data-stu-id="0c236-195">The lazy-loading proxies load the dependent products data.</span></span>
1. <span data-ttu-id="0c236-196">Wbudowane śledzenie zmian EF Core wprowadza odpowiednie modyfikacje jednostek, w tym wstawienia i usunięcia, gdy `_context.SaveChanges()` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="0c236-196">EF Core's built-in change tracking makes the necessary modifications to entities, including insertions and deletions, when `_context.SaveChanges()` is called.</span></span>
1. <span data-ttu-id="0c236-197">Wywołania, aby `DataGridView.Items.Refresh()` wymusić ponowne załadowanie z nowo wygenerowanymi identyfikatorami.</span><span class="sxs-lookup"><span data-stu-id="0c236-197">The calls to `DataGridView.Items.Refresh()` force a reload with the newly generated ids.</span></span>

<span data-ttu-id="0c236-198">Działa to w przypadku przykładu wprowadzenie, ale może być potrzebny dodatkowy kod dla innych scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="0c236-198">This works for our getting started sample, but you may require additional code for other scenarios.</span></span> <span data-ttu-id="0c236-199">Formanty WPF renderują interfejs użytkownika, odczytując pola i właściwości w jednostkach.</span><span class="sxs-lookup"><span data-stu-id="0c236-199">WPF controls render the UI by reading the fields and properties on your entities.</span></span> <span data-ttu-id="0c236-200">W przypadku edycji wartości w interfejsie użytkownika (UI) ta wartość jest przesyłana do jednostki.</span><span class="sxs-lookup"><span data-stu-id="0c236-200">When you edit a value in the user interface (UI), that value is passed to your entity.</span></span> <span data-ttu-id="0c236-201">Gdy zmieniasz wartość właściwości bezpośrednio w jednostce, na przykład ładując ją z bazy danych, WPF nie będzie natychmiast odzwierciedlać zmian wprowadzonych w interfejsie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="0c236-201">When you change the value of a property directly on your entity, such as loading it from the database, WPF will not immediately reflect the changes in the UI.</span></span> <span data-ttu-id="0c236-202">Aparat renderowania musi zostać powiadomiony o zmianach.</span><span class="sxs-lookup"><span data-stu-id="0c236-202">The rendering engine must be notified of the changes.</span></span> <span data-ttu-id="0c236-203">Projekt został spowodowany przez ręczne wywołanie `Refresh()` .</span><span class="sxs-lookup"><span data-stu-id="0c236-203">The project did this by manually calling `Refresh()`.</span></span> <span data-ttu-id="0c236-204">Łatwym sposobem automatyzacji tego powiadomienia jest zaimplementowanie interfejsu [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged) .</span><span class="sxs-lookup"><span data-stu-id="0c236-204">An easy way to automate this notification is by implementing the [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged) interface.</span></span> <span data-ttu-id="0c236-205">Składniki WPF automatycznie wykrywają interfejs i rejestrują zdarzenia zmiany.</span><span class="sxs-lookup"><span data-stu-id="0c236-205">WPF components will automatically detect the interface and register for change events.</span></span> <span data-ttu-id="0c236-206">Jednostka jest odpowiedzialna za wywoływanie tych zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="0c236-206">The entity is responsible for raising these events.</span></span> 

> [!TIP]
> <span data-ttu-id="0c236-207">Aby dowiedzieć się więcej na temat sposobu obsługi zmian, Przeczytaj: [jak zaimplementować powiadomienie o zmianie właściwości](/dotnet/framework/wpf/data/how-to-implement-property-change-notification).</span><span class="sxs-lookup"><span data-stu-id="0c236-207">To learn more about how to handle changes, read: [How to implement property change notification](/dotnet/framework/wpf/data/how-to-implement-property-change-notification).</span></span>

## <a name="next-steps"></a><span data-ttu-id="0c236-208">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="0c236-208">Next Steps</span></span>

<span data-ttu-id="0c236-209">Dowiedz się więcej o [konfigurowaniu DbContext](/ef/core/miscellaneous/configuring-dbcontext).</span><span class="sxs-lookup"><span data-stu-id="0c236-209">Learn more about [Configuring a DbContext](/ef/core/miscellaneous/configuring-dbcontext).</span></span>