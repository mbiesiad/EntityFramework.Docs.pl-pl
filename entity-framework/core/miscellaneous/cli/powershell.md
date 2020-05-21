---
title: Dokumentacja narzędzi EF Core Tools (konsola Menedżera pakietów) — EF Core
author: bricelam
ms.author: bricelam
ms.date: 09/18/2018
uid: core/miscellaneous/cli/powershell
ms.openlocfilehash: 698a2cadadb1389f2e659e3ecab2fb21d020322e
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672938"
---
# <a name="entity-framework-core-tools-reference---package-manager-console-in-visual-studio"></a><span data-ttu-id="8e033-102">Dokumentacja narzędzi Entity Framework Core Tools — konsola Menedżera pakietów w programie Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8e033-102">Entity Framework Core tools reference - Package Manager Console in Visual Studio</span></span>

<span data-ttu-id="8e033-103">Narzędzia konsoli Menedżera pakietów (PMC) dla Entity Framework Core wykonują zadania deweloperskie czasu projektowania.</span><span class="sxs-lookup"><span data-stu-id="8e033-103">The Package Manager Console (PMC) tools for Entity Framework Core perform design-time development tasks.</span></span> <span data-ttu-id="8e033-104">Na przykład tworzą [migracje](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0), stosują migracje i generują kod dla modelu na podstawie istniejącej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="8e033-104">For example, they create [migrations](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0), apply migrations, and generate code for a model based on an existing database.</span></span> <span data-ttu-id="8e033-105">Polecenia są uruchamiane w programie Visual Studio przy użyciu [konsoli Menedżera pakietów](/nuget/tools/package-manager-console).</span><span class="sxs-lookup"><span data-stu-id="8e033-105">The commands run inside of Visual Studio using the [Package Manager Console](/nuget/tools/package-manager-console).</span></span> <span data-ttu-id="8e033-106">Te narzędzia współpracują z projektami .NET Framework i .NET Core.</span><span class="sxs-lookup"><span data-stu-id="8e033-106">These tools work with both .NET Framework and .NET Core projects.</span></span>

<span data-ttu-id="8e033-107">Jeśli nie korzystasz z programu Visual Studio, zamiast tego zalecamy używanie [EF Core narzędzi wiersza polecenia](dotnet.md) .</span><span class="sxs-lookup"><span data-stu-id="8e033-107">If you aren't using Visual Studio, we recommend the [EF Core Command-line Tools](dotnet.md) instead.</span></span> <span data-ttu-id="8e033-108">Narzędzia interfejsu wiersza polecenia są na wielu platformach i uruchamiane w wierszu poleceń.</span><span class="sxs-lookup"><span data-stu-id="8e033-108">The CLI tools are cross-platform and run inside a command prompt.</span></span>

## <a name="installing-the-tools"></a><span data-ttu-id="8e033-109">Instalowanie narzędzi</span><span class="sxs-lookup"><span data-stu-id="8e033-109">Installing the tools</span></span>

<span data-ttu-id="8e033-110">Procedury instalowania i aktualizowania narzędzi różnią się między wersjami ASP.NET Core 2.1 + i starszymi lub innymi typami projektów.</span><span class="sxs-lookup"><span data-stu-id="8e033-110">The procedures for installing and updating the tools differ between ASP.NET Core 2.1+ and earlier versions or other project types.</span></span>

### <a name="aspnet-core-version-21-and-later"></a><span data-ttu-id="8e033-111">ASP.NET Core wersja 2,1 lub nowsza</span><span class="sxs-lookup"><span data-stu-id="8e033-111">ASP.NET Core version 2.1 and later</span></span>

<span data-ttu-id="8e033-112">Narzędzia są automatycznie dołączane do ASP.NET Core 2.1 i projektu, ponieważ `Microsoft.EntityFrameworkCore.Tools` pakiet jest zawarty w [pakiecie Microsoft. AspNetCore. app](/aspnet/core/fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="8e033-112">The tools are automatically included in an ASP.NET Core 2.1+ project because the `Microsoft.EntityFrameworkCore.Tools` package is included in the [Microsoft.AspNetCore.App metapackage](/aspnet/core/fundamentals/metapackage-app).</span></span>

<span data-ttu-id="8e033-113">W związku z tym nie trzeba wykonywać żadnych czynności w celu zainstalowania narzędzi, ale trzeba:</span><span class="sxs-lookup"><span data-stu-id="8e033-113">Therefore, you don't have to do anything to install the tools, but you do have to:</span></span>

* <span data-ttu-id="8e033-114">Przywróć pakiety przed użyciem narzędzi w nowym projekcie.</span><span class="sxs-lookup"><span data-stu-id="8e033-114">Restore packages before using the tools in a new project.</span></span>
* <span data-ttu-id="8e033-115">Zainstaluj pakiet, aby zaktualizować narzędzia do nowszej wersji.</span><span class="sxs-lookup"><span data-stu-id="8e033-115">Install a package to update the tools to a newer version.</span></span>

<span data-ttu-id="8e033-116">Aby upewnić się, że korzystasz z najnowszej wersji narzędzi, zalecamy również wykonanie następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="8e033-116">To make sure that you're getting the latest version of the tools, we recommend that you also do the following step:</span></span>

* <span data-ttu-id="8e033-117">Edytuj plik *. csproj* i Dodaj wiersz określający najnowszą wersję pakietu [Microsoft. EntityFrameworkCore. Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/) .</span><span class="sxs-lookup"><span data-stu-id="8e033-117">Edit your *.csproj* file and add a line specifying the latest version of the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/) package.</span></span> <span data-ttu-id="8e033-118">Na przykład plik *. csproj* może zawierać następujący komunikat `ItemGroup` :</span><span class="sxs-lookup"><span data-stu-id="8e033-118">For example, the *.csproj* file might include an `ItemGroup` that looks like this:</span></span>

  ```xml
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="3.1.3" />
    <PackageReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Design" Version="3.1.2" />
  </ItemGroup>
  ```

<span data-ttu-id="8e033-119">Zaktualizuj narzędzia, gdy zostanie wyświetlony komunikat podobny do następującego:</span><span class="sxs-lookup"><span data-stu-id="8e033-119">Update the tools when you get a message like the following example:</span></span>

