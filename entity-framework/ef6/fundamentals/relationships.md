---
title: Relacje, właściwości nawigacji i klucze obce - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 8a21ae73-6d9b-4b50-838a-ec1fddffcf37
ms.openlocfilehash: 76169f25d01bed81ad0748c9e85a67bd112a6e65
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434329"
---
# <a name="relationships-navigation-properties-and-foreign-keys"></a><span data-ttu-id="e7cb4-102">Relacje, właściwości nawigacji i klucze obce</span><span class="sxs-lookup"><span data-stu-id="e7cb4-102">Relationships, navigation properties, and foreign keys</span></span>

<span data-ttu-id="e7cb4-103">W tym artykule przedstawiono omówienie sposobu, w jaki entity framework zarządza relacjami między jednostkami.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-103">This article gives an overview of how Entity Framework manages relationships between entities.</span></span> <span data-ttu-id="e7cb4-104">Zawiera również pewne wskazówki dotyczące mapowania i manipulowania relacjami.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-104">It also gives some guidance on how to map and manipulate relationships.</span></span>

## <a name="relationships-in-ef"></a><span data-ttu-id="e7cb4-105">Relacje w EF</span><span class="sxs-lookup"><span data-stu-id="e7cb4-105">Relationships in EF</span></span>

<span data-ttu-id="e7cb4-106">W relacyjnych bazach danych relacje (nazywane również skojarzeniami) między tabelami są definiowane za pomocą kluczy obcych.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-106">In relational databases, relationships (also called associations) between tables are defined through foreign keys.</span></span> <span data-ttu-id="e7cb4-107">Klucz obcy (FK) to kolumna lub kombinacja kolumn, która jest używana do ustanawiania i wymuszania łącza między danymi w dwóch tabelach.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-107">A foreign key (FK) is a column or combination of columns that is used to establish and enforce a link between the data in two tables.</span></span> <span data-ttu-id="e7cb4-108">Istnieją zazwyczaj trzy rodzaje relacji: jeden do jednego, jeden do wielu i wiele do wielu.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-108">There are generally three types of relationships: one-to-one, one-to-many, and many-to-many.</span></span> <span data-ttu-id="e7cb4-109">W relacji jeden do wielu klucz obcy jest zdefiniowany w tabeli, która reprezentuje wiele końca relacji.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-109">In a one-to-many relationship, the foreign key is defined on the table that represents the many end of the relationship.</span></span> <span data-ttu-id="e7cb4-110">Relacja wiele do wielu polega na zdefiniowaniu trzeciej tabeli (zwanej tabelą skrzyżowania lub sprzężenia), której klucz podstawowy składa się z kluczy obcych z obu powiązanych tabel.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-110">The many-to-many relationship involves defining a third table (called a junction or join table), whose primary key is composed of the foreign keys from both related tables.</span></span> <span data-ttu-id="e7cb4-111">W relacji jeden-do-jednego klucz podstawowy działa dodatkowo jako klucz obcy i nie ma oddzielnej kolumny klucza obcego dla obu tabel.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-111">In a one-to-one relationship, the primary key acts additionally as a foreign key and there is no separate foreign key column for either table.</span></span>

<span data-ttu-id="e7cb4-112">Na poniższej ilustracji przedstawiono dwie tabele, które uczestniczą w relacji jeden do wielu.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-112">The following image shows two tables that participate in one-to-many relationship.</span></span> <span data-ttu-id="e7cb4-113">Tabela **Course** jest tabelą zależną, ponieważ zawiera kolumnę **Identyfikator działu,** która łączy ją z tabelą **Dział.**</span><span class="sxs-lookup"><span data-stu-id="e7cb4-113">The **Course** table is the dependent table because it contains the **DepartmentID** column that links it to the **Department** table.</span></span>

![Tabele działów i kursów](~/ef6/media/database2.png)

