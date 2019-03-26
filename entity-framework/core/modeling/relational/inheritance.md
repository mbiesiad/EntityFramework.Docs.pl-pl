---
title: Dziedziczenie (relacyjna baza danych) — EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 9a7c5488-aaf4-4b40-b1ff-f435ff30f6ec
uid: core/modeling/relational/inheritance
ms.openlocfilehash: 2aaceb05bbc1b0eb5c116b3dc1fb33c90c115a70
ms.sourcegitcommit: 645785187ae23ddf7d7b0642c7a4da5ffb0c7f30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58419682"
---
# <a name="inheritance-relational-database"></a><span data-ttu-id="bcb01-102">Dziedziczenie (relacyjna baza danych)</span><span class="sxs-lookup"><span data-stu-id="bcb01-102">Inheritance (Relational Database)</span></span>

> [!NOTE]  
> <span data-ttu-id="bcb01-103">Ogólnie rzecz biorąc jest odpowiednie dla relacyjnych baz danych konfiguracji w tej sekcji.</span><span class="sxs-lookup"><span data-stu-id="bcb01-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="bcb01-104">Metody rozszerzenia, pokazane tutaj staną się dostępne po zainstalowaniu dostawcy relacyjnej bazy danych (z powodu udostępnionej *Microsoft.EntityFrameworkCore.Relational* pakietu).</span><span class="sxs-lookup"><span data-stu-id="bcb01-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="bcb01-105">Dziedziczenie w modelu platformy EF jest używane do kontrolowania, jak dziedziczenia klas jednostek jest reprezentowana w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="bcb01-105">Inheritance in the EF model is used to control how inheritance in the entity classes is represented in the database.</span></span>

> [!NOTE]  
> <span data-ttu-id="bcb01-106">Obecnie tylko tabela wg hierarchii (TPH) ze wzorcem jest zaimplementowana w wersji EF Core.</span><span class="sxs-lookup"><span data-stu-id="bcb01-106">Currently, only the table-per-hierarchy (TPH) pattern is implemented in EF Core.</span></span> <span data-ttu-id="bcb01-107">Innych typowych wzorców, takich jak tabela wg typu (TPT), a tabela konkretny-wg typu (TPC) nie są jeszcze dostępne.</span><span class="sxs-lookup"><span data-stu-id="bcb01-107">Other common patterns like table-per-type (TPT) and table-per-concrete-type (TPC) are not yet available.</span></span>

## <a name="conventions"></a><span data-ttu-id="bcb01-108">Konwencje</span><span class="sxs-lookup"><span data-stu-id="bcb01-108">Conventions</span></span>

<span data-ttu-id="bcb01-109">Zgodnie z Konwencją dziedziczenia zostanie zamapowane przy użyciu wzorca Tabela wg hierarchii (TPH).</span><span class="sxs-lookup"><span data-stu-id="bcb01-109">By convention, inheritance will be mapped using the table-per-hierarchy (TPH) pattern.</span></span> <span data-ttu-id="bcb01-110">TPH używa jednej tabeli do przechowywania danych dla wszystkich typów w hierarchii.</span><span class="sxs-lookup"><span data-stu-id="bcb01-110">TPH uses a single table to store the data for all types in the hierarchy.</span></span> <span data-ttu-id="bcb01-111">Kolumna dyskryminatora jest używany do identyfikowania typu każdy wiersz reprezentuje.</span><span class="sxs-lookup"><span data-stu-id="bcb01-111">A discriminator column is used to identify which type each row represents.</span></span>

<span data-ttu-id="bcb01-112">EF Core tylko skonfigurować dziedziczenie, jeśli co najmniej dwa dziedziczone typy są jawnie uwzględnione w modelu (zobacz [dziedziczenia](../inheritance.md) Aby uzyskać więcej informacji).</span><span class="sxs-lookup"><span data-stu-id="bcb01-112">EF Core will only setup inheritance if two or more inherited types are explicitly included in the model (see [Inheritance](../inheritance.md) for more details).</span></span>

<span data-ttu-id="bcb01-113">Poniżej przedstawiono przykład przedstawiający Scenariusz proste dziedziczenie i dane przechowywane w tabeli relacyjnej bazy danych za pomocą wzorca TPH.</span><span class="sxs-lookup"><span data-stu-id="bcb01-113">Below is an example showing a simple inheritance scenario and the data stored in a relational database table using the TPH pattern.</span></span> <span data-ttu-id="bcb01-114">*Dyskryminatora* kolumna określa, jakiego typu *blogu* są przechowywane w każdym wierszu.</span><span class="sxs-lookup"><span data-stu-id="bcb01-114">The *Discriminator* column identifies which type of *Blog* is stored in each row.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/Conventions/Samples/InheritanceDbSets.cs)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<RssBlog> RssBlogs { get; set; }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}

