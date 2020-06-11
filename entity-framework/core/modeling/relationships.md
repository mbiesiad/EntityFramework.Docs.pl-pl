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
# <a name="relationships"></a><span data-ttu-id="903eb-103">Relacje</span><span class="sxs-lookup"><span data-stu-id="903eb-103">Relationships</span></span>

<span data-ttu-id="903eb-104">Relacja określa, jak dwie jednostki są powiązane ze sobą.</span><span class="sxs-lookup"><span data-stu-id="903eb-104">A relationship defines how two entities relate to each other.</span></span> <span data-ttu-id="903eb-105">W relacyjnej bazie danych jest to reprezentowane przez ograniczenie klucza obcego.</span><span class="sxs-lookup"><span data-stu-id="903eb-105">In a relational database, this is represented by a foreign key constraint.</span></span>

> [!NOTE]  
> <span data-ttu-id="903eb-106">Większość przykładów w tym artykule używa relacji jeden-do-wielu, aby przedstawić koncepcje.</span><span class="sxs-lookup"><span data-stu-id="903eb-106">Most of the samples in this article use a one-to-many relationship to demonstrate concepts.</span></span> <span data-ttu-id="903eb-107">Przykłady relacji jeden-do-jednego i wiele-do-wielu można znaleźć w sekcji [inne wzorce relacji](#other-relationship-patterns) na końcu artykułu.</span><span class="sxs-lookup"><span data-stu-id="903eb-107">For examples of one-to-one and many-to-many relationships see the [Other Relationship Patterns](#other-relationship-patterns) section at the end of the article.</span></span>

## <a name="definition-of-terms"></a><span data-ttu-id="903eb-108">Definicja warunków</span><span class="sxs-lookup"><span data-stu-id="903eb-108">Definition of terms</span></span>

<span data-ttu-id="903eb-109">Istnieje kilka terminów używanych do opisywania relacji</span><span class="sxs-lookup"><span data-stu-id="903eb-109">There are a number of terms used to describe relationships</span></span>

* <span data-ttu-id="903eb-110">**Jednostka zależna:** Jest to jednostka, która zawiera właściwości klucza obcego.</span><span class="sxs-lookup"><span data-stu-id="903eb-110">**Dependent entity:** This is the entity that contains the foreign key properties.</span></span> <span data-ttu-id="903eb-111">Czasami określana jako "podrzędny" relacji.</span><span class="sxs-lookup"><span data-stu-id="903eb-111">Sometimes referred to as the 'child' of the relationship.</span></span>

* <span data-ttu-id="903eb-112">**Podmiot podmiotu zabezpieczeń:** Jest to jednostka, która zawiera właściwości klucza podstawowego/alternatywnego.</span><span class="sxs-lookup"><span data-stu-id="903eb-112">**Principal entity:** This is the entity that contains the primary/alternate key properties.</span></span> <span data-ttu-id="903eb-113">Czasami określana jako "Parent" relacji.</span><span class="sxs-lookup"><span data-stu-id="903eb-113">Sometimes referred to as the 'parent' of the relationship.</span></span>

* <span data-ttu-id="903eb-114">**Klucz podmiotu zabezpieczeń:** Właściwości, które jednoznacznie identyfikują jednostkę podmiotu zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="903eb-114">**Principal key:** The properties that uniquely identify the principal entity.</span></span> <span data-ttu-id="903eb-115">Może to być klucz podstawowy lub klucz alternatywny.</span><span class="sxs-lookup"><span data-stu-id="903eb-115">This may be the primary key or an alternate key.</span></span>

* <span data-ttu-id="903eb-116">**Klucz obcy:** Właściwości w jednostce zależnej, które są używane do przechowywania wartości klucza podmiotu zabezpieczeń powiązanej jednostki.</span><span class="sxs-lookup"><span data-stu-id="903eb-116">**Foreign key:** The properties in the dependent entity that are used to store the principal key values for the related entity.</span></span>

* <span data-ttu-id="903eb-117">**Właściwość nawigacji:** Właściwość zdefiniowana dla podmiotu zabezpieczeń i/lub zależnego, która odwołuje się do powiązanej jednostki.</span><span class="sxs-lookup"><span data-stu-id="903eb-117">**Navigation property:** A property defined on the principal and/or dependent entity that references the related entity.</span></span>

  * <span data-ttu-id="903eb-118">**Właściwość nawigacji kolekcji:** Właściwość nawigacji, która zawiera odwołania do wielu powiązanych jednostek.</span><span class="sxs-lookup"><span data-stu-id="903eb-118">**Collection navigation property:** A navigation property that contains references to many related entities.</span></span>

  * <span data-ttu-id="903eb-119">**Właściwość nawigacji odwołania:** Właściwość nawigacji, która zawiera odwołanie do pojedynczej powiązanej jednostki.</span><span class="sxs-lookup"><span data-stu-id="903eb-119">**Reference navigation property:** A navigation property that holds a reference to a single related entity.</span></span>

  * <span data-ttu-id="903eb-120">**Właściwość nawigacji odwrotnej:** Omawiając konkretną właściwość nawigacji, ten termin odnosi się do właściwości nawigacji na drugim końcu relacji.</span><span class="sxs-lookup"><span data-stu-id="903eb-120">**Inverse navigation property:** When discussing a particular navigation property, this term refers to the navigation property on the other end of the relationship.</span></span>
  
* <span data-ttu-id="903eb-121">**Relacja odwołująca się do samego siebie:** Relacja, w której zależne i główne typy jednostek są takie same.</span><span class="sxs-lookup"><span data-stu-id="903eb-121">**Self-referencing relationship:** A relationship in which the dependent and the principal entity types are the same.</span></span>

<span data-ttu-id="903eb-122">Poniższy kod przedstawia relację "jeden do wielu" między `Blog` i`Post`</span><span class="sxs-lookup"><span data-stu-id="903eb-122">The following code shows a one-to-many relationship between `Blog` and `Post`</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs#Full)]

* <span data-ttu-id="903eb-123">`Post`jest jednostką zależną</span><span class="sxs-lookup"><span data-stu-id="903eb-123">`Post` is the dependent entity</span></span>

* <span data-ttu-id="903eb-124">`Blog`jest jednostką główną</span><span class="sxs-lookup"><span data-stu-id="903eb-124">`Blog` is the principal entity</span></span>

* <span data-ttu-id="903eb-125">`Blog.BlogId`jest kluczem podmiotu zabezpieczeń (w tym przypadku jest kluczem podstawowym zamiast alternatywnym kluczem)</span><span class="sxs-lookup"><span data-stu-id="903eb-125">`Blog.BlogId` is the principal key (in this case it is a primary key rather than an alternate key)</span></span>

* <span data-ttu-id="903eb-126">`Post.BlogId`jest kluczem obcym</span><span class="sxs-lookup"><span data-stu-id="903eb-126">`Post.BlogId` is the foreign key</span></span>

* <span data-ttu-id="903eb-127">`Post.Blog`jest właściwością nawigacji referencyjnej</span><span class="sxs-lookup"><span data-stu-id="903eb-127">`Post.Blog` is a reference navigation property</span></span>

* <span data-ttu-id="903eb-128">`Blog.Posts`jest właściwością nawigacji kolekcji</span><span class="sxs-lookup"><span data-stu-id="903eb-128">`Blog.Posts` is a collection navigation property</span></span>

* <span data-ttu-id="903eb-129">`Post.Blog`jest właściwością nawigacji odwrotnej `Blog.Posts` (i odwrotnie)</span><span class="sxs-lookup"><span data-stu-id="903eb-129">`Post.Blog` is the inverse navigation property of `Blog.Posts` (and vice versa)</span></span>

## <a name="conventions"></a><span data-ttu-id="903eb-130">Konwencje</span><span class="sxs-lookup"><span data-stu-id="903eb-130">Conventions</span></span>

<span data-ttu-id="903eb-131">Domyślnie relacja zostanie utworzona, gdy w typie zostanie wykryta właściwość nawigacji.</span><span class="sxs-lookup"><span data-stu-id="903eb-131">By default, a relationship will be created when there is a navigation property discovered on a type.</span></span> <span data-ttu-id="903eb-132">Właściwość jest uznawana za właściwość nawigacji, jeśli typ wskazujący nie może być mapowany jako typ skalarny przez bieżącego dostawcę bazy danych.</span><span class="sxs-lookup"><span data-stu-id="903eb-132">A property is considered a navigation property if the type it points to can not be mapped as a scalar type by the current database provider.</span></span>

> [!NOTE]  
> <span data-ttu-id="903eb-133">Relacje, które są odnajdywane według Konwencji, będą zawsze ukierunkowane na klucz podstawowy jednostki głównej.</span><span class="sxs-lookup"><span data-stu-id="903eb-133">Relationships that are discovered by convention will always target the primary key of the principal entity.</span></span> <span data-ttu-id="903eb-134">Aby określić klucz alternatywny, należy przeprowadzić dodatkową konfigurację przy użyciu interfejsu API Fluent.</span><span class="sxs-lookup"><span data-stu-id="903eb-134">To target an alternate key, additional configuration must be performed using the Fluent API.</span></span>

### <a name="fully-defined-relationships"></a><span data-ttu-id="903eb-135">W pełni zdefiniowane relacje</span><span class="sxs-lookup"><span data-stu-id="903eb-135">Fully defined relationships</span></span>

<span data-ttu-id="903eb-136">Najbardziej typowym wzorcem relacji jest posiadanie właściwości nawigacji zdefiniowanych na obu końcach relacji i właściwości klucza obcego zdefiniowanej w klasie jednostki zależnej.</span><span class="sxs-lookup"><span data-stu-id="903eb-136">The most common pattern for relationships is to have navigation properties defined on both ends of the relationship and a foreign key property defined in the dependent entity class.</span></span>

* <span data-ttu-id="903eb-137">Jeśli para właściwości nawigacji zostanie znaleziona między dwoma typami, zostaną one skonfigurowane jako właściwości nawigacji odwrotnej tej samej relacji.</span><span class="sxs-lookup"><span data-stu-id="903eb-137">If a pair of navigation properties is found between two types, then they will be configured as inverse navigation properties of the same relationship.</span></span>

* <span data-ttu-id="903eb-138">Jeśli jednostka zależna zawiera właściwość o nazwie zgodnej z jednym z tych wzorców, zostanie ona skonfigurowana jako klucz obcy:</span><span class="sxs-lookup"><span data-stu-id="903eb-138">If the dependent entity contains a property with a name matching one of these patterns then it will be configured as the foreign key:</span></span>
  * `<navigation property name><principal key property name>`
  * `<navigation property name>Id`
  * `<principal entity name><principal key property name>`
  * `<principal entity name>Id`

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs?name=Full&highlight=6,15-16)]

