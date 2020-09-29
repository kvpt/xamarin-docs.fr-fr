---
title: Support objective-C
description: Ce document fournit une description de la prise en charge de Objective-C dans l’incorporation .NET. Il aborde le décompte de références automatiques, chaîne NSString, les protocoles, le protocole NSObject, les exceptions et bien plus encore.
ms.prod: xamarin
ms.assetid: 3367A4A4-EC88-4B75-96D0-51B1FCBCE614
author: davidortinau
ms.author: daortin
ms.date: 11/14/2017
ms.openlocfilehash: c172208b77728423617d17b3f4c5b5a516a0b932
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457352"
---
# <a name="objective-c-support"></a>Support objective-C

## <a name="specific-features"></a>Fonctionnalités spécifiques

La génération d’Objective-C a quelques fonctionnalités spéciales à noter.

### <a name="automatic-reference-counting"></a>Décompte de références automatiques

L’utilisation du comptage de références automatique (ARC) est **nécessaire** pour appeler les liaisons générées. Le projet utilisant une bibliothèque basée sur l’incorporation .NET doit être compilé avec `-fobjc-arc` .

### <a name="nsstring-support"></a>Support chaîne NSString

Les API qui exposent `System.String` les types sont converties en `NSString` . Cela facilite la gestion de la mémoire par rapport à `char*` .

### <a name="protocols-support"></a>Prise en charge des protocoles

Les interfaces managées sont converties en protocoles objective-C où tous les membres sont `@required` .

### <a name="nsobject-protocol-support"></a>Prise en charge du protocole NSObject

Par défaut, le hachage et l’égalité par défaut de .NET et du runtime objective-C sont supposés être interchangeables, car ils partagent une sémantique similaire.

Lorsqu’un type managé substitue `Equals(Object)` ou `GetHashCode` , cela signifie généralement que le comportement par défaut (.net) n’était pas suffisant ; cela implique que le comportement objective-C par défaut n’est probablement pas suffisant.

Dans ce cas, le générateur remplace la [`isEqual:`](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418795-isequal?language=objc) méthode et la [`hash`](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418859-hash?language=objc) propriété définies dans le [ `NSObject` protocole](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc). Cela permet d’utiliser l’implémentation managée personnalisée de manière transparente à partir du code Objective-C.

### <a name="exceptions-support"></a>Prise en charge des exceptions

Passer `--nativeexception` en tant qu’argument de `objcgen` convertira les exceptions managées en exceptions objective-C qui peuvent être interceptées et traitées. 

### <a name="comparison"></a>Comparaison

Les types managés qui implémentent `IComparable` (ou sa version générique `IComparable<T>` ) produisent des méthodes conviviales objective-C qui retournent un `NSComparisonResult` et acceptent un `nil` argument. Cela rend l’API générée plus conviviale pour les développeurs objective-C. Par exemple :

```objc
- (NSComparisonResult)compare:(XAMComparableType * _Nullable)other;
```

### <a name="categories"></a>Catégories

Les méthodes d’extensions managées sont converties en catégories. Par exemple, les méthodes d’extension suivantes sur `Collection` :

```csharp
public static class SomeExtensions {
    public static int CountNonNull (this Collection collection) { ... }
    public static int CountNull (this Collection collection) { ... }
}
```

créerait une catégorie objective-C similaire à celle-ci :

```objc
@interface Collection (SomeExtensions)

- (int)countNonNull;
- (int)countNull;

@end
```

Lorsqu’un type managé unique étend plusieurs types, plusieurs catégories objective-C sont générées.

### <a name="subscripting"></a>Indices

Les propriétés indexées managées sont converties en scripts d’objets. Par exemple :

```csharp
public bool this[int index] {
    get { return c[index]; }
    set { c[index] = value; }
}
```

crée objective-C similaire à ce qui suit :

```objc
- (id)objectAtIndexedSubscript:(int)idx;
- (void)setObject:(id)obj atIndexedSubscript:(int)idx;
```

qui peut être utilisé par le biais de la syntaxe de script objective-C :

```objc
if ([intCollection [0] isEqual:@42])
    intCollection[0] = @13;
```

Selon le type de votre indexeur, l’indexation ou la génération de scripts indexés sera générée le cas échéant.

