---
title: Relacje, właściwości nawigacji i klucze obce — EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 8a21ae73-6d9b-4b50-838a-ec1fddffcf37
ms.openlocfilehash: 5807f7aeeb68328821cf45ac4f8c28efa17ed399
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664198"
---
# <a name="relationships-navigation-properties-and-foreign-keys"></a><span data-ttu-id="b2697-102">Relacje, właściwości nawigacji i klucze obce</span><span class="sxs-lookup"><span data-stu-id="b2697-102">Relationships, navigation properties, and foreign keys</span></span>

<span data-ttu-id="b2697-103">Ten artykuł zawiera omówienie sposobu, w jaki Entity Framework zarządza relacjami między jednostkami.</span><span class="sxs-lookup"><span data-stu-id="b2697-103">This article gives an overview of how Entity Framework manages relationships between entities.</span></span> <span data-ttu-id="b2697-104">Przedstawiono w nim również pewne wskazówki dotyczące sposobu mapowania relacji i manipulowania nimi.</span><span class="sxs-lookup"><span data-stu-id="b2697-104">It also gives some guidance on how to map and manipulate relationships.</span></span>

## <a name="relationships-in-ef"></a><span data-ttu-id="b2697-105">Relacje w EF</span><span class="sxs-lookup"><span data-stu-id="b2697-105">Relationships in EF</span></span>

<span data-ttu-id="b2697-106">W relacyjnych bazach danych relacje (nazywane również skojarzeniami) między tabelami są definiowane za poorednictwem kluczy obcych.</span><span class="sxs-lookup"><span data-stu-id="b2697-106">In relational databases, relationships (also called associations) between tables are defined through foreign keys.</span></span> <span data-ttu-id="b2697-107">Klucz obcy (FK) to kolumna lub kombinacja kolumn, które są używane do ustanawiania i wymuszania połączenia między danymi w dwóch tabelach.</span><span class="sxs-lookup"><span data-stu-id="b2697-107">A foreign key (FK) is a column or combination of columns that is used to establish and enforce a link between the data in two tables.</span></span> <span data-ttu-id="b2697-108">Zwykle istnieją trzy typy relacji: jeden do jednego, jeden do wielu i wiele-do-wielu.</span><span class="sxs-lookup"><span data-stu-id="b2697-108">There are generally three types of relationships: one-to-one, one-to-many, and many-to-many.</span></span> <span data-ttu-id="b2697-109">W relacji jeden-do-wielu klucz obcy jest zdefiniowany w tabeli, która reprezentuje wiele końców relacji.</span><span class="sxs-lookup"><span data-stu-id="b2697-109">In a one-to-many relationship, the foreign key is defined on the table that represents the many end of the relationship.</span></span> <span data-ttu-id="b2697-110">Relacja wiele do wielu obejmuje definiowanie trzeciej tabeli (nazywanej tabelą rozgałęzienia lub sprzężenia), której klucz podstawowy składa się z kluczy obcych z obu powiązanych tabel.</span><span class="sxs-lookup"><span data-stu-id="b2697-110">The many-to-many relationship involves defining a third table (called a junction or join table), whose primary key is composed of the foreign keys from both related tables.</span></span> <span data-ttu-id="b2697-111">W relacji jeden-do-jednego klucz podstawowy działa również jako klucz obcy i nie istnieje osobna kolumna klucza obcego dla żadnej tabeli.</span><span class="sxs-lookup"><span data-stu-id="b2697-111">In a one-to-one relationship, the primary key acts additionally as a foreign key and there is no separate foreign key column for either table.</span></span>

<span data-ttu-id="b2697-112">Na poniższej ilustracji przedstawiono dwie tabele, które uczestniczą w relacji jeden do wielu.</span><span class="sxs-lookup"><span data-stu-id="b2697-112">The following image shows two tables that participate in one-to-many relationship.</span></span> <span data-ttu-id="b2697-113">Tabela **kursów** jest tabelą zależną, ponieważ zawiera kolumnę **DepartmentID** , która łączy ją z tabelą **działów** .</span><span class="sxs-lookup"><span data-stu-id="b2697-113">The **Course** table is the dependent table because it contains the **DepartmentID** column that links it to the **Department** table.</span></span>

![Tabele działów i kursów](~/ef6/media/database2.png)

