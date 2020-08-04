---
title: Pola zapasowe — EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: a628795e-64df-4f24-a5e8-76bc261e7ed8
uid: core/modeling/backing-field
ms.openlocfilehash: e015c4f3fca767d25bee179c027813bd9fcf4c07
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526761"
---
# <a name="backing-fields"></a><span data-ttu-id="4833b-102">Pola zapasowe</span><span class="sxs-lookup"><span data-stu-id="4833b-102">Backing Fields</span></span>

<span data-ttu-id="4833b-103">Pola zapasowe umożliwiają EF odczyt i/lub zapis do pola, a nie właściwości.</span><span class="sxs-lookup"><span data-stu-id="4833b-103">Backing fields allow EF to read and/or write to a field rather than a property.</span></span> <span data-ttu-id="4833b-104">Może to być przydatne, gdy hermetyzacja w klasie jest używana do ograniczania użycia i/lub podniesienia semantyki dostępu do danych przez kod aplikacji, ale wartość powinna zostać odczytana i/lub zapisywana w bazie danych bez użycia tych ograniczeń/ulepszeń.</span><span class="sxs-lookup"><span data-stu-id="4833b-104">This can be useful when encapsulation in the class is being used to restrict the use of and/or enhance the semantics around access to the data by application code, but the value should be read from and/or written to the database without using those restrictions/enhancements.</span></span>

## <a name="basic-configuration"></a><span data-ttu-id="4833b-105">Konfiguracja podstawowa</span><span class="sxs-lookup"><span data-stu-id="4833b-105">Basic configuration</span></span>

<span data-ttu-id="4833b-106">Według Konwencji następujące pola zostaną odnalezione jako pola zapasowe dla danej właściwości (wymienione w kolejności pierwszeństwa).</span><span class="sxs-lookup"><span data-stu-id="4833b-106">By convention, the following fields will be discovered as backing fields for a given property (listed in precedence order).</span></span> 

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

<span data-ttu-id="4833b-107">W poniższym przykładzie `Url` Właściwość jest skonfigurowana tak, aby zawierała `_url` jako pole zapasowe:</span><span class="sxs-lookup"><span data-stu-id="4833b-107">In the following sample, the `Url` property is configured to have `_url` as its backing field:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

<span data-ttu-id="4833b-108">Należy zauważyć, że pola zapasowe są odnajdywane tylko dla właściwości, które są uwzględnione w modelu.</span><span class="sxs-lookup"><span data-stu-id="4833b-108">Note that backing fields are only discovered for properties that are included in the model.</span></span> <span data-ttu-id="4833b-109">Aby uzyskać więcej informacji na temat właściwości uwzględnionych w modelu, zobacz [m.in. & z wyłączeniem właściwości](included-properties.md).</span><span class="sxs-lookup"><span data-stu-id="4833b-109">For more information on which properties are included in the model, see [Including & Excluding Properties](included-properties.md).</span></span>

<span data-ttu-id="4833b-110">Możesz również skonfigurować pola do tworzenia kopii zapasowych za pomocą adnotacji danych (dostępnej w EFCore 5,0) lub interfejsu API Fluent, np. Jeśli nazwa pola nie odpowiada powyższym konwencjom:</span><span class="sxs-lookup"><span data-stu-id="4833b-110">You can also configure backing fields by using a Data Annotation (available in EFCore 5.0) or the Fluent API, e.g. if the field name doesn't correspond to the above conventions:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="4833b-111">Adnotacje danych</span><span class="sxs-lookup"><span data-stu-id="4833b-111">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/BackingField.cs?name=BackingField&highlight=7)]

### <a name="fluent-api"></a>[<span data-ttu-id="4833b-112">Interfejs API Fluent</span><span class="sxs-lookup"><span data-stu-id="4833b-112">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs?name=BackingField&highlight=5)]

## <a name="field-and-property-access"></a><span data-ttu-id="4833b-113">Dostęp do pól i właściwości</span><span class="sxs-lookup"><span data-stu-id="4833b-113">Field and property access</span></span>

