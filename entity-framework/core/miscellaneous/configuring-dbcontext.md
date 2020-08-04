---
title: Konfigurowanie DbContext-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: d7a22b5a-4c5b-4e3b-9897-4d7320fcd13f
uid: core/miscellaneous/configuring-dbcontext
ms.openlocfilehash: 9614449f6ead393b514f42b718b4cae5f97dfc98
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526423"
---
# <a name="configuring-a-dbcontext"></a>Konfigurowanie klasy DbContext

W tym artykule przedstawiono podstawowe wzorce konfigurowania programu `DbContext` za pośrednictwem programu w `DbContextOptions` celu nawiązania połączenia z bazą danych przy użyciu określonego dostawcy EF Core i opcjonalnych zachowań.

## <a name="design-time-dbcontext-configuration"></a>Konfiguracja DbContext czasu projektowania

EF Core narzędzia czasu projektowania, takie jak [migracje](xref:core/managing-schemas/migrations/index) , muszą mieć możliwość odnajdywania i tworzenia działającego wystąpienia `DbContext` typu w celu zebrania szczegółowych informacji o typach jednostek aplikacji i sposobie ich mapowania na schemat bazy danych. Ten proces może być automatyczny, tak długo, jak narzędzie można łatwo utworzyć `DbContext` w taki sposób, że zostanie ono skonfigurowane podobnie do konfiguracji w czasie wykonywania.

Chociaż każdy wzorzec, który zapewnia niezbędne informacje konfiguracyjne `DbContext` , może działać w czasie wykonywania, narzędzia, które wymagają użycia `DbContext` w czasie projektowania, mogą pracować tylko z ograniczoną liczbą wzorców. Są one omówione bardziej szczegółowo w sekcji [Tworzenie kontekstu w czasie projektowania](xref:core/miscellaneous/cli/dbcontext-creation) .

## <a name="configuring-dbcontextoptions"></a>Konfigurowanie DbContextOptions

`DbContext`musi mieć wystąpienie, `DbContextOptions` Aby można było wykonać dowolną ilość pracy. `DbContextOptions`Wystąpienie przenosi informacje o konfiguracji, takie jak:

- Dostawca bazy danych do użycia, zazwyczaj wybierany przez wywołanie metody, takiej jak `UseSqlServer` lub `UseSqlite` . Te metody rozszerzające wymagają odpowiedniego pakietu dostawcy, takiego jak `Microsoft.EntityFrameworkCore.SqlServer` lub `Microsoft.EntityFrameworkCore.Sqlite` . Metody są zdefiniowane w `Microsoft.EntityFrameworkCore` przestrzeni nazw.
- Wszystkie niezbędne parametry połączenia lub identyfikator wystąpienia bazy danych, zwykle przesyłane jako argument do metody wyboru dostawcy wymienione powyżej
- Wszystkie selektory nieopcjonalnych zachowań na poziomie dostawcy, zwykle również łańcucha wewnątrz wywołania metody wyboru dostawcy
- Wszystkie selektory zachowań ogólnych EF Core, zwykle łańcucha po lub przed metodą selektora dostawcy

Poniższy przykład konfiguruje `DbContextOptions` Używanie dostawcy SQL Server, połączenie zawarte w `connectionString` zmiennej, przekroczenie limitu czasu polecenia dostawcy i selektor zachowania EF Core, które powoduje, że wszystkie zapytania wykonywane `DbContext` Domyślnie [nie są śledzone](xref:core/querying/tracking#no-tracking-queries) :

``` csharp
optionsBuilder
    .UseSqlServer(connectionString, providerOptions=>providerOptions.CommandTimeout(60))
    .UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
```

> [!NOTE]  
> Metody selektora dostawcy i inne metody selektora zachowań wymienione powyżej to metody rozszerzające dotyczące `DbContextOptions` klas opcji lub specyficznych dla dostawcy. Aby można było uzyskać dostęp do tych metod rozszerzających, może być konieczne posiadanie przestrzeni nazw (zazwyczaj `Microsoft.EntityFrameworkCore` ) w zakresie i uwzględnienie dodatkowych zależności pakietu w projekcie.

`DbContextOptions`Można dostarczyć do obiektu, `DbContext` zastępując `OnConfiguring` metodę lub zewnętrznie za pośrednictwem argumentu konstruktora.

Jeśli oba są używane, `OnConfiguring` są stosowane jako ostatnie i mogą zastąpić opcje dostarczone do argumentu konstruktora.

### <a name="constructor-argument"></a>Argument konstruktora

Konstruktor może po prostu akceptować w `DbContextOptions` następujący sposób:

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
        : base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

> [!TIP]  
> Konstruktor podstawowy DbContext również akceptuje nieogólną wersję `DbContextOptions` , ale użycie nieogólnej wersji nie jest zalecane w przypadku aplikacji z wieloma typami kontekstu.

Aplikacja może teraz przekazać `DbContextOptions` wystąpienie podczas tworzenia wystąpienia kontekstu w następujący sposób:

``` csharp
var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
optionsBuilder.UseSqlite("Data Source=blog.db");

using (var context = new BloggingContext(optionsBuilder.Options))
{
  // do stuff
}
```

### <a name="onconfiguring"></a>Trwa konfigurowanie

Możesz również zainicjować `DbContextOptions` w ramach samego kontekstu. Chociaż ta technika jest używana w konfiguracji podstawowej, zwykle nadal trzeba uzyskać pewne szczegóły konfiguracji z zewnątrz, np. parametry połączenia z bazą danych. Można to zrobić za pomocą interfejsu API konfiguracji lub w inny sposób.

Aby zainicjować `DbContextOptions` w kontekście, Zastąp `OnConfiguring` metodę i wywołaj metody z podanej `DbContextOptionsBuilder` :

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlite("Data Source=blog.db");
    }
}
```

Aplikacja może po prostu utworzyć wystąpienie takiego kontekstu bez przekazywania wszystkiego do jego konstruktora:

``` csharp
using (var context = new BloggingContext())
{
  // do stuff
}
```

> [!TIP]
> Takie podejście nie nadaje się do testowania, chyba że testy są przeznaczone dla całej bazy danych.

### <a name="using-dbcontext-with-dependency-injection"></a>Używanie DbContext z iniekcją zależności

EF Core obsługuje używanie `DbContext` z kontenerem iniekcji zależności. Typ kontekstu DbContext można dodać do kontenera usługi przy użyciu `AddDbContext<TContext>` metody.

`AddDbContext<TContext>`nastąpi zarówno typ kontekstu DB, `TContext` jak i odpowiednie `DbContextOptions<TContext>` dostępne do iniekcji z kontenera usług.

Aby uzyskać dodatkowe informacje na temat iniekcji zależności, zobacz [więcej](#more-reading) informacji poniżej.

Dodawanie `DbContext` do iniekcji zależności:

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db"));
}
```

