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
# <a name="entity-properties"></a>Właściwości jednostki

Każdy typ jednostki w modelu ma zestaw właściwości, które EF Core będą odczytywać i zapisywać dane z bazy danych. W przypadku korzystania z relacyjnej bazy danych właściwości jednostki są mapowane na kolumny tabeli.

## <a name="included-and-excluded-properties"></a>Właściwości uwzględnione i wykluczone

Według Konwencji wszystkie właściwości publiczne z metodą pobierającą i setter zostaną uwzględnione w modelu.

Określone właściwości można wykluczyć w następujący sposób:

### <a name="data-annotations"></a>[Adnotacje danych](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreProperty.cs?name=IgnoreProperty&highlight=6)]

### <a name="fluent-api"></a>[Interfejs API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreProperty.cs?name=IgnoreProperty&highlight=3,4)]

***

## <a name="column-names"></a>Nazwy kolumn

Zgodnie z Konwencją, w przypadku korzystania z relacyjnej bazy danych właściwości jednostki są mapowane na kolumny tabeli o tej samej nazwie co właściwość.

Jeśli wolisz skonfigurować kolumny z różnymi nazwami, możesz to zrobić w następujący sposób:

### <a name="data-annotations"></a>[Adnotacje danych](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnName.cs?Name=ColumnName&highlight=3)]

### <a name="fluent-api"></a>[Interfejs API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnName.cs?Name=ColumnName&highlight=3-5)]

***

## <a name="column-data-types"></a>Column — typy danych