<span data-ttu-id="e7cb4-115">W ramach encji jednostka może być powiązana z innymi jednostkami za pośrednictwem skojarzenia lub relacji.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-115">In Entity Framework, an entity can be related to other entities through an association or relationship.</span></span> <span data-ttu-id="e7cb4-116">Każda relacja zawiera dwa zakończenia, które opisują typ jednostki i liczebność typu (jeden, zero-lub-jeden lub wiele) dla dwóch jednostek w tej relacji.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-116">Each relationship contains two ends that describe the entity type and the multiplicity of the type (one, zero-or-one, or many) for the two entities in that relationship.</span></span> <span data-ttu-id="e7cb4-117">Relacja może podlegać więzom referencyjnym, które opisuje, który koniec relacji jest główną rolą, a która jest rolą zależną.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-117">The relationship may be governed by a referential constraint, which describes which end in the relationship is a principal role and which is a dependent role.</span></span>

<span data-ttu-id="e7cb4-118">Właściwości nawigacji umożliwiają poruszanie się po skojarzeniu między dwoma typami jednostek.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-118">Navigation properties provide a way to navigate an association between two entity types.</span></span> <span data-ttu-id="e7cb4-119">Każdy obiekt może mieć właściwość nawigacji dla każdej relacji, w której uczestniczy.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-119">Every object can have a navigation property for every relationship in which it participates.</span></span> <span data-ttu-id="e7cb4-120">Właściwości nawigacji umożliwiają nawigowanie i zarządzanie relacjami w obu kierunkach, zwracając obiekt odniesienia (jeśli liczebność jest jeden lub zero-lub jeden) lub kolekcji (jeśli liczebność jest wiele).</span><span class="sxs-lookup"><span data-stu-id="e7cb4-120">Navigation properties allow you to navigate and manage relationships in both directions, returning either a reference object (if the multiplicity is either one or zero-or-one) or a collection (if the multiplicity is many).</span></span> <span data-ttu-id="e7cb4-121">Można również wybrać, aby mieć jednokierunkową nawigację, w którym to przypadku można zdefiniować właściwość nawigacji tylko na jednym z typów, który uczestniczy w relacji, a nie na obu.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-121">You may also choose to have one-way navigation, in which case you define the navigation property on only one of the types that participates in the relationship and not on both.</span></span>

<span data-ttu-id="e7cb4-122">Zaleca się uwzględnienie właściwości w modelu, które mapują klucze obce w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-122">It is recommended to include properties in the model that map to foreign keys in the database.</span></span> <span data-ttu-id="e7cb4-123">W zestawie właściwości klucza obcego można utworzyć lub zmienić relację, modyfikując wartość klucza obcego w obiekcie zależnym.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-123">With foreign key properties included, you can create or change a relationship by modifying the foreign key value on a dependent object.</span></span> <span data-ttu-id="e7cb4-124">Ten rodzaj skojarzenia jest nazywany stowarzyszeniem klucza zagranicznego.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-124">This kind of association is called a foreign key association.</span></span> <span data-ttu-id="e7cb4-125">Korzystanie z kluczy obcych jest jeszcze bardziej istotne podczas pracy z odłączonych jednostek.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-125">Using foreign keys is even more essential when working with disconnected entities.</span></span> <span data-ttu-id="e7cb4-126">Należy pamiętać, że podczas pracy z 1-to-1 lub 1-to-0.. 1 relacje, nie ma oddzielnej kolumny klucza obcego, właściwość klucza podstawowego działa jako klucz obcy i jest zawsze uwzględniona w modelu.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-126">Note, that when working with 1-to-1 or 1-to-0..1 relationships, there is no separate foreign key column, the primary key property acts as the foreign key and is always included in the model.</span></span>

<span data-ttu-id="e7cb4-127">Gdy kolumny klucza obcego nie są uwzględniane w modelu, informacje o skojarzeniu jest zarządzany jako niezależny obiekt.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-127">When foreign key columns are not included in the model, the association information is managed as an independent object.</span></span> <span data-ttu-id="e7cb4-128">Relacje są śledzone za pomocą odwołań do obiektów zamiast właściwości klucza obcego.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-128">Relationships are tracked through object references instead of foreign key properties.</span></span> <span data-ttu-id="e7cb4-129">Ten typ skojarzenia jest nazywany *niezależnym stowarzyszeniem*.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-129">This type of association is called an *independent association*.</span></span> <span data-ttu-id="e7cb4-130">Najczęstszym sposobem modyfikowania *niezależnego skojarzenia* jest zmodyfikowanie właściwości nawigacji, które są generowane dla każdej jednostki, która uczestniczy w skojarzeniu.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-130">The most common way to modify an *independent association* is to modify the navigation properties that are generated for each entity that participates in the association.</span></span>

