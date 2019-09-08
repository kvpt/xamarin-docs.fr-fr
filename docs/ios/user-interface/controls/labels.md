---
title: Étiquettes dans Xamarin. iOS
description: Ce document explique comment utiliser des étiquettes dans Xamarin. iOS. Il décrit comment créer des étiquettes par programmation et avec le concepteur iOS.
ms.prod: xamarin
ms.assetid: 54DA1221-13E4-4D45-B263-5F22A0AC7B53
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/11/2017
ms.openlocfilehash: 275acd02e6cece0842344d8e5833c23af1014a0f
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768945"
---
# <a name="labels-in-xamarinios"></a>Étiquettes dans Xamarin. iOS

Le `UILabel` contrôle est utilisé pour afficher du texte en lecture seule et multiligne.

## <a name="implementing-a-label"></a>Implémentation d’une étiquette

Une nouvelle étiquette est créée en instanciant [`UILabel`](xref:UIKit.UILabel)un :

```csharp
UILabel label = new UILabel();
```

### <a name="labels-and-storyboards"></a>Étiquettes et storyboards

Vous pouvez également ajouter une étiquette à votre interface utilisateur lors de l’utilisation du concepteur iOS. Recherchez **étiquette** dans la **boîte à outils** et faites-la glisser vers votre vue :

![Étiquette dans la boîte à outils](labels-images/image3.png)

Les propriétés suivantes peuvent être ajustées dans le panneau Propriétés :

![Panneau des propriétés de l’étiquette](labels-images/image2.png)

- **Contexte de texte** : brut ou attribué. Le texte brut vous permet de définir les [attributs de mise en forme](#Formatting_Text_and_Label) de la chaîne entière. Les textes avec attributs vous permettent de définir une mise en forme avec des caractères ou des mots différents dans la chaîne.
- **Couleur, police, alignement** -attributs de mise en forme qui peuvent être appliqués à l’étiquette.
- **Lignes** : définit le nombre de lignes que l’étiquette peut couvrir. Affectez-lui la valeur 0 pour permettre à l’étiquette d’utiliser autant de lignes que nécessaire.
- **Comportement** : peut être défini sur activé ou mis en surbrillance. Activé est défini par défaut, le texte désactivé s’affiche dans une couleur plus claire. La mise en surbrillance est désactivée par défaut et permet de redessiner l’étiquette avec un État mis en surbrillance lorsqu’elle est sélectionnée par un utilisateur.
- **Baselane et saut de ligne** :
  - Limites détermine la façon dont le texte est positionné si les tailles de police sont différentes de celles spécifiées.
  - Les sauts de ligne déterminent la façon dont une chaîne est encapsulée ou tronquée si elle est plus longue qu’une seule ligne.
- **AutoShrink** : détermine la façon dont la taille de la police est réduite au sein d’une étiquette, si nécessaire.
- **Surligné, ombre, décalage** : vous permet de définir la maillage et la couleur de l’ombre, ainsi que le décalage de l’ombre.

## <a name="truncating-and-wrapping"></a>Troncation et encapsulation

Pour plus d’informations sur l’utilisation des sauts de ligne dans iOS, reportez-vous à la recette de [troncation et de retour automatique](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/labels/uilabel-truncate-wrap-text) à la ligne.

<a name="Formatting_Text_and_Label"/>

## <a name="formatting-text-and-label"></a>Mise en forme du texte et de l’étiquette

Pour mettre en forme la chaîne que vous utilisez dans une étiquette, vous pouvez soit définir des attributs de mise en forme sur la chaîne entière, soit utiliser des chaînes avec attributs. Les exemples suivants montrent comment les implémenter :

```csharp
label = new UILabel(){
                Text = "Hello, this is a string",
                Font = UIFont.FromName("Papyrus", 20f),
                TextColor = UIColor.Magenta,
                TextAlignment = UITextAlignment.Center
            };
```

```csharp
label.AttributedText = new NSAttributedString(
                "This is some formatted text",
                font: UIFont.FromName("GillSans", 16.0f),
                foregroundColor: UIColor.Blue,
                backgroundColor: UIColor.White
            );
```

Pour plus d’informations sur le style du `NSAttributedString` texte à l’aide de, consultez la recette du [style de texte](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/text_field/style_text) .

Par défaut, les étiquettes `Enabled` ont la valeur true, mais il est possible de lui affecter la valeur Disabled pour indiquer à l’utilisateur qu’un contrôle spécifique est désactivé :

```csharp
label.Enabled = false;
```

Cela affecte à l’étiquette une couleur gris clair, comme illustré dans l’exemple suivant d’image de l’écran restrictions dans iOS :

![Bouton désactivé dans iOS](labels-images/image1.png)

Vous pouvez également définir les couleurs de surbrillance et de texte ombré sur le texte de votre étiquette pour des effets supplémentaires :

```csharp
label.Highlighted = true;
label.HighlightedTextColor = UIColor.Cyan;

label.ShadowColor = UIColor.Black;
label.ShadowOffset = new CoreGraphics.CGSize(1.0f, 1.0f);
```

Qui affiche le texte comme suit :

![Mise en surbrillance et jeu d’ombres sur le texte](labels-images/image4.png)

Pour plus d’informations sur la modification de la police d’un UILabel, consultez [modifier la](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/labels/change_the_font) recette de la police.
