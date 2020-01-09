---
title: Indeksy — EF Core
author: roji
ms.date: 12/16/2019
ms.assetid: 85b92003-b692-417d-ac1d-76d40dce664b
uid: core/modeling/indexes
ms.openlocfilehash: 810fccc0c6b035f515107601b245811f7b4118a6
ms.sourcegitcommit: 32c51c22988c6f83ed4f8e50a1d01be3f4114e81
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/27/2019
ms.locfileid: "75502139"
---
# <a name="indexes"></a><span data-ttu-id="01e5d-102">Indeksy</span><span class="sxs-lookup"><span data-stu-id="01e5d-102">Indexes</span></span>

<span data-ttu-id="01e5d-103">Indeksy są typowymi koncepcjami w wielu magazynach danych.</span><span class="sxs-lookup"><span data-stu-id="01e5d-103">Indexes are a common concept across many data stores.</span></span> <span data-ttu-id="01e5d-104">Chociaż ich implementacja w magazynie danych może się różnić, są one używane do wykonywania wyszukiwania na podstawie kolumny (lub zestawu kolumn) bardziej wydajne.</span><span class="sxs-lookup"><span data-stu-id="01e5d-104">While their implementation in the data store may vary, they are used to make lookups based on a column (or set of columns) more efficient.</span></span>

<span data-ttu-id="01e5d-105">Nie można tworzyć indeksów przy użyciu adnotacji danych.</span><span class="sxs-lookup"><span data-stu-id="01e5d-105">Indexes cannot be created using data annotations.</span></span> <span data-ttu-id="01e5d-106">Możesz użyć interfejsu API Fluent, aby określić indeks w jednej kolumnie w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="01e5d-106">You can use the Fluent API to specify an index on a single column as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Index.cs?name=Index&highlight=4)]

<span data-ttu-id="01e5d-107">Można również określić indeks w więcej niż jednej kolumnie:</span><span class="sxs-lookup"><span data-stu-id="01e5d-107">You can also specify an index over more than one column:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexComposite.cs?name=Composite&highlight=4)]

> [!NOTE]
> <span data-ttu-id="01e5d-108">Zgodnie z Konwencją indeks jest tworzony we wszystkich właściwościach (lub zestawach właściwości), które są używane jako klucz obcy.</span><span class="sxs-lookup"><span data-stu-id="01e5d-108">By convention, an index is created in each property (or set of properties) that are used as a foreign key.</span></span>
>
> <span data-ttu-id="01e5d-109">EF Core obsługuje tylko jeden indeks według odrębnego zestawu właściwości.</span><span class="sxs-lookup"><span data-stu-id="01e5d-109">EF Core only supports one index per distinct set of properties.</span></span> <span data-ttu-id="01e5d-110">Jeśli używasz interfejsu API Fluent do skonfigurowania indeksu dla zestawu właściwości, który ma już zdefiniowany indeks, w ramach Konwencji lub poprzedniej konfiguracji, zmienisz definicję tego indeksu.</span><span class="sxs-lookup"><span data-stu-id="01e5d-110">If you use the Fluent API to configure an index on a set of properties that already has an index defined, either by convention or previous configuration, then you will be changing the definition of that index.</span></span> <span data-ttu-id="01e5d-111">Jest to przydatne, jeśli chcesz skonfigurować indeks, który został utworzony przez Konwencję.</span><span class="sxs-lookup"><span data-stu-id="01e5d-111">This is useful if you want to further configure an index that was created by convention.</span></span>

## <a name="index-uniqueness"></a><span data-ttu-id="01e5d-112">Unikatowość indeksu</span><span class="sxs-lookup"><span data-stu-id="01e5d-112">Index uniqueness</span></span>

<span data-ttu-id="01e5d-113">Domyślnie indeksy nie są unikatowe: wiele wierszy może mieć te same wartości dla zestawu kolumn indeksu.</span><span class="sxs-lookup"><span data-stu-id="01e5d-113">By default, indexes aren't unique: multiple rows are allowed to have the same value(s) for the index's column set.</span></span> <span data-ttu-id="01e5d-114">Indeks można zmienić w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="01e5d-114">You can make an index unique as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexUnique.cs?name=IndexUnique&highlight=5)]

<span data-ttu-id="01e5d-115">Próba wstawienia więcej niż jednej jednostki z tymi samymi wartościami dla zestawu kolumn indeksu spowoduje zgłoszenie wyjątku.</span><span class="sxs-lookup"><span data-stu-id="01e5d-115">Attempting to insert more than one entity with the same values for the index's column set will cause an exception to be thrown.</span></span>

## <a name="index-name"></a><span data-ttu-id="01e5d-116">Nazwa indeksu</span><span class="sxs-lookup"><span data-stu-id="01e5d-116">Index name</span></span>

<span data-ttu-id="01e5d-117">Według Konwencji indeksy utworzone w relacyjnej bazie danych mają nazwę `IX_<type name>_<property name>`.</span><span class="sxs-lookup"><span data-stu-id="01e5d-117">By convention, indexes created in a relational database are named `IX_<type name>_<property name>`.</span></span> <span data-ttu-id="01e5d-118">W przypadku indeksów złożonych `<property name>` stać się rozdzielaną podkreśleniem listą nazw właściwości.</span><span class="sxs-lookup"><span data-stu-id="01e5d-118">For composite indexes, `<property name>` becomes an underscore separated list of property names.</span></span>

