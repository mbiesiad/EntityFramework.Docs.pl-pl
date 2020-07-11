---
title: Odtwarzanie przez EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/13/2018
ms.assetid: 6263EF7D-4989-42E6-BDEE-45DA770342FB
uid: core/managing-schemas/scaffolding
ms.openlocfilehash: 19945ef2eb99ac423cc50510edc85439964024b8
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238193"
---
# <a name="reverse-engineering"></a> Odtwarzanie

Odtwarzanie jest procesem tworzenia szkieletu klas typu jednostki i klasy DbContext na podstawie schematu bazy danych. Można to zrobić za pomocą `Scaffold-DbContext` polecenia narzędzi EF Core Console Menedżera pakietów (PMC) lub `dotnet ef dbcontext scaffold` polecenia narzędzi interfejsu wiersza polecenia (CLI) platformy .NET.

## <a name="installing"></a>Instalowanie

Przed odwróceniem zespołu należy zainstalować [Narzędzia PMC](xref:core/miscellaneous/cli/powershell) (tylko Visual Studio) lub [Narzędzia interfejsu wiersza polecenia](xref:core/miscellaneous/cli/dotnet). Zobacz linki, aby uzyskać szczegółowe informacje.

Należy również zainstalować odpowiedniego [dostawcę bazy danych](xref:core/providers/index) dla schematu bazy danych, który ma być odtwarzany.

## <a name="connection-string"></a>Parametry połączenia

Pierwszym argumentem polecenia są parametry połączenia z bazą danych. Narzędzia użyją tych parametrów połączenia, aby odczytać schemat bazy danych.

Sposób tworzenia cudzysłowów i ucieczki parametrów połączenia zależy od powłoki, która jest używana do wykonywania polecenia. Szczegółowe informacje można znaleźć w dokumentacji powłoki. Na przykład program PowerShell wymaga znaku ucieczki `$` , ale nie `\` .

### <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer
```

### <a name="visual-studio"></a>[Program Visual Studio](#tab/vs)

``` powershell
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer
```

***

### <a name="configuration-and-user-secrets"></a>Klucze tajne konfiguracji i użytkownika

Jeśli masz projekt ASP.NET Core, możesz użyć `Name=<connection-string>` składni, aby odczytać parametry połączenia z konfiguracji.

Jest to dobre rozwiązanie przy użyciu [Narzędzia tajnego Menedżera](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager) w celu zachowania podzielenia hasła bazy danych od kodu bazowej.

```dotnetcli
dotnet user-secrets set ConnectionStrings.Chinook "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook"
dotnet ef dbcontext scaffold Name=ConnectionStrings.Chinook Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="provider-name"></a>Nazwa dostawcy

Drugi argument jest nazwą dostawcy. Nazwa dostawcy jest zwykle taka sama jak nazwa pakietu NuGet dostawcy.

## <a name="specifying-tables"></a>Określanie tabel

Wszystkie tabele w schemacie bazy danych są domyślnie odtwarzane w postaci typów jednostek. Można ograniczyć, które tabele są odtworzone, określając schematy i tabele.

### <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/dotnet-core-cli)

`--schema`Opcja może być używana do dołączania każdej tabeli w schemacie, natomiast `--table` może być używana do dołączania określonych tabel.

Aby dołączyć wiele tabel, określ opcję wiele razy:

```dotnetcli
dotnet ef dbcontext scaffold ... --table Artist --table Album
```

### <a name="visual-studio"></a>[Program Visual Studio](#tab/vs)

`-Schemas`Opcja może być używana do dołączania każdej tabeli w schemacie, natomiast `-Tables` może być używana do dołączania określonych tabel.

Aby dołączyć wiele tabel, użyj tablicy:

``` powershell
Scaffold-DbContext ... -Tables Artist, Album
```

***

## <a name="preserving-names"></a>Zachowywanie nazw

Nazwy tabel i kolumn są rozwiązywane w celu lepszego dopasowania do konwencji nazewnictwa platformy .NET dla typów i właściwości. Określenie `-UseDatabaseNames` przełącznika w przystawce PMC lub `--use-database-names` opcji w interfejs wiersza polecenia platformy .NET Core spowoduje wyłączenie tego zachowania, które zachowuje pierwotne nazwy baz danych, tak jak to możliwe. Nieprawidłowe identyfikatory platformy .NET nadal będą stałe i są one zgodne z konwencjami nazewnictwa platformy .NET.

## <a name="fluent-api-or-data-annotations"></a>Interfejs API Fluent lub adnotacje danych

Typy jednostek są domyślnie konfigurowane przy użyciu interfejsu API Fluent. Określ `-DataAnnotations` (PMC) lub `--data-annotations` (interfejs wiersza polecenia platformy .NET Core), aby zamiast tego używać adnotacji danych, gdy jest to możliwe.

Na przykład korzystanie z interfejsu API Fluent spowoduje przetworzenie szkieletu:

``` csharp
entity.Property(e => e.Title)
    .IsRequired()
    .HasMaxLength(160);
