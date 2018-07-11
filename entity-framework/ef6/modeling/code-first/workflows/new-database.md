---
title: Najpierw kod do nowej bazy danych — EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 2df6cb0a-7d8b-4e28-9d05-e2b9a90125af
caps.latest.revision: 3
ms.openlocfilehash: 75057fc73b082f4c745171ed77cddc358229a130
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2018
ms.locfileid: "37912122"
---
# <a name="code-first-to-a-new-database"></a><span data-ttu-id="025d1-102">Najpierw kod do nowej bazy danych</span><span class="sxs-lookup"><span data-stu-id="025d1-102">Code First to a New Database</span></span>
<span data-ttu-id="025d1-103">W tym przewodniku krok po kroku i wideo zawierają wprowadzenie do rozwiązania deweloperskiego Code First dla nowej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="025d1-103">This video and step-by-step walkthrough provide an introduction to Code First development targeting a new database.</span></span> <span data-ttu-id="025d1-104">Ten scenariusz obejmuje przeznaczonych dla bazy danych, która nie istnieje i utworzy Code First lub pustej bazy danych, który Code First doda nowe tabele zbyt.</span><span class="sxs-lookup"><span data-stu-id="025d1-104">This scenario includes targeting a database that doesn’t exist and Code First will create, or an empty database that Code First will add new tables too.</span></span> <span data-ttu-id="025d1-105">Kod umożliwia najpierw zdefiniować model za pomocą języka C\# lub klasy VB.Net.</span><span class="sxs-lookup"><span data-stu-id="025d1-105">Code First allows you to define your model using C\# or VB.Net classes.</span></span> <span data-ttu-id="025d1-106">Opcjonalnie można wykonać dodatkowe czynności konfiguracyjne przy użyciu atrybutów klas i właściwości lub za pomocą interfejsu API fluent.</span><span class="sxs-lookup"><span data-stu-id="025d1-106">Additional configuration can optionally be performed using attributes on your classes and properties or by using a fluent API.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="025d1-107">Obejrzyj wideo</span><span class="sxs-lookup"><span data-stu-id="025d1-107">Watch the video</span></span>
<span data-ttu-id="025d1-108">To wideo zawiera wprowadzenie do rozwiązania deweloperskiego Code First dla nowej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="025d1-108">This video provides an introduction to Code First development targeting a new database.</span></span> <span data-ttu-id="025d1-109">Ten scenariusz obejmuje przeznaczonych dla bazy danych, która nie istnieje i utworzy Code First lub pustej bazy danych, który Code First doda nowe tabele zbyt.</span><span class="sxs-lookup"><span data-stu-id="025d1-109">This scenario includes targeting a database that doesn’t exist and Code First will create, or an empty database that Code First will add new tables too.</span></span> <span data-ttu-id="025d1-110">Najpierw kod pozwala na zdefiniowanie modelu przy użyciu klas języka C# lub VB.Net.</span><span class="sxs-lookup"><span data-stu-id="025d1-110">Code First allows you to define your model using C# or VB.Net classes.</span></span> <span data-ttu-id="025d1-111">Opcjonalnie można wykonać dodatkowe czynności konfiguracyjne przy użyciu atrybutów klas i właściwości lub za pomocą interfejsu API fluent.</span><span class="sxs-lookup"><span data-stu-id="025d1-111">Additional configuration can optionally be performed using attributes on your classes and properties or by using a fluent API.</span></span>

