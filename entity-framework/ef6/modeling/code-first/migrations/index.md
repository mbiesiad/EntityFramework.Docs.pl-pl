---
title: Migracje Code First - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 36591d8f-36e1-4835-8a51-90f34f633d1e
ms.openlocfilehash: e5a91af73bab9d45b0f1f4242ce503c6b6f407f6
ms.sourcegitcommit: 159c2e9afed7745e7512730ffffaf154bcf2ff4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2019
ms.locfileid: "55668703"
---
# <a name="code-first-migrations"></a><span data-ttu-id="079bc-102">Migracje Code First</span><span class="sxs-lookup"><span data-stu-id="079bc-102">Code First Migrations</span></span>
<span data-ttu-id="079bc-103">Migracje Code First jest zalecany sposób rozwijać schemat bazy danych aplikacji, jeśli używasz kodu pierwszego przepływu pracy.</span><span class="sxs-lookup"><span data-stu-id="079bc-103">Code First Migrations is the recommended way to evolve your application's database schema if you are using the Code First workflow.</span></span> <span data-ttu-id="079bc-104">Migracje udostępniają zestaw narzędzi, które umożliwiają:</span><span class="sxs-lookup"><span data-stu-id="079bc-104">Migrations provide a set of tools that allow:</span></span>

1. <span data-ttu-id="079bc-105">Tworzenie początkowej bazy danych, która współdziała z modelu platformy EF</span><span class="sxs-lookup"><span data-stu-id="079bc-105">Create an initial database that works with your EF model</span></span>
2. <span data-ttu-id="079bc-106">Generowanie migracji, aby śledzić zmiany wprowadzone do modelu platformy EF</span><span class="sxs-lookup"><span data-stu-id="079bc-106">Generating migrations to keep track of changes you make to your EF model</span></span>
2. <span data-ttu-id="079bc-107">Bazy danych na bieżąco z tymi zmianami</span><span class="sxs-lookup"><span data-stu-id="079bc-107">Keep your database up to date with those changes</span></span>

<span data-ttu-id="079bc-108">Następujące Instruktaż zapewnia przegląd migracje Code First platformy Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="079bc-108">The following walkthrough will provide an overview Code First Migrations in Entity Framework.</span></span> <span data-ttu-id="079bc-109">Możesz ukończenia całego instruktażu lub od razu przejść do tematu, który Cię interesuje.</span><span class="sxs-lookup"><span data-stu-id="079bc-109">You can either complete the entire walkthrough or skip to the topic you are interested in.</span></span> <span data-ttu-id="079bc-110">Omówiono następujące tematy:</span><span class="sxs-lookup"><span data-stu-id="079bc-110">The following topics are covered:</span></span>

## <a name="building-an-initial-model--database"></a><span data-ttu-id="079bc-111">Tworzenie początkowej modelu i bazy danych</span><span class="sxs-lookup"><span data-stu-id="079bc-111">Building an Initial Model & Database</span></span>

<span data-ttu-id="079bc-112">Zanim zaczniemy, za pomocą migracji potrzebujemy projektu i model Code First chcesz pracować.</span><span class="sxs-lookup"><span data-stu-id="079bc-112">Before we start using migrations we need a project and a Code First model to work with.</span></span> <span data-ttu-id="079bc-113">W tym przewodniku są użyjemy canonical **Blog** i **wpis** modelu.</span><span class="sxs-lookup"><span data-stu-id="079bc-113">For this walkthrough we are going to use the canonical **Blog** and **Post** model.</span></span>

-   <span data-ttu-id="079bc-114">Utwórz nową **MigrationsDemo** aplikacji konsoli</span><span class="sxs-lookup"><span data-stu-id="079bc-114">Create a new **MigrationsDemo** Console application</span></span>
-   <span data-ttu-id="079bc-115">Dodaj najnowszą wersję **EntityFramework** pakiet NuGet do projektu</span><span class="sxs-lookup"><span data-stu-id="079bc-115">Add the latest version of the **EntityFramework** NuGet package to the project</span></span>
    -   <span data-ttu-id="079bc-116">**Narzędzia —&gt; Menedżer pakietów biblioteki —&gt; Konsola Menedżera pakietów**</span><span class="sxs-lookup"><span data-stu-id="079bc-116">**Tools –&gt; Library Package Manager –&gt; Package Manager Console**</span></span>
    -   <span data-ttu-id="079bc-117">Uruchom **EntityFramework instalacji pakietu** polecenia</span><span class="sxs-lookup"><span data-stu-id="079bc-117">Run the **Install-Package EntityFramework** command</span></span>
-   <span data-ttu-id="079bc-118">Dodaj **Model.cs** pliku z kodem, pokazano poniżej.</span><span class="sxs-lookup"><span data-stu-id="079bc-118">Add a **Model.cs** file with the code shown below.</span></span> <span data-ttu-id="079bc-119">Ten kod definiuje pojedynczy **Blog** klasę, która sprawia, że nasz model domeny i **BlogContext** klasę, która jest nasz kontekst EF Code First</span><span class="sxs-lookup"><span data-stu-id="079bc-119">This code defines a single **Blog** class that makes up our domain model and a **BlogContext** class that is our EF Code First context</span></span>

  ``` csharp
      using System.Data.Entity;
      using System.Collections.Generic;
      using System.ComponentModel.DataAnnotations;
      using System.Data.Entity.Infrastructure;

      namespace MigrationsDemo
      {
          public class BlogContext : DbContext
          {
              public DbSet<Blog> Blogs { get; set; }
          }

          public class Blog
          {
              public int BlogId { get; set; }
              public string Name { get; set; }
          }
      }
  ```

