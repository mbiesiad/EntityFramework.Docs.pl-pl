---
title: Obsługa błędów zatwierdzania transakcji — EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 5b1f7a7d-1b24-4645-95ec-5608a31ef577
ms.openlocfilehash: cf2722496e207a8ecaa9cfaa4ca61e7248e5e58f
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434139"
---
# <a name="handling-transaction-commit-failures"></a><span data-ttu-id="ebbe5-102">Obsługa błędów zatwierdzania transakcji</span><span class="sxs-lookup"><span data-stu-id="ebbe5-102">Handling transaction commit failures</span></span>
> [!NOTE]
> <span data-ttu-id="ebbe5-103">**EF6.1 Tylko —** funkcje, interfejsy API itp.</span><span class="sxs-lookup"><span data-stu-id="ebbe5-103">**EF6.1 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.1.</span></span> <span data-ttu-id="ebbe5-104">Jeśli używasz wcześniejszej wersji, niektóre lub wszystkie informacje nie mają zastosowania.</span><span class="sxs-lookup"><span data-stu-id="ebbe5-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="ebbe5-105">W ramach 6.1 wprowadzamy nową funkcję odporności połączenia dla EF: możliwość automatycznego wykrywania i odzyskiwania, gdy przejściowe błędy połączenia wpływają na potwierdzenie zatwierdzeń transakcji.</span><span class="sxs-lookup"><span data-stu-id="ebbe5-105">As part of 6.1 we are introducing a new connection resiliency feature for EF: the ability to detect and recover automatically when transient connection failures affect the acknowledgement of transaction commits.</span></span> <span data-ttu-id="ebbe5-106">Pełne szczegóły scenariusza najlepiej opisać w blogu [SQL Database Connectivity i problem idempotency .](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue)</span><span class="sxs-lookup"><span data-stu-id="ebbe5-106">The full details of the scenario are best described in the blog post [SQL Database Connectivity and the Idempotency Issue](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue).</span></span>  <span data-ttu-id="ebbe5-107">Podsumowując, scenariusz jest taki, że gdy wyjątek jest zgłaszany podczas zatwierdzania transakcji, istnieją dwie możliwe przyczyny:</span><span class="sxs-lookup"><span data-stu-id="ebbe5-107">In summary, the scenario is that when an exception is raised during a transaction commit there are two possible causes:</span></span>  

1. <span data-ttu-id="ebbe5-108">Zatwierdzenie transakcji nie powiodło się na serwerze</span><span class="sxs-lookup"><span data-stu-id="ebbe5-108">The transaction commit failed on the server</span></span>
2. <span data-ttu-id="ebbe5-109">Zatwierdzenie transakcji powiodło się na serwerze, ale problem z łącznością uniemożliwił dotarcie powiadomienia o powodzeniu do klienta</span><span class="sxs-lookup"><span data-stu-id="ebbe5-109">The transaction commit succeeded on the server but a connectivity issue prevented the success notification from reaching the client</span></span>  

<span data-ttu-id="ebbe5-110">Gdy pierwsza sytuacja się stanie, aplikacja lub użytkownik może ponowić próbę wykonania operacji, ale gdy wystąpi druga sytuacja ponawia próby należy unikać i aplikacja może odzyskać automatycznie.</span><span class="sxs-lookup"><span data-stu-id="ebbe5-110">When the first situation happens the application or the user can retry the operation, but when the second situation occurs retries should be avoided and the application could recover automatically.</span></span> <span data-ttu-id="ebbe5-111">Wyzwaniem jest to, że bez możliwości wykrycia, co było rzeczywistą przyczyną wyjątek został zgłoszony podczas zatwierdzania, aplikacja nie może wybrać właściwy kierunek działania.</span><span class="sxs-lookup"><span data-stu-id="ebbe5-111">The challenge is that without the ability to detect what was the actual reason an exception was reported during commit, the application cannot choose the right course of action.</span></span> <span data-ttu-id="ebbe5-112">Nowa funkcja w EF 6.1 umożliwia EF dokładnie sprawdzić z bazy danych, jeśli transakcja powiodła się i podjąć właściwy kierunek działania w sposób przejrzysty.</span><span class="sxs-lookup"><span data-stu-id="ebbe5-112">The new feature in EF 6.1 allows EF to double-check with the database if the transaction succeeded and take the right course of action transparently.</span></span>  

