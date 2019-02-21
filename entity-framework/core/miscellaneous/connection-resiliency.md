---
title: Elastyczność połączenia — EF Core
author: rowanmiller
ms.date: 11/15/2016
ms.assetid: e079d4af-c455-4a14-8e15-a8471516d748
uid: core/miscellaneous/connection-resiliency
ms.openlocfilehash: 6d8cf117dfd94524a53e10bb4a23c2a44c4c8e7b
ms.sourcegitcommit: 33b2e84dae96040f60a613186a24ff3c7b00b6db
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2019
ms.locfileid: "56459175"
---
# <a name="connection-resiliency"></a><span data-ttu-id="738a5-102">Elastyczność połączenia</span><span class="sxs-lookup"><span data-stu-id="738a5-102">Connection Resiliency</span></span>

<span data-ttu-id="738a5-103">Elastyczność połączenia automatycznie ponawia próbę polecenia bazy danych nie powiodło się.</span><span class="sxs-lookup"><span data-stu-id="738a5-103">Connection resiliency automatically retries failed database commands.</span></span> <span data-ttu-id="738a5-104">Funkcja może być używana z żadną bazą danych, podając "strategii wykonywania", która hermetyzuje logikę niezbędną do wykrywania błędów i ponów próbę wykonania polecenia.</span><span class="sxs-lookup"><span data-stu-id="738a5-104">The feature can be used with any database by supplying an "execution strategy", which encapsulates the logic necessary to detect failures and retry commands.</span></span> <span data-ttu-id="738a5-105">EF Core dostawców można podać strategii wykonywania dopasowane do swoich warunków błędów konkretnej bazy danych i zasady ponawiania optymalne.</span><span class="sxs-lookup"><span data-stu-id="738a5-105">EF Core providers can supply execution strategies tailored to their specific database failure conditions and optimal retry policies.</span></span>

<span data-ttu-id="738a5-106">Na przykład dostawca programu SQL Server zawiera strategii wykonywania, który jest w szczególny sposób dopasowane do programu SQL Server (w tym usługi SQL Azure).</span><span class="sxs-lookup"><span data-stu-id="738a5-106">As an example, the SQL Server provider includes an execution strategy that is specifically tailored to SQL Server (including SQL Azure).</span></span> <span data-ttu-id="738a5-107">Ją rozpoznaje rodzaje wyjątków, które mogą być ponawiane i posiada odpowiednie ustawienia domyślne, aby uzyskać maksymalną liczbę ponownych prób, opóźnienie między ponownych prób.</span><span class="sxs-lookup"><span data-stu-id="738a5-107">It is aware of the exception types that can be retried and has sensible defaults for maximum retries, delay between retries, etc.</span></span>

<span data-ttu-id="738a5-108">Strategia wykonywania jest określony podczas konfigurowania opcji dla kontekstu.</span><span class="sxs-lookup"><span data-stu-id="738a5-108">An execution strategy is specified when configuring the options for your context.</span></span> <span data-ttu-id="738a5-109">Jest to zazwyczaj w `OnConfiguring` metody pochodnej kontekstu:</span><span class="sxs-lookup"><span data-stu-id="738a5-109">This is typically in the `OnConfiguring` method of your derived context:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#OnConfiguring)]

<span data-ttu-id="738a5-110">lub `Startup.cs` dla aplikacji platformy ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="738a5-110">or in `Startup.cs` for an ASP.NET Core application:</span></span>

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<PicnicContext>(
        options => options.UseSqlServer(
            "<connection string>",
            providerOptions => providerOptions.EnableRetryOnFailure()));
}
```

## <a name="custom-execution-strategy"></a><span data-ttu-id="738a5-111">Strategia wykonywania niestandardowych</span><span class="sxs-lookup"><span data-stu-id="738a5-111">Custom execution strategy</span></span>

<span data-ttu-id="738a5-112">Istnieje mechanizm do zarejestrowania strategii wykonywania niestandardowych samodzielnie, jeśli chcesz zmienić dowolne z ustawień domyślnych.</span><span class="sxs-lookup"><span data-stu-id="738a5-112">There is a mechanism to register a custom execution strategy of your own if you wish to change any of the defaults.</span></span>

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseMyProvider(
            "<connection string>",
            options => options.ExecutionStrategy(...));
}
```

