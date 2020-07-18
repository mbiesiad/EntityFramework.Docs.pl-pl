---
title: Wprowadzenie z EF Core i Xamarin-EF Core
author: codemillmatt
ms.date: 07/07/2020
ms.author: masoucou
uid: core/get-started/xamarin
ms.openlocfilehash: 14fe61e0ddd648f824bfe3c1c91a58af91b1c2d4
ms.sourcegitcommit: d85263b5d5d665dbaf94de8832e2917bce048b34
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2020
ms.locfileid: "86453053"
---
# <a name="getting-started-with-ef-core-and-xamarin"></a><span data-ttu-id="da20e-102">Wprowadzenie z EF Core i Xamarin</span><span class="sxs-lookup"><span data-stu-id="da20e-102">Getting Started with EF Core and Xamarin</span></span>

<span data-ttu-id="da20e-103">W tym samouczku utworzysz aplikację platformy [Xamarin. Forms](/xamarin/get-started/what-is-xamarin-forms) , która wykonuje dostęp do danych w bazie danych programu SQLite przy użyciu Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="da20e-103">In this tutorial, you create a [Xamarin.Forms](/xamarin/get-started/what-is-xamarin-forms) application that performs data access against a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="da20e-104">Możesz wykonać czynności opisane w samouczku za pomocą programu Visual Studio w systemie Windows lub Visual Studio dla komputerów Mac.</span><span class="sxs-lookup"><span data-stu-id="da20e-104">You can follow the tutorial by using Visual Studio on Windows or Visual Studio for Mac.</span></span>

> [!TIP]
> <span data-ttu-id="da20e-105">Przykład tego artykułu można wyświetlić [w witrynie GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Xamarin).</span><span class="sxs-lookup"><span data-stu-id="da20e-105">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Xamarin).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="da20e-106">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="da20e-106">Prerequisites</span></span>

<span data-ttu-id="da20e-107">Zainstaluj jedną z poniższych czynności:</span><span class="sxs-lookup"><span data-stu-id="da20e-107">Install one of the below:</span></span>

