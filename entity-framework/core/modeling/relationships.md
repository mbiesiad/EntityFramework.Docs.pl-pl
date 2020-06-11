---
title: Relacje — EF Core
description: Jak skonfigurować relacje między typami jednostek podczas korzystania z Entity Framework Core
author: AndriySvyryd
ms.date: 11/21/2019
uid: core/modeling/relationships
ms.openlocfilehash: 3ed25b752970cf0f55d38f1ce5b8383f1c90edc3
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664146"
---
# <a name="relationships"></a>Relacje

Relacja określa, jak dwie jednostki są powiązane ze sobą. W relacyjnej bazie danych jest to reprezentowane przez ograniczenie klucza obcego.

> [!NOTE]  
> Większość przykładów w tym artykule używa relacji jeden-do-wielu, aby przedstawić koncepcje. Przykłady relacji jeden-do-jednego i wiele-do-wielu można znaleźć w sekcji [inne wzorce relacji](#other-relationship-patterns) na końcu artykułu.

## <a name="definition-of-terms"></a>Definicja warunków

Istnieje kilka terminów używanych do opisywania relacji

* **Jednostka zależna:** Jest to jednostka, która zawiera właściwości klucza obcego. Czasami określana jako "podrzędny" relacji.

* **Podmiot podmiotu zabezpieczeń:** Jest to jednostka, która zawiera właściwości klucza podstawowego/alternatywnego. Czasami określana jako "Parent" relacji.

* **Klucz podmiotu zabezpieczeń:** Właściwości, które jednoznacznie identyfikują jednostkę podmiotu zabezpieczeń. Może to być klucz podstawowy lub klucz alternatywny.

* **Klucz obcy:** Właściwości w jednostce zależnej, które są używane do przechowywania wartości klucza podmiotu zabezpieczeń powiązanej jednostki.

* **Właściwość nawigacji:** Właściwość zdefiniowana dla podmiotu zabezpieczeń i/lub zależnego, która odwołuje się do powiązanej jednostki.

  * **Właściwość nawigacji kolekcji:** Właściwość nawigacji, która zawiera odwołania do wielu powiązanych jednostek.

  * **Właściwość nawigacji odwołania:** Właściwość nawigacji, która zawiera odwołanie do pojedynczej powiązanej jednostki.

  * **Właściwość nawigacji odwrotnej:** Omawiając konkretną właściwość nawigacji, ten termin odnosi się do właściwości nawigacji na drugim końcu relacji.
  
* **Relacja odwołująca się do samego siebie:** Relacja, w której zależne i główne typy jednostek są takie same.

Poniższy kod przedstawia relację "jeden do wielu" między `Blog` i`Post`

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs#Full)]

* `Post`jest jednostką zależną

* `Blog`jest jednostką główną

* `Blog.BlogId`jest kluczem podmiotu zabezpieczeń (w tym przypadku jest kluczem podstawowym zamiast alternatywnym kluczem)

* `Post.BlogId`jest kluczem obcym

* `Post.Blog`jest właściwością nawigacji referencyjnej

* `Blog.Posts`jest właściwością nawigacji kolekcji

* `Post.Blog`jest właściwością nawigacji odwrotnej `Blog.Posts` (i odwrotnie)

## <a name="conventions"></a>Konwencje

Domyślnie relacja zostanie utworzona, gdy w typie zostanie wykryta właściwość nawigacji. Właściwość jest uznawana za właściwość nawigacji, jeśli typ wskazujący nie może być mapowany jako typ skalarny przez bieżącego dostawcę bazy danych.

> [!NOTE]  
> Relacje, które są odnajdywane według Konwencji, będą zawsze ukierunkowane na klucz podstawowy jednostki głównej. Aby określić klucz alternatywny, należy przeprowadzić dodatkową konfigurację przy użyciu interfejsu API Fluent.

### <a name="fully-defined-relationships"></a>W pełni zdefiniowane relacje

Najbardziej typowym wzorcem relacji jest posiadanie właściwości nawigacji zdefiniowanych na obu końcach relacji i właściwości klucza obcego zdefiniowanej w klasie jednostki zależnej.

* Jeśli para właściwości nawigacji zostanie znaleziona między dwoma typami, zostaną one skonfigurowane jako właściwości nawigacji odwrotnej tej samej relacji.

* Jeśli jednostka zależna zawiera właściwość o nazwie zgodnej z jednym z tych wzorców, zostanie ona skonfigurowana jako klucz obcy:
  * `<navigation property name><principal key property name>`
  * `<navigation property name>Id`
  * `<principal entity name><principal key property name>`
  * `<principal entity name>Id`

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs?name=Full&highlight=6,15-16)]

