---
title: Résumé du chapitre 27. Renderers personnalisés
description: 'Création d’applications mobiles avec Xamarin.Forms : résumé du chapitre 27. Renderers personnalisés'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 49961953-9336-4FD4-A42F-6D9B05FF52E7
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: fd4014fa4db4e90596c100d454cf0467512240a4
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "70760502"
---
# <a name="summary-of-chapter-27-custom-renderers"></a>Résumé du chapitre 27. Renderers personnalisés

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)

> [!NOTE] 
> Notes sur cette page indiquent des zones où Xamarin.Forms est différente de la matière présentée dans le livre.

Un élément Xamarin. Forms, tel que `Button`, est affiché avec un bouton spécifique à la plateforme encapsulé dans une classe nommée `ButtonRenderer`.  Voici la [version IOS de `ButtonRenderer`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/Renderers/ButtonRenderer.cs), la [version Android de `ButtonRenderer`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/Renderers/ButtonRenderer.cs)et la [version UWP de `ButtonRenderer`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.UAP/ButtonRenderer.cs).

Ce chapitre explique comment vous pouvez écrire vos propres convertisseurs pour créer des vues personnalisées qui mappent aux objets spécifiques à la plateforme.

## <a name="the-complete-class-hierarchy"></a>La hiérarchie de classe complète

Il existe quatre assemblys qui contiennent le code spécifique à la plateforme Xamarin.Forms.
Vous pouvez afficher la source sur GitHub à l’aide de ces liens :

- [**Xamarin. Forms. Platform**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform) (très petit)
- [**Xamarin. Forms. Platform. iOS**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.iOS)
- [**Xamarin. Forms. Platform. Android**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.Android)
- [**Xamarin. Forms. Platform. UAP**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.UAP)

> [!NOTE]
> Les assemblys `WinRT` mentionnés dans le livre ne font plus partie de cette solution. 

L’exemple [**PlatformClassHierarchy**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/PlatformClassHierarchy) affiche une hiérarchie de classes pour les assemblys qui sont valides pour la plateforme en cours d’exécution.

Vous remarquerez une classe importante nommée `ViewRenderer`. Il s’agit de la classe que vous dérivez de lors de la création d’un convertisseur spécifique à la plateforme. Il existe en trois versions différentes, car il est lié au système de vue de la plateforme cible :

