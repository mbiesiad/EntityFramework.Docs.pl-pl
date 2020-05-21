---
title: Co nowego w EF Core 2,0 — EF Core
author: divega
ms.date: 02/20/2018
ms.assetid: 2CB5809E-0EFB-44F6-AF14-9D5BFFFBFF9D
uid: core/what-is-new/ef-core-2.0
ms.openlocfilehash: a3e066056fc67031060920f5f7763007bdc1d2d3
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672887"
---
# <a name="new-features-in-ef-core-20"></a>Nowe funkcje w EF Core 2,0

## <a name="net-standard-20"></a>.NET Standard 2,0

EF Core teraz dotyczy .NET Standard 2,0, co oznacza, że może współdziałać z platformą .NET Core 2,0, .NET Framework 4.6.1 i innymi bibliotekami, które implementują .NET Standard 2,0.
Zobacz [obsługiwane implementacje platformy .NET](../platforms/index.md) , aby uzyskać więcej informacji na temat tego, co jest obsługiwane.

## <a name="modeling"></a>Modelowanie

### <a name="table-splitting"></a>Dzielenie tabeli

Teraz można zmapować dwa lub więcej typów jednostek do tej samej tabeli, w której kolumny klucza podstawowego zostaną udostępnione, a każdy wiersz będzie odpowiadać co najmniej dwóm jednostkom.

Aby użyć dzielenia tabeli, należy skonfigurować relację identyfikującą (gdzie właściwości klucza obcego w formularzu klucz podstawowy) muszą być skonfigurowane między wszystkimi typami jednostek udostępniającymi tabelę:

``` csharp
modelBuilder.Entity<Product>()
    .HasOne(e => e.Details).WithOne(e => e.Product)
    .HasForeignKey<ProductDetails>(e => e.Id);
modelBuilder.Entity<Product>().ToTable("Products");
modelBuilder.Entity<ProductDetails>().ToTable("Products");
```

Zapoznaj się z [sekcją dotyczącą dzielenia tabeli](xref:core/modeling/table-splitting) , aby uzyskać więcej informacji na temat tej funkcji.

### <a name="owned-types"></a>Typy własności

Typ jednostki będącej własnością może współużytkować ten sam typ .NET z innym typem jednostki będącej własnością, ale ponieważ nie można go zidentyfikować tylko przez typ .NET, musi istnieć Nawigacja z innego typu jednostki. Jednostką zawierającą zdefiniowaną nawigację jest właściciel. Podczas wykonywania zapytania dotyczącego właściciela typy będą uwzględniane domyślnie.

Według Konwencji klucz podstawowy w tle zostanie utworzony dla typu będącego własnością i zostanie zamapowany do tej samej tabeli co właściciel przy użyciu dzielenia tabeli. Pozwala to na używanie typów posiadanych w sposób podobny do sposobu używania typów złożonych w EF6:

``` csharp
modelBuilder.Entity<Order>().OwnsOne(p => p.OrderDetails, cb =>
    {
        cb.OwnsOne(c => c.BillingAddress);
        cb.OwnsOne(c => c.ShippingAddress);
    });

public class Order
{
    public int Id { get; set; }
    public OrderDetails OrderDetails { get; set; }
}

public class OrderDetails
{
    public StreetAddress BillingAddress { get; set; }
    public StreetAddress ShippingAddress { get; set; }
}

public class StreetAddress
{
    public string Street { get; set; }
    public string City { get; set; }
}
```

Aby uzyskać więcej informacji na temat tej funkcji, zapoznaj się z [sekcją należącej do typów jednostek](xref:core/modeling/owned-entities) .

### <a name="model-level-query-filters"></a>Filtry zapytań na poziomie modelu

EF Core 2,0 zawiera nową funkcję wywołującą filtry zapytań na poziomie modelu. Ta funkcja zezwala na predykaty zapytań LINQ (wyrażenie logiczne zwykle przenoszone do składnika LINQ WHERE Query operator) do zdefiniowania bezpośrednio w typach jednostek w modelu metadanych (zwykle w OnModelCreating). Takie filtry są automatycznie stosowane do dowolnych zapytań LINQ obejmujących te typy jednostek, w tym typy jednostek, do których odwołuje się pośrednio, na przykład przy użyciu odwołań do właściwości dołączania lub nawigacji bezpośredniej. Niektóre typowe aplikacje tej funkcji to:

- Usuwanie nietrwałe — typy jednostek definiują Właściwość IsDeleted.
- Wielodostępność — typ jednostki definiuje Właściwość TenantId.

