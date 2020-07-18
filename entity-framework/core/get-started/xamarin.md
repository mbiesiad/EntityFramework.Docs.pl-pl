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
# <a name="getting-started-with-ef-core-and-xamarin"></a>Wprowadzenie z EF Core i Xamarin

W tym samouczku utworzysz aplikację platformy [Xamarin. Forms](/xamarin/get-started/what-is-xamarin-forms) , która wykonuje dostęp do danych w bazie danych programu SQLite przy użyciu Entity Framework Core.

Możesz wykonać czynności opisane w samouczku za pomocą programu Visual Studio w systemie Windows lub Visual Studio dla komputerów Mac.

> [!TIP]
> Przykład tego artykułu można wyświetlić [w witrynie GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Xamarin).

## <a name="prerequisites"></a>Wymagania wstępne

Zainstaluj jedną z poniższych czynności:

* [Program Visual Studio 2019 w wersji 16,3 lub nowszej](https://www.visualstudio.com/downloads/) z tym obciążeniem:
  * **Programowanie aplikacji mobilnych za pomocą platformy .NET**
* [Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/)

Ta [Dokumentacja zawiera szczegółowe instrukcje dotyczące instalacji krok po kroku](/xamarin/get-started/installation) dla każdej platformy.

## <a name="download-and-run-the-sample-project"></a>Pobieranie i uruchamianie przykładowego projektu

Aby uruchomić i eksplorować tę przykładową aplikację, Pobierz kod w witrynie GitHub.

Po pobraniu otwórz plik rozwiązania `EFGettingStarted.sln` w programie Visual Studio lub Visual Studio dla komputerów Mac i uruchom aplikację na wybranej platformie.

Po pierwszym uruchomieniu aplikacji zostanie wypełniona lokalna baza danych programu SQLite z dwoma wpisami reprezentującymi blogi.

![Zrzut ekranu przedstawiający stronę listy wszystkie blogi](_static/xamarin-tutorial-1.png)

Kliknij przycisk **Dodaj** na pasku narzędzi.

Zostanie wyświetlona nowa strona, która umożliwia wprowadzanie informacji o nowym blogu.

![Zrzut ekranu przedstawiający nową stronę edytowania blogu](_static/xamarin-tutorial-2.png)

Wypełnij wszystkie informacje i kliknij przycisk **Zapisz** na pasku narzędzi. Nowy blog zostanie zapisany w bazie danych programu SQLite aplikacji i będzie widoczny na liście.

Możesz kliknąć jeden z wpisów w blogu na liście i zobaczyć wszystkie wpisy dla tego blogu.

![Zrzut ekranu strony listy wpisów w blogu](_static/xamarin-tutorial-3.png)

Kliknij przycisk **Dodaj** na pasku narzędzi.

Zostanie wyświetlona strona, która umożliwia wypełnienie informacji o nowym wpisie w blogu.

![Zrzut ekranu przedstawiający stronę Dodawanie nowej strony post](_static/xamarin-tutorial-4.png)

Wypełnij wszystkie informacje i kliknij przycisk **Zapisz** na pasku narzędzi.

Nowy wpis zostanie skojarzony z wpisem w blogu, który został kliknięty w poprzednim kroku i zostanie zapisany w bazie danych programu SQLite aplikacji i wyświetlony na liście.

Wróć do strony listy blogów. I kliknij pozycję **Usuń wszystko** na pasku narzędzi. Wszystkie blogi i odpowiadające im wpisy zostaną usunięte z bazy danych programu SQLite aplikacji.

![Zrzut ekranu aplikacji z usuniętymi wszystkimi blogami](_static/xamarin-tutorial-5.png)

## <a name="explore-the-code"></a>Eksplorowanie kodu

Poniższe sekcje przeprowadzą Cię przez kod w przykładowym projekcie, który odczytuje, tworzy, aktualizuje i usuwa dane z bazy danych programu SQLite przy użyciu EF Core za pomocą platformy Xamarin. Forms.

Przyjęto założenie, że znasz już tematy dotyczące [wyświetlania danych](/xamarin/xamarin-forms/app-fundamentals/data-binding/) i [nawigowania między stronami](/xamarin/xamarin-forms/app-fundamentals/navigation/).

## <a name="entity-framework-core-nuget-packages"></a>Entity Framework Core pakietów NuGet

Aby utworzyć aplikacje platformy Xamarin. Forms za pomocą EF Core, należy zainstalować pakiet dla dostawców bazy danych EF Core, które mają być przeznaczone do wszystkich projektów w rozwiązaniu Xamarin. Forms. W tym samouczku jest stosowany dostawca oprogramowania SQLite.

W każdym projekcie w rozwiązaniu Xamarin. Forms jest wymagany następujący pakiet NuGet.

* `Microsoft.EntityFrameworkCore.Sqlite`

## <a name="model-classes"></a>Klasy modelu

Każda tabela w bazie danych programu SQLite, do której można uzyskać dostęp za pomocą EF Core, jest modelowana w klasie. W tym przykładzie używane są dwie klasy: i, które znajdują się `Blog` `Post` w `Models` folderze.

Klasy modelu składają się tylko z właściwości, które są kolumnami modelu w bazie danych.

* **Blog.cs**

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Blog.cs)]

