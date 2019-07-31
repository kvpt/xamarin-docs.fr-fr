---
title: Affichages de la pile dans Xamarin. iOS
description: Cet article traite de l’utilisation du nouveau contrôle UIStackView dans une application Xamarin. iOS pour gérer un ensemble de sous-affichages dans une pile horizontale ou verticale.
ms.prod: xamarin
ms.assetid: 20246E87-2A49-438A-9BD7-756A1B50A617
ms.technology: xamarin-ios
ms.custom: xamu-video
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 5519f6e5b1bb0b63ab3169dd8f48f2f87a025ba5
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642816"
---
# <a name="stack-views-in-xamarinios"></a>Affichages de la pile dans Xamarin. iOS

_Cet article traite de l’utilisation du nouveau contrôle UIStackView dans une application Xamarin. iOS pour gérer un ensemble de sous-affichages dans une pile horizontale ou verticale._

> [!IMPORTANT]
> Notez que si StackView est pris en charge dans le concepteur iOS, vous risquez de rencontrer des bogues d’utilisation lors de l’utilisation du canal stable. Le passage de la version bêta ou des canaux alpha devrait résoudre ce problème. Nous avons décidé de présenter cette procédure pas à pas à l’aide de Xcode jusqu’à ce que les correctifs requis soient implémentés dans le canal stable.

Le contrôle d’affichage de`UIStackView`la pile () tire parti de la puissance des classes de mise en page et de taille automatiques pour gérer une pile de sous-vues, horizontale ou verticale, qui répond de manière dynamique à l’orientation et à la taille de l’écran de l’appareil iOS.

La disposition de toutes les sous-vues attachées à un affichage de la pile est gérée par celle-ci en fonction des propriétés définies par le développeur, telles que l’axe, la distribution, l’alignement et l’espacement:

