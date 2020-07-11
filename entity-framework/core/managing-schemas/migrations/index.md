---
title: Przegląd migracji — EF Core
author: bricelam
ms.author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/index
ms.openlocfilehash: 8539a8da6f0051d3737efc583f0adfaf05fb2d3d
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238232"
---
# <a name="migrations-overview"></a><span data-ttu-id="fd3a7-102">Przegląd migracji</span><span class="sxs-lookup"><span data-stu-id="fd3a7-102">Migrations Overview</span></span>

<span data-ttu-id="fd3a7-103">W realnych projektach modele danych zmieniają się wraz z zaimplementowanymi funkcjami: nowe jednostki lub właściwości są dodawane i usuwane, a schematy bazy danych muszą być odpowiednio zmieniane, aby były utrzymywane w synchronizacji z aplikacją.</span><span class="sxs-lookup"><span data-stu-id="fd3a7-103">In real world projects, data models change as features get implemented: new entities or properties are added and removed, and database schemas needs to be changed accordingly to be kept in sync with the application.</span></span> <span data-ttu-id="fd3a7-104">Funkcja migracji w EF Core zapewnia sposób stopniowego aktualizowania schematu bazy danych, aby zachować synchronizację z modelem danych aplikacji przy zachowaniu istniejących danych w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="fd3a7-104">The migrations feature in EF Core provides a way to incrementally update the database schema to keep it in sync with the application's data model while preserving existing data in the database.</span></span>

<span data-ttu-id="fd3a7-105">Na wysokim poziomie migracja działa w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="fd3a7-105">At a high level, migrations function in the following way:</span></span>

* <span data-ttu-id="fd3a7-106">Gdy zostanie wprowadzona zmiana modelu danych, programista korzysta z EF Core narzędzi, aby dodać odpowiednią migrację opisującą aktualizacje niezbędne do synchronizowania schematu bazy danych. EF Core porównuje bieżący model z migawką starego modelu w celu określenia różnic i generuje pliki źródłowe migracji; pliki mogą być śledzone w kontroli źródła projektu, podobnie jak każdy inny plik źródłowy.</span><span class="sxs-lookup"><span data-stu-id="fd3a7-106">When a data model change is introduced, the developer uses EF Core tools to add a corresponding migration describing the updates necessary to keep the database schema in sync. EF Core compares the current model against a snapshot of the old model to determine the differences, and generates migration source files; the files can be tracked in your project's source control like any other source file.</span></span>
* <span data-ttu-id="fd3a7-107">Po wygenerowaniu nowej migracji można ją zastosować do bazy danych na różne sposoby.</span><span class="sxs-lookup"><span data-stu-id="fd3a7-107">Once a new migration has been generated, it can be applied to a database in various ways.</span></span> <span data-ttu-id="fd3a7-108">EF Core rejestruje wszystkie zastosowane migracje w specjalnej tabeli historii, dzięki czemu może ona wiedzieć, które migracje zostały zastosowane, a które nie.</span><span class="sxs-lookup"><span data-stu-id="fd3a7-108">EF Core records all applied migrations in a special history table, allowing it to know which migrations have been applied and which haven't.</span></span>

<span data-ttu-id="fd3a7-109">Pozostała część tej strony to przewodnik początkującego krok po kroku dotyczący korzystania z migracji.</span><span class="sxs-lookup"><span data-stu-id="fd3a7-109">The rest of this page is a step-by-step beginner's guide for using migrations.</span></span> <span data-ttu-id="fd3a7-110">Aby uzyskać szczegółowe informacje, zapoznaj się z innymi stronami w tej sekcji.</span><span class="sxs-lookup"><span data-stu-id="fd3a7-110">Consult the other pages in this section for more in-depth information.</span></span>

## <a name="getting-started"></a><span data-ttu-id="fd3a7-111">Wprowadzenie</span><span class="sxs-lookup"><span data-stu-id="fd3a7-111">Getting started</span></span>

