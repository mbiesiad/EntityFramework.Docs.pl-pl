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
# <a name="handling-transaction-commit-failures"></a>Obsługa błędów zatwierdzania transakcji
> [!NOTE]
> **EF6.1 Tylko —** funkcje, interfejsy API itp. Jeśli używasz wcześniejszej wersji, niektóre lub wszystkie informacje nie mają zastosowania.  

W ramach 6.1 wprowadzamy nową funkcję odporności połączenia dla EF: możliwość automatycznego wykrywania i odzyskiwania, gdy przejściowe błędy połączenia wpływają na potwierdzenie zatwierdzeń transakcji. Pełne szczegóły scenariusza najlepiej opisać w blogu [SQL Database Connectivity i problem idempotency .](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue)  Podsumowując, scenariusz jest taki, że gdy wyjątek jest zgłaszany podczas zatwierdzania transakcji, istnieją dwie możliwe przyczyny:  

1. Zatwierdzenie transakcji nie powiodło się na serwerze
2. Zatwierdzenie transakcji powiodło się na serwerze, ale problem z łącznością uniemożliwił dotarcie powiadomienia o powodzeniu do klienta  

Gdy pierwsza sytuacja się stanie, aplikacja lub użytkownik może ponowić próbę wykonania operacji, ale gdy wystąpi druga sytuacja ponawia próby należy unikać i aplikacja może odzyskać automatycznie. Wyzwaniem jest to, że bez możliwości wykrycia, co było rzeczywistą przyczyną wyjątek został zgłoszony podczas zatwierdzania, aplikacja nie może wybrać właściwy kierunek działania. Nowa funkcja w EF 6.1 umożliwia EF dokładnie sprawdzić z bazy danych, jeśli transakcja powiodła się i podjąć właściwy kierunek działania w sposób przejrzysty.  

## <a name="using-the-feature"></a>Korzystanie z funkcji  

Aby włączyć funkcję, należy dołączyć wywołanie [SetTransactionHandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx) w konstruktorze **DbConfiguration**. Jeśli nie znasz **konfiguracji dbconfiguration,** zobacz [Konfiguracja oparta na kodzie](~/ef6/fundamentals/configuring/code-based.md). Ta funkcja może być używana w połączeniu z automatycznymi próbami ponawiania wprowadzonymi w EF6, które pomagają w sytuacji, w której transakcja faktycznie nie została zatwierdzona na serwerze z powodu przejściowego błędu:  

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

## <a name="how-transactions-are-tracked"></a>Sposób śledzenia transakcji  

Gdy funkcja jest włączona, EF automatycznie doda nową tabelę do bazy danych o nazwie **__Transactions**. Nowy wiersz jest wstawiany w tej tabeli za każdym razem, gdy transakcja jest tworzona przez EF i ten wiersz jest sprawdzany pod kątem istnienia, jeśli wystąpi błąd transakcji podczas zatwierdzania.  

Mimo że EF dołoży wszelkich starań, aby przyciąć wiersze z tabeli, gdy nie są już potrzebne, tabela może wzrosnąć, jeśli aplikacja kończy pracę przedwcześnie i z tego powodu może być konieczne przeczyszczenie tabeli ręcznie w niektórych przypadkach.  

## <a name="how-to-handle-commit-failures-with-previous-versions"></a>Jak radzić sobie z błędami zatwierdzania w poprzednich wersjach

Przed EF 6.1 nie było mechanizmu do obsługi błędów zatwierdzania w produkcie EF. Istnieje kilka sposobów radzenia sobie z tą sytuacją, które mogą być stosowane do poprzednich wersji EF6:  

* Opcja 1 - Nic nie rób  

  Prawdopodobieństwo niepowodzenia połączenia podczas zatwierdzania transakcji jest niska, więc może być dopuszczalne dla aplikacji po prostu zakończyć się niepowodzeniem, jeśli ten warunek faktycznie występuje.  

* Opcja 2 - Użyj bazy danych, aby zresetować stan  

  1. Odrzucanie bieżącego kontekstu db  
  2. Tworzenie nowego dbContext i przywracanie stanu aplikacji z bazy danych  
  3. Poinformuj użytkownika, że ostatnia operacja mogła nie zostać ukończona pomyślnie  

* Opcja 3 - Ręczne śledzenie transakcji  

  1. Dodaj nieśledzonych tabeli do bazy danych używanej do śledzenia stanu transakcji.  
  2. Wstaw wiersz do tabeli na początku każdej transakcji.  
  3. Jeśli połączenie nie powiedzie się podczas zatwierdzania, sprawdź obecność odpowiedniego wiersza w bazie danych.  
     - Jeśli wiersz jest obecny, kontynuuj normalnie, ponieważ transakcja została pomyślnie zatwierdzona  
     - Jeśli wiersz jest nieobecny, użyj strategii wykonywania, aby ponowić próbę bieżącej operacji.  
  4. Jeśli zatwierdzenie zakończy się pomyślnie, usuń odpowiedni wiersz, aby uniknąć wzrostu tabeli.  

[Ten wpis w blogu](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue) zawiera przykładowy kod do wykonania tego na platformie SQL Azure.  