<span data-ttu-id="b2697-115">W Entity Framework jednostka może być powiązana z innymi jednostkami przy użyciu skojarzenia lub relacji.</span><span class="sxs-lookup"><span data-stu-id="b2697-115">In Entity Framework, an entity can be related to other entities through an association or relationship.</span></span> <span data-ttu-id="b2697-116">Każda relacja zawiera dwa zakończenia, które opisują typ jednostki i liczebność typu (jeden, zero-lub-jeden lub wiele) dla dwóch jednostek w tej relacji.</span><span class="sxs-lookup"><span data-stu-id="b2697-116">Each relationship contains two ends that describe the entity type and the multiplicity of the type (one, zero-or-one, or many) for the two entities in that relationship.</span></span> <span data-ttu-id="b2697-117">Relacja może podlegać ograniczeniu referencyjnemu, który opisuje, co kończy się w relacji, jest rolą podmiotu zabezpieczeń, która jest rolą zależną.</span><span class="sxs-lookup"><span data-stu-id="b2697-117">The relationship may be governed by a referential constraint, which describes which end in the relationship is a principal role and which is a dependent role.</span></span>

<span data-ttu-id="b2697-118">Właściwości nawigacji umożliwiają nawigowanie po skojarzeniu między dwoma typami jednostek.</span><span class="sxs-lookup"><span data-stu-id="b2697-118">Navigation properties provide a way to navigate an association between two entity types.</span></span> <span data-ttu-id="b2697-119">Każdy obiekt może mieć właściwość nawigacji dla każdej relacji, w której uczestniczy.</span><span class="sxs-lookup"><span data-stu-id="b2697-119">Every object can have a navigation property for every relationship in which it participates.</span></span> <span data-ttu-id="b2697-120">Właściwości nawigacji umożliwiają Nawigowanie między relacjami i zarządzanie nimi w obu kierunkach, zwracając obiekt odniesienia (Jeśli liczebność jest równa jeden lub zero-lub-jeden) lub kolekcji (Jeśli liczebność ma wiele wartości).</span><span class="sxs-lookup"><span data-stu-id="b2697-120">Navigation properties allow you to navigate and manage relationships in both directions, returning either a reference object (if the multiplicity is either one or zero-or-one) or a collection (if the multiplicity is many).</span></span> <span data-ttu-id="b2697-121">Możesz również wybrać opcję nawigacji jednokierunkowej, w której przypadku należy zdefiniować właściwość nawigacji tylko dla jednego z typów, które uczestniczą w relacji, a nie na obu.</span><span class="sxs-lookup"><span data-stu-id="b2697-121">You may also choose to have one-way navigation, in which case you define the navigation property on only one of the types that participates in the relationship and not on both.</span></span>

<span data-ttu-id="b2697-122">Zalecane jest uwzględnienie w modelu właściwości, które są mapowane na klucze obce w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="b2697-122">It is recommended to include properties in the model that map to foreign keys in the database.</span></span> <span data-ttu-id="b2697-123">Za pomocą zawartych w nim właściwości klucza obcego można utworzyć lub zmienić relację, modyfikując wartość klucza obcego w obiekcie zależnym.</span><span class="sxs-lookup"><span data-stu-id="b2697-123">With foreign key properties included, you can create or change a relationship by modifying the foreign key value on a dependent object.</span></span> <span data-ttu-id="b2697-124">Tego rodzaju skojarzenie nazywa się skojarzenie klucza obcego.</span><span class="sxs-lookup"><span data-stu-id="b2697-124">This kind of association is called a foreign key association.</span></span> <span data-ttu-id="b2697-125">Korzystanie z kluczy obcych jest jeszcze bardziej istotne podczas pracy z odłączonymi jednostkami.</span><span class="sxs-lookup"><span data-stu-id="b2697-125">Using foreign keys is even more essential when working with disconnected entities.</span></span> <span data-ttu-id="b2697-126">Należy pamiętać, że podczas pracy z 1-lub-1 lub 1-do-0... 1 relacje, brak oddzielnej kolumny klucza obcego, właściwość klucza podstawowego pełni rolę klucza obcego i jest zawsze uwzględniona w modelu.</span><span class="sxs-lookup"><span data-stu-id="b2697-126">Note, that when working with 1-to-1 or 1-to-0..1 relationships, there is no separate foreign key column, the primary key property acts as the foreign key and is always included in the model.</span></span>

