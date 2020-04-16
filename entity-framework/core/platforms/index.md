---
title: Obsługiwane implementacje platformy .NET — EF Core
author: bricelam
ms.date: 03/03/2020
uid: core/platforms/index
ms.openlocfilehash: 2738645596a325a1a43fd5b2164e370dd8c5893e
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434126"
---
# <a name="net-implementations-supported-by-ef-core"></a>Implementacje .NET obsługiwane przez EF Core

Chcemy, aby EF Core był dostępny dla deweloperów we wszystkich nowoczesnych implementacjach platformy .NET i nadal pracujemy nad tym celem. Podczas gdy wsparcie EF Core w programie .NET Core jest objęte automatycznymi testami i wiele aplikacji, o których wiadomo, że używa go pomyślnie, Mono, Xamarin i platformy uniwersalnej systemu zbytu mają pewne problemy.

## <a name="overview"></a>Omówienie

Poniższa tabela zawiera wskazówki dla każdej implementacji platformy .NET:

| EF Core                       | 2.1 i 3.1 |
|:------------------------------|:------------|
| .NET Standard                 | 2.0         |
| .NET Core                     | 2.0         |
| .NET Framework<sup>(1)</sup>  | 4.7.2       |
| Mono                          | 5.4         |
| Xamarin.iOS<sup>(2)</sup>     | 10.14       |
| Xamarin.Android<sup>(2)</sup> | 8.0         |
| UwP<sup>(3)</sup>             | 10.0.16299  |
| Jedność<sup>(4)</sup>           | 2018.1      |

<sup>(1)</sup> Zobacz sekcję [.NET Framework](#net-framework) poniżej.

<sup>(2)</sup> Istnieją problemy i znane ograniczenia z xamarin, które mogą uniemożliwić niektóre aplikacje opracowane przy użyciu EF Core działa poprawnie. Sprawdź listę [aktywnych problemów](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-xamarin) pod kątem obejść.

<sup>(3)</sup> EF Core 2.0.1 i nowsze zalecane. Zainstaluj [pakiet platformy .NET Core UWP 6.x](https://www.nuget.org/packages/Microsoft.NETCore.UniversalWindowsPlatform/). Zobacz sekcję [Uniwersalna platforma systemu Windows](#universal-windows-platform) w tym artykule.

<sup>(4)</sup> Istnieją problemy i znane ograniczenia jedności. Sprawdź listę [aktywnych problemów](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-unity).

## <a name="net-framework"></a>.NET Framework

Aplikacje przeznaczone dla programu .NET Framework mogą wymagać zmian w celu pracy z bibliotekami .NET Standard:

Edytuj plik projektu i upewnij się, że w początkowej grupie właściwości pojawi się następujący wpis:

``` xml
<AutoGenerateBindingRedirects>true</AutoGenerateBindingRedirects>
```

W przypadku projektów testowych upewnij się również, że dostępny jest następujący wpis:

``` xml
<GenerateBindingRedirectsOutputType>true</GenerateBindingRedirectsOutputType>
```

Jeśli chcesz użyć starszej wersji programu Visual Studio, upewnij się, że [uaktualnisz klienta NuGet do wersji 3.6.0,](https://www.nuget.org/downloads) aby pracować z bibliotekami .NET Standard 2.0.

Zalecamy również migrację z pakietu NuGet.config do PackageReference, jeśli to możliwe. Dodaj następującą właściwość do pliku projektu:

``` xml
<RestoreProjectStyle>PackageReference</RestoreProjectStyle>
```

## <a name="universal-windows-platform"></a>Platforma uniwersalna systemu Windows

Starsze wersje platformy uniwersalnej systemu Windows i platformy uniwersalnej systemu Windows firmy .NET miały wiele problemów ze zgodnością, szczególnie w przypadku aplikacji skompilowanych z natywnym pasmem narzędzi .NET. Nowa wersja platformy uniwersalnej systemu Windows .NET dodaje obsługę platformy .NET Standard 2.0 i zawiera program .NET Native 2.0, który rozwiązuje większość wcześniej zgłoszonych problemów ze zgodnością. EF Core 2.0.1 został przetestowany dokładniej za pomocą platformy uniwersalnej systemu uniwersalnego systemu, ale testowanie nie jest zautomatyzowane.

W przypadku korzystania z EF Core na platformie uniwersalnej systemu wyurzcie na platformy uniwersalnej systemu:

* Aby zoptymalizować wydajność kwerendy, należy unikać typów anonimowych w zapytaniach LINQ. Wdrażanie aplikacji platformy uniwersalnej systemu i platformy uniwersalnej w sklepie z aplikacjami wymaga skompilowania aplikacji z programem .NET Native. Zapytania z typami anonimowymi mają gorszą wydajność na natywnej .NET.

* Aby `SaveChanges()` zoptymalizować wydajność, należy użyć [ChangeTrackingStrategy.ChangingAndChangedNotifications](/dotnet/api/microsoft.entityframeworkcore.changetrackingstrategy) i zaimplementować [INotifyPropertyChanged](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanged.aspx), [INotifyPropertyChanging](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanging.aspx)i [INotifyCollectionZmieniane](https://msdn.microsoft.com/library/system.collections.specialized.inotifycollectionchanged.aspx) w typach jednostek.

## <a name="report-issues"></a>Zgłaszanie problemów

Dla każdej kombinacji, która nie działa zgodnie z oczekiwaniami, zachęcamy do tworzenia nowych problemów na [trackerze problemów EF Core](https://github.com/aspnet/entityframeworkcore/issues/new). W przypadku problemów specyficznych dla platformy Xamarin użyj modułu śledzącego problemy dla [platformy Xamarin.Android](https://github.com/xamarin/xamarin-android/issues/new) lub [Xamarin.iOS](https://github.com/xamarin/xamarin-macios/issues/new).
