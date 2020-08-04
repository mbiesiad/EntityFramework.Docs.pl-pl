---
title: Porównania wartości — EF Core
description: Używanie funkcji porównujących wartości do kontrolowania sposobu, w jaki EF Core porównuje wartości właściwości
author: ajcvickers
ms.date: 03/20/2020
uid: core/modeling/value-comparers
ms.openlocfilehash: fa5352129977d858d54d4aede746b320c91b0ad3
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526787"
---
# <a name="value-comparers"></a><span data-ttu-id="bd56f-103">Porównania wartości</span><span class="sxs-lookup"><span data-stu-id="bd56f-103">Value Comparers</span></span>

> [!NOTE]  
> <span data-ttu-id="bd56f-104">Ta funkcja jest nowa w EF Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="bd56f-104">This feature is new in EF Core 3.0.</span></span>

> [!TIP]  
> <span data-ttu-id="bd56f-105">Kod w tym dokumencie można znaleźć w witrynie GitHub jako [przykład możliwy do uruchomienia](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/).</span><span class="sxs-lookup"><span data-stu-id="bd56f-105">The code in this document can be found on GitHub as a [runnable sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/).</span></span>

## <a name="background"></a><span data-ttu-id="bd56f-106">Tło</span><span class="sxs-lookup"><span data-stu-id="bd56f-106">Background</span></span>

<span data-ttu-id="bd56f-107">EF Core musi porównać wartości właściwości, gdy:</span><span class="sxs-lookup"><span data-stu-id="bd56f-107">EF Core needs to compare property values when:</span></span>

* <span data-ttu-id="bd56f-108">Określanie, czy właściwość została zmieniona w ramach [wykrywania zmian w aktualizacjach](xref:core/saving/basic)</span><span class="sxs-lookup"><span data-stu-id="bd56f-108">Determining whether a property has been changed as part of [detecting changes for updates](xref:core/saving/basic)</span></span>
* <span data-ttu-id="bd56f-109">Określanie, czy dwie wartości klucza są takie same podczas rozpoznawania relacji</span><span class="sxs-lookup"><span data-stu-id="bd56f-109">Determining whether two key values are the same when resolving relationships</span></span> 

<span data-ttu-id="bd56f-110">Jest to obsługiwane automatycznie dla wspólnych typów pierwotnych, takich jak int, bool, DateTime itd.</span><span class="sxs-lookup"><span data-stu-id="bd56f-110">This is handled automatically for common primitive types such as int, bool, DateTime, etc.</span></span>

<span data-ttu-id="bd56f-111">W przypadku bardziej złożonych typów, należy wybrać opcje, aby przeprowadzić porównanie.</span><span class="sxs-lookup"><span data-stu-id="bd56f-111">For more complex types, choices need to be made as to how to do the comparison.</span></span>
<span data-ttu-id="bd56f-112">Na przykład można porównać tablicę bajtową:</span><span class="sxs-lookup"><span data-stu-id="bd56f-112">For example, a byte array could be compared:</span></span>

* <span data-ttu-id="bd56f-113">Według odniesienia, w taki sposób, aby różnica była wykrywana tylko wtedy, gdy jest używana nowa tablica bajtów</span><span class="sxs-lookup"><span data-stu-id="bd56f-113">By reference, such that a difference is only detected if a new byte array is used</span></span>
* <span data-ttu-id="bd56f-114">Przez głębokie porównanie, takie, że mutacja bajtów w tablicy jest wykrywana</span><span class="sxs-lookup"><span data-stu-id="bd56f-114">By deep comparison, such that mutation of the bytes in the array is detected</span></span>

