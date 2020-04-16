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
# <a name="relationships-navigation-properties-and-foreign-keys"></a>Relacje, właściwości nawigacji i klucze obce

W tym artykule przedstawiono omówienie sposobu, w jaki entity framework zarządza relacjami między jednostkami. Zawiera również pewne wskazówki dotyczące mapowania i manipulowania relacjami.

## <a name="relationships-in-ef"></a>Relacje w EF

W relacyjnych bazach danych relacje (nazywane również skojarzeniami) między tabelami są definiowane za pomocą kluczy obcych. Klucz obcy (FK) to kolumna lub kombinacja kolumn, która jest używana do ustanawiania i wymuszania łącza między danymi w dwóch tabelach. Istnieją zazwyczaj trzy rodzaje relacji: jeden do jednego, jeden do wielu i wiele do wielu. W relacji jeden do wielu klucz obcy jest zdefiniowany w tabeli, która reprezentuje wiele końca relacji. Relacja wiele do wielu polega na zdefiniowaniu trzeciej tabeli (zwanej tabelą skrzyżowania lub sprzężenia), której klucz podstawowy składa się z kluczy obcych z obu powiązanych tabel. W relacji jeden-do-jednego klucz podstawowy działa dodatkowo jako klucz obcy i nie ma oddzielnej kolumny klucza obcego dla obu tabel.

Na poniższej ilustracji przedstawiono dwie tabele, które uczestniczą w relacji jeden do wielu. Tabela **Course** jest tabelą zależną, ponieważ zawiera kolumnę **Identyfikator działu,** która łączy ją z tabelą **Dział.**

![Tabele działów i kursów](~/ef6/media/database2.png)

W ramach encji jednostka może być powiązana z innymi jednostkami za pośrednictwem skojarzenia lub relacji. Każda relacja zawiera dwa zakończenia, które opisują typ jednostki i liczebność typu (jeden, zero-lub-jeden lub wiele) dla dwóch jednostek w tej relacji. Relacja może podlegać więzom referencyjnym, które opisuje, który koniec relacji jest główną rolą, a która jest rolą zależną.

Właściwości nawigacji umożliwiają poruszanie się po skojarzeniu między dwoma typami jednostek. Każdy obiekt może mieć właściwość nawigacji dla każdej relacji, w której uczestniczy. Właściwości nawigacji umożliwiają nawigowanie i zarządzanie relacjami w obu kierunkach, zwracając obiekt odniesienia (jeśli liczebność jest jeden lub zero-lub jeden) lub kolekcji (jeśli liczebność jest wiele). Można również wybrać, aby mieć jednokierunkową nawigację, w którym to przypadku można zdefiniować właściwość nawigacji tylko na jednym z typów, który uczestniczy w relacji, a nie na obu.

Zaleca się uwzględnienie właściwości w modelu, które mapują klucze obce w bazie danych. W zestawie właściwości klucza obcego można utworzyć lub zmienić relację, modyfikując wartość klucza obcego w obiekcie zależnym. Ten rodzaj skojarzenia jest nazywany stowarzyszeniem klucza zagranicznego. Korzystanie z kluczy obcych jest jeszcze bardziej istotne podczas pracy z odłączonych jednostek. Należy pamiętać, że podczas pracy z 1-to-1 lub 1-to-0.. 1 relacje, nie ma oddzielnej kolumny klucza obcego, właściwość klucza podstawowego działa jako klucz obcy i jest zawsze uwzględniona w modelu.

Gdy kolumny klucza obcego nie są uwzględniane w modelu, informacje o skojarzeniu jest zarządzany jako niezależny obiekt. Relacje są śledzone za pomocą odwołań do obiektów zamiast właściwości klucza obcego. Ten typ skojarzenia jest nazywany *niezależnym stowarzyszeniem*. Najczęstszym sposobem modyfikowania *niezależnego skojarzenia* jest zmodyfikowanie właściwości nawigacji, które są generowane dla każdej jednostki, która uczestniczy w skojarzeniu.

Można użyć jednego lub obu typów skojarzeń w modelu. Jednak jeśli masz czystą relację wiele do wielu, która jest połączona przez tabelę sprzężenia, która zawiera tylko klucze obce, EF użyje niezależnego skojarzenia do zarządzania tak relacją wiele do wielu.   

Na poniższej ilustracji przedstawiono model koncepcyjny, który został utworzony za pomocą Projektanta struktury encji. Model zawiera dwie jednostki, które uczestniczą w relacji jeden do wielu. Obie jednostki mają właściwości nawigacji. **Kurs** jest jednostką zależną i ma właściwość klucza obcego **DepartmentID** zdefiniowana.

