---
title: Filtry zapytań globalnych - programu EF Core
author: anpete
ms.date: 11/03/2017
uid: core/querying/filters
ms.openlocfilehash: 201292a440d37d240f31452eaebb23dcd4aee1a6
ms.sourcegitcommit: 8dd71a57a01c439431164c163a0722877d0e5cd8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2018
ms.locfileid: "53028170"
---
# <a name="global-query-filters"></a><span data-ttu-id="860e3-102">Filtry zapytań globalnych</span><span class="sxs-lookup"><span data-stu-id="860e3-102">Global Query Filters</span></span>

<span data-ttu-id="860e3-103">Filtry zapytań globalnych są predykatów zapytań LINQ (wyrażenia logicznego zwykle przekazywane do programu LINQ *gdzie* — operator zapytań) stosowane do typów jednostek w modelu metadanych (zwykle w *OnModelCreating*).</span><span class="sxs-lookup"><span data-stu-id="860e3-103">Global query filters are LINQ query predicates (a boolean expression typically passed to the LINQ *Where* query operator) applied to Entity Types in the metadata model (usually in *OnModelCreating*).</span></span> <span data-ttu-id="860e3-104">Takie filtry są automatycznie stosowane do żadnych zapytań LINQ, obejmujące tych typów jednostek, w tym odwołania do właściwości nawigacji odwołanie pośrednio, takie jak przy użyciu Include lub bezpośredniego typów jednostek.</span><span class="sxs-lookup"><span data-stu-id="860e3-104">Such filters are automatically applied to any LINQ queries involving those Entity Types, including Entity Types referenced indirectly, such as through the use of Include or direct navigation property references.</span></span> <span data-ttu-id="860e3-105">Niektóre typowe aplikacje tej funkcji są następujące:</span><span class="sxs-lookup"><span data-stu-id="860e3-105">Some common applications of this feature are:</span></span>

* <span data-ttu-id="860e3-106">**Usuwanie nietrwałe** — Określa typ jednostki *IsDeleted* właściwości.</span><span class="sxs-lookup"><span data-stu-id="860e3-106">**Soft delete** - An Entity Type defines an *IsDeleted* property.</span></span>
* <span data-ttu-id="860e3-107">**Wielodostępność** — Określa typ jednostki *TenantId* właściwości.</span><span class="sxs-lookup"><span data-stu-id="860e3-107">**Multi-tenancy** - An Entity Type defines a *TenantId* property.</span></span>

## <a name="example"></a><span data-ttu-id="860e3-108">Przykład</span><span class="sxs-lookup"><span data-stu-id="860e3-108">Example</span></span>

<span data-ttu-id="860e3-109">Poniższy przykład pokazuje sposób użycia globalne filtry kwerendy do implementacji zachowania kwerendy opcji soft-delete oraz obsługi wielu dzierżawców w modelu prostego do obsługi blogów.</span><span class="sxs-lookup"><span data-stu-id="860e3-109">The following example shows how to use Global Query Filters to implement soft-delete and multi-tenancy query behaviors in a simple blogging model.</span></span>

> [!TIP]
> <span data-ttu-id="860e3-110">[Przykład](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters) użyty w tym artykule można zobaczyć w witrynie GitHub.</span><span class="sxs-lookup"><span data-stu-id="860e3-110">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters) on GitHub.</span></span>

<span data-ttu-id="860e3-111">Najpierw należy zdefiniować jednostki:</span><span class="sxs-lookup"><span data-stu-id="860e3-111">First, define the entities:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Entities)]