<span data-ttu-id="bd56f-115">Domyślnie EF Core używa pierwszego z tych podejścia w przypadku tablic nieopartych na kluczowym bajcie.</span><span class="sxs-lookup"><span data-stu-id="bd56f-115">By default, EF Core uses the first of these approaches for non-key byte arrays.</span></span>
<span data-ttu-id="bd56f-116">Oznacza to, że porównywane są tylko odwołania, a zmiana jest wykrywana tylko wtedy, gdy istniejąca tablica bajtów jest zastępowana nową.</span><span class="sxs-lookup"><span data-stu-id="bd56f-116">That is, only references are compared and a change is detected only when an existing byte array is replaced with a new one.</span></span>
<span data-ttu-id="bd56f-117">Jest to pragmatyczna decyzja, która pozwala uniknąć głębokiego porównania wielu tablic dużych bajtów podczas wykonywania metody SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="bd56f-117">This is a pragmatic decision that avoids deep comparison of many large byte arrays when executing SaveChanges.</span></span>
<span data-ttu-id="bd56f-118">Jednak typowy scenariusz zamiany, powiedzmy, obraz z innym obrazem jest obsługiwany w sposób efektywny.</span><span class="sxs-lookup"><span data-stu-id="bd56f-118">But the common scenario of replacing, say, an image with a different image is handled in a performant way.</span></span>

<span data-ttu-id="bd56f-119">Z drugiej strony równość odwołania nie będzie działała, gdy tablice bajtowe są używane do reprezentowania kluczy binarnych.</span><span class="sxs-lookup"><span data-stu-id="bd56f-119">On the other hand, reference equality would not work when byte arrays are used to represent binary keys.</span></span>
<span data-ttu-id="bd56f-120">Jest bardzo mało prawdopodobne, że właściwość FK jest ustawiona na to _samo wystąpienie_ , co Właściwość PK, do której ma być porównywana.</span><span class="sxs-lookup"><span data-stu-id="bd56f-120">It's very unlikely that an FK property is set to the _same instance_ as a PK property to which it needs to be compared.</span></span>
<span data-ttu-id="bd56f-121">W związku z tym EF Core używa głębokiego porównania dla tablic bajtowych działających jako klucze.</span><span class="sxs-lookup"><span data-stu-id="bd56f-121">Therefore, EF Core uses deep comparisons for byte arrays acting as keys.</span></span>
<span data-ttu-id="bd56f-122">Jest to mało prawdopodobne, ponieważ klucze binarne są zwykle krótkie.</span><span class="sxs-lookup"><span data-stu-id="bd56f-122">This is unlikely to have a big performance hit since binary keys are usually short.</span></span>

### <a name="snapshots"></a><span data-ttu-id="bd56f-123">Migawki</span><span class="sxs-lookup"><span data-stu-id="bd56f-123">Snapshots</span></span>

<span data-ttu-id="bd56f-124">Głębokie porównania typów modyfikowalnych oznacza, że EF Core wymaga możliwości utworzenia głębokiej "migawki" wartości właściwości.</span><span class="sxs-lookup"><span data-stu-id="bd56f-124">Deep comparisons on mutable types means that EF Core needs the ability to create a deep "snapshot" of the property value.</span></span>
<span data-ttu-id="bd56f-125">Skopiowanie odwołania zamiast tego spowodowało mutację zarówno bieżącej wartości, jak i migawki, ponieważ są one _tym samym obiektem_.</span><span class="sxs-lookup"><span data-stu-id="bd56f-125">Just copying the reference instead would result in mutating both the current value and the snapshot, since they are _the same object_.</span></span>
<span data-ttu-id="bd56f-126">W związku z tym, gdy głębokie porównania są używane dla modyfikowalnych typów, wymagana jest również Szczegółowa snapshotting.</span><span class="sxs-lookup"><span data-stu-id="bd56f-126">Therefore, when deep comparisons are used on mutable types, deep snapshotting is also required.</span></span>

## <a name="properties-with-value-converters"></a><span data-ttu-id="bd56f-127">Właściwości z konwerterami wartości</span><span class="sxs-lookup"><span data-stu-id="bd56f-127">Properties with value converters</span></span>