<span data-ttu-id="e7cb4-131">Można użyć jednego lub obu typów skojarzeń w modelu.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-131">You can choose to use one or both types of associations in your model.</span></span> <span data-ttu-id="e7cb4-132">Jednak jeśli masz czystą relację wiele do wielu, która jest połączona przez tabelę sprzężenia, która zawiera tylko klucze obce, EF użyje niezależnego skojarzenia do zarządzania tak relacją wiele do wielu.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-132">However, if you have a pure many-to-many relationship that is connected by a join table that contains only foreign keys, the EF will use an independent association to manage such many-to-many relationship.</span></span>   

<span data-ttu-id="e7cb4-133">Na poniższej ilustracji przedstawiono model koncepcyjny, który został utworzony za pomocą Projektanta struktury encji.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-133">The following image shows a conceptual model that was created with the Entity Framework Designer.</span></span> <span data-ttu-id="e7cb4-134">Model zawiera dwie jednostki, które uczestniczą w relacji jeden do wielu.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-134">The model contains two entities that participate in one-to-many relationship.</span></span> <span data-ttu-id="e7cb4-135">Obie jednostki mają właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-135">Both entities have navigation properties.</span></span> <span data-ttu-id="e7cb4-136">**Kurs** jest jednostką zależną i ma właściwość klucza obcego **DepartmentID** zdefiniowana.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-136">**Course** is the dependent entity and has the **DepartmentID** foreign key property defined.</span></span>

![Tabele działów i kursów z właściwościami nawigacji](~/ef6/media/relationshipefdesigner.png)

<span data-ttu-id="e7cb4-138">Poniższy fragment kodu pokazuje ten sam model, który został utworzony za pomocą code first.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-138">The following code snippet shows the same model that was created with Code First.</span></span>

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

## <a name="configuring-or-mapping-relationships"></a><span data-ttu-id="e7cb4-139">Konfigurowanie lub mapowanie relacji</span><span class="sxs-lookup"><span data-stu-id="e7cb4-139">Configuring or mapping relationships</span></span>

<span data-ttu-id="e7cb4-140">Pozostała część tej strony obejmuje sposób uzyskiwania dostępu do danych i manipulowania nimi przy użyciu relacji.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-140">The rest of this page covers how to access and manipulate data using relationships.</span></span> <span data-ttu-id="e7cb4-141">Aby uzyskać informacje na temat konfigurowania relacji w modelu, zobacz następujące strony.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-141">For information on setting up relationships in your model, see the following pages.</span></span>

-   <span data-ttu-id="e7cb4-142">Aby skonfigurować relacje w code first, zobacz [Adnotacje danych](~/ef6/modeling/code-first/data-annotations.md) i [Płynny interfejs API — relacje](~/ef6/modeling/code-first/fluent/relationships.md).</span><span class="sxs-lookup"><span data-stu-id="e7cb4-142">To configure relationships in Code First, see [Data Annotations](~/ef6/modeling/code-first/data-annotations.md) and [Fluent API – Relationships](~/ef6/modeling/code-first/fluent/relationships.md).</span></span>
-   <span data-ttu-id="e7cb4-143">Aby skonfigurować relacje przy użyciu Projektanta struktury encji, zobacz [Relacje z projektantem ef](~/ef6/modeling/designer/relationships.md).</span><span class="sxs-lookup"><span data-stu-id="e7cb4-143">To configure relationships using the Entity Framework Designer, see [Relationships with the EF Designer](~/ef6/modeling/designer/relationships.md).</span></span>

## <a name="creating-and-modifying-relationships"></a><span data-ttu-id="e7cb4-144">Tworzenie i modyfikowanie relacji</span><span class="sxs-lookup"><span data-stu-id="e7cb4-144">Creating and modifying relationships</span></span>

<span data-ttu-id="e7cb4-145">W *skojarzeniu klucza obcego*po zmianie relacji stan obiektu `EntityState.Unchanged` zależnego `EntityState.Modified`ze stanem zmienia się na .</span><span class="sxs-lookup"><span data-stu-id="e7cb4-145">In a *foreign key association*, when you change the relationship, the state of a dependent object with an `EntityState.Unchanged` state changes to `EntityState.Modified`.</span></span> <span data-ttu-id="e7cb4-146">W niezależnej relacji zmiana relacji nie aktualizuje stanu obiektu zależnego.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-146">In an independent relationship, changing the relationship does not update the state of the dependent object.</span></span>

