---
title: Typy jednostek o mniejszej długości — EF Core
description: Jak skonfigurować typy jednostek mniej niż przy użyciu Entity Framework Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 9/13/2019
uid: core/modeling/keyless-entity-types
ms.openlocfilehash: 00e8f17c88fd51e39df3c1e45c648c203bbbe324
ms.sourcegitcommit: 387cbd8109c0fc5ce6bdc85d0dec1aed72ad4c33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82103129"
---
# <a name="keyless-entity-types"></a><span data-ttu-id="dae79-103">Typy jednostek bez kluczy</span><span class="sxs-lookup"><span data-stu-id="dae79-103">Keyless Entity Types</span></span>

> [!NOTE]
> <span data-ttu-id="dae79-104">Ta funkcja została dodana w EF Core 2,1 pod nazwą typów zapytań.</span><span class="sxs-lookup"><span data-stu-id="dae79-104">This feature was added in EF Core 2.1 under the name of query types.</span></span> <span data-ttu-id="dae79-105">W EF Core 3,0 nazwa koncepcji została zmieniona na typy jednostek, które nie są mniejsze.</span><span class="sxs-lookup"><span data-stu-id="dae79-105">In EF Core 3.0 the concept was renamed to keyless entity types.</span></span> <span data-ttu-id="dae79-106">Adnotacja danych jest `[Keyless]` dostępna w EFCore 5,0.</span><span class="sxs-lookup"><span data-stu-id="dae79-106">The `[Keyless]` Data Annotation became available in EFCore 5.0.</span></span>

<span data-ttu-id="dae79-107">Oprócz zwykłych typów jednostek model EF Core może zawierać _typy jednostek_bez kluczy, które mogą być używane do przeprowadzania zapytań bazy danych do danych, które nie zawierają wartości klucza.</span><span class="sxs-lookup"><span data-stu-id="dae79-107">In addition to regular entity types, an EF Core model can contain _keyless entity types_, which can be used to carry out database queries against data that doesn't contain key values.</span></span>

## <a name="defining-keyless-entity-types"></a><span data-ttu-id="dae79-108">Definiowanie typów jednostek bez mniejszych</span><span class="sxs-lookup"><span data-stu-id="dae79-108">Defining Keyless entity types</span></span>

<span data-ttu-id="dae79-109">Typy jednostek o mniejszej długości można definiować przy użyciu adnotacji danych lub interfejsu API Fluent:</span><span class="sxs-lookup"><span data-stu-id="dae79-109">Keyless entity types can be defined using either the Data Annotation or the Fluent API:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="dae79-110">Adnotacje danych</span><span class="sxs-lookup"><span data-stu-id="dae79-110">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Keyless.cs?Name=Keyless&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="dae79-111">Interfejs API Fluent</span><span class="sxs-lookup"><span data-stu-id="dae79-111">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Keyless.cs?Name=Keyless&highlight=4)]

***

## <a name="keyless-entity-types-characteristics"></a><span data-ttu-id="dae79-112">Charakterystyka typów jednostek bez parametrów</span><span class="sxs-lookup"><span data-stu-id="dae79-112">Keyless entity types characteristics</span></span>

<span data-ttu-id="dae79-113">Typy jednostek o mniejszej liczbie obsługują wiele takich samych możliwości mapowania jak zwykłe typy jednostek, takie jak mapowanie dziedziczenia i właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="dae79-113">Keyless entity types support many of the same mapping capabilities as regular entity types, like inheritance mapping and navigation properties.</span></span> <span data-ttu-id="dae79-114">W magazynach relacyjnych można konfigurować docelowe obiekty i kolumny bazy danych za pomocą metod interfejsu API Fluent lub adnotacji danych.</span><span class="sxs-lookup"><span data-stu-id="dae79-114">On relational stores, they can configure the target database objects and columns via fluent API methods or data annotations.</span></span>

<span data-ttu-id="dae79-115">Jednak różnią się one od zwykłych typów jednostek w tym, że:</span><span class="sxs-lookup"><span data-stu-id="dae79-115">However, they are different from regular entity types in that they:</span></span>