* <span data-ttu-id="da20e-108">[Program Visual Studio 2019 w wersji 16,3 lub nowszej](https://www.visualstudio.com/downloads/) z tym obciążeniem:</span><span class="sxs-lookup"><span data-stu-id="da20e-108">[Visual Studio 2019 version 16.3 or later](https://www.visualstudio.com/downloads/) with this workload:</span></span>
  * <span data-ttu-id="da20e-109">**Programowanie aplikacji mobilnych za pomocą platformy .NET**</span><span class="sxs-lookup"><span data-stu-id="da20e-109">**Mobile Development with .NET**</span></span>
* [<span data-ttu-id="da20e-110">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="da20e-110">Visual Studio for Mac</span></span>](https://visualstudio.microsoft.com/vs/mac/)

<span data-ttu-id="da20e-111">Ta [Dokumentacja zawiera szczegółowe instrukcje dotyczące instalacji krok po kroku](/xamarin/get-started/installation) dla każdej platformy.</span><span class="sxs-lookup"><span data-stu-id="da20e-111">This [documentation provides detailed step-by-step installation instructions](/xamarin/get-started/installation) for each platform.</span></span>

## <a name="download-and-run-the-sample-project"></a><span data-ttu-id="da20e-112">Pobieranie i uruchamianie przykładowego projektu</span><span class="sxs-lookup"><span data-stu-id="da20e-112">Download and run the sample project</span></span>

<span data-ttu-id="da20e-113">Aby uruchomić i eksplorować tę przykładową aplikację, Pobierz kod w witrynie GitHub.</span><span class="sxs-lookup"><span data-stu-id="da20e-113">To run and explore this sample application, download the code on GitHub.</span></span>

<span data-ttu-id="da20e-114">Po pobraniu otwórz plik rozwiązania `EFGettingStarted.sln` w programie Visual Studio lub Visual Studio dla komputerów Mac i uruchom aplikację na wybranej platformie.</span><span class="sxs-lookup"><span data-stu-id="da20e-114">Once downloaded, open the solution file `EFGettingStarted.sln` in Visual Studio or Visual Studio for Mac and run the application on the platform of your choice.</span></span>

<span data-ttu-id="da20e-115">Po pierwszym uruchomieniu aplikacji zostanie wypełniona lokalna baza danych programu SQLite z dwoma wpisami reprezentującymi blogi.</span><span class="sxs-lookup"><span data-stu-id="da20e-115">When the app first starts, it will populate the local SQLite database with two entries representing blogs.</span></span>

![Zrzut ekranu przedstawiający stronę listy wszystkie blogi](_static/xamarin-tutorial-1.png)

<span data-ttu-id="da20e-117">Kliknij przycisk **Dodaj** na pasku narzędzi.</span><span class="sxs-lookup"><span data-stu-id="da20e-117">Click the **Add** button in the toolbar.</span></span>

<span data-ttu-id="da20e-118">Zostanie wyświetlona nowa strona, która umożliwia wprowadzanie informacji o nowym blogu.</span><span class="sxs-lookup"><span data-stu-id="da20e-118">A new page will appear that allows you to enter information about a new blog.</span></span>

![Zrzut ekranu przedstawiający nową stronę edytowania blogu](_static/xamarin-tutorial-2.png)

<span data-ttu-id="da20e-120">Wypełnij wszystkie informacje i kliknij przycisk **Zapisz** na pasku narzędzi.</span><span class="sxs-lookup"><span data-stu-id="da20e-120">Fill out all the info and click **Save** from the toolbar.</span></span> <span data-ttu-id="da20e-121">Nowy blog zostanie zapisany w bazie danych programu SQLite aplikacji i będzie widoczny na liście.</span><span class="sxs-lookup"><span data-stu-id="da20e-121">The new blog will save to the app's SQLite database and will show in the list.</span></span>

<span data-ttu-id="da20e-122">Możesz kliknąć jeden z wpisów w blogu na liście i zobaczyć wszystkie wpisy dla tego blogu.</span><span class="sxs-lookup"><span data-stu-id="da20e-122">You can click on one of the blog entries in the list and see any posts for that blog.</span></span>

![Zrzut ekranu strony listy wpisów w blogu](_static/xamarin-tutorial-3.png)

<span data-ttu-id="da20e-124">Kliknij przycisk **Dodaj** na pasku narzędzi.</span><span class="sxs-lookup"><span data-stu-id="da20e-124">Click **Add** in the toolbar.</span></span>

<span data-ttu-id="da20e-125">Zostanie wyświetlona strona, która umożliwia wypełnienie informacji o nowym wpisie w blogu.</span><span class="sxs-lookup"><span data-stu-id="da20e-125">A page then appears that allows you to fill out information about a new blog post.</span></span>

![Zrzut ekranu przedstawiający stronę Dodawanie nowej strony post](_static/xamarin-tutorial-4.png)

<span data-ttu-id="da20e-127">Wypełnij wszystkie informacje i kliknij przycisk **Zapisz** na pasku narzędzi.</span><span class="sxs-lookup"><span data-stu-id="da20e-127">Fill out all the information and click **Save** in the toolbar.</span></span>

<span data-ttu-id="da20e-128">Nowy wpis zostanie skojarzony z wpisem w blogu, który został kliknięty w poprzednim kroku i zostanie zapisany w bazie danych programu SQLite aplikacji i wyświetlony na liście.</span><span class="sxs-lookup"><span data-stu-id="da20e-128">The new post will be associated to the blog post you clicked on in a previous step and will save to the app's SQLite database and show in the list.</span></span>

<span data-ttu-id="da20e-129">Wróć do strony listy blogów.</span><span class="sxs-lookup"><span data-stu-id="da20e-129">Go back to the blog list page.</span></span> <span data-ttu-id="da20e-130">I kliknij pozycję **Usuń wszystko** na pasku narzędzi.</span><span class="sxs-lookup"><span data-stu-id="da20e-130">And click **Delete All** in the toolbar.</span></span> <span data-ttu-id="da20e-131">Wszystkie blogi i odpowiadające im wpisy zostaną usunięte z bazy danych programu SQLite aplikacji.</span><span class="sxs-lookup"><span data-stu-id="da20e-131">All blogs and their corresponding posts will then be deleted from the app's SQLite database.</span></span>

![Zrzut ekranu aplikacji z usuniętymi wszystkimi blogami](_static/xamarin-tutorial-5.png)

## <a name="explore-the-code"></a><span data-ttu-id="da20e-133">Eksplorowanie kodu</span><span class="sxs-lookup"><span data-stu-id="da20e-133">Explore the code</span></span>

<span data-ttu-id="da20e-134">Poniższe sekcje przeprowadzą Cię przez kod w przykładowym projekcie, który odczytuje, tworzy, aktualizuje i usuwa dane z bazy danych programu SQLite przy użyciu EF Core za pomocą platformy Xamarin. Forms.</span><span class="sxs-lookup"><span data-stu-id="da20e-134">The following sections will walk you through the code in the sample project that reads, creates, updates, and deletes data from a SQLite database using EF Core with Xamarin.Forms.</span></span>

<span data-ttu-id="da20e-135">Przyjęto założenie, że znasz już tematy dotyczące [wyświetlania danych](/xamarin/xamarin-forms/app-fundamentals/data-binding/) i [nawigowania między stronami](/xamarin/xamarin-forms/app-fundamentals/navigation/).</span><span class="sxs-lookup"><span data-stu-id="da20e-135">It is assumed that you are familiar with the Xamarin.Forms topics of [displaying data](/xamarin/xamarin-forms/app-fundamentals/data-binding/) and [navigating between pages](/xamarin/xamarin-forms/app-fundamentals/navigation/).</span></span>

## <a name="entity-framework-core-nuget-packages"></a><span data-ttu-id="da20e-136">Entity Framework Core pakietów NuGet</span><span class="sxs-lookup"><span data-stu-id="da20e-136">Entity Framework Core NuGet packages</span></span>

<span data-ttu-id="da20e-137">Aby utworzyć aplikacje platformy Xamarin. Forms za pomocą EF Core, należy zainstalować pakiet dla dostawców bazy danych EF Core, które mają być przeznaczone do wszystkich projektów w rozwiązaniu Xamarin. Forms.</span><span class="sxs-lookup"><span data-stu-id="da20e-137">To create Xamarin.Forms apps with EF Core, you install the package for the EF Core database provider(s) you want to target into all of the projects in the Xamarin.Forms solution.</span></span> <span data-ttu-id="da20e-138">W tym samouczku jest stosowany dostawca oprogramowania SQLite.</span><span class="sxs-lookup"><span data-stu-id="da20e-138">This tutorial uses the SQLite provider.</span></span>

<span data-ttu-id="da20e-139">W każdym projekcie w rozwiązaniu Xamarin. Forms jest wymagany następujący pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="da20e-139">The following NuGet package is needed in each of the projects in the Xamarin.Forms solution.</span></span>

* `Microsoft.EntityFrameworkCore.Sqlite`

## <a name="model-classes"></a><span data-ttu-id="da20e-140">Klasy modelu</span><span class="sxs-lookup"><span data-stu-id="da20e-140">Model classes</span></span>

<span data-ttu-id="da20e-141">Każda tabela w bazie danych programu SQLite, do której można uzyskać dostęp za pomocą EF Core, jest modelowana w klasie.</span><span class="sxs-lookup"><span data-stu-id="da20e-141">Each table in the SQLite database accessed through EF Core is modeled in a class.</span></span> <span data-ttu-id="da20e-142">W tym przykładzie używane są dwie klasy: i, które znajdują się `Blog` `Post` w `Models` folderze.</span><span class="sxs-lookup"><span data-stu-id="da20e-142">In this sample, two classes are used: `Blog` and `Post` which can be found in the `Models` folder.</span></span>

<span data-ttu-id="da20e-143">Klasy modelu składają się tylko z właściwości, które są kolumnami modelu w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="da20e-143">The model classes are composed only of properties, which model columns in the database.</span></span>

* <span data-ttu-id="da20e-144">**Blog.cs**</span><span class="sxs-lookup"><span data-stu-id="da20e-144">**Blog.cs**</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Blog.cs)]

