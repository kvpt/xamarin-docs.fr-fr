---
title: Résumé du chapitre 4. Défilement de la pile
description: 'Création d’applications mobiles avec Xamarin.Forms: Résumé du chapitre 4. Défilement de la pile'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 7A39FD4F-15AD-4F94-960E-9FEEB63FFD44
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: bda9d5cb323524981bed9c3bb55998513dd69aab
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73032874"
---
# <a name="summary-of-chapter-4-scrolling-the-stack"></a>Résumé du chapitre 4. Défilement de la pile

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)

Ce chapitre est principalement consacré à l’introduction du concept de *mise en page*, qui est le terme global pour les classes et les techniques que Xamarin.Forms utilise pour organiser l’affichage visuel de plusieurs vues sur la page.

La mise en page [`Layout`](xref:Xamarin.Forms.Layout) [`Layout<T>`](xref:Xamarin.Forms.Layout`1)implique plusieurs classes qui dérivent et . Ce chapitre [`StackLayout`](xref:Xamarin.Forms.StackLayout)se concentre sur .

> [!NOTE]
> L’introduit [`FlexLayout`](~/xamarin-forms/user-interface/layouts/flex-layout.md) dans Xamarin.Forms 3.0 peut être `StackLayout` utilisé d’une manière similaire à mais avec plus de flexibilité.

Également introduit dans ce [`ScrollView`](xref:Xamarin.Forms.ScrollView) [`Frame`](xref:Xamarin.Forms.Frame)chapitre [`BoxView`](xref:Xamarin.Forms.BoxView) sont les , , et les classes.

## <a name="stacks-of-views"></a>Piles de vues

[`StackLayout`](xref:Xamarin.Forms.StackLayout)dérive `Layout<View>` et hérite [`Children`](xref:Xamarin.Forms.Layout`1) d’une `IList<View>`propriété de type . Vous ajoutez plusieurs éléments de `StackLayout` vue à cette collection, et les affiche dans une pile horizontale ou verticale.