![Tabele działów i kursów z właściwościami nawigacji](~/ef6/media/relationshipefdesigner.png)

Poniższy fragment kodu pokazuje ten sam model, który został utworzony za pomocą code first.

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

## <a name="configuring-or-mapping-relationships"></a>Konfigurowanie lub mapowanie relacji

Pozostała część tej strony obejmuje sposób uzyskiwania dostępu do danych i manipulowania nimi przy użyciu relacji. Aby uzyskać informacje na temat konfigurowania relacji w modelu, zobacz następujące strony.

-   Aby skonfigurować relacje w code first, zobacz [Adnotacje danych](~/ef6/modeling/code-first/data-annotations.md) i [Płynny interfejs API — relacje](~/ef6/modeling/code-first/fluent/relationships.md).
-   Aby skonfigurować relacje przy użyciu Projektanta struktury encji, zobacz [Relacje z projektantem ef](~/ef6/modeling/designer/relationships.md).

## <a name="creating-and-modifying-relationships"></a>Tworzenie i modyfikowanie relacji

W *skojarzeniu klucza obcego*po zmianie relacji stan obiektu `EntityState.Unchanged` zależnego `EntityState.Modified`ze stanem zmienia się na . W niezależnej relacji zmiana relacji nie aktualizuje stanu obiektu zależnego.

Poniższe przykłady pokazują, jak używać właściwości klucza obcego i właściwości nawigacji do skojarzenia powiązanych obiektów. W przypadku skojarzeń kluczy obcych można użyć jednej z metod do zmiany, utworzenia lub zmodyfikowania relacji. W przypadku niezależnych skojarzeń nie można używać właściwości klucza obcego.

- Przypisując nową wartość do właściwości klucza obcego, jak w poniższym przykładzie.  
  ``` csharp
  course.DepartmentID = newCourse.DepartmentID;
  ```

- Poniższy kod usuwa relację, ustawiając klucz obcy na **null**. Należy zauważyć, że właściwość klucza obcego musi być nullable.  
  ``` csharp
  course.DepartmentID = null;
  ```

  >[!NOTE]
  > Jeśli odwołanie znajduje się w stanie dodanym (w tym przykładzie obiekt kursu), właściwość nawigacji odwołania nie zostanie zsynchronizowana z wartościami klucza nowego obiektu, dopóki savechanges nie zostanie wywołana. Synchronizacja nie występuje, ponieważ kontekst obiektu nie zawiera kluczy stałych dla dodanych obiektów, dopóki nie zostaną zapisane. Jeśli nowe obiekty muszą być w pełni zsynchronizowane zaraz po ustawieniu relacji, użyj jednej z następujących metod.*

- Przypisując nowy obiekt do właściwości nawigacji. Poniższy kod tworzy relację między `department`kursem a . Jeśli obiekty są dołączone do kontekstu, `course` jest `department.Courses` również dodawany do kolekcji, `course` a odpowiednia właściwość klucza obcego na obiekcie jest ustawiona na wartość właściwości klucza działu.  
  ``` csharp
  course.Department = department;
  ```

- Aby usunąć relację, ustaw `null`właściwość nawigacji na . Jeśli pracujesz z entity framework, który jest oparty na .NET 4.0, a następnie powiązany koniec musi zostać załadowany przed ustawieniem go na null. Przykład:   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).Load();
  course.Department = null;
  ```

  Począwszy od entity framework 5.0, który jest oparty na .NET 4.5, można ustawić relację na wartość null bez ładowania powiązanego końca. Można również ustawić bieżącą wartość null przy użyciu następującej metody.   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).CurrentValue = null;
  ```

- Przez usunięcie lub dodanie obiektu w kolekcji jednostek. Na przykład można dodać obiekt `Course` typu `department.Courses` do kolekcji. Ta operacja tworzy relację między określonym `department` **kursem** a określonym . Jeśli obiekty są dołączone do kontekstu, odwołanie do działu **course** i właściwość klucza `department`obcego na obiekcie kursu zostaną ustawione na odpowiednie .  
  ``` csharp
  department.Courses.Add(newCourse);
  ```

- Za pomocą `ChangeRelationshipState` metody, aby zmienić stan określonej relacji między dwoma obiektami jednostki. Ta metoda jest najczęściej używana podczas pracy z aplikacjami n-warstwowymi i *niezależnym skojarzeniem* (nie może być używana z skojarzeniem kluczy obcych). Ponadto, aby użyć tej metody, `ObjectContext`należy złożyć kroplówka do , jak pokazano w poniższym przykładzie.  
W poniższym przykładzie istnieje wiele do wielu relacji między instruktorów i kursów. Wywołanie `ChangeRelationshipState` metody i `EntityState.Added` przekazanie parametru, informuje, `SchoolContext` że relacja została dodana między dwoma obiektami:
  ``` csharp

  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, instructor, c => c.Instructor, EntityState.Added);
  ```

  Należy zauważyć, że w przypadku aktualizowania (a nie tylko dodawania) relacji, należy usunąć starą relację po dodaniu nowej relacji:

  ``` csharp
  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, oldInstructor, c => c.Instructor, EntityState.Deleted);
  ```

