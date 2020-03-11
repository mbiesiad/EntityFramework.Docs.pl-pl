---
title: Code First adnotacje danych — EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 80abefbd-23c9-4fce-9cd3-520e5df9856e
ms.openlocfilehash: 9fac2a90c46d78ff5fd632800cc0050276467773
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78419186"
---
# <a name="code-first-data-annotations"></a><span data-ttu-id="2dab4-102">Code First adnotacje danych</span><span class="sxs-lookup"><span data-stu-id="2dab4-102">Code First Data Annotations</span></span>
> [!NOTE]
> <span data-ttu-id="2dab4-103">**Dr 4.1 tylko** — funkcje, interfejsy API itp. omówione na tej stronie zostały wprowadzone w Entity Framework 4,1.</span><span class="sxs-lookup"><span data-stu-id="2dab4-103">**EF4.1 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 4.1.</span></span> <span data-ttu-id="2dab4-104">Jeśli używasz wcześniejszej wersji, niektóre lub wszystkie te informacje nie są stosowane.</span><span class="sxs-lookup"><span data-stu-id="2dab4-104">If you are using an earlier version, some or all of this information does not apply.</span></span>

<span data-ttu-id="2dab4-105">Zawartość na tej stronie jest dostosowana z artykułu utworzonego pierwotnie przez Julie Lerman (\<http://thedatafarm.com>).</span><span class="sxs-lookup"><span data-stu-id="2dab4-105">The content on this page is adapted from an article originally written by Julie Lerman (\<http://thedatafarm.com>).</span></span>

<span data-ttu-id="2dab4-106">Entity Framework Code First umożliwia korzystanie z własnych klas domeny do reprezentowania modelu, w którym opiera się program Dr, aby wykonywać zapytania, śledzenie zmian i aktualizowanie funkcji.</span><span class="sxs-lookup"><span data-stu-id="2dab4-106">Entity Framework Code First allows you to use your own domain classes to represent the model that EF relies on to perform querying, change tracking, and updating functions.</span></span> <span data-ttu-id="2dab4-107">Code First wykorzystuje wzorzec programistyczny nazywany "Konwencją od konfiguracji".</span><span class="sxs-lookup"><span data-stu-id="2dab4-107">Code First leverages a programming pattern referred to as 'convention over configuration.'</span></span> <span data-ttu-id="2dab4-108">W Code First założono, że klasy są zgodne z konwencjami Entity Framework i w takim przypadku automatycznie postanowili, jak wykonać swoje zadanie.</span><span class="sxs-lookup"><span data-stu-id="2dab4-108">Code First will assume that your classes follow the conventions of Entity Framework, and in that case, will automatically work out how to perform its job.</span></span> <span data-ttu-id="2dab4-109">Jeśli jednak klasy nie są zgodne z tymi konwencjami, można dodać konfiguracje do klas, aby dostarczyć EF z wymaganymi informacjami.</span><span class="sxs-lookup"><span data-stu-id="2dab4-109">However, if your classes do not follow those conventions, you have the ability to add configurations to your classes to provide EF with the requisite information.</span></span>

<span data-ttu-id="2dab4-110">Code First zapewnia dwa sposoby dodawania tych konfiguracji do klas.</span><span class="sxs-lookup"><span data-stu-id="2dab4-110">Code First gives you two ways to add these configurations to your classes.</span></span> <span data-ttu-id="2dab4-111">Jeden z nich korzysta z prostych atrybutów o nazwie DataAnnotations, a drugi korzysta z interfejsu API Fluent Code First, który zapewnia sposób bezpodstawnego opisywania konfiguracji w kodzie.</span><span class="sxs-lookup"><span data-stu-id="2dab4-111">One is using simple attributes called DataAnnotations, and the second is using Code First’s Fluent API, which provides you with a way to describe configurations imperatively, in code.</span></span>

<span data-ttu-id="2dab4-112">Ten artykuł koncentruje się na używaniu adnotacji danych (w przestrzeni nazw System. ComponentModel. DataAnnotations) w celu skonfigurowania klas — wyróżnianie najczęściej potrzebnych konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="2dab4-112">This article will focus on using DataAnnotations (in the System.ComponentModel.DataAnnotations namespace) to configure your classes – highlighting the most commonly needed configurations.</span></span> <span data-ttu-id="2dab4-113">Adnotacje DataAnnotations są również zrozumiałe dla wielu aplikacji platformy .NET, takich jak ASP.NET MVC, dzięki czemu te aplikacje mogą korzystać z tych samych adnotacji w przypadku walidacji po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="2dab4-113">DataAnnotations are also understood by a number of .NET applications, such as ASP.NET MVC which allows these applications to leverage the same annotations for client-side validations.</span></span>


## <a name="the-model"></a><span data-ttu-id="2dab4-114">Model</span><span class="sxs-lookup"><span data-stu-id="2dab4-114">The model</span></span>

<span data-ttu-id="2dab4-115">Pokażę Code First DataAnnotations z prostą parą klas: blog i post.</span><span class="sxs-lookup"><span data-stu-id="2dab4-115">I’ll demonstrate Code First DataAnnotations with a simple pair of classes: Blog and Post.</span></span>

``` csharp
    public class Blog
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string BloggerName { get; set;}
        public virtual ICollection<Post> Posts { get; set; }
    }

    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public DateTime DateCreated { get; set; }
        public string Content { get; set; }
        public int BlogId { get; set; }
        public ICollection<Comment> Comments { get; set; }
    }
```

<span data-ttu-id="2dab4-116">Podobnie jak w przypadku, w blogu i na wpisach klasy te wygodnie przestrzegać pierwszej Konwencji kodu i nie wymagają żadnych dostosowań, aby zapewnić kompatybilność EF.</span><span class="sxs-lookup"><span data-stu-id="2dab4-116">As they are, the Blog and Post classes conveniently follow code first convention and require no tweaks to enable EF compatability.</span></span> <span data-ttu-id="2dab4-117">Można jednak również użyć adnotacji, aby uzyskać więcej informacji na temat klas i bazy danych, do której są mapowane.</span><span class="sxs-lookup"><span data-stu-id="2dab4-117">However, you can also use the annotations to provide more information to EF about the classes and the database to which they map.</span></span>

 

## <a name="key"></a><span data-ttu-id="2dab4-118">Klucz</span><span class="sxs-lookup"><span data-stu-id="2dab4-118">Key</span></span>

<span data-ttu-id="2dab4-119">Entity Framework opiera się na każdej jednostce mającej wartość klucza, która jest używana do śledzenia jednostek.</span><span class="sxs-lookup"><span data-stu-id="2dab4-119">Entity Framework relies on every entity having a key value that is used for entity tracking.</span></span> <span data-ttu-id="2dab4-120">Jedną z Konwencji Code First są niejawne właściwości klucza; Code First będzie szukać właściwości o nazwie "ID" lub kombinacji nazwy klasy i "ID", takiej jak "BlogId".</span><span class="sxs-lookup"><span data-stu-id="2dab4-120">One convention of Code First is implicit key properties; Code First will look for a property named “Id”, or a combination of class name and “Id”, such as “BlogId”.</span></span> <span data-ttu-id="2dab4-121">Ta właściwość zostanie zamapowana na kolumnę klucza podstawowego w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="2dab4-121">This property will map to a primary key column in the database.</span></span>

<span data-ttu-id="2dab4-122">Zarówno w blogu, jak i po tej Konwencji są stosowane następujące klasy.</span><span class="sxs-lookup"><span data-stu-id="2dab4-122">The Blog and Post classes both follow this convention.</span></span> <span data-ttu-id="2dab4-123">Co zrobić, jeśli nie?</span><span class="sxs-lookup"><span data-stu-id="2dab4-123">What if they didn’t?</span></span> <span data-ttu-id="2dab4-124">Co zrobić, jeśli blog użył nazwy *PrimaryTrackingKey* zamiast tego, a nawet *foo*?</span><span class="sxs-lookup"><span data-stu-id="2dab4-124">What if Blog used the name *PrimaryTrackingKey* instead, or even *foo*?</span></span> <span data-ttu-id="2dab4-125">Jeśli kod nie znajduje właściwości zgodnej z tą konwencją, zgłosi wyjątek z powodu Entity Framework wymaga, aby właściwość klucza była wymagana.</span><span class="sxs-lookup"><span data-stu-id="2dab4-125">If code first does not find a property that matches this convention it will throw an exception because of Entity Framework’s requirement that you must have a key property.</span></span> <span data-ttu-id="2dab4-126">Możesz użyć adnotacji klucza, aby określić, która właściwość ma być używana jako obiekt EntityKey.</span><span class="sxs-lookup"><span data-stu-id="2dab4-126">You can use the key annotation to specify which property is to be used as the EntityKey.</span></span>

``` csharp
    public class Blog
    {
        [Key]
        public int PrimaryTrackingKey { get; set; }
        public string Title { get; set; }
        public string BloggerName { get; set;}
        public virtual ICollection<Post> Posts { get; set; }
    }
```

<span data-ttu-id="2dab4-127">Jeśli używasz funkcji generowania bazy danych w pierwszej kolejności, tabela blogu będzie miała kolumnę klucza podstawowego o nazwie PrimaryTrackingKey, która również jest definiowana jako tożsamość domyślnie.</span><span class="sxs-lookup"><span data-stu-id="2dab4-127">If you are using code first’s database generation feature, the Blog table will have a primary key column named PrimaryTrackingKey, which is also defined as Identity by default.</span></span>

![Tabela blogów z kluczem podstawowym](~/ef6/media/jj591583-figure01.png)

### <a name="composite-keys"></a><span data-ttu-id="2dab4-129">Klucze złożone</span><span class="sxs-lookup"><span data-stu-id="2dab4-129">Composite keys</span></span>

<span data-ttu-id="2dab4-130">Entity Framework obsługuje klucze złożone — klucze podstawowe, które składają się z więcej niż jednej właściwości.</span><span class="sxs-lookup"><span data-stu-id="2dab4-130">Entity Framework supports composite keys - primary keys that consist of more than one property.</span></span> <span data-ttu-id="2dab4-131">Na przykład może istnieć Klasa paszportu, której klucz podstawowy jest kombinacją PassportNumber i IssuingCountry.</span><span class="sxs-lookup"><span data-stu-id="2dab4-131">For example, you could have a Passport class whose primary key is a combination of PassportNumber and IssuingCountry.</span></span>

``` csharp
    public class Passport
    {
        [Key]
        public int PassportNumber { get; set; }
        [Key]
        public string IssuingCountry { get; set; }
        public DateTime Issued { get; set; }
        public DateTime Expires { get; set; }
    }
```

<span data-ttu-id="2dab4-132">Próba użycia powyższej klasy w modelu EF spowoduje, że `InvalidOperationException`:</span><span class="sxs-lookup"><span data-stu-id="2dab4-132">Attempting to use the above class in your EF model would result in an `InvalidOperationException`:</span></span>

<span data-ttu-id="2dab4-133">*Nie można określić porządkowania złożonego klucza podstawowego dla typu "Passport". Użyj metody ColumnAttribute lub Haskey —, aby określić zamówienie dla złożonych kluczy podstawowych.*</span><span class="sxs-lookup"><span data-stu-id="2dab4-133">*Unable to determine composite primary key ordering for type 'Passport'. Use the ColumnAttribute or the HasKey method to specify an order for composite primary keys.*</span></span>

<span data-ttu-id="2dab4-134">Aby można było używać kluczy złożonych, Entity Framework wymaga zdefiniowania zamówienia dla właściwości klucza.</span><span class="sxs-lookup"><span data-stu-id="2dab4-134">In order to use composite keys, Entity Framework requires you to define an order for the key properties.</span></span> <span data-ttu-id="2dab4-135">Można to zrobić za pomocą adnotacji kolumny, aby określić kolejność.</span><span class="sxs-lookup"><span data-stu-id="2dab4-135">You can do this by using the Column annotation to specify an order.</span></span>

>[!NOTE]
> <span data-ttu-id="2dab4-136">Wartość Order jest względna (a nie oparta na indeksach), więc można użyć dowolnych wartości.</span><span class="sxs-lookup"><span data-stu-id="2dab4-136">The order value is relative (rather than index based) so any values can be used.</span></span> <span data-ttu-id="2dab4-137">Na przykład 100 i 200 byłyby dopuszczalne zamiast 1 i 2.</span><span class="sxs-lookup"><span data-stu-id="2dab4-137">For example, 100 and 200 would be acceptable in place of 1 and 2.</span></span>

``` csharp
    public class Passport
    {
        [Key]
        [Column(Order=1)]
        public int PassportNumber { get; set; }
        [Key]
        [Column(Order = 2)]
        public string IssuingCountry { get; set; }
        public DateTime Issued { get; set; }
        public DateTime Expires { get; set; }
    }
```

<span data-ttu-id="2dab4-138">Jeśli masz jednostki z złożonymi kluczami obcymi, musisz określić tę samą kolejność kolumn, która została użyta dla odpowiednich właściwości klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="2dab4-138">If you have entities with composite foreign keys, then you must specify the same column ordering that you used for the corresponding primary key properties.</span></span>

<span data-ttu-id="2dab4-139">Tylko kolejność względna we właściwościach klucza obcego musi być taka sama, dokładne wartości przypisane do **kolejności** nie muszą być zgodne.</span><span class="sxs-lookup"><span data-stu-id="2dab4-139">Only the relative ordering within the foreign key properties needs to be the same, the exact values assigned to **Order** do not need to match.</span></span> <span data-ttu-id="2dab4-140">Na przykład w przypadku następujących klas można użyć 3 i 4 zamiast 1 i 2.</span><span class="sxs-lookup"><span data-stu-id="2dab4-140">For example, in the following class, 3 and 4 could be used in place of 1 and 2.</span></span>

``` csharp
    public class PassportStamp
    {
        [Key]
        public int StampId { get; set; }
        public DateTime Stamped { get; set; }
        public string StampingCountry { get; set; }

        [ForeignKey("Passport")]
        [Column(Order = 1)]
        public int PassportNumber { get; set; }

        [ForeignKey("Passport")]
        [Column(Order = 2)]
        public string IssuingCountry { get; set; }

        public Passport Passport { get; set; }
    }
```

## <a name="required"></a><span data-ttu-id="2dab4-141">Wymagany</span><span class="sxs-lookup"><span data-stu-id="2dab4-141">Required</span></span>

<span data-ttu-id="2dab4-142">Wymagana adnotacja informuje Dr, że określona właściwość jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="2dab4-142">The Required annotation tells EF that a particular property is required.</span></span>

<span data-ttu-id="2dab4-143">Dodanie wymagane do właściwości title spowoduje wymuszenie EF (i MVC) w celu upewnienia się, że właściwość zawiera dane.</span><span class="sxs-lookup"><span data-stu-id="2dab4-143">Adding Required to the Title property will force EF (and MVC) to ensure that the property has data in it.</span></span>

``` csharp
    [Required]
    public string Title { get; set; }
```

<span data-ttu-id="2dab4-144">W przypadku braku dodatkowych zmian kodu lub znaczników w aplikacji aplikacja MVC przeprowadzi weryfikację po stronie klienta, nawet dynamicznie kompilując komunikat przy użyciu nazw właściwości i adnotacji.</span><span class="sxs-lookup"><span data-stu-id="2dab4-144">With no additional code or markup changes in the application, an MVC application will perform client side validation, even dynamically building a message using the property and annotation names.</span></span>

![Błąd tworzenia strony z tytułem](~/ef6/media/jj591583-figure02.png)

<span data-ttu-id="2dab4-146">Wymagany atrybut będzie również miał wpływ na wytworzoną bazę danych, ponieważ nie dopuszcza ona wartości null dla mapowanej właściwości.</span><span class="sxs-lookup"><span data-stu-id="2dab4-146">The Required attribute will also affect the generated database by making the mapped property non-nullable.</span></span> <span data-ttu-id="2dab4-147">Zwróć uwagę, że pole title zostało zmienione na wartość "not null".</span><span class="sxs-lookup"><span data-stu-id="2dab4-147">Notice that the Title field has changed to “not null”.</span></span>

>[!NOTE]
> <span data-ttu-id="2dab4-148">W niektórych przypadkach może nie być możliwe, aby kolumna w bazie danych nie mogła dopuszczać wartości null, mimo że właściwość jest wymagana.</span><span class="sxs-lookup"><span data-stu-id="2dab4-148">In some cases it may not be possible for the column in the database to be non-nullable even though the property is required.</span></span> <span data-ttu-id="2dab4-149">Na przykład w przypadku korzystania z danych strategii dziedziczenia TPH dla wielu typów jest przechowywany w jednej tabeli.</span><span class="sxs-lookup"><span data-stu-id="2dab4-149">For example, when using a TPH inheritance strategy data for multiple types is stored in a single table.</span></span> <span data-ttu-id="2dab4-150">Jeśli typ pochodny zawiera wymaganą właściwość, nie można wprowadzić wartości null w kolumnie, ponieważ nie wszystkie typy w hierarchii będą mieć tę właściwość.</span><span class="sxs-lookup"><span data-stu-id="2dab4-150">If a derived type includes a required property the column cannot be made non-nullable since not all types in the hierarchy will have this property.</span></span>

 

![Tabela blogów](~/ef6/media/jj591583-figure03.png)

 

## <a name="maxlength-and-minlength"></a><span data-ttu-id="2dab4-152">MaxLength i MinLength</span><span class="sxs-lookup"><span data-stu-id="2dab4-152">MaxLength and MinLength</span></span>

<span data-ttu-id="2dab4-153">Atrybuty MaxLength i MinLength umożliwiają określenie dodatkowych walidacji właściwości, tak jak w przypadku, gdy jest to wymagane.</span><span class="sxs-lookup"><span data-stu-id="2dab4-153">The MaxLength and MinLength attributes allow you to specify additional property validations, just as you did with Required.</span></span>

<span data-ttu-id="2dab4-154">Oto wymagania dotyczące długości.</span><span class="sxs-lookup"><span data-stu-id="2dab4-154">Here is the BloggerName with length requirements.</span></span> <span data-ttu-id="2dab4-155">W przykładzie pokazano również, jak łączyć atrybuty.</span><span class="sxs-lookup"><span data-stu-id="2dab4-155">The example also demonstrates how to combine attributes.</span></span>

``` csharp
    [MaxLength(10),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="2dab4-156">Adnotacja MaxLength będzie miała wpływ na bazę danych, ustawiając długość właściwości na 10.</span><span class="sxs-lookup"><span data-stu-id="2dab4-156">The MaxLength annotation will impact the database by setting the property’s length to 10.</span></span>

![Tabela blogów pokazująca maksymalną długość w kolumnie Bloggername](~/ef6/media/jj591583-figure04.png)

<span data-ttu-id="2dab4-158">Adnotacja po stronie klienta MVC i adnotacja po stronie serwera EF 4,1 będą zarówno uwzględniane w tej weryfikacji, jak i ponownie dynamicznie kompilują komunikat o błędzie: "pole Bloggername musi być ciągiem lub typem tablicy o maksymalnej długości" 10 "." Ten komunikat jest nieco długi.</span><span class="sxs-lookup"><span data-stu-id="2dab4-158">MVC client-side annotation and EF 4.1 server-side annotation will both honor this validation, again dynamically building an error message: “The field BloggerName must be a string or array type with a maximum length of '10'.” That message is a little long.</span></span> <span data-ttu-id="2dab4-159">Wiele adnotacji pozwala określić komunikat o błędzie z atrybutem ErrorMessage.</span><span class="sxs-lookup"><span data-stu-id="2dab4-159">Many annotations let you specify an error message with the ErrorMessage attribute.</span></span>

``` csharp
    [MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="2dab4-160">W wymaganej adnotacji można także określić ErrorMessage.</span><span class="sxs-lookup"><span data-stu-id="2dab4-160">You can also specify ErrorMessage in the Required annotation.</span></span>

![Utwórz stronę z niestandardowym komunikatem o błędzie](~/ef6/media/jj591583-figure05.png)

 

## <a name="notmapped"></a><span data-ttu-id="2dab4-162">NotMapped</span><span class="sxs-lookup"><span data-stu-id="2dab4-162">NotMapped</span></span>

<span data-ttu-id="2dab4-163">Pierwsza Konwencja kodu wskazuje, że każda właściwość o obsługiwanym typie danych jest reprezentowana w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="2dab4-163">Code first convention dictates that every property that is of a supported data type is represented in the database.</span></span> <span data-ttu-id="2dab4-164">Ale nie zawsze jest to przypadek w aplikacjach.</span><span class="sxs-lookup"><span data-stu-id="2dab4-164">But this isn’t always the case in your applications.</span></span> <span data-ttu-id="2dab4-165">Na przykład może istnieć właściwość w klasie blogu, która tworzy kod na podstawie pól tytuł i Bloggername.</span><span class="sxs-lookup"><span data-stu-id="2dab4-165">For example you might have a property in the Blog class that creates a code based on the Title and BloggerName fields.</span></span> <span data-ttu-id="2dab4-166">Tę właściwość można utworzyć dynamicznie i nie musi być przechowywana.</span><span class="sxs-lookup"><span data-stu-id="2dab4-166">That property can be created dynamically and does not need to be stored.</span></span> <span data-ttu-id="2dab4-167">Można oznaczyć wszystkie właściwości, które nie są mapowane do bazy danych za pomocą adnotacji NotMapped, takiej jak ta właściwość BlogCode.</span><span class="sxs-lookup"><span data-stu-id="2dab4-167">You can mark any properties that do not map to the database with the NotMapped annotation such as this BlogCode property.</span></span>

``` csharp
    [NotMapped]
    public string BlogCode
    {
        get
        {
            return Title.Substring(0, 1) + ":" + BloggerName.Substring(0, 1);
        }
    }
```

 

## <a name="complextype"></a><span data-ttu-id="2dab4-168">ComplexType</span><span class="sxs-lookup"><span data-stu-id="2dab4-168">ComplexType</span></span>

<span data-ttu-id="2dab4-169">Nie jest bardzo rzadko opisywanie jednostek domeny w zestawie klas, a następnie warstwowe klasy do opisywania kompletnej jednostki.</span><span class="sxs-lookup"><span data-stu-id="2dab4-169">It’s not uncommon to describe your domain entities across a set of classes and then layer those classes to describe a complete entity.</span></span> <span data-ttu-id="2dab4-170">Na przykład możesz dodać klasę o nazwie BlogDetails do modelu.</span><span class="sxs-lookup"><span data-stu-id="2dab4-170">For example, you may add a class called BlogDetails to your model.</span></span>

``` csharp
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

<span data-ttu-id="2dab4-171">Zwróć uwagę, że BlogDetails nie ma żadnego typu właściwości klucza.</span><span class="sxs-lookup"><span data-stu-id="2dab4-171">Notice that BlogDetails does not have any type of key property.</span></span> <span data-ttu-id="2dab4-172">W projekcie opartym na domenie BlogDetails jest określany jako obiekt wartości.</span><span class="sxs-lookup"><span data-stu-id="2dab4-172">In domain driven design, BlogDetails is referred to as a value object.</span></span> <span data-ttu-id="2dab4-173">Entity Framework odnosi się do obiektów wartości jako typów złożonych.</span><span class="sxs-lookup"><span data-stu-id="2dab4-173">Entity Framework refers to value objects as complex types.</span></span><span data-ttu-id="2dab4-174">  Typy złożone nie mogą być śledzone we własnym zakresie.</span><span class="sxs-lookup"><span data-stu-id="2dab4-174">  Complex types cannot be tracked on their own.</span></span>

<span data-ttu-id="2dab4-175">Jednak jako właściwość w klasie blogu BlogDetails ją będzie śledzony jako część obiektu blogu.</span><span class="sxs-lookup"><span data-stu-id="2dab4-175">However as a property in the Blog class, BlogDetails it will be tracked as part of a Blog object.</span></span> <span data-ttu-id="2dab4-176">Aby najpierw rozpoznać kod, należy oznaczyć klasę BlogDetails jako ComplexType.</span><span class="sxs-lookup"><span data-stu-id="2dab4-176">In order for code first to recognize this, you must mark the BlogDetails class as a ComplexType.</span></span>

``` csharp
    [ComplexType]
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

<span data-ttu-id="2dab4-177">Teraz możesz dodać właściwość w klasie blogu, aby reprezentować BlogDetails dla tego bloga.</span><span class="sxs-lookup"><span data-stu-id="2dab4-177">Now you can add a property in the Blog class to represent the BlogDetails for that blog.</span></span>

``` csharp
        public BlogDetails BlogDetail { get; set; }
```

<span data-ttu-id="2dab4-178">W bazie danych tabela blogów zawiera wszystkie właściwości blogu, w tym właściwości zawarte we właściwości BlogDetail.</span><span class="sxs-lookup"><span data-stu-id="2dab4-178">In the database, the Blog table will contain all of the properties of the blog including the properties contained in its BlogDetail property.</span></span> <span data-ttu-id="2dab4-179">Domyślnie każda z nich jest poprzedzona nazwą typu złożonego, BlogDetail.</span><span class="sxs-lookup"><span data-stu-id="2dab4-179">By default, each one is preceded with the name of the complex type, BlogDetail.</span></span>

![Tabela blogów z typem złożonym](~/ef6/media/jj591583-figure06.png)


## <a name="concurrencycheck"></a><span data-ttu-id="2dab4-181">ConcurrencyCheck</span><span class="sxs-lookup"><span data-stu-id="2dab4-181">ConcurrencyCheck</span></span>

<span data-ttu-id="2dab4-182">Adnotacja ConcurrencyCheck umożliwia Oflagowanie jednej lub wielu właściwości, które mają być używane na potrzeby sprawdzania współbieżności w bazie danych, gdy użytkownik edytuje lub usunie jednostkę.</span><span class="sxs-lookup"><span data-stu-id="2dab4-182">The ConcurrencyCheck annotation allows you to flag one or more properties to be used for concurrency checking in the database when a user edits or deletes an entity.</span></span> <span data-ttu-id="2dab4-183">Jeśli pracujesz z projektantem EF, jest to zgodne z ustawieniem ConcurrencyMode właściwości, który został naprawiony.</span><span class="sxs-lookup"><span data-stu-id="2dab4-183">If you've been working with the EF Designer, this aligns with setting a property's ConcurrencyMode to Fixed.</span></span>

<span data-ttu-id="2dab4-184">Zobaczmy, jak działa ConcurrencyCheck przez dodanie go do właściwości Bloggername.</span><span class="sxs-lookup"><span data-stu-id="2dab4-184">Let’s see how ConcurrencyCheck works by adding it to the BloggerName property.</span></span>

``` csharp
    [ConcurrencyCheck, MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="2dab4-185">Gdy metody SaveChanges jest wywoływana, ze względu na adnotację ConcurrencyCheck w polu Bloggername, oryginalna wartość tej właściwości zostanie użyta w ramach aktualizacji.</span><span class="sxs-lookup"><span data-stu-id="2dab4-185">When SaveChanges is called, because of the ConcurrencyCheck annotation on the BloggerName field, the original value of that property will be used in the update.</span></span> <span data-ttu-id="2dab4-186">Polecenie spróbuje zlokalizować prawidłowy wiersz przez filtrowanie nie tylko dla wartości klucza, ale również od oryginalnej wartości Bloggername.</span><span class="sxs-lookup"><span data-stu-id="2dab4-186">The command will attempt to locate the correct row by filtering not only on the key value but also on the original value of BloggerName.</span></span><span data-ttu-id="2dab4-187">  Poniżej przedstawiono krytyczne części polecenia aktualizacji wysyłane do bazy danych, w którym można zobaczyć, że polecenie zaktualizuje wiersz, który ma PrimaryTrackingKey ma wartość 1, a w polu Bloggername elementu "Julie", który był oryginalną wartością, gdy ten blog został pobrany z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="2dab4-187">  Here are the critical parts of the UPDATE command sent to the database, where you can see the command will update the row that has a PrimaryTrackingKey is 1 and a BloggerName of “Julie” which was the original value when that blog was retrieved from the database.</span></span>

``` SQL
    where (([PrimaryTrackingKey] = @4) and ([BloggerName] = @5))
    @4=1,@5=N'Julie'
```

<span data-ttu-id="2dab4-188">Jeśli ktoś zmienił nazwę usługi Blogger dla tego bloga w międzyczasie, ta aktualizacja zakończy się niepowodzeniem i uzyskasz DbUpdateConcurrencyException, które należy obsłużyć.</span><span class="sxs-lookup"><span data-stu-id="2dab4-188">If someone has changed the blogger name for that blog in the meantime, this update will fail and you’ll get a DbUpdateConcurrencyException that you'll need to handle.</span></span>

 

## <a name="timestamp"></a><span data-ttu-id="2dab4-189">TimeStamp</span><span class="sxs-lookup"><span data-stu-id="2dab4-189">TimeStamp</span></span>

<span data-ttu-id="2dab4-190">Jest to bardziej powszechne, aby użyć pól rowversion lub timestamp do sprawdzania współbieżności.</span><span class="sxs-lookup"><span data-stu-id="2dab4-190">It's more common to use rowversion or timestamp fields for concurrency checking.</span></span> <span data-ttu-id="2dab4-191">Zamiast używać adnotacji ConcurrencyCheck, można użyć bardziej specyficznej adnotacji sygnatury czasowej, tak długo, jak typ właściwości jest tablicą bajtów.</span><span class="sxs-lookup"><span data-stu-id="2dab4-191">But rather than using the ConcurrencyCheck annotation, you can use the more specific TimeStamp annotation as long as the type of the property is byte array.</span></span> <span data-ttu-id="2dab4-192">Kod najpierw traktuje właściwości sygnatur czasowych tak samo jak właściwości ConcurrencyCheck, ale również upewnij się, że pole bazy danych, które generuje najpierw kod, nie dopuszcza wartości null.</span><span class="sxs-lookup"><span data-stu-id="2dab4-192">Code first will treat Timestamp properties the same as ConcurrencyCheck properties, but it will also ensure that the database field that code first generates is non-nullable.</span></span> <span data-ttu-id="2dab4-193">W danej klasie można mieć tylko jedną właściwość timestamp.</span><span class="sxs-lookup"><span data-stu-id="2dab4-193">You can only have one timestamp property in a given class.</span></span>

<span data-ttu-id="2dab4-194">Dodanie następującej właściwości do klasy bloga:</span><span class="sxs-lookup"><span data-stu-id="2dab4-194">Adding the following property to the Blog class:</span></span>

``` csharp
    [Timestamp]
    public Byte[] TimeStamp { get; set; }
```

<span data-ttu-id="2dab4-195">wyniki w kodzie najpierw tworzą kolumnę znacznika czasu niedopuszczające wartości null w tabeli bazy danych.</span><span class="sxs-lookup"><span data-stu-id="2dab4-195">results in code first creating a non-nullable timestamp column in the database table.</span></span>

![Tabela blogów z kolumną sygnatury czasowej](~/ef6/media/jj591583-figure07.png)

 

## <a name="table-and-column"></a><span data-ttu-id="2dab4-197">Tabela i kolumna</span><span class="sxs-lookup"><span data-stu-id="2dab4-197">Table and Column</span></span>

<span data-ttu-id="2dab4-198">Jeśli chcesz Code First utworzyć bazę danych, możesz zmienić nazwę tabel i kolumn, które tworzy.</span><span class="sxs-lookup"><span data-stu-id="2dab4-198">If you are letting Code First create the database, you may want to change the name of the tables and columns it is creating.</span></span> <span data-ttu-id="2dab4-199">Możesz również użyć Code First z istniejącą bazą danych.</span><span class="sxs-lookup"><span data-stu-id="2dab4-199">You can also use Code First with an existing database.</span></span> <span data-ttu-id="2dab4-200">Ale nie zawsze jest to przypadek, że nazwy klas i właściwości w domenie są zgodne z nazwami tabel i kolumn w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="2dab4-200">But it's not always the case that the names of the classes and properties in your domain match the names of the tables and columns in your database.</span></span>

<span data-ttu-id="2dab4-201">Moja Klasa jest nazywana blogiem i Konwencją, kod najpierw przyjmuje, że zostanie on zmapowany do tabeli o nazwie blogi.</span><span class="sxs-lookup"><span data-stu-id="2dab4-201">My class is named Blog and by convention, code first presumes this will map to a table named Blogs.</span></span> <span data-ttu-id="2dab4-202">Jeśli tak nie jest, możesz określić nazwę tabeli z atrybutem tabeli.</span><span class="sxs-lookup"><span data-stu-id="2dab4-202">If that's not the case you can specify the name of the table with the Table attribute.</span></span> <span data-ttu-id="2dab4-203">Na przykład adnotacja określa, że nazwa tabeli to InternalBlogs.</span><span class="sxs-lookup"><span data-stu-id="2dab4-203">Here for example, the annotation is specifying that the table name is InternalBlogs.</span></span>

``` csharp
    [Table("InternalBlogs")]
    public class Blog
```

<span data-ttu-id="2dab4-204">Adnotacja kolumny to więcej doświadczony w określaniu atrybutów mapowanej kolumny.</span><span class="sxs-lookup"><span data-stu-id="2dab4-204">The Column annotation is a more adept in specifying the attributes of a mapped column.</span></span> <span data-ttu-id="2dab4-205">Można określić nazwę, typ danych, a nawet kolejność, w jakiej w tabeli pojawia się kolumna.</span><span class="sxs-lookup"><span data-stu-id="2dab4-205">You can stipulate a name, data type or even the order in which a column appears in the table.</span></span> <span data-ttu-id="2dab4-206">Oto przykład atrybutu Column.</span><span class="sxs-lookup"><span data-stu-id="2dab4-206">Here is an example of the Column attribute.</span></span>

``` csharp
    [Column("BlogDescription", TypeName="ntext")]
    public String Description {get;set;}
```

<span data-ttu-id="2dab4-207">Nie należy mylić atrybutu TypeName kolumny z adnotacją datadatatype.</span><span class="sxs-lookup"><span data-stu-id="2dab4-207">Don’t confuse Column’s TypeName attribute with the DataType DataAnnotation.</span></span> <span data-ttu-id="2dab4-208">DataType jest adnotacją użytą dla interfejsu użytkownika i jest ignorowana przez Code First.</span><span class="sxs-lookup"><span data-stu-id="2dab4-208">DataType is an annotation used for the UI and is ignored by Code First.</span></span>

<span data-ttu-id="2dab4-209">Poniżej znajduje się tabela po jej ponownej generacji.</span><span class="sxs-lookup"><span data-stu-id="2dab4-209">Here is the table after it’s been regenerated.</span></span> <span data-ttu-id="2dab4-210">Nazwa tabeli została zmieniona na InternalBlogs, a kolumna opisu typu złożonego jest teraz BlogDescription.</span><span class="sxs-lookup"><span data-stu-id="2dab4-210">The table name has changed to InternalBlogs and Description column from the complex type is now BlogDescription.</span></span> <span data-ttu-id="2dab4-211">Ponieważ nazwa została określona w adnotacji, kod pierwszy nie będzie używać konwencji rozpoczynającej nazwę kolumny o nazwie typu złożonego.</span><span class="sxs-lookup"><span data-stu-id="2dab4-211">Because the name was specified in the annotation, code first will not use the convention of starting the column name with the name of the complex type.</span></span>

![Zmieniono nazwę tabeli i kolumny blogów](~/ef6/media/jj591583-figure08.png)

 

## <a name="databasegenerated"></a><span data-ttu-id="2dab4-213">DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="2dab4-213">DatabaseGenerated</span></span>

<span data-ttu-id="2dab4-214">Ważne funkcje bazy danych to możliwość posiadania obliczanych właściwości.</span><span class="sxs-lookup"><span data-stu-id="2dab4-214">An important database features is the ability to have computed properties.</span></span> <span data-ttu-id="2dab4-215">Jeśli mapujesz klasy Code First do tabel zawierających kolumny obliczane, nie chcesz, aby Entity Framework do aktualizacji tych kolumn.</span><span class="sxs-lookup"><span data-stu-id="2dab4-215">If you're mapping your Code First classes to tables that contain computed columns, you don't want Entity Framework to try to update those columns.</span></span> <span data-ttu-id="2dab4-216">Ale chcesz, aby program Dr zwracał te wartości z bazy danych po wstawieniu lub zaktualizowaniu danych.</span><span class="sxs-lookup"><span data-stu-id="2dab4-216">But you do want EF to return those values from the database after you've inserted or updated data.</span></span> <span data-ttu-id="2dab4-217">Możesz użyć adnotacji DatabaseGenerated, aby oflagować te właściwości w klasie wraz z obliczanym wyliczeniem.</span><span class="sxs-lookup"><span data-stu-id="2dab4-217">You can use the DatabaseGenerated annotation to flag those properties in your class along with the Computed enum.</span></span> <span data-ttu-id="2dab4-218">Inne wyliczenia to brak i tożsamość.</span><span class="sxs-lookup"><span data-stu-id="2dab4-218">Other enums are None and Identity.</span></span>

``` csharp
    [DatabaseGenerated(DatabaseGeneratedOption.Computed)]
    public DateTime DateCreated { get; set; }
```

<span data-ttu-id="2dab4-219">Możesz użyć bazy danych wygenerowanej w kolumnach bajtowych lub sygnatur czasowych, gdy pierwszy generuje bazę danych, w przeciwnym razie należy używać jej tylko podczas wskazywania istniejących baz danych, ponieważ kod nie będzie mógł określić formuły dla kolumny obliczanej.</span><span class="sxs-lookup"><span data-stu-id="2dab4-219">You can use database generated on byte or timestamp columns when code first is generating the database, otherwise you should only use this when pointing to existing databases because code first won't be able to determine the formula for the computed column.</span></span>

<span data-ttu-id="2dab4-220">Przeczytasz powyżej, ponieważ domyślnie właściwość klucza, która jest liczbą całkowitą, staje się kluczem tożsamości w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="2dab4-220">You read above that by default, a key property that is an integer will become an identity key in the database.</span></span> <span data-ttu-id="2dab4-221">Jest to takie samo, jak ustawienie DatabaseGenerated na DatabaseGeneratedOption. Identity.</span><span class="sxs-lookup"><span data-stu-id="2dab4-221">That would be the same as setting DatabaseGenerated to DatabaseGeneratedOption.Identity.</span></span> <span data-ttu-id="2dab4-222">Jeśli nie chcesz, aby był on kluczem tożsamości, możesz ustawić wartość na DatabaseGeneratedOption. None.</span><span class="sxs-lookup"><span data-stu-id="2dab4-222">If you do not want it to be an identity key, you can set the value to DatabaseGeneratedOption.None.</span></span>

 

## <a name="index"></a><span data-ttu-id="2dab4-223">Indeks</span><span class="sxs-lookup"><span data-stu-id="2dab4-223">Index</span></span>

> [!NOTE]
> <span data-ttu-id="2dab4-224">**Dr 6.1 tylko** -atrybut indeksu został wprowadzony w Entity Framework 6,1.</span><span class="sxs-lookup"><span data-stu-id="2dab4-224">**EF6.1 Onwards Only** - The Index attribute was introduced in Entity Framework 6.1.</span></span> <span data-ttu-id="2dab4-225">Jeśli używasz wcześniejszej wersji, informacje przedstawione w tej sekcji nie są stosowane.</span><span class="sxs-lookup"><span data-stu-id="2dab4-225">If you are using an earlier version the information in this section does not apply.</span></span>

<span data-ttu-id="2dab4-226">Możesz utworzyć indeks dla jednej lub wielu kolumn przy użyciu **indeksuattribute**.</span><span class="sxs-lookup"><span data-stu-id="2dab4-226">You can create an index on one or more columns using the **IndexAttribute**.</span></span> <span data-ttu-id="2dab4-227">Dodanie atrybutu do co najmniej jednej właściwości spowoduje, że program Dr utworzy odpowiedni indeks w bazie danych podczas tworzenia bazy danych lub tworzy szkielet odpowiednich wywołań funkcji **indeksu** w przypadku korzystania z migracje Code First.</span><span class="sxs-lookup"><span data-stu-id="2dab4-227">Adding the attribute to one or more properties will cause EF to create the corresponding index in the database when it creates the database, or scaffold the corresponding **CreateIndex** calls if you are using Code First Migrations.</span></span>

<span data-ttu-id="2dab4-228">Na przykład poniższy kod spowoduje utworzenie indeksu w kolumnie **Klasyfikacja** tabeli **wpisy** w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="2dab4-228">For example, the following code will result in an index being created on the **Rating** column of the **Posts** table in the database.</span></span>

``` csharp
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }
        [Index]
        public int Rating { get; set; }
        public int BlogId { get; set; }
    }