- <span data-ttu-id="dae79-116">Nie można zdefiniować klucza.</span><span class="sxs-lookup"><span data-stu-id="dae79-116">Cannot have a key defined.</span></span>
- <span data-ttu-id="dae79-117">Nigdy nie są śledzone pod kątem zmian w _kontekście DbContext_ , dlatego nigdy nie są wstawiane, aktualizowane ani usuwane w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="dae79-117">Are never tracked for changes in the _DbContext_ and therefore are never inserted, updated or deleted on the database.</span></span>
- <span data-ttu-id="dae79-118">Nigdy nie są odnajdywane według Konwencji.</span><span class="sxs-lookup"><span data-stu-id="dae79-118">Are never discovered by convention.</span></span>
- <span data-ttu-id="dae79-119">Obsługiwane jest tylko podzbiór funkcji mapowania nawigacji, w tym:</span><span class="sxs-lookup"><span data-stu-id="dae79-119">Only support a subset of navigation mapping capabilities, specifically:</span></span>
  - <span data-ttu-id="dae79-120">Mogą nigdy nie działać jako główny koniec relacji.</span><span class="sxs-lookup"><span data-stu-id="dae79-120">They may never act as the principal end of a relationship.</span></span>
  - <span data-ttu-id="dae79-121">Mogą nie mieć nawigacji do jednostek będących własnością</span><span class="sxs-lookup"><span data-stu-id="dae79-121">They may not have navigations to owned entities</span></span>
  - <span data-ttu-id="dae79-122">Mogą zawierać tylko właściwości nawigacji referencyjnej wskazujące zwykłe jednostki.</span><span class="sxs-lookup"><span data-stu-id="dae79-122">They can only contain reference navigation properties pointing to regular entities.</span></span>
  - <span data-ttu-id="dae79-123">Jednostki nie mogą zawierać właściwości nawigacji do typów jednostek, które nie są mniejsze.</span><span class="sxs-lookup"><span data-stu-id="dae79-123">Entities cannot contain navigation properties to keyless entity types.</span></span>
- <span data-ttu-id="dae79-124">Należy skonfigurować za pomocą adnotacji `[Keyless]` danych lub wywołania `.HasNoKey()` metody.</span><span class="sxs-lookup"><span data-stu-id="dae79-124">Need to be configured with a `[Keyless]` data annotation or a `.HasNoKey()` method call.</span></span>
- <span data-ttu-id="dae79-125">Może być zamapowany na _zapytanie definiujące_.</span><span class="sxs-lookup"><span data-stu-id="dae79-125">May be mapped to a _defining query_.</span></span> <span data-ttu-id="dae79-126">Zapytanie definiujące jest zapytaniem zadeklarowanym w modelu, który działa jako źródło danych dla typu jednostki o niższym stopniu.</span><span class="sxs-lookup"><span data-stu-id="dae79-126">A defining query is a query declared in the model that acts as a data source for a keyless entity type.</span></span>

## <a name="usage-scenarios"></a><span data-ttu-id="dae79-127">Scenariusze użytkowania</span><span class="sxs-lookup"><span data-stu-id="dae79-127">Usage scenarios</span></span>

<span data-ttu-id="dae79-128">Niektóre główne scenariusze użycia dla typów jednostek nie są następujące:</span><span class="sxs-lookup"><span data-stu-id="dae79-128">Some of the main usage scenarios for keyless entity types are:</span></span>

- <span data-ttu-id="dae79-129">Służy jako typ zwracany dla [nieprzetworzonych zapytań SQL](xref:core/querying/raw-sql).</span><span class="sxs-lookup"><span data-stu-id="dae79-129">Serving as the return type for [raw SQL queries](xref:core/querying/raw-sql).</span></span>
- <span data-ttu-id="dae79-130">Mapowanie do widoków bazy danych, które nie zawierają klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="dae79-130">Mapping to database views that do not contain a primary key.</span></span>
- <span data-ttu-id="dae79-131">Mapowanie do tabel, które nie mają zdefiniowanego klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="dae79-131">Mapping to tables that do not have a primary key defined.</span></span>
- <span data-ttu-id="dae79-132">Mapowanie na zapytania zdefiniowane w modelu.</span><span class="sxs-lookup"><span data-stu-id="dae79-132">Mapping to queries defined in the model.</span></span>

## <a name="mapping-to-database-objects"></a><span data-ttu-id="dae79-133">Mapowanie na obiekty bazy danych</span><span class="sxs-lookup"><span data-stu-id="dae79-133">Mapping to database objects</span></span>

