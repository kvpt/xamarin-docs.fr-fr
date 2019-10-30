---
title: Résumé du chapitre 4. Défilement de la pile
description: 'Création d’Mobile Apps avec Xamarin. Forms : Résumé du chapitre 4. Défilement de la pile'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 7A39FD4F-15AD-4F94-960E-9FEEB63FFD44
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: bda9d5cb323524981bed9c3bb55998513dd69aab
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032874"
---
# <a name="summary-of-chapter-4-scrolling-the-stack"></a>Résumé du chapitre 4. Défilement de la pile

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)

Ce chapitre est principalement consacré à l’introduction du concept de *disposition*, qui est le terme global pour les classes et les techniques utilisées par Xamarin. Forms pour organiser l’affichage de plusieurs vues sur la page.

La disposition implique plusieurs classes qui dérivent de [`Layout`](xref:Xamarin.Forms.Layout) et [`Layout<T>`](xref:Xamarin.Forms.Layout`1). Ce chapitre se concentre sur [`StackLayout`](xref:Xamarin.Forms.StackLayout).

> [!NOTE]
> Les [`FlexLayout`](~/xamarin-forms/user-interface/layouts/flex-layout.md) introduites dans Xamarin. forms 3,0 peuvent être utilisées de manière similaire à `StackLayout`, mais avec davantage de flexibilité.

Les classes [`ScrollView`](xref:Xamarin.Forms.ScrollView), [`Frame`](xref:Xamarin.Forms.Frame)et [`BoxView`](xref:Xamarin.Forms.BoxView) sont également présentées dans ce chapitre.

## <a name="stacks-of-views"></a>Piles de vues

[`StackLayout`](xref:Xamarin.Forms.StackLayout) dérive de `Layout<View>` et hérite d’une propriété [`Children`](xref:Xamarin.Forms.Layout`1) de type `IList<View>`. Vous ajoutez plusieurs éléments d’affichage à cette collection et `StackLayout` les affiche dans une pile horizontale ou verticale.

