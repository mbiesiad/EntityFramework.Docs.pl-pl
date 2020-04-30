---
title: Testowanie za pomocą bazy danych EF w pamięci — EF Core
author: ajcvickers
description: Korzystanie z bazy danych EF w pamięci do testowania aplikacji EF Core
ms.date: 10/27/2016
uid: core/miscellaneous/testing/in-memory
ms.openlocfilehash: f31b3bdedb8c339dbb6baa9d7f2031d023d5757f
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82538349"
---
# <a name="testing-with-the-ef-in-memory-database"></a><span data-ttu-id="6382b-103">Testowanie za pomocą bazy danych EF w pamięci</span><span class="sxs-lookup"><span data-stu-id="6382b-103">Testing with the EF In-Memory Database</span></span>

> [!WARNING]
> <span data-ttu-id="6382b-104">W pamięci bazy danych EF często działa inaczej niż relacyjne bazy danych.</span><span class="sxs-lookup"><span data-stu-id="6382b-104">The EF in-memory database often behaves differently than relational databases.</span></span>
> <span data-ttu-id="6382b-105">Po pełnym zrozumieniu problemów i zalet związanych z handlem należy używać tylko EF w pamięci, jak opisano w [testowaniu kodu, który używa EF Core](xref:core/miscellaneous/testing/index).</span><span class="sxs-lookup"><span data-stu-id="6382b-105">Only use the EF in-memory database after fully understanding the issues and trade-offs involved, as discussed in [Testing code that uses EF Core](xref:core/miscellaneous/testing/index).</span></span>  

> [!TIP]
> <span data-ttu-id="6382b-106">Program SQLite jest dostawcą relacyjnym i może również używać baz danych znajdujących się w pamięci.</span><span class="sxs-lookup"><span data-stu-id="6382b-106">SQLite is a relational provider and can also use in-memory databases.</span></span>
> <span data-ttu-id="6382b-107">Rozważ użycie tego do testowania, aby lepiej dopasować typowe zachowania relacyjnej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="6382b-107">Consider using this for testing to more closely match common relational database behaviors.</span></span>
> <span data-ttu-id="6382b-108">Jest to omówione w temacie [Używanie oprogramowania SQLite do testowania aplikacji EF Core](xref:core/miscellaneous/testing/sqlite).</span><span class="sxs-lookup"><span data-stu-id="6382b-108">This is covered in [Using SQLite to test an EF Core application](xref:core/miscellaneous/testing/sqlite).</span></span>   

<span data-ttu-id="6382b-109">Informacje na tej stronie znajdują się teraz w innych lokalizacjach:</span><span class="sxs-lookup"><span data-stu-id="6382b-109">The information on this page now lives in other locations:</span></span>
* <span data-ttu-id="6382b-110">Zobacz [testowanie kodu, który używa EF Core,](xref:core/miscellaneous/testing/index) Aby uzyskać ogólne informacje o testowaniu z bazą danych EF w pamięci.</span><span class="sxs-lookup"><span data-stu-id="6382b-110">See [Testing code that uses EF Core](xref:core/miscellaneous/testing/index) for general information on testing with the EF in-memory database.</span></span>
* <span data-ttu-id="6382b-111">Zobacz [przykład pokazujący, jak testować aplikacje, które używają EF Core](xref:core/miscellaneous/testing/testing-sample) , aby uzyskać przykład przy użyciu EF bazy danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="6382b-111">See [Sample showing how to test applications that use EF Core](xref:core/miscellaneous/testing/testing-sample) for a sample using the EF in-memory database.</span></span>
* <span data-ttu-id="6382b-112">Aby uzyskać ogólne informacje o bazie danych EF w pamięci, zobacz artykuł [EF inMemory Provider](xref:core/providers/in-memory/index) .</span><span class="sxs-lookup"><span data-stu-id="6382b-112">See [The EF in-memory database provider](xref:core/providers/in-memory/index) for general information about the EF in-memory database.</span></span>