> <span data-ttu-id="8e033-120">Narzędzia EF Core w wersji "2.1.1-RTM-30846" są starsze niż środowisko uruchomieniowe "2.1.3-RTM-32065".</span><span class="sxs-lookup"><span data-stu-id="8e033-120">The EF Core tools version '2.1.1-rtm-30846' is older than that of the runtime '2.1.3-rtm-32065'.</span></span> <span data-ttu-id="8e033-121">Zaktualizuj narzędzia dla najnowszych funkcji i poprawek błędów.</span><span class="sxs-lookup"><span data-stu-id="8e033-121">Update the tools for the latest features and bug fixes.</span></span>

<span data-ttu-id="8e033-122">Aby zaktualizować narzędzia:</span><span class="sxs-lookup"><span data-stu-id="8e033-122">To update the tools:</span></span>

* <span data-ttu-id="8e033-123">Zainstaluj najnowszą zestaw .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="8e033-123">Install the latest .NET Core SDK.</span></span>
* <span data-ttu-id="8e033-124">Zaktualizuj program Visual Studio do najnowszej wersji.</span><span class="sxs-lookup"><span data-stu-id="8e033-124">Update Visual Studio to the latest version.</span></span>
* <span data-ttu-id="8e033-125">Edytuj plik *. csproj* , tak aby zawierał odwołanie do pakietu dla najnowszych narzędzi, jak pokazano wcześniej.</span><span class="sxs-lookup"><span data-stu-id="8e033-125">Edit the *.csproj* file so that it includes a package reference to the latest tools package, as shown earlier.</span></span>

### <a name="other-versions-and-project-types"></a><span data-ttu-id="8e033-126">Inne wersje i typy projektów</span><span class="sxs-lookup"><span data-stu-id="8e033-126">Other versions and project types</span></span>

<span data-ttu-id="8e033-127">Zainstaluj narzędzia konsoli Menedżera pakietów, uruchamiając następujące polecenie w **konsoli Menedżera pakietów**:</span><span class="sxs-lookup"><span data-stu-id="8e033-127">Install the Package Manager Console tools by running the following command in **Package Manager Console**:</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="8e033-128">Zaktualizuj narzędzia, uruchamiając następujące polecenie w **konsoli Menedżera pakietów**.</span><span class="sxs-lookup"><span data-stu-id="8e033-128">Update the tools by running the following command in **Package Manager Console**.</span></span>

``` powershell
Update-Package Microsoft.EntityFrameworkCore.Tools
```

### <a name="verify-the-installation"></a><span data-ttu-id="8e033-129">Weryfikacja instalacji</span><span class="sxs-lookup"><span data-stu-id="8e033-129">Verify the installation</span></span>

<span data-ttu-id="8e033-130">Sprawdź, czy narzędzia są zainstalowane, uruchamiając następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="8e033-130">Verify that the tools are installed by running this command:</span></span>

``` powershell
Get-Help about_EntityFrameworkCore
```

<span data-ttu-id="8e033-131">Dane wyjściowe wyglądają następująco (nie informuje o jakiej wersji narzędzi, z których korzystasz):</span><span class="sxs-lookup"><span data-stu-id="8e033-131">The output looks like this (it doesn't tell you which version of the tools you're using):</span></span>

```console

                     _/\__
               ---==/    \\
         ___  ___   |.    \|\
        | __|| __|  |  )   \\\
        | _| | _|   \_/ |  //|\\
        |___||_|       /   \\\/\\

TOPIC
    about_EntityFrameworkCore

SHORT DESCRIPTION
    Provides information about the Entity Framework Core Package Manager Console Tools.

<A list of available commands follows, omitted here.>
```

## <a name="using-the-tools"></a><span data-ttu-id="8e033-132">Korzystanie z narzędzi</span><span class="sxs-lookup"><span data-stu-id="8e033-132">Using the tools</span></span>

<span data-ttu-id="8e033-133">Przed rozpoczęciem korzystania z narzędzi:</span><span class="sxs-lookup"><span data-stu-id="8e033-133">Before using the tools:</span></span>

* <span data-ttu-id="8e033-134">Zapoznaj się z różnicą między projektem docelowym i początkowym.</span><span class="sxs-lookup"><span data-stu-id="8e033-134">Understand the difference between target and startup project.</span></span>
* <span data-ttu-id="8e033-135">Dowiedz się, jak używać narzędzi z bibliotekami klas .NET Standard.</span><span class="sxs-lookup"><span data-stu-id="8e033-135">Learn how to use the tools with .NET Standard class libraries.</span></span>
* <span data-ttu-id="8e033-136">W przypadku projektów ASP.NET Core Ustaw środowisko.</span><span class="sxs-lookup"><span data-stu-id="8e033-136">For ASP.NET Core projects, set the environment.</span></span>

### <a name="target-and-startup-project"></a><span data-ttu-id="8e033-137">Projekt docelowy i startowy</span><span class="sxs-lookup"><span data-stu-id="8e033-137">Target and startup project</span></span>

<span data-ttu-id="8e033-138">Polecenia odnoszą się do *projektu* i *projektu startowego*.</span><span class="sxs-lookup"><span data-stu-id="8e033-138">The commands refer to a *project* and a *startup project*.</span></span>

* <span data-ttu-id="8e033-139">*Projekt* jest również znany jako *projekt docelowy* , ponieważ jest to miejsce, w którym polecenia dodają lub usuwają pliki.</span><span class="sxs-lookup"><span data-stu-id="8e033-139">The *project* is also known as the *target project* because it's where the commands add or remove files.</span></span> <span data-ttu-id="8e033-140">Domyślnie **domyślnym projektem** wybranym w **konsoli Menedżera pakietów** jest projekt docelowy.</span><span class="sxs-lookup"><span data-stu-id="8e033-140">By default, the **Default project** selected in **Package Manager Console** is the target project.</span></span> <span data-ttu-id="8e033-141">Możesz określić inny projekt jako projekt docelowy przy użyciu <nobr>`--project`</nobr> opcji.</span><span class="sxs-lookup"><span data-stu-id="8e033-141">You can specify a different project as target project by using the <nobr>`--project`</nobr> option.</span></span>

