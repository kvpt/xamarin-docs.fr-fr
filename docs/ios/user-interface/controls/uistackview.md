---
title: Vues de pile dans Xamarin.iOS
description: Cet article traite du nouveau contrôle UIStackView dans une application Xamarin.iOS à gérer un ensemble de sous-vues dans soit une pile organisée horizontalement ou verticalement.
ms.prod: xamarin
ms.assetid: 20246E87-2A49-438A-9BD7-756A1B50A617
ms.technology: xamarin-ios
ms.custom: xamu-video
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 88496452595f308c97d26d0f27fae305baef894f
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830824"
---
# <a name="stack-views-in-xamarinios"></a>Vues de pile dans Xamarin.iOS

_Cet article traite du nouveau contrôle UIStackView dans une application Xamarin.iOS à gérer un ensemble de sous-vues dans soit une pile organisée horizontalement ou verticalement._

> [!IMPORTANT]
> Veuillez noter que StackView est pris en charge dans le concepteur iOS, vous pouvez rencontrer des bogues de facilité d’utilisation lorsque vous utilisez le canal Stable. Le changement de la version bêta ou Alpha canal doit permettre de résoudre ce problème. Nous avons décidé de présenter cette procédure pas à pas à l’aide de Xcode jusqu'à ce que les correctifs requis sont implémentées dans le canal Stable.

Le contrôle de la vue de la pile (`UIStackView`) tire parti de la puissance de disposition automatique et des Classes de taille pour gérer une pile de sous-vues, horizontalement ou verticalement, qui répond dynamiquement à la taille d’écran et l’orientation de l’appareil iOS.

La disposition de tous les sous-vues attachée à une vue de la pile sont gérées par en fonction des propriétés de développeur défini comme axe, distribution, alignement et espacement :

