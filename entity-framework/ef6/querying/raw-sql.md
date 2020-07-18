---
title: Nieprzetworzone zapytania SQL — EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9e1ee76e-2499-408c-81e8-9b6c5d1945a0
ms.openlocfilehash: d336066b982e682e81067bbdac5b3781524cf6be
ms.sourcegitcommit: d85263b5d5d665dbaf94de8832e2917bce048b34
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2020
ms.locfileid: "86451219"
---
# <a name="raw-sql-queries-ef6"></a><span data-ttu-id="8c7cb-102">Nieprzetworzone zapytania SQL (EF6)</span><span class="sxs-lookup"><span data-stu-id="8c7cb-102">Raw SQL Queries (EF6)</span></span>

<span data-ttu-id="8c7cb-103">Entity Framework umożliwia wykonywanie zapytań przy użyciu LINQ z klasami jednostek.</span><span class="sxs-lookup"><span data-stu-id="8c7cb-103">Entity Framework allows you to query using LINQ with your entity classes.</span></span> <span data-ttu-id="8c7cb-104">Mogą jednak wystąpić sytuacje, w których chcesz uruchamiać zapytania przy użyciu RAW SQL bezpośrednio względem bazy danych.</span><span class="sxs-lookup"><span data-stu-id="8c7cb-104">However, there may be times that you want to run queries using raw SQL directly against the database.</span></span> <span data-ttu-id="8c7cb-105">Obejmuje to wywoływanie procedur składowanych, które mogą być przydatne w przypadku modeli Code First, które obecnie nie obsługują mapowania na procedury składowane.</span><span class="sxs-lookup"><span data-stu-id="8c7cb-105">This includes calling stored procedures, which can be helpful for Code First models that currently do not support mapping to stored procedures.</span></span> <span data-ttu-id="8c7cb-106">Techniki przedstawione w tym temacie dotyczą również modeli utworzonych przy użyciu Code First i programu Dr Designer.</span><span class="sxs-lookup"><span data-stu-id="8c7cb-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="writing-sql-queries-for-entities"></a><span data-ttu-id="8c7cb-107">Pisanie zapytań SQL dla jednostek</span><span class="sxs-lookup"><span data-stu-id="8c7cb-107">Writing SQL queries for entities</span></span>  

<span data-ttu-id="8c7cb-108">Metoda sqlQuery w Nieogólnymi umożliwia zapisanie nieprzetworzonego zapytania SQL, które zwróci wystąpienia jednostki.</span><span class="sxs-lookup"><span data-stu-id="8c7cb-108">The SqlQuery method on DbSet allows a raw SQL query to be written that will return entity instances.</span></span> <span data-ttu-id="8c7cb-109">Zwracane obiekty będą śledzone według kontekstu, tak jakby były zwracane przez zapytanie LINQ.</span><span class="sxs-lookup"><span data-stu-id="8c7cb-109">The returned objects will be tracked by the context just as they would be if they were returned by a LINQ query.</span></span> <span data-ttu-id="8c7cb-110">Przykład:</span><span class="sxs-lookup"><span data-stu-id="8c7cb-110">For example:</span></span>  

``` csharp  
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("SELECT * FROM dbo.Blogs").ToList();
}
```  

<span data-ttu-id="8c7cb-111">Należy pamiętać, że tak samo jak w przypadku zapytań LINQ zapytanie nie jest wykonywane, dopóki wyniki nie zostaną wyliczone — w powyższym przykładzie jest to wykonywane z wywołaniem do ToList —.</span><span class="sxs-lookup"><span data-stu-id="8c7cb-111">Note that, just as for LINQ queries, the query is not executed until the results are enumerated—in the example above this is done with the call to ToList.</span></span>  

<span data-ttu-id="8c7cb-112">Należy zachować ostrożność, gdy surowe zapytania SQL są zapisywane z dwóch przyczyn.</span><span class="sxs-lookup"><span data-stu-id="8c7cb-112">Care should be taken whenever raw SQL queries are written for two reasons.</span></span> <span data-ttu-id="8c7cb-113">Najpierw należy napisać zapytanie, aby upewnić się, że zwraca tylko jednostki, które są naprawdę żądany typ.</span><span class="sxs-lookup"><span data-stu-id="8c7cb-113">First, the query should be written to ensure that it only returns entities that are really of the requested type.</span></span> <span data-ttu-id="8c7cb-114">Na przykład podczas korzystania z funkcji, takich jak dziedziczenie, można łatwo napisać zapytanie, które będzie tworzyć jednostki o nieprawidłowym typie CLR.</span><span class="sxs-lookup"><span data-stu-id="8c7cb-114">For example, when using features such as inheritance it is easy to write a query that will create entities that are of the wrong CLR type.</span></span>  

<span data-ttu-id="8c7cb-115">Drugim, niektóre typy nieprzetworzonego zapytania SQL ujawniają potencjalne zagrożenia bezpieczeństwa, szczególnie w przypadku ataków na iniekcję SQL.</span><span class="sxs-lookup"><span data-stu-id="8c7cb-115">Second, some types of raw SQL query expose potential security risks, especially around SQL injection attacks.</span></span> <span data-ttu-id="8c7cb-116">Upewnij się, że parametry w zapytaniu są używane w prawidłowy sposób do ochrony przed takimi atakami.</span><span class="sxs-lookup"><span data-stu-id="8c7cb-116">Make sure that you use parameters in your query in the correct way to guard against such attacks.</span></span>  

