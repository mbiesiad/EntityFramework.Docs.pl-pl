---
title: Właściwości jednostki — EF Core
description: Jak skonfigurować i zmapować właściwości jednostki przy użyciu Entity Framework Core
author: roji
ms.date: 12/10/2019
ms.assetid: e9dff604-3469-4a05-8f9e-18ac281d82a9
uid: core/modeling/entity-properties
ms.openlocfilehash: e4a1867a90df1fb277e7dd44b93d6c2d47895030
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664159"
---
# <a name="entity-properties"></a><span data-ttu-id="92740-103">Właściwości jednostki</span><span class="sxs-lookup"><span data-stu-id="92740-103">Entity Properties</span></span>

<span data-ttu-id="92740-104">Każdy typ jednostki w modelu ma zestaw właściwości, które EF Core będą odczytywać i zapisywać dane z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="92740-104">Each entity type in your model has a set of properties, which EF Core will read and write from the database.</span></span> <span data-ttu-id="92740-105">W przypadku korzystania z relacyjnej bazy danych właściwości jednostki są mapowane na kolumny tabeli.</span><span class="sxs-lookup"><span data-stu-id="92740-105">If you're using a relational database, entity properties map to table columns.</span></span>

## <a name="included-and-excluded-properties"></a><span data-ttu-id="92740-106">Właściwości uwzględnione i wykluczone</span><span class="sxs-lookup"><span data-stu-id="92740-106">Included and excluded properties</span></span>

<span data-ttu-id="92740-107">Według Konwencji wszystkie właściwości publiczne z metodą pobierającą i setter zostaną uwzględnione w modelu.</span><span class="sxs-lookup"><span data-stu-id="92740-107">By convention, all public properties with a getter and a setter will be included in the model.</span></span>

<span data-ttu-id="92740-108">Określone właściwości można wykluczyć w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="92740-108">Specific properties can be excluded as follows:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="92740-109">Adnotacje danych</span><span class="sxs-lookup"><span data-stu-id="92740-109">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreProperty.cs?name=IgnoreProperty&highlight=6)]

### <a name="fluent-api"></a>[<span data-ttu-id="92740-110">Interfejs API Fluent</span><span class="sxs-lookup"><span data-stu-id="92740-110">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreProperty.cs?name=IgnoreProperty&highlight=3,4)]

***

## <a name="column-names"></a><span data-ttu-id="92740-111">Nazwy kolumn</span><span class="sxs-lookup"><span data-stu-id="92740-111">Column names</span></span>

<span data-ttu-id="92740-112">Zgodnie z Konwencją, w przypadku korzystania z relacyjnej bazy danych właściwości jednostki są mapowane na kolumny tabeli o tej samej nazwie co właściwość.</span><span class="sxs-lookup"><span data-stu-id="92740-112">By convention, when using a relational database, entity properties are mapped to table columns having the same name as the property.</span></span>

<span data-ttu-id="92740-113">Jeśli wolisz skonfigurować kolumny z różnymi nazwami, możesz to zrobić w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="92740-113">If you prefer to configure your columns with different names, you can do so as following:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="92740-114">Adnotacje danych</span><span class="sxs-lookup"><span data-stu-id="92740-114">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnName.cs?Name=ColumnName&highlight=3)]

### <a name="fluent-api"></a>[<span data-ttu-id="92740-115">Interfejs API Fluent</span><span class="sxs-lookup"><span data-stu-id="92740-115">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnName.cs?Name=ColumnName&highlight=3-5)]

***

## <a name="column-data-types"></a><span data-ttu-id="92740-116">Column — typy danych</span><span class="sxs-lookup"><span data-stu-id="92740-116">Column data types</span></span>

<span data-ttu-id="92740-117">W przypadku korzystania z relacyjnej bazy danych dostawca bazy danych wybiera typ danych na podstawie typu .NET właściwości.</span><span class="sxs-lookup"><span data-stu-id="92740-117">When using a relational database, the database provider selects a data type based on the .NET type of the property.</span></span> <span data-ttu-id="92740-118">Uwzględnia również inne metadane, takie jak skonfigurowana [Maksymalna długość](#maximum-length), czy właściwość jest częścią klucza podstawowego itd.</span><span class="sxs-lookup"><span data-stu-id="92740-118">It also takes into account other metadata, such as the configured [maximum length](#maximum-length), whether the property is part of a primary key, etc.</span></span>

<span data-ttu-id="92740-119">Na przykład SQL Server mapuje `DateTime` właściwości do `datetime2(7)` kolumn i `string` właściwości na `nvarchar(max)` kolumny (lub do `nvarchar(450)` właściwości, które są używane jako klucz).</span><span class="sxs-lookup"><span data-stu-id="92740-119">For example, SQL Server maps `DateTime` properties to `datetime2(7)` columns, and `string` properties to `nvarchar(max)` columns (or to `nvarchar(450)` for properties that are used as a key).</span></span>

<span data-ttu-id="92740-120">Możesz również skonfigurować kolumny, aby określić dokładny typ danych dla kolumny.</span><span class="sxs-lookup"><span data-stu-id="92740-120">You can also configure your columns to specify an exact data type for a column.</span></span> <span data-ttu-id="92740-121">Na przykład poniższy kod konfiguruje `Url` jako ciąg niebędący znakiem Unicode z maksymalną długością `200` i `Rating` jako dziesiętną z dokładnością `5` i skalą `2` :</span><span class="sxs-lookup"><span data-stu-id="92740-121">For example the following code configures `Url` as a non-unicode string with maximum length of `200` and `Rating` as decimal with precision of `5` and scale of `2`:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="92740-122">Adnotacje danych</span><span class="sxs-lookup"><span data-stu-id="92740-122">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnDataType.cs?name=ColumnDataType&highlight=4,6)]

