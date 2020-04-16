---
title: Pola zapasowe — EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: a628795e-64df-4f24-a5e8-76bc261e7ed8
uid: core/modeling/backing-field
ms.openlocfilehash: d6faf7c0a07c7b855c1ce402ae29e19c80b9d3b4
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434178"
---
# <a name="backing-fields"></a><span data-ttu-id="4171f-102">Pola zapasowe</span><span class="sxs-lookup"><span data-stu-id="4171f-102">Backing Fields</span></span>

<span data-ttu-id="4171f-103">Pola zapasowe umożliwiają EF odczyt i/lub zapis w polu, a nie we właściwości.</span><span class="sxs-lookup"><span data-stu-id="4171f-103">Backing fields allow EF to read and/or write to a field rather than a property.</span></span> <span data-ttu-id="4171f-104">Może to być przydatne, gdy hermetyzacja w klasie jest używana do ograniczania użycia i/lub zwiększenia semantyki wokół dostępu do danych przez kod aplikacji, ale wartość powinna być odczytywana z i/lub zapisywana w bazie danych bez użycia tych ograniczeń/ulepszeń.</span><span class="sxs-lookup"><span data-stu-id="4171f-104">This can be useful when encapsulation in the class is being used to restrict the use of and/or enhance the semantics around access to the data by application code, but the value should be read from and/or written to the database without using those restrictions/enhancements.</span></span>

## <a name="basic-configuration"></a><span data-ttu-id="4171f-105">Konfiguracja podstawowa</span><span class="sxs-lookup"><span data-stu-id="4171f-105">Basic configuration</span></span>

<span data-ttu-id="4171f-106">Zgodnie z konwencją następujące pola zostaną odnalezione jako pola zapasowe dla danej właściwości (wymienione w kolejności pierwszeństwa).</span><span class="sxs-lookup"><span data-stu-id="4171f-106">By convention, the following fields will be discovered as backing fields for a given property (listed in precedence order).</span></span> 

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

<span data-ttu-id="4171f-107">W poniższym przykładzie `Url` właściwość jest `_url` skonfigurowana jako jego pole podkładowe:</span><span class="sxs-lookup"><span data-stu-id="4171f-107">In the following sample, the `Url` property is configured to have `_url` as its backing field:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

<span data-ttu-id="4171f-108">Należy zauważyć, że pola zapasowe są odnajdywki tylko dla właściwości, które są zawarte w modelu.</span><span class="sxs-lookup"><span data-stu-id="4171f-108">Note that backing fields are only discovered for properties that are included in the model.</span></span> <span data-ttu-id="4171f-109">Aby uzyskać więcej informacji na temat właściwości, które są uwzględnione w modelu, zobacz [Uwzględnianie & Wykluczanie właściwości](included-properties.md).</span><span class="sxs-lookup"><span data-stu-id="4171f-109">For more information on which properties are included in the model, see [Including & Excluding Properties](included-properties.md).</span></span>

<span data-ttu-id="4171f-110">Można również skonfigurować pola kopii zapasowej przy użyciu adnotacji danych lub interfejsu API Fluent, na przykład jeśli nazwa pola nie odpowiada powyższym konwencjom:</span><span class="sxs-lookup"><span data-stu-id="4171f-110">You can also configure backing fields by using a Data Annotation or the Fluent API, e.g. if the field name doesn't correspond to the above conventions:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="4171f-111">Adnotacje danych</span><span class="sxs-lookup"><span data-stu-id="4171f-111">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/BackingField.cs?name=BackingField&highlight=7)]

### <a name="fluent-api"></a>[<span data-ttu-id="4171f-112">Płynne API</span><span class="sxs-lookup"><span data-stu-id="4171f-112">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs?name=BackingField&highlight=5)]

## <a name="field-and-property-access"></a><span data-ttu-id="4171f-113">Dostęp do pól i właściwości</span><span class="sxs-lookup"><span data-stu-id="4171f-113">Field and property access</span></span>