<span data-ttu-id="bd56f-128">W powyższym przypadku EF Core ma obsługę mapowania natywnego dla tablic bajtowych i dlatego mogą automatycznie wybierać odpowiednie wartości domyślne.</span><span class="sxs-lookup"><span data-stu-id="bd56f-128">In the case above, EF Core has native mapping support for byte arrays and so can automatically choose appropriate defaults.</span></span>
<span data-ttu-id="bd56f-129">Jeśli jednak właściwość jest zamapowana za pomocą [konwertera wartości](xref:core/modeling/value-conversions), EF Core nie zawsze określać odpowiedniego porównania do użycia.</span><span class="sxs-lookup"><span data-stu-id="bd56f-129">However, if the property is mapped through a [value converter](xref:core/modeling/value-conversions), then EF Core can't always determine the appropriate comparison to use.</span></span>
<span data-ttu-id="bd56f-130">Zamiast tego, EF Core zawsze używa domyślnego porównania równości zdefiniowanego przez typ właściwości.</span><span class="sxs-lookup"><span data-stu-id="bd56f-130">Instead, EF Core always uses the default equality comparison defined by the type of the property.</span></span>
<span data-ttu-id="bd56f-131">Jest to często poprawne, ale może być konieczne przesłanianie podczas mapowania bardziej złożonych typów.</span><span class="sxs-lookup"><span data-stu-id="bd56f-131">This is often correct, but may need to be overridden when mapping more complex types.</span></span>

### <a name="simple-immutable-classes"></a><span data-ttu-id="bd56f-132">Proste klasy niemodyfikowalne</span><span class="sxs-lookup"><span data-stu-id="bd56f-132">Simple immutable classes</span></span>

<span data-ttu-id="bd56f-133">Rozważmy właściwość, która używa konwertera wartości do mapowania prostej, niezmiennej klasy.</span><span class="sxs-lookup"><span data-stu-id="bd56f-133">Consider a property that uses a value converter to map a simple, immutable class.</span></span>

[!code-csharp[SimpleImmutableClass](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=SimpleImmutableClass)]

[!code-csharp[ConfigureImmutableClassProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=ConfigureImmutableClassProperty)]

<span data-ttu-id="bd56f-134">Właściwości tego typu nie wymagają specjalnych porównań ani migawek, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="bd56f-134">Properties of this type do not need special comparisons or snapshots because:</span></span>
* <span data-ttu-id="bd56f-135">Równość została zastąpiona, tak aby różne wystąpienia były porównywane prawidłowo</span><span class="sxs-lookup"><span data-stu-id="bd56f-135">Equality is overridden so that different instances will compare correctly</span></span>
* <span data-ttu-id="bd56f-136">Typ jest niezmienny, dlatego nie ma możliwości mutacji wartości migawki</span><span class="sxs-lookup"><span data-stu-id="bd56f-136">The type is immutable, so there is no chance of mutating a snapshot value</span></span>

<span data-ttu-id="bd56f-137">Dlatego w tym przypadku domyślne zachowanie EF Core jest zgodne z oczekiwaniami.</span><span class="sxs-lookup"><span data-stu-id="bd56f-137">So in this case the default behavior of EF Core is fine as it is.</span></span>

### <a name="simple-immutable-structs"></a><span data-ttu-id="bd56f-138">Proste niezmienne struktury</span><span class="sxs-lookup"><span data-stu-id="bd56f-138">Simple immutable Structs</span></span>

<span data-ttu-id="bd56f-139">Mapowanie dla prostych struktur jest również proste i nie wymaga żadnych specjalnych porównań ani snapshotting.</span><span class="sxs-lookup"><span data-stu-id="bd56f-139">The mapping for simple structs is also simple and requires no special comparers or snapshotting.</span></span>

[!code-csharp[SimpleImmutableStruct](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=SimpleImmutableStruct)]

[!code-csharp[ConfigureImmutableStructProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=ConfigureImmutableStructProperty)]

