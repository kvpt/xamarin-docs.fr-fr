---
title: Utilisation de l’entrée de texte Watchos dans Xamarin
description: Ce document décrit l’entrée de texte Watchos dans Xamarin. Il aborde la méthode PresentTextInputController, le griffonnage, le texte brut, les Emoji et la dictée.
ms.prod: xamarin
ms.assetid: E9CDF1DE-4233-4C39-99A9-C0AA643D314D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: f523b6a028c8d9dcc0df772dc617c57bc947905d
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86936880"
---
# <a name="working-with-watchos-text-input-in-xamarin"></a>Utilisation de l’entrée de texte Watchos dans Xamarin

Le Apple Watch ne fournit pas de clavier permettant aux utilisateurs d’entrer du texte. Toutefois, il prend en charge certaines alternatives conviviales :

- Sélection à partir d’une liste prédéfinie d’options de texte,
- Siri dictée,
- Choix d’un emoji,
- La reconnaissance de l’écriture manuscrite lettre par lettre (présentée dans Watchos 3).

Le simulateur ne prend pas actuellement en charge la dictée, mais vous pouvez toujours tester les autres options du contrôleur d’entrée de texte, telles que Scribble, comme illustré ici :

![Test de l’option Scribble](text-input-images/textinput-sml.png)

Pour accepter la saisie de texte dans une application Watch :

1. Créez un tableau de chaînes d’options prédéfinies.
2. Appelez `PresentTextInputController` avec le tableau, s’il faut autoriser l’Emoji ou non, et un `Action` appelé lorsque l’utilisateur a terminé.
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

L' `WKTextInputMode` énumération a trois valeurs :

- Traditionnelle
- AllowEmoji
- AllowAnimatedEmoji

## <a name="plain"></a>Traditionnelle

Lorsque le mode normal est défini, l’utilisateur peut choisir :

- Dictée
- Scribble ou
- à partir d’une liste prédéfinie que l’application fournit.

[![Dictée, griffonnage ou à partir d’une liste prédéfinie que l’application fournit](text-input-images/plain-scribble-sml.png)](text-input-images/plain-scribble.png#lightbox)

Le résultat est toujours retourné comme un `NSObject` qui peut être casté en `string` .

## <a name="emoji"></a>Emoji

Il existe deux types d’Emoji :

- Emoji Unicode standard
- Images animées

Quand l’utilisateur choisit un Emoji Unicode, il est retourné sous forme de chaîne.

Si une image animée Emoji est sélectionnée `result` , le dans le gestionnaire d’achèvement contient un `NSData` objet qui contient l’Emoji `UIImage` .

## <a name="accepting-dictation-only"></a>Acceptation de la dictée uniquement

Pour amener l’utilisateur directement à l’écran de dictée sans montrer de suggestions (ou l’option Scribble) :

- passer un tableau vide pour la liste des suggestions, et
- définissez `WatchKit.WKTextInputMode.Plain` .

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

![Lorsque l’utilisateur parle, l’écran espion affiche le texte tel qu’il est compris](text-input-images/dictation.png)

Une fois que vous **avez** appuyé sur le bouton terminé, le texte est renvoyé.

## <a name="related-links"></a>Liens associés

- [Document texte et étiquettes d’Apple](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/TextandLabels.html)
- [Introduction à watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md)
