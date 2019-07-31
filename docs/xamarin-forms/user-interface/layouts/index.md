---
title: Dispositions dans Xamarin.Forms
description: Xamarin.Forms a plusieurs dispositions et les fonctionnalités pour organiser le contenu sur l’écran, et cet article explique les.
ms.prod: xamarin
ms.assetid: 65030DA3-C7C1-4A02-B478-811073C39139
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 12/18/2018
ms.openlocfilehash: 1889579a48364204a977d63bd9bdb875df37a2bf
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657031"
---
# <a name="layouts-in-xamarinforms"></a>Dispositions dans Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

Xamarin.Forms a plusieurs dispositions et les fonctionnalités pour organiser le contenu sur l’écran.

> [!VIDEO https://youtube.com/embed/4HlLjTZQzjM]

**Vidéo sur les dispositions Xamarin. Forms**

Chaque contrôle de disposition est décrite ci-dessous, ainsi que des détails sur la façon de gérer les changements d’orientation de l’écran :

* **[StackLayout](stack-layout.md)** : permet d’organiser les affichages de manière linéaire, horizontalement ou verticalement. Vues dans un StackLayout peuvent être alignés au centre, gauche ou droit de la disposition.
* **[AbsoluteLayout](absolute-layout.md)** : permet d’organiser les vues en définissant des coordonnées & taille en termes de valeurs ou de ratios absolus. DispositionAbsolue peut servir à la couche de vues comme ancrer à gauche, droite ou au centre.
* **[RelativeLayout](relative-layout.md)** : permet d’organiser les vues en définissant des contraintes par rapport aux dimensions & position de leurs parents.
* **[Grid](grid.md)** : permet d’organiser les vues dans une grille. Lignes et colonnes peuvent être spécifiés en termes de valeurs absolues ou des proportions.
* **[FlexLayout](flex-layout.md)** : permet d’organiser les affichages horizontalement ou verticalement avec l’encapsulation.
* **[ScrollView](scroll-view.md)** : permet de fournir un défilement lorsqu’une vue ne peut pas s’ajuster entièrement dans les limites de l’écran.
* **[LayoutOptions](layout-options.md)** : définissez l’alignement et l’expansion d’une vue, par rapport à son parent.
* **[Transparence d’entrée](#input_transparency)** : spécifie si un élément reçoit une entrée.
* **[Margin et Padding](margin-and-padding.md)** : montre comment contrôler le comportement de disposition lorsqu’un élément est affiché dans l’interface utilisateur.
* **[Orientation](device-orientation.md)** de l’appareil: explique comment gérer les modifications de l’orientation de l’appareil.
* **[Disposition sur les tablettes et les appareils de bureau](tablet.md)** : indique comment optimiser pour les écrans de grande taille sur chaque plateforme.
* **[Dispositions pouvant être liées](bindable-layouts.md)** : activez les classes de disposition pour générer leur contenu en les liant à une collection d’éléments.
* **[Création d’une disposition personnalisée](custom.md)** : explique comment créer une classe de disposition personnalisée.
* **[Compression](layout-compression.md)** de la disposition: supprime la disposition spécifiée de l’arborescence d’éléments visuels afin d’améliorer les performances de rendu des pages.

Plateforme contrôles peuvent également être utilisés directement dans les dispositions Xamarin.Forms avec [ **incorporation Native** ](~/xamarin-forms/platform/native-views/index.md) (Nouveautés de Xamarin.Forms 2.2), et vous pouvez [ **créer des dispositions personnalisées** ](custom.md) pour répondre aux besoins spécifiques.

Le graphique suivant visualise les contrôles de disposition :

[![](images/layouts-sml.png "Dispositions Xamarin.Forms")](images/layouts.png#lightbox "dispositions Xamarin.Forms")

## <a name="choosing-the-right-layout"></a>Choisir la bonne disposition

Les dispositions que vous choisissez dans votre application peuvent ou aider à faire des dégâts lorsque vous créez une application Xamarin.Forms attrayante et utilisable. Prend un certain temps à prendre en compte la façon dont chaque fonctionne de la mise en page peut vous aider à écrire du code de l’interface utilisateur plus clair et plus évolutif. Un écran peut avoir une combinaison de différentes dispositions pour atteindre une conception spécifique.

### <a name="stacklayoutstack-layoutmd"></a>[StackLayout](stack-layout.md)

Le `StackLayout` est utilisé pour afficher les vues le long d’une ligne horizontale ou verticale. Position et la taille de la mise en page est déterminée en fonction de l’affichage `HeightRequest`, `WidthRequest`, `HorizontalOptions` et `VerticalOptions`. `StackLayout` est souvent utilisé en tant que la disposition de base, réorganiser les autres dispositions sur l’écran.

Pour obtenir un exemple de lorsque `StackLayout` est un bon choix, prenez une application qui doit afficher un bouton et une étiquette, avec l’étiquette aligné à gauche et le bouton aligné à droite.

```xaml
<StackLayout Orientation="Horizontal">
  <Label HorizontalOptions="StartAndExpand" Text="Label" />
  <Button HorizontalOptions="End" Text="Button" />
</StackLayout>
```

### <a name="flexlayoutflex-layoutmd"></a>[FlexLayout](flex-layout.md)

Le `FlexLayout` est similaire à `StackLayout` car il affiche les vues enfants horizontalement ou verticalement :

```xaml
<FlexLayout Direction="Column"
            AlignItems="Center"
            JustifyContent="SpaceEvenly">

    <Label Text="FlexLayout in Action" />
    <Button Text="Button" />
    <Label Text="Another Label" />
</FlexLayout>
```

Toutefois, s’il existe trop grand nombre d’enfants pour tenir dans une seule ligne ou colonne, `FlexLayout` est également capable d’encapsuler ces vues. `FlexLayout` est basé sur le Module de mise en page de boîte Flexible CSS et a de nombreuses options de la mêmes intégrées pour positionner et aligner ses enfants.

### <a name="absolutelayoutabsolute-layoutmd"></a>[AbsoluteLayout](absolute-layout.md)

Le `AbsoluteLayout` est utilisé pour afficher des vues, avec la taille et la position spécifiés soit comme des valeurs explicites ou des valeurs par rapport à la taille de la disposition. Contrairement aux `StackLayout` et `Grid`, `AbsoluteLayout` permet l’enfant vues se chevauchent. Contrairement aux `RelativeLayout`, `AbsoluteLayout` ne vous permettent de placer des éléments hors écran.

Pour obtenir un exemple de lorsque `AbsoluteLayout` est un bon choix, prenez une application qui doit présenter des collections d’objets en tant que piles. Cela est souvent lors de la présentation des albums de photos ou des morceaux. Le code suivant donne l’apparence d’une pile, avec des éléments de faire pivotés pour cacher le contenu de la pile :

Dans XAML :

```xaml
<AbsoluteLayout Padding="15">
  <Image AbsoluteLayout.LayoutFlags="PositionProportional" AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100" Rotation="30"
    Source="bottom.png" />
  <Image AbsoluteLayout.LayoutFlags="PositionProportional" AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100" Rotation="60"
    Source="middle.png" />
  <Image AbsoluteLayout.LayoutFlags="PositionProportional" AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100"
    Source="cover.png" />
</AbsoluteLayout>
```

Notez les aspects suivants du code ci-dessus :

- Chaque `Image` s’affiche dans la même position (au milieu de l’espace horizontal)
- Le `Padding` est considéré par `AbsoluteLayout`, contrairement à `RelativeLayout`, qui l’ignore.
- `AbsoluteLayout.LayoutFlags` Spécifie comment les limites de disposition seront interprétées. Dans ce cas `PositionProportional`, signifie que les coordonnées sera un ratio de la taille de la mise en page, tandis que la taille sera interprétée comme une taille explicite.
- `AbsoluteLayout.Layoutbounds` Spécifie la position horizontale, la position verticale, la largeur et la hauteur dans cet ordre.

### <a name="relativelayoutrelative-layoutmd"></a>[RelativeLayout](relative-layout.md)

Le `RelativeLayout` est utilisé pour afficher des vues, avec la taille et la position spécifiée en tant que valeurs par rapport aux valeurs de la mise en page ou une autre vue. Valeurs relatives n’avez pas besoin de correspondre il correspondant valeur sur la vue associée. Par exemple, il est possible de définir une vue `Width` propriété soit proportionnelle d’une autre vue `X` propriété.

RelativeLayout peut être utilisé pour créer des interfaces utilisateur qui s’adaptent proportionnellement entre les tailles d’unités. Le XAML suivant implémente une conception avec des zones dans les angles au premier plan, avec un drapeau de golf avec indicateur dans le centre de :

```xaml
<RelativeLayout HorizontalOptions="FillAndExpand" VerticalOptions="FillAndExpand">
  <BoxView Color="Blue" HeightRequest="50" WidthRequest="50"
    RelativeLayout.XConstraint= "{ConstraintExpression Type=RelativeToParent, Property=Width, Factor = 0}"
    RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor = 0}" />
  <BoxView Color="Red" HeightRequest="50" WidthRequest="50"
    RelativeLayout.XConstraint= "{ConstraintExpression Type=RelativeToParent, Property=Width, Factor = .9}"
    RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor = 0}" />
  <BoxView Color="Gray" WidthRequest="15" x:Name="pole"
    RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=.75}"
    RelativeLayout.XConstraint= "{ConstraintExpression Type=RelativeToParent, Property=Width, Factor = .45}"
    RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor = .25}" />
  <BoxView Color="Green"
    RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=.10, Constant=10}"
    RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent,Property=Width, Factor=.2, Constant=20}"
    RelativeLayout.XConstraint= "{ConstraintExpression Type=RelativeToView, ElementName=pole, Property=X, Constant=15}"
    RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToView, ElementName=pole, Property=Y, Constant=0}" />
</RelativeLayout>
```

Notez les aspects suivants du code ci-dessus :

- Les tailles et positions sont spécifiés en tant que contraintes.
- Le drapeau de Golf est nommé afin que l’indicateur (vert de la zone) position peut être définie par rapport au drapeau de golf.
- Les expressions de contrainte ont `Factor` et `Constant` propriétés, qui peuvent être utilisées pour définir des tailles et positions en tant que multiples (ou fractions) des propriétés d’autres objets, ainsi qu’une constante. Constantes peuvent être négatifs.

### <a name="gridgridmd"></a>[Grid](grid.md)

Le `Grid` est utilisé pour afficher des éléments dans les lignes et colonnes. Notez que la grille n’est pas une table, donc il n’a pas le concept de cellules, des lignes d’en-tête et pied de page ou des bordures entre les lignes et colonnes. En règle générale, grille ne convient pas pour l’affichage des données tabulaires. Pour cette utilisation, envisagez un [ListView](~/xamarin-forms/user-interface/listview/index.md) ou [TableView](~/xamarin-forms/user-interface/tableview.md).

Pour obtenir un exemple de quand un `Grid` est la bonne disposition à utiliser, envisagez une entrée numérique pour une calculatrice. Une entrée numérique pour une calculatrice peut être constitué de quatre lignes et trois colonnes, chacune avec un bouton. Le code suivant implémente cette conception :

```xaml
<Grid>
  <Grid.RowDefinitions>
    <RowDefinition Height="*" />
    <RowDefinition Height="*" />
    <RowDefinition Height="*" />
    <RowDefinition Height="*" />
  </Grid.RowDefinitions>

  <Grid.ColumnDefinitions>
    <ColumnDefinition Width="*" />
    <ColumnDefinition Width="*" />
    <ColumnDefinition Width="*" />
  </Grid.ColumnDefinitions>
  <Button Text="1" Grid.Row="0" Grid.Column="0" />
  <Button Text="2" Grid.Row="0" Grid.Column="1" />
  <Button Text="3" Grid.Row="0" Grid.Column="2" />
  <Button Text="4" Grid.Row="1" Grid.Column="0" />
  <Button Text="5" Grid.Row="1" Grid.Column="1" />
  <Button Text="6" Grid.Row="1" Grid.Column="2" />
  <Button Text="7" Grid.Row="2" Grid.Column="0" />
  <Button Text="8" Grid.Row="2" Grid.Column="1" />
  <Button Text="9" Grid.Row="2" Grid.Column="2" />
  <Button Text="0" Grid.Row="3" Grid.Column="1" />
  <Button Text="&lt;-" Grid.Row="3" Grid.Column="2" />
</Grid>
```

Notez les aspects suivants du code ci-dessus :

- Grilles et les colonnes sont explicitement spécifiés, ne pas déduit à partir du contenu.
- `Height` et `Width` valeurs peuvent être définies en étoile, ce qui signifie que la grille définit ces valeurs pour remplir l’espace disponible.
- La position de chaque bouton est spécifiée par `Grid.Row`  &  `Grid.Column` propriétés.

### <a name="layoutoptionslayout-optionsmd"></a>[LayoutOptions](layout-options.md)

Le [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) structure peut être utilisée pour définir l’alignement et l’expansion pour une vue, par rapport à son parent.

### <a name="margin-and-paddingmargin-and-paddingmd"></a>[Marge et remplissage](margin-and-padding.md)

Le [ `Margin` ](xref:Xamarin.Forms.View.Margin) et [ `Padding` ](xref:Xamarin.Forms.Layout.Padding) propriétés contrôlent le comportement de disposition quand un élément est affiché dans l’interface utilisateur.

<a name="input_transparency" />

### <a name="input-transparency"></a>Transparence d’entrée

Chaque élément a un [ `InputTransparent` ](xref:Xamarin.Forms.VisualElement.InputTransparent) propriété qui est utilisée pour définir si l’élément reçoit l’entrée. Sa valeur par défaut est `false`, en garantissant que l’élément reçoit l’entrée.

Lorsque cette propriété est définie sur une classe de conteneur, tel qu’une classe de mise en page, les transferts de sa valeur aux éléments enfants. Par conséquent, si le [ `InputTransparent` ](xref:Xamarin.Forms.VisualElement.InputTransparent) propriété `true` sur une disposition de classe entraîne les éléments dans la disposition ne reçoit ne pas d’entrée.

### <a name="device-orientationdevice-orientationmd"></a>[Orientation de l’appareil](device-orientation.md)

Xamarin.Forms et ses mises en page intégrés sont capables de gérer les modifications apportées à l’orientation de l’appareil. Prendre en compte les orientations que votre application prendra en charge, ainsi que la manière dont vous apporterez utilisent l’espace fourni dans les modes paysage et portrait.

### <a name="layout-for-tablet-and-desktop-appstabletmd"></a>[Mise en page pour les applications de tablette et bureau](tablet.md)

appareils pour iOS, Android et plateforme Windows universelle prennent toutes en charge les tailles d’écran plus grands sur tablette (ainsi que les ordinateurs portables et postes de travail pour Windows). Xamarin.Forms vous permet d’optimiser votre application pour les écrans plus grands en détectant le type d’appareil et l’ajustement de la mise en page, ou à l’aide d’une page totalement différente complètement pour les écrans plus grands.

### <a name="bindable-layoutsbindable-layoutsmd"></a>[Dispositions liables](bindable-layouts.md)

La `BindableLayout` classe active une classe de disposition qui dérive de [`Layout<T>`](xref:Xamarin.Forms.Layout`1) la classe pour générer son contenu en liant à une collection d’éléments, avec l’option permettant de définir l’apparence de chaque élément [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)avec un.

### <a name="creating-a-custom-layoutcustommd"></a>[Création d’une disposition personnalisée](custom.md)

Xamarin.Forms définit quatre classes de mise en page - [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), [ `AbsoluteLayout` ](xref:Xamarin.Forms.AbsoluteLayout), [ `RelativeLayout` ](xref:Xamarin.Forms.RelativeLayout), et [ `Grid` ](xref:Xamarin.Forms.Grid), et chacune dispose ses enfants d’une manière différente. Cependant, parfois ses besoins pour organiser le contenu de la page à l’aide d’une disposition ne pas fournies par Xamarin.Forms. Cet article explique comment écrire une classe de disposition personnalisée et montre l’orientation accents `WrapLayout` classe qui réorganise ses enfants horizontalement sur la page et encapsule ensuite afficher les enfants suivants à des lignes supplémentaires.

### <a name="layout-compressionlayout-compressionmd"></a>[Compression de la disposition](layout-compression.md)

La compression des dispositions supprime les dispositions spécifiées à partir de l’arborescence visuelle dans le but d’améliorer les performances de rendu de page. Les avantages en matière de performances de cette technique varient selon la complexité d’une page, la version du système d’exploitation utilisé et l’appareil sur lequel l’application est en cours d’exécution. Toutefois, les gains en termes de performances les plus importants seront visibles sur les appareils les plus anciens.

## <a name="making-your-choice"></a>Rendre votre choix

N’oubliez pas que dans la plupart des cas, plusieurs choix de disposition peut servir à implémenter votre conception de votre choix. Lorsqu’il existe plusieurs choix possibles, envisagez d’approche qui sera la plus facile à votre situation.
La plupart des conceptions impossibles avec une disposition qu’un seul, donc des dispositions d’imbrication en tant que nécessaire créer des conceptions plus complexes.

## <a name="related-links"></a>Liens associés

- [D’Apple Human Interface Guidelines pour](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG)
- [Site Web de conception Android](https://developer.android.com/design/index.html)
- [Disposition (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Exemple BusinessTumble (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
