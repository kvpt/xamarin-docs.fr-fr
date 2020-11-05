---
title: Résumé du chapitre 22. Animation
description: 'Création d’Mobile Apps avec Xamarin.Forms : Résumé du chapitre 22. Animation'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 47C2B9AB-E688-4412-8AF5-9F633B3DA695
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f1eca69ac9e5ff802132041f4084a86c3443d745
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374002"
---
# <a name="summary-of-chapter-22-animation"></a>Résumé du chapitre 22. Animation

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)

> [!NOTE]
> Ce livre a été publié à la printemps de 2016 et n’a pas été mis à jour depuis. Le livre reste très utile, mais certains d’entre eux sont obsolètes et certaines rubriques ne sont plus entièrement correctes ou complètes.

Vous avez vu que vous pouvez créer vos propres animations à l’aide du Xamarin.Forms minuteur ou `Task.Delay` , mais il est généralement plus facile d’utiliser les fonctionnalités d’animation fournies par Xamarin.Forms . Trois classes implémentent les animations suivantes :

- [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions), l’approche de haut niveau
- [`Animation`](xref:Xamarin.Forms.Animation)plus polyvalent, mais plus difficile
- [`AnimationExtension`](xref:Xamarin.Forms.AnimationExtensions), l’approche la plus polyvalente et de niveau le plus bas

En général, les animations ciblent les propriétés qui sont sauvegardées par des propriétés pouvant être liées. Ce n’est pas une exigence, mais il s’agit des seules propriétés qui réagissent de manière dynamique aux modifications.

Il n’existe pas d’interface XAML pour ces animations, mais vous pouvez intégrer des animations dans XAML à l’aide de techniques présentées dans le [**chapitre 23. Déclencheurs et comportements**](chapter23.md).

## <a name="exploring-basic-animations"></a>Exploration des animations de base

Les fonctions d’animation de base sont des méthodes d’extension trouvées dans la [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) classe. Ces méthodes s’appliquent à tout objet qui dérive de `VisualElement` . Les animations les plus simples ciblent les propriétés transformations décrites dans [`Chapter 21. Transforms`](chapter21.md) .

Le [**AnimationTryout**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/AnimationTryout) montre comment le `Clicked` Gestionnaire d’événements pour un `Button` peut appeler le [ `RotateTo` ] (XREF : Xamarin.Forms . ViewExtensions. RotateTo ( Xamarin.Forms . VisualElement, System. double, System. UInt32, Xamarin.Forms . Accélération)) pour faire pivoter le bouton dans un cercle.

La `RotateTo` méthode modifie la `Rotation` propriété de la `Button` valeur de 0 à 360 au cours d’un quart de seconde (par défaut). `Button`Toutefois, si le est à nouveau utilisé, il ne fait rien, car la `Rotation` propriété est déjà de 360 degrés.

### <a name="setting-the-animation-duration"></a>Définition de la durée de l’animation

Le deuxième argument de `RotateTo` est une durée en millisecondes. S’il est défini sur une valeur élevée, le fait de cliquer sur le `Button` pendant une animation démarre une nouvelle animation en commençant à l’angle actuel.

### <a name="relative-animations"></a>Animations relatives

La `RelRotateTo` méthode effectue une rotation relative en ajoutant une valeur spécifiée à la valeur existante. Cette méthode permet `Button` à d’être exploitée plusieurs fois, et chaque fois augmente la `Rotation` propriété de 360 degrés.

### <a name="awaiting-animations"></a>En attente d’animations

Toutes les méthodes d’animation dans `ViewExtensions` retournent des `Task<bool>` objets. Cela signifie que vous pouvez définir une série d’animations séquentielles à l’aide `ContinueWith` de ou de `await` . La `bool` valeur de retour de fin est `false` si l’animation s’est terminée sans interruption ou `true` si elle a été annulée par le [ `CancelAnimation` ] (XREF : Xamarin.Forms . ViewExtensions. CancelAnimations ( Xamarin.Forms . VisualElement)), qui annule toutes les animations initiées par l’autre méthode dans `ViewExtensions` qui sont définies sur le même élément.

### <a name="composite-animations"></a>Animations composites

Vous pouvez mélanger des animations await et non attendues pour créer des animations composites. Voici les animations `ViewExtensions` qui ciblent les `TranslationX` Propriétés de `TranslationY` transformation, et `Scale` :

