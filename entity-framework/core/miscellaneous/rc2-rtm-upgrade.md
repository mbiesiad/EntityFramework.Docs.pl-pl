---
title: Uaktualnianie z wersji EF Core 1,0 RC2 do wersji RTM — EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c3c1940b-136d-45d8-aa4f-cb5040f8980a
uid: core/miscellaneous/rc2-rtm-upgrade
ms.openlocfilehash: f496806ea6330c60cf43068882b7de839e18e383
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526774"
---
# <a name="upgrading-from-ef-core-10-rc2-to-rtm"></a><span data-ttu-id="06205-102">Uaktualnianie z wersji EF Core 1,0 RC2 do wersji RTM</span><span class="sxs-lookup"><span data-stu-id="06205-102">Upgrading from EF Core 1.0 RC2 to RTM</span></span>

<span data-ttu-id="06205-103">Ten artykuł zawiera wskazówki dotyczące przesuwania aplikacji skompilowanej z pakietami RC2 do 1.0.0 RTM.</span><span class="sxs-lookup"><span data-stu-id="06205-103">This article provides guidance for moving an application built with the RC2 packages to 1.0.0 RTM.</span></span>

## <a name="package-versions"></a><span data-ttu-id="06205-104">Wersje pakietu</span><span class="sxs-lookup"><span data-stu-id="06205-104">Package Versions</span></span>

<span data-ttu-id="06205-105">Nazwy pakietów najwyższego poziomu, które zazwyczaj instalują się do aplikacji, nie zmieniają się między RC2 i RTM.</span><span class="sxs-lookup"><span data-stu-id="06205-105">The names of the top level packages that you would typically install into an application did not change between RC2 and RTM.</span></span>

<span data-ttu-id="06205-106">**Należy uaktualnić zainstalowane pakiety do wersji RTM:**</span><span class="sxs-lookup"><span data-stu-id="06205-106">**You need to upgrade the installed packages to the RTM versions:**</span></span>

* <span data-ttu-id="06205-107">Pakiety środowiska uruchomieniowego (na przykład `Microsoft.EntityFrameworkCore.SqlServer` ) zmieniły `1.0.0-rc2-final` się z na `1.0.0` .</span><span class="sxs-lookup"><span data-stu-id="06205-107">Runtime packages (for example, `Microsoft.EntityFrameworkCore.SqlServer`) changed from `1.0.0-rc2-final` to `1.0.0`.</span></span>

* <span data-ttu-id="06205-108">`Microsoft.EntityFrameworkCore.Tools`Pakiet został zmieniony z `1.0.0-preview1-final` na `1.0.0-preview2-final` .</span><span class="sxs-lookup"><span data-stu-id="06205-108">The `Microsoft.EntityFrameworkCore.Tools` package changed from `1.0.0-preview1-final` to `1.0.0-preview2-final`.</span></span> <span data-ttu-id="06205-109">Należy zauważyć, że narzędzia nadal są w wersji wstępnej.</span><span class="sxs-lookup"><span data-stu-id="06205-109">Note that tooling is still pre-release.</span></span>

## <a name="existing-migrations-may-need-maxlength-added"></a><span data-ttu-id="06205-110">Istniejące migracje mogą wymagać dodania maxLength</span><span class="sxs-lookup"><span data-stu-id="06205-110">Existing migrations may need maxLength added</span></span>

<span data-ttu-id="06205-111">W RC2, definicja kolumny w migracji wygląda jak `table.Column<string>(nullable: true)` i długość kolumny została wyszukana w niektórych metadanych przechowywanych w kodzie związanym z migracją.</span><span class="sxs-lookup"><span data-stu-id="06205-111">In RC2, the column definition in a migration looked like `table.Column<string>(nullable: true)` and the length of the column was looked up in some metadata we store in the code behind the migration.</span></span> <span data-ttu-id="06205-112">W wersji RTM, długość jest teraz uwzględniona w kodzie szkieletowym `table.Column<string>(maxLength: 450, nullable: true)` .</span><span class="sxs-lookup"><span data-stu-id="06205-112">In RTM, the length is now included in the scaffolded code `table.Column<string>(maxLength: 450, nullable: true)`.</span></span>

