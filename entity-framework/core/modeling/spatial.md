---
title: Dane przestrzenne — EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/01/2018
ms.assetid: 2BDE29FC-4161-41A0-841E-69F51CCD9341
uid: core/modeling/spatial
ms.openlocfilehash: 8dae1ab949c77ffa08904b12a5716b729e6913a1
ms.sourcegitcommit: 32c51c22988c6f83ed4f8e50a1d01be3f4114e81
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/27/2019
ms.locfileid: "75502243"
---
# <a name="spatial-data"></a><span data-ttu-id="95aa6-102">Dane przestrzenne</span><span class="sxs-lookup"><span data-stu-id="95aa6-102">Spatial Data</span></span>

> [!NOTE]
> <span data-ttu-id="95aa6-103">Ta funkcja została dodana w EF Core 2,2.</span><span class="sxs-lookup"><span data-stu-id="95aa6-103">This feature was added in EF Core 2.2.</span></span>

<span data-ttu-id="95aa6-104">Dane przestrzenne reprezentują lokalizację fizyczną i kształt obiektów.</span><span class="sxs-lookup"><span data-stu-id="95aa6-104">Spatial data represents the physical location and the shape of objects.</span></span> <span data-ttu-id="95aa6-105">Wiele baz danych zapewnia obsługę tego typu danych, dzięki czemu można je indeksować i badać wraz z innymi danymi.</span><span class="sxs-lookup"><span data-stu-id="95aa6-105">Many databases provide support for this type of data so it can be indexed and queried alongside other data.</span></span> <span data-ttu-id="95aa6-106">Typowe scenariusze obejmują zapytania dotyczące obiektów w danej odległości od lokalizacji lub wybierając obiekt, którego obramowanie zawiera daną lokalizację.</span><span class="sxs-lookup"><span data-stu-id="95aa6-106">Common scenarios include querying for objects within a given distance from a location, or selecting the object whose border contains a given location.</span></span> <span data-ttu-id="95aa6-107">EF Core obsługuje mapowanie do typów danych przestrzennych przy użyciu biblioteki przestrzennej [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) .</span><span class="sxs-lookup"><span data-stu-id="95aa6-107">EF Core supports mapping to spatial data types using the [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) spatial library.</span></span>

## <a name="installing"></a><span data-ttu-id="95aa6-108">Instalowanie programu</span><span class="sxs-lookup"><span data-stu-id="95aa6-108">Installing</span></span>

<span data-ttu-id="95aa6-109">Aby można było używać danych przestrzennych z EF Core, należy zainstalować odpowiedni pomocniczy pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="95aa6-109">In order to use spatial data with EF Core, you need to install the appropriate supporting NuGet package.</span></span> <span data-ttu-id="95aa6-110">Który pakiet należy zainstalować zależy od dostawcy, którego używasz.</span><span class="sxs-lookup"><span data-stu-id="95aa6-110">Which package you need to install depends on the provider you're using.</span></span>