## <a name="using-the-feature"></a><span data-ttu-id="ebbe5-113">Korzystanie z funkcji</span><span class="sxs-lookup"><span data-stu-id="ebbe5-113">Using the feature</span></span>  

<span data-ttu-id="ebbe5-114">Aby włączyć funkcję, należy dołączyć wywołanie [SetTransactionHandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx) w konstruktorze **DbConfiguration**.</span><span class="sxs-lookup"><span data-stu-id="ebbe5-114">In order to enable the feature you need include a call to [SetTransactionHandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx) in the constructor of your **DbConfiguration**.</span></span> <span data-ttu-id="ebbe5-115">Jeśli nie znasz **konfiguracji dbconfiguration,** zobacz [Konfiguracja oparta na kodzie](~/ef6/fundamentals/configuring/code-based.md).</span><span class="sxs-lookup"><span data-stu-id="ebbe5-115">If you are unfamiliar with **DbConfiguration**, see [Code Based Configuration](~/ef6/fundamentals/configuring/code-based.md).</span></span> <span data-ttu-id="ebbe5-116">Ta funkcja może być używana w połączeniu z automatycznymi próbami ponawiania wprowadzonymi w EF6, które pomagają w sytuacji, w której transakcja faktycznie nie została zatwierdzona na serwerze z powodu przejściowego błędu:</span><span class="sxs-lookup"><span data-stu-id="ebbe5-116">This feature can be used in combination with the automatic retries we introduced in EF6, which help in the situation in which the transaction actually failed to commit on the server due to a transient failure:</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.SqlServer;

