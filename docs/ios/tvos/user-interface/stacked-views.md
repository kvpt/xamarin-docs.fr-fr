---
title: Utilisation des vues empilées tvOS dans Xamarin
description: Ce document décrit comment utiliser les vues empilées tvOS dans une application générée avec Xamarin. Il fournit une vue d’ensemble des vues empilées et décrit la disposition automatique, le positionnement et le dimensionnement d’une vue empilée, les utilisations courantes, l’intégration avec les storyboards, et bien plus encore.
ms.prod: xamarin
ms.assetid: 00B07F85-F30B-4DD4-8664-A61D0A1CDB0E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 321ea87bfa4f460c4eaab31ef2ede6f3eb1b927f
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84573571"
---
# <a name="working-with-tvos-stacked-views-in-xamarin"></a>Utilisation des vues empilées tvOS dans Xamarin

Le contrôle d’affichage de la pile ( `UIStackView` ) tire parti de la puissance des classes de mise en page automatique et de taille pour gérer une pile de sous-vues, horizontale ou verticale, qui répond dynamiquement aux modifications de contenu et à la taille de l’écran du périphérique TV Apple.

La disposition de toutes les sous-vues attachées à un affichage de la pile est gérée par celle-ci en fonction des propriétés définies par le développeur, telles que l’axe, la distribution, l’alignement et l’espacement :

