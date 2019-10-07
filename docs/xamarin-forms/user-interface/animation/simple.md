---
title: Animations simples dans Xamarin.Forms
description: La classe ViewExtensions fournit des méthodes d’extension qui peuvent être utilisées pour construire des animations simples. Cet article montre comment créer et annuler des animations à l’aide de la classe ViewExtensions.
ms.prod: xamarin
ms.assetid: 4A6FAE5A-848F-4CE0-BFA1-22A6309B5225
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/27/2017
ms.openlocfilehash: 26068973fd91d5229b7e2108f5df46ae4476ef74
ms.sourcegitcommit: 4cf434b126eb7df6b2fd9bb1d71613bf2b6aac0e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2019
ms.locfileid: "71997203"
---
# <a name="simple-animations-in-xamarinforms"></a>Animations simples dans Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-basic)

_La classe ViewExtensions fournit des méthodes d’extension qui peuvent être utilisées pour construire des animations simples. Cet article montre comment créer et annuler des animations à l’aide de la classe ViewExtensions._

Le [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) classe fournit les méthodes d’extension qui peuvent être utilisées pour créer des animations simples suivantes :

- [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) Anime la [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX) et [ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY) propriétés d’un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).
- [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) Anime la [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) propriété d’un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).
- [`RelScaleTo`](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) s’applique une augmentation incrémentielle animée ou la réduire pour le [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) propriété d’un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).
- [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) Anime la [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) propriété d’un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).
- [`RelRotateTo`](xref:Xamarin.Forms.ViewExtensions.RelRotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) s’applique une augmentation incrémentielle animée ou la réduire pour le [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) propriété d’un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).
- [`RotateXTo`](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) Anime la [ `RotationX` ](xref:Xamarin.Forms.VisualElement.RotationX) propriété d’un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).
- [`RotateYTo`](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) Anime la [ `RotationY` ](xref:Xamarin.Forms.VisualElement.RotationY) propriété d’un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).
- [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) Anime la [ `Opacity` ](xref:Xamarin.Forms.VisualElement.Opacity) propriété d’un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).

Par défaut, chaque animation prendra 250 millisecondes. Toutefois, une durée de chaque animation peut être spécifiée lors de la création de l’animation.

Le [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) classe inclut également un [ `CancelAnimations` ](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement)) méthode qui peut être utilisé pour annuler toutes les animations.

> [!NOTE]
> Le [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) classe fournit un [ `LayoutTo` ](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing)) méthode d’extension. Toutefois, cette méthode est destinée à être utilisé par les dispositions pour animer des transitions entre les États de mise en page qui contiennent la taille et de position des modifications. Par conséquent, il ne doit être utilisé par [ `Layout` ](xref:Xamarin.Forms.Layout) sous-classes.

