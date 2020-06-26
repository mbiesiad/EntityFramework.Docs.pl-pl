---
title: Ocena klienta a serwer — EF Core
author: smitpatel
ms.date: 10/03/2019
ms.assetid: 8b6697cc-7067-4dc2-8007-85d80503d123
uid: core/querying/client-eval
ms.openlocfilehash: 429fec2d2da6eb8910ac20531455ec8183096ec6
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370425"
---
# <a name="client-vs-server-evaluation"></a>Klient a wersja ewaluacyjna serwera

Zgodnie z ogólną zasadą Entity Framework Core próbuje w miarę możliwości oszacować zapytanie na serwerze. EF Core konwertuje części zapytania na parametry, które mogą być oceniane po stronie klienta. Pozostała część zapytania (wraz z wygenerowanymi parametrami) jest podawana dostawcy bazy danych w celu ustalenia równoważnej kwerendy bazy danych, która ma zostać obliczona na serwerze. EF Core obsługuje częściową ocenę klienta w projekcji najwyższego poziomu (zasadniczo jest to ostatnie wywołanie do `Select()` ). Jeśli projekcja najwyższego poziomu w zapytaniu nie może zostać przetłumaczona na serwer, EF Core pobierze wymagane dane z serwera i Oceń pozostałe części zapytania na kliencie. Jeśli EF Core wykryje wyrażenie w innym miejscu niż projekcja najwyższego poziomu, którego nie można przetłumaczyć na serwer, wówczas zgłasza wyjątek czasu wykonania. Zobacz, [jak działają zapytania](xref:core/querying/how-query-works) , aby zrozumieć, jak EF Core określa, czego nie można przetłumaczyć na serwer.