<span data-ttu-id="bd56f-140">EF Core ma wbudowaną obsługę generowania skompilowanych, składowych porównania właściwości struktury.</span><span class="sxs-lookup"><span data-stu-id="bd56f-140">EF Core has built-in support for generating compiled, memberwise comparisons of struct properties.</span></span>
<span data-ttu-id="bd56f-141">Oznacza to, że struktury nie muszą mieć równej równości dla EF, ale nadal można to zrobić z [innych przyczyn](/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-define-value-equality-for-a-type).</span><span class="sxs-lookup"><span data-stu-id="bd56f-141">This means structs don't need to have equality overridden for EF, but you may still choose to do this for [other reasons](/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-define-value-equality-for-a-type).</span></span>
<span data-ttu-id="bd56f-142">Ponadto specjalna snapshotting nie jest wymagana, ponieważ struktury są niezmienne i zawsze składowych kopiowane.</span><span class="sxs-lookup"><span data-stu-id="bd56f-142">Also, special snapshotting is not needed since structs immutable and are always memberwise copied anyway.</span></span>
<span data-ttu-id="bd56f-143">(Dotyczy to również niemodyfikowalnych struktur, ale [ogólnie rzecz biorąc, należy unikać modyfikowalnych struktur](/dotnet/csharp/write-safe-efficient-code)).</span><span class="sxs-lookup"><span data-stu-id="bd56f-143">(This is also true for mutable structs, but [mutable structs should in general be avoided](/dotnet/csharp/write-safe-efficient-code).)</span></span>

### <a name="mutable-classes"></a><span data-ttu-id="bd56f-144">Klasy modyfikowalne</span><span class="sxs-lookup"><span data-stu-id="bd56f-144">Mutable classes</span></span>

<span data-ttu-id="bd56f-145">Zaleca się używanie typów niemodyfikowalnych (klas lub struktur) z konwerterami wartości, gdy jest to możliwe.</span><span class="sxs-lookup"><span data-stu-id="bd56f-145">It is recommended that you use immutable types (classes or structs) with value converters when possible.</span></span>
<span data-ttu-id="bd56f-146">Jest to zwykle bardziej wydajne i ma semantykę oczyszczarki niż przy użyciu modyfikowalnego typu.</span><span class="sxs-lookup"><span data-stu-id="bd56f-146">This is usually more efficient and has cleaner semantics than using a mutable type.</span></span>

<span data-ttu-id="bd56f-147">Jednak jest to typowy sposób użycia właściwości typów, których aplikacja nie może zmienić.</span><span class="sxs-lookup"><span data-stu-id="bd56f-147">However, that being said, it is common to use properties of types that the application cannot change.</span></span>
<span data-ttu-id="bd56f-148">Na przykład mapowanie właściwości zawierającej listę liczb:</span><span class="sxs-lookup"><span data-stu-id="bd56f-148">For example, mapping a property containing a list of numbers:</span></span> 

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ListProperty)]

<span data-ttu-id="bd56f-149">[ `List<T>` Klasa](/dotnet/api/system.collections.generic.list-1?view=netstandard-2.1):</span><span class="sxs-lookup"><span data-stu-id="bd56f-149">The [`List<T>` class](/dotnet/api/system.collections.generic.list-1?view=netstandard-2.1):</span></span>
* <span data-ttu-id="bd56f-150">Ma równość odwołania; dwie listy zawierające te same wartości są traktowane jako różne.</span><span class="sxs-lookup"><span data-stu-id="bd56f-150">Has reference equality; two lists containing the same values are treated as different.</span></span>
* <span data-ttu-id="bd56f-151">Jest modyfikowalny; wartości na liście można dodawać i usuwać.</span><span class="sxs-lookup"><span data-stu-id="bd56f-151">Is mutable; values in the list can be added and removed.</span></span>

<span data-ttu-id="bd56f-152">Typowa konwersja wartości we właściwości listy może konwertować listę do i z formatu JSON:</span><span class="sxs-lookup"><span data-stu-id="bd56f-152">A typical value conversion on a list property might convert the list to and from JSON:</span></span>

[!code-csharp[ConfigureListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListProperty)]

<span data-ttu-id="bd56f-153">Następnie wymaga ustawienia a `ValueComparer<T>` we właściwości, aby wymusić EF Core poprawne porównania z tą konwersją:</span><span class="sxs-lookup"><span data-stu-id="bd56f-153">This then requires setting a `ValueComparer<T>` on the property to force EF Core use correct comparisons with this conversion:</span></span>

[!code-csharp[ConfigureListPropertyComparer](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListPropertyComparer)]