<span data-ttu-id="b2697-127">Gdy kolumny klucza obcego nie są uwzględnione w modelu, informacje o skojarzeniu są zarządzane jako obiekt niezależny.</span><span class="sxs-lookup"><span data-stu-id="b2697-127">When foreign key columns are not included in the model, the association information is managed as an independent object.</span></span> <span data-ttu-id="b2697-128">Relacje są śledzone za poorednictwem odwołań do obiektów zamiast właściwości klucza obcego.</span><span class="sxs-lookup"><span data-stu-id="b2697-128">Relationships are tracked through object references instead of foreign key properties.</span></span> <span data-ttu-id="b2697-129">Skojarzenie tego typu jest nazywane *skojarzeniem niezależnym*.</span><span class="sxs-lookup"><span data-stu-id="b2697-129">This type of association is called an *independent association*.</span></span> <span data-ttu-id="b2697-130">Najbardziej typowym sposobem modyfikacji *niezależnego skojarzenia* jest zmodyfikowanie właściwości nawigacji, które są generowane dla każdej jednostki, która uczestniczy w skojarzeniu.</span><span class="sxs-lookup"><span data-stu-id="b2697-130">The most common way to modify an *independent association* is to modify the navigation properties that are generated for each entity that participates in the association.</span></span>

<span data-ttu-id="b2697-131">Możesz użyć jednego lub obu typów skojarzeń w modelu.</span><span class="sxs-lookup"><span data-stu-id="b2697-131">You can choose to use one or both types of associations in your model.</span></span> <span data-ttu-id="b2697-132">Jeśli jednak masz czystą relację wiele-do-wielu, która jest połączona z tabelą sprzężenia, która zawiera tylko klucze obce, EF użyje niezależnego skojarzenia do zarządzania taką relacją wiele-do-wielu.</span><span class="sxs-lookup"><span data-stu-id="b2697-132">However, if you have a pure many-to-many relationship that is connected by a join table that contains only foreign keys, the EF will use an independent association to manage such many-to-many relationship.</span></span>   

<span data-ttu-id="b2697-133">Na poniższej ilustracji przedstawiono model koncepcyjny, który został utworzony przy użyciu Entity Framework Designer.</span><span class="sxs-lookup"><span data-stu-id="b2697-133">The following image shows a conceptual model that was created with the Entity Framework Designer.</span></span> <span data-ttu-id="b2697-134">Model zawiera dwie jednostki, które uczestniczą w relacji jeden do wielu.</span><span class="sxs-lookup"><span data-stu-id="b2697-134">The model contains two entities that participate in one-to-many relationship.</span></span> <span data-ttu-id="b2697-135">Obie jednostki mają właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="b2697-135">Both entities have navigation properties.</span></span> <span data-ttu-id="b2697-136">**Kurs** jest jednostką zależną i ma zdefiniowaną właściwość klucza obcego **DepartmentID** .</span><span class="sxs-lookup"><span data-stu-id="b2697-136">**Course** is the dependent entity and has the **DepartmentID** foreign key property defined.</span></span>

![Tabele działów i kursów z właściwościami nawigacji](~/ef6/media/relationshipefdesigner.png)

<span data-ttu-id="b2697-138">Poniższy fragment kodu przedstawia ten sam model, który został utworzony przy użyciu Code First.</span><span class="sxs-lookup"><span data-stu-id="b2697-138">The following code snippet shows the same model that was created with Code First.</span></span>

``` csharp
public class Course
{
  public int CourseID { get; set; }
  public string Title { get; set; }
  public int Credits { get; set; }
  public int DepartmentID { get; set; }
  public virtual Department Department { get; set; }
}

public class Department
{
   public Department()
   {
     this.Courses = new HashSet<Course>();
   }  
   public int DepartmentID { get; set; }
   public string Name { get; set; }
   public decimal Budget { get; set; }
   public DateTime StartDate { get; set; }
   public int? Administrator {get ; set; }
   public virtual ICollection<Course> Courses { get; set; }
}
```

## <a name="configuring-or-mapping-relationships"></a><span data-ttu-id="b2697-139">Konfigurowanie lub Mapowanie relacji</span><span class="sxs-lookup"><span data-stu-id="b2697-139">Configuring or mapping relationships</span></span>