```

Używanie adnotacji danych spowoduje szkielet:

``` csharp
[Required]
[StringLength(160)]
public string Title { get; set; }
```

## <a name="dbcontext-name"></a>Nazwa kontekstu

Nazwa klasy DbContext z szkieletem jest nazwą bazy danych domyślnie sufiksem z *kontekstem* . Aby określić inny, użyj `-Context` w PMC i `--context` w interfejs wiersza polecenia platformy .NET Core.

## <a name="directories-and-namespaces"></a>Katalogi i przestrzenie nazw

Klasy jednostek i Klasa DbContext są szkieletowe w katalogu głównym projektu i używają domyślnej przestrzeni nazw projektu.

### <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/dotnet-core-cli)

Można określić katalog, w którym są używane szkieletowe klasy `--output-dir` , i `--context-dir` może służyć do tworzenia szkieletu klasy DbContext w oddzielnym katalogu z klas typu jednostki:

```dotnetcli
dotnet ef dbcontext scaffold ... --context-dir Data --output-dir Models
```

Domyślnie przestrzeń nazw będzie główną przestrzenią nazw oraz nazwami wszystkich podkatalogów w katalogu głównym projektu. Jednakże z EFCore 5,0, można przesłonić przestrzeń nazw dla wszystkich klas wyjściowych za pomocą `--namespace` . Można również przesłonić przestrzeń nazw tylko dla klasy DbContext przy użyciu `--context-namespace` :

```dotnetcli
dotnet ef dbcontext scaffold ... --namespace Your.Namespace --context-namespace Your.DbContext.Namespace
```

### <a name="visual-studio"></a>[Program Visual Studio](#tab/vs)

Można określić katalog, w którym są używane szkieletowe klasy `-OutputDir` , i `-ContextDir` może służyć do tworzenia szkieletu klasy DbContext w oddzielnym katalogu z klas typu jednostki:

``` powershell
Scaffold-DbContext ... -ContextDir Data -OutputDir Models
```

Domyślnie przestrzeń nazw będzie główną przestrzenią nazw oraz nazwami wszystkich podkatalogów w katalogu głównym projektu. Jednakże z EFCore 5,0, można przesłonić przestrzeń nazw dla wszystkich klas wyjściowych za pomocą `-Namespace` . Można również przesłonić przestrzeń nazw tylko dla klasy DbContext przy użyciu `-ContextNamespace` .

``` powershell
Scaffold-DbContext ... -Namespace Your.Namespace -ContextNamespace Your.DbContext.Namespace
```

***

## <a name="how-it-works"></a>Jak to działa

Odtwarzanie rozpocznie się, odczytując schemat bazy danych. Odczytuje informacje o tabelach, kolumnach, ograniczeniach i indeksach.

Następnie używa informacji o schemacie do tworzenia modelu EF Core. Tabele są używane do tworzenia typów jednostek; kolumny są używane do tworzenia właściwości; i klucze obce są używane do tworzenia relacji.

Na koniec model jest używany do generowania kodu. Odpowiednie klasy typu jednostki, interfejs API Fluent i adnotacje danych są szkieletami, aby można było ponownie utworzyć ten sam model z poziomu aplikacji.

## <a name="limitations"></a>Ograniczenia

* Nie wszystkie elementy dotyczące modelu mogą być reprezentowane za pomocą schematu bazy danych. Na przykład informacje o [**hierarchiach dziedziczenia**](../modeling/inheritance.md), [**typach będących własnością**](../modeling/owned-entities.md)i [**podziałie tabeli**](../modeling/table-splitting.md) nie są obecne w schemacie bazy danych. W związku z tym konstrukcje te nigdy nie będą odtwarzane.
* Ponadto **niektóre typy kolumn** mogą nie być obsługiwane przez dostawcę EF Core. Te kolumny nie zostaną uwzględnione w modelu.
* Można zdefiniować [**tokeny współbieżności**](../modeling/concurrency.md)w modelu EF Core, aby uniemożliwić dwóm użytkownikom Aktualizowanie tej samej jednostki w tym samym czasie. Niektóre bazy danych mają specjalny typ reprezentujący ten typ kolumny (na przykład rowversion w SQL Server), w którym można odtworzyć te informacje. Jednak inne tokeny współbieżności nie będą odtwarzane.
* [Funkcja typ referencyjny języka C# 8](/dotnet/csharp/tutorials/nullable-reference-types) nie jest obecnie obsługiwana w przypadku odtwarzania: EF Core zawsze generuje kod C#, który zakłada, że ta funkcja jest wyłączona. Na przykład kolumny tekstu dopuszczające wartość null będą szkieletem jako właściwość typu `string` , a nie `string?` z interfejsem API Fluent lub adnotacjami danych używanymi do konfigurowania, czy właściwość jest wymagana. Można edytować kod szkieletowy i zastąpić je za pomocą adnotacji o wartości null w języku C#. Obsługa tworzenia szkieletów dla typów odwołań do wartości null jest śledzona przez [#15520](https://github.com/aspnet/EntityFrameworkCore/issues/15520)problemu.

## <a name="customizing-the-model"></a>Dostosowywanie modelu

Kod wygenerowany przez EF Core to Twój kod. Możesz go zmienić. Zostanie on wygenerowany ponownie tylko w przypadku ponownego odtworzenia tego samego modelu. Kod szkieletowy reprezentuje *jeden* model, który może być używany w celu uzyskania dostępu do bazy danych, ale nie jest to *jedyny* model, który może być używany.

Dostosuj klasy typu jednostki i klasy DbContext do Twoich potrzeb. Można na przykład zmienić nazwy typów i właściwości, wprowadzić hierarchie dziedziczenia lub podzielić tabelę na wiele jednostek. Można również usunąć nieunikatowe indeksy, nieużywane sekwencje i właściwości nawigacji, opcjonalne właściwości skalarne i nazwy ograniczeń z modelu.

Można również dodać dodatkowe konstruktory, metody, właściwości itp. użycie innej klasy częściowej w oddzielnym pliku. Takie podejście działa nawet wtedy, gdy zamierzasz ponownie odtworzyć model.

## <a name="updating-the-model"></a>Aktualizowanie modelu

Po wprowadzeniu zmian w bazie danych może być konieczne zaktualizowanie modelu EF Core w celu odzwierciedlenia tych zmian. W przypadku, gdy baza danych jest prosta, może być najłatwiej ręcznie wprowadzić zmiany w modelu EF Core. Na przykład zmiana nazwy tabeli lub kolumny, usunięcie kolumny lub aktualizacja typu kolumny to proste zmiany wprowadzane w kodzie.

Bardziej znaczące zmiany nie są jednak łatwe do ręcznego wprowadzania. Jednym z typowych przepływów pracy jest ponowne odtwarzanie modelu z bazy danych przy użyciu `-Force` (PMC) lub `--force` (CLI), aby zastąpić istniejący model zaktualizowanym.

Inną często żądaną funkcją jest możliwość aktualizowania modelu z bazy danych przy zachowaniu dostosowania, takiego jak renames, hierarchie typów itd. Użyj [#831](https://github.com/aspnet/EntityFrameworkCore/issues/831) problemu, aby śledzić postęp tej funkcji.

> [!WARNING]
> Jeśli odwrócisz model z bazy danych, wszelkie zmiany wprowadzone w plikach zostaną utracone.
