---
title: ''
description: ''
Creating Mobile Apps with Xamarin.Forms: Summary of Chapter 21. Transforms''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 32393108f84ea3a57079c86b6a9a8e628ceca03a
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136667"
---
# <a name="summary-of-chapter-21-transforms"></a>Résumé du chapitre 21. Transformations

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)

Une Xamarin.Forms vue apparaît à l’écran dans un emplacement et une taille déterminés par son parent, qui est généralement `Layout` un `Layout<View>` dérivé de ou. La *transformation* est une Xamarin.Forms fonctionnalité qui peut modifier cet emplacement, sa taille ou même l’orientation.

Xamarin.Formsprend en charge trois types de transformations de base :

- *Traduction* &mdash; déplacer un élément horizontalement ou verticalement
- Mise à l' *échelle* &mdash; modifier la taille d’un élément
- *Rotation* &mdash; transformer un élément autour d’un point ou d’un axe

Dans Xamarin.Forms , la mise à l’échelle est isotrope ; elle affecte uniformément la largeur et la hauteur. La rotation est prise en charge à la fois sur la surface à deux dimensions de l’écran et dans l’espace 3D. Il n’y a aucune transformation d’inclinaison (ou de nature) et aucune transformation de matrice généralisée.

Les transformations sont prises en charge avec huit propriétés de type `double` définies par la `VisualElement` classe :

- [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)
- [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)
- [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)
- [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)
- [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX)
- [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)
- [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX)
- [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY)

