---
title: Zwiększanie wydajności uruchamiania za pomocą narzędzia NGen — EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: dc6110a0-80a0-4370-8190-cea942841cee
caps.latest.revision: 4
ms.openlocfilehash: cffd2deea3148a16ed704d1e5e7b365eda06f72b
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37949049"
---
# <a name="improving-startup-performance-with-ngen"></a><span data-ttu-id="9204e-102">Zwiększanie wydajności uruchamiania za pomocą narzędzia NGen</span><span class="sxs-lookup"><span data-stu-id="9204e-102">Improving startup performance with NGen</span></span>
> [!NOTE]
> <span data-ttu-id="9204e-103">**EF6 począwszy tylko** — funkcje, interfejsów API itp. z opisem na tej stronie zostały wprowadzone w programie Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="9204e-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="9204e-104">Jeśli używasz starszej wersji, niektóre lub wszystkie informacje, nie ma zastosowania.</span><span class="sxs-lookup"><span data-stu-id="9204e-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="9204e-105">Program .NET Framework obsługuje generowanie obrazów natywnych dla zarządzanych aplikacji i bibliotek, aby ułatwić szybsze uruchamianie aplikacji, a także w niektórych przypadkach Użyj mniejszej ilości pamięci.</span><span class="sxs-lookup"><span data-stu-id="9204e-105">The .NET Framework supports the generation of native images for managed applications and libraries as a way to help applications start faster and also in some cases use less memory.</span></span> <span data-ttu-id="9204e-106">Obrazy natywne są tworzone przez tłumaczenie zestawów kodu zarządzanego w plikach zawierających instrukcji maszyny macierzystej, zanim aplikacja zostanie wykonany, zwalniania kompilatora .NET JIT (Just-In-Time), trzeba wygenerować macierzysty zgodnie z instrukcjami na środowisko uruchomieniowe aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9204e-106">Native images are created by translating managed code assemblies into files containing native machine instructions before the application is executed, relieving the .NET JIT (Just-In-Time) compiler from having to generate the native instructions at application runtime.</span></span>  

<span data-ttu-id="9204e-107">Przed wersją 6 środowiska uruchomieniowego EF core biblioteki były częścią programu .NET Framework i obrazy natywne są generowane automatycznie dla nich.</span><span class="sxs-lookup"><span data-stu-id="9204e-107">Prior to version 6, the EF runtime’s core libraries were part of the .NET Framework and native images were generated automatically for them.</span></span> <span data-ttu-id="9204e-108">Począwszy od wersji 6 całego środowiska uruchomieniowego EF została połączona w pakiet NuGet platformy EntityFramework.</span><span class="sxs-lookup"><span data-stu-id="9204e-108">Starting with version 6 the whole EF runtime has been combined into the EntityFramework NuGet package.</span></span> <span data-ttu-id="9204e-109">Obrazy natywne mają do chwili obecnej można wygenerować za pomocą narzędzia wiersza polecenia NGen.exe, aby uzyskać wyniki podobne.</span><span class="sxs-lookup"><span data-stu-id="9204e-109">Native images have to now be generated using the NGen.exe command line tool to obtain similar results.</span></span>  

<span data-ttu-id="9204e-110">Empiryczne obserwacje pokazują, że obrazy natywne zestawów środowiska uruchomieniowego EF można wyciąć od 1 do 3 sekund czas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9204e-110">Empirical observations show that native images of the EF runtime assemblies can cut between 1 and 3 seconds of application startup time.</span></span>  

## <a name="how-to-use-ngenexe"></a><span data-ttu-id="9204e-111">Jak używać NGen.exe</span><span class="sxs-lookup"><span data-stu-id="9204e-111">How to use NGen.exe</span></span>  

