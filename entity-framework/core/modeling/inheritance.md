---
title: Dziedziczenie — EF Core
description: Jak skonfigurować dziedziczenie typu jednostki przy użyciu Entity Framework Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 10/27/2016
uid: core/modeling/inheritance
ms.openlocfilehash: 0e94013a0b894b162f4bb3ca8e7acb1aca349011
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664055"
---
# <a name="inheritance"></a>Dziedziczenie

EF można zmapować hierarchię typów .NET do bazy danych. Dzięki temu można pisać jednostki platformy .NET w kodzie w zwykły sposób, przy użyciu podstawowych i pochodnych typów, a program EF bezproblemowo tworzy odpowiedni schemat bazy danych, wysyła zapytania itp. Rzeczywiste szczegóły dotyczące sposobu mapowania hierarchii typów są zależne od dostawcy. Ta strona zawiera opis obsługi dziedziczenia w kontekście relacyjnej bazy danych.

W tej chwili EF Core obsługuje tylko wzorzec Table-per-Hierarchy (TPH). TPH używa pojedynczej tabeli do przechowywania danych dla wszystkich typów w hierarchii, a kolumna rozróżniacza służy do identyfikowania, który typ reprezentuje każdy wiersz.

> [!NOTE]
> EF Core nie są jeszcze obsługiwane przez program na podstawie typu tabeli (TPT) i typu tabeli (TPC), które są obsługiwane przez EF6. TPT to główna funkcja zaplanowana dla EF Core 5,0.

## <a name="entity-type-hierarchy-mapping"></a>Mapowanie hierarchii typów jednostek

Zgodnie z Konwencją EF nie skanuje automatycznie dla typów podstawowych lub pochodnych; oznacza to, że jeśli typ CLR ma być mapowany w hierarchii, należy jawnie określić ten typ w modelu. Na przykład określenie tylko typu podstawowego hierarchii nie powoduje, że EF Core niejawnie uwzględnić wszystkie jego podtypy.

Poniższy przykład uwidacznia Nieogólnymi dla `Blog` i jego podklasę `RssBlog` . Jeśli `Blog` ma inną podklasę, nie zostanie ona uwzględniona w modelu.

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs?name=InheritanceDbSets&highlight=3-4)]

Ten model jest mapowany na następujący schemat bazy danych (należy zwrócić uwagę na niejawnie utworzoną kolumnę *rozróżniacza* , która określa, który typ *blogu* jest przechowywany w każdym wierszu):

![image (obraz)](_static/inheritance-tph-data.png)

>[!NOTE]
> W przypadku korzystania z mapowania TPH kolumny bazy danych są automatycznie wprowadzane do wartości null. Na przykład kolumna *RssUrl* ma wartość null, ponieważ zwykłe wystąpienia *blogu* nie mają tej właściwości.

Jeśli nie chcesz uwidaczniać Nieogólnymi dla co najmniej jednej jednostki w hierarchii, możesz również użyć interfejsu API Fluent, aby upewnić się, że są one uwzględnione w modelu.

> [!TIP]
> Jeśli nie korzystasz z Konwencji, możesz określić typ podstawowy jawnie przy użyciu `HasBaseType` . Można również użyć, `.HasBaseType((Type)null)` Aby usunąć typ jednostki z hierarchii.

## <a name="discriminator-configuration"></a>Konfiguracja rozróżniacza

Można skonfigurować nazwę i typ kolumny rozróżniacza oraz wartości, które są używane do identyfikacji poszczególnych typów w hierarchii:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorConfiguration.cs?name=DiscriminatorConfiguration&highlight=4-6)]

W powyższych przykładach EF dodał rozróżniacz niejawnie jako [Właściwość Shadow](xref:core/modeling/shadow-properties) w jednostce podstawowej hierarchii. Tę właściwość można skonfigurować w taki sam sposób:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorPropertyConfiguration.cs?name=DiscriminatorPropertyConfiguration&highlight=4-5)]

Na koniec rozróżniacz może być również mapowany na zwykłą Właściwość platformy .NET w jednostce:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/NonShadowDiscriminator.cs?name=NonShadowDiscriminator&highlight=4)]

## <a name="shared-columns"></a>Udostępnione kolumny

Domyślnie, gdy dwa typy jednostek równorzędnych w hierarchii mają właściwość o tej samej nazwie, zostaną zamapowane na dwie oddzielne kolumny. Jeśli jednak ich typ jest identyczny, można go zamapować na tę samą kolumnę bazy danych:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedTPHColumns.cs?name=SharedTPHColumns&highlight=9,13)]
