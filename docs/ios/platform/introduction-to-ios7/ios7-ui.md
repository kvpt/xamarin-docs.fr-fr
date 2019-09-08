---
title: Vue d’ensemble de l’interface utilisateur iOS 7
description: iOS 7 introduit une multitude de modifications de l’interface utilisateur. Cet article met en évidence certaines des modifications les plus importantes, à la fois dans l’apparence visuelle des contrôles et dans les API qui prennent en charge la nouvelle conception.
ms.prod: xamarin
ms.assetid: FADCEA7C-8968-42A1-9E9E-F4BBAB7BCF2C
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/21/2017
ms.openlocfilehash: 97542c50041bfe24a3ad9494443ed8a4df85f113
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70752237"
---
# <a name="ios-7-user-interface-overview"></a>Vue d’ensemble de l’interface utilisateur iOS 7

_iOS 7 introduit une multitude de modifications de l’interface utilisateur. Cet article met en évidence certaines des modifications les plus importantes, à la fois dans l’apparence visuelle des contrôles et dans les API qui prennent en charge la nouvelle conception._

iOS 7 se concentre sur le contenu sur chrome. Les éléments d’interface utilisateur dans iOS 7 détaillent le chrome en supprimant des attributs tels que les bordures superflues, les barres d’État et les barres de navigation, qui réduisent la quantité d’espace d’écran utilisée par les vues de contenu. Dans iOS 7, le contenu est conçu pour utiliser tout l’écran.

iOS 7 introduit plusieurs autres modifications : la couleur est utilisée pour distinguer les éléments d’interface utilisateur, à la place des attributs tels que les bordures de bouton. De nombreux éléments, tels que les barres de navigation et les barres d’État, sont désormais flous, translucides ou transparents, avec les vues de contenu qui se trouvent en dessous. Ces vues de contenu sont rendues dans les barres floues, ce qui donne un sentiment de profondeur dans l’interface utilisateur.

Cet article aborde plusieurs des modifications apportées aux éléments d’interface utilisateur dans iOS 7, ainsi que diverses API associées à la nouvelle conception de l’interface utilisateur.

## <a name="view-and-control-changes"></a>Afficher et contrôler les modifications

Toutes les vues dans UIKit sont conformes à la nouvelle apparence d’iOS 7. Cette section met en évidence certaines des modifications apportées à ces vues, ainsi que les API associées qui ont été modifiées pour prendre en charge la nouvelle interface utilisateur.

### <a name="uibutton"></a>UIButton

Les boutons créés à `UIButton` partir de la classe sont désormais sans bordure, sans arrière-plan par défaut, comme indiqué ci-dessous :

 ![](ios7-ui-images/button.png "Exemple de UIButton")

Le `UIButtonType.RoundedRect` style est déconseillé. S’il est utilisé dans iOS `UIButtonType.RoundedRect` 7, est `UIButtonType.System` utilisé, ce qui produit le style de bouton par défaut sans bords d’arrière-plan ou visibles, comme indiqué ci-dessus.

### <a name="uibarbuttonitem"></a>UIBarButtonItem

De la `UIButton`même façon que les boutons de barre sont également sans bordure, le `UIBarButtonItemStyle.Plain` nouveau style présenté ci-dessous est utilisé par défaut :

 ![](ios7-ui-images/barbuttonplain.png "Exemple de UIBarButtonItem")

En outre, le `UIBarButtonItemStyle.Bordered` style est déconseillé. Dans iOS 7, le `UIBarButtonItemStyle.Plain` style est utilisé. `UIBarButtonItemStyle.Bordered`

Le `UIBarButtonItemStyle.Done` style n’est pas déconseillé. Toutefois, il crée également un bouton sans bordure, uniquement avec un style de texte en gras, comme indiqué ci-dessous :

 ![](ios7-ui-images/barbuttondone.png "Exemple de UIBarButtonItem dans le style terminé")

### <a name="uialertview"></a>UIAlertView

En plus de la modification de style pour la nouvelle apparence iOS 7, les affichages des alertes ne prennent plus en charge la personnalisation via la sous-vue. Même si `UIAlertView` hérite de `UIView`, l' `AddSubview` appel de `UIAlertView` sur un n’a aucun effet. Considérons par exemple le code suivant :

```csharp
UIBarButtonItem button = new UIBarButtonItem ("Bar Button", UIBarButtonItemStyle.Plain, (s,e) =>
{
    UIAlertView alert = new UIAlertView ("Title", "Message", null, "Cancel", "OK");

    alert.AddSubview (new UIView () {
        Frame = new CGRect(50, 50,100, 100),
        BackgroundColor = UIColor.Green
    });

    alert.Show ();
});
```