<span data-ttu-id="903eb-139">W tym przykładzie wyróżnione właściwości zostaną użyte do skonfigurowania relacji.</span><span class="sxs-lookup"><span data-stu-id="903eb-139">In this example the highlighted properties will be used to configure the relationship.</span></span>

> [!NOTE]
> <span data-ttu-id="903eb-140">Jeśli właściwość jest kluczem podstawowym lub jest typu niezgodnego z kluczem podmiotu zabezpieczeń, nie zostanie on skonfigurowany jako klucz obcy.</span><span class="sxs-lookup"><span data-stu-id="903eb-140">If the property is the primary key or is of a type not compatible with the principal key then it won't be configured as the foreign key.</span></span>

> [!NOTE]
> <span data-ttu-id="903eb-141">Przed EF Core 3,0 właściwość o nazwie dokładnie taka sama jak właściwość klucza głównego [była również zgodna jako klucz obcy](https://github.com/aspnet/EntityFrameworkCore/issues/13274)</span><span class="sxs-lookup"><span data-stu-id="903eb-141">Before EF Core 3.0 the property named exactly the same as the principal key property [was also matched as the foreign key](https://github.com/aspnet/EntityFrameworkCore/issues/13274)</span></span>

### <a name="no-foreign-key-property"></a><span data-ttu-id="903eb-142">Brak właściwości klucza obcego</span><span class="sxs-lookup"><span data-stu-id="903eb-142">No foreign key property</span></span>

<span data-ttu-id="903eb-143">Chociaż zaleca się zdefiniowanie właściwości klucza obcego w klasie jednostki zależnej, nie jest to wymagane.</span><span class="sxs-lookup"><span data-stu-id="903eb-143">While it is recommended to have a foreign key property defined in the dependent entity class, it is not required.</span></span> <span data-ttu-id="903eb-144">Jeśli właściwość klucza obcego nie zostanie znaleziona, [Właściwość klucza obcego cienia](shadow-properties.md) zostanie wprowadzona przy użyciu nazwy `<navigation property name><principal key property name>` lub `<principal entity name><principal key property name>` Jeśli w typie zależnym nie ma żadnej nawigacji.</span><span class="sxs-lookup"><span data-stu-id="903eb-144">If no foreign key property is found, a [shadow foreign key property](shadow-properties.md) will be introduced with the name `<navigation property name><principal key property name>` or `<principal entity name><principal key property name>` if no navigation is present on the dependent type.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/NoForeignKey.cs?name=NoForeignKey&highlight=6,15)]

