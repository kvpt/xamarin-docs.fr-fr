---
title: Résumé du chapitre 21. Transformations
description: 'Création d’applications mobiles avec Xamarin.Forms: Résumé du chapitre 21. Transformations'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3642F112-C7FA-4A74-9000-F9087BA89AD9
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 40c091d0c5042d172108709f89774e41e9339d4b
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70760582"
---
# <a name="summary-of-chapter-21-transforms"></a>Résumé du chapitre 21. Transformations

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)

Une vue Xamarin.Forms apparaît à l’écran dans un endroit et `Layout` `Layout<View>` une taille déterminés par son parent, qui est généralement un ou dérivé. La *transformation* est une fonction Xamarin.Forms qui peut modifier cet emplacement, cette taille ou même cette orientation.

Xamarin.Forms prend en charge trois types de transformations de base :

- *La traduction* &mdash; déplace un élément horizontalement ou verticalement
- *L’échelle* &mdash; modifie la taille d’un élément
- *Rotation* &mdash; tourne un élément autour d’un point ou d’un axe

Dans Xamarin.Forms, la mise à l’échelle est isotropique; il affecte la largeur et la hauteur uniformément. La rotation est prise en charge à la fois sur la surface bidimensionnelle de l’écran et dans l’espace 3D. Il n’y a pas de transformation biaisée (ou pure), et aucune matrice généralisée ne se transforme.

Les transformations sont prises `double` en charge `VisualElement` avec huit propriétés de type définies par la classe :

- [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)
- [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)
- [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)
- [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)
- [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX)
- [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)
- [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX)
- [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY)