<span data-ttu-id="e7cb4-147">Poniższe przykłady pokazują, jak używać właściwości klucza obcego i właściwości nawigacji do skojarzenia powiązanych obiektów.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-147">The following examples show how to use the foreign key properties and navigation properties to associate the related objects.</span></span> <span data-ttu-id="e7cb4-148">W przypadku skojarzeń kluczy obcych można użyć jednej z metod do zmiany, utworzenia lub zmodyfikowania relacji.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-148">With foreign key associations, you can use either method to change, create, or modify relationships.</span></span> <span data-ttu-id="e7cb4-149">W przypadku niezależnych skojarzeń nie można używać właściwości klucza obcego.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-149">With independent associations, you cannot use the foreign key property.</span></span>

- <span data-ttu-id="e7cb4-150">Przypisując nową wartość do właściwości klucza obcego, jak w poniższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-150">By assigning a new value to a foreign key property, as in the following example.</span></span>  
  ``` csharp
  course.DepartmentID = newCourse.DepartmentID;
  ```

- <span data-ttu-id="e7cb4-151">Poniższy kod usuwa relację, ustawiając klucz obcy na **null**.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-151">The following code removes a relationship by setting the foreign key to **null**.</span></span> <span data-ttu-id="e7cb4-152">Należy zauważyć, że właściwość klucza obcego musi być nullable.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-152">Note, that the foreign key property must be nullable.</span></span>  
  ``` csharp
  course.DepartmentID = null;
  ```

  >[!NOTE]
  > <span data-ttu-id="e7cb4-153">Jeśli odwołanie znajduje się w stanie dodanym (w tym przykładzie obiekt kursu), właściwość nawigacji odwołania nie zostanie zsynchronizowana z wartościami klucza nowego obiektu, dopóki savechanges nie zostanie wywołana.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-153">If the reference is in the added state (in this example, the course object), the reference navigation property will not be synchronized with the key values of a new object until SaveChanges is called.</span></span> <span data-ttu-id="e7cb4-154">Synchronizacja nie występuje, ponieważ kontekst obiektu nie zawiera kluczy stałych dla dodanych obiektów, dopóki nie zostaną zapisane.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-154">Synchronization does not occur because the object context does not contain permanent keys for added objects until they are saved.</span></span> <span data-ttu-id="e7cb4-155">Jeśli nowe obiekty muszą być w pełni zsynchronizowane zaraz po ustawieniu relacji, użyj jednej z następujących metod.\*</span><span class="sxs-lookup"><span data-stu-id="e7cb4-155">If you must have new objects fully synchronized as soon as you set the relationship, use one of the following methods.\*</span></span>

- <span data-ttu-id="e7cb4-156">Przypisując nowy obiekt do właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-156">By assigning a new object to a navigation property.</span></span> <span data-ttu-id="e7cb4-157">Poniższy kod tworzy relację między `department`kursem a .</span><span class="sxs-lookup"><span data-stu-id="e7cb4-157">The following code creates a relationship between a course and a `department`.</span></span> <span data-ttu-id="e7cb4-158">Jeśli obiekty są dołączone do kontekstu, `course` jest `department.Courses` również dodawany do kolekcji, `course` a odpowiednia właściwość klucza obcego na obiekcie jest ustawiona na wartość właściwości klucza działu.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-158">If the objects are attached to the context, the `course` is also added to the `department.Courses` collection, and the corresponding foreign key property on the `course` object is set to the key property value of the department.</span></span>  
  ``` csharp
  course.Department = department;
  ```

