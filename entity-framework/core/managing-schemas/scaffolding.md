---
title: Odtwarzanie przez EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/13/2018
ms.assetid: 6263EF7D-4989-42E6-BDEE-45DA770342FB
uid: core/managing-schemas/scaffolding
ms.openlocfilehash: cb20120154101a9b92b4bf2bc06d20b1dafe88c1
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672973"
---
# <a name="reverse-engineering"></a><span data-ttu-id="7cc7e-102"> Odtwarzanie</span><span class="sxs-lookup"><span data-stu-id="7cc7e-102">Reverse Engineering</span></span>

<span data-ttu-id="7cc7e-103">Odtwarzanie jest procesem tworzenia szkieletu klas typu jednostki i klasy DbContext na podstawie schematu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-103">Reverse engineering is the process of scaffolding entity type classes and a DbContext class based on a database schema.</span></span> <span data-ttu-id="7cc7e-104">Można to zrobić za pomocą `Scaffold-DbContext` polecenia narzędzi EF Core Console Menedżera pakietów (PMC) lub `dotnet ef dbcontext scaffold` polecenia narzędzi interfejsu wiersza polecenia (CLI) platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-104">It can be performed using the `Scaffold-DbContext` command of the EF Core Package Manager Console (PMC) tools or the `dotnet ef dbcontext scaffold` command of the .NET Command-line Interface (CLI) tools.</span></span>

## <a name="installing"></a><span data-ttu-id="7cc7e-105">Instalowanie</span><span class="sxs-lookup"><span data-stu-id="7cc7e-105">Installing</span></span>