W tym przykładzie wyróżnione właściwości zostaną użyte do skonfigurowania relacji.

> [!NOTE]
> Jeśli właściwość jest kluczem podstawowym lub jest typu niezgodnego z kluczem podmiotu zabezpieczeń, nie zostanie on skonfigurowany jako klucz obcy.

> [!NOTE]
> Przed EF Core 3,0 właściwość o nazwie dokładnie taka sama jak właściwość klucza głównego [była również zgodna jako klucz obcy](https://github.com/aspnet/EntityFrameworkCore/issues/13274)

### <a name="no-foreign-key-property"></a>Brak właściwości klucza obcego

Chociaż zaleca się zdefiniowanie właściwości klucza obcego w klasie jednostki zależnej, nie jest to wymagane. Jeśli właściwość klucza obcego nie zostanie znaleziona, [Właściwość klucza obcego cienia](shadow-properties.md) zostanie wprowadzona przy użyciu nazwy `<navigation property name><principal key property name>` lub `<principal entity name><principal key property name>` Jeśli w typie zależnym nie ma żadnej nawigacji.

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/NoForeignKey.cs?name=NoForeignKey&highlight=6,15)]

W tym przykładzie klucz obcy Shadow jest, `BlogId` ponieważ oczekiwano, że nazwa nawigacji będzie nadmiarowa.

> [!NOTE]
> Jeśli właściwość o tej samej nazwie już istnieje, nazwa właściwości cienia zostanie poddana sufiksem liczbowym.

### <a name="single-navigation-property"></a>Właściwość pojedynczej nawigacji

Dołączenie tylko jednej właściwości nawigacji (bez nawigacji odwrotnej i brak właściwości klucza obcego) nie wystarcza do zdefiniowania relacji przez Konwencję. Można również mieć pojedynczą właściwość nawigacji i właściwość klucza obcego.

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneNavigation.cs?name=OneNavigation&highlight=6)]

### <a name="limitations"></a>Ograniczenia

W przypadku zdefiniowania wielu właściwości nawigacji między dwoma typami (oznacza to, że więcej niż tylko jedna para nawigacji wskazuje na siebie) relacje reprezentowane przez właściwości nawigacji są niejednoznaczne. Należy ręcznie skonfigurować je, aby usunąć niejednoznaczność.

### <a name="cascade-delete"></a>Usuwanie kaskadowe

Według Konwencji kaskadowe usuwanie zostanie ustawione na *kaskadowe* dla wymaganych relacji i *ClientSetNull* dla relacji opcjonalnych. *Kaskadowo* oznacza również usunięcie jednostek zależnych. *ClientSetNull* oznacza, że jednostki zależne, które nie są ładowane do pamięci, pozostaną bez zmian i muszą zostać ręcznie usunięte lub zaktualizowane, aby wskazywały prawidłową jednostkę podmiotu zabezpieczeń. W przypadku jednostek, które są ładowane do pamięci, EF Core podejmie próbę ustawienia właściwości klucza obcego na wartość null.

Zapoznaj się z sekcją [wymagane i opcjonalne relacje](#required-and-optional-relationships) , aby uzyskać różnicę między wymaganymi i opcjonalnymi relacjami.

Zobacz [Kaskada Delete](../saving/cascade-delete.md) , aby uzyskać więcej informacji na temat różnych zachowań usuwania i ustawień domyślnych używanych przez Konwencję.

## <a name="manual-configuration"></a>Konfiguracja ręczna

### <a name="fluent-api"></a>[Interfejs API Fluent](#tab/fluent-api)

Aby skonfigurować relację w interfejsie API Fluent, należy rozpocząć od zidentyfikowania właściwości nawigacji, które tworzą relację. `HasOne`lub `HasMany` identyfikuje właściwość nawigacji dla typu jednostki, w którym rozpoczyna się konfiguracja. Następnie utworzysz łańcuch wywołań lub, `WithOne` `WithMany` Aby zidentyfikować nawigację odwrotną. `HasOne`/`WithOne`są używane do właściwości nawigacji referencyjnej i `HasMany` / `WithMany` są używane dla właściwości nawigacji kolekcji.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoForeignKey.cs?name=NoForeignKey&highlight=8-10)]

### <a name="data-annotations"></a>[Adnotacje danych](#tab/data-annotations)

Możesz użyć adnotacji danych, aby skonfigurować sposób pary właściwości nawigacji dla jednostek zależnych i głównych. Jest to zazwyczaj wykonywane, gdy istnieje więcej niż jedna para właściwości nawigacji między dwoma typami jednostek.

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/InverseProperty.cs?name=InverseProperty&highlight=20,23)]