Cet [article](https://nshipster.com/object-subscripting/) est une introduction intéressante à la mise en indice.

## <a name="main-differences-with-net"></a>Principales différences avec .NET

### <a name="constructors-vs-initializers"></a>Constructeurs vs initialiseurs

En Objective-C, vous pouvez appeler n’importe quel prototype d’initialiseur de l’une des classes parentes dans la chaîne d’héritage, sauf s’il est marqué comme non disponible ( `NS_UNAVAILABLE` ).

En C#, vous devez déclarer explicitement un membre de constructeur à l’intérieur d’une classe, ce qui signifie que les constructeurs ne sont pas hérités.

Pour exposer la représentation appropriée de l’API C# à Objective-C, `NS_UNAVAILABLE` est ajouté à tout initialiseur qui n’est pas présent dans la classe enfant de la classe parente.

API C# :

```csharp
public class Unique {
    public Unique () : this (1)
    {
    }

    public Unique (int id)
    {
    }
}

public class SuperUnique : Unique {
    public SuperUnique () : base (911)
    {
    }
}
```

API en surface objective-C :

```objc
@interface SuperUnique : Unique

- (instancetype)initWithId:(int)id NS_UNAVAILABLE;
- (instancetype)init;

@end
```

Ici, `initWithId:` a été marqué comme non disponible.

### <a name="operator"></a>Opérateur

Objective-C ne prend pas en charge la surcharge d’opérateur en C#. par conséquent, les opérateurs sont convertis en sélecteurs de classe :

```csharp
public static AllOperators operator + (AllOperators c1, AllOperators c2)
{
    return new AllOperators (c1.Value + c2.Value);
}
```

par

```objc
+ (instancetype)add:(Overloads_AllOperators *)anObjectC1 c2:(Overloads_AllOperators *)anObjectC2;
```

Toutefois, certains langages .NET ne prennent pas en charge la surcharge d’opérateur. il est donc courant d’inclure également une méthode nommée [« conviviale »](/dotnet/standard/design-guidelines/operator-overloads) en plus de la surcharge d’opérateur.

Si la version de l’opérateur et la version « conviviale » sont trouvées, seule la version conviviale sera générée, car elles seront générées avec le même nom objective-C.

```csharp
public static AllOperatorsWithFriendly operator + (AllOperatorsWithFriendly c1, AllOperatorsWithFriendly c2)
{
    return new AllOperatorsWithFriendly (c1.Value + c2.Value);
}

public static AllOperatorsWithFriendly Add (AllOperatorsWithFriendly c1, AllOperatorsWithFriendly c2)
{
    return new AllOperatorsWithFriendly (c1.Value + c2.Value);
}
```

devient :

```objc
+ (instancetype)add:(Overloads_AllOperatorsWithFriendly *)anObjectC1 c2:(Overloads_AllOperatorsWithFriendly *)anObjectC2;
```

### <a name="equality-operator"></a>Opérateur d’égalité

En général, l’opérateur en `==` C# est géré en tant qu’opérateur général, comme indiqué ci-dessus.

Toutefois, si l’opérateur « convivial » est trouvé, l’opérateur et l’opérateur `==` `!=` seront ignorés dans la génération.

### <a name="datetime-vs-nsdate"></a>DateTime vs NSDate

À partir de la [`NSDate`](https://developer.apple.com/reference/foundation/nsdate?language=objc) documentation :

> `NSDate` les objets encapsulent un point unique dans le temps, indépendamment d’un système calendrical ou d’un fuseau horaire particulier. Les objets date sont immuables, ce qui représente un intervalle de temps invariant par rapport à une date de référence absolue (00:00:00 UTC le 1er janvier 2001).

En raison de la `NSDate` Date de référence, toutes les conversions entre les deux et `DateTime` doivent être effectuées en UTC.

#### <a name="datetime-to-nsdate"></a>DateTime pour NSDate

Lors de la conversion de `DateTime` en `NSDate` , la `Kind` propriété sur `DateTime` est prise en compte :

|Kind|Résultats|
|---|---|
|`Utc`|La conversion est effectuée à l’aide de l' `DateTime` objet fourni tel quel.|
|`Local`|Le résultat de l’appel de `ToUniversalTime()` dans l' `DateTime` objet fourni est utilisé pour la conversion.|
|`Unspecified`|L' `DateTime` objet fourni est supposé être UTC, ce qui est le même comportement lorsque `Kind` est `Utc` .|

La conversion utilise la formule suivante :

```
TimeInterval = DateTimeObjectTicks - NSDateReferenceDateTicks / TicksPerSecond
```

Dans cette formule : 

- `NSDateReferenceDateTicks` est calculé en fonction de la `NSDate` Date de référence de 00:00:00 UTC le 1er janvier 2001 : 

    ```csharp
    new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;
    ```

- [`TicksPerSecond`](/dotnet/api/system.timespan.tickspersecond) est défini sur [`TimeSpan`](/dotnet/api/system.timespan)

Pour créer l' `NSDate` objet, `TimeInterval` est utilisé avec le sélecteur `NSDate` [dateWithTimeIntervalSinceReferenceDate :](https://developer.apple.com/reference/foundation/nsdate/1591577-datewithtimeintervalsincereferen?language=objc) .

#### <a name="nsdate-to-datetime"></a>NSDate à DateTime

La conversion de `NSDate` en `DateTime` utilise la formule suivante :

```
DateTimeTicks = NSDateTimeIntervalSinceReferenceDate * TicksPerSecond + NSDateReferenceDateTicks
```

Dans cette formule : 

- `NSDateReferenceDateTicks` est calculé en fonction de la `NSDate` Date de référence de 00:00:00 UTC le 1er janvier 2001 : 

    ```csharp
    new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;
    ```

- [`TicksPerSecond`](/dotnet/api/system.timespan.tickspersecond) est défini sur [`TimeSpan`](/dotnet/api/system.timespan)

Après `DateTimeTicks` le calcul, le `DateTime` [constructeur](/dotnet/api/system.datetime.-ctor#System_DateTime__ctor_System_Int64_System_DateTimeKind_) est appelé, en affectant à la valeur `kind` `DateTimeKind.Utc` .

> [!NOTE]
> `NSDate` peut être `nil` , mais un `DateTime` est un struct dans .net, qui, par définition, ne peut pas être `null` . Si vous attribuez un `nil` `NSDate` , il sera traduit par la valeur par défaut `DateTime` , qui est mappée à `DateTime.MinValue` .

`NSDate` prend en charge une valeur maximale supérieure et une valeur minimale inférieure à `DateTime` . Lors de la conversion de `NSDate` en `DateTime` , ces valeurs supérieures et inférieures sont modifiées `DateTime` respectivement en [MaxValue](/dotnet/api/system.datetime.maxvalue) ou [MinValue](/dotnet/api/system.datetime.minvalue).