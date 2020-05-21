---
title: Obsługa konfliktów współbieżności — EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 2318e4d3-f561-4720-bbc3-921556806476
ms.openlocfilehash: a99f824fe256a10b84f539a5339a09624315efa4
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672710"
---
# <a name="handling-concurrency-conflicts"></a><span data-ttu-id="e0e51-102">Obsługa konfliktów współbieżności</span><span class="sxs-lookup"><span data-stu-id="e0e51-102">Handling Concurrency Conflicts</span></span>
<span data-ttu-id="e0e51-103">Optymistyczna współbieżność obejmuje optymistycznie próbę zapisania jednostki w bazie danych w celu uzyskania, że dane nie uległy zmianie od momentu załadowania jednostki.</span><span class="sxs-lookup"><span data-stu-id="e0e51-103">Optimistic concurrency involves optimistically attempting to save your entity to the database in the hope that the data there has not changed since the entity was loaded.</span></span> <span data-ttu-id="e0e51-104">Jeśli spowoduje to wypróbowanie, że dane uległy zmianie, zostanie zgłoszony wyjątek i należy rozwiązać konflikt przed podjęciem ponownej próby zapisania.</span><span class="sxs-lookup"><span data-stu-id="e0e51-104">If it turns out that the data has changed then an exception is thrown and you must resolve the conflict before attempting to save again.</span></span> <span data-ttu-id="e0e51-105">W tym temacie omówiono sposób obsługi takich wyjątków w Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="e0e51-105">This topic covers how to handle such exceptions in Entity Framework.</span></span> <span data-ttu-id="e0e51-106">Techniki przedstawione w tym temacie dotyczą również modeli utworzonych przy użyciu Code First i programu Dr Designer.</span><span class="sxs-lookup"><span data-stu-id="e0e51-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="e0e51-107">Ten wpis nie jest odpowiednim miejscem do pełnej dyskusji optymistycznej współbieżności.</span><span class="sxs-lookup"><span data-stu-id="e0e51-107">This post is not the appropriate place for a full discussion of optimistic concurrency.</span></span> <span data-ttu-id="e0e51-108">W poniższych sekcjach założono pewną wiedzę na temat rozwiązywania współbieżności i przedstawiono wzorce dla typowych zadań.</span><span class="sxs-lookup"><span data-stu-id="e0e51-108">The sections below assume some knowledge of concurrency resolution and show patterns for common tasks.</span></span>  

<span data-ttu-id="e0e51-109">Wiele z tych wzorców wykorzystuje tematy omówione w temacie [Praca z wartościami właściwości](~/ef6/saving/change-tracking/property-values.md).</span><span class="sxs-lookup"><span data-stu-id="e0e51-109">Many of these patterns make use of the topics discussed in [Working with Property Values](~/ef6/saving/change-tracking/property-values.md).</span></span>  

<span data-ttu-id="e0e51-110">Rozwiązywanie problemów współbieżności w przypadku korzystania z niezależnych skojarzeń (w przypadku których klucz obcy nie jest mapowany do właściwości w jednostce) jest znacznie trudniejsze niż w przypadku używania skojarzeń kluczy obcych.</span><span class="sxs-lookup"><span data-stu-id="e0e51-110">Resolving concurrency issues when you are using independent associations (where the foreign key is not mapped to a property in your entity) is much more difficult than when you are using foreign key associations.</span></span> <span data-ttu-id="e0e51-111">W związku z tym jeśli zamierzasz przeprowadzić rozwiązanie współbieżności w aplikacji, zaleca się, aby zawsze mapować klucze obce do jednostek.</span><span class="sxs-lookup"><span data-stu-id="e0e51-111">Therefore if you are going to do concurrency resolution in your application it is advised that you always map foreign keys into your entities.</span></span> <span data-ttu-id="e0e51-112">We wszystkich przykładach przyjęto założenie, że używane są skojarzenia kluczy obcych.</span><span class="sxs-lookup"><span data-stu-id="e0e51-112">All the examples below assume that you are using foreign key associations.</span></span>  

<span data-ttu-id="e0e51-113">DbUpdateConcurrencyException jest generowany przez metody SaveChanges, gdy zostanie wykryty optymistyczny wyjątek współbieżności podczas próby zapisania jednostki, która korzysta z skojarzeń kluczy obcych.</span><span class="sxs-lookup"><span data-stu-id="e0e51-113">A DbUpdateConcurrencyException is thrown by SaveChanges when an optimistic concurrency exception is detected while attempting to save an entity that uses foreign key associations.</span></span>  

