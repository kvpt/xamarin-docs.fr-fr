---
title: Chaîne NSString dans Xamarin. iOS et Xamarin. Mac
description: Ce document décrit comment Xamarin. iOS convertit en toute transparence les objets C# chaîne NSString en objets String, lorsque cela ne se produit pas.
ms.prod: xamarin
ms.assetid: 785744B3-42E2-4590-8F41-435325E609B9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: f744f4ed5619e4e7f4a9d85897c4451bf7e5b9bc
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022345"
---
# <a name="nsstring-in-xamarinios-and-xamarinmac"></a>Chaîne NSString dans Xamarin. iOS et Xamarin. Mac

La conception de Xamarin. iOS et Xamarin. Mac appelle l’API d’utilisation pour exposer le type de chaîne .NET natif, `string`, pour la manipulation de chaînes C# dans et d’autres langages de programmation .net, et pour exposer une chaîne comme type de données exposé par l’API au lieu de la `NSString` type de données.

Cela signifie que les développeurs ne doivent pas nécessairement conserver les chaînes destinées à être appelées dans Xamarin. iOS & l’API Xamarin. Mac (Unified) dans un type spécial (`Foundation.NSString`), ils peuvent continuer à utiliser les `System.String` mono pour toutes les opérations, et chaque fois qu’une API dans Xamarin. iOS ou Xamarin. Mac requiert une chaîne, notre liaison d’API prend soin de marshaler les informations.

Par exemple, la propriété objective-C « Text » sur un `UILabel` de type `NSString`, est déclarée comme suit :

```objc
@property(nonatomic, copy) NSString *text
```

Cela est exposé dans Xamarin. iOS en tant que :

```csharp
class UILabel {
    public string Text { get; set; }
}
```

En arrière-plan, l’implémentation de cette propriété marshale C# la chaîne dans une `NSString` et appelle la méthode `objc_msgSend` de la même façon que objective-C.

Il existe un certain nombre d’API objective-C tierces qui ne consomment pas d' `NSString`, mais utilisent plutôt une chaîne C («*char*»). Dans ce cas, vous pouvez toujours utiliser le C# type de données String, mais vous devez utiliser l’attribut [[PlainString]](~/cross-platform/macios/binding/objective-c-libraries.md) pour informer le générateur de liaison que cette chaîne ne doit pas être marshalée en tant que `NSString`, mais plutôt en tant que chaîne C.

 <a name="Exceptions_to_the_Rule" />

## <a name="exceptions-to-the-rule"></a>Exceptions à la règle

Dans Xamarin. iOS et Xamarin. Mac, nous avons fait une exception à cette règle. La décision entre le moment où nous exposons `string`s et le moment où nous faisons un exception et expose `NSString`s est faite si la méthode de `NSString` peut faire une comparaison de pointeur au lieu d’une comparaison de contenu.

Cela peut se produire lorsqu’une API objective-C utilise une constante `NSString`publique  en tant que jeton qui représente une action, au lieu de comparer le contenu réel de la chaîne.

Dans ce cas, `NSString`API  sont exposées, et il y a une minorité d’API qui en ont besoin. Vous remarquerez également que les propriétés chaîne NSString sont exposées dans certaines classes. Ces propriétés de `NSString` sont exposées pour les éléments tels que les notifications. Ces propriétés se présentent généralement comme suit :

```csharp
class Foo {
     public NSString FooNotification { get; }
}
```

Les notifications sont des clés utilisées pour la classe `NSNotification` lorsque vous souhaitez vous inscrire à un événement particulier qui est diffusé par le Runtime.

Les clés se présentent généralement de la façon suivante :

```csharp
class Foo {
     public NSString FooBarKey { get; }
}
```

Un autre emplacement où `NSString`sont exposés dans l’API est en tant que jetons utilisés comme paramètres pour certaines API dans iOS ou OS X qui acceptent `NSDictionary` objets comme paramètres. Le dictionnaire contient généralement des clés de `NSString`. Par Convention, Xamarin. iOS nomme ces propriétés de `NSString` statiques en ajoutant le nom de « clé ».
