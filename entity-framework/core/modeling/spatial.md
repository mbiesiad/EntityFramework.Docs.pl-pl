---
title: Dane przestrzenne — EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/01/2018
ms.assetid: 2BDE29FC-4161-41A0-841E-69F51CCD9341
uid: core/modeling/spatial
ms.openlocfilehash: cf488c6b7d94ca19018efe1c23ff410fe7eb594b
ms.sourcegitcommit: 81c53ac43d8f15b900f117294ec71dc49fe028fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/16/2018
ms.locfileid: "51817913"
---
# <a name="spatial-data"></a><span data-ttu-id="38623-102">Dane przestrzenne</span><span class="sxs-lookup"><span data-stu-id="38623-102">Spatial Data</span></span>

> [!NOTE]
> <span data-ttu-id="38623-103">Ta funkcja jest nowa w programie EF Core 2.2.</span><span class="sxs-lookup"><span data-stu-id="38623-103">This feature is new in EF Core 2.2.</span></span>

<span data-ttu-id="38623-104">Dane przestrzenne reprezentuje lokalizację fizyczną i kształt obiektów.</span><span class="sxs-lookup"><span data-stu-id="38623-104">Spatial data represents the physical location and the shape of objects.</span></span> <span data-ttu-id="38623-105">Wiele baz danych zapewniają obsługę tego rodzaju danych, więc może być indeksowana i zapytania wraz z innymi danymi.</span><span class="sxs-lookup"><span data-stu-id="38623-105">Many databases provide support for this type of data so it can be indexed and queried alongside other data.</span></span> <span data-ttu-id="38623-106">Typowe scenariusze obejmują tworzenie zapytań dotyczących obiektów w określonej odległości od lokalizacji lub zaznaczając ten obiekt, którego obramowanie zawiera danej lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="38623-106">Common scenarios include querying for objects within a given distance from a location, or selecting the object whose border contains a given location.</span></span> <span data-ttu-id="38623-107">EF Core obsługuje Mapowanie typów danych przestrzennych przy użyciu [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) przestrzenne biblioteki.</span><span class="sxs-lookup"><span data-stu-id="38623-107">EF Core supports mapping to spatial data types using the [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) spatial library.</span></span>

## <a name="installing"></a><span data-ttu-id="38623-108">Instalowanie programu</span><span class="sxs-lookup"><span data-stu-id="38623-108">Installing</span></span>

<span data-ttu-id="38623-109">Aby można było używać danych przestrzennych z programem EF Core, musisz zainstalować odpowiedni pakiet NuGet pomocniczych.</span><span class="sxs-lookup"><span data-stu-id="38623-109">In order to use spatial data with EF Core, you need to install the appropriate supporting NuGet package.</span></span> <span data-ttu-id="38623-110">Pakiet, który należy zainstalować zależy od dostawcy, którego używasz.</span><span class="sxs-lookup"><span data-stu-id="38623-110">Which package you need to install depends on the provider you're using.</span></span>

