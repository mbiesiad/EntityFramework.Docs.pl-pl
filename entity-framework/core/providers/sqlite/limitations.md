---
title: Dostawca bazy danych programu SQLite — ograniczenia — EF Core
author: bricelam
ms.date: 07/16/2020
ms.assetid: 94ab4800-c460-4caa-a5e8-acdfee6e6ce2
uid: core/providers/sqlite/limitations
ms.openlocfilehash: 393f5e80ce2e11dcb11c2048e06effa27e48dc13
ms.sourcegitcommit: d85263b5d5d665dbaf94de8832e2917bce048b34
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2020
ms.locfileid: "86451232"
---
# <a name="sqlite-ef-core-database-provider-limitations"></a>Ograniczenia dostawcy bazy danych EF Core SQLite

Dostawca oprogramowania SQLite ma wiele ograniczeń migracji. Większość z tych ograniczeń jest wynikiem ograniczeń w źródłowym aparacie bazy danych programu SQLite i nie są specyficzne dla EF.

## <a name="modeling-limitations"></a>Ograniczenia modelowania

Wspólna Biblioteka relacyjna (współdzielona przez Entity Framework dostawcy relacyjnej bazy danych) definiuje interfejsy API dla koncepcji modelowania wspólnych dla większości aparatów relacyjnej bazy danych. Niektóre z tych pojęć nie są obsługiwane przez dostawcę oprogramowania SQLite.

* Schematy
* Sekwencje

## <a name="query-limitations"></a>Ograniczenia zapytania

SQLite nie obsługuje natywnie następujących typów danych. EF Core mogą odczytywać i zapisywać wartości tych typów, a także wysyłać zapytania o równość ( `where e.Property == value` ). Inne operacje, takie jak porównywanie i porządkowanie, będą wymagały oceny klienta.

* DateTimeOffset
* Wartość dziesiętna
* przedział_czasu
* UInt64

Zamiast tego `DateTimeOffset` zalecamy używanie wartości DateTime. W przypadku obsługi wielu stref czasowych zaleca się przekonwertowanie wartości na czas UTC przed zapisaniem, a następnie przekonwertowanie z powrotem do odpowiedniej strefy czasowej.

`Decimal`Typ zapewnia wysoki poziom precyzji. Jeśli nie potrzebujesz tego poziomu dokładności, zalecamy użycie funkcji Double. Możesz użyć [konwertera wartości](../../modeling/value-conversions.md) , aby dalej używać miejsc dziesiętnych w klasach.

``` csharp
modelBuilder.Entity<MyEntity>()
    .Property(e => e.DecimalProperty)
    .HasConversion<double>();
```

## <a name="migrations-limitations"></a>Ograniczenia migracji

Aparat bazy danych programu SQLite nie obsługuje wielu operacji schematu, które są obsługiwane przez większość innych relacyjnych baz danych. Jeśli podjęto próbę zastosowania jednej z nieobsługiwanych operacji do bazy danych programu SQLite, `NotSupportedException` zostanie zgłoszony.

Zostanie podjęta próba ponownego kompilowania w celu wykonania pewnych operacji. Ponowne kompilacje są możliwe tylko dla artefaktów bazy danych będących częścią modelu EF Core. Jeśli artefakt bazy danych nie jest częścią modelu — na przykład, jeśli został on utworzony ręcznie wewnątrz migracji, a następnie `NotSupportedException` nadal jest generowany.

| Operacja            | Obsługiwane?  | Wymaga wersji |
|:---------------------|:------------|:-----------------|
| AddCheckConstraint   | ✔ (Skompiluj ponownie) | 5.0              |
| AddColumn            | ✔           | 1.0              |
| AddForeignKey        | ✔ (Skompiluj ponownie) | 5.0              |
| Addprimarykey        | ✔ (Skompiluj ponownie) | 5.0              |
| AddUniqueConstraint  | ✔ (Skompiluj ponownie) | 5.0              |
| AlterColumn          | ✔ (Skompiluj ponownie) | 5.0              |
| Indeksowanie          | ✔           | 1.0              |
| Utwórz          | ✔           | 1.0              |
| DropCheckConstraint  | ✔ (Skompiluj ponownie) | 5.0              |
| DropColumn           | ✔ (Skompiluj ponownie) | 5.0              |
| DropForeignKey       | ✔ (Skompiluj ponownie) | 5.0              |
| DropIndex            | ✔           | 1.0              |
| DropPrimaryKey       | ✔ (Skompiluj ponownie) | 5.0              |
| Lista rozwijana            | ✔           | 1.0              |
| DropUniqueConstraint | ✔ (Skompiluj ponownie) | 5.0              |
| RenameColumn         | ✔           | 2.2.2            |
| RenameIndex          | ✔ (Skompiluj ponownie) | 2.1              |
| Nazwa Rename          | ✔           | 1.0              |
| EnsureSchema         | ✔ (No-op)   | 2,0              |
| DropSchema           | ✔ (No-op)   | 2,0              |
| Insert               | ✔           | 2,0              |
| Aktualizacja               | ✔           | 2,0              |
| Usuń               | ✔           | 2,0              |

## <a name="migrations-limitations-workaround"></a>Obejście ograniczeń dla migracji

Niektóre z tych ograniczeń można obejść, ręcznie pisząc kod w migracjach, aby przeprowadzić ponowną kompilację. Rekompilacje tabeli obejmują tworzenie nowej tabeli, kopiowanie danych do nowej tabeli, usuwanie starej tabeli, zmiana nazwy nowej tabeli. Aby `Sql(string)` wykonać niektóre z tych kroków, należy użyć metody.

Aby uzyskać więcej informacji, zobacz artykuł [Tworzenie innych rodzajów zmian schematu tabeli](https://sqlite.org/lang_altertable.html#otheralter) w dokumentacji oprogramowania SQLite.
