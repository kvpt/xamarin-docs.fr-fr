---
title: NSString dans Xamarin.iOS et Xamarin.Mac
description: Ce document décrit comment Xamarin.iOS convertit de façon transparente les objets NSString en objets à cordes C, lorsque cela ne se produit pas.
ms.prod: xamarin
ms.assetid: 785744B3-42E2-4590-8F41-435325E609B9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: f744f4ed5619e4e7f4a9d85897c4451bf7e5b9bc
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73022345"
---
# <a name="nsstring-in-xamarinios-and-xamarinmac"></a>NSString dans Xamarin.iOS et Xamarin.Mac

La conception de Xamarin.iOS et Xamarin.Mac appelle à l’utilisation API `string`pour exposer du type de chaîne natif .NET, , pour la manipulation des cordes `NSString` dans C et d’autres langages de programmation .NET, et d’exposer la chaîne comme le type de données exposés par l’API au lieu du type de données.

Cela signifie que les développeurs ne devraient pas avoir à garder les chaînes qui sont destinés à être utilisés pour appeler dans Xamarin.iOS & Xamarin.Mac API (Unified) dans un type spécial (),`Foundation.NSString`ils peuvent continuer à utiliser Mono `System.String` pour toutes les opérations, et chaque fois qu’un API dans Xamarin.iOS ou Xamarin.Mac nécessite une chaîne, notre liaison API prend soin de marshaling les informations.

Par exemple, la propriété Objective-C `UILabel` "texte" sur un type `NSString`, est déclarée comme ceci:

```objc
@property(nonatomic, copy) NSString *text
```

Ceci est exposé dans Xamarin.iOS comme:

```csharp
class UILabel {
    public string Text { get; set; }
}
```

Dans les coulisses, la mise en œuvre `NSString` de cette `objc_msgSend` propriété rassemble la chaîne C et appelle la méthode de la même manière qu’Objective-C.

Il ya une poignée de tiers Objectif-C API `NSString`qui ne consomment pas un , mais au lieu de consommer une chaîne C (un "*char*"). Dans ces cas, vous pouvez toujours utiliser le type de données de chaîne C, mais vous devez utiliser [l’attribut [PlainString]](~/cross-platform/macios/binding/objective-c-libraries.md) pour informer le générateur de liaison que cette chaîne ne doit pas être marshaled comme un `NSString`, mais plutôt comme une chaîne C.

 <a name="Exceptions_to_the_Rule" />

## <a name="exceptions-to-the-rule"></a>Exceptions à la Règle

Dans Xamarin.iOS et Xamarin.Mac, nous avons fait une exception à cette règle. La décision entre `string`quand nous exposons s, `NSString`et quand nous `NSString` faisons un excepté et exposer s, est prise si la méthode pourrait faire une comparaison de pointeur au lieu d’une comparaison de contenu.

Cela pourrait se produire lorsqu’une API Objective-C utilise une constante publique `NSString` comme un jeton qui représente une certaine action, au lieu de comparer le contenu réel de la chaîne.

Dans ces `NSString`  cas, les API sont exposées, et il y a une minorité d’API qui ont ceci. Vous remarquerez également que les propriétés NSString sont exposées dans certaines classes. Ces `NSString` propriétés sont exposées pour des éléments comme les notifications. Ce sont des propriétés ressemblent généralement à ceci:

```csharp
class Foo {
     public NSString FooNotification { get; }
}
```

Les notifications sont des `NSNotification` clés qui sont utilisées pour la classe lorsque vous souhaitez vous inscrire à un événement particulier diffusé par l’heure d’exécution.

Les clés ressemblent généralement à ceci :

```csharp
class Foo {
     public NSString FooBarKey { get; }
}
```

Un autre `NSString`endroit où s sont exposés dans l’API est comme des jetons qui sont `NSDictionary` utilisés comme paramètres à certaines API dans iOS ou OS X qui prennent des objets comme paramètres. Le dictionnaire `NSString` contient généralement des clés. Xamarin.iOS, par convention, `NSString` nomme ces propriétés statiques en ajoutant le nom "Key".
