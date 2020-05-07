# <a name="contributing-to-the-entity-framework-documentation"></a><span data-ttu-id="e4c72-101">Współtworzenie dokumentacji Entity Framework</span><span class="sxs-lookup"><span data-stu-id="e4c72-101">Contributing to the Entity Framework documentation</span></span>

<span data-ttu-id="e4c72-102">Poniżej opisano proces tworzenia artykułów i przykładów kodu do dokumentacji Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="e4c72-102">The process of contributing articles and code samples to the Entity Framework documentation is explained below.</span></span> <span data-ttu-id="e4c72-103">Wkłady mogą być bardzo proste jako poprawki literówki lub złożone jako nowe artykuły.</span><span class="sxs-lookup"><span data-stu-id="e4c72-103">Contributions can be as simple as typo corrections or as complex as new articles.</span></span>

## <a name="how-to-make-a-simple-correction-or-suggestion"></a><span data-ttu-id="e4c72-104">Jak wykonać prostą korektę lub sugestię</span><span class="sxs-lookup"><span data-stu-id="e4c72-104">How to make a simple correction or suggestion</span></span>

<span data-ttu-id="e4c72-105">Artykuły są przechowywane jako pliki promocji w tym repozytorium.</span><span class="sxs-lookup"><span data-stu-id="e4c72-105">Articles are stored as Markdown files in this repository.</span></span> <span data-ttu-id="e4c72-106">Aby wprowadzić prostą zmianę zawartości pliku z promocji, kliknij link **Edytuj** w prawym górnym rogu okna przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="e4c72-106">To make a simple change to the content of a Markdown file, click the **Edit** link in the upper right corner of the browser window.</span></span> <span data-ttu-id="e4c72-107">Może być konieczne rozwinięcie paska **opcji** , aby zobaczyć link **Edytuj** .</span><span class="sxs-lookup"><span data-stu-id="e4c72-107">You might need to expand the **options** bar to see the **Edit** link.</span></span> <span data-ttu-id="e4c72-108">Postępuj zgodnie z instrukcjami, aby utworzyć żądanie ściągnięcia.</span><span class="sxs-lookup"><span data-stu-id="e4c72-108">Follow the directions to create a pull request (PR).</span></span> <span data-ttu-id="e4c72-109">Zespół EF sprawdzi żądanie ściągnięcia i zaakceptuje je lub zasugeruje zmianę.</span><span class="sxs-lookup"><span data-stu-id="e4c72-109">The EF team will review the PR and accept it or suggest changes.</span></span>

## <a name="how-to-make-a-more-complex-submission"></a><span data-ttu-id="e4c72-110">Jak utworzyć bardziej złożone przesyłanie</span><span class="sxs-lookup"><span data-stu-id="e4c72-110">How to make a more complex submission</span></span>