<span data-ttu-id="903eb-145">W tym przykładzie klucz obcy Shadow jest, `BlogId` ponieważ oczekiwano, że nazwa nawigacji będzie nadmiarowa.</span><span class="sxs-lookup"><span data-stu-id="903eb-145">In this example the shadow foreign key is `BlogId` because prepending the navigation name would be redundant.</span></span>

> [!NOTE]
> <span data-ttu-id="903eb-146">Jeśli właściwość o tej samej nazwie już istnieje, nazwa właściwości cienia zostanie poddana sufiksem liczbowym.</span><span class="sxs-lookup"><span data-stu-id="903eb-146">If a property with the same name already exists then the shadow property name will be suffixed with a number.</span></span>

### <a name="single-navigation-property"></a><span data-ttu-id="903eb-147">Właściwość pojedynczej nawigacji</span><span class="sxs-lookup"><span data-stu-id="903eb-147">Single navigation property</span></span>

<span data-ttu-id="903eb-148">Dołączenie tylko jednej właściwości nawigacji (bez nawigacji odwrotnej i brak właściwości klucza obcego) nie wystarcza do zdefiniowania relacji przez Konwencję.</span><span class="sxs-lookup"><span data-stu-id="903eb-148">Including just one navigation property (no inverse navigation, and no foreign key property) is enough to have a relationship defined by convention.</span></span> <span data-ttu-id="903eb-149">Można również mieć pojedynczą właściwość nawigacji i właściwość klucza obcego.</span><span class="sxs-lookup"><span data-stu-id="903eb-149">You can also have a single navigation property and a foreign key property.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneNavigation.cs?name=OneNavigation&highlight=6)]

### <a name="limitations"></a><span data-ttu-id="903eb-150">Ograniczenia</span><span class="sxs-lookup"><span data-stu-id="903eb-150">Limitations</span></span>

