---
title: Utilisation de tvOS contrôleurs d’affichage fractionné dans Xamarin
description: Ce document décrit comment travailler avec tvOS fractionner les affichages dans une application conçue avec Xamarin. Il fournit une vue d’ensemble de contrôleurs d’affichage fractionné, comment les utiliser avec des storyboards, les vues maître et détail, de l’accès à l’affichage et masquage de la vue maître.
ms.prod: xamarin
ms.assetid: 21248CFB-5A94-4C19-B223-C72E0DC5F1D5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 775fe01122440378f68973549d46e3010400614e
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830849"
---
# <a name="working-with-tvos-split-view-controllers-in-xamarin"></a>Utilisation de tvOS contrôleurs d’affichage fractionné dans Xamarin

Un contrôleur d’affichage fractionné présente et gère un maître et détail-View-Controller-côte, à l’écran en même temps. Contrôleurs d’affichage fractionné sont utilisées pour présenter du contenu persistant, pouvant prendre le focus dans la vue principale (la plus petite section sur la gauche) et liés détails dans la vue de détail (la section supérieure droite).

[![](split-views-images/intro01.png "Vue d’exemples de fractionnement")](split-views-images/intro01.png#lightbox)

<a name="About-Split-View-Controllers" />

## <a name="about-split-view-controllers"></a>Sur les contrôleurs d’affichage fractionné

Comme indiqué ci-dessus, un contrôleur d’affichage fractionné gère un Master et le contrôleur d’affichage de détails sont présentés côte à côte, avec le maître en cours de la vue plus petits sur la gauche, le détail le plus grand sur la droite. 

En outre, le peut contrôleur d’affichage maître été masquée ou affichée en fonction des besoins : 

[![](split-views-images/intro02.png "Le contrôleur d’affichage maître masqué")](split-views-images/intro02.png#lightbox)

Contrôleurs de vues de fractionnement sont utilisent souvent pour présenter une liste de contenu filtrable, avec les catégories dans la vue principale et les résultats filtrés dans la vue détail. Cela est généralement présenté comme une vue de Table sur la gauche et un [vue de Collection](~/ios/tvos/user-interface/collection-views.md) sur la droite.

Lorsque vous concevez une Interface utilisateur qui nécessite un contrôleur d’affichage fractionné, les suggestions d’Apple à l’aide de Master et les contrôleurs d’affichage Détails qui ne changent pas (uniquement les modifications de contenu, pas la structure). Si vous n’avez pas besoin de contrôleurs d’affichage de permutation, il est préférable d’utiliser un contrôleur de Navigation en tant que la base de contrôleur d’affichage qui doit être modifié (Master ou détails).

Apple a les suggestions suivantes pour l’utilisation des contrôleurs d’affichage fractionné :

- **Utiliser le pourcentage de fractionnement Correct** -par défaut le contrôleur d’affichage fractionné utilise un tiers de l’écran pour le contrôleur d’affichage maître et deux tiers pour le contrôleur d’affichage Détails. Si vous le souhaitez, vous pouvez utiliser un fractionnement 50/50. Choisissez le pourcentage correct pour afficher votre contenu à charge équilibrée à l’écran.
- **Conserver la sélection principale** - tout le contenu sur la vue de détail peuvent modifier la réponse à la sélection d’un utilisateur dans la vue principale, le contenu de la vue principale devrait être corrigé. En outre, vous devez afficher clairement l’élément actuellement sélectionné dans la vue principale.
- **Utiliser un seul titre unifiée** -en général, vous souhaitez utiliser un titre unique, centré dans la vue détail, au lieu d’un titre dans le détail et la vue principale.

<a name="Split-View-Controllers-and-Storyboards" />

## <a name="split-view-controllers-and-storyboards"></a>Fractionner les contrôleurs d’affichage et Storyboards

Pour travailler avec les contrôleurs d’affichage fractionné dans une application Xamarin.tvOS, le plus simple consiste à ajouter à l’interface utilisateur de l’application à l’aide du concepteur iOS.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Dans le **panneau solutions**, double-cliquez sur le `Main.storyboard` de fichier et l’ouvrir pour modification.
1. Faites glisser un **contrôleurs d’affichage fractionné** à partir de la **boîte à outils** et déposez-la sur la vue : 

    [![](split-views-images/activity01.png "Un contrôleur d’affichage fractionné")](split-views-images/activity01.png#lightbox)
1. Par défaut, le concepteur iOS installera un contrôleur de Navigation et un contrôleur d’affichage dans la vue principale. Si cela ne tient pas les exigences de votre application, simplement les supprimer.
1. Si vous ne supprimez pas la vue principale de la valeur par défaut, faites glisser un contrôleur d’affichage sur l’aire de conception : 

    [![](split-views-images/activity02.png "Un contrôleur d’affichage")](split-views-images/activity02.png#lightbox)
1. Cliquez et faites glisser le contrôleur d’affichage fractionné vers le nouveau contrôleur de vue maître. 
1. Sélectionnez **Master** à partir de la **Menu contextuel**: 

    [![](split-views-images/activity03.png "Sélectionnez maître dans le Menu contextuel")](split-views-images/activity03.png#lightbox)
1. Concevoir le contenu de votre maître et détail : 

    [![](split-views-images/activity04.png "Exemple de disposition")](split-views-images/activity04.png#lightbox)
1. Affecter **noms** dans le **onglet Widget** de la **panneau Propriétés** pour travailler avec les contrôles d’IU dans C# code.
1. Enregistrez vos modifications et revenir à Visual Studio pour Mac.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Dans le **l’Explorateur de solutions**, double-cliquez sur le `Main.storyboard` de fichier et l’ouvrir pour modification.
1. Faites glisser un **contrôleurs d’affichage fractionné** à partir de la **boîte à outils** et déposez-la sur la vue : 

    [![](split-views-images/activity01-vs.png "Un contrôleur d’affichage fractionné")](split-views-images/activity01-vs.png#lightbox)
1. Par défaut, le concepteur iOS ajoute un contrôleur de Navigation et le contrôleur d’affichage dans la vue principale. Si cela ne tient pas les exigences de votre application, simplement les supprimer.
1. Si vous ne supprimez pas la vue principale de la valeur par défaut, faites glisser un contrôleur d’affichage sur l’aire de conception : 

    [![](split-views-images/activity02-vs.png "Un contrôleur d’affichage")](split-views-images/activity02-vs.png#lightbox)
1. Cliquez et faites glisser le contrôleur d’affichage fractionné vers le nouveau contrôleur de vue maître. 
1. Sélectionnez **Master** à partir de la **Menu contextuel**: 

    [![](split-views-images/activity03-vs.png "Sélectionnez maître dans le Menu contextuel")](split-views-images/activity03-vs.png#lightbox)
1. Concevoir le contenu de votre maître et détail : 

    [![](split-views-images/activity04.png "Disposition du contenu")](split-views-images/activity04.png#lightbox)
1. Affecter **noms** dans le **onglet Widget** de la **l’Explorateur de propriétés** pour travailler avec les contrôles d’IU dans C# code.
1. Enregistrez les modifications apportées.
    
-----

Pour plus d’informations sur l’utilisation des tables de montage séquentiel, consultez notre [Hello, tvOS Guide de démarrage rapide](~/ios/tvos/get-started/hello-tvos.md).

<a name="Working-with-Split-View-Controllers" />

## <a name="working-with-split-view-controllers"></a>Utilisation des contrôleurs d’affichage fractionné

Comme indiqué ci-dessus, un contrôleur d’affichage fractionné est souvent utilisé dans les situations où vous affichez contenu filtré à l’utilisateur. Les principales catégories sont affichées sur la gauche dans la vue principale, et les résultats filtrés sur la droite dans la vue détail en fonction de sélection de l’utilisateur.

<a name="Accessing-Master-and-Detail" />

### <a name="accessing-master-and-detail"></a>L’accès aux maître et détail

Si vous avez besoin d’accéder par programme les maîtres et des contrôleurs d’affichage de détails, utilisez le `ViewControllers` propriété du contrôleur d’affichage fractionné. Par exemple :

```csharp
// Gain access to master and detail view controllers
var masterController = ViewControllers [0] as MasterViewController;
var detailController = ViewControllers [1] as DetailViewController;
```

Il est présenté sous forme de tableau, où le premier élément (0) dans le contrôleur d’affichage maître et le deuxième élément (1) sont le détail.

<a name="Accessing-Detail-from-Master" />

### <a name="accessing-detail-from-master"></a>L’accès aux détails du principal

Puisque vous affichez généralement des informations détaillées dans la vue de détail en fonction de sélection de l’utilisateur dans le maître, vous aurez besoin d’un moyen pour accéder aux détails du serveur maître.

Pour ce faire, le plus simple consiste à exposer une propriété sur votre classe de contrôleur d’affichage principal, par exemple :

```csharp
public DetailViewController DetailController { get; set;}
```

Dans le contrôleur d’affichage fractionné, substituez le `ViewDidLoad` (méthode) et attache les deux vues d’ensemble. Par exemple :

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

Vous pouvez exposer des propriétés et des méthodes sur votre contrôleur d’affichage de détail que le maître peut utiliser pour présenter les nouvelles données en fonction des besoins.

<a name="Showing-and-Hiding-Master" />

### <a name="showing-and-hiding-master"></a>Affichage et masquage de Master

Si vous le souhaitez, vous pouvez afficher et masquer le contrôleur d’affichage maître à l’aide de la `PreferredDisplayMode` propriété du contrôleur d’affichage fractionné. Par exemple :

```csharp
// Show hide split view
if (SplitViewController.DisplayMode == UISplitViewControllerDisplayMode.PrimaryHidden) {
    SplitViewController.PreferredDisplayMode = UISplitViewControllerDisplayMode.AllVisible;
} else {
    SplitViewController.PreferredDisplayMode = UISplitViewControllerDisplayMode.PrimaryHidden;
}
```

Le `UISplitViewControllerDisplayMode` enum définit comment le contrôleur d’affichage principale s’affiche comme l’une des opérations suivantes :

- **Automatique** -tvOS contrôlera la présentation des vues de détail et Master.
- **PrimaryHidden** -cela masque le contrôleur d’affichage maître.
- **AllVisible** -affiche le maître et le contrôleurs d’affichage Détails côte à côte. Il s’agit de la normale, présentation par défaut.
- **PrimaryOverlay** -étend sous le contrôleur d’affichage Détails et est couvert par le maître.

Pour obtenir l’état actuel de la présentation, utilisez la `DisplayMode` propriété du contrôleur d’affichage fractionné.

<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Cet article a présenté la conception et l’utilisation des contrôleurs d’affichage fractionné à l’intérieur d’une application Xamarin.tvOS.



## <a name="related-links"></a>Liens associés

- [Exemples tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS Human Interface Guides](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guide de programmation d’application pour tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
