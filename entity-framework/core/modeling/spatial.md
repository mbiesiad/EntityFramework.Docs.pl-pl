---
title: Dane przestrzenne — EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/01/2018
ms.assetid: 2BDE29FC-4161-41A0-841E-69F51CCD9341
uid: core/modeling/spatial
ms.openlocfilehash: 5b45f83ca7f02665f52ccfe16b5af506a6046a62
ms.sourcegitcommit: f2a38c086291699422d8b28a72d9611d1b24ad0d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76124434"
---
# <a name="spatial-data"></a><span data-ttu-id="deeb9-102">Dane przestrzenne</span><span class="sxs-lookup"><span data-stu-id="deeb9-102">Spatial Data</span></span>

> [!NOTE]
> <span data-ttu-id="deeb9-103">Ta funkcja została dodana w EF Core 2,2.</span><span class="sxs-lookup"><span data-stu-id="deeb9-103">This feature was added in EF Core 2.2.</span></span>

<span data-ttu-id="deeb9-104">Dane przestrzenne reprezentują lokalizację fizyczną i kształt obiektów.</span><span class="sxs-lookup"><span data-stu-id="deeb9-104">Spatial data represents the physical location and the shape of objects.</span></span> <span data-ttu-id="deeb9-105">Wiele baz danych zapewnia obsługę tego typu danych, dzięki czemu można je indeksować i badać wraz z innymi danymi.</span><span class="sxs-lookup"><span data-stu-id="deeb9-105">Many databases provide support for this type of data so it can be indexed and queried alongside other data.</span></span> <span data-ttu-id="deeb9-106">Typowe scenariusze obejmują zapytania dotyczące obiektów w danej odległości od lokalizacji lub wybierając obiekt, którego obramowanie zawiera daną lokalizację.</span><span class="sxs-lookup"><span data-stu-id="deeb9-106">Common scenarios include querying for objects within a given distance from a location, or selecting the object whose border contains a given location.</span></span> <span data-ttu-id="deeb9-107">EF Core obsługuje mapowanie do typów danych przestrzennych przy użyciu biblioteki przestrzennej [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) .</span><span class="sxs-lookup"><span data-stu-id="deeb9-107">EF Core supports mapping to spatial data types using the [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) spatial library.</span></span>

## <a name="installing"></a><span data-ttu-id="deeb9-108">Instalowanie programu</span><span class="sxs-lookup"><span data-stu-id="deeb9-108">Installing</span></span>

<span data-ttu-id="deeb9-109">Aby można było używać danych przestrzennych z EF Core, należy zainstalować odpowiedni pomocniczy pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="deeb9-109">In order to use spatial data with EF Core, you need to install the appropriate supporting NuGet package.</span></span> <span data-ttu-id="deeb9-110">Który pakiet należy zainstalować zależy od dostawcy, którego używasz.</span><span class="sxs-lookup"><span data-stu-id="deeb9-110">Which package you need to install depends on the provider you're using.</span></span>