<span data-ttu-id="025d1-112">**Osoba prezentująca**: [Rowan Miller](http://romiller.com/)</span><span class="sxs-lookup"><span data-stu-id="025d1-112">**Presented By**: [Rowan Miller](http://romiller.com/)</span></span>

<span data-ttu-id="025d1-113">**Film wideo**: [WMV](http://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.wmv) | [MP4](http://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-mp4Video-CodeFirstNewDatabase.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.zip)</span><span class="sxs-lookup"><span data-stu-id="025d1-113">**Video**: [WMV](http://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.wmv) | [MP4](http://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-mp4Video-CodeFirstNewDatabase.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.zip)</span></span>

# <a name="pre-requisites"></a><span data-ttu-id="025d1-114">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="025d1-114">Pre-Requisites</span></span>

<span data-ttu-id="025d1-115">Musisz mieć co najmniej programu Visual Studio 2010 lub Visual Studio 2012 są zainstalowane w tym przewodniku.</span><span class="sxs-lookup"><span data-stu-id="025d1-115">You will need to have at least Visual Studio 2010 or Visual Studio 2012 installed to complete this walkthrough.</span></span>

<span data-ttu-id="025d1-116">Jeśli używasz programu Visual Studio 2010, należy również mieć [NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) zainstalowane.</span><span class="sxs-lookup"><span data-stu-id="025d1-116">If you are using Visual Studio 2010, you will also need to have [NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) installed.</span></span>

## <a name="1-create-the-application"></a><span data-ttu-id="025d1-117">1. Tworzenie aplikacji</span><span class="sxs-lookup"><span data-stu-id="025d1-117">1. Create the Application</span></span>

<span data-ttu-id="025d1-118">Aby zachować ich prostotę zamierzamy utworzyć aplikację podstawowa konsola, która używa Code First do dostępu do danych.</span><span class="sxs-lookup"><span data-stu-id="025d1-118">To keep things simple we’re going to build a basic console application that uses Code First to perform data access.</span></span>

-   <span data-ttu-id="025d1-119">Otwórz program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="025d1-119">Open Visual Studio</span></span>
-   <span data-ttu-id="025d1-120">**Plik —&gt; New -&gt; projektu...**</span><span class="sxs-lookup"><span data-stu-id="025d1-120">**File -&gt; New -&gt; Project…**</span></span>
-   <span data-ttu-id="025d1-121">Wybierz **Windows** menu po lewej stronie i **aplikacji konsoli**</span><span class="sxs-lookup"><span data-stu-id="025d1-121">Select **Windows** from the left menu and **Console Application**</span></span>
-   <span data-ttu-id="025d1-122">Wprowadź **CodeFirstNewDatabaseSample** jako nazwę</span><span class="sxs-lookup"><span data-stu-id="025d1-122">Enter **CodeFirstNewDatabaseSample** as the name</span></span>
-   <span data-ttu-id="025d1-123">Wybierz **OK**</span><span class="sxs-lookup"><span data-stu-id="025d1-123">Select **OK**</span></span>

## <a name="2-create-the-model"></a><span data-ttu-id="025d1-124">2. Tworzenie modelu</span><span class="sxs-lookup"><span data-stu-id="025d1-124">2. Create the Model</span></span>

<span data-ttu-id="025d1-125">Czynnością jest zdefiniowanie bardzo prosty model przy użyciu klas.</span><span class="sxs-lookup"><span data-stu-id="025d1-125">Let’s define a very simple model using classes.</span></span> <span data-ttu-id="025d1-126">Wystarczy zdefiniować je w pliku Program.cs, ale w rzeczywistej aplikacji, w Twoich zajęciach się będzie podzielić na osobne pliki i potencjalnie oddzielnego projektu.</span><span class="sxs-lookup"><span data-stu-id="025d1-126">We’re just defining them in the Program.cs file but in a real world application you would split your classes out into separate files and potentially a separate project.</span></span>

<span data-ttu-id="025d1-127">Poniżej definicji klasy programu w pliku Program.cs Dodaj następujące dwie klasy.</span><span class="sxs-lookup"><span data-stu-id="025d1-127">Below the Program class definition in Program.cs add the following two classes.</span></span>

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }

    public virtual List<Post> Posts { get; set; }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public int BlogId { get; set; }
    public virtual Blog Blog { get; set; }
}
```

<span data-ttu-id="025d1-128">Można zauważyć, że wprowadzamy dwie właściwości nawigacji (Blog.Posts i Post.Blog) wirtualnego.</span><span class="sxs-lookup"><span data-stu-id="025d1-128">You’ll notice that we’re making the two navigation properties (Blog.Posts and Post.Blog) virtual.</span></span> <span data-ttu-id="025d1-129">Dzięki temu funkcja powolne ładowanie programu Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="025d1-129">This enables the Lazy Loading feature of Entity Framework.</span></span> <span data-ttu-id="025d1-130">Powolne ładowanie oznacza, że zawartość te właściwości zostaną automatycznie załadowane z bazy danych przy próbie uzyskiwać do nich dostęp.</span><span class="sxs-lookup"><span data-stu-id="025d1-130">Lazy Loading means that the contents of these properties will be automatically loaded from the database when you try to access them.</span></span>

## <a name="3-create-a-context"></a><span data-ttu-id="025d1-131">3. Utwórz kontekst</span><span class="sxs-lookup"><span data-stu-id="025d1-131">3. Create a Context</span></span>

<span data-ttu-id="025d1-132">Teraz nadszedł czas, aby zdefiniować pochodnej kontekst, który reprezentuje sesję z bazą danych, dzięki czemu nam zapytania i Zapisz dane.</span><span class="sxs-lookup"><span data-stu-id="025d1-132">Now it’s time to define a derived context, which represents a session with the database, allowing us to query and save data.</span></span> <span data-ttu-id="025d1-133">Definiujemy kontekstu, który pochodzi z System.Data.Entity.DbContext i udostępnia wpisane DbSet&lt;TEntity&gt; dla każdej klasy w naszym modelu.</span><span class="sxs-lookup"><span data-stu-id="025d1-133">We define a context that derives from System.Data.Entity.DbContext and exposes a typed DbSet&lt;TEntity&gt; for each class in our model.</span></span>

<span data-ttu-id="025d1-134">Zaczynamy teraz użyć typów z programu Entity Framework, dlatego musimy Dodaj pakiet NuGet platformy EntityFramework.</span><span class="sxs-lookup"><span data-stu-id="025d1-134">We’re now starting to use types from the Entity Framework so we need to add the EntityFramework NuGet package.</span></span>

-   <span data-ttu-id="025d1-135">**Project —&gt; Zarządzaj pakietami NuGet...**</span><span class="sxs-lookup"><span data-stu-id="025d1-135">**Project –&gt; Manage NuGet Packages…**</span></span>
    <span data-ttu-id="025d1-136">Uwaga: Jeśli nie masz **Zarządzaj pakietami NuGet...**</span><span class="sxs-lookup"><span data-stu-id="025d1-136">Note: If you don’t have the **Manage NuGet Packages…**</span></span> <span data-ttu-id="025d1-137">opcja, należy zainstalować [najnowszej wersji pakietu NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)</span><span class="sxs-lookup"><span data-stu-id="025d1-137">option you should install the [latest version of NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)</span></span>
-   <span data-ttu-id="025d1-138">Wybierz **Online** kartę</span><span class="sxs-lookup"><span data-stu-id="025d1-138">Select the **Online** tab</span></span>
-   <span data-ttu-id="025d1-139">Wybierz **EntityFramework** pakietu</span><span class="sxs-lookup"><span data-stu-id="025d1-139">Select the **EntityFramework** package</span></span>
-   <span data-ttu-id="025d1-140">Kliknij przycisk **instalacji**</span><span class="sxs-lookup"><span data-stu-id="025d1-140">Click **Install**</span></span>

<span data-ttu-id="025d1-141">Dodaj instrukcję using poufności informacji dotyczące System.Data.Entity w górnej części pliku Program.cs.</span><span class="sxs-lookup"><span data-stu-id="025d1-141">Add a using statement for System.Data.Entity at the top of Program.cs.</span></span>

``` csharp
using System.Data.Entity;
```

<span data-ttu-id="025d1-142">Poniższe klasy wpisu w pliku Program.cs Dodaj następującym kontekstem pochodnych.</span><span class="sxs-lookup"><span data-stu-id="025d1-142">Below the Post class in Program.cs add the following derived context.</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```

