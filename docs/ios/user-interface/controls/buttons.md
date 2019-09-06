---
title: Boutons dans Xamarin. iOS
description: La classe UIButton est utilisée pour représenter différents styles de bouton dans les écrans iOS. Ce guide présente les différentes options d’utilisation des boutons dans iOS.
ms.prod: xamarin
ms.assetid: 304229E5-8FA8-41BD-8563-D19E1D2A0296
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/11/2018
ms.openlocfilehash: bbb423b01a477b0589903f96d0f4313c25733b91
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70284336"
---
# <a name="buttons-in-xamarinios"></a>Boutons dans Xamarin. iOS

Dans iOS, la `UIButton` classe représente un contrôle bouton.

Les propriétés d’un bouton peuvent être modifiées par programme ou à l’aide de la **panneau Propriétés** du concepteur iOS :

![Panneau Propriétés du concepteur iOS](buttons-images/properties.png "Panneau Propriétés du concepteur iOS")

## <a name="creating-a-button-programmatically"></a>Création d’un bouton par programmation

Un `UIButton` peut être créé avec quelques lignes de code seulement.

- Instanciez un bouton et spécifiez son type :

  ```csharp
  UIButton myButton = new UIButton(UIButtonType.System);
  ```

  Le type du bouton est spécifié par un `UIButtonType`:

  - `UIButtonType.System`-Bouton à usage général
  - `UIButtonType.DetailDisclosure`-Indique la disponibilité d’informations détaillées, généralement sur un élément spécifique dans une table
  - `UIButtonType.InfoDark`: Indique la disponibilité des informations de configuration ; couleur foncée
  - `UIButtonType.InfoLight`: Indique la disponibilité des informations de configuration ; couleur claire
  - `UIButtonType..AddContact`-Indique qu’un contact peut être ajouté
  - `UIButtonType.Custom`-Bouton personnalisable

  Pour plus d’informations sur les différents types de boutons, jetez un coup d’œil à :
  
  - La section [types de boutons personnalisés](#custom-button-types) de ce document
  - La recette des [types de bouton](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/create_different_types_of_buttons)
  - [Instructions relatives à l’interface humaine iOS](https://developer.apple.com/design/human-interface-guidelines/ios/controls/buttons/)d’Apple.

- Définir la taille et la position du bouton :

  ```csharp
  myButton.Frame = new CGRect(25, 25, 300, 150);
  ```

- Définissez le texte du bouton. Utilisez la `SetTitle` méthode, qui requiert le texte et une `UIControlState` valeur :

  ```csharp
  myButton.SetTitle("Hello, World!", UIControlState.Normal);
  ```

  Pour plus d’informations sur le style d’un bouton et la définition de son texte, reportez-vous à :

  - Le [style d’une](#styling-a-button) section de bouton de ce document
  - La recette du [texte du bouton définir](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/set_button_text) .

## <a name="handling-a-button-tap"></a>Gestion d’un appui sur un bouton

Pour répondre à un appui sur un bouton, fournissez un gestionnaire pour `TouchUpInside` l’événement du bouton :

```csharp
button.TouchUpInside += (sender, e) => {
    DoSomething();
};
```

> [!NOTE]
> `TouchUpInside`n’est pas le seul événement de bouton disponible. `UIButton`est une classe enfant de `UIControl`, qui définit de [nombreux événements différents](xref:UIKit.UIControlEvent).

### <a name="using-the-ios-designer-to-specify-button-event-handlers"></a>Utilisation du concepteur iOS pour spécifier des gestionnaires d’événements de bouton

Utilisez l’onglet **événements** de la **panneau Propriétés** pour spécifier des gestionnaires d’événements pour les divers événements d’un bouton.

Pour l’événement approprié, tapez le nom d’un nouveau gestionnaire d’événements ou sélectionnez-en un dans la liste. Cette opération crée un gestionnaire d’événements dans le code pour le contrôleur d’affichage du bouton.

![Onglet événements de l’panneau Propriétés](buttons-images/image1.png "Onglet événements de l’panneau Propriétés")

## <a name="styling-a-button"></a>Stylisation d’un bouton

`UIButton` les contrôles peuvent exister dans un nombre d’états différents, spécifié par un `UIControlState` valeur – `Normal`, `Disabled`, `Focused`, `Highlighted`, etc. Chaque État peut recevoir un style unique, spécifié par programme ou à l’aide du concepteur iOS.

> [!NOTE]
> Pour obtenir la liste complète de `UIControlState` toutes les valeurs, jetez un coup d’œil au[`UIKit.UIControlState enumeration`](xref:UIKit.UIControlState)
> correspondante.

Par exemple, pour définir la couleur du titre et la couleur `UIControlState.Normal`de l’ombre pour :

```csharp
button.SetTitleColor(UIColor.White, UIControlState.Normal);
button.SetTitleShadowColor(UIColor.Black, UIControlState.Normal);
```

Le code suivant définit le titre du bouton sur une chaîne avec attributs (stylisé) `UIControlState.Normal` pour `UIControlState.Highlighted`et :

```csharp
var normalAttributedTitle = new NSAttributedString(buttonTitle, foregroundColor: UIColor.Blue, strikethroughStyle: NSUnderlineStyle.Single);
myButton.SetAttributedTitle(normalAttributedTitle, UIControlState.Normal);

var highlightedAttributedTitle = new NSAttributedString(buttonTitle, foregroundColor: UIColor.Green, strikethroughStyle: NSUnderlineStyle.Thick);
myButton.SetAttributedTitle(highlightedAttributedTitle, UIControlState.Highlighted);
```

## <a name="custom-button-types"></a>Types de boutons personnalisés

Les boutons avec `UIButtonType` un `Custom` de n’ont pas de styles par défaut. Toutefois, il est possible de configurer l’apparence du bouton en définissant une image pour ses différents États :

```csharp
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand.png"), UIControlState.Normal);
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand_Highlight.png"), UIControlState.Highlighted);
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand_On.png"), UIControlState.Selected);
```

Selon que l’utilisateur touche ou non le bouton, il est restitué comme l’une des images suivantes (`UIControlState.Normal` `UIControlState.Highlighted` et `UIControlState.Selected` États, respectivement) :

![UIControlState.Normal](buttons-images/image22.png "UIControlState.Normal")
![UIControlState.Highlighted](buttons-images/image23.png "UIControlState.Highlighted")
![UIControlState.Selected](buttons-images/image24.png "UIControlState.Selected")

Pour plus d’informations sur l’utilisation de boutons personnalisés, reportez-vous à la recette [utiliser une image pour un bouton](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/use_an_image_for_a_button) .