> [!NOTE]  
> <span data-ttu-id="bd56f-154">Interfejs API konstruktora modeli ("Fluent") służący do ustawiania funkcji porównującej wartości nie został jeszcze zaimplementowany.</span><span class="sxs-lookup"><span data-stu-id="bd56f-154">The model builder ("fluent") API to set a value comparer has not yet been implemented.</span></span>
> <span data-ttu-id="bd56f-155">Zamiast tego kod powyżej wywołuje SetValueComparer na niższym poziomie IMutableProperty uwidocznionym przez konstruktora jako "Metadata".</span><span class="sxs-lookup"><span data-stu-id="bd56f-155">Instead, the code above calls SetValueComparer on the lower-level IMutableProperty exposed by the builder as 'Metadata'.</span></span>

<span data-ttu-id="bd56f-156">`ValueComparer<T>`Konstruktor akceptuje trzy wyrażenia:</span><span class="sxs-lookup"><span data-stu-id="bd56f-156">The `ValueComparer<T>` constructor accepts three expressions:</span></span>
* <span data-ttu-id="bd56f-157">Wyrażenie do sprawdzania równości</span><span class="sxs-lookup"><span data-stu-id="bd56f-157">An expression for checking equality</span></span>
* <span data-ttu-id="bd56f-158">Wyrażenie służące do generowania kodu skrótu</span><span class="sxs-lookup"><span data-stu-id="bd56f-158">An expression for generating a hash code</span></span>
* <span data-ttu-id="bd56f-159">Wyrażenie służące do tworzenia migawek wartości</span><span class="sxs-lookup"><span data-stu-id="bd56f-159">An expression to snapshot a value</span></span>  

<span data-ttu-id="bd56f-160">W takim przypadku porównanie jest wykonywane przez sprawdzenie, czy sekwencje liczb są takie same.</span><span class="sxs-lookup"><span data-stu-id="bd56f-160">In this case the comparison is done by checking if the sequences of numbers are the same.</span></span>