## <a name="execution-strategies-and-transactions"></a><span data-ttu-id="738a5-113">Strategii wykonywania i transakcji</span><span class="sxs-lookup"><span data-stu-id="738a5-113">Execution strategies and transactions</span></span>

<span data-ttu-id="738a5-114">Strategii wykonywania, który automatycznie ponawia próbę na błędy musi być w stanie odtworzyć każdej operacji w bloku ponawiania, która kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="738a5-114">An execution strategy that automatically retries on failures needs to be able to play back each operation in a retry block that fails.</span></span> <span data-ttu-id="738a5-115">Po włączeniu ponownych prób każdej operacji wykonywanych przy użyciu programu EF Core staje się własną wywołały operacji.</span><span class="sxs-lookup"><span data-stu-id="738a5-115">When retries are enabled, each operation you perform via EF Core becomes its own retriable operation.</span></span> <span data-ttu-id="738a5-116">Oznacza to, każde zapytanie i każde wywołanie `SaveChanges()` zostanie ponowiona jako jednostki, jeśli wystąpi błąd przejściowy.</span><span class="sxs-lookup"><span data-stu-id="738a5-116">That is, each query and each call to `SaveChanges()` will be retried as a unit if a transient failure occurs.</span></span>

<span data-ttu-id="738a5-117">Jednakże jeżeli Twój kod inicjuje transakcji przy użyciu `BeginTransaction()` definiujesz własną grupę działań, które muszą być traktowane jako jednostka i wszystko wewnątrz transakcji musi być odtwarzane ma miejsce awaria.</span><span class="sxs-lookup"><span data-stu-id="738a5-117">However, if your code initiates a transaction using `BeginTransaction()` you are defining your own group of operations that need to be treated as a unit, and everything inside the transaction would need to be played back shall a failure occur.</span></span> <span data-ttu-id="738a5-118">Jeśli spróbuje to zrobić, korzystając z strategii wykonywania, zostanie wyświetlony następujący wyjątek:</span><span class="sxs-lookup"><span data-stu-id="738a5-118">You will receive an exception like the following if you attempt to do this when using an execution strategy:</span></span>

> <span data-ttu-id="738a5-119">InvalidOperationException: Strategia wykonywania skonfigurowany "SqlServerRetryingExecutionStrategy" nie obsługuje transakcji zainicjowanej przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="738a5-119">InvalidOperationException: The configured execution strategy 'SqlServerRetryingExecutionStrategy' does not support user initiated transactions.</span></span> <span data-ttu-id="738a5-120">Strategia wykonywania zwróconych przez "DbContext.Database.CreateExecutionStrategy()" umożliwia wykonywanie wszystkich operacji w transakcji jako jednostka z możliwością ponowienia próby.</span><span class="sxs-lookup"><span data-stu-id="738a5-120">Use the execution strategy returned by 'DbContext.Database.CreateExecutionStrategy()' to execute all the operations in the transaction as a retriable unit.</span></span>

<span data-ttu-id="738a5-121">To rozwiązanie jest ręcznie wywołać strategii wykonywania z delegatem reprezentujący wszystko, co ma zostać wykonana.</span><span class="sxs-lookup"><span data-stu-id="738a5-121">The solution is to manually invoke the execution strategy with a delegate representing everything that needs to be executed.</span></span> <span data-ttu-id="738a5-122">Jeśli wystąpi błąd przejściowy, strategia wykonywania wywoła delegata ponownie.</span><span class="sxs-lookup"><span data-stu-id="738a5-122">If a transient failure occurs, the execution strategy will invoke the delegate again.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#ManualTransaction)]

<span data-ttu-id="738a5-123">To podejście można również otoczenia transakcji.</span><span class="sxs-lookup"><span data-stu-id="738a5-123">This approach can also be used with ambient transactions.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#AmbientTransaction)]

## <a name="transaction-commit-failure-and-the-idempotency-issue"></a><span data-ttu-id="738a5-124">Błąd zatwierdzania transakcji i problem idempotentności</span><span class="sxs-lookup"><span data-stu-id="738a5-124">Transaction commit failure and the idempotency issue</span></span>