<span data-ttu-id="903eb-151">W przypadku zdefiniowania wielu właściwości nawigacji między dwoma typami (oznacza to, że więcej niż tylko jedna para nawigacji wskazuje na siebie) relacje reprezentowane przez właściwości nawigacji są niejednoznaczne.</span><span class="sxs-lookup"><span data-stu-id="903eb-151">When there are multiple navigation properties defined between two types (that is, more than just one pair of navigations that point to each other) the relationships represented by the navigation properties are ambiguous.</span></span> <span data-ttu-id="903eb-152">Należy ręcznie skonfigurować je, aby usunąć niejednoznaczność.</span><span class="sxs-lookup"><span data-stu-id="903eb-152">You will need to manually configure them to resolve the ambiguity.</span></span>

### <a name="cascade-delete"></a><span data-ttu-id="903eb-153">Usuwanie kaskadowe</span><span class="sxs-lookup"><span data-stu-id="903eb-153">Cascade delete</span></span>

<span data-ttu-id="903eb-154">Według Konwencji kaskadowe usuwanie zostanie ustawione na *kaskadowe* dla wymaganych relacji i *ClientSetNull* dla relacji opcjonalnych.</span><span class="sxs-lookup"><span data-stu-id="903eb-154">By convention, cascade delete will be set to *Cascade* for required relationships and *ClientSetNull* for optional relationships.</span></span> <span data-ttu-id="903eb-155">*Kaskadowo* oznacza również usunięcie jednostek zależnych.</span><span class="sxs-lookup"><span data-stu-id="903eb-155">*Cascade* means dependent entities are also deleted.</span></span> <span data-ttu-id="903eb-156">*ClientSetNull* oznacza, że jednostki zależne, które nie są ładowane do pamięci, pozostaną bez zmian i muszą zostać ręcznie usunięte lub zaktualizowane, aby wskazywały prawidłową jednostkę podmiotu zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="903eb-156">*ClientSetNull* means that dependent entities that are not loaded into memory will remain unchanged and must be manually deleted, or updated to point to a valid principal entity.</span></span> <span data-ttu-id="903eb-157">W przypadku jednostek, które są ładowane do pamięci, EF Core podejmie próbę ustawienia właściwości klucza obcego na wartość null.</span><span class="sxs-lookup"><span data-stu-id="903eb-157">For entities that are loaded into memory, EF Core will attempt to set the foreign key properties to null.</span></span>

<span data-ttu-id="903eb-158">Zapoznaj się z sekcją [wymagane i opcjonalne relacje](#required-and-optional-relationships) , aby uzyskać różnicę między wymaganymi i opcjonalnymi relacjami.</span><span class="sxs-lookup"><span data-stu-id="903eb-158">See the [Required and Optional Relationships](#required-and-optional-relationships) section for the difference between required and optional relationships.</span></span>

<span data-ttu-id="903eb-159">Zobacz [Kaskada Delete](../saving/cascade-delete.md) , aby uzyskać więcej informacji na temat różnych zachowań usuwania i ustawień domyślnych używanych przez Konwencję.</span><span class="sxs-lookup"><span data-stu-id="903eb-159">See [Cascade Delete](../saving/cascade-delete.md) for more details about the different delete behaviors and the defaults used by convention.</span></span>

## <a name="manual-configuration"></a><span data-ttu-id="903eb-160">Konfiguracja ręczna</span><span class="sxs-lookup"><span data-stu-id="903eb-160">Manual configuration</span></span>

### <a name="fluent-api"></a>[<span data-ttu-id="903eb-161">Interfejs API Fluent</span><span class="sxs-lookup"><span data-stu-id="903eb-161">Fluent API</span></span>](#tab/fluent-api)

<span data-ttu-id="903eb-162">Aby skonfigurować relację w interfejsie API Fluent, należy rozpocząć od zidentyfikowania właściwości nawigacji, które tworzą relację.</span><span class="sxs-lookup"><span data-stu-id="903eb-162">To configure a relationship in the Fluent API, you start by identifying the navigation properties that make up the relationship.</span></span> <span data-ttu-id="903eb-163">`HasOne`lub `HasMany` identyfikuje właściwość nawigacji dla typu jednostki, w którym rozpoczyna się konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="903eb-163">`HasOne` or `HasMany` identifies the navigation property on the entity type you are beginning the configuration on.</span></span> <span data-ttu-id="903eb-164">Następnie utworzysz łańcuch wywołań lub, `WithOne` `WithMany` Aby zidentyfikować nawigację odwrotną.</span><span class="sxs-lookup"><span data-stu-id="903eb-164">You then chain a call to `WithOne` or `WithMany` to identify the inverse navigation.</span></span> <span data-ttu-id="903eb-165">`HasOne`/`WithOne`są używane do właściwości nawigacji referencyjnej i `HasMany` / `WithMany` są używane dla właściwości nawigacji kolekcji.</span><span class="sxs-lookup"><span data-stu-id="903eb-165">`HasOne`/`WithOne` are used for reference navigation properties and `HasMany`/`WithMany` are used for collection navigation properties.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoForeignKey.cs?name=NoForeignKey&highlight=8-10)]

### <a name="data-annotations"></a>[<span data-ttu-id="903eb-166">Adnotacje danych</span><span class="sxs-lookup"><span data-stu-id="903eb-166">Data annotations</span></span>](#tab/data-annotations)