- <span data-ttu-id="e7cb4-159">Aby usunąć relację, ustaw `null`właściwość nawigacji na .</span><span class="sxs-lookup"><span data-stu-id="e7cb4-159">To delete the relationship, set the navigation property to `null`.</span></span> <span data-ttu-id="e7cb4-160">Jeśli pracujesz z entity framework, który jest oparty na .NET 4.0, a następnie powiązany koniec musi zostać załadowany przed ustawieniem go na null.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-160">If you are working with Entity Framework that is based on .NET 4.0, then the related end needs to be loaded before you set it to null.</span></span> <span data-ttu-id="e7cb4-161">Przykład:</span><span class="sxs-lookup"><span data-stu-id="e7cb4-161">For example:</span></span>   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).Load();
  course.Department = null;
  ```

  <span data-ttu-id="e7cb4-162">Począwszy od entity framework 5.0, który jest oparty na .NET 4.5, można ustawić relację na wartość null bez ładowania powiązanego końca.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-162">Starting with Entity Framework 5.0, that is based on .NET 4.5, you can set the relationship to null without loading the related end.</span></span> <span data-ttu-id="e7cb4-163">Można również ustawić bieżącą wartość null przy użyciu następującej metody.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-163">You can also set the current value to null using the following method.</span></span>   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).CurrentValue = null;
  ```

- <span data-ttu-id="e7cb4-164">Przez usunięcie lub dodanie obiektu w kolekcji jednostek.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-164">By deleting or adding an object in an entity collection.</span></span> <span data-ttu-id="e7cb4-165">Na przykład można dodać obiekt `Course` typu `department.Courses` do kolekcji.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-165">For example, you can add an object of type `Course` to the `department.Courses` collection.</span></span> <span data-ttu-id="e7cb4-166">Ta operacja tworzy relację między określonym `department` **kursem** a określonym .</span><span class="sxs-lookup"><span data-stu-id="e7cb4-166">This operation creates a relationship between a particular **course** and a particular `department`.</span></span> <span data-ttu-id="e7cb4-167">Jeśli obiekty są dołączone do kontekstu, odwołanie do działu **course** i właściwość klucza `department`obcego na obiekcie kursu zostaną ustawione na odpowiednie .</span><span class="sxs-lookup"><span data-stu-id="e7cb4-167">If the objects are attached to the context, the department reference and the foreign key property on the **course** object will be set to the appropriate `department`.</span></span>  
  ``` csharp
  department.Courses.Add(newCourse);
  ```

- <span data-ttu-id="e7cb4-168">Za pomocą `ChangeRelationshipState` metody, aby zmienić stan określonej relacji między dwoma obiektami jednostki.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-168">By using the `ChangeRelationshipState` method to change the state of the specified relationship between two entity objects.</span></span> <span data-ttu-id="e7cb4-169">Ta metoda jest najczęściej używana podczas pracy z aplikacjami n-warstwowymi i *niezależnym skojarzeniem* (nie może być używana z skojarzeniem kluczy obcych).</span><span class="sxs-lookup"><span data-stu-id="e7cb4-169">This method is most commonly used when working with N-Tier applications and an *independent association* (it cannot be used with a foreign key association).</span></span> <span data-ttu-id="e7cb4-170">Ponadto, aby użyć tej metody, `ObjectContext`należy złożyć kroplówka do , jak pokazano w poniższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-170">Also, to use this method you must drop down to `ObjectContext`, as shown in the example below.</span></span>  
<span data-ttu-id="e7cb4-171">W poniższym przykładzie istnieje wiele do wielu relacji między instruktorów i kursów.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-171">In the following example, there is a many-to-many relationship between Instructors and Courses.</span></span> <span data-ttu-id="e7cb4-172">Wywołanie `ChangeRelationshipState` metody i `EntityState.Added` przekazanie parametru, informuje, `SchoolContext` że relacja została dodana między dwoma obiektami:</span><span class="sxs-lookup"><span data-stu-id="e7cb4-172">Calling the `ChangeRelationshipState` method and passing the `EntityState.Added` parameter, lets the `SchoolContext` know that a relationship has been added between the two objects:</span></span>
  ``` csharp

  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, instructor, c => c.Instructor, EntityState.Added);
  ```

  <span data-ttu-id="e7cb4-173">Należy zauważyć, że w przypadku aktualizowania (a nie tylko dodawania) relacji, należy usunąć starą relację po dodaniu nowej relacji:</span><span class="sxs-lookup"><span data-stu-id="e7cb4-173">Note that if you are updating (not just adding) a relationship, you must delete the old relationship after adding the new one:</span></span>

  ``` csharp
  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, oldInstructor, c => c.Instructor, EntityState.Deleted);
  ```