### <a name="loading-entities-from-stored-procedures"></a><span data-ttu-id="8c7cb-117">Ładowanie jednostek z procedur składowanych</span><span class="sxs-lookup"><span data-stu-id="8c7cb-117">Loading entities from stored procedures</span></span>  

<span data-ttu-id="8c7cb-118">Można użyć Nieogólnymi. sqlQuery do załadowania jednostek z wyników procedury składowanej.</span><span class="sxs-lookup"><span data-stu-id="8c7cb-118">You can use DbSet.SqlQuery to load entities from the results of a stored procedure.</span></span> <span data-ttu-id="8c7cb-119">Na przykład poniższy kod wywołuje obiekt dbo. Procedura getblogs w bazie danych:</span><span class="sxs-lookup"><span data-stu-id="8c7cb-119">For example, the following code calls the dbo.GetBlogs procedure in the database:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("dbo.GetBlogs").ToList();
}
```  

<span data-ttu-id="8c7cb-120">Parametry można także przekazać do procedury składowanej przy użyciu następującej składni:</span><span class="sxs-lookup"><span data-stu-id="8c7cb-120">You can also pass parameters to a stored procedure using the following syntax:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogId = 1;

    var blogs = context.Blogs.SqlQuery("dbo.GetBlogById @p0", blogId).Single();
}
```  

## <a name="writing-sql-queries-for-non-entity-types"></a><span data-ttu-id="8c7cb-121">Pisanie zapytań SQL dla typów innych niż Entity</span><span class="sxs-lookup"><span data-stu-id="8c7cb-121">Writing SQL queries for non-entity types</span></span>  

<span data-ttu-id="8c7cb-122">Zapytanie SQL zwracające wystąpienia dowolnego typu, w tym typy pierwotne, można utworzyć za pomocą metody sqlQuery w klasie Database.</span><span class="sxs-lookup"><span data-stu-id="8c7cb-122">A SQL query returning instances of any type, including primitive types, can be created using the SqlQuery method on the Database class.</span></span> <span data-ttu-id="8c7cb-123">Przykład:</span><span class="sxs-lookup"><span data-stu-id="8c7cb-123">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogNames = context.Database.SqlQuery<string>(
                       "SELECT Name FROM dbo.Blogs").ToList();
}
```  

<span data-ttu-id="8c7cb-124">Wyniki zwrócone z sqlQuery w bazie danych nigdy nie będą śledzone przez kontekst, nawet jeśli obiekty są wystąpieniami typu jednostki.</span><span class="sxs-lookup"><span data-stu-id="8c7cb-124">The results returned from SqlQuery on Database will never be tracked by the context even if the objects are instances of an entity type.</span></span>  

## <a name="sending-raw-commands-to-the-database"></a><span data-ttu-id="8c7cb-125">Wysyłanie poleceń RAW do bazy danych</span><span class="sxs-lookup"><span data-stu-id="8c7cb-125">Sending raw commands to the database</span></span>  

<span data-ttu-id="8c7cb-126">Polecenia niebędące zapytaniami można wysyłać do bazy danych przy użyciu metody ExecuteSqlCommand w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="8c7cb-126">Non-query commands can be sent to the database using the ExecuteSqlCommand method on Database.</span></span> <span data-ttu-id="8c7cb-127">Przykład:</span><span class="sxs-lookup"><span data-stu-id="8c7cb-127">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    context.Database.ExecuteSqlCommand(
        "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
}
```  

<span data-ttu-id="8c7cb-128">Należy pamiętać, że zmiany wprowadzone w danych w bazie danych przy użyciu ExecuteSqlCommand są nieprzezroczyste dla kontekstu do momentu załadowania lub ponownego załadowania jednostek z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="8c7cb-128">Note that any changes made to data in the database using ExecuteSqlCommand are opaque to the context until entities are loaded or reloaded from the database.</span></span>  

### <a name="output-parameters"></a><span data-ttu-id="8c7cb-129">Parametry wyjściowe</span><span class="sxs-lookup"><span data-stu-id="8c7cb-129">Output Parameters</span></span>  

<span data-ttu-id="8c7cb-130">Jeśli są używane parametry wyjściowe, ich wartości nie będą dostępne, dopóki wyniki nie zostaną całkowicie odczytane.</span><span class="sxs-lookup"><span data-stu-id="8c7cb-130">If output parameters are used, their values will not be available until the results have been read completely.</span></span> <span data-ttu-id="8c7cb-131">Jest to spowodowane zachowaniem podstawowego elementu DbDataReader. zobacz [pobieranie danych za pomocą elementu DataReader](https://go.microsoft.com/fwlink/?LinkID=398589) , aby uzyskać więcej szczegółów.</span><span class="sxs-lookup"><span data-stu-id="8c7cb-131">This is due to the underlying behavior of DbDataReader, see [Retrieving Data Using a DataReader](https://go.microsoft.com/fwlink/?LinkID=398589) for more details.</span></span>  
