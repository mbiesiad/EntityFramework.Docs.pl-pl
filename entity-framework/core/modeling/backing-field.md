---
title: Pola zapasowe — EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: a628795e-64df-4f24-a5e8-76bc261e7ed8
uid: core/modeling/backing-field
ms.openlocfilehash: 20cf9dc9b0d556f29680bce588bcbdc4ea48fa74
ms.sourcegitcommit: f2a38c086291699422d8b28a72d9611d1b24ad0d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76124382"
---
# <a name="backing-fields"></a>Pola zapasowe

Pola zapasowe umożliwiają EF odczyt i/lub zapis do pola, a nie właściwości. Może to być przydatne, gdy hermetyzacja w klasie jest używana do ograniczania użycia i/lub podniesienia semantyki dostępu do danych przez kod aplikacji, ale wartość powinna zostać odczytana i/lub zapisywana w bazie danych bez użycia tych ograniczeń/ulepszeń.

## <a name="basic-configuration"></a>Konfiguracja podstawowa

Według Konwencji następujące pola zostaną odnalezione jako pola zapasowe dla danej właściwości (wymienione w kolejności pierwszeństwa). 

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

W poniższym przykładzie właściwość `Url` jest skonfigurowana tak, aby miała `_url` jako pole zapasowe:

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

Należy zauważyć, że pola zapasowe są odnajdywane tylko dla właściwości, które są uwzględnione w modelu. Aby uzyskać więcej informacji na temat właściwości uwzględnionych w modelu, zobacz [m.in. & z wyłączeniem właściwości](included-properties.md).

Możesz również jawnie skonfigurować pola zapasowe, np. Jeśli nazwa pola nie odpowiada powyższym konwencjom:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs?name=BackingField&highlight=5)]

## <a name="field-and-property-access"></a>Dostęp do pól i właściwości

Domyślnie EF zawsze odczytuje i zapisu do pola zapasowego — przy założeniu, że został on poprawnie skonfigurowany — i nigdy nie będzie używać właściwości. Jednakże EF obsługuje również inne wzorce dostępu. Na przykład poniższy przykład instruuje EF do zapisu w polu zapasowym tylko wtedy, gdy materializacji, i do używania właściwości we wszystkich innych przypadkach:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs?name=BackingFieldAccessMode&highlight=6)]

Aby uzyskać pełny zestaw obsługiwanych opcji, zobacz [Wyliczenie PropertyAccessMode](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) .

> [!NOTE]
> W EF Core 3,0 domyślny tryb dostępu do właściwości został zmieniony z `PreferFieldDuringConstruction` na `PreferField`.

## <a name="field-only-properties"></a>Właściwości tylko dla pól

Można również utworzyć w modelu Właściwość koncepcyjną, która nie ma odpowiedniej właściwości CLR w klasie Entity, ale zamiast tego używa pola do przechowywania danych w jednostce. Różni się to od [Właściwości cienia](shadow-properties.md), gdzie dane są przechowywane w monitorze zmian, a nie w typie CLR obiektu. Właściwości tylko dla pól są często używane, gdy Klasa jednostki używa metod zamiast właściwości do pobierania/ustawiania wartości lub w przypadkach, gdy pola nie powinny być ujawniane w modelu domeny (np. klucze podstawowe).

Właściwość "tylko pole" można skonfigurować, podając nazwę w interfejsie API `Property(...)`:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

Dr podejmie próbę znalezienia właściwości CLR o podaną nazwę lub pola, jeśli nie można odnaleźć właściwości. Jeśli nie zostanie znaleziona żadna właściwość ani pole, zamiast tego zostanie ustawiona właściwość Shadow.

Może być konieczne odwoływanie się do właściwości tylko pola z zapytań LINQ, ale takie pola są zwykle prywatne. Można użyć metody `EF.Property(...)` w zapytaniu LINQ, aby odwołać się do pola:

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "_validatedUrl"));
```
