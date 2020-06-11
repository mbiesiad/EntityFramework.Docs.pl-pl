---
title: Porównania wartości — EF Core
description: Używanie funkcji porównujących wartości do kontrolowania sposobu, w jaki EF Core porównuje wartości właściwości
author: ajcvickers
ms.date: 03/20/2020
uid: core/modeling/value-comparers
ms.openlocfilehash: 9e33bfb840606b746626daa1da7331904b495ab4
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664120"
---
# <a name="value-comparers"></a>Porównania wartości

> [!NOTE]  
> Ta funkcja jest nowa w EF Core 3,0.

> [!TIP]  
> Kod w tym dokumencie można znaleźć w witrynie GitHub jako [przykład możliwy do uruchomienia](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/).

## <a name="background"></a>Tło

EF Core musi porównać wartości właściwości, gdy:

* Określanie, czy właściwość została zmieniona w ramach [wykrywania zmian w aktualizacjach](xref:core/saving/basic)
* Określanie, czy dwie wartości klucza są takie same podczas rozpoznawania relacji 

Jest to obsługiwane automatycznie dla wspólnych typów pierwotnych, takich jak int, bool, DateTime itd.

W przypadku bardziej złożonych typów, należy wybrać opcje, aby przeprowadzić porównanie.
Na przykład można porównać tablicę bajtową:

* Według odniesienia, w taki sposób, aby różnica była wykrywana tylko wtedy, gdy jest używana nowa tablica bajtów
* Przez głębokie porównanie, takie, że mutacja bajtów w tablicy jest wykrywana

Domyślnie EF Core używa pierwszego z tych podejścia w przypadku tablic nieopartych na kluczowym bajcie.
Oznacza to, że porównywane są tylko odwołania, a zmiana jest wykrywana tylko wtedy, gdy istniejąca tablica bajtów jest zastępowana nową.
Jest to pragmatyczna decyzja, która pozwala uniknąć głębokiego porównania wielu tablic dużych bajtów podczas wykonywania metody SaveChanges.
Jednak typowy scenariusz zamiany, powiedzmy, obraz z innym obrazem jest obsługiwany w sposób efektywny.

Z drugiej strony równość odwołania nie będzie działała, gdy tablice bajtowe są używane do reprezentowania kluczy binarnych.
Jest bardzo mało prawdopodobne, że właściwość FK jest ustawiona na to _samo wystąpienie_ , co Właściwość PK, do której ma być porównywana.
W związku z tym EF Core używa głębokiego porównania dla tablic bajtowych działających jako klucze.
Jest to mało prawdopodobne, ponieważ klucze binarne są zwykle krótkie.

### <a name="snapshots"></a>Migawki

Głębokie porównania typów modyfikowalnych oznacza, że EF Core wymaga możliwości utworzenia głębokiej "migawki" wartości właściwości.
Skopiowanie odwołania zamiast tego spowodowało mutację zarówno bieżącej wartości, jak i migawki, ponieważ są one _tym samym obiektem_.
W związku z tym, gdy głębokie porównania są używane dla modyfikowalnych typów, wymagana jest również Szczegółowa snapshotting.

## <a name="properties-with-value-converters"></a>Właściwości z konwerterami wartości

W powyższym przypadku EF Core ma obsługę mapowania natywnego dla tablic bajtowych i dlatego mogą automatycznie wybierać odpowiednie wartości domyślne.
Jeśli jednak właściwość jest zamapowana za pomocą [konwertera wartości](xref:core/modeling/value-conversions), EF Core nie zawsze określać odpowiedniego porównania do użycia.
Zamiast tego, EF Core zawsze używa domyślnego porównania równości zdefiniowanego przez typ właściwości.
Jest to często poprawne, ale może być konieczne przesłanianie podczas mapowania bardziej złożonych typów.

### <a name="simple-immutable-classes"></a>Proste klasy niemodyfikowalne

Rozważmy właściwość, która używa konwertera wartości do mapowania prostej, niezmiennej klasy.

[!code-csharp[SimpleImmutableClass](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=SimpleImmutableClass)]

[!code-csharp[ConfigureImmutableClassProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=ConfigureImmutableClassProperty)]

Właściwości tego typu nie wymagają specjalnych porównań ani migawek, ponieważ:
* Równość została zastąpiona, tak aby różne wystąpienia były porównywane prawidłowo
* Typ jest niezmienny, dlatego nie ma możliwości mutacji wartości migawki

Dlatego w tym przypadku domyślne zachowanie EF Core jest zgodne z oczekiwaniami.

### <a name="simple-immutable-structs"></a>Proste niezmienne struktury

Mapowanie dla prostych struktur jest również proste i nie wymaga żadnych specjalnych porównań ani snapshotting.

[!code-csharp[SimpleImmutableStruct](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=SimpleImmutableStruct)]

[!code-csharp[ConfigureImmutableStructProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=ConfigureImmutableStructProperty)]