Cela génère un affichage des alertes standard, avec la sous-vue ignorée, comme indiqué ci-dessous :

 ![](ios7-ui-images/alert.png "Exemple de UIAlertView")

 Remarque : UIAlertView a été déconseillé dans iOS 8. Affichez la recette du [contrôleur d’alertes](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller) dans à l’aide d’un affichage des alertes dans iOS 8 et versions ultérieures.

### <a name="uisegmentedcontrol"></a>UISegmentedControl

Les contrôles segmentés dans iOS 7 sont transparents et prennent en charge la teinte. La couleur de teinte est utilisée pour le texte et la couleur de la bordure. Lorsqu’un segment est sélectionné, la couleur est permutée entre l’arrière-plan et le texte, la couleur de teinte étant utilisée pour mettre en surbrillance le segment sélectionné, comme indiqué ci-dessous :

 ![](ios7-ui-images/segmentedcontrol.png "Exemple de UISegmentedControl")

En outre, le `UISegmentedControlStyle` a été déconseillé dans iOS 7.

### <a name="picker-views"></a>Vues du sélecteur

L’API pour les vues du sélecteur est en grande partie inchangée. Toutefois, les instructions de conception du sélecteur d’état d’iOS 7 doivent être présentées en ligne plutôt que sous forme d’affichages animés animés à partir du bas de l’écran ou par le biais d’un nouveau contrôleur envoyé dans la pile d’un contrôleur de navigation, comme dans les versions iOS précédentes. Vous pouvez le voir dans l’application Calendrier système :

 ![](ios7-ui-images/inlinepicker.png "Cela peut être observé dans l’application Calendrier système.")

### <a name="uisearchdisplaycontroller"></a>UISearchDisplayController

La barre de recherche est maintenant affichée à l’intérieur de la `UISearchDisplayController.DisplaysSearchBarInNavigationBar` barre de navigation lorsque la propriété a la valeur true. Si la valeur est false (valeur par défaut), la barre de navigation est masquée lorsque le contrôleur de recherche est affiché.

La capture d’écran suivante montre la barre de `UISearchDisplayController`recherche dans un :

 ![](ios7-ui-images/searchbar.png "Exemple de UISearchDisplayController")

### <a name="uitableview"></a>UITableView

Les API autour `UITableView` de sont principalement inchangées. Toutefois, le style a changé de manière spectaculaire pour se conformer à la nouvelle conception de l’interface utilisateur. La hiérarchie d’affichage interne est également légèrement différente. Cette modification n’affecte pas la plupart des applications, mais elle doit être prise en compte.

#### <a name="grouped-table-style"></a>Style de tableau groupé

Le style groupé modifié a été mis à jour et le contenu s’étend maintenant aux bords de l’écran, comme indiqué ci-dessous :

 ![](ios7-ui-images/table1.png "Exemple de style de tableau groupé")

#### <a name="separatorinset"></a>SeparatorInset

Les séparateurs de lignes peuvent maintenant être mis en retrait `UITableVIewCell.SeparatorInset` en définissant la propriété. Par exemple, le code suivant est utilisé pour mettre en retrait les cellules du bord gauche :

```csharp
cell.SeparatorInset = new UIEdgeInsets (0, 50, 0, 0);
```

Cela produit dans la vue table avec des cellules mises en retrait comme indiqué ci-dessous :

 ![](ios7-ui-images/separatorinset.png "Exemple de SeparatorInset UITableView")

#### <a name="table-button-styles"></a>Styles de bouton de tableau

Les différents boutons utilisés dans les vues de table ont tous changé. La capture d’écran suivante présente une vue de table en mode d’édition :

 ![](ios7-ui-images/table2.png "Cette capture d’écran présente une vue de table en mode d’édition")

### <a name="additional-control-changes"></a>Modifications de contrôle supplémentaires

D’autres contrôles UIKit ont également été modifiés, y compris les curseurs, les commutateurs et les exécutions pas à pas. Ces modifications sont purement visuelles. Pour plus d’informations, reportez-vous au [Guide de transition de l’interface utilisateur iOS 7](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/TransitionGuide/index.html)d’Apple.

## <a name="general-user-interface-changes"></a>Modifications générales de l’interface utilisateur

En plus des modifications apportées à UIKit, iOS 7 introduit une variété de modifications visuelles apportées à l’interface utilisateur, notamment :

- Contenu plein écran
- Apparence de la barre
- Couleur de teinte

<a name="fullscreen" />

### <a name="full-screen-content"></a>Contenu plein écran

iOS 7 est conçu pour permettre aux applications de tirer parti de la totalité de l’écran. Les contrôleurs d’affichage s’affichent désormais sous la forme d’une barre d’État et d’une barre de navigation, s’il en existe un, au lieu d’apparaître sous les barres d’État et de navigation.