Oto prosty przykład demonstrujący funkcję dla dwóch wymienionych powyżej scenariuszy:

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    public int TenantId { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Post>().HasQueryFilter(
            p => !p.IsDeleted
            && p.TenantId == this.TenantId);
    }
}
```

Definiujemy filtr na poziomie modelu, który implementuje wiele dzierżawców i usuwanie nietrwałe dla wystąpień `Post` typu jednostki. Zwróć uwagę na użycie `DbContext` Właściwości poziomu wystąpienia: `TenantId` . Filtry na poziomie modelu będą używać wartości z poprawnego wystąpienia kontekstu (czyli wystąpienia kontekstu, które wykonuje zapytanie).

Filtry mogą być wyłączone dla poszczególnych zapytań LINQ przy użyciu operatora IgnoreQueryFilters ().

#### <a name="limitations"></a>Ograniczenia

- Odwołania nawigacji są niedozwolone. Ta funkcja może zostać dodana na podstawie opinii.
- Filtry można definiować tylko w typie jednostki głównej hierarchii.

### <a name="database-scalar-function-mapping"></a>Mapowanie funkcji skalarnej bazy danych

EF Core 2,0 zawiera istotny udział od [Paul Middleton](https://github.com/pmiddleton) , który umożliwia mapowanie funkcji skalarnych bazy danych na metody pośredniczące, tak aby mogły one być używane w zapytaniach LINQ i TŁUMACZONE na SQL.

Poniżej znajduje się krótki opis sposobu użycia funkcji:

Zadeklaruj metodę statyczną w `DbContext` i Dodaj do niej adnotację przy użyciu `DbFunctionAttribute` :

``` csharp
public class BloggingContext : DbContext
{
    [DbFunction]
    public static int PostReadCount(int blogId)
    {
        throw new NotImplementedException();
    }
}
```

Metody takie jak te są rejestrowane automatycznie. Po zarejestrowaniu wywołania metody w zapytaniu LINQ można przetłumaczyć na wywołania funkcji w programie SQL:

``` csharp
var query =
    from p in context.Posts
    where BloggingContext.PostReadCount(p.Id) > 5
    select p;
```

Kilka kwestii, na które warto zwrócić uwagę:

- Zgodnie z Konwencją nazwa metody jest używana jako nazwa funkcji (w tym przypadku funkcji zdefiniowanej przez użytkownika) podczas generowania kodu SQL, ale można zastąpić nazwę i schemat podczas rejestracji metody.
- Obecnie obsługiwane są tylko funkcje skalarne.
- Należy utworzyć zamapowanej funkcji w bazie danych. Migracje EF Core nie zapewnią ich tworzenia.

### <a name="self-contained-type-configuration-for-code-first"></a>Samodzielna konfiguracja typu dla kodu

W EF6 można hermetyzować kod pierwszej konfiguracji określonego typu jednostki, pobierając je z *EntityTypeConfiguration*. W EF Core 2,0 ten wzorzec zostanie przywrócony:

``` csharp
class CustomerConfiguration : IEntityTypeConfiguration<Customer>
{
    public void Configure(EntityTypeBuilder<Customer> builder)
    {
        builder.HasKey(c => c.AlternateKey);
        builder.Property(c => c.Name).HasMaxLength(200);
    }
}

...
// OnModelCreating
builder.ApplyConfiguration(new CustomerConfiguration());
```

## <a name="high-performance"></a>Wysoka wydajność

### <a name="dbcontext-pooling"></a>Buforowanie kontekstu DbContext

Wzorzec podstawowy służący do używania EF Core w aplikacji ASP.NET Core zazwyczaj polega na zarejestrowaniu niestandardowego typu kontekstu DBW systemie iniekcji zależności i późniejszym uzyskaniu wystąpień tego typu przez parametry konstruktora w kontrolerach. Oznacza to, że nowe wystąpienie DbContext jest tworzone dla każdego żądania.

W wersji 2,0 wprowadzamy nowy sposób rejestrowania niestandardowych typów kontekstu DbContext w iniekcji zależności, które w sposób przezroczysty wprowadzają pulę wystąpień DbContext wielokrotnego użytku. Aby użyć usługi DbContext Pool, użyj `AddDbContextPool` zamiast `AddDbContext` podczas rejestracji w usłudze:

``` csharp
services.AddDbContextPool<BloggingContext>(
    options => options.UseSqlServer(connectionString));