* <span data-ttu-id="8e033-142">*Projekt startowy* jest tym, że narzędzia kompilują i uruchamiają.</span><span class="sxs-lookup"><span data-stu-id="8e033-142">The *startup project* is the one that the tools build and run.</span></span> <span data-ttu-id="8e033-143">Narzędzia muszą wykonywać kod aplikacji w czasie projektowania, aby uzyskać informacje o projekcie, takie jak parametry połączenia bazy danych i Konfiguracja modelu.</span><span class="sxs-lookup"><span data-stu-id="8e033-143">The tools have to execute application code at design time to get information about the project, such as the database connection string and the configuration of the model.</span></span> <span data-ttu-id="8e033-144">Domyślnie **projekt startowy** w **Eksplorator rozwiązań** jest projektem startowym.</span><span class="sxs-lookup"><span data-stu-id="8e033-144">By default, the **Startup Project** in **Solution Explorer** is the startup project.</span></span> <span data-ttu-id="8e033-145">Możesz określić inny projekt jako projekt startowy przy użyciu <nobr>`--startup-project`</nobr> opcji.</span><span class="sxs-lookup"><span data-stu-id="8e033-145">You can specify a different project as startup project by using the <nobr>`--startup-project`</nobr> option.</span></span>

<span data-ttu-id="8e033-146">Projekt startowy i projekt docelowy są często tymi samymi projektami.</span><span class="sxs-lookup"><span data-stu-id="8e033-146">The startup project and target project are often the same project.</span></span> <span data-ttu-id="8e033-147">Typowy scenariusz, w którym są oddzielnymi projektami, to:</span><span class="sxs-lookup"><span data-stu-id="8e033-147">A typical scenario where they are separate projects is when:</span></span>

* <span data-ttu-id="8e033-148">EF Core kontekstu i klasy jednostek znajdują się w bibliotece klas .NET Core.</span><span class="sxs-lookup"><span data-stu-id="8e033-148">The EF Core context and entity classes are in a .NET Core class library.</span></span>
* <span data-ttu-id="8e033-149">Aplikacja konsolowa platformy .NET Core lub aplikacja internetowa odwołuje się do biblioteki klas.</span><span class="sxs-lookup"><span data-stu-id="8e033-149">A .NET Core console app or web app references the class library.</span></span>

<span data-ttu-id="8e033-150">Możliwe jest również [umieszczenie kodu migracji w bibliotece klas odrębnie od kontekstu EF Core](xref:core/managing-schemas/migrations/projects).</span><span class="sxs-lookup"><span data-stu-id="8e033-150">It's also possible to [put migrations code in a class library separate from the EF Core context](xref:core/managing-schemas/migrations/projects).</span></span>

### <a name="other-target-frameworks"></a><span data-ttu-id="8e033-151">Inne platformy docelowe</span><span class="sxs-lookup"><span data-stu-id="8e033-151">Other target frameworks</span></span>

<span data-ttu-id="8e033-152">Narzędzia konsoli Menedżera pakietów współpracują z projektami .NET Core lub .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="8e033-152">The Package Manager Console tools work with .NET Core or .NET Framework projects.</span></span> <span data-ttu-id="8e033-153">Aplikacje, które mają model EF Core w bibliotece klas .NET Standard mogą nie mieć projektu .NET Core lub .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="8e033-153">Apps that have the EF Core model in a .NET Standard class library might not have a .NET Core or .NET Framework project.</span></span> <span data-ttu-id="8e033-154">Na przykład jest to prawdziwe w aplikacjach Xamarin i platforma uniwersalna systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="8e033-154">For example, this is true of Xamarin and Universal Windows Platform apps.</span></span> <span data-ttu-id="8e033-155">W takich przypadkach można utworzyć projekt aplikacji konsolowej .NET Core lub .NET Framework, którego jedynym celem jest działanie jako projekt startowy dla narzędzi.</span><span class="sxs-lookup"><span data-stu-id="8e033-155">In such cases, you can create a .NET Core or .NET Framework console app project whose only purpose is to act as startup project for the tools.</span></span> <span data-ttu-id="8e033-156">Projekt może być fikcyjnym projektem bez rzeczywistego kodu, który &mdash; jest wymagany tylko do udostępnienia obiektu docelowego dla narzędzi.</span><span class="sxs-lookup"><span data-stu-id="8e033-156">The project can be a dummy project with no real code &mdash; it is only needed to provide a target for the tooling.</span></span>

<span data-ttu-id="8e033-157">Dlaczego jest wymagany projekt fikcyjny?</span><span class="sxs-lookup"><span data-stu-id="8e033-157">Why is a dummy project required?</span></span> <span data-ttu-id="8e033-158">Jak wspomniano wcześniej, narzędzia muszą wykonać kod aplikacji w czasie projektowania.</span><span class="sxs-lookup"><span data-stu-id="8e033-158">As mentioned earlier, the tools have to execute application code at design time.</span></span> <span data-ttu-id="8e033-159">W tym celu należy użyć środowiska uruchomieniowego .NET Core lub .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="8e033-159">To do that, they need to use the .NET Core or .NET Framework runtime.</span></span> <span data-ttu-id="8e033-160">Gdy model EF Core znajduje się w projekcie, który jest przeznaczony dla programu .NET Core lub .NET Framework, narzędzia EF Core zażyczą sobie środowisko uruchomieniowe z projektu.</span><span class="sxs-lookup"><span data-stu-id="8e033-160">When the EF Core model is in a project that targets .NET Core or .NET Framework, the EF Core tools borrow the runtime from the project.</span></span> <span data-ttu-id="8e033-161">Nie można tego zrobić, jeśli model EF Core znajduje się w .NET Standardej bibliotece klas.</span><span class="sxs-lookup"><span data-stu-id="8e033-161">They can't do that if the EF Core model is in a .NET Standard class library.</span></span> <span data-ttu-id="8e033-162">.NET Standard nie jest rzeczywistą implementacją platformy .NET; jest to specyfikacja zestawu interfejsów API, które muszą być obsługiwane przez implementacje platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="8e033-162">The .NET Standard is not an actual .NET implementation; it's a specification of a set of APIs that .NET implementations must support.</span></span> <span data-ttu-id="8e033-163">W związku z tym .NET Standard nie są wystarczające dla narzędzi EF Core do wykonywania kodu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8e033-163">Therefore .NET Standard is not sufficient for the EF Core tools to execute application code.</span></span> <span data-ttu-id="8e033-164">Projekt fikcyjny tworzony do użycia jako projekt startowy zapewnia konkretną platformę docelową, do której narzędzia mogą ładować .NET Standard biblioteki klas.</span><span class="sxs-lookup"><span data-stu-id="8e033-164">The dummy project you create to use as startup project provides a concrete target platform into which the tools can load the .NET Standard class library.</span></span>