<span data-ttu-id="e4c72-111">Potrzebna jest podstawowa znajomość usługi [git i GitHub.com](https://guides.github.com/activities/hello-world/).</span><span class="sxs-lookup"><span data-stu-id="e4c72-111">You'll need a basic understanding of [Git and GitHub.com](https://guides.github.com/activities/hello-world/).</span></span>

* <span data-ttu-id="e4c72-112">Otwórz [problem](https://github.com/dotnet/EntityFramework.Docs/issues/new) z opisem tego, co chcesz zrobić, na przykład zmień istniejący artykuł lub Utwórz nowy.</span><span class="sxs-lookup"><span data-stu-id="e4c72-112">Open an [issue](https://github.com/dotnet/EntityFramework.Docs/issues/new) describing what you want to do, such as change an existing article or create a new one.</span></span> <span data-ttu-id="e4c72-113">Zaczekaj na zatwierdzenie przez zespół EF przed zainwestowaniem dużo czasu.</span><span class="sxs-lookup"><span data-stu-id="e4c72-113">Wait for approval from the EF team before you invest much time.</span></span>
* <span data-ttu-id="e4c72-114">Rozwidlenie repozytorium [dotnet/EntityFramework. docs](https://github.com/dotnet/EntityFramework.Docs/) i utworzenie gałęzi dla zmian.</span><span class="sxs-lookup"><span data-stu-id="e4c72-114">Fork the [dotnet/EntityFramework.Docs](https://github.com/dotnet/EntityFramework.Docs/) repo and create a branch for your changes.</span></span>
* <span data-ttu-id="e4c72-115">Prześlij żądanie ściągnięcia do wzorca ze zmianami.</span><span class="sxs-lookup"><span data-stu-id="e4c72-115">Submit a pull request (PR) to master with your changes.</span></span>
* <span data-ttu-id="e4c72-116">Odpowiedź na opinię żądania ściągnięcia.</span><span class="sxs-lookup"><span data-stu-id="e4c72-116">Respond to PR feedback.</span></span>

## <a name="markdown-syntax"></a><span data-ttu-id="e4c72-117">Składnia promocji</span><span class="sxs-lookup"><span data-stu-id="e4c72-117">Markdown syntax</span></span>

<span data-ttu-id="e4c72-118">Artykuły są napisywane w [DocFxej promocji (DFM)](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html)z rozłożeniami ( [GFM) z obsługą usługi GitHub](https://guides.github.com/features/mastering-markdown/).</span><span class="sxs-lookup"><span data-stu-id="e4c72-118">Articles are written in [DocFx-flavored Markdown (DFM)](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html), a superset of [GitHub-flavored Markdown (GFM)](https://guides.github.com/features/mastering-markdown/).</span></span> <span data-ttu-id="e4c72-119">Aby zapoznać się z przykładami składni DFM i metadanych funkcji interfejsu użytkownika powszechnie używanych w dokumentacji Dr, zobacz artykuł [Metadata and promocji — szablon](https://github.com/dotnet/docs/blob/master/styleguide/template.md) w przewodniku po stylu repozytorium .NET Core.</span><span class="sxs-lookup"><span data-stu-id="e4c72-119">For examples of DFM syntax and metadata for UI features commonly used in the EF documentation, see [Metadata and Markdown Template](https://github.com/dotnet/docs/blob/master/styleguide/template.md) in the .NET Core repo style guide.</span></span>

## <a name="folder-structure-conventions"></a><span data-ttu-id="e4c72-120">Konwencje struktury folderów</span><span class="sxs-lookup"><span data-stu-id="e4c72-120">Folder structure conventions</span></span>

<span data-ttu-id="e4c72-121">Obrazy i inna zawartość statyczna są przechowywane w `_static` folderze w każdym obszarze/folderze witryny.</span><span class="sxs-lookup"><span data-stu-id="e4c72-121">Images and other static content are stored in an `_static` folder within each area/folder of the site.</span></span>

<span data-ttu-id="e4c72-122">Przykłady kodu są przechowywane w folderze `samples` głównym.</span><span class="sxs-lookup"><span data-stu-id="e4c72-122">Code samples are stored in the `samples` root folder.</span></span> <span data-ttu-id="e4c72-123">Są one zorganizowane w strukturę folderów, która naśladuje strukturę dokumentacji (znajdującą się `entity-framework` w folderze głównym).</span><span class="sxs-lookup"><span data-stu-id="e4c72-123">They are organized into a folder structure that mimics the documentation structure (found under the `entity-framework` root folder).</span></span>

## <a name="code-snippets"></a><span data-ttu-id="e4c72-124">Fragmenty kodu</span><span class="sxs-lookup"><span data-stu-id="e4c72-124">Code snippets</span></span>

<span data-ttu-id="e4c72-125">Artykuły często zawierają fragmenty kodu przedstawiające punkty.</span><span class="sxs-lookup"><span data-stu-id="e4c72-125">Articles frequently contain code snippets to illustrate points.</span></span> <span data-ttu-id="e4c72-126">DFM umożliwia skopiowanie kodu do pliku o promocji lub zapoznaj się z osobnym plikiem kodu.</span><span class="sxs-lookup"><span data-stu-id="e4c72-126">DFM lets you copy code into the Markdown file or refer to a separate code file.</span></span> <span data-ttu-id="e4c72-127">Jeśli to możliwe, użyj oddzielnych plików kodu, aby zminimalizować prawdopodobieństwo wystąpienia błędów w kodzie.</span><span class="sxs-lookup"><span data-stu-id="e4c72-127">Whenever possible, use separate code files to minimize the chance of errors in the code.</span></span> <span data-ttu-id="e4c72-128">Pliki kodu powinny być przechowywane w repozytorium przy użyciu struktury folderów opisanej powyżej dla przykładowych projektów.</span><span class="sxs-lookup"><span data-stu-id="e4c72-128">The code files should be stored in the repo using the folder structure described above for sample projects.</span></span>

<span data-ttu-id="e4c72-129">Poniżej przedstawiono kilka przykładów [składni fragmentu kodu DFM](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet).</span><span class="sxs-lookup"><span data-stu-id="e4c72-129">Here are some examples of [DFM code snippet syntax](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet).</span></span>

<span data-ttu-id="e4c72-130">Aby renderować cały plik kodu jako fragment:</span><span class="sxs-lookup"><span data-stu-id="e4c72-130">To render an entire code file as a snippet:</span></span>

``` none
[!code-csharp[Main](../../../samples/core/saving/Program.cs)]
```

<span data-ttu-id="e4c72-131">Aby renderować część pliku jako fragment przy użyciu numerów wierszy:</span><span class="sxs-lookup"><span data-stu-id="e4c72-131">To render a portion of a file as a snippet by using line numbers:</span></span>

``` none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?range=1-10]
```

<span data-ttu-id="e4c72-132">W przypadku fragmentów języka C# można odwołać się do [regionu języka c#](https://msdn.microsoft.com/library/9a1ybwek.aspx).</span><span class="sxs-lookup"><span data-stu-id="e4c72-132">For C# snippets, you can reference a [C# region](https://msdn.microsoft.com/library/9a1ybwek.aspx).</span></span> <span data-ttu-id="e4c72-133">Używaj regionów zamiast numerów wierszy.</span><span class="sxs-lookup"><span data-stu-id="e4c72-133">Use regions rather than line numbers.</span></span> <span data-ttu-id="e4c72-134">Numery wierszy w pliku kodu mogą ulec zmianie i nie są zsynchronizowane z odwołaniami do numerów wierszy w ramach promocji.</span><span class="sxs-lookup"><span data-stu-id="e4c72-134">Line numbers in a code file tend to change and get out of sync with line number references in Markdown.</span></span> <span data-ttu-id="e4c72-135">Regiony języka C# mogą być zagnieżdżane.</span><span class="sxs-lookup"><span data-stu-id="e4c72-135">C# regions can be nested.</span></span> <span data-ttu-id="e4c72-136">Jeśli odwołujesz się do regionu zewnętrznego, `#region` wewnętrzne `#endregion` i dyrektywy nie są renderowane w fragmencie kodu.</span><span class="sxs-lookup"><span data-stu-id="e4c72-136">If you reference the outer region, the inner `#region` and `#endregion` directives are not rendered in a snippet.</span></span>

<span data-ttu-id="e4c72-137">Aby renderować region języka C# o nazwie "snippet_Example":</span><span class="sxs-lookup"><span data-stu-id="e4c72-137">To render a C# region named "snippet_Example":</span></span>

``` none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?name=snippet_Example)]
```

<span data-ttu-id="e4c72-138">Aby wyróżnić wybrane wiersze w renderowanym fragmencie (zwykle jest to kolor żółtego tła):</span><span class="sxs-lookup"><span data-stu-id="e4c72-138">To highlight selected lines in a rendered snippet (usually renders as yellow background color):</span></span>

``` none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?name=snippet_Example&highlight=1-3,10,20-25)]
```

## <a name="test-your-changes-with-docfx"></a><span data-ttu-id="e4c72-139">Testowanie zmian za pomocą DocFX</span><span class="sxs-lookup"><span data-stu-id="e4c72-139">Test your changes with DocFX</span></span>

<span data-ttu-id="e4c72-140">Przetestuj zmiany za pomocą [narzędzia wiersza polecenia DocFX](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool), które tworzy lokalnie hostowaną wersję lokacji.</span><span class="sxs-lookup"><span data-stu-id="e4c72-140">Test your changes with the [DocFX command-line tool](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool), which creates a locally hosted version of the site.</span></span> <span data-ttu-id="e4c72-141">DocFX nie renderuje stylu i rozszerzeń witryny utworzonych dla docs.microsoft.com.</span><span class="sxs-lookup"><span data-stu-id="e4c72-141">DocFX doesn't render style and site extensions created for docs.microsoft.com.</span></span>

<span data-ttu-id="e4c72-142">DocFX wymaga .NET Framework w systemie Windows lub mono dla systemu Linux lub macOS.</span><span class="sxs-lookup"><span data-stu-id="e4c72-142">DocFX requires the .NET Framework on Windows, or Mono for Linux or macOS.</span></span>

### <a name="windows-instructions"></a><span data-ttu-id="e4c72-143">Instrukcje dotyczące systemu Windows</span><span class="sxs-lookup"><span data-stu-id="e4c72-143">Windows instructions</span></span>

* <span data-ttu-id="e4c72-144">Pobierz i rozpakuj *docfx. zip* z [wydań docfx](https://github.com/dotnet/docfx/releases).</span><span class="sxs-lookup"><span data-stu-id="e4c72-144">Download and unzip *docfx.zip* from [DocFX releases](https://github.com/dotnet/docfx/releases).</span></span>
* <span data-ttu-id="e4c72-145">Dodaj DocFX do ścieżki.</span><span class="sxs-lookup"><span data-stu-id="e4c72-145">Add DocFX to your PATH.</span></span>
* <span data-ttu-id="e4c72-146">W oknie wiersza polecenia przejdź do sklonowanego repozytorium (zawierającego plik *docfx. JSON* ) i uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="e4c72-146">In a command-line window, navigate to the cloned repository (which contains the *docfx.json* file) and run the following command:</span></span>

   ``` console
   docfx -t default --serve
   ```

* <span data-ttu-id="e4c72-147">W przeglądarce przejdź do `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="e4c72-147">In a browser, navigate to `http://localhost:8080`.</span></span>

### <a name="mono-instructions"></a><span data-ttu-id="e4c72-148">Instrukcje dotyczące narzędzia mono</span><span class="sxs-lookup"><span data-stu-id="e4c72-148">Mono instructions</span></span>

* <span data-ttu-id="e4c72-149">Zainstaluj program mono za pośrednictwem oprogramowania Homebrew- `brew install mono`.</span><span class="sxs-lookup"><span data-stu-id="e4c72-149">Install Mono via Homebrew - `brew install mono`.</span></span>
* <span data-ttu-id="e4c72-150">Pobierz [najnowszą wersję programu DocFX](https://github.com/dotnet/docfx/releases/tag/v2.7.2).</span><span class="sxs-lookup"><span data-stu-id="e4c72-150">Download the [latest version of DocFX](https://github.com/dotnet/docfx/releases/tag/v2.7.2).</span></span>
* <span data-ttu-id="e4c72-151">Wyodrębnij `\bin\docfx`do.</span><span class="sxs-lookup"><span data-stu-id="e4c72-151">Extract to `\bin\docfx`.</span></span>
* <span data-ttu-id="e4c72-152">Utwórz alias dla **docfx**:</span><span class="sxs-lookup"><span data-stu-id="e4c72-152">Create an alias for **docfx**:</span></span>

  ``` console
  function docfx {
    mono $HOME/bin/docfx/docfx.exe
  }

  function docfx-serve {
    mono $HOME/bin/docfx/docfx.exe serve _site
  }
  ```

* <span data-ttu-id="e4c72-153">Uruchom **docfx** w sklonowanym repozytorium, aby skompilować lokację, i **docfx-** zapoznaj się z witryną `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="e4c72-153">Run **docfx** in the cloned repository to build the site, and **docfx-serve** to view the site at `http://localhost:8080`.</span></span>

## <a name="voice-and-tone"></a><span data-ttu-id="e4c72-154">Głos i dźwięk</span><span class="sxs-lookup"><span data-stu-id="e4c72-154">Voice and tone</span></span>

<span data-ttu-id="e4c72-155">Naszym celem jest pisanie dokumentacji, która jest łatwa do zrozumienia przez szerszego grona odbiorców.</span><span class="sxs-lookup"><span data-stu-id="e4c72-155">Our goal is to write documentation that is easily understandable by the widest possible audience.</span></span> <span data-ttu-id="e4c72-156">W tym celu wprowadziliśmy wskazówki dotyczące pisania stylów, które poprosiły naszych współpracowników.</span><span class="sxs-lookup"><span data-stu-id="e4c72-156">To that end we have established guidelines for writing style that we ask our contributors to follow.</span></span> <span data-ttu-id="e4c72-157">Aby uzyskać więcej informacji, zobacz [wytyczne dotyczące głosu i tonu](https://github.com/dotnet/docs/blob/master/styleguide/voice-tone.md) w repozytorium .NET Core.</span><span class="sxs-lookup"><span data-stu-id="e4c72-157">For more information, see [Voice and tone guidelines](https://github.com/dotnet/docs/blob/master/styleguide/voice-tone.md) in the .NET Core repo.</span></span>
