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
# <a name="backing-fields"></a>Pola zapasowe

Pola zapasowe umożliwiają EF odczyt i/lub zapis w polu, a nie we właściwości. Może to być przydatne, gdy hermetyzacja w klasie jest używana do ograniczania użycia i/lub zwiększenia semantyki wokół dostępu do danych przez kod aplikacji, ale wartość powinna być odczytywana z i/lub zapisywana w bazie danych bez użycia tych ograniczeń/ulepszeń.

## <a name="basic-configuration"></a>Konfiguracja podstawowa

Zgodnie z konwencją następujące pola zostaną odnalezione jako pola zapasowe dla danej właściwości (wymienione w kolejności pierwszeństwa). 

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

W poniższym przykładzie `Url` właściwość jest `_url` skonfigurowana jako jego pole podkładowe:

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

Należy zauważyć, że pola zapasowe są odnajdywki tylko dla właściwości, które są zawarte w modelu. Aby uzyskać więcej informacji na temat właściwości, które są uwzględnione w modelu, zobacz [Uwzględnianie & Wykluczanie właściwości](included-properties.md).

Można również skonfigurować pola kopii zapasowej przy użyciu adnotacji danych lub interfejsu API Fluent, na przykład jeśli nazwa pola nie odpowiada powyższym konwencjom:

### <a name="data-annotations"></a>[Adnotacje danych](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/BackingField.cs?name=BackingField&highlight=7)]

### <a name="fluent-api"></a>[Płynne API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs?name=BackingField&highlight=5)]

## <a name="field-and-property-access"></a>Dostęp do pól i właściwości

Domyślnie EF zawsze będzie odczytywać i zapisywać w polu zapasowym — przy założeniu, że jeden został poprawnie skonfigurowany — i nigdy nie będzie używać właściwości. Jednak EF obsługuje również inne wzorce dostępu. Na przykład poniższy przykład nakazuje EF zapisywać w polu zapasowym tylko podczas materializacji i używać właściwości we wszystkich innych przypadkach:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs?name=BackingFieldAccessMode&highlight=6)]

Zobacz [PropertyAccessMode wyliczenia](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) dla pełnego zestawu obsługiwanych opcji.

> [!NOTE]
> W ef core 3.0 domyślny tryb `PreferFieldDuringConstruction` `PreferField`dostępu do właściwości zmienił się z na .

## <a name="field-only-properties"></a>Właściwości tylko w polu

Można również utworzyć właściwości koncepcyjnej w modelu, który nie ma odpowiedniej właściwości CLR w klasie jednostki, ale zamiast tego używa pola do przechowywania danych w jednostce. Różni się to od [właściwości cienia](shadow-properties.md), gdzie dane są przechowywane w monitorze zmian, a nie w typie CLR jednostki. Właściwości tylko pola są często używane, gdy klasa jednostki używa metod zamiast właściwości, aby uzyskać/ustawić wartości lub w przypadkach, gdy pola nie powinny być widoczne w ogóle w modelu domeny (np. klucze podstawowe).

Właściwość tylko do pól można skonfigurować, `Property(...)` podając nazwę w interfejsie API:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

EF spróbuje znaleźć właściwość CLR o podanej nazwie lub pole, jeśli właściwość nie zostanie znaleziona. Jeśli nie zostanie znaleziona żadna właściwość ani pole, zamiast tego zostanie skonfigurowana właściwość cienia.

Może być konieczne odwoływanie się do właściwości tylko w polu z zapytań LINQ, ale takie pola są zazwyczaj prywatne. Można użyć `EF.Property(...)` metody w kwerendzie LINQ, aby odwołać się do pola:

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "_validatedUrl"));
```