```

<span data-ttu-id="2dab4-229">Domyślnie indeks będzie miał nazwę **\_, &lt;nazwa właściwości&gt;** (IX\_Klasyfikacja w powyższym przykładzie).</span><span class="sxs-lookup"><span data-stu-id="2dab4-229">By default, the index will be named **IX\_&lt;property name&gt;** (IX\_Rating in the above example).</span></span> <span data-ttu-id="2dab4-230">Możesz również określić nazwę dla indeksu.</span><span class="sxs-lookup"><span data-stu-id="2dab4-230">You can also specify a name for the index though.</span></span> <span data-ttu-id="2dab4-231">Poniższy przykład określa, że indeks powinien mieć nazwę **PostRatingIndex**.</span><span class="sxs-lookup"><span data-stu-id="2dab4-231">The following example specifies that the index should be named **PostRatingIndex**.</span></span>

``` csharp
    [Index("PostRatingIndex")]
    public int Rating { get; set; }
```

<span data-ttu-id="2dab4-232">Domyślnie indeksy nie są unikatowe, ale można użyć **Isuniqued** nazwanego parametru, aby określić, że indeks powinien być unikatowy.</span><span class="sxs-lookup"><span data-stu-id="2dab4-232">By default, indexes are non-unique, but you can use the **IsUnique** named parameter to specify that an index should be unique.</span></span> <span data-ttu-id="2dab4-233">W poniższym przykładzie wprowadzono unikatowy indeks nazwy logowania **użytkownika**.</span><span class="sxs-lookup"><span data-stu-id="2dab4-233">The following example introduces a unique index on a **User**'s login name.</span></span>

``` csharp
    public class User
    {
        public int UserId { get; set; }

        [Index(IsUnique = true)]
        [StringLength(200)]
        public string Username { get; set; }

        public string DisplayName { get; set; }
    }