## <a name="synchronizing-the-changes-between-the-foreign-keys-and-navigation-properties"></a>Synchronizowanie zmian między klawiszami obcymi a właściwościami nawigacji

Po zmianie relacji obiektów dołączonych do kontekstu przy użyciu jednej z metod opisanych powyżej, Entity Framework musi zachować klucze obce, odwołania i kolekcje w synchronizacji. Entity Framework automatycznie zarządza tej synchronizacji (znany również jako fix-up relacji) dla jednostek POCO z serwerami proxy. Aby uzyskać więcej informacji, zobacz [Praca z serwerami proxy](~/ef6/fundamentals/proxies.md).

Jeśli używasz jednostek POCO bez serwerów proxy, należy upewnić się, że **DetectChanges** metoda jest wywoływana do synchronizowania powiązanych obiektów w kontekście. Należy zauważyć, że następujące interfejsy API automatycznie **wyzwalają wywołanie DetectChanges.**

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
-   Wykonywanie kwerendy LINQ względem`DbSet`

## <a name="loading-related-objects"></a>Ładowanie powiązanych obiektów

W entity framework często używasz właściwości nawigacji do ładowania jednostek, które są związane z zwróconą jednostką przez zdefiniowane skojarzenie. Aby uzyskać więcej informacji, zobacz [Ładowanie powiązanych obiektów](~/ef6/querying/related-data.md).

> [!NOTE]
> W skojarzeniu klucza obcego podczas ładowania powiązanego końca obiektu zależnego obiekt pokrewny zostanie załadowany na podstawie wartości klucza obcego zależnego, który jest obecnie w pamięci:

``` csharp
    // Get the course where currently DepartmentID = 2.
    Course course2 = context.Courses.First(c => c.DepartmentID == 2);

    // Use DepartmentID foreign key property
    // to change the association.
    course2.DepartmentID = 3;

    // Load the related Department where DepartmentID = 3
    context.Entry(course).Reference(c => c.Department).Load();
```

W niezależnym skojarzeniu powiązany koniec obiektu zależnego jest wyszukiwany na podstawie wartości klucza obcego, która znajduje się obecnie w bazie danych. Jednak jeśli relacja została zmodyfikowana, a właściwość odwołania na obiekt zależny wskazuje na inny obiekt główny, który jest ładowany w kontekście obiektu, Entity Framework spróbuje utworzyć relację, jak jest zdefiniowana na kliencie.

## <a name="managing-concurrency"></a>Zarządzanie współbieżnością

Zarówno w kluczu obcym, jak i niezależnych skojarzeniach kontrole współbieżności są oparte na kluczach jednostki i innych właściwościach jednostki, które są zdefiniowane w modelu. Podczas korzystania z projektanta EF do `ConcurrencyMode` tworzenia modelu, ustaw atrybut **fixed,** aby określić, że właściwość powinna być sprawdzana pod kątem współbieżności. Podczas korzystania z code first do `ConcurrencyCheck` definiowania modelu, należy użyć adnotacji na właściwości, które mają być sprawdzane pod kątem współbieżności. Podczas pracy z Code First `TimeStamp` można również użyć adnotacji, aby określić, że właściwość powinna być sprawdzana pod kątem współbieżności. W danej klasie można mieć tylko jedną właściwość sygnatury czasowej. Code First mapuje tę właściwość do pola niewystępnego w bazie danych.

Zaleca się, aby zawsze używać skojarzenia klucza obcego podczas pracy z jednostkami, które uczestniczą w sprawdzaniu współbieżności i rozwiązywaniu problemów.

Aby uzyskać więcej informacji, zobacz [Obsługa konfliktów współbieżności](~/ef6/saving/concurrency.md).

## <a name="working-with-overlapping-keys"></a>Praca z nakładającymi się klawiszami

Nakładające się klucze są klucze złożone, gdzie niektóre właściwości w kluczu są również częścią innego klucza w jednostce. Nie można mieć nakładającego się klucza w niezależnym skojarzeniu. Aby zmienić skojarzenie klucza obcego, które zawiera nakładające się klucze, zaleca się modyfikowanie wartości klucza obcego zamiast używania odwołań do obiektów.
