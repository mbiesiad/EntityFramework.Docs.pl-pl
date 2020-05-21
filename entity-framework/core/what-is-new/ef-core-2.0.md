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
# <a name="new-features-in-ef-core-20"></a><span data-ttu-id="8ee11-102">Nowe funkcje w EF Core 2,0</span><span class="sxs-lookup"><span data-stu-id="8ee11-102">New features in EF Core 2.0</span></span>

## <a name="net-standard-20"></a><span data-ttu-id="8ee11-103">.NET Standard 2,0</span><span class="sxs-lookup"><span data-stu-id="8ee11-103">.NET Standard 2.0</span></span>

<span data-ttu-id="8ee11-104">EF Core teraz dotyczy .NET Standard 2,0, co oznacza, że może współdziałać z platformą .NET Core 2,0, .NET Framework 4.6.1 i innymi bibliotekami, które implementują .NET Standard 2,0.</span><span class="sxs-lookup"><span data-stu-id="8ee11-104">EF Core now targets .NET Standard 2.0, which means it can work with .NET Core 2.0, .NET Framework 4.6.1, and other libraries that implement .NET Standard 2.0.</span></span>
<span data-ttu-id="8ee11-105">Zobacz [obsługiwane implementacje platformy .NET](../platforms/index.md) , aby uzyskać więcej informacji na temat tego, co jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="8ee11-105">See [Supported .NET Implementations](../platforms/index.md) for more details on what is supported.</span></span>

## <a name="modeling"></a><span data-ttu-id="8ee11-106">Modelowanie</span><span class="sxs-lookup"><span data-stu-id="8ee11-106">Modeling</span></span>

### <a name="table-splitting"></a><span data-ttu-id="8ee11-107">Dzielenie tabeli</span><span class="sxs-lookup"><span data-stu-id="8ee11-107">Table splitting</span></span>

<span data-ttu-id="8ee11-108">Teraz można zmapować dwa lub więcej typów jednostek do tej samej tabeli, w której kolumny klucza podstawowego zostaną udostępnione, a każdy wiersz będzie odpowiadać co najmniej dwóm jednostkom.</span><span class="sxs-lookup"><span data-stu-id="8ee11-108">It is now possible to map two or more entity types to the same table where the primary key column(s) will be shared and each row will correspond to two or more entities.</span></span>

<span data-ttu-id="8ee11-109">Aby użyć dzielenia tabeli, należy skonfigurować relację identyfikującą (gdzie właściwości klucza obcego w formularzu klucz podstawowy) muszą być skonfigurowane między wszystkimi typami jednostek udostępniającymi tabelę:</span><span class="sxs-lookup"><span data-stu-id="8ee11-109">To use table splitting an identifying relationship (where foreign key properties form the primary key) must be configured between all of the entity types sharing the table:</span></span>

``` csharp
modelBuilder.Entity<Product>()
    .HasOne(e => e.Details).WithOne(e => e.Product)
    .HasForeignKey<ProductDetails>(e => e.Id);
modelBuilder.Entity<Product>().ToTable("Products");
modelBuilder.Entity<ProductDetails>().ToTable("Products");
```

<span data-ttu-id="8ee11-110">Zapoznaj się z [sekcją dotyczącą dzielenia tabeli](xref:core/modeling/table-splitting) , aby uzyskać więcej informacji na temat tej funkcji.</span><span class="sxs-lookup"><span data-stu-id="8ee11-110">Read the [section on table splitting](xref:core/modeling/table-splitting) for more information on this feature.</span></span>

### <a name="owned-types"></a><span data-ttu-id="8ee11-111">Typy własności</span><span class="sxs-lookup"><span data-stu-id="8ee11-111">Owned types</span></span>

<span data-ttu-id="8ee11-112">Typ jednostki będącej własnością może współużytkować ten sam typ .NET z innym typem jednostki będącej własnością, ale ponieważ nie można go zidentyfikować tylko przez typ .NET, musi istnieć Nawigacja z innego typu jednostki.</span><span class="sxs-lookup"><span data-stu-id="8ee11-112">An owned entity type can share the same .NET type with another owned entity type, but since it cannot be identified just by the .NET type there must be a navigation to it from another entity type.</span></span> <span data-ttu-id="8ee11-113">Jednostką zawierającą zdefiniowaną nawigację jest właściciel.</span><span class="sxs-lookup"><span data-stu-id="8ee11-113">The entity containing the defining navigation is the owner.</span></span> <span data-ttu-id="8ee11-114">Podczas wykonywania zapytania dotyczącego właściciela typy będą uwzględniane domyślnie.</span><span class="sxs-lookup"><span data-stu-id="8ee11-114">When querying the owner the owned types will be included by default.</span></span>