<span data-ttu-id="06205-113">Wszystkie istniejące migracje, które były szkieletem przed użyciem wersji RTM, nie będą miały `maxLength` określonego argumentu.</span><span class="sxs-lookup"><span data-stu-id="06205-113">Any existing migrations that were scaffolded prior to using RTM will not have the `maxLength` argument specified.</span></span> <span data-ttu-id="06205-114">Oznacza to, że maksymalna długość obsługiwana przez bazę danych ( `nvarchar(max)` w SQL Server).</span><span class="sxs-lookup"><span data-stu-id="06205-114">This means the maximum length supported by the database will be used (`nvarchar(max)` on SQL Server).</span></span> <span data-ttu-id="06205-115">Może to być konieczne w przypadku niektórych kolumn, ale kolumny, które są częścią klucza, klucza obcego lub indeksu, muszą zostać zaktualizowane, aby zawierały maksymalną długość.</span><span class="sxs-lookup"><span data-stu-id="06205-115">This may be fine for some columns, but columns that are part of a key, foreign key, or index need to be updated to include a maximum length.</span></span> <span data-ttu-id="06205-116">Zgodnie z Konwencją 450 jest maksymalną długość używaną dla kluczy, kluczy obcych i indeksowanych kolumn.</span><span class="sxs-lookup"><span data-stu-id="06205-116">By convention, 450 is the maximum length used for keys, foreign keys, and indexed columns.</span></span> <span data-ttu-id="06205-117">Jeśli w modelu określono jawnie długość, należy zamiast tego użyć tej długości.</span><span class="sxs-lookup"><span data-stu-id="06205-117">If you have explicitly configured a length in the model, then you should use that length instead.</span></span>

### <a name="aspnet-identity"></a><span data-ttu-id="06205-118">ASP.NET Identity</span><span class="sxs-lookup"><span data-stu-id="06205-118">ASP.NET Identity</span></span>

<span data-ttu-id="06205-119">Ta zmiana wpływa na projekty, które używają ASP.NET Identity i zostały utworzone na podstawie szablonu projektu sprzed-RTM.</span><span class="sxs-lookup"><span data-stu-id="06205-119">This change impacts projects that use ASP.NET Identity and were created from a pre-RTM project template.</span></span> <span data-ttu-id="06205-120">Szablon projektu zawiera migrację używaną do utworzenia bazy danych.</span><span class="sxs-lookup"><span data-stu-id="06205-120">The project template includes a migration used to create the database.</span></span> <span data-ttu-id="06205-121">Tę migrację należy edytować, aby określić maksymalną długość `256` dla następujących kolumn.</span><span class="sxs-lookup"><span data-stu-id="06205-121">This migration must be edited to specify a maximum length of `256` for the following columns.</span></span>

* <span data-ttu-id="06205-122">**AspNetRoles**</span><span class="sxs-lookup"><span data-stu-id="06205-122">**AspNetRoles**</span></span>
  * <span data-ttu-id="06205-123">Nazwa</span><span class="sxs-lookup"><span data-stu-id="06205-123">Name</span></span>
  * <span data-ttu-id="06205-124">NormalizedName</span><span class="sxs-lookup"><span data-stu-id="06205-124">NormalizedName</span></span>
* <span data-ttu-id="06205-125">**AspNetUsers**</span><span class="sxs-lookup"><span data-stu-id="06205-125">**AspNetUsers**</span></span>
  * <span data-ttu-id="06205-126">E-mail</span><span class="sxs-lookup"><span data-stu-id="06205-126">Email</span></span>
  * <span data-ttu-id="06205-127">NormalizedEmail</span><span class="sxs-lookup"><span data-stu-id="06205-127">NormalizedEmail</span></span>
  * <span data-ttu-id="06205-128">NormalizedUserName</span><span class="sxs-lookup"><span data-stu-id="06205-128">NormalizedUserName</span></span>
  * <span data-ttu-id="06205-129">Nazwa użytkownika</span><span class="sxs-lookup"><span data-stu-id="06205-129">UserName</span></span>

<span data-ttu-id="06205-130">Niewprowadzenie tej zmiany spowoduje, że po zastosowaniu początkowej migracji do bazy danych wystąpi następujący wyjątek.</span><span class="sxs-lookup"><span data-stu-id="06205-130">Failure to make this change will result in the following exception when the initial migration is applied to a database.</span></span>

``` Console
System.Data.SqlClient.SqlException (0x80131904): Column 'NormalizedName' in table 'AspNetRoles' is of a type that is invalid for use as a key column in an index.
```