### <a name="aspnet-core-environment"></a><span data-ttu-id="8e033-165">Środowisko ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8e033-165">ASP.NET Core environment</span></span>

<span data-ttu-id="8e033-166">Aby określić środowisko dla projektów ASP.NET Core, ustaw **env: ASPNETCORE_ENVIRONMENT** przed uruchomionymi poleceniami.</span><span class="sxs-lookup"><span data-stu-id="8e033-166">To specify the environment for ASP.NET Core projects, set **env:ASPNETCORE_ENVIRONMENT** before running commands.</span></span>

## <a name="common-parameters"></a><span data-ttu-id="8e033-167">Parametry wspólne</span><span class="sxs-lookup"><span data-stu-id="8e033-167">Common parameters</span></span>

<span data-ttu-id="8e033-168">W poniższej tabeli przedstawiono parametry wspólne dla wszystkich poleceń EF Core:</span><span class="sxs-lookup"><span data-stu-id="8e033-168">The following table shows parameters that are common to all of the EF Core commands:</span></span>

| <span data-ttu-id="8e033-169">Parametr</span><span class="sxs-lookup"><span data-stu-id="8e033-169">Parameter</span></span>                 | <span data-ttu-id="8e033-170">Opis</span><span class="sxs-lookup"><span data-stu-id="8e033-170">Description</span></span>                                                                                                                                                                                                          |
|:--------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="8e033-171">- \<> ciągu kontekstu</span><span class="sxs-lookup"><span data-stu-id="8e033-171">-Context \<String></span></span>        | <span data-ttu-id="8e033-172">`DbContext`Klasa, która ma zostać użyta.</span><span class="sxs-lookup"><span data-stu-id="8e033-172">The `DbContext` class to use.</span></span> <span data-ttu-id="8e033-173">Nazwa klasy lub w pełni kwalifikowana z przestrzeniami nazw.</span><span class="sxs-lookup"><span data-stu-id="8e033-173">Class name only or fully qualified with namespaces.</span></span>  <span data-ttu-id="8e033-174">Jeśli ten parametr zostanie pominięty, EF Core odnajdzie klasę kontekstową.</span><span class="sxs-lookup"><span data-stu-id="8e033-174">If this parameter is omitted, EF Core finds the context class.</span></span> <span data-ttu-id="8e033-175">Jeśli istnieje wiele klas kontekstu, ten parametr jest wymagany.</span><span class="sxs-lookup"><span data-stu-id="8e033-175">If there are multiple context classes, this parameter is required.</span></span> |
| <span data-ttu-id="8e033-176">- \<> ciągu projektu</span><span class="sxs-lookup"><span data-stu-id="8e033-176">-Project \<String></span></span>        | <span data-ttu-id="8e033-177">Projekt docelowy.</span><span class="sxs-lookup"><span data-stu-id="8e033-177">The target project.</span></span> <span data-ttu-id="8e033-178">Jeśli ten parametr zostanie pominięty, **domyślny projekt** **konsoli Menedżera pakietów** jest używany jako projekt docelowy.</span><span class="sxs-lookup"><span data-stu-id="8e033-178">If this parameter is omitted, the **Default project** for **Package Manager Console** is used as the target project.</span></span>                                                                             |
| <span data-ttu-id="8e033-179"><nobr>-StartupProject</nobr> \< Ciąg></span><span class="sxs-lookup"><span data-stu-id="8e033-179"><nobr>-StartupProject</nobr> \<String></span></span> | <span data-ttu-id="8e033-180">Projekt startowy.</span><span class="sxs-lookup"><span data-stu-id="8e033-180">The startup project.</span></span> <span data-ttu-id="8e033-181">Jeśli ten parametr zostanie pominięty, **projekt startowy** we **właściwościach rozwiązania** jest używany jako projekt docelowy.</span><span class="sxs-lookup"><span data-stu-id="8e033-181">If this parameter is omitted, the **Startup project** in **Solution properties** is used as the target project.</span></span>                                                                                 |
| <span data-ttu-id="8e033-182">-Verbose</span><span class="sxs-lookup"><span data-stu-id="8e033-182">-Verbose</span></span>                  | <span data-ttu-id="8e033-183">Pokaż pełne dane wyjściowe.</span><span class="sxs-lookup"><span data-stu-id="8e033-183">Show verbose output.</span></span>                                                                                                                                                                                                 |

<span data-ttu-id="8e033-184">Aby wyświetlić informacje pomocy dotyczące polecenia, należy użyć polecenia programu PowerShell `Get-Help` .</span><span class="sxs-lookup"><span data-stu-id="8e033-184">To show help information about a command, use PowerShell's `Get-Help` command.</span></span>

> [!TIP]
> <span data-ttu-id="8e033-185">Parametry Context, Project i StartupProject obsługują rozszerzanie kart.</span><span class="sxs-lookup"><span data-stu-id="8e033-185">The Context, Project, and StartupProject parameters support tab-expansion.</span></span>

## <a name="add-migration"></a><span data-ttu-id="8e033-186">Dodawanie-migracja</span><span class="sxs-lookup"><span data-stu-id="8e033-186">Add-Migration</span></span>

<span data-ttu-id="8e033-187">Dodaje nową migrację.</span><span class="sxs-lookup"><span data-stu-id="8e033-187">Adds a new migration.</span></span>

<span data-ttu-id="8e033-188">Parametry:</span><span class="sxs-lookup"><span data-stu-id="8e033-188">Parameters:</span></span>

