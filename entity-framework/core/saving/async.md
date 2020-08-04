---
title: Zapisywanie asynchroniczne — EF Core
author: rowanmiller
ms.date: 01/24/2017
ms.assetid: b64a606e-ecd9-4807-829a-b6ec05ade33f
uid: core/saving/async
ms.openlocfilehash: aa1fbfdc44895eace04354063435f98370aed2c4
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526917"
---
# <a name="asynchronous-saving"></a><span data-ttu-id="63334-102">Zapisywanie asynchroniczne</span><span class="sxs-lookup"><span data-stu-id="63334-102">Asynchronous Saving</span></span>

<span data-ttu-id="63334-103">Zapisywanie asynchroniczne pozwala uniknąć blokowania wątku, podczas gdy zmiany są zapisywane w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="63334-103">Asynchronous saving avoids blocking a thread while the changes are written to the database.</span></span> <span data-ttu-id="63334-104">Może to być przydatne, aby uniknąć zamarzania interfejsu użytkownika aplikacji z szeroką obsługą klienta.</span><span class="sxs-lookup"><span data-stu-id="63334-104">This can be useful to avoid freezing the UI of a thick-client application.</span></span> <span data-ttu-id="63334-105">Operacje asynchroniczne mogą również zwiększyć przepływność w aplikacji sieci Web, gdzie wątek może zostać zwolniony do obsługi innych żądań podczas kończenia operacji bazy danych.</span><span class="sxs-lookup"><span data-stu-id="63334-105">Asynchronous operations can also increase throughput in a web application, where the thread can be freed up to service other requests while the database operation completes.</span></span> <span data-ttu-id="63334-106">Aby uzyskać więcej informacji, zobacz [programowanie asynchroniczne w języku C#](/dotnet/csharp/async).</span><span class="sxs-lookup"><span data-stu-id="63334-106">For more information, see [Asynchronous Programming in C#](/dotnet/csharp/async).</span></span>

> [!WARNING]  
> <span data-ttu-id="63334-107">EF Core nie obsługuje wielu operacji równoległych wykonywanych w tym samym wystąpieniu kontekstu.</span><span class="sxs-lookup"><span data-stu-id="63334-107">EF Core does not support multiple parallel operations being run on the same context instance.</span></span> <span data-ttu-id="63334-108">Przed rozpoczęciem następnej operacji zawsze należy czekać na ukończenie operacji.</span><span class="sxs-lookup"><span data-stu-id="63334-108">You should always wait for an operation to complete before beginning the next operation.</span></span> <span data-ttu-id="63334-109">Zwykle jest to wykonywane przy użyciu `await` słowa kluczowego dla każdej operacji asynchronicznej.</span><span class="sxs-lookup"><span data-stu-id="63334-109">This is typically done by using the `await` keyword on each asynchronous operation.</span></span>

<span data-ttu-id="63334-110">Entity Framework Core stanowi `DbContext.SaveChangesAsync()` alternatywę asynchroniczną dla `DbContext.SaveChanges()` .</span><span class="sxs-lookup"><span data-stu-id="63334-110">Entity Framework Core provides `DbContext.SaveChangesAsync()` as an asynchronous alternative to `DbContext.SaveChanges()`.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Async/Sample.cs#Sample)]