[![](stacked-views-images/stacked01.png "Subview layout diagram")](stacked-views-images/stacked01.png#lightbox)

Lors de l’utilisation `UIStackView` d’un dans une application Xamarin. tvOS, le développeur peut définir les sous-vues soit à l’intérieur d’un Storyboard dans le concepteur iOS, soit en ajoutant et supprimant des sous-vues dans le code C#.

## <a name="about-stacked-view-controls"></a>À propos des contrôles d’affichage empilé

Le `UIStackView` est conçu comme une vue de conteneur sans rendu et, par conséquent, il n’est pas dessiné dans le canevas comme les autres sous-classes de `UIView` . La définition de propriétés telles que `BackgroundColor` ou la substitution n' `DrawRect` a aucun effet visuel.

Il existe plusieurs propriétés qui contrôlent la façon dont un affichage de la pile réorganisera sa collection de sous-vues :

- **Axis** : détermine si l’affichage de la pile réorganise les sous-affichages **horizontalement** ou **verticalement**.
- **Alignment** : contrôle la manière dont les sous-affichages sont alignés dans l’affichage de la pile.
- **Distribution** : contrôle la manière dont les sous-vues sont dimensionnées dans l’affichage de la pile.
- **Espacement** : contrôle l’espace minimal entre chaque sous-vue de l’affichage de la pile.
- **Ligne de base relative** : si `true` , l’espacement vertical de chaque sous-vue est dérivé de la ligne de base.
- **Marges de disposition relatives** : place les sous-vues par rapport aux marges de disposition standard.

En général, vous allez utiliser une vue de la pile pour réorganiser un petit nombre de sous-affichages. Vous pouvez créer des interfaces utilisateur plus complexes en imbriquant une ou plusieurs vues de pile.

Vous pouvez affiner l’apparence des interfaces utilisateur en ajoutant des contraintes aux sous-affichages (par exemple, pour contrôler la hauteur ou la largeur). Toutefois, il convient de veiller à ne pas inclure de contraintes conflictuelles à celles introduites par l’affichage de la pile.

<a name="Auto-Layout-and-Size-Classes"></a>

## <a name="auto-layout-and-size-classes"></a>Disposition automatique et classes de taille

Quand une sous-vue est ajoutée à un affichage de la pile, sa disposition est entièrement contrôlée par cette vue de la pile à l’aide des classes de disposition et de taille automatiques pour positionner et dimensionner les vues organisées.

L’affichage de la pile _épingle_ le premier et le dernier sous-affichage de sa collection aux bords **supérieur** et **inférieur** pour les vues verticales de la pile ou les bords **gauche** et **droit** pour les vues de la pile horizontale. Si vous affectez `LayoutMarginsRelativeArrangement` à la propriété la valeur `true` , la vue épingle la sous-vue aux marges appropriées au lieu du bord.

L’affichage de la pile utilise la propriété de la sous-vue `IntrinsicContentSize` lors du calcul de la taille des sous-affichages le long du défini `Axis` (à l’exception de `FillEqually Distribution` ). Le `FillEqually Distribution` redimensionne toutes les sous-vues afin qu’elles aient la même taille, ce qui remplit l’affichage de la pile le long du `Axis` .

À l’exception de `Fill Alignment` , l’affichage de la pile utilise la propriété de la sous-vue `IntrinsicContentSize` pour calculer la taille de la vue perpendiculairement au donné `Axis` . Pour le `Fill Alignment` , toutes les sous-vues sont dimensionnées de sorte qu’elles remplissent la vue de la pile perpendiculairement au donné `Axis` .

<a name="Positioning-and-Sizing-the-Stack-View"></a>

## <a name="positioning-and-sizing-the-stack-view"></a>Positionnement et redimensionnement de l’affichage des piles

Tandis que l’affichage de la pile a un contrôle total sur la disposition d’une sous-vue (en fonction de propriétés telles que `Axis` et `Distribution` ), vous devez toujours positionner l’affichage de la pile ( `UIStackView` ) dans sa vue parente à l’aide des classes de disposition et de taille automatiques.

En général, cela signifie épingler au moins deux bords de l’affichage de la pile pour développer et contracter, définissant ainsi sa position. Sans contraintes supplémentaires, l’affichage de la pile sera automatiquement redimensionné pour s’adapter à toutes ses sous-vues, comme suit :

- La taille le long `Axis` de son correspond à la somme de toutes les tailles de sous-affichages, ainsi qu’à l’espace qui a été défini entre chaque sous-vue.
- Si la `LayoutMarginsRelativeArrangement` propriété a la valeur `true` , la taille des vues de la pile inclut également l’espace pour les marges.
- La taille perpendiculaire à `Axis` sera définie sur la plus grande sous-vue de la collection.

En outre, vous pouvez spécifier des contraintes pour la **hauteur** et la **largeur**de la vue de la pile. Dans ce cas, les sous-affichages sont disposés (dimensionnés) pour remplir l’espace spécifié par l’affichage de la pile comme déterminé par les `Distribution` `Alignment` Propriétés et.

Si la `BaselineRelativeArrangement` propriété est `true` , les sous-affichages sont disposés en fonction de la première ou de la dernière ligne de base du sous-affichage, au lieu d’utiliser la position **supérieure**, **inférieure** ou*centrale* -  **Y** . Celles-ci sont calculées sur le contenu de la vue de la pile comme suit :

- Une vue verticale de la pile retourne la première sous-vue pour la première ligne de base et la dernière pour la dernière. Si l’une de ces sous-vues est elle-même des vues de pile, la première ou la dernière ligne de base sera utilisée.
- Une vue de pile horizontale utilise sa sous-vue la plus grande pour la première et la dernière ligne de base. Si la vue la plus grande est également un affichage de la pile, elle utilise la sous-vue la plus haute comme ligne de base.

> [!IMPORTANT]
> L’alignement de la ligne de base ne fonctionne pas sur les tailles de sous-affichage étirées ou compressées, car la ligne de base est calculée à la mauvaise position. Pour l’alignement de ligne de base, vérifiez que la **hauteur** de la sous-vue correspond à la **hauteur**de l’affichage de contenu intrinsèque.

<a name="Common-Stack-View-Uses"></a>

## <a name="common-stack-view-uses"></a>Utilisation des vues de pile courantes

Il existe plusieurs types de disposition qui fonctionnent bien avec les contrôles d’affichage de pile. D’après Apple, voici quelques-unes des utilisations les plus courantes :

- **Définir la taille le long de l’axe** : en épinglant les bords le long de la vue de la pile `Axis` et l’un des bords adjacents pour définir la position, l’affichage de la pile s’agrandit le long de l’axe pour s’ajuster à l’espace défini par ses sous-vues.
- **Définir la position de la** sous-vue : en épinglant les bords adjacents de l’affichage de la pile à sa vue parente, l’affichage de la pile augmente dans les deux dimensions pour s’adapter aux sous-vues.
- **Définissez la taille et la position de la pile** , en épinglant les quatre bords de l’affichage de la pile à la vue parent, l’affichage de la pile organise les sous-vues en fonction de l’espace défini dans l’affichage de la pile.
- **Définissez la taille perpendiculairement de l’axe** : en épinglant les deux bords perpendiculairement à l’affichage de la pile `Axis` et l’un des bords le long de l’axe pour définir la position, l’affichage de la pile va croître perpendiculairement à l’axe pour s’ajuster à l’espace défini par ses sous-vues.

<a name="Stack-Views-and-Storyboards"></a>

## <a name="stack-views-and-storyboards"></a>Vues et storyboards de la pile

Le moyen le plus simple d’utiliser les vues de pile dans une application Xamarin. tvOS consiste à les ajouter à l’interface utilisateur de l’application à l’aide du concepteur iOS.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

1. Dans la **panneau solutions**, double-cliquez sur le `Main.storyboard` fichier et ouvrez-le pour le modifier.
1. Concevez la disposition de vos éléments individuels que vous allez ajouter à l’affichage de la pile :

    [![](stacked-views-images/layout01.png "Element layout example")](stacked-views-images/layout01.png#lightbox)
1. Ajoutez toutes les contraintes requises aux éléments pour garantir leur mise à l’échelle correctement. Cette étape est importante une fois que l’élément est ajouté à l’affichage de la pile.
1. Effectuez le nombre requis de copies (quatre dans ce cas) :

    [![](stacked-views-images/layout02.png "The required number of copies")](stacked-views-images/layout02.png#lightbox)
1. Faites glisser une vue de la **pile** de la **boîte à outils** et déposez-la sur la vue :

    [![](stacked-views-images/layout03.png "A Stack View")](stacked-views-images/layout03.png#lightbox)
1. Sélectionnez l’affichage des piles, sous l' **onglet widget** du **panneau Propriétés** sélectionnez **remplissage** pour l' **alignement**, **remplissez la même** position pour la **distribution** et entrez `25` pour l' **espacement**:

    [![](stacked-views-images/layout04.png "The Widget Tab")](stacked-views-images/layout04.png#lightbox)
1. Placez l’affichage de la pile à l’écran où vous le souhaitez et ajoutez des contraintes pour le conserver à l’emplacement requis.
1. Sélectionnez les éléments individuels et faites-les glisser vers l’affichage de la pile :

    [![](stacked-views-images/layout05.png "The individual elements in the Stack View")](stacked-views-images/layout05.png#lightbox)
1. La disposition est ajustée et les éléments sont disposés en vue pile en fonction des attributs que vous avez définis ci-dessus.
1. Affectez des **noms** sous l' **onglet widget** de l' **Explorateur de propriétés** pour travailler avec vos contrôles d’interface utilisateur dans du code C#.
1. Enregistrez vos modifications.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Dans la **Explorateur de solutions**, double-cliquez sur le `Main.storyboard` fichier et ouvrez-le pour le modifier.
1. Concevez la disposition de vos éléments individuels que vous allez ajouter à l’affichage de la pile :

    [![](stacked-views-images/layout01.png "Example element layout")](stacked-views-images/layout01.png#lightbox)
1. Ajoutez toutes les contraintes requises aux éléments pour garantir leur mise à l’échelle correctement. Cette étape est importante une fois que l’élément est ajouté à l’affichage de la pile.
1. Effectuez le nombre requis de copies (quatre dans ce cas) :

    [![](stacked-views-images/layout02.png "The required number of copies")](stacked-views-images/layout02.png#lightbox)
1. Faites glisser une vue de la **pile** de la **boîte à outils** et déposez-la sur la vue :

    [![](stacked-views-images/layout03-vs.png "A Stack View")](stacked-views-images/layout03-vs.png#lightbox)
1. Sélectionnez l’affichage des piles, puis dans l' **onglet widget** de l' **Explorateur de propriétés** , sélectionnez **remplissage** pour l' **alignement**, **remplissez la même** **distribution** et entrez `25` pour l' **espacement**:

    [![](stacked-views-images/layout04-vs.png "The Widget Tab")](stacked-views-images/layout04-vs.png#lightbox)
1. Placez l’affichage de la pile à l’écran où vous le souhaitez et ajoutez des contraintes pour le conserver à l’emplacement requis.
1. Sélectionnez les éléments individuels et faites-les glisser vers l’affichage de la pile :

    [![](stacked-views-images/layout05-vs.png "The individual elements in the Stack View")](stacked-views-images/layout05-vs.png#lightbox)
1. La disposition est ajustée et les éléments sont disposés en vue pile en fonction des attributs que vous avez définis ci-dessus.
1. Affectez des **noms** sous l' **onglet widget** de l' **Explorateur de propriétés** pour travailler avec vos contrôles d’interface utilisateur dans du code C#.
1. Enregistrez vos modifications.

-----

> [!IMPORTANT]
> Bien qu’il soit possible d’assigner des actions telles que `TouchUpInside` à un élément d’interface utilisateur (par exemple, `UIButton` ) dans le concepteur iOS lors de la création d’un gestionnaire d’événements, il ne sera jamais appelé, car Apple TV ne dispose pas d’un écran tactile ni d’événements tactiles. Vous devez toujours utiliser la valeur par défaut `Action Type` lors de la création d’actions pour les éléments de l’interface utilisateur tvOS.

Pour plus d’informations sur l’utilisation des storyboards, consultez notre [Guide de démarrage rapide Hello, tvOS](~/ios/tvos/get-started/hello-tvos.md).

Dans le cas de notre exemple, nous avons exposé une sortie et une action pour le contrôle de segment et une sortie pour chaque « carte de joueur ». Dans le code, nous masquons et affichons Player en fonction du segment actuel. Par exemple :

```csharp
partial void PlayerCountChanged (Foundation.NSObject sender) {

    // Take Action based on the segment
    switch(PlayerCount.SelectedSegment) {
    case 0:
        Player1.Hidden = false;
        Player2.Hidden = true;
        Player3.Hidden = true;
        Player4.Hidden = true;
        break;
    case 1:
        Player1.Hidden = false;
        Player2.Hidden = false;
        Player3.Hidden = true;
        Player4.Hidden = true;
        break;
    case 2:
        Player1.Hidden = false;
        Player2.Hidden = false;
        Player3.Hidden = false;
        Player4.Hidden = true;
        break;
    case 3:
        Player1.Hidden = false;
        Player2.Hidden = false;
        Player3.Hidden = false;
        Player4.Hidden = false;
        break;
    }
}
```

Lorsque l’application est exécutée, les quatre éléments sont répartis de manière égale dans notre vue de la pile :

[![](stacked-views-images/layout06.png "When the app is run, the four elements will equally be distributed in our Stack View")](stacked-views-images/layout06.png#lightbox)

Si le nombre de joueurs est réduit, les vues inutilisées sont masquées et l’affichage de la pile ajuste la disposition pour s’ajuster :

[![](stacked-views-images/layout07.png "If the number of players is decreased, the unused views are hidden and the Stack View adjust the layout to fit")](stacked-views-images/layout07.png#lightbox)

<a name="Populate-a-Stack-View-from-Code"></a>

### <a name="populate-a-stack-view-from-code"></a>Remplir un affichage de la pile à partir du code

Outre la définition totale du contenu et de la disposition d’un affichage de la pile dans le concepteur iOS, vous pouvez le créer et le supprimer dynamiquement du code C#.

Prenons l’exemple suivant qui utilise une vue de la pile pour gérer les « étoiles » dans une révision (1 à 5) :

```csharp
public int Rating { get; set;} = 0;
...

partial void IncreaseRating (Foundation.NSObject sender) {

    // Maximum of 5 "stars"
    if (++Rating > 5 ) {
        // Abort
        Rating = 5;
        return;
    }

    // Create new rating icon and add it to stack
    var icon = new UIImageView (new UIImage("icon.png"));
    icon.ContentMode = UIViewContentMode.ScaleAspectFit;
    RatingView.AddArrangedSubview(icon);

    // Animate stack
    UIView.Animate(0.25, ()=>{
        // Adjust stack view
        RatingView.LayoutIfNeeded();
    });

}

partial void DecreaseRating (Foundation.NSObject sender) {

    // Minimum of zero "stars"
    if (--Rating < 0) {
        // Abort
        Rating =0;
        return;
    }

    // Get the last subview added
    var icon = RatingView.ArrangedSubviews[RatingView.ArrangedSubviews.Length-1];

    // Remove from stack and screen
    RatingView.RemoveArrangedSubview(icon);
    icon.RemoveFromSuperview();

    // Animate stack
    UIView.Animate(0.25, ()=>{
        // Adjust stack view
        RatingView.LayoutIfNeeded();
    });
}
```

Jetons un coup d’œil à quelques éléments de ce code en détail. Tout d’abord, nous utilisons une `if` instruction pour vérifier qu’il n’y a pas plus de cinq « étoiles » ou moins de zéro.

Pour ajouter une nouvelle « étoile », nous chargeons son image et définissons son **mode de contenu** sur **ajuster l’aspect**:

```csharp
var icon = new UIImageView (new UIImage("icon.png"));
icon.ContentMode = UIViewContentMode.ScaleAspectFit;
```

Cela empêche la déformation de l’icône « étoile » lorsqu’elle est ajoutée à l’affichage de la pile.

Ensuite, nous ajoutons la nouvelle icône « Star » à la collection d’affichages de la vue de la pile :

```csharp
RatingView.AddArrangedSubview(icon);
```

Vous remarquerez que nous avons ajouté `UIImageView` à `UIStackView` la `ArrangedSubviews` propriété de et non à `SubView` . Toute vue dont vous souhaitez que l’affichage de la pile contrôle sa disposition doit être ajoutée à la `ArrangedSubviews` propriété.

Pour supprimer une sous-vue d’une vue de la pile, nous obtenons d’abord la sous-vue à supprimer :

```csharp
var icon = RatingView.ArrangedSubviews[RatingView.ArrangedSubviews.Length-1];
```

Ensuite, nous devons la supprimer de la `ArrangedSubviews` collection et de la vue super :

```csharp
// Remove from stack and screen
RatingView.RemoveArrangedSubview(icon);
icon.RemoveFromSuperview();
```

La suppression d’une sous-vue à partir de la collection uniquement la `ArrangedSubviews` fait sortir du contrôle de l’affichage de la pile, mais ne la supprime pas de l’écran.

<a name="Dynamically-Changing-Content"></a>

## <a name="dynamically-changing-content"></a>Modification dynamique du contenu

Un affichage de la pile ajuste automatiquement la disposition des sous-vues chaque fois qu’une sous-vue est ajoutée, supprimée ou masquée. La disposition est également ajustée si une propriété de l’affichage de la pile est ajustée (telle que son `Axis` ).

Les modifications de disposition peuvent être animées en les plaçant dans un bloc d’animation, par exemple :

```csharp
// Animate stack
UIView.Animate(0.25, ()=>{
    // Adjust stack view
    RatingView.LayoutIfNeeded();
});
```

La plupart des propriétés de la vue de la pile peuvent être spécifiées à l’aide de classes de taille dans une table de montage séquentiel. Ces propriétés sont automatiquement animées pour répondre à des modifications de taille ou d’orientation.

<a name="Summary"></a>

## <a name="summary"></a>Résumé

Cet article a abordé la conception et l’utilisation de la vue empilée dans une application Xamarin. tvOS.

## <a name="related-links"></a>Liens connexes

- [Exemples tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guides de l’interface utilisateur tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guide de programmation d’applications pour tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