> [!NOTE]
> Przed wersjami 3,0 Entity Framework Core obsługiwaną ocenę klienta w dowolnym miejscu zapytania. Aby uzyskać więcej informacji, zobacz [sekcję poprzednie wersje](#previous-versions).

> [!TIP]
> [Przykład](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) tego artykułu można wyświetlić w witrynie GitHub.

## <a name="client-evaluation-in-the-top-level-projection"></a>Ocena klienta w projekcji najwyższego poziomu

W poniższym przykładzie metoda pomocnika służy do standaryzacji adresów URL blogów, które są zwracane z bazy danych SQL Server. Ponieważ dostawca SQL Server nie ma wglądu w sposób implementacji tej metody, nie można go przetłumaczyć na SQL. Wszystkie inne aspekty zapytania są oceniane w bazie danych, ale przekazywanie zwracanych `URL` przez tę metodę jest wykonywane na kliencie.

[!code-csharp[Main](../../../samples/core/Querying/ClientEval/Sample.cs#ClientProjection)]

[!code-csharp[Main](../../../samples/core/Querying/ClientEval/Sample.cs#ClientMethod)]

## <a name="unsupported-client-evaluation"></a>Nieobsługiwana Ocena klienta

Gdy Ocena klienta jest użyteczna, może to spowodować niską wydajność. Rozważmy następujące zapytanie, w którym metoda pomocnika jest teraz używana w filtrze WHERE. Ponieważ w bazie danych nie można zastosować filtru, wszystkie dane muszą zostać pobrane do pamięci, aby zastosować filtr na kliencie. Na podstawie filtru i ilości danych na serwerze Ocena klienta może spowodować spadek wydajności. Dlatego Entity Framework Core blokuje takie oceny klienta i zgłasza wyjątek czasu wykonywania.

[!code-csharp[Main](../../../samples/core/Querying/ClientEval/Sample.cs#ClientWhere)]

## <a name="explicit-client-evaluation"></a>Jawne oszacowanie klienta

Może być konieczne jawne wymuszenie oceny klienta w niektórych przypadkach, takich jak następujące

- Ilość danych jest mała, co oznacza, że Ocena na kliencie nie wiąże się z ogromnymi karami za wydajność.
- Używany operator LINQ nie ma żadnego tłumaczenia po stronie serwera.

W takich przypadkach można jawnie zadecydować o ocenie klienta, wywołując metody takie jak `AsEnumerable` lub `ToList` ( `AsAsyncEnumerable` lub `ToListAsync` dla Async). Użycie `AsEnumerable` przesyłania strumieniowego spowoduje przetworzenie strumienia wyników, ale użycie `ToList` buforu spowoduje utworzenie listy, która również zajmuje dodatkową pamięć. Jeśli jednak wyliczasz wiele razy, przechowywanie wyników na liście pomoże więcej, ponieważ istnieje tylko jedno zapytanie do bazy danych. W zależności od określonego użycia należy oszacować, która metoda jest bardziej przydatna w przypadku.

[!code-csharp[Main](../../../samples/core/Querying/ClientEval/Sample.cs#ExplicitClientEval)]

## <a name="potential-memory-leak-in-client-evaluation"></a>Potencjalny przeciek pamięci w ocenie klienta

Ponieważ translacja zapytań i kompilacja są kosztowne, EF Core buforowania skompilowanego planu zapytania. Obiekt delegowany pamięci podręcznej może używać kodu klienta podczas przeprowadzania oceny klienta projekcji najwyższego poziomu. EF Core generuje parametry dla części drzewa, które są oceniane przez klienta, i ponownie używa planu zapytania przez zastąpienie wartości parametrów. Ale niektóre stałe w drzewie wyrażenia nie mogą być konwertowane do parametrów. Jeśli delegowany pamięci podręcznej zawiera takie stałe, wówczas te obiekty nie mogą być zbierane jako elementy bezużyteczne, ponieważ nadal występują odwołania. Jeśli taki obiekt zawiera DbContext lub inne usługi, może to spowodować zwiększenie użycia pamięci przez aplikację w czasie. To zachowanie zwykle jest znakiem przecieku pamięci. EF Core zgłasza wyjątek za każdym razem, gdy zawiera on stałe typu, którego nie można mapować przy użyciu bieżącego dostawcy bazy danych. Typowe przyczyny i ich rozwiązania są następujące:

- **Przy użyciu metody wystąpienia**: w przypadku używania metod wystąpienia w projekcji klienta drzewo wyrażenia zawiera stałą wystąpienia. Jeśli metoda nie używa żadnych danych z wystąpienia, należy rozważyć uczynienie metody statyczną. Jeśli potrzebujesz danych wystąpienia w treści metody, Przekaż określone dane jako argument do metody.
- **Przekazywanie stałych argumentów do metody**: ten przypadek występuje ogólnie przy użyciu `this` argumentu w metodzie klienta. Rozważ podzielenie argumentu w do wielu argumentów skalarnych, które mogą być mapowane przez dostawcę bazy danych.
- **Inne stałe**: Jeśli stała jest w każdym innym przypadku, można sprawdzić, czy stała jest wymagana podczas przetwarzania. Jeśli jest to konieczne, aby stała się dostępna, lub jeśli nie możesz użyć rozwiązania z powyższych przypadków, Utwórz zmienną lokalną do przechowywania wartości i Użyj zmiennej lokalnej w zapytaniu. EF Core przekonwertuje zmienną lokalną do parametru.

## <a name="previous-versions"></a>Poprzednie wersje

Poniższa sekcja ma zastosowanie do wersji EF Core przed 3,0.

Starsze wersje EF Core obsługują ocenę klienta w dowolnej części zapytania — a nie tylko projekcję najwyższego poziomu. Dlatego, że zapytania podobne do jednego ogłoszono w sekcji [nieobsługiwana wersja ewaluacyjna klienta](#unsupported-client-evaluation) działały prawidłowo. Ponieważ takie zachowanie może spowodować problemy z wydajnością, EF Core rejestrować ostrzeżenia oceny klienta. Aby uzyskać więcej informacji o wyświetlaniu danych wyjściowych rejestrowania, zobacz [Rejestrowanie](xref:core/miscellaneous/logging).

Opcjonalnie EF Core można zmienić zachowanie domyślne, aby zgłosić wyjątek lub nic nie robić podczas oceny klienta (z wyjątkiem w projekcji). Wyjątek zgłaszający zachowanie mógłby być podobny do zachowania w 3,0. Aby zmienić zachowanie, należy skonfigurować ostrzeżenia podczas konfigurowania opcji dla kontekstu — zwykle w programie `DbContext.OnConfiguring` , lub w `Startup.cs` przypadku korzystania z ASP.NET Core.

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFQuerying;Trusted_Connection=True;")
        .ConfigureWarnings(warnings => warnings.Throw(RelationalEventId.QueryClientEvaluationWarning));
}
```