Fixez [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) la `StackLayout` propriété d’un membre de [`Vertical`](xref:Xamarin.Forms.StackOrientation.Vertical) [`Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal)l’énumération, [`StackOrientation`](xref:Xamarin.Forms.StackOrientation) soit . Par défaut, il s’agit de `Vertical`.

Définissez [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) la `StackLayout` propriété `double` d’une valeur pour spécifier un espacement entre les enfants. La valeur par défaut est de 6.

Dans le code, vous `Children` pouvez `StackLayout` ajouter `for` des `foreach` éléments à la collection d’une ou `Children` d’une boucle comme démontré dans l’échantillon [**ColorLoop,**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorLoop) ou vous pouvez initialiser la collection avec une liste des vues individuelles comme démontré dans [**ColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorList). Les enfants doivent `View` dériver `StackLayout` mais peuvent inclure d’autres objets.

## <a name="scrolling-content"></a>Contenu défilant

Si `StackLayout` un contient trop d’enfants pour afficher `StackLayout` sur [`ScrollView`](xref:Xamarin.Forms.ScrollView) une page, vous pouvez mettre le dans un pour permettre le défilement.

Définissez [`Content`](xref:Xamarin.Forms.ScrollView.Content) la `ScrollView` propriété de la vue que vous souhaitez faire défiler. C’est `StackLayout`souvent un , mais il peut être n’importe quelle vue.

Définissez [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation) la `ScrollView` propriété d’un [`Vertical`](xref:Xamarin.Forms.ScrollOrientation.Vertical)membre [`Horizontal`](xref:Xamarin.Forms.ScrollOrientation.Horizontal)de [`Both`](xref:Xamarin.Forms.ScrollOrientation.Both)la [`ScrollOrientation`](xref:Xamarin.Forms.ScrollOrientation) propriété, , , ou . Par défaut, il s’agit de `Vertical`. Si le contenu `ScrollView` d’un est un `StackLayout`, les deux orientations doivent être cohérentes.

[**L’échantillon ReflectedColors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ReflectedColors) démontre `ScrollView` l’utilisation et `StackLayout` l’affichage des couleurs disponibles. L’échantillon montre également comment utiliser la réflexion .NET pour obtenir `Color` toutes les propriétés et les champs statiques publics de la structure sans avoir besoin de les énumérer explicitement.

## <a name="the-expands-option"></a>L’option Expands

Lorsqu’un `StackLayout` enfant empile ses enfants, chaque enfant occupe `StackLayout` une fente particulière dans la hauteur totale `HorizontalOptions` `VerticalOptions` de ce qui dépend de la taille de l’enfant et des paramètres de son et de ses propriétés. Ces propriétés sont [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions)attribuées des valeurs de type .

La `LayoutOptions` structure définit deux propriétés :

- [`Alignment`](xref:Xamarin.Forms.LayoutOptions.Alignment)du [`LayoutAlignment`](xref:Xamarin.Forms.LayoutAlignment) type d’énumération avec [`Start`](xref:Xamarin.Forms.LayoutAlignment.Start) [`Center`](xref:Xamarin.Forms.LayoutAlignment.Center)quatre [`End`](xref:Xamarin.Forms.LayoutAlignment.End)membres, , , , et[`Fill`](xref:Xamarin.Forms.LayoutAlignment.Fill)
- [`Expands`](xref:Xamarin.Forms.LayoutOptions.Expands)de type`bool`

Pour votre commodité, la `LayoutOptions` structure définit également huit `LayoutOptions` champs statiques de type qui englobent toutes les combinaisons des propriétés de deux instances :

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

La discussion suivante `StackLayout` implique une orientation verticale par défaut. L’horizontale `StackLayout` est analogue.

Pour une `StackLayout`verticale, le `HorizontalOptions` réglage détermine comment un enfant est `StackLayout`positionné horizontalement dans la largeur de la . Un `Alignment` réglage `Start` `Center`de `End` , , ou provoque l’enfant d’être horizontalement sans contrainte. L’enfant détermine sa propre largeur et est positionné à gauche, au centre ou à droite de la `StackLayout`. L’option `Fill` provoque l’enfant d’être horizontalement contraint `StackLayout`et remplit la largeur de la .

Pour une `StackLayout`verticale, chaque enfant est verticalement sans contrainte et obtient une fente verticale `VerticalOptions` en fonction de la hauteur de l’enfant, auquel cas le réglage n’est pas pertinent.

Si la `StackLayout` verticale elle-même n’est `Start`pas `Center`contrainte `End`&mdash;qui est si son `VerticalOptions` réglage est , , ou , alors la hauteur de la `StackLayout` est la hauteur totale de ses enfants.

Toutefois, si `StackLayout` la verticale est&mdash;limitée `VerticalOptions` verticalement si son réglage est `Fill` &mdash;alors la hauteur de la `StackLayout` sera la hauteur de son conteneur, qui pourrait être supérieure à la hauteur totale de ses enfants. Si c’est le cas, et si `VerticalOptions` au `Expands` moins `true`un enfant a un `StackLayout` réglage avec un drapeau de `Expands` , `true`alors l’espace supplémentaire dans le est alloué également parmi tous ces enfants avec un drapeau de . La hauteur totale des enfants sera alors `StackLayout`égale `Alignment` à la `VerticalOptions` hauteur du , et la partie du cadre détermine comment l’enfant est positionné verticalement dans sa fente.

Ceci est démontré dans l’échantillon [**VerticalOptionsDemo.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/VerticalOptionsDemo)

## <a name="frame-and-boxview"></a>Cadre et BoxView

Ces deux vues rectangulaires sont souvent utilisées à des fins de présentation.

La [`Frame`](xref:Xamarin.Forms.Frame) vue affiche un cadre rectangulaire autour d’une `StackLayout`autre vue, qui peut être une mise en page telle que . `Frame`hérite [`Content`](xref:Xamarin.Forms.ContentView.Content) d’une propriété à partir de [`ContentView`](xref:Xamarin.Forms.ContentView) laquelle `Frame`vous définissez à la vue pour être affiché dans le . Le `Frame` est transparent par défaut. Définissez les trois propriétés suivantes pour personnaliser l’apparence du cadre :

- La [`OutlineColor`](xref:Xamarin.Forms.Frame.OutlineColor) propriété pour le rendre visible. Il est courant `OutlineColor` `Color.Accent` de régler quand vous ne connaissez pas le schéma de couleur sous-jacent.
- La [`HasShadow`](xref:Xamarin.Forms.Frame.HasShadow) propriété peut `true` être définie pour afficher une ombre noire sur les appareils iOS.
- Définissez [`Padding`](xref:Xamarin.Forms.Layout.Padding) la `Thickness` propriété à une valeur de laisser un espace entre le cadre et le contenu de l’image. La valeur par défaut est de 20 unités de tous les côtés.

Le `Frame` défaut `HorizontalOptions` `VerticalOptions` et `LayoutOptions.Fill`les valeurs de `Frame` , ce qui signifie que le remplira son conteneur. Avec d’autres paramètres, `Frame` la taille de la est basée sur la taille de son contenu.

Le `Frame` sont démontrés dans l’échantillon [**FramedText.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FramedText)

L’affichage [`BoxView`](xref:Xamarin.Forms.BoxView) d’une zone rectangulaire de couleur spécifiée par sa [`Color`](xref:Xamarin.Forms.BoxView.Color) propriété.

Si `BoxView` le est contraint `HorizontalOptions` `VerticalOptions` (son et les `LayoutOptions.Fill`propriétés ont leurs paramètres par défaut de ), le `BoxView` remplit l’espace disponible pour elle. Si `BoxView` le n’est `HorizontalOptions` pas `LayoutOptions` tendu `Start` `Center`(avec `End`et les paramètres de , , ou ), il a une dimension par défaut de 40 unités carrées. A `BoxView` peut être limité dans une dimension et sans contrainte dans l’autre.

Souvent, vous définissez [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) le `BoxView` et les propriétés de pour lui donner une taille spécifique. Ceci est illustré par l’échantillon [**SizedBoxView.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/SizedBoxView)

Vous pouvez utiliser `StackLayout` plusieurs instances `BoxView` de `Label` combiner `Frame` un et plusieurs cas dans un pour afficher `StackLayout` une `ScrollView` couleur particulière, puis mettre chacune de ces vues dans un dans un pour créer la liste attrayante de couleurs montrées dans l’échantillon [**ColorBlocks:**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorBlocks)

[![Triple capture d’écran de blocs de couleurs](images/ch04fg11-small.png "Liste des couleurs")](images/ch04fg11-large.png#lightbox "Liste des couleurs")

## <a name="a-scrollview-in-a-stacklayout"></a>Un ScrollView dans un StackLayout?

Mettre `StackLayout` un `ScrollView` dans un est `ScrollView` commun, `StackLayout` mais mettre un dans un est aussi parfois pratique. En théorie, cela ne devrait pas être `StackLayout` possible parce que les enfants d’une verticale sont verticalement sans contrainte. Mais `ScrollView` un doit être verticalement contraint. Il doit être donné une hauteur spécifique afin qu’il puisse alors déterminer la taille de son enfant pour le défilement.

L’astuce est `ScrollView` de donner `StackLayout` `VerticalOptions` à `FillAndExpand`l’enfant d’un cadre de . Ceci est démontré dans l’échantillon [**BlackCat.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCat)

**L’échantillon BlackCat** montre également comment définir et accéder aux ressources du programme qui sont intégrées dans la bibliothèque partagée. Cela peut également être réalisé avec les projets d’actifs partagés (SAP), mais le processus est un peu plus difficile, comme le démontre l’échantillon [**BlackCatSap.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCatSap)

## <a name="related-links"></a>Liens connexes

- [Chapitre 4 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch04-Apr2016.pdf)
- [Échantillons du chapitre 4](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)
- [Échantillons du chapitre 4 F](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FS)
- [StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md)
- [ScrollView](~/xamarin-forms/user-interface/layouts/scroll-view.md)
- [BoxView](~/xamarin-forms/user-interface/boxview.md)