| <span data-ttu-id="8e033-189">Parametr</span><span class="sxs-lookup"><span data-stu-id="8e033-189">Parameter</span></span>                         | <span data-ttu-id="8e033-190">Opis</span><span class="sxs-lookup"><span data-stu-id="8e033-190">Description</span></span>                                                                                                             |
|:----------------------------------|:------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="8e033-191"><nobr>-Nazwa \< ciąg><nobr></span><span class="sxs-lookup"><span data-stu-id="8e033-191"><nobr>-Name \<String><nobr></span></span>       | <span data-ttu-id="8e033-192">Nazwa migracji.</span><span class="sxs-lookup"><span data-stu-id="8e033-192">The name of the migration.</span></span> <span data-ttu-id="8e033-193">Jest to parametr pozycyjny i jest wymagany.</span><span class="sxs-lookup"><span data-stu-id="8e033-193">This is a positional parameter and is required.</span></span>                                              |
| <span data-ttu-id="8e033-194"><nobr>-OutputDir \< ciąg></nobr></span><span class="sxs-lookup"><span data-stu-id="8e033-194"><nobr>-OutputDir \<String></nobr></span></span> | <span data-ttu-id="8e033-195">Katalog używany do wyprowadzania plików.</span><span class="sxs-lookup"><span data-stu-id="8e033-195">The directory use to output the files.</span></span> <span data-ttu-id="8e033-196">Ścieżki są względne dla docelowego katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="8e033-196">Paths are relative to the target project directory.</span></span> <span data-ttu-id="8e033-197">Wartość domyślna to "migracje".</span><span class="sxs-lookup"><span data-stu-id="8e033-197">Defaults to "Migrations".</span></span> |
| <span data-ttu-id="8e033-198"><nobr>- \<>ciągu przestrzeni nazw</nobr></span><span class="sxs-lookup"><span data-stu-id="8e033-198"><nobr>-Namespace \<String></nobr></span></span> | <span data-ttu-id="8e033-199">Przestrzeń nazw, która ma być używana dla wygenerowanych klas.</span><span class="sxs-lookup"><span data-stu-id="8e033-199">The namespace to use for the generated classes.</span></span> <span data-ttu-id="8e033-200">Wartość domyślna jest generowana z katalogu wyjściowego.</span><span class="sxs-lookup"><span data-stu-id="8e033-200">Defaults to generated from the output directory.</span></span> |

## <a name="drop-database"></a><span data-ttu-id="8e033-201">Usuń bazę danych</span><span class="sxs-lookup"><span data-stu-id="8e033-201">Drop-Database</span></span>

<span data-ttu-id="8e033-202">Odrzuca bazę danych.</span><span class="sxs-lookup"><span data-stu-id="8e033-202">Drops the database.</span></span>

<span data-ttu-id="8e033-203">Parametry:</span><span class="sxs-lookup"><span data-stu-id="8e033-203">Parameters:</span></span>

| <span data-ttu-id="8e033-204">Parametr</span><span class="sxs-lookup"><span data-stu-id="8e033-204">Parameter</span></span> | <span data-ttu-id="8e033-205">Opis</span><span class="sxs-lookup"><span data-stu-id="8e033-205">Description</span></span>                                              |
|:----------|:---------------------------------------------------------|
| <span data-ttu-id="8e033-206">-WhatIf</span><span class="sxs-lookup"><span data-stu-id="8e033-206">-WhatIf</span></span>   | <span data-ttu-id="8e033-207">Pokazuje, która baza danych zostanie porzucona, ale nie Porzuć jej.</span><span class="sxs-lookup"><span data-stu-id="8e033-207">Show which database would be dropped, but don't drop it.</span></span> |

## <a name="get-dbcontext"></a><span data-ttu-id="8e033-208">Kontekst Get-</span><span class="sxs-lookup"><span data-stu-id="8e033-208">Get-DbContext</span></span>

<span data-ttu-id="8e033-209">Pobiera informacje o `DbContext` typie.</span><span class="sxs-lookup"><span data-stu-id="8e033-209">Gets information about a `DbContext` type.</span></span>

## <a name="remove-migration"></a><span data-ttu-id="8e033-210">Usuń migrację</span><span class="sxs-lookup"><span data-stu-id="8e033-210">Remove-Migration</span></span>

<span data-ttu-id="8e033-211">Usuwa ostatnią migrację (przywraca zmiany kodu, które zostały wykonane podczas migracji).</span><span class="sxs-lookup"><span data-stu-id="8e033-211">Removes the last migration (rolls back the code changes that were done for the migration).</span></span>

<span data-ttu-id="8e033-212">Parametry:</span><span class="sxs-lookup"><span data-stu-id="8e033-212">Parameters:</span></span>

| <span data-ttu-id="8e033-213">Parametr</span><span class="sxs-lookup"><span data-stu-id="8e033-213">Parameter</span></span> | <span data-ttu-id="8e033-214">Opis</span><span class="sxs-lookup"><span data-stu-id="8e033-214">Description</span></span>                                                                     |
|:----------|:--------------------------------------------------------------------------------|
| <span data-ttu-id="8e033-215">-Force</span><span class="sxs-lookup"><span data-stu-id="8e033-215">-Force</span></span>    | <span data-ttu-id="8e033-216">Przywróć migrację (Wycofaj zmiany, które zostały zastosowane do bazy danych).</span><span class="sxs-lookup"><span data-stu-id="8e033-216">Revert the migration (roll back the changes that were applied to the database).</span></span> |

## <a name="scaffold-dbcontext"></a><span data-ttu-id="8e033-217">Szkielet — DbContext</span><span class="sxs-lookup"><span data-stu-id="8e033-217">Scaffold-DbContext</span></span>

<span data-ttu-id="8e033-218">Generuje kod dla `DbContext` typów jednostek i dla bazy danych.</span><span class="sxs-lookup"><span data-stu-id="8e033-218">Generates code for a `DbContext` and entity types for a database.</span></span> <span data-ttu-id="8e033-219">`Scaffold-DbContext`Aby można było wygenerować typ jednostki, tabela bazy danych musi mieć klucz podstawowy.</span><span class="sxs-lookup"><span data-stu-id="8e033-219">In order for `Scaffold-DbContext` to generate an entity type, the database table must have a primary key.</span></span>