```

Jeśli ta metoda jest używana, w chwili, gdy wystąpienie DbContext żąda żądania przez kontroler, należy najpierw sprawdzić, czy w puli jest dostępne wystąpienie. Po zakończeniu przetwarzania żądania wszystkie Stany wystąpienia zostaną zresetowane, a wystąpienie zostanie zwrócone do puli.

Jest to koncepcyjnie podobne do sposobu, w jaki pula połączeń działa w ADO.NET dostawcy i ma zalety zapisania niektórych kosztów inicjalizacji wystąpienia DbContext.

### <a name="limitations"></a>Ograniczenia

Nowa metoda wprowadza kilka ograniczeń dotyczących tego, co można zrobić w `OnConfiguring()` metodzie DbContext.

> [!WARNING]  
> Unikaj korzystania z puli kontekstu DbContext, Jeśli przechowujesz własny stan (na przykład pola prywatne) w klasie pochodnej DbContext, która nie powinna być współdzielona między żądaniami. EF Core resetuje tylko ten stan, który zna przed dodaniem wystąpienia DbContext do puli.

### <a name="explicitly-compiled-queries"></a>Jawne skompilowane zapytania

Jest to druga funkcja wydajności, która umożliwia oferowanie korzyści w scenariuszach o dużej skali.

Ręcznie lub jawnie skompilowane interfejsy API zapytań są dostępne w poprzednich wersjach EF, a także w LINQ to SQL, aby umożliwić aplikacjom buforowanie zapytań, aby mogły być obliczane tylko raz i wykonywane wiele razy.

Mimo że w ogólnym EF Core można automatycznie kompilować i buforować zapytania w oparciu o skrótową reprezentację wyrażeń zapytania, ten mechanizm może służyć do uzyskania małego wzmocnienia wydajności, pomijając obliczenia skrótu i przeszukiwania pamięci podręcznej, umożliwiając aplikacji użycie już skompilowanego zapytania za pośrednictwem wywołania delegata.

``` csharp
// Create an explicitly compiled query
private static Func<CustomerContext, int, Customer> _customerById =
    EF.CompileQuery((CustomerContext db, int id) =>
        db.Customers
            .Include(c => c.Address)
            .Single(c => c.Id == id));

// Use the compiled query by invoking it
using (var db = new CustomerContext())
{
   var customer = _customerById(db, 147);
}
```

## <a name="change-tracking"></a>Śledzenie zmian

### <a name="attach-can-track-a-graph-of-new-and-existing-entities"></a>Attach można śledzić Graf nowych i istniejących jednostek

EF Core obsługuje automatyczne generowanie wartości kluczy za pomocą różnych mechanizmów. W przypadku korzystania z tej funkcji jest generowana wartość, jeśli właściwość klucza jest wartością domyślną środowiska CLR — zazwyczaj zero lub null. Oznacza to, że wykres jednostek może być przekazanie do `DbContext.Attach` lub `DbSet.Attach` , a EF Core będzie oznaczać te jednostki, dla których klucz jest już ustawiony, ponieważ `Unchanged` te jednostki, które nie mają zestawu kluczy, będą oznaczone jako `Added` . Dzięki temu można łatwo dołączać wykres mieszanych nowych i istniejących jednostek podczas korzystania z wygenerowanych kluczy. `DbContext.Update`i `DbSet.Update` Pracuj w taki sam sposób, z tą różnicą, że jednostki z zestawem kluczy są oznaczone jako `Modified` zamiast `Unchanged` .

## <a name="query"></a>Zapytanie

### <a name="improved-linq-translation"></a>Ulepszone tłumaczenie LINQ

Umożliwia pomyślne wykonanie większej liczby zapytań, dzięki czemu w bazie danych jest przeprowadzana większa logika (a nie w pamięci) i mniejszej ilości danych pobieranych z bazy danych.

### <a name="groupjoin-improvements"></a>Udoskonalenia GroupJoin —

To działanie poprawi SQL wygenerowanego dla sprzężeń grup. Sprzężenia grup najczęściej są wynikiem podzapytania w przypadku opcjonalnych właściwości nawigacji.

### <a name="string-interpolation-in-fromsql-and-executesqlcommand"></a>Interpolacja ciągów w Z tabel i ExecuteSqlCommand

W języku c# 6 wprowadzono interpolację ciągów, funkcję, która umożliwia wyrażeniem języka C#, które mają być bezpośrednio osadzone w literałach ciągów, co zapewnia świetny sposób tworzenia ciągów w czasie wykonywania. W EF Core 2,0 dodaliśmy specjalną obsługę ciągów interpolowanych do naszych dwóch głównych interfejsów API, które akceptują surowe ciągi SQL: `FromSql` i `ExecuteSqlCommand` . Ta nowa obsługa umożliwia interpolację ciągów języka C# do użycia w sposób bezpieczny. Oznacza to, że w sposób chroniący przed typowymi błędami iniekcji SQL, które mogą wystąpić podczas dynamicznego konstruowania bazy danych SQL w środowisku uruchomieniowym.

Oto przykład:

``` csharp
var city = "London";
var contactTitle = "Sales Representative";