Le [`ViewRenderer<TView, TNativeView>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/ViewRenderer.cs#L25) iOS a des arguments génériques :

- `TView` limités à [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeView` limités à [`UIKit.UIView`](xref:UIKit.UIView)

Le [`ViewRenderer<TView, TNativeView>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/ViewRenderer.cs#L17) Android a des arguments génériques :

- `TView` limités à [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeView` limités à [`Android.Views.View`](xref:Android.Views.View)

Le [`ViewRenderer<TElement, TNativeElement>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.UAP/ViewRenderer.cs#L6) UWP a des arguments génériques nommés différemment :

- `TElement` limités à [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeElement` limités à [`Windows.UI.Xaml.FrameworkElement`](/uwp/api/Windows.UI.Xaml.FrameworkElement)

Lors de l’écriture d’un convertisseur, vous allez dériver une classe de `View`, puis écrire plusieurs classes `ViewRenderer`, une pour chaque plateforme prise en charge. Chaque implémentation spécifique à la plateforme fait référence à une classe native qui dérive du type que vous spécifiez comme paramètre `TNativeView` ou `TNativeElement`.

## <a name="hello-custom-renderers"></a>Hello, renderers personnalisés !

Le programme [**HelloRenderers**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/HelloRenderers) fait référence à une vue personnalisée nommée `HelloView` dans sa classe [`App`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/App.cs) .

La classe [`HelloView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/HelloView.cs) est incluse dans le projet **HelloRenderers** et dérive simplement de `View`.

La classe [`HelloViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.iOS/HelloViewRenderer.cs) dans le projet **HelloRenderers. iOS** dérive de `ViewRenderer<HelloView, UILabel>`. Dans le `OnElementChanged` remplacement, il crée une `UILabel` iOS native et appelle `SetNativeControl`.

La classe [`HelloViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.Droid/HelloViewRenderer.cs) dans le projet **HelloRenderers. Droid** dérive de `ViewRenderer<HelloView, TextView>`. Dans le `OnElementChanged` remplacement, il crée un `TextView` Android et appelle `SetNativeControl`.

La classe [`HelloViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.UWP/HelloViewRenderer.cs) dans **HelloRenderers. UWP** et d’autres projets Windows dérivent de `ViewRenderer<HelloView, TextBlock>`. Dans le `OnElementChanged` remplacement, il crée un `TextBlock` Windows et appelle `SetNativeControl`.

Tous les dérivés `ViewRenderer` contiennent un attribut `ExportRenderer` au niveau de l’assembly qui associe la classe `HelloView` à la classe `HelloViewRenderer` particulière. Voici comment Xamarin.Forms localise les convertisseurs dans les projets de plateforme individuels :

[![Capture d’écran triple de la vue Hello](images/ch27fg02-small.png "Renderers personnalisés")](images/ch27fg02-large.png#lightbox "Renderers personnalisés")

## <a name="renderers-and-properties"></a>Convertisseurs et des propriétés

L’ensemble suivant de convertisseurs implémente le dessin ellipse et se trouve dans les différents projets de la solution [**Xamarin. FormsBook. Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) .

La classe [`EllipseView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/EllipseView.cs) se trouve dans la plateforme **Xamarin. FormsBook. Platform** . La classe est semblable à `BoxView` et définit simplement une propriété unique : `Color` de type `Color`.

Les convertisseurs peuvent transférer les valeurs de propriété définies sur un `View` à l’objet natif en substituant la méthode `OnElementPropertyChanged` dans le convertisseur. Dans cette méthode (et, dans la plupart du convertisseur), deux propriétés sont disponibles :

- `Element`, élément Xamarin. Forms
- `Control`, l’affichage natif ou le widget ou l’objet contrôle

Les types de ces propriétés sont déterminés par les paramètres génériques à `ViewRenderer`. Dans cet exemple, `Element` est de type `EllipseView`.

La substitution de `OnElementPropertyChanged` peut donc transférer la valeur `Color` de la `Element` à l’objet `Control` natif, probablement avec un certain type de conversion. Les convertisseurs de trois sont :

- iOS : [`EllipseViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseViewRenderer.cs), qui utilise une classe [`EllipseUIView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseUIView.cs) pour l’ellipse.
- Android : [`EllipseViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseViewRenderer.cs), qui utilise une classe [`EllipseDrawableView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseDrawableView.cs) pour l’ellipse.
- UWP : [`EllipseViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/EllipseViewRenderer.cs), qui peut utiliser la classe Windows [`Ellipse`](/uwp/api/Windows.UI.Xaml.Shapes.Ellipse) native.

La classe [**EllipseDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/EllipseDemo) affiche plusieurs de ces objets `EllipseView` :

[![Capture d’écran triple de la démo d’ellipse](images/ch27fg03-small.png "Convertisseurs personnalisés EllipseView")](images/ch27fg03-large.png#lightbox "Convertisseurs personnalisés EllipseView")

Le [**BouncingBall**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/BouncingBall) rebondit un `EllipseView` sur les côtés de l’écran.

## <a name="renderers-and-events"></a>Convertisseurs et des événements

Il est également possible pour des convertisseurs générer des événements indirectement. La classe [`StepSlider`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/StepSlider.cs) est semblable à la `Slider` normale Xamarin. Forms, mais permet de spécifier un nombre d’étapes discrètes entre les valeurs `Minimum` et `Maximum`.

Les convertisseurs de trois sont :

- iOS : [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/StepSliderRenderer.cs)
- Android : [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/StepSliderRenderer.cs)
- UWP : [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/StepSliderRenderer.cs)

Les convertisseurs détectent les modifications apportées au contrôle natif, puis appellent `SetValueFromRenderer`, qui fait référence à une propriété pouvant être liée définie dans le `StepSlider`, une modification dans laquelle provoque le déclenchement d’un événement `ValueChanged` par l' `StepSlider`.

L’exemple [**StepSliderDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/StepSliderDemo) illustre ce nouveau curseur.

## <a name="related-links"></a>Liens connexes

- [Chapitre 27 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf)
- [Exemples du chapitre 27](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)
- [Renderers personnalisés](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