-   <span data-ttu-id="079bc-120">Teraz, gdy model, nadszedł czas na jej używać do wykonywania dostęp do danych.</span><span class="sxs-lookup"><span data-stu-id="079bc-120">Now that we have a model it’s time to use it to perform data access.</span></span> <span data-ttu-id="079bc-121">Aktualizacja **Program.cs** pliku z kodem, pokazano poniżej.</span><span class="sxs-lookup"><span data-stu-id="079bc-121">Update the **Program.cs** file with the code shown below.</span></span>

  ``` csharp
      using System;
      using System.Collections.Generic;
      using System.Linq;
      using System.Text;

      namespace MigrationsDemo
      {
          class Program
          {
              static void Main(string[] args)
              {
                  using (var db = new BlogContext())
                  {
                      db.Blogs.Add(new Blog { Name = "Another Blog " });
                      db.SaveChanges();

                      foreach (var blog in db.Blogs)
                      {
                          Console.WriteLine(blog.Name);
                      }
                  }

                  Console.WriteLine("Press any key to exit...");
                  Console.ReadKey();
              }
          }
      }
  ```

-   <span data-ttu-id="079bc-122">Uruchom aplikację i zostanie wyświetlony **MigrationsCodeDemo.BlogContext** baza danych została utworzona dla Ciebie.</span><span class="sxs-lookup"><span data-stu-id="079bc-122">Run your application and you will see that a **MigrationsCodeDemo.BlogContext** database is created for you.</span></span>

    ![Bazy danych LocalDB](~/ef6/media/databaselocaldb.png)

## <a name="enabling-migrations"></a><span data-ttu-id="079bc-124">Włączanie migracji</span><span class="sxs-lookup"><span data-stu-id="079bc-124">Enabling Migrations</span></span>

<span data-ttu-id="079bc-125">Nadszedł czas na pewnych zmian więcej w naszym modelu.</span><span class="sxs-lookup"><span data-stu-id="079bc-125">It’s time to make some more changes to our model.</span></span>

-   <span data-ttu-id="079bc-126">Umożliwia wprowadzenie właściwość adres Url do klasy blogu.</span><span class="sxs-lookup"><span data-stu-id="079bc-126">Let’s introduce a Url property to the Blog class.</span></span>

``` csharp
    public string Url { get; set; }
```

