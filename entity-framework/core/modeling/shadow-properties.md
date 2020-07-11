---
title: Właściwości cienia — EF Core
author: AndriySvyryd
ms.date: 01/03/2020
ms.assetid: 75369266-d2b9-4416-b118-ed238f81f599
uid: core/modeling/shadow-properties
ms.openlocfilehash: b24ff85d8f5910a5625910e7225a94112d769824
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238284"
---
# <a name="shadow-properties"></a>Właściwości w tle

Właściwości cienia to właściwości, które nie są zdefiniowane w klasie jednostki .NET, ale są zdefiniowane dla tego typu jednostki w modelu EF Core. Wartość i stan tych właściwości są utrzymywane wyłącznie w monitorze zmian. Właściwości cienia są przydatne, gdy w bazie danych znajdują się dane, które nie powinny być uwidocznione w mapowanych typach obiektów.

## <a name="foreign-key-shadow-properties"></a>Właściwości cienia klucza obcego

Właściwości cienia są najczęściej używane w przypadku właściwości klucza obcego, gdzie relacja między dwiema jednostkami jest reprezentowana przez wartość klucza obcego w bazie danych, ale relacja jest zarządzana w typach jednostek przy użyciu właściwości nawigacji między typami jednostek. Zgodnie z Konwencją EF wprowadzi Właściwość Shadow w przypadku odnalezienia relacji, ale w klasie jednostki zależnej nie znaleziono żadnej właściwości klucza obcego.

Właściwość będzie nosiła nazwę `<navigation property name><principal key property name>` (Nawigacja w jednostce zależnej, która wskazuje jednostkę główną, jest używana do nazewnictwa). Jeśli nazwa właściwości klucza podmiotu zabezpieczeń zawiera nazwę właściwości nawigacji, nazwa zostanie wydana `<principal key property name>` . Jeśli w jednostce zależnej nie ma właściwości nawigacji, w jej miejscu zostanie użyta nazwa typu podmiotu zabezpieczeń.

Na przykład następująca lista kodu spowoduje, że `BlogId` Właściwość Shadow zostanie wprowadzona do `Post` jednostki:

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/ShadowForeignKey.cs?name=Conventions&highlight=21-23)]

## <a name="configuring-shadow-properties"></a>Konfigurowanie właściwości cienia

Aby skonfigurować właściwości cienia, można użyć interfejsu API Fluent. Po wywołaniu ciągu przeciążenia, można utworzyć `Property` łańcuch dowolnego wywołania konfiguracji dla innych właściwości. W poniższym przykładzie, ponieważ `Blog` nie ma właściwości CLR o nazwie `LastUpdated` , tworzona jest właściwość Shadow:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ShadowProperty.cs?name=ShadowProperty&highlight=8)]

Jeśli nazwa przekazywana do `Property` metody jest zgodna z nazwą istniejącej właściwości (właściwości cienia lub jednej zdefiniowanej w klasie jednostki), wówczas kod skonfiguruje istniejącą właściwość zamiast wprowadzać nową właściwość Shadow.

## <a name="accessing-shadow-properties"></a>Uzyskiwanie dostępu do właściwości cienia

Wartości właściwości cienia można uzyskać i zmienić za pomocą `ChangeTracker` interfejsu API:

``` csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

Właściwości cienia można przywoływać w zapytaniach LINQ za pomocą `EF.Property` metody statycznej:

``` csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

Nie można uzyskać dostępu do właściwości cienia po zapytaniu bez śledzenia, ponieważ zwrócone jednostki nie są śledzone przez śledzenie zmian.