Lorsque vous préparez votre application pour iOS 7, vous pouvez réaligner les sous-vues visuellement à l’aide de *Interface Builder* ou du *Concepteur iOS Xamarin*. Vous pouvez également utiliser l’une des nouvelles API pour gérer le contenu en plein écran par programmation. Ces API sont présentées ci-dessous.

#### <a name="toplayoutguide-and-bottomlayoutguide"></a>TopLayoutGuide et BottomLayoutGuide

 `TopLayoutGuide`et `BottomLayoutGuide` servent de référence pour les emplacements où les vues doivent commencer ou se terminer, de sorte que le contenu ne se chevauche `UIKit` pas par une barre translucide, comme dans l’exemple suivant :

 [![](ios7-ui-images/clipped.png "Exemple de contenu ne chevauchant pas une barre UIKit translucide")](ios7-ui-images/clipped.png#lightbox)

Ces API peuvent être utilisées pour calculer le décalage d’une vue à partir du haut ou du bas de l’écran, et ajuster le positionnement du contenu en conséquence :

```csharp
public override void ViewDidLayoutSubviews ()
{
    base.ViewDidLayoutSubviews ();

    if (UIDevice.CurrentDevice.CheckSystemVersion (7, 0)) { 
        nfloat displacement_y = this.TopLayoutGuide.Length;

        //load subviews with displacement
    }

}
```

Nous pouvons utiliser la valeur calculée ci-dessus pour `ImageView`définir notre déplacement de la partie supérieure de l’écran, de sorte que l’image entière soit visible :

 [![](ios7-ui-images/good2.png "Exemple de déplacement ImageViews à partir du haut de l’écran")](ios7-ui-images/good2.png#lightbox)

Reportez-vous à [imageviewer](https://docs.microsoft.com/samples/xamarin/ios-samples/ios7-ui-updates/) pour obtenir un exemple fonctionnel.

La valeur de déplacement est générée dynamiquement après que la vue a été ajoutée à la hiérarchie, si bien que la `TopLayoutGuide` tentative `BottomLayoutGuide` de lecture `ViewDidLoad` des valeurs et dans retourne 0. Calculez la valeur après le chargement de la vue, par exemple dans `ViewDidLayoutSubviews`.

> [!IMPORTANT]
> `TopLayoutGuide`et `BottomLayoutGuide` sont dépréciées dans iOS 11 en faveur de la nouvelle disposition de zone sécurisée. Apple a indiqué que l’utilisation de la zone sécurisée est compatible avec la version d’iOS antérieure à iOS 11. Pour plus d’informations, consultez le Guide de [mise à jour de votre application pour iOS 11](~/ios/platform/introduction-to-ios11/updating-your-app/visual-design.md#fullscreen) .

#### <a name="edgesforextendedlayout"></a>EdgesForExtendedLayout

Cette API spécifie les bords d’une vue qui doivent être étendus en plein écran, indépendamment de la barre translucidité. Dans iOS 7, les barres de navigation et les barres d’outils apparaissent en couche au-dessus de la vue du contrôleur, contrairement aux versions iOS précédentes, où elles n’occupent pas le même espace. L’application iOS 7 photos illustre la valeur par `UIViewController.EdgesForExtendedLayout` défaut, `UIRectEdge.All`. Ce paramètre remplit les quatre bords de la vue avec le contenu, créant ainsi l’effet de chevauchement et d’affichage plein écran :

 [![](ios7-ui-images/photos.png "Exemple de EdgesForExtendedLayout")](ios7-ui-images/photos.png#lightbox)

Le fait de cliquer sur l’image permet de supprimer les barres et d’afficher l’image en plein écran :

 [![](ios7-ui-images/photos2.png "EdgesForExtendedLayout avec les barres supprimées")](ios7-ui-images/photos2.png#lightbox)

Étant donné que le contenu plein écran est la valeur par défaut, les applications configurées pour iOS 6 feront partie de la vue découpée, comme dans la capture d’écran ci-dessous :

 [![](ios7-ui-images/clipped.png "Les applications configurées pour iOS 6 feront partie de la vue découpée, comme dans cette capture d’écran")](ios7-ui-images/clipped.png#lightbox)

La modification de `UIViewController.EdgesForExtendedLayout` la propriété ajuste ce comportement. Nous pouvons spécifier que la vue ne remplit aucune arête, donc notre vue évitera d’afficher le contenu dans l’espace occupé par la navigation ou les barres d’outils (à chaque orientation) :

```csharp
if (UIDevice.CurrentDevice.CheckSystemVersion (7, 0)) { 
    this.EdgesForExtendedLayout = UIRectEdge.None;
}
```

Dans notre application, nous voyons que la vue est à nouveau repositionnée, de sorte que l’image entière est visible :

 [![](ios7-ui-images/good.png "Exemple avec l’image entière visible")](ios7-ui-images/good.png#lightbox)

Notez que, bien que les effets `TopLayoutGuide/BottomLayoutGuide` des `EdgesForExtendedLayout` API et soient similaires, ils sont destinés à remplir différents objectifs. Si vous `EdgesForExtendedLayout` modifiez le paramètre par défaut, vous pouvez corriger les vues tronquées dans les applications conçues pour iOS 6, mais une bonne conception iOS 7 doit respecter l’esthétique plein écran et fournir une expérience d’affichage plein écran `TopLayoutGuide` , en s’appuyant sur et `BottomLayoutGuide`pour positionner correctement le contenu destiné à être manipulé à un emplacement confortable pour l’utilisateur.

Reportez-vous à [imageviewer](https://docs.microsoft.com/samples/xamarin/ios-samples/ios7-ui-updates/) pour obtenir un exemple fonctionnel.

### <a name="status-and-navigation-bars"></a>État et barres de navigation

La barre d’État et les barres de navigation sont rendues avec transparence. Les barres d’État sont transparentes, tandis que les barres d’outils et les barres de navigation sont translucides et floues pour donner le sentiment de profondeur dans l’interface utilisateur. La capture d’écran suivante montre ce flou et cette transparence, où la couleur d’arrière-plan bleue de la vue de collection s’affiche à la fois dans les barres d’État et de navigation, en leur donnant une apparence bleue claire :

 ![](ios7-ui-images/transparent-navbar.png "Exemple d’État et de flou de la barre de navigation")

#### <a name="status-bar-styles"></a>Styles de barre d’État

Avec le flou et la transparence, le premier plan d’une barre d’État peut être clair ou sombre (la valeur par défaut est le noir). Le style de barre d’État peut être défini à partir du contrôleur d’affichage. Un contrôleur d’affichage peut également définir si la barre d’État est masquée ou affichée.

Par exemple, le code suivant remplace la `PreferredStatusBarStyle` méthode d’un contrôleur d’affichage pour que la barre d’état affiche un premier plan clair :

```csharp
public override UIStatusBarStyle PreferredStatusBarStyle ()
{
    return UIStatusBarStyle.LightContent;
}
```

La barre d’État s’affiche alors comme indiqué ci-dessous :

 ![](ios7-ui-images/light-status-bar.png "Exemple de barre d’État")

Pour masquer la barre d’État du code du contrôleur d’affichage, remplacez `PrefersStatusBarHidden`, comme indiqué ci-dessous :

```csharp
public override bool PrefersStatusBarHidden ()
{
    return true;
}
```

Cela masque la barre d’État :

 ![](ios7-ui-images/status-bar-hidden.png "Barre d’État masquée")

### <a name="tint-color"></a>Couleur de teinte

Les boutons s’affichent désormais sous forme de texte sans chrome. La couleur du texte peut être contrôlée à l' `TintColor` aide de `UIView`la nouvelle propriété sur. La définition `TintColor` de la propriété applique la couleur à la hiérarchie d’affichage entière pour la vue qui la définit. Pour appliquer un `TintColor`tout dans une application, définissez-le `Window`sur le. Vous pouvez également détecter quand la couleur de teinte change `UIView.TintColorDidChange` via la méthode.

Par exemple, la capture d’écran suivante montre l’effet de la modification de la couleur de teinte de la vue d’un contrôleur de navigation en violet :

 ![](ios7-ui-images/tint-color.png "Couleur de teinte violette dans une vue contrôleurs de navigation")

La couleur de teinte peut également être appliquée aux images lorsque `RenderingMode` est défini sur `UIImageRenderingMode.AlwaysTemplate`.

> [!IMPORTANT]
> Impossible de définir la couleur de `UIAppearance`teinte à l’aide de.

### <a name="dynamic-type"></a>Type dynamique

Dans iOS 7, l’utilisateur peut spécifier la taille du texte dans les paramètres système. Avec le type dynamique, la police est ajustée dynamiquement pour paraître correcte quelle que soit la taille. `UIFont.PreferredFontForTextStyle`doit être utilisé pour obtenir une police optimisée pour la taille contrôlée par l’utilisateur.

## <a name="summary"></a>Récapitulatif

Cet article traite des modifications apportées aux éléments d’interface utilisateur dans iOS 7. Il examine plusieurs des modifications apportées aux vues et aux contrôles dans UIKit, en mettant en évidence les modifications visuelles ainsi que les modifications apportées aux API associées. Enfin, il introduit de nouvelles API pour travailler avec du contenu plein écran, la prise en charge des couleurs de teinte et le type dynamique.

## <a name="related-links"></a>Liens associés

- [ImageViewer (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios7-ui-updates)