* <span data-ttu-id="da20e-145">`Posts`Właściwość definiuje relację nadrzędny-podrzędny między `Blog` i `Post` .</span><span class="sxs-lookup"><span data-stu-id="da20e-145">The `Posts` property defines a parent-child relationship between `Blog` and `Post`.</span></span>

* <span data-ttu-id="da20e-146">**Post.cs**</span><span class="sxs-lookup"><span data-stu-id="da20e-146">**Post.cs**</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Post.cs)]

* <span data-ttu-id="da20e-147">`BlogId`Właściwości i `Blog` odnoszą się do obiektu nadrzędnego `Blog` dla wystąpienia elementu `Post` .</span><span class="sxs-lookup"><span data-stu-id="da20e-147">The `BlogId` and `Blog` properties relate back to the parent `Blog` object for the instance of the `Post`.</span></span>

## <a name="data-context"></a><span data-ttu-id="da20e-148">Kontekst danych</span><span class="sxs-lookup"><span data-stu-id="da20e-148">Data context</span></span>

<span data-ttu-id="da20e-149">Klasa znajduje się `BloggingContext` w `Services` folderze i dziedziczy z `DbContext` klasy EF Core.</span><span class="sxs-lookup"><span data-stu-id="da20e-149">The `BloggingContext` class is located in the `Services` folder and inherits from the EF Core `DbContext` class.</span></span> <span data-ttu-id="da20e-150">A służy `DbContext` do grupowania zapytań bazy danych i zmian.</span><span class="sxs-lookup"><span data-stu-id="da20e-150">A `DbContext` is used to group together database queries and changes.</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Services/BloggingContext.cs)]