> [!NOTE]
> Możesz użyć [Required] właściwości w jednostce zależnej, aby mieć wpływ na wymaganą relację. [Wymagane] w nawigacji z jednostki głównej jest zwykle ignorowany, ale może spowodować, że jednostka stanie się jednostką zależną.

> [!NOTE]
> Adnotacje danych `[ForeignKey]` i `[InverseProperty]` są dostępne w `System.ComponentModel.DataAnnotations.Schema` przestrzeni nazw. `[Required]`jest dostępny w `System.ComponentModel.DataAnnotations` przestrzeni nazw.

---

### <a name="single-navigation-property"></a>Właściwość pojedynczej nawigacji

Jeśli masz tylko jedną właściwość nawigacji, istnieją nadmierne przeciążenia parametrów `WithOne` i `WithMany` . Oznacza to, że istnieje koncepcyjnie odwołanie lub kolekcja na drugim końcu relacji, ale nie ma żadnej właściwości nawigacji zawartej w klasie Entity.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneNavigation.cs?name=OneNavigation&highlight=8-10)]

---

### <a name="configuring-navigation-properties"></a>Konfigurowanie właściwości nawigacji

Po utworzeniu właściwości nawigacji może być konieczne jej dalsze skonfigurowanie. W programie EFCore 5,0 dodano nowy interfejs API Fluent, który umożliwia wykonanie tej konfiguracji.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NavigationConfiguration.cs?name=NavigationConfiguration&highlight=7-9)]

> [!NOTE]
> Tego wywołania nie można użyć do utworzenia właściwości nawigacji. Służy tylko do konfigurowania właściwości nawigacji, która została wcześniej utworzona przez zdefiniowanie relacji lub Konwencji.

### <a name="foreign-key"></a>Klucz obcy

#### <a name="fluent-api-simple-key"></a>[Interfejs API Fluent (klucz prosty)](#tab/fluent-api-simple-key)

Korzystając z interfejsu API Fluent, można skonfigurować właściwość, która powinna być używana jako właściwość klucza obcego dla danej relacji:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ForeignKey.cs?name=ForeignKey&highlight=11)]

#### <a name="fluent-api-composite-key"></a>[Interfejs API Fluent (klucz złożony)](#tab/fluent-api-composite-key)

Korzystając z interfejsu API Fluent, można skonfigurować właściwości, które mają być używane jako złożone właściwości klucza obcego dla danej relacji:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositeForeignKey.cs?name=CompositeForeignKey&highlight=13)]

#### <a name="data-annotations-simple-key"></a>[Adnotacje danych (klucz prosty)](#tab/data-annotations-simple-key)

Możesz użyć adnotacji danych, aby skonfigurować właściwość, która powinna być używana jako właściwość klucza obcego dla danej relacji. Zwykle jest to wykonywane, gdy właściwość klucza obcego nie zostanie odnaleziona według Konwencji:

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/ForeignKey.cs?name=ForeignKey&highlight=17)]

> [!TIP]  
> `[ForeignKey]`Adnotację można umieścić na każdej właściwości nawigacji w relacji. Nie musi ona być zgodna z właściwością nawigacji w klasie jednostki zależnej.

> [!NOTE]
> Właściwość określona przy użyciu `[ForeignKey]` właściwości nawigacji nie musi istnieć w typie zależnym. W takim przypadku określona nazwa zostanie użyta do utworzenia klucza obcego cienia.

---

#### <a name="shadow-foreign-key"></a>Obcy klucz cienia

Możesz użyć przeciążenia ciągu, `HasForeignKey(...)` Aby skonfigurować właściwość Shadow jako klucz obcy (Aby uzyskać więcej informacji, zobacz [Właściwości cienia](shadow-properties.md) ). Zalecamy jawne dodanie właściwości Shadow do modelu przed użyciem go jako klucza obcego (jak pokazano poniżej).

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ShadowForeignKey.cs?name=ShadowForeignKey&highlight=10,16)]

#### <a name="foreign-key-constraint-name"></a>Nazwa ograniczenia klucza obcego

Zgodnie z Konwencją, podczas określania relacyjnej bazy danych ograniczenia klucza obcego mają nazwę FK_ <dependent type name> _<principal type name>_ <foreign key property name> . W przypadku złożonych kluczy obcych <foreign key property name> jest rozdzielana podkreśleniem lista nazw właściwości klucza obcego.

Nazwę ograniczenia można również skonfigurować w następujący sposób:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ConstraintName.cs?name=ConstraintName&highlight=6-7)]

