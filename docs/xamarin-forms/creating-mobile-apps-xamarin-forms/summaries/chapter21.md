---
title: Résumé du chapitre 21. Transforms
description: 'Création d’applications mobiles avec Xamarin.Forms : résumé du chapitre 21. Transforms'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3642F112-C7FA-4A74-9000-F9087BA89AD9
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 40c091d0c5042d172108709f89774e41e9339d4b
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "70760582"
---
# <a name="summary-of-chapter-21-transforms"></a>Résumé du chapitre 21. Transforms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)

Un affichage Xamarin. Forms apparaît à l’écran dans un emplacement et une taille déterminés par son parent, qui est généralement un `Layout` ou `Layout<View>` dérivé. La *transformation* est une fonctionnalité Xamarin. Forms qui peut modifier cet emplacement, sa taille ou même l’orientation.

Xamarin.Forms prend en charge trois types de transformations :

- *Translation* &mdash; déplacer un élément horizontalement ou verticalement
- Mettre à l' *échelle* &mdash; modifier la taille d’un élément
- *Rotation* &mdash; transformer un élément autour d’un point ou d’un axe

Dans Xamarin.Forms, la mise à l’échelle est Isotrope ; elle affecte la largeur et la hauteur uniforme. Rotation est pris en charge à la fois sur la surface à deux dimensions de l’écran et dans l’espace 3D. Il existe aucune transformation d’inclinaison (ou élevée) et aucune transformation de matrice généralisée.

Les transformations sont prises en charge avec huit propriétés de type `double` définies par la classe `VisualElement` :

- [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)
- [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)
- [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)
- [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)
- [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX)
- [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)
- [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX)
- [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY)