using (var context = CreateContext())
{
    context.Set<Customer>()
        .FromSql($@"
            SELECT *
            FROM ""Customers""
            WHERE ""City"" = {city} AND
                ""ContactTitle"" = {contactTitle}")
            .ToArray();
  }
```

W tym przykładzie w ciągu formatu SQL są osadzone dwie zmienne. EF Core będzie generować następujące SQL:

```sql
@p0='London' (Size = 4000)
@p1='Sales Representative' (Size = 4000)

SELECT *
FROM ""Customers""
WHERE ""City"" = @p0
    AND ""ContactTitle"" = @p1
```

### <a name="effunctionslike"></a>Bieżąco. Functions. like ()

Dodaliśmy EF. Właściwość Functions, która może być używana przez EF Core lub dostawców do definiowania metod, które mapują na funkcje bazy danych lub operatory, aby mogły być wywoływane w zapytaniach LINQ. Pierwszym przykładem takiej metody jest like ():

``` csharp
var aCustomers =
    from c in context.Customers
    where EF.Functions.Like(c.Name, "a%")
    select c;
```

Należy pamiętać, że takie jak () jest dostarczane z implementacją w pamięci, która może być przydatna podczas pracy z bazą danych w pamięci lub kiedy Ocena predykatu musi następować po stronie klienta.

## <a name="database-management"></a>Zarządzanie bazami danych

### <a name="pluralization-hook-for-dbcontext-scaffolding"></a>Hak pluralizacja dla szkieletu DbContext

EF Core 2,0 wprowadza nową usługę *IPluralizer* , która jest używana do nazwom nazw typów jednostek i pluralize nieogólnymi. Domyślna implementacja to no-op, więc jest to element Hook, gdzie osób może łatwo podłączyć własne pluralizer.

Oto co wygląda na to, aby deweloper mógł go podłączyć do swoich własnych pluralizer:

``` csharp
public class MyDesignTimeServices : IDesignTimeServices
{
    public void ConfigureDesignTimeServices(IServiceCollection services)
    {
        services.AddSingleton<IPluralizer, MyPluralizer>();
    }
}

public class MyPluralizer : IPluralizer
{
    public string Pluralize(string name)
    {
        return Inflector.Inflector.Pluralize(name) ?? name;
    }

    public string Singularize(string name)
    {
        return Inflector.Inflector.Singularize(name) ?? name;
    }
}
```

## <a name="others"></a>Inne

### <a name="move-adonet-sqlite-provider-to-sqlitepclraw"></a>Przenieś dostawcę oprogramowania SQLite ADO.NET do SQLitePCL. Raw

Zapewnia to bardziej niezawodne rozwiązanie w firmie Microsoft. Data. sqlite do dystrybucji natywnych plików binarnych oprogramowania SQLite na różnych platformach.

### <a name="only-one-provider-per-model"></a>Tylko jeden dostawca na model

Znacznie rozszerza, jak dostawcy mogą współdziałać z modelem i upraszczają konwencje, adnotacje i interfejsy API Fluent współpracują z różnymi dostawcami.

EF Core 2,0 będzie teraz kompilować inne [IModel](https://github.com/aspnet/EntityFramework/blob/master/src/EFCore/Metadata/IModel.cs) dla każdego używanego dostawcy. Jest to zazwyczaj przezroczyste dla aplikacji. Ułatwia to uproszczenie interfejsów API metadanych niższego poziomu, takich jak każdy dostęp do *wspólnych koncepcji metadanych relacyjnych* jest zawsze realizowany przez wywołanie `.Relational` zamiast `.SqlServer` , `.Sqlite` itp.

### <a name="consolidated-logging-and-diagnostics"></a>Rejestrowanie skonsolidowane i Diagnostyka

Rejestrowanie (oparte na ILogger) i Diagnostyka (oparta na DiagnosticSource) teraz udostępnia więcej kodu.

Identyfikatory zdarzeń dla komunikatów wysyłanych do ILogger uległy zmianie w 2,0. Identyfikatory zdarzeń są teraz unikatowe w EF Core kodzie. Te komunikaty są teraz zgodne ze standardowym wzorcem rejestrowania strukturalnego używanego przez program, na przykład MVC.

Zmieniono także kategorie rejestratora. Istnieje teraz dobrze znany zestaw kategorii, do których można uzyskać dostęp za pomocą [DbLoggerCategory](https://github.com/aspnet/EntityFramework/blob/master/src/EFCore/DbLoggerCategory.cs).

Zdarzenia DiagnosticSource teraz używają tych samych nazw identyfikatorów zdarzeń co odpowiadające im `ILogger` komunikaty.
