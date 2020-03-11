---
title: Korzystanie z oddzielnego projektu migracji — EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/migrations/projects
ms.openlocfilehash: 89b7f50fe750c2953aa75efcdffcb1a5199ce90c
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78416816"
---
# <a name="using-a-separate-migrations-project"></a><span data-ttu-id="44cc4-102">Korzystanie z oddzielnego projektu migracji</span><span class="sxs-lookup"><span data-stu-id="44cc4-102">Using a Separate Migrations Project</span></span>

<span data-ttu-id="44cc4-103">Możesz chcieć przechowywać migracje w innym zestawie niż ten, który zawiera `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="44cc4-103">You may want to store your migrations in a different assembly than the one containing your `DbContext`.</span></span> <span data-ttu-id="44cc4-104">Ta strategia służy również do obsługi wielu zestawów migracji, na przykład jednej do programowania i dla uaktualnień Release-to-Release.</span><span class="sxs-lookup"><span data-stu-id="44cc4-104">You can also use this strategy to maintain multiple sets of migrations, for example, one for development and another for release-to-release upgrades.</span></span>

<span data-ttu-id="44cc4-105">Aby wykonać tę czynność...</span><span class="sxs-lookup"><span data-stu-id="44cc4-105">To do this...</span></span>

1. <span data-ttu-id="44cc4-106">Utwórz nową bibliotekę klas.</span><span class="sxs-lookup"><span data-stu-id="44cc4-106">Create a new class library.</span></span>

2. <span data-ttu-id="44cc4-107">Dodaj odwołanie do zestawu DbContext.</span><span class="sxs-lookup"><span data-stu-id="44cc4-107">Add a reference to your DbContext assembly.</span></span>

3. <span data-ttu-id="44cc4-108">Przenieś migracje i pliki migawek modelu do biblioteki klas.</span><span class="sxs-lookup"><span data-stu-id="44cc4-108">Move the migrations and model snapshot files to the class library.</span></span>
   > [!TIP]
   > <span data-ttu-id="44cc4-109">Jeśli nie masz żadnych istniejących migracji, wygeneruj je w projekcie zawierającym DbContext, a następnie przenieś go.</span><span class="sxs-lookup"><span data-stu-id="44cc4-109">If you have no existing migrations, generate one in the project containing the DbContext then move it.</span></span>
   > <span data-ttu-id="44cc4-110">Jest to ważne, ponieważ w przypadku, gdy zestaw migracji nie zawiera istniejącej migracji, polecenie Add-Migration nie będzie mogło odnaleźć kontekstu DB.</span><span class="sxs-lookup"><span data-stu-id="44cc4-110">This is important because if the migrations assembly does not contain an existing migration, the Add-Migration command will be unable to find the DbContext.</span></span>

4. <span data-ttu-id="44cc4-111">Skonfiguruj zestaw migracji:</span><span class="sxs-lookup"><span data-stu-id="44cc4-111">Configure the migrations assembly:</span></span>

   ``` csharp
   options.UseSqlServer(
       connectionString,
       x => x.MigrationsAssembly("MyApp.Migrations"));
   ```

5. <span data-ttu-id="44cc4-112">Dodaj odwołanie do zestawu migracji z zestawu startowego.</span><span class="sxs-lookup"><span data-stu-id="44cc4-112">Add a reference to your migrations assembly from the startup assembly.</span></span>
   * <span data-ttu-id="44cc4-113">Jeśli spowoduje to zależność cykliczną, zaktualizuj ścieżkę wyjściową biblioteki klas:</span><span class="sxs-lookup"><span data-stu-id="44cc4-113">If this causes a circular dependency, update the output path of the class library:</span></span>

     ``` xml
     <PropertyGroup>
       <OutputPath>..\MyStartupProject\bin\$(Configuration)\</OutputPath>
     </PropertyGroup>
     ```

<span data-ttu-id="44cc4-114">Jeśli wszystko zostało prawidłowo wykonane, powinno być możliwe dodanie nowych migracji do projektu.</span><span class="sxs-lookup"><span data-stu-id="44cc4-114">If you did everything correctly, you should be able to add new migrations to the project.</span></span>

## <a name="net-core-cli"></a>[<span data-ttu-id="44cc4-115">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="44cc4-115">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add NewMigration --project MyApp.Migrations
```

## <a name="visual-studio"></a>[<span data-ttu-id="44cc4-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44cc4-116">Visual Studio</span></span>](#tab/vs)

``` powershell
Add-Migration NewMigration -Project MyApp.Migrations
```

***