- [ `TranslateTo` ] (XREF : Xamarin.Forms . ViewExtensions. TranslateTo ( Xamarin.Forms . VisualElement, System. double, System. double, System. UInt32, Xamarin.Forms . Accélération))
- [ `ScaleTo` ] (XREF : Xamarin.Forms . ViewExtensions. ScaleTo ( Xamarin.Forms . VisualElement, System. double, System. UInt32, Xamarin.Forms . Accélération))
- [ `RelScaleTo` ] (XREF : Xamarin.Forms . ViewExtensions. RelScaleTo ( Xamarin.Forms . VisualElement, System. double, System. UInt32, Xamarin.Forms . Accélération))

Notez que `TranslateTo` potentiellement affecte à la fois les `TranslationX` `TranslationY` Propriétés et.

### <a name="taskwhenall-and-taskwhenany"></a>Task. WhenAll et Task. WhenAny

Il est également possible de gérer des animations simultanées à l’aide de [`Task.WhenAll`](xref:System.Threading.Tasks.Task.WhenAll*) , qui signale quand plusieurs tâches sont terminées, et [`Task.WhenAny`](xref:System.Threading.Tasks.Task.WhenAny*) , qui signale quand la première des nombreuses tâches est terminée.

### <a name="rotation-and-anchors"></a>Rotation et ancrages

Lors de l’appel des `ScaleTo` méthodes,, `RelScaleTo` `RotateTo` et `RelRotateTo` , vous pouvez définir [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) les [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) Propriétés et pour indiquer le centre de la mise à l’échelle et de la rotation.

Le [**CircleButton**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CircleButton) illustre cette technique en tournant un `Button` autour du centre de la page.

### <a name="easing-functions"></a>Fonctions d’accélération

En général, les animations sont linéaires d’une valeur de début à une valeur de fin. Les fonctions d’accélération peuvent entraîner une accélération ou un ralentissement des animations au cours de leur cours. Le dernier argument facultatif des méthodes d’animation est de type [`Easing`](xref:Xamarin.Forms.Easing) , une classe qui définit 11 champs statiques en lecture seule de type `Easing` :

- [`Linear`](xref:Xamarin.Forms.Easing.Linear), la valeur par défaut
- [`SinIn`](xref:Xamarin.Forms.Easing.SinIn) , [`SinOut`](xref:Xamarin.Forms.Easing.SinOut) et [`SinInOut`](xref:Xamarin.Forms.Easing.SinInOut)
- [`CubicIn`](xref:Xamarin.Forms.Easing.CubicIn) , [`CubicOut`](xref:Xamarin.Forms.Easing.CubicOut) et [`CubicInOut`](xref:Xamarin.Forms.Easing.CubicInOut)
- [`BounceIn`](xref:Xamarin.Forms.Easing.BounceIn) les [`BounceOut`](xref:Xamarin.Forms.Easing.BounceOut)
- [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn) les [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut)

Le `In` suffixe indique que l’effet se trouve au début de l’animation, `Out` signifie à la fin et `InOut` signifie qu’il est au début et à la fin de l’animation.

L’exemple [**BounceButton**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BounceButton) illustre l’utilisation de fonctions d’accélération.

### <a name="your-own-easing-functions"></a>Vos propres fonctions d’accélération