<span data-ttu-id="deeb9-111">Dostawca platformy EF Core</span><span class="sxs-lookup"><span data-stu-id="deeb9-111">EF Core Provider</span></span>                        | <span data-ttu-id="deeb9-112">Przestrzenny pakiet NuGet</span><span class="sxs-lookup"><span data-stu-id="deeb9-112">Spatial NuGet Package</span></span>
--------------------------------------- | ---------------------
<span data-ttu-id="deeb9-113">Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="deeb9-113">Microsoft.EntityFrameworkCore.SqlServer</span></span> | [<span data-ttu-id="deeb9-114">Microsoft. EntityFrameworkCore. SqlServer. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="deeb9-114">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
<span data-ttu-id="deeb9-115">Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="deeb9-115">Microsoft.EntityFrameworkCore.Sqlite</span></span>    | [<span data-ttu-id="deeb9-116">Microsoft. EntityFrameworkCore. sqlite. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="deeb9-116">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
<span data-ttu-id="deeb9-117">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="deeb9-117">Microsoft.EntityFrameworkCore.InMemory</span></span>  | [<span data-ttu-id="deeb9-118">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="deeb9-118">NetTopologySuite</span></span>](https://www.nuget.org/packages/NetTopologySuite)
<span data-ttu-id="deeb9-119">Npgsql.EntityFrameworkCore.PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="deeb9-119">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>   | [<span data-ttu-id="deeb9-120">Npgsql. EntityFrameworkCore. PostgreSQL. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="deeb9-120">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)

## <a name="reverse-engineering"></a><span data-ttu-id="deeb9-121">Odwrócenie inżynierii</span><span class="sxs-lookup"><span data-stu-id="deeb9-121">Reverse engineering</span></span>

<span data-ttu-id="deeb9-122">Przestrzenne pakiety NuGet umożliwiają również [Tworzenie modeli odtwarzania](../managing-schemas/scaffolding.md) z właściwościami przestrzennymi, ale należy zainstalować pakiet ***przed*** uruchomieniem `Scaffold-DbContext` lub `dotnet ef dbcontext scaffold`.</span><span class="sxs-lookup"><span data-stu-id="deeb9-122">The spatial NuGet packages also enable [reverse engineering](../managing-schemas/scaffolding.md) models with spatial properties, but you need to install the package ***before*** running `Scaffold-DbContext` or `dotnet ef dbcontext scaffold`.</span></span> <span data-ttu-id="deeb9-123">W przeciwnym razie otrzymasz ostrzeżenia dotyczące nieznajdowania mapowań typów dla kolumn, a kolumny zostaną pominięte.</span><span class="sxs-lookup"><span data-stu-id="deeb9-123">If you don't, you'll receive warnings about not finding type mappings for the columns and the columns will be skipped.</span></span>

## <a name="nettopologysuite-nts"></a><span data-ttu-id="deeb9-124">NetTopologySuite (NKTY przerwania)</span><span class="sxs-lookup"><span data-stu-id="deeb9-124">NetTopologySuite (NTS)</span></span>

<span data-ttu-id="deeb9-125">NetTopologySuite jest biblioteką przestrzenną dla platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="deeb9-125">NetTopologySuite is a spatial library for .NET.</span></span> <span data-ttu-id="deeb9-126">EF Core umożliwia mapowanie do typów danych przestrzennych w bazie danych przy użyciu typów NKTY przerwania w modelu.</span><span class="sxs-lookup"><span data-stu-id="deeb9-126">EF Core enables mapping to spatial data types in the database by using NTS types in your model.</span></span>

<span data-ttu-id="deeb9-127">Aby włączyć mapowanie do typów przestrzennych za pośrednictwem NKTY przerwania, wywołaj metodę UseNetTopologySuite w konstruktorze opcji DbContext dostawcy.</span><span class="sxs-lookup"><span data-stu-id="deeb9-127">To enable mapping to spatial types via NTS, call the UseNetTopologySuite method on the provider's DbContext options builder.</span></span> <span data-ttu-id="deeb9-128">Na przykład w przypadku SQL Server należy wywołać tę metodę.</span><span class="sxs-lookup"><span data-stu-id="deeb9-128">For example, with SQL Server you'd call it like this.</span></span>

``` csharp
optionsBuilder.UseSqlServer(
    @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=WideWorldImporters",
    x => x.UseNetTopologySuite());
```

<span data-ttu-id="deeb9-129">Istnieje kilka typów danych przestrzennych.</span><span class="sxs-lookup"><span data-stu-id="deeb9-129">There are several spatial data types.</span></span> <span data-ttu-id="deeb9-130">Używany typ zależy od typów kształtów, które mają być dozwolone.</span><span class="sxs-lookup"><span data-stu-id="deeb9-130">Which type you use depends on the types of shapes you want to allow.</span></span> <span data-ttu-id="deeb9-131">Poniżej znajduje się hierarchia typów NKTY przerwania, których można użyć do właściwości w modelu.</span><span class="sxs-lookup"><span data-stu-id="deeb9-131">Here is the hierarchy of NTS types that you can use for properties in your model.</span></span> <span data-ttu-id="deeb9-132">Znajdują się one w przestrzeni nazw `NetTopologySuite.Geometries`.</span><span class="sxs-lookup"><span data-stu-id="deeb9-132">They're located within the `NetTopologySuite.Geometries` namespace.</span></span>

* <span data-ttu-id="deeb9-133">Geometrii</span><span class="sxs-lookup"><span data-stu-id="deeb9-133">Geometry</span></span>
  * <span data-ttu-id="deeb9-134">Punkt</span><span class="sxs-lookup"><span data-stu-id="deeb9-134">Point</span></span>
  * <span data-ttu-id="deeb9-135">LineString</span><span class="sxs-lookup"><span data-stu-id="deeb9-135">LineString</span></span>
  * <span data-ttu-id="deeb9-136">Tworząc</span><span class="sxs-lookup"><span data-stu-id="deeb9-136">Polygon</span></span>
  * <span data-ttu-id="deeb9-137">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="deeb9-137">GeometryCollection</span></span>
    * <span data-ttu-id="deeb9-138">MultiPoint</span><span class="sxs-lookup"><span data-stu-id="deeb9-138">MultiPoint</span></span>
    * <span data-ttu-id="deeb9-139">MultiLineString</span><span class="sxs-lookup"><span data-stu-id="deeb9-139">MultiLineString</span></span>
    * <span data-ttu-id="deeb9-140">MultiPolygon</span><span class="sxs-lookup"><span data-stu-id="deeb9-140">MultiPolygon</span></span>

> [!WARNING]
> <span data-ttu-id="deeb9-141">CircularString, CompoundCurve i CurePolygon nie są obsługiwane przez NKTY przerwania.</span><span class="sxs-lookup"><span data-stu-id="deeb9-141">CircularString, CompoundCurve, and CurePolygon aren't supported by NTS.</span></span>

<span data-ttu-id="deeb9-142">Użycie podstawowego typu geometrii umożliwia określenie dowolnego typu kształtu przez właściwość.</span><span class="sxs-lookup"><span data-stu-id="deeb9-142">Using the base Geometry type allows any type of shape to be specified by the property.</span></span>

<span data-ttu-id="deeb9-143">Poniższe klasy jednostek mogą służyć do mapowania tabel w [przykładowej bazie danych](https://go.microsoft.com/fwlink/?LinkID=800630)na całym świecie.</span><span class="sxs-lookup"><span data-stu-id="deeb9-143">The following entity classes could be used to map to tables in the [Wide World Importers sample database](https://go.microsoft.com/fwlink/?LinkID=800630).</span></span>

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

### <a name="creating-values"></a><span data-ttu-id="deeb9-144">Tworzenie wartości</span><span class="sxs-lookup"><span data-stu-id="deeb9-144">Creating values</span></span>

<span data-ttu-id="deeb9-145">Można użyć konstruktorów do tworzenia obiektów geometrycznych; jednak NKTY przerwania zaleca się użycie fabryki geometrycznej.</span><span class="sxs-lookup"><span data-stu-id="deeb9-145">You can use constructors to create geometry objects; however, NTS recommends using a geometry factory instead.</span></span> <span data-ttu-id="deeb9-146">Pozwala to określić domyślny SRID (System referencyjny przestrzennej używany przez współrzędne) i zapewnia kontrolę nad bardziej zaawansowanymi elementami, takimi jak model precyzji (używany podczas obliczeń) i sekwencją współrzędnych (określa, które współrzędne są wymiarami i miary--są dostępne).</span><span class="sxs-lookup"><span data-stu-id="deeb9-146">This lets you specify a default SRID (the spatial reference system used by the coordinates) and gives you control over more advanced things like the precision model (used during calculations) and the coordinate sequence (determines which ordinates--dimensions and measures--are available).</span></span>

``` csharp
var geometryFactory = NtsGeometryServices.Instance.CreateGeometryFactory(srid: 4326);
var currentLocation = geometryFactory.CreatePoint(-122.121512, 47.6739882);
```

> [!NOTE]
> <span data-ttu-id="deeb9-147">4326 dotyczy WGS 84, standardowego używanego w systemach GPS i innych systemów geograficznych.</span><span class="sxs-lookup"><span data-stu-id="deeb9-147">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

### <a name="longitude-and-latitude"></a><span data-ttu-id="deeb9-148">Długości i szerokości geograficznej</span><span class="sxs-lookup"><span data-stu-id="deeb9-148">Longitude and Latitude</span></span>

<span data-ttu-id="deeb9-149">Współrzędne w NKTY przerwania są pod względem wartości X i Y.</span><span class="sxs-lookup"><span data-stu-id="deeb9-149">Coordinates in NTS are in terms of X and Y values.</span></span> <span data-ttu-id="deeb9-150">Aby przedstawić długość i szerokość geograficzną, użyj X dla długości geograficznej i Y dla szerokości geograficznej.</span><span class="sxs-lookup"><span data-stu-id="deeb9-150">To represent longitude and latitude, use X for longitude and Y for latitude.</span></span> <span data-ttu-id="deeb9-151">Należy pamiętać, że jest to **wsteczne** w formacie `latitude, longitude`, w którym są zwykle wyświetlane te wartości.</span><span class="sxs-lookup"><span data-stu-id="deeb9-151">Note that this is **backwards** from the `latitude, longitude` format in which you typically see these values.</span></span>

### <a name="srid-ignored-during-client-operations"></a><span data-ttu-id="deeb9-152">SRID zignorowane podczas operacji klienta</span><span class="sxs-lookup"><span data-stu-id="deeb9-152">SRID Ignored during client operations</span></span>

<span data-ttu-id="deeb9-153">NKTY przerwania ignoruje wartości SRID podczas operacji.</span><span class="sxs-lookup"><span data-stu-id="deeb9-153">NTS ignores SRID values during operations.</span></span> <span data-ttu-id="deeb9-154">Przyjęto założenie układu współrzędnych.</span><span class="sxs-lookup"><span data-stu-id="deeb9-154">It assumes a planar coordinate system.</span></span> <span data-ttu-id="deeb9-155">Oznacza to, że jeśli określisz współrzędne w zakresie długości i szerokości geograficznej, niektóre wartości oceniane przez klienta, takie jak odległość, długość i obszar, będą znajdować się w stopniach, a nie w metrach.</span><span class="sxs-lookup"><span data-stu-id="deeb9-155">This means that if you specify coordinates in terms of longitude and latitude, some client-evaluated values like distance, length, and area will be in degrees, not meters.</span></span> <span data-ttu-id="deeb9-156">Aby uzyskać bardziej zrozumiałe wartości, najpierw należy umieścić współrzędne w innym układzie współrzędnych przy użyciu biblioteki, takiej jak [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) przed obliczeniem tych wartości.</span><span class="sxs-lookup"><span data-stu-id="deeb9-156">For more meaningful values, you first need to project the coordinates to another coordinate system using a library like [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) before calculating these values.</span></span>

<span data-ttu-id="deeb9-157">Jeśli operacja jest Szacowana przez serwer EF Core za pośrednictwem SQL, jednostka wyniku zostanie określona przez bazę danych.</span><span class="sxs-lookup"><span data-stu-id="deeb9-157">If an operation is server-evaluated by EF Core via SQL, the result's unit will be determined by the database.</span></span>

<span data-ttu-id="deeb9-158">Oto przykład użycia ProjNet4GeoAPI do obliczenia odległości między dwoma miastami.</span><span class="sxs-lookup"><span data-stu-id="deeb9-158">Here is an example of using ProjNet4GeoAPI to calculate the distance between two cities.</span></span>

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

## <a name="querying-data"></a><span data-ttu-id="deeb9-159">Wykonanie zapytania o dane</span><span class="sxs-lookup"><span data-stu-id="deeb9-159">Querying Data</span></span>

<span data-ttu-id="deeb9-160">W LINQ, metody i właściwości NKTY przerwania dostępne jako funkcje bazy danych zostaną przetłumaczone na SQL.</span><span class="sxs-lookup"><span data-stu-id="deeb9-160">In LINQ, the NTS methods and properties available as database functions will be translated to SQL.</span></span> <span data-ttu-id="deeb9-161">Na przykład odległość i zawiera metody są tłumaczone w poniższych zapytaniach.</span><span class="sxs-lookup"><span data-stu-id="deeb9-161">For example, the Distance and Contains methods are translated in the following queries.</span></span> <span data-ttu-id="deeb9-162">W tabeli na końcu tego artykułu przedstawiono, które elementy członkowskie są obsługiwane przez różnych dostawców EF Core.</span><span class="sxs-lookup"><span data-stu-id="deeb9-162">The table at the end of this article shows which members are supported by various EF Core providers.</span></span>

``` csharp
var nearestCity = db.Cities
    .OrderBy(c => c.Location.Distance(currentLocation))
    .FirstOrDefault();

var currentCountry = db.Countries
    .FirstOrDefault(c => c.Border.Contains(currentLocation));
```

## <a name="sql-server"></a><span data-ttu-id="deeb9-163">Serwer SQL</span><span class="sxs-lookup"><span data-stu-id="deeb9-163">SQL Server</span></span>

<span data-ttu-id="deeb9-164">Jeśli używasz SQL Server, musisz wiedzieć o kilku dodatkowych kwestiach.</span><span class="sxs-lookup"><span data-stu-id="deeb9-164">If you're using SQL Server, there are some additional things you should be aware of.</span></span>

### <a name="geography-or-geometry"></a><span data-ttu-id="deeb9-165">Geografia lub geometria</span><span class="sxs-lookup"><span data-stu-id="deeb9-165">Geography or geometry</span></span>

<span data-ttu-id="deeb9-166">Domyślnie właściwości przestrzenne są mapowane do `geography` kolumn w SQL Server.</span><span class="sxs-lookup"><span data-stu-id="deeb9-166">By default, spatial properties are mapped to `geography` columns in SQL Server.</span></span> <span data-ttu-id="deeb9-167">Aby użyć `geometry`, należy [skonfigurować typ kolumny](xref:core/modeling/entity-properties#column-data-types) w modelu.</span><span class="sxs-lookup"><span data-stu-id="deeb9-167">To use `geometry`, [configure the column type](xref:core/modeling/entity-properties#column-data-types) in your model.</span></span>

### <a name="geography-polygon-rings"></a><span data-ttu-id="deeb9-168">Pierścienie wielokątów geograficznych</span><span class="sxs-lookup"><span data-stu-id="deeb9-168">Geography polygon rings</span></span>

<span data-ttu-id="deeb9-169">W przypadku używania `geography` typu kolumny SQL Server nakładają dodatkowe wymagania dotyczące pierścienia zewnętrznego (lub powłoki) i wewnętrznych pierścieni (lub dziur).</span><span class="sxs-lookup"><span data-stu-id="deeb9-169">When using the `geography` column type, SQL Server imposes additional requirements on the exterior ring (or shell) and interior rings (or holes).</span></span> <span data-ttu-id="deeb9-170">Pierścień zewnętrzny musi być zorientowany w lewo i w prawo.</span><span class="sxs-lookup"><span data-stu-id="deeb9-170">The exterior ring must be oriented counterclockwise and the interior rings clockwise.</span></span> <span data-ttu-id="deeb9-171">NKTY przerwania sprawdza to przed wysłaniem wartości do bazy danych.</span><span class="sxs-lookup"><span data-stu-id="deeb9-171">NTS validates this before sending values to the database.</span></span>

### <a name="fullglobe"></a><span data-ttu-id="deeb9-172">FullGlobe</span><span class="sxs-lookup"><span data-stu-id="deeb9-172">FullGlobe</span></span>

<span data-ttu-id="deeb9-173">SQL Server ma niestandardowy typ geometrii reprezentujący pełny Globus przy użyciu typu kolumny `geography`.</span><span class="sxs-lookup"><span data-stu-id="deeb9-173">SQL Server has a non-standard geometry type to represent the full globe when using the `geography` column type.</span></span> <span data-ttu-id="deeb9-174">Ma również sposób reprezentowania wielokątów w oparciu o pełny Globus (bez pierścienia zewnętrznego).</span><span class="sxs-lookup"><span data-stu-id="deeb9-174">It also has a way to represent polygons based on the full globe (without an exterior ring).</span></span> <span data-ttu-id="deeb9-175">Żadna z tych elementów nie jest obsługiwana przez NKTY przerwania.</span><span class="sxs-lookup"><span data-stu-id="deeb9-175">Neither of these are supported by NTS.</span></span>

> [!WARNING]
> <span data-ttu-id="deeb9-176">FullGlobe i wielokąty oparte na nim nie są obsługiwane przez NKTY przerwania.</span><span class="sxs-lookup"><span data-stu-id="deeb9-176">FullGlobe and polygons based on it aren't supported by NTS.</span></span>

## <a name="sqlite"></a><span data-ttu-id="deeb9-177">SQLite</span><span class="sxs-lookup"><span data-stu-id="deeb9-177">SQLite</span></span>

<span data-ttu-id="deeb9-178">Poniżej przedstawiono dodatkowe informacje dotyczące tych, które są używane przez program SQLite.</span><span class="sxs-lookup"><span data-stu-id="deeb9-178">Here is some additional information for those using SQLite.</span></span>

### <a name="installing-spatialite"></a><span data-ttu-id="deeb9-179">Instalowanie SpatiaLite</span><span class="sxs-lookup"><span data-stu-id="deeb9-179">Installing SpatiaLite</span></span>

<span data-ttu-id="deeb9-180">W systemie Windows natywna biblioteka mod_spatialite jest dystrybuowana jako zależność pakietu NuGet.</span><span class="sxs-lookup"><span data-stu-id="deeb9-180">On Windows, the native mod_spatialite library is distributed as a NuGet package dependency.</span></span> <span data-ttu-id="deeb9-181">Inne platformy muszą zainstalować je oddzielnie.</span><span class="sxs-lookup"><span data-stu-id="deeb9-181">Other platforms need to install it separately.</span></span> <span data-ttu-id="deeb9-182">Jest to zazwyczaj realizowane przy użyciu Menedżera pakietów oprogramowania.</span><span class="sxs-lookup"><span data-stu-id="deeb9-182">This is typically done using a software package manager.</span></span> <span data-ttu-id="deeb9-183">Na przykład można użyć APT w Ubuntu i oprogramowania Homebrew na MacOS.</span><span class="sxs-lookup"><span data-stu-id="deeb9-183">For example, you can use APT on Ubuntu and Homebrew on MacOS.</span></span>

``` sh
# Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

<span data-ttu-id="deeb9-184">Niestety, nowsze wersje programu PROJ (zależność SpatiaLite) są niezgodne z domyślnym [pakietem SQLITEPCLRAW](/dotnet/standard/data/sqlite/custom-versions#bundles)EF.</span><span class="sxs-lookup"><span data-stu-id="deeb9-184">Unfortunately, newer versions of PROJ (a dependency of SpatiaLite) are incompatible with EF's default [SQLitePCLRaw bundle](/dotnet/standard/data/sqlite/custom-versions#bundles).</span></span> <span data-ttu-id="deeb9-185">Można to obejść przez utworzenie niestandardowego [dostawcy SQLitePCLRaw](/dotnet/standard/data/sqlite/custom-versions#sqlitepclraw-providers) , który korzysta z biblioteki programu SQLite systemu, lub można zainstalować niestandardową kompilację SpatiaLite.</span><span class="sxs-lookup"><span data-stu-id="deeb9-185">You can work around this by either creating a custom [SQLitePCLRaw provider](/dotnet/standard/data/sqlite/custom-versions#sqlitepclraw-providers) that uses the system SQLite library, or you can install a custom build of SpatiaLite disabling PROJ support.</span></span>

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

### <a name="configuring-srid"></a><span data-ttu-id="deeb9-186">Konfigurowanie SRID</span><span class="sxs-lookup"><span data-stu-id="deeb9-186">Configuring SRID</span></span>

<span data-ttu-id="deeb9-187">W SpatiaLite, kolumny muszą określać SRID na kolumnę.</span><span class="sxs-lookup"><span data-stu-id="deeb9-187">In SpatiaLite, columns need to specify an SRID per column.</span></span> <span data-ttu-id="deeb9-188">Domyślny SRID jest `0`.</span><span class="sxs-lookup"><span data-stu-id="deeb9-188">The default SRID is `0`.</span></span> <span data-ttu-id="deeb9-189">Określ inny SRID przy użyciu metody ForSqliteHasSrid.</span><span class="sxs-lookup"><span data-stu-id="deeb9-189">Specify a different SRID using the ForSqliteHasSrid method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

### <a name="dimension"></a><span data-ttu-id="deeb9-190">Wymiar</span><span class="sxs-lookup"><span data-stu-id="deeb9-190">Dimension</span></span>

<span data-ttu-id="deeb9-191">Podobnie jak w przypadku SRID, wymiar kolumny (lub współrzędnej) jest również określony jako część kolumny.</span><span class="sxs-lookup"><span data-stu-id="deeb9-191">Similar to SRID, a column's dimension (or ordinates) is also specified as part of the column.</span></span> <span data-ttu-id="deeb9-192">Domyślnymi współrzędnymi są X i Y. Włącz dodatkowe współrzędne (Z i M) przy użyciu metody ForSqliteHasDimension.</span><span class="sxs-lookup"><span data-stu-id="deeb9-192">The default ordinates are X and Y. Enable additional ordinates (Z and M) using the ForSqliteHasDimension method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasDimension(Ordinates.XYZ);
```

## <a name="translated-operations"></a><span data-ttu-id="deeb9-193">Operacje tłumaczone</span><span class="sxs-lookup"><span data-stu-id="deeb9-193">Translated Operations</span></span>

<span data-ttu-id="deeb9-194">W tej tabeli przedstawiono, które elementy członkowskie NKTY przerwania są tłumaczone na SQL przez każdego dostawcę EF Core.</span><span class="sxs-lookup"><span data-stu-id="deeb9-194">This table shows which NTS members are translated into SQL by each EF Core provider.</span></span>

<span data-ttu-id="deeb9-195">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="deeb9-195">NetTopologySuite</span></span> | <span data-ttu-id="deeb9-196">SQL Server (Geometria)</span><span class="sxs-lookup"><span data-stu-id="deeb9-196">SQL Server (geometry)</span></span> | <span data-ttu-id="deeb9-197">SQL Server (Geografia)</span><span class="sxs-lookup"><span data-stu-id="deeb9-197">SQL Server (geography)</span></span> | <span data-ttu-id="deeb9-198">SQLite</span><span class="sxs-lookup"><span data-stu-id="deeb9-198">SQLite</span></span> | <span data-ttu-id="deeb9-199">Npgsql</span><span class="sxs-lookup"><span data-stu-id="deeb9-199">Npgsql</span></span>
--- |:---:|:---:|:---:|:---:
<span data-ttu-id="deeb9-200">Geometria. obszar</span><span class="sxs-lookup"><span data-stu-id="deeb9-200">Geometry.Area</span></span> | <span data-ttu-id="deeb9-201">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-201">✔</span></span> | <span data-ttu-id="deeb9-202">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-202">✔</span></span> | <span data-ttu-id="deeb9-203">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-203">✔</span></span> | <span data-ttu-id="deeb9-204">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-204">✔</span></span>
<span data-ttu-id="deeb9-205">Geometry. AsBinary ()</span><span class="sxs-lookup"><span data-stu-id="deeb9-205">Geometry.AsBinary()</span></span> | <span data-ttu-id="deeb9-206">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-206">✔</span></span> | <span data-ttu-id="deeb9-207">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-207">✔</span></span> | <span data-ttu-id="deeb9-208">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-208">✔</span></span> | <span data-ttu-id="deeb9-209">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-209">✔</span></span>
<span data-ttu-id="deeb9-210">Geometry. AsText ()</span><span class="sxs-lookup"><span data-stu-id="deeb9-210">Geometry.AsText()</span></span> | <span data-ttu-id="deeb9-211">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-211">✔</span></span> | <span data-ttu-id="deeb9-212">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-212">✔</span></span> | <span data-ttu-id="deeb9-213">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-213">✔</span></span> | <span data-ttu-id="deeb9-214">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-214">✔</span></span>
<span data-ttu-id="deeb9-215">Geometria. granica</span><span class="sxs-lookup"><span data-stu-id="deeb9-215">Geometry.Boundary</span></span> | <span data-ttu-id="deeb9-216">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-216">✔</span></span> | | <span data-ttu-id="deeb9-217">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-217">✔</span></span> | <span data-ttu-id="deeb9-218">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-218">✔</span></span>
<span data-ttu-id="deeb9-219">Geometry. Buffer (Double)</span><span class="sxs-lookup"><span data-stu-id="deeb9-219">Geometry.Buffer(double)</span></span> | <span data-ttu-id="deeb9-220">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-220">✔</span></span> | <span data-ttu-id="deeb9-221">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-221">✔</span></span> | <span data-ttu-id="deeb9-222">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-222">✔</span></span> | <span data-ttu-id="deeb9-223">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-223">✔</span></span>
<span data-ttu-id="deeb9-224">Geometry. Buffer (Double, int)</span><span class="sxs-lookup"><span data-stu-id="deeb9-224">Geometry.Buffer(double, int)</span></span> | | | <span data-ttu-id="deeb9-225">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-225">✔</span></span> | <span data-ttu-id="deeb9-226">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-226">✔</span></span>
<span data-ttu-id="deeb9-227">Geometria. centroida</span><span class="sxs-lookup"><span data-stu-id="deeb9-227">Geometry.Centroid</span></span> | <span data-ttu-id="deeb9-228">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-228">✔</span></span> | | <span data-ttu-id="deeb9-229">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-229">✔</span></span> | <span data-ttu-id="deeb9-230">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-230">✔</span></span>
<span data-ttu-id="deeb9-231">Geometry. Contains (Geometria)</span><span class="sxs-lookup"><span data-stu-id="deeb9-231">Geometry.Contains(Geometry)</span></span> | <span data-ttu-id="deeb9-232">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-232">✔</span></span> | <span data-ttu-id="deeb9-233">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-233">✔</span></span> | <span data-ttu-id="deeb9-234">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-234">✔</span></span> | <span data-ttu-id="deeb9-235">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-235">✔</span></span>
<span data-ttu-id="deeb9-236">Geometry. ConvexHull ()</span><span class="sxs-lookup"><span data-stu-id="deeb9-236">Geometry.ConvexHull()</span></span> | <span data-ttu-id="deeb9-237">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-237">✔</span></span> | <span data-ttu-id="deeb9-238">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-238">✔</span></span> | <span data-ttu-id="deeb9-239">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-239">✔</span></span> | <span data-ttu-id="deeb9-240">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-240">✔</span></span>
<span data-ttu-id="deeb9-241">Geometry. CoveredBy (Geometria)</span><span class="sxs-lookup"><span data-stu-id="deeb9-241">Geometry.CoveredBy(Geometry)</span></span> | | | <span data-ttu-id="deeb9-242">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-242">✔</span></span> | <span data-ttu-id="deeb9-243">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-243">✔</span></span>
<span data-ttu-id="deeb9-244">Geometria. okładki (Geometria)</span><span class="sxs-lookup"><span data-stu-id="deeb9-244">Geometry.Covers(Geometry)</span></span> | | | <span data-ttu-id="deeb9-245">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-245">✔</span></span> | <span data-ttu-id="deeb9-246">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-246">✔</span></span>
<span data-ttu-id="deeb9-247">Geometry. crosss (Geometria)</span><span class="sxs-lookup"><span data-stu-id="deeb9-247">Geometry.Crosses(Geometry)</span></span> | <span data-ttu-id="deeb9-248">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-248">✔</span></span> | | <span data-ttu-id="deeb9-249">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-249">✔</span></span> | <span data-ttu-id="deeb9-250">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-250">✔</span></span>
<span data-ttu-id="deeb9-251">Geometry. różnica (Geometria)</span><span class="sxs-lookup"><span data-stu-id="deeb9-251">Geometry.Difference(Geometry)</span></span> | <span data-ttu-id="deeb9-252">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-252">✔</span></span> | <span data-ttu-id="deeb9-253">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-253">✔</span></span> | <span data-ttu-id="deeb9-254">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-254">✔</span></span> | <span data-ttu-id="deeb9-255">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-255">✔</span></span>
<span data-ttu-id="deeb9-256">Geometria. wymiar</span><span class="sxs-lookup"><span data-stu-id="deeb9-256">Geometry.Dimension</span></span> | <span data-ttu-id="deeb9-257">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-257">✔</span></span> | <span data-ttu-id="deeb9-258">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-258">✔</span></span> | <span data-ttu-id="deeb9-259">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-259">✔</span></span> | <span data-ttu-id="deeb9-260">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-260">✔</span></span>
<span data-ttu-id="deeb9-261">Geometria. rozłączna (Geometria)</span><span class="sxs-lookup"><span data-stu-id="deeb9-261">Geometry.Disjoint(Geometry)</span></span> | <span data-ttu-id="deeb9-262">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-262">✔</span></span> | <span data-ttu-id="deeb9-263">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-263">✔</span></span> | <span data-ttu-id="deeb9-264">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-264">✔</span></span> | <span data-ttu-id="deeb9-265">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-265">✔</span></span>
<span data-ttu-id="deeb9-266">Geometria. Distance (Geometria)</span><span class="sxs-lookup"><span data-stu-id="deeb9-266">Geometry.Distance(Geometry)</span></span> | <span data-ttu-id="deeb9-267">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-267">✔</span></span> | <span data-ttu-id="deeb9-268">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-268">✔</span></span> | <span data-ttu-id="deeb9-269">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-269">✔</span></span> | <span data-ttu-id="deeb9-270">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-270">✔</span></span>
<span data-ttu-id="deeb9-271">Geometria. koperta</span><span class="sxs-lookup"><span data-stu-id="deeb9-271">Geometry.Envelope</span></span> | <span data-ttu-id="deeb9-272">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-272">✔</span></span> | | <span data-ttu-id="deeb9-273">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-273">✔</span></span> | <span data-ttu-id="deeb9-274">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-274">✔</span></span>
<span data-ttu-id="deeb9-275">Geometry. EqualsExact (Geometria)</span><span class="sxs-lookup"><span data-stu-id="deeb9-275">Geometry.EqualsExact(Geometry)</span></span> | | | | <span data-ttu-id="deeb9-276">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-276">✔</span></span>
<span data-ttu-id="deeb9-277">Geometry. EqualsTopologically (Geometria)</span><span class="sxs-lookup"><span data-stu-id="deeb9-277">Geometry.EqualsTopologically(Geometry)</span></span> | <span data-ttu-id="deeb9-278">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-278">✔</span></span> | <span data-ttu-id="deeb9-279">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-279">✔</span></span> | <span data-ttu-id="deeb9-280">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-280">✔</span></span> | <span data-ttu-id="deeb9-281">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-281">✔</span></span>
<span data-ttu-id="deeb9-282">Geometry. Geometrytype</span><span class="sxs-lookup"><span data-stu-id="deeb9-282">Geometry.GeometryType</span></span> | <span data-ttu-id="deeb9-283">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-283">✔</span></span> | <span data-ttu-id="deeb9-284">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-284">✔</span></span> | <span data-ttu-id="deeb9-285">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-285">✔</span></span> | <span data-ttu-id="deeb9-286">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-286">✔</span></span>
<span data-ttu-id="deeb9-287">Geometry. GetGeometryN (int)</span><span class="sxs-lookup"><span data-stu-id="deeb9-287">Geometry.GetGeometryN(int)</span></span> | <span data-ttu-id="deeb9-288">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-288">✔</span></span> | | <span data-ttu-id="deeb9-289">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-289">✔</span></span> | <span data-ttu-id="deeb9-290">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-290">✔</span></span>
<span data-ttu-id="deeb9-291">Geometria. InteriorPoint</span><span class="sxs-lookup"><span data-stu-id="deeb9-291">Geometry.InteriorPoint</span></span> | <span data-ttu-id="deeb9-292">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-292">✔</span></span> | | <span data-ttu-id="deeb9-293">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-293">✔</span></span> | <span data-ttu-id="deeb9-294">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-294">✔</span></span>
<span data-ttu-id="deeb9-295">Geometria. część wspólna (Geometria)</span><span class="sxs-lookup"><span data-stu-id="deeb9-295">Geometry.Intersection(Geometry)</span></span> | <span data-ttu-id="deeb9-296">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-296">✔</span></span> | <span data-ttu-id="deeb9-297">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-297">✔</span></span> | <span data-ttu-id="deeb9-298">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-298">✔</span></span> | <span data-ttu-id="deeb9-299">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-299">✔</span></span>
<span data-ttu-id="deeb9-300">Geometria. Intersects (Geometria)</span><span class="sxs-lookup"><span data-stu-id="deeb9-300">Geometry.Intersects(Geometry)</span></span> | <span data-ttu-id="deeb9-301">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-301">✔</span></span> | <span data-ttu-id="deeb9-302">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-302">✔</span></span> | <span data-ttu-id="deeb9-303">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-303">✔</span></span> | <span data-ttu-id="deeb9-304">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-304">✔</span></span>
<span data-ttu-id="deeb9-305">Geometria. IsEmpty</span><span class="sxs-lookup"><span data-stu-id="deeb9-305">Geometry.IsEmpty</span></span> | <span data-ttu-id="deeb9-306">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-306">✔</span></span> | <span data-ttu-id="deeb9-307">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-307">✔</span></span> | <span data-ttu-id="deeb9-308">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-308">✔</span></span> | <span data-ttu-id="deeb9-309">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-309">✔</span></span>
<span data-ttu-id="deeb9-310">Geometria. IsSimple</span><span class="sxs-lookup"><span data-stu-id="deeb9-310">Geometry.IsSimple</span></span> | <span data-ttu-id="deeb9-311">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-311">✔</span></span> | | <span data-ttu-id="deeb9-312">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-312">✔</span></span> | <span data-ttu-id="deeb9-313">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-313">✔</span></span>
<span data-ttu-id="deeb9-314">Geometria. IsValid</span><span class="sxs-lookup"><span data-stu-id="deeb9-314">Geometry.IsValid</span></span> | <span data-ttu-id="deeb9-315">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-315">✔</span></span> | <span data-ttu-id="deeb9-316">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-316">✔</span></span> | <span data-ttu-id="deeb9-317">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-317">✔</span></span> | <span data-ttu-id="deeb9-318">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-318">✔</span></span>
<span data-ttu-id="deeb9-319">Geometry. IsWithinDistance (Geometria, Double)</span><span class="sxs-lookup"><span data-stu-id="deeb9-319">Geometry.IsWithinDistance(Geometry, double)</span></span> | <span data-ttu-id="deeb9-320">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-320">✔</span></span> | | <span data-ttu-id="deeb9-321">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-321">✔</span></span> | <span data-ttu-id="deeb9-322">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-322">✔</span></span>
<span data-ttu-id="deeb9-323">Geometria. Długość</span><span class="sxs-lookup"><span data-stu-id="deeb9-323">Geometry.Length</span></span> | <span data-ttu-id="deeb9-324">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-324">✔</span></span> | <span data-ttu-id="deeb9-325">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-325">✔</span></span> | <span data-ttu-id="deeb9-326">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-326">✔</span></span> | <span data-ttu-id="deeb9-327">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-327">✔</span></span>
<span data-ttu-id="deeb9-328">Geometria. NumGeometries</span><span class="sxs-lookup"><span data-stu-id="deeb9-328">Geometry.NumGeometries</span></span> | <span data-ttu-id="deeb9-329">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-329">✔</span></span> | <span data-ttu-id="deeb9-330">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-330">✔</span></span> | <span data-ttu-id="deeb9-331">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-331">✔</span></span> | <span data-ttu-id="deeb9-332">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-332">✔</span></span>
<span data-ttu-id="deeb9-333">Geometria. NumPoints</span><span class="sxs-lookup"><span data-stu-id="deeb9-333">Geometry.NumPoints</span></span> | <span data-ttu-id="deeb9-334">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-334">✔</span></span> | <span data-ttu-id="deeb9-335">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-335">✔</span></span> | <span data-ttu-id="deeb9-336">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-336">✔</span></span> | <span data-ttu-id="deeb9-337">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-337">✔</span></span>
<span data-ttu-id="deeb9-338">Geometria. OgcGeometryType</span><span class="sxs-lookup"><span data-stu-id="deeb9-338">Geometry.OgcGeometryType</span></span> | <span data-ttu-id="deeb9-339">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-339">✔</span></span> | <span data-ttu-id="deeb9-340">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-340">✔</span></span> | <span data-ttu-id="deeb9-341">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-341">✔</span></span> | <span data-ttu-id="deeb9-342">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-342">✔</span></span>
<span data-ttu-id="deeb9-343">Geometria. nakładanie się (Geometria)</span><span class="sxs-lookup"><span data-stu-id="deeb9-343">Geometry.Overlaps(Geometry)</span></span> | <span data-ttu-id="deeb9-344">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-344">✔</span></span> | <span data-ttu-id="deeb9-345">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-345">✔</span></span> | <span data-ttu-id="deeb9-346">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-346">✔</span></span> | <span data-ttu-id="deeb9-347">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-347">✔</span></span>
<span data-ttu-id="deeb9-348">Geometria. PointOnSurface</span><span class="sxs-lookup"><span data-stu-id="deeb9-348">Geometry.PointOnSurface</span></span> | <span data-ttu-id="deeb9-349">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-349">✔</span></span> | | <span data-ttu-id="deeb9-350">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-350">✔</span></span> | <span data-ttu-id="deeb9-351">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-351">✔</span></span>
<span data-ttu-id="deeb9-352">Geometry. rerelacja (Geometria, ciąg)</span><span class="sxs-lookup"><span data-stu-id="deeb9-352">Geometry.Relate(Geometry, string)</span></span> | <span data-ttu-id="deeb9-353">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-353">✔</span></span> | | <span data-ttu-id="deeb9-354">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-354">✔</span></span> | <span data-ttu-id="deeb9-355">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-355">✔</span></span>
<span data-ttu-id="deeb9-356">Geometry. Reverse ()</span><span class="sxs-lookup"><span data-stu-id="deeb9-356">Geometry.Reverse()</span></span> | | | <span data-ttu-id="deeb9-357">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-357">✔</span></span> | <span data-ttu-id="deeb9-358">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-358">✔</span></span>
<span data-ttu-id="deeb9-359">Geometria. SRID</span><span class="sxs-lookup"><span data-stu-id="deeb9-359">Geometry.SRID</span></span> | <span data-ttu-id="deeb9-360">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-360">✔</span></span> | <span data-ttu-id="deeb9-361">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-361">✔</span></span> | <span data-ttu-id="deeb9-362">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-362">✔</span></span> | <span data-ttu-id="deeb9-363">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-363">✔</span></span>
<span data-ttu-id="deeb9-364">Geometry. SymmetricDifference (Geometria)</span><span class="sxs-lookup"><span data-stu-id="deeb9-364">Geometry.SymmetricDifference(Geometry)</span></span> | <span data-ttu-id="deeb9-365">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-365">✔</span></span> | <span data-ttu-id="deeb9-366">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-366">✔</span></span> | <span data-ttu-id="deeb9-367">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-367">✔</span></span> | <span data-ttu-id="deeb9-368">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-368">✔</span></span>
<span data-ttu-id="deeb9-369">Geometry. ToBinary ()</span><span class="sxs-lookup"><span data-stu-id="deeb9-369">Geometry.ToBinary()</span></span> | <span data-ttu-id="deeb9-370">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-370">✔</span></span> | <span data-ttu-id="deeb9-371">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-371">✔</span></span> | <span data-ttu-id="deeb9-372">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-372">✔</span></span> | <span data-ttu-id="deeb9-373">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-373">✔</span></span>
<span data-ttu-id="deeb9-374">Geometry. ToText ()</span><span class="sxs-lookup"><span data-stu-id="deeb9-374">Geometry.ToText()</span></span> | <span data-ttu-id="deeb9-375">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-375">✔</span></span> | <span data-ttu-id="deeb9-376">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-376">✔</span></span> | <span data-ttu-id="deeb9-377">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-377">✔</span></span> | <span data-ttu-id="deeb9-378">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-378">✔</span></span>
<span data-ttu-id="deeb9-379">Geometria. touch (Geometria)</span><span class="sxs-lookup"><span data-stu-id="deeb9-379">Geometry.Touches(Geometry)</span></span> | <span data-ttu-id="deeb9-380">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-380">✔</span></span> | | <span data-ttu-id="deeb9-381">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-381">✔</span></span> | <span data-ttu-id="deeb9-382">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-382">✔</span></span>
<span data-ttu-id="deeb9-383">Geometry. Union ()</span><span class="sxs-lookup"><span data-stu-id="deeb9-383">Geometry.Union()</span></span> | | | <span data-ttu-id="deeb9-384">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-384">✔</span></span> | <span data-ttu-id="deeb9-385">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-385">✔</span></span>
<span data-ttu-id="deeb9-386">Geometry. Union (Geometria)</span><span class="sxs-lookup"><span data-stu-id="deeb9-386">Geometry.Union(Geometry)</span></span> | <span data-ttu-id="deeb9-387">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-387">✔</span></span> | <span data-ttu-id="deeb9-388">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-388">✔</span></span> | <span data-ttu-id="deeb9-389">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-389">✔</span></span> | <span data-ttu-id="deeb9-390">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-390">✔</span></span>
<span data-ttu-id="deeb9-391">Geometria. w elemencie (Geometria)</span><span class="sxs-lookup"><span data-stu-id="deeb9-391">Geometry.Within(Geometry)</span></span> | <span data-ttu-id="deeb9-392">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-392">✔</span></span> | <span data-ttu-id="deeb9-393">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-393">✔</span></span> | <span data-ttu-id="deeb9-394">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-394">✔</span></span> | <span data-ttu-id="deeb9-395">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-395">✔</span></span>
<span data-ttu-id="deeb9-396">GeometryCollection. Count</span><span class="sxs-lookup"><span data-stu-id="deeb9-396">GeometryCollection.Count</span></span> | <span data-ttu-id="deeb9-397">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-397">✔</span></span> | <span data-ttu-id="deeb9-398">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-398">✔</span></span> | <span data-ttu-id="deeb9-399">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-399">✔</span></span> | <span data-ttu-id="deeb9-400">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-400">✔</span></span>
<span data-ttu-id="deeb9-401">GeometryCollection [int]</span><span class="sxs-lookup"><span data-stu-id="deeb9-401">GeometryCollection[int]</span></span> | <span data-ttu-id="deeb9-402">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-402">✔</span></span> | <span data-ttu-id="deeb9-403">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-403">✔</span></span> | <span data-ttu-id="deeb9-404">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-404">✔</span></span> | <span data-ttu-id="deeb9-405">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-405">✔</span></span>
<span data-ttu-id="deeb9-406">LineString. Count</span><span class="sxs-lookup"><span data-stu-id="deeb9-406">LineString.Count</span></span> | <span data-ttu-id="deeb9-407">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-407">✔</span></span> | <span data-ttu-id="deeb9-408">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-408">✔</span></span> | <span data-ttu-id="deeb9-409">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-409">✔</span></span> | <span data-ttu-id="deeb9-410">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-410">✔</span></span>
<span data-ttu-id="deeb9-411">LineString. EndPoint</span><span class="sxs-lookup"><span data-stu-id="deeb9-411">LineString.EndPoint</span></span> | <span data-ttu-id="deeb9-412">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-412">✔</span></span> | <span data-ttu-id="deeb9-413">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-413">✔</span></span> | <span data-ttu-id="deeb9-414">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-414">✔</span></span> | <span data-ttu-id="deeb9-415">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-415">✔</span></span>
<span data-ttu-id="deeb9-416">LineString. GetPointN (int)</span><span class="sxs-lookup"><span data-stu-id="deeb9-416">LineString.GetPointN(int)</span></span> | <span data-ttu-id="deeb9-417">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-417">✔</span></span> | <span data-ttu-id="deeb9-418">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-418">✔</span></span> | <span data-ttu-id="deeb9-419">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-419">✔</span></span> | <span data-ttu-id="deeb9-420">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-420">✔</span></span>
<span data-ttu-id="deeb9-421">LineString. IsClosed</span><span class="sxs-lookup"><span data-stu-id="deeb9-421">LineString.IsClosed</span></span> | <span data-ttu-id="deeb9-422">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-422">✔</span></span> | <span data-ttu-id="deeb9-423">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-423">✔</span></span> | <span data-ttu-id="deeb9-424">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-424">✔</span></span> | <span data-ttu-id="deeb9-425">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-425">✔</span></span>
<span data-ttu-id="deeb9-426">LineString. IsRing</span><span class="sxs-lookup"><span data-stu-id="deeb9-426">LineString.IsRing</span></span> | <span data-ttu-id="deeb9-427">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-427">✔</span></span> | | <span data-ttu-id="deeb9-428">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-428">✔</span></span> | <span data-ttu-id="deeb9-429">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-429">✔</span></span>
<span data-ttu-id="deeb9-430">LineString. StartPoint</span><span class="sxs-lookup"><span data-stu-id="deeb9-430">LineString.StartPoint</span></span> | <span data-ttu-id="deeb9-431">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-431">✔</span></span> | <span data-ttu-id="deeb9-432">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-432">✔</span></span> | <span data-ttu-id="deeb9-433">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-433">✔</span></span> | <span data-ttu-id="deeb9-434">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-434">✔</span></span>
<span data-ttu-id="deeb9-435">MultiLineString. IsClosed</span><span class="sxs-lookup"><span data-stu-id="deeb9-435">MultiLineString.IsClosed</span></span> | <span data-ttu-id="deeb9-436">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-436">✔</span></span> | <span data-ttu-id="deeb9-437">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-437">✔</span></span> | <span data-ttu-id="deeb9-438">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-438">✔</span></span> | <span data-ttu-id="deeb9-439">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-439">✔</span></span>
<span data-ttu-id="deeb9-440">Punkt. M</span><span class="sxs-lookup"><span data-stu-id="deeb9-440">Point.M</span></span> | <span data-ttu-id="deeb9-441">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-441">✔</span></span> | <span data-ttu-id="deeb9-442">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-442">✔</span></span> | <span data-ttu-id="deeb9-443">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-443">✔</span></span> | <span data-ttu-id="deeb9-444">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-444">✔</span></span>
<span data-ttu-id="deeb9-445">Punkt. X</span><span class="sxs-lookup"><span data-stu-id="deeb9-445">Point.X</span></span> | <span data-ttu-id="deeb9-446">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-446">✔</span></span> | <span data-ttu-id="deeb9-447">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-447">✔</span></span> | <span data-ttu-id="deeb9-448">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-448">✔</span></span> | <span data-ttu-id="deeb9-449">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-449">✔</span></span>
<span data-ttu-id="deeb9-450">Punkt. Y</span><span class="sxs-lookup"><span data-stu-id="deeb9-450">Point.Y</span></span> | <span data-ttu-id="deeb9-451">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-451">✔</span></span> | <span data-ttu-id="deeb9-452">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-452">✔</span></span> | <span data-ttu-id="deeb9-453">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-453">✔</span></span> | <span data-ttu-id="deeb9-454">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-454">✔</span></span>
<span data-ttu-id="deeb9-455">Punkt. Z</span><span class="sxs-lookup"><span data-stu-id="deeb9-455">Point.Z</span></span> | <span data-ttu-id="deeb9-456">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-456">✔</span></span> | <span data-ttu-id="deeb9-457">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-457">✔</span></span> | <span data-ttu-id="deeb9-458">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-458">✔</span></span> | <span data-ttu-id="deeb9-459">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-459">✔</span></span>
<span data-ttu-id="deeb9-460">Wielokąt. ExteriorRing</span><span class="sxs-lookup"><span data-stu-id="deeb9-460">Polygon.ExteriorRing</span></span> | <span data-ttu-id="deeb9-461">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-461">✔</span></span> | <span data-ttu-id="deeb9-462">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-462">✔</span></span> | <span data-ttu-id="deeb9-463">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-463">✔</span></span> | <span data-ttu-id="deeb9-464">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-464">✔</span></span>
<span data-ttu-id="deeb9-465">Wielokąt. GetInteriorRingN (int)</span><span class="sxs-lookup"><span data-stu-id="deeb9-465">Polygon.GetInteriorRingN(int)</span></span> | <span data-ttu-id="deeb9-466">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-466">✔</span></span> | <span data-ttu-id="deeb9-467">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-467">✔</span></span> | <span data-ttu-id="deeb9-468">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-468">✔</span></span> | <span data-ttu-id="deeb9-469">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-469">✔</span></span>
<span data-ttu-id="deeb9-470">Wielokąt. NumInteriorRings</span><span class="sxs-lookup"><span data-stu-id="deeb9-470">Polygon.NumInteriorRings</span></span> | <span data-ttu-id="deeb9-471">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-471">✔</span></span> | <span data-ttu-id="deeb9-472">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-472">✔</span></span> | <span data-ttu-id="deeb9-473">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-473">✔</span></span> | <span data-ttu-id="deeb9-474">✔</span><span class="sxs-lookup"><span data-stu-id="deeb9-474">✔</span></span>

## <a name="additional-resources"></a><span data-ttu-id="deeb9-475">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="deeb9-475">Additional resources</span></span>

* [<span data-ttu-id="deeb9-476">Dane przestrzenne w SQL Server</span><span class="sxs-lookup"><span data-stu-id="deeb9-476">Spatial Data in SQL Server</span></span>](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [<span data-ttu-id="deeb9-477">Strona główna SpatiaLite</span><span class="sxs-lookup"><span data-stu-id="deeb9-477">SpatiaLite Homepage</span></span>](https://www.gaia-gis.it/fossil/libspatialite)
* [<span data-ttu-id="deeb9-478">Dokumentacja przestrzenna Npgsql</span><span class="sxs-lookup"><span data-stu-id="deeb9-478">Npgsql Spatial Documentation</span></span>](https://www.npgsql.org/efcore/mapping/nts.html)
* [<span data-ttu-id="deeb9-479">Dokumentacja PostGIS</span><span class="sxs-lookup"><span data-stu-id="deeb9-479">PostGIS Documentation</span></span>](https://postgis.net/documentation/)
