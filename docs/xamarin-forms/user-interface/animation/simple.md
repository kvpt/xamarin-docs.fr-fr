---
title: Animations simples dans Xamarin. Forms
description: La classe ViewExtensions fournit des méthodes d’extension qui peuvent être utilisées pour construire des animations simples. Cet article explique comment créer et annuler des animations à l’aide de la classe ViewExtensions.
ms.prod: xamarin
ms.assetid: 4A6FAE5A-848F-4CE0-BFA1-22A6309B5225
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2019
ms.openlocfilehash: 116911787db128b103fb555554076704a0549db5
ms.sourcegitcommit: f8583585c501607fdfa061b95e9a9f385ed1d591
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/26/2019
ms.locfileid: "72959162"
---
# <a name="simple-animations-in-xamarinforms"></a>Animations simples dans Xamarin. Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-basic)

_La classe ViewExtensions fournit des méthodes d’extension qui peuvent être utilisées pour construire des animations simples. Cet article explique comment créer et annuler des animations à l’aide de la classe ViewExtensions._

La classe [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) fournit les méthodes d’extension suivantes qui peuvent être utilisées pour créer des animations simples :

- [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) anime les propriétés de [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) et de [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) d’un [`VisualElement`](xref:Xamarin.Forms.VisualElement).
- [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) anime la propriété [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) d’un [`VisualElement`](xref:Xamarin.Forms.VisualElement).
- [`RelScaleTo`](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) applique une augmentation ou une diminution incrémentielle animée à la propriété [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) d’un [`VisualElement`](xref:Xamarin.Forms.VisualElement).
- [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) anime la propriété [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) d’un [`VisualElement`](xref:Xamarin.Forms.VisualElement).
- [`RelRotateTo`](xref:Xamarin.Forms.ViewExtensions.RelRotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) applique une augmentation ou une diminution incrémentielle animée à la propriété [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) d’un [`VisualElement`](xref:Xamarin.Forms.VisualElement).
- [`RotateXTo`](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) anime la propriété [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX) d’un [`VisualElement`](xref:Xamarin.Forms.VisualElement).
- [`RotateYTo`](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) anime la propriété [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY) d’un [`VisualElement`](xref:Xamarin.Forms.VisualElement).
- [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) anime la propriété [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) d’un [`VisualElement`](xref:Xamarin.Forms.VisualElement).

Par défaut, chaque animation prendra 250 millisecondes. Toutefois, une durée pour chaque animation peut être spécifiée lors de la création de l’animation.

La classe [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) comprend également une méthode [`CancelAnimations`](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement)) qui peut être utilisée pour annuler les animations.

> [!NOTE]
> La classe [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) fournit une méthode d’extension [`LayoutTo`](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing)) . Toutefois, cette méthode est destinée à être utilisée par les dispositions pour animer des transitions entre des États de disposition qui contiennent des modifications de taille et de position. Par conséquent, elle ne doit être utilisée que par [`Layout`](xref:Xamarin.Forms.Layout) sous-classes.