<span data-ttu-id="903eb-167">Możesz użyć adnotacji danych, aby skonfigurować sposób pary właściwości nawigacji dla jednostek zależnych i głównych.</span><span class="sxs-lookup"><span data-stu-id="903eb-167">You can use the Data Annotations to configure how navigation properties on the dependent and principal entities pair up.</span></span> <span data-ttu-id="903eb-168">Jest to zazwyczaj wykonywane, gdy istnieje więcej niż jedna para właściwości nawigacji między dwoma typami jednostek.</span><span class="sxs-lookup"><span data-stu-id="903eb-168">This is typically done when there is more than one pair of navigation properties between two entity types.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/InverseProperty.cs?name=InverseProperty&highlight=20,23)]

> [!NOTE]
> <span data-ttu-id="903eb-169">Możesz użyć [Required] właściwości w jednostce zależnej, aby mieć wpływ na wymaganą relację.</span><span class="sxs-lookup"><span data-stu-id="903eb-169">You can only use [Required] on properties on the dependent entity to impact the requiredness of the relationship.</span></span> <span data-ttu-id="903eb-170">[Wymagane] w nawigacji z jednostki głównej jest zwykle ignorowany, ale może spowodować, że jednostka stanie się jednostką zależną.</span><span class="sxs-lookup"><span data-stu-id="903eb-170">[Required] on the navigation from the principal entity is usually ignored, but it may cause the entity to become the dependent one.</span></span>

> [!NOTE]
> <span data-ttu-id="903eb-171">Adnotacje danych `[ForeignKey]` i `[InverseProperty]` są dostępne w `System.ComponentModel.DataAnnotations.Schema` przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="903eb-171">The data annotations `[ForeignKey]` and `[InverseProperty]` are available in the `System.ComponentModel.DataAnnotations.Schema` namespace.</span></span> <span data-ttu-id="903eb-172">`[Required]`jest dostępny w `System.ComponentModel.DataAnnotations` przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="903eb-172">`[Required]` is available in the `System.ComponentModel.DataAnnotations` namespace.</span></span>

---

### <a name="single-navigation-property"></a><span data-ttu-id="903eb-173">Właściwość pojedynczej nawigacji</span><span class="sxs-lookup"><span data-stu-id="903eb-173">Single navigation property</span></span>

<span data-ttu-id="903eb-174">Jeśli masz tylko jedną właściwość nawigacji, istnieją nadmierne przeciążenia parametrów `WithOne` i `WithMany` .</span><span class="sxs-lookup"><span data-stu-id="903eb-174">If you only have one navigation property then there are parameterless overloads of `WithOne` and `WithMany`.</span></span> <span data-ttu-id="903eb-175">Oznacza to, że istnieje koncepcyjnie odwołanie lub kolekcja na drugim końcu relacji, ale nie ma żadnej właściwości nawigacji zawartej w klasie Entity.</span><span class="sxs-lookup"><span data-stu-id="903eb-175">This indicates that there is conceptually a reference or collection on the other end of the relationship, but there is no navigation property included in the entity class.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneNavigation.cs?name=OneNavigation&highlight=8-10)]

---

### <a name="configuring-navigation-properties"></a><span data-ttu-id="903eb-176">Konfigurowanie właściwości nawigacji</span><span class="sxs-lookup"><span data-stu-id="903eb-176">Configuring navigation properties</span></span>

<span data-ttu-id="903eb-177">Po utworzeniu właściwości nawigacji może być konieczne jej dalsze skonfigurowanie.</span><span class="sxs-lookup"><span data-stu-id="903eb-177">After the navigation property has been created, you may need to further configure it.</span></span> <span data-ttu-id="903eb-178">W programie EFCore 5,0 dodano nowy interfejs API Fluent, który umożliwia wykonanie tej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="903eb-178">In EFCore 5.0 new Fluent API is added to allow you perform that configuration.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NavigationConfiguration.cs?name=NavigationConfiguration&highlight=7-9)]

> [!NOTE]
> <span data-ttu-id="903eb-179">Tego wywołania nie można użyć do utworzenia właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="903eb-179">This call cannot be used to create a navigation property.</span></span> <span data-ttu-id="903eb-180">Służy tylko do konfigurowania właściwości nawigacji, która została wcześniej utworzona przez zdefiniowanie relacji lub Konwencji.</span><span class="sxs-lookup"><span data-stu-id="903eb-180">It is only used to configure a navigation property which has been previously created by defining a relationship or from a convention.</span></span>

### <a name="foreign-key"></a><span data-ttu-id="903eb-181">Klucz obcy</span><span class="sxs-lookup"><span data-stu-id="903eb-181">Foreign key</span></span>

#### <a name="fluent-api-simple-key"></a>[<span data-ttu-id="903eb-182">Interfejs API Fluent (klucz prosty)</span><span class="sxs-lookup"><span data-stu-id="903eb-182">Fluent API (simple key)</span></span>](#tab/fluent-api-simple-key)

