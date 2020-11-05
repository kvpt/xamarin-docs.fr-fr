---
title: Résumé du chapitre 27. Renderers personnalisés
description: 'Création d’Mobile Apps avec Xamarin.Forms : Résumé du chapitre 27. Renderers personnalisés'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 49961953-9336-4FD4-A42F-6D9B05FF52E7
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6b117688e1bf85266c274a44e6a4b1fda1ef5134
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375146"
---
# <a name="summary-of-chapter-27-custom-renderers"></a>Résumé du chapitre 27. Renderers personnalisés

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)

> [!NOTE]
> Ce livre a été publié à la printemps de 2016 et n’a pas été mis à jour depuis. Le livre reste très utile, mais certains d’entre eux sont obsolètes et certaines rubriques ne sont plus entièrement correctes ou complètes.

Un Xamarin.Forms élément tel que `Button` est restitué avec un bouton spécifique à la plateforme encapsulé dans une classe nommée `ButtonRenderer` .  Voici la [version IOS de `ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/Renderers/ButtonRenderer.cs), la [version Android de `ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/Renderers/ButtonRenderer.cs)et la [ `ButtonRenderer` version UWP de ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.UAP/ButtonRenderer.cs).

Ce chapitre explique comment vous pouvez écrire vos propres convertisseurs pour créer des vues personnalisées qui correspondent à des objets spécifiques à la plateforme.

## <a name="the-complete-class-hierarchy"></a>Hiérarchie de classes complète

Il y a quatre assemblys qui contiennent le Xamarin.Forms code spécifique à la plateforme.
Vous pouvez afficher la source sur GitHub à l’aide des liens suivants :

- [**Xamarin.Forms. Plateforme**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform) (très petite)
- [**Xamarin.Forms. Platform. iOS**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.iOS)
- [**Xamarin.Forms. Platform. Android**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.Android)
- [**Xamarin.Forms. Platform. UAP**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.UAP)

> [!NOTE]
> Les `WinRT` assemblys mentionnés dans le livre ne font plus partie de cette solution.

L’exemple [**PlatformClassHierarchy**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/PlatformClassHierarchy) affiche une hiérarchie de classes pour les assemblys qui sont valides pour la plateforme en cours d’exécution.

Vous remarquerez une classe importante nommée `ViewRenderer` . Il s’agit de la classe à partir de laquelle vous dérivez lors de la création d’un convertisseur spécifique à la plateforme. Il existe dans trois versions différentes, car il est lié au système de vue de la plateforme cible :