Wymaga to dodania [argumentu konstruktora](#constructor-argument) do typu DbContext, który akceptuje `DbContextOptions<TContext>` .

Kod kontekstu:

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
      :base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

Kod aplikacji (w ASP.NET Core):

``` csharp
public class MyController
{
    private readonly BloggingContext _context;

    public MyController(BloggingContext context)
    {
      _context = context;
    }

    ...
}
```

Kod aplikacji (bezpośrednio, rzadziej używany):

``` csharp
using (var context = serviceProvider.GetService<BloggingContext>())
{
  // do stuff
}

var options = serviceProvider.GetService<DbContextOptions<BloggingContext>>();
```

## <a name="avoiding-dbcontext-threading-issues"></a>Unikanie problemów wielowątkowości DbContext

Entity Framework Core nie obsługuje wielu operacji równoległych wykonywanych w tym samym `DbContext` wystąpieniu. Obejmuje to zarówno równoległe wykonywanie zapytań asynchronicznych, jak i jawne jednoczesne użycie z wielu wątków. W związku z tym zawsze `await` asynchroniczne wywołania są natychmiast lub używają osobnych `DbContext` wystąpień dla operacji wykonywanych równolegle.

Gdy EF Core wykryje próbę użycia `DbContext` wystąpienia współbieżnie, zobaczysz komunikat o takiej postaci `InvalidOperationException` :

> Druga operacja rozpoczęta w tym kontekście przed ukończeniem poprzedniej operacji. Jest to zazwyczaj spowodowane przez różne wątki korzystające z tego samego wystąpienia DbContext, jednak elementy członkowskie wystąpienia nie są gwarantowane bezpieczny wątkowo.

Gdy dostęp współbieżny nie zostanie wykryty, może to spowodować niezdefiniowane zachowanie, awarie aplikacji i uszkodzenie danych.

Istnieją typowe błędy, które mogą przypadkowo spowodować jednoczesne dostęp do tego samego `DbContext` wystąpienia:

### <a name="forgetting-to-await-the-completion-of-an-asynchronous-operation-before-starting-any-other-operation-on-the-same-dbcontext"></a>Zapominanie do oczekiwania na ukończenie operacji asynchronicznej przed rozpoczęciem jakiejkolwiek innej operacji w tym samym kontekście DbContext

Metody asynchroniczne umożliwiają EF Core inicjowania operacji, które uzyskują dostęp do bazy danych w sposób nieblokowany. Ale jeśli obiekt wywołujący nie oczekuje na zakończenie jednej z tych metod, a następnie wykonuje inne operacje na `DbContext` , stan `DbContext` może być (i bardzo prawdopodobnie będzie) uszkodzony.

Zawsze Czekaj na EF Core metod asynchronicznych.  

### <a name="implicitly-sharing-dbcontext-instances-across-multiple-threads-via-dependency-injection"></a>Niejawnie udostępnianie wystąpień DbContext w wielu wątkach przy użyciu iniekcji zależności

[`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)Metoda rozszerzająca `DbContext` domyślnie rejestruje typy z [okresem istnienia w zakresie](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) .

Jest to bezpieczne przed równoczesnymi problemami z dostępem w większości aplikacji ASP.NET Core, ponieważ w danym momencie istnieje tylko jeden wątek, w którym każde żądanie odbiera oddzielny zakres iniekcji zależności (i w związku z tym jest osobnym `DbContext` wystąpieniem). W przypadku modelu hostingu serwera Blazor jedno żądanie logiczne jest używane do obsługi obwodu użytkownika Blazor i w związku z tym w przypadku użycia domyślnego zakresu iniekcji dostępne jest tylko jedno wystąpienie DbContext o określonym zakresie.

Każdy kod, który jawnie wykonuje wiele wątków równolegle, powinien mieć pewność, że `DbContext` wystąpienia nie są kiedykolwiek dostępne współbieżnie.

Przy użyciu iniekcji zależności można to osiągnąć przez zarejestrowanie kontekstu jako zakresu i Tworzenie zakresów (za pomocą `IServiceScopeFactory` ) dla każdego wątku lub przez zarejestrowanie `DbContext` jako przejściowego (przy użyciu przeciążenia, `AddDbContext` które przyjmuje `ServiceLifetime` parametr).

## <a name="more-reading"></a>Więcej informacji

- Przeczytaj [wstrzyknięcie zależności](/aspnet/core/fundamentals/dependency-injection) , aby dowiedzieć się więcej o używaniu di.
- Przeczytaj [test](testing/index.md) , aby uzyskać więcej informacji.
