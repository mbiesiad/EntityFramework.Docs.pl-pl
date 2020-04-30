---
title: Testowanie przy użyciu oprogramowania SQLite-EF Core
description: Testowanie aplikacji EF Core przy użyciu oprogramowania SQLite
author: ajcvickers
ms.date: 04/24/2020
uid: core/miscellaneous/testing/sqlite
ms.openlocfilehash: 327fdc230df2a3b4094accf93fffa81f92e0a931
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82538285"
---
# <a name="using-sqlite-to-test-an-ef-core-application"></a><span data-ttu-id="8747b-103">Testowanie aplikacji EF Core przy użyciu oprogramowania SQLite</span><span class="sxs-lookup"><span data-stu-id="8747b-103">Using SQLite to test an EF Core application</span></span>

> [!WARNING]
> <span data-ttu-id="8747b-104">Korzystanie z oprogramowania SQLite może być skutecznym sposobem przetestowania aplikacji EF Core.</span><span class="sxs-lookup"><span data-stu-id="8747b-104">Using SQLite can be an effective way to test an EF Core application.</span></span>
> <span data-ttu-id="8747b-105">Problemy mogą jednak wystąpić w sytuacji, gdy program SQLite działa inaczej niż w przypadku innych systemów baz danych.</span><span class="sxs-lookup"><span data-stu-id="8747b-105">However, problems can arise where SQLite behaves differently from other database systems.</span></span> <span data-ttu-id="8747b-106">Zobacz [testowanie kodu, który używa EF Core](xref:core/miscellaneous/testing/index) do omówienia problemów i zalet.</span><span class="sxs-lookup"><span data-stu-id="8747b-106">See [Testing code that uses EF Core](xref:core/miscellaneous/testing/index) for a discussion of the issues and trade-offs.</span></span>  

<span data-ttu-id="8747b-107">Ten dokument zawiera informacje dotyczące pojęć wprowadzonych w [przykładzie pokazujący sposób testowania aplikacji, które używają EF Core](xref:core/miscellaneous/testing/testing-sample).</span><span class="sxs-lookup"><span data-stu-id="8747b-107">This document builds uses on the concepts introduced in [Sample showing how to test applications that use EF Core](xref:core/miscellaneous/testing/testing-sample).</span></span>
<span data-ttu-id="8747b-108">Przykłady kodu przedstawione w tym miejscu pochodzą z tego przykładu.</span><span class="sxs-lookup"><span data-stu-id="8747b-108">The code examples shown here come from this sample.</span></span>

## <a name="using-sqlite-in-memory-databases"></a><span data-ttu-id="8747b-109">Korzystanie z baz danych programu SQLite w pamięci</span><span class="sxs-lookup"><span data-stu-id="8747b-109">Using SQLite in-memory databases</span></span>

<span data-ttu-id="8747b-110">Zwykle Program SQLite tworzy bazy danych jako proste pliki i uzyskuje dostęp do pliku w procesie przy użyciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8747b-110">Normally, SQLite creates databases as simple files and accesses the file in-process with your application.</span></span>
<span data-ttu-id="8747b-111">Jest to bardzo szybkie, szczególnie w przypadku korzystania z szybkiego dysku [SSD](https://en.wikipedia.org/wiki/Solid-state_drive).</span><span class="sxs-lookup"><span data-stu-id="8747b-111">This is very fast, especially when using a fast [SSD](https://en.wikipedia.org/wiki/Solid-state_drive).</span></span> 

<span data-ttu-id="8747b-112">Program SQLite może również używać baz danych utworzonych w pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="8747b-112">SQLite can also use databases created purely in-memory.</span></span>
<span data-ttu-id="8747b-113">Jest to łatwe w użyciu z EF Core, o ile zrozumiesz okres istnienia bazy danych w pamięci:</span><span class="sxs-lookup"><span data-stu-id="8747b-113">This is easy to use with EF Core as long as you understand the in-memory database lifetime:</span></span>
* <span data-ttu-id="8747b-114">Baza danych jest tworzona, gdy zostanie otwarte połączenie</span><span class="sxs-lookup"><span data-stu-id="8747b-114">The database is created when the connection to it is opened</span></span>
* <span data-ttu-id="8747b-115">Baza danych jest usuwana po zamknięciu połączenia</span><span class="sxs-lookup"><span data-stu-id="8747b-115">The database is deleted when the connection to it is closed</span></span>

<span data-ttu-id="8747b-116">EF Core użyje już otwartego połączenia, gdy zostanie on określony, i nigdy nie spróbuje go zamknąć.</span><span class="sxs-lookup"><span data-stu-id="8747b-116">EF Core will use an already open connection when given one, and will never attempt to close it.</span></span>
<span data-ttu-id="8747b-117">Dlatego klucz używany EF Core z bazą danych programu SQLite w pamięci polega na otwarciu połączenia przed przekazaniem go do EF.</span><span class="sxs-lookup"><span data-stu-id="8747b-117">So the key to using EF Core with an in-memory SQLite database is to open the connection before passing it to EF.</span></span>  

<span data-ttu-id="8747b-118">[Przykład](xref:core/miscellaneous/testing/testing-sample) osiąga ten kod przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="8747b-118">The [sample](xref:core/miscellaneous/testing/testing-sample) achieves this with the following code:</span></span>

[!code-csharp[SqliteInMemory](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/SqliteInMemoryItemsControllerTest.cs?name=SqliteInMemory)]

<span data-ttu-id="8747b-119">Wzory</span><span class="sxs-lookup"><span data-stu-id="8747b-119">Notice:</span></span>
* <span data-ttu-id="8747b-120">`CreateInMemoryDatabase` Metoda tworzy bazę danych w pamięci programu SQLite i otwiera połączenie z nim.</span><span class="sxs-lookup"><span data-stu-id="8747b-120">The `CreateInMemoryDatabase` method creates a SQLite in-memory database and opens the connection to it.</span></span>
* <span data-ttu-id="8747b-121">Utworzony `DbConnection` został wyodrębniony z `ContextOptions` i zapisany.</span><span class="sxs-lookup"><span data-stu-id="8747b-121">The created `DbConnection` is extracted from the `ContextOptions` and saved.</span></span>
* <span data-ttu-id="8747b-122">Połączenie jest usuwane, gdy test zostanie usunięty, aby zasoby nie były wyciekami.</span><span class="sxs-lookup"><span data-stu-id="8747b-122">The connection is disposed when the test is disposed so that resources are not leaked.</span></span> 

> [!NOTE]
> <span data-ttu-id="8747b-123">[#16103 problemu](https://github.com/dotnet/efcore/issues/16103) śledzi sposoby ułatwienia tego zarządzania połączeniami.</span><span class="sxs-lookup"><span data-stu-id="8747b-123">[Issue #16103](https://github.com/dotnet/efcore/issues/16103) is tracking ways to make this connection management easier.</span></span> 
