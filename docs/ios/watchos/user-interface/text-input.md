---
title: Utilisation de l’entrée de texte Watchos dans Xamarin
description: Ce document décrit l’entrée de texte Watchos dans Xamarin. Il aborde la méthode PresentTextInputController, le griffonnage, le texte brut, les Emoji et la dictée.
ms.prod: xamarin
ms.assetid: E9CDF1DE-4233-4C39-99A9-C0AA643D314D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 156a31e37d14ce3e3cbe7173ae97b608e9d4c32e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032648"
---
# <a name="working-with-watchos-text-input-in-xamarin"></a>Utilisation de l’entrée de texte Watchos dans Xamarin

Le Apple Watch ne fournit pas de clavier permettant aux utilisateurs d’entrer du texte. Toutefois, il prend en charge certaines alternatives conviviales :

- Sélection à partir d’une liste prédéfinie d’options de texte,
- Siri dictée,
- Choix d’un emoji,
- La reconnaissance de l’écriture manuscrite lettre par lettre (présentée dans Watchos 3).

Le simulateur ne prend pas actuellement en charge la dictée, mais vous pouvez toujours tester les autres options du contrôleur d’entrée de texte, telles que Scribble, comme illustré ici :

![](text-input-images/textinput-sml.png "Testing the scribble option")

Pour accepter la saisie de texte dans une application Watch :

1. Créez un tableau de chaînes d’options prédéfinies.
2. Appelez `PresentTextInputController` avec le tableau, s’il faut autoriser l’Emoji ou non, et un `Action` qui est appelé lorsque l’utilisateur a terminé.
3. Dans l’action d’achèvement, testez le résultat d’entrée et prenez les mesures appropriées dans l’application (en définissant éventuellement la valeur de texte d’une étiquette).

L’extrait de code suivant présente trois options prédéfinies pour l’utilisateur :

```csharp
var suggest = new string[] {"Get groceries", "Buy gas", "Post letter"};

PresentTextInputController (suggest, WatchKit.WKTextInputMode.AllowEmoji, (result) => {
    // action when the "text input" is complete
    if (result != null && result.Count > 0) {
    // this only works if result is a text response (Plain or AllowEmoji)
        enteredText = result.GetItem<NSObject>(0).ToString();
        Console.WriteLine (enteredText);
        // do something, such as myLabel.SetText(enteredText);
    }
});
```

L’énumération `WKTextInputMode` a trois valeurs :

- Traditionnelle
- AllowEmoji
- AllowAnimatedEmoji

## <a name="plain"></a>Traditionnelle

Lorsque le mode normal est défini, l’utilisateur peut choisir :

- Dictée
- Scribble ou
- à partir d’une liste prédéfinie que l’application fournit.

[![](text-input-images/plain-scribble-sml.png "Dictation, Scribble, or from a pre-defined list that the app supplies")](text-input-images/plain-scribble.png#lightbox)

Le résultat est toujours retourné en tant que `NSObject` qui peut être casté en `string`.

## <a name="emoji"></a>Emoji

Il existe deux types d’Emoji :

- Emoji Unicode standard
- Images animées

Quand l’utilisateur choisit un Emoji Unicode, il est retourné sous forme de chaîne.

Si un Emoji d’image animée est sélectionné, le `result` dans le gestionnaire d’achèvement contient un objet `NSData` contenant l’Emoji `UIImage`.

## <a name="accepting-dictation-only"></a>Acceptation de la dictée uniquement

Pour amener l’utilisateur directement à l’écran de dictée sans montrer de suggestions (ou l’option Scribble) :

- passer un tableau vide pour la liste des suggestions, et
- Définissez `WatchKit.WKTextInputMode.Plain`.

```csharp
PresentTextInputController (new string[0], WatchKit.WKTextInputMode.Plain, (result) => {
    // action when the "text input" is complete
    if (result != null && result.Count > 0) {
        dictatedText = result.GetItem<NSObject>(0).ToString();
        Console.WriteLine (dictatedText);
        // do something, such as myLabel.SetText(dictatedText);
    }
});
```

Lorsque l’utilisateur parle, l’écran de surveillance affiche l’écran suivant, qui comprend le texte tel qu’il est compris (par exemple, « il s’agit d’un test ») :

![](text-input-images/dictation.png "When the user is speaking, the watch screen displays the text as it is understood")

Une fois que vous **avez** appuyé sur le bouton terminé, le texte est renvoyé.

## <a name="related-links"></a>Liens associés

- [Document texte et étiquettes d’Apple](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/TextandLabels.html)
- [Introduction à watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md)
