# <a name="contributing-to-the-entity-framework-documentation"></a>Współtworzenie dokumentacji Entity Framework

Poniżej opisano proces tworzenia artykułów i przykładów kodu do dokumentacji Entity Framework. Wkłady mogą być bardzo proste jako poprawki literówki lub złożone jako nowe artykuły.

## <a name="how-to-make-a-simple-correction-or-suggestion"></a>Jak wykonać prostą korektę lub sugestię

Artykuły są przechowywane jako pliki promocji w tym repozytorium. Aby wprowadzić prostą zmianę zawartości pliku z promocji, kliknij link **Edytuj** w prawym górnym rogu okna przeglądarki. Może być konieczne rozwinięcie paska **opcji** , aby zobaczyć link **Edytuj** . Postępuj zgodnie z instrukcjami, aby utworzyć żądanie ściągnięcia. Zespół EF sprawdzi żądanie ściągnięcia i zaakceptuje je lub zasugeruje zmianę.

## <a name="how-to-make-a-more-complex-submission"></a>Jak utworzyć bardziej złożone przesyłanie

Potrzebna jest podstawowa znajomość usługi [git i GitHub.com](https://guides.github.com/activities/hello-world/).

* Otwórz [problem](https://github.com/dotnet/EntityFramework.Docs/issues/new) z opisem tego, co chcesz zrobić, na przykład zmień istniejący artykuł lub Utwórz nowy. Zaczekaj na zatwierdzenie przez zespół EF przed zainwestowaniem dużo czasu.
* Rozwidlenie repozytorium [dotnet/EntityFramework. docs](https://github.com/dotnet/EntityFramework.Docs/) i utworzenie gałęzi dla zmian.
* Prześlij żądanie ściągnięcia do wzorca ze zmianami.
* Odpowiedź na opinię żądania ściągnięcia.

## <a name="markdown-syntax"></a>Składnia promocji

Artykuły są napisywane w [DocFxej promocji (DFM)](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html)z rozłożeniami ( [GFM) z obsługą usługi GitHub](https://guides.github.com/features/mastering-markdown/). Aby zapoznać się z przykładami składni DFM i metadanych funkcji interfejsu użytkownika powszechnie używanych w dokumentacji Dr, zobacz artykuł [Metadata and promocji — szablon](https://github.com/dotnet/docs/blob/master/styleguide/template.md) w przewodniku po stylu repozytorium .NET Core.

## <a name="folder-structure-conventions"></a>Konwencje struktury folderów

Obrazy i inna zawartość statyczna są przechowywane w `_static` folderze w każdym obszarze/folderze witryny.

Przykłady kodu są przechowywane w folderze `samples` głównym. Są one zorganizowane w strukturę folderów, która naśladuje strukturę dokumentacji (znajdującą się `entity-framework` w folderze głównym).

## <a name="code-snippets"></a>Fragmenty kodu

Artykuły często zawierają fragmenty kodu przedstawiające punkty. DFM umożliwia skopiowanie kodu do pliku o promocji lub zapoznaj się z osobnym plikiem kodu. Jeśli to możliwe, użyj oddzielnych plików kodu, aby zminimalizować prawdopodobieństwo wystąpienia błędów w kodzie. Pliki kodu powinny być przechowywane w repozytorium przy użyciu struktury folderów opisanej powyżej dla przykładowych projektów.

Poniżej przedstawiono kilka przykładów [składni fragmentu kodu DFM](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet).

Aby renderować cały plik kodu jako fragment:

``` none
[!code-csharp[Main](../../../samples/core/saving/Program.cs)]
```

Aby renderować część pliku jako fragment przy użyciu numerów wierszy:

``` none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?range=1-10]
```

W przypadku fragmentów języka C# można odwołać się do [regionu języka c#](https://msdn.microsoft.com/library/9a1ybwek.aspx). Używaj regionów zamiast numerów wierszy. Numery wierszy w pliku kodu mogą ulec zmianie i nie są zsynchronizowane z odwołaniami do numerów wierszy w ramach promocji. Regiony języka C# mogą być zagnieżdżane. Jeśli odwołujesz się do regionu zewnętrznego, `#region` wewnętrzne `#endregion` i dyrektywy nie są renderowane w fragmencie kodu.

Aby renderować region języka C# o nazwie "snippet_Example":

``` none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?name=snippet_Example)]
```

Aby wyróżnić wybrane wiersze w renderowanym fragmencie (zwykle jest to kolor żółtego tła):

``` none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?name=snippet_Example&highlight=1-3,10,20-25)]
```

## <a name="test-your-changes-with-docfx"></a>Testowanie zmian za pomocą DocFX

Przetestuj zmiany za pomocą [narzędzia wiersza polecenia DocFX](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool), które tworzy lokalnie hostowaną wersję lokacji. DocFX nie renderuje stylu i rozszerzeń witryny utworzonych dla docs.microsoft.com.

DocFX wymaga .NET Framework w systemie Windows lub mono dla systemu Linux lub macOS.

### <a name="windows-instructions"></a>Instrukcje dotyczące systemu Windows

* Pobierz i rozpakuj *docfx. zip* z [wydań docfx](https://github.com/dotnet/docfx/releases).
* Dodaj DocFX do ścieżki.
* W oknie wiersza polecenia przejdź do sklonowanego repozytorium (zawierającego plik *docfx. JSON* ) i uruchom następujące polecenie:

   ``` console
   docfx -t default --serve
   ```

* W przeglądarce przejdź do `http://localhost:8080`.

### <a name="mono-instructions"></a>Instrukcje dotyczące narzędzia mono

* Zainstaluj program mono za pośrednictwem oprogramowania Homebrew- `brew install mono`.
* Pobierz [najnowszą wersję programu DocFX](https://github.com/dotnet/docfx/releases/tag/v2.7.2).
* Wyodrębnij `\bin\docfx`do.
* Utwórz alias dla **docfx**:

  ``` console
  function docfx {
    mono $HOME/bin/docfx/docfx.exe
  }

  function docfx-serve {
    mono $HOME/bin/docfx/docfx.exe serve _site
  }
  ```

* Uruchom **docfx** w sklonowanym repozytorium, aby skompilować lokację, i **docfx-** zapoznaj się z witryną `http://localhost:8080`.

## <a name="voice-and-tone"></a>Głos i dźwięk

Naszym celem jest pisanie dokumentacji, która jest łatwa do zrozumienia przez szerszego grona odbiorców. W tym celu wprowadziliśmy wskazówki dotyczące pisania stylów, które poprosiły naszych współpracowników. Aby uzyskać więcej informacji, zobacz [wytyczne dotyczące głosu i tonu](https://github.com/dotnet/docs/blob/master/styleguide/voice-tone.md) w repozytorium .NET Core.
