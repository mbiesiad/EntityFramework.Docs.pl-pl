---
title: Obsługiwane implementacje .NET — EF Core
author: bricelam
ms.date: 06/26/2020
uid: core/platforms/index
ms.openlocfilehash: f7e7a4f7993061aa46c1f9c648169158f7a35193
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238050"
---
# <a name="net-implementations-supported-by-ef-core"></a>Implementacje platformy .NET obsługiwane przez EF Core

Chcemy, aby EF Core były dostępne dla deweloperów we wszystkich nowoczesnych implementacjach platformy .NET i nadal pracujemy nad tym celem. Mimo że obsługa EF Core na platformie .NET Core jest objęta testowaniem automatycznym i wiele aplikacji, o których wiadomo, że ich używanie powiodło się, rozwiązanie mono, Xamarin i platformy UWP zawierają pewne problemy.

## <a name="overview"></a>Omówienie

W poniższej tabeli przedstawiono wskazówki dotyczące każdej implementacji platformy .NET:

| EF Core                       | 2,1 i 3,1 | 5.0             |
|:------------------------------|:------------|:----------------|
| .NET Standard                 | 2.0         | 2.1             |
| .NET Core                     | 2.0         | 3.0             |
| .NET Framework<sup>(1)</sup>  | 4.7.2       | (nieobsługiwane) |
| Mono                          | 5.4         | 6.4             |
| Xamarin. iOS<sup>(2)</sup>     | 10,14       | 12,16           |
| Xamarin. Mac<sup>(2)</sup>     | 3,8         | 5,16            |
| Xamarin. Android<sup>(2)</sup> | 8.0         | 10,0            |
| PLATFORMY UWP<sup>(3)</sup>             | 10.0.16299  | TBD             |
| Unity<sup>(4)</sup>           | 2018,1      | TBD             |

<sup>(1)</sup> zapoznaj się z sekcją [.NET Framework](#net-framework) poniżej.

<sup>(2)</sup> istnieją problemy i znane ograniczenia dotyczące platformy Xamarin, które mogą uniemożliwić poprawne działanie niektórych aplikacji korzystających z programu EF Core. Zapoznaj się z listą [aktywnych problemów](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-xamarin) dotyczących obejść.

<sup>(3)</sup> EF Core 2.0.1 i nowsze zalecane. Zainstaluj [pakiet .NET Core platformy UWP 6. x](https://www.nuget.org/packages/Microsoft.NETCore.UniversalWindowsPlatform/). Zapoznaj się z sekcją [platforma uniwersalna systemu Windows](#universal-windows-platform) tego artykułu.

<sup>(4)</sup> występują problemy i znane ograniczenia dotyczące aparatu Unity. Sprawdź listę [aktywnych problemów](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-unity).

## <a name="net-framework"></a>.NET Framework

Aplikacje, które są przeznaczone dla .NET Framework mogą potrzebować zmian do pracy z bibliotekami .NET Standard:

Edytuj plik projektu i upewnij się, że w grupie Właściwości początkowej pojawia się następujący wpis:

``` xml
<AutoGenerateBindingRedirects>true</AutoGenerateBindingRedirects>
```

W przypadku projektów testowych upewnij się również, że jest obecny następujący wpis:

``` xml
<GenerateBindingRedirectsOutputType>true</GenerateBindingRedirectsOutputType>
```

Jeśli chcesz użyć starszej wersji programu Visual Studio, upewnij się, że [uaktualniasz klienta NuGet do wersji 3.6.0](https://www.nuget.org/downloads) , aby współpracował z bibliotekami .NET Standard 2,0.

Zalecamy również Migrowanie z packages.config NuGet do PackageReference, jeśli jest to możliwe. Dodaj następującą właściwość do pliku projektu:

``` xml
<RestoreProjectStyle>PackageReference</RestoreProjectStyle>
```

## <a name="universal-windows-platform"></a>Platforma uniwersalna systemu Windows

We wcześniejszych wersjach EF Core i .NET platformy UWP istniały liczne problemy ze zgodnością, szczególnie w przypadku aplikacji skompilowanych za pomocą .NET Native łańcucha narzędzi. Nowa wersja programu .NET platformy UWP dodaje obsługę .NET Standard 2,0 i zawiera .NET Native 2,0, które naprawiają większość raportowanych wcześniej problemów ze zgodnością. EF Core 2.0.1 został dokładnie przetestowany przy użyciu platformy UWP, ale testowanie nie jest zautomatyzowane.

W przypadku korzystania z EF Core w platformy UWP:

* Aby zoptymalizować wydajność zapytań, unikaj typów anonimowych w zapytaniach LINQ. Wdrożenie aplikacji platformy UWP w sklepie App Store wymaga, aby aplikacja była skompilowana przy użyciu .NET Native. Zapytania z typami anonimowymi mają gorszą wydajność na .NET Native.

* Aby zoptymalizować `SaveChanges()` wydajność, należy użyć [ChangeTrackingStrategy. ChangingAndChangedNotifications](/dotnet/api/microsoft.entityframeworkcore.changetrackingstrategy) i zaimplementować [INotifyPropertyChanged](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanged.aspx), [INotifyPropertyChanging](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanging.aspx)i [INotifyCollectionChanged](https://msdn.microsoft.com/library/system.collections.specialized.inotifycollectionchanged.aspx) w typach jednostek.

## <a name="report-issues"></a>Zgłaszanie problemów

W przypadku dowolnej kombinacji, która nie działa zgodnie z oczekiwaniami, zachęcamy do tworzenia nowych problemów w ramach [EF Core śledzenia problemów](https://github.com/aspnet/entityframeworkcore/issues/new). W przypadku problemów specyficznych dla platformy Xamarin Użyj narzędzia do śledzenia problemów dla platformy [Xamarin. Android](https://github.com/xamarin/xamarin-android/issues/new) lub [Xamarin. iOS](https://github.com/xamarin/xamarin-macios/issues/new).