W przypadku korzystania z relacyjnej bazy danych dostawca bazy danych wybiera typ danych na podstawie typu .NET właściwości. Uwzględnia również inne metadane, takie jak skonfigurowana [Maksymalna długość](#maximum-length), czy właściwość jest częścią klucza podstawowego itd.

Na przykład SQL Server mapuje `DateTime` właściwości do `datetime2(7)` kolumn i `string` właściwości na `nvarchar(max)` kolumny (lub do `nvarchar(450)` właściwości, które są używane jako klucz).

Możesz również skonfigurować kolumny, aby określić dokładny typ danych dla kolumny. Na przykład poniższy kod konfiguruje `Url` jako ciąg niebędący znakiem Unicode z maksymalną długością `200` i `Rating` jako dziesiętną z dokładnością `5` i skalą `2` :

### <a name="data-annotations"></a>[Adnotacje danych](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnDataType.cs?name=ColumnDataType&highlight=4,6)]

### <a name="fluent-api"></a>[Interfejs API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnDataType.cs?name=ColumnDataType&highlight=5-6)]

***

### <a name="maximum-length"></a>Maksymalna długość

Skonfigurowanie maksymalnej długości zapewnia wskazówkę dla dostawcy bazy danych o odpowiednim typie danych kolumny do wyboru dla danej właściwości. Maksymalna długość dotyczy tylko typów danych tablicowych, takich jak `string` i `byte[]` .

> [!NOTE]
> Entity Framework nie sprawdza żadnej weryfikacji maksymalnej długości przed przekazaniem danych do dostawcy. Jest to dostawca lub magazyn danych do zweryfikowania, jeśli jest to konieczne. Na przykład podczas określania wartości docelowej SQL Server przekroczenie maksymalnej długości spowoduje wyjątek, ponieważ typ danych kolumny źródłowej nie zezwala na przechowywanie nadmiarowych danych.

W poniższym przykładzie skonfigurowanie maksymalnej długości 500 spowoduje utworzenie kolumny typu `nvarchar(500)` na SQL Server:

#### <a name="data-annotations"></a>[Adnotacje danych](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/MaxLength.cs?name=MaxLength&highlight=4)]

#### <a name="fluent-api"></a>[Interfejs API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/MaxLength.cs?name=MaxLength&highlight=3-5)]

***

## <a name="required-and-optional-properties"></a>Właściwości wymagane i opcjonalne

Właściwość jest uważana za opcjonalną, jeśli jest poprawna, aby mogła ją zawierać `null` . Jeśli `null` nie jest prawidłową wartością do przypisania do właściwości, zostanie ona uznana za właściwość wymaganą. Podczas mapowania na schemat relacyjnej bazy danych, wymagane właściwości są tworzone jako kolumny niedopuszczające wartości null, a właściwości opcjonalne są tworzone jako kolumny dopuszczające wartości null.

### <a name="conventions"></a>Konwencje

Zgodnie z Konwencją właściwość, której typ .NET może zawierać wartość null, zostanie skonfigurowana jako opcjonalna, natomiast właściwości, których typ .NET nie może zawierać wartości null, zostaną skonfigurowane zgodnie z wymaganiami. Na przykład wszystkie właściwości z typami wartości .NET ( `int` ,, `decimal` `bool` itp.) są skonfigurowane jako wymagane, a wszystkie właściwości z typami wartości null platformy .NET ( `int?` ,, `decimal?` `bool?` itp.) są skonfigurowane jako opcjonalne.

W języku C# 8 wprowadzono nową funkcję o nazwie [typu referencyjnego nullable](/dotnet/csharp/tutorials/nullable-reference-types), która umożliwia dodawanie adnotacji do typów odwołań, wskazujących, czy są one prawidłowe dla nich puste. Ta funkcja jest domyślnie wyłączona, a jeśli ta opcja jest włączona, modyfikuje zachowanie EF Core w następujący sposób:

* Jeśli typy odwołań do wartości null są wyłączone (wartość domyślna), wszystkie właściwości z typami odwołań platformy .NET są konfigurowane jako opcjonalne według Konwencji (np. `string` ).
* Jeśli typy odwołań do wartości null są włączone, właściwości zostaną skonfigurowane na podstawie wartości null w języku C# typu .NET: `string?` zostaną skonfigurowane jako opcjonalne, a następnie `string` zostaną skonfigurowane zgodnie z wymaganiami.

W poniższym przykładzie przedstawiono typ jednostki z wymaganymi i opcjonalnymi właściwościami z włączoną funkcją odwołania do wartości null (ustawienie domyślne) i włączony:

#### <a name="without-nullable-reference-types-default"></a>[Bez wartości null typów referencyjnych (wartość domyślna)](#tab/without-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithoutNullableReferenceTypes.cs?name=Customer&highlight=4-8)]

#### <a name="with-nullable-reference-types"></a>[Z typami odwołań dopuszczających wartość null](#tab/with-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Customer.cs?name=Customer&highlight=4-6)]

***

Zaleca się użycie typów referencyjnych dopuszczających wartość null, ponieważ powoduje ona przepływność w kodzie C# w celu EF Core modelu i bazy danych, a także eliminuje użycie interfejsu API Fluent lub adnotacji danych w celu wyrażenia tego samego pojęcia dwa razy.

> [!NOTE]
> Należy zachować ostrożność podczas włączania typów referencyjnych dopuszczających wartość null w istniejącym projekcie: właściwości typu referencyjnego, które zostały wcześniej skonfigurowane jako opcjonalne, zostaną teraz skonfigurowane zgodnie z wymaganiami, chyba że zostaną jawnie dodane do wartości null. W przypadku zarządzania schematem relacyjnej bazy danych może to spowodować wygenerowanie migracji, co pozwala zmienić wartość null kolumny bazy danych.

Aby uzyskać więcej informacji na temat typów referencyjnych dopuszczających wartości null i sposobu ich używania z EF Core, [Zobacz stronę dedykowanej dokumentacji dla tej funkcji](xref:core/miscellaneous/nullable-reference-types).

### <a name="explicit-configuration"></a>Konfiguracja jawna

Właściwość, która będzie opcjonalna w Konwencji, można skonfigurować tak, aby była wymagana w następujący sposób:

#### <a name="data-annotations"></a>[Adnotacje danych](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?name=Required&highlight=4)]

#### <a name="fluent-api"></a>[Interfejs API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?name=Required&highlight=3-5)]

***

## <a name="column-collations"></a>Sortowanie kolumn

> [!NOTE]
> Ta funkcja jest wprowadzana w EF Core 5,0.

Sortowanie można definiować w kolumnach tekstowych, określając, w jaki sposób są porównywane i uporządkowane. Na przykład następujące konfiguruje kolumnę SQL Server, aby nie uwzględniać wielkości liter:

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?range=42-43)]

Jeśli wszystkie kolumny w bazie danych muszą używać określonego sortowania, zdefiniuj sortowanie na poziomie bazy danych.

Ogólne informacje na temat obsługi sortowania EF Core można znaleźć na [stronie dokumentacji sortowania](xref:core/miscellaneous/collations-and-case-sensitivity.md).