<span data-ttu-id="8e033-220">Parametry:</span><span class="sxs-lookup"><span data-stu-id="8e033-220">Parameters:</span></span>

| <span data-ttu-id="8e033-221">Parametr</span><span class="sxs-lookup"><span data-stu-id="8e033-221">Parameter</span></span>                          | <span data-ttu-id="8e033-222">Opis</span><span class="sxs-lookup"><span data-stu-id="8e033-222">Description</span></span>                                                                                                                                                                                                                                                             |
|:-----------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="8e033-223"><nobr>- \< Parametry połączenia></nobr></span><span class="sxs-lookup"><span data-stu-id="8e033-223"><nobr>-Connection \<String></nobr></span></span> | <span data-ttu-id="8e033-224">Parametry połączenia z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="8e033-224">The connection string to the database.</span></span> <span data-ttu-id="8e033-225">W przypadku projektów ASP.NET Core 2. x wartością może być \*nazwa = \< Nazwa>parametrów połączenia \*.</span><span class="sxs-lookup"><span data-stu-id="8e033-225">For ASP.NET Core 2.x projects, the value can be *name=\<name of connection string>*.</span></span> <span data-ttu-id="8e033-226">W takim przypadku nazwa pochodzi ze źródeł konfiguracji skonfigurowanych dla projektu.</span><span class="sxs-lookup"><span data-stu-id="8e033-226">In that case the name comes from the configuration sources that are set up for the project.</span></span> <span data-ttu-id="8e033-227">Jest to parametr pozycyjny i jest wymagany.</span><span class="sxs-lookup"><span data-stu-id="8e033-227">This is a positional parameter and is required.</span></span> |
| <span data-ttu-id="8e033-228"><nobr>- \<>ciągu dostawcy</nobr></span><span class="sxs-lookup"><span data-stu-id="8e033-228"><nobr>-Provider \<String></nobr></span></span>   | <span data-ttu-id="8e033-229">Dostawca do użycia.</span><span class="sxs-lookup"><span data-stu-id="8e033-229">The provider to use.</span></span> <span data-ttu-id="8e033-230">Zazwyczaj jest to nazwa pakietu NuGet, na przykład: `Microsoft.EntityFrameworkCore.SqlServer` .</span><span class="sxs-lookup"><span data-stu-id="8e033-230">Typically this is the name of the NuGet package, for example: `Microsoft.EntityFrameworkCore.SqlServer`.</span></span> <span data-ttu-id="8e033-231">Jest to parametr pozycyjny i jest wymagany.</span><span class="sxs-lookup"><span data-stu-id="8e033-231">This is a positional parameter and is required.</span></span>                                                                                           |
| <span data-ttu-id="8e033-232">-OutputDir \< ciąg></span><span class="sxs-lookup"><span data-stu-id="8e033-232">-OutputDir \<String></span></span>               | <span data-ttu-id="8e033-233">Katalog, w którym mają zostać umieszczone pliki.</span><span class="sxs-lookup"><span data-stu-id="8e033-233">The directory to put files in.</span></span> <span data-ttu-id="8e033-234">Ścieżki są względne dla katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="8e033-234">Paths are relative to the project directory.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="8e033-235">-ContextDir \< ciąg></span><span class="sxs-lookup"><span data-stu-id="8e033-235">-ContextDir \<String></span></span>              | <span data-ttu-id="8e033-236">Katalog, w którym ma zostać umieszczony `DbContext` plik.</span><span class="sxs-lookup"><span data-stu-id="8e033-236">The directory to put the `DbContext` file in.</span></span> <span data-ttu-id="8e033-237">Ścieżki są względne dla katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="8e033-237">Paths are relative to the project directory.</span></span>                                                                                                                                                               |
| <span data-ttu-id="8e033-238">- \<> ciągu przestrzeni nazw</span><span class="sxs-lookup"><span data-stu-id="8e033-238">-Namespace \<String></span></span>               | <span data-ttu-id="8e033-239">Przestrzeń nazw, która ma być używana dla wszystkich wygenerowanych klas.</span><span class="sxs-lookup"><span data-stu-id="8e033-239">The namespace to use for all generated classes.</span></span> <span data-ttu-id="8e033-240">Wartość domyślna jest generowana z głównej przestrzeni nazw i katalogu wyjściowego.</span><span class="sxs-lookup"><span data-stu-id="8e033-240">Defaults to generated from the root namespace and the output directory.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="8e033-241">-Atrybut contextnamespace \< ciąg></span><span class="sxs-lookup"><span data-stu-id="8e033-241">-ContextNamespace \<String></span></span>        | <span data-ttu-id="8e033-242">Przestrzeń nazw, która ma być używana dla wygenerowanej `DbContext` klasy.</span><span class="sxs-lookup"><span data-stu-id="8e033-242">The namespace to use for the generated `DbContext` class.</span></span> <span data-ttu-id="8e033-243">Uwaga: zastąpienia `-Namespace` .</span><span class="sxs-lookup"><span data-stu-id="8e033-243">Note: overrides `-Namespace`.</span></span>                                                                                                                                                                              |
| <span data-ttu-id="8e033-244">- \<> ciągu kontekstu</span><span class="sxs-lookup"><span data-stu-id="8e033-244">-Context \<String></span></span>                 | <span data-ttu-id="8e033-245">Nazwa `DbContext` klasy do wygenerowania.</span><span class="sxs-lookup"><span data-stu-id="8e033-245">The name of the `DbContext` class to generate.</span></span>                                                                                                                                                                                                                          |
| <span data-ttu-id="8e033-246">-Schemat \< ciąg [] ></span><span class="sxs-lookup"><span data-stu-id="8e033-246">-Schemas \<String[]></span></span>               | <span data-ttu-id="8e033-247">Schematy tabel, dla których mają zostać wygenerowane typy jednostek.</span><span class="sxs-lookup"><span data-stu-id="8e033-247">The schemas of tables to generate entity types for.</span></span> <span data-ttu-id="8e033-248">Jeśli ten parametr zostanie pominięty, zostaną uwzględnione wszystkie schematy.</span><span class="sxs-lookup"><span data-stu-id="8e033-248">If this parameter is omitted, all schemas are included.</span></span>                                                                                                                                                             |
| <span data-ttu-id="8e033-249">— \< Ciąg tabel [] ></span><span class="sxs-lookup"><span data-stu-id="8e033-249">-Tables \<String[]></span></span>                | <span data-ttu-id="8e033-250">Tabele, dla których mają zostać wygenerowane typy jednostek.</span><span class="sxs-lookup"><span data-stu-id="8e033-250">The tables to generate entity types for.</span></span> <span data-ttu-id="8e033-251">Jeśli ten parametr zostanie pominięty, zostaną uwzględnione wszystkie tabele.</span><span class="sxs-lookup"><span data-stu-id="8e033-251">If this parameter is omitted, all tables are included.</span></span>                                                                                                                                                                         |
| <span data-ttu-id="8e033-252">— Adnotacje</span><span class="sxs-lookup"><span data-stu-id="8e033-252">-DataAnnotations</span></span>                   | <span data-ttu-id="8e033-253">Użyj atrybutów, aby skonfigurować model (tam, gdzie to możliwe).</span><span class="sxs-lookup"><span data-stu-id="8e033-253">Use attributes to configure the model (where possible).</span></span> <span data-ttu-id="8e033-254">Jeśli ten parametr zostanie pominięty, zostanie użyty tylko interfejs API Fluent.</span><span class="sxs-lookup"><span data-stu-id="8e033-254">If this parameter is omitted, only the fluent API is used.</span></span>                                                                                                                                                      |
| <span data-ttu-id="8e033-255">-UseDatabaseNames</span><span class="sxs-lookup"><span data-stu-id="8e033-255">-UseDatabaseNames</span></span>                  | <span data-ttu-id="8e033-256">Nazwy tabel i kolumn należy używać dokładnie tak, jak pojawiają się one w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="8e033-256">Use table and column names exactly as they appear in the database.</span></span> <span data-ttu-id="8e033-257">Jeśli ten parametr zostanie pominięty, nazwy baz danych są zmieniane na bardziej ściśle zgodne z konwencjami stylu nazwy języka C#.</span><span class="sxs-lookup"><span data-stu-id="8e033-257">If this parameter is omitted, database names are changed to more closely conform to C# name style conventions.</span></span>                                                                                       |
| <span data-ttu-id="8e033-258">-Force</span><span class="sxs-lookup"><span data-stu-id="8e033-258">-Force</span></span>                             | <span data-ttu-id="8e033-259">Zastąp istniejące pliki.</span><span class="sxs-lookup"><span data-stu-id="8e033-259">Overwrite existing files.</span></span>                                                                                                                                                                                                                                               |