<span data-ttu-id="95aa6-111">Dostawca platformy EF Core</span><span class="sxs-lookup"><span data-stu-id="95aa6-111">EF Core Provider</span></span>                        | <span data-ttu-id="95aa6-112">Przestrzenny pakiet NuGet</span><span class="sxs-lookup"><span data-stu-id="95aa6-112">Spatial NuGet Package</span></span>
--------------------------------------- | ---------------------
<span data-ttu-id="95aa6-113">Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="95aa6-113">Microsoft.EntityFrameworkCore.SqlServer</span></span> | [<span data-ttu-id="95aa6-114">Microsoft. EntityFrameworkCore. SqlServer. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="95aa6-114">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
<span data-ttu-id="95aa6-115">Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="95aa6-115">Microsoft.EntityFrameworkCore.Sqlite</span></span>    | [<span data-ttu-id="95aa6-116">Microsoft. EntityFrameworkCore. sqlite. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="95aa6-116">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
<span data-ttu-id="95aa6-117">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="95aa6-117">Microsoft.EntityFrameworkCore.InMemory</span></span>  | [<span data-ttu-id="95aa6-118">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="95aa6-118">NetTopologySuite</span></span>](https://www.nuget.org/packages/NetTopologySuite)
<span data-ttu-id="95aa6-119">Npgsql.EntityFrameworkCore.PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="95aa6-119">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>   | [<span data-ttu-id="95aa6-120">Npgsql. EntityFrameworkCore. PostgreSQL. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="95aa6-120">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)

## <a name="reverse-engineering"></a><span data-ttu-id="95aa6-121">Odwrócenie inżynierii</span><span class="sxs-lookup"><span data-stu-id="95aa6-121">Reverse engineering</span></span>

<span data-ttu-id="95aa6-122">Przestrzenne pakiety NuGet umożliwiają również [Tworzenie modeli odtwarzania](../managing-schemas/scaffolding.md) z właściwościami przestrzennymi, ale należy zainstalować pakiet ***przed*** uruchomieniem `Scaffold-DbContext` lub `dotnet ef dbcontext scaffold`.</span><span class="sxs-lookup"><span data-stu-id="95aa6-122">The spatial NuGet packages also enable [reverse engineering](../managing-schemas/scaffolding.md) models with spatial properties, but you need to install the package ***before*** running `Scaffold-DbContext` or `dotnet ef dbcontext scaffold`.</span></span> <span data-ttu-id="95aa6-123">W przeciwnym razie otrzymasz ostrzeżenia dotyczące nieznajdowania mapowań typów dla kolumn, a kolumny zostaną pominięte.</span><span class="sxs-lookup"><span data-stu-id="95aa6-123">If you don't, you'll receive warnings about not finding type mappings for the columns and the columns will be skipped.</span></span>

## <a name="nettopologysuite-nts"></a><span data-ttu-id="95aa6-124">NetTopologySuite (NKTY przerwania)</span><span class="sxs-lookup"><span data-stu-id="95aa6-124">NetTopologySuite (NTS)</span></span>

<span data-ttu-id="95aa6-125">NetTopologySuite jest biblioteką przestrzenną dla platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="95aa6-125">NetTopologySuite is a spatial library for .NET.</span></span> <span data-ttu-id="95aa6-126">EF Core umożliwia mapowanie do typów danych przestrzennych w bazie danych przy użyciu typów NKTY przerwania w modelu.</span><span class="sxs-lookup"><span data-stu-id="95aa6-126">EF Core enables mapping to spatial data types in the database by using NTS types in your model.</span></span>

<span data-ttu-id="95aa6-127">Aby włączyć mapowanie do typów przestrzennych za pośrednictwem NKTY przerwania, wywołaj metodę UseNetTopologySuite w konstruktorze opcji DbContext dostawcy.</span><span class="sxs-lookup"><span data-stu-id="95aa6-127">To enable mapping to spatial types via NTS, call the UseNetTopologySuite method on the provider's DbContext options builder.</span></span> <span data-ttu-id="95aa6-128">Na przykład w przypadku SQL Server należy wywołać tę metodę.</span><span class="sxs-lookup"><span data-stu-id="95aa6-128">For example, with SQL Server you'd call it like this.</span></span>

``` csharp
optionsBuilder.UseSqlServer(
    @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=WideWorldImporters",
    x => x.UseNetTopologySuite());
```

<span data-ttu-id="95aa6-129">Istnieje kilka typów danych przestrzennych.</span><span class="sxs-lookup"><span data-stu-id="95aa6-129">There are several spatial data types.</span></span> <span data-ttu-id="95aa6-130">Używany typ zależy od typów kształtów, które mają być dozwolone.</span><span class="sxs-lookup"><span data-stu-id="95aa6-130">Which type you use depends on the types of shapes you want to allow.</span></span> <span data-ttu-id="95aa6-131">Poniżej znajduje się hierarchia typów NKTY przerwania, których można użyć do właściwości w modelu.</span><span class="sxs-lookup"><span data-stu-id="95aa6-131">Here is the hierarchy of NTS types that you can use for properties in your model.</span></span> <span data-ttu-id="95aa6-132">Znajdują się one w przestrzeni nazw `NetTopologySuite.Geometries`.</span><span class="sxs-lookup"><span data-stu-id="95aa6-132">They're located within the `NetTopologySuite.Geometries` namespace.</span></span>

* <span data-ttu-id="95aa6-133">Geometrii</span><span class="sxs-lookup"><span data-stu-id="95aa6-133">Geometry</span></span>
  * <span data-ttu-id="95aa6-134">Punkt</span><span class="sxs-lookup"><span data-stu-id="95aa6-134">Point</span></span>
  * <span data-ttu-id="95aa6-135">LineString</span><span class="sxs-lookup"><span data-stu-id="95aa6-135">LineString</span></span>
  * <span data-ttu-id="95aa6-136">Tworząc</span><span class="sxs-lookup"><span data-stu-id="95aa6-136">Polygon</span></span>
  * <span data-ttu-id="95aa6-137">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="95aa6-137">GeometryCollection</span></span>
    * <span data-ttu-id="95aa6-138">MultiPoint</span><span class="sxs-lookup"><span data-stu-id="95aa6-138">MultiPoint</span></span>
    * <span data-ttu-id="95aa6-139">MultiLineString</span><span class="sxs-lookup"><span data-stu-id="95aa6-139">MultiLineString</span></span>
    * <span data-ttu-id="95aa6-140">MultiPolygon</span><span class="sxs-lookup"><span data-stu-id="95aa6-140">MultiPolygon</span></span>

> [!WARNING]
> <span data-ttu-id="95aa6-141">CircularString, CompoundCurve i CurePolygon nie są obsługiwane przez NKTY przerwania.</span><span class="sxs-lookup"><span data-stu-id="95aa6-141">CircularString, CompoundCurve, and CurePolygon aren't supported by NTS.</span></span>

<span data-ttu-id="95aa6-142">Użycie podstawowego typu geometrii umożliwia określenie dowolnego typu kształtu przez właściwość.</span><span class="sxs-lookup"><span data-stu-id="95aa6-142">Using the base Geometry type allows any type of shape to be specified by the property.</span></span>

<span data-ttu-id="95aa6-143">Poniższe klasy jednostek mogą służyć do mapowania tabel w [przykładowej bazie danych](https://go.microsoft.com/fwlink/?LinkID=800630)na całym świecie.</span><span class="sxs-lookup"><span data-stu-id="95aa6-143">The following entity classes could be used to map to tables in the [Wide World Importers sample database](https://go.microsoft.com/fwlink/?LinkID=800630).</span></span>

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

### <a name="creating-values"></a><span data-ttu-id="95aa6-144">Tworzenie wartości</span><span class="sxs-lookup"><span data-stu-id="95aa6-144">Creating values</span></span>

<span data-ttu-id="95aa6-145">Można użyć konstruktorów do tworzenia obiektów geometrycznych; jednak NKTY przerwania zaleca się użycie fabryki geometrycznej.</span><span class="sxs-lookup"><span data-stu-id="95aa6-145">You can use constructors to create geometry objects; however, NTS recommends using a geometry factory instead.</span></span> <span data-ttu-id="95aa6-146">Pozwala to określić domyślny SRID (System referencyjny przestrzennej używany przez współrzędne) i zapewnia kontrolę nad bardziej zaawansowanymi elementami, takimi jak model precyzji (używany podczas obliczeń) i sekwencją współrzędnych (określa, które współrzędne są wymiarami i miary--są dostępne).</span><span class="sxs-lookup"><span data-stu-id="95aa6-146">This lets you specify a default SRID (the spatial reference system used by the coordinates) and gives you control over more advanced things like the precision model (used during calculations) and the coordinate sequence (determines which ordinates--dimensions and measures--are available).</span></span>

``` csharp
var geometryFactory = NtsGeometryServices.Instance.CreateGeometryFactory(srid: 4326);
var currentLocation = geometryFactory.CreatePoint(-122.121512, 47.6739882);
```

> [!NOTE]
> <span data-ttu-id="95aa6-147">4326 dotyczy WGS 84, standardowego używanego w systemach GPS i innych systemów geograficznych.</span><span class="sxs-lookup"><span data-stu-id="95aa6-147">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

### <a name="longitude-and-latitude"></a><span data-ttu-id="95aa6-148">Długości i szerokości geograficznej</span><span class="sxs-lookup"><span data-stu-id="95aa6-148">Longitude and Latitude</span></span>

<span data-ttu-id="95aa6-149">Współrzędne w NKTY przerwania są pod względem wartości X i Y.</span><span class="sxs-lookup"><span data-stu-id="95aa6-149">Coordinates in NTS are in terms of X and Y values.</span></span> <span data-ttu-id="95aa6-150">Aby przedstawić długość i szerokość geograficzną, użyj X dla długości geograficznej i Y dla szerokości geograficznej.</span><span class="sxs-lookup"><span data-stu-id="95aa6-150">To represent longitude and latitude, use X for longitude and Y for latitude.</span></span> <span data-ttu-id="95aa6-151">Należy pamiętać, że jest to **wsteczne** w formacie `latitude, longitude`, w którym są zwykle wyświetlane te wartości.</span><span class="sxs-lookup"><span data-stu-id="95aa6-151">Note that this is **backwards** from the `latitude, longitude` format in which you typically see these values.</span></span>

### <a name="srid-ignored-during-client-operations"></a><span data-ttu-id="95aa6-152">SRID zignorowane podczas operacji klienta</span><span class="sxs-lookup"><span data-stu-id="95aa6-152">SRID Ignored during client operations</span></span>

<span data-ttu-id="95aa6-153">NKTY przerwania ignoruje wartości SRID podczas operacji.</span><span class="sxs-lookup"><span data-stu-id="95aa6-153">NTS ignores SRID values during operations.</span></span> <span data-ttu-id="95aa6-154">Przyjęto założenie układu współrzędnych.</span><span class="sxs-lookup"><span data-stu-id="95aa6-154">It assumes a planar coordinate system.</span></span> <span data-ttu-id="95aa6-155">Oznacza to, że jeśli określisz współrzędne w zakresie długości i szerokości geograficznej, niektóre wartości oceniane przez klienta, takie jak odległość, długość i obszar, będą znajdować się w stopniach, a nie w metrach.</span><span class="sxs-lookup"><span data-stu-id="95aa6-155">This means that if you specify coordinates in terms of longitude and latitude, some client-evaluated values like distance, length, and area will be in degrees, not meters.</span></span> <span data-ttu-id="95aa6-156">Aby uzyskać bardziej zrozumiałe wartości, najpierw należy umieścić współrzędne w innym układzie współrzędnych przy użyciu biblioteki, takiej jak [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) przed obliczeniem tych wartości.</span><span class="sxs-lookup"><span data-stu-id="95aa6-156">For more meaningful values, you first need to project the coordinates to another coordinate system using a library like [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) before calculating these values.</span></span>

<span data-ttu-id="95aa6-157">Jeśli operacja jest Szacowana przez serwer EF Core za pośrednictwem SQL, jednostka wyniku zostanie określona przez bazę danych.</span><span class="sxs-lookup"><span data-stu-id="95aa6-157">If an operation is server-evaluated by EF Core via SQL, the result's unit will be determined by the database.</span></span>

<span data-ttu-id="95aa6-158">Oto przykład użycia ProjNet4GeoAPI do obliczenia odległości między dwoma miastami.</span><span class="sxs-lookup"><span data-stu-id="95aa6-158">Here is an example of using ProjNet4GeoAPI to calculate the distance between two cities.</span></span>

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

## <a name="querying-data"></a><span data-ttu-id="95aa6-159">Wykonanie zapytania o dane</span><span class="sxs-lookup"><span data-stu-id="95aa6-159">Querying Data</span></span>

<span data-ttu-id="95aa6-160">W LINQ, metody i właściwości NKTY przerwania dostępne jako funkcje bazy danych zostaną przetłumaczone na SQL.</span><span class="sxs-lookup"><span data-stu-id="95aa6-160">In LINQ, the NTS methods and properties available as database functions will be translated to SQL.</span></span> <span data-ttu-id="95aa6-161">Na przykład odległość i zawiera metody są tłumaczone w poniższych zapytaniach.</span><span class="sxs-lookup"><span data-stu-id="95aa6-161">For example, the Distance and Contains methods are translated in the following queries.</span></span> <span data-ttu-id="95aa6-162">W tabeli na końcu tego artykułu przedstawiono, które elementy członkowskie są obsługiwane przez różnych dostawców EF Core.</span><span class="sxs-lookup"><span data-stu-id="95aa6-162">The table at the end of this article shows which members are supported by various EF Core providers.</span></span>

``` csharp
var nearestCity = db.Cities
    .OrderBy(c => c.Location.Distance(currentLocation))
    .FirstOrDefault();

var currentCountry = db.Countries
    .FirstOrDefault(c => c.Border.Contains(currentLocation));
```

## <a name="sql-server"></a><span data-ttu-id="95aa6-163">Serwer SQL</span><span class="sxs-lookup"><span data-stu-id="95aa6-163">SQL Server</span></span>

<span data-ttu-id="95aa6-164">Jeśli używasz SQL Server, musisz wiedzieć o kilku dodatkowych kwestiach.</span><span class="sxs-lookup"><span data-stu-id="95aa6-164">If you're using SQL Server, there are some additional things you should be aware of.</span></span>

### <a name="geography-or-geometry"></a><span data-ttu-id="95aa6-165">Geografia lub geometria</span><span class="sxs-lookup"><span data-stu-id="95aa6-165">Geography or geometry</span></span>

<span data-ttu-id="95aa6-166">Domyślnie właściwości przestrzenne są mapowane do `geography` kolumn w SQL Server.</span><span class="sxs-lookup"><span data-stu-id="95aa6-166">By default, spatial properties are mapped to `geography` columns in SQL Server.</span></span> <span data-ttu-id="95aa6-167">Aby użyć `geometry`, należy [skonfigurować typ kolumny](xref:core/modeling/entity-properties#column-data-types) w modelu.</span><span class="sxs-lookup"><span data-stu-id="95aa6-167">To use `geometry`, [configure the column type](xref:core/modeling/entity-properties#column-data-types) in your model.</span></span>

### <a name="geography-polygon-rings"></a><span data-ttu-id="95aa6-168">Pierścienie wielokątów geograficznych</span><span class="sxs-lookup"><span data-stu-id="95aa6-168">Geography polygon rings</span></span>

<span data-ttu-id="95aa6-169">W przypadku używania `geography` typu kolumny SQL Server nakładają dodatkowe wymagania dotyczące pierścienia zewnętrznego (lub powłoki) i wewnętrznych pierścieni (lub dziur).</span><span class="sxs-lookup"><span data-stu-id="95aa6-169">When using the `geography` column type, SQL Server imposes additional requirements on the exterior ring (or shell) and interior rings (or holes).</span></span> <span data-ttu-id="95aa6-170">Pierścień zewnętrzny musi być zorientowany w lewo i w prawo.</span><span class="sxs-lookup"><span data-stu-id="95aa6-170">The exterior ring must be oriented counterclockwise and the interior rings clockwise.</span></span> <span data-ttu-id="95aa6-171">NKTY przerwania sprawdza to przed wysłaniem wartości do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="95aa6-171">NTS validates this before sending values to the database.</span></span>

### <a name="fullglobe"></a><span data-ttu-id="95aa6-172">FullGlobe</span><span class="sxs-lookup"><span data-stu-id="95aa6-172">FullGlobe</span></span>

<span data-ttu-id="95aa6-173">SQL Server ma niestandardowy typ geometrii reprezentujący pełny Globus przy użyciu typu kolumny `geography`.</span><span class="sxs-lookup"><span data-stu-id="95aa6-173">SQL Server has a non-standard geometry type to represent the full globe when using the `geography` column type.</span></span> <span data-ttu-id="95aa6-174">Ma również sposób reprezentowania wielokątów w oparciu o pełny Globus (bez pierścienia zewnętrznego).</span><span class="sxs-lookup"><span data-stu-id="95aa6-174">It also has a way to represent polygons based on the full globe (without an exterior ring).</span></span> <span data-ttu-id="95aa6-175">Żadna z tych elementów nie jest obsługiwana przez NKTY przerwania.</span><span class="sxs-lookup"><span data-stu-id="95aa6-175">Neither of these are supported by NTS.</span></span>

> [!WARNING]
> <span data-ttu-id="95aa6-176">FullGlobe i wielokąty oparte na nim nie są obsługiwane przez NKTY przerwania.</span><span class="sxs-lookup"><span data-stu-id="95aa6-176">FullGlobe and polygons based on it aren't supported by NTS.</span></span>

## <a name="sqlite"></a><span data-ttu-id="95aa6-177">SQLite</span><span class="sxs-lookup"><span data-stu-id="95aa6-177">SQLite</span></span>

<span data-ttu-id="95aa6-178">Poniżej przedstawiono dodatkowe informacje dotyczące tych, które są używane przez program SQLite.</span><span class="sxs-lookup"><span data-stu-id="95aa6-178">Here is some additional information for those using SQLite.</span></span>

### <a name="installing-spatialite"></a><span data-ttu-id="95aa6-179">Instalowanie SpatiaLite</span><span class="sxs-lookup"><span data-stu-id="95aa6-179">Installing SpatiaLite</span></span>

<span data-ttu-id="95aa6-180">W systemie Windows natywna biblioteka mod_spatialite jest dystrybuowana jako zależność pakietu NuGet.</span><span class="sxs-lookup"><span data-stu-id="95aa6-180">On Windows, the native mod_spatialite library is distributed as a NuGet package dependency.</span></span> <span data-ttu-id="95aa6-181">Inne platformy muszą zainstalować je oddzielnie.</span><span class="sxs-lookup"><span data-stu-id="95aa6-181">Other platforms need to install it separately.</span></span> <span data-ttu-id="95aa6-182">Jest to zazwyczaj realizowane przy użyciu Menedżera pakietów oprogramowania.</span><span class="sxs-lookup"><span data-stu-id="95aa6-182">This is typically done using a software package manager.</span></span> <span data-ttu-id="95aa6-183">Na przykład można użyć APT w Ubuntu i oprogramowania Homebrew na MacOS.</span><span class="sxs-lookup"><span data-stu-id="95aa6-183">For example, you can use APT on Ubuntu and Homebrew on MacOS.</span></span>

``` sh
# Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

### <a name="configuring-srid"></a><span data-ttu-id="95aa6-184">Konfigurowanie SRID</span><span class="sxs-lookup"><span data-stu-id="95aa6-184">Configuring SRID</span></span>

<span data-ttu-id="95aa6-185">W SpatiaLite, kolumny muszą określać SRID na kolumnę.</span><span class="sxs-lookup"><span data-stu-id="95aa6-185">In SpatiaLite, columns need to specify an SRID per column.</span></span> <span data-ttu-id="95aa6-186">Domyślny SRID jest `0`.</span><span class="sxs-lookup"><span data-stu-id="95aa6-186">The default SRID is `0`.</span></span> <span data-ttu-id="95aa6-187">Określ inny SRID przy użyciu metody ForSqliteHasSrid.</span><span class="sxs-lookup"><span data-stu-id="95aa6-187">Specify a different SRID using the ForSqliteHasSrid method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

### <a name="dimension"></a><span data-ttu-id="95aa6-188">Wymiar</span><span class="sxs-lookup"><span data-stu-id="95aa6-188">Dimension</span></span>

<span data-ttu-id="95aa6-189">Podobnie jak w przypadku SRID, wymiar kolumny (lub współrzędnej) jest również określony jako część kolumny.</span><span class="sxs-lookup"><span data-stu-id="95aa6-189">Similar to SRID, a column's dimension (or ordinates) is also specified as part of the column.</span></span> <span data-ttu-id="95aa6-190">Domyślnymi współrzędnymi są X i Y. Włącz dodatkowe współrzędne (Z i M) przy użyciu metody ForSqliteHasDimension.</span><span class="sxs-lookup"><span data-stu-id="95aa6-190">The default ordinates are X and Y. Enable additional ordinates (Z and M) using the ForSqliteHasDimension method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasDimension(Ordinates.XYZ);
```

## <a name="translated-operations"></a><span data-ttu-id="95aa6-191">Operacje tłumaczone</span><span class="sxs-lookup"><span data-stu-id="95aa6-191">Translated Operations</span></span>

<span data-ttu-id="95aa6-192">W tej tabeli przedstawiono, które elementy członkowskie NKTY przerwania są tłumaczone na SQL przez każdego dostawcę EF Core.</span><span class="sxs-lookup"><span data-stu-id="95aa6-192">This table shows which NTS members are translated into SQL by each EF Core provider.</span></span>

<span data-ttu-id="95aa6-193">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="95aa6-193">NetTopologySuite</span></span> | <span data-ttu-id="95aa6-194">SQL Server (Geometria)</span><span class="sxs-lookup"><span data-stu-id="95aa6-194">SQL Server (geometry)</span></span> | <span data-ttu-id="95aa6-195">SQL Server (Geografia)</span><span class="sxs-lookup"><span data-stu-id="95aa6-195">SQL Server (geography)</span></span> | <span data-ttu-id="95aa6-196">SQLite</span><span class="sxs-lookup"><span data-stu-id="95aa6-196">SQLite</span></span> | <span data-ttu-id="95aa6-197">Npgsql</span><span class="sxs-lookup"><span data-stu-id="95aa6-197">Npgsql</span></span>
--- |:---:|:---:|:---:|:---:
<span data-ttu-id="95aa6-198">Geometria. obszar</span><span class="sxs-lookup"><span data-stu-id="95aa6-198">Geometry.Area</span></span> | <span data-ttu-id="95aa6-199">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-199">✔</span></span> | <span data-ttu-id="95aa6-200">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-200">✔</span></span> | <span data-ttu-id="95aa6-201">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-201">✔</span></span> | <span data-ttu-id="95aa6-202">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-202">✔</span></span>
<span data-ttu-id="95aa6-203">Geometry. AsBinary ()</span><span class="sxs-lookup"><span data-stu-id="95aa6-203">Geometry.AsBinary()</span></span> | <span data-ttu-id="95aa6-204">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-204">✔</span></span> | <span data-ttu-id="95aa6-205">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-205">✔</span></span> | <span data-ttu-id="95aa6-206">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-206">✔</span></span> | <span data-ttu-id="95aa6-207">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-207">✔</span></span>
<span data-ttu-id="95aa6-208">Geometry. AsText ()</span><span class="sxs-lookup"><span data-stu-id="95aa6-208">Geometry.AsText()</span></span> | <span data-ttu-id="95aa6-209">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-209">✔</span></span> | <span data-ttu-id="95aa6-210">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-210">✔</span></span> | <span data-ttu-id="95aa6-211">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-211">✔</span></span> | <span data-ttu-id="95aa6-212">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-212">✔</span></span>
<span data-ttu-id="95aa6-213">Geometria. granica</span><span class="sxs-lookup"><span data-stu-id="95aa6-213">Geometry.Boundary</span></span> | <span data-ttu-id="95aa6-214">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-214">✔</span></span> | | <span data-ttu-id="95aa6-215">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-215">✔</span></span> | <span data-ttu-id="95aa6-216">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-216">✔</span></span>
<span data-ttu-id="95aa6-217">Geometry. Buffer (Double)</span><span class="sxs-lookup"><span data-stu-id="95aa6-217">Geometry.Buffer(double)</span></span> | <span data-ttu-id="95aa6-218">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-218">✔</span></span> | <span data-ttu-id="95aa6-219">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-219">✔</span></span> | <span data-ttu-id="95aa6-220">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-220">✔</span></span> | <span data-ttu-id="95aa6-221">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-221">✔</span></span>
<span data-ttu-id="95aa6-222">Geometry. Buffer (Double, int)</span><span class="sxs-lookup"><span data-stu-id="95aa6-222">Geometry.Buffer(double, int)</span></span> | | | <span data-ttu-id="95aa6-223">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-223">✔</span></span> | <span data-ttu-id="95aa6-224">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-224">✔</span></span>
<span data-ttu-id="95aa6-225">Geometria. centroida</span><span class="sxs-lookup"><span data-stu-id="95aa6-225">Geometry.Centroid</span></span> | <span data-ttu-id="95aa6-226">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-226">✔</span></span> | | <span data-ttu-id="95aa6-227">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-227">✔</span></span> | <span data-ttu-id="95aa6-228">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-228">✔</span></span>
<span data-ttu-id="95aa6-229">Geometry. Contains (Geometria)</span><span class="sxs-lookup"><span data-stu-id="95aa6-229">Geometry.Contains(Geometry)</span></span> | <span data-ttu-id="95aa6-230">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-230">✔</span></span> | <span data-ttu-id="95aa6-231">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-231">✔</span></span> | <span data-ttu-id="95aa6-232">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-232">✔</span></span> | <span data-ttu-id="95aa6-233">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-233">✔</span></span>
<span data-ttu-id="95aa6-234">Geometry. ConvexHull ()</span><span class="sxs-lookup"><span data-stu-id="95aa6-234">Geometry.ConvexHull()</span></span> | <span data-ttu-id="95aa6-235">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-235">✔</span></span> | <span data-ttu-id="95aa6-236">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-236">✔</span></span> | <span data-ttu-id="95aa6-237">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-237">✔</span></span> | <span data-ttu-id="95aa6-238">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-238">✔</span></span>
<span data-ttu-id="95aa6-239">Geometry. CoveredBy (Geometria)</span><span class="sxs-lookup"><span data-stu-id="95aa6-239">Geometry.CoveredBy(Geometry)</span></span> | | | <span data-ttu-id="95aa6-240">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-240">✔</span></span> | <span data-ttu-id="95aa6-241">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-241">✔</span></span>
<span data-ttu-id="95aa6-242">Geometria. okładki (Geometria)</span><span class="sxs-lookup"><span data-stu-id="95aa6-242">Geometry.Covers(Geometry)</span></span> | | | <span data-ttu-id="95aa6-243">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-243">✔</span></span> | <span data-ttu-id="95aa6-244">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-244">✔</span></span>
<span data-ttu-id="95aa6-245">Geometry. crosss (Geometria)</span><span class="sxs-lookup"><span data-stu-id="95aa6-245">Geometry.Crosses(Geometry)</span></span> | <span data-ttu-id="95aa6-246">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-246">✔</span></span> | | <span data-ttu-id="95aa6-247">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-247">✔</span></span> | <span data-ttu-id="95aa6-248">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-248">✔</span></span>
<span data-ttu-id="95aa6-249">Geometry. różnica (Geometria)</span><span class="sxs-lookup"><span data-stu-id="95aa6-249">Geometry.Difference(Geometry)</span></span> | <span data-ttu-id="95aa6-250">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-250">✔</span></span> | <span data-ttu-id="95aa6-251">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-251">✔</span></span> | <span data-ttu-id="95aa6-252">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-252">✔</span></span> | <span data-ttu-id="95aa6-253">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-253">✔</span></span>
<span data-ttu-id="95aa6-254">Geometria. wymiar</span><span class="sxs-lookup"><span data-stu-id="95aa6-254">Geometry.Dimension</span></span> | <span data-ttu-id="95aa6-255">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-255">✔</span></span> | <span data-ttu-id="95aa6-256">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-256">✔</span></span> | <span data-ttu-id="95aa6-257">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-257">✔</span></span> | <span data-ttu-id="95aa6-258">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-258">✔</span></span>
<span data-ttu-id="95aa6-259">Geometria. rozłączna (Geometria)</span><span class="sxs-lookup"><span data-stu-id="95aa6-259">Geometry.Disjoint(Geometry)</span></span> | <span data-ttu-id="95aa6-260">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-260">✔</span></span> | <span data-ttu-id="95aa6-261">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-261">✔</span></span> | <span data-ttu-id="95aa6-262">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-262">✔</span></span> | <span data-ttu-id="95aa6-263">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-263">✔</span></span>
<span data-ttu-id="95aa6-264">Geometria. Distance (Geometria)</span><span class="sxs-lookup"><span data-stu-id="95aa6-264">Geometry.Distance(Geometry)</span></span> | <span data-ttu-id="95aa6-265">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-265">✔</span></span> | <span data-ttu-id="95aa6-266">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-266">✔</span></span> | <span data-ttu-id="95aa6-267">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-267">✔</span></span> | <span data-ttu-id="95aa6-268">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-268">✔</span></span>
<span data-ttu-id="95aa6-269">Geometria. koperta</span><span class="sxs-lookup"><span data-stu-id="95aa6-269">Geometry.Envelope</span></span> | <span data-ttu-id="95aa6-270">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-270">✔</span></span> | | <span data-ttu-id="95aa6-271">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-271">✔</span></span> | <span data-ttu-id="95aa6-272">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-272">✔</span></span>
<span data-ttu-id="95aa6-273">Geometry. EqualsExact (Geometria)</span><span class="sxs-lookup"><span data-stu-id="95aa6-273">Geometry.EqualsExact(Geometry)</span></span> | | | | <span data-ttu-id="95aa6-274">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-274">✔</span></span>
<span data-ttu-id="95aa6-275">Geometry. EqualsTopologically (Geometria)</span><span class="sxs-lookup"><span data-stu-id="95aa6-275">Geometry.EqualsTopologically(Geometry)</span></span> | <span data-ttu-id="95aa6-276">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-276">✔</span></span> | <span data-ttu-id="95aa6-277">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-277">✔</span></span> | <span data-ttu-id="95aa6-278">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-278">✔</span></span> | <span data-ttu-id="95aa6-279">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-279">✔</span></span>
<span data-ttu-id="95aa6-280">Geometry. Geometrytype</span><span class="sxs-lookup"><span data-stu-id="95aa6-280">Geometry.GeometryType</span></span> | <span data-ttu-id="95aa6-281">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-281">✔</span></span> | <span data-ttu-id="95aa6-282">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-282">✔</span></span> | <span data-ttu-id="95aa6-283">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-283">✔</span></span> | <span data-ttu-id="95aa6-284">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-284">✔</span></span>
<span data-ttu-id="95aa6-285">Geometry. GetGeometryN (int)</span><span class="sxs-lookup"><span data-stu-id="95aa6-285">Geometry.GetGeometryN(int)</span></span> | <span data-ttu-id="95aa6-286">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-286">✔</span></span> | | <span data-ttu-id="95aa6-287">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-287">✔</span></span> | <span data-ttu-id="95aa6-288">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-288">✔</span></span>
<span data-ttu-id="95aa6-289">Geometria. InteriorPoint</span><span class="sxs-lookup"><span data-stu-id="95aa6-289">Geometry.InteriorPoint</span></span> | <span data-ttu-id="95aa6-290">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-290">✔</span></span> | | <span data-ttu-id="95aa6-291">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-291">✔</span></span> | <span data-ttu-id="95aa6-292">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-292">✔</span></span>
<span data-ttu-id="95aa6-293">Geometria. część wspólna (Geometria)</span><span class="sxs-lookup"><span data-stu-id="95aa6-293">Geometry.Intersection(Geometry)</span></span> | <span data-ttu-id="95aa6-294">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-294">✔</span></span> | <span data-ttu-id="95aa6-295">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-295">✔</span></span> | <span data-ttu-id="95aa6-296">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-296">✔</span></span> | <span data-ttu-id="95aa6-297">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-297">✔</span></span>
<span data-ttu-id="95aa6-298">Geometria. Intersects (Geometria)</span><span class="sxs-lookup"><span data-stu-id="95aa6-298">Geometry.Intersects(Geometry)</span></span> | <span data-ttu-id="95aa6-299">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-299">✔</span></span> | <span data-ttu-id="95aa6-300">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-300">✔</span></span> | <span data-ttu-id="95aa6-301">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-301">✔</span></span> | <span data-ttu-id="95aa6-302">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-302">✔</span></span>
<span data-ttu-id="95aa6-303">Geometria. IsEmpty</span><span class="sxs-lookup"><span data-stu-id="95aa6-303">Geometry.IsEmpty</span></span> | <span data-ttu-id="95aa6-304">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-304">✔</span></span> | <span data-ttu-id="95aa6-305">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-305">✔</span></span> | <span data-ttu-id="95aa6-306">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-306">✔</span></span> | <span data-ttu-id="95aa6-307">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-307">✔</span></span>
<span data-ttu-id="95aa6-308">Geometria. IsSimple</span><span class="sxs-lookup"><span data-stu-id="95aa6-308">Geometry.IsSimple</span></span> | <span data-ttu-id="95aa6-309">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-309">✔</span></span> | | <span data-ttu-id="95aa6-310">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-310">✔</span></span> | <span data-ttu-id="95aa6-311">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-311">✔</span></span>
<span data-ttu-id="95aa6-312">Geometria. IsValid</span><span class="sxs-lookup"><span data-stu-id="95aa6-312">Geometry.IsValid</span></span> | <span data-ttu-id="95aa6-313">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-313">✔</span></span> | <span data-ttu-id="95aa6-314">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-314">✔</span></span> | <span data-ttu-id="95aa6-315">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-315">✔</span></span> | <span data-ttu-id="95aa6-316">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-316">✔</span></span>
<span data-ttu-id="95aa6-317">Geometry. IsWithinDistance (Geometria, Double)</span><span class="sxs-lookup"><span data-stu-id="95aa6-317">Geometry.IsWithinDistance(Geometry, double)</span></span> | <span data-ttu-id="95aa6-318">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-318">✔</span></span> | | <span data-ttu-id="95aa6-319">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-319">✔</span></span> | <span data-ttu-id="95aa6-320">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-320">✔</span></span>
<span data-ttu-id="95aa6-321">Geometria. Długość</span><span class="sxs-lookup"><span data-stu-id="95aa6-321">Geometry.Length</span></span> | <span data-ttu-id="95aa6-322">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-322">✔</span></span> | <span data-ttu-id="95aa6-323">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-323">✔</span></span> | <span data-ttu-id="95aa6-324">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-324">✔</span></span> | <span data-ttu-id="95aa6-325">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-325">✔</span></span>
<span data-ttu-id="95aa6-326">Geometria. NumGeometries</span><span class="sxs-lookup"><span data-stu-id="95aa6-326">Geometry.NumGeometries</span></span> | <span data-ttu-id="95aa6-327">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-327">✔</span></span> | <span data-ttu-id="95aa6-328">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-328">✔</span></span> | <span data-ttu-id="95aa6-329">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-329">✔</span></span> | <span data-ttu-id="95aa6-330">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-330">✔</span></span>
<span data-ttu-id="95aa6-331">Geometria. NumPoints</span><span class="sxs-lookup"><span data-stu-id="95aa6-331">Geometry.NumPoints</span></span> | <span data-ttu-id="95aa6-332">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-332">✔</span></span> | <span data-ttu-id="95aa6-333">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-333">✔</span></span> | <span data-ttu-id="95aa6-334">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-334">✔</span></span> | <span data-ttu-id="95aa6-335">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-335">✔</span></span>
<span data-ttu-id="95aa6-336">Geometria. OgcGeometryType</span><span class="sxs-lookup"><span data-stu-id="95aa6-336">Geometry.OgcGeometryType</span></span> | <span data-ttu-id="95aa6-337">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-337">✔</span></span> | <span data-ttu-id="95aa6-338">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-338">✔</span></span> | <span data-ttu-id="95aa6-339">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-339">✔</span></span> | <span data-ttu-id="95aa6-340">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-340">✔</span></span>
<span data-ttu-id="95aa6-341">Geometria. nakładanie się (Geometria)</span><span class="sxs-lookup"><span data-stu-id="95aa6-341">Geometry.Overlaps(Geometry)</span></span> | <span data-ttu-id="95aa6-342">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-342">✔</span></span> | <span data-ttu-id="95aa6-343">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-343">✔</span></span> | <span data-ttu-id="95aa6-344">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-344">✔</span></span> | <span data-ttu-id="95aa6-345">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-345">✔</span></span>
<span data-ttu-id="95aa6-346">Geometria. PointOnSurface</span><span class="sxs-lookup"><span data-stu-id="95aa6-346">Geometry.PointOnSurface</span></span> | <span data-ttu-id="95aa6-347">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-347">✔</span></span> | | <span data-ttu-id="95aa6-348">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-348">✔</span></span> | <span data-ttu-id="95aa6-349">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-349">✔</span></span>
<span data-ttu-id="95aa6-350">Geometry. rerelacja (Geometria, ciąg)</span><span class="sxs-lookup"><span data-stu-id="95aa6-350">Geometry.Relate(Geometry, string)</span></span> | <span data-ttu-id="95aa6-351">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-351">✔</span></span> | | <span data-ttu-id="95aa6-352">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-352">✔</span></span> | <span data-ttu-id="95aa6-353">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-353">✔</span></span>
<span data-ttu-id="95aa6-354">Geometry. Reverse ()</span><span class="sxs-lookup"><span data-stu-id="95aa6-354">Geometry.Reverse()</span></span> | | | <span data-ttu-id="95aa6-355">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-355">✔</span></span> | <span data-ttu-id="95aa6-356">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-356">✔</span></span>
<span data-ttu-id="95aa6-357">Geometria. SRID</span><span class="sxs-lookup"><span data-stu-id="95aa6-357">Geometry.SRID</span></span> | <span data-ttu-id="95aa6-358">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-358">✔</span></span> | <span data-ttu-id="95aa6-359">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-359">✔</span></span> | <span data-ttu-id="95aa6-360">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-360">✔</span></span> | <span data-ttu-id="95aa6-361">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-361">✔</span></span>
<span data-ttu-id="95aa6-362">Geometry. SymmetricDifference (Geometria)</span><span class="sxs-lookup"><span data-stu-id="95aa6-362">Geometry.SymmetricDifference(Geometry)</span></span> | <span data-ttu-id="95aa6-363">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-363">✔</span></span> | <span data-ttu-id="95aa6-364">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-364">✔</span></span> | <span data-ttu-id="95aa6-365">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-365">✔</span></span> | <span data-ttu-id="95aa6-366">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-366">✔</span></span>
<span data-ttu-id="95aa6-367">Geometry. ToBinary ()</span><span class="sxs-lookup"><span data-stu-id="95aa6-367">Geometry.ToBinary()</span></span> | <span data-ttu-id="95aa6-368">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-368">✔</span></span> | <span data-ttu-id="95aa6-369">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-369">✔</span></span> | <span data-ttu-id="95aa6-370">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-370">✔</span></span> | <span data-ttu-id="95aa6-371">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-371">✔</span></span>
<span data-ttu-id="95aa6-372">Geometry. ToText ()</span><span class="sxs-lookup"><span data-stu-id="95aa6-372">Geometry.ToText()</span></span> | <span data-ttu-id="95aa6-373">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-373">✔</span></span> | <span data-ttu-id="95aa6-374">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-374">✔</span></span> | <span data-ttu-id="95aa6-375">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-375">✔</span></span> | <span data-ttu-id="95aa6-376">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-376">✔</span></span>
<span data-ttu-id="95aa6-377">Geometria. touch (Geometria)</span><span class="sxs-lookup"><span data-stu-id="95aa6-377">Geometry.Touches(Geometry)</span></span> | <span data-ttu-id="95aa6-378">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-378">✔</span></span> | | <span data-ttu-id="95aa6-379">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-379">✔</span></span> | <span data-ttu-id="95aa6-380">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-380">✔</span></span>
<span data-ttu-id="95aa6-381">Geometry. Union ()</span><span class="sxs-lookup"><span data-stu-id="95aa6-381">Geometry.Union()</span></span> | | | <span data-ttu-id="95aa6-382">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-382">✔</span></span> | <span data-ttu-id="95aa6-383">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-383">✔</span></span>
<span data-ttu-id="95aa6-384">Geometry. Union (Geometria)</span><span class="sxs-lookup"><span data-stu-id="95aa6-384">Geometry.Union(Geometry)</span></span> | <span data-ttu-id="95aa6-385">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-385">✔</span></span> | <span data-ttu-id="95aa6-386">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-386">✔</span></span> | <span data-ttu-id="95aa6-387">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-387">✔</span></span> | <span data-ttu-id="95aa6-388">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-388">✔</span></span>
<span data-ttu-id="95aa6-389">Geometria. w elemencie (Geometria)</span><span class="sxs-lookup"><span data-stu-id="95aa6-389">Geometry.Within(Geometry)</span></span> | <span data-ttu-id="95aa6-390">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-390">✔</span></span> | <span data-ttu-id="95aa6-391">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-391">✔</span></span> | <span data-ttu-id="95aa6-392">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-392">✔</span></span> | <span data-ttu-id="95aa6-393">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-393">✔</span></span>
<span data-ttu-id="95aa6-394">GeometryCollection. Count</span><span class="sxs-lookup"><span data-stu-id="95aa6-394">GeometryCollection.Count</span></span> | <span data-ttu-id="95aa6-395">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-395">✔</span></span> | <span data-ttu-id="95aa6-396">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-396">✔</span></span> | <span data-ttu-id="95aa6-397">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-397">✔</span></span> | <span data-ttu-id="95aa6-398">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-398">✔</span></span>
<span data-ttu-id="95aa6-399">GeometryCollection [int]</span><span class="sxs-lookup"><span data-stu-id="95aa6-399">GeometryCollection[int]</span></span> | <span data-ttu-id="95aa6-400">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-400">✔</span></span> | <span data-ttu-id="95aa6-401">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-401">✔</span></span> | <span data-ttu-id="95aa6-402">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-402">✔</span></span> | <span data-ttu-id="95aa6-403">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-403">✔</span></span>
<span data-ttu-id="95aa6-404">LineString. Count</span><span class="sxs-lookup"><span data-stu-id="95aa6-404">LineString.Count</span></span> | <span data-ttu-id="95aa6-405">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-405">✔</span></span> | <span data-ttu-id="95aa6-406">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-406">✔</span></span> | <span data-ttu-id="95aa6-407">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-407">✔</span></span> | <span data-ttu-id="95aa6-408">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-408">✔</span></span>
<span data-ttu-id="95aa6-409">LineString. EndPoint</span><span class="sxs-lookup"><span data-stu-id="95aa6-409">LineString.EndPoint</span></span> | <span data-ttu-id="95aa6-410">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-410">✔</span></span> | <span data-ttu-id="95aa6-411">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-411">✔</span></span> | <span data-ttu-id="95aa6-412">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-412">✔</span></span> | <span data-ttu-id="95aa6-413">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-413">✔</span></span>
<span data-ttu-id="95aa6-414">LineString. GetPointN (int)</span><span class="sxs-lookup"><span data-stu-id="95aa6-414">LineString.GetPointN(int)</span></span> | <span data-ttu-id="95aa6-415">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-415">✔</span></span> | <span data-ttu-id="95aa6-416">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-416">✔</span></span> | <span data-ttu-id="95aa6-417">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-417">✔</span></span> | <span data-ttu-id="95aa6-418">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-418">✔</span></span>
<span data-ttu-id="95aa6-419">LineString. IsClosed</span><span class="sxs-lookup"><span data-stu-id="95aa6-419">LineString.IsClosed</span></span> | <span data-ttu-id="95aa6-420">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-420">✔</span></span> | <span data-ttu-id="95aa6-421">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-421">✔</span></span> | <span data-ttu-id="95aa6-422">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-422">✔</span></span> | <span data-ttu-id="95aa6-423">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-423">✔</span></span>
<span data-ttu-id="95aa6-424">LineString. IsRing</span><span class="sxs-lookup"><span data-stu-id="95aa6-424">LineString.IsRing</span></span> | <span data-ttu-id="95aa6-425">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-425">✔</span></span> | | <span data-ttu-id="95aa6-426">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-426">✔</span></span> | <span data-ttu-id="95aa6-427">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-427">✔</span></span>
<span data-ttu-id="95aa6-428">LineString. StartPoint</span><span class="sxs-lookup"><span data-stu-id="95aa6-428">LineString.StartPoint</span></span> | <span data-ttu-id="95aa6-429">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-429">✔</span></span> | <span data-ttu-id="95aa6-430">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-430">✔</span></span> | <span data-ttu-id="95aa6-431">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-431">✔</span></span> | <span data-ttu-id="95aa6-432">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-432">✔</span></span>
<span data-ttu-id="95aa6-433">MultiLineString. IsClosed</span><span class="sxs-lookup"><span data-stu-id="95aa6-433">MultiLineString.IsClosed</span></span> | <span data-ttu-id="95aa6-434">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-434">✔</span></span> | <span data-ttu-id="95aa6-435">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-435">✔</span></span> | <span data-ttu-id="95aa6-436">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-436">✔</span></span> | <span data-ttu-id="95aa6-437">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-437">✔</span></span>
<span data-ttu-id="95aa6-438">Punkt. M</span><span class="sxs-lookup"><span data-stu-id="95aa6-438">Point.M</span></span> | <span data-ttu-id="95aa6-439">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-439">✔</span></span> | <span data-ttu-id="95aa6-440">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-440">✔</span></span> | <span data-ttu-id="95aa6-441">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-441">✔</span></span> | <span data-ttu-id="95aa6-442">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-442">✔</span></span>
<span data-ttu-id="95aa6-443">Punkt. X</span><span class="sxs-lookup"><span data-stu-id="95aa6-443">Point.X</span></span> | <span data-ttu-id="95aa6-444">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-444">✔</span></span> | <span data-ttu-id="95aa6-445">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-445">✔</span></span> | <span data-ttu-id="95aa6-446">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-446">✔</span></span> | <span data-ttu-id="95aa6-447">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-447">✔</span></span>
<span data-ttu-id="95aa6-448">Punkt. Y</span><span class="sxs-lookup"><span data-stu-id="95aa6-448">Point.Y</span></span> | <span data-ttu-id="95aa6-449">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-449">✔</span></span> | <span data-ttu-id="95aa6-450">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-450">✔</span></span> | <span data-ttu-id="95aa6-451">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-451">✔</span></span> | <span data-ttu-id="95aa6-452">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-452">✔</span></span>
<span data-ttu-id="95aa6-453">Punkt. Z</span><span class="sxs-lookup"><span data-stu-id="95aa6-453">Point.Z</span></span> | <span data-ttu-id="95aa6-454">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-454">✔</span></span> | <span data-ttu-id="95aa6-455">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-455">✔</span></span> | <span data-ttu-id="95aa6-456">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-456">✔</span></span> | <span data-ttu-id="95aa6-457">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-457">✔</span></span>
<span data-ttu-id="95aa6-458">Wielokąt. ExteriorRing</span><span class="sxs-lookup"><span data-stu-id="95aa6-458">Polygon.ExteriorRing</span></span> | <span data-ttu-id="95aa6-459">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-459">✔</span></span> | <span data-ttu-id="95aa6-460">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-460">✔</span></span> | <span data-ttu-id="95aa6-461">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-461">✔</span></span> | <span data-ttu-id="95aa6-462">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-462">✔</span></span>
<span data-ttu-id="95aa6-463">Wielokąt. GetInteriorRingN (int)</span><span class="sxs-lookup"><span data-stu-id="95aa6-463">Polygon.GetInteriorRingN(int)</span></span> | <span data-ttu-id="95aa6-464">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-464">✔</span></span> | <span data-ttu-id="95aa6-465">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-465">✔</span></span> | <span data-ttu-id="95aa6-466">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-466">✔</span></span> | <span data-ttu-id="95aa6-467">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-467">✔</span></span>
<span data-ttu-id="95aa6-468">Wielokąt. NumInteriorRings</span><span class="sxs-lookup"><span data-stu-id="95aa6-468">Polygon.NumInteriorRings</span></span> | <span data-ttu-id="95aa6-469">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-469">✔</span></span> | <span data-ttu-id="95aa6-470">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-470">✔</span></span> | <span data-ttu-id="95aa6-471">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-471">✔</span></span> | <span data-ttu-id="95aa6-472">✔</span><span class="sxs-lookup"><span data-stu-id="95aa6-472">✔</span></span>

## <a name="additional-resources"></a><span data-ttu-id="95aa6-473">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="95aa6-473">Additional resources</span></span>

* [<span data-ttu-id="95aa6-474">Dane przestrzenne w SQL Server</span><span class="sxs-lookup"><span data-stu-id="95aa6-474">Spatial Data in SQL Server</span></span>](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [<span data-ttu-id="95aa6-475">Strona główna SpatiaLite</span><span class="sxs-lookup"><span data-stu-id="95aa6-475">SpatiaLite Homepage</span></span>](https://www.gaia-gis.it/fossil/libspatialite)
* [<span data-ttu-id="95aa6-476">Dokumentacja przestrzenna Npgsql</span><span class="sxs-lookup"><span data-stu-id="95aa6-476">Npgsql Spatial Documentation</span></span>](https://www.npgsql.org/efcore/mapping/nts.html)
* [<span data-ttu-id="95aa6-477">Dokumentacja PostGIS</span><span class="sxs-lookup"><span data-stu-id="95aa6-477">PostGIS Documentation</span></span>](https://postgis.net/documentation/)
