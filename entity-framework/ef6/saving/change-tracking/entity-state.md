---
title: Praca z stany jednostki - EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: acb27f46-3f3a-4179-874a-d6bea5d7120c
caps.latest.revision: 3
ms.openlocfilehash: e27d70dd7a6a63c2b225e9cf4fe7ac8cc280d4ca
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2018
ms.locfileid: "37912056"
---
# <a name="working-with-entity-states"></a><span data-ttu-id="f0fdd-102">Praca z stany jednostki</span><span class="sxs-lookup"><span data-stu-id="f0fdd-102">Working with entity states</span></span>
<span data-ttu-id="f0fdd-103">W tym temacie opisano sposób dodawania i dołączyć jednostek do kontekstu i jak przetwarza je podczas SaveChanges Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="f0fdd-103">This topic will cover how to add and attach entities to a context and how Entity Framework processes these during SaveChanges.</span></span>
<span data-ttu-id="f0fdd-104">Śledzenie stanu jednostek, gdy są one połączone z kontekstu, ale w scenariuszach odłączone lub N-warstwowa można pozwolić, aby wiedzieć, jakie stanu jednostek EF powinien być w zajmuje się platformy Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="f0fdd-104">Entity Framework takes care of tracking the state of entities while they are connected to a context, but in disconnected or N-Tier scenarios you can let EF know what state your entities should be in.</span></span>
<span data-ttu-id="f0fdd-105">Techniki przedstawione w tym temacie stosuje się jednakowo do modeli utworzonych za pomocą Code First i projektancie platformy EF.</span><span class="sxs-lookup"><span data-stu-id="f0fdd-105">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="entity-states-and-savechanges"></a><span data-ttu-id="f0fdd-106">Stany jednostki i SaveChanges</span><span class="sxs-lookup"><span data-stu-id="f0fdd-106">Entity states and SaveChanges</span></span>

<span data-ttu-id="f0fdd-107">Jednostki może być jeden z pięciu stanów zdefiniowanych przez wyliczenie EntityState.</span><span class="sxs-lookup"><span data-stu-id="f0fdd-107">An entity can be in one of five states as defined by the EntityState enumeration.</span></span> <span data-ttu-id="f0fdd-108">Te stany są następujące:</span><span class="sxs-lookup"><span data-stu-id="f0fdd-108">These states are:</span></span>  

- <span data-ttu-id="f0fdd-109">Dodano: jednostka jest śledzony przez kontekst, ale jeszcze nie istnieje w bazie danych</span><span class="sxs-lookup"><span data-stu-id="f0fdd-109">Added: the entity is being tracked by the context but does not yet exist in the database</span></span>  
- <span data-ttu-id="f0fdd-110">Niezmieniona: jednostka jest śledzony przez kontekst i istnieje w bazie danych i jego wartości właściwości nie zmieniły się od wartości w bazie danych</span><span class="sxs-lookup"><span data-stu-id="f0fdd-110">Unchanged: the entity is being tracked by the context and exists in the database, and its property values have not changed from the values in the database</span></span>  
- <span data-ttu-id="f0fdd-111">Zmodyfikowane: jednostka jest śledzony przez kontekst i istnieje w bazie danych, a niektóre lub wszystkie wartości właściwości zostały zmodyfikowane</span><span class="sxs-lookup"><span data-stu-id="f0fdd-111">Modified: the entity is being tracked by the context and exists in the database, and some or all of its property values have been modified</span></span>  
- <span data-ttu-id="f0fdd-112">Usunięto: jednostka jest śledzony przez kontekst i istnieje w bazie danych, ale została oznaczona do usunięcia z bazy danych następnym razem, gdy zostanie wywołana SaveChanges</span><span class="sxs-lookup"><span data-stu-id="f0fdd-112">Deleted: the entity is being tracked by the context and exists in the database, but has been marked for deletion from the database the next time SaveChanges is called</span></span>  
- <span data-ttu-id="f0fdd-113">Odłączony: jednostka nie jest śledzony przez kontekst</span><span class="sxs-lookup"><span data-stu-id="f0fdd-113">Detached: the entity is not being tracked by the context</span></span>  

<span data-ttu-id="f0fdd-114">SaveChanges wykonuje różne znaczenie w przypadku jednostek w różnych stanach:</span><span class="sxs-lookup"><span data-stu-id="f0fdd-114">SaveChanges does different things for entities in different states:</span></span>  

