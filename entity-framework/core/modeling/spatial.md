---
title: Dane przestrzenne — EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/01/2018
ms.assetid: 2BDE29FC-4161-41A0-841E-69F51CCD9341
uid: core/modeling/spatial
ms.openlocfilehash: 85124b7e252797ccd952d0d332e7309eff97ba56
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526670"
---
# <a name="spatial-data"></a><span data-ttu-id="77d15-102">Dane przestrzenne</span><span class="sxs-lookup"><span data-stu-id="77d15-102">Spatial Data</span></span>

> [!NOTE]
> <span data-ttu-id="77d15-103">Ta funkcja została dodana w EF Core 2,2.</span><span class="sxs-lookup"><span data-stu-id="77d15-103">This feature was added in EF Core 2.2.</span></span>

<span data-ttu-id="77d15-104">Dane przestrzenne reprezentują lokalizację fizyczną i kształt obiektów.</span><span class="sxs-lookup"><span data-stu-id="77d15-104">Spatial data represents the physical location and the shape of objects.</span></span> <span data-ttu-id="77d15-105">Wiele baz danych zapewnia obsługę tego typu danych, dzięki czemu można je indeksować i badać wraz z innymi danymi.</span><span class="sxs-lookup"><span data-stu-id="77d15-105">Many databases provide support for this type of data so it can be indexed and queried alongside other data.</span></span> <span data-ttu-id="77d15-106">Typowe scenariusze obejmują zapytania dotyczące obiektów w danej odległości od lokalizacji lub wybierając obiekt, którego obramowanie zawiera daną lokalizację.</span><span class="sxs-lookup"><span data-stu-id="77d15-106">Common scenarios include querying for objects within a given distance from a location, or selecting the object whose border contains a given location.</span></span> <span data-ttu-id="77d15-107">EF Core obsługuje mapowanie do typów danych przestrzennych przy użyciu biblioteki przestrzennej [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) .</span><span class="sxs-lookup"><span data-stu-id="77d15-107">EF Core supports mapping to spatial data types using the [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) spatial library.</span></span>

## <a name="installing"></a><span data-ttu-id="77d15-108">Instalowanie</span><span class="sxs-lookup"><span data-stu-id="77d15-108">Installing</span></span>

<span data-ttu-id="77d15-109">Aby można było używać danych przestrzennych z EF Core, należy zainstalować odpowiedni pomocniczy pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="77d15-109">In order to use spatial data with EF Core, you need to install the appropriate supporting NuGet package.</span></span> <span data-ttu-id="77d15-110">Który pakiet należy zainstalować zależy od dostawcy, którego używasz.</span><span class="sxs-lookup"><span data-stu-id="77d15-110">Which package you need to install depends on the provider you're using.</span></span>