### <a name="without-navigation-property"></a>Bez właściwości nawigacji

Nie trzeba podawać właściwości nawigacji. Możesz po prostu podać klucz obcy po jednej stronie relacji.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoNavigation.cs?name=NoNavigation&highlight=8-11)]

### <a name="principal-key"></a>Klucz podmiotu zabezpieczeń

Jeśli chcesz, aby klucz obcy odwołuje się do właściwości innej niż klucz podstawowy, możesz użyć interfejsu API Fluent, aby skonfigurować właściwość klucza podmiotu zabezpieczeń. Właściwość, którą konfigurujesz jako klucz podmiotu zabezpieczeń, zostanie automatycznie skonfigurowana jako [klucz alternatywny](alternate-keys.md).

#### <a name="simple-key"></a>[Klucz prosty](#tab/simple-key)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/PrincipalKey.cs?name=PrincipalKey&highlight=11)]

#### <a name="composite-key"></a>[Klucz złożony](#tab/composite-key)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositePrincipalKey.cs?name=CompositePrincipalKey&highlight=11)]

> [!WARNING]  
> Kolejność, w której określane są właściwości klucza podmiotu zabezpieczeń, musi być zgodna z kolejnością, w której są określone dla klucza obcego.

---

### <a name="required-and-optional-relationships"></a>Wymagane i opcjonalne relacje

Korzystając z interfejsu API Fluent, można określić, czy relacja jest wymagana, czy opcjonalna. Ostatecznie to kontroluje, czy właściwość klucza obcego jest wymagana czy opcjonalna. Jest to najbardziej przydatne w przypadku używania klucza obcego stanu w tle. Jeśli masz właściwość klucza obcego w klasie jednostki, wymagana jest konieczność relacji w zależności od tego, czy właściwość klucza obcego jest wymagana, czy opcjonalna (zobacz [Właściwości wymagane i opcjonalne,](required-optional.md) Aby uzyskać więcej informacji).

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/Required.cs?name=Required&highlight=6)]

> [!NOTE]
> Wywołanie `IsRequired(false)` powoduje również, że właściwość klucza obcego jest opcjonalna, chyba że została skonfigurowana inaczej.

### <a name="cascade-delete"></a>Usuwanie kaskadowe

Korzystając z interfejsu API Fluent, można jawnie skonfigurować zachowanie kaskadowego usuwania dla danej relacji.

Szczegółowe omówienie każdej z tych opcji można znaleźć w temacie [usuwanie kaskadowe](../saving/cascade-delete.md) .

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CascadeDelete.cs?name=CascadeDelete&highlight=6)]

## <a name="other-relationship-patterns"></a>Inne wzorce relacji

### <a name="one-to-one"></a>Jeden do jednego

Relacja jeden do jednego ma właściwość nawigacji odwołania po obu stronach. Są one zgodne z tymi samymi konwencjami co relacje jeden-do-wielu, ale na właściwości klucza obcego jest wprowadzany unikatowy indeks, aby upewnić się, że tylko jedna z nich jest zależna od każdego podmiotu zabezpieczeń.

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneToOne.cs?name=OneToOne&highlight=6,15-16)]

> [!NOTE]  
> Dr wybierze jedną z jednostek, która będzie zależna od możliwości wykrywania właściwości klucza obcego. W przypadku wybrania niewłaściwej jednostki jako zależnej można użyć interfejsu API Fluent, aby rozwiązać ten problem.

Podczas konfigurowania relacji z interfejsem API Fluent należy używać `HasOne` `WithOne` metod i.

Podczas konfigurowania klucza obcego należy określić typ podmiotu zależnego — Zwróć uwagę na parametr ogólny podany `HasForeignKey` w poniższej liście. W relacji jeden-do-wielu jest jasne, że jednostka z nawigacją referencyjną jest zależna, a jeden z kolekcją jest podmiotem zabezpieczeń. Nie jest to jednak w relacji jeden-do-jednego — dlatego trzeba ją jawnie zdefiniować.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneToOne.cs?name=OneToOne&highlight=11)]

### <a name="many-to-many"></a>Wiele do wielu

Relacje wiele-do-wielu bez klasy Entity do reprezentowania tabeli sprzężenia nie są jeszcze obsługiwane. Istnieje jednak możliwość reprezentowania relacji wiele-do-wielu poprzez dołączenie klasy jednostki dla tabeli sprzężenia i mapowanie dwóch oddzielnych relacji jeden-do-wielu.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToMany.cs?name=ManyToMany&highlight=11-14,16-19,39-46)]