<span data-ttu-id="025d1-143">Oto Pełna lista Program.cs powinien teraz zawartość.</span><span class="sxs-lookup"><span data-stu-id="025d1-143">Here is a complete listing of what Program.cs should now contain.</span></span>

``` csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Data.Entity;

namespace CodeFirstNewDatabaseSample
{
    class Program
    {
        static void Main(string[] args)
        {
        }
    }

    public class Blog
    {
        public int BlogId { get; set; }
        public string Name { get; set; }

        public virtual List<Post> Posts { get; set; }
    }

    public class Post
    {
        public int PostId { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public virtual Blog Blog { get; set; }
    }

    public class BloggingContext : DbContext
    {
        public DbSet<Blog> Blogs { get; set; }
        public DbSet<Post> Posts { get; set; }
    }
}
```

<span data-ttu-id="025d1-144">To już cały kod, której potrzebujesz do rozpoczęcia przechowywania i pobierania danych.</span><span class="sxs-lookup"><span data-stu-id="025d1-144">That is all the code we need to start storing and retrieving data.</span></span> <span data-ttu-id="025d1-145">Oczywiście istnieje znacznej dzieje się w tle, a firma Microsoft będzie Przyjrzyj się, w momencie, ale pierwszym umożliwia sprawdzenie w działaniu.</span><span class="sxs-lookup"><span data-stu-id="025d1-145">Obviously there is quite a bit going on behind the scenes and we’ll take a look at that in a moment but first let’s see it in action.</span></span>

## <a name="4-reading--writing-data"></a><span data-ttu-id="025d1-146">4. Odczytywanie i zapisywanie danych</span><span class="sxs-lookup"><span data-stu-id="025d1-146">4. Reading & Writing Data</span></span>

<span data-ttu-id="025d1-147">Implementuje metody Main w pliku Program.cs, jak pokazano poniżej.</span><span class="sxs-lookup"><span data-stu-id="025d1-147">Implement the Main method in Program.cs as shown below.</span></span> <span data-ttu-id="025d1-148">Ten kod tworzy nowe wystąpienie nasz kontekst i używa go do wstawiania nowego bloga.</span><span class="sxs-lookup"><span data-stu-id="025d1-148">This code creates a new instance of our context and then uses it to insert a new Blog.</span></span> <span data-ttu-id="025d1-149">Następnie używa zapytania LINQ, aby pobrać wszystkie blogi z bazy danych uporządkowana w kolejności alfabetycznej według tytułu.</span><span class="sxs-lookup"><span data-stu-id="025d1-149">Then it uses a LINQ query to retrieve all Blogs from the database ordered alphabetically by Title.</span></span>

``` csharp
class Program
{
    static void Main(string[] args)
    {
        using (var db = new BloggingContext())
        {
            // Create and save a new Blog
            Console.Write("Enter a name for a new Blog: ");
            var name = Console.ReadLine();

            var blog = new Blog { Name = name };
            db.Blogs.Add(blog);
            db.SaveChanges();

            // Display all Blogs from the database
            var query = from b in db.Blogs
                        orderby b.Name
                        select b;

            Console.WriteLine("All blogs in the database:");
            foreach (var item in query)
            {
                Console.WriteLine(item.Name);
            }

            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
        }
    }
}
```

<span data-ttu-id="025d1-150">Możesz teraz uruchomić aplikację i ją przetestować.</span><span class="sxs-lookup"><span data-stu-id="025d1-150">You can now run the application and test it out.</span></span>