EF Core ma wbudowaną obsługę generowania skompilowanych, składowych porównania właściwości struktury.
Oznacza to, że struktury nie muszą mieć równej równości dla EF, ale nadal można to zrobić z [innych przyczyn](/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-define-value-equality-for-a-type).
Ponadto specjalna snapshotting nie jest wymagana, ponieważ struktury są niezmienne i zawsze składowych kopiowane.
(Dotyczy to również niemodyfikowalnych struktur, ale [ogólnie rzecz biorąc, należy unikać modyfikowalnych struktur](/dotnet/csharp/write-safe-efficient-code)).

### <a name="mutable-classes"></a>Klasy modyfikowalne

Zaleca się używanie typów niemodyfikowalnych (klas lub struktur) z konwerterami wartości, gdy jest to możliwe.
Jest to zwykle bardziej wydajne i ma semantykę oczyszczarki niż przy użyciu modyfikowalnego typu.

Jednak jest to typowy sposób użycia właściwości typów, których aplikacja nie może zmienić.
Na przykład mapowanie właściwości zawierającej listę liczb: 

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ListProperty)]

[ `List<T>` Klasa](/dotnet/api/system.collections.generic.list-1?view=netstandard-2.1):
* Ma równość odwołania; dwie listy zawierające te same wartości są traktowane jako różne.
* Jest modyfikowalny; wartości na liście można dodawać i usuwać.

Typowa konwersja wartości we właściwości listy może konwertować listę do i z formatu JSON:

[!code-csharp[ConfigureListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListProperty)]

Następnie wymaga ustawienia a `ValueComparer<T>` we właściwości, aby wymusić EF Core poprawne porównania z tą konwersją:

[!code-csharp[ConfigureListPropertyComparer](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListPropertyComparer)]

> [!NOTE]  
> Interfejs API konstruktora modeli ("Fluent") służący do ustawiania funkcji porównującej wartości nie został jeszcze zaimplementowany.
> Zamiast tego kod powyżej wywołuje SetValueComparer na niższym poziomie IMutableProperty uwidocznionym przez konstruktora jako "Metadata".

`ValueComparer<T>`Konstruktor akceptuje trzy wyrażenia:
* Wyrażenie do sprawdzania równości
* Wyrażenie służące do generowania kodu skrótu
* Wyrażenie służące do tworzenia migawek wartości  

W takim przypadku porównanie jest wykonywane przez sprawdzenie, czy sekwencje liczb są takie same.

Analogicznie, kod skrótu jest tworzony na podstawie tej samej sekwencji.
(Należy zauważyć, że jest to kod skrótu dla wartości modyfikowalnych, co może [spowodować problemy](https://ericlippert.com/2011/02/28/guidelines-and-rules-for-gethashcode/).
Zamiast tego możesz być niezmiennym.

Migawka jest tworzona przez sklonowanie listy z ToList —.
Jest to konieczne tylko wtedy, gdy listy zostaną poddawane mutacjom.
Zamiast tego możesz być niezmiennym. 

> [!NOTE]  
> Konwertery wartości i porównania są konstruowane przy użyciu wyrażeń, a nie prostych delegatów.
> Wynika to z faktu, że EF wstawia te wyrażenia do znacznie bardziej złożonego drzewa wyrażeń, które następnie jest kompilowane do delegata kształtu jednostki.
> Koncepcyjnie jest to podobne do funkcji deokładziny kompilatora.
> Na przykład prostą konwersją może być tylko skompilowane rzutowanie, a nie wywołanie innej metody w celu wykonania konwersji.    

### <a name="key-comparers"></a>Najważniejsze funkcje porównujące

Sekcja w tle opisuje, dlaczego porównania kluczy mogą wymagać specjalnej semantyki.
Upewnij się, że utworzono funkcję porównującą, która jest odpowiednia dla kluczy podczas jej ustawiania na Właściwość podstawowa, główna lub klucza obcego.

Używaj [SetKeyValueComparer](/dotnet/api/microsoft.entityframeworkcore.mutablepropertyextensions.setkeyvaluecomparer?view=efcore-3.1) w rzadkich przypadkach, gdy w tej samej właściwości jest wymagana inna semantyka.

> [!NOTE]  
> SetStructuralComparer jest przestarzała w EF Core 5,0.
> Zamiast tego użyj SetKeyValueComparer.

### <a name="overriding-defaults"></a>Zastępowanie wartości domyślnych

Czasami domyślne porównanie używane przez EF Core może być nieodpowiednie.
Na przykład mutacja tablic bajtowych nie jest domyślnie wykrywana w EF Core.
Tę wartość można zastąpić, ustawiając inną funkcję porównującą dla właściwości: 

[!code-csharp[OverrideComparer](../../../samples/core/Modeling/ValueConversions/OverridingByteArrayComparisons.cs?name=OverrideComparer)]

EF Core będzie teraz porównywać sekwencje bajtów i w związku z tym wykrywa mutacje tablicy bajtów.