Les méthodes d’extension de l’animation dans le [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) classe sont toutes asynchrones et retour un `Task<bool>` objet. La valeur de retour est `false` si l’animation se termine, et `true` si l’animation est annulée. Par conséquent, les méthodes d’animation doivent généralement être utilisées avec le `await` opérateur, qui permet de facilement déterminer si une animation est terminée. En outre, il devient alors possible de créer des animations séquentielles avec les méthodes d’animation suivante l’exécution une fois la méthode précédente terminée. Pour plus d’informations, consultez [composée des Animations](#compound).

S’il est nécessaire pour permettre à une animation complète en arrière-plan, puis le `await` opérateur peut être omis. Dans ce scénario, les méthodes d’extension de l’animation retournera rapidement après avoir lancé l’animation, l’animation qui se produisent en arrière-plan. Cette opération peut être exploitée lors de la création d’animations composites. Pour plus d’informations, consultez [Animations Composite](#composite).

Pour plus d’informations sur la `await` opérateur, voir [vue d’ensemble de la prise en charge asynchrone](~/cross-platform/platform/async.md).

## <a name="single-animations"></a>Animations uniques

Chaque méthode d’extension dans le [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) implémente une opération d’animation unique qui modifie progressivement une propriété d’une valeur à une autre valeur sur une période de temps. Cette section explore chaque opération de l’animation.

### <a name="rotation"></a>Rotation

L’exemple de code suivant montre comment utiliser le [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) méthode pour animer la [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) propriété d’un [ `Image` ](xref:Xamarin.Forms.Image):

```csharp
await image.RotateTo (360, 2000);
image.Rotation = 0;
```

Ce code s’anime le [ `Image` ](xref:Xamarin.Forms.Image) instance en appliquant une rotation jusqu'à 360 degrés sur 2 secondes (2 000 millisecondes). Le [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) méthode obtient actuel [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) propriété valeur pour le début de l’animation, puis fait pivoter de cette valeur pour son premier argument (360). Une fois que l’animation est terminé, l’image [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) propriété est réinitialisée à 0. Cela garantit que le `Rotation` propriété ne reste à 360 après la fin de l’animation, ce qui empêcherait les rotations supplémentaires.

Les captures d’écran suivantes montrent la rotation en cours d’exécution sur chaque plateforme :

![](simple-images/rotateto.png "Animation de rotation")

### <a name="relative-rotation"></a>Rotation relative

L’exemple de code suivant montre comment utiliser le [ `RelRotateTo` ](xref:Xamarin.Forms.ViewExtensions.RelRotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) méthode pour augmenter ou diminuer de façon incrémentielle le [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) propriété d’un [ `Image` ](xref:Xamarin.Forms.Image):

```csharp
await image.RelRotateTo (360, 2000);
```

Ce code s’anime le [ `Image` ](xref:Xamarin.Forms.Image) instance en faisant pivoter à 360 degrés à partir de sa position de départ sur 2 secondes (2 000 millisecondes). Le [ `RelRotateTo` ](xref:Xamarin.Forms.ViewExtensions.RelRotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) méthode obtient actuel [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) propriété valeur pour le début de l’animation, puis fait pivoter de cette valeur à la valeur ainsi que son premier argument (360). Cela garantit que chaque animation sera toujours une rotation de 360 degrés à partir de la position de départ. Par conséquent, si une animation est appelée alors qu’une animation est déjà en cours d’exécution, il démarrera à partir de la position actuelle et peut mettre fin à une position qui n’est pas un incrément de 360 degrés.

Les captures d’écran suivantes montrent la rotation relative en cours d’exécution sur chaque plateforme :

![](simple-images/relrotateto.png "Animation de Rotation relatif")

### <a name="scaling"></a>Mise à l'échelle

L’exemple de code suivant montre comment utiliser le [ `ScaleTo` ](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) méthode pour animer la [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) propriété d’un [ `Image` ](xref:Xamarin.Forms.Image):

```csharp
await image.ScaleTo (2, 2000);
```

Ce code s’anime le [ `Image` ](xref:Xamarin.Forms.Image) instance par montée en puissance à deux fois sa taille supérieure à 2 secondes (2 000 millisecondes). Le [ `ScaleTo` ](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) méthode obtient actuel [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) la valeur de propriété (valeur par défaut de 1) pour le début de l’animation et puis échelles de cette valeur pour son premier argument (2). Cela a pour effet de l’augmentation de la taille de l’image à deux fois sa taille.

Les captures d’écran suivantes montrent la mise à l’échelle en cours d’exécution sur chaque plateforme :

![](simple-images/scaleto.png "Mise à l’échelle d’Animation")

> [!NOTE]
> Le [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) classe définit également [ `ScaleX` ](xref:Xamarin.Forms.VisualElement.ScaleX) et [ `ScaleY` ](xref:Xamarin.Forms.VisualElement.ScaleY) propriétés, ce qui peuvent évoluer le `VisualElement` différemment dans le directions horizontale et verticale. Ces propriétés peuvent être animées qu’avec le [ `Animation` ](xref:Xamarin.Forms.Animation) classe. Pour plus d’informations, consultez [Animations personnalisées dans Xamarin.Forms](custom.md).

### <a name="relative-scaling"></a>Mise à l’échelle par rapport

L’exemple de code suivant montre comment utiliser le [ `RelScaleTo` ](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) méthode pour animer la [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) propriété d’un [ `Image` ](xref:Xamarin.Forms.Image):

```csharp
await image.RelScaleTo (2, 2000);
```

Ce code s’anime le [ `Image` ](xref:Xamarin.Forms.Image) instance par montée en puissance à deux fois sa taille supérieure à 2 secondes (2 000 millisecondes). Le [ `RelScaleTo` ](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) méthode obtient actuel [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) valeur de propriété pour le début de l’animation et puis échelles de cette valeur à la valeur ainsi que son premier argument (2). Cela garantit que chaque animation sera toujours une mise à l’échelle de 2 à partir de la position de départ.

### <a name="scaling-and-rotation-with-anchors"></a>Mise à l’échelle et Rotation avec les points d’ancrage

Le [ `AnchorX` ](xref:Xamarin.Forms.VisualElement.AnchorX) et [ `AnchorY` ](xref:Xamarin.Forms.VisualElement.AnchorY) propriétés définies au centre de mise à l’échelle ou rotation pour le [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) et [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) propriétés. Par conséquent, leurs valeurs affectent également le [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) et [ `ScaleTo` ](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) méthodes.

Étant donné un [ `Image` ](xref:Xamarin.Forms.Image) qui a été placé dans le centre d’une disposition, l’exemple de code suivant illustre la permutation de l’image autour du centre de la mise en page en définissant son [ `AnchorY` ](xref:Xamarin.Forms.VisualElement.AnchorY) propriété :

```csharp
image.AnchorY = (Math.Min (absoluteLayout.Width, absoluteLayout.Height) / 2) / image.Height;
await image.RotateTo (360, 2000);
```

Pour faire pivoter le [ `Image` ](xref:Xamarin.Forms.Image) instance autour du centre de la disposition, le [ `AnchorX` ](xref:Xamarin.Forms.VisualElement.AnchorX) et [ `AnchorY` ](xref:Xamarin.Forms.VisualElement.AnchorY) propriétés doivent être définies pour les valeurs par rapport à la largeur et la hauteur de la `Image`. Dans cet exemple, le centre de la `Image` est défini comme étant dans le centre de la disposition et donc la valeur par défaut `AnchorX` la valeur 0,5 n’entraîne pas la modification. Toutefois, le `AnchorY` propriété est redéfinie pour être une valeur à partir du haut de la `Image` au point central de la disposition. Cela garantit que le `Image` effectue une rotation complète de 360 degrés autour du point central de la disposition, comme indiqué dans les captures d’écran suivante :

![](simple-images/rotate-anchors.png "Animation de rotation avec les points d’ancrage")

### <a name="translation"></a>Traduction

L’exemple de code suivant montre comment utiliser le [ `TranslateTo` ](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) méthode pour animer la [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX) et [ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY) propriétés d’un [ `Image`](xref:Xamarin.Forms.Image):

```csharp
await image.TranslateTo (-100, -100, 1000);
```

Ce code s’anime le [ `Image` ](xref:Xamarin.Forms.Image) instance en traduisant il horizontalement et verticalement plus de 1 seconde (1 000 millisecondes). Le [ `TranslateTo` ](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) méthode traduit simultanément l’image 100 pixels vers la gauche et 100 pixels vers le haut. Il s’agit, car le premier et deuxième argument est les deux nombres négatifs. Fournissant des nombres positifs traduit l’image vers la droite et vers le bas.

Les captures d’écran suivantes montrent la traduction en cours d’exécution sur chaque plateforme :

![](simple-images/translateto.png "Animation de traduction")

> [!NOTE]
> Si un élément est disposé initialement hors écran et ensuite traduit à l’écran, après conversion disposition d’entrée de l’élément reste hors écran et l’utilisateur ne peut pas interagir avec lui. Par conséquent, il est recommandé qu’une vue doit être disposée dans sa position finale, et puis toutes les traductions exécutées requises.

### <a name="fading"></a>Suppression

L’exemple de code suivant montre comment utiliser le [ `FadeTo` ](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) méthode pour animer la [ `Opacity` ](xref:Xamarin.Forms.VisualElement.Opacity) propriété d’un [ `Image` ](xref:Xamarin.Forms.Image):

```csharp
image.Opacity = 0;
await image.FadeTo (1, 4000);
```

Ce code s’anime le [ `Image` ](xref:Xamarin.Forms.Image) instance en fondu il dans plus de 4 secondes (4000 millisecondes). Le [ `FadeTo` ](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) méthode obtient actuel [ `Opacity` ](xref:Xamarin.Forms.VisualElement.Opacity) valeur de propriété pour le début de l’animation et puis effectuer des fondus dans à partir de cette valeur pour son premier argument (1).

Les captures d’écran suivantes montrent le fondu en cours d’exécution sur chaque plateforme :

![](simple-images/fadeto.png "Animation de fondu")

<a name="compound" />

## <a name="compound-animations"></a>Animations composées

Une animation composée est une combinaison séquentielle d’animations et peuvent être créée avec le `await` opérateur, comme illustré dans l’exemple de code suivant :

```csharp
await image.TranslateTo (-100, 0, 1000);    // Move image left
await image.TranslateTo (-100, -100, 1000); // Move image up
await image.TranslateTo (100, 100, 2000);   // Move image diagonally down and right
await image.TranslateTo (0, 100, 1000);     // Move image left
await image.TranslateTo (0, 0, 1000);       // Move image up
```

Dans cet exemple, le [ `Image` ](xref:Xamarin.Forms.Image) est traduite plus de 6 secondes (de 6 000 millisecondes). La traduction de la `Image` utilise cinq d’animations, avec le `await` opérateur indiquant que chaque animation s’exécute de manière séquentielle. Par conséquent, les méthodes d’animation suivante exécutent une fois la méthode précédente terminée.

<a name="composite" />

## <a name="composite-animations"></a>Animations composite

Une animation composite est une combinaison d’animations où deux ou plusieurs animations s’exécuter simultanément. Animations composite peuvent être créées en mélangeant des animations attendues et non attendu, comme illustré dans l’exemple de code suivant :

```csharp
image.RotateTo (360, 4000);
await image.ScaleTo (2, 2000);
await image.ScaleTo (1, 2000);
```

Dans cet exemple, le [ `Image` ](xref:Xamarin.Forms.Image) est mis à l’échelle et rotation simultanément plus de 4 secondes (4000 millisecondes). La mise à l’échelle de le `Image` utilise deux animations séquentielles qui se produisent en même temps que la rotation. Le [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) méthode s’exécute sans un `await` opérateur et retourne immédiatement, avec la première [ `ScaleTo` ](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) puis à partir de l’animation. Le `await` opérateur sur le premier `ScaleTo` appel de méthode retarde la seconde `ScaleTo` appel de méthode jusqu'à ce que la première `ScaleTo` appel de méthode terminé. À ce stade le `RotateTo` animation est à moitié terminé de façon et le `Image` sera pivotée de 180 degrés. Au cours des 2 dernières secondes (2 000 millisecondes), le deuxième `ScaleTo` animation et la `RotateTo` animation tous deux effectuer.

### <a name="running-multiple-asynchronous-methods-concurrently"></a>Exécution simultanée de plusieurs méthodes asynchrones

Le `static` `Task.WhenAny` et `Task.WhenAll` méthodes sont utilisées pour exécuter plusieurs méthodes asynchrones simultanément et par conséquent peut être utilisés pour créer des animations composites. Ces deux méthodes retournent un `Task` de l’objet et accepter une collection de méthodes que chaque retour un `Task` objet. Le `Task.WhenAny` méthode se termine lorsque toute méthode dans sa collection termine, comme illustré dans l’exemple de code suivant :

```csharp
await Task.WhenAny<bool>
(
  image.RotateTo (360, 4000),
  image.ScaleTo (2, 2000)
);
await image.ScaleTo (1, 2000);
```

Dans cet exemple, le `Task.WhenAny` appel de méthode contient deux tâches. La première tâche fait pivoter l’image de plus de 4 secondes (4000 millisecondes), et la seconde tâche met à l’échelle l’image sur 2 secondes (2 000 millisecondes). Lorsque la seconde tâche est terminée, le `Task.WhenAny` appel de méthode. Toutefois, même si le [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) méthode est en cours d’exécution, le deuxième [ `ScaleTo` ](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) méthode peut commencer.

Le `Task.WhenAll` méthode se termine lorsque toutes les méthodes dans sa collection terminées, comme illustré dans l’exemple de code suivant :

```csharp
// 10 minute animation
uint duration = 10 * 60 * 1000;

await Task.WhenAll (
  image.RotateTo (307 * 360, duration),
  image.RotateXTo (251 * 360, duration),
  image.RotateYTo (199 * 360, duration)
);
```

Dans cet exemple, le `Task.WhenAll` appel de méthode contient trois tâches, chacun d’eux s’exécute pendant 10 minutes. Chaque `Task` rend un nombre différent de 360 degrés des rotations – 307 rotations pour [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)), 251 rotations pour [ `RotateXTo` ](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))et les 199 rotations pour [ `RotateYTo` ](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)). Ces valeurs sont des nombres premiers, donc s’assurer que les rotations ne sont pas synchronisées et par conséquent, ne provoquera pas répétée.

Les captures d’écran suivantes montrent les rotations plusieurs en cours d’exécution sur chaque plateforme :

![](simple-images/multiple-rotations.png "Animation composite")

## <a name="canceling-animations"></a>Annulation des Animations

Une application peut annuler une ou plusieurs animations avec un appel à la `static` [ `ViewExtensions.CancelAnimations` ](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement)) (méthode), comme illustré dans l’exemple de code suivant :

```csharp
ViewExtensions.CancelAnimations (image);
```

Cette opération annule immédiatement toutes les animations en cours d’exécution sur le [ `Image` ](xref:Xamarin.Forms.Image) instance.

## <a name="summary"></a>Récapitulatif

Création et l’annulation des animations à l’aide de cet article ne présente la [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) classe. Cette classe fournit des méthodes d’extension qui peuvent être utilisées pour construire des animations simples pour faire pivoter, mettre à l’échelle, traduisent et fondu [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) instances.

## <a name="related-links"></a>Liens associés

- [Vue d’ensemble de la prise en charge asynchrone](~/cross-platform/platform/async.md)
- [Animation de base (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-basic)
- [ViewExtensions](xref:Xamarin.Forms.ViewExtensions)