## <a name="net-core-remove-imports-in-projectjson"></a><span data-ttu-id="06205-131">.NET Core: usuwanie "Imports" w project.jsna</span><span class="sxs-lookup"><span data-stu-id="06205-131">.NET Core: Remove "imports" in project.json</span></span>

<span data-ttu-id="06205-132">Jeśli celem jest program .NET Core z RC2, należy dodać `imports` do project.jsw ramach tymczasowego obejścia niektórych EF Core zależności, które nie obsługują .NET Standard.</span><span class="sxs-lookup"><span data-stu-id="06205-132">If you were targeting .NET Core with RC2, you needed to add `imports` to project.json as a temporary workaround for some of EF Core's dependencies not supporting .NET Standard.</span></span> <span data-ttu-id="06205-133">Teraz można je usunąć.</span><span class="sxs-lookup"><span data-stu-id="06205-133">These can now be removed.</span></span>

``` json
{
  "frameworks": {
    "netcoreapp1.0": {
      "imports": ["dnxcore50", "portable-net451+win8"]
    }
  }
}
```

> [!NOTE]  
> <span data-ttu-id="06205-134">Począwszy od wersji 1,0 RTM, [zestaw .NET Core SDK](https://www.microsoft.com/net/download/core) nie obsługuje już `project.json` ani nie opracowują aplikacji platformy .NET Core przy użyciu programu Visual Studio 2015.</span><span class="sxs-lookup"><span data-stu-id="06205-134">As of version 1.0 RTM, the [.NET Core SDK](https://www.microsoft.com/net/download/core) no longer supports `project.json` or developing .NET Core applications using Visual Studio 2015.</span></span> <span data-ttu-id="06205-135">Zalecamy [Migrowanie z project.jsdo csproj](/dotnet/articles/core/migration/).</span><span class="sxs-lookup"><span data-stu-id="06205-135">We recommend you [migrate from project.json to csproj](/dotnet/articles/core/migration/).</span></span> <span data-ttu-id="06205-136">Jeśli używasz programu Visual Studio, zalecamy przeprowadzenie uaktualnienia do [programu Visual studio 2017](https://www.visualstudio.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="06205-136">If you are using Visual Studio, we recommend you upgrade to [Visual Studio 2017](https://www.visualstudio.com/downloads/).</span></span>

## <a name="uwp-add-binding-redirects"></a><span data-ttu-id="06205-137">PLATFORMY UWP: Dodawanie przekierowań powiązań</span><span class="sxs-lookup"><span data-stu-id="06205-137">UWP: Add binding redirects</span></span>

<span data-ttu-id="06205-138">Próba uruchomienia poleceń EF w projektach platforma uniwersalna systemu Windows (platformy UWP) skutkuje następującym błędem:</span><span class="sxs-lookup"><span data-stu-id="06205-138">Attempting to run EF commands on Universal Windows Platform (UWP) projects results in the following error:</span></span>

```output
System.IO.FileLoadException: Could not load file or assembly 'System.IO.FileSystem.Primitives, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies. The located assembly's manifest definition does not match the assembly reference.
```

<span data-ttu-id="06205-139">Należy ręcznie dodać przekierowania powiązań do projektu platformy UWP.</span><span class="sxs-lookup"><span data-stu-id="06205-139">You need to manually add binding redirects to the UWP project.</span></span> <span data-ttu-id="06205-140">Utwórz plik o nazwie `App.config` w folderze głównym projektu i dodaj przekierowania do poprawnych wersji zestawu.</span><span class="sxs-lookup"><span data-stu-id="06205-140">Create a file named `App.config` in the project root folder and add redirects to the correct assembly versions.</span></span>

```xml
<configuration>
 <runtime>
   <assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
     <dependentAssembly>
       <assemblyIdentity name="System.IO.FileSystem.Primitives"
                         publicKeyToken="b03f5f7f11d50a3a"
                         culture="neutral" />
       <bindingRedirect oldVersion="4.0.0.0"
                        newVersion="4.0.1.0"/>
     </dependentAssembly>
     <dependentAssembly>
       <assemblyIdentity name="System.Threading.Overlapped"
                         publicKeyToken="b03f5f7f11d50a3a"
                         culture="neutral" />
       <bindingRedirect oldVersion="4.0.0.0"
                        newVersion="4.0.1.0"/>
     </dependentAssembly>
   </assemblyBinding>
 </runtime>
</configuration>
```