```

### <a name="multiple-column-indexes"></a><span data-ttu-id="2dab4-234">Indeksy z wieloma kolumnami</span><span class="sxs-lookup"><span data-stu-id="2dab4-234">Multiple-Column Indexes</span></span>

<span data-ttu-id="2dab4-235">Indeksy obejmujące wiele kolumn są określane przy użyciu tej samej nazwy w wielu adnotacjach indeksu dla danej tabeli.</span><span class="sxs-lookup"><span data-stu-id="2dab4-235">Indexes that span multiple columns are specified by using the same name in multiple Index annotations for a given table.</span></span> <span data-ttu-id="2dab4-236">Podczas tworzenia indeksów wielokolumnowych należy określić kolejność kolumn w indeksie.</span><span class="sxs-lookup"><span data-stu-id="2dab4-236">When you create multi-column indexes, you need to specify an order for the columns in the index.</span></span> <span data-ttu-id="2dab4-237">Na przykład poniższy kod tworzy indeks z wielokolumnową **klasyfikacją** i **BlogId** o nazwie **IX\_BlogIdAndRating**.</span><span class="sxs-lookup"><span data-stu-id="2dab4-237">For example, the following code creates a multi-column index on **Rating** and **BlogId** called **IX\_BlogIdAndRating**.</span></span> <span data-ttu-id="2dab4-238">**BlogId** jest pierwszą kolumną w indeksie i **klasyfikacją** jest drugi.</span><span class="sxs-lookup"><span data-stu-id="2dab4-238">**BlogId** is the first column in the index and **Rating** is the second.</span></span>

``` csharp
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }
        [Index("IX_BlogIdAndRating", 2)]
        public int Rating { get; set; }
        [Index("IX_BlogIdAndRating", 1)]
        public int BlogId { get; set; }
    }
