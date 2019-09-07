---
title: Résumé du chapitre 21. Transformations
description: 'Création d’Mobile Apps avec Xamarin. Forms : Résumé du chapitre 21. Transformations'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3642F112-C7FA-4A74-9000-F9087BA89AD9
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 40c091d0c5042d172108709f89774e41e9339d4b
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760582"
---
# <a name="summary-of-chapter-21-transforms"></a>Résumé du chapitre 21. Transformations

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)

Xamarin.Forms s’affiche sur l’écran dans un emplacement et une taille déterminée par son parent, qui est généralement un `Layout` ou `Layout<View>` dérivé. Le *transformer* est une fonctionnalité de Xamarin.Forms qui peut modifier cet emplacement, taille ou même l’orientation.

Xamarin.Forms prend en charge trois types de transformations :

- *Traduction* &mdash; déplacer un élément horizontalement ou verticalement
- *Mise à l’échelle* &mdash; modifier la taille d’un élément
- *Rotation* &mdash; activer un élément autour d’un point ou d’un axe

Dans Xamarin.Forms, la mise à l’échelle est Isotrope ; elle affecte la largeur et la hauteur uniforme. Rotation est pris en charge à la fois sur la surface à deux dimensions de l’écran et dans l’espace 3D. Il existe aucune transformation d’inclinaison (ou élevée) et aucune transformation de matrice généralisée.

Transformations sont prises en charge avec huit propriétés de type `double` défini par le `VisualElement` classe :

- [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)
- [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)
- [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)
- [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)
- [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX)
- [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)
- [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX)
- [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY)