- <span data-ttu-id="f0fdd-115">Niezmienione jednostki są nie korzystały SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="f0fdd-115">Unchanged entities are not touched by SaveChanges.</span></span> <span data-ttu-id="f0fdd-116">Aktualizacje nie są wysyłane do bazy danych dla obiektów w stanie niezmieniony.</span><span class="sxs-lookup"><span data-stu-id="f0fdd-116">Updates are not sent to the database for entities in the Unchanged state.</span></span>  
- <span data-ttu-id="f0fdd-117">Dodano jednostki są wstawiane do bazy danych, a następnie stają się zmian podczas SaveChanges zwraca.</span><span class="sxs-lookup"><span data-stu-id="f0fdd-117">Added entities are inserted into the database and then become Unchanged when SaveChanges returns.</span></span>  
- <span data-ttu-id="f0fdd-118">Jednostki zmodyfikowane zostaną zaktualizowane w bazie danych, a następnie stają się zmian podczas SaveChanges zwraca.</span><span class="sxs-lookup"><span data-stu-id="f0fdd-118">Modified entities are updated in the database and then become Unchanged when SaveChanges returns.</span></span>  
- <span data-ttu-id="f0fdd-119">Usuniętych jednostek są usuwane z bazy danych, a następnie zostanie odłączony od kontekstu.</span><span class="sxs-lookup"><span data-stu-id="f0fdd-119">Deleted entities are deleted from the database and are then detached from the context.</span></span>  

<span data-ttu-id="f0fdd-120">W poniższych przykładach pokazano sposób, w którym można zmienić stanu jednostki lub grafu jednostki.</span><span class="sxs-lookup"><span data-stu-id="f0fdd-120">The following examples show ways in which the state of an entity or an entity graph can be changed.</span></span>  

## <a name="adding-a-new-entity-to-the-context"></a><span data-ttu-id="f0fdd-121">Dodawanie nowej jednostki do kontekstu</span><span class="sxs-lookup"><span data-stu-id="f0fdd-121">Adding a new entity to the context</span></span>  

<span data-ttu-id="f0fdd-122">Nowa jednostka można dodać do kontekstu poprzez wywołanie metody Add w DbSet.</span><span class="sxs-lookup"><span data-stu-id="f0fdd-122">A new entity can be added to the context by calling the Add method on DbSet.</span></span>
<span data-ttu-id="f0fdd-123">To powoduje umieszczenie jednostki w stanie Added, co oznacza, że zostanie on wstawiony do bazy danych przy następnym wywołaniu funkcji SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="f0fdd-123">This puts the entity into the Added state, meaning that it will be inserted into the database the next time that SaveChanges is called.</span></span>
<span data-ttu-id="f0fdd-124">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f0fdd-124">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Blogs.Add(blog);
    context.SaveChanges();
}
```  

<span data-ttu-id="f0fdd-125">Innym sposobem dodania nowego obiektu dla kontekstu jest aby zmienić jego stan dodany.</span><span class="sxs-lookup"><span data-stu-id="f0fdd-125">Another way to add a new entity to the context is to change its state to Added.</span></span> <span data-ttu-id="f0fdd-126">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f0fdd-126">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Entry(blog).State = EntityState.Added;
    context.SaveChanges();
}
```  