## <a name="synchronizing-the-changes-between-the-foreign-keys-and-navigation-properties"></a><span data-ttu-id="e7cb4-174">Synchronizowanie zmian między klawiszami obcymi a właściwościami nawigacji</span><span class="sxs-lookup"><span data-stu-id="e7cb4-174">Synchronizing the changes between the foreign keys and navigation properties</span></span>

<span data-ttu-id="e7cb4-175">Po zmianie relacji obiektów dołączonych do kontekstu przy użyciu jednej z metod opisanych powyżej, Entity Framework musi zachować klucze obce, odwołania i kolekcje w synchronizacji. Entity Framework automatycznie zarządza tej synchronizacji (znany również jako fix-up relacji) dla jednostek POCO z serwerami proxy.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-175">When you change the relationship of the objects attached to the context by using one of the methods described above, Entity Framework needs to keep foreign keys, references, and collections in sync. Entity Framework automatically manages this synchronization (also known as relationship fix-up) for the POCO entities with proxies.</span></span> <span data-ttu-id="e7cb4-176">Aby uzyskać więcej informacji, zobacz [Praca z serwerami proxy](~/ef6/fundamentals/proxies.md).</span><span class="sxs-lookup"><span data-stu-id="e7cb4-176">For more information, see [Working with Proxies](~/ef6/fundamentals/proxies.md).</span></span>

<span data-ttu-id="e7cb4-177">Jeśli używasz jednostek POCO bez serwerów proxy, należy upewnić się, że **DetectChanges** metoda jest wywoływana do synchronizowania powiązanych obiektów w kontekście.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-177">If you are using POCO entities without proxies, you must make sure that the **DetectChanges** method is called to synchronize the related objects in the context.</span></span> <span data-ttu-id="e7cb4-178">Należy zauważyć, że następujące interfejsy API automatycznie **wyzwalają wywołanie DetectChanges.**</span><span class="sxs-lookup"><span data-stu-id="e7cb4-178">Note that the following APIs automatically trigger a **DetectChanges** call.</span></span>

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
-   <span data-ttu-id="e7cb4-179">Wykonywanie kwerendy LINQ względem`DbSet`</span><span class="sxs-lookup"><span data-stu-id="e7cb4-179">Executing a LINQ query against a `DbSet`</span></span>

## <a name="loading-related-objects"></a><span data-ttu-id="e7cb4-180">Ładowanie powiązanych obiektów</span><span class="sxs-lookup"><span data-stu-id="e7cb4-180">Loading related objects</span></span>

<span data-ttu-id="e7cb4-181">W entity framework często używasz właściwości nawigacji do ładowania jednostek, które są związane z zwróconą jednostką przez zdefiniowane skojarzenie.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-181">In Entity Framework you commonly use navigation properties to load entities that are related to the returned entity by the defined association.</span></span> <span data-ttu-id="e7cb4-182">Aby uzyskać więcej informacji, zobacz [Ładowanie powiązanych obiektów](~/ef6/querying/related-data.md).</span><span class="sxs-lookup"><span data-stu-id="e7cb4-182">For more information, see [Loading Related Objects](~/ef6/querying/related-data.md).</span></span>

> [!NOTE]
> <span data-ttu-id="e7cb4-183">W skojarzeniu klucza obcego podczas ładowania powiązanego końca obiektu zależnego obiekt pokrewny zostanie załadowany na podstawie wartości klucza obcego zależnego, który jest obecnie w pamięci:</span><span class="sxs-lookup"><span data-stu-id="e7cb4-183">In a foreign key association, when you load a related end of a dependent object, the related object will be loaded based on the foreign key value of the dependent that is currently in memory:</span></span>

``` csharp
    // Get the course where currently DepartmentID = 2.
    Course course2 = context.Courses.First(c => c.DepartmentID == 2);

    // Use DepartmentID foreign key property
    // to change the association.
    course2.DepartmentID = 3;

    // Load the related Department where DepartmentID = 3
    context.Entry(course).Reference(c => c.Department).Load();
```