<span data-ttu-id="fd3a7-112">Załóżmy, że po prostu zakończysz pierwszą aplikację EF Core, która zawiera następujący prosty model:</span><span class="sxs-lookup"><span data-stu-id="fd3a7-112">Let's assume you've just completed your first EF Core application, which contains the following simple model:</span></span>

```c#
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
}
```

<span data-ttu-id="fd3a7-113">Podczas opracowywania możesz użyć [interfejsów API tworzenia i upuszczania](xref:core/managing-schemas/ensure-created) , aby szybko wykonywać iteracje, zmieniając model w miarę potrzeb; Jednak teraz, gdy aplikacja przechodzi do środowiska produkcyjnego, musisz bezpiecznie rozwijać schemat bez porzucania całej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="fd3a7-113">During development, you may have used the [Create and Drop APIs](xref:core/managing-schemas/ensure-created) to iterate quickly, changing your model as needed; but now that your application is going to production, you need a way to safely evolve the schema without dropping the entire database.</span></span>

### <a name="install-the-tools"></a><span data-ttu-id="fd3a7-114">Instalowanie narzędzi</span><span class="sxs-lookup"><span data-stu-id="fd3a7-114">Install the tools</span></span>

<span data-ttu-id="fd3a7-115">Najpierw należy zainstalować [EF Core narzędzia wiersza polecenia](xref:core/miscellaneous/cli/index):</span><span class="sxs-lookup"><span data-stu-id="fd3a7-115">First, you'll have to install the [EF Core command-line tools](xref:core/miscellaneous/cli/index):</span></span>

