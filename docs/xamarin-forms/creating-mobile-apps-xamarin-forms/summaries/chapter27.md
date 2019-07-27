---
title: Résumé du chapitre 27. Renderers personnalisés
description: 'Création d’Mobile Apps avec Xamarin. Forms: Résumé du chapitre 27. Renderers personnalisés'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 49961953-9336-4FD4-A42F-6D9B05FF52E7
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 985510caa78733d1a53aceb41371839f544daa73
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511650"
---
# <a name="summary-of-chapter-27-custom-renderers"></a>Résumé du chapitre 27. Renderers personnalisés

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)

> [!NOTE] 
> Notes sur cette page indiquent des zones où Xamarin.Forms est différente de la matière présentée dans le livre.

Un élément Xamarin.Forms comme `Button` est rendu avec un bouton spécifique à la plateforme encapsulé dans une classe nommée `ButtonRenderer`.  Voici le [version iOS de `ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/Renderers/ButtonRenderer.cs), le [version Android du `ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/Renderers/ButtonRenderer.cs)et le [version UWP de `ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.UAP/ButtonRenderer.cs).

Ce chapitre explique comment vous pouvez écrire vos propres convertisseurs pour créer des vues personnalisées qui mappent aux objets spécifiques à la plateforme.

## <a name="the-complete-class-hierarchy"></a>La hiérarchie de classe complète

Il existe quatre assemblys qui contiennent le code spécifique à la plateforme Xamarin.Forms.
Vous pouvez afficher la source sur GitHub à l’aide de ces liens :

- [**Xamarin.Forms.Platform** ](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform) (très petite)
- [**Xamarin.Forms.Platform.iOS**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.iOS)
- [**Xamarin.Forms.Platform.Android**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.Android)
- [**Xamarin.Forms.Platform.UAP**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.UAP)

> [!NOTE]
> Le `WinRT` mentionnés dans le livre des assemblys ne font plus partie de cette solution. 

Le [ **PlatformClassHierarchy** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/PlatformClassHierarchy) exemple affiche une hiérarchie de classes pour les assemblys qui sont valides pour la plateforme en cours d’exécution.

Vous remarquerez une classe importante nommée `ViewRenderer`. Il s’agit de la classe que vous dérivez de lors de la création d’un convertisseur spécifique à la plateforme. Il existe en trois versions différentes, car il est lié au système de vue de la plateforme cible :

IOS [ `ViewRenderer<TView, TNativeView>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/ViewRenderer.cs#L25) a des arguments génériques :

- `TView` limité à [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeView` limité à [`UIKit.UIView`](xref:UIKit.UIView)

Android [ `ViewRenderer<TView, TNativeView>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/ViewRenderer.cs#L17) a des arguments génériques :

- `TView` limité à [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeView` limité à [`Android.Views.View`](xref:Android.Views.View)

La plateforme Windows universelle [ `ViewRenderer<TElement, TNativeElement>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.UAP/ViewRenderer.cs#L6) a nommé différemment les arguments génériques :

- `TElement` limité à [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeElement` limité à [`Windows.UI.Xaml.FrameworkElement`](/uwp/api/Windows.UI.Xaml.FrameworkElement)

Lorsque vous écrivez un convertisseur, vous serez dérivant une classe de `View`, puis en écrivant plusieurs `ViewRenderer` des classes, une pour chaque plateforme prise en charge. Chaque implémentation propre à la plateforme référencera une classe native qui dérive du type que vous spécifiez comme le `TNativeView` ou `TNativeElement` paramètre.

## <a name="hello-custom-renderers"></a>Hello, renderers personnalisés !

Le [ **HelloRenderers** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/HelloRenderers) programme fait référence à une vue personnalisée nommée `HelloView` dans son [ `App` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/App.cs) classe.

Le [ `HelloView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/HelloView.cs) classe est incluse dans le **HelloRenderers** de projet et dérive simplement de `View`.

Le [ `HelloViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.iOS/HelloViewRenderer.cs) classe dans le **HelloRenderers.iOS** dérive de projet `ViewRenderer<HelloView, UILabel>`. Dans le `OnElementChanged` override, il crée un iOS native `UILabel` et appelle `SetNativeControl`.

Le [ `HelloViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.Droid/HelloViewRenderer.cs) classe dans le **HelloRenderers.Droid** dérive de projet `ViewRenderer<HelloView, TextView>`. Dans le `OnElementChanged` override, il crée un Android `TextView` et appelle `SetNativeControl`.

Le [ `HelloViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.UWP/HelloViewRenderer.cs) classe dans le **HelloRenderers.UWP** et autres projets Windows dérive `ViewRenderer<HelloView, TextBlock>`. Dans le `OnElementChanged` override, il crée un Windows `TextBlock` et appelle `SetNativeControl`.

Tous les le `ViewRenderer` dérivés contiennent un `ExportRenderer` attribut sur le niveau de l’assembly qui associe le `HelloView` classe avec le particulier `HelloViewRenderer` classe. Voici comment Xamarin.Forms localise les convertisseurs dans les projets de plateforme individuels :

[![Capture d’écran triple de Hello vue](images/ch27fg02-small.png "Renderers personnalisés")](images/ch27fg02-large.png#lightbox "Renderers personnalisés")

## <a name="renderers-and-properties"></a>Convertisseurs et des propriétés

L’ensemble suivant de convertisseurs implémente le dessin de l’ellipse et se trouve dans les différents projets de la [ **Xamarin.FormsBook.Platform** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) solution.

Le [ `EllipseView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/EllipseView.cs) classe se trouve dans le **Xamarin.FormsBook.Platform** plateforme. La classe est similaire à `BoxView` et définit qu’une seule propriété : `Color` de type `Color`.

Les convertisseurs peuvent transférer des valeurs de propriétés définies sur un `View` à l’objet natif en substituant le `OnElementPropertyChanged` méthode dans le convertisseur. Dans cette méthode (et, dans la plupart du convertisseur), deux propriétés sont disponibles :

- `Element`, l’élément Xamarin.Forms
- `Control`, le mode natif ou l’objet de widget ou de contrôle

Les types de ces propriétés sont déterminés par les paramètres génériques à `ViewRenderer`. Dans cet exemple, `Element` est de type `EllipseView`.

Le `OnElementPropertyChanged` remplacement peut transférer par conséquent la `Color` valeur de la `Element` natif `Control` objet, probablement avec un type de conversion. Les convertisseurs de trois sont :

- iOS : [ `EllipseViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseViewRenderer.cs), qui utilise un [ `EllipseUIView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseUIView.cs) classe pour l’ellipse.
- Android : [ `EllipseViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseViewRenderer.cs), qui utilise un [ `EllipseDrawableView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseDrawableView.cs) classe pour l’ellipse.
- UWP : [ `EllipseViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/EllipseViewRenderer.cs), qui peut utiliser le Windows native [ `Ellipse` ](/uwp/api/Windows.UI.Xaml.Shapes.Ellipse) classe.

Le [ **EllipseDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/EllipseDemo) classe affiche plusieurs de ces `EllipseView` objets :

[![Capture d’écran triple de démonstration de l’Ellipse](images/ch27fg03-small.png "Renderers personnalisés de EllipseView")](images/ch27fg03-large.png#lightbox "EllipseView personnalisé convertisseurs")

Le [ **BouncingBall** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/BouncingBall) rebondit un `EllipseView` désactivé les côtés de l’écran.

## <a name="renderers-and-events"></a>Convertisseurs et des événements

Il est également possible pour des convertisseurs générer des événements indirectement. Le [ `StepSlider` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/StepSlider.cs) classe est semblable à la normale Xamarin.Forms `Slider` mais permet de spécifier un nombre d’étapes distinctes entre le `Minimum` et `Maximum` valeurs.

Les convertisseurs de trois sont :

- iOS : [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/StepSliderRenderer.cs)
- Android : [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/StepSliderRenderer.cs)
- UWP : [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/StepSliderRenderer.cs)

Les convertisseurs de détecter les modifications apportées au contrôle natif, puis appelez `SetValueFromRenderer`, qui fait référence à une propriété définie dans le `StepSlider`, une modification apportée à ce qui conduit le `StepSlider` pour déclencher un `ValueChanged` événement.

Le [ **StepSliderDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/StepSliderDemo) exemple illustre ce nouveau curseur.



## <a name="related-links"></a>Liens connexes

- [Chapitre 27 de texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf)
- [Exemples de chapitre 27](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)
- [Renderers personnalisés](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
