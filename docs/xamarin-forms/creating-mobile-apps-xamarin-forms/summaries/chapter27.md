---
title: Résumé du chapitre 27. Renderers personnalisés
description: 'Création d’applications mobiles avec Xamarin.Forms: Résumé du chapitre 27. Renderers personnalisés'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 49961953-9336-4FD4-A42F-6D9B05FF52E7
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: fd4014fa4db4e90596c100d454cf0467512240a4
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70760502"
---
# <a name="summary-of-chapter-27-custom-renderers"></a>Résumé du chapitre 27. Renderers personnalisés

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)

> [!NOTE] 
> Les notes sur cette page indiquent les zones où Xamarin.Forms a divergé du matériel présenté dans le livre.

Un élément Xamarin.Forms `Button` tel qu’il est rendu avec un bouton `ButtonRenderer`spécifique à la plate-forme encapsulé dans une classe nommée .  Voici la [version iOS de `ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/Renderers/ButtonRenderer.cs), la version Android [de `ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/Renderers/ButtonRenderer.cs), et la version [UWP de `ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.UAP/ButtonRenderer.cs).

Ce chapitre traite de la façon dont vous pouvez écrire vos propres rendus pour créer des vues personnalisées qui cartographient des objets spécifiques à la plate-forme.

## <a name="the-complete-class-hierarchy"></a>La hiérarchie complète des classes

Il existe quatre assemblages qui contiennent le code spécifique à la plate-forme Xamarin.Forms.
Vous pouvez afficher la source sur GitHub à l’aide de ces liens :

- [**Xamarin.Forms.Platform**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform) (très petit)
- [**Xamarin.Forms.Platform.iOS**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.iOS)
- [**Xamarin.Forms.Platform.Android**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.Android)
- [**Xamarin.Forms.Platform.UAP Xamarin.Forms.Platform.UAP Xamarin.Forms.Platform.UAP Xama**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.UAP)

> [!NOTE]
> Les `WinRT` assemblées mentionnées dans le livre ne font plus partie de cette solution. 

[**L’échantillon PlatformClassHierarchy**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/PlatformClassHierarchy) affiche une hiérarchie de classe pour les assemblages valables pour la plate-forme d’exécution.

Vous remarquerez une classe `ViewRenderer`importante nommée . C’est la classe dont vous dérivez lors de la création d’un rendu spécifique à la plate-forme. Il existe dans trois versions différentes, car il est lié au système de vue de la plate-forme cible:

L’iOS [`ViewRenderer<TView, TNativeView>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/ViewRenderer.cs#L25) a des arguments génériques:

- `TView`contrainte à[`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeView`contrainte à[`UIKit.UIView`](xref:UIKit.UIView)

L’Android [`ViewRenderer<TView, TNativeView>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/ViewRenderer.cs#L17) a des arguments génériques:

- `TView`contrainte à[`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeView`contrainte à[`Android.Views.View`](xref:Android.Views.View)

L’UWP [`ViewRenderer<TElement, TNativeElement>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.UAP/ViewRenderer.cs#L6) a nommé différemment les arguments génériques:

- `TElement`contrainte à[`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeElement`contrainte à[`Windows.UI.Xaml.FrameworkElement`](/uwp/api/Windows.UI.Xaml.FrameworkElement)

Lors de l’écriture d’un rendu, `View`vous serez derérier une classe de , puis l’écriture de plusieurs `ViewRenderer` classes, un pour chaque plate-forme prise en charge. Chaque implémentation spécifique à la plate-forme fera référence `TNativeView` `TNativeElement` à une classe native qui dérive du type que vous spécifiez comme le paramètre ou le paramètre.

## <a name="hello-custom-renderers"></a>Bonjour, les rendus personnalisés!

Le programme [**HelloRenderers**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/HelloRenderers) fait `HelloView` référence [`App`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/App.cs) à une vue personnalisée nommée dans sa classe.

La [`HelloView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/HelloView.cs) classe est incluse dans le projet **HelloRenderers** et dérive simplement de `View`.

La [`HelloViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.iOS/HelloViewRenderer.cs) classe dans le projet **HelloRenderers.iOS** dérive de `ViewRenderer<HelloView, UILabel>`. Dans `OnElementChanged` la dérogation, il `UILabel` crée `SetNativeControl`un iOS natif et les appels .

La [`HelloViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.Droid/HelloViewRenderer.cs) classe dans le projet **HelloRenderers.Droid** dérive de `ViewRenderer<HelloView, TextView>`. Dans `OnElementChanged` la dérogation, `TextView` il `SetNativeControl`crée un Android et appelle .

La [`HelloViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.UWP/HelloViewRenderer.cs) classe dans le **HelloRenderers.UWP** et `ViewRenderer<HelloView, TextBlock>`d’autres projets Windows dérive de . Dans `OnElementChanged` la dérogation, `TextBlock` il `SetNativeControl`crée un Windows et des appels .

Tous `ViewRenderer` les dérivés `ExportRenderer` contiennent un attribut au `HelloView` niveau de `HelloViewRenderer` l’assemblage qui associe la classe à la classe particulière. C’est ainsi que Xamarin.Forms localise les rendus dans les projets de plate-forme individuels :

[![Triple capture d’écran de Hello View](images/ch27fg02-small.png "Renderers personnalisés")](images/ch27fg02-large.png#lightbox "Renderers personnalisés")

## <a name="renderers-and-properties"></a>Rendus et propriétés

La prochaine série de rendus implémente le dessin ellipse, et est situé dans les différents projets de la solution [**Xamarin.FormsBook.Platform.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform)

La [`EllipseView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/EllipseView.cs) classe est dans la plate-forme **Xamarin.FormsBook.Platform.** La classe est `BoxView` similaire et définit juste `Color` une `Color`seule propriété: de type .

Les rendus peuvent transférer les `View` valeurs de propriété fixées `OnElementPropertyChanged` sur un objet indigène en l’emportent sur la méthode dans le rendu. Dans cette méthode (et dans la plupart du rendu), deux propriétés sont disponibles :

- `Element`, l’élément Xamarin.Forms
- `Control`, la vue ou le widget ou l’objet de contrôle natifs

Les types de ces propriétés sont `ViewRenderer`déterminés par les paramètres génériques à . Dans cet `Element` exemple, `EllipseView`est de type .

Le `OnElementPropertyChanged` remplacement peut donc `Color` transférer `Element` la valeur `Control` de l’objet natif, probablement avec une sorte de conversion. Les trois rendus sont :

- iOS: [`EllipseViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseViewRenderer.cs), qui [`EllipseUIView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseUIView.cs) utilise une classe pour l’ellipse.
- Android: [`EllipseViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseViewRenderer.cs), qui [`EllipseDrawableView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseDrawableView.cs) utilise une classe pour l’ellipse.
- UWP: [`EllipseViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/EllipseViewRenderer.cs), qui peut [`Ellipse`](/uwp/api/Windows.UI.Xaml.Shapes.Ellipse) utiliser la classe Windows natif.

La classe [**EllipseDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/EllipseDemo) affiche `EllipseView` plusieurs de ces objets :

[![Triple capture d’écran d’Ellipse Demo](images/ch27fg03-small.png "EllipseView Rendus personnalisés")](images/ch27fg03-large.png#lightbox "EllipseView Rendus personnalisés")

Le [**BouncingBall**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/BouncingBall) rebondit sur `EllipseView` les côtés de l’écran.

## <a name="renderers-and-events"></a>Rendus et événements

Il est également possible pour les rendeurs de générer indirectement des événements. La [`StepSlider`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/StepSlider.cs) classe est similaire à la `Slider` normale Xamarin.Forms, mais permet `Minimum` `Maximum` de spécifier un certain nombre d’étapes discrètes entre les valeurs et les valeurs.

Les trois rendus sont :

- Ios:[`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/StepSliderRenderer.cs)
- Android:[`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/StepSliderRenderer.cs)
- Uwp:[`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/StepSliderRenderer.cs)

Les rendus détectent les changements au `SetValueFromRenderer`contrôle indigène, puis appellent , `StepSlider`ce qui fait `StepSlider` référence à `ValueChanged` une propriété liant définie dans le , un changement auquel provoque le feu d’un événement.

[**L’échantillon StepSliderDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/StepSliderDemo) démontre ce nouveau curseur.

## <a name="related-links"></a>Liens connexes

- [Chapitre 27 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf)
- [Échantillons du chapitre 27](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)
- [Rendus personnalisés](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