[![](uistackview-images/stacked01.png "Diagramme de disposition de vue de pile")](uistackview-images/stacked01.png#lightbox)

Lorsque vous utilisez un `UIStackView` dans une application Xamarin.iOS, le développeur peut définir soit les sous-vues soit à l’intérieur d’une table de montage séquentiel dans le concepteur iOS, ou en ajoutant et supprimant des sous-vues en code c#.

Ce document se compose de deux parties : un démarrage rapide pour vous aider à mettre en œuvre afficher votre pile et puis certains plus de détails techniques sur son fonctionnement.

> [!VIDEO https://youtube.com/embed/p3po6507Ip8]

**UIStackView vidéo**

## <a name="uistackview-quickstart"></a>Guide de démarrage rapide UIStackView

En tant qu’une introduction rapide à la `UIStackView` contrôle, nous allons créer une interface simple qui autorise l’utilisateur à entrer une évaluation à partir de 1 à 5. Nous allons utiliser deux vues de la pile : une pour organiser l’interface verticalement sur l’appareil écran et l’autre pour réorganiser les icônes d’évaluation de 1 à 5 horizontalement à l’écran.

### <a name="define-the-ui"></a>Définir l’interface utilisateur

Démarrer un nouveau projet Xamarin.iOS et modifier le **Main.storyboard** fichier dans Xcode Interface Builder. Tout d’abord, faites glisser un seul **verticale vue pile** sur le **contrôleur d’affichage**:

[![](uistackview-images/quick01.png "Faites glisser une seule vue pile verticale sur le contrôleur d’affichage")](uistackview-images/quick01.png#lightbox)

Dans le **inspecteur d’attributs**, définissez les options suivantes :

[![](uistackview-images/quick02.png "Définir les options de vue de la pile")](uistackview-images/quick02.png#lightbox)

Où :

- **Axe** – détermine si la vue pile organise les sous-vues soit **horizontalement** ou **verticalement**.
- **Alignement** – contrôle la manière dont les sous-affichages sont alignées dans la vue de la pile.
- **Distribution** – contrôle la façon dont les sous-affichages sont dimensionnés au sein de la vue de la pile.
- **Espacement** – contrôle l’espace minimal entre chaque sous-affichage dans la vue de la pile.
- **Ligne de base relatif** – si elle est activée, l’espacement vertical de chaque sous-affichage sera dérivé de sa ligne de base.
- **Mise en page marges relatif** – place les sous-vues relatif aux marges mise en forme standard.

Lorsque vous travaillez avec une vue de la pile, vous pouvez considérer le **alignement** en tant que le **X** et **Y** emplacement de la sous-affichage et **Distribution** comme le **Hauteur** et **largeur**.

> [!IMPORTANT]
> `UIStackView` est conçu comme une vue non rendu conteneur et par conséquent, il n’est pas dessiné dans le canevas, comme d’autres sous-classes de `UIView`. Par conséquent, définissant les propriétés telles que `BackgroundColor` ou la substitution `DrawRect` n’a aucun effet visuel.

Continuer à disposition de l’interface l’application en ajoutant une étiquette, ImageView, deux boutons et un affichage Horizontal de la pile afin qu’il ressemble à ce qui suit :

[![](uistackview-images/quick03.png "Mise en page de l’interface utilisateur de vue pile")](uistackview-images/quick03.png#lightbox)

Configurer l’affichage Horizontal de la pile avec les options suivantes :

[![](uistackview-images/quick04.png "Configurez les options de vue de la pile horizontale")](uistackview-images/quick04.png#lightbox)

Étant donné que nous ne voulons pas l’icône qui représente chaque « point » dans l’évaluation étiré lorsqu’il est ajouté à la vue pile horizontale, nous avons défini le **alignement** à **Center** et  **Distribution** à **remplir tout aussi**.

Enfin, raccordées à ce qui suit **Outlets** et **Actions**:

[![](uistackview-images/quick05.png "Les points de vue pile et les Actions")](uistackview-images/quick05.png#lightbox)

### <a name="populate-a-uistackview-from-code"></a>Remplir un UIStackView à partir du Code

Retournez dans Visual Studio pour Mac et modifier le **ViewController.cs** fichier, puis ajoutez le code suivant :

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

Jetons un œil à quelques éléments de ce code en détail. Tout d’abord, nous utilisons un `if` instructions pour vérifier qu’il n’est pas plus de cinq « étoiles » ou inférieur à zéro.

Pour ajouter un nouveau « star » nous charger son image et définir son **Mode contenu** à **mise à l’échelle Aspect ajuster**:

```csharp
var icon = new UIImageView (new UIImage("icon.png"));
icon.ContentMode = UIViewContentMode.ScaleAspectFit;
```

Cela permet de conserver l’icône « en étoile » de la distorsion lorsqu’il est ajouté à la vue de la pile.

Ensuite, nous ajoutons la nouvelle icône « en étoile » à la collection de la vue de la pile de sous-vues :

```csharp
RatingView.AddArrangedSubview(icon);
```

Vous remarquerez que nous avons ajouté la `UIImageView` à la `UIStackView`de `ArrangedSubviews` propriété et non le `SubView`. N’importe quelle vue que vous souhaitez que l’affichage de pile pour contrôler sa disposition doit être ajouté à la `ArrangedSubviews` propriété.

Pour supprimer un sous-affichage à partir d’une vue de la pile, nous obtenons d’abord le sous-affichage à supprimer :

```csharp
var icon = RatingView.ArrangedSubviews[RatingView.ArrangedSubviews.Length-1];
```

Puis nous avons besoin pour le supprimer à la fois le `ArrangedSubviews` collection et la vue de Super :

```csharp
// Remove from stack and screen
RatingView.RemoveArrangedSubview(icon);
icon.RemoveFromSuperview();
```

Supprime un sous-affichage uniquement le `ArrangedSubviews` collection le supprime du contrôle de la vue de la pile, mais ne le supprime pas de l’écran.

### <a name="testing-the-ui"></a>Testez l’interface utilisateur

Avec tous les éléments d’interface utilisateur et requis code en place, vous pouvez maintenant exécuter et tester l’interface. Lorsque l’interface utilisateur s’affiche, tous les éléments dans la vue pile verticale seront régulièrement espacés de haut en bas.

Lorsque l’utilisateur appuie sur le **augmenter la note** bouton, un autre « star » est ajoutée à l’écran (jusqu'à un maximum de 5) :

[![](uistackview-images/intro01.png "L’exécution de l’application exemple")](uistackview-images/intro01.png#lightbox)

Les « étoiles » seront automatiquement centrées et répartis dans l’affichage Horizontal de la pile. Lorsque l’utilisateur appuie sur le **baisser la note** bouton, un « star » est supprimé (jusqu'à ce qu’aucun sont à gauche).

## <a name="stack-view-details"></a>Afficher les détails de pile

Maintenant que nous avons une idée générale de ce à quoi le `UIStackView` contrôle est et comment il fonctionne, jetons un œil plus approfondie de certaines de ses fonctionnalités et les détails.

### <a name="auto-layout-and-size-classes"></a>Classes de taille et de disposition automatique

Comme nous l’avons vu, lorsqu’un sous-affichage est ajouté à une vue de la pile sa disposition est entièrement contrôlé par cette vue de la pile à l’aide des Classes de taille et de disposition automatique pour positionner et dimensionner les vues réorganisées.

La vue de la pile sera _code confidentiel_ le premier et dernier sous-affichage dans sa collection pour le **haut** et **bas** bords pour les vues de pile verticale ou **gauche**et **droite** bords pour les vues de pile horizontale. Si vous définissez la `LayoutMarginsRelativeArrangement` propriété `true`, puis la vue épingle les sous-affichages entre les marges pertinentes au lieu de la périphérie.

La vue pile utilise le sous-affichage `IntrinsicContentSize` propriété lors du calcul de la taille de sous-vues le long de l’élément défini `Axis` (à l’exception de la `FillEqually Distribution`). Le `FillEqually Distribution` redimensionne tous les sous-vues afin qu’ils soient de la même taille, donc remplissant la vue pile le long de la `Axis`.

À l’exception de la `Fill Alignment`, la vue pile utilise le sous-affichage `IntrinsicContentSize` propriété pour le calcul de taille de la vue perpendiculaire à la donnée `Axis`. Pour le `Fill Alignment`, tous les sous-affichages sont dimensionnés pour qu’elles remplissent la vue pile perpendiculaire à la donnée `Axis`.

### <a name="positioning-and-sizing-the-stack-view"></a>Positionnement et dimensionnement de la vue de la pile

Alors que la vue de la pile a un contrôle total sur la disposition de n’importe quel sous-affichage (selon les propriétés telles que `Axis` et `Distribution`), vous devez toujours positionner la vue de la pile (`UIStackView`) au sein de la vue parent à l’aide des Classes de taille et de disposition automatique.

En règle générale, cela signifie que l’épinglage au moins deux bords de l’affichage de la pile pour être accrus ou réduits, définissant ainsi sa position. Sans contraintes supplémentaires, la vue de la pile sont automatiquement redimensionnée pour s’ajuster à tous ses sous-vues comme suit :

- La taille le long de son `Axis` sera la somme de toutes les tailles de sous-affichage et n’importe quel espace qui a été défini entre chaque sous-affichage.
- Si le `LayoutMarginsRelativeArrangement` propriété est `true`, la taille de pile vues inclut également de place pour les marges.
- La taille perpendiculaire à la `Axis` a la valeur la plus grande sous-affichage dans la collection.

En outre, vous pouvez spécifier des contraintes pour la vue pile **hauteur** et **largeur**. Dans ce cas, les sous-affichages sont disposées (taille) pour remplir l’espace spécifié par la vue pile, comme déterminé par le `Distribution` et `Alignment` propriétés.

Si le `BaselineRelativeArrangement` propriété est `true`, les sous-affichages sont disposées en fonction de la ligne de base du sous-affichage premier ou dernier, au lieu d’utiliser le **haut**, **bas** ou **Center** -  **Y** position. Ceux-ci sont calculées sur le contenu de la vue pile comme suit :

- Une vue de la pile verticale renvoie le premier sous-affichage pour la première ligne de base et le dernier pour la dernière. Si une de ces sous-vues sont eux-mêmes des vues de la pile, puis leur ligne de base du premier ou dernier servira.
- Un affichage Horizontal de la pile utilisera sa plus grand sous-affichage pour les deux la première et la dernière ligne de base. Si la vue la plus haute est également une vue de la pile, il utilisera sa plus grand sous-affichage en tant que la ligne de base.

> [!IMPORTANT]
> Alignement de ligne de base ne fonctionne pas sur les tailles de sous-affichage étirée ou compressée que la ligne de base correspond à une position incorrecte. Pour l’alignement de ligne de base, vérifiez que le sous-affichage **hauteur** correspond à la vue contenu intrinsèque **hauteur**.

### <a name="common-stack-view-uses"></a>Utilisations courantes de vue de pile

Il existe plusieurs types de disposition qui fonctionnent correctement avec les contrôles d’affichage de la pile. En fonction d’Apple, voici quelques-unes des utilisations plus courantes :

- **Définir la taille le long de l’axe** – en épinglant les deux bords le long de la vue pile `Axis` et les bords adjacents pour définir la position, la pile vue augmentera le long de l’axe en fonction de l’espace défini par ses sous-vues.
- **Définir Position du sous-affichage** – en épinglant à bords adjacents de la vue de la pile à la vue de son parent, la vue pile augmentera dans les deux dimensions en fonction de son conteneur sous-vues.
- **Définir la taille et la Position de la pile** – en épinglant toutes les quatre bords de la vue de la pile à la vue parent, la vue pile organise les sous-vues en fonction de l’espace défini dans la vue de la pile.
- **Définir la taille perpendiculaires l’axe** – en épinglant les deux perpendiculairement bords à la vue pile `Axis` et l’autre des bords sur l’axe pour définir la position, la pile vue augmentera perpendiculaire à l’axe en fonction de l’espace défini par ses sous-vues.

### <a name="managing-the-appearance"></a>La gestion de l’apparence

Le `UIStackView` est conçu comme une vue non rendu conteneur et par conséquent, il n’est pas dessiné dans le canevas, comme d’autres sous-classes de `UIView`. Définition des propriétés telles que `BackgroundColor` ou la substitution `DrawRect` n’a aucun effet visuel.

Il existe plusieurs propriétés qui contrôlent la façon dont une vue de la pile réorganiser sa collection de sous-vues :

- **Axe** – détermine si la vue pile organise les sous-vues soit **horizontalement** ou **verticalement**.
- **Alignement** – contrôle la manière dont les sous-affichages sont alignées dans la vue de la pile.
- **Distribution** – contrôle la façon dont les sous-affichages sont dimensionnés au sein de la vue de la pile.
- **Espacement** – contrôle l’espace minimal entre chaque sous-affichage dans la vue de la pile.
- **Ligne de base relatif** : si `true`, l’espacement vertical de chaque sous-affichage sera dérivé de sa ligne de base.
- **Mise en page marges relatif** – place les sous-vues relatif aux marges mise en forme standard.

En règle générale, vous allez utiliser une vue de la pile pour organiser un petit nombre de sous-affichages. Les Interfaces utilisateur plus complexes peuvent être créés par l’imbrication d’une ou plusieurs vues de pile à l’intérieur de l’autre (comme nous l’avons fait le [UIStackView Quickstart](#uistackview-quickstart) ci-dessus).

Vous pouvez affiner l’apparence des interfaces utilisateur en ajoutant des contraintes supplémentaires pour les sous-affichages (par exemple pour le contrôle de la largeur ou hauteur). Toutefois, il convient ne pas à inclure les contraintes en conflit à celles introduites par la vue pile lui-même.

### <a name="maintaining-arranged-views-and-sub-views-consistency"></a>Maintenance organisés vues et cohérence de vues Sub

La vue de la pile permet de garantir que ses `ArrangedSubviews` propriété est toujours un sous-ensemble de ses `Subviews` propriété utilisant les règles suivantes :

- Si un sous-affichage est ajouté à la `ArrangedSubviews` collection, il est automatiquement ajoutée à la `Subviews` collection (sauf si elle fait déjà partie de cette collection).
- Si un sous-affichage est supprimé de la `Subviews` collection (supprimés à partir de l’affichage), il est également supprimé de la `ArrangedSubviews` collection.
- Suppression d’un sous-affichage à partir de la `ArrangedSubviews` collection sans la supprimer à partir de la `Subviews` collection. Par conséquent, il sera n’est plus disposé par la vue de la pile, mais sera toujours visible sur l’écran.

Le `ArrangedSubviews` collection est toujours un sous-ensemble de la `Subview` collection, toutefois l’ordre des sous-vues individuels au sein de chaque collection est distinct et contrôlée par les éléments suivants :

- L’ordre des sous-vues au sein de la `ArrangedSubviews` ensemble déterminer leur ordre d’affichage dans la pile.
- L’ordre des sous-vues au sein de la `Subview` collection détermine leur ordre de plan (ou superposition) dans la vue arrière vers l’avant.

### <a name="dynamically-changing-content"></a>Modifier dynamiquement le contenu

Une vue de la pile s’ajuste automatiquement la disposition des sous-vues chaque fois qu’un sous-affichage est ajouté, supprimé ou masqué. La mise en page soient également ajustée si n’importe quelle propriété de la vue de la pile est ajustée (telles que son `Axis`).

Les changements de disposition peuvent être animés, en les plaçant dans un bloc d’Animation, par exemple :

```csharp
// Animate stack
UIView.Animate(0.25, ()=>{
    // Adjust stack view
    RatingView.LayoutIfNeeded();
});
```

La plupart des propriétés de la vue pile peuvent être spécifiés à l’aide des Classes de taille au sein d’une table de montage séquentiel. Ces propriétés seront automatiquement animé est la réponse aux modifications de taille ou orientation.

## <a name="summary"></a>Récapitulatif

Cet article a présenté la nouvelle `UIStackView` contrôler (pour iOS 9) pour gérer un ensemble de sous-vues dans une pile organisée horizontalement ou verticalement dans une application Xamarin.iOS.
Il a commencé avec un exemple simple d’utilisation des vues de la pile pour créer une interface utilisateur et s’est terminée avec une étude plus détaillée de vues de la pile et leurs propriétés et les fonctionnalités.



## <a name="related-links"></a>Liens associés

- [Exemples iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 pour les développeurs](https://developer.apple.com/ios/pre-release/)
- [Quelles sont les nouveautés dans iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Référence de UIStackView](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIStackView_Class_Reference/)
- [Présentation de UIStackView (vidéo)](https://university.xamarin.com/lightninglectures/introducing-uistackview-on-ios9)
