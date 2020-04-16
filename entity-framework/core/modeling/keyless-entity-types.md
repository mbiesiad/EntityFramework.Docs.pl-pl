---
title: Typy jednostek bezkluzynowych — EF Core
description: Jak skonfigurować typy jednostek bezkluczek przy użyciu programu Entity Framework Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 9/13/2019
uid: core/modeling/keyless-entity-types
ms.openlocfilehash: 496e1e8983ba2d5e15dbee02607ea3f2c861503e
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434217"
---
# <a name="keyless-entity-types"></a><span data-ttu-id="81622-103">Typy jednostek bez kluczy</span><span class="sxs-lookup"><span data-stu-id="81622-103">Keyless Entity Types</span></span>

> [!NOTE]
> <span data-ttu-id="81622-104">Ta funkcja została dodana w EF Core 2.1 pod nazwą typów zapytań.</span><span class="sxs-lookup"><span data-stu-id="81622-104">This feature was added in EF Core 2.1 under the name of query types.</span></span> <span data-ttu-id="81622-105">W EF Core 3.0 koncepcja została zmieniona na typy jednostek bezkluzywowych.</span><span class="sxs-lookup"><span data-stu-id="81622-105">In EF Core 3.0 the concept was renamed to keyless entity types.</span></span>

<span data-ttu-id="81622-106">Oprócz zwykłych typów jednostek model EF Core może zawierać _typy jednostek bezkluzywowych_, które mogą być używane do przeprowadzania zapytań bazy danych względem danych, które nie zawierają wartości klucza.</span><span class="sxs-lookup"><span data-stu-id="81622-106">In addition to regular entity types, an EF Core model can contain _keyless entity types_, which can be used to carry out database queries against data that doesn't contain key values.</span></span>

## <a name="defining-keyless-entity-types"></a><span data-ttu-id="81622-107">Definiowanie typów encji bezkluzywowych</span><span class="sxs-lookup"><span data-stu-id="81622-107">Defining Keyless entity types</span></span>

<span data-ttu-id="81622-108">Typy jednostek bezkluzyfów można zdefiniować za pomocą adnotacji danych lub interfejsu API Fluent:</span><span class="sxs-lookup"><span data-stu-id="81622-108">Keyless entity types can be defined using either the Data Annotation or the Fluent API:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="81622-109">Adnotacje danych</span><span class="sxs-lookup"><span data-stu-id="81622-109">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Keyless.cs?Name=Keyless&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="81622-110">Płynne API</span><span class="sxs-lookup"><span data-stu-id="81622-110">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Keyless.cs?Name=Keyless&highlight=4)]

***

## <a name="keyless-entity-types-characteristics"></a><span data-ttu-id="81622-111">Właściwości typów encji bezkluzytowych</span><span class="sxs-lookup"><span data-stu-id="81622-111">Keyless entity types characteristics</span></span>

<span data-ttu-id="81622-112">Typy jednostek bezklucików obsługują wiele z tych samych możliwości mapowania, co zwykłe typy jednostek, takie jak mapowanie dziedziczenia i właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="81622-112">Keyless entity types support many of the same mapping capabilities as regular entity types, like inheritance mapping and navigation properties.</span></span> <span data-ttu-id="81622-113">W magazynach relacyjnych można skonfigurować obiekty bazy danych docelowej i kolumny za pomocą płynnych metod interfejsu API lub adnotacji danych.</span><span class="sxs-lookup"><span data-stu-id="81622-113">On relational stores, they can configure the target database objects and columns via fluent API methods or data annotations.</span></span>

<span data-ttu-id="81622-114">Jednak różnią się one od zwykłych typów jednostek, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="81622-114">However, they are different from regular entity types in that they:</span></span>