<span data-ttu-id="738a5-125">Ogólnie rzecz biorąc gdy wystąpi awaria połączenia bieżąca transakcja zostanie wycofana.</span><span class="sxs-lookup"><span data-stu-id="738a5-125">In general, when there is a connection failure the current transaction is rolled back.</span></span> <span data-ttu-id="738a5-126">Jednakże, jeśli połączenie zostało przerwane, gdy transakcja jest zwrócenia zatwierdzone Wynikowy stan transakcji jest nieznany.</span><span class="sxs-lookup"><span data-stu-id="738a5-126">However, if the connection is dropped while the transaction is being committed the resulting state of the transaction is unknown.</span></span> <span data-ttu-id="738a5-127">Zobacz ten [wpis w blogu](https://blogs.msdn.com/b/adonet/archive/2013/03/11/sql-database-connectivity-and-the-idempotency-issue.aspx) Aby uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="738a5-127">See this [blog post](https://blogs.msdn.com/b/adonet/archive/2013/03/11/sql-database-connectivity-and-the-idempotency-issue.aspx) for more details.</span></span>

<span data-ttu-id="738a5-128">Domyślnie strategii wykonywania ponowi operację tak, jakby transakcja została wycofana, ale jeśli nie jest to wynikiem będzie wyjątek nowy stan bazy danych jest niezgodny lub może prowadzić do **uszkodzenie danych** Jeśli Operacja nie zależą od określonego stanu, na przykład podczas wstawiania nowego wiersza przy użyciu automatycznego generowania wartości klucza.</span><span class="sxs-lookup"><span data-stu-id="738a5-128">By default, the execution strategy will retry the operation as if the transaction was rolled back, but if it's not the case this will result in an exception if the new database state is incompatible or could lead to **data corruption** if the operation does not rely on a particular state, for example when inserting a new row with auto-generated key values.</span></span>

<span data-ttu-id="738a5-129">Istnieje kilka sposobów, aby poradzić sobie z tym.</span><span class="sxs-lookup"><span data-stu-id="738a5-129">There are several ways to deal with this.</span></span>

### <a name="option-1---do-almost-nothing"></a><span data-ttu-id="738a5-130">Opcja 1 — czy (prawie) nothing</span><span class="sxs-lookup"><span data-stu-id="738a5-130">Option 1 - Do (almost) nothing</span></span>

<span data-ttu-id="738a5-131">Prawdopodobieństwo awarii połączenia podczas zatwierdzania transakcji jest niska, dlatego może być akceptowalne, aby aplikacja została właśnie się niepowodzeniem, jeśli rzeczywiście występuje ten problem.</span><span class="sxs-lookup"><span data-stu-id="738a5-131">The likelihood of a connection failure during transaction commit is low so it may be acceptable for your application to just fail if this condition actually occurs.</span></span>

<span data-ttu-id="738a5-132">Jednak należy unikać używania generowane przez magazyn kluczy w celu zapewnienia, że zamiast opcji dodawania zduplikowany wiersz jest zgłaszany wyjątek.</span><span class="sxs-lookup"><span data-stu-id="738a5-132">However, you need to avoid using store-generated keys in order to ensure that an exception is thrown instead of adding a duplicate row.</span></span> <span data-ttu-id="738a5-133">Należy rozważyć użycie wartość identyfikatora GUID generowany przez klienta lub generator wartości po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="738a5-133">Consider using a client-generated GUID value or a client-side value generator.</span></span>

### <a name="option-2---rebuild-application-state"></a><span data-ttu-id="738a5-134">Opcja 2 — stan aplikacji ponownej kompilacji</span><span class="sxs-lookup"><span data-stu-id="738a5-134">Option 2 - Rebuild application state</span></span>

1. <span data-ttu-id="738a5-135">Odrzucenie aktualnego `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="738a5-135">Discard the current `DbContext`.</span></span>
2. <span data-ttu-id="738a5-136">Utwórz nową `DbContext` i przywrócenia stanu aplikacji z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="738a5-136">Create a new `DbContext` and restore the state of your application from the database.</span></span>
3. <span data-ttu-id="738a5-137">Informuje użytkownika, że ostatnia operacja może nie zostały zakończone pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="738a5-137">Inform the user that the last operation might not have been completed successfully.</span></span>

### <a name="option-3---add-state-verification"></a><span data-ttu-id="738a5-138">Opcja 3 — Dodawanie weryfikacji do stanu</span><span class="sxs-lookup"><span data-stu-id="738a5-138">Option 3 - Add state verification</span></span>

<span data-ttu-id="738a5-139">Dla większości działań, które zmieniają stan bazy danych jest można dodać kod, który sprawdza, czy powiodła się.</span><span class="sxs-lookup"><span data-stu-id="738a5-139">For most of the operations that change the database state it is possible to add code that checks whether it succeeded.</span></span> <span data-ttu-id="738a5-140">EF udostępnia metodę rozszerzenia, aby to ułatwić - `IExecutionStrategy.ExecuteInTransaction`.</span><span class="sxs-lookup"><span data-stu-id="738a5-140">EF provides an extension method to make this easier - `IExecutionStrategy.ExecuteInTransaction`.</span></span>

<span data-ttu-id="738a5-141">Ta metoda rozpoczyna się i zatwierdzeń transakcji i akceptuje także funkcję `verifySucceeded` parametrów, które jest wywoływane, gdy wystąpi błąd przejściowy podczas zatwierdzania transakcji.</span><span class="sxs-lookup"><span data-stu-id="738a5-141">This method begins and commits a transaction and also accepts a function in the `verifySucceeded` parameter that is invoked when a transient error occurs during the transaction commit.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Verification)]

> [!NOTE]
> <span data-ttu-id="738a5-142">W tym miejscu `SaveChanges` jest wywoływana z `acceptAllChangesOnSuccess` równa `false` Aby uniknąć zmieniania stanu `Blog` jednostki do `Unchanged` Jeśli `SaveChanges` zakończy się pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="738a5-142">Here `SaveChanges` is invoked with `acceptAllChangesOnSuccess` set to `false` to avoid changing the state of the `Blog` entity to `Unchanged` if `SaveChanges` succeeds.</span></span> <span data-ttu-id="738a5-143">Dzięki temu, aby ponowić próbę wykonania tej samej operacji, jeśli zatwierdzenie zakończy się niepowodzeniem, a transakcja zostanie wycofana.</span><span class="sxs-lookup"><span data-stu-id="738a5-143">This allows to retry the same operation if the commit fails and the transaction is rolled back.</span></span>

### <a name="option-4---manually-track-the-transaction"></a><span data-ttu-id="738a5-144">Opcja 4 — ręcznie śledzić transakcji</span><span class="sxs-lookup"><span data-stu-id="738a5-144">Option 4 - Manually track the transaction</span></span>

<span data-ttu-id="738a5-145">Jeśli musisz używać generowane przez magazyn kluczy lub potrzebujesz ogólny sposób obsługi błędów zatwierdzania, który nie są zależne od operacji wykonywanych każdej transakcji można przypisać Identyfikatora, który jest sprawdzany, jeśli zatwierdzenie zakończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="738a5-145">If you need to use store-generated keys or need a generic way of handling commit failures that doesn't depend on the operation performed each transaction could be assigned an ID that is checked when the commit fails.</span></span>

1. <span data-ttu-id="738a5-146">Dodaj tabelę w bazie danych używane do śledzenia stanu transakcji.</span><span class="sxs-lookup"><span data-stu-id="738a5-146">Add a table to the database used to track the status of the transactions.</span></span>
2. <span data-ttu-id="738a5-147">Wstaw wiersz do tabeli na początku każdej transakcji.</span><span class="sxs-lookup"><span data-stu-id="738a5-147">Insert a row into the table at the beginning of each transaction.</span></span>
3. <span data-ttu-id="738a5-148">Jeśli połączenie nie powiedzie się podczas zatwierdzania, sprawdź, czy obecność odpowiedni wiersz w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="738a5-148">If the connection fails during the commit, check for the presence of the corresponding row in the database.</span></span>
4. <span data-ttu-id="738a5-149">Jeśli zatwierdzenie zakończy się pomyślnie, usuń odpowiedni wiersz w celu uniknięcia wzrostu tabeli.</span><span class="sxs-lookup"><span data-stu-id="738a5-149">If the commit is successful, delete the corresponding row to avoid the growth of the table.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Tracking)]

> [!NOTE]
> <span data-ttu-id="738a5-150">Upewnij się, że kontekst użyty w celu weryfikacji ma zdefiniowany jako połączenie jest prawdopodobnie nastąpi ich awaria ponownie podczas weryfikacji w przypadku niepowodzenia podczas zatwierdzania transakcji strategii wykonywania.</span><span class="sxs-lookup"><span data-stu-id="738a5-150">Make sure that the context used for the verification has an execution strategy defined as the connection is likely to fail again during verification if it failed during transaction commit.</span></span>