<span data-ttu-id="f0fdd-127">Na koniec możesz dodać nową jednostkę do kontekstu przez podłączenie jej do innej jednostki, która jest już śledzony.</span><span class="sxs-lookup"><span data-stu-id="f0fdd-127">Finally, you can add a new entity to the context by hooking it up to another entity that is already being tracked.</span></span>
<span data-ttu-id="f0fdd-128">Może to być, dodając nową jednostkę do właściwości nawigacji kolekcji innej jednostki lub przez ustawienie właściwości nawigacji odwołanie do innej jednostki, aby wskazywał nową jednostkę.</span><span class="sxs-lookup"><span data-stu-id="f0fdd-128">This could be by adding the new entity to the collection navigation property of another entity or by setting a reference navigation property of another entity to point to the new entity.</span></span> <span data-ttu-id="f0fdd-129">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f0fdd-129">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Add a new User by setting a reference from a tracked Blog
    var blog = context.Blogs.Find(1);
    blog.Owner = new User { UserName = "johndoe1987" };

    // Add a new Post by adding to the collection of a tracked Blog
    var blog = context.Blogs.Find(2);
    blog.Posts.Add(new Post { Name = "How to Add Entities" });

    context.SaveChanges();
}
```  

<span data-ttu-id="f0fdd-130">Należy zauważyć, że dla wszystkich tych przykładach, jeśli jednostka dodawany ma odwołania do innych jednostek, które nie są jeszcze śledzone następnie te nowe jednostki zostanie także dodana do kontekstu i zostaną wstawione do bazy danych przy następnym wywołaniu funkcji SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="f0fdd-130">Note that for all of these examples if the entity being added has references to other entities that are not yet tracked then these new entities will also be added to the context and will be inserted into the database the next time that SaveChanges is called.</span></span>  

## <a name="attaching-an-existing-entity-to-the-context"></a><span data-ttu-id="f0fdd-131">Dołączanie istniejącej jednostki do kontekstu</span><span class="sxs-lookup"><span data-stu-id="f0fdd-131">Attaching an existing entity to the context</span></span>  

<span data-ttu-id="f0fdd-132">Jeśli masz jednostki, który znasz już istnieje w bazie danych, ale który nie jest obecnie śledzony przez kontekst, a następnie będzie widnieć napis kontekstu do śledzenia jednostek przy użyciu metody Dołącz na DbSet.</span><span class="sxs-lookup"><span data-stu-id="f0fdd-132">If you have an entity that you know already exists in the database but which is not currently being tracked by the context then you can tell the context to track the entity using the Attach method on DbSet.</span></span> <span data-ttu-id="f0fdd-133">Jednostki będą w stanie niezmieniony w kontekście.</span><span class="sxs-lookup"><span data-stu-id="f0fdd-133">The entity will be in the Unchanged state in the context.</span></span> <span data-ttu-id="f0fdd-134">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f0fdd-134">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Blogs.Attach(existingBlog);

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="f0fdd-135">Należy pamiętać, że nie zostaną wprowadzone zmiany do bazy danych jeśli SaveChanges nazywa się bez wykonania tej czynności manipulowania dołączonych jednostki.</span><span class="sxs-lookup"><span data-stu-id="f0fdd-135">Note that no changes will be made to the database if SaveChanges is called without doing any other manipulation of the attached entity.</span></span> <span data-ttu-id="f0fdd-136">Jest tak, ponieważ jednostka jest w stanie niezmieniony.</span><span class="sxs-lookup"><span data-stu-id="f0fdd-136">This is because the entity is in the Unchanged state.</span></span>  

<span data-ttu-id="f0fdd-137">Innym sposobem na dołączanie istniejącej jednostki do kontekstu jest aby zmienić jego stan Unchanged.</span><span class="sxs-lookup"><span data-stu-id="f0fdd-137">Another way to attach an existing entity to the context is to change its state to Unchanged.</span></span> <span data-ttu-id="f0fdd-138">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f0fdd-138">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Unchanged;

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="f0fdd-139">Należy pamiętać, że dla obu tych przykładach Jeśli jednostki dołączany ma odwołania do innych jednostek, które nie są jeszcze śledzone następnie te nowe jednostki będą również dołączone do kontekstu w stanie niezmieniony.</span><span class="sxs-lookup"><span data-stu-id="f0fdd-139">Note that for both of these examples if the entity being attached has references to other entities that are not yet tracked then these new entities will also attached to the context in the Unchanged state.</span></span>  

## <a name="attaching-an-existing-but-modified-entity-to-the-context"></a><span data-ttu-id="f0fdd-140">Dołączanie do istniejącego, ale zmodyfikowano jednostkę do kontekstu</span><span class="sxs-lookup"><span data-stu-id="f0fdd-140">Attaching an existing but modified entity to the context</span></span>  

<span data-ttu-id="f0fdd-141">Jeśli masz jednostki, który znasz już istnieje w bazie danych, ale które mogą wprowadzono zmiany, a następnie będzie widnieć napis kontekstu, aby dołączyć jednostki i ustaw jego stan zmodyfikowane.</span><span class="sxs-lookup"><span data-stu-id="f0fdd-141">If you have an entity that you know already exists in the database but to which changes may have been made then you can tell the context to attach the entity and set its state to Modified.</span></span>
<span data-ttu-id="f0fdd-142">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f0fdd-142">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Modified;

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="f0fdd-143">Po zmianie stanu na zmodyfikowane właściwości jednostki zostanie oznaczona jako zmodyfikowana i wartości właściwości wysyłanych do bazy danych po wywołaniu funkcji SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="f0fdd-143">When you change the state to Modified all the properties of the entity will be marked as modified and all the property values will be sent to the database when SaveChanges is called.</span></span>  

<span data-ttu-id="f0fdd-144">Należy pamiętać, że jeśli jednostka dołączany zawiera odwołania do innych jednostek, które nie są jeszcze śledzone, następnie te nowe jednostki zostanie dołączony do kontekstu w stanie niezmieniony — ich nie zostaną automatycznie wprowadzone zmodyfikowane.</span><span class="sxs-lookup"><span data-stu-id="f0fdd-144">Note that if the entity being attached has references to other entities that are not yet tracked, then these new entities will attached to the context in the Unchanged state—they will not automatically be made Modified.</span></span>
<span data-ttu-id="f0fdd-145">Jeśli masz wiele jednostek, które muszą być oznaczone zmodyfikowane należy ustawić stan dla każdego z tych jednostek indywidualnie.</span><span class="sxs-lookup"><span data-stu-id="f0fdd-145">If you have multiple entities that need to be marked Modified you should set the state for each of these entities individually.</span></span>  

## <a name="changing-the-state-of-a-tracked-entity"></a><span data-ttu-id="f0fdd-146">Zmiany stanu jednostki śledzonych</span><span class="sxs-lookup"><span data-stu-id="f0fdd-146">Changing the state of a tracked entity</span></span>  

<span data-ttu-id="f0fdd-147">Można zmienić stanu jednostki, która jest już śledzony przez ustawienie właściwości stanu na jej wejścia.</span><span class="sxs-lookup"><span data-stu-id="f0fdd-147">You can change the state of an entity that is already being tracked by setting the State property on its entry.</span></span> <span data-ttu-id="f0fdd-148">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f0fdd-148">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Blogs.Attach(existingBlog);
    context.Entry(existingBlog).State = EntityState.Unchanged;

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="f0fdd-149">Należy pamiętać, że wywołanie apletu Dodaj lub Dołącz do jednostki, która jest już śledzony umożliwia także zmiany stanu jednostki.</span><span class="sxs-lookup"><span data-stu-id="f0fdd-149">Note that calling Add or Attach for an entity that is already tracked can also be used to change the entity state.</span></span> <span data-ttu-id="f0fdd-150">Na przykład wywołanie dołączenia dla jednostki, która jest obecnie w stanie dodany zmieni jego stan na Unchanged.</span><span class="sxs-lookup"><span data-stu-id="f0fdd-150">For example, calling Attach for an entity that is currently in the Added state will change its state to Unchanged.</span></span>  

## <a name="insert-or-update-pattern"></a><span data-ttu-id="f0fdd-151">Wstaw lub zaktualizuj wzorzec</span><span class="sxs-lookup"><span data-stu-id="f0fdd-151">Insert or update pattern</span></span>  

<span data-ttu-id="f0fdd-152">Typowym wzorcem dla niektórych aplikacji jest dodawanie jednostki jako nowy (co powoduje wstawienie bazy danych) lub dołączyć jednostkę jako istniejące i oznacz go jako zmodyfikowana (co powoduje aktualizację bazy danych) w zależności od wartości klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="f0fdd-152">A common pattern for some applications is to either Add an entity as new (resulting in a database insert) or Attach an entity as existing and mark it as modified (resulting in a database update) depending on the value of the primary key.</span></span>
<span data-ttu-id="f0fdd-153">Na przykład podczas korzystania z kluczy podstawowych całkowitą bazy danych, generowany jest wspólne dla obiektu z kluczem zero jako nowe i jednostki z kluczem różna od zera, jak istniejące.</span><span class="sxs-lookup"><span data-stu-id="f0fdd-153">For example, when using database generated integer primary keys it is common to treat an entity with a zero key as new and an entity with a non-zero key as existing.</span></span>
<span data-ttu-id="f0fdd-154">Ten wzorzec można osiągnąć przez ustawienie stanu jednostki, w oparciu o sprawdzenie wartości klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="f0fdd-154">This pattern can be achieved by setting the entity state based on a check of the primary key value.</span></span> <span data-ttu-id="f0fdd-155">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="f0fdd-155">For example:</span></span>  

``` csharp
public void InsertOrUpdate(Blog blog)
{
    using (var context = new BloggingContext())
    {
        context.Entry(blog).State = blog.BlogId == 0 ?
                                   EntityState.Added :
                                   EntityState.Modified;

        context.SaveChanges();
    }
}
```  

<span data-ttu-id="f0fdd-156">Należy pamiętać, że po zmianie stanu na zmodyfikowane właściwości jednostki zostanie oznaczona jako zmodyfikowana, i wartości właściwości wysyłanych do bazy danych po wywołaniu funkcji SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="f0fdd-156">Note that when you change the state to Modified all the properties of the entity will be marked as modified and all the property values will be sent to the database when SaveChanges is called.</span></span>  