Toutes ces propriétés sont assorties de propriétés pouvant être liées. Ils peuvent être la cible de liaison de données et un style. [**Chapitre 22. L’animation**](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter22.md) montre comment ces propriétés peuvent être animées, mais certains exemples de ce chapitre montrent comment vous pouvez les animer à l’aide du [minuteur](~/xamarin-forms/platform/device.md#devicestarttimer)Xamarin. Forms.

Les propriétés de transformation affectent uniquement le mode de rendu de l’élément et n’affectent *pas* la manière dont l’élément est perçu dans la disposition.

## <a name="the-translation-transform"></a>La transformation de traduction

Les valeurs autres que zéro des propriétés [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) et [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) décalent un élément horizontalement ou verticalement.

Le programme [**TranslationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TranslationDemo) vous permet d’expérimenter ces propriétés avec deux éléments `Slider` qui contrôlent les propriétés `TranslationX` et `TranslationY` d’un `Frame`. La transformation affecte également tous les enfants de ce `Frame`.

### <a name="text-effects"></a>Effets de texte

Une utilisation courante des propriétés de la traduction est pour décaler légèrement le rendu du texte. Cela est illustré dans l’exemple [**TextOffsets**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TextOffsets) :

[![Capture d’écran triple des décalages de texte](images/ch21fg03-small.png "Décalages de texte")](images/ch21fg03-large.png#lightbox "Décalages de texte")

Un autre effet consiste à afficher plusieurs copies d’un `Label` pour ressembler à un bloc 3D, comme illustré dans l’exemple [**BlockText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BlockText) .

### <a name="jumps-and-animations"></a>Sauts et animations

L’exemple [**ButtonJump**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonJump) utilise la traduction pour déplacer un `Button` chaque fois qu’il est frappé, mais l’objectif principal est de démontrer que le `Button` reçoit l’entrée d’utilisateur à l’emplacement où le bouton est rendu.

L’exemple [**ButtonGlide**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonGlide) est similaire, mais utilise un minuteur pour animer le `Button` d’un point à un autre.

## <a name="the-scale-transform"></a>La transformation d’échelle

La transformation [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) peut augmenter ou diminuer la taille rendue de l’élément. La valeur par défaut est 1. La valeur 0 entraîne l’élément à être invisible. Valeurs négatives, l’élément semblent être pivotée de 180 degrés. La propriété `Scale` n’affecte pas les propriétés `Width` ou `Height` de l’élément. Ces valeurs restent les mêmes.

Vous pouvez expérimenter la propriété `Scale` à l’aide de l’exemple [**SimpleScaleDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/SimpleScaleDemo) .

L’exemple [**ButtonScaler**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonScaler) illustre la différence entre l’animation de la propriété `Scale` d’un `Button` et l’animation de la propriété `FontSize`. La propriété `FontSize` affecte le mode de perception du `Button` dans la disposition ; la propriété `Scale` ne le fait pas.

L’exemple [**ScaleToSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ScaleToSize) calcule une propriété `Scale` appliquée à un élément `Label` pour le rendre le plus grand possible tout en continuant à s’ajuster dans la page.

### <a name="anchoring-the-scale"></a>Ancrage de la mise à l’échelle

Les éléments mis à l’échelle dans les trois exemples précédents ont tous les augmenter ou réduire la taille par rapport au centre de l’élément. En d’autres termes, l’élément augmente ou diminue en taille identique dans toutes les directions. Seul le point au centre de l’élément reste dans le même emplacement pendant la mise à l’échelle.

Vous pouvez modifier le centre de la mise à l’échelle en définissant les propriétés [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) et [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) . Ces propriétés sont par rapport à l’élément lui-même. Par `AnchorX`, la valeur 0 fait référence au côté gauche de l’élément et 1 à la partie droite. De même, pour `AnchorY`, 0 est le haut et 1 le bas. Les deux propriétés ont des valeurs par défaut de 0.5, qui est le centre.

L’exemple [**AnchoredScaleDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/AnchoredScaleDemo) vous permet d’expérimenter les propriétés `AnchorX` et `AnchorY`, ainsi que la propriété `Scale`.

Sur iOS, l’utilisation de valeurs autres que celles par défaut des propriétés `AnchorX` et `AnchorY` est généralement incompatible avec les modifications de l’orientation du téléphone.

## <a name="the-rotation-transform"></a>La transformation de rotation

La propriété [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) est spécifiée en degrés et indique une rotation dans le sens des aiguilles d’une montre autour d’un point de l’élément défini par `AnchorX` et `AnchorY`. Le [**PlaneRotationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/PlaneRotationDemo) vous permet d’expérimenter ces trois propriétés.

### <a name="rotated-text-effects"></a>Effets de texte pivoté

L’exemple [**BoxViewCircle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewCircle) illustre la mathématique nécessaire pour dessiner un cercle à l’aide d’éléments de `BoxView` légèrement paysage 64.

L’exemple [**RotatedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/RotatedText) affiche plusieurs éléments `Label` avec la même chaîne de texte pivotée pour apparaître comme spokes.

L’exemple [**CircularText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/CircularText) affiche une chaîne de texte qui s’affiche pour encapsuler un cercle.

### <a name="an-analog-clock"></a>Une horloge analogique

La bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contient une classe [`AnalogClockViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnalogClockViewModel.cs) qui calcule les angles des aiguilles d’une horloge. Pour éviter les dépendances de plateforme dans le ViewModel, la classe utilise `Task.Delay` plutôt qu’un minuteur pour la recherche d’une nouvelle valeur de `DateTime`.

Également inclus dans **Xamarin. FormsBook. Toolkit** est une classe [`SecondTickConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondTickConverter.cs) qui implémente `IValueConverter` et sert à arrondir un deuxième angle à la seconde la plus proche.

[**MinimalBoxViewClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/MinimalBoxViewClock) utilise trois éléments `BoxView` rotatifs pour dessiner une horloge analogique.

Le [**BoxViewClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewClock) utilise `BoxView` pour des graphiques plus étendus, y compris des graduations autour de l’horloge et des mains qui font pivoter une petite distance par rapport à leurs extrémités :

[![Capture d’écran triple de BoxView Clock](images/ch21fg17-small.png "Face de l’horloge analogique")](images/ch21fg17-large.png#lightbox "Face de l’horloge analogique")

En outre, une classe [`SecondBackEaseConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondBackEaseConverter.cs) dans **Xamarin. FormsBook. Toolkit** fait apparaître le deuxième plan pour revenir en arrière avant d’aller plus loin, puis revenir à sa position correcte.

### <a name="vertical-sliders"></a>Curseurs verticales ?

L’exemple [**VerticalSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/VerticalSliders) montre que `Slider` éléments peuvent pivoter de 90 degrés tout en continuant à fonctionner. Toutefois, il est difficile de positionner ces éléments `Slider` pivotés, car dans la disposition, ils semblent toujours horizontaux.

## <a name="3d-ish-rotations"></a>Rotations qui concerne 3D

La propriété [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX) semble faire pivoter un élément autour d’un axe des X 3D afin que le haut et le bas de l’élément semblent se déplacer vers la visionneuse ou s’éloigner de celui-ci. De même, le [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY) semble faire pivoter un élément autour de l’axe des Y pour que les côtés gauche et droit de l’élément semblent se déplacer vers l’extérieur ou l’éloigner de la visionneuse.

La propriété `AnchorX` affecte `RotationY` mais pas `RotationX`. La propriété `AnchorY` affecte `RotationX` mais pas `RotationY`. Vous pouvez expérimenter l’exemple [**ThreeDeeRotationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ThreeDeeRotationDemo) pour explorer les interactions de ces propriétés.

Le système de coordonnées 3D impliqué par Xamarin.Forms est gaucher. Si vous pointez le pouce de votre main gauche dans la direction de l’augmentation de X coordonnées coordonnées (à droite) et votre doigt intermédiaire dans la direction de croissant Y (points vers le bas), puis votre curseur dans le sens d’augmentation de coordonnées Z (hors de l’écran).

En outre, pour tous les trois axes, si vous pointez votre curseur gauche dans la direction de l’augmentation des valeurs, puis la courbe de vos doigts indique la direction de rotation des angles de rotation positives.

## <a name="related-links"></a>Liens connexes

- [Chapitre 21 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch21-Apr2016.pdf)
- [Exemples du chapitre 21](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)