<span data-ttu-id="8e033-260">Przykład:</span><span class="sxs-lookup"><span data-stu-id="8e033-260">Example:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

<span data-ttu-id="8e033-261">Przykład, który szkieletuje tylko wybrane tabele i tworzy kontekst w osobnym folderze z określoną nazwą i przestrzenią nazw:</span><span class="sxs-lookup"><span data-stu-id="8e033-261">Example that scaffolds only selected tables and creates the context in a separate folder with a specified name and namespace:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Tables "Blog","Post" -ContextDir Context -Context BlogContext -ContextNamespace New.Namespace
```

## <a name="script-migration"></a><span data-ttu-id="8e033-262">Skrypt — migracja</span><span class="sxs-lookup"><span data-stu-id="8e033-262">Script-Migration</span></span>

<span data-ttu-id="8e033-263">Generuje skrypt SQL, który stosuje wszystkie zmiany z jednej wybranej migracji do innej wybranej migracji.</span><span class="sxs-lookup"><span data-stu-id="8e033-263">Generates a SQL script that applies all of the changes from one selected migration to another selected migration.</span></span>

<span data-ttu-id="8e033-264">Parametry:</span><span class="sxs-lookup"><span data-stu-id="8e033-264">Parameters:</span></span>

| <span data-ttu-id="8e033-265">Parametr</span><span class="sxs-lookup"><span data-stu-id="8e033-265">Parameter</span></span>                | <span data-ttu-id="8e033-266">Opis</span><span class="sxs-lookup"><span data-stu-id="8e033-266">Description</span></span>                                                                                                                                                                                                                |
|:-------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="8e033-267">*-Z* \< Ciąg></span><span class="sxs-lookup"><span data-stu-id="8e033-267">*-From* \<String></span></span>        | <span data-ttu-id="8e033-268">Rozpoczynanie migracji.</span><span class="sxs-lookup"><span data-stu-id="8e033-268">The starting migration.</span></span> <span data-ttu-id="8e033-269">Migracje mogą być identyfikowane według nazwy lub identyfikatora.</span><span class="sxs-lookup"><span data-stu-id="8e033-269">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="8e033-270">Liczba 0 to specjalny przypadek, który oznacza *przed pierwszą migracją*.</span><span class="sxs-lookup"><span data-stu-id="8e033-270">The number 0 is a special case that means *before the first migration*.</span></span> <span data-ttu-id="8e033-271">Wartość domyślna to 0.</span><span class="sxs-lookup"><span data-stu-id="8e033-271">Defaults to 0.</span></span>                                                              |
| <span data-ttu-id="8e033-272">*-Do* \< Ciąg></span><span class="sxs-lookup"><span data-stu-id="8e033-272">*-To* \<String></span></span>          | <span data-ttu-id="8e033-273">Kończenie migracji.</span><span class="sxs-lookup"><span data-stu-id="8e033-273">The ending migration.</span></span> <span data-ttu-id="8e033-274">Wartość domyślna to Ostatnia migracja.</span><span class="sxs-lookup"><span data-stu-id="8e033-274">Defaults to the last migration.</span></span>                                                                                                                                                                      |
| <span data-ttu-id="8e033-275"><nobr>-Idempotentne</nobr></span><span class="sxs-lookup"><span data-stu-id="8e033-275"><nobr>-Idempotent</nobr></span></span> | <span data-ttu-id="8e033-276">Generuj skrypt, którego można użyć w bazie danych w dowolnej migracji.</span><span class="sxs-lookup"><span data-stu-id="8e033-276">Generate a script that can be used on a database at any migration.</span></span>                                                                                                                                                         |
| <span data-ttu-id="8e033-277">- \< Ciąg wyjściowy></span><span class="sxs-lookup"><span data-stu-id="8e033-277">-Output \<String></span></span>        | <span data-ttu-id="8e033-278">Plik, w którym ma zostać zapisany wynik.</span><span class="sxs-lookup"><span data-stu-id="8e033-278">The file to write the result to.</span></span> <span data-ttu-id="8e033-279">Jeśli ten parametr zostanie pominięty, plik zostanie utworzony przy użyciu wygenerowanej nazwy w tym samym folderze, w którym są tworzone pliki środowiska uruchomieniowego aplikacji, na przykład: */obj/Debug/netcoreapp2.1/ghbkztfz.SQL/*.</span><span class="sxs-lookup"><span data-stu-id="8e033-279">IF this parameter is omitted, the file is created with a generated name in the same folder as the app's runtime files are created, for example: */obj/Debug/netcoreapp2.1/ghbkztfz.sql/*.</span></span> |

> [!TIP]
> <span data-ttu-id="8e033-280">Parametry do, od i Output obsługują rozszerzanie tabulacji.</span><span class="sxs-lookup"><span data-stu-id="8e033-280">The To, From, and Output parameters support tab-expansion.</span></span>

<span data-ttu-id="8e033-281">Poniższy przykład tworzy skrypt migracji InitialCreate przy użyciu nazwy migracji.</span><span class="sxs-lookup"><span data-stu-id="8e033-281">The following example creates a script for the InitialCreate migration, using the migration name.</span></span>

```powershell
Script-Migration -To InitialCreate
```

<span data-ttu-id="8e033-282">Poniższy przykład tworzy skrypt dla wszystkich migracji po migracji InitialCreate przy użyciu identyfikatora migracji.</span><span class="sxs-lookup"><span data-stu-id="8e033-282">The following example creates a script for all migrations after the InitialCreate migration, using the migration ID.</span></span>

```powershell
Script-Migration -From 20180904195021_InitialCreate
```

## <a name="update-database"></a><span data-ttu-id="8e033-283">Update-Database</span><span class="sxs-lookup"><span data-stu-id="8e033-283">Update-Database</span></span>

<span data-ttu-id="8e033-284">Aktualizuje bazę danych do ostatniej migracji lub do określonej migracji.</span><span class="sxs-lookup"><span data-stu-id="8e033-284">Updates the database to the last migration or to a specified migration.</span></span>

| <span data-ttu-id="8e033-285">Parametr</span><span class="sxs-lookup"><span data-stu-id="8e033-285">Parameter</span></span>                           | <span data-ttu-id="8e033-286">Opis</span><span class="sxs-lookup"><span data-stu-id="8e033-286">Description</span></span>                                                                                                                                                                                                                                                     |
|:------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="8e033-287"><nobr>*— Migracja* \< Ciąg></nobr></span><span class="sxs-lookup"><span data-stu-id="8e033-287"><nobr>*-Migration* \<String></nobr></span></span> | <span data-ttu-id="8e033-288">Migracja docelowa.</span><span class="sxs-lookup"><span data-stu-id="8e033-288">The target migration.</span></span> <span data-ttu-id="8e033-289">Migracje mogą być identyfikowane według nazwy lub identyfikatora.</span><span class="sxs-lookup"><span data-stu-id="8e033-289">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="8e033-290">Liczba 0 jest szczególnym przypadkiem *przed pierwszą migracją* i powoduje przywrócenie wszystkich migracji.</span><span class="sxs-lookup"><span data-stu-id="8e033-290">The number 0 is a special case that means *before the first migration* and causes all migrations to be reverted.</span></span> <span data-ttu-id="8e033-291">Jeśli migracja nie zostanie określona, polecenie domyślnie przestanie być ostatnią migracją.</span><span class="sxs-lookup"><span data-stu-id="8e033-291">If no migration is specified, the command defaults to the last migration.</span></span> |
| <span data-ttu-id="8e033-292"><nobr>- \< Parametry połączenia></nobr></span><span class="sxs-lookup"><span data-stu-id="8e033-292"><nobr>-Connection \<String></nobr></span></span>  | <span data-ttu-id="8e033-293">Parametry połączenia z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="8e033-293">The connection string to the database.</span></span> <span data-ttu-id="8e033-294">Wartość domyślna to określona w `AddDbContext` lub `OnConfiguring` .</span><span class="sxs-lookup"><span data-stu-id="8e033-294">Defaults to the one specified in `AddDbContext` or `OnConfiguring`.</span></span> |

> [!TIP]
> <span data-ttu-id="8e033-295">Parametr migracji obsługuje rozszerzanie tabulacji.</span><span class="sxs-lookup"><span data-stu-id="8e033-295">The Migration parameter supports tab-expansion.</span></span>

<span data-ttu-id="8e033-296">Poniższy przykład przywraca wszystkie migracje.</span><span class="sxs-lookup"><span data-stu-id="8e033-296">The following example reverts all migrations.</span></span>

```powershell
Update-Database -Migration 0
```

<span data-ttu-id="8e033-297">Poniższe przykłady umożliwiają zaktualizowanie bazy danych do określonej migracji.</span><span class="sxs-lookup"><span data-stu-id="8e033-297">The following examples update the database to a specified migration.</span></span> <span data-ttu-id="8e033-298">Pierwsza z nich używa nazwy migracji, a drugi używa identyfikatora migracji i określonego połączenia:</span><span class="sxs-lookup"><span data-stu-id="8e033-298">The first uses the migration name and the second uses the migration ID and a specified connection:</span></span>

```powershell
Update-Database -Migration InitialCreate
Update-Database -Migration 20180904195021_InitialCreate -Connection your_connection_string
```

## <a name="additional-resources"></a><span data-ttu-id="8e033-299">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="8e033-299">Additional resources</span></span>

* [<span data-ttu-id="8e033-300">Migracje</span><span class="sxs-lookup"><span data-stu-id="8e033-300">Migrations</span></span>](xref:core/managing-schemas/migrations/index)
* [<span data-ttu-id="8e033-301">Odwrócenie inżynierii</span><span class="sxs-lookup"><span data-stu-id="8e033-301">Reverse Engineering</span></span>](xref:core/managing-schemas/scaffolding)