<span data-ttu-id="b2697-140">Pozostała część tej strony zawiera informacje dotyczące uzyskiwania dostępu do danych i manipulowania nimi przy użyciu relacji.</span><span class="sxs-lookup"><span data-stu-id="b2697-140">The rest of this page covers how to access and manipulate data using relationships.</span></span> <span data-ttu-id="b2697-141">Aby uzyskać informacje na temat konfigurowania relacji w modelu, zobacz następujące strony.</span><span class="sxs-lookup"><span data-stu-id="b2697-141">For information on setting up relationships in your model, see the following pages.</span></span>

-   <span data-ttu-id="b2697-142">Aby skonfigurować relacje w Code First, zobacz [Adnotacje dotyczące danych](~/ef6/modeling/code-first/data-annotations.md) i [interfejs API Fluent — relacje](~/ef6/modeling/code-first/fluent/relationships.md).</span><span class="sxs-lookup"><span data-stu-id="b2697-142">To configure relationships in Code First, see [Data Annotations](~/ef6/modeling/code-first/data-annotations.md) and [Fluent API – Relationships](~/ef6/modeling/code-first/fluent/relationships.md).</span></span>
-   <span data-ttu-id="b2697-143">Aby skonfigurować relacje przy użyciu Entity Framework Designer, zobacz [relacje z programem Dr Designer](~/ef6/modeling/designer/relationships.md).</span><span class="sxs-lookup"><span data-stu-id="b2697-143">To configure relationships using the Entity Framework Designer, see [Relationships with the EF Designer](~/ef6/modeling/designer/relationships.md).</span></span>

## <a name="creating-and-modifying-relationships"></a><span data-ttu-id="b2697-144">Tworzenie i modyfikowanie relacji</span><span class="sxs-lookup"><span data-stu-id="b2697-144">Creating and modifying relationships</span></span>

<span data-ttu-id="b2697-145">W *skojarzeniu klucza obcego*, gdy zmienisz relację, stan obiektu zależnego ze `EntityState.Unchanged` stanem zmieni się na `EntityState.Modified` .</span><span class="sxs-lookup"><span data-stu-id="b2697-145">In a *foreign key association*, when you change the relationship, the state of a dependent object with an `EntityState.Unchanged` state changes to `EntityState.Modified`.</span></span> <span data-ttu-id="b2697-146">W niezależnej relacji zmiana relacji nie aktualizuje stanu obiektu zależnego.</span><span class="sxs-lookup"><span data-stu-id="b2697-146">In an independent relationship, changing the relationship does not update the state of the dependent object.</span></span>

<span data-ttu-id="b2697-147">W poniższych przykładach pokazano, jak za pomocą właściwości klucza obcego i właściwości nawigacji skojarzyć powiązane obiekty.</span><span class="sxs-lookup"><span data-stu-id="b2697-147">The following examples show how to use the foreign key properties and navigation properties to associate the related objects.</span></span> <span data-ttu-id="b2697-148">Przy użyciu skojarzenia klucza obcego można użyć dowolnej metody, aby zmienić, utworzyć lub zmodyfikować relacje.</span><span class="sxs-lookup"><span data-stu-id="b2697-148">With foreign key associations, you can use either method to change, create, or modify relationships.</span></span> <span data-ttu-id="b2697-149">Z niezależnymi skojarzeniami nie można użyć właściwości klucza obcego.</span><span class="sxs-lookup"><span data-stu-id="b2697-149">With independent associations, you cannot use the foreign key property.</span></span>

- <span data-ttu-id="b2697-150">Przypisanie nowej wartości do właściwości klucza obcego, jak w poniższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="b2697-150">By assigning a new value to a foreign key property, as in the following example.</span></span>  
  ``` csharp
  course.DepartmentID = newCourse.DepartmentID;
  ```