<span data-ttu-id="4171f-114">Domyślnie EF zawsze będzie odczytywać i zapisywać w polu zapasowym — przy założeniu, że jeden został poprawnie skonfigurowany — i nigdy nie będzie używać właściwości.</span><span class="sxs-lookup"><span data-stu-id="4171f-114">By default, EF will always read and write to the backing field - assuming one has been properly configured - and will never use the property.</span></span> <span data-ttu-id="4171f-115">Jednak EF obsługuje również inne wzorce dostępu.</span><span class="sxs-lookup"><span data-stu-id="4171f-115">However, EF also supports other access patterns.</span></span> <span data-ttu-id="4171f-116">Na przykład poniższy przykład nakazuje EF zapisywać w polu zapasowym tylko podczas materializacji i używać właściwości we wszystkich innych przypadkach:</span><span class="sxs-lookup"><span data-stu-id="4171f-116">For example, the following sample instructs EF to write to the backing field only while materializing, and to use the property in all other cases:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs?name=BackingFieldAccessMode&highlight=6)]

<span data-ttu-id="4171f-117">Zobacz [PropertyAccessMode wyliczenia](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) dla pełnego zestawu obsługiwanych opcji.</span><span class="sxs-lookup"><span data-stu-id="4171f-117">See the [PropertyAccessMode enum](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) for the complete set of supported options.</span></span>

> [!NOTE]
> <span data-ttu-id="4171f-118">W ef core 3.0 domyślny tryb `PreferFieldDuringConstruction` `PreferField`dostępu do właściwości zmienił się z na .</span><span class="sxs-lookup"><span data-stu-id="4171f-118">With EF Core 3.0, the default property access mode changed from `PreferFieldDuringConstruction` to `PreferField`.</span></span>

## <a name="field-only-properties"></a><span data-ttu-id="4171f-119">Właściwości tylko w polu</span><span class="sxs-lookup"><span data-stu-id="4171f-119">Field-only properties</span></span>

<span data-ttu-id="4171f-120">Można również utworzyć właściwości koncepcyjnej w modelu, który nie ma odpowiedniej właściwości CLR w klasie jednostki, ale zamiast tego używa pola do przechowywania danych w jednostce.</span><span class="sxs-lookup"><span data-stu-id="4171f-120">You can also create a conceptual property in your model that does not have a corresponding CLR property in the entity class, but instead uses a field to store the data in the entity.</span></span> <span data-ttu-id="4171f-121">Różni się to od [właściwości cienia](shadow-properties.md), gdzie dane są przechowywane w monitorze zmian, a nie w typie CLR jednostki.</span><span class="sxs-lookup"><span data-stu-id="4171f-121">This is different from [Shadow Properties](shadow-properties.md), where the data is stored in the change tracker, rather than in the entity's CLR type.</span></span> <span data-ttu-id="4171f-122">Właściwości tylko pola są często używane, gdy klasa jednostki używa metod zamiast właściwości, aby uzyskać/ustawić wartości lub w przypadkach, gdy pola nie powinny być widoczne w ogóle w modelu domeny (np. klucze podstawowe).</span><span class="sxs-lookup"><span data-stu-id="4171f-122">Field-only properties are commonly used when the entity class uses methods instead of properties to get/set values, or in cases where fields shouldn't be exposed at all in the domain model (e.g. primary keys).</span></span>

<span data-ttu-id="4171f-123">Właściwość tylko do pól można skonfigurować, `Property(...)` podając nazwę w interfejsie API:</span><span class="sxs-lookup"><span data-stu-id="4171f-123">You can configure a field-only property by providing a name in the `Property(...)` API:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

<span data-ttu-id="4171f-124">EF spróbuje znaleźć właściwość CLR o podanej nazwie lub pole, jeśli właściwość nie zostanie znaleziona.</span><span class="sxs-lookup"><span data-stu-id="4171f-124">EF will attempt to find a CLR property with the given name, or a field if a property isn't found.</span></span> <span data-ttu-id="4171f-125">Jeśli nie zostanie znaleziona żadna właściwość ani pole, zamiast tego zostanie skonfigurowana właściwość cienia.</span><span class="sxs-lookup"><span data-stu-id="4171f-125">If neither a property nor a field are found, a shadow property will be set up instead.</span></span>

<span data-ttu-id="4171f-126">Może być konieczne odwoływanie się do właściwości tylko w polu z zapytań LINQ, ale takie pola są zazwyczaj prywatne.</span><span class="sxs-lookup"><span data-stu-id="4171f-126">You may need to refer to a field-only property from LINQ queries, but such fields are typically private.</span></span> <span data-ttu-id="4171f-127">Można użyć `EF.Property(...)` metody w kwerendzie LINQ, aby odwołać się do pola:</span><span class="sxs-lookup"><span data-stu-id="4171f-127">You can use the `EF.Property(...)` method in a LINQ query to refer to the field:</span></span>

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "_validatedUrl"));
```