<span data-ttu-id="e7cb4-184">W niezależnym skojarzeniu powiązany koniec obiektu zależnego jest wyszukiwany na podstawie wartości klucza obcego, która znajduje się obecnie w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-184">In an independent association, the related end of a dependent object is queried based on the foreign key value that is currently in the database.</span></span> <span data-ttu-id="e7cb4-185">Jednak jeśli relacja została zmodyfikowana, a właściwość odwołania na obiekt zależny wskazuje na inny obiekt główny, który jest ładowany w kontekście obiektu, Entity Framework spróbuje utworzyć relację, jak jest zdefiniowana na kliencie.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-185">However, if the relationship was modified, and the reference property on the dependent object points to a different principal object that is loaded in the object context, Entity Framework will try to create a relationship as it is defined on the client.</span></span>

## <a name="managing-concurrency"></a><span data-ttu-id="e7cb4-186">Zarządzanie współbieżnością</span><span class="sxs-lookup"><span data-stu-id="e7cb4-186">Managing concurrency</span></span>

<span data-ttu-id="e7cb4-187">Zarówno w kluczu obcym, jak i niezależnych skojarzeniach kontrole współbieżności są oparte na kluczach jednostki i innych właściwościach jednostki, które są zdefiniowane w modelu.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-187">In both foreign key and independent associations, concurrency checks are based on the entity keys and other entity properties that are defined in the model.</span></span> <span data-ttu-id="e7cb4-188">Podczas korzystania z projektanta EF do `ConcurrencyMode` tworzenia modelu, ustaw atrybut **fixed,** aby określić, że właściwość powinna być sprawdzana pod kątem współbieżności.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-188">When using the EF Designer to create a model, set the `ConcurrencyMode` attribute to **fixed** to specify that the property should be checked for concurrency.</span></span> <span data-ttu-id="e7cb4-189">Podczas korzystania z code first do `ConcurrencyCheck` definiowania modelu, należy użyć adnotacji na właściwości, które mają być sprawdzane pod kątem współbieżności.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-189">When using Code First to define a model, use the `ConcurrencyCheck` annotation on properties that you want to be checked for concurrency.</span></span> <span data-ttu-id="e7cb4-190">Podczas pracy z Code First `TimeStamp` można również użyć adnotacji, aby określić, że właściwość powinna być sprawdzana pod kątem współbieżności.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-190">When working with Code First you can also use the `TimeStamp` annotation to specify that the property should be checked for concurrency.</span></span> <span data-ttu-id="e7cb4-191">W danej klasie można mieć tylko jedną właściwość sygnatury czasowej.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-191">You can have only one timestamp property in a given class.</span></span> <span data-ttu-id="e7cb4-192">Code First mapuje tę właściwość do pola niewystępnego w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-192">Code First maps this property to a non-nullable field in the database.</span></span>

<span data-ttu-id="e7cb4-193">Zaleca się, aby zawsze używać skojarzenia klucza obcego podczas pracy z jednostkami, które uczestniczą w sprawdzaniu współbieżności i rozwiązywaniu problemów.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-193">We recommend that you always use the foreign key association when working with entities that participate in concurrency checking and resolution.</span></span>

<span data-ttu-id="e7cb4-194">Aby uzyskać więcej informacji, zobacz [Obsługa konfliktów współbieżności](~/ef6/saving/concurrency.md).</span><span class="sxs-lookup"><span data-stu-id="e7cb4-194">For more information, see [Handling Concurrency Conflicts](~/ef6/saving/concurrency.md).</span></span>

## <a name="working-with-overlapping-keys"></a><span data-ttu-id="e7cb4-195">Praca z nakładającymi się klawiszami</span><span class="sxs-lookup"><span data-stu-id="e7cb4-195">Working with overlapping Keys</span></span>

<span data-ttu-id="e7cb4-196">Nakładające się klucze są klucze złożone, gdzie niektóre właściwości w kluczu są również częścią innego klucza w jednostce.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-196">Overlapping keys are composite keys where some properties in the key are also part of another key in the entity.</span></span> <span data-ttu-id="e7cb4-197">Nie można mieć nakładającego się klucza w niezależnym skojarzeniu.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-197">You cannot have an overlapping key in an independent association.</span></span> <span data-ttu-id="e7cb4-198">Aby zmienić skojarzenie klucza obcego, które zawiera nakładające się klucze, zaleca się modyfikowanie wartości klucza obcego zamiast używania odwołań do obiektów.</span><span class="sxs-lookup"><span data-stu-id="e7cb4-198">To change a foreign key association that includes overlapping keys, we recommend that you modify the foreign key values instead of using the object references.</span></span>
