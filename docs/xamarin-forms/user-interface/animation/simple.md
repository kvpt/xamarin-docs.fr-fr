---
title : "animations simples dans Xamarin.Forms " Description : "la classe ViewExtensions fournit des méthodes d’extension qui peuvent être utilisées pour construire des animations simples. Cet article explique comment créer et annuler des animations à l’aide de la classe ViewExtensions.
ms. Prod : xamarin ms. AssetID : 4A6FAE5A-848F-4CE0-BFA1-22A6309B5225 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 11/05/2019 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="simple-animations-in-xamarinforms"></a>Animations simples dansXamarin.Forms

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-basic)

_La classe ViewExtensions fournit des méthodes d’extension qui peuvent être utilisées pour construire des animations simples. Cet article explique comment créer et annuler des animations à l’aide de la classe ViewExtensions._

La [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) classe fournit les méthodes d’extension suivantes qui peuvent être utilisées pour créer des animations simples :

- [ `TranslateTo` ] (XREF : Xamarin.Forms . ViewExtensions. TranslateTo ( Xamarin.Forms . VisualElement, System. double, System. double, System. UInt32, Xamarin.Forms . Accélération)) anime les [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) Propriétés et [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) d’un [`VisualElement`](xref:Xamarin.Forms.VisualElement) .
- [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*)anime la [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) propriété d’un [`VisualElement`](xref:Xamarin.Forms.VisualElement) .
- `ScaleXTo`anime la [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX) propriété d’un [`VisualElement`](xref:Xamarin.Forms.VisualElement) .
- `ScaleYTo`anime la [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY) propriété d’un [`VisualElement`](xref:Xamarin.Forms.VisualElement) .
- [ `RelScaleTo` ] (XREF : Xamarin.Forms . ViewExtensions. RelScaleTo ( Xamarin.Forms . VisualElement, System. double, System. UInt32, Xamarin.Forms . Accélération)) applique une augmentation ou une diminution incrémentielle animée à la [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) propriété d’un [`VisualElement`](xref:Xamarin.Forms.VisualElement) .
- [ `RotateTo` ] (XREF : Xamarin.Forms . ViewExtensions. RotateTo ( Xamarin.Forms . VisualElement, System. double, System. UInt32, Xamarin.Forms . Accélération)) anime la [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) propriété d’un [`VisualElement`](xref:Xamarin.Forms.VisualElement) .
- [ `RelRotateTo` ] (XREF : Xamarin.Forms . ViewExtensions. RelRotateTo ( Xamarin.Forms . VisualElement, System. double, System. UInt32, Xamarin.Forms . Accélération)) applique une augmentation ou une diminution incrémentielle animée à la [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) propriété d’un [`VisualElement`](xref:Xamarin.Forms.VisualElement) .
- [ `RotateXTo` ] (XREF : Xamarin.Forms . ViewExtensions. RotateXTo ( Xamarin.Forms . VisualElement, System. double, System. UInt32, Xamarin.Forms . Accélération)) anime la [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX) propriété d’un [`VisualElement`](xref:Xamarin.Forms.VisualElement) .
- [ `RotateYTo` ] (XREF : Xamarin.Forms . ViewExtensions. RotateYTo ( Xamarin.Forms . VisualElement, System. double, System. UInt32, Xamarin.Forms . Accélération)) anime la [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY) propriété d’un [`VisualElement`](xref:Xamarin.Forms.VisualElement) .
- [ `FadeTo` ] (XREF : Xamarin.Forms . ViewExtensions. FadeTo ( Xamarin.Forms . VisualElement, System. double, System. UInt32, Xamarin.Forms . Accélération)) anime la [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) propriété d’un [`VisualElement`](xref:Xamarin.Forms.VisualElement) .

Par défaut, chaque animation prendra 250 millisecondes. Toutefois, une durée pour chaque animation peut être spécifiée lors de la création de l’animation.

La [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) classe comprend également un [ `CancelAnimations` ] (XREF : Xamarin.Forms . ViewExtensions. CancelAnimations ( Xamarin.Forms . VisualElement)) qui peut être utilisée pour annuler les animations.

> [!NOTE]
> La [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) classe fournit un [ `LayoutTo` ] (XREF : Xamarin.Forms . ViewExtensions. LayoutTo ( Xamarin.Forms . VisualElement, Xamarin.Forms . Rectangle, System. UInt32, Xamarin.Forms . Accélération)). Toutefois, cette méthode est destinée à être utilisée par les dispositions pour animer des transitions entre des États de disposition qui contiennent des modifications de taille et de position. Par conséquent, il doit être utilisé uniquement par les [`Layout`](xref:Xamarin.Forms.Layout) sous-classes.