<span data-ttu-id="38623-111">EF Core dostawcy</span><span class="sxs-lookup"><span data-stu-id="38623-111">EF Core Provider</span></span>                        | <span data-ttu-id="38623-112">Przestrzenne pakietu NuGet</span><span class="sxs-lookup"><span data-stu-id="38623-112">Spatial NuGet Package</span></span>
--------------------------------------- | ---------------------
<span data-ttu-id="38623-113">Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="38623-113">Microsoft.EntityFrameworkCore.SqlServer</span></span> | [<span data-ttu-id="38623-114">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="38623-114">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
<span data-ttu-id="38623-115">Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="38623-115">Microsoft.EntityFrameworkCore.Sqlite</span></span>    | [<span data-ttu-id="38623-116">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="38623-116">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
<span data-ttu-id="38623-117">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="38623-117">Microsoft.EntityFrameworkCore.InMemory</span></span>  | [<span data-ttu-id="38623-118">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="38623-118">NetTopologySuite</span></span>](https://www.nuget.org/packages/NetTopologySuite)
<span data-ttu-id="38623-119">Npgsql.EntityFrameworkCore.PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="38623-119">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>   | [<span data-ttu-id="38623-120">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="38623-120">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)

## <a name="reverse-engineering"></a><span data-ttu-id="38623-121">Odtwarzanie</span><span class="sxs-lookup"><span data-stu-id="38623-121">Reverse engineering</span></span>

<span data-ttu-id="38623-122">Przestrzenne NuGet również pakietów Włącz [odtwarzanie](../managing-schemas/scaffolding.md) modeli przy użyciu właściwości przestrzennych, ale trzeba zainstalować pakiet ***przed*** systemem `Scaffold-DbContext` lub `dotnet ef dbcontext scaffold`.</span><span class="sxs-lookup"><span data-stu-id="38623-122">The spatial NuGet packages also enable [reverse engineering](../managing-schemas/scaffolding.md) models with spatial properties, but you need to install the package ***before*** running `Scaffold-DbContext` or `dotnet ef dbcontext scaffold`.</span></span> <span data-ttu-id="38623-123">Jeśli nie istnieje, otrzymasz ostrzeżenia dotyczące nie możesz znaleźć mapowania typów kolumn i kolumny zostaną pominięte.</span><span class="sxs-lookup"><span data-stu-id="38623-123">If you don't, you'll receive warnings about not finding type mappings for the columns and the columns will be skipped.</span></span>

## <a name="nettopologysuite-nts"></a><span data-ttu-id="38623-124">NetTopologySuite (NTS)</span><span class="sxs-lookup"><span data-stu-id="38623-124">NetTopologySuite (NTS)</span></span>

<span data-ttu-id="38623-125">NetTopologySuite jest biblioteką przestrzennych dla platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="38623-125">NetTopologySuite is a spatial library for .NET.</span></span> <span data-ttu-id="38623-126">EF Core umożliwia mapowanie danych przestrzennych typów w bazie danych przy użyciu typów nkty przerwania w modelu.</span><span class="sxs-lookup"><span data-stu-id="38623-126">EF Core enables mapping to spatial data types in the database by using NTS types in your model.</span></span>

<span data-ttu-id="38623-127">Aby włączyć mapowanie typów przestrzennych za pośrednictwem nkty przerwania, należy wywołać metodę UseNetTopologySuite dla dostawcy typu DbContext opcje konstruktora.</span><span class="sxs-lookup"><span data-stu-id="38623-127">To enable mapping to spatial types via NTS, call the UseNetTopologySuite method on the provider's DbContext options builder.</span></span> <span data-ttu-id="38623-128">Na przykład z programem SQL Server możesz wywołać go następująco.</span><span class="sxs-lookup"><span data-stu-id="38623-128">For example, with SQL Server you'd call it like this.</span></span>

``` csharp
optionsBuilder.UseSqlServer(
    @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=WideWorldImporters",
    x => x.UseNetTopologySuite());
```

<span data-ttu-id="38623-129">Istnieją różne typy danych przestrzennych.</span><span class="sxs-lookup"><span data-stu-id="38623-129">There are several spatial data types.</span></span> <span data-ttu-id="38623-130">Jakiego typu, możesz użyć zależy od typów kształtami, które chcesz zezwolić.</span><span class="sxs-lookup"><span data-stu-id="38623-130">Which type you use depends on the types of shapes you want to allow.</span></span> <span data-ttu-id="38623-131">Oto hierarchii typów nkty przerwania, które służy do właściwości w modelu.</span><span class="sxs-lookup"><span data-stu-id="38623-131">Here is the hierarchy of NTS types that you can use for properties in your model.</span></span> <span data-ttu-id="38623-132">Są one umieszczone w ramach `NetTopologySuite.Geometries` przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="38623-132">They're located within the `NetTopologySuite.Geometries` namespace.</span></span> <span data-ttu-id="38623-133">Odpowiednich interfejsów w pakiecie GeoAPI (`GeoAPI.Geometries` przestrzeni nazw) może również służyć.</span><span class="sxs-lookup"><span data-stu-id="38623-133">Corresponding interfaces in the GeoAPI package (`GeoAPI.Geometries` namespace) can also be used.</span></span>

* <span data-ttu-id="38623-134">Geometrii</span><span class="sxs-lookup"><span data-stu-id="38623-134">Geometry</span></span>
  * <span data-ttu-id="38623-135">Punkt</span><span class="sxs-lookup"><span data-stu-id="38623-135">Point</span></span>
  * <span data-ttu-id="38623-136">LineString</span><span class="sxs-lookup"><span data-stu-id="38623-136">LineString</span></span>
  * <span data-ttu-id="38623-137">Wielokąt</span><span class="sxs-lookup"><span data-stu-id="38623-137">Polygon</span></span>
  * <span data-ttu-id="38623-138">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="38623-138">GeometryCollection</span></span>
    * <span data-ttu-id="38623-139">MultiPoint</span><span class="sxs-lookup"><span data-stu-id="38623-139">MultiPoint</span></span>
    * <span data-ttu-id="38623-140">MultiLineString</span><span class="sxs-lookup"><span data-stu-id="38623-140">MultiLineString</span></span>
    * <span data-ttu-id="38623-141">MultiPolygon</span><span class="sxs-lookup"><span data-stu-id="38623-141">MultiPolygon</span></span>

> [!WARNING]
> <span data-ttu-id="38623-142">CircularString, CompoundCurve i CurePolygon nie są obsługiwane przez nkty przerwania.</span><span class="sxs-lookup"><span data-stu-id="38623-142">CircularString, CompoundCurve, and CurePolygon aren't supported by NTS.</span></span>

<span data-ttu-id="38623-143">Przy użyciu podstawowego typu Geometria umożliwia dowolny typ kształtu do określonych we właściwości.</span><span class="sxs-lookup"><span data-stu-id="38623-143">Using the base Geometry type allows any type of shape to be specified by the property.</span></span>

<span data-ttu-id="38623-144">Następujących klas jednostek może być używany do mapowania z tabelami w [Wide World Importers przykładowej bazy danych](http://go.microsoft.com/fwlink/?LinkID=800630).</span><span class="sxs-lookup"><span data-stu-id="38623-144">The following entity classes could be used to map to tables in the [Wide World Importers sample database](http://go.microsoft.com/fwlink/?LinkID=800630).</span></span>

``` csharp
[Table("Cities", Schema = "Application"))]
class City
{
    public int CityID { get; set; }

    public string CityName { get; set; }

    public IPoint Location { get; set; }
}

[Table("Countries", Schema = "Application"))]
class Country
{
    public int CountryID { get; set; }

    public string CountryName { get; set; }

    // Database includes both Polygon and MultiPolygon values
    public IGeometry Border { get; set; }
}
```

### <a name="creating-values"></a><span data-ttu-id="38623-145">Tworzenie wartości</span><span class="sxs-lookup"><span data-stu-id="38623-145">Creating values</span></span>

<span data-ttu-id="38623-146">Można używać konstruktorów do tworzenia obiektów geometrii; jednak nkty przerwania zaleca używanie fabrykę geometrii zamiast tego.</span><span class="sxs-lookup"><span data-stu-id="38623-146">You can use constructors to create geometry objects; however, NTS recommends using a geometry factory instead.</span></span> <span data-ttu-id="38623-147">To pozwala określić domyślną SRID (używane przez współrzędne system odwołania przestrzennego) i zapewnia kontrolę nad bardziej zaawansowanych np. model precyzji (używane podczas obliczeń) i sekwencji współrzędnych (określa, które rzędne — wymiarów i miary — są dostępne).</span><span class="sxs-lookup"><span data-stu-id="38623-147">This lets you specify a default SRID (the spatial reference system used by the coordinates) and gives you control over more advanced things like the precision model (used during calculations) and the coordinate sequence (determines which ordinates--dimensions and measures--are available).</span></span>

``` csharp
var geometryFactory = NtsGeometryServices.Instance.CreateGeometryFactory(srid: 4326);
var currentLocation = geometryFactory.CreatePoint(-122.121512, 47.6739882);
```

> [!NOTE]
> <span data-ttu-id="38623-148">4326 odnosi się do WGS 84 standard używany w GPS i innych systemach geograficznego.</span><span class="sxs-lookup"><span data-stu-id="38623-148">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

### <a name="longitude-and-latitude"></a><span data-ttu-id="38623-149">Długość i szerokość geograficzną</span><span class="sxs-lookup"><span data-stu-id="38623-149">Longitude and Latitude</span></span>

<span data-ttu-id="38623-150">Współrzędne nkty przerwania są na podstawie wartości X i Y.</span><span class="sxs-lookup"><span data-stu-id="38623-150">Coordinates in NTS are in terms of X and Y values.</span></span> <span data-ttu-id="38623-151">Do reprezentowania długości i szerokości geograficznej, na użytek X dla współrzędnych i Y szerokości geograficznej.</span><span class="sxs-lookup"><span data-stu-id="38623-151">To represent longitude and latitude, use X for longitude and Y for latitude.</span></span> <span data-ttu-id="38623-152">Należy zauważyć, że jest to **Wstecz** z `latitude, longitude` format, w którym zwykle widzieć te wartości.</span><span class="sxs-lookup"><span data-stu-id="38623-152">Note that this is **backwards** from the `latitude, longitude` format in which you typically see these values.</span></span>

### <a name="srid-ignored-during-client-operations"></a><span data-ttu-id="38623-153">SRID ignorowany podczas operacji klienta</span><span class="sxs-lookup"><span data-stu-id="38623-153">SRID Ignored during client operations</span></span>

<span data-ttu-id="38623-154">Nkty przerwania ignoruje wartości SRID podczas wykonywania operacji.</span><span class="sxs-lookup"><span data-stu-id="38623-154">NTS ignores SRID values during operations.</span></span> <span data-ttu-id="38623-155">Przyjęto założenie, współrzędnych planarnych.</span><span class="sxs-lookup"><span data-stu-id="38623-155">It assumes a planar coordinate system.</span></span> <span data-ttu-id="38623-156">Oznacza to, że w przypadku określenia współrzędnych pod względem długości i szerokości geograficznej, niektóre wartości takie jak odległości, długość i obszar będzie w stopniach, liczniki nie oceniane przez klienta.</span><span class="sxs-lookup"><span data-stu-id="38623-156">This means that if you specify coordinates in terms of longitude and latitude, some client-evaluated values like distance, length, and area will be in degrees, not meters.</span></span> <span data-ttu-id="38623-157">Dla bardziej zrozumiały wartości, należy najpierw projektu współrzędne miejsca inny układ współrzędnych przy użyciu biblioteki, takich jak [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) przed obliczeniem te wartości.</span><span class="sxs-lookup"><span data-stu-id="38623-157">For more meaningful values, you first need to project the coordinates to another coordinate system using a library like [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) before calculating these values.</span></span>

<span data-ttu-id="38623-158">W przypadku operacji serwera oceniane przez platformę EF Core przy użyciu programu SQL, jednostka wynik będzie określana przez bazę danych.</span><span class="sxs-lookup"><span data-stu-id="38623-158">If an operation is server-evaluated by EF Core via SQL, the result's unit will be determined by the database.</span></span>

<span data-ttu-id="38623-159">Oto przykład użycia ProjNet4GeoAPI, aby obliczyć odległość między dwoma miast.</span><span class="sxs-lookup"><span data-stu-id="38623-159">Here is an example of using ProjNet4GeoAPI to calculate the distance between two cities.</span></span>

``` csharp
static class GeometryExtensions
{
    static readonly IGeometryServices _geometryServices = NtsGeometryServices.Instance;
    static readonly ICoordinateSystemServices _coordinateSystemServices
        = new CoordinateSystemServices(
            new CoordinateSystemFactory(),
            new CoordinateTransformationFactory(),
            new Dictionary<int, string>
            {
                // Coordinate systems:

                // (3857 and 4326 included automatically)

                // This coordinate system covers the area of our data.
                // Different data requires a different coordinate system.
                [2855] =
                @"
                    PROJCS[""NAD83(HARN) / Washington North"",
                        GEOGCS[""NAD83(HARN)"",
                            DATUM[""NAD83_High_Accuracy_Regional_Network"",
                                SPHEROID[""GRS 1980"",6378137,298.257222101,
                                    AUTHORITY[""EPSG"",""7019""]],
                                AUTHORITY[""EPSG"",""6152""]],
                            PRIMEM[""Greenwich"",0,
                                AUTHORITY[""EPSG"",""8901""]],
                            UNIT[""degree"",0.01745329251994328,
                                AUTHORITY[""EPSG"",""9122""]],
                            AUTHORITY[""EPSG"",""4152""]],
                        PROJECTION[""Lambert_Conformal_Conic_2SP""],
                        PARAMETER[""standard_parallel_1"",48.73333333333333],
                        PARAMETER[""standard_parallel_2"",47.5],
                        PARAMETER[""latitude_of_origin"",47],
                        PARAMETER[""central_meridian"",-120.8333333333333],
                        PARAMETER[""false_easting"",500000],
                        PARAMETER[""false_northing"",0],
                        UNIT[""metre"",1,
                            AUTHORITY[""EPSG"",""9001""]],
                        AUTHORITY[""EPSG"",""2855""]]
                "
            });

    public static IGeometry ProjectTo(this IGeometry geometry, int srid)
    {
        var geometryFactory = _geometryServices.CreateGeometryFactory(srid);
        var transformation = _coordinateSystemServices.CreateTransformation(geometry.SRID, srid);

        return GeometryTransform.TransformGeometry(
            geometryFactory,
            geometry,
            transformation.MathTransform);
    }
}
```

``` csharp
var seattle = new Point(-122.333056, 47.609722) { SRID = 4326 };
var redmond = new Point(-122.123889, 47.669444) { SRID = 4326 };

var distance = seattle.ProjectTo(2855).Distance(redmond.ProjectTo(2855));
```

## <a name="querying-data"></a><span data-ttu-id="38623-160">Wykonanie zapytania o dane</span><span class="sxs-lookup"><span data-stu-id="38623-160">Querying Data</span></span>

<span data-ttu-id="38623-161">W programie LINQ nkty przerwania metody i właściwości dostępne jako funkcje bazy danych ma być tłumaczony na SQL.</span><span class="sxs-lookup"><span data-stu-id="38623-161">In LINQ, the NTS methods and properties available as database functions will be translated to SQL.</span></span> <span data-ttu-id="38623-162">Na przykład odległość i zawiera metody są tłumaczone w następujących zapytań.</span><span class="sxs-lookup"><span data-stu-id="38623-162">For example, the Distance and Contains methods are translated in the following queries.</span></span> <span data-ttu-id="38623-163">Tabeli na końcu tego artykułu przedstawiono składniki, które są obsługiwane przez różnych dostawców programu EF Core.</span><span class="sxs-lookup"><span data-stu-id="38623-163">The table at the end of this article shows which members are supported by various EF Core providers.</span></span>

``` csharp
var nearestCity = db.Cities
    .OrderBy(c => c.Location.Distance(currentLocation))
    .FirstOrDefault();

var currentCountry = db.Countries
    .FirstOrDefault(c => c.Border.Contains(currentLocation));
```

## <a name="sql-server"></a><span data-ttu-id="38623-164">SQL Server</span><span class="sxs-lookup"><span data-stu-id="38623-164">SQL Server</span></span>

<span data-ttu-id="38623-165">Jeśli używasz programu SQL Server, istnieją pewne dodatkowe rzeczy, na których należy wiedzieć.</span><span class="sxs-lookup"><span data-stu-id="38623-165">If you're using SQL Server, there are some additional things you should be aware of.</span></span>

### <a name="geography-or-geometry"></a><span data-ttu-id="38623-166">Położenia geograficznego lub geometrii</span><span class="sxs-lookup"><span data-stu-id="38623-166">Geography or geometry</span></span>

<span data-ttu-id="38623-167">Domyślnie przestrzenne właściwości są mapowane na `geography` kolumny w programie SQL Server.</span><span class="sxs-lookup"><span data-stu-id="38623-167">By default, spatial properties are mapped to `geography` columns in SQL Server.</span></span> <span data-ttu-id="38623-168">Aby użyć `geometry`, [skonfigurować typ kolumny](xref:core/modeling/relational/data-types) w modelu.</span><span class="sxs-lookup"><span data-stu-id="38623-168">To use `geometry`, [configure the column type](xref:core/modeling/relational/data-types) in your model.</span></span>

### <a name="geography-polygon-rings"></a><span data-ttu-id="38623-169">Pierścienie wielokąta lokalizacji geograficznej</span><span class="sxs-lookup"><span data-stu-id="38623-169">Geography polygon rings</span></span>

<span data-ttu-id="38623-170">Korzystając z `geography` typ kolumny, programu SQL Server nakłada dodatkowe wymagania dotyczące pierścienia zewnętrznego (lub powłoki) i posługiwanie się nimi pierścieniami (lub otworów).</span><span class="sxs-lookup"><span data-stu-id="38623-170">When using the `geography` column type, SQL Server imposes additional requirements on the exterior ring (or shell) and interior rings (or holes).</span></span> <span data-ttu-id="38623-171">Pierścieniem musi być zorientowana zegara i wewnętrznych pierścieni zgodnie ze wskazówkami zegara.</span><span class="sxs-lookup"><span data-stu-id="38623-171">The exterior ring must be oriented counterclockwise and the interior rings clockwise.</span></span> <span data-ttu-id="38623-172">NTFS sprawdza poprawność to przed wysłaniem wartości w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="38623-172">NTS validates this before sending values to the database.</span></span>

### <a name="fullglobe"></a><span data-ttu-id="38623-173">FullGlobe</span><span class="sxs-lookup"><span data-stu-id="38623-173">FullGlobe</span></span>

<span data-ttu-id="38623-174">Program SQL Server ma typ geometrii niestandardowych do reprezentowania pełną całym świecie, korzystając z `geography` typ kolumny.</span><span class="sxs-lookup"><span data-stu-id="38623-174">SQL Server has a non-standard geometry type to represent the full globe when using the `geography` column type.</span></span> <span data-ttu-id="38623-175">Ma również sposób reprezentacji wielokątów oparte na świecie pełny (bez pierścieniem).</span><span class="sxs-lookup"><span data-stu-id="38623-175">It also has a way to represent polygons based on the full globe (without an exterior ring).</span></span> <span data-ttu-id="38623-176">Oba te są obsługiwane przez nkty przerwania.</span><span class="sxs-lookup"><span data-stu-id="38623-176">Neither of these are supported by NTS.</span></span>

> [!WARNING]
> <span data-ttu-id="38623-177">FullGlobe i wielokątów na jego podstawie nie są obsługiwane przez nkty przerwania.</span><span class="sxs-lookup"><span data-stu-id="38623-177">FullGlobe and polygons based on it aren't supported by NTS.</span></span>

## <a name="sqlite"></a><span data-ttu-id="38623-178">Bazy danych SQLite</span><span class="sxs-lookup"><span data-stu-id="38623-178">SQLite</span></span>

<span data-ttu-id="38623-179">Poniżej przedstawiono niektóre dodatkowe informacje dotyczące tych przy użyciu systemu SQLite.</span><span class="sxs-lookup"><span data-stu-id="38623-179">Here is some additional information for those using SQLite.</span></span>

### <a name="installing-spatialite"></a><span data-ttu-id="38623-180">Instalowanie SpatiaLite</span><span class="sxs-lookup"><span data-stu-id="38623-180">Installing SpatiaLite</span></span>

<span data-ttu-id="38623-181">W Windows biblioteki natywnej mod_spatialite jest rozpowszechniany jako zależność pakietu NuGet.</span><span class="sxs-lookup"><span data-stu-id="38623-181">On Windows, the native mod_spatialite library is distributed as a NuGet package dependency.</span></span> <span data-ttu-id="38623-182">Platformami, trzeba go zainstalować osobno.</span><span class="sxs-lookup"><span data-stu-id="38623-182">Other platforms need to install it separately.</span></span> <span data-ttu-id="38623-183">Zazwyczaj jest to wykonywane przy użyciu Menedżera pakietów oprogramowania.</span><span class="sxs-lookup"><span data-stu-id="38623-183">This is typically done using a software package manager.</span></span> <span data-ttu-id="38623-184">Na przykład można użyć APT w systemie Ubuntu i Homebrew w systemie MacOS.</span><span class="sxs-lookup"><span data-stu-id="38623-184">For example, you can use APT on Ubuntu and Homebrew on MacOS.</span></span>

``` sh
# Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

### <a name="configuring-srid"></a><span data-ttu-id="38623-185">Konfigurowanie SRID</span><span class="sxs-lookup"><span data-stu-id="38623-185">Configuring SRID</span></span>

<span data-ttu-id="38623-186">Kolumny SpatiaLite, muszą określić SRID według kolumny.</span><span class="sxs-lookup"><span data-stu-id="38623-186">In SpatiaLite, columns need to specify an SRID per column.</span></span> <span data-ttu-id="38623-187">Wartość domyślna to SRID `0`.</span><span class="sxs-lookup"><span data-stu-id="38623-187">The default SRID is `0`.</span></span> <span data-ttu-id="38623-188">Określ inną SRID, przy użyciu metody ForSqliteHasSrid.</span><span class="sxs-lookup"><span data-stu-id="38623-188">Specify a different SRID using the ForSqliteHasSrid method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

### <a name="dimension"></a><span data-ttu-id="38623-189">Wymiar</span><span class="sxs-lookup"><span data-stu-id="38623-189">Dimension</span></span>

<span data-ttu-id="38623-190">Podobnie jak SRID, wymiaru (lub kolumny rzędne) jest również określona jako część kolumny.</span><span class="sxs-lookup"><span data-stu-id="38623-190">Similar to SRID, a column's dimension (or ordinates) is also specified as part of the column.</span></span> <span data-ttu-id="38623-191">Rzędne domyślne są X i Y. Włącz dodatkowe rzędne (Z i M) przy użyciu metody ForSqliteHasDimension.</span><span class="sxs-lookup"><span data-stu-id="38623-191">The default ordinates are X and Y. Enable additional ordinates (Z and M) using the ForSqliteHasDimension method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasDimension(Ordinates.XYZ);
```

## <a name="translated-operations"></a><span data-ttu-id="38623-192">Operacje tłumaczenia</span><span class="sxs-lookup"><span data-stu-id="38623-192">Translated Operations</span></span>

<span data-ttu-id="38623-193">W poniższej tabeli przedstawiono, które elementy członkowskie nkty przerwania są tłumaczone na SQL przez każdy dostawca programu EF Core.</span><span class="sxs-lookup"><span data-stu-id="38623-193">This table shows which NTS members are translated into SQL by each EF Core provider.</span></span>

<span data-ttu-id="38623-194">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="38623-194">NetTopologySuite</span></span> | <span data-ttu-id="38623-195">Program SQL Server (geometrii)</span><span class="sxs-lookup"><span data-stu-id="38623-195">SQL Server (geometry)</span></span> | <span data-ttu-id="38623-196">Program SQL Server (geograficzne)</span><span class="sxs-lookup"><span data-stu-id="38623-196">SQL Server (geography)</span></span> | <span data-ttu-id="38623-197">Bazy danych SQLite</span><span class="sxs-lookup"><span data-stu-id="38623-197">SQLite</span></span> | <span data-ttu-id="38623-198">Npgsql</span><span class="sxs-lookup"><span data-stu-id="38623-198">Npgsql</span></span>
--- |:---:|:---:|:---:|:---:
<span data-ttu-id="38623-199">Geometry.Area</span><span class="sxs-lookup"><span data-stu-id="38623-199">Geometry.Area</span></span> | <span data-ttu-id="38623-200">✔</span><span class="sxs-lookup"><span data-stu-id="38623-200">✔</span></span> | <span data-ttu-id="38623-201">✔</span><span class="sxs-lookup"><span data-stu-id="38623-201">✔</span></span> | <span data-ttu-id="38623-202">✔</span><span class="sxs-lookup"><span data-stu-id="38623-202">✔</span></span> | <span data-ttu-id="38623-203">✔</span><span class="sxs-lookup"><span data-stu-id="38623-203">✔</span></span>
<span data-ttu-id="38623-204">Geometry.AsBinary()</span><span class="sxs-lookup"><span data-stu-id="38623-204">Geometry.AsBinary()</span></span> | <span data-ttu-id="38623-205">✔</span><span class="sxs-lookup"><span data-stu-id="38623-205">✔</span></span> | <span data-ttu-id="38623-206">✔</span><span class="sxs-lookup"><span data-stu-id="38623-206">✔</span></span> | <span data-ttu-id="38623-207">✔</span><span class="sxs-lookup"><span data-stu-id="38623-207">✔</span></span> | <span data-ttu-id="38623-208">✔</span><span class="sxs-lookup"><span data-stu-id="38623-208">✔</span></span>
<span data-ttu-id="38623-209">Geometry.AsText()</span><span class="sxs-lookup"><span data-stu-id="38623-209">Geometry.AsText()</span></span> | <span data-ttu-id="38623-210">✔</span><span class="sxs-lookup"><span data-stu-id="38623-210">✔</span></span> | <span data-ttu-id="38623-211">✔</span><span class="sxs-lookup"><span data-stu-id="38623-211">✔</span></span> | <span data-ttu-id="38623-212">✔</span><span class="sxs-lookup"><span data-stu-id="38623-212">✔</span></span> | <span data-ttu-id="38623-213">✔</span><span class="sxs-lookup"><span data-stu-id="38623-213">✔</span></span>
<span data-ttu-id="38623-214">Geometry.Boundary</span><span class="sxs-lookup"><span data-stu-id="38623-214">Geometry.Boundary</span></span> | <span data-ttu-id="38623-215">✔</span><span class="sxs-lookup"><span data-stu-id="38623-215">✔</span></span> | | <span data-ttu-id="38623-216">✔</span><span class="sxs-lookup"><span data-stu-id="38623-216">✔</span></span> | <span data-ttu-id="38623-217">✔</span><span class="sxs-lookup"><span data-stu-id="38623-217">✔</span></span>
<span data-ttu-id="38623-218">Geometry.Buffer(double)</span><span class="sxs-lookup"><span data-stu-id="38623-218">Geometry.Buffer(double)</span></span> | <span data-ttu-id="38623-219">✔</span><span class="sxs-lookup"><span data-stu-id="38623-219">✔</span></span> | <span data-ttu-id="38623-220">✔</span><span class="sxs-lookup"><span data-stu-id="38623-220">✔</span></span> | <span data-ttu-id="38623-221">✔</span><span class="sxs-lookup"><span data-stu-id="38623-221">✔</span></span> | <span data-ttu-id="38623-222">✔</span><span class="sxs-lookup"><span data-stu-id="38623-222">✔</span></span>
<span data-ttu-id="38623-223">Geometry.Buffer (double, int)</span><span class="sxs-lookup"><span data-stu-id="38623-223">Geometry.Buffer(double, int)</span></span> | | | <span data-ttu-id="38623-224">✔</span><span class="sxs-lookup"><span data-stu-id="38623-224">✔</span></span>
<span data-ttu-id="38623-225">Geometry.Centroid</span><span class="sxs-lookup"><span data-stu-id="38623-225">Geometry.Centroid</span></span> | <span data-ttu-id="38623-226">✔</span><span class="sxs-lookup"><span data-stu-id="38623-226">✔</span></span> | | <span data-ttu-id="38623-227">✔</span><span class="sxs-lookup"><span data-stu-id="38623-227">✔</span></span> | <span data-ttu-id="38623-228">✔</span><span class="sxs-lookup"><span data-stu-id="38623-228">✔</span></span>
<span data-ttu-id="38623-229">Geometry.Contains(Geometry)</span><span class="sxs-lookup"><span data-stu-id="38623-229">Geometry.Contains(Geometry)</span></span> | <span data-ttu-id="38623-230">✔</span><span class="sxs-lookup"><span data-stu-id="38623-230">✔</span></span> | <span data-ttu-id="38623-231">✔</span><span class="sxs-lookup"><span data-stu-id="38623-231">✔</span></span> | <span data-ttu-id="38623-232">✔</span><span class="sxs-lookup"><span data-stu-id="38623-232">✔</span></span> | <span data-ttu-id="38623-233">✔</span><span class="sxs-lookup"><span data-stu-id="38623-233">✔</span></span>
<span data-ttu-id="38623-234">Geometry.ConvexHull()</span><span class="sxs-lookup"><span data-stu-id="38623-234">Geometry.ConvexHull()</span></span> | <span data-ttu-id="38623-235">✔</span><span class="sxs-lookup"><span data-stu-id="38623-235">✔</span></span> | <span data-ttu-id="38623-236">✔</span><span class="sxs-lookup"><span data-stu-id="38623-236">✔</span></span> | <span data-ttu-id="38623-237">✔</span><span class="sxs-lookup"><span data-stu-id="38623-237">✔</span></span> | <span data-ttu-id="38623-238">✔</span><span class="sxs-lookup"><span data-stu-id="38623-238">✔</span></span>
<span data-ttu-id="38623-239">Geometry.CoveredBy(Geometry)</span><span class="sxs-lookup"><span data-stu-id="38623-239">Geometry.CoveredBy(Geometry)</span></span> | | | <span data-ttu-id="38623-240">✔</span><span class="sxs-lookup"><span data-stu-id="38623-240">✔</span></span> | <span data-ttu-id="38623-241">✔</span><span class="sxs-lookup"><span data-stu-id="38623-241">✔</span></span>
<span data-ttu-id="38623-242">Geometry.Covers(Geometry)</span><span class="sxs-lookup"><span data-stu-id="38623-242">Geometry.Covers(Geometry)</span></span> | | | <span data-ttu-id="38623-243">✔</span><span class="sxs-lookup"><span data-stu-id="38623-243">✔</span></span> | <span data-ttu-id="38623-244">✔</span><span class="sxs-lookup"><span data-stu-id="38623-244">✔</span></span>
<span data-ttu-id="38623-245">Geometry.Crosses(Geometry)</span><span class="sxs-lookup"><span data-stu-id="38623-245">Geometry.Crosses(Geometry)</span></span> | <span data-ttu-id="38623-246">✔</span><span class="sxs-lookup"><span data-stu-id="38623-246">✔</span></span> | | <span data-ttu-id="38623-247">✔</span><span class="sxs-lookup"><span data-stu-id="38623-247">✔</span></span> | <span data-ttu-id="38623-248">✔</span><span class="sxs-lookup"><span data-stu-id="38623-248">✔</span></span>
<span data-ttu-id="38623-249">Geometry.Difference(Geometry)</span><span class="sxs-lookup"><span data-stu-id="38623-249">Geometry.Difference(Geometry)</span></span> | <span data-ttu-id="38623-250">✔</span><span class="sxs-lookup"><span data-stu-id="38623-250">✔</span></span> | <span data-ttu-id="38623-251">✔</span><span class="sxs-lookup"><span data-stu-id="38623-251">✔</span></span> | <span data-ttu-id="38623-252">✔</span><span class="sxs-lookup"><span data-stu-id="38623-252">✔</span></span> | <span data-ttu-id="38623-253">✔</span><span class="sxs-lookup"><span data-stu-id="38623-253">✔</span></span>
<span data-ttu-id="38623-254">Geometry.Dimension</span><span class="sxs-lookup"><span data-stu-id="38623-254">Geometry.Dimension</span></span> | <span data-ttu-id="38623-255">✔</span><span class="sxs-lookup"><span data-stu-id="38623-255">✔</span></span> | <span data-ttu-id="38623-256">✔</span><span class="sxs-lookup"><span data-stu-id="38623-256">✔</span></span> | <span data-ttu-id="38623-257">✔</span><span class="sxs-lookup"><span data-stu-id="38623-257">✔</span></span> | <span data-ttu-id="38623-258">✔</span><span class="sxs-lookup"><span data-stu-id="38623-258">✔</span></span>
<span data-ttu-id="38623-259">Geometry.Disjoint(Geometry)</span><span class="sxs-lookup"><span data-stu-id="38623-259">Geometry.Disjoint(Geometry)</span></span> | <span data-ttu-id="38623-260">✔</span><span class="sxs-lookup"><span data-stu-id="38623-260">✔</span></span> | <span data-ttu-id="38623-261">✔</span><span class="sxs-lookup"><span data-stu-id="38623-261">✔</span></span> | <span data-ttu-id="38623-262">✔</span><span class="sxs-lookup"><span data-stu-id="38623-262">✔</span></span> | <span data-ttu-id="38623-263">✔</span><span class="sxs-lookup"><span data-stu-id="38623-263">✔</span></span>
<span data-ttu-id="38623-264">Geometry.Distance(Geometry)</span><span class="sxs-lookup"><span data-stu-id="38623-264">Geometry.Distance(Geometry)</span></span> | <span data-ttu-id="38623-265">✔</span><span class="sxs-lookup"><span data-stu-id="38623-265">✔</span></span> | <span data-ttu-id="38623-266">✔</span><span class="sxs-lookup"><span data-stu-id="38623-266">✔</span></span> | <span data-ttu-id="38623-267">✔</span><span class="sxs-lookup"><span data-stu-id="38623-267">✔</span></span> | <span data-ttu-id="38623-268">✔</span><span class="sxs-lookup"><span data-stu-id="38623-268">✔</span></span>
<span data-ttu-id="38623-269">Geometry.Envelope</span><span class="sxs-lookup"><span data-stu-id="38623-269">Geometry.Envelope</span></span> | <span data-ttu-id="38623-270">✔</span><span class="sxs-lookup"><span data-stu-id="38623-270">✔</span></span> | | <span data-ttu-id="38623-271">✔</span><span class="sxs-lookup"><span data-stu-id="38623-271">✔</span></span> | <span data-ttu-id="38623-272">✔</span><span class="sxs-lookup"><span data-stu-id="38623-272">✔</span></span>
<span data-ttu-id="38623-273">Geometry.EqualsExact(Geometry)</span><span class="sxs-lookup"><span data-stu-id="38623-273">Geometry.EqualsExact(Geometry)</span></span> | | | | <span data-ttu-id="38623-274">✔</span><span class="sxs-lookup"><span data-stu-id="38623-274">✔</span></span>
<span data-ttu-id="38623-275">Geometry.EqualsTopologically(Geometry)</span><span class="sxs-lookup"><span data-stu-id="38623-275">Geometry.EqualsTopologically(Geometry)</span></span> | <span data-ttu-id="38623-276">✔</span><span class="sxs-lookup"><span data-stu-id="38623-276">✔</span></span> | <span data-ttu-id="38623-277">✔</span><span class="sxs-lookup"><span data-stu-id="38623-277">✔</span></span> | <span data-ttu-id="38623-278">✔</span><span class="sxs-lookup"><span data-stu-id="38623-278">✔</span></span> | <span data-ttu-id="38623-279">✔</span><span class="sxs-lookup"><span data-stu-id="38623-279">✔</span></span>
<span data-ttu-id="38623-280">Geometry.GeometryType</span><span class="sxs-lookup"><span data-stu-id="38623-280">Geometry.GeometryType</span></span> | <span data-ttu-id="38623-281">✔</span><span class="sxs-lookup"><span data-stu-id="38623-281">✔</span></span> | <span data-ttu-id="38623-282">✔</span><span class="sxs-lookup"><span data-stu-id="38623-282">✔</span></span> | <span data-ttu-id="38623-283">✔</span><span class="sxs-lookup"><span data-stu-id="38623-283">✔</span></span> | <span data-ttu-id="38623-284">✔</span><span class="sxs-lookup"><span data-stu-id="38623-284">✔</span></span>
<span data-ttu-id="38623-285">Geometry.GetGeometryN(int)</span><span class="sxs-lookup"><span data-stu-id="38623-285">Geometry.GetGeometryN(int)</span></span> | <span data-ttu-id="38623-286">✔</span><span class="sxs-lookup"><span data-stu-id="38623-286">✔</span></span> | | <span data-ttu-id="38623-287">✔</span><span class="sxs-lookup"><span data-stu-id="38623-287">✔</span></span> | <span data-ttu-id="38623-288">✔</span><span class="sxs-lookup"><span data-stu-id="38623-288">✔</span></span>
<span data-ttu-id="38623-289">Geometry.InteriorPoint</span><span class="sxs-lookup"><span data-stu-id="38623-289">Geometry.InteriorPoint</span></span> | <span data-ttu-id="38623-290">✔</span><span class="sxs-lookup"><span data-stu-id="38623-290">✔</span></span> | | <span data-ttu-id="38623-291">✔</span><span class="sxs-lookup"><span data-stu-id="38623-291">✔</span></span>
<span data-ttu-id="38623-292">Geometry.Intersection(Geometry)</span><span class="sxs-lookup"><span data-stu-id="38623-292">Geometry.Intersection(Geometry)</span></span> | <span data-ttu-id="38623-293">✔</span><span class="sxs-lookup"><span data-stu-id="38623-293">✔</span></span> | <span data-ttu-id="38623-294">✔</span><span class="sxs-lookup"><span data-stu-id="38623-294">✔</span></span> | <span data-ttu-id="38623-295">✔</span><span class="sxs-lookup"><span data-stu-id="38623-295">✔</span></span> | <span data-ttu-id="38623-296">✔</span><span class="sxs-lookup"><span data-stu-id="38623-296">✔</span></span>
<span data-ttu-id="38623-297">Geometry.Intersects(Geometry)</span><span class="sxs-lookup"><span data-stu-id="38623-297">Geometry.Intersects(Geometry)</span></span> | <span data-ttu-id="38623-298">✔</span><span class="sxs-lookup"><span data-stu-id="38623-298">✔</span></span> | <span data-ttu-id="38623-299">✔</span><span class="sxs-lookup"><span data-stu-id="38623-299">✔</span></span> | <span data-ttu-id="38623-300">✔</span><span class="sxs-lookup"><span data-stu-id="38623-300">✔</span></span> | <span data-ttu-id="38623-301">✔</span><span class="sxs-lookup"><span data-stu-id="38623-301">✔</span></span>
<span data-ttu-id="38623-302">Geometry.IsEmpty</span><span class="sxs-lookup"><span data-stu-id="38623-302">Geometry.IsEmpty</span></span> | <span data-ttu-id="38623-303">✔</span><span class="sxs-lookup"><span data-stu-id="38623-303">✔</span></span> | <span data-ttu-id="38623-304">✔</span><span class="sxs-lookup"><span data-stu-id="38623-304">✔</span></span> | <span data-ttu-id="38623-305">✔</span><span class="sxs-lookup"><span data-stu-id="38623-305">✔</span></span> | <span data-ttu-id="38623-306">✔</span><span class="sxs-lookup"><span data-stu-id="38623-306">✔</span></span>
<span data-ttu-id="38623-307">Geometry.IsSimple</span><span class="sxs-lookup"><span data-stu-id="38623-307">Geometry.IsSimple</span></span> | <span data-ttu-id="38623-308">✔</span><span class="sxs-lookup"><span data-stu-id="38623-308">✔</span></span> | | <span data-ttu-id="38623-309">✔</span><span class="sxs-lookup"><span data-stu-id="38623-309">✔</span></span> | <span data-ttu-id="38623-310">✔</span><span class="sxs-lookup"><span data-stu-id="38623-310">✔</span></span>
<span data-ttu-id="38623-311">Geometry.IsValid</span><span class="sxs-lookup"><span data-stu-id="38623-311">Geometry.IsValid</span></span> | <span data-ttu-id="38623-312">✔</span><span class="sxs-lookup"><span data-stu-id="38623-312">✔</span></span> | <span data-ttu-id="38623-313">✔</span><span class="sxs-lookup"><span data-stu-id="38623-313">✔</span></span> | <span data-ttu-id="38623-314">✔</span><span class="sxs-lookup"><span data-stu-id="38623-314">✔</span></span> | <span data-ttu-id="38623-315">✔</span><span class="sxs-lookup"><span data-stu-id="38623-315">✔</span></span>
<span data-ttu-id="38623-316">Geometry.IsWithinDistance (Geometry, double)</span><span class="sxs-lookup"><span data-stu-id="38623-316">Geometry.IsWithinDistance(Geometry, double)</span></span> | <span data-ttu-id="38623-317">✔</span><span class="sxs-lookup"><span data-stu-id="38623-317">✔</span></span> | | <span data-ttu-id="38623-318">✔</span><span class="sxs-lookup"><span data-stu-id="38623-318">✔</span></span>
<span data-ttu-id="38623-319">Geometry.Length</span><span class="sxs-lookup"><span data-stu-id="38623-319">Geometry.Length</span></span> | <span data-ttu-id="38623-320">✔</span><span class="sxs-lookup"><span data-stu-id="38623-320">✔</span></span> | <span data-ttu-id="38623-321">✔</span><span class="sxs-lookup"><span data-stu-id="38623-321">✔</span></span> | <span data-ttu-id="38623-322">✔</span><span class="sxs-lookup"><span data-stu-id="38623-322">✔</span></span> | <span data-ttu-id="38623-323">✔</span><span class="sxs-lookup"><span data-stu-id="38623-323">✔</span></span>
<span data-ttu-id="38623-324">Geometry.NumGeometries</span><span class="sxs-lookup"><span data-stu-id="38623-324">Geometry.NumGeometries</span></span> | <span data-ttu-id="38623-325">✔</span><span class="sxs-lookup"><span data-stu-id="38623-325">✔</span></span> | <span data-ttu-id="38623-326">✔</span><span class="sxs-lookup"><span data-stu-id="38623-326">✔</span></span> | <span data-ttu-id="38623-327">✔</span><span class="sxs-lookup"><span data-stu-id="38623-327">✔</span></span> | <span data-ttu-id="38623-328">✔</span><span class="sxs-lookup"><span data-stu-id="38623-328">✔</span></span>
<span data-ttu-id="38623-329">Geometry.NumPoints</span><span class="sxs-lookup"><span data-stu-id="38623-329">Geometry.NumPoints</span></span> | <span data-ttu-id="38623-330">✔</span><span class="sxs-lookup"><span data-stu-id="38623-330">✔</span></span> | <span data-ttu-id="38623-331">✔</span><span class="sxs-lookup"><span data-stu-id="38623-331">✔</span></span> | <span data-ttu-id="38623-332">✔</span><span class="sxs-lookup"><span data-stu-id="38623-332">✔</span></span> | <span data-ttu-id="38623-333">✔</span><span class="sxs-lookup"><span data-stu-id="38623-333">✔</span></span>
<span data-ttu-id="38623-334">Geometry.OgcGeometryType</span><span class="sxs-lookup"><span data-stu-id="38623-334">Geometry.OgcGeometryType</span></span> | <span data-ttu-id="38623-335">✔</span><span class="sxs-lookup"><span data-stu-id="38623-335">✔</span></span> | <span data-ttu-id="38623-336">✔</span><span class="sxs-lookup"><span data-stu-id="38623-336">✔</span></span> | <span data-ttu-id="38623-337">✔</span><span class="sxs-lookup"><span data-stu-id="38623-337">✔</span></span>
<span data-ttu-id="38623-338">Geometry.Overlaps(Geometry)</span><span class="sxs-lookup"><span data-stu-id="38623-338">Geometry.Overlaps(Geometry)</span></span> | <span data-ttu-id="38623-339">✔</span><span class="sxs-lookup"><span data-stu-id="38623-339">✔</span></span> | <span data-ttu-id="38623-340">✔</span><span class="sxs-lookup"><span data-stu-id="38623-340">✔</span></span> | <span data-ttu-id="38623-341">✔</span><span class="sxs-lookup"><span data-stu-id="38623-341">✔</span></span> | <span data-ttu-id="38623-342">✔</span><span class="sxs-lookup"><span data-stu-id="38623-342">✔</span></span>
<span data-ttu-id="38623-343">Geometry.PointOnSurface</span><span class="sxs-lookup"><span data-stu-id="38623-343">Geometry.PointOnSurface</span></span> | <span data-ttu-id="38623-344">✔</span><span class="sxs-lookup"><span data-stu-id="38623-344">✔</span></span> | | <span data-ttu-id="38623-345">✔</span><span class="sxs-lookup"><span data-stu-id="38623-345">✔</span></span> | <span data-ttu-id="38623-346">✔</span><span class="sxs-lookup"><span data-stu-id="38623-346">✔</span></span>
<span data-ttu-id="38623-347">Geometry.Relate (Geometry, ciąg)</span><span class="sxs-lookup"><span data-stu-id="38623-347">Geometry.Relate(Geometry, string)</span></span> | <span data-ttu-id="38623-348">✔</span><span class="sxs-lookup"><span data-stu-id="38623-348">✔</span></span> | | <span data-ttu-id="38623-349">✔</span><span class="sxs-lookup"><span data-stu-id="38623-349">✔</span></span> | <span data-ttu-id="38623-350">✔</span><span class="sxs-lookup"><span data-stu-id="38623-350">✔</span></span>
<span data-ttu-id="38623-351">Geometry.Reverse()</span><span class="sxs-lookup"><span data-stu-id="38623-351">Geometry.Reverse()</span></span> | | | <span data-ttu-id="38623-352">✔</span><span class="sxs-lookup"><span data-stu-id="38623-352">✔</span></span> | <span data-ttu-id="38623-353">✔</span><span class="sxs-lookup"><span data-stu-id="38623-353">✔</span></span>
<span data-ttu-id="38623-354">Geometry.SRID</span><span class="sxs-lookup"><span data-stu-id="38623-354">Geometry.SRID</span></span> | <span data-ttu-id="38623-355">✔</span><span class="sxs-lookup"><span data-stu-id="38623-355">✔</span></span> | <span data-ttu-id="38623-356">✔</span><span class="sxs-lookup"><span data-stu-id="38623-356">✔</span></span> | <span data-ttu-id="38623-357">✔</span><span class="sxs-lookup"><span data-stu-id="38623-357">✔</span></span> | <span data-ttu-id="38623-358">✔</span><span class="sxs-lookup"><span data-stu-id="38623-358">✔</span></span>
<span data-ttu-id="38623-359">Geometry.SymmetricDifference(Geometry)</span><span class="sxs-lookup"><span data-stu-id="38623-359">Geometry.SymmetricDifference(Geometry)</span></span> | <span data-ttu-id="38623-360">✔</span><span class="sxs-lookup"><span data-stu-id="38623-360">✔</span></span> | <span data-ttu-id="38623-361">✔</span><span class="sxs-lookup"><span data-stu-id="38623-361">✔</span></span> | <span data-ttu-id="38623-362">✔</span><span class="sxs-lookup"><span data-stu-id="38623-362">✔</span></span> | <span data-ttu-id="38623-363">✔</span><span class="sxs-lookup"><span data-stu-id="38623-363">✔</span></span>
<span data-ttu-id="38623-364">Geometry.ToBinary()</span><span class="sxs-lookup"><span data-stu-id="38623-364">Geometry.ToBinary()</span></span> | <span data-ttu-id="38623-365">✔</span><span class="sxs-lookup"><span data-stu-id="38623-365">✔</span></span> | <span data-ttu-id="38623-366">✔</span><span class="sxs-lookup"><span data-stu-id="38623-366">✔</span></span> | <span data-ttu-id="38623-367">✔</span><span class="sxs-lookup"><span data-stu-id="38623-367">✔</span></span> | <span data-ttu-id="38623-368">✔</span><span class="sxs-lookup"><span data-stu-id="38623-368">✔</span></span>
<span data-ttu-id="38623-369">Geometry.ToText()</span><span class="sxs-lookup"><span data-stu-id="38623-369">Geometry.ToText()</span></span> | <span data-ttu-id="38623-370">✔</span><span class="sxs-lookup"><span data-stu-id="38623-370">✔</span></span> | <span data-ttu-id="38623-371">✔</span><span class="sxs-lookup"><span data-stu-id="38623-371">✔</span></span> | <span data-ttu-id="38623-372">✔</span><span class="sxs-lookup"><span data-stu-id="38623-372">✔</span></span> | <span data-ttu-id="38623-373">✔</span><span class="sxs-lookup"><span data-stu-id="38623-373">✔</span></span>
<span data-ttu-id="38623-374">Geometry.Touches(Geometry)</span><span class="sxs-lookup"><span data-stu-id="38623-374">Geometry.Touches(Geometry)</span></span> | <span data-ttu-id="38623-375">✔</span><span class="sxs-lookup"><span data-stu-id="38623-375">✔</span></span> | | <span data-ttu-id="38623-376">✔</span><span class="sxs-lookup"><span data-stu-id="38623-376">✔</span></span> | <span data-ttu-id="38623-377">✔</span><span class="sxs-lookup"><span data-stu-id="38623-377">✔</span></span>
<span data-ttu-id="38623-378">Geometry.Union()</span><span class="sxs-lookup"><span data-stu-id="38623-378">Geometry.Union()</span></span> | | | <span data-ttu-id="38623-379">✔</span><span class="sxs-lookup"><span data-stu-id="38623-379">✔</span></span>
<span data-ttu-id="38623-380">Geometry.Union(Geometry)</span><span class="sxs-lookup"><span data-stu-id="38623-380">Geometry.Union(Geometry)</span></span> | <span data-ttu-id="38623-381">✔</span><span class="sxs-lookup"><span data-stu-id="38623-381">✔</span></span> | <span data-ttu-id="38623-382">✔</span><span class="sxs-lookup"><span data-stu-id="38623-382">✔</span></span> | <span data-ttu-id="38623-383">✔</span><span class="sxs-lookup"><span data-stu-id="38623-383">✔</span></span> | <span data-ttu-id="38623-384">✔</span><span class="sxs-lookup"><span data-stu-id="38623-384">✔</span></span>
<span data-ttu-id="38623-385">Geometry.Within(Geometry)</span><span class="sxs-lookup"><span data-stu-id="38623-385">Geometry.Within(Geometry)</span></span> | <span data-ttu-id="38623-386">✔</span><span class="sxs-lookup"><span data-stu-id="38623-386">✔</span></span> | <span data-ttu-id="38623-387">✔</span><span class="sxs-lookup"><span data-stu-id="38623-387">✔</span></span> | <span data-ttu-id="38623-388">✔</span><span class="sxs-lookup"><span data-stu-id="38623-388">✔</span></span> | <span data-ttu-id="38623-389">✔</span><span class="sxs-lookup"><span data-stu-id="38623-389">✔</span></span>
<span data-ttu-id="38623-390">GeometryCollection.Count</span><span class="sxs-lookup"><span data-stu-id="38623-390">GeometryCollection.Count</span></span> | <span data-ttu-id="38623-391">✔</span><span class="sxs-lookup"><span data-stu-id="38623-391">✔</span></span> | <span data-ttu-id="38623-392">✔</span><span class="sxs-lookup"><span data-stu-id="38623-392">✔</span></span> | <span data-ttu-id="38623-393">✔</span><span class="sxs-lookup"><span data-stu-id="38623-393">✔</span></span> | <span data-ttu-id="38623-394">✔</span><span class="sxs-lookup"><span data-stu-id="38623-394">✔</span></span>
<span data-ttu-id="38623-395">GeometryCollection [int]</span><span class="sxs-lookup"><span data-stu-id="38623-395">GeometryCollection[int]</span></span> | <span data-ttu-id="38623-396">✔</span><span class="sxs-lookup"><span data-stu-id="38623-396">✔</span></span> | <span data-ttu-id="38623-397">✔</span><span class="sxs-lookup"><span data-stu-id="38623-397">✔</span></span> | <span data-ttu-id="38623-398">✔</span><span class="sxs-lookup"><span data-stu-id="38623-398">✔</span></span> | <span data-ttu-id="38623-399">✔</span><span class="sxs-lookup"><span data-stu-id="38623-399">✔</span></span>
<span data-ttu-id="38623-400">LineString.Count</span><span class="sxs-lookup"><span data-stu-id="38623-400">LineString.Count</span></span> | <span data-ttu-id="38623-401">✔</span><span class="sxs-lookup"><span data-stu-id="38623-401">✔</span></span> | <span data-ttu-id="38623-402">✔</span><span class="sxs-lookup"><span data-stu-id="38623-402">✔</span></span> | <span data-ttu-id="38623-403">✔</span><span class="sxs-lookup"><span data-stu-id="38623-403">✔</span></span> | <span data-ttu-id="38623-404">✔</span><span class="sxs-lookup"><span data-stu-id="38623-404">✔</span></span>
<span data-ttu-id="38623-405">LineString.EndPoint</span><span class="sxs-lookup"><span data-stu-id="38623-405">LineString.EndPoint</span></span> | <span data-ttu-id="38623-406">✔</span><span class="sxs-lookup"><span data-stu-id="38623-406">✔</span></span> | <span data-ttu-id="38623-407">✔</span><span class="sxs-lookup"><span data-stu-id="38623-407">✔</span></span> | <span data-ttu-id="38623-408">✔</span><span class="sxs-lookup"><span data-stu-id="38623-408">✔</span></span> | <span data-ttu-id="38623-409">✔</span><span class="sxs-lookup"><span data-stu-id="38623-409">✔</span></span>
<span data-ttu-id="38623-410">LineString.GetPointN(int)</span><span class="sxs-lookup"><span data-stu-id="38623-410">LineString.GetPointN(int)</span></span> | <span data-ttu-id="38623-411">✔</span><span class="sxs-lookup"><span data-stu-id="38623-411">✔</span></span> | <span data-ttu-id="38623-412">✔</span><span class="sxs-lookup"><span data-stu-id="38623-412">✔</span></span> | <span data-ttu-id="38623-413">✔</span><span class="sxs-lookup"><span data-stu-id="38623-413">✔</span></span> | <span data-ttu-id="38623-414">✔</span><span class="sxs-lookup"><span data-stu-id="38623-414">✔</span></span>
<span data-ttu-id="38623-415">LineString.IsClosed</span><span class="sxs-lookup"><span data-stu-id="38623-415">LineString.IsClosed</span></span> | <span data-ttu-id="38623-416">✔</span><span class="sxs-lookup"><span data-stu-id="38623-416">✔</span></span> | <span data-ttu-id="38623-417">✔</span><span class="sxs-lookup"><span data-stu-id="38623-417">✔</span></span> | <span data-ttu-id="38623-418">✔</span><span class="sxs-lookup"><span data-stu-id="38623-418">✔</span></span> | <span data-ttu-id="38623-419">✔</span><span class="sxs-lookup"><span data-stu-id="38623-419">✔</span></span>
<span data-ttu-id="38623-420">LineString.IsRing</span><span class="sxs-lookup"><span data-stu-id="38623-420">LineString.IsRing</span></span> | <span data-ttu-id="38623-421">✔</span><span class="sxs-lookup"><span data-stu-id="38623-421">✔</span></span> | | <span data-ttu-id="38623-422">✔</span><span class="sxs-lookup"><span data-stu-id="38623-422">✔</span></span> | <span data-ttu-id="38623-423">✔</span><span class="sxs-lookup"><span data-stu-id="38623-423">✔</span></span>
<span data-ttu-id="38623-424">LineString.StartPoint</span><span class="sxs-lookup"><span data-stu-id="38623-424">LineString.StartPoint</span></span> | <span data-ttu-id="38623-425">✔</span><span class="sxs-lookup"><span data-stu-id="38623-425">✔</span></span> | <span data-ttu-id="38623-426">✔</span><span class="sxs-lookup"><span data-stu-id="38623-426">✔</span></span> | <span data-ttu-id="38623-427">✔</span><span class="sxs-lookup"><span data-stu-id="38623-427">✔</span></span> | <span data-ttu-id="38623-428">✔</span><span class="sxs-lookup"><span data-stu-id="38623-428">✔</span></span>
<span data-ttu-id="38623-429">MultiLineString.IsClosed</span><span class="sxs-lookup"><span data-stu-id="38623-429">MultiLineString.IsClosed</span></span> | <span data-ttu-id="38623-430">✔</span><span class="sxs-lookup"><span data-stu-id="38623-430">✔</span></span> | <span data-ttu-id="38623-431">✔</span><span class="sxs-lookup"><span data-stu-id="38623-431">✔</span></span> | <span data-ttu-id="38623-432">✔</span><span class="sxs-lookup"><span data-stu-id="38623-432">✔</span></span> | <span data-ttu-id="38623-433">✔</span><span class="sxs-lookup"><span data-stu-id="38623-433">✔</span></span>
<span data-ttu-id="38623-434">Point.M</span><span class="sxs-lookup"><span data-stu-id="38623-434">Point.M</span></span> | <span data-ttu-id="38623-435">✔</span><span class="sxs-lookup"><span data-stu-id="38623-435">✔</span></span> | <span data-ttu-id="38623-436">✔</span><span class="sxs-lookup"><span data-stu-id="38623-436">✔</span></span> | <span data-ttu-id="38623-437">✔</span><span class="sxs-lookup"><span data-stu-id="38623-437">✔</span></span> | <span data-ttu-id="38623-438">✔</span><span class="sxs-lookup"><span data-stu-id="38623-438">✔</span></span>
<span data-ttu-id="38623-439">Point.X</span><span class="sxs-lookup"><span data-stu-id="38623-439">Point.X</span></span> | <span data-ttu-id="38623-440">✔</span><span class="sxs-lookup"><span data-stu-id="38623-440">✔</span></span> | <span data-ttu-id="38623-441">✔</span><span class="sxs-lookup"><span data-stu-id="38623-441">✔</span></span> | <span data-ttu-id="38623-442">✔</span><span class="sxs-lookup"><span data-stu-id="38623-442">✔</span></span> | <span data-ttu-id="38623-443">✔</span><span class="sxs-lookup"><span data-stu-id="38623-443">✔</span></span>
<span data-ttu-id="38623-444">Point.Y</span><span class="sxs-lookup"><span data-stu-id="38623-444">Point.Y</span></span> | <span data-ttu-id="38623-445">✔</span><span class="sxs-lookup"><span data-stu-id="38623-445">✔</span></span> | <span data-ttu-id="38623-446">✔</span><span class="sxs-lookup"><span data-stu-id="38623-446">✔</span></span> | <span data-ttu-id="38623-447">✔</span><span class="sxs-lookup"><span data-stu-id="38623-447">✔</span></span> | <span data-ttu-id="38623-448">✔</span><span class="sxs-lookup"><span data-stu-id="38623-448">✔</span></span>
<span data-ttu-id="38623-449">Point.Z</span><span class="sxs-lookup"><span data-stu-id="38623-449">Point.Z</span></span> | <span data-ttu-id="38623-450">✔</span><span class="sxs-lookup"><span data-stu-id="38623-450">✔</span></span> | <span data-ttu-id="38623-451">✔</span><span class="sxs-lookup"><span data-stu-id="38623-451">✔</span></span> | <span data-ttu-id="38623-452">✔</span><span class="sxs-lookup"><span data-stu-id="38623-452">✔</span></span> | <span data-ttu-id="38623-453">✔</span><span class="sxs-lookup"><span data-stu-id="38623-453">✔</span></span>
<span data-ttu-id="38623-454">Polygon.ExteriorRing</span><span class="sxs-lookup"><span data-stu-id="38623-454">Polygon.ExteriorRing</span></span> | <span data-ttu-id="38623-455">✔</span><span class="sxs-lookup"><span data-stu-id="38623-455">✔</span></span> | <span data-ttu-id="38623-456">✔</span><span class="sxs-lookup"><span data-stu-id="38623-456">✔</span></span> | <span data-ttu-id="38623-457">✔</span><span class="sxs-lookup"><span data-stu-id="38623-457">✔</span></span> | <span data-ttu-id="38623-458">✔</span><span class="sxs-lookup"><span data-stu-id="38623-458">✔</span></span>
<span data-ttu-id="38623-459">Polygon.GetInteriorRingN(int)</span><span class="sxs-lookup"><span data-stu-id="38623-459">Polygon.GetInteriorRingN(int)</span></span> | <span data-ttu-id="38623-460">✔</span><span class="sxs-lookup"><span data-stu-id="38623-460">✔</span></span> | <span data-ttu-id="38623-461">✔</span><span class="sxs-lookup"><span data-stu-id="38623-461">✔</span></span> | <span data-ttu-id="38623-462">✔</span><span class="sxs-lookup"><span data-stu-id="38623-462">✔</span></span> | <span data-ttu-id="38623-463">✔</span><span class="sxs-lookup"><span data-stu-id="38623-463">✔</span></span>
<span data-ttu-id="38623-464">Polygon.NumInteriorRings</span><span class="sxs-lookup"><span data-stu-id="38623-464">Polygon.NumInteriorRings</span></span> | <span data-ttu-id="38623-465">✔</span><span class="sxs-lookup"><span data-stu-id="38623-465">✔</span></span> | <span data-ttu-id="38623-466">✔</span><span class="sxs-lookup"><span data-stu-id="38623-466">✔</span></span> | <span data-ttu-id="38623-467">✔</span><span class="sxs-lookup"><span data-stu-id="38623-467">✔</span></span> | <span data-ttu-id="38623-468">✔</span><span class="sxs-lookup"><span data-stu-id="38623-468">✔</span></span>

## <a name="additional-resources"></a><span data-ttu-id="38623-469">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="38623-469">Additional resources</span></span>

* [<span data-ttu-id="38623-470">Dane przestrzenne programu SQL Server</span><span class="sxs-lookup"><span data-stu-id="38623-470">Spatial Data in SQL Server</span></span>](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [<span data-ttu-id="38623-471">Strona główna SpatiaLite</span><span class="sxs-lookup"><span data-stu-id="38623-471">SpatiaLite Homepage</span></span>](https://www.gaia-gis.it/fossil/libspatialite)
* [<span data-ttu-id="38623-472">Dokumentacja przestrzenne Npgsql</span><span class="sxs-lookup"><span data-stu-id="38623-472">Npgsql Spatial Documentation</span></span>](http://www.npgsql.org/efcore/mapping/nts.html)
* [<span data-ttu-id="38623-473">Dokumentacja PostGIS</span><span class="sxs-lookup"><span data-stu-id="38623-473">PostGIS Documentation</span></span>](http://postgis.net/documentation/)