### <a name="fluent-api"></a>[<span data-ttu-id="92740-123">Interfejs API Fluent</span><span class="sxs-lookup"><span data-stu-id="92740-123">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnDataType.cs?name=ColumnDataType&highlight=5-6)]

***

### <a name="maximum-length"></a><span data-ttu-id="92740-124">Maksymalna długość</span><span class="sxs-lookup"><span data-stu-id="92740-124">Maximum length</span></span>

<span data-ttu-id="92740-125">Skonfigurowanie maksymalnej długości zapewnia wskazówkę dla dostawcy bazy danych o odpowiednim typie danych kolumny do wyboru dla danej właściwości.</span><span class="sxs-lookup"><span data-stu-id="92740-125">Configuring a maximum length provides a hint to the database provider about the appropriate column data type to choose for a given property.</span></span> <span data-ttu-id="92740-126">Maksymalna długość dotyczy tylko typów danych tablicowych, takich jak `string` i `byte[]` .</span><span class="sxs-lookup"><span data-stu-id="92740-126">Maximum length only applies to array data types, such as `string` and `byte[]`.</span></span>

> [!NOTE]
> <span data-ttu-id="92740-127">Entity Framework nie sprawdza żadnej weryfikacji maksymalnej długości przed przekazaniem danych do dostawcy.</span><span class="sxs-lookup"><span data-stu-id="92740-127">Entity Framework does not do any validation of maximum length before passing data to the provider.</span></span> <span data-ttu-id="92740-128">Jest to dostawca lub magazyn danych do zweryfikowania, jeśli jest to konieczne.</span><span class="sxs-lookup"><span data-stu-id="92740-128">It is up to the provider or data store to validate if appropriate.</span></span> <span data-ttu-id="92740-129">Na przykład podczas określania wartości docelowej SQL Server przekroczenie maksymalnej długości spowoduje wyjątek, ponieważ typ danych kolumny źródłowej nie zezwala na przechowywanie nadmiarowych danych.</span><span class="sxs-lookup"><span data-stu-id="92740-129">For example, when targeting SQL Server, exceeding the maximum length will result in an exception as the data type of the underlying column will not allow excess data to be stored.</span></span>

<span data-ttu-id="92740-130">W poniższym przykładzie skonfigurowanie maksymalnej długości 500 spowoduje utworzenie kolumny typu `nvarchar(500)` na SQL Server:</span><span class="sxs-lookup"><span data-stu-id="92740-130">In the following example, configuring a maximum length of 500 will cause a column of type `nvarchar(500)` to be created on SQL Server:</span></span>

#### <a name="data-annotations"></a>[<span data-ttu-id="92740-131">Adnotacje danych</span><span class="sxs-lookup"><span data-stu-id="92740-131">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/MaxLength.cs?name=MaxLength&highlight=4)]

#### <a name="fluent-api"></a>[<span data-ttu-id="92740-132">Interfejs API Fluent</span><span class="sxs-lookup"><span data-stu-id="92740-132">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/MaxLength.cs?name=MaxLength&highlight=3-5)]

***

## <a name="required-and-optional-properties"></a><span data-ttu-id="92740-133">Właściwości wymagane i opcjonalne</span><span class="sxs-lookup"><span data-stu-id="92740-133">Required and optional properties</span></span>