Les méthodes d’extension d’animation de la [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) classe sont toutes asynchrones et retournent un `Task<bool>` objet. La valeur de retour est `false` si l’animation se termine, et `true` si l’animation est annulée. Par conséquent, les méthodes d’animation doivent généralement être utilisées avec l' `await` opérateur, ce qui permet de déterminer facilement quand une animation est terminée. En outre, il devient possible de créer des animations séquentielles avec les méthodes d’animation suivantes s’exécutant après la fin de la méthode précédente. Pour plus d’informations, consultez [animations composées](#compound-animations).

S’il est nécessaire de laisser une animation se terminer en arrière-plan, l' `await` opérateur peut être omis. Dans ce scénario, les méthodes d’extension d’animation sont rapidement retournées après l’initialisation de l’animation, avec l’animation qui se produit en arrière-plan. Cette opération peut être utilisée lors de la création d’animations composites. Pour plus d’informations, consultez [animations composites](#composite-animations).

Pour plus d’informations sur l' `await` opérateur, consultez [vue d’ensemble de la prise en charge asynchrone](~/cross-platform/platform/async.md).

## <a name="single-animations"></a>Animations uniques

Chaque méthode d’extension dans le [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) implémente une seule opération d’animation qui modifie progressivement une propriété d’une valeur à une autre sur une période donnée. Cette section explore chaque opération d’animation.

### <a name="rotation"></a>Rotation

L’exemple de code suivant illustre l’utilisation de la [ `RotateTo` ] (XREF : Xamarin.Forms . ViewExtensions. RotateTo ( Xamarin.Forms . VisualElement, System. double, System. UInt32, Xamarin.Forms . Accélération)) pour animer la [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) propriété d’un [`Image`](xref:Xamarin.Forms.Image) :

```csharp
await image.RotateTo (360, 2000);
image.Rotation = 0;
```

Ce code anime l' [`Image`](xref:Xamarin.Forms.Image) instance en faisant pivoter jusqu’à 360 degrés sur 2 secondes (2000 millisecondes). [ `RotateTo` ] (XREF : Xamarin.Forms . ViewExtensions. RotateTo ( Xamarin.Forms . VisualElement, System. double, System. UInt32, Xamarin.Forms . Accélération)) obtient la [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) valeur de propriété actuelle pour le début de l’animation, puis pivote de cette valeur vers son premier argument (360). Une fois l’animation terminée, la propriété de l’image [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) est réinitialisée à 0. Cela garantit que la `Rotation` propriété ne reste pas à 360 une fois l’animation terminée, ce qui empêche les rotations supplémentaires.

Les captures d’écran suivantes montrent la rotation en cours sur chaque plateforme :

![](simple-images/rotateto.png "Rotation Animation")

> [!NOTE]
> En plus de [ `RotateTo` ] (XREF : Xamarin.Forms . ViewExtensions. RotateTo ( Xamarin.Forms . VisualElement, System. double, System. UInt32, Xamarin.Forms . Accélération)), il y a également [ `RotateXTo` ] (XREF : Xamarin.Forms . ViewExtensions. RotateXTo ( Xamarin.Forms . VisualElement, System. double, System. UInt32, Xamarin.Forms . Accélération)) et [ `RotateYTo` ] (XREF : Xamarin.Forms . ViewExtensions. RotateYTo ( Xamarin.Forms . VisualElement, System. double, System. UInt32, Xamarin.Forms . Accélération)) qui animent les [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX) [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY) Propriétés et, respectivement.

### <a name="relative-rotation"></a>Rotation relative

L’exemple de code suivant illustre l’utilisation de la [ `RelRotateTo` ] (XREF : Xamarin.Forms . ViewExtensions. RelRotateTo ( Xamarin.Forms . VisualElement, System. double, System. UInt32, Xamarin.Forms . Accélération)) pour augmenter ou diminuer de manière incrémentielle la [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) propriété d’un [`Image`](xref:Xamarin.Forms.Image) :

```csharp
await image.RelRotateTo (360, 2000);
```

Ce code anime l' [`Image`](xref:Xamarin.Forms.Image) instance en faisant pivoter 360 degrés de sa position de départ sur 2 secondes (2000 millisecondes). [ `RelRotateTo` ] (XREF : Xamarin.Forms . ViewExtensions. RelRotateTo ( Xamarin.Forms . VisualElement, System. double, System. UInt32, Xamarin.Forms . Accélération)) obtient la [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) valeur de propriété actuelle pour le début de l’animation, puis pivote de cette valeur à la valeur plus son premier argument (360). Cela garantit que chaque animation sera toujours une rotation de 360 degrés à partir de la position de départ. Par conséquent, si une nouvelle animation est appelée alors qu’une animation est déjà en cours, elle démarre à partir de la position actuelle et peut se terminer à une position qui n’est pas un incrément de 360 degrés.

