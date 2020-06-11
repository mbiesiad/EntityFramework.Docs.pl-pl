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
# <a name="relationships-navigation-properties-and-foreign-keys"></a>Relacje, właściwości nawigacji i klucze obce

Ten artykuł zawiera omówienie sposobu, w jaki Entity Framework zarządza relacjami między jednostkami. Przedstawiono w nim również pewne wskazówki dotyczące sposobu mapowania relacji i manipulowania nimi.

## <a name="relationships-in-ef"></a>Relacje w EF

W relacyjnych bazach danych relacje (nazywane również skojarzeniami) między tabelami są definiowane za poorednictwem kluczy obcych. Klucz obcy (FK) to kolumna lub kombinacja kolumn, które są używane do ustanawiania i wymuszania połączenia między danymi w dwóch tabelach. Zwykle istnieją trzy typy relacji: jeden do jednego, jeden do wielu i wiele-do-wielu. W relacji jeden-do-wielu klucz obcy jest zdefiniowany w tabeli, która reprezentuje wiele końców relacji. Relacja wiele do wielu obejmuje definiowanie trzeciej tabeli (nazywanej tabelą rozgałęzienia lub sprzężenia), której klucz podstawowy składa się z kluczy obcych z obu powiązanych tabel. W relacji jeden-do-jednego klucz podstawowy działa również jako klucz obcy i nie istnieje osobna kolumna klucza obcego dla żadnej tabeli.

Na poniższej ilustracji przedstawiono dwie tabele, które uczestniczą w relacji jeden do wielu. Tabela **kursów** jest tabelą zależną, ponieważ zawiera kolumnę **DepartmentID** , która łączy ją z tabelą **działów** .

![Tabele działów i kursów](~/ef6/media/database2.png)

W Entity Framework jednostka może być powiązana z innymi jednostkami przy użyciu skojarzenia lub relacji. Każda relacja zawiera dwa zakończenia, które opisują typ jednostki i liczebność typu (jeden, zero-lub-jeden lub wiele) dla dwóch jednostek w tej relacji. Relacja może podlegać ograniczeniu referencyjnemu, który opisuje, co kończy się w relacji, jest rolą podmiotu zabezpieczeń, która jest rolą zależną.

Właściwości nawigacji umożliwiają nawigowanie po skojarzeniu między dwoma typami jednostek. Każdy obiekt może mieć właściwość nawigacji dla każdej relacji, w której uczestniczy. Właściwości nawigacji umożliwiają Nawigowanie między relacjami i zarządzanie nimi w obu kierunkach, zwracając obiekt odniesienia (Jeśli liczebność jest równa jeden lub zero-lub-jeden) lub kolekcji (Jeśli liczebność ma wiele wartości). Możesz również wybrać opcję nawigacji jednokierunkowej, w której przypadku należy zdefiniować właściwość nawigacji tylko dla jednego z typów, które uczestniczą w relacji, a nie na obu.

Zalecane jest uwzględnienie w modelu właściwości, które są mapowane na klucze obce w bazie danych. Za pomocą zawartych w nim właściwości klucza obcego można utworzyć lub zmienić relację, modyfikując wartość klucza obcego w obiekcie zależnym. Tego rodzaju skojarzenie nazywa się skojarzenie klucza obcego. Korzystanie z kluczy obcych jest jeszcze bardziej istotne podczas pracy z odłączonymi jednostkami. Należy pamiętać, że podczas pracy z 1-lub-1 lub 1-do-0... 1 relacje, brak oddzielnej kolumny klucza obcego, właściwość klucza podstawowego pełni rolę klucza obcego i jest zawsze uwzględniona w modelu.

Gdy kolumny klucza obcego nie są uwzględnione w modelu, informacje o skojarzeniu są zarządzane jako obiekt niezależny. Relacje są śledzone za poorednictwem odwołań do obiektów zamiast właściwości klucza obcego. Skojarzenie tego typu jest nazywane *skojarzeniem niezależnym*. Najbardziej typowym sposobem modyfikacji *niezależnego skojarzenia* jest zmodyfikowanie właściwości nawigacji, które są generowane dla każdej jednostki, która uczestniczy w skojarzeniu.

Możesz użyć jednego lub obu typów skojarzeń w modelu. Jeśli jednak masz czystą relację wiele-do-wielu, która jest połączona z tabelą sprzężenia, która zawiera tylko klucze obce, EF użyje niezależnego skojarzenia do zarządzania taką relacją wiele-do-wielu.   

Na poniższej ilustracji przedstawiono model koncepcyjny, który został utworzony przy użyciu Entity Framework Designer. Model zawiera dwie jednostki, które uczestniczą w relacji jeden do wielu. Obie jednostki mają właściwości nawigacji. **Kurs** jest jednostką zależną i ma zdefiniowaną właściwość klucza obcego **DepartmentID** .