- <span data-ttu-id="81622-115">Nie można zdefiniować klucza.</span><span class="sxs-lookup"><span data-stu-id="81622-115">Cannot have a key defined.</span></span>
- <span data-ttu-id="81622-116">Nigdy nie są śledzone pod kątem zmian w _DbContext_ i dlatego nigdy nie są wstawiane, aktualizowane lub usuwane w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="81622-116">Are never tracked for changes in the _DbContext_ and therefore are never inserted, updated or deleted on the database.</span></span>
- <span data-ttu-id="81622-117">Nigdy nie są odkrywane przez konwencję.</span><span class="sxs-lookup"><span data-stu-id="81622-117">Are never discovered by convention.</span></span>
- <span data-ttu-id="81622-118">Obsługuje tylko podzbiór możliwości mapowania nawigacji, w szczególności:</span><span class="sxs-lookup"><span data-stu-id="81622-118">Only support a subset of navigation mapping capabilities, specifically:</span></span>
  - <span data-ttu-id="81622-119">Nigdy nie mogą działać jako główny koniec związku.</span><span class="sxs-lookup"><span data-stu-id="81622-119">They may never act as the principal end of a relationship.</span></span>
  - <span data-ttu-id="81622-120">Mogą nie mieć nawigacji do posiadanych podmiotów</span><span class="sxs-lookup"><span data-stu-id="81622-120">They may not have navigations to owned entities</span></span>
  - <span data-ttu-id="81622-121">Mogą one zawierać tylko właściwości nawigacji odwołania wskazujące na regularne jednostki.</span><span class="sxs-lookup"><span data-stu-id="81622-121">They can only contain reference navigation properties pointing to regular entities.</span></span>
  - <span data-ttu-id="81622-122">Jednostki nie mogą zawierać właściwości nawigacji do typów jednostek bezkluczek.</span><span class="sxs-lookup"><span data-stu-id="81622-122">Entities cannot contain navigation properties to keyless entity types.</span></span>
- <span data-ttu-id="81622-123">Należy skonfigurować z `[Keyless]` adnotacją danych `.HasNoKey()` lub wywołaniem metody.</span><span class="sxs-lookup"><span data-stu-id="81622-123">Need to be configured with a `[Keyless]` data annotation or a `.HasNoKey()` method call.</span></span>
- <span data-ttu-id="81622-124">Może być mapowany na _kwerendę definiującą_.</span><span class="sxs-lookup"><span data-stu-id="81622-124">May be mapped to a _defining query_.</span></span> <span data-ttu-id="81622-125">Kwerenda definiująca to kwerenda zadeklarowana w modelu, która działa jako źródło danych dla typu jednostki bezkluczynowej.</span><span class="sxs-lookup"><span data-stu-id="81622-125">A defining query is a query declared in the model that acts as a data source for a keyless entity type.</span></span>

## <a name="usage-scenarios"></a><span data-ttu-id="81622-126">Scenariusze użytkowania</span><span class="sxs-lookup"><span data-stu-id="81622-126">Usage scenarios</span></span>

<span data-ttu-id="81622-127">Niektóre z głównych scenariuszy użycia dla typów jednostek bezkluczek są:</span><span class="sxs-lookup"><span data-stu-id="81622-127">Some of the main usage scenarios for keyless entity types are:</span></span>

- <span data-ttu-id="81622-128">Służąc jako typ zwracany dla [nieprzetworzonych zapytań SQL](xref:core/querying/raw-sql).</span><span class="sxs-lookup"><span data-stu-id="81622-128">Serving as the return type for [raw SQL queries](xref:core/querying/raw-sql).</span></span>
- <span data-ttu-id="81622-129">Mapowanie do widoków bazy danych, które nie zawierają klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="81622-129">Mapping to database views that do not contain a primary key.</span></span>
- <span data-ttu-id="81622-130">Mapowanie do tabel, które nie mają zdefiniowanego klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="81622-130">Mapping to tables that do not have a primary key defined.</span></span>
- <span data-ttu-id="81622-131">Mapowanie do zapytań zdefiniowanych w modelu.</span><span class="sxs-lookup"><span data-stu-id="81622-131">Mapping to queries defined in the model.</span></span>

## <a name="mapping-to-database-objects"></a><span data-ttu-id="81622-132">Mapowanie do obiektów bazy danych</span><span class="sxs-lookup"><span data-stu-id="81622-132">Mapping to database objects</span></span>