Les captures d’écran suivantes montrent la rotation relative en cours sur chaque plateforme :

![](simple-images/relrotateto.png "Relative Rotation Animation")

### <a name="scaling"></a>Mise à l'échelle

L’exemple de code suivant illustre l’utilisation de la [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) méthode pour animer la [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) propriété d’un [`Image`](xref:Xamarin.Forms.Image) :

```csharp
await image.ScaleTo (2, 2000);
```

Ce code anime l' [`Image`](xref:Xamarin.Forms.Image) instance en mettant à l’échelle jusqu’à deux fois sa taille sur 2 secondes (2000 millisecondes). La [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) méthode obtient la valeur de [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) propriété actuelle (valeur par défaut de 1) pour le début de l’animation, puis met à l’échelle de cette valeur à son premier argument (2). Cela a pour effet d’étendre la taille de l’image à deux fois sa taille.

Les captures d’écran suivantes illustrent la mise à l’échelle en cours sur chaque plateforme :

![](simple-images/scaleto.png "Scaling Animation")

> [!NOTE]
> En plus de la [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) méthode, il existe également `ScaleXTo` des `ScaleYTo` méthodes et qui animent les [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX) [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY) Propriétés et, respectivement.

### <a name="relative-scaling"></a>Mise à l’échelle relative

L’exemple de code suivant illustre l’utilisation de la [ `RelScaleTo` ] (XREF : Xamarin.Forms . ViewExtensions. RelScaleTo ( Xamarin.Forms . VisualElement, System. double, System. UInt32, Xamarin.Forms . Accélération)) pour animer la [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) propriété d’un [`Image`](xref:Xamarin.Forms.Image) :

```csharp
await image.RelScaleTo (2, 2000);
```

Ce code anime l' [`Image`](xref:Xamarin.Forms.Image) instance en mettant à l’échelle jusqu’à deux fois sa taille sur 2 secondes (2000 millisecondes). [ `RelScaleTo` ] (XREF : Xamarin.Forms . ViewExtensions. RelScaleTo ( Xamarin.Forms . VisualElement, System. double, System. UInt32, Xamarin.Forms . Accélération)) obtient la [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) valeur de propriété actuelle pour le début de l’animation, puis met à l’échelle de cette valeur à la valeur plus son premier argument (2). Cela garantit que chaque animation sera toujours une mise à l’échelle de 2 à partir de la position de départ.

### <a name="scaling-and-rotation-with-anchors"></a>Mise à l’échelle et rotation avec les ancres

Les [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) Propriétés et définissent le centre de mise à l’échelle ou de rotation pour les [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) Propriétés et. Par conséquent, leurs valeurs affectent également le [ `RotateTo` ] (XREF : Xamarin.Forms . ViewExtensions. RotateTo ( Xamarin.Forms . VisualElement, System. double, System. UInt32, Xamarin.Forms . Accélération)) et des [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) méthodes.

À partir d’un [`Image`](xref:Xamarin.Forms.Image) qui a été placé au centre d’une disposition, l’exemple de code suivant montre comment faire pivoter l’image autour du centre de la disposition en définissant sa [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) propriété :

```csharp
double radius = Math.Min(absoluteLayout.Width, absoluteLayout.Height) / 2;
image.AnchorY = radius / image.Height;
await image.RotateTo(360, 2000);
```

Pour faire pivoter l' [`Image`](xref:Xamarin.Forms.Image) instance autour du centre de la disposition, les [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) Propriétés et doivent être définies sur des valeurs relatives à la largeur et à la hauteur du `Image` . Dans cet exemple, le centre de `Image` est défini pour être au centre de la disposition, de sorte que la valeur par défaut `AnchorX` de 0,5 ne nécessite pas de modification. Toutefois, la `AnchorY` propriété est redéfinie comme étant une valeur à partir du haut de l’interface `Image` jusqu’au point central de la disposition. Cela garantit que `Image` effectue une rotation complète de 360 degrés autour du point central de la disposition, comme illustré dans les captures d’écran suivantes :

![](simple-images/rotate-anchors.png "Rotation Animation with Anchors")

### <a name="translation"></a>Traduction

