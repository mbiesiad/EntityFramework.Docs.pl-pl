---
title: Powodująca niezgodność zmiany w EF Core 3.0 — EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: EE2878C9-71F9-4FA5-9BC4-60517C7C9830
uid: core/what-is-new/ef-core-3.0/breaking-changes
ms.openlocfilehash: 7ed55d4cae36f6b25059a5b218db4b0d5e2fb266
ms.sourcegitcommit: 645785187ae23ddf7d7b0642c7a4da5ffb0c7f30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58419747"
---
# <a name="breaking-changes-included-in-ef-core-30-currently-in-preview"></a>Istotne zmiany zawarte w programie EF Core 3.0 (obecnie w wersji zapoznawczej)

> [!IMPORTANT]
> Należy pamiętać, że zestawy funkcji i harmonogramy przyszłych wersji zawsze mogą ulec zmianie i czasu, mimo że firma Microsoft podejmie próbę tej strony na bieżąco, jego mogą nie odzwierciedlać najnowszych plany na wszystkich.

Następujące zmiany interfejsu API i zachowanie mogą potencjalnie przerwanie aplikacje opracowane dla platformy EF Core 2.2.x po uaktualnieniu ich do 3.0.0.
Zmiany, oczekujemy, że mają wpływ tylko na dostawcy baz danych, które są udokumentowane w obszarze [zmian dostawcy](../../providers/provider-log.md).
Podział w nowych funkcji wprowadzonych w jednym 3.0 w wersji zapoznawczej do innej wersji 3.0 w wersji zapoznawczej nie są opisane tutaj.

## <a name="linq-queries-are-no-longer-evaluated-on-the-client"></a>Zapytania LINQ nie są obliczane na komputerze klienckim