Affectez à la propriété [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) de `StackLayout` la valeur d’un membre de l’énumération [`StackOrientation`](xref:Xamarin.Forms.StackOrientation) , [`Vertical`](xref:Xamarin.Forms.StackOrientation.Vertical) ou [`Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal). La valeur par défaut est `Vertical`,

Affectez à la propriété [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) de `StackLayout` une valeur `double` pour spécifier l’espacement entre les enfants. La valeur par défaut est 6.

Dans le code, vous pouvez ajouter des éléments à la collection `Children` de `StackLayout` dans une boucle `for` ou `foreach`, comme illustré dans l’exemple [**ColorLoop**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorLoop) , ou vous pouvez initialiser la collection `Children` avec une liste des vues individuelles, comme illustré dans [**ColorList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorList). Les enfants doivent dériver de `View` mais peuvent inclure d’autres objets `StackLayout`.

## <a name="scrolling-content"></a>Défilement du contenu

Si un `StackLayout` contient trop d’enfants à afficher sur une page, vous pouvez placer le `StackLayout` dans un [`ScrollView`](xref:Xamarin.Forms.ScrollView) pour autoriser le défilement.

Définissez la propriété [`Content`](xref:Xamarin.Forms.ScrollView.Content) de `ScrollView` sur la vue que vous souhaitez faire défiler. Il s’agit souvent d’un `StackLayout`, mais il peut s’agir de n’importe quelle vue.

Affectez à la propriété [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation) de `ScrollView` la valeur d’un membre de la propriété [`ScrollOrientation`](xref:Xamarin.Forms.ScrollOrientation) , [`Vertical`](xref:Xamarin.Forms.ScrollOrientation.Vertical), [`Horizontal`](xref:Xamarin.Forms.ScrollOrientation.Horizontal)ou [`Both`](xref:Xamarin.Forms.ScrollOrientation.Both). La valeur par défaut est `Vertical`, Si le contenu d’un `ScrollView` est un `StackLayout`, les deux orientations doivent être cohérentes.

L’exemple [**ReflectedColors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ReflectedColors) illustre l’utilisation de `ScrollView` et `StackLayout` pour afficher les couleurs disponibles. L’exemple montre également comment utiliser la réflexion .NET pour obtenir toutes les propriétés et les champs statiques publics de la structure `Color` sans avoir à les répertorier explicitement.

## <a name="the-expands-option"></a>L’option développée

Lorsqu’un `StackLayout` empile ses enfants, chaque enfant occupe un emplacement particulier dans la hauteur totale du `StackLayout` qui dépend de la taille de l’enfant et des paramètres de ses propriétés `HorizontalOptions` et `VerticalOptions`. Les valeurs de type [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions)sont affectées à ces propriétés.

La structure `LayoutOptions` définit deux propriétés :

- [`Alignment`](xref:Xamarin.Forms.LayoutOptions.Alignment) du type d’énumération [`LayoutAlignment`](xref:Xamarin.Forms.LayoutAlignment) avec quatre membres, [`Start`](xref:Xamarin.Forms.LayoutAlignment.Start), [`Center`](xref:Xamarin.Forms.LayoutAlignment.Center), [`End`](xref:Xamarin.Forms.LayoutAlignment.End)et [`Fill`](xref:Xamarin.Forms.LayoutAlignment.Fill)
- [`Expands`](xref:Xamarin.Forms.LayoutOptions.Expands) de type `bool`

Pour plus de commodité, la structure `LayoutOptions` définit également huit champs statiques en lecture seule de type `LayoutOptions` qui englobent toutes les combinaisons des deux propriétés d’instance :

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

La discussion suivante implique une `StackLayout` avec une orientation verticale par défaut. Le `StackLayout` horizontal est analogue.

Pour une `StackLayout`verticale, le paramètre `HorizontalOptions` détermine comment un enfant est positionné horizontalement dans la largeur du `StackLayout`. Un paramètre `Alignment` de `Start`, `Center`ou `End` provoque l’incontrainte horizontale de l’enfant. L’enfant détermine sa propre largeur et est positionné à gauche, au centre ou à droite du `StackLayout`. Avec l’option `Fill`, l’enfant est contraint horizontalement et remplit la largeur du `StackLayout`.

Pour une `StackLayout`verticale, chaque enfant est sans contrainte verticalement et obtient un emplacement vertical en fonction de la hauteur de l’enfant, auquel cas le paramètre de `VerticalOptions` n’est pas pertinent.

Si le `StackLayout` vertical lui-même n’est pas contraint&mdash;si son paramètre `VerticalOptions` est `Start`, `Center`ou `End`, la hauteur du `StackLayout` est la hauteur totale de ses enfants.

Toutefois, si le `StackLayout` vertical est soumis à une restriction verticale&mdash;si son paramètre de `VerticalOptions` est `Fill`&mdash;, la hauteur du `StackLayout` sera la hauteur de son conteneur, qui peut être supérieure à la hauteur totale de ses enfants. Si tel est le cas, et si au moins un enfant a un paramètre `VerticalOptions` avec un indicateur de `Expands` de `true`, l’espace supplémentaire dans le `StackLayout` est également alloué entre tous ces enfants avec un indicateur `Expands` de `true`. La hauteur totale des enfants est alors égale à la hauteur de la `StackLayout`et la partie `Alignment` du paramètre `VerticalOptions` détermine le positionnement vertical de l’enfant dans son emplacement.

Cela est illustré dans l’exemple [**VerticalOptionsDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/VerticalOptionsDemo) .

## <a name="frame-and-boxview"></a>Frame et BoxView

Ces deux vues rectangulaires sont souvent utilisées à des fins de présentation.

La vue [`Frame`](xref:Xamarin.Forms.Frame) affiche un cadre rectangulaire autour d’une autre vue, qui peut être une disposition telle que `StackLayout`. `Frame` hérite d’une propriété [`Content`](xref:Xamarin.Forms.ContentView.Content) de [`ContentView`](xref:Xamarin.Forms.ContentView) que vous avez définie sur la vue à afficher dans le `Frame`. La `Frame` est transparente par défaut. Définissez les trois propriétés suivantes pour personnaliser l’apparence du frame :

- Propriété [`OutlineColor`](xref:Xamarin.Forms.Frame.OutlineColor) pour la rendre visible. Il est courant de définir `OutlineColor` sur `Color.Accent` lorsque vous ne connaissez pas le modèle de couleurs sous-jacent.
- La propriété [`HasShadow`](xref:Xamarin.Forms.Frame.HasShadow) peut être définie sur `true` pour afficher une ombre noire sur les appareils iOS.
- Affectez à la propriété [`Padding`](xref:Xamarin.Forms.Layout.Padding) une valeur `Thickness` pour conserver un espace entre le frame et le contenu du frame. La valeur par défaut est 20 unités sur tous les côtés.

Le `Frame` a des valeurs par défaut `HorizontalOptions` et `VerticalOptions` de `LayoutOptions.Fill`, ce qui signifie que le `Frame` remplira son conteneur. Avec d’autres paramètres, la taille du `Frame` est basée sur la taille de son contenu.

Le `Frame` est illustré dans l’exemple [**FramedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FramedText) .

La [`BoxView`](xref:Xamarin.Forms.BoxView) affiche une zone rectangulaire de couleur spécifiée par sa propriété [`Color`](xref:Xamarin.Forms.BoxView.Color) .

Si le `BoxView` est restreint (ses propriétés `HorizontalOptions` et `VerticalOptions` ont leurs paramètres par défaut `LayoutOptions.Fill`), le `BoxView` remplit l’espace disponible. Si la `BoxView` n’est pas contrainte (avec les paramètres `HorizontalOptions` et `LayoutOptions` de `Start`, `Center`ou `End`), elle a une dimension par défaut de 40 unités carrées. Un `BoxView` peut être contraint dans une dimension et sans contrainte dans l’autre.

Souvent, vous définissez les propriétés [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) et [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) de `BoxView` pour lui attribuer une taille spécifique. Cela est illustré par l’exemple [**SizedBoxView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/SizedBoxView) .

Vous pouvez utiliser plusieurs instances de `StackLayout` pour combiner une `BoxView` et plusieurs instances de `Label` dans une `Frame` pour afficher une couleur particulière, puis mettre chacune de ces vues dans une `StackLayout` dans une `ScrollView` pour créer la liste attrayante de couleurs illustré dans l’exemple [**ColorBlocks**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorBlocks) :

[![Capture d’écran triple des blocs de couleur](images/ch04fg11-small.png "Liste de couleurs")](images/ch04fg11-large.png#lightbox "Liste de couleurs")

## <a name="a-scrollview-in-a-stacklayout"></a>Un ScrollView dans un StackLayout ?

Le fait de placer un `StackLayout` dans un `ScrollView` est courant, mais le fait de placer un `ScrollView` dans une `StackLayout` est également parfois pratique. En théorie, cela ne devrait pas être possible, car les enfants d’un `StackLayout` vertical ne sont pas restreints verticalement. Mais un `ScrollView` doit être restreint verticalement. Elle doit avoir une hauteur spécifique pour pouvoir déterminer ensuite la taille de son enfant pour le défilement.

L’astuce consiste à attribuer à l' `ScrollView` enfant du `StackLayout` un paramètre `VerticalOptions` de `FillAndExpand`. Cela est illustré dans l’exemple [**BlackCat**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCat) .

L’exemple **BlackCat** montre également comment définir des ressources de programme incorporées dans la bibliothèque partagée et y accéder. Cela peut également être accompli avec les SAP (Shared Asset Projects), mais le processus est un peu plus délicat, comme le montre l’exemple [**BlackCatSap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCatSap) .

## <a name="related-links"></a>Liens connexes

- [Chapitre 4 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch04-Apr2016.pdf)
- [Exemples du chapitre 4](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)
- [Exemples du F# chapitre 4](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FS)
- [StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md)
- [ScrollView](~/xamarin-forms/user-interface/layouts/scroll-view.md)
- [BoxView](~/xamarin-forms/user-interface/boxview.md)