- <span data-ttu-id="b2697-151">Poniższy kod usuwa relację, ustawiając klucz obcy na **wartość null**.</span><span class="sxs-lookup"><span data-stu-id="b2697-151">The following code removes a relationship by setting the foreign key to **null**.</span></span> <span data-ttu-id="b2697-152">Należy zauważyć, że właściwość klucza obcego musi dopuszczać wartości null.</span><span class="sxs-lookup"><span data-stu-id="b2697-152">Note, that the foreign key property must be nullable.</span></span>  
  ``` csharp
  course.DepartmentID = null;
  ```

  >[!NOTE]
  > <span data-ttu-id="b2697-153">Jeśli odwołanie jest w stanie dodany (w tym przykładzie obiekt kursu), właściwość nawigacji odwołania nie zostanie zsynchronizowana z wartościami klucza nowego obiektu, dopóki nie zostanie wywołana metody SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="b2697-153">If the reference is in the added state (in this example, the course object), the reference navigation property will not be synchronized with the key values of a new object until SaveChanges is called.</span></span> <span data-ttu-id="b2697-154">Synchronizacja nie występuje, ponieważ kontekst obiektu nie zawiera trwałych kluczy dla dodanych obiektów, dopóki nie zostaną zapisane.</span><span class="sxs-lookup"><span data-stu-id="b2697-154">Synchronization does not occur because the object context does not contain permanent keys for added objects until they are saved.</span></span> <span data-ttu-id="b2697-155">Jeśli nowe obiekty muszą być w pełni zsynchronizowane zaraz po ustawieniu relacji, użyj jednej z następujących metod. \*</span><span class="sxs-lookup"><span data-stu-id="b2697-155">If you must have new objects fully synchronized as soon as you set the relationship, use one of the following methods.\*</span></span>

- <span data-ttu-id="b2697-156">Przypisanie nowego obiektu do właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="b2697-156">By assigning a new object to a navigation property.</span></span> <span data-ttu-id="b2697-157">Poniższy kod tworzy relację między kursem a `department` .</span><span class="sxs-lookup"><span data-stu-id="b2697-157">The following code creates a relationship between a course and a `department`.</span></span> <span data-ttu-id="b2697-158">Jeśli obiekty są dołączone do kontekstu, `course` jest również dodawane do `department.Courses` kolekcji, a odpowiednia właściwość klucza obcego dla `course` obiektu jest ustawiona na wartość właściwości klucza działu.</span><span class="sxs-lookup"><span data-stu-id="b2697-158">If the objects are attached to the context, the `course` is also added to the `department.Courses` collection, and the corresponding foreign key property on the `course` object is set to the key property value of the department.</span></span>  
  ``` csharp
  course.Department = department;
  ```

- <span data-ttu-id="b2697-159">Aby usunąć relację, ustaw właściwość nawigacji na `null` .</span><span class="sxs-lookup"><span data-stu-id="b2697-159">To delete the relationship, set the navigation property to `null`.</span></span> <span data-ttu-id="b2697-160">Jeśli pracujesz z Entity Frameworkami opartymi na platformie .NET 4,0, należy załadować powiązane zakończenie przed ustawieniem na wartość null.</span><span class="sxs-lookup"><span data-stu-id="b2697-160">If you are working with Entity Framework that is based on .NET 4.0, then the related end needs to be loaded before you set it to null.</span></span> <span data-ttu-id="b2697-161">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="b2697-161">For example:</span></span>   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).Load();
  course.Department = null;
  ```

  <span data-ttu-id="b2697-162">Począwszy od Entity Framework 5,0, który jest oparty na platformie .NET 4,5, można ustawić relację na wartość null bez ładowania powiązanego końca.</span><span class="sxs-lookup"><span data-stu-id="b2697-162">Starting with Entity Framework 5.0, that is based on .NET 4.5, you can set the relationship to null without loading the related end.</span></span> <span data-ttu-id="b2697-163">Możesz również ustawić bieżącą wartość na null przy użyciu następującej metody.</span><span class="sxs-lookup"><span data-stu-id="b2697-163">You can also set the current value to null using the following method.</span></span>   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).CurrentValue = null;
  ```