```
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
ADO.NET Blog
Press any key to exit...
```
### <a name="wheres-my-data"></a><span data-ttu-id="025d1-151">Gdzie są moje dane?</span><span class="sxs-lookup"><span data-stu-id="025d1-151">Where’s My Data?</span></span>

<span data-ttu-id="025d1-152">Zgodnie z Konwencją DbContext utworzył bazę danych dla Ciebie.</span><span class="sxs-lookup"><span data-stu-id="025d1-152">By convention DbContext has created a database for you.</span></span>

-   <span data-ttu-id="025d1-153">Jeśli lokalne wystąpienie programu SQL Express jest dostępny (instalowany domyślnie w programie Visual Studio 2010) następnie Code First została utworzona baza danych w tym wystąpieniu</span><span class="sxs-lookup"><span data-stu-id="025d1-153">If a local SQL Express instance is available (installed by default with Visual Studio 2010) then Code First has created the database on that instance</span></span>
-   <span data-ttu-id="025d1-154">Jeśli program SQL Express nie jest dostępny, a następnie Code First będzie próbują i użyć [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) (instalowany domyślnie w programie Visual Studio 2012)</span><span class="sxs-lookup"><span data-stu-id="025d1-154">If SQL Express isn’t available then Code First will try and use [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) (installed by default with Visual Studio 2012)</span></span>
-   <span data-ttu-id="025d1-155">Baza danych jest nazwana w pełni kwalifikowaną nazwę pochodnego kontekst, w tym przypadku, który jest **CodeFirstNewDatabaseSample.BloggingContext**</span><span class="sxs-lookup"><span data-stu-id="025d1-155">The database is named after the fully qualified name of the derived context, in our case that is **CodeFirstNewDatabaseSample.BloggingContext**</span></span>

<span data-ttu-id="025d1-156">Są to po prostu domyślnych Konwencji istnieją różne sposoby, aby zmienić bazę danych, która używa Code First, więcej informacji znajduje się w **jak DbContext wykrywa Model i połączenie z bazą danych** tematu.</span><span class="sxs-lookup"><span data-stu-id="025d1-156">These are just the default conventions and there are various ways to change the database that Code First uses, more information is available in the **How DbContext Discovers the Model and Database Connection** topic.</span></span>
<span data-ttu-id="025d1-157">Możesz nawiązać połączenie tej bazy danych za pomocą Eksploratora serwera w programie Visual Studio</span><span class="sxs-lookup"><span data-stu-id="025d1-157">You can connect to this database using Server Explorer in Visual Studio</span></span>

-   <span data-ttu-id="025d1-158">**Widok —&gt; Eksploratora serwera**</span><span class="sxs-lookup"><span data-stu-id="025d1-158">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="025d1-159">Kliknij prawym przyciskiem myszy **połączeń danych** i wybierz **Dodaj połączenie...**</span><span class="sxs-lookup"><span data-stu-id="025d1-159">Right click on **Data Connections** and select **Add Connection…**</span></span>
-   <span data-ttu-id="025d1-160">Jeśli nie jest połączona z bazą danych za pomocą Eksploratora serwera przed, musisz wybrać programu Microsoft SQL Server jako źródło danych</span><span class="sxs-lookup"><span data-stu-id="025d1-160">If you haven’t connected to a database from Server Explorer before you’ll need to select Microsoft SQL Server as the data source</span></span>

    ![SelectDataSource](~/ef6/media/selectdatasource.png)

-   <span data-ttu-id="025d1-162">Łączenie się z LocalDB lub SQL Express, w zależności od tego, który z nich został zainstalowany</span><span class="sxs-lookup"><span data-stu-id="025d1-162">Connect to either LocalDB or SQL Express, depending on which one you have installed</span></span>

<span data-ttu-id="025d1-163">Firma Microsoft jest teraz Zbadaj schematu utworzonego Code First.</span><span class="sxs-lookup"><span data-stu-id="025d1-163">We can now inspect the schema that Code First created.</span></span>

![SchemaInitial](~/ef6/media/schemainitial.png)

<span data-ttu-id="025d1-165">Które klasy do uwzględnienia w modelu, analizując właściwości DbSet, które zdefiniowaliśmy opracowaną w DbContext.</span><span class="sxs-lookup"><span data-stu-id="025d1-165">DbContext worked out what classes to include in the model by looking at the DbSet properties that we defined.</span></span> <span data-ttu-id="025d1-166">Następnie używa domyślnego zestawu Code First konwencje do określenia nazwy tabel i kolumn, określanie typów danych, znajdowanie, kluczy podstawowych, itp.</span><span class="sxs-lookup"><span data-stu-id="025d1-166">It then uses the default set of Code First conventions to determine table and column names, determine data types, find primary keys, etc.</span></span> <span data-ttu-id="025d1-167">W dalszej części tego przewodnika, omówimy sposób można zastąpić tych konwencji.</span><span class="sxs-lookup"><span data-stu-id="025d1-167">Later in this walkthrough we’ll look at how you can override these conventions.</span></span>

## <a name="5-dealing-with-model-changes"></a><span data-ttu-id="025d1-168">5. Obsługa zmiany modelu</span><span class="sxs-lookup"><span data-stu-id="025d1-168">5. Dealing with Model Changes</span></span>

