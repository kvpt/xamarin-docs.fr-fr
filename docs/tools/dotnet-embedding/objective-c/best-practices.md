---
title: Méthodes recommandées pour l’incorporation .NET pour objective-C
description: Ce document décrit les différentes pratiques recommandées pour l’utilisation de l’incorporation .NET avec Objective-C. Il aborde l’exposition d’un sous-ensemble du code managé, l’exposition d’une API chunkier, l’attribution d’un nom et bien plus encore.
ms.prod: xamarin
ms.assetid: 63C7F5D2-8933-4D4A-8348-E9CBDA45C472
author: davidortinau
ms.author: daortin
ms.date: 11/14/2017
ms.openlocfilehash: 2f632e3218d817aa0162a63ea81c61ca18c52b93
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73006770"
---
# <a name="net-embedding-best-practices-for-objective-c"></a>Méthodes recommandées pour l’incorporation .NET pour objective-C

Il s’agit d’un brouillon qui peut ne pas être synchronisé avec les fonctionnalités actuellement prises en charge par l’outil. Nous espérons que ce document va évoluer séparément et qu’il correspond finalement à l’outil final. par conséquent, nous vous suggérons les meilleures approches à long terme : pas de solutions immédiates.

Une grande partie de ce document s’applique également à d’autres langues prises en charge. Toutefois, tous les exemples fournis C# sont dans et objective-C.

## <a name="exposing-a-subset-of-the-managed-code"></a>Exposition d’un sous-ensemble du code managé

La bibliothèque/infrastructure native générée contient du code Objective-C pour appeler chacune des API managées exposées. Plus le nombre d’API que vous surfacez (rendre public) est élevé, plus la bibliothèque de _colles_ Native deviendra.

Il peut être judicieux de créer un assembly différent, plus petit, pour exposer uniquement les API requises au développeur natif. Cette façade vous permettra également de mieux contrôler la visibilité, l’affectation de noms, la vérification des erreurs... du code généré.

## <a name="exposing-a-chunkier-api"></a>Exposition d’une API chunkier

Il y a un prix à payer pour passer du mode natif au mode managé (et inversement). Par conséquent, il est préférable d’exposer des interfaces groupées _au lieu d’API bavardes_ pour les développeurs natifs, par exemple

**Bavardes**

```csharp
public class Person {
  public string FirstName { get; set; }
  public string LastName { get; set; }
}
```

```objc
// this requires 3 calls / transitions to initialize the instance
Person *p = [[Person alloc] init];
p.firstName = @"Sebastien";
p.lastName = @"Pouliot";
```

**Regroupée**

```csharp
public class Person {
  public Person (string firstName, string lastName) {}
}
```

```objc
// a single call / transition will perform better
Person *p = [[Person alloc] initWithFirstName:@"Sebastien" lastName:@"Pouliot"];
```

Étant donné que le nombre de transitions est plus faible, les performances seront meilleures. Il nécessite également moins de code à générer, ce qui produit une bibliothèque Native plus petite.

## <a name="naming"></a>Attribution des noms

L’attribution d’un nom à des choses est l’un des deux problèmes les plus difficiles en informatique, les autres étant des erreurs d’invalidation et de désactivation en fonction du 1. Espérons que l’incorporation .NET peut vous protéger de tout sauf en nommant.

### <a name="types"></a>Types

Objective-C ne prend pas en charge les espaces de noms. En général, ses types sont préfixés avec un préfixe de caractère 2 (pour Apple) ou 3 (pour les tiers), comme `UIView` pour la vue de UIKit, qui désigne l’infrastructure.

Pour les types .NET, l’omission de l’espace de noms n’est pas possible, car elle peut introduire des noms en double ou confus. Cela rend les types .NET existants très longs, par exemple

```csharp
namespace Xamarin.Xml.Configuration {
  public class Reader {}
}
```

est utilisé comme :

```objc
id reader = [[Xamarin_Xml_Configuration_Reader alloc] init];
```

Toutefois, vous pouvez à nouveau exposer le type comme suit :

```csharp
public class XAMXmlConfigReader : Xamarin.Xml.Configuration.Reader {}
```

rendre l’utilisation plus conviviale de Objective-C, par exemple :

```objc
id reader = [[XAMXmlConfigReader alloc] init];
```

### <a name="methods"></a>Méthodes

Même les bons noms .NET peuvent ne pas être idéaux pour une API objective-C.

Les conventions d’affectation de noms en Objective-C sont différentes de celles de .NET (casse mixte au lieu de la casse Pascal, plus détaillée).
Veuillez lire les [instructions de codage pour le cacao](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingMethods.html#//apple_ref/doc/uid/20001282-BCIGIJJF).

Du point de vue du développeur objective-C, une méthode avec un préfixe `Get` implique que vous n’êtes pas propriétaire de l’instance, c’est-à-dire la [règle d’extraction](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-SW1).

Cette règle de nommage n’a pas de correspondance dans le monde de la GLOBALisation .NET. une méthode .NET avec un préfixe `Create` se comportera de la même manière dans .NET. Toutefois, pour les développeurs objective-C, cela signifie généralement que vous êtes propriétaire de l’instance retournée, c.-à-d. la [règle Create](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029).

## <a name="exceptions"></a>Exceptions

Dans .NET, il est assez courant d’utiliser largement les exceptions pour signaler les erreurs. Toutefois, ils sont lents et ne sont pas tout à fait identiques en Objective-C. Dans la mesure du possible, vous devez les masquer dans le développeur objective-C.

Par exemple, le modèle de `Try` .NET sera beaucoup plus facile à consommer à partir du code Objective-C :

```csharp
public int Parse (string number)
{
  return Int32.Parse (number);
}
```

alternative

```csharp
public bool TryParse (string number, out int value)
{
  return Int32.TryParse (number, out value);
}
```

### <a name="exceptions-inside-init"></a>Exceptions dans `init*`

Dans .NET, un constructeur doit être correctement exécuté et retourner une instance valide (avec le plus de_chance_) ou lever une exception.

En revanche, Objective-C permet à `init*` de retourner des `nil` lorsqu’une instance ne peut pas être créée. Il s’agit d’un modèle commun, mais pas général, utilisé dans de nombreuses infrastructures d’Apple. Dans d’autres cas, une `assert` peut se produire (et tuer le processus en cours).

Le générateur suit le même `return nil` modèle pour les méthodes de `init*` générées. Si une exception managée est levée, elle est imprimée (à l’aide de `NSLog`) et `nil` est retournée à l’appelant.

## <a name="operators"></a>Opérateurs

Objective-C n’autorise pas les opérateurs à être surchargés, de sorte qu' C# ils sont convertis en sélecteurs de classe.

Les méthodes nommées [« conviviales »](https://docs.microsoft.com/dotnet/standard/design-guidelines/operator-overloads) sont générées de préférence aux surcharges d’opérateur lorsqu’elles sont trouvées, et peuvent produire une API plus facile à consommer.

Les classes qui remplacent les opérateurs `==` et/ou `!=` doivent également remplacer la méthode Equals (Object) standard.
