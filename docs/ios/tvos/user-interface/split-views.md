---
title: Utilisation des contrôleurs tvOS Split View dans Xamarin
description: Ce document décrit comment utiliser les vues fractionnées tvOS dans une application générée avec Xamarin. Il fournit une vue d’ensemble des contrôleurs de vue fractionnée, explique comment les utiliser avec les storyboards, accéder aux vues maître et détail et afficher et masquer la vue maître.
ms.prod: xamarin
ms.assetid: 21248CFB-5A94-4C19-B223-C72E0DC5F1D5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 52160d11c8c17a67a5db92e6a95f94815d54a0a6
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68648929"
---
# <a name="working-with-tvos-split-view-controllers-in-xamarin"></a>Utilisation des contrôleurs tvOS Split View dans Xamarin

Un contrôleur d’affichage fractionné présente et gère un contrôleur d’affichage maître et détaillé côte à côte, à l’écran en même temps. Les contrôleurs de vue fractionnée sont utilisés pour présenter le contenu permanent et pouvant être actif dans la vue principale (la plus petite section à gauche) et les détails connexes dans la vue détaillée (la plus grande section à droite).

[![](split-views-images/intro01.png "Exemple d’affichage fractionné")](split-views-images/intro01.png#lightbox)

<a name="About-Split-View-Controllers" />

## <a name="about-split-view-controllers"></a>À propos des contrôleurs de vue fractionnée

Comme indiqué ci-dessus, un contrôleur de vue fractionnée gère un contrôleur d’affichage maître et détaillé présenté côte à côte, le maître étant la plus petite vue sur la gauche, le plus grand à droite. 

En outre, le contrôleur d’affichage maître peut être masqué ou affiché comme requis: 

[![](split-views-images/intro02.png "Contrôleur d’affichage maître masqué")](split-views-images/intro02.png#lightbox)

Les contrôleurs de vues fractionnées sont souvent utilisés pour présenter une liste de contenu filtrable, avec les catégories dans la vue principale et les résultats filtrés dans la vue détaillée. Elle est généralement présentée sous la forme d’une vue de table à gauche et d’une [vue de collection](~/ios/tvos/user-interface/collection-views.md) à droite.

Lors de la conception d’une interface utilisateur qui nécessite un contrôleur d’affichage fractionné, Apple suggère d’utiliser des contrôleurs d’affichage maître et détaillé qui ne changent pas (uniquement les modifications de contenu, et non la structure). Si vous avez besoin de permuter des contrôleurs d’affichage, il est préférable d’utiliser un contrôleur de navigation comme base du contrôleur d’affichage qui doit être modifié (maître ou détail).

Apple propose les suggestions suivantes pour travailler avec les contrôleurs de vue fractionnée:

- **Utiliser le pourcentage de fractionnement correct** : par défaut, le contrôleur du mode fractionné utilise un tiers de l’écran pour le contrôleur d’affichage principal et les deux tiers pour le contrôleur d’affichage détaillé. Si vous le souhaitez, vous pouvez utiliser une division 50/50. Choisissez le pourcentage approprié pour que votre contenu apparaisse équilibré à l’écran.
- **Conserver la sélection principale** -même si le contenu de l’affichage des détails peut changer est la réponse à la sélection d’un utilisateur dans la vue principale, le contenu du mode maître doit être corrigé. En outre, vous devez clairement afficher l’élément actuellement sélectionné dans la vue principale.
- **Utilisez un titre unifié unique** : en général, vous voudrez utiliser un titre unique et centré dans l’affichage des détails plutôt qu’un titre dans les détails et dans la vue principale.

<a name="Split-View-Controllers-and-Storyboards" />

## <a name="split-view-controllers-and-storyboards"></a>Contrôleurs de vue fractionnée et storyboards

Le moyen le plus simple de travailler avec les contrôleurs de vue fractionnée dans une application Xamarin. tvOS consiste à les ajouter à l’interface utilisateur de l’application à l’aide du concepteur iOS.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Dans la **panneau solutions**, double-cliquez sur `Main.storyboard` le fichier et ouvrez-le pour le modifier.
1. Faites glisser les contrôleurs de **vue fractionnée** à partir de la **boîte à outils** et déposez-les sur la vue: 

    [![](split-views-images/activity01.png "Un contrôleur d’affichage fractionné")](split-views-images/activity01.png#lightbox)
1. Par défaut, le concepteur iOS installe un contrôleur de navigation et un contrôleur d’affichage dans la vue maître. Si cela ne répond pas aux exigences de votre application, il vous suffit de les supprimer.
1. Si vous supprimez le mode maître par défaut, faites glisser un nouveau contrôleur d’affichage sur l’aire de conception: 

    [![](split-views-images/activity02.png "Un contrôleur d’affichage")](split-views-images/activity02.png#lightbox)
1. Cliquez avec le contrôle sur le contrôleur du mode fractionné et faites-le glisser vers le nouveau contrôleur d’affichage principal. 
1. Sélectionnez **Master** dans le **menu contextuel**: 

    [![](split-views-images/activity03.png "Sélectionner master dans le menu contextuel")](split-views-images/activity03.png#lightbox)
1. Concevez le contenu des vues principales et détaillées: 

    [![](split-views-images/activity04.png "Exemple de disposition")](split-views-images/activity04.png#lightbox)
1. Affectez des **noms** sous l' **onglet widget** du **panneau Propriétés** pour utiliser vos contrôles d’interface C# utilisateur dans le code.
1. Enregistrez vos modifications et revenez à Visual Studio pour Mac.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Dans la **Explorateur de solutions**, double-cliquez sur `Main.storyboard` le fichier et ouvrez-le pour le modifier.
1. Faites glisser les contrôleurs de **vue fractionnée** à partir de la **boîte à outils** et déposez-les sur la vue: 

    [![](split-views-images/activity01-vs.png "Un contrôleur d’affichage fractionné")](split-views-images/activity01-vs.png#lightbox)
1. Par défaut, le concepteur iOS ajoute un contrôleur de navigation et un contrôleur d’affichage dans la vue maître. Si cela ne répond pas aux exigences de votre application, il vous suffit de les supprimer.
1. Si vous supprimez le mode maître par défaut, faites glisser un nouveau contrôleur d’affichage sur l’aire de conception: 

    [![](split-views-images/activity02-vs.png "Un contrôleur d’affichage")](split-views-images/activity02-vs.png#lightbox)
1. Cliquez avec le contrôle sur le contrôleur du mode fractionné et faites-le glisser vers le nouveau contrôleur d’affichage principal. 
1. Sélectionnez **Master** dans le **menu contextuel**: 

    [![](split-views-images/activity03-vs.png "Sélectionner master dans le menu contextuel")](split-views-images/activity03-vs.png#lightbox)
1. Concevez le contenu des vues principales et détaillées: 

    [![](split-views-images/activity04.png "Disposition du contenu")](split-views-images/activity04.png#lightbox)
1. Affectez des **noms** sous l' **onglet widget** de l' **Explorateur de propriétés** pour travailler avec vos C# contrôles d’interface utilisateur dans le code.
1. Enregistrez les modifications apportées.
    
-----

Pour plus d’informations sur l’utilisation des storyboards, consultez notre [Guide de démarrage rapide Hello, tvOS](~/ios/tvos/get-started/hello-tvos.md).

<a name="Working-with-Split-View-Controllers" />

## <a name="working-with-split-view-controllers"></a>Utilisation des contrôleurs de vue fractionnée

Comme indiqué ci-dessus, un contrôleur de vue fractionnée est souvent utilisé dans les situations où vous affichez le contenu filtré pour l’utilisateur. Les catégories principales sont affichées à gauche dans la vue principale et les résultats filtrés à droite dans l’affichage détaillé en fonction de la sélection de l’utilisateur.

<a name="Accessing-Master-and-Detail" />

### <a name="accessing-master-and-detail"></a>Accès au maître et au détail

Si vous devez accéder aux contrôleurs d’affichage maître et détail par programme, utilisez la `ViewControllers` propriété du contrôleur d’affichage fractionné. Par exemple :

```csharp
// Gain access to master and detail view controllers
var masterController = ViewControllers [0] as MasterViewController;
var detailController = ViewControllers [1] as DetailViewController;
```

Il est présenté sous la forme d’un tableau, où le premier élément (0) dans le contrôleur d’affichage principal et le deuxième élément (1) sont les détails.

<a name="Accessing-Detail-from-Master" />

### <a name="accessing-detail-from-master"></a>Accès aux détails à partir du maître

Dans la mesure où vous affichez généralement des informations détaillées dans l’affichage détaillé en fonction de la sélection de l’utilisateur dans le maître, vous devez disposer d’un moyen d’accéder aux détails à partir du maître.

Le moyen le plus simple consiste à exposer une propriété sur votre classe de contrôleur d’affichage maître, par exemple:

```csharp
public DetailViewController DetailController { get; set;}
```

Dans le contrôleur d’affichage fractionné, remplacez la `ViewDidLoad` méthode et liez les deux vues ensemble. Par exemple :

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Gain access to master and detail view controllers
    var masterController = ViewControllers [0] as MasterViewController;
    var detailController = ViewControllers [1] as DetailViewController;

    // Wire-up views
    masterController.SplitViewController = this;
    masterController.DetailController = detailController;
    detailController.SplitViewController = this;
}
```

Vous pouvez exposer des propriétés et des méthodes sur votre contrôleur d’affichage détaillé que le maître peut utiliser pour présenter de nouvelles données selon les besoins.

<a name="Showing-and-Hiding-Master" />

### <a name="showing-and-hiding-master"></a>Représentation et masquage du maître

Si vous le souhaitez, vous pouvez afficher et masquer le contrôleur d’affichage `PreferredDisplayMode` principal à l’aide de la propriété du contrôleur d’affichage fractionné. Par exemple :

```csharp
// Show hide split view
if (SplitViewController.DisplayMode == UISplitViewControllerDisplayMode.PrimaryHidden) {
    SplitViewController.PreferredDisplayMode = UISplitViewControllerDisplayMode.AllVisible;
} else {
    SplitViewController.PreferredDisplayMode = UISplitViewControllerDisplayMode.PrimaryHidden;
}
```

L' `UISplitViewControllerDisplayMode` énumération définit la manière dont le contrôleur d’affichage principal sera présenté comme l’un des éléments suivants:

- **Automatique** -tvOS contrôle la présentation des vues maître et détail.
- **PrimaryHidden** : masque le contrôleur d’affichage principal.
- **AllVisible** : affiche les contrôleurs principaux et détaillés côte à côte. Il s’agit de la présentation normale par défaut.
- **PrimaryOverlay** : le contrôleur d’affichage détaillé s’étend sous et est couvert par le maître.

Pour obtenir l’état actuel de la présentation, `DisplayMode` utilisez la propriété du contrôleur d’affichage fractionné.

<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Cet article a abordé la conception et l’utilisation des contrôleurs de vue fractionnée à l’intérieur d’une application Xamarin. tvOS.



## <a name="related-links"></a>Liens associés

- [Exemples tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guides de l’interface utilisateur tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guide de programmation d’applications pour tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