[Śledzenie 14935 # problem](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[Zobacz też problem #12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)

Ta zmiana zostanie wprowadzona w programu EF Core 3.0 — w wersji zapoznawczej 4.

**Stare zachowanie**

Przed 3.0 po programu EF Core nie można przekonwertować na wyrażenie, które było częścią zapytania SQL lub parametru, jego automatycznie oceniane wyrażenie na komputerze klienckim.
Domyślnie klient obliczanie wyrażeń potencjalnie kosztownym wyzwalane tylko ostrzeżenie.

**Nowe zachowanie**

Począwszy od 3.0 programu EF Core zezwala tylko wyrażenia w projekcji najwyższego poziomu (ostatnie `Select()` wywołań w zapytaniu) ma zostać obliczone na komputerze klienckim.
Jeśli nie można przekonwertować wyrażenia w dowolnej części zapytania SQL lub parametr, jest zgłaszany wyjątek.

**Dlaczego**

Automatyczne klienta zapytań pozwala ona wiele zapytań do wykonania, nawet wtedy, gdy nie można przetłumaczyć ważne elementy.
To zachowanie może powodować nieoczekiwane i potencjalnie szkodliwych zachowanie, które mogą stać się widoczne w środowisku produkcyjnym.
Na przykład warunku w `Where()` wywołanie, którego nie można przetłumaczyć może spowodować, że wszystkie wiersze z tabeli z serwera bazy danych i filtrów, które mają być stosowane na komputerze klienckim.
Ta sytuacja może łatwo przejść niewykryte, jeśli tabela zawiera tylko kilka wierszy w rozwoju, ale twardych trafień, gdy aplikacja przejdzie do środowiska produkcyjnego, gdzie tabeli może zawierać miliony wierszy.
Ostrzeżenia dotyczące oceny klienta również okazały się zbyt łatwe do zignorowania podczas programowania.

Poza tym oceny klienta może prowadzić do problemów, w których usprawnienie translacji zapytania dla określonych wyrażeń spowodowane niezamierzone przełomowych zmian między wersjami.

**Środki zaradcze**

Jeśli zapytanie nie może być w pełni przetłumaczona, a następnie zastąp kwerendy w postaci, które mogą być tłumaczone lub użyj `AsEnumerable()`, `ToList()`, lub podobne do jawnie możesz przenosić dane do klienta której następnie można dalsze przetworzona za pomocą LINQ do obiektów.

## <a name="entity-framework-core-is-no-longer-part-of-the-aspnet-core-shared-framework"></a>Entity Framework Core nie jest już częścią udostępnionej platformy ASP.NET Core

[Anonse problem śledzenia #325](https://github.com/aspnet/Announcements/issues/325)

Ta zmiana została wprowadzona w programie ASP.NET Core 3.0 w wersji zapoznawczej 1. 

**Stare zachowanie**

Przed platformy ASP.NET Core 3.0, po dodaniu odwołania do pakietu do `Microsoft.AspNetCore.App` lub `Microsoft.AspNetCore.All`, może on zawierać programu EF Core i niektóre z programem EF Core dostawcy danych, takich jak dostawcy programu SQL Server.

**Nowe zachowanie**

Począwszy od 3.0, udostępnionej platformy ASP.NET Core nie zawiera programu EF Core lub żadnego dostawcy danych programu EF Core.

**Dlaczego**

Przed wprowadzeniem tej zmiany pobierania programu EF Core wymaga różnych kroków w zależności od tego, czy aplikacja docelowej platformy ASP.NET Core i SQL Server, lub nie. Ponadto uaktualnienie platformy ASP.NET Core wymuszone uaktualnienie programu EF Core i dostawcy programu SQL Server, który nie zawsze jest pożądane.

Dzięki tej zmianie środowiska pobierania programu EF Core jest taka sama we wszystkich dostawców, obsługiwane implementacje platformy .NET i typy aplikacji.
Deweloperzy również teraz można kontrolować, dokładnie tak, po uaktualnieniu programu EF Core i programem EF Core dostawcy danych.

**Środki zaradcze**

Aby użyć programu EF Core w aplikacji ASP.NET Core 3.0 lub dowolnej innej obsługiwanej aplikacji, należy jawnie dodać odwołania do pakietu do dostawcy bazy danych programu EF Core, używanego przez aplikację.

## <a name="query-execution-is-logged-at-debug-level"></a>Wykonanie zapytania jest rejestrowane na poziomie debugowania

[Śledzenie problem #14523](https://github.com/aspnet/EntityFrameworkCore/issues/14523)

Ta zmiana została wprowadzona w programu EF Core 3.0 — w wersji zapoznawczej 3.

**Stare zachowanie**

Przed programem EF Core 3.0, wykonywanie zapytań i innych poleceniach, zarejestrowano w `Info` poziom.

**Nowe zachowanie**

Począwszy od programu EF Core 3.0 rejestrowanie wykonywania polecenia/SQL jest w `Debug` poziom.

**Dlaczego**

Ta zmiana została wprowadzona redukcji szumu w `Info` poziom dziennika.

**Środki zaradcze**

To zdarzenie logowania jest definiowany przez `RelationalEventId.CommandExecuting` zdarzenia o identyfikatorze 20100.
Do logowania SQL w `Info` poziomu ponownie, jawnie skonfigurować poziom w `OnConfiguring` lub `AddDbContext`.
Na przykład:
```C#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(connectionString)
        .ConfigureWarnings(c => c.Log((RelationalEventId.CommandExecuting, LogLevel.Info)));
```

## <a name="temporary-key-values-are-no-longer-set-onto-entity-instances"></a>Wartości klucza tymczasowego nie są już ustawione na wystąpień jednostek

[Śledzenie problem #12378](https://github.com/aspnet/EntityFrameworkCore/issues/12378)

Ta zmiana została wprowadzona w programu EF Core 3.0-preview 2.

**Stare zachowanie**

Przed programem EF Core 3.0 to tymczasowe wartości zostały przypisane do wszystkich właściwości klucza, które później będzie miała wartość rzeczywistego generowane przez bazę danych.
Zazwyczaj te wartości tymczasowej były dużych liczb ujemnych.

**Nowe zachowanie**

Począwszy od 3.0 tymczasowej wartości klucza są przechowywane jako część informacji śledzenia jednostki programu EF Core i pozostawia właściwość klucza, sama bez zmian.

**Dlaczego**

Ta zmiana została wprowadzona, aby zapobiec wartości klucza tymczasowego błędnie trwałe, gdy jednostka, która ma zostać wcześniej śledzone przez niektóre `DbContext` wystąpienia zostanie przeniesiony do innego `DbContext` wystąpienia. 

**Środki zaradcze**

Aplikacje, które przypisać wartości klucza podstawowego na klucze obce do formularza skojarzenia między jednostkami może zależeć od starego zachowania, jeśli są generowane przez magazyn kluczy podstawowych, a należą do jednostki w `Added` stanu.
Można tego uniknąć, za pomocą:
* Nie używa generowane przez magazyn kluczy.
* Ustawianie właściwości nawigacji do utworzenia relacji zamiast ustawiać wartości klucza obcego.
* Uzyskać rzeczywiste wartości klucza tymczasowego z jednostki informacje ze śledzenia.
Na przykład `context.Entry(blog).Property(e => e.Id).CurrentValue` zwróci wartości tymczasowej, nawet jeśli `blog.Id` sam nie została ustawiona.

## <a name="detectchanges-honors-store-generated-key-values"></a>Metody DetectChanges honoruje generowane przez Magazyn wartości klucza

[Śledzenie problem #14616](https://github.com/aspnet/EntityFrameworkCore/issues/14616)

Ta zmiana została wprowadzona w programu EF Core 3.0 — w wersji zapoznawczej 3.

**Stare zachowanie**

Przed programem EF Core 3.0 to jednostki nieśledzone znalezione przez `DetectChanges` będą śledzone w `Added` stanu i wstawiony jako nowy wiersz, gdy `SaveChanges` jest wywoływana.

**Nowe zachowanie**

Począwszy od programu EF Core 3.0, jeśli korzysta z jednostki generowane wartości klucza i niektóre wartości klucza jest ustawiona, a następnie jednostki będą śledzone w `Modified` stanu.
Oznacza to, że przyjęto, że wiersz dla tej jednostki istnieje i będzie aktualizowane, gdy `SaveChanges` jest wywoływana.
Jeśli nie ustawiono wartości klucza, lub jeśli typ jednostki nie jest używana wygenerowanych kluczy, a następnie nową jednostkę, będą nadal być śledzone jako `Added` tak jak w poprzednich wersjach.

**Dlaczego**

Ta zmiana została wprowadzona do umożliwiają łatwiejsze i bardziej spójną do pracy z wykresami odłączonych jednostek podczas korzystania z generowane przez magazyn kluczy.

**Środki zaradcze**

Ta zmiana może przerwać aplikacji, jeśli typ jednostki jest skonfigurowany do użycia wygenerowanych kluczy, ale wartości klucza są jawnie ustawione dla nowych wystąpień.
Obejście polega na jawnego konfigurowania właściwości klucza nie Użyj generowane wartości.
Na przykład przy użyciu wygodnego interfejsu API:

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedNever();
```

Lub przy użyciu adnotacji danych:

```C#
[DatabaseGenerated(DatabaseGeneratedOption.None)]
public string Id { get; set; }
```

## <a name="cascade-deletions-now-happen-immediately-by-default"></a>Usuwanie kaskadowe teraz realizowane natychmiast domyślnie

[Śledzenie problem #10114](https://github.com/aspnet/EntityFrameworkCore/issues/10114)

Ta zmiana została wprowadzona w programu EF Core 3.0 — w wersji zapoznawczej 3.

**Stare zachowanie**

Przed 3.0 obejmuje ją Sekwencja akcji programu EF Core stosowane (Usuwanie jednostki zależne, gdy wymagane podmiot zabezpieczeń został usunięty lub gdy relacji z podmiotem zabezpieczeń wymagane jest oddzielone) nie nastąpiły aż SaveChanges została wywołana.

**Nowe zachowanie**

Począwszy od 3.0 programu EF Core dotyczy obejmuje ją Sekwencja akcji zaraz po wykryciu warunku wyzwalania.
Na przykład, wywołanie `context.Remove()` konieczne usunięcie jednostki głównej będzie wynik we wszystkich śledzone związane z wymaganych zależności również ustawiona na wartość `Deleted` natychmiast.

**Dlaczego**

Ta zmiana została wprowadzona w celu usprawnienie obsługi wiązaniu danych i inspekcji scenariusze, w których ważne jest, aby zrozumieć, w których obiektach zostaną usunięte _przed_ `SaveChanges` jest wywoływana.

**Środki zaradcze**

Poprzednie zachowanie można przywrócić za pomocą ustawień na `context.ChangedTracker`.
Na przykład:

```C#
context.ChangeTracker.CascadeDeleteTiming = CascadeTiming.OnSaveChanges;
context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;
```

## <a name="query-types-are-consolidated-with-entity-types"></a>Typy zapytań i dalszych są skonsolidowane przy użyciu typów jednostek

[Śledzenie problem #14194](https://github.com/aspnet/EntityFrameworkCore/issues/14194)

Ta zmiana została wprowadzona w programu EF Core 3.0 — w wersji zapoznawczej 3.

**Stare zachowanie**

Przed programem EF Core 3.0 to [typy zapytań](xref:core/modeling/query-types) zostały oznacza wykonywać zapytania względem danych, która nie zdefiniowano klucza podstawowego w taki sposób, ze strukturą.
Oznacza to, że typ zapytania został użyty do mapowania typów jednostek, bez kluczy (najprawdopodobniej w widoku, ale prawdopodobnie z tabeli) podczas regularnego jednostki typu podczas użyto klucz był dostępny (większe prawdopodobieństwo z tabeli, ale prawdopodobnie z widoku).

**Nowe zachowanie**

Typ zapytania staje się teraz tylko typ jednostki bez klucza podstawowego.
Typy bez kluczy jednostki mają taką samą funkcjonalność jak typy zapytań w poprzednich wersjach.

**Dlaczego**

Ta zmiana została wprowadzona do pomyłek wokół celem typów zapytań.
W szczególności są typy jednostek bez kluczy i w związku z tym są założenia tylko do odczytu, ale nie powinny być używane tylko w przypadku, ponieważ typ jednostki musi być tylko do odczytu.
Podobnie często są mapowane do widoków, ale jest to tylko w przypadku, ponieważ często widoki nie określają kluczy.

**Środki zaradcze**

Następujące elementy interfejsu API są obecnie przestarzałe:
* **`ModelBuilder.Query<>()`** — Zamiast tego `ModelBuilder.Entity<>().HasNoKey()` należy wywołać, aby oznaczyć typ jednostki jako posiadające żadnych kluczy.
To będzie nadal nie można skonfigurować zgodnie z Konwencją, aby uniknąć błędnej konfiguracji, gdy klucz podstawowy jest oczekiwany, ale nie jest zgodna z Konwencji.
* **`DbQuery<>`** — Zamiast tego `DbSet<>` powinny być używane.
* **`DbContext.Query<>()`** — Zamiast tego `DbContext.Set<>()` powinny być używane.

## <a name="configuration-api-for-owned-type-relationships-has-changed"></a>Konfiguracja interfejsu API w przypadku relacji należących do typu została zmieniona

[Śledzenie problem #12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[śledzenia problemu #9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[śledzenia problemu #14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)

Ta zmiana została wprowadzona w programu EF Core 3.0 — w wersji zapoznawczej 3.

**Stare zachowanie**

Przed programem EF Core 3.0 to konfiguracja należących do relacji zostało wykonane bezpośrednio po `OwnsOne` lub `OwnsMany` wywołania. 

**Nowe zachowanie**

Począwszy od programu EF Core 3.0 jest teraz wygodnego interfejsu API, aby skonfigurować właściwości nawigacji do właściciela, przy użyciu funkcji `WithOwner()`.
Na przykład:

```C#
modelBuilder.Entity<Order>.OwnsOne(e => e.Details).WithOwner(e => e.Order);
```

Powinny teraz zezwalającym konfiguracji związanych z relacją między właściciela, jak i po `WithOwner()` podobnie do sposobu konfiguracji relacje.
Podczas konfiguracji dla samego typu należące do firmy będą nadal zezwalającym po `OwnsOne()/OwnsMany()`.
Na przykład:

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

Ponadto podczas wywoływania `Entity()`, `HasOne()`, lub `Set()` z typem należących do docelowego teraz spowoduje zgłoszenie wyjątku.

**Dlaczego**

Ta zmiana została wprowadzona do utworzenia jaśniejsze rozdzielenie między skonfigurowaniem należących do samego typu i _relacji_ należących do typu.
To z kolei usuwa niejednoznaczności i pomyłek wokół metod, takich jak `HasForeignKey`.

**Środki zaradcze**

Zmień konfigurację relacje typów należących do używania nowych powierzchni interfejsu API, jak pokazano w powyższym przykładzie.

## <a name="the-foreign-key-property-convention-no-longer-matches-same-name-as-the-principal-property"></a>Konwencja właściwość klucza obcego nie jest już zgodny takiej samej nazwie jak właściwość podmiotu zabezpieczeń

[Śledzenie problem #13274](https://github.com/aspnet/EntityFrameworkCore/issues/13274)

Ta zmiana została wprowadzona w programu EF Core 3.0 — w wersji zapoznawczej 3.

**Stare zachowanie**

Należy wziąć pod uwagę następujący wzór:
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
Przed programem EF Core 3.0 to `CustomerId` właściwość będzie używana dla klucza obcego przez Konwencję.
Jednak jeśli `Order` jest typem należące do firmy, dzięki temu upewnisz się również `CustomerId` klucz podstawowy i to nie jest zazwyczaj oczekuje.

**Nowe zachowanie**

Począwszy od 3.0 programu EF Core nie próbował użyć właściwości kluczy obcych zgodnie z Konwencją, jeśli mają taką samą nazwę jak właściwość jednostki.
Nazwa typu jednostki połączona z nazwą właściwości jednostki, a nazwa nawigacji połączona z wzorców nazwy właściwości podmiotu zabezpieczeń nadal są dopasowywane.
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

**Dlaczego**

Ta zmiana została wprowadzona w celu uniknięcia błędnie Definiowanie właściwości klucza podstawowego w typie należące do firmy.

**Środki zaradcze**

Jeśli właściwość ma to być klucza obcego, a więc część klucza podstawowego, a następnie jawnie skonfigurować go jako takie.

## <a name="each-property-uses-independent-in-memory-integer-key-generation"></a>Każda właściwość używa generowania kluczy niezależnie od liczby całkowitej w pamięci

[Śledzenie problem #6872](https://github.com/aspnet/EntityFrameworkCore/issues/6872)

Ta zmiana zostanie wprowadzona w programu EF Core 3.0 — w wersji zapoznawczej 4.

**Stare zachowanie**

Przed programem EF Core 3.0 to co generator wartości udostępnionego był używany dla wszystkich właściwości klucza liczby całkowitej w pamięci.

**Nowe zachowanie**

Począwszy od programu EF Core 3.0 właściwości klucza każda liczba całkowita pobiera własnego generator wartości podczas korzystania z bazy danych w pamięci.
Ponadto jeśli baza danych zostanie usunięty, generowania klucza jest resetowany dla wszystkich tabel.

**Dlaczego**

Ta zmiana została wprowadzona, aby wyrównać generowania klucza w pamięci do generowania kluczy rzeczywista baza danych i poprawić możliwość izolowania testów od siebie nawzajem, korzystając z bazy danych w pamięci.

**Środki zaradcze**

Może to spowodować awarię aplikacji, która jest polegania na określonych wartości klucza w pamięci do ustawienia.
Rozważ w zamian nie opierając się na konkretnych wartości klucza, lub aktualizacja odpowiadający nowe zachowanie.

## <a name="backing-fields-are-used-by-default"></a>Domyślnie są używane pola zapasowego

[Śledzenie problem #12430](https://github.com/aspnet/EntityFrameworkCore/issues/12430)

Ta zmiana została wprowadzona w programu EF Core 3.0-preview 2.

**Stare zachowanie**

Przed 3.0 nawet wtedy, gdy pole zapasowe dla właściwości jest znane, programem EF Core będzie domyślnie nadal odczytu i zapisu wartości właściwości, za pomocą metody getter i setter właściwości.
Wyjątkiem od tej była wykonywania zapytania, gdzie pole zapasowe będzie miał ustawienie bezpośrednio Jeśli jest znany.

**Nowe zachowanie**

Począwszy od programu EF Core 3.0, jeśli pole zapasowe dla właściwości jest znany, następnie będzie zawsze Odczyt i zapis tej właściwości, za pomocą pola pomocniczego.
Może to spowodować przerwanie aplikacji, jeżeli aplikacja powołuje się na zachowanie dodatkowego kodowane do metody pobierającą czy ustawiającą.

**Dlaczego**

Ta zmiana została wprowadzona aby zapobiec błędnie wyzwalanie logiki biznesowej EF Core domyślnie podczas wykonywania operacji bazy danych dotyczących jednostki.

**Środki zaradcze**

Zachowanie pre 3.0 można przywrócić za pomocą konfiguracji tryb dostępu do właściwości w interfejsie API fluent element modelBuilder.
Na przykład:

```C#
modelBuilder.UsePropertyAccessMode(PropertyAccessMode.PreferFieldDuringConstruction);
```

## <a name="throw-if-multiple-compatible-backing-fields-are-found"></a>Throw, jeśli zostaną znalezione wiele pól zgodnych zapasowy

[Śledzenie problem #12523](https://github.com/aspnet/EntityFrameworkCore/issues/12523)

Ta zmiana zostanie wprowadzona w programu EF Core 3.0 — w wersji zapoznawczej 4.

**Stare zachowanie**

Przed programem EF Core 3.0 Jeśli pasuje wiele pól reguły służące do znajdowania do pola pomocniczego, właściwości, a następnie będzie można wybrać jedno pole na podstawie niektórych kolejność pierwszeństwa.
Może to spowodować nieprawidłowe pole ma być używany w przypadku niejednoznaczne.

**Nowe zachowanie**

Zaczynając od programu EF Core 3.0 to, jeśli wiele pól są dopasowywane do tej samej właściwości, następnie jest zgłaszany wyjątek.

**Dlaczego**

Ta zmiana została wprowadzona w celu uniknięcia dyskretne przy użyciu jedno pole zamiast innego, gdy tylko jeden może być poprawne.

**Środki zaradcze**

Właściwości z polami niejednoznaczne zapasowy musi mieć pole do użycia, jawnie określony.
Na przykład przy użyciu wygodnego interfejsu API:

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .HasField("_id");
```

## <a name="adddbcontextadddbcontextpool-no-longer-call-addlogging-and-addmemorycache"></a>AddDbContext/AddDbContextPool już wywołać AddLogging AddMemoryCache

[Śledzenie problem #14756](https://github.com/aspnet/EntityFrameworkCore/issues/14756)

Ta zmiana zostanie wprowadzona w programu EF Core 3.0 — w wersji zapoznawczej 4.

**Stare zachowanie**

Przed programem EF Core 3.0, wywołanie `AddDbContext` lub `AddDbContextPool` będzie również zarejestrować rejestrowanie i buforowanie usług za pomocą D.I za pośrednictwem wywołania do pamięci [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) i [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).

**Nowe zachowanie**

Począwszy od programu EF Core 3.0 to `AddDbContext` i `AddDbContextPool` nie będą już rejestrowanie tych usług za pomocą iniekcji zależności (DI).

**Dlaczego**

EF Core 3.0 nie wymaga, że te usługi są w cotainer DI aplikacji. Jednak jeśli `ILoggerFactory` jest zarejestrowany w kontenerze DI aplikacji, a następnie nadal będzie on używany przez platformę EF Core.

**Środki zaradcze**

Jeśli Twoja aplikacja potrzebuje tych usług, następnie zarejestruj je jawnie przy użyciu kontenera DI [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) lub [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).

## <a name="dbcontextentry-now-performs-a-local-detectchanges"></a>DbContext.Entry wykonuje obecnie lokalnego metody DetectChanges

[Śledzenie problem #13552](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

Ta zmiana została wprowadzona w programu EF Core 3.0 — w wersji zapoznawczej 3.

**Stare zachowanie**

Przed programem EF Core 3.0, wywołanie `DbContext.Entry` spowodowałoby zmiany zostało wykryte, dla wszystkich śledzonych jednostek.
To zapewnić, że stan ujawnione w `EntityEntry` był aktualny.

**Nowe zachowanie**

Począwszy od programu EF Core 3.0, wywołanie `DbContext.Entry` zostanie teraz tylko próba wykrycia zmiany w danej jednostki, a wszystkie śledzone jednostki głównej odnoszących się do niego.
Oznacza to, że zmiany w innym miejscu może nie została wykryta przez wywołanie tej metody, który może mieć wpływ na stan aplikacji.

Należy pamiętać, że jeśli `ChangeTracker.AutoDetectChangesEnabled` ustawiono `false` , a następnie nawet wykryciem lokalne zmiany zostaną wyłączone.

Inne metody, które powodują wykrywania zmian — na przykład `ChangeTracker.Entries` i `SaveChanges`— nadal powodują pełne `DetectChanges` wszystkich śledzone jednostek.

**Dlaczego**

Ta zmiana została wprowadzona w celu zwiększenia wydajności domyślnego korzystania z `context.Entry`.

**Środki zaradcze**

Wywołaj `ChgangeTracker.DetectChanges()` jawnie przed wywołaniem `Entry` aby zapewnić zachowanie pre 3.0.

## <a name="string-and-byte-array-keys-are-not-client-generated-by-default"></a>Parametry i bajtów klucze tablicy nie są generowane przez klientów domyślnie

[Śledzenie problem #14617](https://github.com/aspnet/EntityFrameworkCore/issues/14617)

Ta zmiana zostanie wprowadzona w programu EF Core 3.0 — w wersji zapoznawczej 4.

**Stare zachowanie**

Przed programem EF Core 3.0 to `string` i `byte[]` właściwości klucza, można użyć bez jawne ustawienie wartości innej niż null.
W takim przypadku wartość klucza powinien zostać wygenerowany na komputerze klienckim jako identyfikatora GUID, serializować do bajtów `byte[]`.

**Nowe zachowanie**

Począwszy od programu EF Core 3.0 wyjątek zostanie zgłoszony, wskazujący, że ustawiono żadnej wartości klucza.

**Dlaczego**

Ta zmiana została wprowadzona ponieważ generowane przez klientów `string` / `byte[]` wartościami ogólnie nie są przydatne i domyślne zachowanie dotarło twardych przeglądanie informacji o wartości klucza wygenerowanego w typowy sposób.

**Środki zaradcze**

Zachowanie pre 3.0 można uzyskać przez jawne określenie, że właściwości klucza należy używać wartości wygenerowany, jeśli jest ustawiona żadna wartość inną niż null.
Na przykład przy użyciu wygodnego interfejsu API:

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedOnAdd();
```

Lub przy użyciu adnotacji danych:

```C#
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public string Id { get; set; }
```

## <a name="iloggerfactory-is-now-a-scoped-service"></a>Element ILoggerFactory jest teraz usługą o określonym zakresie

[Śledzenie problem #14698](https://github.com/aspnet/EntityFrameworkCore/issues/14698)

Ta zmiana została wprowadzona w programu EF Core 3.0 — w wersji zapoznawczej 3.

**Stare zachowanie**

Przed programem EF Core 3.0 to `ILoggerFactory` został zarejestrowany jako usługa pojedynczego wystąpienia.

**Nowe zachowanie**

Począwszy od programu EF Core 3.0 to `ILoggerFactory` został zarejestrowany zgodnie z zakresem.

**Dlaczego**

Ta zmiana została wprowadzona, aby umożliwić skojarzenie rejestratora o `DbContext` wystąpienia, co umożliwia inne funkcje i usuwa czasami patologicznych zachowanie, na przykład rozłożenie wewnętrznego usługodawców.

**Środki zaradcze**

Ta zmiana nie powinna wpływu na kod aplikacji, chyba że jest rejestrowanie, a za pomocą niestandardowych usług dla dostawcy usługi wewnętrznej programu EF Core.
Nie jest to typowe.
W takich przypadkach większości zadań będą nadal działają, ale dowolnej usługi singleton, który został w zależności od `ILoggerFactory` będzie musiał zostać zmienione w celu uzyskania `ILoggerFactory` w inny sposób.

Jeśli napotkasz sytuacjach, w następujący sposób na Zgłoś problem w [narzędzie do śledzenia problemów EF Core w usłudze GitHub](https://github.com/aspnet/EntityFrameworkCore/issues) aby dać nam znać, jak używasz `ILoggerFactory` taki sposób, że firma Microsoft można lepiej zrozumieć, jak się to w przyszłości.

## <a name="idbcontextoptionsextensionwithdebuginfo-merged-into-idbcontextoptionsextension"></a>IDbContextOptionsExtensionWithDebugInfo IDbContextOptionsExtension została scalona z

[Śledzenie problem #13552](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

Ta zmiana została wprowadzona w programu EF Core 3.0 — w wersji zapoznawczej 3.

**Stare zachowanie**

`IDbContextOptionsExtensionWithDebugInfo` przedłużono dodatkowy opcjonalny interfejs z `IDbContextOptionsExtension` Aby uniknąć konieczności istotne zmiany w interfejsie podczas cyklu wersji 2.x.

**Nowe zachowanie**

Interfejsy są teraz scalone razem `IDbContextOptionsExtension`.

**Dlaczego**

Ta zmiana została wprowadzona, ponieważ interfejsy są koncepcyjnie jeden.

**Środki zaradcze**

Wszelkie implementacje `IDbContextOptionsExtension` musi zostać zaktualizowany do obsługi nowego elementu członkowskiego.

## <a name="lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded"></a>Serwery proxy ładowanych z opóźnieniem nie jest już założono, że właściwości nawigacji są w pełni załadowany

[Śledzenie problem #12780](https://github.com/aspnet/EntityFrameworkCore/issues/12780)

Ta zmiana zostanie wprowadzona w programu EF Core 3.0 — w wersji zapoznawczej 4.

**Stare zachowanie**

Przed EF Core 3.0 to raz `DbContext` zlikwidowano nie było możliwości określenia, czy danej właściwości nawigacji jednostki uzyskany z tego kontekstu został w pełni załadowany, czy nie.
Serwery proxy zamiast tego będzie założono, że nawigacji odwołanie jest ładowany, jeśli ma on wartość inną niż null i że nawigacji kolekcji jest ładowany, jeśli nie jest pusty.
W takich przypadkach próby ładowanych z opóźnieniem będzie pusta.

**Nowe zachowanie**

Począwszy od programu EF Core 3.0, serwery proxy zachować informacje o informację określającą, czy właściwość nawigacji jest ładowany.
Oznacza to, że próba dostępu do właściwości nawigacji, który jest ładowany po został usunięty kontekst zawsze będzie pusta, nawet wtedy, gdy załadowano Nawigacja jest pusty lub ma wartość null.
Z drugiej strony próby uzyskania dostępu do właściwości nawigacji, który nie został załadowany, spowoduje zgłoszenie wyjątku, jeśli kontekst zostanie usunięty, nawet jeśli właściwość nawigacji jest pusta kolekcja.
W razie takiej sytuacji, oznacza to, próbę wykorzystania ładowanych z opóźnieniem w czasie nieprawidłowy kod aplikacji i aplikacji, należy zmienić należy tego robić.

**Dlaczego**

Ta zmiana została wprowadzona się zachowanie spójnego i prawidłowe przy próbie z opóźnieniem obciążenia na usunięte `DbContext` wystąpienia.

**Środki zaradcze**

Zaktualizować kod aplikacji, aby nie podejmował prób powolne ładowanie kontekstu usunięte lub skonfigurować, aby była pusta, zgodnie z opisem w komunikat o wyjątku.

## <a name="excessive-creation-of-internal-service-providers-is-now-an-error-by-default"></a>Nadmierne tworzenia dostawców usług wewnętrznego błędu teraz domyślnie

[Śledzenie problem #10236](https://github.com/aspnet/EntityFrameworkCore/issues/10236)

Ta zmiana została wprowadzona w programu EF Core 3.0 — w wersji zapoznawczej 3.

**Stare zachowanie**

Przed programem EF Core 3.0 to ostrzeżenie zostanie zarejestrowany dla aplikacji utworzenie patologicznych numeru wewnętrznego usługodawców.

**Nowe zachowanie**

Począwszy od programu EF Core 3.0 to ostrzeżenie została uznana za i zostanie zgłoszony błąd i wyjątek. 

**Dlaczego**

Ta zmiana została wprowadzona w celu tworzenia lepszych kod aplikacji za pośrednictwem udostępnianie takim patologicznych bardziej jawny sposób.

**Środki zaradcze**

Najbardziej odpowiednią przyczynę akcji po wystąpieniu tego błędu jest poznać główną przyczynę i Zatrzymaj tworzenie tak wielu dostawców usługi wewnętrznej.
Jednak błąd można przekonwertować ostrzeżenie (lub ignorowane) za pośrednictwem konfiguracji na `DbContextOptionsBuilder`.
Na przykład:

```C#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .ConfigureWarnings(w => w.Log(CoreEventId.ManyServiceProvidersCreatedWarning));
}
```

## <a name="new-behavior-for-hasonehasmany-called-with-a-single-string"></a>Nowe zachowanie HasOne/HasMany volat pojedynczy ciąg

[Śledzenie problem #9171](https://github.com/aspnet/EntityFrameworkCore/issues/9171)

Ta zmiana zostanie wprowadzona w programu EF Core 3.0 — w wersji zapoznawczej 4.

**Stare zachowanie**

Przed programem EF Core 3.0 to kod wywoływania `HasOne` lub `HasMany` przy użyciu jednego ciągu została zinterpretowana w sposób mylące.
Na przykład:
```C#
modelBuilder.Entity<Samurai>().HasOne("Entrance").WithOne();
```

Kod wygląda na to jest odnoszących się `Samurai` do niektóre inne jednostki typu przy użyciu `Entrance` właściwość nawigacji, która może być prywatny.

W rzeczywistości, ten kod próbuje utworzyć relację pewnego typu jednostki o nazwie `Entrance` przy użyciu żadnej właściwości nawigacji.

**Nowe zachowanie**

Począwszy od programu EF Core 3.0 powyższy kod teraz robi co wyglądało na to powinno mieć czynności przed.

**Dlaczego**

Stare zachowanie było mylące, szczególnie podczas odczytywania kodu konfiguracji i wyszukiwania błędów.

**Środki zaradcze**

Spowoduje to przerwanie tylko aplikacji, które jawnego konfigurowania relacji za pomocą ciągów, nazwy typów oraz bez jawne określenie właściwości nawigacji.
To nie jest powszechne.
Poprzednie zachowanie można uzyskać poprzez jawne przekazywanie `null` dla danej nazwy właściwości nawigacji.
Na przykład:

```C#
modelBuilder.Entity<Samurai>().HasOne("Some.Entity.Type.Name", null).WithOne();
```

## <a name="the-relationaltypemapping-annotation-is-now-just-typemapping"></a>Adnotacja relacyjne: właściwość TypeMapping jest obecnie tylko właściwość TypeMapping.

[Śledzenie problem #9913](https://github.com/aspnet/EntityFrameworkCore/issues/9913)

Ta zmiana została wprowadzona w programu EF Core 3.0-preview 2.

**Stare zachowanie**

Nazwa adnotacji dla adnotacji mapowania typu ma wartość "Relacyjne: właściwość TypeMapping".

**Nowe zachowanie**

Nazwa adnotacji dla adnotacji mapowania typu jest teraz "Właściwość TypeMapping".

**Dlaczego**

Mapowanie typu teraz są używane dla dostawców więcej niż tylko relacyjnej bazy danych.

**Środki zaradcze**

Spowoduje to przerwanie tylko aplikacje uzyskujące dostęp do mapowania typów bezpośrednio jako adnotacja nie jest wspólne.
Najbardziej odpowiednią akcję, aby rozwiązać problem polega na użyciu powierzchni interfejsu API do mapowania typu dostępu, a nie bezpośrednio przy użyciu adnotacji.

## <a name="totable-on-a-derived-type-throws-an-exception"></a>Zgłasza wyjątek, ToTable na typ pochodny 

[Śledzenie problem #11811](https://github.com/aspnet/EntityFrameworkCore/issues/11811)

Ta zmiana została wprowadzona w programu EF Core 3.0 — w wersji zapoznawczej 3.

**Stare zachowanie**

Przed programem EF Core 3.0 to `ToTable()` wywoływana na pochodnego typu będzie zignorowany, ponieważ tylko strategii mapowania dziedziczenia został TPH, w którym jest on nieprawidłowy. 

**Nowe zachowanie**

Uruchamianie przy użyciu programu EF Core 3.0 i w ramach przygotowania do dodawania TPT i TPC pomocy technicznej w nowszej wersji, `ToTable()` o nazwie na typ pochodny będą teraz throw wyjątek, aby uniknąć zmian nieoczekiwane mapowanie w przyszłości.

**Dlaczego**

Obecnie nie jest prawidłowy do mapowania typu pochodnego do innej tabeli.
Ta zmiana eliminuje istotne w przyszłości, kiedy stanie się nieprawidłowy niczego robić.

**Środki zaradcze**

Usuń wszelkie próby mapowania typów pochodnych do innych tabel.

## <a name="forsqlserverhasindex-replaced-with-hasindex"></a>ForSqlServerHasIndex zastąpione HasIndex 

[Śledzenie problem #12366](https://github.com/aspnet/EntityFrameworkCore/issues/12366)

Ta zmiana została wprowadzona w programu EF Core 3.0 — w wersji zapoznawczej 3.

**Stare zachowanie**

Przed programem EF Core 3.0 to `ForSqlServerHasIndex().ForSqlServerInclude()` podany sposób konfigurowania kolumn używanych z `INCLUDE`.

**Nowe zachowanie**

Począwszy od programu EF Core 3.0, przy użyciu `Include` w indeksie jest teraz obsługiwane na poziomie relacyjnych.
Użyj `HasIndex().ForSqlServerInclude()`.

**Dlaczego**

Ta zmiana została wprowadzona w celu skonsolidowania interfejsu API dla indeksów, z `Includes` w jednym miejscu dla wszystkich dostawców w bazie danych.

**Środki zaradcze**

Użyj nowego interfejsu API, jak pokazano powyżej.

## <a name="ef-core-no-longer-sends-pragma-for-sqlite-fk-enforcement"></a>EF Core nie jest już wysyła pragmy do wymuszania klucza Obcego SQLite

[Śledzenie problem #12151](https://github.com/aspnet/EntityFrameworkCore/issues/12151)

Ta zmiana została wprowadzona w programu EF Core 3.0 — w wersji zapoznawczej 3.

**Stare zachowanie**

Przed programem EF Core 3.0 to prześle programu EF Core `PRAGMA foreign_keys = 1` po otwarciu połączenia bazy danych SQLite.

**Nowe zachowanie**

Począwszy od programu EF Core 3.0 programu EF Core nie jest już wysyła `PRAGMA foreign_keys = 1` po otwarciu połączenia bazy danych SQLite.

**Dlaczego**

Ta zmiana została wprowadzona, ponieważ korzysta z programu EF Core `SQLitePCLRaw.bundle_e_sqlite3` domyślnie, co z kolei oznacza, że wymuszania klucza Obcego jest włączone domyślnie i nie muszą być jawnie włączone każdorazowo, połączenie jest otwarte.

**Środki zaradcze**

Kluczy obcych są domyślnie włączone w SQLitePCLRaw.bundle_e_sqlite3, który jest używany domyślnie dla platformy EF Core.
W pozostałych przypadkach można włączyć kluczy obcych, określając `Foreign Keys=True` w ciągu połączenia.

## <a name="microsoftentityframeworkcoresqlite-now-depends-on-sqlitepclrawbundleesqlite3"></a>Microsoft.EntityFrameworkCore.Sqlite zależy od teraz SQLitePCLRaw.bundle_e_sqlite3

**Stare zachowanie**

Przed programem EF Core 3.0, użyć programu EF Core `SQLitePCLRaw.bundle_green`.

**Nowe zachowanie**

Począwszy od programu EF Core 3.0 korzysta z programu EF Core `SQLitePCLRaw.bundle_e_sqlite3`.

**Dlaczego**

Ta zmiana została wprowadzona, co powoduje wersję bazy danych SQLite w systemie iOS zgodne z innymi platformami.

**Środki zaradcze**

Aby korzystać z natywnych wersji bazy danych SQLite w systemie iOS, należy skonfigurować `Microsoft.Data.Sqlite` zostać użyty inny `SQLitePCLRaw` pakietu.

## <a name="guid-values-are-now-stored-as-text-on-sqlite"></a>Wartości identyfikatora GUID są obecnie przechowywane jako tekst w SQLite

[Śledzenie problem #15078](https://github.com/aspnet/EntityFrameworkCore/issues/15078)

Ta zmiana została wprowadzona w programu EF Core 3.0 — w wersji zapoznawczej 4.

**Stare zachowanie**

Identyfikator GUID wartości zostały wcześniej sored jako wartości obiektu BLOB dla bazy danych SQLite.

**Nowe zachowanie**

Wartości identyfikatora GUID są teraz sotred jako tekst.

**Dlaczego**

Format binarny identyfikatorów GUID nie jest standardowym. Przechowywanie wartości jako tekst sprawia, że baza danych większą zgodność z innymi technologiami.

**Środki zaradcze**

Aby Migrowanie istniejących baz danych do nowego formatu, wykonywanie SQL, jak pokazano poniżej.

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

W programie EF Core można także kontynuować, używając poprzednie zachowanie configuirng konwertera wartości tych właściwości.

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.GuidProperty)
    .HasConversion(
        g => g.ToByteArray(),
        b => new Guid(b));
```

Microsoft.Data.Sqlite pozostaje możliwość odczytywania wartości identyfikatora Guid z kolumny obiektów BLOB i tekst; Jednakże ponieważ zmieniono domyślny format parametrów i stałych prawdopodobnie należy podjąć działania w przypadku większości scenariuszy obejmujących identyfikatorów GUID.

## <a name="char-values-are-now-stored-as-text-on-sqlite"></a>Wartości char są obecnie przechowywane jako tekst w SQLite

[Śledzenie problem #15020](https://github.com/aspnet/EntityFrameworkCore/issues/15020)

Ta zmiana została wprowadzona w programu EF Core 3.0 — w wersji zapoznawczej 4.

**Stare zachowanie**

Wartości char zostały wcześniej sored jako wartości całkowite na bazy danych SQLite. Na przykład wartości znakowej na wartość z *A* została zapisana jako wartość całkowitą 65.

**Nowe zachowanie**

Wartości char są teraz sotred jako tekst.

**Dlaczego**

Przechowywanie wartości jako tekst jest bardziej naturalne i sprawia, że baza danych jest bardziej zgodne z innymi technologiami.

**Środki zaradcze**

Aby Migrowanie istniejących baz danych do nowego formatu, wykonywanie SQL, jak pokazano poniżej.

``` sql
UPDATE MyTable
SET CharColumn = char(CharColumn)
WHERE typeof(CharColumn) = 'integer';
```

W programie EF Core można także kontynuować, używając poprzednie zachowanie configuirng konwertera wartości tych właściwości.

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.CharProperty)
    .HasConversion(
        c => (long)c,
        i => (char)i);
```

Microsoft.Data.Sqlite pozostaje też obecna mogą odczytywać wartości znakowych z kolumn tekst i liczby całkowitej, więc w niektórych scenariuszach może nie wymagają żadnych działań.

## <a name="migration-ids-are-now-generated-using-the-invariant-cultures-calendar"></a>Migracja identyfikatorów są teraz generowane przy użyciu kalendarza kultury niezmiennej

[Śledzenie problem #12978](https://github.com/aspnet/EntityFrameworkCore/issues/12978)

Ta zmiana została wprowadzona w programu EF Core 3.0 — w wersji zapoznawczej 4.

**Stare zachowanie**

Migracja identyfikatorów były generowane przy użyciu kalendarza kultury currret inadvertantly.

**Nowe zachowanie**

Migracja identyfikatorów są teraz zawsze generowane przy użyciu niezmiennej kultury kalendarza (gregoriańskiego).

**Dlaczego**

Kolejność migracji jest ważne w przypadku aktualizowania bazy danych lub Rozwiązywanie konfliktów scalania. Przy użyciu niezmiennej kalendarza pozwala uniknąć porządkowanie problemów mogących powodować od członków zespołu o kalendarzach różnych systemów.

**Środki zaradcze**

Ta zmiana dotyczy wszystkich osób korzystających kalendarzowe innych niż gregoriański, gdzie rok jest większa niż kalendarz gregoriański (np. tajski kalendarz buddyjski). Migracji istniejących identyfikatorów będą musiały zostać zaktualizowane, tak, aby nowe migracje są uporządkowane od istniejących migracji.

Identyfikator migracji można znaleźć w atrybucie migracji w plikach projektanta migracji.

``` diff
 [DbContext(typeof(MyDbContext))]
-[Migration("25620318122820_MyMigration")]
+[Migration("20190318122820_MyMigration")]
 partial class MyMigration
 {
```

Tabela migracji historii musi zostać zaktualizowany.

``` sql
UPDATE __EFMigrationsHistory
SET MigrationId = CONCAT(LEFT(MigrationId, 4)  - 543, SUBSTRING(MigrationId, 4, 150))
```

## <a name="logquerypossibleexceptionwithaggregateoperator-has-been-renamed"></a>LogQueryPossibleExceptionWithAggregateOperator została zmieniona.

[Śledzenie problem #10985](https://github.com/aspnet/EntityFrameworkCore/issues/10985)

Ta zmiana została wprowadzona w programu EF Core 3.0 — w wersji zapoznawczej 4.

**Change**

`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` została zmieniona na `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.

**Dlaczego**

Wyrównuje nazewnictwa to zdarzenie ostrzegawcze z innymi zdarzeniami ostrzeżeń.

**Środki zaradcze**

Użycie nowej nazwy. (Zwróć uwagę, czy nie zmieniono numer identyfikacyjny zdarzeń).

## <a name="clarify-api-for-foreign-key-constraint-names"></a>Wyjaśnienie interfejsu API dla nazw ograniczenie klucza obcego

[Śledzenie problem #10730](https://github.com/aspnet/EntityFrameworkCore/issues/10730)

Ta zmiana została wprowadzona w programu EF Core 3.0 — w wersji zapoznawczej 4.

**Stare zachowanie**

Przed programem EF Core 3.0 to ograniczenie klucza obcego nazwy były nazywane po prostu "name". Na przykład:

```C#
var constraintName = myForeignKey.Name;
```

**Nowe zachowanie**

Począwszy od programu EF Core 3.0, ograniczenie klucza obcego nazwy są teraz określane jako "tego name". Na przykład:

```C#
var constraintName = myForeignKey.ConstraintName;
```

**Dlaczego**

Ta zmiana oferuje spójność nazw w tym obszarze, a także wyjaśnia, że jest to nazwa nazwy klucza obcego tego, a nie kolumny lub właściwość klucza obcego zdefiniowany na.

**Środki zaradcze**

Użycie nowej nazwy.