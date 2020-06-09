---
title: Chaîne NSString dans Xamarin. iOS et Xamarin. Mac
description: Ce document décrit comment Xamarin. iOS convertit en toute transparence les objets chaîne NSString en objets String C#, lorsque cela ne se produit pas.
ms.prod: xamarin
ms.assetid: 785744B3-42E2-4590-8F41-435325E609B9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 314c94fc9208a63e2f9305511df262327df921a5
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84565068"
---
# <a name="nsstring-in-xamarinios-and-xamarinmac"></a>Chaîne NSString dans Xamarin. iOS et Xamarin. Mac

La conception de Xamarin. iOS et Xamarin. Mac requiert l’utilisation de l’API pour exposer le type de chaîne .NET natif, `string` , pour la manipulation de chaînes en C# et d’autres langages de programmation .net, et pour exposer une chaîne comme type de données exposé par l’API au lieu du  `NSString`   type de données.

Cela signifie que les développeurs ne doivent pas nécessairement conserver les chaînes destinées à être appelées dans Xamarin. iOS & l’API Xamarin. Mac (Unified) dans un type spécial ( `Foundation.NSString` ), ils peuvent continuer à utiliser `System.String` des mono pour toutes les opérations, et chaque fois qu’une API dans Xamarin. iOS ou Xamarin. Mac requiert une chaîne, notre liaison d’API prend soin de marshaler les informations

Par exemple, la propriété objective-C « Text » sur un `UILabel` de type `NSString` est déclarée comme suit :

```objc
@property(nonatomic, copy) NSString *text
```

Cela est exposé dans Xamarin. iOS en tant que :

```csharp
class UILabel {
    public string Text { get; set; }
}
```

En arrière-plan, l’implémentation de cette propriété marshale la chaîne C# dans un `NSString` et appelle la `objc_msgSend` méthode de la même façon que objective-C.

Il existe un certain nombre d’API objective-C tierces qui ne consomment pas un `NSString` , mais consomment plutôt une chaîne C («*char*»). Dans ce cas, vous pouvez toujours utiliser le type de données chaîne C#, mais vous devez utiliser l’attribut [[PlainString]](~/cross-platform/macios/binding/objective-c-libraries.md) pour informer le générateur de liaison que cette chaîne ne doit pas être marshalée en tant que `NSString` , mais plutôt en tant que chaîne C.

 <a name="Exceptions_to_the_Rule"></a>

## <a name="exceptions-to-the-rule"></a>Exceptions à la règle

Dans Xamarin. iOS et Xamarin. Mac, nous avons fait une exception à cette règle. La décision entre le moment où nous exposait les  `string` et le moment où nous créons un, sauf et expose  `NSString` , est faite si la  `NSString`   méthode peut effectuer une comparaison de pointeur au lieu d’une comparaison de contenu.

Cela peut se produire lorsqu’une API objective-C utilise une  `NSString`   constante publique comme jeton qui représente une action, au lieu de comparer le contenu réel de la chaîne.

Dans ces cas, les `NSString`   API sont exposées et il y a une minorité d’API qui le possèdent. Vous remarquerez également que les propriétés chaîne NSString sont exposées dans certaines classes. Ces `NSString` propriétés sont exposées pour les éléments tels que les notifications. Ces propriétés se présentent généralement comme suit :

```csharp
class Foo {
     public NSString FooNotification { get; }
}
```

Les notifications sont des clés utilisées pour la `NSNotification` classe lorsque vous souhaitez vous inscrire à un événement particulier qui est diffusé par le Runtime.

Les clés se présentent généralement de la façon suivante :

```csharp
class Foo {
     public NSString FooBarKey { get; }
}
```

Un autre emplacement `NSString` dans lequel les s sont exposés dans l’API est utilisé comme des jetons utilisés comme paramètres pour certaines API dans iOS ou OS X qui prennent des `NSDictionary` objets en tant que paramètres. Le dictionnaire contient généralement des `NSString` clés. Par Convention, Xamarin. iOS nomme ces propriétés statiques `NSString` en ajoutant le nom « clé ».