<span data-ttu-id="9204e-112">Najbardziej podstawowa funkcja narzędzia NGen.exe jest "Zainstaluj" (oznacza to, aby utworzyć i zachować na dysku) obrazów natywnych dla zestawu i wszystkich jego zależności bezpośrednich.</span><span class="sxs-lookup"><span data-stu-id="9204e-112">The most basic function of the NGen.exe tool is to “install” (that is, to create and persist to disk) native images for an assembly and all its direct dependencies.</span></span> <span data-ttu-id="9204e-113">Poniżej przedstawiono, jak można uzyskać, który:</span><span class="sxs-lookup"><span data-stu-id="9204e-113">Here is how you can achieve that:</span></span>  

1. <span data-ttu-id="9204e-114">Otwórz okno wiersza polecenia jako administrator</span><span class="sxs-lookup"><span data-stu-id="9204e-114">Open a Command Prompt window as an administrator</span></span>  
2. <span data-ttu-id="9204e-115">Zmień bieżący katalog roboczy do lokalizacji zestawów, który chcesz wygenerować obrazów natywnych dla:</span><span class="sxs-lookup"><span data-stu-id="9204e-115">Change the current working directory to the location of the assemblies you want to generate native images for:</span></span>  

  ``` console
    cd <*Assemblies location*>  
  ```
3. <span data-ttu-id="9204e-116">W zależności od używanego systemu operacyjnego i konfiguracji aplikacji może być konieczne generuje obrazy natywne dla architektury 32-bitowej i 64-bitowa architektura lub obu.</span><span class="sxs-lookup"><span data-stu-id="9204e-116">Depending on your operating system and the application’s configuration you might need to generate native images for 32 bit architecture, 64 bit architecture or for both.</span></span>  

    <span data-ttu-id="9204e-117">Dla 32-bitowych, uruchom:</span><span class="sxs-lookup"><span data-stu-id="9204e-117">For 32 bit run:</span></span>  
  ``` console
    %WINDIR%\Microsoft.NET\Framework\v4.0.30319\ngen install <Assembly name>  
  ```
    <span data-ttu-id="9204e-118">Dla 64-bitowy, uruchom:</span><span class="sxs-lookup"><span data-stu-id="9204e-118">For 64 bit run:</span></span>
  ``` console
    %WINDIR%\Microsoft.NET\Framework64\v4.0.30319\ngen install <Assembly name>  
  ```

> [!TIP]
> <span data-ttu-id="9204e-119">Generowanie obrazów macierzystych dla niewłaściwego architektury jest bardzo Częsty błąd.</span><span class="sxs-lookup"><span data-stu-id="9204e-119">Generating native images for the wrong architecture is a very common mistake.</span></span> <span data-ttu-id="9204e-120">W razie wątpliwości można po prostu generowanie obrazów natywnych dla wszystkich architektur, które dotyczą system operacyjny zainstalowany na maszynie.</span><span class="sxs-lookup"><span data-stu-id="9204e-120">When in doubt you can simply generate native images for all the architectures that apply to the operating system installed in the machine.</span></span>  