<span data-ttu-id="bd56f-161">Analogicznie, kod skrótu jest tworzony na podstawie tej samej sekwencji.</span><span class="sxs-lookup"><span data-stu-id="bd56f-161">Likewise, the hash code is built from this same sequence.</span></span>
<span data-ttu-id="bd56f-162">(Należy zauważyć, że jest to kod skrótu dla wartości modyfikowalnych, co może [spowodować problemy](https://ericlippert.com/2011/02/28/guidelines-and-rules-for-gethashcode/).</span><span class="sxs-lookup"><span data-stu-id="bd56f-162">(Note that this is a hash code over mutable values and hence can [cause problems](https://ericlippert.com/2011/02/28/guidelines-and-rules-for-gethashcode/).</span></span>
<span data-ttu-id="bd56f-163">Zamiast tego możesz być niezmiennym.</span><span class="sxs-lookup"><span data-stu-id="bd56f-163">Be immutable instead if you can.)</span></span>

<span data-ttu-id="bd56f-164">Migawka jest tworzona przez sklonowanie listy z ToList —.</span><span class="sxs-lookup"><span data-stu-id="bd56f-164">The snapshot is created by cloning the list with ToList.</span></span>
<span data-ttu-id="bd56f-165">Jest to konieczne tylko wtedy, gdy listy zostaną poddawane mutacjom.</span><span class="sxs-lookup"><span data-stu-id="bd56f-165">Again, this is only needed if the lists are going to be mutated.</span></span>
<span data-ttu-id="bd56f-166">Zamiast tego możesz być niezmiennym.</span><span class="sxs-lookup"><span data-stu-id="bd56f-166">Be immutable instead if you can.</span></span> 

> [!NOTE]  
> <span data-ttu-id="bd56f-167">Konwertery wartości i porównania są konstruowane przy użyciu wyrażeń, a nie prostych delegatów.</span><span class="sxs-lookup"><span data-stu-id="bd56f-167">Value converters and comparers are constructed using expressions rather than simple delegates.</span></span>
> <span data-ttu-id="bd56f-168">Wynika to z faktu, że EF wstawia te wyrażenia do znacznie bardziej złożonego drzewa wyrażeń, które następnie jest kompilowane do delegata kształtu jednostki.</span><span class="sxs-lookup"><span data-stu-id="bd56f-168">This is because EF inserts these expressions into a much more complex expression tree that is then compiled into an entity shaper delegate.</span></span>
> <span data-ttu-id="bd56f-169">Koncepcyjnie jest to podobne do funkcji deokładziny kompilatora.</span><span class="sxs-lookup"><span data-stu-id="bd56f-169">Conceptually, this is similar to compiler inlining.</span></span>
> <span data-ttu-id="bd56f-170">Na przykład prostą konwersją może być tylko skompilowane rzutowanie, a nie wywołanie innej metody w celu wykonania konwersji.</span><span class="sxs-lookup"><span data-stu-id="bd56f-170">For example, a simple conversion may just be a compiled in cast, rather than a call to another method to do the conversion.</span></span>    

### <a name="key-comparers"></a><span data-ttu-id="bd56f-171">Najważniejsze funkcje porównujące</span><span class="sxs-lookup"><span data-stu-id="bd56f-171">Key comparers</span></span>

<span data-ttu-id="bd56f-172">Sekcja w tle opisuje, dlaczego porównania kluczy mogą wymagać specjalnej semantyki.</span><span class="sxs-lookup"><span data-stu-id="bd56f-172">The background section covers why key comparisons may require special semantics.</span></span>
<span data-ttu-id="bd56f-173">Upewnij się, że utworzono funkcję porównującą, która jest odpowiednia dla kluczy podczas jej ustawiania na Właściwość podstawowa, główna lub klucza obcego.</span><span class="sxs-lookup"><span data-stu-id="bd56f-173">Make sure to create a comparer that is appropriate for keys when setting it on a primary, principal, or foreign key property.</span></span>

<span data-ttu-id="bd56f-174">Używaj [SetKeyValueComparer](/dotnet/api/microsoft.entityframeworkcore.mutablepropertyextensions.setkeyvaluecomparer?view=efcore-3.1) w rzadkich przypadkach, gdy w tej samej właściwości jest wymagana inna semantyka.</span><span class="sxs-lookup"><span data-stu-id="bd56f-174">Use [SetKeyValueComparer](/dotnet/api/microsoft.entityframeworkcore.mutablepropertyextensions.setkeyvaluecomparer?view=efcore-3.1) in the rare cases where different semantics is required on the same property.</span></span>

> [!NOTE]  
> <span data-ttu-id="bd56f-175">SetStructuralComparer jest przestarzała w EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="bd56f-175">SetStructuralComparer has been obsoleted in EF Core 5.0.</span></span>
> <span data-ttu-id="bd56f-176">Zamiast tego użyj SetKeyValueComparer.</span><span class="sxs-lookup"><span data-stu-id="bd56f-176">Use SetKeyValueComparer instead.</span></span>

### <a name="overriding-defaults"></a><span data-ttu-id="bd56f-177">Zastępowanie wartości domyślnych</span><span class="sxs-lookup"><span data-stu-id="bd56f-177">Overriding defaults</span></span>

<span data-ttu-id="bd56f-178">Czasami domyślne porównanie używane przez EF Core może być nieodpowiednie.</span><span class="sxs-lookup"><span data-stu-id="bd56f-178">Sometimes the default comparison used by EF Core may not be appropriate.</span></span>
<span data-ttu-id="bd56f-179">Na przykład mutacja tablic bajtowych nie jest domyślnie wykrywana w EF Core.</span><span class="sxs-lookup"><span data-stu-id="bd56f-179">For example, mutation of byte arrays is not, by default, detected in EF Core.</span></span>
<span data-ttu-id="bd56f-180">Tę wartość można zastąpić, ustawiając inną funkcję porównującą dla właściwości:</span><span class="sxs-lookup"><span data-stu-id="bd56f-180">This can be overridden by setting a different comparer on the property:</span></span> 

[!code-csharp[OverrideComparer](../../../samples/core/Modeling/ValueConversions/OverridingByteArrayComparisons.cs?name=OverrideComparer)]

<span data-ttu-id="bd56f-181">EF Core będzie teraz porównywać sekwencje bajtów i w związku z tym wykrywa mutacje tablicy bajtów.</span><span class="sxs-lookup"><span data-stu-id="bd56f-181">EF Core will now compare byte sequences and will therefore detect byte array mutations.</span></span>