L’exemple de code suivant illustre l’utilisation de la [ `TranslateTo` ] (XREF : Xamarin.Forms . ViewExtensions. TranslateTo ( Xamarin.Forms . VisualElement, System. double, System. double, System. UInt32, Xamarin.Forms . Accélération)) pour animer les [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) Propriétés et d’un [`Image`](xref:Xamarin.Forms.Image) :

```csharp
await image.TranslateTo (-100, -100, 1000);
```

Ce code anime l' [`Image`](xref:Xamarin.Forms.Image) instance en le traduisant horizontalement et verticalement sur 1 seconde (1000 millisecondes). [ `TranslateTo` ] (XREF : Xamarin.Forms . ViewExtensions. TranslateTo ( Xamarin.Forms . VisualElement, System. double, System. double, System. UInt32, Xamarin.Forms . Accélération)) convertit simultanément l’image 100 pixels vers la gauche et 100 pixels vers le haut. Cela est dû au fait que les premier et deuxième arguments sont des nombres négatifs. Le fait de fournir des nombres positifs permet de traduire l’image vers la droite et vers le dessous.

Les captures d’écran suivantes illustrent la traduction en cours sur chaque plateforme :

![](simple-images/translateto.png "Translation Animation")

> [!NOTE]
> Si un élément est initialement disposé hors de l’écran, puis traduit sur l’écran, après la traduction, la disposition d’entrée de l’élément reste hors de l’écran et l’utilisateur ne peut pas interagir avec lui. Par conséquent, il est recommandé de disposer d’une vue à sa position finale, puis d’effectuer toutes les traductions requises.

### <a name="fading"></a>Suppression

L’exemple de code suivant illustre l’utilisation de la [ `FadeTo` ] (XREF : Xamarin.Forms . ViewExtensions. FadeTo ( Xamarin.Forms . VisualElement, System. double, System. UInt32, Xamarin.Forms . Accélération)) pour animer la [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) propriété d’un [`Image`](xref:Xamarin.Forms.Image) :

```csharp
image.Opacity = 0;
await image.FadeTo (1, 4000);
```

Ce code anime l' [`Image`](xref:Xamarin.Forms.Image) instance en l’atténuant en plus de 4 secondes (4000 millisecondes). [ `FadeTo` ] (XREF : Xamarin.Forms . ViewExtensions. FadeTo ( Xamarin.Forms . VisualElement, System. double, System. UInt32, Xamarin.Forms . Accélération)) obtient la [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) valeur de propriété actuelle pour le début de l’animation, puis apparaît en fondu de cette valeur à son premier argument (1).

Les captures d’écran suivantes montrent le fondu en cours sur chaque plateforme :

![](simple-images/fadeto.png "Fading Animation")

## <a name="compound-animations"></a>Animations composées

Une animation composée est une combinaison séquentielle d’animations et peut être créée avec l' `await` opérateur, comme illustré dans l’exemple de code suivant :

```csharp
await image.TranslateTo (-100, 0, 1000);    // Move image left
await image.TranslateTo (-100, -100, 1000); // Move image up
await image.TranslateTo (100, 100, 2000);   // Move image diagonally down and right
await image.TranslateTo (0, 100, 1000);     // Move image left
await image.TranslateTo (0, 0, 1000);       // Move image up
```

Dans cet exemple, le [`Image`](xref:Xamarin.Forms.Image) est converti en 6 secondes (6000 millisecondes). La traduction du `Image` utilise cinq animations, l' `await` opérateur indiquant que chaque animation s’exécute de manière séquentielle. Par conséquent, les méthodes d’animation suivantes s’exécutent après la fin de la méthode précédente.

## <a name="composite-animations"></a>Animations composites

Une animation composite est une combinaison d’animations dans laquelle deux animations ou plus s’exécutent simultanément. Les animations composites peuvent être créées en combinant les animations attendues et non attendues, comme illustré dans l’exemple de code suivant :

```csharp
image.RotateTo (360, 4000);
await image.ScaleTo (2, 2000);
await image.ScaleTo (1, 2000);
```

Dans cet exemple, la est mise à l' [`Image`](xref:Xamarin.Forms.Image) échelle et pivotée simultanément sur 4 secondes (4000 millisecondes). La mise à l’échelle du `Image` utilise deux animations séquentielles qui se produisent en même temps que la rotation. [ `RotateTo` ] (XREF : Xamarin.Forms . ViewExtensions. RotateTo ( Xamarin.Forms . VisualElement, System. double, System. UInt32, Xamarin.Forms . Accélération)) s’exécute sans `await` opérateur et est retourné immédiatement, avec la première [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) animation, puis en commençant par. L' `await` opérateur sur le premier `ScaleTo` appel de méthode retarde le deuxième appel de méthode `ScaleTo` jusqu’à ce que le premier `ScaleTo` appel de méthode soit terminé. À ce stade `RotateTo` , l’animation est terminée et le `Image` sera pivoté de 180 degrés. Au cours des 2 dernières secondes (2000 millisecondes), la deuxième `ScaleTo` animation et l' `RotateTo` animation sont toutes deux terminées.