<span data-ttu-id="860e3-112">Należy pamiętać, deklaracja __tenantId_ na _blogu_ jednostki.</span><span class="sxs-lookup"><span data-stu-id="860e3-112">Note the declaration of a __tenantId_ field on the _Blog_ entity.</span></span> <span data-ttu-id="860e3-113">To posłuży do powiązania każde wystąpienie blogu z określonej dzierżawy.</span><span class="sxs-lookup"><span data-stu-id="860e3-113">This will be used to associate each Blog instance with a specific tenant.</span></span> <span data-ttu-id="860e3-114">Jest również definiowany _IsDeleted_ właściwość _wpis_ typu jednostki.</span><span class="sxs-lookup"><span data-stu-id="860e3-114">Also defined is an _IsDeleted_ property on the _Post_ entity type.</span></span> <span data-ttu-id="860e3-115">Służy to śledzenie czy _wpis_ wystąpienie zostało "wszystkie usunięte nietrwale".</span><span class="sxs-lookup"><span data-stu-id="860e3-115">This is used to keep track of whether a _Post_ instance has been "soft-deleted".</span></span> <span data-ttu-id="860e3-116">Oznacza to wystąpienie jest oznaczony jako usunięty bez fizycznym usunięciu danych bazowych.</span><span class="sxs-lookup"><span data-stu-id="860e3-116">That is, the instance is marked as deleted without physically removing the underlying data.</span></span>

<span data-ttu-id="860e3-117">Następnie skonfiguruj filtrów zapytania w _OnModelCreating_ przy użyciu ```HasQueryFilter``` interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="860e3-117">Next, configure the query filters in _OnModelCreating_ using the ```HasQueryFilter``` API.</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Configuration)]

<span data-ttu-id="860e3-118">Predykatu wyrażenie przekazany do _HasQueryFilter_ wywołania teraz zostaną automatycznie zastosowane na żadne zapytania LINQ dla tych typów.</span><span class="sxs-lookup"><span data-stu-id="860e3-118">The predicate expressions passed to the _HasQueryFilter_ calls will now automatically be applied to any LINQ queries for those types.</span></span>

> [!TIP]
> <span data-ttu-id="860e3-119">Zwróć uwagę na użycie pola poziomu wystąpienia typu DbContext: ```_tenantId``` używane do ustawiania bieżącej dzierżawy.</span><span class="sxs-lookup"><span data-stu-id="860e3-119">Note the use of a DbContext instance level field: ```_tenantId``` used to set the current tenant.</span></span> <span data-ttu-id="860e3-120">Filtry na poziomie modelu będzie używać wartości z wystąpienia poprawny kontekst (oznacza to, że wystąpienie, które jest wykonywane zapytanie).</span><span class="sxs-lookup"><span data-stu-id="860e3-120">Model-level filters will use the value from the correct context instance (that is, the instance that is executing the query).</span></span>

## <a name="disabling-filters"></a><span data-ttu-id="860e3-121">Wyłączanie filtrów</span><span class="sxs-lookup"><span data-stu-id="860e3-121">Disabling Filters</span></span>

<span data-ttu-id="860e3-122">Filtry mogą być wyłączone dla poszczególnych zapytań LINQ za pomocą ```IgnoreQueryFilters()``` operatora.</span><span class="sxs-lookup"><span data-stu-id="860e3-122">Filters may be disabled for individual LINQ queries by using the ```IgnoreQueryFilters()``` operator.</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a><span data-ttu-id="860e3-123">Ograniczenia</span><span class="sxs-lookup"><span data-stu-id="860e3-123">Limitations</span></span>

<span data-ttu-id="860e3-124">Filtry zapytań globalnych mają następujące ograniczenia:</span><span class="sxs-lookup"><span data-stu-id="860e3-124">Global query filters have the following limitations:</span></span>

* <span data-ttu-id="860e3-125">Filtry nie może zawierać odwołania do właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="860e3-125">Filters cannot contain references to navigation properties.</span></span>
* <span data-ttu-id="860e3-126">Filtry można zdefiniować tylko dla głównego typu jednostki z hierarchii dziedziczenia.</span><span class="sxs-lookup"><span data-stu-id="860e3-126">Filters can only be defined for the root Entity Type of an inheritance hierarchy.</span></span>