<span data-ttu-id="4833b-114">Domyślnie EF zawsze odczytuje i zapisu do pola zapasowego — przy założeniu, że został on poprawnie skonfigurowany — i nigdy nie będzie używać właściwości.</span><span class="sxs-lookup"><span data-stu-id="4833b-114">By default, EF will always read and write to the backing field - assuming one has been properly configured - and will never use the property.</span></span> <span data-ttu-id="4833b-115">Jednakże EF obsługuje również inne wzorce dostępu.</span><span class="sxs-lookup"><span data-stu-id="4833b-115">However, EF also supports other access patterns.</span></span> <span data-ttu-id="4833b-116">Na przykład poniższy przykład instruuje EF do zapisu w polu zapasowym tylko wtedy, gdy materializacji, i do używania właściwości we wszystkich innych przypadkach:</span><span class="sxs-lookup"><span data-stu-id="4833b-116">For example, the following sample instructs EF to write to the backing field only while materializing, and to use the property in all other cases:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs?name=BackingFieldAccessMode&highlight=6)]

<span data-ttu-id="4833b-117">Aby uzyskać pełny zestaw obsługiwanych opcji, zobacz [Wyliczenie PropertyAccessMode](/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) .</span><span class="sxs-lookup"><span data-stu-id="4833b-117">See the [PropertyAccessMode enum](/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) for the complete set of supported options.</span></span>

> [!NOTE]
> <span data-ttu-id="4833b-118">W EF Core 3,0 domyślny tryb dostępu do właściwości został zmieniony z `PreferFieldDuringConstruction` na `PreferField` .</span><span class="sxs-lookup"><span data-stu-id="4833b-118">With EF Core 3.0, the default property access mode changed from `PreferFieldDuringConstruction` to `PreferField`.</span></span>

## <a name="field-only-properties"></a><span data-ttu-id="4833b-119">Właściwości tylko dla pól</span><span class="sxs-lookup"><span data-stu-id="4833b-119">Field-only properties</span></span>

<span data-ttu-id="4833b-120">Można również utworzyć w modelu Właściwość koncepcyjną, która nie ma odpowiedniej właściwości CLR w klasie Entity, ale zamiast tego używa pola do przechowywania danych w jednostce.</span><span class="sxs-lookup"><span data-stu-id="4833b-120">You can also create a conceptual property in your model that does not have a corresponding CLR property in the entity class, but instead uses a field to store the data in the entity.</span></span> <span data-ttu-id="4833b-121">Różni się to od [Właściwości cienia](shadow-properties.md), gdzie dane są przechowywane w monitorze zmian, a nie w typie CLR obiektu.</span><span class="sxs-lookup"><span data-stu-id="4833b-121">This is different from [Shadow Properties](shadow-properties.md), where the data is stored in the change tracker, rather than in the entity's CLR type.</span></span> <span data-ttu-id="4833b-122">Właściwości tylko dla pól są często używane, gdy Klasa jednostki używa metod zamiast właściwości do pobierania/ustawiania wartości lub w przypadkach, gdy pola nie powinny być ujawniane w modelu domeny (np. klucze podstawowe).</span><span class="sxs-lookup"><span data-stu-id="4833b-122">Field-only properties are commonly used when the entity class uses methods instead of properties to get/set values, or in cases where fields shouldn't be exposed at all in the domain model (e.g. primary keys).</span></span>

<span data-ttu-id="4833b-123">Można skonfigurować właściwość tylko do pola, podając nazwę w `Property(...)` interfejsie API:</span><span class="sxs-lookup"><span data-stu-id="4833b-123">You can configure a field-only property by providing a name in the `Property(...)` API:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

<span data-ttu-id="4833b-124">Dr podejmie próbę znalezienia właściwości CLR o podaną nazwę lub pola, jeśli nie można odnaleźć właściwości.</span><span class="sxs-lookup"><span data-stu-id="4833b-124">EF will attempt to find a CLR property with the given name, or a field if a property isn't found.</span></span> <span data-ttu-id="4833b-125">Jeśli nie zostanie znaleziona żadna właściwość ani pole, zamiast tego zostanie ustawiona właściwość Shadow.</span><span class="sxs-lookup"><span data-stu-id="4833b-125">If neither a property nor a field are found, a shadow property will be set up instead.</span></span>

<span data-ttu-id="4833b-126">Może być konieczne odwoływanie się do właściwości tylko pola z zapytań LINQ, ale takie pola są zwykle prywatne.</span><span class="sxs-lookup"><span data-stu-id="4833b-126">You may need to refer to a field-only property from LINQ queries, but such fields are typically private.</span></span> <span data-ttu-id="4833b-127">Możesz użyć `EF.Property(...)` metody w zapytaniu LINQ, aby odwołać się do pola:</span><span class="sxs-lookup"><span data-stu-id="4833b-127">You can use the `EF.Property(...)` method in a LINQ query to refer to the field:</span></span>

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "_validatedUrl"));
```
