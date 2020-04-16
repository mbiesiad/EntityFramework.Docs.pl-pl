---
title: Typy jednostek bezkluzynowych — EF Core
description: Jak skonfigurować typy jednostek bezkluczek przy użyciu programu Entity Framework Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 9/13/2019
uid: core/modeling/keyless-entity-types
ms.openlocfilehash: 496e1e8983ba2d5e15dbee02607ea3f2c861503e
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434217"
---
# <a name="keyless-entity-types"></a>Typy jednostek bez kluczy

> [!NOTE]
> Ta funkcja została dodana w EF Core 2.1 pod nazwą typów zapytań. W EF Core 3.0 koncepcja została zmieniona na typy jednostek bezkluzywowych.

Oprócz zwykłych typów jednostek model EF Core może zawierać _typy jednostek bezkluzywowych_, które mogą być używane do przeprowadzania zapytań bazy danych względem danych, które nie zawierają wartości klucza.

## <a name="defining-keyless-entity-types"></a>Definiowanie typów encji bezkluzywowych

Typy jednostek bezkluzyfów można zdefiniować za pomocą adnotacji danych lub interfejsu API Fluent:

### <a name="data-annotations"></a>[Adnotacje danych](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Keyless.cs?Name=Keyless&highlight=1)]

### <a name="fluent-api"></a>[Płynne API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Keyless.cs?Name=Keyless&highlight=4)]

***

## <a name="keyless-entity-types-characteristics"></a>Właściwości typów encji bezkluzytowych

Typy jednostek bezklucików obsługują wiele z tych samych możliwości mapowania, co zwykłe typy jednostek, takie jak mapowanie dziedziczenia i właściwości nawigacji. W magazynach relacyjnych można skonfigurować obiekty bazy danych docelowej i kolumny za pomocą płynnych metod interfejsu API lub adnotacji danych.

Jednak różnią się one od zwykłych typów jednostek, ponieważ:

- Nie można zdefiniować klucza.
- Nigdy nie są śledzone pod kątem zmian w _DbContext_ i dlatego nigdy nie są wstawiane, aktualizowane lub usuwane w bazie danych.
- Nigdy nie są odkrywane przez konwencję.
- Obsługuje tylko podzbiór możliwości mapowania nawigacji, w szczególności:
  - Nigdy nie mogą działać jako główny koniec związku.
  - Mogą nie mieć nawigacji do posiadanych podmiotów
  - Mogą one zawierać tylko właściwości nawigacji odwołania wskazujące na regularne jednostki.
  - Jednostki nie mogą zawierać właściwości nawigacji do typów jednostek bezkluczek.
- Należy skonfigurować z `[Keyless]` adnotacją danych `.HasNoKey()` lub wywołaniem metody.
- Może być mapowany na _kwerendę definiującą_. Kwerenda definiująca to kwerenda zadeklarowana w modelu, która działa jako źródło danych dla typu jednostki bezkluczynowej.

## <a name="usage-scenarios"></a>Scenariusze użytkowania

Niektóre z głównych scenariuszy użycia dla typów jednostek bezkluczek są:

- Służąc jako typ zwracany dla [nieprzetworzonych zapytań SQL](xref:core/querying/raw-sql).
- Mapowanie do widoków bazy danych, które nie zawierają klucza podstawowego.
- Mapowanie do tabel, które nie mają zdefiniowanego klucza podstawowego.
- Mapowanie do zapytań zdefiniowanych w modelu.

## <a name="mapping-to-database-objects"></a>Mapowanie do obiektów bazy danych

Mapowanie typu jednostki bezkluzyfów `ToTable` do `ToView` obiektu bazy danych jest osiągane przy użyciu lub płynnego interfejsu API. Z punktu widzenia EF Core obiekt bazy danych określony w tej metodzie jest _widok_, co oznacza, że jest traktowany jako źródło kwerendy tylko do odczytu i nie może być celem operacji aktualizacji, wstawiania lub usuwania. Nie oznacza to jednak, że obiekt bazy danych jest faktycznie wymagany do widoku bazy danych. Alternatywnie może to być tabela bazy danych, która będzie traktowana jako tylko do odczytu. Z drugiej strony dla zwykłych typów jednostek EF Core `ToTable` zakłada, że obiekt bazy danych określony w metodzie może być traktowany jako _tabela,_ co oznacza, że może służyć jako źródło kwerendy, ale także kierowane przez operacje aktualizacji, usuwania i wstawiania. W rzeczywistości można określić nazwę widoku `ToTable` bazy danych w i wszystko powinno działać poprawnie, tak długo, jak widok jest skonfigurowany do aktualizacji w bazie danych.

> [!NOTE]
> `ToView`zakłada, że obiekt już istnieje w bazie danych i nie zostanie utworzony przez migracje.

## <a name="example"></a>Przykład

W poniższym przykładzie pokazano, jak używać typów jednostek bezkluzytowych do wykonywania zapytań o widok bazy danych.

> [!TIP]
> Możesz wyświetlić [ten](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes) przykład artykułu na GitHub.

Najpierw definiujemy prosty model bloga i postu:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Entities)]

Następnie definiujemy prosty widok bazy danych, który pozwoli nam zbadać liczbę wpisów skojarzonych z każdym blogiem:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#View)]

Następnie definiujemy klasę do przechowywania wyniku z widoku bazy danych:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#KeylessEntityType)]

Następnie konfigurujemy typ jednostki bezkluzywowe `HasNoKey` w _OnModelCreating_ przy użyciu interfejsu API.
Używamy płynnego interfejsu API konfiguracji, aby skonfigurować mapowanie dla typu jednostki bezkluzyfowej:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Configuration)]

Następnie konfigurujemy, `DbContext` aby `DbSet<T>`uwzględnić:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#DbSet)]

Na koniec możemy zbadać widok bazy danych w standardowy sposób:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Query)]

> [!TIP]
> Należy zauważyć, że zdefiniowaliśmy również właściwość kwerendy na poziomie kontekstu (DbSet), aby działać jako katalog główny dla zapytań dotyczących tego typu.