public class RssBlog : Blog
{
    public string RssUrl { get; set; }
}
```

![obraz](_static/inheritance-tph-data.png)

>[!NOTE]
> <span data-ttu-id="bcb01-116">Korzystając z mapowania TPH colmmns bazy danych zostaną zastosowane automatycznie dopuszczającego wartość null, zgodnie z potrzebami.</span><span class="sxs-lookup"><span data-stu-id="bcb01-116">Database colmmns are automatically made nullable as necessary when using TPH mapping.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="bcb01-117">Adnotacje danych</span><span class="sxs-lookup"><span data-stu-id="bcb01-117">Data Annotations</span></span>

<span data-ttu-id="bcb01-118">Nie można użyć adnotacji danych, aby skonfigurować dziedziczenie.</span><span class="sxs-lookup"><span data-stu-id="bcb01-118">You cannot use Data Annotations to configure inheritance.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="bcb01-119">Interfejs Fluent API</span><span class="sxs-lookup"><span data-stu-id="bcb01-119">Fluent API</span></span>

<span data-ttu-id="bcb01-120">Interfejs Fluent API umożliwiają skonfigurowanie nazwy i typu kolumna dyskryminatora i wartości, które są używane do identyfikowania poszczególnych typów w hierarchii.</span><span class="sxs-lookup"><span data-stu-id="bcb01-120">You can use the Fluent API to configure the name and type of the discriminator column and the values that are used to identify each type in the hierarchy.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Samples/InheritanceTPHDiscriminator.cs?highlight=7,8,9,10)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .HasDiscriminator<string>("blog_type")
            .HasValue<Blog>("blog_base")
            .HasValue<RssBlog>("blog_rss");
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}

public class RssBlog : Blog
{
    public string RssUrl { get; set; }
}
```

## <a name="configuring-the-discriminator-property"></a><span data-ttu-id="bcb01-121">Konfigurowanie właściwość rozróżniacza</span><span class="sxs-lookup"><span data-stu-id="bcb01-121">Configuring the discriminator property</span></span>

<span data-ttu-id="bcb01-122">W powyższych przykładach dyskryminatora jest tworzona jako [w tle właściwość](xref:core/modeling/shadow-properties) na podstawowej jednostce w hierarchii.</span><span class="sxs-lookup"><span data-stu-id="bcb01-122">In the examples above, the discriminator is created as a [shadow property](xref:core/modeling/shadow-properties) on the base entity of the hierarchy.</span></span> <span data-ttu-id="bcb01-123">Ponieważ właściwości w modelu, można skonfigurować tak jak inne właściwości.</span><span class="sxs-lookup"><span data-stu-id="bcb01-123">Since it is a property in the model, it can be configured just like other properties.</span></span> <span data-ttu-id="bcb01-124">Na przykład, aby ustawić maksymalną długość, gdy jest używany domyślnie przez Konwencję dyskryminatora:</span><span class="sxs-lookup"><span data-stu-id="bcb01-124">For example, to set the max length when the default, by-convention discriminator is being used:</span></span>

```C#
modelBuilder.Entity<Blog>()
    .Property("Discriminator")
    .HasMaxLength(200);
```

<span data-ttu-id="bcb01-125">Rozróżniacza również mogą być mapowane z właściwością rzeczywiste CLR w jednostce.</span><span class="sxs-lookup"><span data-stu-id="bcb01-125">The discriminator can also be mapped to an actual CLR property in your entity.</span></span> <span data-ttu-id="bcb01-126">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="bcb01-126">For example:</span></span>
```C#
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .HasDiscriminator<string>("BlogType");
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
    public string BlogType { get; set; }
}

public class RssBlog : Blog
{
    public string RssUrl { get; set; }
}
```

<span data-ttu-id="bcb01-127">Łącząc te dwie rzeczy ze sobą istnieje możliwość zarówno mapy rozróżniacza z właściwością rzeczywistym i skonfiguruj go tak:</span><span class="sxs-lookup"><span data-stu-id="bcb01-127">Combining these two things together it is possible to both map the discriminator to a real property and configure it:</span></span>
```C#
modelBuilder.Entity<Blog>(b =>
{
    b.HasDiscriminator<string>("BlogType");

    b.Property(e => e.BlogType)
        .HasMaxLength(200)
        .HasColumnName("blog_type");
});
```