Toutes ces propriétés sont sauvegardées par des propriétés pouvant être liées. Ils peuvent être des cibles de liaison de données et de style. [**Chapitre 22. L’animation**](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter22.md) montre comment ces propriétés peuvent être animées, mais certains exemples de ce chapitre montrent comment vous pouvez les animer à l’aide de la Xamarin.Forms [minuterie](~/xamarin-forms/platform/device.md#devicestarttimer).

Les propriétés de transformation affectent uniquement le mode de rendu de l’élément et n’affectent *pas* la manière dont l’élément est perçu dans la disposition.

## <a name="the-translation-transform"></a>Transformation de traduction

Les valeurs autres que zéro [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) des [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) Propriétés et déplacent un élément horizontalement ou verticalement.

Le programme [**TranslationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TranslationDemo) vous permet d’expérimenter ces propriétés avec deux `Slider` éléments qui contrôlent les `TranslationX` Propriétés et `TranslationY` d’un `Frame` . La transformation affecte également tous les enfants de ce `Frame` .

### <a name="text-effects"></a>Effets de texte

Une utilisation courante des propriétés de traduction consiste à décaler légèrement le rendu du texte. Cela est illustré dans l’exemple [**TextOffsets**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TextOffsets) :

[![Capture d’écran triple des décalages de texte](images/ch21fg03-small.png "Décalages de texte")](images/ch21fg03-large.png#lightbox "Décalages de texte")

Un autre effet consiste à afficher plusieurs copies d’un `Label` pour ressembler à un bloc 3D, comme illustré dans l’exemple [**BlockText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BlockText) .

### <a name="jumps-and-animations"></a>Sauts et animations

L’exemple [**ButtonJump**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonJump) utilise la traduction pour déplacer un `Button` chaque fois qu’il est frappé, mais l’objectif principal est de démontrer que le reçoit l' `Button` entrée d’utilisateur à l’emplacement où le bouton est rendu.

L’exemple [**ButtonGlide**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonGlide) est similaire, mais utilise un minuteur pour animer le `Button` d’un point à un autre.

## <a name="the-scale-transform"></a>Transformation d’échelle

La [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) transformation peut augmenter ou diminuer la taille rendue de l’élément. La valeur par défaut est 1. La valeur 0 provoque l’invisibilité de l’élément. Les valeurs négatives provoquent l’affichage de la rotation de l’élément à 180 degrés. La `Scale` propriété n’affecte pas les `Width` `Height` Propriétés ou de l’élément. Ces valeurs restent identiques.

Vous pouvez expérimenter la `Scale` propriété à l’aide de l’exemple [**SimpleScaleDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/SimpleScaleDemo) .

L’exemple [**ButtonScaler**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonScaler) illustre la différence entre l’animation `Scale` de la propriété d’un `Button` et l’animation de la `FontSize` propriété. La `FontSize` propriété affecte la façon dont le `Button` est perçu dans la disposition ; la `Scale` propriété ne le fait pas.

L’exemple [**ScaleToSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ScaleToSize) calcule une `Scale` propriété qui est appliquée à un `Label` élément pour le rendre le plus grand possible tout en continuant à s’ajuster dans la page.

### <a name="anchoring-the-scale"></a>Ancrage de l’échelle

Les éléments mis à l’échelle dans les trois échantillons précédents ont tous augmenté ou diminué en taille par rapport au centre de l’élément. En d’autres termes, l’élément augmente ou diminue la taille de la même façon dans toutes les directions. Seul le point au centre de l’élément reste au même emplacement au cours de la mise à l’échelle.

Vous pouvez modifier le centre de la mise à l’échelle en définissant les [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) Propriétés et. Ces propriétés sont relatives à l’élément lui-même. Pour `AnchorX` , la valeur 0 fait référence au côté gauche de l’élément et 1 fait référence au côté droit. De la même façon `AnchorY` , 0 est le haut et 1 le bas. Les deux propriétés ont des valeurs par défaut de 0,5, qui est le centre.

L’exemple [**AnchoredScaleDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/AnchoredScaleDemo) vous permet d’expérimenter les `AnchorX` Propriétés et, ainsi `AnchorY` que la `Scale` propriété.

Sur iOS, l’utilisation de valeurs autres que celles par défaut des `AnchorX` `AnchorY` Propriétés et est généralement incompatible avec les modifications de l’orientation du téléphone.

## <a name="the-rotation-transform"></a>Transformation de rotation

La [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) propriété est spécifiée en degrés et indique une rotation dans le sens des aiguilles d’une montre autour d’un point de l’élément défini par `AnchorX` et `AnchorY` . Le [**PlaneRotationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/PlaneRotationDemo) vous permet d’expérimenter ces trois propriétés.

### <a name="rotated-text-effects"></a>Effets de texte pivoté

L’exemple [**BoxViewCircle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewCircle) illustre la mathématique nécessaire pour dessiner un cercle à l’aide d’éléments paysage 64 minuscules `BoxView` .

L’exemple [**RotatedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/RotatedText) affiche plusieurs `Label` éléments avec la même chaîne de texte pivotée pour apparaître comme spokes.

L’exemple [**CircularText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/CircularText) affiche une chaîne de texte qui s’affiche pour encapsuler un cercle.

### <a name="an-analog-clock"></a>Une horloge analogique

La bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contient une [`AnalogClockViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnalogClockViewModel.cs) classe qui calcule les angles des aiguilles d’une horloge. Pour éviter les dépendances de plateforme dans le ViewModel, la classe utilise `Task.Delay` plutôt qu’un minuteur pour la recherche d’une nouvelle `DateTime` valeur.

Également inclus dans **Xamarin. FormsBook. Toolkit** est une [`SecondTickConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondTickConverter.cs) classe qui implémente `IValueConverter` et sert à arrondir un deuxième angle à la seconde la plus proche.

Le [**MinimalBoxViewClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/MinimalBoxViewClock) utilise trois éléments rotatifs `BoxView` pour dessiner une horloge analogique.

Le [**BoxViewClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewClock) utilise `BoxView` pour des graphiques plus étendus, y compris des graduations autour de l’horloge et des mains qui font pivoter une petite distance par rapport à leurs extrémités :

[![Capture d’écran triple de BoxView Clock](images/ch21fg17-small.png "Face de l’horloge analogique")](images/ch21fg17-large.png#lightbox "Face de l’horloge analogique")

En outre [`SecondBackEaseConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondBackEaseConverter.cs) , une classe dans **Xamarin. FormsBook. Toolkit** fait apparaître un petit peu avant de passer en arrière, puis revenir à sa position correcte.

### <a name="vertical-sliders"></a>Curseurs verticaux ?

L’exemple [**VerticalSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/VerticalSliders) montre que les `Slider` éléments peuvent pivoter de 90 degrés tout en continuant à fonctionner. Toutefois, il est difficile de positionner ces éléments pivotés `Slider` , car dans la disposition, ils semblent toujours horizontaux.

## <a name="3d-ish-rotations"></a>rotations 3D-longues

La [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX) propriété semble faire pivoter un élément autour d’un axe des X 3D afin que le haut et le bas de l’élément semblent se déplacer vers la visionneuse ou en être éloignés. De même, le [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY) semble faire pivoter un élément autour de l’axe des Y pour que les côtés gauche et droit de l’élément semblent se déplacer vers l’extérieur ou l’éloigner de la visionneuse.

La `AnchorX` propriété affecte `RotationY` , mais pas `RotationX` . La `AnchorY` propriété affecte `RotationX` , mais pas `RotationY` . Vous pouvez expérimenter l’exemple [**ThreeDeeRotationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ThreeDeeRotationDemo) pour explorer les interactions de ces propriétés.

Le système de coordonnées 3D impliqué par Xamarin.Forms est gauche. Si vous pointez le index de votre main gauche dans le sens de l’incrémentation des coordonnées X (à droite) et de votre doigt central dans le sens de l’incrémentation des coordonnées Y (vers le bas), votre curseur pointe dans le sens de la croissance des coordonnées Z (en dehors de l’écran).

En outre, pour l’un des trois axes, si vous pointez votre curseur de gauche dans le sens de l’incrémentation des valeurs, la courbe de vos doigts indique le sens de rotation pour les angles rotatifs positifs.

## <a name="related-links"></a>Liens connexes

- [Chapitre 21 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch21-Apr2016.pdf)
- [Exemples du chapitre 21](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)