<span data-ttu-id="903eb-183">Korzystając z interfejsu API Fluent, można skonfigurować właściwość, która powinna być używana jako właściwość klucza obcego dla danej relacji:</span><span class="sxs-lookup"><span data-stu-id="903eb-183">You can use the Fluent API to configure which property should be used as the foreign key property for a given relationship:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ForeignKey.cs?name=ForeignKey&highlight=11)]

#### <a name="fluent-api-composite-key"></a>[<span data-ttu-id="903eb-184">Interfejs API Fluent (klucz złożony)</span><span class="sxs-lookup"><span data-stu-id="903eb-184">Fluent API (composite key)</span></span>](#tab/fluent-api-composite-key)

<span data-ttu-id="903eb-185">Korzystając z interfejsu API Fluent, można skonfigurować właściwości, które mają być używane jako złożone właściwości klucza obcego dla danej relacji:</span><span class="sxs-lookup"><span data-stu-id="903eb-185">You can use the Fluent API to configure which properties should be used as the composite foreign key properties for a given relationship:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositeForeignKey.cs?name=CompositeForeignKey&highlight=13)]

#### <a name="data-annotations-simple-key"></a>[<span data-ttu-id="903eb-186">Adnotacje danych (klucz prosty)</span><span class="sxs-lookup"><span data-stu-id="903eb-186">Data annotations (simple key)</span></span>](#tab/data-annotations-simple-key)

<span data-ttu-id="903eb-187">Możesz użyć adnotacji danych, aby skonfigurować właściwość, która powinna być używana jako właściwość klucza obcego dla danej relacji.</span><span class="sxs-lookup"><span data-stu-id="903eb-187">You can use the Data Annotations to configure which property should be used as the foreign key property for a given relationship.</span></span> <span data-ttu-id="903eb-188">Zwykle jest to wykonywane, gdy właściwość klucza obcego nie zostanie odnaleziona według Konwencji:</span><span class="sxs-lookup"><span data-stu-id="903eb-188">This is typically done when the foreign key property is not discovered by convention:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/ForeignKey.cs?name=ForeignKey&highlight=17)]

> [!TIP]  
> <span data-ttu-id="903eb-189">`[ForeignKey]`Adnotację można umieścić na każdej właściwości nawigacji w relacji.</span><span class="sxs-lookup"><span data-stu-id="903eb-189">The `[ForeignKey]` annotation can be placed on either navigation property in the relationship.</span></span> <span data-ttu-id="903eb-190">Nie musi ona być zgodna z właściwością nawigacji w klasie jednostki zależnej.</span><span class="sxs-lookup"><span data-stu-id="903eb-190">It does not need to go on the navigation property in the dependent entity class.</span></span>

> [!NOTE]
> <span data-ttu-id="903eb-191">Właściwość określona przy użyciu `[ForeignKey]` właściwości nawigacji nie musi istnieć w typie zależnym.</span><span class="sxs-lookup"><span data-stu-id="903eb-191">The property specified using `[ForeignKey]` on a navigation property doesn't need to exist on the dependent type.</span></span> <span data-ttu-id="903eb-192">W takim przypadku określona nazwa zostanie użyta do utworzenia klucza obcego cienia.</span><span class="sxs-lookup"><span data-stu-id="903eb-192">In this case the specified name will be used to create a shadow foreign key.</span></span>

---

#### <a name="shadow-foreign-key"></a><span data-ttu-id="903eb-193">Obcy klucz cienia</span><span class="sxs-lookup"><span data-stu-id="903eb-193">Shadow foreign key</span></span>

<span data-ttu-id="903eb-194">Możesz użyć przeciążenia ciągu, `HasForeignKey(...)` Aby skonfigurować właściwość Shadow jako klucz obcy (Aby uzyskać więcej informacji, zobacz [Właściwości cienia](shadow-properties.md) ).</span><span class="sxs-lookup"><span data-stu-id="903eb-194">You can use the string overload of `HasForeignKey(...)` to configure a shadow property as a foreign key (see [Shadow Properties](shadow-properties.md) for more information).</span></span> <span data-ttu-id="903eb-195">Zalecamy jawne dodanie właściwości Shadow do modelu przed użyciem go jako klucza obcego (jak pokazano poniżej).</span><span class="sxs-lookup"><span data-stu-id="903eb-195">We recommend explicitly adding the shadow property to the model before using it as a foreign key (as shown below).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ShadowForeignKey.cs?name=ShadowForeignKey&highlight=10,16)]

#### <a name="foreign-key-constraint-name"></a><span data-ttu-id="903eb-196">Nazwa ograniczenia klucza obcego</span><span class="sxs-lookup"><span data-stu-id="903eb-196">Foreign key constraint name</span></span>

<span data-ttu-id="903eb-197">Zgodnie z Konwencją, podczas określania relacyjnej bazy danych ograniczenia klucza obcego mają nazwę FK_ <dependent type name> _<principal type name>_ <foreign key property name> .</span><span class="sxs-lookup"><span data-stu-id="903eb-197">By convention, when targeting a relational database, foreign key constraints are named FK_<dependent type name>_<principal type name>_<foreign key property name>.</span></span> <span data-ttu-id="903eb-198">W przypadku złożonych kluczy obcych <foreign key property name> jest rozdzielana podkreśleniem lista nazw właściwości klucza obcego.</span><span class="sxs-lookup"><span data-stu-id="903eb-198">For composite foreign keys <foreign key property name> becomes an underscore separated list of foreign key property names.</span></span>

