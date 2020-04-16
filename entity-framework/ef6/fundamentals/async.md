---
title: Asynchronicznej kwerendy i zapisz - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: d56e6f1d-4bd1-4b50-9558-9a30e04a8ec3
ms.openlocfilehash: a001df726b7cd3342fcca566c1373f452811d07d
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434243"
---
# <a name="async-query-and-save"></a><span data-ttu-id="104cf-102">Asynchronizowanie zapytania i zapisywania</span><span class="sxs-lookup"><span data-stu-id="104cf-102">Async query and save</span></span>
> [!NOTE]
> <span data-ttu-id="104cf-103">**Ef6 Tylko —** funkcje, interfejsy API, itp omówione na tej stronie zostały wprowadzone w entity framework 6.</span><span class="sxs-lookup"><span data-stu-id="104cf-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="104cf-104">Jeśli używasz wcześniejszej wersji, niektóre lub wszystkie informacje nie mają zastosowania.</span><span class="sxs-lookup"><span data-stu-id="104cf-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="104cf-105">EF6 wprowadzono obsługę kwerendy asynchronicznej i zapisać przy użyciu [asynchon i await słowa kluczowe,](https://msdn.microsoft.com/library/vstudio/hh191443.aspx) które zostały wprowadzone w .NET 4.5.</span><span class="sxs-lookup"><span data-stu-id="104cf-105">EF6 introduced support for asynchronous query and save using the [async and await keywords](https://msdn.microsoft.com/library/vstudio/hh191443.aspx) that were introduced in .NET 4.5.</span></span> <span data-ttu-id="104cf-106">Chociaż nie wszystkie aplikacje mogą korzystać z asynchrony, może służyć do poprawy responsywności klienta i skalowalności serwera podczas obsługi długotrwałych, sieci lub zadań związanych z we/wy.</span><span class="sxs-lookup"><span data-stu-id="104cf-106">While not all applications may benefit from asynchrony, it can be used to improve client responsiveness and server scalability when handling long-running, network or I/O-bound tasks.</span></span>

## <a name="when-to-really-use-async"></a><span data-ttu-id="104cf-107">Kiedy naprawdę używać asynchronii</span><span class="sxs-lookup"><span data-stu-id="104cf-107">When to really use async</span></span>

<span data-ttu-id="104cf-108">Celem tego przewodnika jest wprowadzenie pojęć asynchronicznych w sposób, który ułatwia obserwowanie różnicy między wykonaniem programu asynchronicznego i synchronicznego.</span><span class="sxs-lookup"><span data-stu-id="104cf-108">The purpose of this walkthrough is to introduce the async concepts in a way that makes it easy to observe the difference between asynchronous and synchronous program execution.</span></span> <span data-ttu-id="104cf-109">Ten instruktaż nie ma na celu zilustrowanie żadnego z kluczowych scenariuszy, w którym programowanie asynchowe zapewnia korzyści.</span><span class="sxs-lookup"><span data-stu-id="104cf-109">This walkthrough is not intended to illustrate any of the key scenarios where async programming provides benefits.</span></span>

<span data-ttu-id="104cf-110">Programowanie asynchroniowe koncentruje się przede wszystkim na zwalnianiu bieżącego wątku zarządzanego (wątek z uruchomionym kodem .NET), aby wykonać inną pracę, podczas gdy czeka na operację, która nie wymaga czasu obliczeniowego z zarządzanego wątku.</span><span class="sxs-lookup"><span data-stu-id="104cf-110">Async programming is primarily focused on freeing up the current managed thread (thread running .NET code) to do other work while it waits for an operation that does not require any compute time from a managed thread.</span></span> <span data-ttu-id="104cf-111">Na przykład podczas gdy aparat bazy danych przetwarza kwerendę nie ma nic do zrobienia przez kod .NET.</span><span class="sxs-lookup"><span data-stu-id="104cf-111">For example, whilst the database engine is processing a query there is nothing to be done by .NET code.</span></span>

<span data-ttu-id="104cf-112">W aplikacjach klienckich (WinForms, WPF, itp.) bieżący wątek może służyć do utrzymania interfejsu użytkownika elastyczne podczas wykonywania operacji asynchronii.</span><span class="sxs-lookup"><span data-stu-id="104cf-112">In client applications (WinForms, WPF, etc.) the current thread can be used to keep the UI responsive while the async operation is performed.</span></span> <span data-ttu-id="104cf-113">W aplikacjach serwerowych (ASP.NET itp.) wątek może służyć do przetwarzania innych żądań przychodzących — może to zmniejszyć użycie pamięci i/lub zwiększyć przepustowość serwera.</span><span class="sxs-lookup"><span data-stu-id="104cf-113">In server applications (ASP.NET etc.) the thread can be used to process other incoming requests - this can reduce memory usage and/or increase throughput of the server.</span></span>

<span data-ttu-id="104cf-114">W większości aplikacji korzystających z asynchronii nie będzie żadnych zauważalnych korzyści, a nawet może być szkodliwe.</span><span class="sxs-lookup"><span data-stu-id="104cf-114">In most applications using async will have no noticeable benefits and even could be detrimental.</span></span> <span data-ttu-id="104cf-115">Użyj testów, profilowania i zdrowego rozsądku, aby zmierzyć wpływ asynchronii w danym scenariuszu przed zatwierdzeniem go.</span><span class="sxs-lookup"><span data-stu-id="104cf-115">Use tests, profiling and common sense to measure the impact of async in your particular scenario before committing to it.</span></span>

<span data-ttu-id="104cf-116">Oto kilka innych zasobów, aby dowiedzieć się więcej o asynchronii:</span><span class="sxs-lookup"><span data-stu-id="104cf-116">Here are some more resources to learn about async:</span></span>

-   [<span data-ttu-id="104cf-117">Brandon Bray przegląd asynchronii / await w .NET 4.5</span><span class="sxs-lookup"><span data-stu-id="104cf-117">Brandon Bray’s overview of async/await in .NET 4.5</span></span>](https://devblogs.microsoft.com/dotnet/async-in-4-5-worth-the-await/)
-   <span data-ttu-id="104cf-118">Strony [programowania asynchroniiowego](https://msdn.microsoft.com/library/hh191443.aspx) w bibliotece MSDN</span><span class="sxs-lookup"><span data-stu-id="104cf-118">[Asynchronous Programming](https://msdn.microsoft.com/library/hh191443.aspx) pages in the MSDN Library</span></span>
-   <span data-ttu-id="104cf-119">[Jak budować ASP.NET aplikacji sieci Web za pomocą Asynchowy](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) (zawiera demo zwiększonej przepustowości serwera)</span><span class="sxs-lookup"><span data-stu-id="104cf-119">[How to Build ASP.NET Web Applications Using Async](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) (includes a demo of increased server throughput)</span></span>

## <a name="create-the-model"></a><span data-ttu-id="104cf-120">Tworzenie modelu</span><span class="sxs-lookup"><span data-stu-id="104cf-120">Create the model</span></span>

<span data-ttu-id="104cf-121">Będziemy używać przepływu [pracy Code First](~/ef6/modeling/code-first/workflows/new-database.md) do tworzenia naszego modelu i generowania bazy danych, jednak funkcje asynchroniczne będą działać ze wszystkimi modelami EF, w tym utworzonymi za pomocą projektanta EF.</span><span class="sxs-lookup"><span data-stu-id="104cf-121">We’ll be using the [Code First workflow](~/ef6/modeling/code-first/workflows/new-database.md) to create our model and generate the database, however the asynchronous functionality will work with all EF models including those created with the EF Designer.</span></span>

-   <span data-ttu-id="104cf-122">Tworzenie aplikacji konsoli i nazywanie jej **AsyncDemo**</span><span class="sxs-lookup"><span data-stu-id="104cf-122">Create a Console Application and call it **AsyncDemo**</span></span>
-   <span data-ttu-id="104cf-123">Dodawanie pakietu Enframework NuGet</span><span class="sxs-lookup"><span data-stu-id="104cf-123">Add the EntityFramework NuGet package</span></span>
    -   <span data-ttu-id="104cf-124">W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt **AsyncDemo**</span><span class="sxs-lookup"><span data-stu-id="104cf-124">In Solution Explorer, right-click on the **AsyncDemo** project</span></span>
    -   <span data-ttu-id="104cf-125">Wybierz **pozycję Zarządzaj pakietami NuGet...**</span><span class="sxs-lookup"><span data-stu-id="104cf-125">Select **Manage NuGet Packages…**</span></span>
    -   <span data-ttu-id="104cf-126">W oknie dialogowym Zarządzanie pakietami NuGet wybierz kartę **Online** i wybierz pakiet **EntityFramework**</span><span class="sxs-lookup"><span data-stu-id="104cf-126">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package</span></span>
    -   <span data-ttu-id="104cf-127">Kliknij **pozycję Zainstaluj**</span><span class="sxs-lookup"><span data-stu-id="104cf-127">Click **Install**</span></span>
-   <span data-ttu-id="104cf-128">Dodawanie klasy **Model.cs** z następującą implementacją</span><span class="sxs-lookup"><span data-stu-id="104cf-128">Add a **Model.cs** class with the following implementation</span></span>

``` csharp
    using System.Collections.Generic;
    using System.Data.Entity;

    namespace AsyncDemo
    {
        public class BloggingContext : DbContext
        {
            public DbSet<Blog> Blogs { get; set; }
            public DbSet<Post> Posts { get; set; }
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
    }
```

 

## <a name="create-a-synchronous-program"></a><span data-ttu-id="104cf-129">Tworzenie programu synchroniczalnego</span><span class="sxs-lookup"><span data-stu-id="104cf-129">Create a synchronous program</span></span>

<span data-ttu-id="104cf-130">Teraz, gdy mamy model EF, napiszmy kod, który używa go do wykonywania niektórych dostępu do danych.</span><span class="sxs-lookup"><span data-stu-id="104cf-130">Now that we have an EF model, let's write some code that uses it to perform some data access.</span></span>

-   <span data-ttu-id="104cf-131">Zastąp zawartość **Program.cs** następującym kodem</span><span class="sxs-lookup"><span data-stu-id="104cf-131">Replace the contents of **Program.cs** with the following code</span></span>

``` csharp
    using System;
    using System.Linq;

    namespace AsyncDemo
    {
        class Program
        {
            static void Main(string[] args)
            {
                PerformDatabaseOperations();

                Console.WriteLine("Quote of the day");
                Console.WriteLine(" Don't worry about the world coming to an end today... ");
                Console.WriteLine(" It's already tomorrow in Australia.");

                Console.WriteLine();
                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }

            public static void PerformDatabaseOperations()
            {
                using (var db = new BloggingContext())
                {
                    // Create a new blog and save it
                    db.Blogs.Add(new Blog
                    {
                        Name = "Test Blog #" + (db.Blogs.Count() + 1)
                    });
                    Console.WriteLine("Calling SaveChanges.");
                    db.SaveChanges();
                    Console.WriteLine("SaveChanges completed.");

                    // Query for all blogs ordered by name
                    Console.WriteLine("Executing query.");
                    var blogs = (from b in db.Blogs
                                orderby b.Name
                                select b).ToList();

                    // Write all blogs out to Console
                    Console.WriteLine("Query completed with following results:");
                    foreach (var blog in blogs)
                    {
                        Console.WriteLine(" " + blog.Name);
                    }
                }
            }
        }
    }
```

<span data-ttu-id="104cf-132">Ten kod wywołuje **PerformDatabaseOperations** metoda, która zapisuje nowy **blog** do bazy danych, a następnie pobiera wszystkie **blogi** z bazy danych i drukuje je do **konsoli**.</span><span class="sxs-lookup"><span data-stu-id="104cf-132">This code calls the **PerformDatabaseOperations** method which saves a new **Blog** to the database and then retrieves all **Blogs** from the database and prints them to the **Console**.</span></span> <span data-ttu-id="104cf-133">Po tym, program pisze cytat dnia do **konsoli**.</span><span class="sxs-lookup"><span data-stu-id="104cf-133">After this, the program writes a quote of the day to the **Console**.</span></span>

<span data-ttu-id="104cf-134">Ponieważ kod jest synchroniczne, możemy obserwować następujący przepływ wykonywania po uruchomieniu programu:</span><span class="sxs-lookup"><span data-stu-id="104cf-134">Since the code is synchronous, we can observe the following execution flow when we run the program:</span></span>

1.  <span data-ttu-id="104cf-135">**SaveChanges** zaczyna wypychać nowy **blog** do bazy danych</span><span class="sxs-lookup"><span data-stu-id="104cf-135">**SaveChanges** begins to push the new **Blog** to the database</span></span>
2.  <span data-ttu-id="104cf-136">**SaveChanges** kończy</span><span class="sxs-lookup"><span data-stu-id="104cf-136">**SaveChanges** completes</span></span>
3.  <span data-ttu-id="104cf-137">Zapytanie dla wszystkich **blogów** jest wysyłane do bazy danych</span><span class="sxs-lookup"><span data-stu-id="104cf-137">Query for all **Blogs** is sent to the database</span></span>
4.  <span data-ttu-id="104cf-138">Zwraca kwerendy i wyniki są zapisywane w **konsoli**</span><span class="sxs-lookup"><span data-stu-id="104cf-138">Query returns and results are written to **Console**</span></span>
5.  <span data-ttu-id="104cf-139">Cytat dnia jest napisany na **konsoli**</span><span class="sxs-lookup"><span data-stu-id="104cf-139">Quote of the day is written to **Console**</span></span>

![Synchronizacja danych wyjściowych](~/ef6/media/syncoutput.png) 

 

## <a name="making-it-asynchronous"></a><span data-ttu-id="104cf-141">Uczynienie go asynchronizem</span><span class="sxs-lookup"><span data-stu-id="104cf-141">Making it asynchronous</span></span>

<span data-ttu-id="104cf-142">Teraz, gdy mamy nasz program uruchomiony, możemy zacząć korzystać z nowego asynchronii i czekać na słowa kluczowe.</span><span class="sxs-lookup"><span data-stu-id="104cf-142">Now that we have our program up and running, we can begin making use of the new async and await keywords.</span></span> <span data-ttu-id="104cf-143">W Program.cs</span><span class="sxs-lookup"><span data-stu-id="104cf-143">We've made the following changes to Program.cs</span></span>

1.  <span data-ttu-id="104cf-144">Wiersz 2: Using instrukcji dla Obszaru nazw **System.Data.Entity** daje nam dostęp do metody rozszerzenia asynchronii EF.</span><span class="sxs-lookup"><span data-stu-id="104cf-144">Line 2: The using statement for the **System.Data.Entity** namespace gives us access to the EF async extension methods.</span></span>
2.  <span data-ttu-id="104cf-145">Wiersz 4: Instrukcja using dla obszaru nazw **System.Threading.Tasks** umożliwia nam użycie typu **zadania.**</span><span class="sxs-lookup"><span data-stu-id="104cf-145">Line 4: The using statement for the **System.Threading.Tasks** namespace allows us to use the **Task** type.</span></span>
3.  <span data-ttu-id="104cf-146">Wiersz 12 & 18: Przechwytujemy jako zadanie, które monitoruje postęp **PerformSomeDatabaseOperations** (wiersz 12), a następnie blokuje wykonanie programu dla tego zadania, aby zakończyć po zakończeniu całej pracy dla programu (wiersz 18).</span><span class="sxs-lookup"><span data-stu-id="104cf-146">Line 12 & 18: We are capturing as task that monitors the progress of **PerformSomeDatabaseOperations** (line 12) and then blocking program execution for this task to complete once all the work for the program is done (line 18).</span></span>
4.  <span data-ttu-id="104cf-147">Wiersz 25: Zaktualizowaliśmy **PerformSomeDatabaseOperations,** aby **oznaczyć jako asynchronicznego** i zwrócić **zadanie**.</span><span class="sxs-lookup"><span data-stu-id="104cf-147">Line 25: We've update **PerformSomeDatabaseOperations** to be marked as **async** and return a **Task**.</span></span>
5.  <span data-ttu-id="104cf-148">Linia 35: Teraz dzwonimy do wersji Asynna SaveChanges i oczekujemy na jej zakończenie.</span><span class="sxs-lookup"><span data-stu-id="104cf-148">Line 35: We're now calling the Async version of SaveChanges and awaiting it's completion.</span></span>
6.  <span data-ttu-id="104cf-149">Linia 42: Teraz dzwonimy do wersji Asynna listy ToList i czekamy na wynik.</span><span class="sxs-lookup"><span data-stu-id="104cf-149">Line 42: We're now calling the Async version of ToList and awaiting on the result.</span></span>

<span data-ttu-id="104cf-150">Aby uzyskać pełną listę dostępnych metod rozszerzenia w obszarze nazw System.Data.Entity, zobacz QueryableExtensions klasy.</span><span class="sxs-lookup"><span data-stu-id="104cf-150">For a comprehensive list of available extension methods in the System.Data.Entity namespace, refer to the QueryableExtensions class.</span></span> <span data-ttu-id="104cf-151">*Należy również dodać "using System.Data.Entity" do instrukcji using.*</span><span class="sxs-lookup"><span data-stu-id="104cf-151">*You’ll also need to add “using System.Data.Entity” to your using statements.*</span></span>

``` csharp
    using System;
    using System.Data.Entity;
    using System.Linq;
    using System.Threading.Tasks;

    namespace AsyncDemo
    {
        class Program
        {
            static void Main(string[] args)
            {
                var task = PerformDatabaseOperations();

                Console.WriteLine("Quote of the day");
                Console.WriteLine(" Don't worry about the world coming to an end today... ");
                Console.WriteLine(" It's already tomorrow in Australia.");

                task.Wait();

                Console.WriteLine();
                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }

            public static async Task PerformDatabaseOperations()
            {
                using (var db = new BloggingContext())
                {
                    // Create a new blog and save it
                    db.Blogs.Add(new Blog
                    {
                        Name = "Test Blog #" + (db.Blogs.Count() + 1)
                    });
                    Console.WriteLine("Calling SaveChanges.");
                    await db.SaveChangesAsync();
                    Console.WriteLine("SaveChanges completed.");

                    // Query for all blogs ordered by name
                    Console.WriteLine("Executing query.");
                    var blogs = await (from b in db.Blogs
                                orderby b.Name
                                select b).ToListAsync();

                    // Write all blogs out to Console
                    Console.WriteLine("Query completed with following results:");
                    foreach (var blog in blogs)
                    {
                        Console.WriteLine(" - " + blog.Name);
                    }
                }
            }
        }
    }
```

<span data-ttu-id="104cf-152">Teraz, gdy kod jest asynchroniczne, możemy zaobserwować inny przepływ wykonywania po uruchomieniu programu:</span><span class="sxs-lookup"><span data-stu-id="104cf-152">Now that the code is asynchronous, we can observe a different execution flow when we run the program:</span></span>

1. <span data-ttu-id="104cf-153">**SaveChanges** zaczyna wypychać nowy **blog** do bazy danych</span><span class="sxs-lookup"><span data-stu-id="104cf-153">**SaveChanges** begins to push the new **Blog** to the database</span></span>  
    <span data-ttu-id="104cf-154">*Po wysłaniu polecenia do bazy danych nie więcej czasu obliczeniowego jest potrzebne w bieżącym wątku zarządzanym. **PerformDatabaseOperations** Metoda zwraca (nawet jeśli nie zakończył wykonywania) i przepływ programu w Main metoda jest kontynuowana.*</span><span class="sxs-lookup"><span data-stu-id="104cf-154">*Once the command is sent to the database no more compute time is needed on the current managed thread. The **PerformDatabaseOperations** method returns (even though it hasn't finished executing) and program flow in the Main method continues.*</span></span>
2. <span data-ttu-id="104cf-155">**Cytat dnia jest napisany na konsoli**</span><span class="sxs-lookup"><span data-stu-id="104cf-155">**Quote of the day is written to Console**</span></span>  
    <span data-ttu-id="104cf-156">*Ponieważ nie ma więcej pracy do wykonania w Main metody, zarządzany wątek jest zablokowany na Wait wywołanie, dopóki operacja bazy danych zostanie zakończona. Po jego zakończeniu, pozostała część naszego **PerformDatabaseOperations** zostaną wykonane.*</span><span class="sxs-lookup"><span data-stu-id="104cf-156">*Since there is no more work to do in the Main method, the managed thread is blocked on the Wait call until the database operation completes. Once it completes, the remainder of our **PerformDatabaseOperations** will be executed.*</span></span>
3.  <span data-ttu-id="104cf-157">**SaveChanges** kończy</span><span class="sxs-lookup"><span data-stu-id="104cf-157">**SaveChanges** completes</span></span>  
4.  <span data-ttu-id="104cf-158">Zapytanie dla wszystkich **blogów** jest wysyłane do bazy danych</span><span class="sxs-lookup"><span data-stu-id="104cf-158">Query for all **Blogs** is sent to the database</span></span>  
    <span data-ttu-id="104cf-159">*Ponownie zarządzany wątek jest wolny do wykonywania innych prac, podczas gdy kwerenda jest przetwarzana w bazie danych. Ponieważ wszystkie inne wykonanie zostało zakończone, wątek po prostu zatrzyma się na Wait wywołanie choć.*</span><span class="sxs-lookup"><span data-stu-id="104cf-159">*Again, the managed thread is free to do other work while the query is processed in the database. Since all other execution has completed, the thread will just halt on the Wait call though.*</span></span>
5.  <span data-ttu-id="104cf-160">Zwraca kwerendy i wyniki są zapisywane w **konsoli**</span><span class="sxs-lookup"><span data-stu-id="104cf-160">Query returns and results are written to **Console**</span></span>  

![Wyjście asynchroniowe](~/ef6/media/asyncoutput.png) 

 

## <a name="the-takeaway"></a><span data-ttu-id="104cf-162">Na wynos</span><span class="sxs-lookup"><span data-stu-id="104cf-162">The takeaway</span></span>

<span data-ttu-id="104cf-163">Teraz zobaczyliśmy, jak łatwo jest korzystać z metod asynchronicznych EF.</span><span class="sxs-lookup"><span data-stu-id="104cf-163">We now saw how easy it is to make use of EF’s asynchronous methods.</span></span> <span data-ttu-id="104cf-164">Mimo że zalety asynchronii może nie być bardzo widoczne w przypadku prostej aplikacji konsoli, te same strategie mogą być stosowane w sytuacjach, gdy długotrwałe lub związane z siecią działania mogą w przeciwnym razie blokować aplikację lub powodować dużą liczbę wątków, aby zwiększyć zużycie pamięci.</span><span class="sxs-lookup"><span data-stu-id="104cf-164">Although the advantages of async may not be very apparent with a simple console app, these same strategies can be applied in situations where long-running or network-bound activities might otherwise block the application, or cause a large number of threads to increase the memory footprint.</span></span>