Vous pouvez également définir vos propres fonctions d’accélération en passant un [`Func<double, double>`](xref:System.Func`2) au [ `Easing` constructeur](xref:Xamarin.Forms.Easing.%23ctor(System.Func{System.Double,System.Double})). `Easing` définit également une conversion implicite de `Func<double, double>` en `Easing` . L’argument de la fonction d’accélération est toujours compris entre 0 et 1, car l’animation se poursuit de façon linéaire du début à la fin. La fonction retourne *généralement* une valeur comprise entre 0 et 1, mais elle peut être brièvement négative ou supérieure à 1 (comme c’est le cas avec les `SpringIn` `SpringOut` fonctions et) ou peut arrêter les règles si vous savez ce que vous faites.

L’exemple [**UneasyScale**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/UneasyScale) illustre une fonction d’accélération personnalisée, et [**CustomCubicEase**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CustomCubicEase) en illustre une autre.

L’exemple [**SwingButton**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingButton) illustre également une fonction d’accélération personnalisée, ainsi qu’une technique permettant de modifier les `AnchorX` `AnchorY` Propriétés et dans une séquence d’animations de rotation.

La bibliothèque [**Xamarin.Forms book. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) a une [`JiggleButton`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/JiggleButton.cs) classe qui utilise une fonction d’accélération personnalisée pour agiter un bouton quand l’utilisateur clique dessus. L’exemple [**JiggleButtonDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/JiggleButtonDemo) le montre.

### <a name="entrance-animations"></a>Animations d’entrée

Un type d’animation courant se produit lorsqu’une page s’affiche pour la première fois. Une telle animation peut être démarrée dans la [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) substitution de la page. Pour ces animations, il est préférable de configurer le code XAML pour la façon dont vous souhaitez que la page apparaisse *après* l’animation, puis d’initialiser et d’animer la disposition à partir du code.

L’exemple [**FadingEntrance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingEntrance) utilise la [ `FadeTo` ] (XREF : Xamarin.Forms . ViewExtensions. FadeTo ( Xamarin.Forms . VisualElement, System. double, System. UInt32, Xamarin.Forms . Accélération)) pour effectuer un fondu dans le contenu de la page.

L’exemple [**SlidingEntrance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SlidingEntrance) utilise la [ `TranslateTo` ] (XREF : Xamarin.Forms . ViewExtensions. TranslateTo ( Xamarin.Forms . VisualElement, System. double, System. double, System. UInt32, Xamarin.Forms . Accélération)) pour faire glisser le contenu de la page à partir des côtés.

L’exemple [**SwingingEntrance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingingEntrance) utilise la [ `RotateYTo` ] (XREF : Xamarin.Forms . ViewExtensions. RotateYTo ( Xamarin.Forms . VisualElement, System. double, System. UInt32, Xamarin.Forms . Accélération)) pour animer la `RotationY` propriété. A [ `RotateXTo` ] (XREF : Xamarin.Forms . ViewExtensions. RotateXTo ( Xamarin.Forms . VisualElement, System. double, System. UInt32, Xamarin.Forms . Accélération)) est également disponible.

### <a name="forever-animations"></a>Animations à l’infini

À l’autre extrême, les animations « toujours » s’exécutent jusqu’à la fin du programme. Ils sont généralement destinés à des fins de démonstration.

L’exemple [**FadingTextAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingTextAnimation) utilise [ `FadeTo` ] (XREF : Xamarin.Forms . ViewExtensions. FadeTo ( Xamarin.Forms . VisualElement, System. double, System. UInt32, Xamarin.Forms . Accélération)) pour estomper deux parties de texte.

[**PalindromeAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/PalindromeAnimation) affiche un palindrome, puis fait pivoter séquentiellement les lettres individuelles de 180 degrés afin qu’elles soient toutes à l’envers. Ensuite, la chaîne entière est retournée de 180 degrés pour lire le même que la chaîne d’origine.

L’exemple [**CopterAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CopterAnimation) fait pivoter un `BoxView` hélicoptère simple tout en le faisant tourner au centre de l’écran.

[**RotatingSpokes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotatingSpokes) `BoxView` fait tourner les rayons au centre de l’écran, puis fait pivoter chaque rayon pour créer des modèles intéressants :

[![Capture d’écran triple des rayons rotatifs](images/ch22fg21-small.png "Rotation des rayons")](images/ch22fg21-large.png#lightbox "Rotation des rayons")

Toutefois, l’extension progressive `Rotation` de la propriété d’un élément peut ne pas fonctionner à long terme, comme le montre l’exemple [**RotationBreakdown**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotationBreakdown) .

L’exemple [**SpinningImage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpinningImage) utilise [ `RotateTo` ] (XREF : Xamarin.Forms . ViewExtensions. RotateTo ( Xamarin.Forms . VisualElement, System. double, System. UInt32, Xamarin.Forms . Accélération)), [ `RotateXTo` ] (XREF : Xamarin.Forms . ViewExtensions. RotateXTo ( Xamarin.Forms . VisualElement, System. double, System. UInt32, Xamarin.Forms . Accélération)) et [ `RotateYTo` ] (XREF : Xamarin.Forms . ViewExtensions. RotateYTo ( Xamarin.Forms . VisualElement, System. double, System. UInt32, Xamarin.Forms . Accélération)) pour faire croire qu’une image bitmap est en rotation dans l’espace 3D.

### <a name="animating-the-bounds-property"></a>Animer la propriété Bounds

La seule méthode d’extension de `ViewExtensions` n’est pas encore démontrée est [ `LayoutTo` ] (XREF : Xamarin.Forms . ViewExtensions. LayoutTo ( Xamarin.Forms . VisualElement, Xamarin.Forms . Rectangle, System. UInt32, Xamarin.Forms . Accélération)), qui anime efficacement la propriété en lecture seule [`Bounds`](xref:Xamarin.Forms.VisualElement.Bounds) en appelant le [ `Layout` ] (XREF : Xamarin.Forms . VisualElement. Layout ( Xamarin.Forms . Rectangle)). Cette méthode est normalement appelée par `Layout` les dérivés comme indiqué dans le [**chapitre 26. CustomLayouts**](chapter26.md).

La `LayoutTo` méthode doit être limitée à des fins spéciales. Le programme [**BouncingBox**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BouncingBox) l’utilise pour compresser et développer un `BoxView` lorsqu’il rebondit sur les côtés d’une page.

L’exemple [**XamagonXuzzle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle) utilise `LayoutTo` pour déplacer des vignettes dans une implémentation du puzzle 15-16 classique qui affiche une image brouillée plutôt que des vignettes numérotées :

[![Capture d’écran triple de Xamarin Xuzzle](images/ch22fg26-small.png "Jeu de puzzle Xuzzle")](images/ch22fg26-large.png#lightbox "Jeu de puzzle Xuzzle")

### <a name="your-own-awaitable-animations"></a>Vos propres animations await

L’exemple [**TryAwaitableAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/TryAwaitableAnimation) crée une animation pouvant être attendue. La classe essentielle qui peut retourner un `Task` objet à partir de la méthode et signal lorsque l’animation est terminée est [`TaskCompletionSource`](xref:System.Threading.Tasks.TaskCompletionSource`1) .