<span data-ttu-id="77d15-111">Dostawca EF Core</span><span class="sxs-lookup"><span data-stu-id="77d15-111">EF Core Provider</span></span>                        | <span data-ttu-id="77d15-112">Przestrzenny pakiet NuGet</span><span class="sxs-lookup"><span data-stu-id="77d15-112">Spatial NuGet Package</span></span>
--------------------------------------- | ---------------------
<span data-ttu-id="77d15-113">Microsoft. EntityFrameworkCore. SqlServer</span><span class="sxs-lookup"><span data-stu-id="77d15-113">Microsoft.EntityFrameworkCore.SqlServer</span></span> | [<span data-ttu-id="77d15-114">Microsoft. EntityFrameworkCore. SqlServer. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="77d15-114">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
<span data-ttu-id="77d15-115">Microsoft. EntityFrameworkCore. sqlite</span><span class="sxs-lookup"><span data-stu-id="77d15-115">Microsoft.EntityFrameworkCore.Sqlite</span></span>    | [<span data-ttu-id="77d15-116">Microsoft. EntityFrameworkCore. sqlite. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="77d15-116">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
<span data-ttu-id="77d15-117">Microsoft. EntityFrameworkCore. inMemory</span><span class="sxs-lookup"><span data-stu-id="77d15-117">Microsoft.EntityFrameworkCore.InMemory</span></span>  | [<span data-ttu-id="77d15-118">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="77d15-118">NetTopologySuite</span></span>](https://www.nuget.org/packages/NetTopologySuite)
<span data-ttu-id="77d15-119">Npgsql. EntityFrameworkCore. PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="77d15-119">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>   | [<span data-ttu-id="77d15-120">Npgsql. EntityFrameworkCore. PostgreSQL. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="77d15-120">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)

## <a name="reverse-engineering"></a><span data-ttu-id="77d15-121">Odwrócenie inżynierii</span><span class="sxs-lookup"><span data-stu-id="77d15-121">Reverse engineering</span></span>

<span data-ttu-id="77d15-122">Przestrzenne pakiety NuGet umożliwiają również [Tworzenie modeli odtwarzania](../managing-schemas/scaffolding.md) z właściwościami przestrzennymi, ale należy zainstalować pakiet ***przed*** uruchomieniem `Scaffold-DbContext` lub `dotnet ef dbcontext scaffold` .</span><span class="sxs-lookup"><span data-stu-id="77d15-122">The spatial NuGet packages also enable [reverse engineering](../managing-schemas/scaffolding.md) models with spatial properties, but you need to install the package ***before*** running `Scaffold-DbContext` or `dotnet ef dbcontext scaffold`.</span></span> <span data-ttu-id="77d15-123">W przeciwnym razie otrzymasz ostrzeżenia dotyczące nieznajdowania mapowań typów dla kolumn, a kolumny zostaną pominięte.</span><span class="sxs-lookup"><span data-stu-id="77d15-123">If you don't, you'll receive warnings about not finding type mappings for the columns and the columns will be skipped.</span></span>

## <a name="nettopologysuite-nts"></a><span data-ttu-id="77d15-124">NetTopologySuite (NKTY przerwania)</span><span class="sxs-lookup"><span data-stu-id="77d15-124">NetTopologySuite (NTS)</span></span>

<span data-ttu-id="77d15-125">NetTopologySuite jest biblioteką przestrzenną dla platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="77d15-125">NetTopologySuite is a spatial library for .NET.</span></span> <span data-ttu-id="77d15-126">EF Core umożliwia mapowanie do typów danych przestrzennych w bazie danych przy użyciu typów NKTY przerwania w modelu.</span><span class="sxs-lookup"><span data-stu-id="77d15-126">EF Core enables mapping to spatial data types in the database by using NTS types in your model.</span></span>

<span data-ttu-id="77d15-127">Aby włączyć mapowanie do typów przestrzennych za pośrednictwem NKTY przerwania, wywołaj metodę UseNetTopologySuite w konstruktorze opcji DbContext dostawcy.</span><span class="sxs-lookup"><span data-stu-id="77d15-127">To enable mapping to spatial types via NTS, call the UseNetTopologySuite method on the provider's DbContext options builder.</span></span> <span data-ttu-id="77d15-128">Na przykład w przypadku SQL Server należy wywołać tę metodę.</span><span class="sxs-lookup"><span data-stu-id="77d15-128">For example, with SQL Server you'd call it like this.</span></span>

``` csharp
optionsBuilder.UseSqlServer(
    @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=WideWorldImporters",
    x => x.UseNetTopologySuite());
```

<span data-ttu-id="77d15-129">Istnieje kilka typów danych przestrzennych.</span><span class="sxs-lookup"><span data-stu-id="77d15-129">There are several spatial data types.</span></span> <span data-ttu-id="77d15-130">Używany typ zależy od typów kształtów, które mają być dozwolone.</span><span class="sxs-lookup"><span data-stu-id="77d15-130">Which type you use depends on the types of shapes you want to allow.</span></span> <span data-ttu-id="77d15-131">Poniżej znajduje się hierarchia typów NKTY przerwania, których można użyć do właściwości w modelu.</span><span class="sxs-lookup"><span data-stu-id="77d15-131">Here is the hierarchy of NTS types that you can use for properties in your model.</span></span> <span data-ttu-id="77d15-132">Znajdują się one w `NetTopologySuite.Geometries` przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="77d15-132">They're located within the `NetTopologySuite.Geometries` namespace.</span></span>

* <span data-ttu-id="77d15-133">Geometria</span><span class="sxs-lookup"><span data-stu-id="77d15-133">Geometry</span></span>
  * <span data-ttu-id="77d15-134">Moment</span><span class="sxs-lookup"><span data-stu-id="77d15-134">Point</span></span>
  * <span data-ttu-id="77d15-135">LineString</span><span class="sxs-lookup"><span data-stu-id="77d15-135">LineString</span></span>
  * <span data-ttu-id="77d15-136">Wielokąt</span><span class="sxs-lookup"><span data-stu-id="77d15-136">Polygon</span></span>
  * <span data-ttu-id="77d15-137">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="77d15-137">GeometryCollection</span></span>
    * <span data-ttu-id="77d15-138">Usług</span><span class="sxs-lookup"><span data-stu-id="77d15-138">MultiPoint</span></span>
    * <span data-ttu-id="77d15-139">MultiLineString</span><span class="sxs-lookup"><span data-stu-id="77d15-139">MultiLineString</span></span>
    * <span data-ttu-id="77d15-140">MultiPolygon</span><span class="sxs-lookup"><span data-stu-id="77d15-140">MultiPolygon</span></span>

> [!WARNING]
> <span data-ttu-id="77d15-141">CircularString, CompoundCurve i CurePolygon nie są obsługiwane przez NKTY przerwania.</span><span class="sxs-lookup"><span data-stu-id="77d15-141">CircularString, CompoundCurve, and CurePolygon aren't supported by NTS.</span></span>

<span data-ttu-id="77d15-142">Użycie podstawowego typu geometrii umożliwia określenie dowolnego typu kształtu przez właściwość.</span><span class="sxs-lookup"><span data-stu-id="77d15-142">Using the base Geometry type allows any type of shape to be specified by the property.</span></span>

<span data-ttu-id="77d15-143">Poniższe klasy jednostek mogą służyć do mapowania tabel w [przykładowej bazie danych](https://go.microsoft.com/fwlink/?LinkID=800630)na całym świecie.</span><span class="sxs-lookup"><span data-stu-id="77d15-143">The following entity classes could be used to map to tables in the [Wide World Importers sample database](https://go.microsoft.com/fwlink/?LinkID=800630).</span></span>

``` csharp
[Table("Cities", Schema = "Application"))]
class City
{
    public int CityID { get; set; }

    public string CityName { get; set; }

    public Point Location { get; set; }
}

[Table("Countries", Schema = "Application"))]
class Country
{
    public int CountryID { get; set; }

    public string CountryName { get; set; }

    // Database includes both Polygon and MultiPolygon values
    public Geometry Border { get; set; }
}
```

### <a name="creating-values"></a><span data-ttu-id="77d15-144">Tworzenie wartości</span><span class="sxs-lookup"><span data-stu-id="77d15-144">Creating values</span></span>

<span data-ttu-id="77d15-145">Można użyć konstruktorów do tworzenia obiektów geometrycznych; jednak NKTY przerwania zaleca się użycie fabryki geometrycznej.</span><span class="sxs-lookup"><span data-stu-id="77d15-145">You can use constructors to create geometry objects; however, NTS recommends using a geometry factory instead.</span></span> <span data-ttu-id="77d15-146">Pozwala to określić domyślny SRID (System referencyjny przestrzennej używany przez współrzędne) i zapewnia kontrolę nad bardziej zaawansowanymi elementami, takimi jak model precyzji (używany podczas obliczeń) i sekwencją współrzędnych (określa, które wymiary i miary--są dostępne).</span><span class="sxs-lookup"><span data-stu-id="77d15-146">This lets you specify a default SRID (the spatial reference system used by the coordinates) and gives you control over more advanced things like the precision model (used during calculations) and the coordinate sequence (determines which ordinates--dimensions and measures--are available).</span></span>

``` csharp
var geometryFactory = NtsGeometryServices.Instance.CreateGeometryFactory(srid: 4326);
var currentLocation = geometryFactory.CreatePoint(new Coordinate(-122.121512, 47.6739882));
```

> [!NOTE]
> <span data-ttu-id="77d15-147">4326 dotyczy WGS 84, standardowego używanego w systemach GPS i innych systemów geograficznych.</span><span class="sxs-lookup"><span data-stu-id="77d15-147">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

### <a name="longitude-and-latitude"></a><span data-ttu-id="77d15-148">Długości i szerokości geograficznej</span><span class="sxs-lookup"><span data-stu-id="77d15-148">Longitude and Latitude</span></span>

<span data-ttu-id="77d15-149">Współrzędne w NKTY przerwania są pod względem wartości X i Y.</span><span class="sxs-lookup"><span data-stu-id="77d15-149">Coordinates in NTS are in terms of X and Y values.</span></span> <span data-ttu-id="77d15-150">Aby przedstawić długość i szerokość geograficzną, użyj X dla długości geograficznej i Y dla szerokości geograficznej.</span><span class="sxs-lookup"><span data-stu-id="77d15-150">To represent longitude and latitude, use X for longitude and Y for latitude.</span></span> <span data-ttu-id="77d15-151">Należy zauważyć, że jest to **Wstecz** od `latitude, longitude` formatu, w którym są zwykle wyświetlane te wartości.</span><span class="sxs-lookup"><span data-stu-id="77d15-151">Note that this is **backwards** from the `latitude, longitude` format in which you typically see these values.</span></span>

### <a name="srid-ignored-during-client-operations"></a><span data-ttu-id="77d15-152">SRID zignorowane podczas operacji klienta</span><span class="sxs-lookup"><span data-stu-id="77d15-152">SRID Ignored during client operations</span></span>

<span data-ttu-id="77d15-153">NKTY przerwania ignoruje wartości SRID podczas operacji.</span><span class="sxs-lookup"><span data-stu-id="77d15-153">NTS ignores SRID values during operations.</span></span> <span data-ttu-id="77d15-154">Przyjęto założenie układu współrzędnych.</span><span class="sxs-lookup"><span data-stu-id="77d15-154">It assumes a planar coordinate system.</span></span> <span data-ttu-id="77d15-155">Oznacza to, że jeśli określisz współrzędne w zakresie długości i szerokości geograficznej, niektóre wartości oceniane przez klienta, takie jak odległość, długość i obszar, będą znajdować się w stopniach, a nie w metrach.</span><span class="sxs-lookup"><span data-stu-id="77d15-155">This means that if you specify coordinates in terms of longitude and latitude, some client-evaluated values like distance, length, and area will be in degrees, not meters.</span></span> <span data-ttu-id="77d15-156">Aby uzyskać bardziej zrozumiałe wartości, najpierw należy umieścić współrzędne w innym układzie współrzędnych przy użyciu biblioteki, takiej jak [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) przed obliczeniem tych wartości.</span><span class="sxs-lookup"><span data-stu-id="77d15-156">For more meaningful values, you first need to project the coordinates to another coordinate system using a library like [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) before calculating these values.</span></span>

<span data-ttu-id="77d15-157">Jeśli operacja jest Szacowana przez serwer EF Core za pośrednictwem SQL, jednostka wyniku zostanie określona przez bazę danych.</span><span class="sxs-lookup"><span data-stu-id="77d15-157">If an operation is server-evaluated by EF Core via SQL, the result's unit will be determined by the database.</span></span>

<span data-ttu-id="77d15-158">Oto przykład użycia ProjNet4GeoAPI do obliczenia odległości między dwoma miastami.</span><span class="sxs-lookup"><span data-stu-id="77d15-158">Here is an example of using ProjNet4GeoAPI to calculate the distance between two cities.</span></span>

``` csharp
static class GeometryExtensions
{
    static readonly CoordinateSystemServices _coordinateSystemServices
        = new CoordinateSystemServices(
            new CoordinateSystemFactory(),
            new CoordinateTransformationFactory(),
            new Dictionary<int, string>
            {
                // Coordinate systems:

                [4326] = GeographicCoordinateSystem.WGS84.WKT,

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

    public static Geometry ProjectTo(this Geometry geometry, int srid)
    {
        var transformation = _coordinateSystemServices.CreateTransformation(geometry.SRID, srid);

        var result = geometry.Copy();
        result.Apply(new MathTransformFilter(transformation.MathTransform));

        return result;
    }

    class MathTransformFilter : ICoordinateSequenceFilter
    {
        readonly MathTransform _transform;

        public MathTransformFilter(MathTransform transform)
            => _transform = transform;

        public bool Done => false;
        public bool GeometryChanged => true;

        public void Filter(CoordinateSequence seq, int i)
        {
            var result = _transform.Transform(
                new[]
                {
                    seq.GetOrdinate(i, Ordinate.X),
                    seq.GetOrdinate(i, Ordinate.Y)
                });
            seq.SetOrdinate(i, Ordinate.X, result[0]);
            seq.SetOrdinate(i, Ordinate.Y, result[1]);
        }
    }
}
```

``` csharp
var seattle = new Point(-122.333056, 47.609722) { SRID = 4326 };
var redmond = new Point(-122.123889, 47.669444) { SRID = 4326 };

var distance = seattle.ProjectTo(2855).Distance(redmond.ProjectTo(2855));
```

## <a name="querying-data"></a><span data-ttu-id="77d15-159">Wykonanie zapytania o dane</span><span class="sxs-lookup"><span data-stu-id="77d15-159">Querying Data</span></span>

<span data-ttu-id="77d15-160">W LINQ, metody i właściwości NKTY przerwania dostępne jako funkcje bazy danych zostaną przetłumaczone na SQL.</span><span class="sxs-lookup"><span data-stu-id="77d15-160">In LINQ, the NTS methods and properties available as database functions will be translated to SQL.</span></span> <span data-ttu-id="77d15-161">Na przykład odległość i zawiera metody są tłumaczone w poniższych zapytaniach.</span><span class="sxs-lookup"><span data-stu-id="77d15-161">For example, the Distance and Contains methods are translated in the following queries.</span></span> <span data-ttu-id="77d15-162">W tabeli na końcu tego artykułu przedstawiono, które elementy członkowskie są obsługiwane przez różnych dostawców EF Core.</span><span class="sxs-lookup"><span data-stu-id="77d15-162">The table at the end of this article shows which members are supported by various EF Core providers.</span></span>

``` csharp
var nearestCity = db.Cities
    .OrderBy(c => c.Location.Distance(currentLocation))
    .FirstOrDefault();

var currentCountry = db.Countries
    .FirstOrDefault(c => c.Border.Contains(currentLocation));
```

## <a name="sql-server"></a><span data-ttu-id="77d15-163">SQL Server</span><span class="sxs-lookup"><span data-stu-id="77d15-163">SQL Server</span></span>

<span data-ttu-id="77d15-164">Jeśli używasz SQL Server, musisz wiedzieć o kilku dodatkowych kwestiach.</span><span class="sxs-lookup"><span data-stu-id="77d15-164">If you're using SQL Server, there are some additional things you should be aware of.</span></span>

### <a name="geography-or-geometry"></a><span data-ttu-id="77d15-165">Geografia lub geometria</span><span class="sxs-lookup"><span data-stu-id="77d15-165">Geography or geometry</span></span>

<span data-ttu-id="77d15-166">Domyślnie właściwości przestrzenne są mapowane na `geography` kolumny w SQL Server.</span><span class="sxs-lookup"><span data-stu-id="77d15-166">By default, spatial properties are mapped to `geography` columns in SQL Server.</span></span> <span data-ttu-id="77d15-167">Aby użyć `geometry` , należy [skonfigurować typ kolumny](xref:core/modeling/entity-properties#column-data-types) w modelu.</span><span class="sxs-lookup"><span data-stu-id="77d15-167">To use `geometry`, [configure the column type](xref:core/modeling/entity-properties#column-data-types) in your model.</span></span>

### <a name="geography-polygon-rings"></a><span data-ttu-id="77d15-168">Pierścienie wielokątów geograficznych</span><span class="sxs-lookup"><span data-stu-id="77d15-168">Geography polygon rings</span></span>

<span data-ttu-id="77d15-169">W przypadku używania `geography` typu kolumny SQL Server nakładają dodatkowe wymagania dotyczące pierścienia zewnętrznego (lub powłoki) i wewnętrznych pierścieni (lub dziur).</span><span class="sxs-lookup"><span data-stu-id="77d15-169">When using the `geography` column type, SQL Server imposes additional requirements on the exterior ring (or shell) and interior rings (or holes).</span></span> <span data-ttu-id="77d15-170">Pierścień zewnętrzny musi być zorientowany w lewo i w prawo.</span><span class="sxs-lookup"><span data-stu-id="77d15-170">The exterior ring must be oriented counterclockwise and the interior rings clockwise.</span></span> <span data-ttu-id="77d15-171">NKTY przerwania sprawdza to przed wysłaniem wartości do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="77d15-171">NTS validates this before sending values to the database.</span></span>

### <a name="fullglobe"></a><span data-ttu-id="77d15-172">FullGlobe</span><span class="sxs-lookup"><span data-stu-id="77d15-172">FullGlobe</span></span>

<span data-ttu-id="77d15-173">SQL Server ma niestandardowy typ geometrii reprezentujący pełny Globus przy użyciu `geography` typu kolumny.</span><span class="sxs-lookup"><span data-stu-id="77d15-173">SQL Server has a non-standard geometry type to represent the full globe when using the `geography` column type.</span></span> <span data-ttu-id="77d15-174">Ma również sposób reprezentowania wielokątów w oparciu o pełny Globus (bez pierścienia zewnętrznego).</span><span class="sxs-lookup"><span data-stu-id="77d15-174">It also has a way to represent polygons based on the full globe (without an exterior ring).</span></span> <span data-ttu-id="77d15-175">Żadna z tych elementów nie jest obsługiwana przez NKTY przerwania.</span><span class="sxs-lookup"><span data-stu-id="77d15-175">Neither of these are supported by NTS.</span></span>

> [!WARNING]
> <span data-ttu-id="77d15-176">FullGlobe i wielokąty oparte na nim nie są obsługiwane przez NKTY przerwania.</span><span class="sxs-lookup"><span data-stu-id="77d15-176">FullGlobe and polygons based on it aren't supported by NTS.</span></span>

## <a name="sqlite"></a><span data-ttu-id="77d15-177">SQLite</span><span class="sxs-lookup"><span data-stu-id="77d15-177">SQLite</span></span>

<span data-ttu-id="77d15-178">Poniżej przedstawiono dodatkowe informacje dotyczące tych, które są używane przez program SQLite.</span><span class="sxs-lookup"><span data-stu-id="77d15-178">Here is some additional information for those using SQLite.</span></span>

### <a name="installing-spatialite"></a><span data-ttu-id="77d15-179">Instalowanie SpatiaLite</span><span class="sxs-lookup"><span data-stu-id="77d15-179">Installing SpatiaLite</span></span>

<span data-ttu-id="77d15-180">W systemie Windows natywna biblioteka mod_spatialite jest dystrybuowana jako zależność pakietu NuGet.</span><span class="sxs-lookup"><span data-stu-id="77d15-180">On Windows, the native mod_spatialite library is distributed as a NuGet package dependency.</span></span> <span data-ttu-id="77d15-181">Inne platformy muszą zainstalować je oddzielnie.</span><span class="sxs-lookup"><span data-stu-id="77d15-181">Other platforms need to install it separately.</span></span> <span data-ttu-id="77d15-182">Jest to zazwyczaj realizowane przy użyciu Menedżera pakietów oprogramowania.</span><span class="sxs-lookup"><span data-stu-id="77d15-182">This is typically done using a software package manager.</span></span> <span data-ttu-id="77d15-183">Na przykład można użyć APT w Ubuntu i oprogramowania Homebrew na MacOS.</span><span class="sxs-lookup"><span data-stu-id="77d15-183">For example, you can use APT on Ubuntu and Homebrew on MacOS.</span></span>

``` sh
# Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

<span data-ttu-id="77d15-184">Niestety, nowsze wersje programu PROJ (zależność SpatiaLite) są niezgodne z domyślnym [pakietem SQLITEPCLRAW](/dotnet/standard/data/sqlite/custom-versions#bundles)EF.</span><span class="sxs-lookup"><span data-stu-id="77d15-184">Unfortunately, newer versions of PROJ (a dependency of SpatiaLite) are incompatible with EF's default [SQLitePCLRaw bundle](/dotnet/standard/data/sqlite/custom-versions#bundles).</span></span> <span data-ttu-id="77d15-185">Można to obejść przez utworzenie niestandardowego [dostawcy SQLitePCLRaw](/dotnet/standard/data/sqlite/custom-versions#sqlitepclraw-providers) , który korzysta z biblioteki programu SQLite systemu, lub można zainstalować niestandardową kompilację SpatiaLite.</span><span class="sxs-lookup"><span data-stu-id="77d15-185">You can work around this by either creating a custom [SQLitePCLRaw provider](/dotnet/standard/data/sqlite/custom-versions#sqlitepclraw-providers) that uses the system SQLite library, or you can install a custom build of SpatiaLite disabling PROJ support.</span></span>

``` sh
curl https://www.gaia-gis.it/gaia-sins/libspatialite-4.3.0a.tar.gz | tar -xz
cd libspatialite-4.3.0a

if [[ `uname -s` == Darwin* ]]; then
    # Mac OS requires some minor patching
    sed -i "" "s/shrext_cmds='\`test \\.\$module = .yes && echo .so \\|\\| echo \\.dylib\`'/shrext_cmds='.dylib'/g" configure
fi

./configure --disable-proj
make
make install
```

### <a name="configuring-srid"></a><span data-ttu-id="77d15-186">Konfigurowanie SRID</span><span class="sxs-lookup"><span data-stu-id="77d15-186">Configuring SRID</span></span>

<span data-ttu-id="77d15-187">W SpatiaLite, kolumny muszą określać SRID na kolumnę.</span><span class="sxs-lookup"><span data-stu-id="77d15-187">In SpatiaLite, columns need to specify an SRID per column.</span></span> <span data-ttu-id="77d15-188">Wartość domyślna to SRID `0` .</span><span class="sxs-lookup"><span data-stu-id="77d15-188">The default SRID is `0`.</span></span> <span data-ttu-id="77d15-189">Określ inny SRID przy użyciu metody ForSqliteHasSrid.</span><span class="sxs-lookup"><span data-stu-id="77d15-189">Specify a different SRID using the ForSqliteHasSrid method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

### <a name="dimension"></a><span data-ttu-id="77d15-190">Wymiar</span><span class="sxs-lookup"><span data-stu-id="77d15-190">Dimension</span></span>

<span data-ttu-id="77d15-191">Podobnie jak w przypadku SRID, wymiar kolumny (lub współrzędnej) jest również określony jako część kolumny.</span><span class="sxs-lookup"><span data-stu-id="77d15-191">Similar to SRID, a column's dimension (or ordinates) is also specified as part of the column.</span></span> <span data-ttu-id="77d15-192">Domyślnymi współrzędnymi są X i Y. Włącz dodatkowe współrzędne (Z i M) przy użyciu metody ForSqliteHasDimension.</span><span class="sxs-lookup"><span data-stu-id="77d15-192">The default ordinates are X and Y. Enable additional ordinates (Z and M) using the ForSqliteHasDimension method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasDimension(Ordinates.XYZ);
```

## <a name="translated-operations"></a><span data-ttu-id="77d15-193">Operacje tłumaczone</span><span class="sxs-lookup"><span data-stu-id="77d15-193">Translated Operations</span></span>

<span data-ttu-id="77d15-194">W tej tabeli przedstawiono, które elementy członkowskie NKTY przerwania są tłumaczone na SQL przez każdego dostawcę EF Core.</span><span class="sxs-lookup"><span data-stu-id="77d15-194">This table shows which NTS members are translated into SQL by each EF Core provider.</span></span>

<span data-ttu-id="77d15-195">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="77d15-195">NetTopologySuite</span></span> | <span data-ttu-id="77d15-196">SQL Server (Geometria)</span><span class="sxs-lookup"><span data-stu-id="77d15-196">SQL Server (geometry)</span></span> | <span data-ttu-id="77d15-197">SQL Server (Geografia)</span><span class="sxs-lookup"><span data-stu-id="77d15-197">SQL Server (geography)</span></span> | <span data-ttu-id="77d15-198">SQLite</span><span class="sxs-lookup"><span data-stu-id="77d15-198">SQLite</span></span> | <span data-ttu-id="77d15-199">Npgsql</span><span class="sxs-lookup"><span data-stu-id="77d15-199">Npgsql</span></span>
--- |:---:|:---:|:---:|:---:
<span data-ttu-id="77d15-200">Geometria. obszar</span><span class="sxs-lookup"><span data-stu-id="77d15-200">Geometry.Area</span></span> | <span data-ttu-id="77d15-201">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-201">✔</span></span> | <span data-ttu-id="77d15-202">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-202">✔</span></span> | <span data-ttu-id="77d15-203">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-203">✔</span></span> | <span data-ttu-id="77d15-204">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-204">✔</span></span>
<span data-ttu-id="77d15-205">Geometry. AsBinary ()</span><span class="sxs-lookup"><span data-stu-id="77d15-205">Geometry.AsBinary()</span></span> | <span data-ttu-id="77d15-206">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-206">✔</span></span> | <span data-ttu-id="77d15-207">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-207">✔</span></span> | <span data-ttu-id="77d15-208">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-208">✔</span></span> | <span data-ttu-id="77d15-209">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-209">✔</span></span>
<span data-ttu-id="77d15-210">Geometry. AsText ()</span><span class="sxs-lookup"><span data-stu-id="77d15-210">Geometry.AsText()</span></span> | <span data-ttu-id="77d15-211">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-211">✔</span></span> | <span data-ttu-id="77d15-212">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-212">✔</span></span> | <span data-ttu-id="77d15-213">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-213">✔</span></span> | <span data-ttu-id="77d15-214">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-214">✔</span></span>
<span data-ttu-id="77d15-215">Geometria. granica</span><span class="sxs-lookup"><span data-stu-id="77d15-215">Geometry.Boundary</span></span> | <span data-ttu-id="77d15-216">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-216">✔</span></span> | | <span data-ttu-id="77d15-217">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-217">✔</span></span> | <span data-ttu-id="77d15-218">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-218">✔</span></span>
<span data-ttu-id="77d15-219">Geometry. Buffer (Double)</span><span class="sxs-lookup"><span data-stu-id="77d15-219">Geometry.Buffer(double)</span></span> | <span data-ttu-id="77d15-220">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-220">✔</span></span> | <span data-ttu-id="77d15-221">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-221">✔</span></span> | <span data-ttu-id="77d15-222">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-222">✔</span></span> | <span data-ttu-id="77d15-223">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-223">✔</span></span>
<span data-ttu-id="77d15-224">Geometry. Buffer (Double, int)</span><span class="sxs-lookup"><span data-stu-id="77d15-224">Geometry.Buffer(double, int)</span></span> | | | <span data-ttu-id="77d15-225">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-225">✔</span></span> | <span data-ttu-id="77d15-226">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-226">✔</span></span>
<span data-ttu-id="77d15-227">Geometria. centroida</span><span class="sxs-lookup"><span data-stu-id="77d15-227">Geometry.Centroid</span></span> | <span data-ttu-id="77d15-228">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-228">✔</span></span> | | <span data-ttu-id="77d15-229">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-229">✔</span></span> | <span data-ttu-id="77d15-230">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-230">✔</span></span>
<span data-ttu-id="77d15-231">Geometry. Contains (Geometria)</span><span class="sxs-lookup"><span data-stu-id="77d15-231">Geometry.Contains(Geometry)</span></span> | <span data-ttu-id="77d15-232">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-232">✔</span></span> | <span data-ttu-id="77d15-233">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-233">✔</span></span> | <span data-ttu-id="77d15-234">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-234">✔</span></span> | <span data-ttu-id="77d15-235">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-235">✔</span></span>
<span data-ttu-id="77d15-236">Geometry. ConvexHull ()</span><span class="sxs-lookup"><span data-stu-id="77d15-236">Geometry.ConvexHull()</span></span> | <span data-ttu-id="77d15-237">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-237">✔</span></span> | <span data-ttu-id="77d15-238">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-238">✔</span></span> | <span data-ttu-id="77d15-239">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-239">✔</span></span> | <span data-ttu-id="77d15-240">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-240">✔</span></span>
<span data-ttu-id="77d15-241">Geometry. CoveredBy (Geometria)</span><span class="sxs-lookup"><span data-stu-id="77d15-241">Geometry.CoveredBy(Geometry)</span></span> | | | <span data-ttu-id="77d15-242">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-242">✔</span></span> | <span data-ttu-id="77d15-243">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-243">✔</span></span>
<span data-ttu-id="77d15-244">Geometria. okładki (Geometria)</span><span class="sxs-lookup"><span data-stu-id="77d15-244">Geometry.Covers(Geometry)</span></span> | | | <span data-ttu-id="77d15-245">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-245">✔</span></span> | <span data-ttu-id="77d15-246">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-246">✔</span></span>
<span data-ttu-id="77d15-247">Geometry. crosss (Geometria)</span><span class="sxs-lookup"><span data-stu-id="77d15-247">Geometry.Crosses(Geometry)</span></span> | <span data-ttu-id="77d15-248">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-248">✔</span></span> | | <span data-ttu-id="77d15-249">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-249">✔</span></span> | <span data-ttu-id="77d15-250">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-250">✔</span></span>
<span data-ttu-id="77d15-251">Geometry. różnica (Geometria)</span><span class="sxs-lookup"><span data-stu-id="77d15-251">Geometry.Difference(Geometry)</span></span> | <span data-ttu-id="77d15-252">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-252">✔</span></span> | <span data-ttu-id="77d15-253">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-253">✔</span></span> | <span data-ttu-id="77d15-254">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-254">✔</span></span> | <span data-ttu-id="77d15-255">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-255">✔</span></span>
<span data-ttu-id="77d15-256">Geometria. wymiar</span><span class="sxs-lookup"><span data-stu-id="77d15-256">Geometry.Dimension</span></span> | <span data-ttu-id="77d15-257">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-257">✔</span></span> | <span data-ttu-id="77d15-258">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-258">✔</span></span> | <span data-ttu-id="77d15-259">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-259">✔</span></span> | <span data-ttu-id="77d15-260">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-260">✔</span></span>
<span data-ttu-id="77d15-261">Geometria. rozłączna (Geometria)</span><span class="sxs-lookup"><span data-stu-id="77d15-261">Geometry.Disjoint(Geometry)</span></span> | <span data-ttu-id="77d15-262">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-262">✔</span></span> | <span data-ttu-id="77d15-263">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-263">✔</span></span> | <span data-ttu-id="77d15-264">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-264">✔</span></span> | <span data-ttu-id="77d15-265">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-265">✔</span></span>
<span data-ttu-id="77d15-266">Geometria. Distance (Geometria)</span><span class="sxs-lookup"><span data-stu-id="77d15-266">Geometry.Distance(Geometry)</span></span> | <span data-ttu-id="77d15-267">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-267">✔</span></span> | <span data-ttu-id="77d15-268">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-268">✔</span></span> | <span data-ttu-id="77d15-269">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-269">✔</span></span> | <span data-ttu-id="77d15-270">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-270">✔</span></span>
<span data-ttu-id="77d15-271">Geometria. koperta</span><span class="sxs-lookup"><span data-stu-id="77d15-271">Geometry.Envelope</span></span> | <span data-ttu-id="77d15-272">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-272">✔</span></span> | | <span data-ttu-id="77d15-273">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-273">✔</span></span> | <span data-ttu-id="77d15-274">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-274">✔</span></span>
<span data-ttu-id="77d15-275">Geometry. EqualsExact (Geometria)</span><span class="sxs-lookup"><span data-stu-id="77d15-275">Geometry.EqualsExact(Geometry)</span></span> | | | | <span data-ttu-id="77d15-276">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-276">✔</span></span>
<span data-ttu-id="77d15-277">Geometry. EqualsTopologically (Geometria)</span><span class="sxs-lookup"><span data-stu-id="77d15-277">Geometry.EqualsTopologically(Geometry)</span></span> | <span data-ttu-id="77d15-278">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-278">✔</span></span> | <span data-ttu-id="77d15-279">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-279">✔</span></span> | <span data-ttu-id="77d15-280">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-280">✔</span></span> | <span data-ttu-id="77d15-281">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-281">✔</span></span>
<span data-ttu-id="77d15-282">Geometry. Geometrytype</span><span class="sxs-lookup"><span data-stu-id="77d15-282">Geometry.GeometryType</span></span> | <span data-ttu-id="77d15-283">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-283">✔</span></span> | <span data-ttu-id="77d15-284">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-284">✔</span></span> | <span data-ttu-id="77d15-285">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-285">✔</span></span> | <span data-ttu-id="77d15-286">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-286">✔</span></span>
<span data-ttu-id="77d15-287">Geometry. GetGeometryN (int)</span><span class="sxs-lookup"><span data-stu-id="77d15-287">Geometry.GetGeometryN(int)</span></span> | <span data-ttu-id="77d15-288">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-288">✔</span></span> | | <span data-ttu-id="77d15-289">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-289">✔</span></span> | <span data-ttu-id="77d15-290">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-290">✔</span></span>
<span data-ttu-id="77d15-291">Geometria. InteriorPoint</span><span class="sxs-lookup"><span data-stu-id="77d15-291">Geometry.InteriorPoint</span></span> | <span data-ttu-id="77d15-292">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-292">✔</span></span> | | <span data-ttu-id="77d15-293">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-293">✔</span></span> | <span data-ttu-id="77d15-294">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-294">✔</span></span>
<span data-ttu-id="77d15-295">Geometria. część wspólna (Geometria)</span><span class="sxs-lookup"><span data-stu-id="77d15-295">Geometry.Intersection(Geometry)</span></span> | <span data-ttu-id="77d15-296">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-296">✔</span></span> | <span data-ttu-id="77d15-297">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-297">✔</span></span> | <span data-ttu-id="77d15-298">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-298">✔</span></span> | <span data-ttu-id="77d15-299">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-299">✔</span></span>
<span data-ttu-id="77d15-300">Geometria. Intersects (Geometria)</span><span class="sxs-lookup"><span data-stu-id="77d15-300">Geometry.Intersects(Geometry)</span></span> | <span data-ttu-id="77d15-301">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-301">✔</span></span> | <span data-ttu-id="77d15-302">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-302">✔</span></span> | <span data-ttu-id="77d15-303">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-303">✔</span></span> | <span data-ttu-id="77d15-304">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-304">✔</span></span>
<span data-ttu-id="77d15-305">Geometria. IsEmpty</span><span class="sxs-lookup"><span data-stu-id="77d15-305">Geometry.IsEmpty</span></span> | <span data-ttu-id="77d15-306">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-306">✔</span></span> | <span data-ttu-id="77d15-307">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-307">✔</span></span> | <span data-ttu-id="77d15-308">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-308">✔</span></span> | <span data-ttu-id="77d15-309">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-309">✔</span></span>
<span data-ttu-id="77d15-310">Geometria. IsSimple</span><span class="sxs-lookup"><span data-stu-id="77d15-310">Geometry.IsSimple</span></span> | <span data-ttu-id="77d15-311">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-311">✔</span></span> | | <span data-ttu-id="77d15-312">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-312">✔</span></span> | <span data-ttu-id="77d15-313">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-313">✔</span></span>
<span data-ttu-id="77d15-314">Geometria. IsValid</span><span class="sxs-lookup"><span data-stu-id="77d15-314">Geometry.IsValid</span></span> | <span data-ttu-id="77d15-315">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-315">✔</span></span> | <span data-ttu-id="77d15-316">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-316">✔</span></span> | <span data-ttu-id="77d15-317">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-317">✔</span></span> | <span data-ttu-id="77d15-318">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-318">✔</span></span>
<span data-ttu-id="77d15-319">Geometry. IsWithinDistance (Geometria, Double)</span><span class="sxs-lookup"><span data-stu-id="77d15-319">Geometry.IsWithinDistance(Geometry, double)</span></span> | <span data-ttu-id="77d15-320">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-320">✔</span></span> | | <span data-ttu-id="77d15-321">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-321">✔</span></span> | <span data-ttu-id="77d15-322">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-322">✔</span></span>
<span data-ttu-id="77d15-323">Geometria. Długość</span><span class="sxs-lookup"><span data-stu-id="77d15-323">Geometry.Length</span></span> | <span data-ttu-id="77d15-324">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-324">✔</span></span> | <span data-ttu-id="77d15-325">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-325">✔</span></span> | <span data-ttu-id="77d15-326">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-326">✔</span></span> | <span data-ttu-id="77d15-327">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-327">✔</span></span>
<span data-ttu-id="77d15-328">Geometria. NumGeometries</span><span class="sxs-lookup"><span data-stu-id="77d15-328">Geometry.NumGeometries</span></span> | <span data-ttu-id="77d15-329">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-329">✔</span></span> | <span data-ttu-id="77d15-330">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-330">✔</span></span> | <span data-ttu-id="77d15-331">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-331">✔</span></span> | <span data-ttu-id="77d15-332">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-332">✔</span></span>
<span data-ttu-id="77d15-333">Geometria. NumPoints</span><span class="sxs-lookup"><span data-stu-id="77d15-333">Geometry.NumPoints</span></span> | <span data-ttu-id="77d15-334">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-334">✔</span></span> | <span data-ttu-id="77d15-335">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-335">✔</span></span> | <span data-ttu-id="77d15-336">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-336">✔</span></span> | <span data-ttu-id="77d15-337">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-337">✔</span></span>
<span data-ttu-id="77d15-338">Geometria. OgcGeometryType</span><span class="sxs-lookup"><span data-stu-id="77d15-338">Geometry.OgcGeometryType</span></span> | <span data-ttu-id="77d15-339">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-339">✔</span></span> | <span data-ttu-id="77d15-340">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-340">✔</span></span> | <span data-ttu-id="77d15-341">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-341">✔</span></span> | <span data-ttu-id="77d15-342">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-342">✔</span></span>
<span data-ttu-id="77d15-343">Geometria. nakładanie się (Geometria)</span><span class="sxs-lookup"><span data-stu-id="77d15-343">Geometry.Overlaps(Geometry)</span></span> | <span data-ttu-id="77d15-344">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-344">✔</span></span> | <span data-ttu-id="77d15-345">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-345">✔</span></span> | <span data-ttu-id="77d15-346">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-346">✔</span></span> | <span data-ttu-id="77d15-347">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-347">✔</span></span>
<span data-ttu-id="77d15-348">Geometria. PointOnSurface</span><span class="sxs-lookup"><span data-stu-id="77d15-348">Geometry.PointOnSurface</span></span> | <span data-ttu-id="77d15-349">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-349">✔</span></span> | | <span data-ttu-id="77d15-350">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-350">✔</span></span> | <span data-ttu-id="77d15-351">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-351">✔</span></span>
<span data-ttu-id="77d15-352">Geometry. rerelacja (Geometria, ciąg)</span><span class="sxs-lookup"><span data-stu-id="77d15-352">Geometry.Relate(Geometry, string)</span></span> | <span data-ttu-id="77d15-353">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-353">✔</span></span> | | <span data-ttu-id="77d15-354">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-354">✔</span></span> | <span data-ttu-id="77d15-355">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-355">✔</span></span>
<span data-ttu-id="77d15-356">Geometry. Reverse ()</span><span class="sxs-lookup"><span data-stu-id="77d15-356">Geometry.Reverse()</span></span> | | | <span data-ttu-id="77d15-357">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-357">✔</span></span> | <span data-ttu-id="77d15-358">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-358">✔</span></span>
<span data-ttu-id="77d15-359">Geometria. SRID</span><span class="sxs-lookup"><span data-stu-id="77d15-359">Geometry.SRID</span></span> | <span data-ttu-id="77d15-360">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-360">✔</span></span> | <span data-ttu-id="77d15-361">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-361">✔</span></span> | <span data-ttu-id="77d15-362">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-362">✔</span></span> | <span data-ttu-id="77d15-363">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-363">✔</span></span>
<span data-ttu-id="77d15-364">Geometry. SymmetricDifference (Geometria)</span><span class="sxs-lookup"><span data-stu-id="77d15-364">Geometry.SymmetricDifference(Geometry)</span></span> | <span data-ttu-id="77d15-365">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-365">✔</span></span> | <span data-ttu-id="77d15-366">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-366">✔</span></span> | <span data-ttu-id="77d15-367">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-367">✔</span></span> | <span data-ttu-id="77d15-368">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-368">✔</span></span>
<span data-ttu-id="77d15-369">Geometry. ToBinary ()</span><span class="sxs-lookup"><span data-stu-id="77d15-369">Geometry.ToBinary()</span></span> | <span data-ttu-id="77d15-370">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-370">✔</span></span> | <span data-ttu-id="77d15-371">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-371">✔</span></span> | <span data-ttu-id="77d15-372">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-372">✔</span></span> | <span data-ttu-id="77d15-373">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-373">✔</span></span>
<span data-ttu-id="77d15-374">Geometry. ToText ()</span><span class="sxs-lookup"><span data-stu-id="77d15-374">Geometry.ToText()</span></span> | <span data-ttu-id="77d15-375">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-375">✔</span></span> | <span data-ttu-id="77d15-376">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-376">✔</span></span> | <span data-ttu-id="77d15-377">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-377">✔</span></span> | <span data-ttu-id="77d15-378">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-378">✔</span></span>
<span data-ttu-id="77d15-379">Geometria. touch (Geometria)</span><span class="sxs-lookup"><span data-stu-id="77d15-379">Geometry.Touches(Geometry)</span></span> | <span data-ttu-id="77d15-380">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-380">✔</span></span> | | <span data-ttu-id="77d15-381">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-381">✔</span></span> | <span data-ttu-id="77d15-382">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-382">✔</span></span>
<span data-ttu-id="77d15-383">Geometry. Union ()</span><span class="sxs-lookup"><span data-stu-id="77d15-383">Geometry.Union()</span></span> | | | <span data-ttu-id="77d15-384">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-384">✔</span></span> | <span data-ttu-id="77d15-385">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-385">✔</span></span>
<span data-ttu-id="77d15-386">Geometry. Union (Geometria)</span><span class="sxs-lookup"><span data-stu-id="77d15-386">Geometry.Union(Geometry)</span></span> | <span data-ttu-id="77d15-387">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-387">✔</span></span> | <span data-ttu-id="77d15-388">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-388">✔</span></span> | <span data-ttu-id="77d15-389">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-389">✔</span></span> | <span data-ttu-id="77d15-390">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-390">✔</span></span>
<span data-ttu-id="77d15-391">Geometria. w elemencie (Geometria)</span><span class="sxs-lookup"><span data-stu-id="77d15-391">Geometry.Within(Geometry)</span></span> | <span data-ttu-id="77d15-392">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-392">✔</span></span> | <span data-ttu-id="77d15-393">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-393">✔</span></span> | <span data-ttu-id="77d15-394">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-394">✔</span></span> | <span data-ttu-id="77d15-395">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-395">✔</span></span>
<span data-ttu-id="77d15-396">GeometryCollection. Count</span><span class="sxs-lookup"><span data-stu-id="77d15-396">GeometryCollection.Count</span></span> | <span data-ttu-id="77d15-397">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-397">✔</span></span> | <span data-ttu-id="77d15-398">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-398">✔</span></span> | <span data-ttu-id="77d15-399">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-399">✔</span></span> | <span data-ttu-id="77d15-400">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-400">✔</span></span>
<span data-ttu-id="77d15-401">GeometryCollection [int]</span><span class="sxs-lookup"><span data-stu-id="77d15-401">GeometryCollection[int]</span></span> | <span data-ttu-id="77d15-402">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-402">✔</span></span> | <span data-ttu-id="77d15-403">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-403">✔</span></span> | <span data-ttu-id="77d15-404">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-404">✔</span></span> | <span data-ttu-id="77d15-405">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-405">✔</span></span>
<span data-ttu-id="77d15-406">LineString. Count</span><span class="sxs-lookup"><span data-stu-id="77d15-406">LineString.Count</span></span> | <span data-ttu-id="77d15-407">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-407">✔</span></span> | <span data-ttu-id="77d15-408">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-408">✔</span></span> | <span data-ttu-id="77d15-409">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-409">✔</span></span> | <span data-ttu-id="77d15-410">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-410">✔</span></span>
<span data-ttu-id="77d15-411">LineString. EndPoint</span><span class="sxs-lookup"><span data-stu-id="77d15-411">LineString.EndPoint</span></span> | <span data-ttu-id="77d15-412">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-412">✔</span></span> | <span data-ttu-id="77d15-413">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-413">✔</span></span> | <span data-ttu-id="77d15-414">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-414">✔</span></span> | <span data-ttu-id="77d15-415">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-415">✔</span></span>
<span data-ttu-id="77d15-416">LineString. GetPointN (int)</span><span class="sxs-lookup"><span data-stu-id="77d15-416">LineString.GetPointN(int)</span></span> | <span data-ttu-id="77d15-417">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-417">✔</span></span> | <span data-ttu-id="77d15-418">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-418">✔</span></span> | <span data-ttu-id="77d15-419">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-419">✔</span></span> | <span data-ttu-id="77d15-420">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-420">✔</span></span>
<span data-ttu-id="77d15-421">LineString. IsClosed</span><span class="sxs-lookup"><span data-stu-id="77d15-421">LineString.IsClosed</span></span> | <span data-ttu-id="77d15-422">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-422">✔</span></span> | <span data-ttu-id="77d15-423">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-423">✔</span></span> | <span data-ttu-id="77d15-424">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-424">✔</span></span> | <span data-ttu-id="77d15-425">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-425">✔</span></span>
<span data-ttu-id="77d15-426">LineString. IsRing</span><span class="sxs-lookup"><span data-stu-id="77d15-426">LineString.IsRing</span></span> | <span data-ttu-id="77d15-427">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-427">✔</span></span> | | <span data-ttu-id="77d15-428">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-428">✔</span></span> | <span data-ttu-id="77d15-429">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-429">✔</span></span>
<span data-ttu-id="77d15-430">LineString. StartPoint</span><span class="sxs-lookup"><span data-stu-id="77d15-430">LineString.StartPoint</span></span> | <span data-ttu-id="77d15-431">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-431">✔</span></span> | <span data-ttu-id="77d15-432">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-432">✔</span></span> | <span data-ttu-id="77d15-433">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-433">✔</span></span> | <span data-ttu-id="77d15-434">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-434">✔</span></span>
<span data-ttu-id="77d15-435">MultiLineString. IsClosed</span><span class="sxs-lookup"><span data-stu-id="77d15-435">MultiLineString.IsClosed</span></span> | <span data-ttu-id="77d15-436">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-436">✔</span></span> | <span data-ttu-id="77d15-437">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-437">✔</span></span> | <span data-ttu-id="77d15-438">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-438">✔</span></span> | <span data-ttu-id="77d15-439">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-439">✔</span></span>
<span data-ttu-id="77d15-440">Punkt. M</span><span class="sxs-lookup"><span data-stu-id="77d15-440">Point.M</span></span> | <span data-ttu-id="77d15-441">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-441">✔</span></span> | <span data-ttu-id="77d15-442">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-442">✔</span></span> | <span data-ttu-id="77d15-443">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-443">✔</span></span> | <span data-ttu-id="77d15-444">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-444">✔</span></span>
<span data-ttu-id="77d15-445">Punkt. X</span><span class="sxs-lookup"><span data-stu-id="77d15-445">Point.X</span></span> | <span data-ttu-id="77d15-446">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-446">✔</span></span> | <span data-ttu-id="77d15-447">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-447">✔</span></span> | <span data-ttu-id="77d15-448">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-448">✔</span></span> | <span data-ttu-id="77d15-449">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-449">✔</span></span>
<span data-ttu-id="77d15-450">Punkt. Y</span><span class="sxs-lookup"><span data-stu-id="77d15-450">Point.Y</span></span> | <span data-ttu-id="77d15-451">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-451">✔</span></span> | <span data-ttu-id="77d15-452">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-452">✔</span></span> | <span data-ttu-id="77d15-453">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-453">✔</span></span> | <span data-ttu-id="77d15-454">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-454">✔</span></span>
<span data-ttu-id="77d15-455">Punkt. Z</span><span class="sxs-lookup"><span data-stu-id="77d15-455">Point.Z</span></span> | <span data-ttu-id="77d15-456">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-456">✔</span></span> | <span data-ttu-id="77d15-457">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-457">✔</span></span> | <span data-ttu-id="77d15-458">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-458">✔</span></span> | <span data-ttu-id="77d15-459">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-459">✔</span></span>
<span data-ttu-id="77d15-460">Wielokąt. ExteriorRing</span><span class="sxs-lookup"><span data-stu-id="77d15-460">Polygon.ExteriorRing</span></span> | <span data-ttu-id="77d15-461">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-461">✔</span></span> | <span data-ttu-id="77d15-462">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-462">✔</span></span> | <span data-ttu-id="77d15-463">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-463">✔</span></span> | <span data-ttu-id="77d15-464">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-464">✔</span></span>
<span data-ttu-id="77d15-465">Wielokąt. GetInteriorRingN (int)</span><span class="sxs-lookup"><span data-stu-id="77d15-465">Polygon.GetInteriorRingN(int)</span></span> | <span data-ttu-id="77d15-466">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-466">✔</span></span> | <span data-ttu-id="77d15-467">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-467">✔</span></span> | <span data-ttu-id="77d15-468">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-468">✔</span></span> | <span data-ttu-id="77d15-469">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-469">✔</span></span>
<span data-ttu-id="77d15-470">Wielokąt. NumInteriorRings</span><span class="sxs-lookup"><span data-stu-id="77d15-470">Polygon.NumInteriorRings</span></span> | <span data-ttu-id="77d15-471">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-471">✔</span></span> | <span data-ttu-id="77d15-472">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-472">✔</span></span> | <span data-ttu-id="77d15-473">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-473">✔</span></span> | <span data-ttu-id="77d15-474">✔</span><span class="sxs-lookup"><span data-stu-id="77d15-474">✔</span></span>

## <a name="additional-resources"></a><span data-ttu-id="77d15-475">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="77d15-475">Additional resources</span></span>

* [<span data-ttu-id="77d15-476">Dane przestrzenne w SQL Server</span><span class="sxs-lookup"><span data-stu-id="77d15-476">Spatial Data in SQL Server</span></span>](/sql/relational-databases/spatial/spatial-data-sql-server)
* [<span data-ttu-id="77d15-477">Strona główna SpatiaLite</span><span class="sxs-lookup"><span data-stu-id="77d15-477">SpatiaLite Homepage</span></span>](https://www.gaia-gis.it/fossil/libspatialite)
* [<span data-ttu-id="77d15-478">Dokumentacja przestrzenna Npgsql</span><span class="sxs-lookup"><span data-stu-id="77d15-478">Npgsql Spatial Documentation</span></span>](https://www.npgsql.org/efcore/mapping/nts.html)
* [<span data-ttu-id="77d15-479">Dokumentacja PostGIS</span><span class="sxs-lookup"><span data-stu-id="77d15-479">PostGIS Documentation</span></span>](https://postgis.net/documentation/)