- <span data-ttu-id="b2697-164">Przez usunięcie lub dodanie obiektu w kolekcji jednostek.</span><span class="sxs-lookup"><span data-stu-id="b2697-164">By deleting or adding an object in an entity collection.</span></span> <span data-ttu-id="b2697-165">Na przykład można dodać obiekt typu `Course` do `department.Courses` kolekcji.</span><span class="sxs-lookup"><span data-stu-id="b2697-165">For example, you can add an object of type `Course` to the `department.Courses` collection.</span></span> <span data-ttu-id="b2697-166">Ta operacja tworzy relację między określonym **kursem** a konkretnym `department` .</span><span class="sxs-lookup"><span data-stu-id="b2697-166">This operation creates a relationship between a particular **course** and a particular `department`.</span></span> <span data-ttu-id="b2697-167">Jeśli obiekty są dołączone do kontekstu, odwołanie do działu i właściwość klucza obcego w obiekcie **kursu** zostaną ustawione na odpowiednie `department` .</span><span class="sxs-lookup"><span data-stu-id="b2697-167">If the objects are attached to the context, the department reference and the foreign key property on the **course** object will be set to the appropriate `department`.</span></span>  
  ``` csharp
  department.Courses.Add(newCourse);
  ```

- <span data-ttu-id="b2697-168">Za pomocą `ChangeRelationshipState` metody, aby zmienić stan określonej relacji między dwoma obiektami jednostek.</span><span class="sxs-lookup"><span data-stu-id="b2697-168">By using the `ChangeRelationshipState` method to change the state of the specified relationship between two entity objects.</span></span> <span data-ttu-id="b2697-169">Ta metoda jest najczęściej używana podczas pracy z aplikacjami N-warstwowymi i *niezależnym skojarzeniem* (nie można jej używać z skojarzeniem klucza obcego).</span><span class="sxs-lookup"><span data-stu-id="b2697-169">This method is most commonly used when working with N-Tier applications and an *independent association* (it cannot be used with a foreign key association).</span></span> <span data-ttu-id="b2697-170">Aby użyć tej metody, należy również najpierw rozwinąć do `ObjectContext` , jak pokazano w poniższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="b2697-170">Also, to use this method you must drop down to `ObjectContext`, as shown in the example below.</span></span>  
<span data-ttu-id="b2697-171">W poniższym przykładzie istnieje relacja wiele-do-wielu między instruktorami i kursami.</span><span class="sxs-lookup"><span data-stu-id="b2697-171">In the following example, there is a many-to-many relationship between Instructors and Courses.</span></span> <span data-ttu-id="b2697-172">Wywołanie `ChangeRelationshipState` metody i przekazanie `EntityState.Added` parametru umożliwia określenie `SchoolContext` , że relacja została dodana między dwoma obiektami:</span><span class="sxs-lookup"><span data-stu-id="b2697-172">Calling the `ChangeRelationshipState` method and passing the `EntityState.Added` parameter, lets the `SchoolContext` know that a relationship has been added between the two objects:</span></span>
  ``` csharp

  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, instructor, c => c.Instructor, EntityState.Added);
  ```

  <span data-ttu-id="b2697-173">Należy pamiętać, że w przypadku aktualizowania (nie tylko dodawania) relacji należy usunąć starą relację po dodaniu nowej:</span><span class="sxs-lookup"><span data-stu-id="b2697-173">Note that if you are updating (not just adding) a relationship, you must delete the old relationship after adding the new one:</span></span>

  ``` csharp
  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, oldInstructor, c => c.Instructor, EntityState.Deleted);
  ```

## <a name="synchronizing-the-changes-between-the-foreign-keys-and-navigation-properties"></a><span data-ttu-id="b2697-174">Synchronizowanie zmian między kluczami obcymi i właściwościami nawigacji</span><span class="sxs-lookup"><span data-stu-id="b2697-174">Synchronizing the changes between the foreign keys and navigation properties</span></span>

<span data-ttu-id="b2697-175">W przypadku zmiany relacji między obiektami dołączonymi do kontekstu przy użyciu jednej z metod opisanych powyżej Entity Framework należy zachować synchronizację kluczy obcych, odwołań i kolekcji. Entity Framework automatycznie zarządza tą synchronizacją (znaną również jako poprawka relacji) dla jednostek POCO z serwerami proxy.</span><span class="sxs-lookup"><span data-stu-id="b2697-175">When you change the relationship of the objects attached to the context by using one of the methods described above, Entity Framework needs to keep foreign keys, references, and collections in sync. Entity Framework automatically manages this synchronization (also known as relationship fix-up) for the POCO entities with proxies.</span></span> <span data-ttu-id="b2697-176">Aby uzyskać więcej informacji, zobacz [Praca z serwerami proxy](~/ef6/fundamentals/proxies.md).</span><span class="sxs-lookup"><span data-stu-id="b2697-176">For more information, see [Working with Proxies](~/ef6/fundamentals/proxies.md).</span></span>