<span data-ttu-id="9204e-121">NGen.exe obsługuje także inne funkcje, takie jak odinstalowywanie i wyświetlanie zainstalowanych obrazów natywnych, kolejkowania generowania wielu obrazów itd. Aby uzyskać więcej szczegółów dotyczących użycia przeczytaj [dokumentacji NGen.exe](https://msdn.microsoft.com/library/6t9t5wcf.aspx).</span><span class="sxs-lookup"><span data-stu-id="9204e-121">NGen.exe also supports other functions such as uninstalling and displaying the installed native images, queuing the generation of multiple images, etc. For more details of usage read the [NGen.exe documentation](https://msdn.microsoft.com/library/6t9t5wcf.aspx).</span></span>  

## <a name="when-to-use-ngenexe"></a><span data-ttu-id="9204e-122">Kiedy należy używać NGen.exe</span><span class="sxs-lookup"><span data-stu-id="9204e-122">When to use NGen.exe</span></span>  

<span data-ttu-id="9204e-123">Jeśli chodzi o określić zestawy, które generuje obrazy natywne dla w aplikacji opartej na EF w wersji 6 lub nowszego, należy rozważyć następujące opcje:</span><span class="sxs-lookup"><span data-stu-id="9204e-123">When it comes to decide which assemblies to generate native images for in an application based on EF version 6 or greater, you should consider the following options:</span></span>  

- <span data-ttu-id="9204e-124">**Głównym zestawie środowiska uruchomieniowego EF, EntityFramework.dll**: Typowa aplikacja na podstawie EF jest wykonywany znacząca ilość kodu z tego zestawu podczas uruchamiania lub w jego pierwszego dostępu do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="9204e-124">**The main EF runtime assembly, EntityFramework.dll**: A typical EF based application executes a significant amount of code from this assembly on startup or on its first access to the database.</span></span> <span data-ttu-id="9204e-125">W związku z tym tworzenie obrazów natywnych tego zestawu powoduje wygenerowanie największy wzrost wydajności uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="9204e-125">Consequently, creating native images of this assembly will produce the biggest gains in startup performance.</span></span>  
- <span data-ttu-id="9204e-126">**Każdy zespół dostawcy EF używanych przez aplikację**: czas uruchamiania mogą również zyskać nieco dzięki generowanie obrazów macierzystych, które z nich.</span><span class="sxs-lookup"><span data-stu-id="9204e-126">**Any EF provider assembly used by your application**: Startup time can also benefit slightly from generating native images of these.</span></span> <span data-ttu-id="9204e-127">Na przykład jeśli aplikacja używa dostawcy EF dla programu SQL Server należy wygenerować obraz natywny dla EntityFramework.SqlServer.dll.</span><span class="sxs-lookup"><span data-stu-id="9204e-127">For example, if the application uses the EF provider for SQL Server you will want to generate a native image for EntityFramework.SqlServer.dll.</span></span>  
- <span data-ttu-id="9204e-128">**Zestawy aplikacji i inne zależności**: [dokumentacji NGen.exe](https://msdn.microsoft.com/library/6t9t5wcf.aspx) opisano ogólne kryteria wyboru zestawy, które można wygenerować obrazów natywnych dla i wpływu na obrazy natywne dotyczący zabezpieczeń, Zaawansowane opcje, takie jak "trwałego powiązania" scenariuszy, takich jak przy użyciu obrazów natywnych w debugowania i profilowania w scenariuszach itp.</span><span class="sxs-lookup"><span data-stu-id="9204e-128">**Your application’s assemblies and other dependencies**: The [NGen.exe documentation](https://msdn.microsoft.com/library/6t9t5wcf.aspx) covers general criteria for choosing which assemblies to generate native images for and the impact of native images on security, advanced options such as “hard binding”, scenarios such as using native images in debugging and profiling scenarios, etc.</span></span>  

> [!TIP]
> <span data-ttu-id="9204e-129">Upewnij się, starannie mierzenie wpływu na wydajność uruchamiania i ogólną wydajność aplikacji przy użyciu obrazów macierzystych i porównać ich rzeczywiste wymagania.</span><span class="sxs-lookup"><span data-stu-id="9204e-129">Make sure you carefully measure the impact of using native images on both the startup performance and the overall performance of your application and compare them against actual requirements.</span></span> <span data-ttu-id="9204e-130">Natomiast obrazy natywne pomoże ogólnie poprawy uruchomienia wydajność i w niektórych przypadkach zmniejszenie zużycia pamięci, nie wszystkie scenariusze skorzystają równomiernie.</span><span class="sxs-lookup"><span data-stu-id="9204e-130">While native images will generally help improve startup up performance and in some cases reduce memory usage, not all scenarios will benefit equally.</span></span> <span data-ttu-id="9204e-131">Na przykład w stanie stabilności wykonywania (gdy co najmniej raz wywołania wszystkich metod, które są używane przez aplikację) kod wygenerowany przez kompilator JIT może w rzeczywistości przynieść wydajność nieco lepiej niż obrazy natywne.</span><span class="sxs-lookup"><span data-stu-id="9204e-131">For instance, on steady state execution (that is, once all the methods being used by the application have been invoked at least once) code generated by the JIT compiler can in fact yield slightly better performance than native images.</span></span>  

## <a name="using-ngenexe-in-a-development-machine"></a><span data-ttu-id="9204e-132">Za pomocą NGen.exe w komputerze deweloperskim</span><span class="sxs-lookup"><span data-stu-id="9204e-132">Using NGen.exe in a development machine</span></span>  

<span data-ttu-id="9204e-133">Podczas tworzenia .NET JIT kompilatora oferuje najlepsze rozwiązanie ogólne dla kodu, które zmieniają się często.</span><span class="sxs-lookup"><span data-stu-id="9204e-133">During development the .NET JIT compiler will offer the best overall tradeoff for code that is changing frequently.</span></span> <span data-ttu-id="9204e-134">Generowanie obrazów macierzystych skompilowanych zależności, takich jak zestawy środowiska uruchomieniowego EF może pomóc przyspieszyć programowanie i testowanie przez wycinanie kilka sekund na początku każdego wykonania.</span><span class="sxs-lookup"><span data-stu-id="9204e-134">Generating native images for compiled dependencies such as the EF runtime assemblies can help accelerate development and testing by cutting a few seconds out at the beginning of each execution.</span></span>  

<span data-ttu-id="9204e-135">Dobrym miejscem do śledzenia Znajdź zestawy środowiska uruchomieniowego EF jest lokalizacja pakietu NuGet dla rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="9204e-135">A good place to find the EF runtime assemblies is the NuGet package location for the solution.</span></span> <span data-ttu-id="9204e-136">Na przykład w aplikacji przy użyciu programu EF 6.0.2 z programem SQL Server i przeznaczonych dla platformy .NET 4.5 lub nowszej w oknie wiersza polecenia można wpisać następujące (Pamiętaj otworzyć go jako administrator):</span><span class="sxs-lookup"><span data-stu-id="9204e-136">For example, for an application using EF 6.0.2 with SQL Server and targeting .NET 4.5 or greater you can type the following in a Command Prompt window (remember to open it as an administrator):</span></span>  

``` console
cd <Solution directory>\packages\EntityFramework.6.0.2\lib\net45
%WINDIR%\Microsoft.NET\Framework\v4.0.30319\ngen install EntityFramework.SqlServer.dll
%WINDIR%\Microsoft.NET\Framework64\v4.0.30319\ngen install EntityFramework.SqlServer.dll
```  

> [!NOTE]
> <span data-ttu-id="9204e-137">To wykorzystuje fakt, że instalowanie obrazów natywnych dla platformy EF dostawcy dla programu SQL Server zostanie również domyślnie zainstalowany obrazy natywne dla zestawu głównego środowiska uruchomieniowego EF.</span><span class="sxs-lookup"><span data-stu-id="9204e-137">This takes advantage of the fact that installing the native images for EF provider for SQL Server will also by default install the native images for the main EF runtime assembly.</span></span> <span data-ttu-id="9204e-138">To działa, ponieważ NGen.exe może wykryć, czy EntityFramework.dll jest bezpośrednia zależność zestawu EntityFramework.SqlServer.dll, znajdującego się w tym samym katalogu.</span><span class="sxs-lookup"><span data-stu-id="9204e-138">This works because NGen.exe can detect that EntityFramework.dll is a direct dependency of the EntityFramework.SqlServer.dll assembly located in the same directory.</span></span>  

## <a name="creating-native-images-during-setup"></a><span data-ttu-id="9204e-139">Tworzenie obrazów natywnych podczas instalacji</span><span class="sxs-lookup"><span data-stu-id="9204e-139">Creating native images during setup</span></span>  

<span data-ttu-id="9204e-140">Zestaw narzędzi WiX obsługuje kolejkowania generowanie obrazów natywnych dla zestawów zarządzanych podczas instalacji, jak wyjaśniono w tym [poradnik](http://wixtoolset.org/documentation/manual/v3/howtos/files_and_registry/ngen_managed_assemblies.html).</span><span class="sxs-lookup"><span data-stu-id="9204e-140">The WiX Toolkit supports queuing the generation of native images for managed assemblies during setup, as explained in this [how-to guide](http://wixtoolset.org/documentation/manual/v3/howtos/files_and_registry/ngen_managed_assemblies.html).</span></span> <span data-ttu-id="9204e-141">Kolejny alternatywny sposób polega na utworzeniu zadania instalacji niestandardowej, wykonaj polecenie NGen.exe.</span><span class="sxs-lookup"><span data-stu-id="9204e-141">Another alternative is to create a custom setup task that execute the NGen.exe command.</span></span>  

## <a name="verifying-that-native-images-are-being-used-for-ef"></a><span data-ttu-id="9204e-142">Weryfikowanie, czy obrazy natywne są używane na platformie EF</span><span class="sxs-lookup"><span data-stu-id="9204e-142">Verifying that native images are being used for EF</span></span>  

<span data-ttu-id="9204e-143">Można sprawdzić, czy określona aplikacja używa natywny zestaw, wyszukując załadowanych zestawów, które mają rozszerzenie ". ni.dll"or". ni.exe".</span><span class="sxs-lookup"><span data-stu-id="9204e-143">You can verify that a specific application is using a native assembly by looking for loaded assemblies that have the extension “.ni.dll” or “.ni.exe”.</span></span> <span data-ttu-id="9204e-144">Na przykład obraz natywny dla zestawu głównego środowiska uruchomieniowego EF zostanie wywołana EntityFramework.ni.dll.</span><span class="sxs-lookup"><span data-stu-id="9204e-144">For example, a native image for the EF’s main runtime assembly will be called EntityFramework.ni.dll.</span></span> <span data-ttu-id="9204e-145">Prosty sposób sprawdzić załadowanych zestawów .NET procesu jest użycie [Eksplorator procesów](https://technet.microsoft.com/sysinternals/bb896653).</span><span class="sxs-lookup"><span data-stu-id="9204e-145">An easy way to inspect the loaded .NET assemblies of a process is to use [Process Explorer](https://technet.microsoft.com/sysinternals/bb896653).</span></span>  

## <a name="other-things-to-be-aware-of"></a><span data-ttu-id="9204e-146">Inne zagadnienia, które należy pamiętać o</span><span class="sxs-lookup"><span data-stu-id="9204e-146">Other things to be aware of</span></span>  

<span data-ttu-id="9204e-147">**Tworzenie obrazu natywnego zestawu, nie należy mylić z rejestrowaniem zestawu w [globalnej pamięci podręcznej zestawów (Global Assembly Cache)](https://msdn.microsoft.com/library/yf1d93sz.aspx)**.</span><span class="sxs-lookup"><span data-stu-id="9204e-147">**Creating a native image of an assembly should not be confused with registering the assembly in the [GAC (Global Assembly Cache)](https://msdn.microsoft.com/library/yf1d93sz.aspx)**.</span></span> <span data-ttu-id="9204e-148">NGen.exe umożliwia tworzenie obrazów zestawów, które nie znajdują się w pamięci podręcznej GAC, a w rzeczywistości wielu zastosowań, które korzystały z określonej wersji EF mogą udostępniać tego samego obrazu natywnego.</span><span class="sxs-lookup"><span data-stu-id="9204e-148">NGen.exe allows creating images of assemblies that are not in the GAC, and in fact, multiple applications that use a specific version of EF can share the same native image.</span></span> <span data-ttu-id="9204e-149">Windows 8 może automatycznie tworzyć obrazy natywne dla zestawów umieszczane w pamięci podręcznej GAC, środowiska uruchomieniowego EF jest zoptymalizowany do wdrożenia wraz z aplikacji i nie jest zalecane rejestrowanie w pamięci podręcznej GAC, ponieważ ma negatywny wpływ na rozpoznawania zestawu i Obsługa aplikacji wśród innych aspektów.</span><span class="sxs-lookup"><span data-stu-id="9204e-149">While Windows 8 can automatically create native images for assemblies placed in the GAC, the EF runtime is optimized to be deployed alongside your application and we do not recommend registering it in the GAC as this has a negative impact on assembly resolution and servicing your applications among other aspects.</span></span>  