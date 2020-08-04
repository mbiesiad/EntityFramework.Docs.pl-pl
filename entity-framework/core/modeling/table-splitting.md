---
title: Podział tabeli — EF Core
description: Jak skonfigurować podział tabeli przy użyciu Entity Framework Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 01/03/2020
uid: core/modeling/table-splitting
ms.openlocfilehash: e7428bc516a69310b6a6f521acc49aee0ba9f802
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526502"
---
# <a name="table-splitting"></a><span data-ttu-id="f294b-103">Dzielenie tabeli</span><span class="sxs-lookup"><span data-stu-id="f294b-103">Table Splitting</span></span>

<span data-ttu-id="f294b-104">EF Core umożliwia mapowanie dwóch lub więcej jednostek do jednego wiersza.</span><span class="sxs-lookup"><span data-stu-id="f294b-104">EF Core allows to map two or more entities to a single row.</span></span> <span data-ttu-id="f294b-105">Jest to tzw. _dzielenie tabeli_ lub _udostępnianie tabel_.</span><span class="sxs-lookup"><span data-stu-id="f294b-105">This is called _table splitting_ or _table sharing_.</span></span>

## <a name="configuration"></a><span data-ttu-id="f294b-106">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="f294b-106">Configuration</span></span>

<span data-ttu-id="f294b-107">Aby używać dzielenia tabel, należy zamapować typy jednostek na tę samą tabelę, mieć klucze podstawowe zamapowane na te same kolumny i co najmniej jedną relację skonfigurowaną między kluczem podstawowym jednego typu jednostki a inną w tej samej tabeli.</span><span class="sxs-lookup"><span data-stu-id="f294b-107">To use table splitting the entity types need to be mapped to the same table, have the primary keys mapped to the same columns and at least one relationship configured between the primary key of one entity type and another in the same table.</span></span>

<span data-ttu-id="f294b-108">Typowy scenariusz dzielenia tabeli polega na użyciu tylko podzbioru kolumn w tabeli w celu uzyskania większej wydajności lub hermetyzacji.</span><span class="sxs-lookup"><span data-stu-id="f294b-108">A common scenario for table splitting is using only a subset of the columns in the table for greater performance or encapsulation.</span></span>

<span data-ttu-id="f294b-109">W tym przykładzie `Order` reprezentuje podzestaw `DetailedOrder` .</span><span class="sxs-lookup"><span data-stu-id="f294b-109">In this example `Order` represents a subset of `DetailedOrder`.</span></span>

[!code-csharp[Order](../../../samples/core/Modeling/TableSplitting/Order.cs?name=Order)]

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/TableSplitting/DetailedOrder.cs?name=DetailedOrder)]

<span data-ttu-id="f294b-110">Poza wymaganą konfiguracją, `Property(o => o.Status).HasColumnName("Status")` która jest wywoływana w celu mapowania `DetailedOrder.Status` do tej samej kolumny co `Order.Status` .</span><span class="sxs-lookup"><span data-stu-id="f294b-110">In addition to the required configuration we call `Property(o => o.Status).HasColumnName("Status")` to map `DetailedOrder.Status` to the same column as `Order.Status`.</span></span>

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=TableSplitting)]

> [!TIP]
> <span data-ttu-id="f294b-111">Zobacz [pełny przykładowy projekt](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting) , aby uzyskać więcej kontekstu.</span><span class="sxs-lookup"><span data-stu-id="f294b-111">See the [full sample project](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting) for more context.</span></span>

## <a name="usage"></a><span data-ttu-id="f294b-112">Użycie</span><span class="sxs-lookup"><span data-stu-id="f294b-112">Usage</span></span>

<span data-ttu-id="f294b-113">Zapisywanie i wykonywanie zapytań względem jednostek przy użyciu dzielenia tabeli odbywa się w taki sam sposób jak inne jednostki:</span><span class="sxs-lookup"><span data-stu-id="f294b-113">Saving and querying entities using table splitting is done in the same way as other entities:</span></span>

[!code-csharp[Usage](../../../samples/core/Modeling/TableSplitting/Program.cs?name=Usage)]

## <a name="optional-dependent-entity"></a><span data-ttu-id="f294b-114">Opcjonalna jednostka zależna</span><span class="sxs-lookup"><span data-stu-id="f294b-114">Optional dependent entity</span></span>

> [!NOTE]
> <span data-ttu-id="f294b-115">Ta funkcja została wprowadzona w EF Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="f294b-115">This feature was introduced in EF Core 3.0.</span></span>

<span data-ttu-id="f294b-116">Jeśli wszystkie kolumny używane przez jednostkę zależną znajdują się `NULL` w bazie danych, żadne wystąpienie dla niego nie zostanie utworzone podczas wykonywania zapytania.</span><span class="sxs-lookup"><span data-stu-id="f294b-116">If all of the columns used by a dependent entity are `NULL` in the database, then no instance for it will be created when queried.</span></span> <span data-ttu-id="f294b-117">Umożliwia to modelowanie opcjonalnej jednostki zależnej, gdzie Właściwość relacji na podmiotu zabezpieczeń będzie równa null.</span><span class="sxs-lookup"><span data-stu-id="f294b-117">This allows modeling an optional dependent entity, where the relationship property on the principal would be null.</span></span> <span data-ttu-id="f294b-118">Należy zauważyć, że taka sytuacja ma miejsce, jeśli wszystkie właściwości zależne są opcjonalne i ustawione na `null` , co może nie być oczekiwane.</span><span class="sxs-lookup"><span data-stu-id="f294b-118">Note that this would also happen if all of the dependent's properties are optional and set to `null`, which might not be expected.</span></span>

## <a name="concurrency-tokens"></a><span data-ttu-id="f294b-119">Tokeny współbieżności</span><span class="sxs-lookup"><span data-stu-id="f294b-119">Concurrency tokens</span></span>

<span data-ttu-id="f294b-120">Jeśli którykolwiek z typów jednostek współużytkujących tabelę ma token współbieżności, musi być również uwzględniony we wszystkich innych typach jednostek.</span><span class="sxs-lookup"><span data-stu-id="f294b-120">If any of the entity types sharing a table has a concurrency token then it must be included in all other entity types as well.</span></span> <span data-ttu-id="f294b-121">Jest to konieczne w celu uniknięcia nieodświeżonej wartości tokenu współbieżności, gdy jest aktualizowana tylko jedna z jednostek zamapowanych na tę samą tabelę.</span><span class="sxs-lookup"><span data-stu-id="f294b-121">This is necessary in order to avoid a stale concurrency token value when only one of the entities mapped to the same table is updated.</span></span>

<span data-ttu-id="f294b-122">Aby uniknąć ujawniania tokenu współbieżności w kodzie konsumowanym, możliwe jest utworzenie jednej jako [Właściwości cienia](xref:core/modeling/shadow-properties):</span><span class="sxs-lookup"><span data-stu-id="f294b-122">To avoid exposing the concurrency token to the consuming code, it's possible the create one as a [shadow property](xref:core/modeling/shadow-properties):</span></span>

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=ConcurrencyToken&highlight=2)]