IOS [`ViewRenderer<TView, TNativeView>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/ViewRenderer.cs#L25) a des arguments génériques :

- `TView` soumis à [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeView` soumis à [`UIKit.UIView`](xref:UIKit.UIView)

Android [`ViewRenderer<TView, TNativeView>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/ViewRenderer.cs#L17) a des arguments génériques :

- `TView` soumis à [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeView` soumis à [`Android.Views.View`](xref:Android.Views.View)

La UWP [`ViewRenderer<TElement, TNativeElement>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.UAP/ViewRenderer.cs#L6) a des arguments génériques nommés différemment :

- `TElement` soumis à [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeElement` soumis à [`Windows.UI.Xaml.FrameworkElement`](/uwp/api/Windows.UI.Xaml.FrameworkElement)

Lors de l’écriture d’un convertisseur, vous allez dériver une classe de `View` , puis écrire plusieurs `ViewRenderer` classes, une pour chaque plateforme prise en charge. Chaque implémentation spécifique à la plateforme fait référence à une classe native qui dérive du type que vous spécifiez comme `TNativeView` `TNativeElement` paramètre ou.

## <a name="hello-custom-renderers"></a>Bonjour, les convertisseurs personnalisés !

Le programme [**HelloRenderers**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/HelloRenderers) fait référence à une vue personnalisée nommée `HelloView` dans sa [`App`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/App.cs) classe.

La [`HelloView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/HelloView.cs) classe est incluse dans le projet **HelloRenderers** et dérive simplement de `View` .

La [`HelloViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.iOS/HelloViewRenderer.cs) classe du projet **HelloRenderers. iOS** dérive de `ViewRenderer<HelloView, UILabel>` . Dans la `OnElementChanged` substitution, il crée une iOS `UILabel` et des appels natifs `SetNativeControl` .

La [`HelloViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.Droid/HelloViewRenderer.cs) classe du projet **HelloRenderers. Droid** dérive de `ViewRenderer<HelloView, TextView>` . Dans le `OnElementChanged` remplacement, il crée un Android `TextView` et appelle `SetNativeControl` .

La [`HelloViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.UWP/HelloViewRenderer.cs) classe de **HELLORENDERERS. UWP** et d’autres projets Windows dérive de `ViewRenderer<HelloView, TextBlock>` . Dans la `OnElementChanged` substitution, il crée une fenêtre `TextBlock` et appelle `SetNativeControl` .

Tous les `ViewRenderer` dérivés contiennent un `ExportRenderer` attribut au niveau de l’assembly qui associe la `HelloView` classe à la `HelloViewRenderer` classe particulière. Voici comment Xamarin.Forms localise les convertisseurs dans les projets de plateforme individuels :

[![Capture d’écran triple de la vue Hello](images/ch27fg02-small.png "Renderers personnalisés")](images/ch27fg02-large.png#lightbox "Renderers personnalisés")

## <a name="renderers-and-properties"></a>Convertisseurs et propriétés

L’ensemble de convertisseurs suivant implémente le dessin ellipse et se trouve dans les différents projets de la solution [**Xamarin.Forms book. Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) .

La [`EllipseView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/EllipseView.cs) classe se trouve dans la plateforme **Xamarin.Forms book. Platform** . La classe est semblable à `BoxView` et définit simplement une propriété unique : `Color` de type `Color` .

Les convertisseurs peuvent transférer les valeurs de propriété définies sur un `View` à l’objet natif en substituant la `OnElementPropertyChanged` méthode dans le convertisseur. Au sein de cette méthode (et dans la plupart du convertisseur), deux propriétés sont disponibles :

- `Element`, l' Xamarin.Forms élément
- `Control`, l’affichage natif ou le widget ou l’objet contrôle

Les types de ces propriétés sont déterminés par les paramètres génériques à `ViewRenderer` . Dans cet exemple, `Element` est de type `EllipseView` .

La `OnElementPropertyChanged` substitution peut donc transférer la `Color` valeur de `Element` à l' `Control` objet natif, probablement avec un type de conversion. Les trois convertisseurs sont les suivants :

- iOS : [`EllipseViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseViewRenderer.cs) , qui utilise une [`EllipseUIView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseUIView.cs) classe pour l’ellipse.
- Android : [`EllipseViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseViewRenderer.cs) , qui utilise une [`EllipseDrawableView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseDrawableView.cs) classe pour l’ellipse.
- UWP : [`EllipseViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/EllipseViewRenderer.cs) , qui peut utiliser la classe Windows native [`Ellipse`](/uwp/api/Windows.UI.Xaml.Shapes.Ellipse) .

La classe [**EllipseDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/EllipseDemo) affiche plusieurs de ces `EllipseView` objets :

[![Capture d’écran triple de la démo d’ellipse](images/ch27fg03-small.png "Convertisseurs personnalisés EllipseView")](images/ch27fg03-large.png#lightbox "Convertisseurs personnalisés EllipseView")

Le [**BouncingBall**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/BouncingBall) rebondit d’un `EllipseView` côté de l’écran.

## <a name="renderers-and-events"></a>Convertisseurs et événements

Il est également possible que les convertisseurs génèrent indirectement des événements. La [`StepSlider`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/StepSlider.cs) classe est semblable à la normale Xamarin.Forms `Slider` , mais permet de spécifier un nombre d’étapes discrètes entre les `Minimum` `Maximum` valeurs et.

Les trois convertisseurs sont les suivants :

- Libéré [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/StepSliderRenderer.cs)
- Tablette [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/StepSliderRenderer.cs)
- UWP [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/StepSliderRenderer.cs)

Les convertisseurs détectent les modifications apportées au contrôle natif, puis appellent `SetValueFromRenderer` , qui fait référence à une propriété pouvant être liée définie dans le `StepSlider` , une modification dans laquelle déclenche `StepSlider` un `ValueChanged` événement.

L’exemple [**StepSliderDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/StepSliderDemo) illustre ce nouveau curseur.

## <a name="related-links"></a>Liens connexes

- [Chapitre 27 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf)
- [Exemples du chapitre 27](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)
- [Renderers personnalisés](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