### <a name="running-multiple-asynchronous-methods-concurrently"></a>Exécution simultanée de plusieurs méthodes asynchrones

Les `static` `Task.WhenAny` `Task.WhenAll` méthodes et sont utilisées pour exécuter simultanément plusieurs méthodes asynchrones et peuvent donc être utilisées pour créer des animations composites. Les deux méthodes retournent un `Task` objet et acceptent une collection de méthodes qui retournent chacune un `Task` objet. La `Task.WhenAny` méthode se termine lorsque l’exécution d’une méthode dans sa collection est terminée, comme illustré dans l’exemple de code suivant :

```csharp
await Task.WhenAny<bool>
(
  image.RotateTo (360, 4000),
  image.ScaleTo (2, 2000)
);
await image.ScaleTo (1, 2000);
```

Dans cet exemple, l' `Task.WhenAny` appel de méthode contient deux tâches. La première tâche fait pivoter l’image de plus de 4 secondes (4000 millisecondes), tandis que la deuxième tâche met l’image à l’échelle sur 2 secondes (2000 millisecondes). Lorsque la deuxième tâche est terminée, l' `Task.WhenAny` appel de la méthode se termine. Toutefois, même si le [ `RotateTo` ] (XREF : Xamarin.Forms . ViewExtensions. RotateTo ( Xamarin.Forms . VisualElement, System. double, System. UInt32, Xamarin.Forms . Accélération)) est toujours en cours d’exécution, la deuxième [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) méthode peut commencer.

La `Task.WhenAll` méthode se termine lorsque toutes les méthodes de sa collection sont terminées, comme illustré dans l’exemple de code suivant :

```csharp
// 10 minute animation
uint duration = 10 * 60 * 1000;

await Task.WhenAll (
  image.RotateTo (307 * 360, duration),
  image.RotateXTo (251 * 360, duration),
  image.RotateYTo (199 * 360, duration)
);
```

Dans cet exemple, l' `Task.WhenAll` appel de méthode contient trois tâches, chacune s’exécutant sur 10 minutes. Chacun `Task` effectue un nombre différent de rotations de 360 degrés – 307 rotations pour [ `RotateTo` ] (XREF : Xamarin.Forms . ViewExtensions. RotateTo ( Xamarin.Forms . VisualElement, System. double, System. UInt32, Xamarin.Forms . Accélération)), 251 rotations pour [ `RotateXTo` ] (XREF : Xamarin.Forms . ViewExtensions. RotateXTo ( Xamarin.Forms . VisualElement, System. double, System. UInt32, Xamarin.Forms . Accélération)) et les rotations 199 pour [ `RotateYTo` ] (XREF : Xamarin.Forms . ViewExtensions. RotateYTo ( Xamarin.Forms . VisualElement, System. double, System. UInt32, Xamarin.Forms . Accélération)). Ces valeurs sont des nombres premiers, garantissant ainsi que les rotations ne sont pas synchronisées et, par conséquent, ne génèrent pas de modèles répétitifs.

Les captures d’écran suivantes montrent les multiples rotations en cours sur chaque plateforme :

![](simple-images/multiple-rotations.png "Composite Animation")

## <a name="canceling-animations"></a>Annulation d’animations

Une application peut annuler une ou plusieurs animations avec un appel à `static` [ `ViewExtensions.CancelAnimations` ] (XREF : Xamarin.Forms . ViewExtensions. CancelAnimations ( Xamarin.Forms . VisualElement)), comme illustré dans l’exemple de code suivant :

```csharp
ViewExtensions.CancelAnimations (image);
```

Cette opération annule immédiatement toutes les animations en cours d’exécution sur l' [`Image`](xref:Xamarin.Forms.Image) instance.

## <a name="summary"></a>Résumé

Cet article a montré comment créer et annuler des animations à l’aide de la [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) classe. Cette classe fournit des méthodes d’extension qui peuvent être utilisées pour construire des animations simples qui font pivoter, mettre à l’échelle, traduire et estomper des [`VisualElement`](xref:Xamarin.Forms.VisualElement) instances.

## <a name="related-links"></a>Liens connexes

- [Vue d’ensemble de la prise en charge Async](~/cross-platform/platform/async.md)
- [Animation de base (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-basic)
- [ViewExtensions](xref:Xamarin.Forms.ViewExtensions)