<span data-ttu-id="8ee11-115">Według Konwencji klucz podstawowy w tle zostanie utworzony dla typu będącego własnością i zostanie zamapowany do tej samej tabeli co właściciel przy użyciu dzielenia tabeli.</span><span class="sxs-lookup"><span data-stu-id="8ee11-115">By convention a shadow primary key will be created for the owned type and it will be mapped to the same table as the owner by using table splitting.</span></span> <span data-ttu-id="8ee11-116">Pozwala to na używanie typów posiadanych w sposób podobny do sposobu używania typów złożonych w EF6:</span><span class="sxs-lookup"><span data-stu-id="8ee11-116">This allows to use owned types similarly to how complex types are used in EF6:</span></span>

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

<span data-ttu-id="8ee11-117">Aby uzyskać więcej informacji na temat tej funkcji, zapoznaj się z [sekcją należącej do typów jednostek](xref:core/modeling/owned-entities) .</span><span class="sxs-lookup"><span data-stu-id="8ee11-117">Read the [section on owned entity types](xref:core/modeling/owned-entities) for more information on this feature.</span></span>

### <a name="model-level-query-filters"></a><span data-ttu-id="8ee11-118">Filtry zapytań na poziomie modelu</span><span class="sxs-lookup"><span data-stu-id="8ee11-118">Model-level query filters</span></span>

<span data-ttu-id="8ee11-119">EF Core 2,0 zawiera nową funkcję wywołującą filtry zapytań na poziomie modelu.</span><span class="sxs-lookup"><span data-stu-id="8ee11-119">EF Core 2.0 includes a new feature we call Model-level query filters.</span></span> <span data-ttu-id="8ee11-120">Ta funkcja zezwala na predykaty zapytań LINQ (wyrażenie logiczne zwykle przenoszone do składnika LINQ WHERE Query operator) do zdefiniowania bezpośrednio w typach jednostek w modelu metadanych (zwykle w OnModelCreating).</span><span class="sxs-lookup"><span data-stu-id="8ee11-120">This feature allows LINQ query predicates (a boolean expression typically passed to the LINQ Where query operator) to be defined directly on Entity Types in the metadata model (usually in OnModelCreating).</span></span> <span data-ttu-id="8ee11-121">Takie filtry są automatycznie stosowane do dowolnych zapytań LINQ obejmujących te typy jednostek, w tym typy jednostek, do których odwołuje się pośrednio, na przykład przy użyciu odwołań do właściwości dołączania lub nawigacji bezpośredniej.</span><span class="sxs-lookup"><span data-stu-id="8ee11-121">Such filters are automatically applied to any LINQ queries involving those Entity Types, including Entity Types referenced indirectly, such as through the use of Include or direct navigation property references.</span></span> <span data-ttu-id="8ee11-122">Niektóre typowe aplikacje tej funkcji to:</span><span class="sxs-lookup"><span data-stu-id="8ee11-122">Some common applications of this feature are:</span></span>

- <span data-ttu-id="8ee11-123">Usuwanie nietrwałe — typy jednostek definiują Właściwość IsDeleted.</span><span class="sxs-lookup"><span data-stu-id="8ee11-123">Soft delete - An Entity Types defines an IsDeleted property.</span></span>
- <span data-ttu-id="8ee11-124">Wielodostępność — typ jednostki definiuje Właściwość TenantId.</span><span class="sxs-lookup"><span data-stu-id="8ee11-124">Multi-tenancy - An Entity Type defines a TenantId property.</span></span>

<span data-ttu-id="8ee11-125">Oto prosty przykład demonstrujący funkcję dla dwóch wymienionych powyżej scenariuszy:</span><span class="sxs-lookup"><span data-stu-id="8ee11-125">Here is a simple example demonstrating the feature for the two scenarios listed above:</span></span>

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

<span data-ttu-id="8ee11-126">Definiujemy filtr na poziomie modelu, który implementuje wiele dzierżawców i usuwanie nietrwałe dla wystąpień `Post` typu jednostki.</span><span class="sxs-lookup"><span data-stu-id="8ee11-126">We define a model-level filter that implements multi-tenancy and soft-delete for instances of the `Post` Entity Type.</span></span> <span data-ttu-id="8ee11-127">Zwróć uwagę na użycie `DbContext` Właściwości poziomu wystąpienia: `TenantId` .</span><span class="sxs-lookup"><span data-stu-id="8ee11-127">Note the use of a `DbContext` instance-level property: `TenantId`.</span></span> <span data-ttu-id="8ee11-128">Filtry na poziomie modelu będą używać wartości z poprawnego wystąpienia kontekstu (czyli wystąpienia kontekstu, które wykonuje zapytanie).</span><span class="sxs-lookup"><span data-stu-id="8ee11-128">Model-level filters will use the value from the correct context instance (that is, the context instance that is executing the query).</span></span>