<span data-ttu-id="903eb-199">Nazwę ograniczenia można również skonfigurować w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="903eb-199">You can also configure the constraint name as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ConstraintName.cs?name=ConstraintName&highlight=6-7)]

### <a name="without-navigation-property"></a><span data-ttu-id="903eb-200">Bez właściwości nawigacji</span><span class="sxs-lookup"><span data-stu-id="903eb-200">Without navigation property</span></span>

<span data-ttu-id="903eb-201">Nie trzeba podawać właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="903eb-201">You don't necessarily need to provide a navigation property.</span></span> <span data-ttu-id="903eb-202">Możesz po prostu podać klucz obcy po jednej stronie relacji.</span><span class="sxs-lookup"><span data-stu-id="903eb-202">You can simply provide a foreign key on one side of the relationship.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoNavigation.cs?name=NoNavigation&highlight=8-11)]

### <a name="principal-key"></a><span data-ttu-id="903eb-203">Klucz podmiotu zabezpieczeń</span><span class="sxs-lookup"><span data-stu-id="903eb-203">Principal key</span></span>

<span data-ttu-id="903eb-204">Jeśli chcesz, aby klucz obcy odwołuje się do właściwości innej niż klucz podstawowy, możesz użyć interfejsu API Fluent, aby skonfigurować właściwość klucza podmiotu zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="903eb-204">If you want the foreign key to reference a property other than the primary key, you can use the Fluent API to configure the principal key property for the relationship.</span></span> <span data-ttu-id="903eb-205">Właściwość, którą konfigurujesz jako klucz podmiotu zabezpieczeń, zostanie automatycznie skonfigurowana jako [klucz alternatywny](alternate-keys.md).</span><span class="sxs-lookup"><span data-stu-id="903eb-205">The property that you configure as the principal key will automatically be setup as an [alternate key](alternate-keys.md).</span></span>

#### <a name="simple-key"></a>[<span data-ttu-id="903eb-206">Klucz prosty</span><span class="sxs-lookup"><span data-stu-id="903eb-206">Simple key</span></span>](#tab/simple-key)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/PrincipalKey.cs?name=PrincipalKey&highlight=11)]

#### <a name="composite-key"></a>[<span data-ttu-id="903eb-207">Klucz złożony</span><span class="sxs-lookup"><span data-stu-id="903eb-207">Composite key</span></span>](#tab/composite-key)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositePrincipalKey.cs?name=CompositePrincipalKey&highlight=11)]

> [!WARNING]  
> <span data-ttu-id="903eb-208">Kolejność, w której określane są właściwości klucza podmiotu zabezpieczeń, musi być zgodna z kolejnością, w której są określone dla klucza obcego.</span><span class="sxs-lookup"><span data-stu-id="903eb-208">The order in which you specify principal key properties must match the order in which they are specified for the foreign key.</span></span>

---

### <a name="required-and-optional-relationships"></a><span data-ttu-id="903eb-209">Wymagane i opcjonalne relacje</span><span class="sxs-lookup"><span data-stu-id="903eb-209">Required and optional relationships</span></span>

<span data-ttu-id="903eb-210">Korzystając z interfejsu API Fluent, można określić, czy relacja jest wymagana, czy opcjonalna.</span><span class="sxs-lookup"><span data-stu-id="903eb-210">You can use the Fluent API to configure whether the relationship is required or optional.</span></span> <span data-ttu-id="903eb-211">Ostatecznie to kontroluje, czy właściwość klucza obcego jest wymagana czy opcjonalna.</span><span class="sxs-lookup"><span data-stu-id="903eb-211">Ultimately this controls whether the foreign key property is required or optional.</span></span> <span data-ttu-id="903eb-212">Jest to najbardziej przydatne w przypadku używania klucza obcego stanu w tle.</span><span class="sxs-lookup"><span data-stu-id="903eb-212">This is most useful when you are using a shadow state foreign key.</span></span> <span data-ttu-id="903eb-213">Jeśli masz właściwość klucza obcego w klasie jednostki, wymagana jest konieczność relacji w zależności od tego, czy właściwość klucza obcego jest wymagana, czy opcjonalna (zobacz [Właściwości wymagane i opcjonalne,](required-optional.md) Aby uzyskać więcej informacji).</span><span class="sxs-lookup"><span data-stu-id="903eb-213">If you have a foreign key property in your entity class then the requiredness of the relationship is determined based on whether the foreign key property is required or optional (see [Required and Optional properties](required-optional.md) for more information).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/Required.cs?name=Required&highlight=6)]

> [!NOTE]
> <span data-ttu-id="903eb-214">Wywołanie `IsRequired(false)` powoduje również, że właściwość klucza obcego jest opcjonalna, chyba że została skonfigurowana inaczej.</span><span class="sxs-lookup"><span data-stu-id="903eb-214">Calling `IsRequired(false)` also makes the foreign key property optional unless it's configured otherwise.</span></span>

