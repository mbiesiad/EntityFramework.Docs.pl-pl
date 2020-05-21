---
title: Tworzenie DbContext w czasie projektowania — EF Core
author: bricelam
ms.author: bricelam
ms.date: 09/16/2019
uid: core/miscellaneous/cli/dbcontext-creation
ms.openlocfilehash: 0b0271dcabea63a2529c091cc14cb9059d56ac8d
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672956"
---
# <a name="design-time-dbcontext-creation"></a>Tworzenie klasy DbContext w czasie projektowania

Niektóre polecenia narzędzi EF Core (na przykład polecenia [migracji][1] ) wymagają utworzenia wystąpienia pochodnego w `DbContext` czasie projektowania w celu zebrania szczegółowych informacji o typach jednostek aplikacji i sposobie ich mapowania na schemat bazy danych. W większości przypadków wskazane jest, aby utworzone w `DbContext` ten sposób jest skonfigurowane w podobny sposób jak w [czasie wykonywania][2].

Istnieją różne sposoby tworzenia `DbContext` :

## <a name="from-application-services"></a>Z usług aplikacji

Jeśli projekt startowy używa hosta [sieci Web ASP.NET Core][3] lub [hosta ogólnego platformy .NET Core][4], narzędzia próbują uzyskać obiekt DbContext od dostawcy usług aplikacji.

Narzędzia najpierw próbują uzyskać dostawcę usług `Program.CreateHostBuilder()` , wywołując wywoływanie, `Build()` a następnie uzyskując dostęp do `Services` właściwości.

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/ApplicationService.cs)]

> [!NOTE]
> Podczas tworzenia nowej aplikacji ASP.NET Core ten element Hook jest uwzględniany domyślnie.

`DbContext`Sama i wszystkie zależności w jego konstruktorze muszą być zarejestrowane jako usługi w dostawcy usług aplikacji. Można to łatwo osiągnąć poprzez posiadanie [konstruktora na `DbContext` , który przyjmuje wystąpienie `DbContextOptions<TContext>` jako argument][5] i przy użyciu [ `AddDbContext<TContext>` metody][6].

## <a name="using-a-constructor-with-no-parameters"></a>Korzystanie z konstruktora bez parametrów

Jeśli nie można uzyskać elementu DbContext od dostawcy usług aplikacji, narzędzia szukają typu pochodnego `DbContext` wewnątrz projektu. Następnie próbują utworzyć wystąpienie przy użyciu konstruktora bez parametrów. Może to być Konstruktor domyślny, jeśli `DbContext` jest skonfigurowany przy użyciu [`OnConfiguring`][7] metody.

## <a name="from-a-design-time-factory"></a>Z fabryki czasu projektowania

Możesz również poinformować narzędzia jak utworzyć DbContext, implementując `IDesignTimeDbContextFactory<TContext>` Interfejs: Jeśli klasa implementująca ten interfejs znajduje się w tym samym projekcie, co pochodna `DbContext` lub w projekcie startowym aplikacji, narzędzia te pomijają inne sposoby tworzenia kontekstu DBI używania fabryki czasu projektowania.

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/BloggingContextFactory.cs)]

> [!NOTE]
> Przed EFCore 5,0 `args` parametr był nieużywany (zobacz [ten problem][8]).
> Jest to rozwiązane w EFCore 5,0 i wszelkie dodatkowe argumenty czasu projektowania są przesyłane do aplikacji za pomocą tego parametru.

Fabryka czasu projektowania może być szczególnie przydatna, jeśli trzeba skonfigurować DbContext w inny sposób niż czas projektowania niż w czasie wykonywania, jeśli `DbContext` Konstruktor przyjmuje dodatkowe parametry nie są zarejestrowane w programie di, jeśli nie używasz di lub jeśli z jakiegoś powodu wolisz, aby nie mieć `BuildWebHost` metody w klasie aplikacji ASP.NET Core `Main` .

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/miscellaneous/configuring-dbcontext
  [3]: /aspnet/core/fundamentals/host/web-host
  [4]: /aspnet/core/fundamentals/host/generic-host
  [5]: xref:core/miscellaneous/configuring-dbcontext#constructor-argument
  [6]: xref:core/miscellaneous/configuring-dbcontext#using-dbcontext-with-dependency-injection
  [7]: xref:core/miscellaneous/configuring-dbcontext#onconfiguring
  [8]: https://github.com/aspnet/EntityFrameworkCore/issues/8332