```

 

## <a name="relationship-attributes-inverseproperty-and-foreignkey"></a><span data-ttu-id="2dab4-239">Atrybuty relacji: InverseProperty i ForeignKey</span><span class="sxs-lookup"><span data-stu-id="2dab4-239">Relationship Attributes: InverseProperty and ForeignKey</span></span>

> [!NOTE]
> <span data-ttu-id="2dab4-240">Ta strona zawiera informacje na temat konfigurowania relacji w modelu Code First przy użyciu adnotacji danych.</span><span class="sxs-lookup"><span data-stu-id="2dab4-240">This page provides information about setting up relationships in your Code First model using Data Annotations.</span></span> <span data-ttu-id="2dab4-241">Aby uzyskać ogólne informacje na temat relacji w EF oraz jak uzyskać dostęp do danych i manipulować nimi przy użyciu relacji, zobacz [relacje & właściwości nawigacji](~/ef6/fundamentals/relationships.md). \*</span><span class="sxs-lookup"><span data-stu-id="2dab4-241">For general information about relationships in EF and how to access and manipulate data using relationships, see [Relationships & Navigation Properties](~/ef6/fundamentals/relationships.md).\*</span></span>

<span data-ttu-id="2dab4-242">Pierwsza Konwencja kodowa będzie uwzględniać najpopularniejsze relacje w modelu, ale zdarzają się sytuacje, w których potrzebna jest pomoc.</span><span class="sxs-lookup"><span data-stu-id="2dab4-242">Code first convention will take care of the most common relationships in your model, but there are some cases where it needs help.</span></span>

<span data-ttu-id="2dab4-243">Zmiana nazwy właściwości klucza w klasie blogu stworzyła problem z relacją do opublikowania.</span><span class="sxs-lookup"><span data-stu-id="2dab4-243">Changing the name of the key property in the Blog class created a problem with its relationship to Post.</span></span> 

<span data-ttu-id="2dab4-244">Podczas generowania bazy danych, kod najpierw widzi Właściwość BlogId w klasie post i rozpoznaje ją, zgodnie z Konwencją zgodną z nazwą klasy i "identyfikatorem" jako kluczem obcym klasy blogu.</span><span class="sxs-lookup"><span data-stu-id="2dab4-244">When generating the database, code first sees the BlogId property in the Post class and recognizes it, by the convention that it matches a class name plus “Id”, as a foreign key to the Blog class.</span></span> <span data-ttu-id="2dab4-245">Nie istnieje jednak żadna właściwość BlogId w klasie blogu.</span><span class="sxs-lookup"><span data-stu-id="2dab4-245">But there is no BlogId property in the blog class.</span></span> <span data-ttu-id="2dab4-246">Rozwiązanie to służy do tworzenia właściwości nawigacji w wpisie i używania zastosowanej adnotacji DataAnnotation, aby pomóc w kodzie najpierw zrozumieć, jak utworzyć relację między dwoma klasami — za pomocą właściwości post. BlogId — a także określić ograniczenia w Database.</span><span class="sxs-lookup"><span data-stu-id="2dab4-246">The solution for this is to create a navigation property in the Post and use the Foreign DataAnnotation to help code first understand how to build the relationship between the two classes —using the Post.BlogId property — as well as how to specify constraints in the database.</span></span>

``` csharp
    public class Post
    {
            public int Id { get; set; }
            public string Title { get; set; }
            public DateTime DateCreated { get; set; }
            public string Content { get; set; }
            public int BlogId { get; set; }
            [ForeignKey("BlogId")]
            public Blog Blog { get; set; }
            public ICollection<Comment> Comments { get; set; }
    }