## <a name="deeper-into-animations"></a>Plus profondément dans les animations

Le Xamarin.Forms système d’animation peut être un peu confus. En plus de la `Easing` classe, le système d’animation comprend `ViewExtensions` les `Animation` classes, et `AnimationExtension` .

### <a name="viewextensions-class"></a>ViewExtensions, classe

Vous avez déjà vu [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) . Il définit neuf méthodes qui retournent `Task<bool>` et [ `CancelAnimations` ] (XREF : Xamarin.Forms . ViewExtensions. CancelAnimations ( Xamarin.Forms . VisualElement)). Sept des neuf méthodes ciblent les propriétés de transformation. Les deux autres sont [ `FadeTo` ] (XREF : Xamarin.Forms . ViewExtensions. FadeTo ( Xamarin.Forms . VisualElement, System. double, System. UInt32, Xamarin.Forms . Accélération)), qui cible la [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) propriété, et [ `LayoutTo` ] (XREF : Xamarin.Forms . ViewExtensions. LayoutTo ( Xamarin.Forms . VisualElement, Xamarin.Forms . Rectangle, System. UInt32, Xamarin.Forms . Accélération)), qui appelle la [ `Layout` ] (XREF : Xamarin.Forms . VisualElement. Layout ( Xamarin.Forms . Rectangle)).

### <a name="animation-class"></a>Classe d’animation

La [`Animation`](xref:Xamarin.Forms.AnimationExtensions) classe a un [constructeur] (XREF : Xamarin.Forms . Animation .% 23ctor (System. Action {System. double}, System. double, System. double, Xamarin.Forms . Accélération, System. action)) avec cinq arguments pour définir les méthodes de rappel et de fin, ainsi que les paramètres de l’animation.