* <span data-ttu-id="da20e-151">Obie właściwości w tej klasie typu `DbSet` są używane do działania na podstawowych tabelach reprezentujących blogi i wpisy.</span><span class="sxs-lookup"><span data-stu-id="da20e-151">Both properties in this class of type `DbSet` are used to operate on the underlying tables representing Blogs and Posts.</span></span>
* <span data-ttu-id="da20e-152">`SQLitePCL.Batteries_V2.Init()`Jest to konieczne w konstruktorze w celu zainicjowania oprogramowania SQLite w systemie iOS.</span><span class="sxs-lookup"><span data-stu-id="da20e-152">The `SQLitePCL.Batteries_V2.Init()` is needed in the constructor to initiate SQLite on iOS.</span></span>
* <span data-ttu-id="da20e-153">`OnConfiguring`Funkcja konfiguruje lokalizację bazy danych programu SQLite na urządzeniu fizycznym.</span><span class="sxs-lookup"><span data-stu-id="da20e-153">The `OnConfiguring` function sets up the location of the SQLite database on the physical device.</span></span>

## <a name="create-read-update--delete"></a><span data-ttu-id="da20e-154">Tworzenie, odczytywanie, aktualizowanie & usuwanie</span><span class="sxs-lookup"><span data-stu-id="da20e-154">Create, read, update & delete</span></span>

<span data-ttu-id="da20e-155">Poniżej przedstawiono niektóre wystąpienia w aplikacji, w których EF Core jest używany do uzyskiwania dostępu do oprogramowania SQLite.</span><span class="sxs-lookup"><span data-stu-id="da20e-155">The following are some instances in the app where EF Core is used to access SQLite.</span></span>

### <a name="read"></a><span data-ttu-id="da20e-156">Odczyt</span><span class="sxs-lookup"><span data-stu-id="da20e-156">Read</span></span>

* <span data-ttu-id="da20e-157">Zwróć wszystkie rekordy.</span><span class="sxs-lookup"><span data-stu-id="da20e-157">Return all records.</span></span>
  * <span data-ttu-id="da20e-158">`OnAppearing`Funkcja `BlogsPage.xaml.cs` zwraca wszystkie `Blog` rekordy i zapisuje je w `List` zmiennej.</span><span class="sxs-lookup"><span data-stu-id="da20e-158">The `OnAppearing` function of `BlogsPage.xaml.cs` returns all `Blog` records and stores them into a `List` variable.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    var theBlogs = blogContext.Blogs.ToList();
}
```

* <span data-ttu-id="da20e-159">Zwróć określone rekordy.</span><span class="sxs-lookup"><span data-stu-id="da20e-159">Return specific records.</span></span>
  * <span data-ttu-id="da20e-160">`OnAppearing`Funkcja `PostsPage.xaml.cs` zwraca `Post` rekordy, które zawierają określony `BlogId` .</span><span class="sxs-lookup"><span data-stu-id="da20e-160">The `OnAppearing` function of `PostsPage.xaml.cs` returns `Post` records that contain a specific `BlogId`.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    var postList = blogContext.Posts
        .Where(p => p.BlogId == BlogId)
        .ToList();
}
```

### <a name="create"></a><span data-ttu-id="da20e-161">Utwórz</span><span class="sxs-lookup"><span data-stu-id="da20e-161">Create</span></span>

* <span data-ttu-id="da20e-162">Wstaw nowy rekord.</span><span class="sxs-lookup"><span data-stu-id="da20e-162">Insert a new record.</span></span>
  * <span data-ttu-id="da20e-163">`Save_Clicked`Funkcja `AddBlogPage.xaml.cs` wstawia nowy `Blog` obiekt do bazy danych programu SQLite.</span><span class="sxs-lookup"><span data-stu-id="da20e-163">The `Save_Clicked` function of `AddBlogPage.xaml.cs` inserts a new `Blog` object into the SQLite database.</span></span>

