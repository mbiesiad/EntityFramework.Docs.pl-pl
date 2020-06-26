---
title: Migracja z wieloma dostawcami — EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/08/2017
uid: core/managing-schemas/migrations/providers
ms.openlocfilehash: 388829992687e626648889a47130cc61df1c0d1f
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370625"
---
# <a name="migrations-with-multiple-providers"></a><span data-ttu-id="4f595-102">Migracja z wieloma dostawcami</span><span class="sxs-lookup"><span data-stu-id="4f595-102">Migrations with Multiple Providers</span></span>

<span data-ttu-id="4f595-103">[Narzędzia EF Core][1] dotyczą tylko migracji szkieletowej dla aktywnego dostawcy.</span><span class="sxs-lookup"><span data-stu-id="4f595-103">The [EF Core Tools][1] only scaffold migrations for the active provider.</span></span> <span data-ttu-id="4f595-104">Czasami jednak może być konieczne użycie więcej niż jednego dostawcy (na przykład Microsoft SQL Server i oprogramowania SQLite) w kontekście DbContext.</span><span class="sxs-lookup"><span data-stu-id="4f595-104">Sometimes, however, you may want to use more than one provider (for example Microsoft SQL Server and SQLite) with your DbContext.</span></span> <span data-ttu-id="4f595-105">Istnieją dwa sposoby obsługi tego w przypadku migracji.</span><span class="sxs-lookup"><span data-stu-id="4f595-105">There are two ways to handle this with Migrations.</span></span> <span data-ttu-id="4f595-106">Można zachować dwa zestawy migracji — jeden dla każdego dostawcy — lub scalić je w jeden zestaw, który może współpracować z obydwoma.</span><span class="sxs-lookup"><span data-stu-id="4f595-106">You can maintain two sets of migrations--one for each provider--or merge them into a single set that can work on both.</span></span>

## <a name="two-migration-sets"></a><span data-ttu-id="4f595-107">Dwa zestawy migracji</span><span class="sxs-lookup"><span data-stu-id="4f595-107">Two migration sets</span></span>

<span data-ttu-id="4f595-108">W pierwszej kolejności są generowane dwie migracje dla każdej zmiany modelu.</span><span class="sxs-lookup"><span data-stu-id="4f595-108">In the first approach, you generate two migrations for each model change.</span></span>

<span data-ttu-id="4f595-109">Jednym ze sposobów jest umieszczenie każdego zestawu migracji [w osobnym zestawie][2] i ręczne przełączenie aktywnego dostawcy (i zestawu migracji) między dodawaniem dwóch migracji.</span><span class="sxs-lookup"><span data-stu-id="4f595-109">One way to do this is to put each migration set [in a separate assembly][2] and manually switch the active provider (and migrations assembly) between adding the two migrations.</span></span>

<span data-ttu-id="4f595-110">Innym podejściem, które ułatwia pracę z narzędziami, jest utworzenie nowego typu pochodzącego z kontekstu DbContext i zastąpienie aktywnego dostawcy.</span><span class="sxs-lookup"><span data-stu-id="4f595-110">Another approach that makes working with the tools easier is to create a new type that derives from your DbContext and overrides the active provider.</span></span> <span data-ttu-id="4f595-111">Ten typ jest używany w czasie projektowania podczas dodawania lub stosowania migracji.</span><span class="sxs-lookup"><span data-stu-id="4f595-111">This type is used at design time when adding or applying migrations.</span></span>

``` csharp
class MySqliteDbContext : MyDbContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=my.db");
}
```

> [!NOTE]
> <span data-ttu-id="4f595-112">Ponieważ każdy zestaw migracji używa własnych typów DbContext, to podejście nie wymaga użycia oddzielnego zestawu migracji.</span><span class="sxs-lookup"><span data-stu-id="4f595-112">Since each migration set uses its own DbContext types, this approach doesn't require using a separate migrations assembly.</span></span>

<span data-ttu-id="4f595-113">Podczas dodawania nowej migracji należy określić typy kontekstowe.</span><span class="sxs-lookup"><span data-stu-id="4f595-113">When adding new migration, specify the context types.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="4f595-114">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="4f595-114">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --context MyDbContext --output-dir Migrations/SqlServerMigrations
dotnet ef migrations add InitialCreate --context MySqliteDbContext --output-dir Migrations/SqliteMigrations
```

### <a name="visual-studio"></a>[<span data-ttu-id="4f595-115">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4f595-115">Visual Studio</span></span>](#tab/vs)

``` powershell
Add-Migration InitialCreate -Context MyDbContext -OutputDir Migrations\SqlServerMigrations
Add-Migration InitialCreate -Context MySqliteDbContext -OutputDir Migrations\SqliteMigrations
```

***

> [!TIP]
> <span data-ttu-id="4f595-116">Nie musisz określać katalogu wyjściowego dla kolejnych migracji, ponieważ są one tworzone jako elementy równorzędne do ostatniego.</span><span class="sxs-lookup"><span data-stu-id="4f595-116">You don't need to specify the output directory for subsequent migrations since they are created as siblings to the last one.</span></span>

## <a name="one-migration-set"></a><span data-ttu-id="4f595-117">Jeden zestaw migracji</span><span class="sxs-lookup"><span data-stu-id="4f595-117">One migration set</span></span>

<span data-ttu-id="4f595-118">Jeśli nie chcesz mieć dwóch zestawów migracji, możesz połączyć je ręcznie w jeden zestaw, który można zastosować do obu dostawców.</span><span class="sxs-lookup"><span data-stu-id="4f595-118">If you don't like having two sets of migrations, you can manually combine them into a single set that can be applied to both providers.</span></span>

<span data-ttu-id="4f595-119">Adnotacje mogą współistnieć, ponieważ dostawca ignoruje wszelkie adnotacje, które nie są zrozumiałe.</span><span class="sxs-lookup"><span data-stu-id="4f595-119">Annotations can coexist since a provider ignores any annotations that it doesn't understand.</span></span> <span data-ttu-id="4f595-120">Na przykład kolumna klucza podstawowego, która działa z obydwoma Microsoft SQL Server i SQLite może wyglądać następująco.</span><span class="sxs-lookup"><span data-stu-id="4f595-120">For example, a primary key column that works with both Microsoft SQL Server and SQLite might look like this.</span></span>

``` csharp
Id = table.Column<int>(nullable: false)
    .Annotation("SqlServer:ValueGenerationStrategy",
        SqlServerValueGenerationStrategy.IdentityColumn)
    .Annotation("Sqlite:Autoincrement", true),
```

<span data-ttu-id="4f595-121">Jeśli operacje mogą być stosowane tylko dla jednego dostawcy lub różnią się między dostawcami, należy użyć `ActiveProvider` właściwości, aby określić, który dostawca jest aktywny:</span><span class="sxs-lookup"><span data-stu-id="4f595-121">If operations can be applied only for one provider, or they're different between providers, use the `ActiveProvider` property to determine which provider is active:</span></span>

``` csharp
if (migrationBuilder.ActiveProvider == "Microsoft.EntityFrameworkCore.SqlServer")
{
    migrationBuilder.CreateSequence(
        name: "EntityFrameworkHiLoSequence");
}
```

  [1]: ../../miscellaneous/cli/index.md
  [2]: projects.md