### <a name="cascade-delete"></a><span data-ttu-id="903eb-215">Usuwanie kaskadowe</span><span class="sxs-lookup"><span data-stu-id="903eb-215">Cascade delete</span></span>

<span data-ttu-id="903eb-216">Korzystając z interfejsu API Fluent, można jawnie skonfigurować zachowanie kaskadowego usuwania dla danej relacji.</span><span class="sxs-lookup"><span data-stu-id="903eb-216">You can use the Fluent API to configure the cascade delete behavior for a given relationship explicitly.</span></span>

<span data-ttu-id="903eb-217">Szczegółowe omówienie każdej z tych opcji można znaleźć w temacie [usuwanie kaskadowe](../saving/cascade-delete.md) .</span><span class="sxs-lookup"><span data-stu-id="903eb-217">See [Cascade Delete](../saving/cascade-delete.md) for a detailed discussion of each option.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CascadeDelete.cs?name=CascadeDelete&highlight=6)]

## <a name="other-relationship-patterns"></a><span data-ttu-id="903eb-218">Inne wzorce relacji</span><span class="sxs-lookup"><span data-stu-id="903eb-218">Other relationship patterns</span></span>

### <a name="one-to-one"></a><span data-ttu-id="903eb-219">Jeden do jednego</span><span class="sxs-lookup"><span data-stu-id="903eb-219">One-to-one</span></span>

<span data-ttu-id="903eb-220">Relacja jeden do jednego ma właściwość nawigacji odwołania po obu stronach.</span><span class="sxs-lookup"><span data-stu-id="903eb-220">One to one relationships have a reference navigation property on both sides.</span></span> <span data-ttu-id="903eb-221">Są one zgodne z tymi samymi konwencjami co relacje jeden-do-wielu, ale na właściwości klucza obcego jest wprowadzany unikatowy indeks, aby upewnić się, że tylko jedna z nich jest zależna od każdego podmiotu zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="903eb-221">They follow the same conventions as one-to-many relationships, but a unique index is introduced on the foreign key property to ensure only one dependent is related to each principal.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneToOne.cs?name=OneToOne&highlight=6,15-16)]

> [!NOTE]  
> <span data-ttu-id="903eb-222">Dr wybierze jedną z jednostek, która będzie zależna od możliwości wykrywania właściwości klucza obcego.</span><span class="sxs-lookup"><span data-stu-id="903eb-222">EF will choose one of the entities to be the dependent based on its ability to detect a foreign key property.</span></span> <span data-ttu-id="903eb-223">W przypadku wybrania niewłaściwej jednostki jako zależnej można użyć interfejsu API Fluent, aby rozwiązać ten problem.</span><span class="sxs-lookup"><span data-stu-id="903eb-223">If the wrong entity is chosen as the dependent, you can use the Fluent API to correct this.</span></span>

<span data-ttu-id="903eb-224">Podczas konfigurowania relacji z interfejsem API Fluent należy używać `HasOne` `WithOne` metod i.</span><span class="sxs-lookup"><span data-stu-id="903eb-224">When configuring the relationship with the Fluent API, you use the `HasOne` and `WithOne` methods.</span></span>

<span data-ttu-id="903eb-225">Podczas konfigurowania klucza obcego należy określić typ podmiotu zależnego — Zwróć uwagę na parametr ogólny podany `HasForeignKey` w poniższej liście.</span><span class="sxs-lookup"><span data-stu-id="903eb-225">When configuring the foreign key you need to specify the dependent entity type - notice the generic parameter provided to `HasForeignKey` in the listing below.</span></span> <span data-ttu-id="903eb-226">W relacji jeden-do-wielu jest jasne, że jednostka z nawigacją referencyjną jest zależna, a jeden z kolekcją jest podmiotem zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="903eb-226">In a one-to-many relationship it is clear that the entity with the reference navigation is the dependent and the one with the collection is the principal.</span></span> <span data-ttu-id="903eb-227">Nie jest to jednak w relacji jeden-do-jednego — dlatego trzeba ją jawnie zdefiniować.</span><span class="sxs-lookup"><span data-stu-id="903eb-227">But this is not so in a one-to-one relationship - hence the need to explicitly define it.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneToOne.cs?name=OneToOne&highlight=11)]

### <a name="many-to-many"></a><span data-ttu-id="903eb-228">Wiele do wielu</span><span class="sxs-lookup"><span data-stu-id="903eb-228">Many-to-many</span></span>

<span data-ttu-id="903eb-229">Relacje wiele-do-wielu bez klasy Entity do reprezentowania tabeli sprzężenia nie są jeszcze obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="903eb-229">Many-to-many relationships without an entity class to represent the join table are not yet supported.</span></span> <span data-ttu-id="903eb-230">Istnieje jednak możliwość reprezentowania relacji wiele-do-wielu poprzez dołączenie klasy jednostki dla tabeli sprzężenia i mapowanie dwóch oddzielnych relacji jeden-do-wielu.</span><span class="sxs-lookup"><span data-stu-id="903eb-230">However, you can represent a many-to-many relationship by including an entity class for the join table and mapping two separate one-to-many relationships.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToMany.cs?name=ManyToMany&highlight=11-14,16-19,39-46)]
