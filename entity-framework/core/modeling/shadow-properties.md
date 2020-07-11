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
# <a name="shadow-properties"></a><span data-ttu-id="d352c-102">Właściwości w tle</span><span class="sxs-lookup"><span data-stu-id="d352c-102">Shadow Properties</span></span>

<span data-ttu-id="d352c-103">Właściwości cienia to właściwości, które nie są zdefiniowane w klasie jednostki .NET, ale są zdefiniowane dla tego typu jednostki w modelu EF Core.</span><span class="sxs-lookup"><span data-stu-id="d352c-103">Shadow properties are properties that are not defined in your .NET entity class but are defined for that entity type in the EF Core model.</span></span> <span data-ttu-id="d352c-104">Wartość i stan tych właściwości są utrzymywane wyłącznie w monitorze zmian.</span><span class="sxs-lookup"><span data-stu-id="d352c-104">The value and state of these properties is maintained purely in the Change Tracker.</span></span> <span data-ttu-id="d352c-105">Właściwości cienia są przydatne, gdy w bazie danych znajdują się dane, które nie powinny być uwidocznione w mapowanych typach obiektów.</span><span class="sxs-lookup"><span data-stu-id="d352c-105">Shadow properties are useful when there is data in the database that should not be exposed on the mapped entity types.</span></span>

## <a name="foreign-key-shadow-properties"></a><span data-ttu-id="d352c-106">Właściwości cienia klucza obcego</span><span class="sxs-lookup"><span data-stu-id="d352c-106">Foreign key shadow properties</span></span>

<span data-ttu-id="d352c-107">Właściwości cienia są najczęściej używane w przypadku właściwości klucza obcego, gdzie relacja między dwiema jednostkami jest reprezentowana przez wartość klucza obcego w bazie danych, ale relacja jest zarządzana w typach jednostek przy użyciu właściwości nawigacji między typami jednostek.</span><span class="sxs-lookup"><span data-stu-id="d352c-107">Shadow properties are most often used for foreign key properties, where the relationship between two entities is represented by a foreign key value in the database, but the relationship is managed on the entity types using navigation properties between the entity types.</span></span> <span data-ttu-id="d352c-108">Zgodnie z Konwencją EF wprowadzi Właściwość Shadow w przypadku odnalezienia relacji, ale w klasie jednostki zależnej nie znaleziono żadnej właściwości klucza obcego.</span><span class="sxs-lookup"><span data-stu-id="d352c-108">By convention, EF will introduce a shadow property when a relationship is discovered but no foreign key property is found in the dependent entity class.</span></span>

<span data-ttu-id="d352c-109">Właściwość będzie nosiła nazwę `<navigation property name><principal key property name>` (Nawigacja w jednostce zależnej, która wskazuje jednostkę główną, jest używana do nazewnictwa).</span><span class="sxs-lookup"><span data-stu-id="d352c-109">The property will be named `<navigation property name><principal key property name>` (the navigation on the dependent entity, which points to the principal entity, is used for the naming).</span></span> <span data-ttu-id="d352c-110">Jeśli nazwa właściwości klucza podmiotu zabezpieczeń zawiera nazwę właściwości nawigacji, nazwa zostanie wydana `<principal key property name>` .</span><span class="sxs-lookup"><span data-stu-id="d352c-110">If the principal key property name includes the name of the navigation property, then the name will just be `<principal key property name>`.</span></span> <span data-ttu-id="d352c-111">Jeśli w jednostce zależnej nie ma właściwości nawigacji, w jej miejscu zostanie użyta nazwa typu podmiotu zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="d352c-111">If there is no navigation property on the dependent entity, then the principal type name is used in its place.</span></span>

<span data-ttu-id="d352c-112">Na przykład następująca lista kodu spowoduje, że `BlogId` Właściwość Shadow zostanie wprowadzona do `Post` jednostki:</span><span class="sxs-lookup"><span data-stu-id="d352c-112">For example, the following code listing will result in a `BlogId` shadow property being introduced to the `Post` entity:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/ShadowForeignKey.cs?name=Conventions&highlight=21-23)]

## <a name="configuring-shadow-properties"></a><span data-ttu-id="d352c-113">Konfigurowanie właściwości cienia</span><span class="sxs-lookup"><span data-stu-id="d352c-113">Configuring shadow properties</span></span>

<span data-ttu-id="d352c-114">Aby skonfigurować właściwości cienia, można użyć interfejsu API Fluent.</span><span class="sxs-lookup"><span data-stu-id="d352c-114">You can use the Fluent API to configure shadow properties.</span></span> <span data-ttu-id="d352c-115">Po wywołaniu ciągu przeciążenia, można utworzyć `Property` łańcuch dowolnego wywołania konfiguracji dla innych właściwości.</span><span class="sxs-lookup"><span data-stu-id="d352c-115">Once you have called the string overload of `Property`, you can chain any of the configuration calls you would for other properties.</span></span> <span data-ttu-id="d352c-116">W poniższym przykładzie, ponieważ `Blog` nie ma właściwości CLR o nazwie `LastUpdated` , tworzona jest właściwość Shadow:</span><span class="sxs-lookup"><span data-stu-id="d352c-116">In the following sample, since `Blog` has no CLR property named `LastUpdated`, a shadow property is created:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ShadowProperty.cs?name=ShadowProperty&highlight=8)]

<span data-ttu-id="d352c-117">Jeśli nazwa przekazywana do `Property` metody jest zgodna z nazwą istniejącej właściwości (właściwości cienia lub jednej zdefiniowanej w klasie jednostki), wówczas kod skonfiguruje istniejącą właściwość zamiast wprowadzać nową właściwość Shadow.</span><span class="sxs-lookup"><span data-stu-id="d352c-117">If the name supplied to the `Property` method matches the name of an existing property (a shadow property or one defined on the entity class), then the code will configure that existing property rather than introducing a new shadow property.</span></span>

## <a name="accessing-shadow-properties"></a><span data-ttu-id="d352c-118">Uzyskiwanie dostępu do właściwości cienia</span><span class="sxs-lookup"><span data-stu-id="d352c-118">Accessing shadow properties</span></span>

<span data-ttu-id="d352c-119">Wartości właściwości cienia można uzyskać i zmienić za pomocą `ChangeTracker` interfejsu API:</span><span class="sxs-lookup"><span data-stu-id="d352c-119">Shadow property values can be obtained and changed through the `ChangeTracker` API:</span></span>

``` csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

<span data-ttu-id="d352c-120">Właściwości cienia można przywoływać w zapytaniach LINQ za pomocą `EF.Property` metody statycznej:</span><span class="sxs-lookup"><span data-stu-id="d352c-120">Shadow properties can be referenced in LINQ queries via the `EF.Property` static method:</span></span>

``` csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

<span data-ttu-id="d352c-121">Nie można uzyskać dostępu do właściwości cienia po zapytaniu bez śledzenia, ponieważ zwrócone jednostki nie są śledzone przez śledzenie zmian.</span><span class="sxs-lookup"><span data-stu-id="d352c-121">Shadow properties cannot be accessed after a no-tracking query since the entities returned are not tracked by the change tracker.</span></span>