<span data-ttu-id="81622-133">Mapowanie typu jednostki bezkluzyfów `ToTable` do `ToView` obiektu bazy danych jest osiągane przy użyciu lub płynnego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="81622-133">Mapping a keyless entity type to a database object is achieved using the `ToTable` or `ToView` fluent API.</span></span> <span data-ttu-id="81622-134">Z punktu widzenia EF Core obiekt bazy danych określony w tej metodzie jest _widok_, co oznacza, że jest traktowany jako źródło kwerendy tylko do odczytu i nie może być celem operacji aktualizacji, wstawiania lub usuwania.</span><span class="sxs-lookup"><span data-stu-id="81622-134">From the perspective of EF Core, the database object specified in this method is a _view_, meaning that it is treated as a read-only query source and cannot be the target of update, insert or delete operations.</span></span> <span data-ttu-id="81622-135">Nie oznacza to jednak, że obiekt bazy danych jest faktycznie wymagany do widoku bazy danych.</span><span class="sxs-lookup"><span data-stu-id="81622-135">However, this does not mean that the database object is actually required to be a database view.</span></span> <span data-ttu-id="81622-136">Alternatywnie może to być tabela bazy danych, która będzie traktowana jako tylko do odczytu.</span><span class="sxs-lookup"><span data-stu-id="81622-136">It can alternatively be a database table that will be treated as read-only.</span></span> <span data-ttu-id="81622-137">Z drugiej strony dla zwykłych typów jednostek EF Core `ToTable` zakłada, że obiekt bazy danych określony w metodzie może być traktowany jako _tabela,_ co oznacza, że może służyć jako źródło kwerendy, ale także kierowane przez operacje aktualizacji, usuwania i wstawiania.</span><span class="sxs-lookup"><span data-stu-id="81622-137">Conversely, for regular entity types, EF Core assumes that a database object specified in the `ToTable` method can be treated as a _table_, meaning that it can be used as a query source but also targeted by update, delete and insert operations.</span></span> <span data-ttu-id="81622-138">W rzeczywistości można określić nazwę widoku `ToTable` bazy danych w i wszystko powinno działać poprawnie, tak długo, jak widok jest skonfigurowany do aktualizacji w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="81622-138">In fact, you can specify the name of a database view in `ToTable` and everything should work fine as long as the view is configured to be updatable on the database.</span></span>

> [!NOTE]
> <span data-ttu-id="81622-139">`ToView`zakłada, że obiekt już istnieje w bazie danych i nie zostanie utworzony przez migracje.</span><span class="sxs-lookup"><span data-stu-id="81622-139">`ToView` assumes that the object already exists in the database and it won't be created by migrations.</span></span>

## <a name="example"></a><span data-ttu-id="81622-140">Przykład</span><span class="sxs-lookup"><span data-stu-id="81622-140">Example</span></span>

<span data-ttu-id="81622-141">W poniższym przykładzie pokazano, jak używać typów jednostek bezkluzytowych do wykonywania zapytań o widok bazy danych.</span><span class="sxs-lookup"><span data-stu-id="81622-141">The following example shows how to use keyless entity types to query a database view.</span></span>

> [!TIP]
> <span data-ttu-id="81622-142">Możesz wyświetlić [ten](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes) przykład artykułu na GitHub.</span><span class="sxs-lookup"><span data-stu-id="81622-142">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes) on GitHub.</span></span>

<span data-ttu-id="81622-143">Najpierw definiujemy prosty model bloga i postu:</span><span class="sxs-lookup"><span data-stu-id="81622-143">First, we define a simple Blog and Post model:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Entities)]

<span data-ttu-id="81622-144">Następnie definiujemy prosty widok bazy danych, który pozwoli nam zbadać liczbę wpisów skojarzonych z każdym blogiem:</span><span class="sxs-lookup"><span data-stu-id="81622-144">Next, we define a simple database view that will allow us to query the number of posts associated with each blog:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#View)]

<span data-ttu-id="81622-145">Następnie definiujemy klasę do przechowywania wyniku z widoku bazy danych:</span><span class="sxs-lookup"><span data-stu-id="81622-145">Next, we define a class to hold the result from the database view:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#KeylessEntityType)]

<span data-ttu-id="81622-146">Następnie konfigurujemy typ jednostki bezkluzywowe `HasNoKey` w _OnModelCreating_ przy użyciu interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="81622-146">Next, we configure the keyless entity type in _OnModelCreating_ using the `HasNoKey` API.</span></span>
<span data-ttu-id="81622-147">Używamy płynnego interfejsu API konfiguracji, aby skonfigurować mapowanie dla typu jednostki bezkluzyfowej:</span><span class="sxs-lookup"><span data-stu-id="81622-147">We use fluent configuration API to configure the mapping for the keyless entity type:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Configuration)]

<span data-ttu-id="81622-148">Następnie konfigurujemy, `DbContext` aby `DbSet<T>`uwzględnić:</span><span class="sxs-lookup"><span data-stu-id="81622-148">Next, we configure the `DbContext` to include the `DbSet<T>`:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#DbSet)]

<span data-ttu-id="81622-149">Na koniec możemy zbadać widok bazy danych w standardowy sposób:</span><span class="sxs-lookup"><span data-stu-id="81622-149">Finally, we can query the database view in the standard way:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Query)]

> [!TIP]
> <span data-ttu-id="81622-150">Należy zauważyć, że zdefiniowaliśmy również właściwość kwerendy na poziomie kontekstu (DbSet), aby działać jako katalog główny dla zapytań dotyczących tego typu.</span><span class="sxs-lookup"><span data-stu-id="81622-150">Note we have also defined a context level query property (DbSet) to act as a root for queries against this type.</span></span>