Toutes ces propriétés sont assorties de propriétés pouvant être liées. Ils peuvent être la cible de liaison de données et un style. [**Chapitre 22. Animation** ](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter22.md) montre comment ces propriétés peuvent être animées, mais certains exemples de ce chapitre indiquent comment vous pouvez animer à l’aide de Xamarin.Forms [minuteur](~/xamarin-forms/platform/device.md#devicestarttimer).

Transformer les propriétés affectent uniquement la façon dont l’élément est rendu et faire *pas* affectent la façon dont l’élément est perçu dans mise en page.

## <a name="the-translation-transform"></a>La transformation de traduction

Des valeurs différentes de zéro de la [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX) et [ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY) propriétés déplacer un élément horizontalement ou verticalement.

Le [ **TranslationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TranslationDemo) programme vous permet de faire des essais avec ces propriétés avec deux `Slider` éléments qui contrôlent la `TranslationX` et `TranslationY` propriétés d’un `Frame`. La transformation affecte également tous les enfants de cet `Frame`.

### <a name="text-effects"></a>Effets de texte

Une utilisation courante des propriétés de la traduction est pour décaler légèrement le rendu du texte. Cela est illustré dans le [ **TextOffsets** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TextOffsets) exemple :

[![Capture d’écran de triple des Offsets de texte](images/ch21fg03-small.png "texte Offsets")](images/ch21fg03-large.png#lightbox "Offsets du texte")

Un autre effet consiste à rendre plusieurs copies d’un `Label` pour ressembler à un bloc 3D, comme illustré dans le [ **BlockText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BlockText) exemple.

### <a name="jumps-and-animations"></a>Sauts et animations

Le [ **ButtonJump** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonJump) exemple utilise la traduction pour déplacer un `Button` chaque fois qu’il est activé par un clic, mais son principal objectif est de démontrer que le `Button` reçoit l’entrée d’utilisateur à l’emplacement où le bouton est restitué.

Le [ **ButtonGlide** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonGlide) exemple est similaire, mais utilise une horloge pour animer la `Button` d’un point vers un autre.

## <a name="the-scale-transform"></a>La transformation d’échelle

Le [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) transformation peut augmenter ou diminuer la taille rendue de l’élément. La valeur par défaut est 1. La valeur 0 entraîne l’élément à être invisible. Valeurs négatives, l’élément semblent être pivotée de 180 degrés. Le `Scale` propriété n’affecte pas la `Width` ou `Height` propriétés de l’élément. Ces valeurs restent les mêmes.

Vous pouvez expérimenter la `Scale` à l’aide de la propriété le [ **SimpleScaleDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/SimpleScaleDemo) exemple.

Le [ **ButtonScaler** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonScaler) exemple illustre la différence entre l’animation de la `Scale` propriété d’un `Button` et animer le `FontSize` propriété. Le `FontSize` propriété affecte la `Button` est perçu dans disposition ; le `Scale` propriété n’a pas.

Le [ **ScaleToSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ScaleToSize) exemple calcule un `Scale` propriété qui est appliquée à un `Label` élément pour le rendre aussi grand que possible tout en adaptant toujours au sein de la page.

### <a name="anchoring-the-scale"></a>Ancrage de la mise à l’échelle

Les éléments mis à l’échelle dans les trois exemples précédents ont tous les augmenter ou réduire la taille par rapport au centre de l’élément. En d’autres termes, l’élément augmente ou diminue en taille identique dans toutes les directions. Seul le point au centre de l’élément reste dans le même emplacement pendant la mise à l’échelle.

Vous pouvez modifier le centre de la mise à l’échelle en définissant le [ `AnchorX` ](xref:Xamarin.Forms.VisualElement.AnchorX) et [ `AnchorY` ](xref:Xamarin.Forms.VisualElement.AnchorY) propriétés. Ces propriétés sont par rapport à l’élément lui-même. Pour `AnchorX`, la valeur 0 fait référence à gauche de l’élément et 1 fait référence à la partie droite. De même pour `AnchorY`, 0 est la partie supérieure et 1 est bas. Les deux propriétés ont des valeurs par défaut de 0.5, qui est le centre.

Le [ **AnchoredScaleDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/AnchoredScaleDemo) exemple vous permet de faire des essais avec la `AnchorX` et `AnchorY` propriétés ainsi que le `Scale` propriété.

Sur iOS, à l’aide de valeurs non défaut de `AnchorX` et `AnchorY` propriétés est généralement pas compatible avec les changements d’orientation de téléphone.

## <a name="the-rotation-transform"></a>La transformation de rotation

Le [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) propriété est spécifiée en degrés et indique une rotation autour d’un point de l’élément défini par `AnchorX` et `AnchorY`. Le [ **PlaneRotationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/PlaneRotationDemo) vous permet de faire des essais avec ces trois propriétés.

### <a name="rotated-text-effects"></a>Effets de texte pivoté

Le [ **BoxViewCircle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewCircle) exemple illustre les calculs nécessaires pour dessiner un cercle à l’aide de 64 minuscule paysage `BoxView` éléments.

Le [ **RotatedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/RotatedText) exemple affiche plusieurs `Label` éléments avec la même chaîne de texte pivoté apparaît comme membres spokes.

Le [ **CircularText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/CircularText) exemple affiche une chaîne de texte qui apparaît à la ligne dans un cercle.

### <a name="an-analog-clock"></a>Une horloge analogique

Le [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) bibliothèque contient un [ `AnalogClockViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnalogClockViewModel.cs) classe qui calcule les angles pour entre les mains d’une horloge. Pour éviter les dépendances de plateforme dans le ViewModel, la classe utilise `Task.Delay` au lieu d’un minuteur pour rechercher un nouveau `DateTime` valeur.

Également inclus dans **Xamarin.FormsBook.Toolkit** est un [ `SecondTickConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondTickConverter.cs) classe qui implémente `IValueConverter` et permet d’arrondir un angle deuxième à la seconde près.

Le [ **MinimalBoxViewClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/MinimalBoxViewClock) utilise trois rotation `BoxView` éléments pour dessiner une horloge analogique.

Le [ **BoxViewClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewClock) utilise `BoxView` pour les graphiques plus étendues, y compris les graduations marque autour de la face de l’horloge et remet autrement faire pivoter une distance peu à partir de leurs principaux :

[![Capture d’écran triple d’horloge de BoxView](images/ch21fg17-small.png "cadran analogique")](images/ch21fg17-large.png#lightbox "cadran analogique")

En outre un [ `SecondBackEaseConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondBackEaseConverter.cs) classe **Xamarin.FormsBook.Toolkit** provoque la seconde aiguille apparaisse sur ressortir un peu avant de passer à l’avance, puis à les replacer dans sa position correcte.

### <a name="vertical-sliders"></a>Curseurs verticales ?

Le [ **VerticalSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/VerticalSliders) exemple montre que `Slider` éléments peut être pivotées de 90 degrés et fonctionnent toujours. Toutefois, il est difficile de positionner ces paysage `Slider` éléments, car dans la mise en page ils toujours apparaître à l’horizontale.

## <a name="3d-ish-rotations"></a>Rotations qui concerne 3D

Le [ `RotationX` ](xref:Xamarin.Forms.VisualElement.RotationX) propriété s’affiche pour faire pivoter un élément autour d’un axe des abscisses 3D afin que le haut et le bas de l’élément semblent déplacer vers ou à partir de la visionneuse. De même, le [ `RotationY` ](xref:Xamarin.Forms.VisualElement.RotationY) semble faire pivoter un élément autour de l’axe des ordonnées pour rendre les côtés gauche et droit de l’élément semblent à déplacer vers ou à partir de la visionneuse.

Le `AnchorX` propriété affecte `RotationY` mais pas `RotationX`. Le `AnchorY` propriété affecte `RotationX` mais pas `RotationY`. Vous pouvez expérimenter la [ **ThreeDeeRotationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ThreeDeeRotationDemo) exemple pour Explorer les interactions de ces propriétés.

Le système de coordonnées 3D impliqué par Xamarin.Forms est gaucher. Si vous pointez le pouce de votre main gauche dans la direction de l’augmentation de X coordonnées coordonnées (à droite) et votre doigt intermédiaire dans la direction de croissant Y (points vers le bas), puis votre curseur dans le sens d’augmentation de coordonnées Z (hors de l’écran).

En outre, pour tous les trois axes, si vous pointez votre curseur gauche dans la direction de l’augmentation des valeurs, puis la courbe de vos doigts indique la direction de rotation des angles de rotation positives.

## <a name="related-links"></a>Liens connexes

- [Chapitre 21 de texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch21-Apr2016.pdf)
- [Exemples de chapitre 21](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)