public class MyConfiguration : DbConfiguration  
{
  public MyConfiguration()  
  {  
    SetTransactionHandler(SqlProviderServices.ProviderInvariantName, () => new CommitFailureHandler());  
    SetExecutionStrategy(SqlProviderServices.ProviderInvariantName, () => new SqlAzureExecutionStrategy());  
  }  
}
```  

## <a name="how-transactions-are-tracked"></a><span data-ttu-id="ebbe5-117">Sposób śledzenia transakcji</span><span class="sxs-lookup"><span data-stu-id="ebbe5-117">How transactions are tracked</span></span>  

<span data-ttu-id="ebbe5-118">Gdy funkcja jest włączona, EF automatycznie doda nową tabelę do bazy danych o nazwie **__Transactions**.</span><span class="sxs-lookup"><span data-stu-id="ebbe5-118">When the feature is enabled, EF will automatically add a new table to the database called **__Transactions**.</span></span> <span data-ttu-id="ebbe5-119">Nowy wiersz jest wstawiany w tej tabeli za każdym razem, gdy transakcja jest tworzona przez EF i ten wiersz jest sprawdzany pod kątem istnienia, jeśli wystąpi błąd transakcji podczas zatwierdzania.</span><span class="sxs-lookup"><span data-stu-id="ebbe5-119">A new row is inserted in this table every time a transaction is created by EF and that row is checked for existence if a transaction failure occurs during commit.</span></span>  

<span data-ttu-id="ebbe5-120">Mimo że EF dołoży wszelkich starań, aby przyciąć wiersze z tabeli, gdy nie są już potrzebne, tabela może wzrosnąć, jeśli aplikacja kończy pracę przedwcześnie i z tego powodu może być konieczne przeczyszczenie tabeli ręcznie w niektórych przypadkach.</span><span class="sxs-lookup"><span data-stu-id="ebbe5-120">Although EF will do a best effort to prune rows from the table when they aren’t needed anymore, the table can grow if the application exits prematurely and for that reason you may need to purge the table manually in some cases.</span></span>  

## <a name="how-to-handle-commit-failures-with-previous-versions"></a><span data-ttu-id="ebbe5-121">Jak radzić sobie z błędami zatwierdzania w poprzednich wersjach</span><span class="sxs-lookup"><span data-stu-id="ebbe5-121">How to handle commit failures with previous Versions</span></span>

<span data-ttu-id="ebbe5-122">Przed EF 6.1 nie było mechanizmu do obsługi błędów zatwierdzania w produkcie EF.</span><span class="sxs-lookup"><span data-stu-id="ebbe5-122">Before EF 6.1 there was not mechanism to handle commit failures in the EF product.</span></span> <span data-ttu-id="ebbe5-123">Istnieje kilka sposobów radzenia sobie z tą sytuacją, które mogą być stosowane do poprzednich wersji EF6:</span><span class="sxs-lookup"><span data-stu-id="ebbe5-123">There are several ways to dealing with this situation that can be applied to previous versions of EF6:</span></span>  

* <span data-ttu-id="ebbe5-124">Opcja 1 - Nic nie rób</span><span class="sxs-lookup"><span data-stu-id="ebbe5-124">Option 1 - Do nothing</span></span>  

  <span data-ttu-id="ebbe5-125">Prawdopodobieństwo niepowodzenia połączenia podczas zatwierdzania transakcji jest niska, więc może być dopuszczalne dla aplikacji po prostu zakończyć się niepowodzeniem, jeśli ten warunek faktycznie występuje.</span><span class="sxs-lookup"><span data-stu-id="ebbe5-125">The likelihood of a connection failure during transaction commit is low so it may be acceptable for your application to just fail if this condition actually occurs.</span></span>  

* <span data-ttu-id="ebbe5-126">Opcja 2 - Użyj bazy danych, aby zresetować stan</span><span class="sxs-lookup"><span data-stu-id="ebbe5-126">Option 2 - Use the database to reset state</span></span>  

  1. <span data-ttu-id="ebbe5-127">Odrzucanie bieżącego kontekstu db</span><span class="sxs-lookup"><span data-stu-id="ebbe5-127">Discard the current DbContext</span></span>  
  2. <span data-ttu-id="ebbe5-128">Tworzenie nowego dbContext i przywracanie stanu aplikacji z bazy danych</span><span class="sxs-lookup"><span data-stu-id="ebbe5-128">Create a new DbContext and restore the state of your application from the database</span></span>  
  3. <span data-ttu-id="ebbe5-129">Poinformuj użytkownika, że ostatnia operacja mogła nie zostać ukończona pomyślnie</span><span class="sxs-lookup"><span data-stu-id="ebbe5-129">Inform the user that the last operation might not have been completed successfully</span></span>  

* <span data-ttu-id="ebbe5-130">Opcja 3 - Ręczne śledzenie transakcji</span><span class="sxs-lookup"><span data-stu-id="ebbe5-130">Option 3 - Manually track the transaction</span></span>  

  1. <span data-ttu-id="ebbe5-131">Dodaj nieśledzonych tabeli do bazy danych używanej do śledzenia stanu transakcji.</span><span class="sxs-lookup"><span data-stu-id="ebbe5-131">Add a non-tracked table to the database used to track the status of the transactions.</span></span>  
  2. <span data-ttu-id="ebbe5-132">Wstaw wiersz do tabeli na początku każdej transakcji.</span><span class="sxs-lookup"><span data-stu-id="ebbe5-132">Insert a row into the table at the beginning of each transaction.</span></span>  
  3. <span data-ttu-id="ebbe5-133">Jeśli połączenie nie powiedzie się podczas zatwierdzania, sprawdź obecność odpowiedniego wiersza w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="ebbe5-133">If the connection fails during the commit, check for the presence of the corresponding row in the database.</span></span>  
     - <span data-ttu-id="ebbe5-134">Jeśli wiersz jest obecny, kontynuuj normalnie, ponieważ transakcja została pomyślnie zatwierdzona</span><span class="sxs-lookup"><span data-stu-id="ebbe5-134">If the row is present, continue normally, as the transaction was committed successfully</span></span>  
     - <span data-ttu-id="ebbe5-135">Jeśli wiersz jest nieobecny, użyj strategii wykonywania, aby ponowić próbę bieżącej operacji.</span><span class="sxs-lookup"><span data-stu-id="ebbe5-135">If the row is absent, use an execution strategy to retry the current operation.</span></span>  
  4. <span data-ttu-id="ebbe5-136">Jeśli zatwierdzenie zakończy się pomyślnie, usuń odpowiedni wiersz, aby uniknąć wzrostu tabeli.</span><span class="sxs-lookup"><span data-stu-id="ebbe5-136">If the commit is successful, delete the corresponding row to avoid the growth of the table.</span></span>  

<span data-ttu-id="ebbe5-137">[Ten wpis w blogu](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue) zawiera przykładowy kod do wykonania tego na platformie SQL Azure.</span><span class="sxs-lookup"><span data-stu-id="ebbe5-137">[This blog post](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue) contains sample code for accomplishing this on SQL Azure.</span></span>  