## <a name="resolving-optimistic-concurrency-exceptions-with-reload-database-wins"></a><span data-ttu-id="e0e51-114">Rozpoznawanie optymistycznych wyjątków współbieżności z ponownym załadowaniem (baza danych: WINS)</span><span class="sxs-lookup"><span data-stu-id="e0e51-114">Resolving optimistic concurrency exceptions with Reload (database wins)</span></span>  

<span data-ttu-id="e0e51-115">Metoda reload może służyć do zastępowania bieżących wartości jednostki wartościami teraz w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="e0e51-115">The Reload method can be used to overwrite the current values of the entity with the values now in the database.</span></span> <span data-ttu-id="e0e51-116">Następnie jednostka jest zazwyczaj podawana do użytkownika w postaci jakiejś i musi próbować ponownie wprowadzić zmiany i ponownie zapisać.</span><span class="sxs-lookup"><span data-stu-id="e0e51-116">The entity is then typically given back to the user in some form and they must try to make their changes again and re-save.</span></span> <span data-ttu-id="e0e51-117">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="e0e51-117">For example:</span></span>  

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

<span data-ttu-id="e0e51-118">Dobrym sposobem symulowania wyjątku współbieżności jest ustawienie punktu przerwania w wywołaniu metody SaveChanges, a następnie zmodyfikowanie jednostki, która jest zapisywana w bazie danych przy użyciu innego narzędzia, takiego jak SQL Server Management Studio.</span><span class="sxs-lookup"><span data-stu-id="e0e51-118">A good way to simulate a concurrency exception is to set a breakpoint on the SaveChanges call and then modify an entity that is being saved in the database using another tool such as SQL Server Management Studio.</span></span> <span data-ttu-id="e0e51-119">Możesz również wstawić wiersz przed metody SaveChanges, aby zaktualizować bazę danych bezpośrednio przy użyciu polecenia SqlCommand.</span><span class="sxs-lookup"><span data-stu-id="e0e51-119">You could also insert a line before SaveChanges to update the database directly using SqlCommand.</span></span> <span data-ttu-id="e0e51-120">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="e0e51-120">For example:</span></span>  

``` csharp
context.Database.SqlCommand(
    "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
```  

<span data-ttu-id="e0e51-121">Metoda wpisów w DbUpdateConcurrencyException zwraca wystąpienia DbEntityEntry dla jednostek, które nie zostały zaktualizowane.</span><span class="sxs-lookup"><span data-stu-id="e0e51-121">The Entries method on DbUpdateConcurrencyException returns the DbEntityEntry instances for the entities that failed to update.</span></span> <span data-ttu-id="e0e51-122">(Ta właściwość zawsze zwraca pojedynczą wartość dla problemów współbieżności.</span><span class="sxs-lookup"><span data-stu-id="e0e51-122">(This property currently always returns a single value for concurrency issues.</span></span> <span data-ttu-id="e0e51-123">Może zwracać wiele wartości dla wyjątków aktualizacji ogólnych.) Alternatywą dla niektórych sytuacji może być uzyskanie wpisów dla wszystkich jednostek, które mogą wymagać ponownego załadowania z bazy danych i wypróbowania załadowania dla każdego z nich.</span><span class="sxs-lookup"><span data-stu-id="e0e51-123">It may return multiple values for general update exceptions.) An alternative for some situations might be to get entries for all entities that may need to be reloaded from the database and call reload for each of these.</span></span>  

## <a name="resolving-optimistic-concurrency-exceptions-as-client-wins"></a><span data-ttu-id="e0e51-124">Rozpoznawanie optymistycznych wyjątków współbieżności jako klienta WINS</span><span class="sxs-lookup"><span data-stu-id="e0e51-124">Resolving optimistic concurrency exceptions as client wins</span></span>  