![Tabele działów i kursów z właściwościami nawigacji](~/ef6/media/relationshipefdesigner.png)

Poniższy fragment kodu przedstawia ten sam model, który został utworzony przy użyciu Code First.

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

## <a name="configuring-or-mapping-relationships"></a>Konfigurowanie lub Mapowanie relacji

Pozostała część tej strony zawiera informacje dotyczące uzyskiwania dostępu do danych i manipulowania nimi przy użyciu relacji. Aby uzyskać informacje na temat konfigurowania relacji w modelu, zobacz następujące strony.

-   Aby skonfigurować relacje w Code First, zobacz [Adnotacje dotyczące danych](~/ef6/modeling/code-first/data-annotations.md) i [interfejs API Fluent — relacje](~/ef6/modeling/code-first/fluent/relationships.md).
-   Aby skonfigurować relacje przy użyciu Entity Framework Designer, zobacz [relacje z programem Dr Designer](~/ef6/modeling/designer/relationships.md).

## <a name="creating-and-modifying-relationships"></a>Tworzenie i modyfikowanie relacji

W *skojarzeniu klucza obcego*, gdy zmienisz relację, stan obiektu zależnego ze `EntityState.Unchanged` stanem zmieni się na `EntityState.Modified` . W niezależnej relacji zmiana relacji nie aktualizuje stanu obiektu zależnego.

W poniższych przykładach pokazano, jak za pomocą właściwości klucza obcego i właściwości nawigacji skojarzyć powiązane obiekty. Przy użyciu skojarzenia klucza obcego można użyć dowolnej metody, aby zmienić, utworzyć lub zmodyfikować relacje. Z niezależnymi skojarzeniami nie można użyć właściwości klucza obcego.

- Przypisanie nowej wartości do właściwości klucza obcego, jak w poniższym przykładzie.  
  ``` csharp
  course.DepartmentID = newCourse.DepartmentID;
  ```

- Poniższy kod usuwa relację, ustawiając klucz obcy na **wartość null**. Należy zauważyć, że właściwość klucza obcego musi dopuszczać wartości null.  
  ``` csharp
  course.DepartmentID = null;
  ```

  >[!NOTE]
  > Jeśli odwołanie jest w stanie dodany (w tym przykładzie obiekt kursu), właściwość nawigacji odwołania nie zostanie zsynchronizowana z wartościami klucza nowego obiektu, dopóki nie zostanie wywołana metody SaveChanges. Synchronizacja nie występuje, ponieważ kontekst obiektu nie zawiera trwałych kluczy dla dodanych obiektów, dopóki nie zostaną zapisane. Jeśli nowe obiekty muszą być w pełni zsynchronizowane zaraz po ustawieniu relacji, użyj jednej z następujących metod. *

- Przypisanie nowego obiektu do właściwości nawigacji. Poniższy kod tworzy relację między kursem a `department` . Jeśli obiekty są dołączone do kontekstu, `course` jest również dodawane do `department.Courses` kolekcji, a odpowiednia właściwość klucza obcego dla `course` obiektu jest ustawiona na wartość właściwości klucza działu.  
  ``` csharp
  course.Department = department;
  ```

- Aby usunąć relację, ustaw właściwość nawigacji na `null` . Jeśli pracujesz z Entity Frameworkami opartymi na platformie .NET 4,0, należy załadować powiązane zakończenie przed ustawieniem na wartość null. Na przykład:   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).Load();
  course.Department = null;
  ```

  Począwszy od Entity Framework 5,0, który jest oparty na platformie .NET 4,5, można ustawić relację na wartość null bez ładowania powiązanego końca. Możesz również ustawić bieżącą wartość na null przy użyciu następującej metody.   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).CurrentValue = null;
  ```

- Przez usunięcie lub dodanie obiektu w kolekcji jednostek. Na przykład można dodać obiekt typu `Course` do `department.Courses` kolekcji. Ta operacja tworzy relację między określonym **kursem** a konkretnym `department` . Jeśli obiekty są dołączone do kontekstu, odwołanie do działu i właściwość klucza obcego w obiekcie **kursu** zostaną ustawione na odpowiednie `department` .  
  ``` csharp
  department.Courses.Add(newCourse);
  ```

