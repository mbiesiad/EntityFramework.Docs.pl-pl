---
title: Typy jednostek posiadanych — EF Core
description: Jak skonfigurować własne typy jednostek lub agregacje podczas korzystania z Entity Framework Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/06/2019
uid: core/modeling/owned-entities
ms.openlocfilehash: 6ff98d005c0a868d420509571378756c56edc54a
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238128"
---
# <a name="owned-entity-types"></a><span data-ttu-id="5da82-103">Posiadane typy jednostek</span><span class="sxs-lookup"><span data-stu-id="5da82-103">Owned Entity Types</span></span>

<span data-ttu-id="5da82-104">EF Core umożliwia modelowanie typów jednostek, które mogą być wyświetlane tylko w przypadku właściwości nawigacji innych typów jednostek.</span><span class="sxs-lookup"><span data-stu-id="5da82-104">EF Core allows you to model entity types that can only ever appear on navigation properties of other entity types.</span></span> <span data-ttu-id="5da82-105">Są one nazywane _własnością typów jednostek_.</span><span class="sxs-lookup"><span data-stu-id="5da82-105">These are called _owned entity types_.</span></span> <span data-ttu-id="5da82-106">Jednostką zawierającą typ jednostki będącej właścicielem jest jej _właścicielem_.</span><span class="sxs-lookup"><span data-stu-id="5da82-106">The entity containing an owned entity type is its _owner_.</span></span>

