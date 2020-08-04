---
title: Rejestrowanie — EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: f6e35c6d-45b7-4258-be1d-87c1bb67438d
uid: core/miscellaneous/logging
ms.openlocfilehash: 4f39e0ad1f061970aae7f7eb7abdc72e4bb0d691
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526462"
---
# <a name="logging"></a>Rejestrowanie

> [!TIP]  
> [Przykład](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) tego artykułu można wyświetlić w witrynie GitHub.

## <a name="aspnet-core-applications"></a>ASP.NET Core aplikacji

EF Core integruje się automatycznie z mechanizmami rejestrowania ASP.NET Core za każdym razem, gdy `AddDbContext` `AddDbContextPool` jest używany. W związku z tym podczas korzystania z ASP.NET Core należy skonfigurować rejestrowanie zgodnie z opisem w [dokumentacji ASP.NET Core](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).

## <a name="other-applications"></a>Inne aplikacje

Rejestrowanie EF Core wymaga ILoggerFactory, który sam jest skonfigurowany przy użyciu co najmniej jednego dostawcy rejestrowania. Typowi dostawcy są dostarczani w następujących pakietach:

* [Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): prosty Rejestrator konsoli.
* [Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/): obsługuje funkcje platformy App Services Azure "dzienniki diagnostyczne" i "strumień dzienników".
* [Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/): rejestruje do monitora debugera za pomocą metody System. Diagnostics. Debug. WriteLine ().
* [Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/): rejestruje dziennik zdarzeń systemu Windows.
* [Microsoft. Extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/): obsługuje funkcję EventSource/odbiornika.
* [Microsoft. Extensions. Logging. TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/): rejestruje do odbiornika śledzenia przy użyciu `System.Diagnostics.TraceSource.TraceEvent()` .

Po zainstalowaniu odpowiednich pakietów aplikacja powinna utworzyć wystąpienie pojedyncze/globalne LoggerFactory. Na przykład przy użyciu rejestratora konsoli:

### <a name="version-3x"></a>[Wersja 3. x](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[Wersja 2. x](#tab/v2)

> [!NOTE]
> Poniższy przykład kodu używa `ConsoleLoggerProvider` konstruktora, który został przestarzały w wersji 2,2 i zastąpiony w 3,0. Można bezpiecznie zignorować i pominąć ostrzeżenia podczas korzystania z 2,2.

``` csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

***

To wystąpienie pojedyncze/globalne powinno następnie zostać zarejestrowane przy użyciu EF Core na `DbContextOptionsBuilder` . Na przykład:

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

> [!WARNING]
> Bardzo ważne jest, aby aplikacje nie utworzyły nowego wystąpienia ILoggerFactory dla każdego wystąpienia kontekstu. Wykonanie tej czynności spowoduje przeciek pamięci i niską wydajność.

## <a name="filtering-what-is-logged"></a>Filtrowanie co jest rejestrowane

Aplikacja może kontrolować, co jest rejestrowane przez skonfigurowanie filtru na ILoggerProvider. Na przykład:

### <a name="version-3x"></a>[Wersja 3. x](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContextWithFiltering.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[Wersja 2. x](#tab/v2)

> [!NOTE]
> Poniższy przykład kodu używa `ConsoleLoggerProvider` konstruktora, który został przestarzały w wersji 2,2 i zastąpiony w 3,0. Można bezpiecznie zignorować i pominąć ostrzeżenia podczas korzystania z 2,2.

``` csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[]
    {
        new ConsoleLoggerProvider((category, level)
            => category == DbLoggerCategory.Database.Command.Name
               && level == LogLevel.Information, true)
    });
```

***

W tym przykładzie dziennik jest filtrowany tylko w celu zwrócenia komunikatów:

* w kategorii "Microsoft. EntityFrameworkCore. Database. Command"
* na poziomie "informacje"

W przypadku EF Core kategorie rejestratorów są zdefiniowane w `DbLoggerCategory` klasie, aby ułatwić znajdowanie kategorii, ale te rozpoznają się z prostymi ciągami.

Więcej szczegółów dotyczących źródłowej infrastruktury rejestrowania można znaleźć w [dokumentacji rejestrowania ASP.NET Core](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).