<span data-ttu-id="079bc-127">Jeśli zamierzasz uruchomić aplikację ponownie otrzymamy InvalidOperationException, podając *modelu kopii kontekstu "BlogContext" została zmieniona od czasu utworzenia bazy danych. Należy rozważyć użycie migracje Code First w aktualizacji bazy danych (* [ *http://go.microsoft.com/fwlink/?LinkId=238269* ](https://go.microsoft.com/fwlink/?LinkId=238269) *).*</span><span class="sxs-lookup"><span data-stu-id="079bc-127">If you were to run the application again you would get an InvalidOperationException stating *The model backing the 'BlogContext' context has changed since the database was created. Consider using Code First Migrations to update the database (* [*http://go.microsoft.com/fwlink/?LinkId=238269*](https://go.microsoft.com/fwlink/?LinkId=238269)*).*</span></span>

<span data-ttu-id="079bc-128">Jak sugeruje wyjątek, jest czas, aby rozpocząć korzystanie z migracje Code First.</span><span class="sxs-lookup"><span data-stu-id="079bc-128">As the exception suggests, it’s time to start using Code First Migrations.</span></span> <span data-ttu-id="079bc-129">Pierwszym krokiem jest włączanie funkcji migracje dla nasz kontekst.</span><span class="sxs-lookup"><span data-stu-id="079bc-129">The first step is to enable migrations for our context.</span></span>

-   <span data-ttu-id="079bc-130">Uruchom **Enable-Migrations** polecenia w konsoli Menedżera pakietów</span><span class="sxs-lookup"><span data-stu-id="079bc-130">Run the **Enable-Migrations** command in Package Manager Console</span></span>

    <span data-ttu-id="079bc-131">To polecenie został dodany **migracje** folderu do naszego projektu.</span><span class="sxs-lookup"><span data-stu-id="079bc-131">This command has added a **Migrations** folder to our project.</span></span> <span data-ttu-id="079bc-132">Ten nowy folder zawiera dwa pliki:</span><span class="sxs-lookup"><span data-stu-id="079bc-132">This new folder contains two files:</span></span>

-   <span data-ttu-id="079bc-133">**Klasa konfiguracji.**</span><span class="sxs-lookup"><span data-stu-id="079bc-133">**The Configuration class.**</span></span> <span data-ttu-id="079bc-134">Ta klasa pozwala skonfigurować sposób działania migracji w Twoim kontekście.</span><span class="sxs-lookup"><span data-stu-id="079bc-134">This class allows you to configure how Migrations behaves for your context.</span></span> <span data-ttu-id="079bc-135">W tym przewodniku po prostu używamy domyślnej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="079bc-135">For this walkthrough we will just use the default configuration.</span></span>
    <span data-ttu-id="079bc-136">*Ponieważ istnieje tylko pojedynczy kontekst Code First w projekcie, Enable-Migrations jest wypełniane automatycznie typ kontekstu, którego dotyczy ta konfiguracja.*</span><span class="sxs-lookup"><span data-stu-id="079bc-136">*Because there is just a single Code First context in your project, Enable-Migrations has automatically filled in the context type this configuration applies to.*</span></span>
-   <span data-ttu-id="079bc-137">**Migracja InitialCreate**.</span><span class="sxs-lookup"><span data-stu-id="079bc-137">**An InitialCreate migration**.</span></span> <span data-ttu-id="079bc-138">Ta migracja został wygenerowany, ponieważ już mieliśmy Code First utworzyć bazę danych, zanim umożliwiliśmy migracji.</span><span class="sxs-lookup"><span data-stu-id="079bc-138">This migration was generated because we already had Code First create a database for us, before we enabled migrations.</span></span> <span data-ttu-id="079bc-139">Kod w tej migracji szkieletu reprezentuje obiektów, które zostały już utworzone w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="079bc-139">The code in this scaffolded migration represents the objects that have already been created in the database.</span></span> <span data-ttu-id="079bc-140">W tym przypadku, który jest **Blog** tabeli za pomocą **BlogId** i **nazwa** kolumn.</span><span class="sxs-lookup"><span data-stu-id="079bc-140">In our case that is the **Blog** table with a **BlogId** and **Name** columns.</span></span> <span data-ttu-id="079bc-141">Nazwa pliku zawiera sygnaturę czasową, aby pomóc w kolejności.</span><span class="sxs-lookup"><span data-stu-id="079bc-141">The filename includes a timestamp to help with ordering.</span></span>
    <span data-ttu-id="079bc-142">*Jeśli baza danych nie został już utworzony tej migracji InitialCreate czy nie zostały dodane do projektu. Zamiast tego po raz pierwszy nazywamy migracji Dodaj kod, aby utworzyć te tabele będą szkieletu do nowej migracji.*</span><span class="sxs-lookup"><span data-stu-id="079bc-142">*If the database had not already been created this InitialCreate migration would not have been added to the project. Instead, the first time we call Add-Migration the code to create these tables would be scaffolded to a new migration.*</span></span>

### <a name="multiple-models-targeting-the-same-database"></a><span data-ttu-id="079bc-143">Wiele modeli przeznaczonych dla tej samej bazy danych</span><span class="sxs-lookup"><span data-stu-id="079bc-143">Multiple Models Targeting the Same Database</span></span>

<span data-ttu-id="079bc-144">W przypadku używania wersji przed EF6, tylko jeden model Code First może służyć do generowania/Zarządzanie schematami bazy danych.</span><span class="sxs-lookup"><span data-stu-id="079bc-144">When using versions prior to EF6, only one Code First model could be used to generate/manage the schema of a database.</span></span> <span data-ttu-id="079bc-145">Jest to wynikiem pojedynczej  **\_ \_MigrationsHistory** tabeli na bazę danych w sposób identyfikowania zapisy, które należą do modelu.</span><span class="sxs-lookup"><span data-stu-id="079bc-145">This is the result of a single **\_\_MigrationsHistory** table per database with no way to identify which entries belong to which model.</span></span>

<span data-ttu-id="079bc-146">Począwszy od platformy EF6, **konfiguracji** klasa zawiera **elementu ContextKey** właściwości.</span><span class="sxs-lookup"><span data-stu-id="079bc-146">Starting with EF6, the **Configuration** class includes a **ContextKey** property.</span></span> <span data-ttu-id="079bc-147">Jest to zabezpieczenie jako unikatowy identyfikator dla każdego modelu Code First.</span><span class="sxs-lookup"><span data-stu-id="079bc-147">This acts as a unique identifier for each Code First model.</span></span> <span data-ttu-id="079bc-148">W kolumnie  **\_ \_MigrationsHistory** tabeli umożliwia wpisy z wielu modeli, aby udostępnić w tabeli.</span><span class="sxs-lookup"><span data-stu-id="079bc-148">A corresponding column in the **\_\_MigrationsHistory** table allows entries from multiple models to share the table.</span></span> <span data-ttu-id="079bc-149">Ta właściwość jest domyślnie do w pełni kwalifikowanej nazwy kontekstu.</span><span class="sxs-lookup"><span data-stu-id="079bc-149">By default, this property is set to the fully qualified name of your context.</span></span>

## <a name="generating--running-migrations"></a><span data-ttu-id="079bc-150">Generowanie i uruchamianie migracji</span><span class="sxs-lookup"><span data-stu-id="079bc-150">Generating & Running Migrations</span></span>

<span data-ttu-id="079bc-151">Migracje Code First ma dwa podstawowe polecenia, które ma być zapoznanie się z.</span><span class="sxs-lookup"><span data-stu-id="079bc-151">Code First Migrations has two primary commands that you are going to become familiar with.</span></span>

-   <span data-ttu-id="079bc-152">**Dodaj migracji** będzie tworzenia szkieletu dalej migracji, w oparciu o zmiany wprowadzone do modelu od chwili utworzenia ostatniej migracji</span><span class="sxs-lookup"><span data-stu-id="079bc-152">**Add-Migration** will scaffold the next migration based on changes you have made to your model since the last migration was created</span></span>
-   <span data-ttu-id="079bc-153">**Update-Database** Zastosuj wszelkie oczekujące migracji do bazy danych</span><span class="sxs-lookup"><span data-stu-id="079bc-153">**Update-Database** will apply any pending migrations to the database</span></span>

<span data-ttu-id="079bc-154">Potrzebujemy do tworzenia szkieletu migracji, aby zadbać o nową właściwość adres Url, które dodaliśmy.</span><span class="sxs-lookup"><span data-stu-id="079bc-154">We need to scaffold a migration to take care of the new Url property we have added.</span></span> <span data-ttu-id="079bc-155">**Migracji Dodaj** polecenie umożliwia nam nazwij te migracji, po prostu nazwiemy naszych **AddBlogUrl**.</span><span class="sxs-lookup"><span data-stu-id="079bc-155">The **Add-Migration** command allows us to give these migrations a name, let’s just call ours **AddBlogUrl**.</span></span>

-   <span data-ttu-id="079bc-156">Uruchom **AddBlogUrl migracji Dodaj** polecenia w konsoli Menedżera pakietów</span><span class="sxs-lookup"><span data-stu-id="079bc-156">Run the **Add-Migration AddBlogUrl** command in Package Manager Console</span></span>
-   <span data-ttu-id="079bc-157">W **migracje** folderu w efekcie powstał nowy **AddBlogUrl** migracji.</span><span class="sxs-lookup"><span data-stu-id="079bc-157">In the **Migrations** folder we now have a new **AddBlogUrl** migration.</span></span> <span data-ttu-id="079bc-158">Filename migracji wstępnie ustalony z sygnaturą czasową pomagające w kolejności</span><span class="sxs-lookup"><span data-stu-id="079bc-158">The migration filename is pre-fixed with a timestamp to help with ordering</span></span>

``` csharp
    namespace MigrationsDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddBlogUrl : DbMigration
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

<span data-ttu-id="079bc-159">Firma Microsoft, można teraz edytować lub dodać do tej migracji, ale wszystko wygląda dość dobrze.</span><span class="sxs-lookup"><span data-stu-id="079bc-159">We could now edit or add to this migration but everything looks pretty good.</span></span> <span data-ttu-id="079bc-160">Użyjmy **Update-Database** dotyczą tej migracji bazy danych.</span><span class="sxs-lookup"><span data-stu-id="079bc-160">Let’s use **Update-Database** to apply this migration to the database.</span></span>

-   <span data-ttu-id="079bc-161">Uruchom **Update-Database** polecenia w konsoli Menedżera pakietów</span><span class="sxs-lookup"><span data-stu-id="079bc-161">Run the **Update-Database** command in Package Manager Console</span></span>
-   <span data-ttu-id="079bc-162">Migracje Code First zostanie porównany migracji w naszym **migracje** folder z tymi, które zostały zastosowane do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="079bc-162">Code First Migrations will compare the migrations in our **Migrations** folder with the ones that have been applied to the database.</span></span> <span data-ttu-id="079bc-163">Zostanie on wyświetlony **AddBlogUrl** migracji musi być zastosowane i uruchom go.</span><span class="sxs-lookup"><span data-stu-id="079bc-163">It will see that the **AddBlogUrl** migration needs to be applied, and run it.</span></span>

<span data-ttu-id="079bc-164">**MigrationsDemo.BlogContext** bazy danych został zaktualizowany do uwzględnienia **adresu Url** kolumny w **blogi** tabeli.</span><span class="sxs-lookup"><span data-stu-id="079bc-164">The **MigrationsDemo.BlogContext** database is now updated to include the **Url** column in the **Blogs** table.</span></span>

## <a name="customizing-migrations"></a><span data-ttu-id="079bc-165">Dostosowywanie migracji</span><span class="sxs-lookup"><span data-stu-id="079bc-165">Customizing Migrations</span></span>

<span data-ttu-id="079bc-166">Do tej pory mamy już i uruchamianie migracji bez wprowadzania żadnych zmian.</span><span class="sxs-lookup"><span data-stu-id="079bc-166">So far we’ve generated and run a migration without making any changes.</span></span> <span data-ttu-id="079bc-167">Teraz Przyjrzyjmy się edycją kodu, który pobiera wygenerowany domyślnie.</span><span class="sxs-lookup"><span data-stu-id="079bc-167">Now let’s look at editing the code that gets generated by default.</span></span>

-   <span data-ttu-id="079bc-168">Nadszedł czas, aby wprowadzić pewne zmiany więcej w naszym modelu, możemy dodać nową **ocena** właściwości **blogu** klasy</span><span class="sxs-lookup"><span data-stu-id="079bc-168">It’s time to make some more changes to our model, let’s add a new **Rating** property to the **Blog** class</span></span>

``` csharp
    public int Rating { get; set; }
```

-   <span data-ttu-id="079bc-169">Możemy również dodać nowy **wpis** klasy</span><span class="sxs-lookup"><span data-stu-id="079bc-169">Let's also add a new **Post** class</span></span>

``` csharp
    public class Post
    {
        public int PostId { get; set; }
        [MaxLength(200)]
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
    }
```

-   <span data-ttu-id="079bc-170">Dodamy również **wpisy** kolekcji **Blog** klasy w celu utworzenia drugiej stronie relacji między **Blog** i **wpis**</span><span class="sxs-lookup"><span data-stu-id="079bc-170">We'll also add a **Posts** collection to the **Blog** class to form the other end of the relationship between **Blog** and **Post**</span></span>

``` csharp
    public virtual List<Post> Posts { get; set; }
```

<span data-ttu-id="079bc-171">Użyjemy **migracji Dodaj** polecenie, aby umożliwić migracje Code First tworzenia szkieletu jego najbardziej prawdopodobny wybór na migrację dla nas.</span><span class="sxs-lookup"><span data-stu-id="079bc-171">We'll use the **Add-Migration** command to let Code First Migrations scaffold its best guess at the migration for us.</span></span> <span data-ttu-id="079bc-172">Zamierzamy wywołania tej migracji **AddPostClass**.</span><span class="sxs-lookup"><span data-stu-id="079bc-172">We’re going to call this migration **AddPostClass**.</span></span>

-   <span data-ttu-id="079bc-173">Uruchom **AddPostClass migracji Dodaj** polecenia w konsoli Menedżera pakietów.</span><span class="sxs-lookup"><span data-stu-id="079bc-173">Run the **Add-Migration AddPostClass** command in Package Manager Console.</span></span>

<span data-ttu-id="079bc-174">Migracje Code First jak całkiem Niezłe rozwiązanie zadanie tworzenia szkieletów te zmiany, ale istnieje kilka kwestii, które firma Microsoft może wystąpić potrzeba zmiany:</span><span class="sxs-lookup"><span data-stu-id="079bc-174">Code First Migrations did a pretty good job of scaffolding these changes, but there are some things we might want to change:</span></span>

1.  <span data-ttu-id="079bc-175">Najpierw w górę, możemy dodać unikatowego indeksu **Posts.Title** kolumny (Dodawanie w wierszu 22 & 29 w poniższym kodzie).</span><span class="sxs-lookup"><span data-stu-id="079bc-175">First up, let’s add a unique index to **Posts.Title** column (Adding in line 22 & 29 in the code below).</span></span>
2.  <span data-ttu-id="079bc-176">Dodajemy również dopuszcza **Blogs.Rating** kolumny.</span><span class="sxs-lookup"><span data-stu-id="079bc-176">We’re also adding a non-nullable **Blogs.Rating** column.</span></span> <span data-ttu-id="079bc-177">W przypadku istniejących danych w tabeli zostaną zostaną przypisane domyślne CLR typu danych dla nowej kolumny (ocena jest liczba całkowita, tak byłoby **0**).</span><span class="sxs-lookup"><span data-stu-id="079bc-177">If there is any existing data in the table it will get assigned the CLR default of the data type for new column (Rating is integer, so that would be **0**).</span></span> <span data-ttu-id="079bc-178">Ale chcemy określić wartość domyślną **3** więc w istniejącym wiersze **blogi** tabeli rozpoczyna się od klasyfikacji znośnego.</span><span class="sxs-lookup"><span data-stu-id="079bc-178">But we want to specify a default value of **3** so that existing rows in the **Blogs** table will start with a decent rating.</span></span>
    <span data-ttu-id="079bc-179">(Wartość domyślna określona w wierszu 24 kod poniżej można znaleźć)</span><span class="sxs-lookup"><span data-stu-id="079bc-179">(You can see the default value specified on line 24 of the code below)</span></span>

``` csharp
    namespace MigrationsDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddPostClass : DbMigration
        {
            public override void Up()
            {
                CreateTable(
                    "dbo.Posts",
                    c => new
                        {
                            PostId = c.Int(nullable: false, identity: true),
                            Title = c.String(maxLength: 200),
                            Content = c.String(),
                            BlogId = c.Int(nullable: false),
                        })
                    .PrimaryKey(t => t.PostId)
                    .ForeignKey("dbo.Blogs", t => t.BlogId, cascadeDelete: true)
                    .Index(t => t.BlogId)
                    .Index(p => p.Title, unique: true);

                AddColumn("dbo.Blogs", "Rating", c => c.Int(nullable: false, defaultValue: 3));
            }

            public override void Down()
            {
                DropIndex("dbo.Posts", new[] { "Title" });
                DropIndex("dbo.Posts", new[] { "BlogId" });
                DropForeignKey("dbo.Posts", "BlogId", "dbo.Blogs");
                DropColumn("dbo.Blogs", "Rating");
                DropTable("dbo.Posts");
            }
        }
    }
```

<span data-ttu-id="079bc-180">Nasze edytowanych migracji jest gotowy do Przejdź, dlatego użyjemy **Update-Database** Aby przełączyć bazę danych aktualne.</span><span class="sxs-lookup"><span data-stu-id="079bc-180">Our edited migration is ready to go, so let’s use **Update-Database** to bring the database up-to-date.</span></span> <span data-ttu-id="079bc-181">Teraz możemy określić **— pełne** Flaga, dzięki czemu możesz zobaczyć SQL Server, który działa migracje Code First.</span><span class="sxs-lookup"><span data-stu-id="079bc-181">This time let’s specify the **–Verbose** flag so that you can see the SQL that Code First Migrations is running.</span></span>

-   <span data-ttu-id="079bc-182">Uruchom **Update-Database — pełne** polecenia w konsoli Menedżera pakietów.</span><span class="sxs-lookup"><span data-stu-id="079bc-182">Run the **Update-Database –Verbose** command in Package Manager Console.</span></span>

## <a name="data-motion--custom-sql"></a><span data-ttu-id="079bc-183">Ruchu danych niestandardowymi SQL</span><span class="sxs-lookup"><span data-stu-id="079bc-183">Data Motion / Custom SQL</span></span>

<span data-ttu-id="079bc-184">Do tej pory mają przyjrzeliśmy się migracji, które operacje, które nie zmieniać i przenieść wszystkie dane teraz możemy przyjrzeć się coś wymagających przenoszenia niektórych danych.</span><span class="sxs-lookup"><span data-stu-id="079bc-184">So far we have looked at migration operations that don’t change or move any data, now let’s look at something that needs to move some data around.</span></span> <span data-ttu-id="079bc-185">Istnieje jeszcze nie natywną obsługę ruchu danych, ale niektóre dowolnych poleceń SQL w dowolnym momencie można uruchomić w naszego skryptu.</span><span class="sxs-lookup"><span data-stu-id="079bc-185">There is no native support for data motion yet, but we can run some arbitrary SQL commands at any point in our script.</span></span>

-   <span data-ttu-id="079bc-186">Dodajmy **Post.Abstract** właściwość nasz model.</span><span class="sxs-lookup"><span data-stu-id="079bc-186">Let’s add a **Post.Abstract** property to our model.</span></span> <span data-ttu-id="079bc-187">Później, będziemy do wstępnego wypełniania **abstrakcyjne** dla istniejących wpisów od początku przy użyciu jakiś tekst **zawartości** kolumny.</span><span class="sxs-lookup"><span data-stu-id="079bc-187">Later, we’re going to pre-populate the **Abstract** for existing posts using some text from the start of the **Content** column.</span></span>

``` csharp
    public string Abstract { get; set; }
```

<span data-ttu-id="079bc-188">Użyjemy **migracji Dodaj** polecenie, aby umożliwić migracje Code First tworzenia szkieletu jego najbardziej prawdopodobny wybór na migrację dla nas.</span><span class="sxs-lookup"><span data-stu-id="079bc-188">We'll use the **Add-Migration** command to let Code First Migrations scaffold its best guess at the migration for us.</span></span>

-   <span data-ttu-id="079bc-189">Uruchom **AddPostAbstract migracji Dodaj** polecenia w konsoli Menedżera pakietów.</span><span class="sxs-lookup"><span data-stu-id="079bc-189">Run the **Add-Migration AddPostAbstract** command in Package Manager Console.</span></span>
-   <span data-ttu-id="079bc-190">Wygenerowany migracji zajmie się zmiany schematu, ale chcemy również do wstępnego wypełniania **abstrakcyjne** kolumny przy użyciu 100 pierwszych znaków zawartości dla każdego wpisu.</span><span class="sxs-lookup"><span data-stu-id="079bc-190">The generated migration takes care of the schema changes but we also want to pre-populate the **Abstract** column using the first 100 characters of content for each post.</span></span> <span data-ttu-id="079bc-191">Możemy to zrobić przez rozwijanej opuszcza SQL i uruchamianie **aktualizacji** instrukcji po dodaniu kolumny.</span><span class="sxs-lookup"><span data-stu-id="079bc-191">We can do this by dropping down to SQL and running an **UPDATE** statement after the column is added.</span></span>
    <span data-ttu-id="079bc-192">(Dodanie w wierszu 12 w poniższym kodzie)</span><span class="sxs-lookup"><span data-stu-id="079bc-192">(Adding in line 12 in the code below)</span></span>

``` csharp
    namespace MigrationsDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddPostAbstract : DbMigration
        {
            public override void Up()
            {
                AddColumn("dbo.Posts", "Abstract", c => c.String());

                Sql("UPDATE dbo.Posts SET Abstract = LEFT(Content, 100) WHERE Abstract IS NULL");
            }

            public override void Down()
            {
                DropColumn("dbo.Posts", "Abstract");
            }
        }
    }