<span data-ttu-id="7cc7e-106">Przed odwróceniem zespołu należy zainstalować [Narzędzia PMC](xref:core/miscellaneous/cli/powershell) (tylko Visual Studio) lub [Narzędzia interfejsu wiersza polecenia](xref:core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="7cc7e-106">Before reverse engineering, you'll need to install either the [PMC tools](xref:core/miscellaneous/cli/powershell) (Visual Studio only) or the [CLI tools](xref:core/miscellaneous/cli/dotnet).</span></span> <span data-ttu-id="7cc7e-107">Zobacz linki, aby uzyskać szczegółowe informacje.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-107">See links for details.</span></span>

<span data-ttu-id="7cc7e-108">Należy również zainstalować odpowiedniego [dostawcę bazy danych](xref:core/providers/index) dla schematu bazy danych, który ma być odtwarzany.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-108">You'll also need to install an appropriate [database provider](xref:core/providers/index) for the database schema you want to reverse engineer.</span></span>

## <a name="connection-string"></a><span data-ttu-id="7cc7e-109">Parametry połączenia</span><span class="sxs-lookup"><span data-stu-id="7cc7e-109">Connection string</span></span>

<span data-ttu-id="7cc7e-110">Pierwszym argumentem polecenia są parametry połączenia z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-110">The first argument to the command is a connection string to the database.</span></span> <span data-ttu-id="7cc7e-111">Narzędzia użyją tych parametrów połączenia, aby odczytać schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-111">The tools will use this connection string to read the database schema.</span></span>

<span data-ttu-id="7cc7e-112">Sposób tworzenia cudzysłowów i ucieczki parametrów połączenia zależy od powłoki, która jest używana do wykonywania polecenia.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-112">How you quote and escape the connection string depends on which shell you are using to execute the command.</span></span> <span data-ttu-id="7cc7e-113">Szczegółowe informacje można znaleźć w dokumentacji powłoki.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-113">Refer to your shell's documentation for specifics.</span></span> <span data-ttu-id="7cc7e-114">Na przykład program PowerShell wymaga znaku ucieczki `$` , ale nie `\` .</span><span class="sxs-lookup"><span data-stu-id="7cc7e-114">For example, PowerShell requires you to escape the `$` character, but not `\`.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="7cc7e-115">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="7cc7e-115">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer
```

### <a name="visual-studio"></a>[<span data-ttu-id="7cc7e-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7cc7e-116">Visual Studio</span></span>](#tab/vs)

``` powershell
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer
```

***

### <a name="configuration-and-user-secrets"></a><span data-ttu-id="7cc7e-117">Klucze tajne konfiguracji i użytkownika</span><span class="sxs-lookup"><span data-stu-id="7cc7e-117">Configuration and User Secrets</span></span>

<span data-ttu-id="7cc7e-118">Jeśli masz projekt ASP.NET Core, możesz użyć `Name=<connection-string>` składni, aby odczytać parametry połączenia z konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-118">If you have an ASP.NET Core project, you can use the `Name=<connection-string>` syntax to read the connection string from configuration.</span></span>

<span data-ttu-id="7cc7e-119">Jest to dobre rozwiązanie przy użyciu [Narzędzia tajnego Menedżera](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager) w celu zachowania podzielenia hasła bazy danych od kodu bazowej.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-119">This works well with the [Secret Manager tool](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager) to keep your database password separate from your codebase.</span></span>

```dotnetcli
dotnet user-secrets set ConnectionStrings.Chinook "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook"
dotnet ef dbcontext scaffold Name=Chinook Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="provider-name"></a><span data-ttu-id="7cc7e-120">Nazwa dostawcy</span><span class="sxs-lookup"><span data-stu-id="7cc7e-120">Provider name</span></span>

<span data-ttu-id="7cc7e-121">Drugi argument jest nazwą dostawcy.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-121">The second argument is the provider name.</span></span> <span data-ttu-id="7cc7e-122">Nazwa dostawcy jest zwykle taka sama jak nazwa pakietu NuGet dostawcy.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-122">The provider name is typically the same as the provider's NuGet package name.</span></span>

## <a name="specifying-tables"></a><span data-ttu-id="7cc7e-123">Określanie tabel</span><span class="sxs-lookup"><span data-stu-id="7cc7e-123">Specifying tables</span></span>

<span data-ttu-id="7cc7e-124">Wszystkie tabele w schemacie bazy danych są domyślnie odtwarzane w postaci typów jednostek.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-124">All tables in the database schema are reverse engineered into entity types by default.</span></span> <span data-ttu-id="7cc7e-125">Można ograniczyć, które tabele są odtworzone, określając schematy i tabele.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-125">You can limit which tables are reverse engineered by specifying schemas and tables.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="7cc7e-126">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="7cc7e-126">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

<span data-ttu-id="7cc7e-127">`--schema`Opcja może być używana do dołączania każdej tabeli w schemacie, natomiast `--table` może być używana do dołączania określonych tabel.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-127">The `--schema` option can be used to include every table within a schema, while `--table` can be used to include specific tables.</span></span>

<span data-ttu-id="7cc7e-128">Aby dołączyć wiele tabel, określ opcję wiele razy:</span><span class="sxs-lookup"><span data-stu-id="7cc7e-128">To include multiple tables, specify the option multiple times:</span></span>

```dotnetcli
dotnet ef dbcontext scaffold ... --table Artist --table Album
```

### <a name="visual-studio"></a>[<span data-ttu-id="7cc7e-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7cc7e-129">Visual Studio</span></span>](#tab/vs)

<span data-ttu-id="7cc7e-130">`-Schemas`Opcja może być używana do dołączania każdej tabeli w schemacie, natomiast `-Tables` może być używana do dołączania określonych tabel.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-130">The `-Schemas` option can be used to include every table within a schema, while `-Tables` can be used to include specific tables.</span></span>

<span data-ttu-id="7cc7e-131">Aby dołączyć wiele tabel, użyj tablicy:</span><span class="sxs-lookup"><span data-stu-id="7cc7e-131">To include multiple tables, use an array:</span></span>

``` powershell
Scaffold-DbContext ... -Tables Artist, Album
```

***

## <a name="preserving-names"></a><span data-ttu-id="7cc7e-132">Zachowywanie nazw</span><span class="sxs-lookup"><span data-stu-id="7cc7e-132">Preserving names</span></span>

<span data-ttu-id="7cc7e-133">Nazwy tabel i kolumn są rozwiązywane w celu lepszego dopasowania do konwencji nazewnictwa platformy .NET dla typów i właściwości.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-133">Table and column names are fixed up to better match the .NET naming conventions for types and properties by default.</span></span> <span data-ttu-id="7cc7e-134">Określenie `-UseDatabaseNames` przełącznika w przystawce PMC lub `--use-database-names` opcja w interfejsie wiersza polecenia spowoduje wyłączenie tego zachowania z zachowaniem oryginalnych nazw baz danych, jak to możliwe.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-134">Specifying the `-UseDatabaseNames` switch in PMC or the `--use-database-names` option in the CLI will disable this behavior preserving the original database names as much as possible.</span></span> <span data-ttu-id="7cc7e-135">Nieprawidłowe identyfikatory platformy .NET nadal będą stałe i są one zgodne z konwencjami nazewnictwa platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-135">Invalid .NET identifiers will still be fixed and synthesized names like navigation properties will still conform to .NET naming conventions.</span></span>

## <a name="fluent-api-or-data-annotations"></a><span data-ttu-id="7cc7e-136">Interfejs API Fluent lub adnotacje danych</span><span class="sxs-lookup"><span data-stu-id="7cc7e-136">Fluent API or Data Annotations</span></span>

<span data-ttu-id="7cc7e-137">Typy jednostek są domyślnie konfigurowane przy użyciu interfejsu API Fluent.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-137">Entity types are configured using the Fluent API by default.</span></span> <span data-ttu-id="7cc7e-138">Określ `-DataAnnotations` (PMC) lub `--data-annotations` (CLI), aby zamiast tego używać adnotacji danych, gdy jest to możliwe.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-138">Specify `-DataAnnotations` (PMC) or `--data-annotations` (CLI) to instead use data annotations when possible.</span></span>

<span data-ttu-id="7cc7e-139">Na przykład korzystanie z interfejsu API Fluent spowoduje przetworzenie szkieletu:</span><span class="sxs-lookup"><span data-stu-id="7cc7e-139">For example, using the Fluent API will scaffold this:</span></span>

``` csharp
entity.Property(e => e.Title)
    .IsRequired()
    .HasMaxLength(160);
```

<span data-ttu-id="7cc7e-140">Używanie adnotacji danych spowoduje szkielet:</span><span class="sxs-lookup"><span data-stu-id="7cc7e-140">While using Data Annotations will scaffold this:</span></span>

``` csharp
[Required]
[StringLength(160)]
public string Title { get; set; }
```

## <a name="dbcontext-name"></a><span data-ttu-id="7cc7e-141">Nazwa kontekstu</span><span class="sxs-lookup"><span data-stu-id="7cc7e-141">DbContext name</span></span>

<span data-ttu-id="7cc7e-142">Nazwa klasy DbContext z szkieletem jest nazwą bazy danych domyślnie sufiksem z *kontekstem* .</span><span class="sxs-lookup"><span data-stu-id="7cc7e-142">The scaffolded DbContext class name will be the name of the database suffixed with *Context* by default.</span></span> <span data-ttu-id="7cc7e-143">Aby określić inny sposób, użyj `-Context` w PMC i `--context` w interfejsie wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-143">To specify a different one, use `-Context` in PMC and `--context` in the CLI.</span></span>

## <a name="directories-and-namespaces"></a><span data-ttu-id="7cc7e-144">Katalogi i przestrzenie nazw</span><span class="sxs-lookup"><span data-stu-id="7cc7e-144">Directories and namespaces</span></span>

<span data-ttu-id="7cc7e-145">Klasy jednostek i Klasa DbContext są szkieletowe w katalogu głównym projektu i używają domyślnej przestrzeni nazw projektu.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-145">The entity classes and a DbContext class are scaffolded into the project's root directory and use the project's default namespace.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="7cc7e-146">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="7cc7e-146">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

<span data-ttu-id="7cc7e-147">Można określić katalog, w którym są używane szkieletowe klasy `--output-dir` , i `--context-dir` może służyć do tworzenia szkieletu klasy DbContext w oddzielnym katalogu z klas typu jednostki:</span><span class="sxs-lookup"><span data-stu-id="7cc7e-147">You can specify the directory where classes are scaffolded using `--output-dir`, and `--context-dir` can be used to scaffold the DbContext class into a separate directory from the entity type classes:</span></span>

```dotnetcli
dotnet ef dbcontext scaffold ... --context-dir Data --output-dir Models
```

<span data-ttu-id="7cc7e-148">Domyślnie przestrzeń nazw będzie główną przestrzenią nazw oraz nazwami wszystkich podkatalogów w katalogu głównym projektu.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-148">By default, the namespace will be the root namespace plus the names of any subdirectories under the project's root directory.</span></span> <span data-ttu-id="7cc7e-149">Jednakże z EFCore 5,0, można przesłonić przestrzeń nazw dla wszystkich klas wyjściowych za pomocą `--namespace` .</span><span class="sxs-lookup"><span data-stu-id="7cc7e-149">However, from EFCore 5.0 onwards, you can override the namespace for all output classes by using `--namespace`.</span></span> <span data-ttu-id="7cc7e-150">Można również przesłonić przestrzeń nazw tylko dla klasy DbContext przy użyciu `--context-namespace` :</span><span class="sxs-lookup"><span data-stu-id="7cc7e-150">You can also override the namespace for just the DbContext class using `--context-namespace`:</span></span>

```dotnetcli
dotnet ef dbcontext scaffold ... --namespace Your.Namespace --context-namespace Your.DbContext.Namespace
```

### <a name="visual-studio"></a>[<span data-ttu-id="7cc7e-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7cc7e-151">Visual Studio</span></span>](#tab/vs)

<span data-ttu-id="7cc7e-152">Można określić katalog, w którym są używane szkieletowe klasy `-OutputDir` , i `-ContextDir` może służyć do tworzenia szkieletu klasy DbContext w oddzielnym katalogu z klas typu jednostki:</span><span class="sxs-lookup"><span data-stu-id="7cc7e-152">You can specify the directory where classes are scaffolded using `-OutputDir`, and `-ContextDir` can be used to scaffold the DbContext class into a separate directory from the entity type classes:</span></span>

``` powershell
Scaffold-DbContext ... -ContextDir Data -OutputDir Models
```

<span data-ttu-id="7cc7e-153">Domyślnie przestrzeń nazw będzie główną przestrzenią nazw oraz nazwami wszystkich podkatalogów w katalogu głównym projektu.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-153">By default, the namespace will be the root namespace plus the names of any subdirectories under the project's root directory.</span></span> <span data-ttu-id="7cc7e-154">Jednakże z EFCore 5,0, można przesłonić przestrzeń nazw dla wszystkich klas wyjściowych za pomocą `-Namespace` .</span><span class="sxs-lookup"><span data-stu-id="7cc7e-154">However, from EFCore 5.0 onwards, you can override the namespace for all output classes by using `-Namespace`.</span></span> <span data-ttu-id="7cc7e-155">Można również przesłonić przestrzeń nazw tylko dla klasy DbContext przy użyciu `-ContextNamespace` .</span><span class="sxs-lookup"><span data-stu-id="7cc7e-155">You can also override the namespace for just the DbContext class using `-ContextNamespace`.</span></span>

``` powershell
Scaffold-DbContext ... -Namespace Your.Namespace -ContextNamespace Your.DbContext.Namespace
```

***

## <a name="how-it-works"></a><span data-ttu-id="7cc7e-156">Jak to działa</span><span class="sxs-lookup"><span data-stu-id="7cc7e-156">How it works</span></span>

<span data-ttu-id="7cc7e-157">Odtwarzanie rozpocznie się, odczytując schemat bazy danych.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-157">Reverse engineering starts by reading the database schema.</span></span> <span data-ttu-id="7cc7e-158">Odczytuje informacje o tabelach, kolumnach, ograniczeniach i indeksach.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-158">It reads information about tables, columns, constraints, and indexes.</span></span>

<span data-ttu-id="7cc7e-159">Następnie używa informacji o schemacie do tworzenia modelu EF Core.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-159">Next, it uses the schema information to create an EF Core model.</span></span> <span data-ttu-id="7cc7e-160">Tabele są używane do tworzenia typów jednostek; kolumny są używane do tworzenia właściwości; i klucze obce są używane do tworzenia relacji.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-160">Tables are used to create entity types; columns are used to create properties; and foreign keys are used to create relationships.</span></span>

<span data-ttu-id="7cc7e-161">Na koniec model jest używany do generowania kodu.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-161">Finally, the model is used to generate code.</span></span> <span data-ttu-id="7cc7e-162">Odpowiednie klasy typu jednostki, interfejs API Fluent i adnotacje danych są szkieletami, aby można było ponownie utworzyć ten sam model z poziomu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-162">The corresponding entity type classes, Fluent API, and data annotations are scaffolded in order to re-create the same model from your app.</span></span>

## <a name="limitations"></a><span data-ttu-id="7cc7e-163">Ograniczenia</span><span class="sxs-lookup"><span data-stu-id="7cc7e-163">Limitations</span></span>

* <span data-ttu-id="7cc7e-164">Nie wszystkie elementy dotyczące modelu mogą być reprezentowane za pomocą schematu bazy danych.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-164">Not everything about a model can be represented using a database schema.</span></span> <span data-ttu-id="7cc7e-165">Na przykład informacje o [**hierarchiach dziedziczenia**](../modeling/inheritance.md), [**typach będących własnością**](../modeling/owned-entities.md)i [**podziałie tabeli**](../modeling/table-splitting.md) nie są obecne w schemacie bazy danych.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-165">For example, information about [**inheritance hierarchies**](../modeling/inheritance.md), [**owned types**](../modeling/owned-entities.md), and [**table splitting**](../modeling/table-splitting.md) are not present in the database schema.</span></span> <span data-ttu-id="7cc7e-166">W związku z tym konstrukcje te nigdy nie będą odtwarzane.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-166">Because of this, these constructs will never be reverse engineered.</span></span>
* <span data-ttu-id="7cc7e-167">Ponadto **niektóre typy kolumn** mogą nie być obsługiwane przez dostawcę EF Core.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-167">In addition, **some column types** may not be supported by the EF Core provider.</span></span> <span data-ttu-id="7cc7e-168">Te kolumny nie zostaną uwzględnione w modelu.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-168">These columns won't be included in the model.</span></span>
* <span data-ttu-id="7cc7e-169">Można zdefiniować [**tokeny współbieżności**](../modeling/concurrency.md)w modelu EF Core, aby uniemożliwić dwóm użytkownikom Aktualizowanie tej samej jednostki w tym samym czasie.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-169">You can define [**concurrency tokens**](../modeling/concurrency.md), in an EF Core model to prevent two users from updating the same entity at the same time.</span></span> <span data-ttu-id="7cc7e-170">Niektóre bazy danych mają specjalny typ reprezentujący ten typ kolumny (na przykład rowversion w SQL Server), w którym można odtworzyć te informacje. Jednak inne tokeny współbieżności nie będą odtwarzane.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-170">Some databases have a special type to represent this type of column (for example, rowversion in SQL Server) in which case we can reverse engineer this information; however, other concurrency tokens will not be reverse engineered.</span></span>
* <span data-ttu-id="7cc7e-171">[Funkcja typ referencyjny języka C# 8](/dotnet/csharp/tutorials/nullable-reference-types) nie jest obecnie obsługiwana w przypadku odtwarzania: EF Core zawsze generuje kod C#, który zakłada, że ta funkcja jest wyłączona.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-171">[The C# 8 nullable reference type feature](/dotnet/csharp/tutorials/nullable-reference-types) is currently unsupported in reverse engineering: EF Core always generates C# code that assumes the feature is disabled.</span></span> <span data-ttu-id="7cc7e-172">Na przykład kolumny tekstu dopuszczające wartość null będą szkieletem jako właściwość typu `string` , a nie `string?` z interfejsem API Fluent lub adnotacjami danych używanymi do konfigurowania, czy właściwość jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-172">For example, nullable text columns will be scaffolded as a property with type `string` , not `string?`, with either the Fluent API or Data Annotations used to configure whether a property is required or not.</span></span> <span data-ttu-id="7cc7e-173">Można edytować kod szkieletowy i zastąpić je za pomocą adnotacji o wartości null w języku C#.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-173">You can edit the scaffolded code and replace these with C# nullability annotations.</span></span> <span data-ttu-id="7cc7e-174">Obsługa tworzenia szkieletów dla typów odwołań do wartości null jest śledzona przez [#15520](https://github.com/aspnet/EntityFrameworkCore/issues/15520)problemu.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-174">Scaffolding support for nullable reference types is tracked by issue [#15520](https://github.com/aspnet/EntityFrameworkCore/issues/15520).</span></span>

## <a name="customizing-the-model"></a><span data-ttu-id="7cc7e-175">Dostosowywanie modelu</span><span class="sxs-lookup"><span data-stu-id="7cc7e-175">Customizing the model</span></span>

<span data-ttu-id="7cc7e-176">Kod wygenerowany przez EF Core to Twój kod.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-176">The code generated by EF Core is your code.</span></span> <span data-ttu-id="7cc7e-177">Możesz go zmienić.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-177">Feel free to change it.</span></span> <span data-ttu-id="7cc7e-178">Zostanie on wygenerowany ponownie tylko w przypadku ponownego odtworzenia tego samego modelu.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-178">It will only be regenerated if you reverse engineer the same model again.</span></span> <span data-ttu-id="7cc7e-179">Kod szkieletowy reprezentuje *jeden* model, który może być używany w celu uzyskania dostępu do bazy danych, ale nie jest to *jedyny* model, który może być używany.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-179">The scaffolded code represents *one* model that can be used to access the database, but it's certainly not the *only* model that can be used.</span></span>

<span data-ttu-id="7cc7e-180">Dostosuj klasy typu jednostki i klasy DbContext do Twoich potrzeb.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-180">Customize the entity type classes and DbContext class to fit your needs.</span></span> <span data-ttu-id="7cc7e-181">Można na przykład zmienić nazwy typów i właściwości, wprowadzić hierarchie dziedziczenia lub podzielić tabelę na wiele jednostek.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-181">For example, you may choose to rename types and properties, introduce inheritance hierarchies, or split a table into to multiple entities.</span></span> <span data-ttu-id="7cc7e-182">Można również usunąć nieunikatowe indeksy, nieużywane sekwencje i właściwości nawigacji, opcjonalne właściwości skalarne i nazwy ograniczeń z modelu.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-182">You can also remove non-unique indexes, unused sequences and navigation properties, optional scalar properties, and constraint names from the model.</span></span>

<span data-ttu-id="7cc7e-183">Można również dodać dodatkowe konstruktory, metody, właściwości itp.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-183">You can also add additional constructors, methods, properties, etc.</span></span> <span data-ttu-id="7cc7e-184">użycie innej klasy częściowej w oddzielnym pliku.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-184">using another partial class in a separate file.</span></span> <span data-ttu-id="7cc7e-185">Takie podejście działa nawet wtedy, gdy zamierzasz ponownie odtworzyć model.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-185">This approach works even when you intend to reverse engineer the model again.</span></span>

## <a name="updating-the-model"></a><span data-ttu-id="7cc7e-186">Aktualizowanie modelu</span><span class="sxs-lookup"><span data-stu-id="7cc7e-186">Updating the model</span></span>

<span data-ttu-id="7cc7e-187">Po wprowadzeniu zmian w bazie danych może być konieczne zaktualizowanie modelu EF Core w celu odzwierciedlenia tych zmian.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-187">After making changes to the database, you may need to update your EF Core model to reflect those changes.</span></span> <span data-ttu-id="7cc7e-188">W przypadku, gdy baza danych jest prosta, może być najłatwiej ręcznie wprowadzić zmiany w modelu EF Core.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-188">If the database changes are simple, it may be easiest just to manually make the changes to your EF Core model.</span></span> <span data-ttu-id="7cc7e-189">Na przykład zmiana nazwy tabeli lub kolumny, usunięcie kolumny lub aktualizacja typu kolumny to proste zmiany wprowadzane w kodzie.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-189">For example, renaming a table or column, removing a column, or updating a column's type are trivial changes to make in code.</span></span>

<span data-ttu-id="7cc7e-190">Bardziej znaczące zmiany nie są jednak łatwe do ręcznego wprowadzania.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-190">More significant changes, however, are not as easy make manually.</span></span> <span data-ttu-id="7cc7e-191">Jednym z typowych przepływów pracy jest ponowne odtwarzanie modelu z bazy danych przy użyciu `-Force` (PMC) lub `--force` (CLI), aby zastąpić istniejący model zaktualizowanym.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-191">One common workflow is to reverse engineer the model from the database again using `-Force` (PMC) or `--force` (CLI) to overwrite the existing model with an updated one.</span></span>

<span data-ttu-id="7cc7e-192">Inną często żądaną funkcją jest możliwość aktualizowania modelu z bazy danych przy zachowaniu dostosowania, takiego jak renames, hierarchie typów itd. Użyj [#831](https://github.com/aspnet/EntityFrameworkCore/issues/831) problemu, aby śledzić postęp tej funkcji.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-192">Another commonly requested feature is the ability to update the model from the database while preserving customization like renames, type hierarchies, etc. Use issue [#831](https://github.com/aspnet/EntityFrameworkCore/issues/831) to track the progress of this feature.</span></span>

> [!WARNING]
> <span data-ttu-id="7cc7e-193">Jeśli odwrócisz model z bazy danych, wszelkie zmiany wprowadzone w plikach zostaną utracone.</span><span class="sxs-lookup"><span data-stu-id="7cc7e-193">If you reverse engineer the model from the database again, any changes you've made to the files will be lost.</span></span>