- Za pomocą `ChangeRelationshipState` metody, aby zmienić stan określonej relacji między dwoma obiektami jednostek. Ta metoda jest najczęściej używana podczas pracy z aplikacjami N-warstwowymi i *niezależnym skojarzeniem* (nie można jej używać z skojarzeniem klucza obcego). Aby użyć tej metody, należy również najpierw rozwinąć do `ObjectContext` , jak pokazano w poniższym przykładzie.  
W poniższym przykładzie istnieje relacja wiele-do-wielu między instruktorami i kursami. Wywołanie `ChangeRelationshipState` metody i przekazanie `EntityState.Added` parametru umożliwia określenie `SchoolContext` , że relacja została dodana między dwoma obiektami:
  ``` csharp

  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, instructor, c => c.Instructor, EntityState.Added);
  ```

  Należy pamiętać, że w przypadku aktualizowania (nie tylko dodawania) relacji należy usunąć starą relację po dodaniu nowej:

  ``` csharp
  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, oldInstructor, c => c.Instructor, EntityState.Deleted);
  ```

## <a name="synchronizing-the-changes-between-the-foreign-keys-and-navigation-properties"></a>Synchronizowanie zmian między kluczami obcymi i właściwościami nawigacji

W przypadku zmiany relacji między obiektami dołączonymi do kontekstu przy użyciu jednej z metod opisanych powyżej Entity Framework należy zachować synchronizację kluczy obcych, odwołań i kolekcji. Entity Framework automatycznie zarządza tą synchronizacją (znaną również jako poprawka relacji) dla jednostek POCO z serwerami proxy. Aby uzyskać więcej informacji, zobacz [Praca z serwerami proxy](~/ef6/fundamentals/proxies.md).

Jeśli używasz jednostek POCO bez serwerów proxy, musisz upewnić się, że metoda **DetectChanges** jest wywoływana w celu zsynchronizowania obiektów pokrewnych w kontekście. Należy zauważyć, że następujące interfejsy API automatycznie wyzwalają wywołanie **DetectChanges** .

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
-   Wykonywanie zapytania LINQ względem`DbSet`

## <a name="loading-related-objects"></a>Ładowanie powiązanych obiektów

W Entity Framework często używasz właściwości nawigacji do ładowania jednostek, które są powiązane z zwróconą jednostką przez zdefiniowane skojarzenie. Aby uzyskać więcej informacji, zobacz [ładowanie powiązanych obiektów](~/ef6/querying/related-data.md).

> [!NOTE]
> W przypadku skojarzenia klucza obcego podczas ładowania powiązanego końca obiektu zależnego, obiekt pokrewny zostanie załadowany na podstawie wartości klucza obcego zależnego, który jest aktualnie w pamięci:

``` csharp
    // Get the course where currently DepartmentID = 2.
    Course course = context.Courses.First(c => c.DepartmentID == 2);

    // Use DepartmentID foreign key property
    // to change the association.
    course.DepartmentID = 3;

    // Load the related Department where DepartmentID = 3
    context.Entry(course).Reference(c => c.Department).Load();
```

W niezależnym skojarzeniu pokrewny koniec obiektu zależnego jest wysyłana na podstawie wartości klucza obcego, która jest obecnie w bazie danych. Jednakże jeśli relacja została zmodyfikowana, a właściwość odwołania w obiekcie zależnym wskazuje na inny obiekt Principal, który jest ładowany w kontekście obiektu, Entity Framework spróbuje utworzyć relację, ponieważ jest ona zdefiniowana na kliencie.

## <a name="managing-concurrency"></a>Zarządzanie współbieżnością

Zarówno w kluczu obcym, jak i w niezależnych skojarzeniach sprawdzanie współbieżności opiera się na kluczach jednostek i innych właściwościach jednostki, które są zdefiniowane w modelu. W przypadku tworzenia modelu przy użyciu programu EF Designer należy ustawić `ConcurrencyMode` atrybut na **FIXED** , aby określić, że właściwość ma być sprawdzana pod kątem współbieżności. Podczas definiowania modelu przy użyciu Code First należy użyć `ConcurrencyCheck` adnotacji na właściwościach, które mają być sprawdzane pod kątem współbieżności. Podczas pracy z Code First można również użyć `TimeStamp` adnotacji, aby określić, że właściwość powinna być sprawdzana pod kątem współbieżności. W danej klasie może istnieć tylko jedna Właściwość sygnatur czasowych. Code First mapuje tę właściwość do pola niedopuszczające wartości null w bazie danych.

Zaleca się, aby zawsze używać skojarzenia klucza obcego podczas pracy z jednostkami, które uczestniczą w sprawdzaniu współbieżności i rozwiązywaniu problemów.

Aby uzyskać więcej informacji, zobacz temat [obsługa konfliktów współbieżności](~/ef6/saving/concurrency.md).

## <a name="working-with-overlapping-keys"></a>Praca z nakładającymi się kluczami

Nakładające się klucze to klucze złożone, w których niektóre właściwości klucza są również częścią innego klucza w jednostce. Nie można mieć nakładającego się klucza w niezależnym skojarzeniu. Aby zmienić skojarzenie klucza obcego zawierające nakładające się klucze, zalecamy modyfikację wartości klucza obcego zamiast używania odwołań do obiektów.