<span data-ttu-id="92740-134">Właściwość jest uważana za opcjonalną, jeśli jest poprawna, aby mogła ją zawierać `null` .</span><span class="sxs-lookup"><span data-stu-id="92740-134">A property is considered optional if it is valid for it to contain `null`.</span></span> <span data-ttu-id="92740-135">Jeśli `null` nie jest prawidłową wartością do przypisania do właściwości, zostanie ona uznana za właściwość wymaganą.</span><span class="sxs-lookup"><span data-stu-id="92740-135">If `null` is not a valid value to be assigned to a property then it is considered to be a required property.</span></span> <span data-ttu-id="92740-136">Podczas mapowania na schemat relacyjnej bazy danych, wymagane właściwości są tworzone jako kolumny niedopuszczające wartości null, a właściwości opcjonalne są tworzone jako kolumny dopuszczające wartości null.</span><span class="sxs-lookup"><span data-stu-id="92740-136">When mapping to a relational database schema, required properties are created as non-nullable columns, and optional properties are created as nullable columns.</span></span>

### <a name="conventions"></a><span data-ttu-id="92740-137">Konwencje</span><span class="sxs-lookup"><span data-stu-id="92740-137">Conventions</span></span>

<span data-ttu-id="92740-138">Zgodnie z Konwencją właściwość, której typ .NET może zawierać wartość null, zostanie skonfigurowana jako opcjonalna, natomiast właściwości, których typ .NET nie może zawierać wartości null, zostaną skonfigurowane zgodnie z wymaganiami.</span><span class="sxs-lookup"><span data-stu-id="92740-138">By convention, a property whose .NET type can contain null will be configured as optional, whereas properties whose .NET type cannot contain null will be configured as required.</span></span> <span data-ttu-id="92740-139">Na przykład wszystkie właściwości z typami wartości .NET ( `int` ,, `decimal` `bool` itp.) są skonfigurowane jako wymagane, a wszystkie właściwości z typami wartości null platformy .NET ( `int?` ,, `decimal?` `bool?` itp.) są skonfigurowane jako opcjonalne.</span><span class="sxs-lookup"><span data-stu-id="92740-139">For example, all properties with .NET value types (`int`, `decimal`, `bool`, etc.) are configured as required, and all properties with nullable .NET value types (`int?`, `decimal?`, `bool?`, etc.) are configured as optional.</span></span>

<span data-ttu-id="92740-140">W języku C# 8 wprowadzono nową funkcję o nazwie [typu referencyjnego nullable](/dotnet/csharp/tutorials/nullable-reference-types), która umożliwia dodawanie adnotacji do typów odwołań, wskazujących, czy są one prawidłowe dla nich puste.</span><span class="sxs-lookup"><span data-stu-id="92740-140">C# 8 introduced a new feature called [nullable reference types](/dotnet/csharp/tutorials/nullable-reference-types), which allows reference types to be annotated, indicating whether it is valid for them to contain null or not.</span></span> <span data-ttu-id="92740-141">Ta funkcja jest domyślnie wyłączona, a jeśli ta opcja jest włączona, modyfikuje zachowanie EF Core w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="92740-141">This feature is disabled by default, and if enabled, it modifies EF Core's behavior in the following way:</span></span>

* <span data-ttu-id="92740-142">Jeśli typy odwołań do wartości null są wyłączone (wartość domyślna), wszystkie właściwości z typami odwołań platformy .NET są konfigurowane jako opcjonalne według Konwencji (np. `string` ).</span><span class="sxs-lookup"><span data-stu-id="92740-142">If nullable reference types are disabled (the default), all properties with .NET reference types are configured as optional by convention (e.g. `string`).</span></span>
* <span data-ttu-id="92740-143">Jeśli typy odwołań do wartości null są włączone, właściwości zostaną skonfigurowane na podstawie wartości null w języku C# typu .NET: `string?` zostaną skonfigurowane jako opcjonalne, a następnie `string` zostaną skonfigurowane zgodnie z wymaganiami.</span><span class="sxs-lookup"><span data-stu-id="92740-143">If nullable reference types are enabled, properties will be configured based on the C# nullability of their .NET type: `string?` will be configured as optional, whereas `string` will be configured as required.</span></span>

<span data-ttu-id="92740-144">W poniższym przykładzie przedstawiono typ jednostki z wymaganymi i opcjonalnymi właściwościami z włączoną funkcją odwołania do wartości null (ustawienie domyślne) i włączony:</span><span class="sxs-lookup"><span data-stu-id="92740-144">The following example shows an entity type with required and optional properties, with the nullable reference feature disabled (the default) and enabled:</span></span>

#### <a name="without-nullable-reference-types-default"></a>[<span data-ttu-id="92740-145">Bez wartości null typów referencyjnych (wartość domyślna)</span><span class="sxs-lookup"><span data-stu-id="92740-145">Without nullable reference types (default)</span></span>](#tab/without-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithoutNullableReferenceTypes.cs?name=Customer&highlight=4-8)]

#### <a name="with-nullable-reference-types"></a>[<span data-ttu-id="92740-146">Z typami odwołań dopuszczających wartość null</span><span class="sxs-lookup"><span data-stu-id="92740-146">With nullable reference types</span></span>](#tab/with-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Customer.cs?name=Customer&highlight=4-6)]

