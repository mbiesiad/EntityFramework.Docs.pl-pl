---
title: Przemienne między wieloma modelami o tym samym typie DbContext — EF Core
author: AndriySvyryd
ms.date: 01/03/2020
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/dynamic-model
ms.openlocfilehash: 35743b5ba87bb4239d7f758320f9facccc74330f
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664224"
---
# <a name="alternating-between-multiple-models-with-the-same-dbcontext-type"></a><span data-ttu-id="8a7c1-102">Przemienne między wieloma modelami o tym samym typie DbContext</span><span class="sxs-lookup"><span data-stu-id="8a7c1-102">Alternating between multiple models with the same DbContext type</span></span>

<span data-ttu-id="8a7c1-103">Model wbudowany w programie `OnModelCreating` może użyć właściwości w kontekście, aby zmienić sposób kompilowania modelu.</span><span class="sxs-lookup"><span data-stu-id="8a7c1-103">The model built in `OnModelCreating` can use a property on the context to change how the model is built.</span></span> <span data-ttu-id="8a7c1-104">Załóżmy na przykład, że chcemy skonfigurować jednostkę w różny sposób w zależności od właściwości:</span><span class="sxs-lookup"><span data-stu-id="8a7c1-104">For example, suppose you wanted to configure an entity differently based on some property:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnModelCreating)]

<span data-ttu-id="8a7c1-105">Niestety, ten kod nie działa tak, jak to jest, ponieważ EF kompiluje model i działa `OnModelCreating` tylko raz, buforowanie wyniku ze względu na wydajność.</span><span class="sxs-lookup"><span data-stu-id="8a7c1-105">Unfortunately, this code wouldn't work as-is, since EF builds the model and runs `OnModelCreating` only once, caching the result for performance reasons.</span></span> <span data-ttu-id="8a7c1-106">Można jednak podpiąć do mechanizmu buforowania modelu, aby zapewnić, że EF wie o właściwości dającej różne modele.</span><span class="sxs-lookup"><span data-stu-id="8a7c1-106">However, you can hook into the model caching mechanism to make EF aware of the property producing different models.</span></span>

## <a name="imodelcachekeyfactory"></a><span data-ttu-id="8a7c1-107">IModelCacheKeyFactory</span><span class="sxs-lookup"><span data-stu-id="8a7c1-107">IModelCacheKeyFactory</span></span>

<span data-ttu-id="8a7c1-108">Dr używa `IModelCacheKeyFactory` do generowania kluczy pamięci podręcznej dla modeli; domyślnie EF zakłada, że dla dowolnego typu kontekstu model będzie taki sam, więc domyślna implementacja tej usługi zwraca klucz, który po prostu zawiera typ kontekstu.</span><span class="sxs-lookup"><span data-stu-id="8a7c1-108">EF uses the `IModelCacheKeyFactory` to generate cache keys for models; by default, EF assumes that for any given context type the model will be the same, so the default implementation of this service returns a key that just contains the context type.</span></span> <span data-ttu-id="8a7c1-109">Aby utworzyć różne modele z tego samego typu kontekstu, należy zastąpić `IModelCacheKeyFactory` usługę poprawną implementacją. wygenerowany klucz będzie porównywany z innymi kluczami modelu przy użyciu `Equals` metody, uwzględniając wszystkie zmienne, które wpływają na model.</span><span class="sxs-lookup"><span data-stu-id="8a7c1-109">To produce different models from the same context type, you need to replace the `IModelCacheKeyFactory` service with the correct implementation; the generated key will be compared to other model keys using the `Equals` method, taking into account all the variables that affect the model.</span></span>

<span data-ttu-id="8a7c1-110">`UseIntProperty`Podczas tworzenia klucza pamięci podręcznej modelu jest uwzględniana Następująca implementacja:</span><span class="sxs-lookup"><span data-stu-id="8a7c1-110">The following implementation takes the `UseIntProperty` into account when producing a model cache key:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicModelCacheKeyFactory.cs?name=DynamicModel)]

<span data-ttu-id="8a7c1-111">Na koniec Zarejestruj swoją nową zawartość `IModelCacheKeyFactory` w Twoim kontekście `OnConfiguring` :</span><span class="sxs-lookup"><span data-stu-id="8a7c1-111">Finally, register your new `IModelCacheKeyFactory` in your context's `OnConfiguring`:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnConfiguring)]

<span data-ttu-id="8a7c1-112">Zobacz [pełny przykładowy projekt](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DynamicModel) , aby uzyskać więcej kontekstu.</span><span class="sxs-lookup"><span data-stu-id="8a7c1-112">See the [full sample project](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DynamicModel) for more context.</span></span>