<span data-ttu-id="dae79-134">Mapowanie typu jednostki o mniejszym stopniu do obiektu bazy danych jest realizowane `ToTable` przy `ToView` użyciu interfejsu API programu lub.</span><span class="sxs-lookup"><span data-stu-id="dae79-134">Mapping a keyless entity type to a database object is achieved using the `ToTable` or `ToView` fluent API.</span></span> <span data-ttu-id="dae79-135">Z perspektywy EF Core obiekt bazy danych określony w tej metodzie jest _widokiem_, co oznacza, że jest traktowany jako źródło zapytania tylko do odczytu i nie może być elementem docelowym operacji Update, INSERT ani Delete.</span><span class="sxs-lookup"><span data-stu-id="dae79-135">From the perspective of EF Core, the database object specified in this method is a _view_, meaning that it is treated as a read-only query source and cannot be the target of update, insert or delete operations.</span></span> <span data-ttu-id="dae79-136">Nie oznacza to jednak, że obiekt bazy danych jest rzeczywiście wymagany jako widok bazy danych.</span><span class="sxs-lookup"><span data-stu-id="dae79-136">However, this does not mean that the database object is actually required to be a database view.</span></span> <span data-ttu-id="dae79-137">Może być również tabelą bazy danych, która będzie traktowana jako tylko do odczytu.</span><span class="sxs-lookup"><span data-stu-id="dae79-137">It can alternatively be a database table that will be treated as read-only.</span></span> <span data-ttu-id="dae79-138">Z kolei w przypadku zwykłych typów jednostek EF Core zakłada, że obiekt bazy danych określony w `ToTable` metodzie może być traktowany jako _tabela_, co oznacza, że może być używany jako źródło zapytania, ale również przeznaczony dla operacji Update, DELETE i Insert.</span><span class="sxs-lookup"><span data-stu-id="dae79-138">Conversely, for regular entity types, EF Core assumes that a database object specified in the `ToTable` method can be treated as a _table_, meaning that it can be used as a query source but also targeted by update, delete and insert operations.</span></span> <span data-ttu-id="dae79-139">W rzeczywistości możesz określić nazwę widoku bazy danych w programie `ToTable` i wszystko powinno działać prawidłowo, dopóki widok zostanie skonfigurowany jako aktualizowalny w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="dae79-139">In fact, you can specify the name of a database view in `ToTable` and everything should work fine as long as the view is configured to be updatable on the database.</span></span>

> [!NOTE]
> <span data-ttu-id="dae79-140">`ToView`przyjęto założenie, że obiekt już istnieje w bazie danych i nie zostanie utworzony przez migracje.</span><span class="sxs-lookup"><span data-stu-id="dae79-140">`ToView` assumes that the object already exists in the database and it won't be created by migrations.</span></span>

## <a name="example"></a><span data-ttu-id="dae79-141">Przykład</span><span class="sxs-lookup"><span data-stu-id="dae79-141">Example</span></span>

<span data-ttu-id="dae79-142">Poniższy przykład pokazuje, jak używać typów jednostek bez użycia do wykonywania zapytań w widoku bazy danych.</span><span class="sxs-lookup"><span data-stu-id="dae79-142">The following example shows how to use keyless entity types to query a database view.</span></span>

> [!TIP]
> <span data-ttu-id="dae79-143">[Przykład](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes) tego artykułu można wyświetlić w witrynie GitHub.</span><span class="sxs-lookup"><span data-stu-id="dae79-143">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes) on GitHub.</span></span>

<span data-ttu-id="dae79-144">Najpierw zdefiniujemy prosty blog i model post:</span><span class="sxs-lookup"><span data-stu-id="dae79-144">First, we define a simple Blog and Post model:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Entities)]

<span data-ttu-id="dae79-145">Następnie definiujemy prosty widok bazy danych, który umożliwi nam wykonywanie zapytań dotyczących liczby wpisów skojarzonych z każdym blogiem:</span><span class="sxs-lookup"><span data-stu-id="dae79-145">Next, we define a simple database view that will allow us to query the number of posts associated with each blog:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#View)]

<span data-ttu-id="dae79-146">Następnie zdefiniujemy klasę do przechowywania wyniku z widoku bazy danych:</span><span class="sxs-lookup"><span data-stu-id="dae79-146">Next, we define a class to hold the result from the database view:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#KeylessEntityType)]

<span data-ttu-id="dae79-147">Następnie skonfigurujemy typ jednostki "Less" w _OnModelCreating_ przy użyciu `HasNoKey` interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="dae79-147">Next, we configure the keyless entity type in _OnModelCreating_ using the `HasNoKey` API.</span></span>
<span data-ttu-id="dae79-148">Korzystamy z interfejsu API konfiguracji Fluent, aby skonfigurować mapowanie dla typu jednostki less:</span><span class="sxs-lookup"><span data-stu-id="dae79-148">We use fluent configuration API to configure the mapping for the keyless entity type:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Configuration)]

<span data-ttu-id="dae79-149">Następnie skonfigurujemy, `DbContext` aby uwzględnić `DbSet<T>`:</span><span class="sxs-lookup"><span data-stu-id="dae79-149">Next, we configure the `DbContext` to include the `DbSet<T>`:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#DbSet)]

<span data-ttu-id="dae79-150">Na koniec możemy zbadać widok bazy danych w standardowy sposób:</span><span class="sxs-lookup"><span data-stu-id="dae79-150">Finally, we can query the database view in the standard way:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Query)]

> [!TIP]
> <span data-ttu-id="dae79-151">Należy zauważyć, że zdefiniowano również właściwość zapytania poziomu kontekstu (Nieogólnymi) do działania jako element główny dla zapytań dla tego typu.</span><span class="sxs-lookup"><span data-stu-id="dae79-151">Note we have also defined a context level query property (DbSet) to act as a root for queries against this type.</span></span>
