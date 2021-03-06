---
title: Utilisation des barres de navigation tvOS dans Xamarin
description: Ce document explique comment utiliser les barres de navigation dans une application tvOS générée avec Xamarin. Elle traite de la configuration des barres de navigation dans un Storyboard et de la réponse aux événements de ces boutons.
ms.prod: xamarin
ms.assetid: 74E396B7-87F0-46F7-BC6C-827DB8884C97
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 3ee159eaaf4a124f652b083e4dd7341909f52d82
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433123"
---
# <a name="working-with-tvos-navigation-bars-in-xamarin"></a>Utilisation des barres de navigation tvOS dans Xamarin

Les barres de navigation peuvent être ajoutées en haut des affichages pour afficher un titre et des boutons de barre de navigation facultatifs. En règle générale, elles sont utilisées lorsque l’utilisateur a navigué à partir d’une page principale, comme une vue de table, une collection ou un menu vers un sous-affichage présentant les détails de l’élément sélectionné.

[![Exemple de barre de navigation](navigation-bars-images/navbar01.png)](navigation-bars-images/navbar01.png#lightbox)

En plus du titre (affiché au centre), les barres de navigation peuvent contenir un ou plusieurs boutons de barre de navigation ( `UIBarButtonItem` ) sur les côtés gauche et droit de la barre.

> [!IMPORTANT]
> Les barres de navigation sont totalement transparentes par défaut. Veillez à veiller à ce que le contenu de la barre de navigation reste lisible sur le contenu situé sous celui-ci. Par exemple, lorsque vous faites défiler le contenu d’une vue ou d’une collection de table.

<a name="Navigation-Bars-and-Storyboards"></a>

## <a name="navigation-bars-and-storyboards"></a>Barres de navigation et storyboards

Le moyen le plus simple d’utiliser les barres de navigation dans une application Xamarin. tvOS consiste à les ajouter à l’interface utilisateur de l’application à l’aide du concepteur iOS.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

1. Dans la **panneau solutions**, double-cliquez sur `Main.storyboard` fichier et ouvrez-le pour le modifier.
1. Faites glisser une **barre de navigation** à partir de la **boîte à outils** et déposez-la sur la vue en haut de l’écran :

    [![Barre de navigation](navigation-bars-images/navbar02.png)](navigation-bars-images/navbar02.png#lightbox)
1. Double-cliquez sur la **barre de navigation** pour sélectionner l' **élément de navigation**. Dans l’onglet **widget** du **panneau Propriétés**, vous pouvez définir le **titre**:

    [![Définir le titre](navigation-bars-images/navbar03.png)](navigation-bars-images/navbar03.png#lightbox)
1. Ensuite, vous pouvez ajouter un ou plusieurs **éléments de bouton de barre** à l’une ou l’autre des extrémités de la barre :

    [![Élément de bouton de barre](navigation-bars-images/navbar04.png)](navigation-bars-images/navbar04.png#lightbox)
1. Enfin, associez les **éléments du bouton de barre** aux actions de l’onglet **événements** de l' **Explorateur de propriétés**:

    [![Action d’élément de bouton de barre](navigation-bars-images/navbar05.png)](navigation-bars-images/navbar05.png#lightbox)
1. Enregistrez vos modifications.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Dans la **Explorateur de solutions**, double-cliquez sur `Main.storyboard` fichier et ouvrez-le pour le modifier.
1. Faites glisser une **barre de navigation** à partir de la **boîte à outils** et déposez-la sur la vue en haut de l’écran :

    [![Barre de navigation](navigation-bars-images/navbar02-vs.png)](navigation-bars-images/navbar02-vs.png#lightbox)
1. Double-cliquez sur la **barre de navigation** pour sélectionner l' **élément de navigation**. Dans l’onglet **widget** de l' **Explorateur de propriétés**, vous pouvez définir le **titre**:

    [![Définir le titre](navigation-bars-images/navbar03-vs.png)](navigation-bars-images/navbar03-vs.png#lightbox)
1. Ensuite, vous pouvez ajouter un ou plusieurs **éléments de bouton de barre** à l’une ou l’autre des extrémités de la barre :

    [![Éléments de bouton de barre](navigation-bars-images/navbar04-vs.png)](navigation-bars-images/navbar04-vs.png#lightbox)
1. Enfin, associez les **éléments du bouton de barre** aux actions de l’onglet **événements** de l' **Explorateur de propriétés**:

    [![Actions d’élément de bouton de barre](navigation-bars-images/navbar05-vs.png)](navigation-bars-images/navbar05-vs.png#lightbox)
1. Enregistrez vos modifications.

-----

> [!IMPORTANT]
> Bien qu’il soit possible d’assigner des événements tels que `TouchUpInside` à un élément d’interface utilisateur (par exemple, un UIButton) dans le concepteur iOS, il ne sera jamais appelé, car Apple TV n’a pas d’écran tactile ou de prise en charge des événements tactiles. Vous devez toujours utiliser l' `Primary Action` événement lors de la création de gestionnaires d’événements pour les éléments d’interface utilisateur tvOS.

Le code suivant fournit un exemple de gestionnaires d’événements sur trois BarButtonItems différents : `ShowFirstHotel` , `ShowSecondHotel` et `ShowThirdHotel` . Lorsque l’utilisateur clique sur chaque élément, l’image d’arrière-plan `HotelImage` est modifiée. Cela est modifié dans le fichier de contrôleur d’affichage (exemple `ViewController.cs` ) :

```csharp
using System;
using Foundation;
using UIKit;

namespace MySingleView
{
    public partial class ViewController : UIViewController
    {
        #region Constructors
        public ViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();
            // Perform any additional setup after loading the view, typically from a nib.
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
        #endregion

        #region Custom Actions
        partial void ShowFirstHotel (UIBarButtonItem sender) {
            // Change background image
            HotelImage.Image = UIImage.FromFile("Motel01.jpg");
        }

        partial void ShowSecondHotel (UIBarButtonItem sender) {
            // Change background image
            HotelImage.Image = UIImage.FromFile("Motel02.jpg");
        }

        partial void ShowThirdHotel (UIBarButtonItem sender) {
            // Change background image
            HotelImage.Image = UIImage.FromFile("Motel03.jpg");
        }
        #endregion
    }
}
```

Tant que la propriété d’un bouton a la valeur `Enabled` `true` et qu’elle n’est pas couverte par un autre contrôle ou vue, elle peut être rendue en élément actif à l’aide de la Siri distante.

Pour plus d’informations sur l’utilisation des storyboards, consultez notre [Guide de démarrage rapide Hello, tvOS](~/ios/tvos/get-started/hello-tvos.md).

<a name="Summary"></a>

## <a name="summary"></a>Récapitulatif

Cet article a abordé la conception et l’utilisation des barres de navigation à l’intérieur d’une application Xamarin. tvOS.

## <a name="related-links"></a>Liens associés

- [Exemples tvOS](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guides de l’interface utilisateur tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guide de programmation d’applications pour tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)