* <span data-ttu-id="fd3a7-116">Ogólnie zalecamy korzystanie z [interfejs wiersza polecenia platformy .NET Core narzędzi](xref:core/miscellaneous/cli/dotnet), które działają na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="fd3a7-116">We generally recommend using the [.NET Core CLI tools](xref:core/miscellaneous/cli/dotnet), which work on all platforms.</span></span>
* <span data-ttu-id="fd3a7-117">Jeśli nie masz doświadczenia w pracy w programie Visual Studio lub masz doświadczenie w korzystaniu z migracji EF6, możesz również użyć [narzędzi konsoli Menedżera pakietów](xref:core/miscellaneous/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="fd3a7-117">If you're more comfortable working inside Visual Studio or have experience with EF6 migrations, you can also use the [Package Manager Console tools](xref:core/miscellaneous/cli/powershell).</span></span>

### <a name="create-your-first-migration"></a><span data-ttu-id="fd3a7-118">Tworzenie pierwszej migracji</span><span class="sxs-lookup"><span data-stu-id="fd3a7-118">Create your first migration</span></span>

<span data-ttu-id="fd3a7-119">Teraz można przystąpić do dodawania pierwszej migracji.</span><span class="sxs-lookup"><span data-stu-id="fd3a7-119">You're now ready to add your first migration!</span></span> <span data-ttu-id="fd3a7-120">Poinstruuj EF Core, aby utworzyć migrację o nazwie **InitialCreate**:</span><span class="sxs-lookup"><span data-stu-id="fd3a7-120">Instruct EF Core to create a migration named **InitialCreate**:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="fd3a7-121">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="fd3a7-121">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate
```

#### <a name="visual-studio"></a>[<span data-ttu-id="fd3a7-122">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fd3a7-122">Visual Studio</span></span>](#tab/vs)

``` powershell
Add-Migration InitialCreate
```

***

<span data-ttu-id="fd3a7-123">EF Core utworzy katalog o nazwie **migrations** w projekcie i wygeneruje niektóre pliki.</span><span class="sxs-lookup"><span data-stu-id="fd3a7-123">EF Core will create a directory called **Migrations** in your project, and generate some files.</span></span> <span data-ttu-id="fd3a7-124">Dobrym pomysłem jest zbadanie dokładnie wygenerowanego EF Core i jego zmianę, ale powrócimy teraz.</span><span class="sxs-lookup"><span data-stu-id="fd3a7-124">It's a good idea to inspect what exactly EF Core generated - and possibly amend it - but we'll skip over that for now.</span></span>

### <a name="create-your-database-and-schema"></a><span data-ttu-id="fd3a7-125">Tworzenie bazy danych i schematu</span><span class="sxs-lookup"><span data-stu-id="fd3a7-125">Create your database and schema</span></span>

<span data-ttu-id="fd3a7-126">W tym momencie można utworzyć bazę danych programu EF i utworzyć schemat z migracji.</span><span class="sxs-lookup"><span data-stu-id="fd3a7-126">At this point you can have EF create your database and create your schema from the migration.</span></span> <span data-ttu-id="fd3a7-127">Można to zrobić, wykonując następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="fd3a7-127">This can be done via the following:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="fd3a7-128">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="fd3a7-128">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```
#### <a name="visual-studio"></a>[<span data-ttu-id="fd3a7-129">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fd3a7-129">Visual Studio</span></span>](#tab/vs)

``` powershell
Update-Database
```

***

<span data-ttu-id="fd3a7-130">To wszystko — aplikacja jest gotowa do uruchamiania w nowej bazie danych i nie trzeba pisać pojedynczego wiersza SQL.</span><span class="sxs-lookup"><span data-stu-id="fd3a7-130">That's all there is to it - your application is ready to run on your new database, and you didn't need to write a single line of SQL.</span></span> <span data-ttu-id="fd3a7-131">Należy pamiętać, że ten sposób stosowania migracji jest idealnym rozwiązaniem do lokalnego rozwoju, ale jest mniej odpowiedni dla środowisk produkcyjnych — Aby uzyskać więcej informacji, zobacz [stronę stosowanie migracji](xref:core/managing-schemas/migrations/applying) .</span><span class="sxs-lookup"><span data-stu-id="fd3a7-131">Note that this way of applying migrations is ideal for local development, but is less suitable for production environments - see the [Applying Migrations page](xref:core/managing-schemas/migrations/applying) for more info.</span></span>

### <a name="evolving-your-model"></a><span data-ttu-id="fd3a7-132">Rozwój modelu</span><span class="sxs-lookup"><span data-stu-id="fd3a7-132">Evolving your model</span></span>

<span data-ttu-id="fd3a7-133">Przeszedł kilka dni i zostanie wyświetlony monit o dodanie sygnatury czasowej tworzenia do blogów.</span><span class="sxs-lookup"><span data-stu-id="fd3a7-133">A few days have passed, and you're asked to add a creation timestamp to your blogs.</span></span> <span data-ttu-id="fd3a7-134">Wprowadzono niezbędne zmiany w aplikacji, a model wygląda teraz następująco:</span><span class="sxs-lookup"><span data-stu-id="fd3a7-134">You've done the necessary changes to your application, and your model now looks like this:</span></span>

```c#
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
    public DateTime CreatedTimestamp { get; set; }
}
```

<span data-ttu-id="fd3a7-135">Model i produkcyjna baza danych nie są teraz zsynchronizowane — należy dodać nową kolumnę do schematu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="fd3a7-135">Your model and your production database are now out of sync - we must add a new column to your database schema.</span></span> <span data-ttu-id="fd3a7-136">Utwórzmy nową migrację:</span><span class="sxs-lookup"><span data-stu-id="fd3a7-136">Let's create a new migration for this:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="fd3a7-137">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="fd3a7-137">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddBlogCreatedTimestamp
```

#### <a name="visual-studio"></a>[<span data-ttu-id="fd3a7-138">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fd3a7-138">Visual Studio</span></span>](#tab/vs)

``` powershell
Add-Migration AddBlogCreatedTimestamp
```

***

<span data-ttu-id="fd3a7-139">Należy pamiętać, że firma Microsoft przekazuje nazwę opisową, aby ułatwić zrozumienie historii projektu później.</span><span class="sxs-lookup"><span data-stu-id="fd3a7-139">Note that we give migrations a descriptive name, to make it easier to understand the project history later.</span></span>

<span data-ttu-id="fd3a7-140">Ponieważ nie jest to pierwsza migracja projektu, EF Core teraz porównuje zaktualizowany model względem migawki starego modelu, zanim kolumna zostanie dodana; migawka modelu jest jednym z plików generowanych przez EF Core podczas dodawania migracji i jest sprawdzany w kontroli źródła.</span><span class="sxs-lookup"><span data-stu-id="fd3a7-140">Since this isn't the project's first migration, EF Core now compares your updated model against a snapshot of the old model, before the column was added; the model snapshot is one of the files generated by EF Core when you add a migration, and is checked into source control.</span></span> <span data-ttu-id="fd3a7-141">Na podstawie tego porównania EF Core wykrywa, że dodano kolumnę i dodaje odpowiednią migrację.</span><span class="sxs-lookup"><span data-stu-id="fd3a7-141">Based on that comparison, EF Core detects that a column has been added, and adds the appropriate migration.</span></span>

<span data-ttu-id="fd3a7-142">Teraz możesz zastosować migrację tak jak wcześniej:</span><span class="sxs-lookup"><span data-stu-id="fd3a7-142">You can now apply your migration as before:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="fd3a7-143">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="fd3a7-143">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```
#### <a name="visual-studio"></a>[<span data-ttu-id="fd3a7-144">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fd3a7-144">Visual Studio</span></span>](#tab/vs)

``` powershell
Update-Database
```

***

<span data-ttu-id="fd3a7-145">Należy pamiętać, że ten czas EF wykrywa, czy baza danych już istnieje.</span><span class="sxs-lookup"><span data-stu-id="fd3a7-145">Note that this time, EF detects that the database already exists.</span></span> <span data-ttu-id="fd3a7-146">Ponadto, gdy pierwsza migracja została zastosowana powyżej, ten fakt został zarejestrowany w specjalnej tabeli historii migracji w bazie danych programu; Umożliwia to programowi EF automatyczne stosowanie tylko nowej migracji.</span><span class="sxs-lookup"><span data-stu-id="fd3a7-146">In addition, when our first migration was applied above, this fact was recorded in a special migrations history table in your database; this allows EF to automatically apply only the new migration.</span></span>

### <a name="next-steps"></a><span data-ttu-id="fd3a7-147">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="fd3a7-147">Next steps</span></span>

<span data-ttu-id="fd3a7-148">Powyższe dane były tylko krótkim wprowadzeniem do migracji.</span><span class="sxs-lookup"><span data-stu-id="fd3a7-148">The above was only a brief introduction to migrations.</span></span> <span data-ttu-id="fd3a7-149">Zapoznaj się z innymi stronami dokumentacji, aby dowiedzieć się więcej na temat [zarządzania migracjami](xref:core/managing-schemas/migrations/managing), [stosowania ich](xref:core/managing-schemas/migrations/applying)i innych aspektów.</span><span class="sxs-lookup"><span data-stu-id="fd3a7-149">Please consult the other documentation pages to learn more about [managing migrations](xref:core/managing-schemas/migrations/managing), [applying them](xref:core/managing-schemas/migrations/applying), and other aspects.</span></span> <span data-ttu-id="fd3a7-150">[Dokumentacja narzędzia interfejs wiersza polecenia platformy .NET Core](xref:core/miscellaneous/cli/index) zawiera również przydatne informacje dotyczące różnych poleceń</span><span class="sxs-lookup"><span data-stu-id="fd3a7-150">The [.NET Core CLI tool reference](xref:core/miscellaneous/cli/index) also contains useful information on the different commands</span></span>