***

<span data-ttu-id="92740-147">Zaleca się użycie typów referencyjnych dopuszczających wartość null, ponieważ powoduje ona przepływność w kodzie C# w celu EF Core modelu i bazy danych, a także eliminuje użycie interfejsu API Fluent lub adnotacji danych w celu wyrażenia tego samego pojęcia dwa razy.</span><span class="sxs-lookup"><span data-stu-id="92740-147">Using nullable reference types is recommended since it flows the nullability expressed in C# code to EF Core's model and to the database, and obviates the use of the Fluent API or Data Annotations to express the same concept twice.</span></span>

> [!NOTE]
> <span data-ttu-id="92740-148">Należy zachować ostrożność podczas włączania typów referencyjnych dopuszczających wartość null w istniejącym projekcie: właściwości typu referencyjnego, które zostały wcześniej skonfigurowane jako opcjonalne, zostaną teraz skonfigurowane zgodnie z wymaganiami, chyba że zostaną jawnie dodane do wartości null.</span><span class="sxs-lookup"><span data-stu-id="92740-148">Exercise caution when enabling nullable reference types on an existing project: reference type properties which were previously configured as optional will now be configured as required, unless they are explicitly annotated to be nullable.</span></span> <span data-ttu-id="92740-149">W przypadku zarządzania schematem relacyjnej bazy danych może to spowodować wygenerowanie migracji, co pozwala zmienić wartość null kolumny bazy danych.</span><span class="sxs-lookup"><span data-stu-id="92740-149">When managing a relational database schema, this may cause migrations to be generated which alter the database column's nullability.</span></span>

<span data-ttu-id="92740-150">Aby uzyskać więcej informacji na temat typów referencyjnych dopuszczających wartości null i sposobu ich używania z EF Core, [Zobacz stronę dedykowanej dokumentacji dla tej funkcji](xref:core/miscellaneous/nullable-reference-types).</span><span class="sxs-lookup"><span data-stu-id="92740-150">For more information on nullable reference types and how to use them with EF Core, [see the dedicated documentation page for this feature](xref:core/miscellaneous/nullable-reference-types).</span></span>

### <a name="explicit-configuration"></a><span data-ttu-id="92740-151">Konfiguracja jawna</span><span class="sxs-lookup"><span data-stu-id="92740-151">Explicit configuration</span></span>

<span data-ttu-id="92740-152">Właściwość, która będzie opcjonalna w Konwencji, można skonfigurować tak, aby była wymagana w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="92740-152">A property that would be optional by convention can be configured to be required as follows:</span></span>

#### <a name="data-annotations"></a>[<span data-ttu-id="92740-153">Adnotacje danych</span><span class="sxs-lookup"><span data-stu-id="92740-153">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?name=Required&highlight=4)]

#### <a name="fluent-api"></a>[<span data-ttu-id="92740-154">Interfejs API Fluent</span><span class="sxs-lookup"><span data-stu-id="92740-154">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?name=Required&highlight=3-5)]

***

## <a name="column-collations"></a><span data-ttu-id="92740-155">Sortowanie kolumn</span><span class="sxs-lookup"><span data-stu-id="92740-155">Column collations</span></span>

> [!NOTE]
> <span data-ttu-id="92740-156">Ta funkcja jest wprowadzana w EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="92740-156">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="92740-157">Sortowanie można definiować w kolumnach tekstowych, określając, w jaki sposób są porównywane i uporządkowane.</span><span class="sxs-lookup"><span data-stu-id="92740-157">A collation can be defined on text columns, determining how they are compared and ordered.</span></span> <span data-ttu-id="92740-158">Na przykład następujące konfiguruje kolumnę SQL Server, aby nie uwzględniać wielkości liter:</span><span class="sxs-lookup"><span data-stu-id="92740-158">For example, the following configures a SQL Server column to be case-insensitive:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?range=42-43)]

<span data-ttu-id="92740-159">Jeśli wszystkie kolumny w bazie danych muszą używać określonego sortowania, zdefiniuj sortowanie na poziomie bazy danych.</span><span class="sxs-lookup"><span data-stu-id="92740-159">If all columns in a database need to use a certain collation, define the collation at the database level instead.</span></span>

<span data-ttu-id="92740-160">Ogólne informacje na temat obsługi sortowania EF Core można znaleźć na [stronie dokumentacji sortowania](xref:core/miscellaneous/collations-and-case-sensitivity.md).</span><span class="sxs-lookup"><span data-stu-id="92740-160">General information about EF Core support for collations can be found in the [collation documentation page](xref:core/miscellaneous/collations-and-case-sensitivity.md).</span></span>