```csharp
var blog = new Blog { Url = blogUrl.Text };

using (var blogContext = new BloggingContext())
{
    blogContext.Add(blog);

    await blogContext.SaveChangesAsync();
}
```

### <a name="update"></a><span data-ttu-id="da20e-164">Aktualizacja</span><span class="sxs-lookup"><span data-stu-id="da20e-164">Update</span></span>

* <span data-ttu-id="da20e-165">Aktualizowanie istniejącego rekordu.</span><span class="sxs-lookup"><span data-stu-id="da20e-165">Update an existing record.</span></span>
  * <span data-ttu-id="da20e-166">`Save_Clicked`Funkcja `AddPostPage.xaml.cs` aktualizuje istniejący `Blog` obiekt z nowym `Post` .</span><span class="sxs-lookup"><span data-stu-id="da20e-166">The `Save_Clicked` function of `AddPostPage.xaml.cs` updates an existing `Blog` object with a new `Post`.</span></span>
  
```csharp
var newPost = new Post
{
    BlogId = BlogId,
    Content = postCell.Text,
    Title = titleCell.Text
};

using (var blogContext = new BloggingContext())
{
    var blog = await blogContext
        .Blogs
        .FirstAsync(b => b.BlogId == BlogId);

    blog.Posts.Add(newPost);

    await blogContext.SaveChangesAsync();
}
```

### <a name="delete"></a><span data-ttu-id="da20e-167">Usuń</span><span class="sxs-lookup"><span data-stu-id="da20e-167">Delete</span></span>

* <span data-ttu-id="da20e-168">Usuń wszystkie rekordy z rekordami kaskadowymi i podrzędnymi.</span><span class="sxs-lookup"><span data-stu-id="da20e-168">Delete all records with cascade to child records.</span></span>
  * <span data-ttu-id="da20e-169">`DeleteAll_Clicked`Funkcja `BlogsPage.xaml.cs` usuwania wszystkich `Blog` rekordów w bazie danych programu SQLite i kaskaduje usunięcia do wszystkich `Blog` `Post` rekordów podrzędnych.</span><span class="sxs-lookup"><span data-stu-id="da20e-169">The `DeleteAll_Clicked` function of `BlogsPage.xaml.cs` deletes all the `Blog` records in the SQLite database and cascades the deletes to all of the `Blog` child `Post` records.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    blogContext.RemoveRange(blogContext.Blogs);

    await blogContext.SaveChangesAsync();
}
```

## <a name="next-steps"></a><span data-ttu-id="da20e-170">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="da20e-170">Next steps</span></span>

<span data-ttu-id="da20e-171">W tym temacie dowiesz się, jak używać aplikacji platformy Xamarin. Forms do uzyskiwania dostępu do bazy danych programu SQLite przy użyciu Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="da20e-171">In this getting started you have learned how to use a Xamarin.Forms application to access a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="da20e-172">Inne tematy Entity Framework Core zainteresowania deweloperów platformy Xamarin:</span><span class="sxs-lookup"><span data-stu-id="da20e-172">Other Entity Framework Core topics of interest to Xamarin developers:</span></span>

* [<span data-ttu-id="da20e-173">Konfigurowanie`DbContext`</span><span class="sxs-lookup"><span data-stu-id="da20e-173">Configuring a `DbContext`</span></span>](xref:core/miscellaneous/configuring-dbcontext)
* <span data-ttu-id="da20e-174">Dowiedz się więcej o [wyrażeniach zapytań LINQ](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span><span class="sxs-lookup"><span data-stu-id="da20e-174">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
* <span data-ttu-id="da20e-175">[Skonfiguruj model](xref:core/modeling/index) , aby określić elementy, takie jak [wymagana](xref:core/modeling/entity-properties#required-and-optional-properties) i [Maksymalna długość](xref:core/modeling/entity-properties#maximum-length)</span><span class="sxs-lookup"><span data-stu-id="da20e-175">[Configure your model](xref:core/modeling/index) to specify things like [required](xref:core/modeling/entity-properties#required-and-optional-properties) and [maximum length](xref:core/modeling/entity-properties#maximum-length)</span></span>
