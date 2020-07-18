---
title: Obsługa konfliktów współbieżności — EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 2318e4d3-f561-4720-bbc3-921556806476
ms.openlocfilehash: 4d29fd7a4d9b6003f71bc8411cea2d863a4c5429
ms.sourcegitcommit: d85263b5d5d665dbaf94de8832e2917bce048b34
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2020
ms.locfileid: "86451245"
---
# <a name="handling-concurrency-conflicts-ef6"></a>Obsługa konfliktów współbieżności (EF6)

Optymistyczna współbieżność obejmuje optymistycznie próbę zapisania jednostki w bazie danych w celu uzyskania, że dane nie uległy zmianie od momentu załadowania jednostki. Jeśli spowoduje to wypróbowanie, że dane uległy zmianie, zostanie zgłoszony wyjątek i należy rozwiązać konflikt przed podjęciem ponownej próby zapisania. W tym temacie omówiono sposób obsługi takich wyjątków w Entity Framework. Techniki przedstawione w tym temacie dotyczą również modeli utworzonych przy użyciu Code First i programu Dr Designer.  

Ten wpis nie jest odpowiednim miejscem do pełnej dyskusji optymistycznej współbieżności. W poniższych sekcjach założono pewną wiedzę na temat rozwiązywania współbieżności i przedstawiono wzorce dla typowych zadań.  

Wiele z tych wzorców wykorzystuje tematy omówione w temacie [Praca z wartościami właściwości](~/ef6/saving/change-tracking/property-values.md).  

Rozwiązywanie problemów współbieżności w przypadku korzystania z niezależnych skojarzeń (w przypadku których klucz obcy nie jest mapowany do właściwości w jednostce) jest znacznie trudniejsze niż w przypadku używania skojarzeń kluczy obcych. W związku z tym jeśli zamierzasz przeprowadzić rozwiązanie współbieżności w aplikacji, zaleca się, aby zawsze mapować klucze obce do jednostek. We wszystkich przykładach przyjęto założenie, że używane są skojarzenia kluczy obcych.  

DbUpdateConcurrencyException jest generowany przez metody SaveChanges, gdy zostanie wykryty optymistyczny wyjątek współbieżności podczas próby zapisania jednostki, która korzysta z skojarzeń kluczy obcych.  

## <a name="resolving-optimistic-concurrency-exceptions-with-reload-database-wins"></a>Rozpoznawanie optymistycznych wyjątków współbieżności z ponownym załadowaniem (baza danych: WINS)  