<span data-ttu-id="025d1-169">Teraz nadszedł czas, aby wprowadzić pewne zmiany na nasz model, jeśli możemy wprowadzić te zmiany, należy również zaktualizować schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="025d1-169">Now it’s time to make some changes to our model, when we make these changes we also need to update the database schema.</span></span> <span data-ttu-id="025d1-170">W tym celu użyjemy do użycia funkcję migracje Code First ani migracji w skrócie.</span><span class="sxs-lookup"><span data-stu-id="025d1-170">To do this we are going to use a feature called Code First Migrations, or Migrations for short.</span></span>

<span data-ttu-id="025d1-171">Migracja pozwala mieć uporządkowany zestaw kroków, które opisują sposób uaktualnienia (i obniżenia poziomu) naszych schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="025d1-171">Migrations allows us to have an ordered set of steps that describe how to upgrade (and downgrade) our database schema.</span></span> <span data-ttu-id="025d1-172">Każdy z tych kroków nazywana migracji, zawiera pewne kod, który opisano zmiany, które mają być stosowane.</span><span class="sxs-lookup"><span data-stu-id="025d1-172">Each of these steps, known as a migration, contains some code that describes the changes to be applied.</span></span> 

<span data-ttu-id="025d1-173">Pierwszym krokiem jest włączenie migracje Code First dla naszych BloggingContext.</span><span class="sxs-lookup"><span data-stu-id="025d1-173">The first step is to enable Code First Migrations for our BloggingContext.</span></span>

-   <span data-ttu-id="025d1-174">**Narzędzia —&gt; Menedżer pakietów biblioteki -&gt; Konsola Menedżera pakietów**</span><span class="sxs-lookup"><span data-stu-id="025d1-174">**Tools -&gt; Library Package Manager -&gt; Package Manager Console**</span></span>
-   <span data-ttu-id="025d1-175">Uruchom **Enable-Migrations** polecenia w konsoli Menedżera pakietów</span><span class="sxs-lookup"><span data-stu-id="025d1-175">Run the **Enable-Migrations** command in Package Manager Console</span></span>
-   <span data-ttu-id="025d1-176">Nowy folder migracji został dodany do naszych projektu, który zawiera dwa elementy:</span><span class="sxs-lookup"><span data-stu-id="025d1-176">A new Migrations folder has been added to our project that contains two items:</span></span>
    -   <span data-ttu-id="025d1-177">**Configuration.cs** — ten plik zawiera ustawienia, używających migracje migracji BloggingContext.</span><span class="sxs-lookup"><span data-stu-id="025d1-177">**Configuration.cs** – This file contains the settings that Migrations will use for migrating BloggingContext.</span></span> <span data-ttu-id="025d1-178">Nie trzeba zmieniać niczego w ramach tego przewodnika, ale poniżej przedstawiono, w którym można określić przestrzeń nazw zmieniają się dane inicjatora, dostawcy rejestru dla innych baz danych, czy migracje są generowane w itp.</span><span class="sxs-lookup"><span data-stu-id="025d1-178">We don’t need to change anything for this walkthrough, but here is where you can specify seed data, register providers for other databases, changes the namespace that migrations are generated in etc.</span></span>
    -   <span data-ttu-id="025d1-179">**&lt;Sygnatura czasowa&gt;\_InitialCreate.cs** — jest to pierwszy migracji, reprezentuje zmiany, które zostały już zastosowane do bazy danych, zrób to przed pustej bazy danych na taki, który zawiera tabele blogów i wpisów .</span><span class="sxs-lookup"><span data-stu-id="025d1-179">**&lt;timestamp&gt;\_InitialCreate.cs** – This is your first migration, it represents the changes that have already been applied to the database to take it from being an empty database to one that includes the Blogs and Posts tables.</span></span> <span data-ttu-id="025d1-180">Mimo że umożliwialiśmy Code First automatycznie utworzyć te tabele, skoro mamy wybranych do migracji, które zostały przekonwertowane do migracji.</span><span class="sxs-lookup"><span data-stu-id="025d1-180">Although we let Code First automatically create these tables for us, now that we have opted in to Migrations they have been converted into a Migration.</span></span> <span data-ttu-id="025d1-181">Kod najpierw również zarejestrował w naszej bazie danych lokalnych, czy migracja została już zainstalowana.</span><span class="sxs-lookup"><span data-stu-id="025d1-181">Code First has also recorded in our local database that this Migration has already been applied.</span></span> <span data-ttu-id="025d1-182">Sygnatury czasowej na nazwę pliku służy do ustalania kolejności celów.</span><span class="sxs-lookup"><span data-stu-id="025d1-182">The timestamp on the filename is used for ordering purposes.</span></span>

    <span data-ttu-id="025d1-183">Teraz możemy wprowadzić zmiany w naszym modelu, Dodaj właściwość adres Url do klasy Blog:</span><span class="sxs-lookup"><span data-stu-id="025d1-183">Now let’s make a change to our model, add a Url property to the Blog class:</span></span>

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }
    public string Url { get; set; }

    public virtual List<Post> Posts { get; set; }
}
```

-   <span data-ttu-id="025d1-184">Uruchom **AddUrl migracji Dodaj** polecenia w konsoli Menedżera pakietów.</span><span class="sxs-lookup"><span data-stu-id="025d1-184">Run the **Add-Migration AddUrl** command in Package Manager Console.</span></span>
    <span data-ttu-id="025d1-185">Polecenie Dodaj migracji sprawdza zmiany od ostatniego migracji i scaffolds nowej migracji ze wszystkimi zmianami, które znajdują się.</span><span class="sxs-lookup"><span data-stu-id="025d1-185">The Add-Migration command checks for changes since your last migration and scaffolds a new migration with any changes that are found.</span></span> <span data-ttu-id="025d1-186">Firma Microsoft może nazwij migracje; w takim przypadku wywołania migracji "AddUrl".</span><span class="sxs-lookup"><span data-stu-id="025d1-186">We can give migrations a name; in this case we are calling the migration ‘AddUrl’.</span></span>
    <span data-ttu-id="025d1-187">Utworzony szkielet kodu mówi, że musimy dodać kolumnę adres Url, który może pomieścić ciąg danych, do dbo. Blogi tabeli.</span><span class="sxs-lookup"><span data-stu-id="025d1-187">The scaffolded code is saying that we need to add a Url column, that can hold string data, to the dbo.Blogs table.</span></span> <span data-ttu-id="025d1-188">Jeśli to konieczne, można będziemy edytować utworzony szkielet kodu, ale nie jest to wymagane w tym przypadku.</span><span class="sxs-lookup"><span data-stu-id="025d1-188">If needed, we could edit the scaffolded code but that’s not required in this case.</span></span>

``` csharp
namespace CodeFirstNewDatabaseSample.Migrations
{
    using System;
    using System.Data.Entity.Migrations;

