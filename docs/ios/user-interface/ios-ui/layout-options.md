---
title: Options de disposition dans Xamarin. iOS
description: Ce document décrit les différentes façons de disposer les interfaces utilisateur dans Xamarin. iOS. Il aborde le dimensionnement automatique et la mise en page automatique.
ms.prod: xamarin
ms.assetid: D8180FEC-F300-42C0-B029-66803E0C1A5F
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 2514287fe06216d62b994cf19ba8f0901dd36c49
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73003328"
---
# <a name="layout-options-in-xamarinios"></a>Options de disposition dans Xamarin. iOS

Il existe deux mécanismes différents pour contrôler la disposition lorsqu’une vue est redimensionnée ou pivotée :

- **Redimensionnement** automatique : l’inspecteur de redimensionnement automatique dans le concepteur permet de définir les propriétés de la `AutoresizingMask`. Cela permet à un contrôle d’être ancré sur les bords de son conteneur et/ou de corriger leur taille. Le redimensionnement automatique fonctionne dans toutes les versions d’iOS. Cela est décrit plus en détail ci-dessous.
- **Disposition automatique** : fonctionnalité, introduite dans iOS 6, qui permet de contrôler précisément les relations des contrôles de l’interface utilisateur. Il permet de contrôler les positions des éléments par rapport à d’autres éléments sur l’aire de conception. Cette rubrique est traitée plus en détail dans le guide [mise en page automatique avec le concepteur Xamarin iOS](~/ios/user-interface/designer/designer-auto-layout.md) .

## <a name="autosizing"></a>Redimensionnement automatique

Quand un utilisateur redimensionne une fenêtre, par exemple lorsque l’appareil est pivoté et que l’orientation change, le système redimensionne automatiquement les vues dans cette fenêtre en fonction de leurs règles de redimensionnement automatique. Ces règles peuvent être définies dans C# à l’aide de la propriété`AutoresizingMask`de la`UIView`ou dans le **panneau Propriétés** du concepteur iOS, comme illustré ci-dessous :

 [![](layout-options-images/image41.png "Visual Studio for Mac Designer")](layout-options-images/image41.png#lightbox)

Lorsqu’un contrôle est sélectionné, cela vous permet de spécifier manuellement l’emplacement et les dimensions du contrôle, et de choisir le comportement de **redimensionnement** automatique. Comme illustré dans la capture d’écran ci-dessous, nous pouvons utiliser les ressorts et les jambes dans le contrôle de redimensionnement automatique pour définir la relation de la vue sélectionnée avec son parent :

 [![](layout-options-images/image42.png "Visual Studio for Mac Designer")](layout-options-images/image42.png#lightbox)

L’ajustement d’un *ressort* entraîne le redimensionnement de la vue en fonction de la largeur ou de la hauteur de sa vue parent. Si vous ajustez une *jambe* , la vue maintient une distance constante entre elle et sa vue parent, sur cette arête particulière.

Ces paramètres peuvent également être définis dans le code :

```csharp
textfield1.Frame = new RectangleF(15, 277, 79, 27);
textfield1.AutoresizingMask = UIViewAutoresizing.FlexibleRightMargin | UIViewAutoresizing.FlexibleBottomMargin;
```

Pour tester les paramètres de redimensionnement automatique, activez les différentes **orientations d’appareils prises en charge** dans les options du projet :

 [![](layout-options-images/image43a.png "Autosizing Settings")](layout-options-images/image43a.png#lightbox)

Dans le code-behind, nous pouvons utiliser le code suivant, qui entraîne le redimensionnement horizontal des deux contrôles de texte :

```csharp
textview1.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
textfield1.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
imageview1.AutoresizingMask = UIViewAutoresizing.FlexibleTopMargin | UIViewAutoresizing.FlexibleLeftMargin;
```

Nous pouvons également ajuster les contrôles à l’aide du concepteur. Si vous sélectionnez les jambes comme indiqué ci-dessous, l’image reste alignée à droite sans être rognée en bas de la vue :

 [![](layout-options-images/autoresize.png "Autorotation")](layout-options-images/autoresize.png#lightbox)

Ces captures d’écran montrent comment les contrôles sont redimensionnés ou repositionnés lors de la rotation de l’écran :

 [![](layout-options-images/image44a.png "Autorotation")](layout-options-images/image44a.png#lightbox)

Notez que l’affichage de texte et le champ de texte sont étirés pour conserver les mêmes marges de gauche et de droite, en raison du paramètre `FlexibleWidth`. La marge supérieure et gauche de l’image est flexible, ce qui signifie qu’elle conserve les marges inférieure et droite, ce qui permet d’afficher l’image lors de la rotation de l’écran. Les dispositions complexes requièrent généralement une combinaison de ces paramètres sur chaque contrôle visible pour maintenir la cohérence de l’interface utilisateur et empêcher le chevauchement des contrôles lorsque les limites de la vue changent (en raison de la rotation ou d’un autre événement de redimensionnement).

## <a name="related-links"></a>Liens associés

- [Contrôles (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/controls)