<span data-ttu-id="8ee11-129">Filtry mogą być wyłączone dla poszczególnych zapytań LINQ przy użyciu operatora IgnoreQueryFilters ().</span><span class="sxs-lookup"><span data-stu-id="8ee11-129">Filters may be disabled for individual LINQ queries using the IgnoreQueryFilters() operator.</span></span>

#### <a name="limitations"></a><span data-ttu-id="8ee11-130">Ograniczenia</span><span class="sxs-lookup"><span data-stu-id="8ee11-130">Limitations</span></span>

- <span data-ttu-id="8ee11-131">Odwołania nawigacji są niedozwolone.</span><span class="sxs-lookup"><span data-stu-id="8ee11-131">Navigation references are not allowed.</span></span> <span data-ttu-id="8ee11-132">Ta funkcja może zostać dodana na podstawie opinii.</span><span class="sxs-lookup"><span data-stu-id="8ee11-132">This feature may be added based on feedback.</span></span>
- <span data-ttu-id="8ee11-133">Filtry można definiować tylko w typie jednostki głównej hierarchii.</span><span class="sxs-lookup"><span data-stu-id="8ee11-133">Filters can only be defined on the root Entity Type of a hierarchy.</span></span>

### <a name="database-scalar-function-mapping"></a><span data-ttu-id="8ee11-134">Mapowanie funkcji skalarnej bazy danych</span><span class="sxs-lookup"><span data-stu-id="8ee11-134">Database scalar function mapping</span></span>