    public partial class AddUrl : DbMigration
    {
        public override void Up()
        {
            AddColumn("dbo.Blogs", "Url", c => c.String());
        }

        public override void Down()
        {
            DropColumn("dbo.Blogs", "Url");
        }
    }
}
```

-   <span data-ttu-id="025d1-189">Uruchom **Update-Database** polecenia w konsoli Menedżera pakietów.</span><span class="sxs-lookup"><span data-stu-id="025d1-189">Run the **Update-Database** command in Package Manager Console.</span></span> <span data-ttu-id="025d1-190">To polecenie będzie dotyczyć wszelkie oczekujące migracji bazy danych.</span><span class="sxs-lookup"><span data-stu-id="025d1-190">This command will apply any pending migrations to the database.</span></span> <span data-ttu-id="025d1-191">Nasze migracji InitialCreate została już zainstalowana, migracje będą po prostu zastosować naszej nowej migracji AddUrl.</span><span class="sxs-lookup"><span data-stu-id="025d1-191">Our InitialCreate migration has already been applied so migrations will just apply our new AddUrl migration.</span></span>
    <span data-ttu-id="025d1-192">Porada: Możesz użyć **— pełne** Przełącz podczas wywoływania Update-Database, aby zobaczyć, SQL Server, który jest wykonywana w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="025d1-192">Tip: You can use the **–Verbose** switch when calling Update-Database to see the SQL that is being executed against the database.</span></span>

<span data-ttu-id="025d1-193">Nowa kolumna adres Url jest dodany do blogów tabeli w bazie danych:</span><span class="sxs-lookup"><span data-stu-id="025d1-193">The new Url column is now added to the Blogs table in the database:</span></span>

![SchemaWithUrl](~/ef6/media/schemawithurl.png)

## <a name="6-data-annotations"></a><span data-ttu-id="025d1-195">6. Adnotacje danych</span><span class="sxs-lookup"><span data-stu-id="025d1-195">6. Data Annotations</span></span>

<span data-ttu-id="025d1-196">Do tej pory został właśnie umożliwialiśmy EF odnajdywanie modelu przy użyciu jego domyślnych Konwencji, ale będą występować sytuacje, gdy nie są zgodne z konwencjami naszych zajęć i musimy wykonać dalszą część konfigurowania.</span><span class="sxs-lookup"><span data-stu-id="025d1-196">So far we’ve just let EF discover the model using its default conventions, but there are going to be times when our classes don’t follow the conventions and we need to be able to perform further configuration.</span></span> <span data-ttu-id="025d1-197">Dostępne są dwie opcje dla tego; omówimy adnotacje danych w tej sekcji, a następnie wygodnego interfejsu API w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="025d1-197">There are two options for this; we’ll look at Data Annotations in this section and then the fluent API in the next section.</span></span>

-   <span data-ttu-id="025d1-198">Dodajmy klasy użytkownika w naszym modelu</span><span class="sxs-lookup"><span data-stu-id="025d1-198">Let’s add a User class to our model</span></span>

``` csharp
public class User
{
    public string Username { get; set; }
    public string DisplayName { get; set; }
}
```

-   <span data-ttu-id="025d1-199">Należy również dodać zestaw do nasz kontekst pochodne</span><span class="sxs-lookup"><span data-stu-id="025d1-199">We also need to add a set to our derived context</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
    public DbSet<User> Users { get; set; }
}
```