```

<span data-ttu-id="2dab4-247">W ograniczeniu w bazie danych zostanie wyświetlona relacja między InternalBlogs. PrimaryTrackingKey i Posts. BlogId.</span><span class="sxs-lookup"><span data-stu-id="2dab4-247">The constraint in the database shows a relationship between InternalBlogs.PrimaryTrackingKey and Posts.BlogId.</span></span> 

![Relacja między InternalBlogs. PrimaryTrackingKey i Posts. BlogId](~/ef6/media/jj591583-figure09.png)

<span data-ttu-id="2dab4-249">InverseProperty jest używany, gdy istnieje wiele relacji między klasami.</span><span class="sxs-lookup"><span data-stu-id="2dab4-249">The InverseProperty is used when you have multiple relationships between classes.</span></span>

<span data-ttu-id="2dab4-250">W klasie post możesz chcieć śledzić, kto napisał wpis w blogu, a także kto go edytował.</span><span class="sxs-lookup"><span data-stu-id="2dab4-250">In the Post class, you may want to keep track of who wrote a blog post as well as who edited it.</span></span> <span data-ttu-id="2dab4-251">Poniżej przedstawiono dwie nowe właściwości nawigacji dla klasy post.</span><span class="sxs-lookup"><span data-stu-id="2dab4-251">Here are two new navigation properties for the Post class.</span></span>

``` csharp
    public Person CreatedBy { get; set; }
    public Person UpdatedBy { get; set; }