<span data-ttu-id="5da82-107">Posiadane jednostki są zasadniczo częścią właściciela i nie mogą istnieć bez niej, są koncepcyjnie podobne do [agregacji](https://martinfowler.com/bliki/DDD_Aggregate.html).</span><span class="sxs-lookup"><span data-stu-id="5da82-107">Owned entities are essentially a part of the owner and cannot exist without it, they are conceptually similar to [aggregates](https://martinfowler.com/bliki/DDD_Aggregate.html).</span></span> <span data-ttu-id="5da82-108">Oznacza to, że właścicielem jednostki jest definicja po stronie zależnej relacji z właścicielem.</span><span class="sxs-lookup"><span data-stu-id="5da82-108">This means that the owned entity is by definition on the dependent side of the relationship with the owner.</span></span>

## <a name="explicit-configuration"></a><span data-ttu-id="5da82-109">Konfiguracja jawna</span><span class="sxs-lookup"><span data-stu-id="5da82-109">Explicit configuration</span></span>

<span data-ttu-id="5da82-110">Typy jednostek będących własnością nie są nigdy uwzględniane przez EF Core w modelu według Konwencji.</span><span class="sxs-lookup"><span data-stu-id="5da82-110">Owned entity types are never included by EF Core in the model by convention.</span></span> <span data-ttu-id="5da82-111">Możesz użyć `OwnsOne` metody w `OnModelCreating` lub dodać adnotację do typu z `OwnedAttribute` (nowość w EF Core 2,1), aby skonfigurować typ jako typ będący własnością.</span><span class="sxs-lookup"><span data-stu-id="5da82-111">You can use the `OwnsOne` method in `OnModelCreating` or annotate the type with `OwnedAttribute` (new in EF Core 2.1) to configure the type as an owned type.</span></span>

<span data-ttu-id="5da82-112">W tym przykładzie `StreetAddress` jest typem bez właściwości Identity.</span><span class="sxs-lookup"><span data-stu-id="5da82-112">In this example, `StreetAddress` is a type with no identity property.</span></span> <span data-ttu-id="5da82-113">Jest ona używana jako właściwość typu zamówienia, aby określić adres wysyłkowy dla konkretnej kolejności.</span><span class="sxs-lookup"><span data-stu-id="5da82-113">It is used as a property of the Order type to specify the shipping address for a particular order.</span></span>

<span data-ttu-id="5da82-114">Możemy użyć, `OwnedAttribute` Aby traktować go jako jednostkę będącą własnością, gdy występuje odwołanie z innego typu jednostki:</span><span class="sxs-lookup"><span data-stu-id="5da82-114">We can use the `OwnedAttribute` to treat it as an owned entity when referenced from another entity type:</span></span>

[!code-csharp[StreetAddress](../../../samples/core/Modeling/OwnedEntities/StreetAddress.cs?name=StreetAddress)]

[!code-csharp[Order](../../../samples/core/Modeling/OwnedEntities/Order.cs?name=Order)]

<span data-ttu-id="5da82-115">Istnieje również możliwość użycia `OwnsOne` metody w, `OnModelCreating` Aby określić, że `ShippingAddress` Właściwość jest jednostką będącą własnością `Order` typu jednostki i aby skonfigurować dodatkowe aspekty w razie potrzeby.</span><span class="sxs-lookup"><span data-stu-id="5da82-115">It is also possible to use the `OwnsOne` method in `OnModelCreating` to specify that the `ShippingAddress` property is an Owned Entity of the `Order` entity type and to configure additional facets if needed.</span></span>

[!code-csharp[OwnsOne](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOne)]

<span data-ttu-id="5da82-116">Jeśli `ShippingAddress` Właściwość jest prywatna w `Order` typie, można użyć wersji ciągu `OwnsOne` metody:</span><span class="sxs-lookup"><span data-stu-id="5da82-116">If the `ShippingAddress` property is private in the `Order` type, you can use the string version of the `OwnsOne` method:</span></span>

[!code-csharp[OwnsOneString](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneString)]

<span data-ttu-id="5da82-117">Zobacz [pełny przykładowy projekt](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities) , aby uzyskać więcej kontekstu.</span><span class="sxs-lookup"><span data-stu-id="5da82-117">See the [full sample project](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities) for more context.</span></span>

## <a name="implicit-keys"></a><span data-ttu-id="5da82-118">Klucze niejawne</span><span class="sxs-lookup"><span data-stu-id="5da82-118">Implicit keys</span></span>

<span data-ttu-id="5da82-119">Typy własnością skonfigurowane z `OwnsOne` lub odnalezione za pomocą nawigacji odwołania zawsze mają relację jeden do jednego z właścicielem, dlatego nie potrzebują własnych wartości klucza, ponieważ wartości klucza obcego są unikatowe.</span><span class="sxs-lookup"><span data-stu-id="5da82-119">Owned types configured with `OwnsOne` or discovered through a reference navigation always have a one-to-one relationship with the owner, therefore they don't need their own key values as the foreign key values are unique.</span></span> <span data-ttu-id="5da82-120">W poprzednim przykładzie `StreetAddress` Typ nie musi definiować właściwości klucza.</span><span class="sxs-lookup"><span data-stu-id="5da82-120">In the previous example, the `StreetAddress` type does not need to define a key property.</span></span>  

<span data-ttu-id="5da82-121">Aby zrozumieć, w jaki sposób EF Core śledzi te obiekty, warto wiedzieć, że klucz podstawowy został utworzony jako [Właściwość Shadow](xref:core/modeling/shadow-properties) dla typu będącego własnością.</span><span class="sxs-lookup"><span data-stu-id="5da82-121">In order to understand how EF Core tracks these objects, it is useful to know that a primary key is created as a [shadow property](xref:core/modeling/shadow-properties) for the owned type.</span></span> <span data-ttu-id="5da82-122">Wartość klucza wystąpienia typu będącego właścicielem będzie taka sama, jak wartość klucza wystąpienia właściciela.</span><span class="sxs-lookup"><span data-stu-id="5da82-122">The value of the key of an instance of the owned type will be the same as the value of the key of the owner instance.</span></span>

## <a name="collections-of-owned-types"></a><span data-ttu-id="5da82-123">Kolekcje typów posiadanych</span><span class="sxs-lookup"><span data-stu-id="5da82-123">Collections of owned types</span></span>

> [!NOTE]
> <span data-ttu-id="5da82-124">Ta funkcja jest nowa w EF Core 2,2.</span><span class="sxs-lookup"><span data-stu-id="5da82-124">This feature is new in EF Core 2.2.</span></span>

<span data-ttu-id="5da82-125">Aby skonfigurować kolekcję typów będących własnością, użyj `OwnsMany` w `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="5da82-125">To configure a collection of owned types use `OwnsMany` in `OnModelCreating`.</span></span>

<span data-ttu-id="5da82-126">Typy należące do muszą być kluczem podstawowym.</span><span class="sxs-lookup"><span data-stu-id="5da82-126">Owned types need a primary key.</span></span> <span data-ttu-id="5da82-127">Jeśli nie ma żadnych właściwości dobrych kandydatów dla typu .NET, EF Core może próbować utworzyć jeden.</span><span class="sxs-lookup"><span data-stu-id="5da82-127">If there are no good candidates properties on the .NET type, EF Core can try to create one.</span></span> <span data-ttu-id="5da82-128">Jednak jeśli typy posiadane są zdefiniowane za pomocą kolekcji, nie wystarczy tylko utworzyć właściwość Shadow do działania jako klucz obcy w właścicielu i kluczu podstawowym danego wystąpienia, jak w przypadku `OwnsOne` : może istnieć wiele wystąpień typu dla każdego właściciela, a tym samym klucz właściciela nie jest wystarczający do zapewnienia unikatowej tożsamości dla każdego należącego wystąpienia.</span><span class="sxs-lookup"><span data-stu-id="5da82-128">However, when owned types are defined through a collection, it isn't enough to just create a shadow property to act as both the foreign key into the owner and the primary key of the owned instance, as we do for `OwnsOne`: there can be multiple owned type instances for each owner, and hence the key of the owner isn't enough to provide a unique identity for each owned instance.</span></span>

<span data-ttu-id="5da82-129">Poniżej przedstawiono dwa najbardziej proste rozwiązania tego problemu:</span><span class="sxs-lookup"><span data-stu-id="5da82-129">The two most straightforward solutions to this are:</span></span>

- <span data-ttu-id="5da82-130">Definiowanie wieloskładnikowego klucza podstawowego na nowej właściwości niezależnie od klucza obcego, który wskazuje na właściciela.</span><span class="sxs-lookup"><span data-stu-id="5da82-130">Defining a surrogate primary key on a new property independent of the foreign key that points to the owner.</span></span> <span data-ttu-id="5da82-131">Zawarte wartości muszą być unikatowe dla wszystkich właścicieli (na przykład jeśli element nadrzędny {1} ma element podrzędny {1} , a element nadrzędny {2} nie może mieć elementu podrzędnego {1} ), więc wartość nie ma znaczenia.</span><span class="sxs-lookup"><span data-stu-id="5da82-131">The contained values would need to be unique across all owners (e.g. if Parent {1} has Child {1}, then Parent {2} cannot have Child {1}), so the value doesn't have any inherent meaning.</span></span> <span data-ttu-id="5da82-132">Ponieważ klucz obcy nie jest częścią klucza podstawowego, jego wartości mogą być zmieniane, więc można przenieść element podrzędny z jednego elementu nadrzędnego do innego, ale zazwyczaj jest on zwykle związany z semantyką agregowania.</span><span class="sxs-lookup"><span data-stu-id="5da82-132">Since the foreign key is not part of the primary key its values can be changed, so you could move a child from one parent to another one, however this usually goes against aggregate semantics.</span></span>
- <span data-ttu-id="5da82-133">Użycie klucza obcego i dodatkowej właściwości jako klucza złożonego.</span><span class="sxs-lookup"><span data-stu-id="5da82-133">Using the foreign key and an additional property as a composite key.</span></span> <span data-ttu-id="5da82-134">Dodatkowa wartość właściwości musi teraz być unikatowa dla danego elementu nadrzędnego (Jeśli element nadrzędny {1} ma element podrzędny, {1,1} {2} nadal może mieć element podrzędny {2,1} ).</span><span class="sxs-lookup"><span data-stu-id="5da82-134">The additional property value now only needs to be unique for a given parent (so if Parent {1} has Child {1,1} then Parent {2} can still have Child {2,1}).</span></span> <span data-ttu-id="5da82-135">Dzięki wprowadzeniu części klucza obcego w kluczu podstawowym relacja między właścicielem a jednostką będącą własnością jest niezmienna i odzwierciedla lepszą semantykę.</span><span class="sxs-lookup"><span data-stu-id="5da82-135">By making the foreign key part of the primary key the relationship between the owner and the owned entity becomes immutable and reflects aggregate semantics better.</span></span> <span data-ttu-id="5da82-136">Jest to EF Core domyślnie.</span><span class="sxs-lookup"><span data-stu-id="5da82-136">This is what EF Core does by default.</span></span>

<span data-ttu-id="5da82-137">W tym przykładzie użyjemy `Distributor` klasy:</span><span class="sxs-lookup"><span data-stu-id="5da82-137">In this example we'll use the `Distributor` class:</span></span>

[!code-csharp[Distributor](../../../samples/core/Modeling/OwnedEntities/Distributor.cs?name=Distributor)]

<span data-ttu-id="5da82-138">Domyślnie klucz podstawowy używany dla typu, do którego odwołuje się `ShippingCenters` Właściwość nawigacji, będzie miał `("DistributorId", "Id")` `"DistributorId"` , gdzie jest kluczem klucza obcego i `"Id"` jest `int` wartością unikatową.</span><span class="sxs-lookup"><span data-stu-id="5da82-138">By default the primary key used for the owned type referenced through the `ShippingCenters` navigation property will be `("DistributorId", "Id")` where `"DistributorId"` is the FK and `"Id"` is a unique `int` value.</span></span>

<span data-ttu-id="5da82-139">Aby skonfigurować inne wywołanie klucza podstawowego `HasKey` :</span><span class="sxs-lookup"><span data-stu-id="5da82-139">To configure a different PK call `HasKey`:</span></span>

[!code-csharp[OwnsMany](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsMany)]

> [!NOTE]
> <span data-ttu-id="5da82-140">Przed EF Core 3,0 `WithOwner()` Metoda nie istnieje, więc należy usunąć to wywołanie.</span><span class="sxs-lookup"><span data-stu-id="5da82-140">Before EF Core 3.0 `WithOwner()` method didn't exist so this call should be removed.</span></span> <span data-ttu-id="5da82-141">Ponadto klucz podstawowy nie został odnaleziony automatycznie, więc zawsze musiał być określony.</span><span class="sxs-lookup"><span data-stu-id="5da82-141">Also the primary key was not discovered automatically so it always had to be specified.</span></span>

## <a name="mapping-owned-types-with-table-splitting"></a><span data-ttu-id="5da82-142">Mapowanie typów posiadanych przy użyciu dzielenia tabeli</span><span class="sxs-lookup"><span data-stu-id="5da82-142">Mapping owned types with table splitting</span></span>

<span data-ttu-id="5da82-143">W przypadku korzystania z relacyjnych baz danych przez domyślne typy odwołań są mapowane na tę samą tabelę co właściciel.</span><span class="sxs-lookup"><span data-stu-id="5da82-143">When using relational databases, by default reference owned types are mapped to the same table as the owner.</span></span> <span data-ttu-id="5da82-144">Wymaga to podziału tabeli w dwóch: niektóre kolumny będą używane do przechowywania danych właściciela, a niektóre kolumny będą używane do przechowywania danych jednostki będącej własnością.</span><span class="sxs-lookup"><span data-stu-id="5da82-144">This requires splitting the table in two: some columns will be used to store the data of the owner, and some columns will be used to store data of the owned entity.</span></span> <span data-ttu-id="5da82-145">Jest to typowa funkcja znana jako [podział tabeli](table-splitting.md).</span><span class="sxs-lookup"><span data-stu-id="5da82-145">This is a common feature known as [table splitting](table-splitting.md).</span></span>

<span data-ttu-id="5da82-146">Domyślnie EF Core nazwy kolumn bazy danych dla właściwości typu jednostki będącej własnością, po _Navigation_OwnedEntityProperty_wzorca.</span><span class="sxs-lookup"><span data-stu-id="5da82-146">By default, EF Core will name the database columns for the properties of the owned entity type following the pattern _Navigation_OwnedEntityProperty_.</span></span> <span data-ttu-id="5da82-147">W związku z tym `StreetAddress` właściwości będą wyświetlane w tabeli "Orders" z nazwami "ShippingAddress_Street" i "ShippingAddress_City".</span><span class="sxs-lookup"><span data-stu-id="5da82-147">Therefore the `StreetAddress` properties will appear in the 'Orders' table with the names 'ShippingAddress_Street' and 'ShippingAddress_City'.</span></span>

<span data-ttu-id="5da82-148">Możesz użyć metody, `HasColumnName` Aby zmienić nazwy tych kolumn:</span><span class="sxs-lookup"><span data-stu-id="5da82-148">You can use the `HasColumnName` method to rename those columns:</span></span>

[!code-csharp[ColumnNames](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=ColumnNames)]

> [!NOTE]
> <span data-ttu-id="5da82-149">Większość normalnych metod konfiguracji typu jednostki, takich jak [Ignore](/dotnet/api/microsoft.entityframeworkcore.metadata.builders.ownednavigationbuilder.ignore) , można wywołać w taki sam sposób.</span><span class="sxs-lookup"><span data-stu-id="5da82-149">Most of the normal entity type configuration methods like [Ignore](/dotnet/api/microsoft.entityframeworkcore.metadata.builders.ownednavigationbuilder.ignore) can be called in the same way.</span></span>

## <a name="sharing-the-same-net-type-among-multiple-owned-types"></a><span data-ttu-id="5da82-150">Współużytkowanie tego samego typu .NET w wielu typach posiadanych</span><span class="sxs-lookup"><span data-stu-id="5da82-150">Sharing the same .NET type among multiple owned types</span></span>

<span data-ttu-id="5da82-151">Typ jednostki będącej właścicielem może być tego samego typu .NET, co inny typ jednostki będącej własnością, dlatego typ .NET może być niewystarczający do zidentyfikowania typu będącego własnością.</span><span class="sxs-lookup"><span data-stu-id="5da82-151">An owned entity type can be of the same .NET type as another owned entity type, therefore the .NET type may not be enough to identify an owned type.</span></span>

<span data-ttu-id="5da82-152">W takich przypadkach Właściwość wskazująca, że obiekt będący właścicielem, jest _określany jako Nawigacja_ typu jednostki będącej własnością.</span><span class="sxs-lookup"><span data-stu-id="5da82-152">In those cases, the property pointing from the owner to the owned entity becomes the _defining navigation_ of the owned entity type.</span></span> <span data-ttu-id="5da82-153">Z perspektywy EF Core, Definiowanie nawigacji jest częścią tożsamości typu obok typu .NET.</span><span class="sxs-lookup"><span data-stu-id="5da82-153">From the perspective of EF Core, the defining navigation is part of the type's identity alongside the .NET type.</span></span>

<span data-ttu-id="5da82-154">Na przykład w poniższej klasie `ShippingAddress` i `BillingAddress` są oba te same typy .NET `StreetAddress` :</span><span class="sxs-lookup"><span data-stu-id="5da82-154">For example, in the following class `ShippingAddress` and `BillingAddress` are both of the same .NET type, `StreetAddress`:</span></span>

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

<span data-ttu-id="5da82-155">Aby zrozumieć, w jaki sposób EF Core będzie odróżniać śledzone wystąpienia tych obiektów, warto zastanowić się, że definiująca Nawigacja stanie się częścią klucza wystąpienia obok wartości klucza właściciela i typu .NET należącego do typu.</span><span class="sxs-lookup"><span data-stu-id="5da82-155">In order to understand how EF Core will distinguish tracked instances of these objects, it may be useful to think that the defining navigation has become part of the key of the instance alongside the value of the key of the owner and the .NET type of the owned type.</span></span>

## <a name="nested-owned-types"></a><span data-ttu-id="5da82-156">Zagnieżdżone typy należące</span><span class="sxs-lookup"><span data-stu-id="5da82-156">Nested owned types</span></span>

<span data-ttu-id="5da82-157">W tym przykładzie `OrderDetails` są to elementy należące `BillingAddress` do `ShippingAddress` obu `StreetAddress` typów.</span><span class="sxs-lookup"><span data-stu-id="5da82-157">In this example `OrderDetails` owns `BillingAddress` and `ShippingAddress`, which are both `StreetAddress` types.</span></span> <span data-ttu-id="5da82-158">Następnie należy do `OrderDetails` `DetailedOrder` typu.</span><span class="sxs-lookup"><span data-stu-id="5da82-158">Then `OrderDetails` is owned by the `DetailedOrder` type.</span></span>

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/OwnedEntities/DetailedOrder.cs?name=DetailedOrder)]

[!code-csharp[OrderStatus](../../../samples/core/Modeling/OwnedEntities/OrderStatus.cs?name=OrderStatus)]

<span data-ttu-id="5da82-159">Każda Nawigacja do typu posiadanego definiuje osobny typ jednostki z całkowicie niezależną konfiguracją.</span><span class="sxs-lookup"><span data-stu-id="5da82-159">Each navigation to an owned type defines a separate entity type with completely independent configuration.</span></span>

<span data-ttu-id="5da82-160">Oprócz zagnieżdżonych typów będących własnością, typ własnością może odwoływać się do zwykłej jednostki, która może być właścicielem lub inną jednostką, o ile właścicielem jednostki znajduje się po stronie zależnej.</span><span class="sxs-lookup"><span data-stu-id="5da82-160">In addition to nested owned types, an owned type can reference a regular entity which can be either the owner or a different entity as long as the owned entity is on the dependent side.</span></span> <span data-ttu-id="5da82-161">Ta funkcja umożliwia określenie typów jednostek posiadanych poza typami złożonymi w EF6.</span><span class="sxs-lookup"><span data-stu-id="5da82-161">This capability sets owned entity types apart from complex types in EF6.</span></span>

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

## <a name="configuring-owned-types"></a><span data-ttu-id="5da82-162">Konfigurowanie typów posiadanych</span><span class="sxs-lookup"><span data-stu-id="5da82-162">Configuring Owned Types</span></span>

<span data-ttu-id="5da82-163">Istnieje możliwość łańcucha `OwnsOne` metody w wywołaniu Fluent, aby skonfigurować ten model:</span><span class="sxs-lookup"><span data-stu-id="5da82-163">It is possible to chain the `OwnsOne` method in a fluent call to configure this model:</span></span>

[!code-csharp[OwnsOneNested](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneNested)]

<span data-ttu-id="5da82-164">Zwróć uwagę na `WithOwner` wywołanie użyte do zdefiniowania właściwości nawigacji wskazującej na właściciela.</span><span class="sxs-lookup"><span data-stu-id="5da82-164">Notice the `WithOwner` call used to define the navigation property pointing back at the owner.</span></span> <span data-ttu-id="5da82-165">Aby zdefiniować nawigację do typu jednostki właściciela, która nie jest częścią relacji własności, `WithOwner()` należy wywołać bez żadnych argumentów.</span><span class="sxs-lookup"><span data-stu-id="5da82-165">To define a navigation to the owner entity type that's not part of the ownership relationship `WithOwner()` should be called without any arguments.</span></span>

<span data-ttu-id="5da82-166">Można również osiągnąć ten wynik przy użyciu zarówno programu `OwnedAttribute` `OrderDetails` , jak i `StreetAddress` .</span><span class="sxs-lookup"><span data-stu-id="5da82-166">It is also possible to achieve this result using `OwnedAttribute` on both `OrderDetails` and `StreetAddress`.</span></span>

<span data-ttu-id="5da82-167">Ponadto Zwróć uwagę na `Navigation` wywołanie.</span><span class="sxs-lookup"><span data-stu-id="5da82-167">In addition, notice the `Navigation` call.</span></span> <span data-ttu-id="5da82-168">W EFCore 5,0, właściwości nawigacji do typów posiadanych można dodatkowo skonfigurować [jako dla właściwości nawigacji niebędącej własnością](relationships.md#configuring-navigation-properties).</span><span class="sxs-lookup"><span data-stu-id="5da82-168">In EFCore 5.0, navigation properties to owned types can be further configured [as for non-owned navigation properties](relationships.md#configuring-navigation-properties).</span></span>

## <a name="storing-owned-types-in-separate-tables"></a><span data-ttu-id="5da82-169">Przechowywanie typów posiadanych w oddzielnych tabelach</span><span class="sxs-lookup"><span data-stu-id="5da82-169">Storing owned types in separate tables</span></span>

<span data-ttu-id="5da82-170">Również w przeciwieństwie do EF6 typów złożonych, typy własnością mogą być przechowywane w oddzielnej tabeli od właściciela.</span><span class="sxs-lookup"><span data-stu-id="5da82-170">Also unlike EF6 complex types, owned types can be stored in a separate table from the owner.</span></span> <span data-ttu-id="5da82-171">W celu zastąpienia Konwencji, która mapuje typ należący do tej samej tabeli co właściciel, można po prostu wywołać `ToTable` i podać inną nazwę tabeli.</span><span class="sxs-lookup"><span data-stu-id="5da82-171">In order to override the convention that maps an owned type to the same table as the owner, you can simply call `ToTable` and provide a different table name.</span></span> <span data-ttu-id="5da82-172">Poniższy przykład mapuje `OrderDetails` i jego dwa adresy na osobną tabelę z `DetailedOrder` :</span><span class="sxs-lookup"><span data-stu-id="5da82-172">The following example will map `OrderDetails` and its two addresses to a separate table from `DetailedOrder`:</span></span>

[!code-csharp[OwnsOneTable](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneTable)]

<span data-ttu-id="5da82-173">Można również użyć `TableAttribute` do tego celu, ale należy zauważyć, że może to się nie powieść, jeśli istnieje wiele nawigacji do tego typu, ponieważ w takim przypadku wiele typów jednostek będzie mapowanych do tej samej tabeli.</span><span class="sxs-lookup"><span data-stu-id="5da82-173">It is also possible to use the `TableAttribute` to accomplish this, but note that this would fail if there are multiple navigations to the owned type since in that case multiple entity types would be mapped to the same table.</span></span>

## <a name="querying-owned-types"></a><span data-ttu-id="5da82-174">Wykonywanie zapytania dotyczącego typów posiadanych</span><span class="sxs-lookup"><span data-stu-id="5da82-174">Querying owned types</span></span>

<span data-ttu-id="5da82-175">Podczas wykonywania zapytania dotyczącego właściciela typy będą uwzględniane domyślnie.</span><span class="sxs-lookup"><span data-stu-id="5da82-175">When querying the owner the owned types will be included by default.</span></span> <span data-ttu-id="5da82-176">Nie jest konieczne używanie `Include` metody, nawet jeśli typy posiadane są przechowywane w oddzielnej tabeli.</span><span class="sxs-lookup"><span data-stu-id="5da82-176">It is not necessary to use the `Include` method, even if the owned types are stored in a separate table.</span></span> <span data-ttu-id="5da82-177">W oparciu o model opisany przed, otrzymasz następujące zapytanie `Order` `OrderDetails` i dwa należące do `StreetAddresses` bazy danych:</span><span class="sxs-lookup"><span data-stu-id="5da82-177">Based on the model described before, the following query will get `Order`, `OrderDetails` and the two owned `StreetAddresses` from the database:</span></span>

[!code-csharp[DetailedOrderQuery](../../../samples/core/Modeling/OwnedEntities/Program.cs?name=DetailedOrderQuery)]

## <a name="limitations"></a><span data-ttu-id="5da82-178">Ograniczenia</span><span class="sxs-lookup"><span data-stu-id="5da82-178">Limitations</span></span>

<span data-ttu-id="5da82-179">Niektóre z tych ograniczeń mają wpływ na sposób działania typów jednostek, ale niektóre inne są ograniczeniami, które możemy usunąć w przyszłych wersjach:</span><span class="sxs-lookup"><span data-stu-id="5da82-179">Some of these limitations are fundamental to how owned entity types work, but some others are restrictions that we may be able to remove in future releases:</span></span>

### <a name="by-design-restrictions"></a><span data-ttu-id="5da82-180">Ograniczenia przez projektowanie</span><span class="sxs-lookup"><span data-stu-id="5da82-180">By-design restrictions</span></span>

- <span data-ttu-id="5da82-181">Nie można utworzyć `DbSet<T>` dla typu będącego właścicielem</span><span class="sxs-lookup"><span data-stu-id="5da82-181">You cannot create a `DbSet<T>` for an owned type</span></span>
- <span data-ttu-id="5da82-182">Nie można wywołać `Entity<T>()` z typem będącym własnością`ModelBuilder`</span><span class="sxs-lookup"><span data-stu-id="5da82-182">You cannot call `Entity<T>()` with an owned type on `ModelBuilder`</span></span>

### <a name="current-shortcomings"></a><span data-ttu-id="5da82-183">Bieżące nieprawidłowości</span><span class="sxs-lookup"><span data-stu-id="5da82-183">Current shortcomings</span></span>

- <span data-ttu-id="5da82-184">Typy jednostek będących własnością nie mogą mieć hierarchii dziedziczenia</span><span class="sxs-lookup"><span data-stu-id="5da82-184">Owned entity types cannot have inheritance hierarchies</span></span>
- <span data-ttu-id="5da82-185">Nawigacja referencyjna do typów jednostek będących własnością nie może mieć wartości null, chyba że są jawnie zamapowane na osobną tabelę od właściciela</span><span class="sxs-lookup"><span data-stu-id="5da82-185">Reference navigations to owned entity types cannot be null unless they are explicitly mapped to a separate table from the owner</span></span>
- <span data-ttu-id="5da82-186">Wystąpienia typów jednostek będących własnością nie mogą być współużytkowane przez wielu właścicieli (jest to dobrze znany scenariusz dla obiektów wartości, których nie można zaimplementować przy użyciu posiadanych typów jednostek)</span><span class="sxs-lookup"><span data-stu-id="5da82-186">Instances of owned entity types cannot be shared by multiple owners (this is a well-known scenario for value objects that cannot be implemented using owned entity types)</span></span>

### <a name="shortcomings-in-previous-versions"></a><span data-ttu-id="5da82-187">Braki w poprzednich wersjach</span><span class="sxs-lookup"><span data-stu-id="5da82-187">Shortcomings in previous versions</span></span>

- <span data-ttu-id="5da82-188">W EF Core 2,0 elementy nawigacyjne należące do typów jednostek posiadanych nie mogą być deklarowane w pochodnych typach jednostek, chyba że należące do niej jednostki są jawnie mapowane do oddzielnej tabeli z hierarchii właściciela.</span><span class="sxs-lookup"><span data-stu-id="5da82-188">In EF Core 2.0, navigations to owned entity types cannot be declared in derived entity types unless the owned entities are explicitly mapped to a separate table from the owner hierarchy.</span></span> <span data-ttu-id="5da82-189">To ograniczenie zostało usunięte w EF Core 2,1</span><span class="sxs-lookup"><span data-stu-id="5da82-189">This limitation has been removed in EF Core 2.1</span></span>
- <span data-ttu-id="5da82-190">W EF Core 2,0 i 2,1 obsługiwane są tylko nawigacji do typów będących własnością.</span><span class="sxs-lookup"><span data-stu-id="5da82-190">In EF Core 2.0 and 2.1 only reference navigations to owned types were supported.</span></span> <span data-ttu-id="5da82-191">To ograniczenie zostało usunięte w EF Core 2,2</span><span class="sxs-lookup"><span data-stu-id="5da82-191">This limitation has been removed in EF Core 2.2</span></span>