-   <span data-ttu-id="025d1-200">Jeśli firma Microsoft podjęto próbę dodania migracji otrzymamy wynik powiedzenie błąd "*obiektu EntityType"User"nie ma zdefiniowanego klucza. Klucz jest definiowany dla tego obiektu EntityType."*</span><span class="sxs-lookup"><span data-stu-id="025d1-200">If we tried to add a migration we’d get an error saying “*EntityType ‘User’ has no key defined. Define the key for this EntityType.”*</span></span> <span data-ttu-id="025d1-201">ponieważ EF nie ma możliwości, wiedząc, że nazwa użytkownika powinna mieć klucz podstawowy dla użytkownika.</span><span class="sxs-lookup"><span data-stu-id="025d1-201">because EF has no way of knowing that Username should be the primary key for User.</span></span>
-   <span data-ttu-id="025d1-202">Zamierzamy korzystanie z adnotacji danych teraz musimy dodać, przy użyciu instrukcji w górnej części pliku Program.cs</span><span class="sxs-lookup"><span data-stu-id="025d1-202">We’re going to use Data Annotations now so we need to add a using statement at the top of Program.cs</span></span>

```
using System.ComponentModel.DataAnnotations;
```

-   <span data-ttu-id="025d1-203">Teraz dodawać adnotacje do właściwości nazwy użytkownika do identyfikowania, czy jest klucz podstawowy</span><span class="sxs-lookup"><span data-stu-id="025d1-203">Now annotate the Username property to identify that it is the primary key</span></span>

``` csharp
public class User
{
    [Key]
    public string Username { get; set; }
    public string DisplayName { get; set; }
}
```

-   <span data-ttu-id="025d1-204">Użyj **AddUser migracji Dodaj** polecenia do tworzenia szkieletu migracji, aby zastosować te zmiany do bazy danych</span><span class="sxs-lookup"><span data-stu-id="025d1-204">Use the **Add-Migration AddUser** command to scaffold a migration to apply these changes to the database</span></span>
-   <span data-ttu-id="025d1-205">Uruchom **Update-Database** polecenie, aby zastosować nową migrację do bazy danych</span><span class="sxs-lookup"><span data-stu-id="025d1-205">Run the **Update-Database** command to apply the new migration to the database</span></span>

<span data-ttu-id="025d1-206">Teraz zostanie dodana nowa tabela, do bazy danych:</span><span class="sxs-lookup"><span data-stu-id="025d1-206">The new table is now added to the database:</span></span>

![SchemaWithUsers](~/ef6/media/schemawithusers.png)

<span data-ttu-id="025d1-208">Pełna lista adnotacje obsługiwane przez EF jest:</span><span class="sxs-lookup"><span data-stu-id="025d1-208">The full list of annotations supported by EF is:</span></span>

-   [<span data-ttu-id="025d1-209">KeyAttribute</span><span class="sxs-lookup"><span data-stu-id="025d1-209">KeyAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.keyattribute)
-   [<span data-ttu-id="025d1-210">StringLengthAttribute</span><span class="sxs-lookup"><span data-stu-id="025d1-210">StringLengthAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.stringlengthattribute)
-   [<span data-ttu-id="025d1-211">MaxLengthAttribute</span><span class="sxs-lookup"><span data-stu-id="025d1-211">MaxLengthAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.maxlengthattribute)
-   [<span data-ttu-id="025d1-212">ConcurrencyCheckAttribute</span><span class="sxs-lookup"><span data-stu-id="025d1-212">ConcurrencyCheckAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.concurrencycheckattribute)
-   [<span data-ttu-id="025d1-213">RequiredAttribute</span><span class="sxs-lookup"><span data-stu-id="025d1-213">RequiredAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.requiredattribute)
-   [<span data-ttu-id="025d1-214">TimestampAttribute</span><span class="sxs-lookup"><span data-stu-id="025d1-214">TimestampAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.timestampattribute)
-   [<span data-ttu-id="025d1-215">ComplexTypeAttribute</span><span class="sxs-lookup"><span data-stu-id="025d1-215">ComplexTypeAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.complextypeattribute)
-   [<span data-ttu-id="025d1-216">ColumnAttribute</span><span class="sxs-lookup"><span data-stu-id="025d1-216">ColumnAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.columnattribute)
-   [<span data-ttu-id="025d1-217">TableAttribute</span><span class="sxs-lookup"><span data-stu-id="025d1-217">TableAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.tableattribute)
-   [<span data-ttu-id="025d1-218">InversePropertyAttribute</span><span class="sxs-lookup"><span data-stu-id="025d1-218">InversePropertyAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.inversepropertyattribute)
-   [<span data-ttu-id="025d1-219">ForeignKeyAttribute</span><span class="sxs-lookup"><span data-stu-id="025d1-219">ForeignKeyAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.foreignkeyattribute)
-   [<span data-ttu-id="025d1-220">DatabaseGeneratedAttribute</span><span class="sxs-lookup"><span data-stu-id="025d1-220">DatabaseGeneratedAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.databasegeneratedattribute)
-   [<span data-ttu-id="025d1-221">NotMappedAttribute</span><span class="sxs-lookup"><span data-stu-id="025d1-221">NotMappedAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.notmappedattribute)

