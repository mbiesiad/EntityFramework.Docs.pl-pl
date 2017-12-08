---
title: "Maksymalna długość - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: c39c5d43-018d-48b8-94f2-b8bc7c686c69
ms.technology: entity-framework-core
uid: core/modeling/max-length
ms.openlocfilehash: 7325c0c3328477473392bf9e7c82f1696bb4f424
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2017
---
# <a name="maximum-length"></a><span data-ttu-id="d0d8c-102">Maksymalna długość</span><span class="sxs-lookup"><span data-stu-id="d0d8c-102">Maximum Length</span></span>

<span data-ttu-id="d0d8c-103">Konfigurowanie maksymalnej długości udostępnia wskazówkę dla magazynu danych o typie danych odpowiednie dla danej właściwości.</span><span class="sxs-lookup"><span data-stu-id="d0d8c-103">Configuring a maximum length provides a hint to the data store about the appropriate data type to use for a given property.</span></span> <span data-ttu-id="d0d8c-104">Maksymalna długość ma zastosowanie tylko do typów danych w tablicy, takich jak `string` i `byte[]`.</span><span class="sxs-lookup"><span data-stu-id="d0d8c-104">Maximum length only applies to array data types, such as `string` and `byte[]`.</span></span>

> [!NOTE]  
> <span data-ttu-id="d0d8c-105">Entity Framework nie wszystkich sprawdzania poprawności maksymalną długość przed przekazaniem do dostawcy.</span><span class="sxs-lookup"><span data-stu-id="d0d8c-105">Entity Framework does not do any validation of maximum length before passing data to the provider.</span></span> <span data-ttu-id="d0d8c-106">Jest magazyn dostawcy lub danych, aby sprawdzić, czy odpowiednie.</span><span class="sxs-lookup"><span data-stu-id="d0d8c-106">It is up to the provider or data store to validate if appropriate.</span></span> <span data-ttu-id="d0d8c-107">Na przykład gdy przeznaczonych dla programu SQL Server, co przekracza maksymalną długość spowodują wyjątek jako typ danych kolumny źródłowej nie zezwala na nadmiarowe dane mają być przechowywane.</span><span class="sxs-lookup"><span data-stu-id="d0d8c-107">For example, when targeting SQL Server, exceeding the maximum length will result in an exception as the data type of the underlying column will not allow excess data to be stored.</span></span>

## <a name="conventions"></a><span data-ttu-id="d0d8c-108">Konwencje</span><span class="sxs-lookup"><span data-stu-id="d0d8c-108">Conventions</span></span>

<span data-ttu-id="d0d8c-109">Konwencja jest pozostawiany do dostawcy bazy danych, aby wybrać odpowiedni typ danych właściwości.</span><span class="sxs-lookup"><span data-stu-id="d0d8c-109">By convention, it is left up to the database provider to choose an appropriate data type for properties.</span></span> <span data-ttu-id="d0d8c-110">Dla właściwości, które mają długość dostawcy bazy danych będzie zazwyczaj wybierz typ danych, umożliwiający najdłuższym długość danych.</span><span class="sxs-lookup"><span data-stu-id="d0d8c-110">For properties that have a length, the database provider will generally choose a data type that allows for the longest length of data.</span></span> <span data-ttu-id="d0d8c-111">Na przykład Microsoft SQL Server będzie używać `nvarchar(max)` dla `string` właściwości (lub `nvarchar(450)` Jeśli kolumna jest używana jako klucz).</span><span class="sxs-lookup"><span data-stu-id="d0d8c-111">For example, Microsoft SQL Server will use `nvarchar(max)` for `string` properties (or `nvarchar(450)` if the column is used as a key).</span></span>

## <a name="data-annotations"></a><span data-ttu-id="d0d8c-112">Adnotacji danych</span><span class="sxs-lookup"><span data-stu-id="d0d8c-112">Data Annotations</span></span>

<span data-ttu-id="d0d8c-113">Aby skonfigurować maksymalną długość dla właściwości, można użyć adnotacji danych.</span><span class="sxs-lookup"><span data-stu-id="d0d8c-113">You can use the Data Annotations to configure a maximum length for a property.</span></span> <span data-ttu-id="d0d8c-114">W tym przykładzie przeznaczonych dla programu SQL Server, spowodowałoby `nvarchar(500)` używany typ danych.</span><span class="sxs-lookup"><span data-stu-id="d0d8c-114">In this example, targeting SQL Server this would result in the `nvarchar(500)` data type being used.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/DataAnnotations/Samples/MaxLength.cs?highlight=4)] -->
``` csharp
public class Blog
{
    public int BlogId { get; set; }
    [MaxLength(500)]
    public string Url { get; set; }
}
```

## <a name="fluent-api"></a><span data-ttu-id="d0d8c-115">Interfejsu API Fluent</span><span class="sxs-lookup"><span data-stu-id="d0d8c-115">Fluent API</span></span>

<span data-ttu-id="d0d8c-116">Aby skonfigurować maksymalną długość dla właściwości, można użyć interfejsu API Fluent.</span><span class="sxs-lookup"><span data-stu-id="d0d8c-116">You can use the Fluent API to configure a maximum length for a property.</span></span> <span data-ttu-id="d0d8c-117">W tym przykładzie przeznaczonych dla programu SQL Server, spowodowałoby `nvarchar(500)` używany typ danych.</span><span class="sxs-lookup"><span data-stu-id="d0d8c-117">In this example, targeting SQL Server this would result in the `nvarchar(500)` data type being used.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/MaxLength.cs?highlight=7,8,9)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .Property(b => b.Url)
            .HasMaxLength(500);
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```