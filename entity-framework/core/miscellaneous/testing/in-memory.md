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
# <a name="testing-with-the-ef-in-memory-database"></a>Testowanie za pomocą bazy danych EF w pamięci

> [!WARNING]
> W pamięci bazy danych EF często działa inaczej niż relacyjne bazy danych.
> Po pełnym zrozumieniu problemów i zalet związanych z handlem należy używać tylko EF w pamięci, jak opisano w [testowaniu kodu, który używa EF Core](xref:core/miscellaneous/testing/index).  

> [!TIP]
> Program SQLite jest dostawcą relacyjnym i może również używać baz danych znajdujących się w pamięci.
> Rozważ użycie tego do testowania, aby lepiej dopasować typowe zachowania relacyjnej bazy danych.
> Jest to omówione w temacie [Używanie oprogramowania SQLite do testowania aplikacji EF Core](xref:core/miscellaneous/testing/sqlite).   

Informacje na tej stronie znajdują się teraz w innych lokalizacjach:
* Zobacz [testowanie kodu, który używa EF Core,](xref:core/miscellaneous/testing/index) Aby uzyskać ogólne informacje o testowaniu z bazą danych EF w pamięci.
* Zobacz [przykład pokazujący, jak testować aplikacje, które używają EF Core](xref:core/miscellaneous/testing/testing-sample) , aby uzyskać przykład przy użyciu EF bazy danych w pamięci.
* Aby uzyskać ogólne informacje o bazie danych EF w pamięci, zobacz artykuł [EF inMemory Provider](xref:core/providers/in-memory/index) .
