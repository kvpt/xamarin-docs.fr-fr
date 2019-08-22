---
title: Chaîne NSString dans Xamarin. iOS et Xamarin. Mac
description: Ce document décrit comment Xamarin. iOS convertit en toute transparence les objets C# chaîne NSString en objets String, lorsque cela ne se produit pas.
ms.prod: xamarin
ms.assetid: 785744B3-42E2-4590-8F41-435325E609B9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 589b584e0526ffdc56dd5ec26aa25a0b61e2141a
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69889899"
---
# <a name="nsstring-in-xamarinios-and-xamarinmac"></a>Chaîne NSString dans Xamarin. iOS et Xamarin. Mac

La conception de Xamarin. iOS et Xamarin. Mac requiert l’utilisation de l’API pour exposer le type de chaîne .net natif, `string`, pour la manipulation de C# chaînes dans et d’autres langages de programmation .net et pour exposer une chaîne comme type de données exposé par l’API au lieu de type `NSString`de données.

Cela signifie que les développeurs ne doivent pas avoir à conserver les chaînes destinées à être appelées dans Xamarin. iOS & l’API Xamarin. Mac (Unified) dans un type`Foundation.NSString`spécial (), ils peuvent continuer à `System.String` utiliser des mono pour toutes les opérations, et quand une API dans Xamarin. iOS ou Xamarin. Mac requiert une chaîne, notre liaison d’API s’occupe du marshaling des informations.

Par exemple, la propriété objective-C «Text» sur `UILabel` un de `NSString`type est déclarée comme suit:

```objc
@property(nonatomic, copy) NSString *text
```

Cela est exposé dans Xamarin. iOS en tant que:

```csharp
class UILabel {
    public string Text { get; set; }
}
```

En arrière-plan, l’implémentation de cette propriété marshale C# la chaîne dans `NSString` un et appelle `objc_msgSend` la méthode de la même façon que objective-C.

Il existe un certain nombre d’API objective-c tierces qui ne consomment pas un `NSString`, mais consomment plutôt une chaîne C («*char*»). Dans ce cas, vous pouvez toujours utiliser le C# type de données String, mais vous devez utiliser l’attribut [[PlainString]](~/cross-platform/macios/binding/objective-c-libraries.md) pour informer le générateur de liaison que cette chaîne ne doit pas être marshalée en tant `NSString`que, mais plutôt en tant que chaîne C.

 <a name="Exceptions_to_the_Rule" />

## <a name="exceptions-to-the-rule"></a>Exceptions à la règle

Dans Xamarin. iOS et Xamarin. Mac, nous avons fait une exception à cette règle. La décision entre le moment où `string`nous exposait les et le moment où nous créons `NSString`un, sauf et expose `NSString`, est faite si la méthode peut effectuer une comparaison de pointeur au lieu d’une comparaison de contenu.

Cela peut se produire lorsqu’une API objective-C utilise `NSString`une constante publique comme jeton qui représente une action, au lieu de comparer le contenu réel de la chaîne.

Dans ces cas, `NSString`  les API sont exposées et il y a une minorité d’API qui le possèdent. Vous remarquerez également que les propriétés chaîne NSString sont exposées dans certaines classes. Ces `NSString` propriétés sont exposées pour les éléments tels que les notifications. Ces propriétés se présentent généralement comme suit:

```csharp
class Foo {
     public NSString FooNotification { get; }
}
```

Les notifications sont des clés utilisées pour `NSNotification` la classe lorsque vous souhaitez vous inscrire à un événement particulier qui est diffusé par le Runtime.

Les clés se présentent généralement de la façon suivante:

```csharp
class Foo {
     public NSString FooBarKey { get; }
}
```

Un autre emplacement `NSString`dans lequel les s sont exposés dans l’API est utilisé comme des jetons utilisés comme paramètres pour certaines API dans iOS ou OS X `NSDictionary` qui prennent des objets en tant que paramètres. Le dictionnaire contient `NSString` généralement des clés. Par Convention, Xamarin. iOS nomme ces propriétés statiques `NSString` en ajoutant le nom «clé».