[![](uistackview-images/stacked01.png "Diagramme de disposition de la vue pile")](uistackview-images/stacked01.png#lightbox)

Lors de l' `UIStackView` utilisation d’un dans une application Xamarin. iOS, le développeur peut soit définir les sous-vues à l’intérieur d’un Storyboard dans le concepteur iOS, soit ajouter et supprimer C# des sous-vues dans le code.

Ce document se compose de deux parties: un guide de démarrage rapide pour vous aider à implémenter votre première vue de la pile, puis d’autres détails techniques sur son fonctionnement.

> [!VIDEO https://youtube.com/embed/p3po6507Ip8]

**Vidéo UIStackView**

## <a name="uistackview-quickstart"></a>Démarrage rapide de UIStackView

En guise d’introduction rapide `UIStackView` au contrôle, nous allons créer une interface simple qui permet à l’utilisateur d’entrer une évaluation de 1 à 5. Nous allons utiliser deux vues de pile: une pour réorganiser l’interface verticalement sur l’écran de l’appareil et une pour réorganiser horizontalement les icônes d’évaluation 1-5 sur l’écran.

### <a name="define-the-ui"></a>Définir l’interface utilisateur

Démarrez un nouveau projet Xamarin. iOS, puis modifiez le fichier **main. Storyboard** dans le Interface Builder de Xcode. Tout d’abord, faites glisser une seule **vue verticale** de la pile sur le **contrôleur d’affichage**:

[![](uistackview-images/quick01.png "Faites glisser une seule vue verticale de la pile sur le contrôleur d’affichage.")](uistackview-images/quick01.png#lightbox)

Dans l' **inspecteur d’attribut**, définissez les options suivantes:

[![](uistackview-images/quick02.png "Définir les options d’affichage de la pile")](uistackview-images/quick02.png#lightbox)

Où :

- **Axis** : détermine si l’affichage de la pile réorganise les sous- affichages horizontalement ou **verticalement**.
- **Alignment** : contrôle la manière dont les sous-affichages sont alignés dans l’affichage de la pile.
- **Distribution** : contrôle la manière dont les sous-vues sont dimensionnées dans l’affichage de la pile.
- **Espacement** : contrôle l’espace minimal entre chaque sous-vue de l’affichage de la pile.
- **Ligne de base relative** : si cette option est activée, l’espacement vertical de chaque sous-vue est dérivé de sa ligne de base.
- **Marges de disposition relatives** : place les sous-vues par rapport aux marges de disposition standard.

Lorsque vous utilisez un affichage de la pile, vous pouvez considérer l' **alignement** comme l’emplacement **X** et **Y** de la sous-vue et la **distribution** comme la **hauteur** et la **largeur**.

> [!IMPORTANT]
> `UIStackView`est conçu comme une vue de conteneur sans rendu et, par conséquent, il n’est pas dessiné dans le canevas comme les autres sous `UIView`-classes de. Par conséquent, la définition `BackgroundColor` de propriétés telles `DrawRect` que ou la substitution n’a aucun effet visuel.

Continuez à mettre en page l’interface de l’application en ajoutant une étiquette, ImageView, deux boutons et une vue de pile horizontale afin qu’elle ressemble à ce qui suit:

[![](uistackview-images/quick03.png "Disposition de l’interface utilisateur de la vue pile")](uistackview-images/quick03.png#lightbox)

Configurez l’affichage de la pile horizontale avec les options suivantes:

[![](uistackview-images/quick04.png "Configurer les options d’affichage horizontal de la pile")](uistackview-images/quick04.png#lightbox)

Étant donné que nous ne souhaitons pas que l’icône représentant chaque «point» de l’évaluation soit étirée lorsqu’elle est ajoutée à l’affichage de la pile horizontale, nous avons défini l' **alignement** sur **Center** et la **distribution** sur **Fill**.

Enfin, associez les **prises** et les **actions**suivantes:

[![](uistackview-images/quick05.png "Sorties et actions de l’affichage de la pile")](uistackview-images/quick05.png#lightbox)

### <a name="populate-a-uistackview-from-code"></a>Remplir un UIStackView à partir du code

Revenez à Visual Studio pour Mac et modifiez le fichier **ViewController.cs** et ajoutez le code suivant:

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

Jetons un coup d’œil à quelques éléments de ce code en détail. Tout d’abord, nous `if` utilisons une instruction pour vérifier qu’il n’y a pas plus de cinq «étoiles» ou moins de zéro.

Pour ajouter une nouvelle «étoile», nous chargeons son image et définissons son **mode de contenu** sur **ajuster l’aspect**:

```csharp
var icon = new UIImageView (new UIImage("icon.png"));
icon.ContentMode = UIViewContentMode.ScaleAspectFit;
```

Cela empêche la déformation de l’icône «étoile» lorsqu’elle est ajoutée à l’affichage de la pile.

Ensuite, nous ajoutons la nouvelle icône «Star» à la collection d’affichages de la vue de la pile:

```csharp
RatingView.AddArrangedSubview(icon);
```

Vous remarquerez que nous avons `UIImageView` ajouté à `UIStackView`la `ArrangedSubviews` propriété de et non à `SubView`. Toute vue dont vous souhaitez que l’affichage de la pile contrôle sa disposition doit être ajoutée `ArrangedSubviews` à la propriété.

Pour supprimer une sous-vue d’une vue de la pile, nous obtenons d’abord la sous-vue à supprimer:

```csharp
var icon = RatingView.ArrangedSubviews[RatingView.ArrangedSubviews.Length-1];
```

Ensuite, nous devons la supprimer de la `ArrangedSubviews` collection et de la vue super:

```csharp
// Remove from stack and screen
RatingView.RemoveArrangedSubview(icon);
icon.RemoveFromSuperview();
```

La suppression d’une sous-vue `ArrangedSubviews` à partir de la collection uniquement la fait sortir du contrôle de l’affichage de la pile, mais ne la supprime pas de l’écran.

### <a name="testing-the-ui"></a>Test de l’interface utilisateur

Avec tous les éléments d’interface utilisateur et le code nécessaires en place, vous pouvez maintenant exécuter et tester l’interface. Lorsque l’interface utilisateur est affichée, tous les éléments de l’affichage vertical de la pile sont espacés de haut en bas.

Quand l’utilisateur appuie sur le bouton **augmenter la notation** , une autre «étoile» est ajoutée à l’écran (5 au maximum):

[![](uistackview-images/intro01.png "L’exécution de l’application exemple")](uistackview-images/intro01.png#lightbox)

Les «étoiles» seront automatiquement centrées et distribuées de manière égale dans l’affichage de la pile horizontale. Quand l’utilisateur appuie sur le bouton **réduire l’évaluation** , une «étoile» est supprimée (jusqu’à ce qu’aucun élément ne soit laissé).

## <a name="stack-view-details"></a>Détails de l’affichage de la pile

Maintenant que nous avons une idée générale du `UIStackView` contrôle et de son fonctionnement, examinons plus en détail certaines de ses fonctionnalités et détails.

### <a name="auto-layout-and-size-classes"></a>Disposition automatique et classes de taille

Comme nous l’avons vu ci-dessus, quand une sous-vue est ajoutée à un affichage de la pile, sa disposition est entièrement contrôlée par cette vue de la pile à l’aide des classes de disposition et de taille automatiques pour positionner et dimensionner les vues organisées.

L’affichage de la pile épingle le premier et le dernier sous-affichage de sa collection aux bords **supérieur** et **inférieur** pour les vues verticales de la pile ou les bords **gauche** et **droit** pour les vues de la pile horizontale. Si vous affectez `LayoutMarginsRelativeArrangement` à `true`la propriété la valeur, la vue épingle la sous-vue aux marges appropriées au lieu du bord.

L’affichage de la pile utilise la propriété `IntrinsicContentSize` de la sous-vue lors du calcul de la taille `Axis` des sous-affichages le long du défini (à l’exception de `FillEqually Distribution`). Le `FillEqually Distribution` redimensionne toutes les sous-vues afin qu’elles aient la même taille, ce qui remplit l’affichage de `Axis`la pile le long du.

À l’exception de `Fill Alignment`, l’affichage de la pile utilise la `IntrinsicContentSize` propriété de la sous-vue pour calculer la taille de la vue `Axis`perpendiculairement au donné. Pour le `Fill Alignment`, toutes les sous-vues sont dimensionnées de sorte qu’elles remplissent la vue de `Axis`la pile perpendiculairement au donné.

### <a name="positioning-and-sizing-the-stack-view"></a>Positionnement et redimensionnement de l’affichage des piles

Tandis que l’affichage de la pile a un contrôle total sur la disposition d’une sous-vue `Axis` ( `Distribution`en fonction de propriétés telles que et), vous devez`UIStackView`toujours positionner l’affichage de la pile () dans sa vue parente à l’aide des classes de disposition et de taille automatiques.

En général, cela signifie épingler au moins deux bords de l’affichage de la pile pour développer et contracter, définissant ainsi sa position. Sans contraintes supplémentaires, l’affichage de la pile sera automatiquement redimensionné pour s’adapter à toutes ses sous-vues, comme suit:

- La taille le long `Axis` de son correspond à la somme de toutes les tailles de sous-affichages, ainsi qu’à l’espace qui a été défini entre chaque sous-vue.
- Si la `LayoutMarginsRelativeArrangement` propriété a `true`la valeur, la taille des vues de la pile inclut également l’espace pour les marges.
- La taille perpendiculaire à `Axis` sera définie sur la plus grande sous-vue de la collection.

En outre, vous pouvez spécifier des contraintes pour la **hauteur** et la **largeur**de la vue de la pile. Dans ce cas, les sous-affichages sont disposés (dimensionnés) pour remplir l’espace spécifié par l’affichage de la pile comme déterminé `Distribution` par `Alignment` les propriétés et.

Si la `BaselineRelativeArrangement` propriété est `true`, les sous-affichages sont disposés en fonction de la première ou de la dernière ligne de base de la sous-vue, au lieu d’utiliser la position **supérieure**, **inférieure** ou **centrale**- **Y** . Celles-ci sont calculées sur le contenu de la vue de la pile comme suit:

- Une vue verticale de la pile retourne la première sous-vue pour la première ligne de base et la dernière pour la dernière. Si l’une de ces sous-vues est elle-même des vues de pile, la première ou la dernière ligne de base sera utilisée.
- Une vue de pile horizontale utilise sa sous-vue la plus grande pour la première et la dernière ligne de base. Si la vue la plus grande est également un affichage de la pile, elle utilise la sous-vue la plus haute comme ligne de base.

> [!IMPORTANT]
> L’alignement de la ligne de base ne fonctionne pas sur les tailles de sous-affichage étirées ou compressées, car la ligne de base est calculée à la mauvaise position. Pour l’alignement de ligne de base, vérifiez que la **hauteur** de la sous-vue correspond à la **hauteur**de l’affichage de contenu intrinsèque.

### <a name="common-stack-view-uses"></a>Utilisation des vues de pile courantes

Il existe plusieurs types de disposition qui fonctionnent bien avec les contrôles d’affichage de pile. D’après Apple, voici quelques-unes des utilisations les plus courantes:

- **Définir la taille le long de l’axe** : en épinglant les bords le long `Axis` de la vue de la pile et l’un des bords adjacents pour définir la position, l’affichage de la pile s’agrandit le long de l’axe pour s’ajuster à l’espace défini par ses sous-vues.
- **Définir la position de la** sous-vue: en épinglant les bords adjacents de l’affichage de la pile à sa vue parente, l’affichage de la pile augmente dans les deux dimensions pour s’adapter aux sous-vues.
- **Définissez la taille et la position de la pile** , en épinglant les quatre bords de l’affichage de la pile à la vue parent, l’affichage de la pile organise les sous-vues en fonction de l’espace défini dans l’affichage de la pile.
- **Définissez la taille perpendiculairement de l’axe** : en épinglant les deux bords perpendiculairement `Axis` à l’affichage de la pile et l’un des bords le long de l’axe pour définir la position, l’affichage de la pile va croître perpendiculairement à l’axe pour s’ajuster à l’espace défini par ses sous-vues.

### <a name="managing-the-appearance"></a>Gestion de l’apparence

Le `UIStackView` est conçu comme une vue de conteneur sans rendu et, par conséquent, il n’est pas dessiné dans le canevas comme les autres sous `UIView`-classes de. La définition de propriétés `BackgroundColor` telles que ou `DrawRect` la substitution n’a aucun effet visuel.

Il existe plusieurs propriétés qui contrôlent la façon dont un affichage de la pile réorganisera sa collection de sous-vues:

- **Axis** : détermine si l’affichage de la pile réorganise les sous- affichages horizontalement ou **verticalement**.
- **Alignment** : contrôle la manière dont les sous-affichages sont alignés dans l’affichage de la pile.
- **Distribution** : contrôle la manière dont les sous-vues sont dimensionnées dans l’affichage de la pile.
- **Espacement** : contrôle l’espace minimal entre chaque sous-vue de l’affichage de la pile.
- **Ligne de base relative** : si `true`, l’espacement vertical de chaque sous-vue est dérivé de la ligne de base.
- **Marges de disposition relatives** : place les sous-vues par rapport aux marges de disposition standard.

En général, vous allez utiliser une vue de la pile pour réorganiser un petit nombre de sous-affichages. Il est possible de créer des interfaces utilisateur plus complexes en imbriquant une ou plusieurs vues de pile les unes dans les autres (comme nous l’avons fait dans le [démarrage rapide UIStackView](#uistackview-quickstart) ci-dessus).

Vous pouvez affiner l’apparence des interfaces utilisateur en ajoutant des contraintes aux sous-affichages (par exemple, pour contrôler la hauteur ou la largeur). Toutefois, il convient de veiller à ne pas inclure de contraintes conflictuelles à celles introduites par l’affichage de la pile.

### <a name="maintaining-arranged-views-and-sub-views-consistency"></a>Maintien de la cohérence des vues et des sous-vues organisées

L’affichage de la pile permet de `ArrangedSubviews` s’assurer que sa propriété est toujours `Subviews` un sous-ensemble de sa propriété à l’aide des règles suivantes:

- Si une sous-vue est ajoutée à `ArrangedSubviews` la collection, elle est automatiquement ajoutée à la `Subviews` collection (sauf si elle fait déjà partie de cette collection).
- Si une sous-vue est supprimée `Subviews` de la collection (supprimée de l’affichage), elle est `ArrangedSubviews` également supprimée de la collection.
- La suppression d’une sous- `ArrangedSubviews` vue de la collection ne la supprime `Subviews` pas de la collection. Elle n’est donc plus présentée par l’affichage de la pile, mais reste visible à l’écran.

La `ArrangedSubviews` collection est toujours un sous-ensemble `Subview` de la collection, mais l’ordre des sous-vues individuelles au sein de chaque collection est séparé et contrôlé par les éléments suivants:

- L’ordre des sous-vues au sein de `ArrangedSubviews` la collection détermine leur ordre d’affichage dans la pile.
- L’ordre des sous-affichages au sein `Subview` de la collection détermine leur ordre de plan (ou en couches) dans la vue.

### <a name="dynamically-changing-content"></a>Modification dynamique du contenu

Un affichage de la pile ajuste automatiquement la disposition des sous-vues chaque fois qu’une sous-vue est ajoutée, supprimée ou masquée. La disposition est également ajustée si une propriété de l’affichage de la pile est ajustée ( `Axis`telle que son).

Les modifications de disposition peuvent être animées en les plaçant dans un bloc d’animation, par exemple:

```csharp
// Animate stack
UIView.Animate(0.25, ()=>{
    // Adjust stack view
    RatingView.LayoutIfNeeded();
});
```

La plupart des propriétés de la vue de la pile peuvent être spécifiées à l’aide de classes de taille dans une table de montage séquentiel. Ces propriétés sont automatiquement animées pour répondre à des modifications de taille ou d’orientation.

## <a name="summary"></a>Récapitulatif

Cet article a abordé le nouveau `UIStackView` contrôle (pour iOS 9) à gérer un ensemble de sous-affichages dans une pile horizontale ou verticale dans une application Xamarin. iOS.
Il a commencé par un exemple simple d’utilisation d’affichages de pile pour créer une interface utilisateur, et a terminé avec une vue plus détaillée des vues de pile et leurs propriétés et fonctionnalités.



## <a name="related-links"></a>Liens associés

- [Exemples iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [iOS 9 pour les développeurs](https://developer.apple.com/ios/pre-release/)
- [Nouveautés d’iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Référence UIStackView](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIStackView_Class_Reference/)
- [Présentation de UIStackView (vidéo)](https://university.xamarin.com/lightninglectures/introducing-uistackview-on-ios9)
