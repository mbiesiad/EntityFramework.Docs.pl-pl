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
# <a name="async-query-and-save"></a>Asynchronizowanie zapytania i zapisywania
> [!NOTE]
> **Ef6 Tylko —** funkcje, interfejsy API, itp omówione na tej stronie zostały wprowadzone w entity framework 6. Jeśli używasz wcześniejszej wersji, niektóre lub wszystkie informacje nie mają zastosowania.

EF6 wprowadzono obsługę kwerendy asynchronicznej i zapisać przy użyciu [asynchon i await słowa kluczowe,](https://msdn.microsoft.com/library/vstudio/hh191443.aspx) które zostały wprowadzone w .NET 4.5. Chociaż nie wszystkie aplikacje mogą korzystać z asynchrony, może służyć do poprawy responsywności klienta i skalowalności serwera podczas obsługi długotrwałych, sieci lub zadań związanych z we/wy.

## <a name="when-to-really-use-async"></a>Kiedy naprawdę używać asynchronii

Celem tego przewodnika jest wprowadzenie pojęć asynchronicznych w sposób, który ułatwia obserwowanie różnicy między wykonaniem programu asynchronicznego i synchronicznego. Ten instruktaż nie ma na celu zilustrowanie żadnego z kluczowych scenariuszy, w którym programowanie asynchowe zapewnia korzyści.

Programowanie asynchroniowe koncentruje się przede wszystkim na zwalnianiu bieżącego wątku zarządzanego (wątek z uruchomionym kodem .NET), aby wykonać inną pracę, podczas gdy czeka na operację, która nie wymaga czasu obliczeniowego z zarządzanego wątku. Na przykład podczas gdy aparat bazy danych przetwarza kwerendę nie ma nic do zrobienia przez kod .NET.

W aplikacjach klienckich (WinForms, WPF, itp.) bieżący wątek może służyć do utrzymania interfejsu użytkownika elastyczne podczas wykonywania operacji asynchronii. W aplikacjach serwerowych (ASP.NET itp.) wątek może służyć do przetwarzania innych żądań przychodzących — może to zmniejszyć użycie pamięci i/lub zwiększyć przepustowość serwera.

W większości aplikacji korzystających z asynchronii nie będzie żadnych zauważalnych korzyści, a nawet może być szkodliwe. Użyj testów, profilowania i zdrowego rozsądku, aby zmierzyć wpływ asynchronii w danym scenariuszu przed zatwierdzeniem go.

Oto kilka innych zasobów, aby dowiedzieć się więcej o asynchronii:

-   [Brandon Bray przegląd asynchronii / await w .NET 4.5](https://devblogs.microsoft.com/dotnet/async-in-4-5-worth-the-await/)
-   Strony [programowania asynchroniiowego](https://msdn.microsoft.com/library/hh191443.aspx) w bibliotece MSDN
-   [Jak budować ASP.NET aplikacji sieci Web za pomocą Asynchowy](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) (zawiera demo zwiększonej przepustowości serwera)

## <a name="create-the-model"></a>Tworzenie modelu

Będziemy używać przepływu [pracy Code First](~/ef6/modeling/code-first/workflows/new-database.md) do tworzenia naszego modelu i generowania bazy danych, jednak funkcje asynchroniczne będą działać ze wszystkimi modelami EF, w tym utworzonymi za pomocą projektanta EF.

-   Tworzenie aplikacji konsoli i nazywanie jej **AsyncDemo**
-   Dodawanie pakietu Enframework NuGet
    -   W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt **AsyncDemo**
    -   Wybierz **pozycję Zarządzaj pakietami NuGet...**
    -   W oknie dialogowym Zarządzanie pakietami NuGet wybierz kartę **Online** i wybierz pakiet **EntityFramework**
    -   Kliknij **pozycję Zainstaluj**
-   Dodawanie klasy **Model.cs** z następującą implementacją

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

 

## <a name="create-a-synchronous-program"></a>Tworzenie programu synchroniczalnego

Teraz, gdy mamy model EF, napiszmy kod, który używa go do wykonywania niektórych dostępu do danych.

-   Zastąp zawartość **Program.cs** następującym kodem

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

Ten kod wywołuje **PerformDatabaseOperations** metoda, która zapisuje nowy **blog** do bazy danych, a następnie pobiera wszystkie **blogi** z bazy danych i drukuje je do **konsoli**. Po tym, program pisze cytat dnia do **konsoli**.

Ponieważ kod jest synchroniczne, możemy obserwować następujący przepływ wykonywania po uruchomieniu programu:

1.  **SaveChanges** zaczyna wypychać nowy **blog** do bazy danych
2.  **SaveChanges** kończy
3.  Zapytanie dla wszystkich **blogów** jest wysyłane do bazy danych
4.  Zwraca kwerendy i wyniki są zapisywane w **konsoli**
5.  Cytat dnia jest napisany na **konsoli**

![Synchronizacja danych wyjściowych](~/ef6/media/syncoutput.png) 

 

## <a name="making-it-asynchronous"></a>Uczynienie go asynchronizem

Teraz, gdy mamy nasz program uruchomiony, możemy zacząć korzystać z nowego asynchronii i czekać na słowa kluczowe. W Program.cs

1.  Wiersz 2: Using instrukcji dla Obszaru nazw **System.Data.Entity** daje nam dostęp do metody rozszerzenia asynchronii EF.
2.  Wiersz 4: Instrukcja using dla obszaru nazw **System.Threading.Tasks** umożliwia nam użycie typu **zadania.**
3.  Wiersz 12 & 18: Przechwytujemy jako zadanie, które monitoruje postęp **PerformSomeDatabaseOperations** (wiersz 12), a następnie blokuje wykonanie programu dla tego zadania, aby zakończyć po zakończeniu całej pracy dla programu (wiersz 18).
4.  Wiersz 25: Zaktualizowaliśmy **PerformSomeDatabaseOperations,** aby **oznaczyć jako asynchronicznego** i zwrócić **zadanie**.
5.  Linia 35: Teraz dzwonimy do wersji Asynna SaveChanges i oczekujemy na jej zakończenie.
6.  Linia 42: Teraz dzwonimy do wersji Asynna listy ToList i czekamy na wynik.

Aby uzyskać pełną listę dostępnych metod rozszerzenia w obszarze nazw System.Data.Entity, zobacz QueryableExtensions klasy. *Należy również dodać "using System.Data.Entity" do instrukcji using.*

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

Teraz, gdy kod jest asynchroniczne, możemy zaobserwować inny przepływ wykonywania po uruchomieniu programu:

1. **SaveChanges** zaczyna wypychać nowy **blog** do bazy danych  
    *Po wysłaniu polecenia do bazy danych nie więcej czasu obliczeniowego jest potrzebne w bieżącym wątku zarządzanym. **PerformDatabaseOperations** Metoda zwraca (nawet jeśli nie zakończył wykonywania) i przepływ programu w Main metoda jest kontynuowana.*
2. **Cytat dnia jest napisany na konsoli**  
    *Ponieważ nie ma więcej pracy do wykonania w Main metody, zarządzany wątek jest zablokowany na Wait wywołanie, dopóki operacja bazy danych zostanie zakończona. Po jego zakończeniu, pozostała część naszego **PerformDatabaseOperations** zostaną wykonane.*
3.  **SaveChanges** kończy  
4.  Zapytanie dla wszystkich **blogów** jest wysyłane do bazy danych  
    *Ponownie zarządzany wątek jest wolny do wykonywania innych prac, podczas gdy kwerenda jest przetwarzana w bazie danych. Ponieważ wszystkie inne wykonanie zostało zakończone, wątek po prostu zatrzyma się na Wait wywołanie choć.*
5.  Zwraca kwerendy i wyniki są zapisywane w **konsoli**  

![Wyjście asynchroniowe](~/ef6/media/asyncoutput.png) 

 

## <a name="the-takeaway"></a>Na wynos

Teraz zobaczyliśmy, jak łatwo jest korzystać z metod asynchronicznych EF. Mimo że zalety asynchronii może nie być bardzo widoczne w przypadku prostej aplikacji konsoli, te same strategie mogą być stosowane w sytuacjach, gdy długotrwałe lub związane z siecią działania mogą w przeciwnym razie blokować aplikację lub powodować dużą liczbę wątków, aby zwiększyć zużycie pamięci.