```

<span data-ttu-id="079bc-193">Nasze edytowanych migracji jest dobra, dlatego użyjemy **Update-Database** Aby przełączyć bazę danych aktualne.</span><span class="sxs-lookup"><span data-stu-id="079bc-193">Our edited migration is looking good, so let’s use **Update-Database** to bring the database up-to-date.</span></span> <span data-ttu-id="079bc-194">Firma Microsoft będzie określić **— pełne** Flaga, dzięki czemu możemy zobaczyć SQL są uruchamiane w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="079bc-194">We’ll specify the **–Verbose** flag so that we can see the SQL being run against the database.</span></span>

-   <span data-ttu-id="079bc-195">Uruchom **Update-Database — pełne** polecenia w konsoli Menedżera pakietów.</span><span class="sxs-lookup"><span data-stu-id="079bc-195">Run the **Update-Database –Verbose** command in Package Manager Console.</span></span>

## <a name="migrate-to-a-specific-version-including-downgrade"></a><span data-ttu-id="079bc-196">Migrowanie do określonej wersji (w tym obniżenia poziomu)</span><span class="sxs-lookup"><span data-stu-id="079bc-196">Migrate to a Specific Version (Including Downgrade)</span></span>

<span data-ttu-id="079bc-197">Do tej pory zawsze uaktualniliśmy do najnowszych migracji, ale mogą zaistnieć sytuacje, kiedy zechcesz, uaktualnianie i obniżanie wersji do migracji.</span><span class="sxs-lookup"><span data-stu-id="079bc-197">So far we have always upgraded to the latest migration, but there may be times when you want upgrade/downgrade to a specific migration.</span></span>

<span data-ttu-id="079bc-198">Załóżmy, że chcemy przeprowadzić migrację naszych bazy danych do stanu sprzed po uruchomieniu naszych **AddBlogUrl** migracji.</span><span class="sxs-lookup"><span data-stu-id="079bc-198">Let’s say we want to migrate our database to the state it was in after running our **AddBlogUrl** migration.</span></span> <span data-ttu-id="079bc-199">Możemy użyć **— TargetMigration** przełącznika na starszą wersję tej migracji.</span><span class="sxs-lookup"><span data-stu-id="079bc-199">We can use the **–TargetMigration** switch to downgrade to this migration.</span></span>

-   <span data-ttu-id="079bc-200">Uruchom **Update-Database-TargetMigration: AddBlogUrl** polecenia w konsoli Menedżera pakietów.</span><span class="sxs-lookup"><span data-stu-id="079bc-200">Run the **Update-Database –TargetMigration: AddBlogUrl** command in Package Manager Console.</span></span>

<span data-ttu-id="079bc-201">To polecenie uruchomi skrypt w dół dla naszych **AddBlogAbstract** i **AddPostClass** migracji.</span><span class="sxs-lookup"><span data-stu-id="079bc-201">This command will run the Down script for our **AddBlogAbstract** and **AddPostClass** migrations.</span></span>

<span data-ttu-id="079bc-202">Jeśli chcesz przeprowadzić wdrożenie aż z powrotem do pustej bazy danych, wówczas można użyć **Update-Database-TargetMigration: $InitialDatabase** polecenia.</span><span class="sxs-lookup"><span data-stu-id="079bc-202">If you want to roll all the way back to an empty database then you can use the **Update-Database –TargetMigration: $InitialDatabase** command.</span></span>

## <a name="getting-a-sql-script"></a><span data-ttu-id="079bc-203">Pobieranie skryptu SQL</span><span class="sxs-lookup"><span data-stu-id="079bc-203">Getting a SQL Script</span></span>

<span data-ttu-id="079bc-204">Jeśli inny Deweloper chce tych zmian na swoich maszynach one po prostu synchronizacji po sprawdzenie zmian w systemie kontroli źródła.</span><span class="sxs-lookup"><span data-stu-id="079bc-204">If another developer wants these changes on their machine they can just sync once we check our changes into source control.</span></span> <span data-ttu-id="079bc-205">Po naszej nowej migracji one po prostu uruchomić polecenie Update-Database, aby zmiany zastosowana lokalnie.</span><span class="sxs-lookup"><span data-stu-id="079bc-205">Once they have our new migrations they can just run the Update-Database command to have the changes applied locally.</span></span> <span data-ttu-id="079bc-206">Jednak jeśli chcemy wdrożyć tych zmian na serwerze testowym i po pewnym czasie produkcji, prawdopodobnie chcemy skrypt SQL, które firma Microsoft może przekazują do naszych DBA.</span><span class="sxs-lookup"><span data-stu-id="079bc-206">However if we want to push these changes out to a test server, and eventually production, we probably want a SQL script we can hand off to our DBA.</span></span>

-   <span data-ttu-id="079bc-207">Uruchom **Update-Database** polecenia, ale tym razem określ **— skrypt** Flaga tak, aby zmiany są zapisywane do skryptu zamiast stosowane.</span><span class="sxs-lookup"><span data-stu-id="079bc-207">Run the **Update-Database** command but this time specify the **–Script** flag so that changes are written to a script rather than applied.</span></span> <span data-ttu-id="079bc-208">Firma Microsoft będzie również określić źródło i cel migracji można wygenerować skryptu dla.</span><span class="sxs-lookup"><span data-stu-id="079bc-208">We’ll also specify a source and target migration to generate the script for.</span></span> <span data-ttu-id="079bc-209">Chcemy, aby skrypt, aby przejść od pustej bazy danych (**$InitialDatabase**) do najnowszej wersji (migracja **AddPostAbstract**).</span><span class="sxs-lookup"><span data-stu-id="079bc-209">We want a script to go from an empty database (**$InitialDatabase**) to the latest version (migration **AddPostAbstract**).</span></span>
    <span data-ttu-id="079bc-210">*Jeśli nie określisz migracji docelowego migracji użyje najnowszych migracji jako element docelowy. Jeśli nie określisz migracje źródła migracje użyje bieżący stan bazy danych.*</span><span class="sxs-lookup"><span data-stu-id="079bc-210">*If you don’t specify a target migration, Migrations will use the latest migration as the target. If you don't specify a source migrations, Migrations will use the current state of the database.*</span></span>
-   <span data-ttu-id="079bc-211">Uruchom **Update-Database-Script - SourceMigration: $InitialDatabase - TargetMigration: AddPostAbstract** polecenia w konsoli Menedżera pakietów</span><span class="sxs-lookup"><span data-stu-id="079bc-211">Run the **Update-Database -Script -SourceMigration: $InitialDatabase -TargetMigration: AddPostAbstract** command in Package Manager Console</span></span>

<span data-ttu-id="079bc-212">Migracje Code First uruchomi proces migracji, ale zamiast faktycznie stosowania zmian go będzie zapisać je do pliku SQL dla Ciebie.</span><span class="sxs-lookup"><span data-stu-id="079bc-212">Code First Migrations will run the migration pipeline but instead of actually applying the changes it will write them out to a .sql file for you.</span></span> <span data-ttu-id="079bc-213">Wygenerowany skrypt jest otwierany automatycznie w programie Visual Studio gotowe wyświetlić lub zapisać.</span><span class="sxs-lookup"><span data-stu-id="079bc-213">Once the script is generated, it is opened for you in Visual Studio, ready for you to view or save.</span></span>

### <a name="generating-idempotent-scripts"></a><span data-ttu-id="079bc-214">Generowanie skryptów Idempotentne</span><span class="sxs-lookup"><span data-stu-id="079bc-214">Generating Idempotent Scripts</span></span>

<span data-ttu-id="079bc-215">Począwszy od platformy EF6, jeśli określisz **— SourceMigration $InitialDatabase** wygenerowany skrypt będzie "idempotentne".</span><span class="sxs-lookup"><span data-stu-id="079bc-215">Starting with EF6, if you specify **–SourceMigration $InitialDatabase** then the generated script will be ‘idempotent’.</span></span> <span data-ttu-id="079bc-216">Skrypty Idempotentne można uaktualnić bazy danych obecnie w dowolnej wersji do najnowszej wersji (lub określonej wersji, jeśli używasz **— TargetMigration**).</span><span class="sxs-lookup"><span data-stu-id="079bc-216">Idempotent scripts can upgrade a database currently at any version to the latest version (or the specified version if you use **–TargetMigration**).</span></span> <span data-ttu-id="079bc-217">Wygenerowany skrypt zawiera logikę do sprawdzenia  **\_ \_MigrationsHistory** tabeli i tylko zastosować zmiany, które nie zostały zastosowane wcześniej.</span><span class="sxs-lookup"><span data-stu-id="079bc-217">The generated script includes logic to check the **\_\_MigrationsHistory** table and only apply changes that haven't been previously applied.</span></span>

## <a name="automatically-upgrading-on-application-startup-migratedatabasetolatestversion-initializer"></a><span data-ttu-id="079bc-218">Automatycznie uaktualnianie przy uruchamianiu aplikacji (inicjatorze MigrateDatabaseToLatestVersion)</span><span class="sxs-lookup"><span data-stu-id="079bc-218">Automatically Upgrading on Application Startup (MigrateDatabaseToLatestVersion Initializer)</span></span>

<span data-ttu-id="079bc-219">Jeśli aplikacja jest wdrażana można ją automatycznie uaktualnić bazy danych (stosując wszystkie oczekujące migracje) po uruchomieniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="079bc-219">If you are deploying your application you may want it to automatically upgrade the database (by applying any pending migrations) when the application launches.</span></span> <span data-ttu-id="079bc-220">Można to zrobić, rejestrując **MigrateDatabaseToLatestVersion** inicjatora bazy danych.</span><span class="sxs-lookup"><span data-stu-id="079bc-220">You can do this by registering the **MigrateDatabaseToLatestVersion** database initializer.</span></span> <span data-ttu-id="079bc-221">Inicjator bazy danych zawiera po prostu logikę, który służy do upewnij się, że baza danych jest prawidłowo skonfigurowana.</span><span class="sxs-lookup"><span data-stu-id="079bc-221">A database initializer simply contains some logic that is used to make sure the database is setup correctly.</span></span> <span data-ttu-id="079bc-222">Logika jest uruchamiany po raz pierwszy kontekstu jest używana w ramach procesu aplikacji (**AppDomain**).</span><span class="sxs-lookup"><span data-stu-id="079bc-222">This logic is run the first time the context is used within the application process (**AppDomain**).</span></span>

<span data-ttu-id="079bc-223">Firma Microsoft może aktualizować **Program.cs** pliku, jak pokazano poniżej, aby ustawić **MigrateDatabaseToLatestVersion** inicjator dla BlogContext, zanim użyjemy kontekstu (wiersz 14).</span><span class="sxs-lookup"><span data-stu-id="079bc-223">We can update the **Program.cs** file, as shown below, to set the **MigrateDatabaseToLatestVersion** initializer for BlogContext before we use the context (Line 14).</span></span> <span data-ttu-id="079bc-224">Należy pamiętać, że trzeba będzie również dodać za pomocą instrukcji for **System.Data.Entity** przestrzeni nazw (wiersz 5).</span><span class="sxs-lookup"><span data-stu-id="079bc-224">Note that you also need to add a using statement for the **System.Data.Entity** namespace (Line 5).</span></span>

<span data-ttu-id="079bc-225">*Kiedy tworzymy wystąpienie tego inicjatora, należy określić typ kontekstu (**BlogContext**) i konfiguracji migracji (**konfiguracji**) — Konfiguracja migracji jest klasa, która otrzymała dodany do naszych **migracje** folderu podczas umożliwiliśmy migracji.*</span><span class="sxs-lookup"><span data-stu-id="079bc-225">*When we create an instance of this initializer we need to specify the context type (**BlogContext**) and the migrations configuration (**Configuration**) - the migrations configuration is the class that got added to our **Migrations** folder when we enabled Migrations.*</span></span>

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Data.Entity;
    using MigrationsDemo.Migrations;

    namespace MigrationsDemo
    {
        class Program
        {
            static void Main(string[] args)
            {
                Database.SetInitializer(new MigrateDatabaseToLatestVersion<BlogContext, Configuration>());

                using (var db = new BlogContext())
                {
                    db.Blogs.Add(new Blog { Name = "Another Blog " });
                    db.SaveChanges();

                    foreach (var blog in db.Blogs)
                    {
                        Console.WriteLine(blog.Name);
                    }
                }

                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }
        }
    }
```

<span data-ttu-id="079bc-226">Teraz zawsze, gdy nasze działania aplikacji, najpierw będzie sprawdzał, jeśli baza danych jego celem jest element jest aktualny i Zastosuj wszelkie oczekujące migracji, jeśli nie jest.</span><span class="sxs-lookup"><span data-stu-id="079bc-226">Now whenever our application runs it will first check if the database it is targeting is up-to-date, and apply any pending migrations if it is not.</span></span>