<span data-ttu-id="b2697-177">Jeśli używasz jednostek POCO bez serwerów proxy, musisz upewnić się, że metoda **DetectChanges** jest wywoływana w celu zsynchronizowania obiektów pokrewnych w kontekście.</span><span class="sxs-lookup"><span data-stu-id="b2697-177">If you are using POCO entities without proxies, you must make sure that the **DetectChanges** method is called to synchronize the related objects in the context.</span></span> <span data-ttu-id="b2697-178">Należy zauważyć, że następujące interfejsy API automatycznie wyzwalają wywołanie **DetectChanges** .</span><span class="sxs-lookup"><span data-stu-id="b2697-178">Note that the following APIs automatically trigger a **DetectChanges** call.</span></span>

-   `DbSet.Add`
-   `DbSet.AddRange`
-   `DbSet.Remove`
-   `DbSet.RemoveRange`
-   `DbSet.Find`
-   `DbSet.Local`
-   `DbContext.SaveChanges`
-   `DbSet.Attach`
-   `DbContext.GetValidationErrors`
-   `DbContext.Entry`
-   `DbChangeTracker.Entries`
-   <span data-ttu-id="b2697-179">Wykonywanie zapytania LINQ względem`DbSet`</span><span class="sxs-lookup"><span data-stu-id="b2697-179">Executing a LINQ query against a `DbSet`</span></span>

## <a name="loading-related-objects"></a><span data-ttu-id="b2697-180">Ładowanie powiązanych obiektów</span><span class="sxs-lookup"><span data-stu-id="b2697-180">Loading related objects</span></span>

<span data-ttu-id="b2697-181">W Entity Framework często używasz właściwości nawigacji do ładowania jednostek, które są powiązane z zwróconą jednostką przez zdefiniowane skojarzenie.</span><span class="sxs-lookup"><span data-stu-id="b2697-181">In Entity Framework you commonly use navigation properties to load entities that are related to the returned entity by the defined association.</span></span> <span data-ttu-id="b2697-182">Aby uzyskać więcej informacji, zobacz [ładowanie powiązanych obiektów](~/ef6/querying/related-data.md).</span><span class="sxs-lookup"><span data-stu-id="b2697-182">For more information, see [Loading Related Objects](~/ef6/querying/related-data.md).</span></span>

> [!NOTE]
> <span data-ttu-id="b2697-183">W przypadku skojarzenia klucza obcego podczas ładowania powiązanego końca obiektu zależnego, obiekt pokrewny zostanie załadowany na podstawie wartości klucza obcego zależnego, który jest aktualnie w pamięci:</span><span class="sxs-lookup"><span data-stu-id="b2697-183">In a foreign key association, when you load a related end of a dependent object, the related object will be loaded based on the foreign key value of the dependent that is currently in memory:</span></span>

``` csharp
    // Get the course where currently DepartmentID = 2.
    Course course = context.Courses.First(c => c.DepartmentID == 2);

    // Use DepartmentID foreign key property
    // to change the association.
    course.DepartmentID = 3;

    // Load the related Department where DepartmentID = 3
    context.Entry(course).Reference(c => c.Department).Load();
```

<span data-ttu-id="b2697-184">W niezależnym skojarzeniu pokrewny koniec obiektu zależnego jest wysyłana na podstawie wartości klucza obcego, która jest obecnie w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="b2697-184">In an independent association, the related end of a dependent object is queried based on the foreign key value that is currently in the database.</span></span> <span data-ttu-id="b2697-185">Jednakże jeśli relacja została zmodyfikowana, a właściwość odwołania w obiekcie zależnym wskazuje na inny obiekt Principal, który jest ładowany w kontekście obiektu, Entity Framework spróbuje utworzyć relację, ponieważ jest ona zdefiniowana na kliencie.</span><span class="sxs-lookup"><span data-stu-id="b2697-185">However, if the relationship was modified, and the reference property on the dependent object points to a different principal object that is loaded in the object context, Entity Framework will try to create a relationship as it is defined on the client.</span></span>

