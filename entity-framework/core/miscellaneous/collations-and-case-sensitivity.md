---
title: Sortowanie i czułość wielkości liter — EF Core
description: Jak skonfigurować sortowanie i uwzględnianie wielkości liter w bazie danych i zapytaniach
author: roji
ms.date: 04/27/2020
ms.assetid: bde4e0ee-fba3-4813-a849-27049323d301
uid: core/miscellaneous/collations-and-case-sensitivity
ms.openlocfilehash: 46a13d341c1b721bb243ee2b205bdc2f4d7e7aee
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526449"
---
# <a name="collations-and-case-sensitivity"></a>Sortowanie i rozróżnianie wielkości liter

> [!NOTE]
> Ta funkcja jest wprowadzana w EF Core 5,0.

Przetwarzanie tekstu w bazach danych może być złożone i wymaga większej uwagi użytkownika, że jedna z nich będzie podejrzana. W jednym z baz danych różnią się w zależności od sposobu obsługi tekstu; na przykład w przypadku niektórych baz danych domyślnie uwzględniana jest wielkość liter (np. SQLite, PostgreSQL), inne nie uwzględniają wielkości liter (SQL Server, MySQL). Ponadto ze względu na użycie indeksu, uwzględnianie wielkości liter i podobne aspekty mogą mieć znacznie idący wpływ na wydajność zapytań: Chociaż może być skłonny do użycia w `string.Lower` celu wymuszenia porównania bez uwzględniania wielkości liter w bazie danych z uwzględnieniem wielkości liter, może to uniemożliwić aplikacji używanie indeksów. Ta strona zawiera szczegółowe informacje dotyczące konfigurowania uwzględniania wielkości liter lub bardziej ogólnych, sortowanych i sposobu wykonywania tych czynności w skuteczny sposób bez naruszania wydajności zapytań.

## <a name="introduction-to-collations"></a>Wprowadzenie do sortowania

Podstawową koncepcją przetwarzania tekstu jest *Sortowanie*, które jest zestawem reguł określających, w jaki sposób wartości tekstowe są uporządkowane i porównywane pod kątem równości. Na przykład, podczas sortowania bez uwzględniania wielkości liter nie są rozróżniane wielkie i małe litery dla celów porównania równości, sortowanie uwzględniające wielkość liter nie jest dozwolone. Jednak ze względu na wielkość liter jest rozróżniana kulturami (np. `i` `I` reprezentuje różne litery w turecki), istnieje wiele nierozróżniających wielkości liter sortowanie, z których każdy ma własny zestaw reguł. Zakres sortowania jest również rozciągany poza wielkość liter, do innych aspektów danych znakowych; na przykład w języku niemieckim jest to czasami (ale nie zawsze) pożądane do traktowania `ä` i `ae` tak samo. Ponadto sortowania definiują sposób *uporządkowania*wartości tekstowych: w przypadku niemieckich miejsc `ä` po `a` , szwedzki umieszcza je na końcu alfabetu.

Wszystkie operacje tekstowe w bazie danych używają sortowania — określa, czy jawnie lub niejawnie — aby określić, w jaki sposób operacja porównuje i porządkuje ciągi. Rzeczywista lista dostępnych ustawień sortowania i ich schemat nazewnictwa jest specyficzna dla bazy danych; Zapoznaj się [z sekcją poniżej,](#database-specific-information) Aby uzyskać łącza do odpowiednich stron dokumentacji różnych baz danych. Na szczęście baza danych zwykle zezwala na Definiowanie sortowania domyślnego na poziomie bazy danych lub kolumny oraz jawnie określać sortowanie, które ma być używane dla określonych operacji w zapytaniu.

## <a name="database-collation"></a>Sortowanie bazy danych

W większości systemów baz danych sortowanie domyślne jest definiowane na poziomie bazy danych; Jeśli nie zostanie zastąpione, sortowanie niejawnie stosuje się do wszystkich operacji tekstowych występujących w tej bazie danych. Sortowanie bazy danych jest zazwyczaj ustawiane podczas tworzenia bazy danych (za pośrednictwem `CREATE DATABASE` instrukcji DDL), a jeśli nie zostanie określony, wartością domyślną jest niektóre wartości na poziomie serwera, które są określane podczas instalacji. Na przykład domyślne sortowanie na poziomie serwera w SQL Server to, w `SQL_Latin1_General_CP1_CI_AS` którym jest rozróżniana wielkość liter, sortowanie z uwzględnieniem akcentów. Mimo że systemy baz danych zwykle umożliwiają zmianę sortowania istniejącej bazy danych, może to prowadzić do komplikacji; zaleca się wybranie sortowania przed utworzeniem bazy danych.

W przypadku korzystania z EF Core migracji w celu zarządzania schematem bazy danych następujące metody modelu umożliwiają `OnModelCreating` skonfigurowanie SQL Serverj bazy danych do używania sortowania z uwzględnieniem wielkości liter:

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?range=40)]

## <a name="column-collation"></a>Sortowanie kolumn

Sortowanie można także definiować w kolumnach tekstowych, zastępując domyślną bazę danych. Może to być przydatne, jeśli niektóre kolumny muszą mieć wielkość liter, podczas gdy pozostała część bazy danych wymaga uwzględnienia wielkości liter.

W przypadku korzystania z EF Core migracji w celu zarządzania schematem bazy danych, poniżej konfiguruje w kolumnie Właściwość uwzględnianie wielkości liter `Name` w bazie danych, która w przeciwnym razie jest zależna od wielkości liter:

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?name=OnModelCreating&highlight=6)]