<span data-ttu-id="e0e51-125">W powyższym przykładzie, który używa ponownego ładowania, nazywa się czasami bazą danych WINS lub magazynem WINS, ponieważ wartości w jednostce są zastępowane przez wartości z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="e0e51-125">The example above that uses Reload is sometimes called database wins or store wins because the values in the entity are overwritten by values from the database.</span></span> <span data-ttu-id="e0e51-126">Czasami warto wykonać odwrotność i zastąpić wartości w bazie danych wartościami znajdującymi się obecnie w jednostce.</span><span class="sxs-lookup"><span data-stu-id="e0e51-126">Sometimes you may wish to do the opposite and overwrite the values in the database with the values currently in the entity.</span></span> <span data-ttu-id="e0e51-127">Jest to czasami nazywane klientem WINS i można je wykonać przez pobranie bieżących wartości bazy danych i ustawienie ich jako oryginalnych wartości dla jednostki.</span><span class="sxs-lookup"><span data-stu-id="e0e51-127">This is sometimes called client wins and can be done by getting the current database values and setting them as the original values for the entity.</span></span> <span data-ttu-id="e0e51-128">(Zobacz [Praca z wartościami właściwości](~/ef6/saving/change-tracking/property-values.md) , aby uzyskać informacje na temat bieżących i oryginalnych wartości). Na przykład:</span><span class="sxs-lookup"><span data-stu-id="e0e51-128">(See [Working with Property Values](~/ef6/saving/change-tracking/property-values.md) for information on current and original values.) For example:</span></span>  

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

## <a name="custom-resolution-of-optimistic-concurrency-exceptions"></a><span data-ttu-id="e0e51-129">Niestandardowe rozpoznawanie optymistycznych wyjątków współbieżności</span><span class="sxs-lookup"><span data-stu-id="e0e51-129">Custom resolution of optimistic concurrency exceptions</span></span>  

<span data-ttu-id="e0e51-130">Czasami warto połączyć wartości znajdujące się obecnie w bazie danych z wartościami znajdującymi się obecnie w jednostce.</span><span class="sxs-lookup"><span data-stu-id="e0e51-130">Sometimes you may want to combine the values currently in the database with the values currently in the entity.</span></span> <span data-ttu-id="e0e51-131">Zwykle wymaga to pewnej logiki niestandardowej lub interakcji z użytkownikiem.</span><span class="sxs-lookup"><span data-stu-id="e0e51-131">This usually requires some custom logic or user interaction.</span></span> <span data-ttu-id="e0e51-132">Można na przykład przedstawić formularz użytkownikowi zawierającym bieżące wartości, wartości w bazie danych i domyślny zestaw rozwiązanych wartości.</span><span class="sxs-lookup"><span data-stu-id="e0e51-132">For example, you might present a form to the user containing the current values, the values in the database, and a default set of resolved values.</span></span> <span data-ttu-id="e0e51-133">Następnie użytkownik może edytować rozwiązane wartości zgodnie z potrzebami, a następnie te rozwiązane wartości, które zostaną zapisane w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="e0e51-133">The user would then edit the resolved values as necessary and it would be these resolved values that get saved to the database.</span></span> <span data-ttu-id="e0e51-134">Można to zrobić za pomocą obiektów dbpropertyvalue zwracanych z CurrentValues i GetDatabaseValues w wpisie jednostki.</span><span class="sxs-lookup"><span data-stu-id="e0e51-134">This can be done using the DbPropertyValues objects returned from CurrentValues and GetDatabaseValues on the entity’s entry.</span></span> <span data-ttu-id="e0e51-135">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="e0e51-135">For example:</span></span>  

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

## <a name="custom-resolution-of-optimistic-concurrency-exceptions-using-objects"></a><span data-ttu-id="e0e51-136">Niestandardowe rozpoznawanie optymistycznych wyjątków współbieżności przy użyciu obiektów</span><span class="sxs-lookup"><span data-stu-id="e0e51-136">Custom resolution of optimistic concurrency exceptions using objects</span></span>  

<span data-ttu-id="e0e51-137">Powyższy kod używa wystąpień dbpropertyvalues do przekazywania między bieżącymi, bazami danych i rozwiązanymi wartościami.</span><span class="sxs-lookup"><span data-stu-id="e0e51-137">The code above uses DbPropertyValues instances for passing around current, database, and resolved values.</span></span> <span data-ttu-id="e0e51-138">Czasami można łatwiej używać wystąpień typu jednostki.</span><span class="sxs-lookup"><span data-stu-id="e0e51-138">Sometimes it may be easier to use instances of your entity type for this.</span></span> <span data-ttu-id="e0e51-139">Można to zrobić przy użyciu metod ToObject i SetValue klasy dbpropertyvalues.</span><span class="sxs-lookup"><span data-stu-id="e0e51-139">This can be done using the ToObject and SetValues methods of DbPropertyValues.</span></span> <span data-ttu-id="e0e51-140">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="e0e51-140">For example:</span></span>  

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