<span data-ttu-id="01e5d-119">Aby ustawić nazwę indeksu utworzonego w bazie danych, można użyć interfejsu API Fluent:</span><span class="sxs-lookup"><span data-stu-id="01e5d-119">You can use the Fluent API to set the name of the index created in the database:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexName.cs?name=IndexName&highlight=5)]

## <a name="index-filter"></a><span data-ttu-id="01e5d-120">Filtr indeksu</span><span class="sxs-lookup"><span data-stu-id="01e5d-120">Index filter</span></span>

<span data-ttu-id="01e5d-121">Niektóre relacyjne bazy danych umożliwiają określenie indeksu filtrowanego lub częściowego.</span><span class="sxs-lookup"><span data-stu-id="01e5d-121">Some relational databases allow you to specify a filtered or partial index.</span></span> <span data-ttu-id="01e5d-122">Pozwala to na indeksowanie tylko podzbioru wartości kolumny, zmniejszenie rozmiaru indeksu i zwiększenie wydajności i miejsca na dysku.</span><span class="sxs-lookup"><span data-stu-id="01e5d-122">This allows you to index only a subset of a column's values, reducing the index's size and improving both performance and disk space usage.</span></span> <span data-ttu-id="01e5d-123">Więcej informacji o SQL Server filtrowanych indeksów [znajduje się w dokumentacji](https://docs.microsoft.com/sql/relational-databases/indexes/create-filtered-indexes).</span><span class="sxs-lookup"><span data-stu-id="01e5d-123">For more information on SQL Server filtered indexes, [see the documentation](https://docs.microsoft.com/sql/relational-databases/indexes/create-filtered-indexes).</span></span>

<span data-ttu-id="01e5d-124">Korzystając z interfejsu API Fluent, można określić filtr dla indeksu, który jest dostarczany jako wyrażenie SQL:</span><span class="sxs-lookup"><span data-stu-id="01e5d-124">You can use the Fluent API to specify a filter on an index, provided as a SQL expression:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexFilter.cs?name=IndexFilter&highlight=5)]

<span data-ttu-id="01e5d-125">W przypadku używania dostawcy SQL Server Dr dodaje filtr `'IS NOT NULL'` dla wszystkich kolumn dopuszczających wartości null, które są częścią unikatowego indeksu.</span><span class="sxs-lookup"><span data-stu-id="01e5d-125">When using the SQL Server provider EF adds an `'IS NOT NULL'` filter for all nullable columns that are part of a unique index.</span></span> <span data-ttu-id="01e5d-126">Aby zastąpić tę Konwencję, możesz podać wartość `null`.</span><span class="sxs-lookup"><span data-stu-id="01e5d-126">To override this convention you can supply a `null` value.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexNoFilter.cs?name=IndexNoFilter&highlight=6)]

## <a name="included-columns"></a><span data-ttu-id="01e5d-127">Uwzględnione kolumny</span><span class="sxs-lookup"><span data-stu-id="01e5d-127">Included columns</span></span>

<span data-ttu-id="01e5d-128">Niektóre relacyjne bazy danych umożliwiają skonfigurowanie zestawu kolumn, które znajdują się w indeksie, ale nie są częścią swojego klucza.</span><span class="sxs-lookup"><span data-stu-id="01e5d-128">Some relational databases allow you to configure a set of columns which get included in the index, but aren't part of its "key".</span></span> <span data-ttu-id="01e5d-129">Może to znacząco poprawić wydajność zapytań, gdy wszystkie kolumny w zapytaniu są uwzględniane w indeksie jako kolumny Key lub nonkey, ponieważ sama tabela nie jest potrzebna.</span><span class="sxs-lookup"><span data-stu-id="01e5d-129">This can significantly improve query performance when all columns in the query are included in the index either as key or nonkey columns, as the table itself doesn't need to be accessed.</span></span> <span data-ttu-id="01e5d-130">Aby uzyskać więcej informacji na temat SQL Server uwzględnionych kolumn, [zapoznaj się z dokumentacją](https://docs.microsoft.com/sql/relational-databases/indexes/create-indexes-with-included-columns).</span><span class="sxs-lookup"><span data-stu-id="01e5d-130">For more information on SQL Server included columns, [see the documentation](https://docs.microsoft.com/sql/relational-databases/indexes/create-indexes-with-included-columns).</span></span>

<span data-ttu-id="01e5d-131">W poniższym przykładzie kolumna `Url` jest częścią klucza indeksu, dlatego każde filtrowanie zapytań w tej kolumnie może korzystać z indeksu.</span><span class="sxs-lookup"><span data-stu-id="01e5d-131">In the following example, the `Url` column is part of the index key, so any query filtering on that column can use the index.</span></span> <span data-ttu-id="01e5d-132">Ponadto zapytania uzyskujące dostęp tylko do `Title` i `PublishedOn` kolumny nie będą miały dostępu do tabeli i będą działać bardziej wydajniej:</span><span class="sxs-lookup"><span data-stu-id="01e5d-132">But in addition, queries accessing only the `Title` and `PublishedOn` columns will not need to access the table and will run more efficiently:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexInclude.cs?name=IndexInclude&highlight=5-9)]