```

<span data-ttu-id="2dab4-252">Należy również dodać do klasy Person, do której odwołują się te właściwości.</span><span class="sxs-lookup"><span data-stu-id="2dab4-252">You’ll also need to add in the Person class referenced by these properties.</span></span> <span data-ttu-id="2dab4-253">Klasa Person ma właściwości nawigacji z powrotem do wpisu, jeden dla wszystkich wpisów zapisanych przez osobę i jeden dla wszystkich wpisów zaktualizowanych przez tę osobę.</span><span class="sxs-lookup"><span data-stu-id="2dab4-253">The Person class has navigation properties back to the Post, one for all of the posts written by the person and one for all of the posts updated by that person.</span></span>

``` csharp
    public class Person
    {
            public int Id { get; set; }
            public string Name { get; set; }
            public List<Post> PostsWritten { get; set; }
            public List<Post> PostsUpdated { get; set; }
    }
```

<span data-ttu-id="2dab4-254">Kod nie jest w stanie dopasować właściwości do obu klas samodzielnie.</span><span class="sxs-lookup"><span data-stu-id="2dab4-254">Code first is not able to match up the properties in the two classes on its own.</span></span> <span data-ttu-id="2dab4-255">Tabela bazy danych dla wpisów powinna mieć jeden klucz obcy dla osoby CreatedBy i jeden dla osoby UpdatedBy, ale najpierw kod utworzy cztery właściwości klucza obcego: Person\_ID, Person\_Id1, CreatedBy\_ID i UpdatedBy\_ID.</span><span class="sxs-lookup"><span data-stu-id="2dab4-255">The database table for Posts should have one foreign key for the CreatedBy person and one for the UpdatedBy person but code first will create four foreign key properties: Person\_Id, Person\_Id1, CreatedBy\_Id and UpdatedBy\_Id.</span></span>

![Tabela ogłoszeń z dodatkowymi kluczami obcym](~/ef6/media/jj591583-figure10.png)

<span data-ttu-id="2dab4-257">Aby rozwiązać te problemy, można użyć adnotacji InverseProperty do określenia wyrównania właściwości.</span><span class="sxs-lookup"><span data-stu-id="2dab4-257">To fix these problems, you can use the InverseProperty annotation to specify the alignment of the properties.</span></span>

``` csharp
    [InverseProperty("CreatedBy")]
    public List<Post> PostsWritten { get; set; }

    [InverseProperty("UpdatedBy")]
    public List<Post> PostsUpdated { get; set; }