## <a name="explicit-collation-in-a-query"></a>Jawne sortowanie w zapytaniu

W niektórych przypadkach ta sama kolumna musi być kwerendą przy użyciu różnych sortowania według różnych zapytań. Na przykład jedno zapytanie może wymagać wykonania porównania z uwzględnieniem wielkości liter w kolumnie, podczas gdy inna może wymagać wykonania porównania bez uwzględniania wielkości liter w tej samej kolumnie. W tym celu można jawnie określić sortowanie w ramach zapytania:

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?name=SimpleQueryCollation)]

Spowoduje to wygenerowanie `COLLATE` w zapytaniu SQL klauzuli, która stosuje sortowanie z uwzględnieniem wielkości liter niezależnie od sortowania zdefiniowanego na poziomie kolumny lub bazy danych:

```sql
SELECT [c].[Id], [c].[Name]
FROM [Customers] AS [c]
WHERE [c].[Name] COLLATE SQL_Latin1_General_CP1_CS_AS = N'John'
```

### <a name="explicit-collations-and-indexes"></a>Jawne sortowanie i indeksy

Indeksy są jednym z najważniejszych czynników wydajności bazy danych — zapytanie, które działa wydajnie z indeksem, może zostać rozdrobne bez tego indeksu. Indeksy niejawnie dziedziczą sortowanie kolumn; oznacza to, że wszystkie zapytania w kolumnie są automatycznie uprawnione do używania indeksów zdefiniowanych w tej kolumnie — pod warunkiem, że zapytanie nie określa innego sortowania. Określenie jawnego sortowania w zapytaniu zwykle uniemożliwi użycie przez zapytanie indeksu zdefiniowanego w tej kolumnie, ponieważ sortowania nie będą już zgodne; w związku z tym zalecane jest zachowanie ostrożności podczas korzystania z tej funkcji. Zawsze zaleca się zdefiniowanie sortowania na poziomie kolumny (lub bazy danych), co pozwala wszystkim zapytaniom niejawnie używać tego sortowania i korzystać z dowolnego indeksu.

Należy pamiętać, że niektóre bazy danych umożliwiają zdefiniowanie sortowania podczas tworzenia indeksu (np. PostgreSQL, SQLite). Pozwala to na zdefiniowanie wielu indeksów w tej samej kolumnie i przyspieszenie operacji z różnymi sortowaniami (np. w przypadku uwzględniania wielkości liter i porównań bez uwzględniania wielkości liter). Aby uzyskać więcej informacji, zapoznaj się z dokumentacją dostawcy bazy danych.

> [!WARNING]
> Zawsze sprawdzaj plany zapytań w zapytaniach i upewnij się, że odpowiednie indeksy są używane w kwerendach o krytycznym znaczeniu dla wydajności wykonywanych w przypadku dużych ilości danych. Zastępowanie wielkości liter w zapytaniach za pośrednictwem `EF.Functions.Collate` (lub przez wywoływanie `string.ToLower` ) może mieć znaczący wpływ na wydajność aplikacji.

## <a name="translation-of-built-in-net-string-operations"></a>Tłumaczenie wbudowanych operacji na ciągach .NET

W przypadku platformy .NET równość w postaci ciągów jest domyślnie rozróżniana: `s1 == s2` wykonuje porównanie porządkowe, które wymaga, aby ciągi były takie same. Ponieważ domyślne sortowanie baz danych jest różne i dlatego pożądane jest proste równość używania indeksów, EF Core nie podejmuje próby przetłumaczenia prostej równości na operacje z uwzględnieniem wielkości liter w bazie danych: równość języka C# jest tłumaczona bezpośrednio do poziomu równości SQL, co może być zależne od wielkości liter, w zależności od określonej używanej bazy danych i jej konfiguracji sortowania.

Ponadto platforma .NET zapewnia przeciążenia [`string.Equals`](/dotnet/api/system.string.equals#System_String_Equals_System_String_System_StringComparison_) akceptujące [`StringComparison`](/dotnet/api/system.stringcomparison) Wyliczenie, które umożliwia określanie uwzględniania wielkości liter i kultury dla porównania. Zgodnie z projektem, EF Core zaniechania tłumaczenia tych przeciążeń na SQL i próba ich użycia spowoduje wyjątek. Dla jednego z tych EF Core nie wie, w jakim przypadku należy użyć sortowania bez uwzględniania wielkości liter lub wielkości liter. Co ważniejsze, zastosowanie sortowania w większości przypadków uniemożliwi użycie indeksu, znacząco wpływając na wydajność dla bardzo podstawowej i powszechnie używanej konstrukcji platformy .NET. Aby wymusić użycie porównania z rozróżnianiem wielkości liter lub bez uwzględniania wielkości liter, określ sortowanie jawnie za pomocą podanego `EF.Functions.Collate` [powyżej](#explicit-collations-and-indexes).

## <a name="database-specific-information"></a>Informacje specyficzne dla bazy danych

* [SQL Server dokumentacja dotycząca sortowania](/sql/relational-databases/collations/collation-and-unicode-support).
* [Dokumentacja Microsoft. Data. sqlite dotycząca sortowania](/dotnet/standard/data/sqlite/collation).
* [PostgreSQL dokumentację dotyczącą sortowania](https://www.postgresql.org/docs/current/collation.html).
* [Dokumentacja bazy danych MySQL dotycząca sortowania](https://dev.mysql.com/doc/refman/en/charset-general.html).