* `Posts`Właściwość definiuje relację nadrzędny-podrzędny między `Blog` i `Post` .

* **Post.cs**

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Post.cs)]

* `BlogId`Właściwości i `Blog` odnoszą się do obiektu nadrzędnego `Blog` dla wystąpienia elementu `Post` .

## <a name="data-context"></a>Kontekst danych

Klasa znajduje się `BloggingContext` w `Services` folderze i dziedziczy z `DbContext` klasy EF Core. A służy `DbContext` do grupowania zapytań bazy danych i zmian.

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Services/BloggingContext.cs)]

* Obie właściwości w tej klasie typu `DbSet` są używane do działania na podstawowych tabelach reprezentujących blogi i wpisy.
* `SQLitePCL.Batteries_V2.Init()`Jest to konieczne w konstruktorze w celu zainicjowania oprogramowania SQLite w systemie iOS.
* `OnConfiguring`Funkcja konfiguruje lokalizację bazy danych programu SQLite na urządzeniu fizycznym.

## <a name="create-read-update--delete"></a>Tworzenie, odczytywanie, aktualizowanie & usuwanie

Poniżej przedstawiono niektóre wystąpienia w aplikacji, w których EF Core jest używany do uzyskiwania dostępu do oprogramowania SQLite.

### <a name="read"></a>Odczyt

* Zwróć wszystkie rekordy.
  * `OnAppearing`Funkcja `BlogsPage.xaml.cs` zwraca wszystkie `Blog` rekordy i zapisuje je w `List` zmiennej.

```csharp
using (var blogContext = new BloggingContext())
{
    var theBlogs = blogContext.Blogs.ToList();
}
```

* Zwróć określone rekordy.
  * `OnAppearing`Funkcja `PostsPage.xaml.cs` zwraca `Post` rekordy, które zawierają określony `BlogId` .

```csharp
using (var blogContext = new BloggingContext())
{
    var postList = blogContext.Posts
        .Where(p => p.BlogId == BlogId)
        .ToList();
}
```

### <a name="create"></a>Utwórz

* Wstaw nowy rekord.
  * `Save_Clicked`Funkcja `AddBlogPage.xaml.cs` wstawia nowy `Blog` obiekt do bazy danych programu SQLite.

```csharp
var blog = new Blog { Url = blogUrl.Text };

using (var blogContext = new BloggingContext())
{
    blogContext.Add(blog);

    await blogContext.SaveChangesAsync();
}
```

### <a name="update"></a>Aktualizacja

* Aktualizowanie istniejącego rekordu.
  * `Save_Clicked`Funkcja `AddPostPage.xaml.cs` aktualizuje istniejący `Blog` obiekt z nowym `Post` .
  
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

### <a name="delete"></a>Usuń

* Usuń wszystkie rekordy z rekordami kaskadowymi i podrzędnymi.
  * `DeleteAll_Clicked`Funkcja `BlogsPage.xaml.cs` usuwania wszystkich `Blog` rekordów w bazie danych programu SQLite i kaskaduje usunięcia do wszystkich `Blog` `Post` rekordów podrzędnych.

```csharp
using (var blogContext = new BloggingContext())
{
    blogContext.RemoveRange(blogContext.Blogs);

    await blogContext.SaveChangesAsync();
}
```

## <a name="next-steps"></a>Następne kroki

W tym temacie dowiesz się, jak używać aplikacji platformy Xamarin. Forms do uzyskiwania dostępu do bazy danych programu SQLite przy użyciu Entity Framework Core.

Inne tematy Entity Framework Core zainteresowania deweloperów platformy Xamarin:

* [Konfigurowanie`DbContext`](xref:core/miscellaneous/configuring-dbcontext)
* Dowiedz się więcej o [wyrażeniach zapytań LINQ](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)
* [Skonfiguruj model](xref:core/modeling/index) , aby określić elementy, takie jak [wymagana](xref:core/modeling/entity-properties#required-and-optional-properties) i [Maksymalna długość](xref:core/modeling/entity-properties#maximum-length)