Les animations enfants peuvent être ajoutées avec [ `Add` ] (XREF : Xamarin.Forms . Animation. Add (System. double, System. double, Xamarin.Forms . Animation)), [ `Insert` ] (XREF : Xamarin.Forms . Animation. Insert (System. double, System. double, Xamarin.Forms . Animation)), [ `WithConcurrent` ] (XREF : Xamarin.Forms . Animation. WithConcurrent ( Xamarin.Forms . Animation, System. double, System. double)) et surcharge de [ `WithConcurrent` ] (XREF : Xamarin.Forms . Animation. WithConcurrent (System. Action {System. double}, System. double, System. double, Xamarin.Forms . Accélération, System. double, System. double).

L’objet d’animation est ensuite démarré avec un appel à [ `Commit` ] (XREF : Xamarin.Forms . Animation. Commit ( Xamarin.Forms . IAnimatable, System. String, System. UInt32, System. UInt32, Xamarin.Forms . Méthode d’accélération, System. Action {System. double, System. Boolean}, System. Func {System. Boolean})).

### <a name="animationextensions-class"></a>AnimationExtensions, classe

La [`AnimationExtensions`](xref:Xamarin.Forms.AnimationExtensions) classe contient principalement des méthodes d’extension. Il existe plusieurs versions d’une `Animate` méthode et la [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) méthode générique est tellement polyvalente qu’il s’agit vraiment de la seule fonction d’animation dont vous avez besoin.

## <a name="working-with-the-animation-class"></a>Utilisation de la classe animation

L’exemple [**ConcurrentAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) illustre la [`Animation`](xref:Xamarin.Forms.Animation) classe avec plusieurs animations différentes.

### <a name="child-animations"></a>Animations enfants

L’exemple [**ConcurrentAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) illustre également les animations enfants, qui utilisent le (très similaire) [ `Add` ] (XREF : Xamarin.Forms . Animation. Add (System. double, System. double, Xamarin.Forms . Animation)) et [ `Insert` ] (XREF : Xamarin.Forms . Animation. Insert (System. double, System. double, Xamarin.Forms . Animation)).

### <a name="beyond-the-high-level-animation-methods"></a>Au-delà des méthodes d’animation de haut niveau

L’exemple [**ConcurrentAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) montre également comment exécuter des animations qui dépassent les propriétés ciblées par les `ViewExtensions` méthodes. Dans un exemple, une série de périodes est plus longue ; dans un autre exemple, une `BackgroundColor` propriété est animée.

### <a name="more-of-your-own-awaitable-methods"></a>Plus de vos propres méthodes await

[ `TranslateTo` ] (XREF : Xamarin.Forms . ViewExtensions. TranslateTo ( Xamarin.Forms . VisualElement, System. double, System. double, System. UInt32, Xamarin.Forms . Accélération)) `ViewExtensions` ne fonctionne pas avec la [`Easing.SpringOut`](xref:Xamarin.Forms.Easing.SpringOut) fonction. Elle s’arrête lorsque la sortie d’accélération est supérieure à 1.

La bibliothèque [**Xamarin.Forms book. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contient une [`MoreViewExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) classe avec [`TranslateXTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L12) des [`TranslateYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L49) méthodes d’extension et qui n’ont pas ce problème, ainsi que les [`CancelTranslateXTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L44) [`CancelTranslateYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L71) méthodes et pour annuler ces animations.

Le [**SpringSlidingEntrance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpringSlidingEntrance) illustre la `TranslateXTo` méthode.

La `MoreExtensions` classe contient également une  [`TranslateXYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L76) méthode d’extension qui combine les traductions X et Y, et une [`CancelTranslateXYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L113) méthode.

### <a name="implementing-a-bezier-animation"></a>Implémentation d’une animation Bézier

Il est également possible de développer une animation qui déplace un élément le long du tracé d’une spline de Bézier. La bibliothèque [**Xamarin.Forms book. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contient une [`BezierSpline`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierSpline.cs) structure qui encapsule une spline de Bézier et une [`BezierTangent`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierTangent.cs) énumération pour contrôler l’orientation.

La [`MoreViewExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) classe contient une [`BezierPathTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L118) méthode d’extension et une [`CancelBezierPathTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L161) méthode.

L’exemple [**BezierLoop**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BezierLoop) montre comment animer un élément le long d’un chemin d’accès Beizer.

## <a name="working-with-animationextensions"></a>Utilisation de AnimationExtensions

Un type d’animation manquant dans la collection standard est une animation de couleur. Le problème est qu’il n’existe aucun moyen d’interpoler entre deux `Color` valeurs. Il est possible d’interpoler les valeurs RVB individuelles, mais le plus simple est d’interpoler les valeurs TSL.

Pour cette raison, la [`MoreViewExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) classe de la bibliothèque [**Xamarin.Forms book. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contient deux `Color` méthodes d’animation : [`RgbColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L166) et [`HslColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L188) . (Il existe également deux méthodes d’annulation : [`CancelRgbColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L183) et [`CancelHslColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L206) ).

Les deux méthodes utilisent [`ColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L211) , qui exécute l’animation en appelant la méthode générique étendue [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) dans [`AnimationExtensions`](xref:Xamarin.Forms.AnimationExtensions) .

L’exemple [**ColorAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ColorAnimations) illustre l’utilisation de ces deux types d’animations de couleurs.

## <a name="structuring-your-animations"></a>Structuration de vos animations

Il est parfois utile d’exprimer des animations en XAML et de les utiliser conjointement avec MVVM. Ce sujet est abordé dans le chapitre [**23 suivant. Déclencheurs et comportements**](chapter23.md).

## <a name="related-links"></a>Liens connexes

- [Chapitre 22 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)
- [Exemples du chapitre 22](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)
- [Animation](~/xamarin-forms/user-interface/animation/index.md)