Les méthodes d’extension d’animation de la classe [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) sont toutes asynchrones et retournent un objet `Task<bool>`. La valeur de retour est `false` si l’animation se termine, et `true` si l’animation est annulée. Par conséquent, les méthodes d’animation doivent généralement être utilisées avec l’opérateur `await`, ce qui permet de déterminer facilement quand une animation est terminée. En outre, il devient possible de créer des animations séquentielles avec les méthodes d’animation suivantes s’exécutant après la fin de la méthode précédente. Pour plus d’informations, consultez [animations composées](#compound).

S’il est nécessaire de laisser une animation se terminer en arrière-plan, l’opérateur `await` peut être omis. Dans ce scénario, les méthodes d’extension d’animation sont rapidement retournées après l’initialisation de l’animation, avec l’animation qui se produit en arrière-plan. Cette opération peut être utilisée lors de la création d’animations composites. Pour plus d’informations, consultez [animations composites](#composite).

Pour plus d’informations sur l’opérateur `await`, consultez [vue d’ensemble de la prise en charge asynchrone](~/cross-platform/platform/async.md).

## <a name="single-animations"></a>Animations uniques

Chaque méthode d’extension dans le [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) implémente une seule opération d’animation qui modifie progressivement une propriété d’une valeur à une autre sur une période donnée. Cette section explore chaque opération d’animation.

### <a name="rotation"></a>Rotation

L’exemple de code suivant illustre l’utilisation de la méthode [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) pour animer la propriété [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) d’un [`Image`](xref:Xamarin.Forms.Image):

```csharp
await image.RotateTo (360, 2000);
image.Rotation = 0;
```

Ce code anime l’instance de [`Image`](xref:Xamarin.Forms.Image) en faisant pivoter jusqu’à 360 degrés sur 2 secondes (2000 millisecondes). La méthode [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) obtient la valeur actuelle de la propriété [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) pour le début de l’animation, puis pivote de cette valeur vers son premier argument (360). Une fois l’animation terminée, la propriété [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) de l’image est réinitialisée à 0. Cela permet de s’assurer que la propriété `Rotation` ne reste pas à 360 une fois l’animation terminée, ce qui empêche les rotations supplémentaires.

Les captures d’écran suivantes montrent la rotation en cours sur chaque plateforme :

![](simple-images/rotateto.png "Rotation Animation")

### <a name="relative-rotation"></a>Rotation relative

L’exemple de code suivant illustre l’utilisation de la méthode [`RelRotateTo`](xref:Xamarin.Forms.ViewExtensions.RelRotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) pour augmenter ou diminuer de façon incrémentielle la propriété [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) d’une [`Image`](xref:Xamarin.Forms.Image):

```csharp
await image.RelRotateTo (360, 2000);
```

Ce code anime l’instance de [`Image`](xref:Xamarin.Forms.Image) en faisant pivoter 360 degrés de sa position de départ sur 2 secondes (2000 millisecondes). La méthode [`RelRotateTo`](xref:Xamarin.Forms.ViewExtensions.RelRotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) obtient la valeur actuelle de la propriété [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) pour le début de l’animation, puis pivote de cette valeur à la valeur plus son premier argument (360). Cela garantit que chaque animation sera toujours une rotation de 360 degrés à partir de la position de départ. Par conséquent, si une nouvelle animation est appelée alors qu’une animation est déjà en cours, elle démarre à partir de la position actuelle et peut se terminer à une position qui n’est pas un incrément de 360 degrés.

Les captures d’écran suivantes montrent la rotation relative en cours sur chaque plateforme :

![](simple-images/relrotateto.png "Relative Rotation Animation")

### <a name="scaling"></a>Mise à l'échelle

L’exemple de code suivant illustre l’utilisation de la méthode [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) pour animer la propriété [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) d’un [`Image`](xref:Xamarin.Forms.Image):

```csharp
await image.ScaleTo (2, 2000);
```

Ce code anime l’instance de [`Image`](xref:Xamarin.Forms.Image) en mettant à l’échelle jusqu’à deux fois sa taille sur 2 secondes (2000 millisecondes). La méthode [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) obtient la valeur actuelle de la propriété [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) (valeur par défaut de 1) pour le début de l’animation, puis met à l’échelle de cette valeur à son premier argument (2). Cela a pour effet d’étendre la taille de l’image à deux fois sa taille.

Les captures d’écran suivantes illustrent la mise à l’échelle en cours sur chaque plateforme :

![](simple-images/scaleto.png "Scaling Animation")

> [!NOTE]
> La classe [`VisualElement`](xref:Xamarin.Forms.VisualElement) définit également les propriétés [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX) et [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY), qui peuvent redimensionner l’élément `VisualElement` dans les directions horizontale et verticale. Ces propriétés peuvent être animées avec la classe [`Animation`](xref:Xamarin.Forms.Animation) . Pour plus d’informations, consultez [animations personnalisées dans Xamarin. Forms](custom.md).

### <a name="relative-scaling"></a>Mise à l’échelle relative

L’exemple de code suivant illustre l’utilisation de la méthode [`RelScaleTo`](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) pour animer la propriété [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) d’un [`Image`](xref:Xamarin.Forms.Image):

```csharp
await image.RelScaleTo (2, 2000);
```

Ce code anime l’instance de [`Image`](xref:Xamarin.Forms.Image) en mettant à l’échelle jusqu’à deux fois sa taille sur 2 secondes (2000 millisecondes). La méthode [`RelScaleTo`](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) obtient la valeur actuelle de la propriété [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) pour le début de l’animation, puis met à l’échelle de cette valeur à la valeur plus son premier argument (2). Cela garantit que chaque animation sera toujours une mise à l’échelle de 2 à partir de la position de départ.

### <a name="scaling-and-rotation-with-anchors"></a>Mise à l’échelle et rotation avec les ancres

Les propriétés [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) et [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) définissent le centre de mise à l’échelle ou de rotation pour les propriétés [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) et [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) . Par conséquent, leurs valeurs affectent également les méthodes [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) et [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) .

À partir d’une [`Image`](xref:Xamarin.Forms.Image) qui a été placée au centre d’une disposition, l’exemple de code suivant montre comment faire pivoter l’image autour du centre de la disposition en définissant sa propriété [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) :

```csharp
double radius = Math.Min(absoluteLayout.Width, absoluteLayout.Height) / 2;
image.AnchorY = radius / image.Height;
await image.RotateTo(360, 2000);
```

Pour faire pivoter l’instance de [`Image`](xref:Xamarin.Forms.Image) autour du centre de la disposition, les propriétés [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) et [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) doivent être définies sur des valeurs relatives à la largeur et à la hauteur de la `Image`. Dans cet exemple, le centre de la `Image` est défini comme étant au centre de la disposition. par conséquent, la valeur de `AnchorX` par défaut de 0,5 ne nécessite pas de modification. Toutefois, la propriété `AnchorY` est redéfinie comme étant une valeur à partir du haut de l' `Image` jusqu’au point central de la disposition. Cela garantit que la `Image` effectue une rotation complète de 360 degrés autour du point central de la disposition, comme illustré dans les captures d’écran suivantes :

![](simple-images/rotate-anchors.png "Rotation Animation with Anchors")

### <a name="translation"></a>Traduction

L’exemple de code suivant illustre l’utilisation de la méthode [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) pour animer les propriétés de [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) et de [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) d’un [`Image`](xref:Xamarin.Forms.Image):

```csharp
await image.TranslateTo (-100, -100, 1000);
```

Ce code anime l’instance de [`Image`](xref:Xamarin.Forms.Image) en le traduisant horizontalement et verticalement sur 1 seconde (1000 millisecondes). La méthode [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) convertit simultanément l’image 100 pixels vers la gauche et 100 pixels vers le haut. Cela est dû au fait que les premier et deuxième arguments sont des nombres négatifs. Le fait de fournir des nombres positifs permet de traduire l’image vers la droite et vers le dessous.

Les captures d’écran suivantes illustrent la traduction en cours sur chaque plateforme :

![](simple-images/translateto.png "Translation Animation")

> [!NOTE]
> Si un élément est initialement disposé hors de l’écran, puis traduit sur l’écran, après la traduction, la disposition d’entrée de l’élément reste hors de l’écran et l’utilisateur ne peut pas interagir avec lui. Par conséquent, il est recommandé de disposer d’une vue à sa position finale, puis d’effectuer toutes les traductions requises.

### <a name="fading"></a>Suppression

L’exemple de code suivant illustre l’utilisation de la méthode [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) pour animer la propriété [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) d’un [`Image`](xref:Xamarin.Forms.Image):

```csharp
image.Opacity = 0;
await image.FadeTo (1, 4000);
```

Ce code anime l’instance de [`Image`](xref:Xamarin.Forms.Image) en la dégradation en plus de 4 secondes (4000 millisecondes). La méthode [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) obtient la valeur actuelle de la propriété [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) pour le début de l’animation, puis apparaît en fondu de cette valeur à son premier argument (1).

Les captures d’écran suivantes montrent le fondu en cours sur chaque plateforme :

![](simple-images/fadeto.png "Fading Animation")

<a name="compound" />

## <a name="compound-animations"></a>Animations composées

Une animation composée est une combinaison séquentielle d’animations et peut être créée avec l’opérateur `await`, comme illustré dans l’exemple de code suivant :

```csharp
await image.TranslateTo (-100, 0, 1000);    // Move image left
await image.TranslateTo (-100, -100, 1000); // Move image up
await image.TranslateTo (100, 100, 2000);   // Move image diagonally down and right
await image.TranslateTo (0, 100, 1000);     // Move image left
await image.TranslateTo (0, 0, 1000);       // Move image up
```

Dans cet exemple, le [`Image`](xref:Xamarin.Forms.Image) est traduit plus de 6 secondes (6000 millisecondes). La traduction de l' `Image` utilise cinq animations, l’opérateur `await` indiquant que chaque animation s’exécute de manière séquentielle. Par conséquent, les méthodes d’animation suivantes s’exécutent après la fin de la méthode précédente.

<a name="composite" />

## <a name="composite-animations"></a>Animations composites

Une animation composite est une combinaison d’animations dans laquelle deux animations ou plus s’exécutent simultanément. Les animations composites peuvent être créées en combinant les animations attendues et non attendues, comme illustré dans l’exemple de code suivant :

```csharp
image.RotateTo (360, 4000);
await image.ScaleTo (2, 2000);
await image.ScaleTo (1, 2000);
```

Dans cet exemple, la [`Image`](xref:Xamarin.Forms.Image) est mise à l’échelle et pivotée simultanément sur 4 secondes (4000 millisecondes). La mise à l’échelle du `Image` utilise deux animations séquentielles qui se produisent en même temps que la rotation. La méthode [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) s’exécute sans opérateur `await` et retourne immédiatement, avec la première animation [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) , puis en commençant par. L’opérateur `await` sur le premier appel de méthode `ScaleTo` retarde le deuxième appel de méthode `ScaleTo` jusqu’à la fin du premier appel de méthode `ScaleTo`. À ce stade, l’animation `RotateTo` est demi-terminée et le `Image` pivote de 180 degrés. Au cours des 2 dernières secondes (2000 millisecondes), la deuxième `ScaleTo` animation et l’animation `RotateTo` toutes deux terminées.

### <a name="running-multiple-asynchronous-methods-concurrently"></a>Exécution simultanée de plusieurs méthodes asynchrones

Les méthodes `static` `Task.WhenAny` et `Task.WhenAll` sont utilisées pour exécuter simultanément plusieurs méthodes asynchrones, et peuvent donc être utilisées pour créer des animations composites. Les deux méthodes retournent un objet `Task` et acceptent une collection de méthodes qui retournent chacune un objet `Task`. La méthode `Task.WhenAny` se termine lorsqu’une méthode de sa collection termine l’exécution, comme illustré dans l’exemple de code suivant :

```csharp
await Task.WhenAny<bool>
(
  image.RotateTo (360, 4000),
  image.ScaleTo (2, 2000)
);
await image.ScaleTo (1, 2000);
```

Dans cet exemple, l’appel de la méthode `Task.WhenAny` contient deux tâches. La première tâche fait pivoter l’image de plus de 4 secondes (4000 millisecondes), tandis que la deuxième tâche met l’image à l’échelle sur 2 secondes (2000 millisecondes). Lorsque la deuxième tâche est terminée, l’appel de la méthode `Task.WhenAny` se termine. Toutefois, même si la méthode [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) est toujours en cours d’exécution, la deuxième méthode [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) peut commencer.

La méthode `Task.WhenAll` se termine lorsque toutes les méthodes de sa collection sont terminées, comme illustré dans l’exemple de code suivant :

```csharp
// 10 minute animation
uint duration = 10 * 60 * 1000;

await Task.WhenAll (
  image.RotateTo (307 * 360, duration),
  image.RotateXTo (251 * 360, duration),
  image.RotateYTo (199 * 360, duration)
);
```

Dans cet exemple, l’appel de la méthode `Task.WhenAll` contient trois tâches, chacune s’exécutant sur 10 minutes. Chaque `Task` effectue un nombre différent de rotations de 360 degrés : 307 rotations pour [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)), 251 rotations pour [`RotateXTo`](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))et des rotations 199 pour [`RotateYTo`](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)). Ces valeurs sont des nombres premiers, garantissant ainsi que les rotations ne sont pas synchronisées et, par conséquent, ne génèrent pas de modèles répétitifs.

Les captures d’écran suivantes montrent les multiples rotations en cours sur chaque plateforme :

![](simple-images/multiple-rotations.png "Composite Animation")

## <a name="canceling-animations"></a>Annulation d’animations

Une application peut annuler une ou plusieurs animations avec un appel à la méthode `static` [`ViewExtensions.CancelAnimations`](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement)) , comme illustré dans l’exemple de code suivant :

```csharp
ViewExtensions.CancelAnimations (image);
```

Cette opération annule immédiatement toutes les animations en cours d’exécution sur l’instance de [`Image`](xref:Xamarin.Forms.Image) .

## <a name="summary"></a>Récapitulatif

Cet article a montré comment créer et annuler des animations à l’aide de la classe [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) . Cette classe fournit des méthodes d’extension qui peuvent être utilisées pour construire des animations simples qui font pivoter, mettre à l’échelle, traduire et estomper des instances de [`VisualElement`](xref:Xamarin.Forms.VisualElement) .

## <a name="related-links"></a>Liens associés

- [Vue d’ensemble de la prise en charge asynchrone](~/cross-platform/platform/async.md)
- [Animation de base (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-basic)
- [ViewExtensions](xref:Xamarin.Forms.ViewExtensions)