```

<span data-ttu-id="2dab4-258">Ponieważ właściwość PostsWritten w osobach wie, że odnosi się to do typu wpisu, spowoduje skompilowanie relacji do post. CreatedBy.</span><span class="sxs-lookup"><span data-stu-id="2dab4-258">Because the PostsWritten property in Person knows that this refers to the Post type, it will build the relationship to Post.CreatedBy.</span></span> <span data-ttu-id="2dab4-259">Podobnie PostsUpdated zostanie połączony z wpisem post. UpdatedBy.</span><span class="sxs-lookup"><span data-stu-id="2dab4-259">Similarly, PostsUpdated will be connected to Post.UpdatedBy.</span></span> <span data-ttu-id="2dab4-260">I najpierw kod nie utworzy dodatkowych kluczy obcych.</span><span class="sxs-lookup"><span data-stu-id="2dab4-260">And code first will not create the extra foreign keys.</span></span>

![Tabela ogłoszeń bez dodatkowych kluczy obcych](~/ef6/media/jj591583-figure11.png)

 

## <a name="summary"></a><span data-ttu-id="2dab4-262">Podsumowanie</span><span class="sxs-lookup"><span data-stu-id="2dab4-262">Summary</span></span>

<span data-ttu-id="2dab4-263">Adnotacje DataAnnotations nie tylko opisują weryfikację po stronie klienta i serwera w pierwszej klasie kodowej, ale umożliwiają również zwiększenie i nawet poprawienie założeń, które w pierwszej kolejności składają się na klasy na podstawie Konwencji.</span><span class="sxs-lookup"><span data-stu-id="2dab4-263">DataAnnotations not only let you describe client and server side validation in your code first classes, but they also allow you to enhance and even correct the assumptions that code first will make about your classes based on its conventions.</span></span> <span data-ttu-id="2dab4-264">Za pomocą adnotacji DataAnnotation można nie tylko dyskowo generować schemat bazy danych, ale można również mapować najpierw klasy kodu do istniejącej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="2dab4-264">With DataAnnotations you can not only drive database schema generation, but you can also map your code first classes to a pre-existing database.</span></span>

<span data-ttu-id="2dab4-265">Chociaż są bardzo elastyczne, należy pamiętać, że adnotacje danych udostępniają tylko najczęściej potrzebne zmiany konfiguracji, które można wprowadzić w przypadku pierwszej klasy kodu.</span><span class="sxs-lookup"><span data-stu-id="2dab4-265">While they are very flexible, keep in mind that DataAnnotations provide only the most commonly needed configuration changes you can make on your code first classes.</span></span> <span data-ttu-id="2dab4-266">Aby skonfigurować klasy dla niektórych przypadków brzegowych, należy odszukać alternatywny mechanizm konfiguracji, Code First interfejs API Fluent.</span><span class="sxs-lookup"><span data-stu-id="2dab4-266">To configure your classes for some of the edge cases, you should look to the alternate configuration mechanism, Code First’s Fluent API .</span></span>
