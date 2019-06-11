---
title: Options de disposition dans Xamarin.iOS
description: Ce document décrit différentes façons d’organiser des interfaces dans Xamarin.iOS. Il aborde le redimensionnement automatique et la disposition automatique.
ms.prod: xamarin
ms.assetid: D8180FEC-F300-42C0-B029-66803E0C1A5F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 1bcced2f43328bf5e7d1ebb171b3c92c9ec22493
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827762"
---
# <a name="layout-options-in-xamarinios"></a>Options de disposition dans Xamarin.iOS

Il existe deux mécanismes différents pour contrôler la disposition lorsqu’une vue est redimensionnée ou pivotée :

-  **Redimensionnement automatique** – inspecteur le redimensionnement automatique dans le concepteur fournit une façon de définir le `AutoresizingMask` propriétés. Ainsi, un contrôle sera ancré sur les bords de leur conteneur et/ou corriger leur taille. Redimensionnement automatique fonctionne dans toutes les versions d’e/s. Cette opération est décrite plus en détail ci-dessous
-  **Disposition automatique** – une fonctionnalité, introduite dans iOS 6, qui permet un contrôle affiné sur les relations entre les contrôles d’interface utilisateur. Il permettra de contrôle de la position des éléments par rapport aux autres éléments sur l’aire de conception. Cette rubrique est couvert plus en détail dans le [disposition automatique avec le Concepteur de Xamarin iOS](~/ios/user-interface/designer/designer-auto-layout.md) guide.

## <a name="autosizing"></a>Redimensionner automatiquement

Lorsqu’un utilisateur redimensionne une fenêtre, comme lorsque l’appareil est pivoté et les changements d’orientation, le système va se redimensionner automatiquement les vues contenues dans cette fenêtre selon leurs règles de redimensionnement automatique. Ces règles peuvent être définies C# à l’aide de la `AutoresizingMask` propriété de la `UIView` ou dans le **panneau Propriétés** d’e/s du concepteur, comme illustré ci-dessous :

 [![](layout-options-images/image41.png "Visual Studio pour Mac Concepteur")](layout-options-images/image41.png#lightbox)

Lorsqu’un contrôle est sélectionné, cela vous permet de spécifier manuellement l’emplacement et les dimensions du contrôle, ainsi que les choix **redimensionnement automatique** comportement. Comme illustré dans la capture d’écran ci-dessous, nous pouvons utiliser le springs et struts dans le contrôle de redimensionnement automatique pour définir les relations de la vue sélectionnée à son parent :

 [![](layout-options-images/image42.png "Visual Studio pour Mac Concepteur")](layout-options-images/image42.png#lightbox)

Ajuster une *spring* entraîne l’affichage redimensionner en fonction de la largeur ou hauteur de la vue parent. Ajuster une *jambe* rendra la vue de maintenir une distance constante entre lui-même et sa vue parent, sur ce bord particulier.

Ces paramètres peuvent également être définis dans le code :

```csharp
textfield1.Frame = new RectangleF(15, 277, 79, 27);
textfield1.AutoresizingMask = UIViewAutoresizing.FlexibleRightMargin | UIViewAutoresizing.FlexibleBottomMargin;
```


Pour tester les paramètres de redimensionnement automatique, activer différents **Orientations prises en charge de l’appareil** dans les options du projet :

 [![](layout-options-images/image43a.png "Paramètres de redimensionnement automatique")](layout-options-images/image43a.png#lightbox)

Dans le code-behind, nous pouvons utiliser le code suivant, ce qui provoque les contrôles de texte de deux à redimensionner horizontalement :

```csharp
textview1.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
textfield1.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
imageview1.AutoresizingMask = UIViewAutoresizing.FlexibleTopMargin | UIViewAutoresizing.FlexibleLeftMargin;
```


Nous pouvons également ajuster les contrôles à l’aide du concepteur. En sélectionnant les struts comme présenté ci-dessous entraîne l’image rester aligné à droite sans être détourée la partie inférieure de la vue :

 [![](layout-options-images/autoresize.png "AutoRotation")](layout-options-images/autoresize.png#lightbox)

Ces captures d’écran montrent comment les contrôles de déplacer ou redimensionnement eux-mêmes lors de la rotation de l’écran :

 [![](layout-options-images/image44a.png "AutoRotation")](layout-options-images/image44a.png#lightbox)

Notez que l’affichage de texte et un champ de texte à la fois s’étire pour conserver la même gauche et droite des marges, en raison du `FlexibleWidth` paramètre. L’image a la supérieure et gauche marge flexible, ce qui signifie qu’il conserve les marges inférieure et droite – en conservant l’image dans la vue lors de la rotation de l’écran. Des dispositions complexes requièrent généralement une combinaison de ces paramètres sur tous les contrôles visibles pour maintenir la cohérence de l’interface utilisateur et pour empêcher que les contrôles qui se chevauchent lorsque les limites de la vue Modifier (en raison d’une rotation ou tout autre événement de redimensionnement).





## <a name="related-links"></a>Liens associés

- [Contrôles (exemple)](https://developer.xamarin.com/samples/monotouch/Controls/)
