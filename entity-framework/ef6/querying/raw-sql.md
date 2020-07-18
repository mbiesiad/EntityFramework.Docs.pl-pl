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
# <a name="raw-sql-queries-ef6"></a>Nieprzetworzone zapytania SQL (EF6)

Entity Framework umożliwia wykonywanie zapytań przy użyciu LINQ z klasami jednostek. Mogą jednak wystąpić sytuacje, w których chcesz uruchamiać zapytania przy użyciu RAW SQL bezpośrednio względem bazy danych. Obejmuje to wywoływanie procedur składowanych, które mogą być przydatne w przypadku modeli Code First, które obecnie nie obsługują mapowania na procedury składowane. Techniki przedstawione w tym temacie dotyczą również modeli utworzonych przy użyciu Code First i programu Dr Designer.  

## <a name="writing-sql-queries-for-entities"></a>Pisanie zapytań SQL dla jednostek  

Metoda sqlQuery w Nieogólnymi umożliwia zapisanie nieprzetworzonego zapytania SQL, które zwróci wystąpienia jednostki. Zwracane obiekty będą śledzone według kontekstu, tak jakby były zwracane przez zapytanie LINQ. Przykład:  

``` csharp  
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("SELECT * FROM dbo.Blogs").ToList();
}
```  

Należy pamiętać, że tak samo jak w przypadku zapytań LINQ zapytanie nie jest wykonywane, dopóki wyniki nie zostaną wyliczone — w powyższym przykładzie jest to wykonywane z wywołaniem do ToList —.  

Należy zachować ostrożność, gdy surowe zapytania SQL są zapisywane z dwóch przyczyn. Najpierw należy napisać zapytanie, aby upewnić się, że zwraca tylko jednostki, które są naprawdę żądany typ. Na przykład podczas korzystania z funkcji, takich jak dziedziczenie, można łatwo napisać zapytanie, które będzie tworzyć jednostki o nieprawidłowym typie CLR.  

Drugim, niektóre typy nieprzetworzonego zapytania SQL ujawniają potencjalne zagrożenia bezpieczeństwa, szczególnie w przypadku ataków na iniekcję SQL. Upewnij się, że parametry w zapytaniu są używane w prawidłowy sposób do ochrony przed takimi atakami.  

### <a name="loading-entities-from-stored-procedures"></a>Ładowanie jednostek z procedur składowanych  

Można użyć Nieogólnymi. sqlQuery do załadowania jednostek z wyników procedury składowanej. Na przykład poniższy kod wywołuje obiekt dbo. Procedura getblogs w bazie danych:  

``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("dbo.GetBlogs").ToList();
}
```  

Parametry można także przekazać do procedury składowanej przy użyciu następującej składni:  

``` csharp
using (var context = new BloggingContext())
{
    var blogId = 1;

    var blogs = context.Blogs.SqlQuery("dbo.GetBlogById @p0", blogId).Single();
}
```  

## <a name="writing-sql-queries-for-non-entity-types"></a>Pisanie zapytań SQL dla typów innych niż Entity  

Zapytanie SQL zwracające wystąpienia dowolnego typu, w tym typy pierwotne, można utworzyć za pomocą metody sqlQuery w klasie Database. Przykład:  

``` csharp
using (var context = new BloggingContext())
{
    var blogNames = context.Database.SqlQuery<string>(
                       "SELECT Name FROM dbo.Blogs").ToList();
}
```  

Wyniki zwrócone z sqlQuery w bazie danych nigdy nie będą śledzone przez kontekst, nawet jeśli obiekty są wystąpieniami typu jednostki.  

## <a name="sending-raw-commands-to-the-database"></a>Wysyłanie poleceń RAW do bazy danych  

Polecenia niebędące zapytaniami można wysyłać do bazy danych przy użyciu metody ExecuteSqlCommand w bazie danych. Przykład:  

``` csharp
using (var context = new BloggingContext())
{
    context.Database.ExecuteSqlCommand(
        "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
}
```  

Należy pamiętać, że zmiany wprowadzone w danych w bazie danych przy użyciu ExecuteSqlCommand są nieprzezroczyste dla kontekstu do momentu załadowania lub ponownego załadowania jednostek z bazy danych.  

### <a name="output-parameters"></a>Parametry wyjściowe  

Jeśli są używane parametry wyjściowe, ich wartości nie będą dostępne, dopóki wyniki nie zostaną całkowicie odczytane. Jest to spowodowane zachowaniem podstawowego elementu DbDataReader. zobacz [pobieranie danych za pomocą elementu DataReader](https://go.microsoft.com/fwlink/?LinkID=398589) , aby uzyskać więcej szczegółów.  