Toutes ces propriétés sont soutenues par des propriétés liantes. Ils peuvent être des cibles de liaison de données et de style. [**Chapitre 22. L’animation**](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter22.md) montre comment ces propriétés peuvent être animées, mais certains échantillons de ce chapitre montrent comment vous pouvez les animer à l’aide de la [minuterie](~/xamarin-forms/platform/device.md#devicestarttimer)Xamarin.Forms .

Transformer les propriétés n’affecte que la façon dont l’élément est rendu, et n’affectent *pas* la façon dont l’élément est perçu dans la mise en page.

## <a name="the-translation-transform"></a>La traduction se transforme

Les valeurs non [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) zéro [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) de la et les propriétés déplacent un élément horizontalement ou verticalement.

Le programme [**TranslationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TranslationDemo) vous permet d’expérimenter avec ces propriétés avec deux `Slider` éléments qui contrôlent le et `TranslationX` `TranslationY` les propriétés d’un `Frame`. La transformation affecte également `Frame`tous les enfants de cela .

### <a name="text-effects"></a>Effets de texte

Une utilisation courante des propriétés de traduction est de compenser légèrement le rendu du texte. Ceci est démontré dans l’échantillon [**TextOffsets**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TextOffsets) :

[![Triple capture d’écran de Text Offsets](images/ch21fg03-small.png "Décalages de texte")](images/ch21fg03-large.png#lightbox "Décalages de texte")

Un autre effet est de `Label` rendre plusieurs copies d’un pour ressembler à un bloc 3D, comme démontré dans l’échantillon [**BlockText.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BlockText)

### <a name="jumps-and-animations"></a>Sauts et animations

[**L’échantillon ButtonJump**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonJump) utilise `Button` la traduction pour déplacer un chaque fois qu’il est exploité, mais l’intention principale est de démontrer que l’entrée de l’utilisateur `Button` reçoit à l’endroit où le bouton est rendu.

[**L’échantillon ButtonGlide**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonGlide) est similaire, mais utilise `Button` une minuterie pour animer le d’un point à l’autre.

## <a name="the-scale-transform"></a>L’échelle se transforme

La [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) transformation peut augmenter ou diminuer la taille rendue de l’élément. La valeur par défaut est 1. Une valeur de 0 rend l’élément invisible. Les valeurs négatives font que l’élément semble être tourné à 180 degrés. La `Scale` propriété n’affecte pas les `Width` propriétés ou `Height` les propriétés de l’élément. Ces valeurs restent les mêmes.

Vous pouvez expérimenter `Scale` avec la propriété à l’aide de l’échantillon [**SimpleScaleDemo.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/SimpleScaleDemo)

[**L’échantillon ButtonScaler**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonScaler) démontre la différence `Scale` entre l’animation de la propriété d’un `Button` et l’animation de la `FontSize` propriété. La `FontSize` propriété affecte `Button` la façon dont le est perçu dans la mise en page; la `Scale` propriété n’a pas.

[**L’échantillon ScaleToSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ScaleToSize) `Scale` calcule une propriété `Label` qui est appliquée à un élément pour la rendre aussi grande que possible tout en restant dans la page.

### <a name="anchoring-the-scale"></a>Ancrer l’échelle

Les éléments mis à l’échelle dans les trois échantillons précédents ont tous augmenté ou diminué en taille par rapport au centre de l’élément. En d’autres termes, l’élément augmente ou diminue la taille de la même dans toutes les directions. Seul le point au centre de l’élément reste au même endroit pendant la mise à l’échelle.

Vous pouvez changer le centre de l’échelle en définissant le et [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) les propriétés. Ces propriétés sont relatives à l’élément lui-même. Pour `AnchorX`, une valeur de 0 se réfère au côté gauche de l’élément et 1 se réfère au côté droit. De `AnchorY`même pour , 0 est le haut et 1 est le fond. Les deux propriétés ont des valeurs par défaut de 0,5, qui est le centre.

[**L’échantillon AnchoredScaleDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/AnchoredScaleDemo) vous permet `AnchorX` `AnchorY` d’expérimenter avec `Scale` les propriétés ainsi que la propriété.

Sur iOS, l’utilisation `AnchorX` de `AnchorY` valeurs et de propriétés non par défaut est généralement incompatible avec les changements d’orientation du téléphone.

## <a name="the-rotation-transform"></a>La rotation se transforme

La [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) propriété est spécifiée en degrés et indique la `AnchorX` rotation `AnchorY`dans le sens des aiguilles d’une montre autour d’un point de l’élément défini par et . Le [**PlaneRotationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/PlaneRotationDemo) vous permet d’expérimenter avec ces trois propriétés.

### <a name="rotated-text-effects"></a>Effets de texte rotimés

[**L’échantillon BoxViewCircle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewCircle) démontre les mathématiques nécessaires pour dessiner un `BoxView` cercle à l’aide de 64 minuscules éléments rotaux.

[**L’échantillon RotatedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/RotatedText) affiche plusieurs `Label` éléments avec la même chaîne de texte tournée pour apparaître comme des rayons.

[**L’échantillon CircularText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/CircularText) affiche une chaîne de texte qui semble s’envelopper en cercle.

### <a name="an-analog-clock"></a>Une horloge analogique

La bibliothèque [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contient une [`AnalogClockViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnalogClockViewModel.cs) classe qui calcule les angles pour les mains d’une horloge. Pour éviter les dépendances de la plate-forme dans le ViewModel, la classe utilise `Task.Delay` plutôt qu’une minuterie pour trouver une nouvelle `DateTime` valeur.

Également inclus dans **Xamarin.FormsBook.Toolkit** est [`SecondTickConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondTickConverter.cs) `IValueConverter` une classe qui implémente et sert à arrondir un deuxième angle à la seconde la plus proche.

Le [**MinimalBoxViewClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/MinimalBoxViewClock) utilise `BoxView` trois éléments rotatifs pour dessiner une horloge analogique.

Le [**BoxViewClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewClock) utilise `BoxView` pour des graphismes plus étendus, y compris des marques de cocher autour de la face de l’horloge, et les mains qui tournent un peu à distance de leurs extrémités:

[![Triple capture d’écran de BoxView Clock](images/ch21fg17-small.png "Visage d’horloge analogique")](images/ch21fg17-large.png#lightbox "Visage d’horloge analogique")

En outre, une [`SecondBackEaseConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondBackEaseConverter.cs) classe dans **Xamarin.FormsBook.Toolkit** provoque l’seconde main à apparaître pour tirer en arrière un peu avant de sauter en avant, puis de revenir dans sa position correcte.

### <a name="vertical-sliders"></a>Des curseurs verticaux ?

[**L’échantillon VerticalSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/VerticalSliders) `Slider` démontre que les éléments peuvent être tournés à 90 degrés et fonctionnent toujours. Cependant, il est difficile de `Slider` positionner ces éléments rotaux parce que dans la mise en page, ils semblent encore être horizontaux.

## <a name="3d-ish-rotations"></a>Rotations 3D-ish

La [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX) propriété semble faire pivoter un élément autour d’un axe X 3D de sorte que le haut et le bas de l’élément semblent se déplacer vers ou loin du spectateur. De même, le [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY) semble faire pivoter un élément autour de l’axe Y pour faire les côtés gauche et droit de l’élément semblent se déplacer vers ou loin du spectateur.

La `AnchorX` propriété `RotationY` affecte, mais pas `RotationX`. La `AnchorY` propriété `RotationX` affecte, mais pas `RotationY`. Vous pouvez expérimenter avec l’échantillon [**ThreeDeeRotationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ThreeDeeRotationDemo) pour explorer les interactions de ces propriétés.

Le système de coordonnées 3D implicite par Xamarin.Forms est gaucher. Si vous pointez l’index de votre main gauche dans la direction de l’augmentation des coordonnées X (à droite) et votre majeur dans la direction de l’augmentation des coordonnées Y (vers le bas), puis votre pouce pointe dans la direction de l’augmentation des coordonnées Z (hors de l’écran).

En outre, pour l’un des trois axes, si vous pointez votre pouce gauche dans la direction des valeurs croissantes, alors la courbe de vos doigts indique la direction de rotation pour les angles rotatifs positifs.

## <a name="related-links"></a>Liens connexes

- [Chapitre 21 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch21-Apr2016.pdf)
- [Échantillons du chapitre 21](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)
