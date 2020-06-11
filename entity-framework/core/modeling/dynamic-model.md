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
# <a name="alternating-between-multiple-models-with-the-same-dbcontext-type"></a>Przemienne między wieloma modelami o tym samym typie DbContext

Model wbudowany w programie `OnModelCreating` może użyć właściwości w kontekście, aby zmienić sposób kompilowania modelu. Załóżmy na przykład, że chcemy skonfigurować jednostkę w różny sposób w zależności od właściwości:

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnModelCreating)]

Niestety, ten kod nie działa tak, jak to jest, ponieważ EF kompiluje model i działa `OnModelCreating` tylko raz, buforowanie wyniku ze względu na wydajność. Można jednak podpiąć do mechanizmu buforowania modelu, aby zapewnić, że EF wie o właściwości dającej różne modele.

## <a name="imodelcachekeyfactory"></a>IModelCacheKeyFactory

Dr używa `IModelCacheKeyFactory` do generowania kluczy pamięci podręcznej dla modeli; domyślnie EF zakłada, że dla dowolnego typu kontekstu model będzie taki sam, więc domyślna implementacja tej usługi zwraca klucz, który po prostu zawiera typ kontekstu. Aby utworzyć różne modele z tego samego typu kontekstu, należy zastąpić `IModelCacheKeyFactory` usługę poprawną implementacją. wygenerowany klucz będzie porównywany z innymi kluczami modelu przy użyciu `Equals` metody, uwzględniając wszystkie zmienne, które wpływają na model.

`UseIntProperty`Podczas tworzenia klucza pamięci podręcznej modelu jest uwzględniana Następująca implementacja:

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicModelCacheKeyFactory.cs?name=DynamicModel)]

Na koniec Zarejestruj swoją nową zawartość `IModelCacheKeyFactory` w Twoim kontekście `OnConfiguring` :

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnConfiguring)]

Zobacz [pełny przykładowy projekt](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DynamicModel) , aby uzyskać więcej kontekstu.