Metoda reload może służyć do zastępowania bieżących wartości jednostki wartościami teraz w bazie danych. Następnie jednostka jest zazwyczaj podawana do użytkownika w postaci jakiejś i musi próbować ponownie wprowadzić zmiany i ponownie zapisać. Przykład:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    blog.Name = "The New ADO.NET Blog";

    bool saveFailed;
    do
    {
        saveFailed = false;

        try
        {
            context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException ex)
        {
            saveFailed = true;

            // Update the values of the entity that failed to save from the store
            ex.Entries.Single().Reload();
        }

    } while (saveFailed);
}
```  

Dobrym sposobem symulowania wyjątku współbieżności jest ustawienie punktu przerwania w wywołaniu metody SaveChanges, a następnie zmodyfikowanie jednostki, która jest zapisywana w bazie danych przy użyciu innego narzędzia, takiego jak SQL Server Management Studio. Możesz również wstawić wiersz przed metody SaveChanges, aby zaktualizować bazę danych bezpośrednio przy użyciu polecenia SqlCommand. Przykład:  

``` csharp
context.Database.SqlCommand(
    "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
```  

Metoda wpisów w DbUpdateConcurrencyException zwraca wystąpienia DbEntityEntry dla jednostek, które nie zostały zaktualizowane. (Ta właściwość zawsze zwraca pojedynczą wartość dla problemów współbieżności. Może zwracać wiele wartości dla wyjątków aktualizacji ogólnych.) Alternatywą dla niektórych sytuacji może być uzyskanie wpisów dla wszystkich jednostek, które mogą wymagać ponownego załadowania z bazy danych i wypróbowania załadowania dla każdego z nich.  

## <a name="resolving-optimistic-concurrency-exceptions-as-client-wins"></a>Rozpoznawanie optymistycznych wyjątków współbieżności jako klienta WINS  

W powyższym przykładzie, który używa ponownego ładowania, nazywa się czasami bazą danych WINS lub magazynem WINS, ponieważ wartości w jednostce są zastępowane przez wartości z bazy danych. Czasami warto wykonać odwrotność i zastąpić wartości w bazie danych wartościami znajdującymi się obecnie w jednostce. Jest to czasami nazywane klientem WINS i można je wykonać przez pobranie bieżących wartości bazy danych i ustawienie ich jako oryginalnych wartości dla jednostki. (Zobacz [Praca z wartościami właściwości](~/ef6/saving/change-tracking/property-values.md) , aby uzyskać informacje na temat bieżących i oryginalnych wartości). Na przykład:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    blog.Name = "The New ADO.NET Blog";

    bool saveFailed;
    do
    {
        saveFailed = false;
        try
        {
            context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException ex)
        {
            saveFailed = true;

            // Update original values from the database
            var entry = ex.Entries.Single();
            entry.OriginalValues.SetValues(entry.GetDatabaseValues());
        }

    } while (saveFailed);
}
```  

## <a name="custom-resolution-of-optimistic-concurrency-exceptions"></a>Niestandardowe rozpoznawanie optymistycznych wyjątków współbieżności  

Czasami warto połączyć wartości znajdujące się obecnie w bazie danych z wartościami znajdującymi się obecnie w jednostce. Zwykle wymaga to pewnej logiki niestandardowej lub interakcji z użytkownikiem. Można na przykład przedstawić formularz użytkownikowi zawierającym bieżące wartości, wartości w bazie danych i domyślny zestaw rozwiązanych wartości. Następnie użytkownik może edytować rozwiązane wartości zgodnie z potrzebami, a następnie te rozwiązane wartości, które zostaną zapisane w bazie danych. Można to zrobić za pomocą obiektów dbpropertyvalue zwracanych z CurrentValues i GetDatabaseValues w wpisie jednostki. Przykład:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    blog.Name = "The New ADO.NET Blog";

    bool saveFailed;
    do
    {
        saveFailed = false;
        try
        {
            context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException ex)
        {
            saveFailed = true;

            // Get the current entity values and the values in the database
            var entry = ex.Entries.Single();
            var currentValues = entry.CurrentValues;
            var databaseValues = entry.GetDatabaseValues();

            // Choose an initial set of resolved values. In this case we
            // make the default be the values currently in the database.
            var resolvedValues = databaseValues.Clone();

            // Have the user choose what the resolved values should be
            HaveUserResolveConcurrency(currentValues, databaseValues, resolvedValues);

            // Update the original values with the database values and
            // the current values with whatever the user choose.
            entry.OriginalValues.SetValues(databaseValues);
            entry.CurrentValues.SetValues(resolvedValues);
        }
    } while (saveFailed);
}

public void HaveUserResolveConcurrency(DbPropertyValues currentValues,
                                       DbPropertyValues databaseValues,
                                       DbPropertyValues resolvedValues)
{
    // Show the current, database, and resolved values to the user and have
    // them edit the resolved values to get the correct resolution.
}
```  

## <a name="custom-resolution-of-optimistic-concurrency-exceptions-using-objects"></a>Niestandardowe rozpoznawanie optymistycznych wyjątków współbieżności przy użyciu obiektów  

Powyższy kod używa wystąpień dbpropertyvalues do przekazywania między bieżącymi, bazami danych i rozwiązanymi wartościami. Czasami można łatwiej używać wystąpień typu jednostki. Można to zrobić przy użyciu metod ToObject i SetValue klasy dbpropertyvalues. Na przykład:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    blog.Name = "The New ADO.NET Blog";

    bool saveFailed;
    do
    {
        saveFailed = false;
        try
        {
            context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException ex)
        {
            saveFailed = true;

            // Get the current entity values and the values in the database
            // as instances of the entity type
            var entry = ex.Entries.Single();
            var databaseValues = entry.GetDatabaseValues();
            var databaseValuesAsBlog = (Blog)databaseValues.ToObject();

            // Choose an initial set of resolved values. In this case we
            // make the default be the values currently in the database.
            var resolvedValuesAsBlog = (Blog)databaseValues.ToObject();

            // Have the user choose what the resolved values should be
            HaveUserResolveConcurrency((Blog)entry.Entity,
                                       databaseValuesAsBlog,
                                       resolvedValuesAsBlog);

            // Update the original values with the database values and
            // the current values with whatever the user choose.
            entry.OriginalValues.SetValues(databaseValues);
            entry.CurrentValues.SetValues(resolvedValuesAsBlog);
        }

    } while (saveFailed);
}

public void HaveUserResolveConcurrency(Blog entity,
                                       Blog databaseValues,
                                       Blog resolvedValues)
{
    // Show the current, database, and resolved values to the user and have
    // them update the resolved values to get the correct resolution.
}
```  
