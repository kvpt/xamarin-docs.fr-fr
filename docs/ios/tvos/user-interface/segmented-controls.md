---
title: Utilisation des contrôles segmentés tvOS dans Xamarin
description: Ce document décrit comment utiliser des contrôles segmentés tvOS dans une application générée avec Xamarin. Il traite des icônes de segment et du texte, des événements, de la modification de l’apparence du contrôle, et bien plus encore.
ms.prod: xamarin
ms.assetid: 23AD94CC-E93A-40B1-8E2B-ECD21FA355BE
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: f4f063cf7004d35aa93f688e71a917caa7a3e5a8
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68648944"
---
# <a name="working-with-tvos-segmented-controls-in-xamarin"></a>Utilisation des contrôles segmentés tvOS dans Xamarin

Un contrôle segmenté fournit un ensemble d’éléments linéaires, chacun pouvant contenir une icône ou un texte, et est utilisé pour fournir un ensemble de choix associés à l’utilisateur.

[![](segmented-controls-images/segment01.png "Exemples de contrôles de segment")](segmented-controls-images/segment01.png#lightbox)

Apple propose les suggestions suivantes pour l’utilisation des contrôles segmentés:

- **Fournir un espace** important: vous devez prendre suffisamment de place pour fournir un espace suffisant entre les autres éléments pouvant être [axés sur le focus](~/ios/tvos/app-fundamentals/navigation-focus.md) et un contrôle segmenté. Un segment individuel devient sélectionné lorsqu’il est actif (et non en cas de clic) et l’utilisateur peut modifier accidentellement des segments lorsqu’il souhaite sélectionner un autre élément pouvant être actif sur le segment actuel.
- **Utilisez des affichages fractionnés pour le filtrage de contenu** : les contrôles segmentés n’offrent pas de bons choix pour le filtrage de contenu, car les affichages fractionnés ont été conçus pour faciliter la navigation entre le contenu et les filtres.
- **Limite à sept segments maximum** : vous devez essayer de conserver le nombre maximal de segments sous huit (8), car il est plus facile de les analyser dans la salle du canapé et de naviguer plus facilement.
- **Utiliser la taille de contenu de segment cohérent** -tous les segments ont la même largeur et, si possible, vous devez essayer de conserver le contenu dans chaque segment de la même taille. Cela rend non seulement le contrôle du segment visuellement agréable, mais facilite la lecture d’un coup d’œil.
- **Évitez de mélanger des icônes et du texte** -chaque segment individuel peut contenir une icône ou du texte, mais pas les deux. Bien qu’il soit possible de mélanger les icônes et le texte dans le même contrôle segmenté, cela doit être évité.

<a name="About-Segment-Icons" />

## <a name="about-segment-icons"></a>À propos des icônes de segment

Apple suggère d’utiliser des images simples et reconnaissables pour les icônes de segment, telles qu’une loupe pour la recherche. Les icônes trop complexes sont difficiles à reconnaître sur un écran TV à travers la pièce. il est donc préférable de limiter vos icônes aux représentations simples.

Vous ne pouvez pas mélanger à la fois du texte et des icônes sur un segment donné et éviter de mélanger des icônes et du texte dans un seul contrôle segmenté. Il doit s’agir de toutes les icônes ou de tout le texte.

<a name="Summary" />

## <a name="segment-text"></a>Texte du segment

Apple effectue les suggestions suivantes pour l’utilisation du texte de segment:

- **Utilisez des noms courts et explicites** : le titre du segment doit indiquer clairement le type de contenu que l’utilisateur doit attendre lors de la sélection du segment donné. Par exemple :  Musique ou vidéos.
- **Utiliser** la mise en majuscules de la casse-chaque mot du titre des segments doit être mis en majuscules, à l’exception des articles, des conjonctions et des prépositions de moins de quatre (4) caractères.
- **Utilisez des titres courts et axés** : conservez les titres, courts et concentrés sur le type de contenu à attendre lorsque le segment est sélectionné.

Là encore, vous ne pouvez pas mélanger à la fois du texte et des icônes sur un segment donné et vous devez éviter de mélanger des icônes et du texte dans un seul contrôle segmenté.

<a name="Segment-Controls-and-Storyboards" />

## <a name="segment-controls-and-storyboards"></a>Contrôles de segment et storyboards

Le moyen le plus simple d’utiliser des contrôles de segment dans une application Xamarin. tvOS consiste à les ajouter à l’interface utilisateur de l’application à l’aide du concepteur iOS.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Dans la **panneau solutions**, double-cliquez sur `Main.storyboard` le fichier et ouvrez-le pour le modifier.
1. Faites glisser un **contrôle segment** de la **boîte à outils** et déposez-le sur la vue: 

    [![](segmented-controls-images/segment02.png "Contrôle de segment")](segmented-controls-images/segment02.png#lightbox)
1. Dans l' **onglet widget** du **bloc de propriétés**, vous pouvez ajuster plusieurs propriétés du contrôle segment, telles que son **style** et son **État**: 

    [![](segmented-controls-images/segment03.png "Onglet widget")](segmented-controls-images/segment03.png#lightbox)
1. Utilisez le champ **segments** pour contrôler le nombre de segments dans le contrôleur.
1. Sélectionnez un segment donné dans la **liste déroulante segment** pour ajuster ses propriétés individuelles, telles que le **titre** ou l' **image** , et pour contrôler si un segment donné est **activé** ou **sélectionné** lorsque le contrôle est affiché.
1. Enfin, assignez des **noms** aux contrôles pour pouvoir y répondre dans C# le code. Par exemple : 

    [![](segmented-controls-images/segment04.png "Attribuer un nom")](segmented-controls-images/segment04.png#lightbox)
1. Enregistrez les modifications apportées.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)
    
1. Dans la **Explorateur de solutions**, double-cliquez sur `Main.storyboard` le fichier et ouvrez-le pour le modifier.
1. Faites glisser un **contrôle segment** de la **boîte à outils** et déposez-le sur la vue: 

    [![](segmented-controls-images/segment02-vs.png "Contrôle de segment")](segmented-controls-images/segment02-vs.png#lightbox)
1. Dans l' **onglet widget** de l' **Explorateur de propriétés**, vous pouvez ajuster plusieurs propriétés du contrôle segment, telles que son **style** et son **État**: 

    [![](segmented-controls-images/segment03-vs.png "Onglet widget")](segmented-controls-images/segment03-vs.png#lightbox)
1. Utilisez le champ **segments** pour contrôler le nombre de segments dans le contrôleur.
1. Sélectionnez un segment donné dans la **liste déroulante segment** pour ajuster ses propriétés individuelles, telles que le **titre** ou l' **image** , et pour contrôler si un segment donné est **activé** ou **sélectionné** lorsque le contrôle est affiché.
1. Enfin, assignez des **noms** aux contrôles pour pouvoir y répondre dans C# le code. Par exemple : 

    [![](segmented-controls-images/segment04-vs.png "Attribuer un nom")](segmented-controls-images/segment04-vs.png#lightbox)
1. Enregistrez les modifications apportées.
    
-----

Pour plus d’informations sur l’utilisation des storyboards, consultez notre [Guide de démarrage rapide Hello, tvOS](~/ios/tvos/get-started/hello-tvos.md). 

<a name="Working-with-Segmented-Controls" />

## <a name="working-with-segmented-controls"></a>Utilisation de contrôles segmentés

Comme indiqué ci-dessus, le contrôle segmenté s fournit un ensemble d’éléments linéaires, chacun pouvant contenir une icône ou un texte, et est utilisé pour fournir un ensemble de choix associés à l’utilisateur.

Vous pouvez travailler avec des contrôles segmentés dans votre application Xamarin. tvOS de différentes façons.

<a name="Exposed-as-Outlets-and-Actions" />

## <a name="exposed-as-names-and-events"></a>Exposés en tant que noms et événements

Si vous avez créé votre contrôle de segment dans le concepteur d’interface et que vous l’avez exposé comme un contrôle nommé et un événement, vous pouvez utiliser le code suivant pour répondre au changement de segment:

```csharp
partial void PlayerCountChanged (Foundation.NSObject sender) {

    // Take action based on the number of players selected
    switch(PlayerCount.SelectedSegment) {
    case 0:
        // Do something if the segment is selected
        ...
        break;
    case 1:
        // Do something if the segment is selected
        ...
        break;
    case 2:
        // Do something if the segment is selected
        ...
        break;
    }
}
```

Dans le cas de l’exemple ci-dessus, le contrôle segment est exposé `PlayerCount` en tant que `PlayerCountChanged` nom et action d’événement. Pour plus d’informations sur l’utilisation des actions et des prises, consultez la section [écriture du code avec des sorties et des actions](~/ios/tvos/get-started/hello-tvos.md#Writing-the-Code) de notre [Guide de démarrage rapide Hello, tvOS](~/ios/tvos/get-started/hello-tvos.md).

La `SelectedSegment` propriété obtient ou définit le segment actuellement sélectionné comme un index de base zéro (0). Par conséquent, si vous avez cinq (5) segments, le premier segment aura un index de zéro (0) et le dernier index de quatre (4).

<a name="Modifying-Segments" />

## <a name="modifying-segments"></a>Modification des segments

À tout moment, vous pouvez modifier à la fois le nombre et le contenu de vos contrôles segmentés. Utilisez le code suivant pour insérer un nouveau segment d’icône:

```csharp
// Icon Segment
SegmentedControl.InsertSegment(UIImage.FromFile("icon.png"), 0, true);

// Text Segment
SegmentedControl.InsertSegment("New Segment", 0, true);
```

Le deuxième paramètre définit l’emplacement où le segment sera inséré à l’aide d’un index de base zéro (0). Si le dernier paramètre est `true` , l’insertion est animée.

Pour supprimer un segment donné, utilisez la commande suivante:

```csharp
SegmentedControl.RemoveSegmentAtIndex(0, true);
```

Ou les éléments suivants pour supprimer tous les segments:

```csharp
SegmentedControl.RemoveAllSegments();
```

Là encore, si le dernier paramètre `true`est, la suppression est animée. Utilisez la `NumberOfSegments` propriété pour retourner le nombre actuel de segments.

Pour obtenir le **titre** ou l' **icône** d’un segment donné, utilisez ce qui suit:

```csharp
// Get title
var title = SegmentedControl.TitleAt(0);

// Get icon
var icon = SegmentedControl.ImageAt(0);
```

Pour modifier le **titre** ou l' **icône**, utilisez la commande suivante:

```csharp
// Set title
SegmentedControl.SetTitle("New Title", 0);

// Set icon
SegmentedControl.SetImage(UIImage.FromFile("icon.png"), 0);
```

Pour voir si un segment donné est **activé**, utilisez ce qui suit:

```csharp
if (SegmentedControl.IsEnabled(0)) {
    // Do something
    ...
}
```

Pour **activer/désactiver** un segment donné, utilisez ce qui suit:

```csharp
SegmentedControl.SetEnabled(false, 0);
```

<a name="Modifying-the-Segmented-Controls-Appearance" />

## <a name="modifying-the-segmented-controls-appearance"></a>Modification de l’apparence du contrôle segmenté

Vous pouvez utiliser le code suivant pour modifier l’arrière-plan d’un segment donné en image:

```csharp
SegmentedControl.SetBackgroundImage (UIImage.FromFile("background.png"), UIControlState.Normal, UIBarMetrics.Default);
```

Où `UIControlState` spécifie l’état du contrôle pour lequel vous définissez l’image comme suit:

- Normale
- Mise
- Désactivé
- Selected
- Avec focus

Et `UIBarMetrics` spécifie les mesures à utiliser comme suit:

- Default
- ROM
- DefaultPrompt
- CompactPrompt

En outre, vous pouvez définir le séparateur entre les segments à l’aide de:

```csharp
SegmentedControl.SetDividerImage (UIImage.FromFile("divider.png"), UIControlState.Normal, UIControlState.Normal, UIBarMetrics.Default);
```

Où le premier `UIControlState` spécifie l’état du segment à gauche du séparateur et le second `UIControlState` spécifie l’état du segment à droite.

<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Cet article a abordé la conception et l’utilisation d’un contrôle segmenté à l’intérieur d’une application Xamarin. tvOS.



## <a name="related-links"></a>Liens associés

- [Exemples tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guides de l’interface utilisateur tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guide de programmation d’applications pour tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