## <a name="managing-concurrency"></a><span data-ttu-id="b2697-186">Zarządzanie współbieżnością</span><span class="sxs-lookup"><span data-stu-id="b2697-186">Managing concurrency</span></span>

<span data-ttu-id="b2697-187">Zarówno w kluczu obcym, jak i w niezależnych skojarzeniach sprawdzanie współbieżności opiera się na kluczach jednostek i innych właściwościach jednostki, które są zdefiniowane w modelu.</span><span class="sxs-lookup"><span data-stu-id="b2697-187">In both foreign key and independent associations, concurrency checks are based on the entity keys and other entity properties that are defined in the model.</span></span> <span data-ttu-id="b2697-188">W przypadku tworzenia modelu przy użyciu programu EF Designer należy ustawić `ConcurrencyMode` atrybut na **FIXED** , aby określić, że właściwość ma być sprawdzana pod kątem współbieżności.</span><span class="sxs-lookup"><span data-stu-id="b2697-188">When using the EF Designer to create a model, set the `ConcurrencyMode` attribute to **fixed** to specify that the property should be checked for concurrency.</span></span> <span data-ttu-id="b2697-189">Podczas definiowania modelu przy użyciu Code First należy użyć `ConcurrencyCheck` adnotacji na właściwościach, które mają być sprawdzane pod kątem współbieżności.</span><span class="sxs-lookup"><span data-stu-id="b2697-189">When using Code First to define a model, use the `ConcurrencyCheck` annotation on properties that you want to be checked for concurrency.</span></span> <span data-ttu-id="b2697-190">Podczas pracy z Code First można również użyć `TimeStamp` adnotacji, aby określić, że właściwość powinna być sprawdzana pod kątem współbieżności.</span><span class="sxs-lookup"><span data-stu-id="b2697-190">When working with Code First you can also use the `TimeStamp` annotation to specify that the property should be checked for concurrency.</span></span> <span data-ttu-id="b2697-191">W danej klasie może istnieć tylko jedna Właściwość sygnatur czasowych.</span><span class="sxs-lookup"><span data-stu-id="b2697-191">You can have only one timestamp property in a given class.</span></span> <span data-ttu-id="b2697-192">Code First mapuje tę właściwość do pola niedopuszczające wartości null w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="b2697-192">Code First maps this property to a non-nullable field in the database.</span></span>

<span data-ttu-id="b2697-193">Zaleca się, aby zawsze używać skojarzenia klucza obcego podczas pracy z jednostkami, które uczestniczą w sprawdzaniu współbieżności i rozwiązywaniu problemów.</span><span class="sxs-lookup"><span data-stu-id="b2697-193">We recommend that you always use the foreign key association when working with entities that participate in concurrency checking and resolution.</span></span>

<span data-ttu-id="b2697-194">Aby uzyskać więcej informacji, zobacz temat [obsługa konfliktów współbieżności](~/ef6/saving/concurrency.md).</span><span class="sxs-lookup"><span data-stu-id="b2697-194">For more information, see [Handling Concurrency Conflicts](~/ef6/saving/concurrency.md).</span></span>

## <a name="working-with-overlapping-keys"></a><span data-ttu-id="b2697-195">Praca z nakładającymi się kluczami</span><span class="sxs-lookup"><span data-stu-id="b2697-195">Working with overlapping Keys</span></span>

<span data-ttu-id="b2697-196">Nakładające się klucze to klucze złożone, w których niektóre właściwości klucza są również częścią innego klucza w jednostce.</span><span class="sxs-lookup"><span data-stu-id="b2697-196">Overlapping keys are composite keys where some properties in the key are also part of another key in the entity.</span></span> <span data-ttu-id="b2697-197">Nie można mieć nakładającego się klucza w niezależnym skojarzeniu.</span><span class="sxs-lookup"><span data-stu-id="b2697-197">You cannot have an overlapping key in an independent association.</span></span> <span data-ttu-id="b2697-198">Aby zmienić skojarzenie klucza obcego zawierające nakładające się klucze, zalecamy modyfikację wartości klucza obcego zamiast używania odwołań do obiektów.</span><span class="sxs-lookup"><span data-stu-id="b2697-198">To change a foreign key association that includes overlapping keys, we recommend that you modify the foreign key values instead of using the object references.</span></span>