<span data-ttu-id="8ee11-135">EF Core 2,0 zawiera istotny udział od [Paul Middleton](https://github.com/pmiddleton) , który umożliwia mapowanie funkcji skalarnych bazy danych na metody pośredniczące, tak aby mogły one być używane w zapytaniach LINQ i TŁUMACZONE na SQL.</span><span class="sxs-lookup"><span data-stu-id="8ee11-135">EF Core 2.0 includes an important contribution from [Paul Middleton](https://github.com/pmiddleton) which enables mapping database scalar functions to method stubs so that they can be used in LINQ queries and translated to SQL.</span></span>

<span data-ttu-id="8ee11-136">Poniżej znajduje się krótki opis sposobu użycia funkcji:</span><span class="sxs-lookup"><span data-stu-id="8ee11-136">Here is a brief description of how the feature can be used:</span></span>

<span data-ttu-id="8ee11-137">Zadeklaruj metodę statyczną w `DbContext` i Dodaj do niej adnotację przy użyciu `DbFunctionAttribute` :</span><span class="sxs-lookup"><span data-stu-id="8ee11-137">Declare a static method on your `DbContext` and annotate it with `DbFunctionAttribute`:</span></span>

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

<span data-ttu-id="8ee11-138">Metody takie jak te są rejestrowane automatycznie.</span><span class="sxs-lookup"><span data-stu-id="8ee11-138">Methods like this are automatically registered.</span></span> <span data-ttu-id="8ee11-139">Po zarejestrowaniu wywołania metody w zapytaniu LINQ można przetłumaczyć na wywołania funkcji w programie SQL:</span><span class="sxs-lookup"><span data-stu-id="8ee11-139">Once registered, calls to the method in a LINQ query can be translated to function calls in SQL:</span></span>

``` csharp
var query =
    from p in context.Posts
    where BloggingContext.PostReadCount(p.Id) > 5
    select p;
```

<span data-ttu-id="8ee11-140">Kilka kwestii, na które warto zwrócić uwagę:</span><span class="sxs-lookup"><span data-stu-id="8ee11-140">A few things to note:</span></span>

- <span data-ttu-id="8ee11-141">Zgodnie z Konwencją nazwa metody jest używana jako nazwa funkcji (w tym przypadku funkcji zdefiniowanej przez użytkownika) podczas generowania kodu SQL, ale można zastąpić nazwę i schemat podczas rejestracji metody.</span><span class="sxs-lookup"><span data-stu-id="8ee11-141">By convention the name of the method is used as the name of a function (in this case a user-defined function) when generating the SQL, but you can override the name and schema during method registration.</span></span>
- <span data-ttu-id="8ee11-142">Obecnie obsługiwane są tylko funkcje skalarne.</span><span class="sxs-lookup"><span data-stu-id="8ee11-142">Currently only scalar functions are supported.</span></span>
- <span data-ttu-id="8ee11-143">Należy utworzyć zamapowanej funkcji w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="8ee11-143">You must create the mapped function in the database.</span></span> <span data-ttu-id="8ee11-144">Migracje EF Core nie zapewnią ich tworzenia.</span><span class="sxs-lookup"><span data-stu-id="8ee11-144">EF Core migrations will not take care of creating it.</span></span>

### <a name="self-contained-type-configuration-for-code-first"></a><span data-ttu-id="8ee11-145">Samodzielna konfiguracja typu dla kodu</span><span class="sxs-lookup"><span data-stu-id="8ee11-145">Self-contained type configuration for code first</span></span>

<span data-ttu-id="8ee11-146">W EF6 można hermetyzować kod pierwszej konfiguracji określonego typu jednostki, pobierając je z *EntityTypeConfiguration*.</span><span class="sxs-lookup"><span data-stu-id="8ee11-146">In EF6 it was possible to encapsulate the code first configuration of a specific entity type by deriving from *EntityTypeConfiguration*.</span></span> <span data-ttu-id="8ee11-147">W EF Core 2,0 ten wzorzec zostanie przywrócony:</span><span class="sxs-lookup"><span data-stu-id="8ee11-147">In EF Core 2.0 we are bringing this pattern back:</span></span>

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

## <a name="high-performance"></a><span data-ttu-id="8ee11-148">Wysoka wydajność</span><span class="sxs-lookup"><span data-stu-id="8ee11-148">High Performance</span></span>

### <a name="dbcontext-pooling"></a><span data-ttu-id="8ee11-149">Buforowanie kontekstu DbContext</span><span class="sxs-lookup"><span data-stu-id="8ee11-149">DbContext pooling</span></span>

<span data-ttu-id="8ee11-150">Wzorzec podstawowy służący do używania EF Core w aplikacji ASP.NET Core zazwyczaj polega na zarejestrowaniu niestandardowego typu kontekstu DBW systemie iniekcji zależności i późniejszym uzyskaniu wystąpień tego typu przez parametry konstruktora w kontrolerach.</span><span class="sxs-lookup"><span data-stu-id="8ee11-150">The basic pattern for using EF Core in an ASP.NET Core application usually involves registering a custom DbContext type into the dependency injection system and later obtaining instances of that type through constructor parameters in controllers.</span></span> <span data-ttu-id="8ee11-151">Oznacza to, że nowe wystąpienie DbContext jest tworzone dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="8ee11-151">This means a new instance of the DbContext is created for each request.</span></span>

<span data-ttu-id="8ee11-152">W wersji 2,0 wprowadzamy nowy sposób rejestrowania niestandardowych typów kontekstu DbContext w iniekcji zależności, które w sposób przezroczysty wprowadzają pulę wystąpień DbContext wielokrotnego użytku.</span><span class="sxs-lookup"><span data-stu-id="8ee11-152">In version 2.0 we are introducing a new way to register custom DbContext types in dependency injection which transparently introduces a pool of reusable DbContext instances.</span></span> <span data-ttu-id="8ee11-153">Aby użyć usługi DbContext Pool, użyj `AddDbContextPool` zamiast `AddDbContext` podczas rejestracji w usłudze:</span><span class="sxs-lookup"><span data-stu-id="8ee11-153">To use DbContext pooling, use the `AddDbContextPool` instead of `AddDbContext` during service registration:</span></span>

``` csharp
services.AddDbContextPool<BloggingContext>(
    options => options.UseSqlServer(connectionString));
```

<span data-ttu-id="8ee11-154">Jeśli ta metoda jest używana, w chwili, gdy wystąpienie DbContext żąda żądania przez kontroler, należy najpierw sprawdzić, czy w puli jest dostępne wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="8ee11-154">If this method is used, at the time a DbContext instance is requested by a controller we will first check if there is an instance available in the pool.</span></span> <span data-ttu-id="8ee11-155">Po zakończeniu przetwarzania żądania wszystkie Stany wystąpienia zostaną zresetowane, a wystąpienie zostanie zwrócone do puli.</span><span class="sxs-lookup"><span data-stu-id="8ee11-155">Once the request processing finalizes, any state on the instance is reset and the instance is itself returned to the pool.</span></span>

<span data-ttu-id="8ee11-156">Jest to koncepcyjnie podobne do sposobu, w jaki pula połączeń działa w ADO.NET dostawcy i ma zalety zapisania niektórych kosztów inicjalizacji wystąpienia DbContext.</span><span class="sxs-lookup"><span data-stu-id="8ee11-156">This is conceptually similar to how connection pooling operates in ADO.NET providers and has the advantage of saving some of the cost of initialization of DbContext instance.</span></span>

### <a name="limitations"></a><span data-ttu-id="8ee11-157">Ograniczenia</span><span class="sxs-lookup"><span data-stu-id="8ee11-157">Limitations</span></span>

<span data-ttu-id="8ee11-158">Nowa metoda wprowadza kilka ograniczeń dotyczących tego, co można zrobić w `OnConfiguring()` metodzie DbContext.</span><span class="sxs-lookup"><span data-stu-id="8ee11-158">The new method introduces a few limitations on what can be done in the `OnConfiguring()` method of the DbContext.</span></span>

> [!WARNING]  
> <span data-ttu-id="8ee11-159">Unikaj korzystania z puli kontekstu DbContext, Jeśli przechowujesz własny stan (na przykład pola prywatne) w klasie pochodnej DbContext, która nie powinna być współdzielona między żądaniami.</span><span class="sxs-lookup"><span data-stu-id="8ee11-159">Avoid using DbContext Pooling if you maintain your own state (for example, private fields) in your derived DbContext class that should not be shared across requests.</span></span> <span data-ttu-id="8ee11-160">EF Core resetuje tylko ten stan, który zna przed dodaniem wystąpienia DbContext do puli.</span><span class="sxs-lookup"><span data-stu-id="8ee11-160">EF Core will only reset the state that it is aware of before adding a DbContext instance to the pool.</span></span>

### <a name="explicitly-compiled-queries"></a><span data-ttu-id="8ee11-161">Jawne skompilowane zapytania</span><span class="sxs-lookup"><span data-stu-id="8ee11-161">Explicitly compiled queries</span></span>

<span data-ttu-id="8ee11-162">Jest to druga funkcja wydajności, która umożliwia oferowanie korzyści w scenariuszach o dużej skali.</span><span class="sxs-lookup"><span data-stu-id="8ee11-162">This is the second opt-in performance feature designed to offer benefits in high-scale scenarios.</span></span>

<span data-ttu-id="8ee11-163">Ręcznie lub jawnie skompilowane interfejsy API zapytań są dostępne w poprzednich wersjach EF, a także w LINQ to SQL, aby umożliwić aplikacjom buforowanie zapytań, aby mogły być obliczane tylko raz i wykonywane wiele razy.</span><span class="sxs-lookup"><span data-stu-id="8ee11-163">Manual or explicitly compiled query APIs have been available in previous versions of EF and also in LINQ to SQL to allow applications to cache the translation of queries so that they can be computed only once and executed many times.</span></span>

<span data-ttu-id="8ee11-164">Mimo że w ogólnym EF Core można automatycznie kompilować i buforować zapytania w oparciu o skrótową reprezentację wyrażeń zapytania, ten mechanizm może służyć do uzyskania małego wzmocnienia wydajności, pomijając obliczenia skrótu i przeszukiwania pamięci podręcznej, umożliwiając aplikacji użycie już skompilowanego zapytania za pośrednictwem wywołania delegata.</span><span class="sxs-lookup"><span data-stu-id="8ee11-164">Although in general EF Core can automatically compile and cache queries based on a hashed representation of the query expressions, this mechanism can be used to obtain a small performance gain by bypassing the computation of the hash and the cache lookup, allowing the application to use an already compiled query through the invocation of a delegate.</span></span>

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

## <a name="change-tracking"></a><span data-ttu-id="8ee11-165">Śledzenie zmian</span><span class="sxs-lookup"><span data-stu-id="8ee11-165">Change Tracking</span></span>

### <a name="attach-can-track-a-graph-of-new-and-existing-entities"></a><span data-ttu-id="8ee11-166">Attach można śledzić Graf nowych i istniejących jednostek</span><span class="sxs-lookup"><span data-stu-id="8ee11-166">Attach can track a graph of new and existing entities</span></span>

<span data-ttu-id="8ee11-167">EF Core obsługuje automatyczne generowanie wartości kluczy za pomocą różnych mechanizmów.</span><span class="sxs-lookup"><span data-stu-id="8ee11-167">EF Core supports automatic generation of key values through a variety of mechanisms.</span></span> <span data-ttu-id="8ee11-168">W przypadku korzystania z tej funkcji jest generowana wartość, jeśli właściwość klucza jest wartością domyślną środowiska CLR — zazwyczaj zero lub null.</span><span class="sxs-lookup"><span data-stu-id="8ee11-168">When using this feature, a value is generated if the key property is the CLR default--usually zero or null.</span></span> <span data-ttu-id="8ee11-169">Oznacza to, że wykres jednostek może być przekazanie do `DbContext.Attach` lub `DbSet.Attach` , a EF Core będzie oznaczać te jednostki, dla których klucz jest już ustawiony, ponieważ `Unchanged` te jednostki, które nie mają zestawu kluczy, będą oznaczone jako `Added` .</span><span class="sxs-lookup"><span data-stu-id="8ee11-169">This means that a graph of entities can be passed to `DbContext.Attach` or `DbSet.Attach` and EF Core will mark those entities that have a key already set as `Unchanged` while those entities that do not have a key set will be marked as `Added`.</span></span> <span data-ttu-id="8ee11-170">Dzięki temu można łatwo dołączać wykres mieszanych nowych i istniejących jednostek podczas korzystania z wygenerowanych kluczy.</span><span class="sxs-lookup"><span data-stu-id="8ee11-170">This makes it easy to attach a graph of mixed new and existing entities when using generated keys.</span></span> <span data-ttu-id="8ee11-171">`DbContext.Update`i `DbSet.Update` Pracuj w taki sam sposób, z tą różnicą, że jednostki z zestawem kluczy są oznaczone jako `Modified` zamiast `Unchanged` .</span><span class="sxs-lookup"><span data-stu-id="8ee11-171">`DbContext.Update` and `DbSet.Update` work in the same way, except that entities with a key set are marked as `Modified` instead of `Unchanged`.</span></span>

## <a name="query"></a><span data-ttu-id="8ee11-172">Zapytanie</span><span class="sxs-lookup"><span data-stu-id="8ee11-172">Query</span></span>

### <a name="improved-linq-translation"></a><span data-ttu-id="8ee11-173">Ulepszone tłumaczenie LINQ</span><span class="sxs-lookup"><span data-stu-id="8ee11-173">Improved LINQ translation</span></span>

<span data-ttu-id="8ee11-174">Umożliwia pomyślne wykonanie większej liczby zapytań, dzięki czemu w bazie danych jest przeprowadzana większa logika (a nie w pamięci) i mniejszej ilości danych pobieranych z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="8ee11-174">Enables more queries to successfully execute, with more logic being evaluated in the database (rather than in-memory) and less data unnecessarily being retrieved from the database.</span></span>

### <a name="groupjoin-improvements"></a><span data-ttu-id="8ee11-175">Udoskonalenia GroupJoin —</span><span class="sxs-lookup"><span data-stu-id="8ee11-175">GroupJoin improvements</span></span>

<span data-ttu-id="8ee11-176">To działanie poprawi SQL wygenerowanego dla sprzężeń grup.</span><span class="sxs-lookup"><span data-stu-id="8ee11-176">This work improves the SQL that is generated for group joins.</span></span> <span data-ttu-id="8ee11-177">Sprzężenia grup najczęściej są wynikiem podzapytania w przypadku opcjonalnych właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="8ee11-177">Group joins are most often a result of sub-queries on optional navigation properties.</span></span>

### <a name="string-interpolation-in-fromsql-and-executesqlcommand"></a><span data-ttu-id="8ee11-178">Interpolacja ciągów w Z tabel i ExecuteSqlCommand</span><span class="sxs-lookup"><span data-stu-id="8ee11-178">String interpolation in FromSql and ExecuteSqlCommand</span></span>

<span data-ttu-id="8ee11-179">W języku c# 6 wprowadzono interpolację ciągów, funkcję, która umożliwia wyrażeniem języka C#, które mają być bezpośrednio osadzone w literałach ciągów, co zapewnia świetny sposób tworzenia ciągów w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="8ee11-179">C# 6 introduced String Interpolation, a feature that allows C# expressions to be directly embedded in string literals, providing a nice way of building strings at runtime.</span></span> <span data-ttu-id="8ee11-180">W EF Core 2,0 dodaliśmy specjalną obsługę ciągów interpolowanych do naszych dwóch głównych interfejsów API, które akceptują surowe ciągi SQL: `FromSql` i `ExecuteSqlCommand` .</span><span class="sxs-lookup"><span data-stu-id="8ee11-180">In EF Core 2.0 we added special support for interpolated strings to our two primary APIs that accept raw SQL strings: `FromSql` and `ExecuteSqlCommand`.</span></span> <span data-ttu-id="8ee11-181">Ta nowa obsługa umożliwia interpolację ciągów języka C# do użycia w sposób bezpieczny.</span><span class="sxs-lookup"><span data-stu-id="8ee11-181">This new support allows C# string interpolation to be used in a "safe" manner.</span></span> <span data-ttu-id="8ee11-182">Oznacza to, że w sposób chroniący przed typowymi błędami iniekcji SQL, które mogą wystąpić podczas dynamicznego konstruowania bazy danych SQL w środowisku uruchomieniowym.</span><span class="sxs-lookup"><span data-stu-id="8ee11-182">That is, in a way that protects against common SQL injection mistakes that can occur when dynamically constructing SQL at runtime.</span></span>

<span data-ttu-id="8ee11-183">Oto przykład:</span><span class="sxs-lookup"><span data-stu-id="8ee11-183">Here is an example:</span></span>

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

<span data-ttu-id="8ee11-184">W tym przykładzie w ciągu formatu SQL są osadzone dwie zmienne.</span><span class="sxs-lookup"><span data-stu-id="8ee11-184">In this example, there are two variables embedded in the SQL format string.</span></span> <span data-ttu-id="8ee11-185">EF Core będzie generować następujące SQL:</span><span class="sxs-lookup"><span data-stu-id="8ee11-185">EF Core will produce the following SQL:</span></span>

```sql
@p0='London' (Size = 4000)
@p1='Sales Representative' (Size = 4000)

SELECT *
FROM ""Customers""
WHERE ""City"" = @p0
    AND ""ContactTitle"" = @p1
```

### <a name="effunctionslike"></a><span data-ttu-id="8ee11-186">Bieżąco. Functions. like ()</span><span class="sxs-lookup"><span data-stu-id="8ee11-186">EF.Functions.Like()</span></span>

<span data-ttu-id="8ee11-187">Dodaliśmy EF. Właściwość Functions, która może być używana przez EF Core lub dostawców do definiowania metod, które mapują na funkcje bazy danych lub operatory, aby mogły być wywoływane w zapytaniach LINQ.</span><span class="sxs-lookup"><span data-stu-id="8ee11-187">We have added the EF.Functions property which can be used by EF Core or providers to define methods that map to database functions or operators so that those can be invoked in LINQ queries.</span></span> <span data-ttu-id="8ee11-188">Pierwszym przykładem takiej metody jest like ():</span><span class="sxs-lookup"><span data-stu-id="8ee11-188">The first example of such a method is Like():</span></span>

``` csharp
var aCustomers =
    from c in context.Customers
    where EF.Functions.Like(c.Name, "a%")
    select c;
```

<span data-ttu-id="8ee11-189">Należy pamiętać, że takie jak () jest dostarczane z implementacją w pamięci, która może być przydatna podczas pracy z bazą danych w pamięci lub kiedy Ocena predykatu musi następować po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="8ee11-189">Note that Like() comes with an in-memory implementation, which can be handy when working against an in-memory database or when evaluation of the predicate needs to occur on the client side.</span></span>

## <a name="database-management"></a><span data-ttu-id="8ee11-190">Zarządzanie bazami danych</span><span class="sxs-lookup"><span data-stu-id="8ee11-190">Database management</span></span>

### <a name="pluralization-hook-for-dbcontext-scaffolding"></a><span data-ttu-id="8ee11-191">Hak pluralizacja dla szkieletu DbContext</span><span class="sxs-lookup"><span data-stu-id="8ee11-191">Pluralization hook for DbContext scaffolding</span></span>

<span data-ttu-id="8ee11-192">EF Core 2,0 wprowadza nową usługę *IPluralizer* , która jest używana do nazwom nazw typów jednostek i pluralize nieogólnymi.</span><span class="sxs-lookup"><span data-stu-id="8ee11-192">EF Core 2.0 introduces a new *IPluralizer* service that is used to singularize entity type names and pluralize DbSet names.</span></span> <span data-ttu-id="8ee11-193">Domyślna implementacja to no-op, więc jest to element Hook, gdzie osób może łatwo podłączyć własne pluralizer.</span><span class="sxs-lookup"><span data-stu-id="8ee11-193">The default implementation is a no-op, so this is just a hook where folks can easily plug in their own pluralizer.</span></span>

<span data-ttu-id="8ee11-194">Oto co wygląda na to, aby deweloper mógł go podłączyć do swoich własnych pluralizer:</span><span class="sxs-lookup"><span data-stu-id="8ee11-194">Here is what it looks like for a developer to hook in their own pluralizer:</span></span>

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

## <a name="others"></a><span data-ttu-id="8ee11-195">Inne</span><span class="sxs-lookup"><span data-stu-id="8ee11-195">Others</span></span>

### <a name="move-adonet-sqlite-provider-to-sqlitepclraw"></a><span data-ttu-id="8ee11-196">Przenieś dostawcę oprogramowania SQLite ADO.NET do SQLitePCL. Raw</span><span class="sxs-lookup"><span data-stu-id="8ee11-196">Move ADO.NET SQLite provider to SQLitePCL.raw</span></span>

<span data-ttu-id="8ee11-197">Zapewnia to bardziej niezawodne rozwiązanie w firmie Microsoft. Data. sqlite do dystrybucji natywnych plików binarnych oprogramowania SQLite na różnych platformach.</span><span class="sxs-lookup"><span data-stu-id="8ee11-197">This gives us a more robust solution in Microsoft.Data.Sqlite for distributing native SQLite binaries on different platforms.</span></span>

### <a name="only-one-provider-per-model"></a><span data-ttu-id="8ee11-198">Tylko jeden dostawca na model</span><span class="sxs-lookup"><span data-stu-id="8ee11-198">Only one provider per model</span></span>

<span data-ttu-id="8ee11-199">Znacznie rozszerza, jak dostawcy mogą współdziałać z modelem i upraszczają konwencje, adnotacje i interfejsy API Fluent współpracują z różnymi dostawcami.</span><span class="sxs-lookup"><span data-stu-id="8ee11-199">Significantly augments how providers can interact with the model and simplifies how conventions, annotations and fluent APIs work with different providers.</span></span>

<span data-ttu-id="8ee11-200">EF Core 2,0 będzie teraz kompilować inne [IModel](https://github.com/aspnet/EntityFramework/blob/master/src/EFCore/Metadata/IModel.cs) dla każdego używanego dostawcy.</span><span class="sxs-lookup"><span data-stu-id="8ee11-200">EF Core 2.0 will now build a different [IModel](https://github.com/aspnet/EntityFramework/blob/master/src/EFCore/Metadata/IModel.cs) for each different provider being used.</span></span> <span data-ttu-id="8ee11-201">Jest to zazwyczaj przezroczyste dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8ee11-201">This is usually transparent to the application.</span></span> <span data-ttu-id="8ee11-202">Ułatwia to uproszczenie interfejsów API metadanych niższego poziomu, takich jak każdy dostęp do *wspólnych koncepcji metadanych relacyjnych* jest zawsze realizowany przez wywołanie `.Relational` zamiast `.SqlServer` , `.Sqlite` itp.</span><span class="sxs-lookup"><span data-stu-id="8ee11-202">This has facilitated a simplification of lower-level metadata APIs such that any access to *common relational metadata concepts* is always made through a call to `.Relational` instead of `.SqlServer`, `.Sqlite`, etc.</span></span>

### <a name="consolidated-logging-and-diagnostics"></a><span data-ttu-id="8ee11-203">Rejestrowanie skonsolidowane i Diagnostyka</span><span class="sxs-lookup"><span data-stu-id="8ee11-203">Consolidated logging and diagnostics</span></span>

<span data-ttu-id="8ee11-204">Rejestrowanie (oparte na ILogger) i Diagnostyka (oparta na DiagnosticSource) teraz udostępnia więcej kodu.</span><span class="sxs-lookup"><span data-stu-id="8ee11-204">Logging (based on ILogger) and Diagnostics (based on DiagnosticSource) mechanisms now share more code.</span></span>

<span data-ttu-id="8ee11-205">Identyfikatory zdarzeń dla komunikatów wysyłanych do ILogger uległy zmianie w 2,0.</span><span class="sxs-lookup"><span data-stu-id="8ee11-205">The event IDs for messages sent to an ILogger have changed in 2.0.</span></span> <span data-ttu-id="8ee11-206">Identyfikatory zdarzeń są teraz unikatowe w EF Core kodzie.</span><span class="sxs-lookup"><span data-stu-id="8ee11-206">The event IDs are now unique across EF Core code.</span></span> <span data-ttu-id="8ee11-207">Te komunikaty są teraz zgodne ze standardowym wzorcem rejestrowania strukturalnego używanego przez program, na przykład MVC.</span><span class="sxs-lookup"><span data-stu-id="8ee11-207">These messages now also follow the standard pattern for structured logging used by, for example, MVC.</span></span>

<span data-ttu-id="8ee11-208">Zmieniono także kategorie rejestratora.</span><span class="sxs-lookup"><span data-stu-id="8ee11-208">Logger categories have also changed.</span></span> <span data-ttu-id="8ee11-209">Istnieje teraz dobrze znany zestaw kategorii, do których można uzyskać dostęp za pomocą [DbLoggerCategory](https://github.com/aspnet/EntityFramework/blob/master/src/EFCore/DbLoggerCategory.cs).</span><span class="sxs-lookup"><span data-stu-id="8ee11-209">There is now a well-known set of categories accessed through [DbLoggerCategory](https://github.com/aspnet/EntityFramework/blob/master/src/EFCore/DbLoggerCategory.cs).</span></span>

<span data-ttu-id="8ee11-210">Zdarzenia DiagnosticSource teraz używają tych samych nazw identyfikatorów zdarzeń co odpowiadające im `ILogger` komunikaty.</span><span class="sxs-lookup"><span data-stu-id="8ee11-210">DiagnosticSource events now use the same event ID names as the corresponding `ILogger` messages.</span></span>
