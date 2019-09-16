---
title: Istotne zmiany w EF Core 3,0 — EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: EE2878C9-71F9-4FA5-9BC4-60517C7C9830
uid: core/what-is-new/ef-core-3.0/breaking-changes
ms.openlocfilehash: 04487291f24bb702dad4b497c34234afdd5e3c9a
ms.sourcegitcommit: d01fc19aa42ca34c3bebccbc96ee26d06fcecaa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/16/2019
ms.locfileid: "71005586"
---
# <a name="breaking-changes-included-in-ef-core-30"></a>Istotne zmiany zawarte w EF Core 3,0
Poniższe zmiany dotyczące interfejsu API i zachowania mogą powodować przerwanie istniejących aplikacji podczas uaktualniania ich do 3.0.0.
Zmiany, których oczekujemy tylko dostawcy bazy danych, są udokumentowane w obszarze [zmiany dostawcy](../../providers/provider-log.md).
Przerwy z jednej wersji zapoznawczej 3,0 do innej wersji zapoznawczej 3,0 nie są tutaj udokumentowane.

## <a name="summary"></a>Podsumowanie

| **Zmiana powodująca niezgodność**                                                                                               | **Wpływ** |
|:------------------------------------------------------------------------------------------------------------------|------------|
| [Zapytania LINQ nie są już oceniane na kliencie](#linq-queries-are-no-longer-evaluated-on-the-client)         | Wysoka       |
| [EF Core 3,0 cele .NET Standard 2,1, a nie .NET Standard 2,0](#netstandard21) | Wysoka      |
| [Narzędzie wiersza polecenia EF Core, dotnet EF, nie jest już częścią zestaw .NET Core SDK](#dotnet-ef) | Wysoka      |
| [Nazwy Z tabel, ExecuteSql by i ExecuteSqlAsync](#fromsql) | Wysoka      |
| [Typy zapytań są konsolidowane z typami jednostek](#qt) | Wysoka      |
| [Entity Framework Core nie jest już częścią ASP.NET Core współdzielonej struktury](#no-longer) | Średni      |
| [Usuwanie kaskadowe jest teraz wykonywane natychmiast domyślnie](#cascade) | Średni      |
| [DeleteBehavior. ograniczanie ma semantykę oczyszczarki](#deletebehavior) | Średni      |
| [Interfejs API konfiguracji dla relacji typu posiadanego został zmieniony](#config) | Średni      |
| [Każda właściwość używa niezależnej generacji klucza w pamięci](#each) | Średni      |
| [Zapytania nie śledzące już nie wykonują rozpoznawania tożsamości](#notrackingresolution) | Średni      |
| [Zmiany interfejsu API metadanych](#metadata-api-changes) | Średni      |
| [Zmiany w interfejsie API metadanych specyficzne dla dostawcy](#provider) | Średni      |
| [UseRowNumberForPaging został usunięty](#urn) | Średni      |
| [Metody Z tabel można określić tylko dla katalogów głównych zapytań](#fromsql) | Małe      |
| [~~Wykonywanie zapytania jest rejestrowane na poziomie debugowania~~ Przywrócono](#qe) | Małe      |
| [Wartości klucza tymczasowego nie są już ustawione na wystąpienia jednostek](#tkv) | Małe      |
| [DetectChanges uznaje wartości klucza generowane przez magazyn](#dc) | Małe      |
| [Jednostki zależne współużytkujące tabelę z podmiotem zabezpieczeń są teraz opcjonalne](#de) | Małe      |
| [Wszystkie jednostki współużytkujące tabelę z kolumną Token współbieżności muszą mapować ją na Właściwość](#aes) | Małe      |
| [Dziedziczone właściwości z niemapowanych typów są teraz mapowane na pojedynczą kolumnę dla wszystkich typów pochodnych](#ip) | Małe      |
| [Konwencja właściwości klucza obcego nie jest już zgodna z tą samą nazwą co właściwość podmiotu zabezpieczeń](#fkp) | Małe      |
| [Połączenie z bazą danych jest teraz zamknięte, jeśli nie jest używane już przed ukończeniem elementu TransactionScope](#dbc) | Małe      |
| [Pola zapasowe są używane domyślnie](#backing-fields-are-used-by-default) | Małe      |
| [Zgłoś, czy znaleziono wiele zgodnych pól zapasowych](#throw-if-multiple-compatible-backing-fields-are-found) | Małe      |
| [Nazwy właściwości tylko dla pól powinny być zgodne z nazwą pola](#field-only-property-names-should-match-the-field-name) | Małe      |
| [AddDbContext/AddDbContextPool nie wywołuje już metody addlogging i AddMemoryCache](#adddbc) | Małe      |
| [DbContext. entry wykonuje teraz lokalną DetectChanges](#dbe) | Małe      |
| [Klucze tablic ciągów i bajtów nie są generowane domyślnie przez klienta](#string-and-byte-array-keys-are-not-client-generated-by-default) | Małe      |
| [ILoggerFactory jest teraz usługą objętą zakresem](#ilf) | Małe      |
| [Pobieranie z opóźnieniem — nie zakłada się już, że właściwości nawigacji są w pełni załadowane](#lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded) | Małe      |
| [Nadmierne Tworzenie wewnętrznych dostawców usług jest teraz domyślnie błędem](#excessive-creation-of-internal-service-providers-is-now-an-error-by-default) | Małe      |
| [Nowe zachowanie dla HasOne/HasMany wywoływane z pojedynczym ciągiem](#nbh) | Małe      |
| [Typ zwracany dla kilku metod asynchronicznych został zmieniony z zadania na ValueTask](#rtnt) | Małe      |
| [Adnotacja relacyjna: TypeMapping ma teraz tylko Właściwość TypeMapping](#rtt) | Małe      |
| [ToTable dla typu pochodnego zgłasza wyjątek](#totable-on-a-derived-type-throws-an-exception) | Małe      |
| [Nie EF Core już wysyłać dyrektywy pragma dla wymuszania programu SQLite FK](#pragma) | Małe      |
| [Microsoft. EntityFrameworkCore. sqlite teraz zależy od SQLitePCLRaw. bundle_e_sqlite3](#sqlite3) | Małe      |
| [Wartości identyfikatorów GUID są teraz przechowywane jako tekst na komputerze SQLite](#guid) | Małe      |
| [Wartości char są teraz przechowywane jako tekst na komputerze SQLite](#char) | Małe      |
| [Identyfikatory migracji są teraz generowane przy użyciu kalendarza niezmiennej kultury](#migid) | Małe      |
| [Informacje o rozszerzeniu/metadane zostały usunięte z IDbContextOptionsExtension](#xinfo) | Małe      |
| [Zmieniono nazwę LogQueryPossibleExceptionWithAggregateOperator](#lqpe) | Małe      |
| [Wyjaśnienie interfejsu API nazw ograniczeń klucza obcego](#clarify) | Małe      |
| [IRelationalDatabaseCreator. HasTables/HasTablesAsync zostały udostępnione publicznie](#irdc2) | Małe      |
| [Microsoft. EntityFrameworkCore. Design jest teraz pakietem DevelopmentDependency](#dip) | Małe      |
| [SQLitePCL. Raw Zaktualizowano do wersji 2.0.0](#SQLitePCL) | Małe      |
| [NetTopologySuite Zaktualizowano do wersji 2.0.0](#NetTopologySuite) | Małe      |
| [Należy skonfigurować wiele niejednoznacznych relacji odwołujących się do siebie.](#mersa) | Małe      |

### <a name="linq-queries-are-no-longer-evaluated-on-the-client"></a>Zapytania LINQ nie są już oceniane na kliencie

[Problemy ze śledzeniem #14935](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[Zobacz też problem #12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 4.

**Stare zachowanie**

Przed 3,0, gdy EF Core nie mógł skonwertować wyrażenia, które było częścią zapytania na SQL lub parametr, automatycznie ocenia wyrażenie na kliencie.
Domyślnie Ocena klienta potencjalnie kosztownych wyrażeń wyzwala jedynie ostrzeżenie.

**Nowe zachowanie**

Począwszy od 3,0, EF Core zezwala tylko na wyrażenia w projekcji najwyższego poziomu ( `Select()` ostatnie wywołanie zapytania) do oceny na kliencie.
Jeśli wyrażenia w dowolnej innej części zapytania nie mogą być konwertowane na SQL lub parametr, zostanie zgłoszony wyjątek.

**Zalet**

Automatyczna Ocena klienta umożliwia wykonywanie wielu zapytań, nawet jeśli nie można przetłumaczyć ważnych części tych zapytań.
Takie zachowanie może spowodować nieoczekiwane i potencjalnie szkodliwe zachowanie, które może stać się widoczne tylko w środowisku produkcyjnym.
Na przykład warunek w `Where()` wywołaniu, którego nie można przetłumaczyć, może spowodować, że wszystkie wiersze z tabeli mają być transferowane z serwera bazy danych, oraz filtr, który ma zostać zastosowany na kliencie.
Ta sytuacja może być łatwo niewykrywalna, jeśli tabela zawiera tylko kilka wierszy do opracowania, ale gdy aplikacja zostanie przeniesiona do produkcji, a tabela może zawierać miliony wierszy.
Ostrzeżenia dotyczące oceny klienta okazały się także łatwe do ignorowania podczas opracowywania.

W związku z tym automatyczne szacowanie klienta może prowadzić do problemów, w których poprawianie tłumaczenia zapytań dla określonych wyrażeń powodowało niezamierzone zmiany między wersjami.

**Środki zaradcze**

Jeśli nie można w pełni przetłumaczyć zapytania, należy ponownie napisać zapytanie w formularzu, który można przetłumaczyć lub użyć `AsEnumerable()`, `ToList()`lub podobnie jak jawnie przenieść dane z powrotem do klienta, na którym można następnie przetworzyć je za pomocą LINQ-to-Objects.

<a name="netstandard21"></a>
### <a name="ef-core-30-targets-net-standard-21-rather-than-net-standard-20"></a>EF Core 3,0 cele .NET Standard 2,1, a nie .NET Standard 2,0

[Śledzenie problemu #15498](https://github.com/aspnet/EntityFrameworkCore/issues/15498)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 7.

**Stare zachowanie**

Przed 3,0, EF Core kierowany .NET Standard 2,0 i będzie działać na wszystkich platformach obsługujących ten standard, w tym .NET Framework.

**Nowe zachowanie**

Począwszy od 3,0, EF Core cele .NET Standard 2,1 i zostaną uruchomione na wszystkich platformach obsługujących ten standard. Nie obejmuje to .NET Framework.

**Zalet**

Jest to część strategii strategicznej w technologiach .NET, która umożliwia skoncentrowanie się na oprogramowaniu .NET Core i innych nowoczesnych platformach .NET, takich jak Xamarin.

**Środki zaradcze**

Rozważ przeniesienie do nowoczesnej platformy .NET. Jeśli nie jest to możliwe, należy nadal używać EF Core 2,1 lub EF Core 2,2, w których są obsługiwane .NET Framework.

<a name="no-longer"></a>
### <a name="entity-framework-core-is-no-longer-part-of-the-aspnet-core-shared-framework"></a>Entity Framework Core nie jest już częścią ASP.NET Core współdzielonej struktury

[Śledzenie anonsów problemów # 325](https://github.com/aspnet/Announcements/issues/325)

Ta zmiana została wprowadzona w ASP.NET Core 3,0 — wersja zapoznawcza 1. 

**Stare zachowanie**

Przed ASP.NET Core 3,0, po dodaniu odwołania do pakietu do `Microsoft.AspNetCore.App` lub `Microsoft.AspNetCore.All`, będzie ona obejmować EF Core i niektórych dostawców danych EF Core, takich jak Dostawca SQL Server.

**Nowe zachowanie**

Począwszy od 3,0, ASP.NET Core współdzielona architektura nie obejmuje EF Core ani żadnych dostawców danych EF Core.

**Zalet**

Przed wprowadzeniem tej zmiany należy uzyskać EF Core wymagane różne kroki, w zależności od tego, czy aplikacja ma ASP.NET Core i SQL Server czy nie. Ponadto uaktualnienie ASP.NET Core wymuszone uaktualnienie EF Core i dostawcy SQL Server, które nie zawsze są pożądane.

W przypadku tej zmiany środowisko pobierania EF Core jest takie samo dla wszystkich dostawców, obsługiwane implementacje .NET i typy aplikacji.
Deweloperzy mogą również kontrolować dokładnie, kiedy EF Core i EF Core dostawców danych.

**Środki zaradcze**

Aby użyć EF Core w aplikacji ASP.NET Core 3,0 lub innej obsługiwanej aplikacji, należy jawnie dodać odwołanie do pakietu do dostawcy bazy danych EF Core, który będzie używany przez aplikację.

<a name="dotnet-ef"></a>
### <a name="the-ef-core-command-line-tool-dotnet-ef-is-no-longer-part-of-the-net-core-sdk"></a>Narzędzie wiersza polecenia EF Core, dotnet EF, nie jest już częścią zestaw .NET Core SDK

[Śledzenie problemu #14016](https://github.com/aspnet/EntityFrameworkCore/issues/14016)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 4 i odpowiadająca jej wersja zestaw .NET Core SDK.

**Stare zachowanie**

Przed 3,0 `dotnet ef` narzędzie zostało dołączone do zestaw .NET Core SDK i było łatwo dostępne do użycia z wiersza polecenia z dowolnego projektu bez konieczności wykonywania dodatkowych czynności. 

**Nowe zachowanie**

Począwszy od 3,0, zestaw SDK platformy .NET nie zawiera `dotnet ef` narzędzia, więc zanim będzie można go użyć, należy jawnie zainstalować go jako narzędzie lokalne lub globalne. 

**Zalet**

Ta zmiana umożliwia dystrybuowanie i aktualizowanie `dotnet ef` jako zwykłego narzędzia interfejsu wiersza polecenia platformy .NET na platformie NuGet, spójnie z faktem, że EF Core 3,0 jest również zawsze dystrybuowana jako pakiet NuGet.

**Środki zaradcze**

Aby móc zarządzać migracjami lub szkieletem a `DbContext`, zainstaluj `dotnet-ef` program jako narzędzie globalne:

  ``` console
    $ dotnet tool install --global dotnet-ef --version 3.0.0-*
  ```

Można również uzyskać narzędzie lokalne podczas przywracania zależności projektu, który deklaruje go jako zależność narzędzia przy użyciu [pliku manifestu narzędzia](https://github.com/dotnet/cli/issues/10288).

<a name="fromsql"></a>
### <a name="fromsql-executesql-and-executesqlasync-have-been-renamed"></a>Nazwy Z tabel, ExecuteSql by i ExecuteSqlAsync

[Śledzenie problemu #10996](https://github.com/aspnet/EntityFrameworkCore/issues/10996)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 4.

**Stare zachowanie**

Przed EF Core 3,0 te nazwy metod były przeciążone w celu pracy z zwykłym ciągiem lub ciągiem, który powinien zostać interpolowany do SQL i parametrów.

**Nowe zachowanie**

Począwszy od EF Core 3,0, użyj `FromSqlRaw`, `ExecuteSqlRaw`, i `ExecuteSqlRawAsync` , aby utworzyć zapytanie parametryczne, gdzie parametry są przesyłane niezależnie od ciągu zapytania.
Przykład:

```C#
context.Products.FromSqlRaw(
    "SELECT * FROM Products WHERE Name = {0}",
    product.Name);
```

Użyj `FromSqlInterpolated`, `ExecuteSqlInterpolated`, i`ExecuteSqlInterpolatedAsync` , aby utworzyć zapytanie parametryczne, gdzie parametry są przenoszone w ramach interpolowanego ciągu zapytania.
Przykład:

```C#
context.Products.FromSqlInterpolated(
    $"SELECT * FROM Products WHERE Name = {product.Name}");
```

Należy zauważyć, że oba zapytania powyżej spowodują utworzenie tego samego sparametryzowanego kodu SQL z tymi samymi parametrami SQL.

**Zalet**

Przeciążenia metod, takie jak to, ułatwiają przypadkowe wywoływanie metody nieprzetworzonego ciągu, gdy zamiarem było wywołanie interpolowanej metody String i odwrotnie.
Może to spowodować, że kwerendy nie są sparametryzowane, gdy powinny one być.

**Środki zaradcze**

Przełącz, aby użyć nowych nazw metod.

<a name="fromsql"></a>

### <a name="fromsql-methods-can-only-be-specified-on-query-roots"></a>Metody Z tabel można określić tylko dla katalogów głównych zapytań

[Śledzenie problemu #15704](https://github.com/aspnet/EntityFrameworkCore/issues/15704)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 6.

**Stare zachowanie**

Przed EF Core 3,0, `FromSql` Metoda może być określona w dowolnym miejscu zapytania.

**Nowe zachowanie**

Począwszy od EF Core 3,0, `FromSqlRaw` nowe i `FromSqlInterpolated` metody (zastępujące `FromSql`) można określić tylko dla katalogów głównych `DbSet<>`zapytań, tj. bezpośrednio na. Próba określenia ich w innym miejscu spowoduje błąd kompilacji.

**Zalet**

Określenie `FromSql` dowolnego miejsca, w którym `DbSet` nie ma żadnego dodanego znaczenia ani dodanej wartości, może spowodować niejednoznaczność w niektórych scenariuszach.

**Środki zaradcze**

`FromSql`wywołania należy przenieść, aby znajdowały się bezpośrednio w, `DbSet` do których mają zastosowanie.

<a name="notrackingresolution"></a>
### <a name="no-tracking-queries-no-longer-perform-identity-resolution"></a>Zapytania nie śledzące już nie wykonują rozpoznawania tożsamości

[Śledzenie problemu #13518](https://github.com/aspnet/EntityFrameworkCore/issues/13518)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 6.

**Stare zachowanie**

Przed EF Core 3,0, to to samo wystąpienie jednostki będzie używane dla każdego wystąpienia jednostki o danym typie i IDENTYFIKATORze. Jest to zgodne z zachowaniem śledzenia zapytań. Na przykład to zapytanie:

```C#
var results = context.Products.Include(e => e.Category).AsNoTracking().ToList();
```
zwróci to samo `Category` wystąpienie dla każdego `Product` , które jest skojarzone z daną kategorią.

**Nowe zachowanie**

Począwszy od EF Core 3,0, zostaną utworzone inne wystąpienia jednostek, gdy jednostka o danym typie i IDENTYFIKATORze zostanie napotkana w różnych miejscach na zwracanym wykresie. Na przykład zapytanie powyżej zwróci teraz nowe `Category` wystąpienie dla każdego z nich `Product` , nawet jeśli dwa produkty są skojarzone z tą samą kategorią.

**Zalet**

Rozpoznawanie tożsamości (oznacza to, że jednostka ma ten sam typ i identyfikator, jak poprzednio napotkana jednostka), dodaje dodatkowe obciążenie związane z wydajnością i pamięcią. Zwykle jest to przyczyną tego, że w pierwszym miejscu nie są używane zapytania śledzące. Ponadto, chociaż rozpoznawanie tożsamości może być przydatne, nie jest to konieczne, jeśli jednostki mają być serializowane i wysyłane do klienta, który jest typowy w przypadku zapytań bez śledzenia.

**Środki zaradcze**

Użyj zapytania śledzenia, jeśli jest wymagane rozpoznawanie tożsamości.

<a name="qe"></a>

### <a name="query-execution-is-logged-at-debug-level-reverted"></a>~~Wykonywanie zapytania jest rejestrowane na poziomie debugowania~~ Przywrócono

[Śledzenie problemu #14523](https://github.com/aspnet/EntityFrameworkCore/issues/14523)

Ta zmiana jest przywracana w EF Core 3,0 — wersja zapoznawcza 7.

Ta zmiana została przywrócona, ponieważ nowa konfiguracja w EF Core 3,0 umożliwia określenie poziomu dziennika dla każdego zdarzenia, które ma zostać określone przez aplikację. Na przykład, aby przełączyć rejestrowanie danych SQL do `Debug`, należy jawnie skonfigurować poziom w `OnConfiguring` lub `AddDbContext`:
```C#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(connectionString)
        .ConfigureWarnings(c => c.Log((RelationalEventId.CommandExecuting, LogLevel.Debug)));
```

<a name="tkv"></a>

### <a name="temporary-key-values-are-no-longer-set-onto-entity-instances"></a>Wartości klucza tymczasowego nie są już ustawione na wystąpienia jednostek

[Śledzenie problemu #12378](https://github.com/aspnet/EntityFrameworkCore/issues/12378)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 2.

**Stare zachowanie**

Przed EF Core 3,0 wartości tymczasowe zostały przypisane do wszystkich właściwości klucza, które później będą miały wartość rzeczywistą wygenerowaną przez bazę danych.
Zwykle te wartości tymczasowe były dużymi liczbami ujemnymi.

**Nowe zachowanie**

Począwszy od 3,0, EF Core przechowuje wartość klucza tymczasowego jako część informacji śledzenia jednostki i pozostawia samą właściwość klucza bez zmian.

**Zalet**

Ta zmiana została wprowadzona w celu zapobieżenia błędnemu utracie wartości kluczy tymczasowych, gdy jednostka, która została wcześniej prześledzona przez niektóre `DbContext` wystąpienie, jest przenoszona do innego `DbContext` wystąpienia. 

**Środki zaradcze**

Aplikacje, które przypisują wartości kluczy podstawowych do kluczy obcych, aby utworzyć skojarzenia między jednostkami, mogą zależeć od starego zachowania, jeśli klucze podstawowe są generowane przez magazyn i `Added` należą do jednostek w stanie.
Można to uniknąć przez:
* Nie używa kluczy generowanych przez magazyn.
* Ustawianie właściwości nawigacji na relacje formularzy zamiast ustawiania wartości kluczy obcych.
* Uzyskaj rzeczywiste wartości klucza tymczasowego z informacji śledzenia jednostki.
Na przykład zwróci `context.Entry(blog).Property(e => e.Id).CurrentValue` wartość tymczasową nawet wtedy, gdy `blog.Id` sama nie została ustawiona.

<a name="dc"></a>

### <a name="detectchanges-honors-store-generated-key-values"></a>DetectChanges uznaje wartości klucza generowane przez magazyn

[Śledzenie problemu #14616](https://github.com/aspnet/EntityFrameworkCore/issues/14616)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 3.

**Stare zachowanie**

Przed EF Core 3,0, Nieśledzona jednostka znaleziona przez `DetectChanges` zostałaby śledzona `Added` w stanie i wstawiona jako nowy wiersz, gdy `SaveChanges` zostanie wywołana.

**Nowe zachowanie**

Począwszy od EF Core 3,0, jeśli jednostka używa wygenerowanych wartości klucza i ustawiono wartość klucza, jednostka będzie śledzona w `Modified` stanie.
Oznacza to, że wiersz dla jednostki jest założono i zostanie zaktualizowany po `SaveChanges` wywołaniu.
Jeśli wartość klucza nie jest ustawiona lub jeśli typ jednostki nie korzysta z wygenerowanych kluczy, Nowa jednostka będzie nadal śledzona tak `Added` jak w poprzednich wersjach.

**Zalet**

Ta zmiana została wprowadzona w celu ułatwienia i spójności działania z odłączonymi wykresami jednostek przy użyciu kluczy generowanych przez magazyn.

**Środki zaradcze**

Ta zmiana może przerwać aplikację, jeśli typ jednostki jest skonfigurowany do korzystania z wygenerowanych kluczy, ale wartości kluczy są jawnie ustawiane dla nowych wystąpień.
Poprawka ma jawnie skonfigurować właściwości klucza, aby nie używać wygenerowanych wartości.
Na przykład za pomocą interfejsu API Fluent:

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedNever();
```

Lub z adnotacjami danych:

```C#
[DatabaseGenerated(DatabaseGeneratedOption.None)]
public string Id { get; set; }
```
<a name="cascade"></a>
### <a name="cascade-deletions-now-happen-immediately-by-default"></a>Usuwanie kaskadowe jest teraz wykonywane natychmiast domyślnie

[Śledzenie problemu #10114](https://github.com/aspnet/EntityFrameworkCore/issues/10114)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 3.

**Stare zachowanie**

Przed 3,0 EF Core zastosowane akcje kaskadowe (usunięcie jednostek zależnych w przypadku usunięcia wymaganego podmiotu zabezpieczeń lub odłączenie relacji do wymaganego podmiotu zabezpieczeń) nie nastąpiło do momentu wywołania metody SaveChanges.

**Nowe zachowanie**

Począwszy od 3,0, EF Core stosuje akcje kaskadowe zaraz po wykryciu warunku wyzwalania.
Na przykład wywołanie `context.Remove()` usunięcia podmiotu zabezpieczeń spowoduje, że wszystkie śledzone powiązane zależności są również ustawione na `Deleted` natychmiast.

**Zalet**

Ta zmiana została wprowadzona w celu poprawy środowiska związanego z scenariuszami powiązań danych i inspekcji, gdzie ważne jest, aby zrozumieć, które jednostki zostaną usunięte _przed_ `SaveChanges` wywołaniem.

**Środki zaradcze**

Poprzednie zachowanie można przywrócić za pomocą ustawień na stronie `context.ChangedTracker`.
Przykład:

```C#
context.ChangeTracker.CascadeDeleteTiming = CascadeTiming.OnSaveChanges;
context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;
```
<a name="deletebehavior"></a>
### <a name="deletebehaviorrestrict-has-cleaner-semantics"></a>DeleteBehavior. ograniczanie ma semantykę oczyszczarki

[Śledzenie problemu #12661](https://github.com/aspnet/EntityFrameworkCore/issues/12661)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 5.

**Stare zachowanie**

Przed 3,0, `DeleteBehavior.Restrict` utworzono klucze obce w bazie danych z `Restrict` semantyką, ale również zmieniono wewnętrzną korektę w nieoczywisty sposób.

**Nowe zachowanie**

Począwszy od 3,0, `DeleteBehavior.Restrict` zapewnia, że klucze obce są tworzone `Restrict` z semantyką--to nie, bez kaskad; throw przy naruszeniu ograniczenia — bez również wpływu na wewnętrzną naprawę EF.

**Zalet**

Ta zmiana została wprowadzona w celu usprawnienia korzystania `DeleteBehavior` z programu w intuicyjny sposób, bez nieoczekiwanych efektów ubocznych.

**Środki zaradcze**

Poprzednie zachowanie można przywrócić za pomocą polecenia `DeleteBehavior.ClientNoAction`.

<a name="qt"></a>
### <a name="query-types-are-consolidated-with-entity-types"></a>Typy zapytań są konsolidowane z typami jednostek

[Śledzenie problemu #14194](https://github.com/aspnet/EntityFrameworkCore/issues/14194)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 3.

**Stare zachowanie**

Przed EF Core 3,0 [typy zapytań](xref:core/modeling/query-types) były sposobem na wykonywanie zapytań dotyczących danych, które nie definiują klucza podstawowego w uporządkowany sposób.
Oznacza to, że typ zapytania został użyty do mapowania typów jednostek bez kluczy (prawdopodobnie z widoku, ale prawdopodobnie z tabeli), podczas gdy jest używany zwykły typ jednostki, gdy klucz był dostępny (prawdopodobnie z tabeli, ale prawdopodobnie z widoku).

**Nowe zachowanie**

Typ zapytania będzie teraz tylko typem jednostki bez klucza podstawowego.
Typy jednostek o niższym stopniu mają takie same funkcje jak typy zapytań w poprzednich wersjach.

**Zalet**

Ta zmiana została wprowadzona w celu zmniejszenia liczby pomyłek w odznaczeniu do typów zapytań.
W odróżnieniu od tego są one typami jednostek i są one tylko do odczytu z tego powodu, ale nie powinny być używane tylko wtedy, gdy typ jednostki musi być tylko do odczytu.
Podobnie często są one zamapowane na widoki, ale jest to tylko dlatego, że widoki często nie definiują kluczy.

**Środki zaradcze**

Następujące części interfejsu API są obecnie przestarzałe:
* **`ModelBuilder.Query<>()`** — Zamiast `ModelBuilder.Entity<>().HasNoKey()` tego należy wywołać, aby oznaczyć typ jednostki jako bez kluczy.
Ta sytuacja nadal nie zostanie skonfigurowana zgodnie z Konwencją, aby uniknąć Niepoważnej konfiguracji, gdy jest oczekiwany klucz podstawowy, ale nie jest zgodna z Konwencją.
* **`DbQuery<>`** — Zamiast `DbSet<>` tego należy używać.
* **`DbContext.Query<>()`** — Zamiast `DbContext.Set<>()` tego należy używać.

<a name="config"></a>
### <a name="configuration-api-for-owned-type-relationships-has-changed"></a>Interfejs API konfiguracji dla relacji typu posiadanego został zmieniony

[Śledzenie](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
problemów ze śledzeniem #12444[#9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
śledzenia problemów[#14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 3.

**Stare zachowanie**

Przed EF Core 3,0 konfiguracja powiązanej relacji została wykonana bezpośrednio po `OwnsOne` wywołaniu lub. `OwnsMany` 

**Nowe zachowanie**

Począwszy od EF Core 3,0, istnieje teraz interfejs API Fluent, aby skonfigurować właściwość nawigacji dla właściciela przy użyciu `WithOwner()`.
Na przykład:

```C#
modelBuilder.Entity<Order>.OwnsOne(e => e.Details).WithOwner(e => e.Order);
```

Konfiguracja odnosząca się do relacji między właścicielem i właścicielem powinna teraz być łańcuchem `WithOwner()` po podobnym sposobie, jak inne relacje są skonfigurowane.
Mimo że konfiguracja dla samego samego typu jest nadal łańcuchem `OwnsOne()/OwnsMany()`.
Przykład:

```C#
modelBuilder.Entity<Order>.OwnsOne(e => e.Details, eb =>
    {
        eb.WithOwner()
            .HasForeignKey(e => e.AlternateId)
            .HasConstraintName("FK_OrderDetails");
            
        eb.ToTable("OrderDetails");
        eb.HasKey(e => e.AlternateId);
        eb.HasIndex(e => e.Id);

        eb.HasOne(e => e.Customer).WithOne();

        eb.HasData(
            new OrderDetails
            {
                AlternateId = 1,
                Id = -1
            });
    });
```

Dodatkowo wywoływanie `Entity()`,, `Set()` lub z obiektem docelowym typu, `HasOne()`spowoduje teraz zgłoszenie wyjątku.

**Zalet**

Ta zmiana została wprowadzona w celu utworzenia oddzielenia oczyszczarki między konfiguracją samego typu i _relacją z_ typem będącym własnością.
To z kolei eliminuje niejednoznaczność i pomyłek wokół metod, `HasForeignKey`takich jak.

**Środki zaradcze**

Zmień konfigurację relacji typu będącego własnością, aby użyć nowej powierzchni interfejsu API, jak pokazano w powyższym przykładzie.

<a name="de"></a>

### <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a>Jednostki zależne współużytkujące tabelę z podmiotem zabezpieczeń są teraz opcjonalne

[Śledzenie problemu #9005](https://github.com/aspnet/EntityFrameworkCore/issues/9005)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 4.

**Stare zachowanie**

Rozważmy następujący model:
```C#
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public OrderDetails Details { get; set; }
}

public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}
```
Przed EF Core 3,0, jeśli `OrderDetails` jest `Order` własnością lub jawnie zmapowana do tej samej tabeli `OrderDetails` , wystąpienie jest zawsze wymagane podczas dodawania nowego `Order`elementu.


**Nowe zachowanie**

Począwszy od 3,0, EF Core umożliwia dodanie `Order` , `OrderDetails` bez `OrderDetails` i mapuje wszystkie właściwości, z wyjątkiem klucza podstawowego do wartości null.
Podczas wykonywania zapytania o zestawy `OrderDetails` EF Core `null` do, jeśli którekolwiek z jego wymaganych właściwości nie ma wartości lub jeśli nie ma żadnych wymaganych właściwości poza kluczem podstawowym i wszystkie właściwości są `null`.

**Środki zaradcze**

Jeśli model ma zależeć do udostępniania tabeli dla wszystkich opcjonalnych kolumn, ale Nawigacja wskazująca, że nie jest oczekiwana `null` , aplikacja powinna zostać zmodyfikowana, aby obsługiwać przypadki, gdy Nawigacja `null`jest. Jeśli nie jest to możliwe, należy dodać wymaganą właściwość do typu jednostki lub co najmniej jedna właściwość powinna mieć`null` przypisaną do niej inną wartość.

<a name="aes"></a>

### <a name="all-entities-sharing-a-table-with-a-concurrency-token-column-have-to-map-it-to-a-property"></a>Wszystkie jednostki współużytkujące tabelę z kolumną Token współbieżności muszą mapować ją na Właściwość

[Śledzenie problemu #14154](https://github.com/aspnet/EntityFrameworkCore/issues/14154)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 4.

**Stare zachowanie**

Rozważmy następujący model:
```C#
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public byte[] Version { get; set; }
    public OrderDetails Details { get; set; }
}

public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}

protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Order>()
        .Property(o => o.Version).IsRowVersion().HasColumnName("Version");
}
```
Przed EF Core 3,0, jeśli `OrderDetails` jest `Order` własnością lub jawnie zmapowana do tej samej tabeli, aktualizacja tylko `OrderDetails` nie spowoduje aktualizacji `Version` wartości na kliencie, a kolejna aktualizacja zakończy się niepowodzeniem.


**Nowe zachowanie**

Począwszy od 3,0, EF Core propaguje nową `Version` wartość do `Order` , jeśli jest właścicielem `OrderDetails`. W przeciwnym razie wyjątek jest zgłaszany podczas walidacji modelu.

**Zalet**

Ta zmiana została wprowadzona w celu uniknięcia nieodświeżonej wartości tokenu współbieżności, gdy jest aktualizowana tylko jedna z jednostek zamapowanych na tę samą tabelę.

**Środki zaradcze**

Wszystkie jednostki współużytkujące tabelę muszą zawierać właściwość, która jest mapowana na kolumnę Token współbieżności. Możliwe jest utworzenie jednego w tle:
```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<OrderDetails>()
        .Property<byte[]>("Version").IsRowVersion().HasColumnName("Version");
}
```

<a name="ip"></a>

### <a name="inherited-properties-from-unmapped-types-are-now-mapped-to-a-single-column-for-all-derived-types"></a>Dziedziczone właściwości z niemapowanych typów są teraz mapowane na pojedynczą kolumnę dla wszystkich typów pochodnych

[Śledzenie problemu #13998](https://github.com/aspnet/EntityFrameworkCore/issues/13998)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 4.

**Stare zachowanie**

Rozważmy następujący model:
```C#
public abstract class EntityBase
{
    public int Id { get; set; }
}

public abstract class OrderBase : EntityBase
{
    public int ShippingAddress { get; set; }
}

public class BulkOrder : OrderBase
{
}

public class Order : OrderBase
{
}

protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Ignore<OrderBase>();
    modelBuilder.Entity<EntityBase>();
    modelBuilder.Entity<BulkOrder>();
    modelBuilder.Entity<Order>();
}
```

Przed EF Core 3,0 `ShippingAddress` Właściwość zostałaby zamapowana do oddzielnych kolumn dla `BulkOrder` i `Order` domyślnie.

**Nowe zachowanie**

Począwszy od 3,0, EF Core tworzy tylko jedną kolumnę dla `ShippingAddress`.

**Zalet**

Stary behavoir był nieoczekiwany.

**Środki zaradcze**

Właściwość nadal może być jawnie zmapowana na oddzielną kolumnę w typach pochodnych:

```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Ignore<OrderBase>();
    modelBuilder.Entity<EntityBase>();
    modelBuilder.Entity<BulkOrder>()
        .Property(o => o.ShippingAddress).HasColumnName("BulkShippingAddress");
    modelBuilder.Entity<Order>()
        .Property(o => o.ShippingAddress).HasColumnName("ShippingAddress");
}
```

<a name="fkp"></a>

### <a name="the-foreign-key-property-convention-no-longer-matches-same-name-as-the-principal-property"></a>Konwencja właściwości klucza obcego nie jest już zgodna z tą samą nazwą co właściwość podmiotu zabezpieczeń

[Śledzenie problemu #13274](https://github.com/aspnet/EntityFrameworkCore/issues/13274)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 3.

**Stare zachowanie**

Rozważmy następujący model:
```C#
public class Customer
{
    public int CustomerId { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
}
```
Przed EF Core 3,0, `CustomerId` właściwość będzie używana dla klucza obcego według Konwencji.
Jeśli `Order` jednak jest typem będącym własnością, to `CustomerId` również klucz podstawowy i zwykle nie jest to oczekiwane.

**Nowe zachowanie**

Począwszy od 3,0, EF Core nie próbuje użyć właściwości kluczy obcych według Konwencji, jeśli mają taką samą nazwę jak właściwość podmiotu zabezpieczeń.
Nazwa typu podmiotu zabezpieczeń połączona z nazwą właściwości głównej i nazwa nawigacji połączonej ze wzorcami nazw właściwości głównych są nadal dopasowane.
Na przykład:

```C#
public class Customer
{
    public int Id { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
}
```

```C#
public class Customer
{
    public int Id { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int BuyerId { get; set; }
    public Customer Buyer { get; set; }
}
```

**Zalet**

Ta zmiana została wprowadzona w celu uniknięcia błędnego zdefiniowania właściwości klucza podstawowego w typie będącym własnością.

**Środki zaradcze**

Jeśli właściwość była zamierzona jako klucz obcy i w związku z tym jest częścią klucza podstawowego, należy ją jawnie skonfigurować.

<a name="dbc"></a>

### <a name="database-connection-is-now-closed-if-not-used-anymore-before-the-transactionscope-has-been-completed"></a>Połączenie z bazą danych jest teraz zamknięte, jeśli nie jest używane już przed ukończeniem elementu TransactionScope

[Śledzenie problemu #14218](https://github.com/aspnet/EntityFrameworkCore/issues/14218)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 4.

**Stare zachowanie**

Przed EF Core 3,0, jeśli kontekst otwiera połączenie wewnątrz `TransactionScope`, połączenie pozostaje otwarte, gdy bieżące `TransactionScope` jest aktywne.

```C#
using (new TransactionScope())
{
    using (AdventureWorks context = new AdventureWorks())
    {
        context.ProductCategories.Add(new ProductCategory());
        context.SaveChanges();

        // Old behavior: Connection is still open at this point
        
        var categories = context.ProductCategories().ToList();
    }
}
```

**Nowe zachowanie**

Począwszy od 3,0, EF Core zamyka połączenie zaraz po jego zakończeniu.

**Zalet**

Ta zmiana umożliwia używanie wielu kontekstów w tym samym `TransactionScope`. Nowe zachowanie jest również zgodne z EF6.

**Środki zaradcze**

Jeśli połączenie musi pozostać otwarte jawne wywołanie w celu `OpenConnection()` upewnienia się, że EF Core nie zamyka go przedwcześnie:

```C#
using (new TransactionScope())
{
    using (AdventureWorks context = new AdventureWorks())
    {
        context.Database.OpenConnection();
        context.ProductCategories.Add(new ProductCategory());
        context.SaveChanges();
        
        var categories = context.ProductCategories().ToList();
        context.Database.CloseConnection();
    }
}
```

<a name="each"></a>

### <a name="each-property-uses-independent-in-memory-integer-key-generation"></a>Każda właściwość używa niezależnej generacji klucza w pamięci

[Śledzenie problemu #6872](https://github.com/aspnet/EntityFrameworkCore/issues/6872)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 4.

**Stare zachowanie**

Przed EF Core 3,0, jeden współużytkowany Generator wartości został użyty dla wszystkich właściwości klucza liczby całkowitej w pamięci.

**Nowe zachowanie**

Począwszy od EF Core 3,0, Każda właściwość klucza Integer pobiera własny Generator wartości podczas korzystania z bazy danych w pamięci.
Ponadto, jeśli baza danych została usunięta, generowanie kluczy jest resetowane dla wszystkich tabel.

**Zalet**

Ta zmiana została wprowadzona w celu dopasowania generowania kluczy w pamięci do generowania klucza rzeczywistej bazy danych i zwiększenia możliwości odizolowania testów od siebie podczas korzystania z bazy danych w pamięci.

**Środki zaradcze**

Może to spowodować przerwanie aplikacji, która polega na określeniu określonych wartości kluczy w pamięci.
Należy rozważyć zamiast nie polegać na określonych wartościach klucza lub zaktualizować w celu dopasowania go do nowego zachowania.

### <a name="backing-fields-are-used-by-default"></a>Pola zapasowe są używane domyślnie

[Śledzenie problemu #12430](https://github.com/aspnet/EntityFrameworkCore/issues/12430)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 2.

**Stare zachowanie**

Przed 3,0, nawet jeśli pole zapasowe właściwości było znane, EF Core nadal jest domyślnie odczytywane i zapisywane wartości właściwości przy użyciu metody pobierającej i ustawiającej właściwości.
Wyjątkiem jest wykonanie zapytania, gdzie pole zapasowe zostanie ustawione bezpośrednio, jeśli jest znane.

**Nowe zachowanie**

Począwszy od EF Core 3,0, jeśli jest znane pole zapasowe dla właściwości, wówczas EF Core będzie zawsze odczytywać i zapisywać tę właściwość przy użyciu pola zapasowego.
Może to spowodować przerwanie działania aplikacji, jeśli aplikacja jest zależna od dodatkowych zachowań zakodowanych w metodach pobierających lub setter.

**Zalet**

Ta zmiana została wprowadzona w celu uniemożliwienia EF Core błędnego wyzwalania logiki biznesowej podczas wykonywania operacji bazy danych obejmujących te jednostki.

**Środki zaradcze**

Zachowanie przed 3,0 może zostać przywrócone przez konfigurację trybu `ModelBuilder`dostępu do właściwości.
Na przykład:

```C#
modelBuilder.UsePropertyAccessMode(PropertyAccessMode.PreferFieldDuringConstruction);
```

### <a name="throw-if-multiple-compatible-backing-fields-are-found"></a>Zgłoś, czy znaleziono wiele zgodnych pól zapasowych

[Śledzenie problemu #12523](https://github.com/aspnet/EntityFrameworkCore/issues/12523)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 4.

**Stare zachowanie**

Przed EF Core 3,0, jeśli wiele pól jest zgodnych z regułami dotyczącymi znajdowania pola zapasowego właściwości, jedno pole zostanie wybrane na podstawie pewnej kolejności pierwszeństwa.
Może to spowodować, że błędne pole ma być używane w niejednoznacznych przypadkach.

**Nowe zachowanie**

Począwszy od EF Core 3,0, jeśli wiele pól jest dopasowanych do tej samej właściwości, zostanie zgłoszony wyjątek.

**Zalet**

Ta zmiana została wprowadzona w celu uniknięcia cichego używania jednego pola na inny, gdy tylko jeden z nich może być poprawny.

**Środki zaradcze**

Właściwości z niejednoznacznymi polami zapasowymi muszą mieć jawnie określone pole do użycia.
Na przykład za pomocą interfejsu API Fluent:

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .HasField("_id");
```

### <a name="field-only-property-names-should-match-the-field-name"></a>Nazwy właściwości tylko dla pól powinny być zgodne z nazwą pola

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 4.

**Stare zachowanie**

Przed EF Core 3,0, właściwość może być określona przez wartość ciągu i jeśli żadna właściwość o tej nazwie nie została znaleziona w typie CLR, EF Core spróbuje dopasować ją do pola przy użyciu reguł Konwencji.
```C#
private class Blog
{
    private int _id;
    public string Name { get; set; }
}
```
```C#
modelBuilder
    .Entity<Blog>()
    .Property("Id");
```

**Nowe zachowanie**

Począwszy od EF Core 3,0, właściwość tylko do pola musi dokładnie odpowiadać nazwie pola.

```C#
modelBuilder
    .Entity<Blog>()
    .Property("_id");
```

**Zalet**

Ta zmiana została podjęta, aby uniknąć używania tego samego pola dla dwóch właściwości o nazwie podobnie, ale również reguł dopasowania dla właściwości, które są takie same jak w przypadku właściwości zamapowanych na właściwości środowiska CLR.

**Środki zaradcze**

Właściwości "Only" muszą mieć taką samą nazwę jak pole, do którego są mapowane.
W przyszłej wersji EF Core po 3,0 planujemy ponowne włączenie jawnie skonfigurowanej nazwy pola, która jest inna niż nazwa właściwości (zobacz problem [#15307](https://github.com/aspnet/EntityFrameworkCore/issues/15307)):

```C#
modelBuilder
    .Entity<Blog>()
    .Property("Id")
    .HasField("_id");
```

<a name="adddbc"></a>

### <a name="adddbcontextadddbcontextpool-no-longer-call-addlogging-and-addmemorycache"></a>AddDbContext/AddDbContextPool nie wywołuje już metody addlogging i AddMemoryCache

[Śledzenie problemu #14756](https://github.com/aspnet/EntityFrameworkCore/issues/14756)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 4.

**Stare zachowanie**

Przed `AddDbContext` EF Core 3,0, wywoływanie `AddDbContextPool` lub zarejestrowanie usługi rejestrowania i pamięci podręcznej w usłudze D. I przez wywołania funkcji [addlogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) i [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).

**Nowe zachowanie**

Począwszy od EF Core 3,0 `AddDbContext` i `AddDbContextPool` nie będą już rejestrować tych usług przy użyciu iniekcji zależności (di).

**Zalet**

EF Core 3,0 nie wymaga, aby te usługi były w zakresie DI kontenera aplikacji. Jeśli `ILoggerFactory` jednak program jest zarejestrowany w kontenerze di aplikacji, będzie nadal używany przez EF Core.

**Środki zaradcze**

Jeśli aplikacja wymaga tych usług, należy zarejestrować je jawnie przy użyciu funkcji [addlogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) lub [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).

<a name="dbe"></a>

### <a name="dbcontextentry-now-performs-a-local-detectchanges"></a>DbContext. entry wykonuje teraz lokalną DetectChanges

[Śledzenie problemu #13552](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 3.

**Stare zachowanie**

Przed EF Core 3,0 wywołanie `DbContext.Entry` mogłoby spowodować wykrycie zmian dla wszystkich śledzonych jednostek.
W ten sposób zapewniono, że stan ujawniony na `EntityEntry` bieżąco.

**Nowe zachowanie**

Począwszy od EF Core 3,0, wywołanie `DbContext.Entry` będzie teraz podejmować próbę wykrycia zmian w danej jednostce i wszystkich śledzonych podmiotach głównych.
Oznacza to, że zmiany w innym miejscu mogą nie zostać wykryte przez wywołanie tej metody, która może mieć wpływ na stan aplikacji.

Należy pamiętać, `ChangeTracker.AutoDetectChangesEnabled` że jeśli jest `false` ustawiona na, nawet to lokalne wykrywanie zmian zostanie wyłączone.

Inne metody, które powodują wykrycie zmiany — na `ChangeTracker.Entries` przykład `SaveChanges`i--nadal powodują pełne `DetectChanges` wszystkie śledzone jednostki.

**Zalet**

Ta zmiana została wprowadzona w celu poprawy domyślnej wydajności korzystania `context.Entry`z programu.

**Środki zaradcze**

Wywołaj `ChgangeTracker.DetectChanges()` jawnie przed `Entry` wywołaniem, aby upewnić się, że zachowanie poprzedzające 3,0.

### <a name="string-and-byte-array-keys-are-not-client-generated-by-default"></a>Klucze tablic ciągów i bajtów nie są generowane domyślnie przez klienta

[Śledzenie problemu #14617](https://github.com/aspnet/EntityFrameworkCore/issues/14617)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 4.

**Stare zachowanie**

Przed EF Core 3,0 `string` i `byte[]` właściwości klucza mogą być używane bez jawnego ustawienia wartości innej niż null.
W takim przypadku wartość klucza jest generowana na kliencie jako identyfikator GUID, Zserializowany do bajtów dla `byte[]`.

**Nowe zachowanie**

Począwszy od EF Core 3,0 zostanie zgłoszony wyjątek wskazujący, że nie ustawiono żadnej wartości klucza.

**Zalet**

Ta zmiana została wprowadzona, ponieważ zazwyczaj wartości `string` generowane / `byte[]` przez klienta nie są przydatne, a domyślne zachowanie spowodowało trudne powody dotyczące wygenerowanych wartości kluczy w typowy sposób.

**Środki zaradcze**

Zachowanie przed 3,0 można uzyskać, jawnie określając, że właściwości klucza powinny używać wygenerowanych wartości, jeśli nie ustawiono żadnej innej wartości innej niż null.
Na przykład za pomocą interfejsu API Fluent:

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedOnAdd();
```

Lub z adnotacjami danych:

```C#
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public string Id { get; set; }
```

<a name="ilf"></a>

### <a name="iloggerfactory-is-now-a-scoped-service"></a>ILoggerFactory jest teraz usługą objętą zakresem

[Śledzenie problemu #14698](https://github.com/aspnet/EntityFrameworkCore/issues/14698)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 3.

**Stare zachowanie**

Przed EF Core 3,0 `ILoggerFactory` został zarejestrowany jako usługa singleton.

**Nowe zachowanie**

Począwszy od EF Core 3,0, `ILoggerFactory` jest teraz zarejestrowany jako zakres.

**Zalet**

Ta zmiana została wprowadzona w celu zezwalania na skojarzenie rejestratora z `DbContext` wystąpieniem, które umożliwia inne funkcje i usuwa niektóre przypadki zachowań patologicznych, takich jak rozłożenie wewnętrznych dostawców usług.

**Środki zaradcze**

Ta zmiana nie powinna mieć wpływu na kod aplikacji, chyba że rejestruje i korzysta z usług niestandardowych na EF Core wewnętrznym dostawcy usług.
To nie jest typowe.
W takich przypadkach większość elementów będzie nadal działać, ale wszystkie pojedyncze usługi, w `ILoggerFactory` zależności od tego, muszą zostać zmienione w celu `ILoggerFactory` uzyskania w inny sposób.

Jeśli używasz `ILoggerFactory` takich sytuacji, Zapisz problem w [EF Core module śledzącym problemy](https://github.com/aspnet/EntityFrameworkCore/issues) w witrynie GitHub, aby poinformować nas, jak można lepiej zrozumieć, jak nie należy ponownie rozbić w przyszłości.

### <a name="lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded"></a>Pobieranie z opóźnieniem — nie zakłada się już, że właściwości nawigacji są w pełni załadowane

[Śledzenie problemu #12780](https://github.com/aspnet/EntityFrameworkCore/issues/12780)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 4.

**Stare zachowanie**

Przed EF Core 3,0, gdy zostanie `DbContext` usunięty, nie było możliwości znajomości, czy dana właściwość nawigacji w jednostce uzyskanej z tego kontekstu została w pełni załadowana.
Zamiast tego przyjmuje się, że jest załadowana Nawigacja referencyjna, jeśli ma ona wartość różną od null i że zostanie załadowana Nawigacja kolekcji, jeśli nie jest pusta.
W takich przypadkach próba załadowania z opóźnieniem będzie równa No-op.

**Nowe zachowanie**

Począwszy od EF Core 3,0, serwery proxy śledziją, czy właściwość nawigacji jest załadowana.
Oznacza to, że próba uzyskania dostępu do właściwości nawigacji, która jest ładowana po usunięciu kontekstu, zawsze będzie równa No-op, nawet jeśli załadowana nawigacja jest pusta lub równa null.
Z drugiej strony, próba uzyskania dostępu do właściwości nawigacji, która nie została załadowana, spowoduje zgłoszenie wyjątku, jeśli kontekst jest usuwany, nawet jeśli właściwość nawigacji jest kolekcją niepustą.
Jeśli taka sytuacja występuje, oznacza to, że kod aplikacji próbuje użyć ładowania opóźnionego w nieprawidłowym czasie, a aplikacja powinna zostać zmieniona w taki sposób, aby nie było to robić.

**Zalet**

Ta zmiana została wprowadzona, aby zachować spójność i poprawić zachowanie przy próbie załadowania `DbContext` opóźnionego wystąpienia.

**Środki zaradcze**

Zaktualizuj kod aplikacji, aby nie próbował próbować ładowania z opóźnieniem przy użyciu usuniętego kontekstu, lub skonfiguruj ten element jako No-op zgodnie z opisem w komunikacie o wyjątku.

### <a name="excessive-creation-of-internal-service-providers-is-now-an-error-by-default"></a>Nadmierne Tworzenie wewnętrznych dostawców usług jest teraz domyślnie błędem

[Śledzenie problemu #10236](https://github.com/aspnet/EntityFrameworkCore/issues/10236)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 3.

**Stare zachowanie**

Przed EF Core 3,0 zostanie zarejestrowane Ostrzeżenie dla aplikacji tworzącej liczbę patologiczną wewnętrznych dostawców usług.

**Nowe zachowanie**

Począwszy od EF Core 3,0, to ostrzeżenie jest teraz uwzględniane i występuje błąd i zgłaszany jest wyjątek. 

**Zalet**

Ta zmiana została wprowadzona w celu zapewnienia lepszej jakości kodu aplikacji przez ujawnienie tego przypadku patologiczne bardziej jawnie.

**Środki zaradcze**

Najbardziej odpowiednią przyczyną działania w przypadku wystąpienia tego błędu jest zrozumienie głównej przyczyny problemu i zatrzymanie tworzenia tak wielu wewnętrznych dostawców usług.
Błąd można jednak przekonwertować z powrotem na ostrzeżenie (lub zignorować) przez konfigurację w `DbContextOptionsBuilder`.
Na przykład:

```C#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .ConfigureWarnings(w => w.Log(CoreEventId.ManyServiceProvidersCreatedWarning));
}
```

<a name="nbh"></a>

### <a name="new-behavior-for-hasonehasmany-called-with-a-single-string"></a>Nowe zachowanie dla HasOne/HasMany wywoływane z pojedynczym ciągiem

[Śledzenie problemu #9171](https://github.com/aspnet/EntityFrameworkCore/issues/9171)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 4.

**Stare zachowanie**

Przed EF Core 3,0 kod wywołujący `HasOne` lub `HasMany` z pojedynczym ciągiem został zinterpretowany w sposób mylący.
Na przykład:
```C#
modelBuilder.Entity<Samurai>().HasOne("Entrance").WithOne();
```

Kod wygląda tak `Samurai` , jakby odnosił się do innego typu jednostki `Entrance` przy użyciu właściwości nawigacji, która może być prywatna.

W rzeczywistości ten kod próbuje utworzyć relację z typem obiektu o nazwie `Entrance` bez właściwości nawigacji.

**Nowe zachowanie**

Począwszy od EF Core 3,0, kod powyżej robi teraz, co wyglądało wcześniej.

**Zalet**

Stare zachowanie było bardzo mylące, szczególnie podczas odczytywania kodu konfiguracji i wyszukiwania błędów.

**Środki zaradcze**

Spowoduje to przerwanie aplikacji, które jawnie konfigurują relacje przy użyciu ciągów nazw typów i bez określania jawnie właściwości nawigacji.
Nie jest to typowy sposób.
Poprzednie zachowanie można uzyskać poprzez jawne przekazanie `null` nazwy właściwości nawigacji.
Przykład:

```C#
modelBuilder.Entity<Samurai>().HasOne("Some.Entity.Type.Name", null).WithOne();
```

<a name="rtnt"></a>

### <a name="the-return-type-for-several-async-methods-has-been-changed-from-task-to-valuetask"></a>Typ zwracany dla kilku metod asynchronicznych został zmieniony z zadania na ValueTask

[Śledzenie problemu #15184](https://github.com/aspnet/EntityFrameworkCore/issues/15184)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 4.

**Stare zachowanie**

Następujące metody asynchroniczne zwróciły `Task<T>`wcześniej:

* `DbContext.FindAsync()`
* `DbSet.FindAsync()`
* `DbContext.AddAsync()`
* `DbSet.AddAsync()`
* `ValueGenerator.NextValueAsync()`(i pochodne klasy)

**Nowe zachowanie**

Powyższe metody teraz zwracają wartość `ValueTask<T>` powyżej `T` , tak jak wcześniej.

**Zalet**

Ta zmiana zmniejsza liczbę przydziałów sterty wynikających z wywołania tych metod, co zwiększa ogólną wydajność.

**Środki zaradcze**

Aplikacje, które po prostu oczekują na powyższe interfejsy API, muszą zostać ponownie skompilowane — nie są wymagane żadne zmiany ze źródła.
Bardziej złożone użycie (np. przekazanie zwróconej `Task` do `Task.WhenAny()`) zwykle wymaga, aby zwrócone `ValueTask<T>` przekonwertowanie na a `Task<T>` przez wywołanie `AsTask()` .
Należy zauważyć, że ta zmiana powoduje spadek przydziału alokacji.

<a name="rtt"></a>

### <a name="the-relationaltypemapping-annotation-is-now-just-typemapping"></a>Adnotacja relacyjna: TypeMapping ma teraz tylko Właściwość TypeMapping

[Śledzenie problemu #9913](https://github.com/aspnet/EntityFrameworkCore/issues/9913)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 2.

**Stare zachowanie**

Nazwa adnotacji dla adnotacji mapowania typu to "relacyjne: TypeMapping".

**Nowe zachowanie**

Nazwa adnotacji dla adnotacji mapowania typu jest teraz "TypeMapping".

**Zalet**

Mapowania typów są teraz używane dla więcej niż tylko dostawców relacyjnych baz danych.

**Środki zaradcze**

Spowoduje to przerwanie aplikacji, które uzyskują dostęp do mapowania typu bezpośrednio jako adnotacji, która nie jest wspólna.
Najbardziej odpowiednią akcją do naprawienia jest użycie funkcji powierzchni interfejsu API w celu uzyskania dostępu do mapowań typów, zamiast bezpośredniego używania adnotacji.

### <a name="totable-on-a-derived-type-throws-an-exception"></a>ToTable dla typu pochodnego zgłasza wyjątek 

[Śledzenie problemu #11811](https://github.com/aspnet/EntityFrameworkCore/issues/11811)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 3.

**Stare zachowanie**

Przed EF Core 3,0, `ToTable()` wywołana dla typu pochodnego zostałaby zignorowana, ponieważ strategia mapowania dziedziczenia była TPH, gdy jest to nieprawidłowe. 

**Nowe zachowanie**

Począwszy od EF Core 3,0 i przygotowania do dodawania obsługi TPT i TPC w późniejszej wersji, wywołana `ToTable()` dla typu pochodnego będzie teraz zgłosić wyjątek, aby uniknąć nieoczekiwanej zmiany mapowania w przyszłości.

**Zalet**

Obecnie nie jest to prawidłowe mapowanie typu pochodnego na inną tabelę.
Ta zmiana pozwala uniknąć przerywania w przyszłości, gdy zostanie ona poprawna.

**Środki zaradcze**

Usuń wszystkie próby mapowania typów pochodnych do innych tabel.

### <a name="forsqlserverhasindex-replaced-with-hasindex"></a>ForSqlServerHasIndex zastąpione HasIndex 

[Śledzenie problemu #12366](https://github.com/aspnet/EntityFrameworkCore/issues/12366)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 3.

**Stare zachowanie**

Przed EF Core 3,0, `ForSqlServerHasIndex().ForSqlServerInclude()` zapewnia sposób konfigurowania kolumn używanych w programie. `INCLUDE`

**Nowe zachowanie**

Począwszy od EF Core 3,0, użycie `Include` na indeksie jest teraz obsługiwane na poziomie relacyjnym.
Użyj `HasIndex().ForSqlServerInclude()`.

**Zalet**

Ta zmiana została wprowadzona w celu konsolidacji interfejsu API dla indeksów `Include` w jednym miejscu dla wszystkich dostawców baz danych.

**Środki zaradcze**

Użyj nowego interfejsu API, jak pokazano powyżej.

### <a name="metadata-api-changes"></a>Zmiany interfejsu API metadanych

[Śledzenie problemu #214](https://github.com/aspnet/EntityFrameworkCore/issues/214)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 4.

**Nowe zachowanie**

Następujące właściwości zostały przekonwertowane na metody rozszerzające:

* `IEntityType.QueryFilter` -> `GetQueryFilter()`
* `IEntityType.DefiningQuery` -> `GetDefiningQuery()`
* `IProperty.IsShadowProperty` -> `IsShadowProperty()`
* `IProperty.BeforeSaveBehavior` -> `GetBeforeSaveBehavior()`
* `IProperty.AfterSaveBehavior` -> `GetAfterSaveBehavior()`

**Zalet**

Ta zmiana upraszcza implementację powyższych interfejsów.

**Środki zaradcze**

Użyj nowych metod rozszerzenia.

<a name="provider"></a>

### <a name="provider-specific-metadata-api-changes"></a>Zmiany w interfejsie API metadanych specyficzne dla dostawcy

[Śledzenie problemu #214](https://github.com/aspnet/EntityFrameworkCore/issues/214)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 6.

**Nowe zachowanie**

Metody rozszerzenia specyficzne dla dostawcy zostaną spłaszczone:

* `IProperty.Relational().ColumnName` -> `IProperty.GetColumnName()`
* `IEntityType.SqlServer().IsMemoryOptimized` -> `IEntityType.IsMemoryOptimized()`
* `PropertyBuilder.UseSqlServerIdentityColumn()` -> `PropertyBuilder.UseIdentityColumn()`

**Zalet**

Ta zmiana upraszcza implementację powyższych metod rozszerzenia.

**Środki zaradcze**

Użyj nowych metod rozszerzenia.

<a name="pragma"></a>

### <a name="ef-core-no-longer-sends-pragma-for-sqlite-fk-enforcement"></a>Nie EF Core już wysyłać dyrektywy pragma dla wymuszania programu SQLite FK

[Śledzenie problemu #12151](https://github.com/aspnet/EntityFrameworkCore/issues/12151)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 3.

**Stare zachowanie**

Przed EF Core 3,0, EF Core wysyłać `PRAGMA foreign_keys = 1` , gdy zostanie otwarte połączenie z systemem SQLite.

**Nowe zachowanie**

Począwszy od EF Core 3,0, EF Core przestać wysyłać `PRAGMA foreign_keys = 1` , gdy zostanie otwarte połączenie z programem SQLite.

**Zalet**

Ta zmiana została wprowadzona, ponieważ domyślnie `SQLitePCLRaw.bundle_e_sqlite3` używa EF Core, co z kolei oznacza, że wymuszanie klucza obcego jest domyślnie włączone i nie trzeba go jawnie włączać przy każdym otwarciu połączenia.

**Środki zaradcze**

Klucze obce są domyślnie włączone w SQLitePCLRaw. bundle_e_sqlite3, który jest używany domyślnie dla EF Core.
W innych przypadkach klucze obce można włączyć, określając `Foreign Keys=True` w parametrach połączenia.

<a name="sqlite3"></a>

### <a name="microsoftentityframeworkcoresqlite-now-depends-on-sqlitepclrawbundle_e_sqlite3"></a>Microsoft. EntityFrameworkCore. sqlite teraz zależy od SQLitePCLRaw. bundle_e_sqlite3

**Stare zachowanie**

Przed EF Core 3,0 EF Core używany `SQLitePCLRaw.bundle_green`.

**Nowe zachowanie**

Począwszy od EF Core 3,0, EF Core używa `SQLitePCLRaw.bundle_e_sqlite3`.

**Zalet**

Ta zmiana została wprowadzona w taki sposób, aby wersja oprogramowania SQLite była zgodna z innymi platformami w systemie iOS.

**Środki zaradcze**

Aby korzystać z natywnej wersji programu SQLite w systemie `Microsoft.Data.Sqlite` iOS, należy skonfigurować `SQLitePCLRaw` do korzystania z innego pakietu.

<a name="guid"></a>

### <a name="guid-values-are-now-stored-as-text-on-sqlite"></a>Wartości identyfikatorów GUID są teraz przechowywane jako tekst na komputerze SQLite

[Śledzenie problemu #15078](https://github.com/aspnet/EntityFrameworkCore/issues/15078)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 4.

**Stare zachowanie**

Wartości identyfikatorów GUID były wcześniej przechowywane jako wartości obiektów BLOB na komputerze SQLite.

**Nowe zachowanie**

Wartości identyfikatorów GUID są teraz przechowywane jako tekst.

**Zalet**

Format binarny identyfikatorów GUID nie jest znormalizowany. Przechowywanie wartości jako tekstu sprawia, że baza danych jest bardziej zgodna z innymi technologiami.

**Środki zaradcze**

Istnieje możliwość migrowania istniejących baz danych do nowego formatu przez wykonanie kodu SQL, takiego jak poniższy.

``` sql
UPDATE MyTable
SET GuidColumn = hex(substr(GuidColumn, 4, 1)) ||
                 hex(substr(GuidColumn, 3, 1)) ||
                 hex(substr(GuidColumn, 2, 1)) ||
                 hex(substr(GuidColumn, 1, 1)) || '-' ||
                 hex(substr(GuidColumn, 6, 1)) ||
                 hex(substr(GuidColumn, 5, 1)) || '-' ||
                 hex(substr(GuidColumn, 8, 1)) ||
                 hex(substr(GuidColumn, 7, 1)) || '-' ||
                 hex(substr(GuidColumn, 9, 2)) || '-' ||
                 hex(substr(GuidColumn, 11, 6))
WHERE typeof(GuidColumn) == 'blob';
```

W EF Core można również nadal korzystać z poprzedniego zachowania przez skonfigurowanie konwertera wartości dla tych właściwości.

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.GuidProperty)
    .HasConversion(
        g => g.ToByteArray(),
        b => new Guid(b));
```

Firma Microsoft. Data. sqlite nadal umożliwia odczytywanie wartości identyfikatora GUID zarówno z obiektów BLOB, jak i kolumn tekstowych. Jednak od momentu zmiany domyślnego formatu parametrów i stałych prawdopodobnie trzeba będzie wykonać akcję dla większości scenariuszy obejmujących identyfikatory GUID.

<a name="char"></a>

### <a name="char-values-are-now-stored-as-text-on-sqlite"></a>Wartości char są teraz przechowywane jako tekst na komputerze SQLite

[Śledzenie problemu #15020](https://github.com/aspnet/EntityFrameworkCore/issues/15020)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 4.

**Stare zachowanie**

Wartości char były wcześniej sored jako wartości całkowite na komputerze SQLite. Na przykład wartość typu char *jest* przechowywana jako wartość całkowita 65.

**Nowe zachowanie**

Wartości char są teraz przechowywane jako tekst.

**Zalet**

Przechowywanie wartości jako tekstu jest bardziej naturalne i sprawia, że baza danych jest bardziej zgodna z innymi technologiami.

**Środki zaradcze**

Istnieje możliwość migrowania istniejących baz danych do nowego formatu przez wykonanie kodu SQL, takiego jak poniższy.

``` sql
UPDATE MyTable
SET CharColumn = char(CharColumn)
WHERE typeof(CharColumn) = 'integer';
```

W EF Core można również nadal korzystać z poprzedniego zachowania przez skonfigurowanie konwertera wartości dla tych właściwości.

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.CharProperty)
    .HasConversion(
        c => (long)c,
        i => (char)i);
```

Microsoft. Data. sqlite również pozostaje w stanie odczytać wartości znaków z kolumn liczb CAŁKOWITYch i tekstowych, dzięki czemu niektóre scenariusze mogą nie wymagać żadnej akcji.

<a name="migid"></a>

### <a name="migration-ids-are-now-generated-using-the-invariant-cultures-calendar"></a>Identyfikatory migracji są teraz generowane przy użyciu kalendarza niezmiennej kultury

[Śledzenie problemu #12978](https://github.com/aspnet/EntityFrameworkCore/issues/12978)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 4.

**Stare zachowanie**

Identyfikatory migracji zostały przypadkowo wygenerowane przy użyciu kalendarza bieżącej kultury.

**Nowe zachowanie**

Identyfikatory migracji są teraz zawsze generowane przy użyciu kalendarza niezmiennej kultury (gregoriański).

**Zalet**

Kolejność migracji jest ważna podczas aktualizowania bazy danych lub rozwiązywania konfliktów scalania. Użycie kalendarza niezmiennej pozwala uniknąć problemów z porządkowaniem, które mogą wynikać z członków zespołu mających różne kalendarze systemu.

**Środki zaradcze**

Ta zmiana ma wpływ na wszystkich użytkowników w kalendarzu innym niż gregoriański, w których rok jest większy niż kalendarz gregoriański (np. tajski kalendarz Buddyjski). Istniejące identyfikatory migracji należy zaktualizować, aby nowe migracje były uporządkowane po zakończeniu migracji.

Identyfikator migracji można znaleźć w atrybucie migracji w plikach projektanta migracji.

``` diff
 [DbContext(typeof(MyDbContext))]
-[Migration("25620318122820_MyMigration")]
+[Migration("20190318122820_MyMigration")]
 partial class MyMigration
 {
```

Należy również zaktualizować tabelę historii migracji.

``` sql
UPDATE __EFMigrationsHistory
SET MigrationId = CONCAT(LEFT(MigrationId, 4)  - 543, SUBSTRING(MigrationId, 4, 150))
```

<a name="urn"></a>

### <a name="userownumberforpaging-has-been-removed"></a>UseRowNumberForPaging został usunięty

[Śledzenie problemu #16400](https://github.com/aspnet/EntityFrameworkCore/issues/16400)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 6.

**Stare zachowanie**

Przed EF Core 3,0 `UseRowNumberForPaging` może zostać użyty do wygenerowania bazy danych SQL na potrzeby stronicowania zgodnego z SQL Server 2008.

**Nowe zachowanie**

Począwszy od EF Core 3,0, EF będzie generować tylko SQL dla stronicowania, który jest zgodny z nowszymi wersjami SQL Server. 

**Zalet**

Wprowadzamy tę zmianę, ponieważ [SQL Server 2008 nie jest już obsługiwanym produktem](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/) i aktualizowanie tej funkcji do pracy z zmianami zapytań wprowadzonymi w EF Core 3,0 jest istotną pracą.

**Środki zaradcze**

Zalecamy zaktualizowanie do nowszej wersji SQL Server lub przy użyciu wyższego poziomu zgodności, aby obsługiwał wygenerowane SQL. Jeśli nie możesz tego zrobić, Dodaj komentarz dotyczący [problemu ze śledzeniem](https://github.com/aspnet/EntityFrameworkCore/issues/16400) ze szczegółowymi informacjami. Firma Microsoft może ponownie odwiedzać tę decyzję na podstawie opinii.

<a name="xinfo"></a>

### <a name="extension-infometadata-has-been-removed-from-idbcontextoptionsextension"></a>Informacje o rozszerzeniu/metadane zostały usunięte z IDbContextOptionsExtension

[Śledzenie problemu #16119](https://github.com/aspnet/EntityFrameworkCore/issues/16119)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 7.

**Stare zachowanie**

`IDbContextOptionsExtension`zawiera metody przekazywania metadanych o rozszerzeniu.

**Nowe zachowanie**

Te metody zostały przeniesione na nową `DbContextOptionsExtensionInfo` abstrakcyjną klasę bazową, która jest zwracana z nowej `IDbContextOptionsExtension.Info` właściwości.

**Zalet**

W wersjach od 2,0 do 3,0 firma Microsoft musi dodać lub zmienić te metody kilka razy.
Rozdzielenie ich na nową abstrakcyjną klasę bazową ułatwi wprowadzanie zmian tego rodzaju bez przerywania istniejących rozszerzeń.

**Środki zaradcze**

Zaktualizuj rozszerzenia, aby były zgodne z nowym wzorcem.
Przykłady można znaleźć w wielu implementacjach `IDbContextOptionsExtension` dla różnych rodzajów rozszerzeń w EF Core kodzie źródłowym.

<a name="lqpe"></a>

### <a name="logquerypossibleexceptionwithaggregateoperator-has-been-renamed"></a>Zmieniono nazwę LogQueryPossibleExceptionWithAggregateOperator

[Śledzenie problemu #10985](https://github.com/aspnet/EntityFrameworkCore/issues/10985)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 4.

**Stąp**

`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator`została zmieniona na `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.

**Zalet**

Wyrównuje nazwę tego zdarzenia ostrzegawczego ze wszystkimi innymi zdarzeniami ostrzegawczymi.

**Środki zaradcze**

Użyj nowej nazwy. (Należy zauważyć, że numer identyfikatora zdarzenia nie został zmieniony).

<a name="clarify"></a>

### <a name="clarify-api-for-foreign-key-constraint-names"></a>Wyjaśnienie interfejsu API nazw ograniczeń klucza obcego

[Śledzenie problemu #10730](https://github.com/aspnet/EntityFrameworkCore/issues/10730)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 4.

**Stare zachowanie**

Przed EF Core 3,0, nazwy ograniczeń klucza obcego były określane jako "nazwa". Przykład:

```C#
var constraintName = myForeignKey.Name;
```

**Nowe zachowanie**

Począwszy od EF Core 3,0, nazwy ograniczeń klucza obcego są teraz określane jako "nazwa ograniczenia". Na przykład:

```C#
var constraintName = myForeignKey.ConstraintName;
```

**Zalet**

Ta zmiana powoduje spójność nazw w tym obszarze, a także wyjaśniono, że jest to nazwa ograniczenia klucza obcego, a nie nazwa kolumny lub właściwości, w której jest zdefiniowany klucz obcy.

**Środki zaradcze**

Użyj nowej nazwy.

<a name="irdc2"></a>

### <a name="irelationaldatabasecreatorhastableshastablesasync-have-been-made-public"></a>IRelationalDatabaseCreator. HasTables/HasTablesAsync zostały udostępnione publicznie

[Śledzenie problemu #15997](https://github.com/aspnet/EntityFrameworkCore/issues/15997)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 7.

**Stare zachowanie**

Przed EF Core 3,0 te metody były chronione.

**Nowe zachowanie**

Począwszy od EF Core 3,0, te metody są publiczne.

**Zalet**

Te metody są używane przez EF do określenia, czy baza danych została utworzona, ale pusta. Może to być również przydatne z zewnątrz EF podczas określania, czy mają być stosowane migracje.

**Środki zaradcze**

Zmień dostępność wszelkich zastąpień.

<a name="dip"></a>

### <a name="microsoftentityframeworkcoredesign-is-now-a-developmentdependency-package"></a>Microsoft. EntityFrameworkCore. Design jest teraz pakietem DevelopmentDependency

[Śledzenie problemu #11506](https://github.com/aspnet/EntityFrameworkCore/issues/11506)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 4.

**Stare zachowanie**

Przed EF Core 3,0, Microsoft. EntityFrameworkCore. Design był zwykłym pakietem NuGet, którego zestaw może odwoływać się do projektów, które od niego zależą.

**Nowe zachowanie**

Począwszy od EF Core 3,0, jest to pakiet DevelopmentDependency. Oznacza to, że zależność nie będzie przepływać przechodnio do innych projektów i nie można już, domyślnie odwołuje się do zestawu.

**Zalet**

Ten pakiet jest przeznaczony wyłącznie do użytku w czasie projektowania. Wdrożone aplikacje nie powinny się do niej odwoływać. Pakiet a DevelopmentDependency wzmacnia to zalecenie.

**Środki zaradcze**

Jeśli musisz odwołać się do tego pakietu, aby przesłonić zachowanie EF Core w czasie projektowania, możesz zaktualizować metadane elementu PackageReference aktualizacji w projekcie. Jeśli pakiet jest przywoływany przechodniie za pośrednictwem Microsoft. EntityFrameworkCore. Tools, należy dodać jawne PackageReference do pakietu, aby zmienić jego metadane.

``` xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.0.0-preview4.19216.3">
  <PrivateAssets>all</PrivateAssets>
  <!-- Remove IncludeAssets to allow compiling against the assembly -->
  <!--<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>-->
</PackageReference>
```

<a name="SQLitePCL"></a>

### <a name="sqlitepclraw-updated-to-version-200"></a>SQLitePCL. Raw Zaktualizowano do wersji 2.0.0

[Śledzenie problemu #14824](https://github.com/aspnet/EntityFrameworkCore/issues/14824)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 7.

**Stare zachowanie**

Microsoft. EntityFrameworkCore. sqlite poprzednio zależała od wersji 1.1.12 SQLitePCL. Raw.

**Nowe zachowanie**

Zaktualizowaliśmy pakiet, aby zależał od wersji 2.0.0.

**Zalet**

Wersja 2.0.0 z SQLitePCL. Raw targets .NET Standard 2,0. Wcześniej była przeznaczona .NET Standard 1,1, co wymagało dużego zamknięcia pakietów przechodnich.

**Środki zaradcze**

SQLitePCL. Raw wersja 2.0.0 zawiera pewne istotne zmiany. Szczegółowe informacje można znaleźć w [informacjach o wersji](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md) .

<a name="NetTopologySuite"></a>

### <a name="nettopologysuite-updated-to-version-200"></a>NetTopologySuite Zaktualizowano do wersji 2.0.0

[Śledzenie problemu #14825](https://github.com/aspnet/EntityFrameworkCore/issues/14825)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 7.

**Stare zachowanie**

Pakiety przestrzenne poprzednio zależały od wersji 1.15.1 NetTopologySuite.

**Nowe zachowanie**

Zaktualizowaliśmy pakiet, aby zależał od wersji 2.0.0.

**Zalet**

Wersja 2.0.0 programu NetTopologySuite ma na celu rozwiązanie kilku problemów użyteczności napotykanych przez EF Core użytkowników.

**Środki zaradcze**

NetTopologySuite wersja 2.0.0 zawiera pewne istotne zmiany. Szczegółowe informacje można znaleźć w [informacjach o wersji](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001) .

<a name="mersa"></a>

### <a name="multiple-ambiguous-self-referencing-relationships-must-be-configured"></a>Należy skonfigurować wiele niejednoznacznych relacji odwołujących się do siebie. 

[Śledzenie problemu #13573](https://github.com/aspnet/EntityFrameworkCore/issues/13573)

Ta zmiana została wprowadzona w EF Core 3,0 — wersja zapoznawcza 6.

**Stare zachowanie**

Typ jednostki z wieloma odwołującymi się do siebie właściwości nawigacji jednokierunkowej i pasujących FKs został niepoprawnie skonfigurowany jako pojedynczej relacji. Przykład:

```C#
public class User 
{
        public Guid Id { get; set; }
        public User CreatedBy { get; set; }
        public User UpdatedBy { get; set; }
        public Guid CreatedById { get; set; }
        public Guid? UpdatedById { get; set; }
}
```

**Nowe zachowanie**

Ten scenariusz jest teraz wykrywany podczas budowania modelu i zgłaszany jest wyjątek wskazujący, że model jest niejednoznaczny.

**Zalet**

Model wynikowy jest niejednoznaczny i prawdopodobnie jest zwykle niewłaściwy w tym przypadku.

**Środki zaradcze**

Użyj pełnej konfiguracji relacji. Przykład:

```C#
modelBuilder
     .Entity<User>()
     .HasOne(e => e.CreatedBy)
     .WithMany();
 
 modelBuilder
     .Entity<User>()
     .HasOne(e => e.UpdatedBy)
     .WithMany();
```
