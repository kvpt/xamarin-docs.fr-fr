---
title: API Appearance dans Xamarin. iOS
description: iOS vous permet d’appliquer des paramètres de propriété visuelle à un niveau de classe statique plutôt qu’à des objets individuels afin que la modification s’applique à toutes les instances de ce contrôle dans l’application.
ms.prod: xamarin
ms.assetid: C1727F0C-82B1-D085-D46F-C6383FF04B16
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 11/15/2018
ms.openlocfilehash: 7ba6eca8f74c10254ae93b95725bc73ae100be70
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433857"
---
# <a name="appearance-api-in-xamarinios"></a>API Appearance dans Xamarin. iOS

_iOS vous permet d’appliquer des paramètres de propriété visuelle à un niveau de classe statique plutôt qu’à des objets individuels afin que la modification s’applique à toutes les instances de ce contrôle dans l’application._

Cette fonctionnalité est exposée dans Xamarin. iOS via une `Appearance` propriété statique sur tous les contrôles UIKit qui la prennent en charge. L’apparence visuelle (des propriétés telles que la couleur de teinte et l’image d’arrière-plan) peut donc être facilement personnalisée pour donner une apparence cohérente à votre application. L’API Appearance a été introduite dans iOS 5, tandis que certaines parties de celle-ci ont été dépréciées dans iOS 9. il s’agit toujours d’un bon moyen d’obtenir des effets de style et de thème dans des applications Xamarin. iOS.

## <a name="overview"></a>Vue d’ensemble

iOS vous permet de personnaliser l’apparence de nombreux contrôles UIKit pour que les contrôles standard soient conformes à la marque que vous souhaitez appliquer à votre application.

Il existe deux façons différentes d’appliquer une apparence personnalisée :

- **Directement sur une instance de contrôle** : vous pouvez définir la couleur de teinte, l’image d’arrière-plan et la position du titre (ainsi que d’autres attributs) sur de nombreux contrôles, notamment les barres d’outils, les barres de navigation, les boutons et les curseurs.

- **Définissez les valeurs par défaut sur la propriété statique d’apparence** : les attributs personnalisables pour chaque contrôle sont exposés via la `Appearance` propriété statique. Toutes les personnalisations que vous appliquez à ces propriétés sont utilisées par défaut pour tout contrôle de ce type créé après la définition de la propriété.

L’exemple d’application Appearance illustre les trois méthodes, comme indiqué dans les captures d’écran suivantes :

[![L’exemple d’application Appearance illustre les trois méthodes](introduction-to-the-appearance-api-images/appearance01-sml.png)](introduction-to-the-appearance-api-images/appearance01.png#lightbox)

Depuis iOS 8, le proxy d’apparence a été étendu à TraitCollections.
 `AppearanceForTraitCollection` peut être utilisé pour définir l’apparence par défaut sur une collection de traits particulière. Pour plus d’informations à ce sujet, consultez le guide [Présentation des storyboards](~/ios/user-interface/storyboards/unified-storyboards.md) .

## <a name="setting-appearance-properties"></a>Définition des propriétés d’apparence

Dans le premier écran, la classe d’apparence statique est utilisée pour appliquer un style aux boutons et aux éléments jaunes/orange comme suit :

```csharp
// Set the default appearance values
UIButton.Appearance.TintColor = UIColor.LightGray;
UIButton.Appearance.SetTitleColor(UIColor.FromRGB(0,127,14), UIControlState.Normal);

UISlider.Appearance.ThumbTintColor = UIColor.Red;
UISlider.Appearance.MinimumTrackTintColor = UIColor.Orange;
UISlider.Appearance.MaximumTrackTintColor = UIColor.Yellow;

UIProgressView.Appearance.ProgressTintColor = UIColor.Yellow;
UIProgressView.Appearance.TrackTintColor = UIColor.Orange;
```

Les styles d’élément verts sont définis comme ceci, dans la `ViewDidLoad` méthode qui remplace les valeurs par défaut et la classe statique d' *apparence* :

```csharp
slider2.ThumbTintColor = UIColor.FromRGB (0,127,70); // dark green
slider2.MinimumTrackTintColor = UIColor.FromRGB (66,255,63);
slider2.MaximumTrackTintColor = UIColor.FromRGB (197,255,132);
```

```csharp
progress2.ProgressTintColor = UIColor.FromRGB (66,255,63);
progress2.TrackTintColor = UIColor.FromRGB (197,255,132);
```

## <a name="using-uiappearance-in-xamarinforms"></a>Utilisation de UIAppearance dans Xamarin. Forms

L’API Appearance peut être utile lorsque vous [appliquez un style à l’application iOS](~/xamarin-forms/platform/ios/formatting.md#uiappearance-api) dans des solutions Xamarin. Forms. Quelques lignes de la `AppDelegate` classe peuvent aider à implémenter un modèle de couleurs spécifique sans avoir à créer un [convertisseur personnalisé](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

### <a name="custom-themes-and-uiappearance"></a>Thèmes personnalisés et UIAppearance

iOS permet à de nombreux attributs visuels des contrôles de l’interface utilisateur d’être « à thème » à l’aide des API *UIAppearance* pour forcer toutes les instances d’un contrôle particulier à avoir la même apparence. Cela est exposé en tant que propriété d’apparence sur de nombreuses classes de contrôle d’interface utilisateur, et non sur des instances individuelles du contrôle. La définition d’une propriété d’affichage sur la `Appearance` propriété statique affecte tous les contrôles de ce type dans votre application.

Pour mieux comprendre le concept, prenons un exemple.

Pour modifier un spécifique `UISegmentedControl` de façon à ce qu’il ait une teinte magenta, nous référençons le contrôle spécifique sur notre écran comme suit dans `ViewDidLoad` :

```csharp
sg1.TintColor = UIColor.Magenta;
```

Vous pouvez également définir la valeur dans le panneau Propriétés du concepteur :

[![Teinte de Panneau Propriétés](introduction-to-the-appearance-api-images/propertiespadtint.png)](introduction-to-the-appearance-api-images/propertiespadtint.png#lightbox)

L’image ci-dessous montre que cela définit la teinte uniquement sur le contrôle nommé « SG1 ».

[![Définition de la teinte d’un contrôle individuel](introduction-to-the-appearance-api-images/image53.png)](introduction-to-the-appearance-api-images/image53.png#lightbox)

Pour définir de nombreux contrôles de cette manière serait totalement inefficace, nous pouvons plutôt définir la propriété statique `Appearance` sur la classe elle-même. Cela est illustré dans le code ci-dessous :

```csharp
UISegmentedControl.Appearance.TintColor = UIColor.Magenta;
```

L’image ci-dessous illustre maintenant les deux contrôles segmentés avec l’apparence définie sur magenta :

[![Définition de la teinte de contrôle d’apparence](introduction-to-the-appearance-api-images/image54.png)](introduction-to-the-appearance-api-images/image54.png#lightbox)

`Appearance` les propriétés doivent être définies au début du cycle de vie de l’application, comme dans l’événement de AppDelegate `FinishedLaunching` ou dans un ViewController avant l’affichage des contrôles affectés.

Reportez-vous à la [Présentation de l’API d’apparence](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md) pour obtenir des informations plus détaillées.

## <a name="related-links"></a>Liens associés

- [Apparence (exemple)](/samples/xamarin/ios-samples/appearance)
- [Référence du protocole UIAppearance](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIAppearance_Protocol/)
- [Apparence dans Xamarin. Forms](~/xamarin-forms/platform/ios/formatting.md#uiappearance-api)