## <a name="7-fluent-api"></a><span data-ttu-id="025d1-222">7. Interfejs Fluent API</span><span class="sxs-lookup"><span data-stu-id="025d1-222">7. Fluent API</span></span>

<span data-ttu-id="025d1-223">W poprzedniej sekcji przyjrzeliśmy się przy użyciu adnotacji danych do uzupełnienia lub zastąpić, co zostało wykryte przez Konwencję.</span><span class="sxs-lookup"><span data-stu-id="025d1-223">In the previous section we looked at using Data Annotations to supplement or override what was detected by convention.</span></span> <span data-ttu-id="025d1-224">Inny sposób, aby skonfigurować model jest za pośrednictwem interfejsu API fluent Code First.</span><span class="sxs-lookup"><span data-stu-id="025d1-224">The other way to configure the model is via the Code First fluent API.</span></span>

<span data-ttu-id="025d1-225">Większość konfiguracji modelu można zrobić za pomocą prostych danych adnotacji.</span><span class="sxs-lookup"><span data-stu-id="025d1-225">Most model configuration can be done using simple data annotations.</span></span> <span data-ttu-id="025d1-226">Interfejs fluent API jest bardziej zaawansowany sposób określania Konfiguracja modelu, który obejmuje wszystko, co dodatkowo zrobić adnotacje danych na niektórych bardziej zaawansowanych konfiguracji nie jest możliwe przy użyciu adnotacji danych.</span><span class="sxs-lookup"><span data-stu-id="025d1-226">The fluent API is a more advanced way of specifying model configuration that covers everything that data annotations can do in addition to some more advanced configuration not possible with data annotations.</span></span> <span data-ttu-id="025d1-227">Adnotacje danych i wygodnego interfejsu API mogą być używane razem.</span><span class="sxs-lookup"><span data-stu-id="025d1-227">Data annotations and the fluent API can be used together.</span></span>

<span data-ttu-id="025d1-228">Dostęp do interfejsu API fluent zastąpienia metody OnModelCreating w DbContext.</span><span class="sxs-lookup"><span data-stu-id="025d1-228">To access the fluent API you override the OnModelCreating method in DbContext.</span></span> <span data-ttu-id="025d1-229">Załóżmy, że Chcieliśmy, aby zmienić nazwę kolumny, która User.DisplayName znajduje się w celu wyświetlenia\_nazwy.</span><span class="sxs-lookup"><span data-stu-id="025d1-229">Let’s say we wanted to rename the column that User.DisplayName is stored in to display\_name.</span></span>

-   <span data-ttu-id="025d1-230">Zastąp metodę OnModelCreating na BloggingContext następującym kodem</span><span class="sxs-lookup"><span data-stu-id="025d1-230">Override the OnModelCreating method on BloggingContext with the following code</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
    public DbSet<User> Users { get; set; }

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Entity<User>()
            .Property(u => u.DisplayName)
            .HasColumnName("display_name");
    }
}
```

-   <span data-ttu-id="025d1-231">Użyj **ChangeDisplayName migracji Dodaj** polecenia do tworzenia szkieletu migracji, aby zastosować te zmiany do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="025d1-231">Use the **Add-Migration ChangeDisplayName** command to scaffold a migration to apply these changes to the database.</span></span>
-   <span data-ttu-id="025d1-232">Uruchom **Update-Database** polecenie, aby zastosować nową migrację do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="025d1-232">Run the **Update-Database** command to apply the new migration to the database.</span></span>

<span data-ttu-id="025d1-233">W kolumnie Nazwa wyświetlana została zmieniona na wyświetlanie\_nazwy:</span><span class="sxs-lookup"><span data-stu-id="025d1-233">The DisplayName column is now renamed to display\_name:</span></span>

![SchemaWithDisplayNameRenamed](~/ef6/media/schemawithdisplaynamerenamed.png)

## <a name="summary"></a><span data-ttu-id="025d1-235">Podsumowanie</span><span class="sxs-lookup"><span data-stu-id="025d1-235">Summary</span></span>

<span data-ttu-id="025d1-236">W tym przewodniku przyjrzeliśmy się rozwiązania deweloperskiego Code First przy użyciu nowej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="025d1-236">In this walkthrough we looked at Code First development using a new database.</span></span> <span data-ttu-id="025d1-237">Firma Microsoft zdefiniowany model przy użyciu klasy, a następnie ten model umożliwia tworzenie bazy danych i przechowywać i pobierać dane.</span><span class="sxs-lookup"><span data-stu-id="025d1-237">We defined a model using classes then used that model to create a database and store and retrieve data.</span></span> <span data-ttu-id="025d1-238">Gdy baza danych została utworzona użyliśmy migracje Code First można zmienić schematu jako nasz model ewoluował.</span><span class="sxs-lookup"><span data-stu-id="025d1-238">Once the database was created we used Code First Migrations to change the schema as our model evolved.</span></span> <span data-ttu-id="025d1-239">Widzieliśmy również sposób konfigurowania model przy użyciu adnotacji danych i interfejsu API Fluent.</span><span class="sxs-lookup"><span data-stu-id="025d1-239">We also saw how to configure a model using Data Annotations and the Fluent API.</span></span>