---
title: Résumé du chapitre 22. Animation
description: 'Création d’applications mobiles avec Xamarin.Forms : résumé du chapitre 22. Animation'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 47C2B9AB-E688-4412-8AF5-9F633B3DA695
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 935be5bd6696600644463eb4ec26410b546f42a0
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "70770993"
---
# <a name="summary-of-chapter-22-animation"></a>Résumé du chapitre 22. Animation

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)

Vous avez vu que vous pouvez créer vos propres animations à l’aide du minuteur Xamarin. Forms ou `Task.Delay`, mais il est généralement plus facile d’utiliser les fonctionnalités d’animation fournies par Xamarin. Forms. Trois classes implémentent ces animations :

- [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions), l’approche de haut niveau
- [`Animation`](xref:Xamarin.Forms.Animation), plus polyvalent, mais plus difficile
- [`AnimationExtension`](xref:Xamarin.Forms.AnimationExtensions), l’approche la plus de niveau le plus polyvalente

En règle générale, les animations cibler les propriétés qui sont assorties de propriétés pouvant être liées. Cela n’est pas obligatoire, mais ce sont les seules propriétés dynamiquement réagissent aux modifications.

Il n’existe pas d’interface XAML pour ces animations, mais vous pouvez intégrer des animations dans XAML à l’aide de techniques présentées dans le [**chapitre 23. Déclencheurs et comportements**](chapter23.md).

## <a name="exploring-basic-animations"></a>Exploration des animations de base

Les fonctions d’animation de base sont des méthodes d’extension trouvées dans la classe [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) . Ces méthodes s’appliquent à tout objet qui dérive de `VisualElement`. Les animations les plus simples ciblent les propriétés transformations décrites dans [`Chapter 21. Transforms`](chapter21.md).

Le [**AnimationTryout**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/AnimationTryout) montre comment le gestionnaire d’événements `Clicked` pour une `Button` peut appeler la méthode d’extension [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) pour faire pivoter le bouton dans un cercle.

La méthode `RotateTo` modifie la propriété `Rotation` de la `Button` de 0 à 360 au cours d’un quart de seconde (par défaut). Toutefois, si la `Button` est à nouveau exploitée, elle ne fait rien car la propriété `Rotation` est déjà de 360 degrés.

### <a name="setting-the-animation-duration"></a>Définition de la durée de l’animation

Le deuxième argument de `RotateTo` est une durée en millisecondes. Si vous définissez une valeur élevée, le fait d’appuyer sur la `Button` pendant une animation démarre une nouvelle animation en commençant à l’angle actuel.

### <a name="relative-animations"></a>Animations relatives

La méthode `RelRotateTo` effectue une rotation relative en ajoutant une valeur spécifiée à la valeur existante. Cette méthode permet à l' `Button` d’être exploitée plusieurs fois, et chaque fois augmente la propriété `Rotation` de 360 degrés.

### <a name="awaiting-animations"></a>En attente d’animations

Toutes les méthodes d’animation dans `ViewExtensions` retournent des objets `Task<bool>`. Cela signifie que vous pouvez définir une série d’animations séquentielles à l’aide de `ContinueWith` ou `await`. La valeur de retour de fin de `bool` est `false` si l’animation s’est terminée sans interruption ou `true` si elle a été annulée par la méthode [`CancelAnimation`](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement)) , qui annule toutes les animations initiées par l’autre méthode dans `ViewExtensions` qui sont définies sur le même élément.

### <a name="composite-animations"></a>Animations composite

Vous pouvez combiner des animations attendues et non attendu pour créer des animations composites. Voici les animations de `ViewExtensions` qui ciblent les propriétés de transformation `TranslationX`, `TranslationY`et `Scale` :

- [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing))
- [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))
- [`RelScaleTo`](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))

Notez que `TranslateTo` affecte potentiellement les propriétés `TranslationX` et `TranslationY`.

### <a name="taskwhenall-and-taskwhenany"></a>Task.WhenAll et Task.WhenAny

Il est également possible de gérer des animations simultanées à l’aide de [`Task.WhenAll`](xref:System.Threading.Tasks.Task.WhenAll*), ce qui indique quand plusieurs tâches sont terminées, et [`Task.WhenAny`](xref:System.Threading.Tasks.Task.WhenAny*), qui signale quand la première des nombreuses tâches est terminée.

### <a name="rotation-and-anchors"></a>Rotation et les points d’ancrage

Lors de l’appel des méthodes `ScaleTo`, `RelScaleTo`, `RotateTo`et `RelRotateTo`, vous pouvez définir les propriétés [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) et [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) pour indiquer le centre de mise à l’échelle et de rotation.

Le [**CircleButton**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CircleButton) illustre cette technique en tournant une `Button` autour du centre de la page.

### <a name="easing-functions"></a>Fonctions d’accélération

En règle générale, les animations sont linéaires à partir d’une valeur de début pour une valeur de fin. Fonctions d’accélération peuvent provoquer des animations accélérer ou ralentir leur cours. Le dernier argument facultatif des méthodes d’animation est de type [`Easing`](xref:Xamarin.Forms.Easing), une classe qui définit 11 champs statiques en lecture seule de type `Easing`:

- [`Linear`](xref:Xamarin.Forms.Easing.Linear), la valeur par défaut
- [`SinIn`](xref:Xamarin.Forms.Easing.SinIn), [`SinOut`](xref:Xamarin.Forms.Easing.SinOut)et [`SinInOut`](xref:Xamarin.Forms.Easing.SinInOut)
- [`CubicIn`](xref:Xamarin.Forms.Easing.CubicIn), [`CubicOut`](xref:Xamarin.Forms.Easing.CubicOut)et [`CubicInOut`](xref:Xamarin.Forms.Easing.CubicInOut)
- [`BounceIn`](xref:Xamarin.Forms.Easing.BounceIn) et [`BounceOut`](xref:Xamarin.Forms.Easing.BounceOut)
- [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn) et [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut)

Le suffixe `In` indique que l’effet se trouve au début de l’animation, `Out` signifie à la fin et `InOut` signifie qu’il est au début et à la fin de l’animation.

L’exemple [**BounceButton**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BounceButton) illustre l’utilisation de fonctions d’accélération.

### <a name="your-own-easing-functions"></a>Vos propres fonctions d’accélération

Vous pouvez également définir vos propres fonctions d’accélération en passant une [`Func<double, double>`](xref:System.Func`2) au [constructeur`Easing`](xref:Xamarin.Forms.Easing.%23ctor(System.Func{System.Double,System.Double})). `Easing` définit également une conversion implicite de `Func<double, double>` en `Easing`. L’argument à la fonction d’accélération est toujours dans la plage de 0 à 1 comme l’animation se poursuit de façon linéaire à partir du début à la fin. La fonction retourne *généralement* une valeur comprise entre 0 et 1, mais elle peut être brièvement négative ou supérieure à 1 (comme c’est le cas avec les fonctions `SpringIn` et `SpringOut`) ou peut arrêter les règles si vous savez ce que vous faites.

L’exemple [**UneasyScale**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/UneasyScale) illustre une fonction d’accélération personnalisée, et [**CustomCubicEase**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CustomCubicEase) en illustre une autre.

L’exemple [**SwingButton**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingButton) illustre également une fonction d’accélération personnalisée, ainsi qu’une technique permettant de modifier les propriétés `AnchorX` et `AnchorY` au sein d’une séquence d’animations de rotation.

La bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) a une classe [`JiggleButton`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/JiggleButton.cs) qui utilise une fonction d’accélération personnalisée pour agiter un bouton quand l’utilisateur clique dessus. L’exemple [**JiggleButtonDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/JiggleButtonDemo) le montre.

### <a name="entrance-animations"></a>Animations d’entrée

Un seul type populaires d’animation se produit lorsqu’une page s’affiche tout d’abord. Une telle animation peut être démarrée dans le [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) remplacement de la page. Pour ces animations, il est préférable de configurer le code XAML pour la façon dont vous souhaitez que la page apparaisse *après* l’animation, puis d’initialiser et d’animer la disposition à partir du code.

L’exemple [**FadingEntrance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingEntrance) utilise la méthode d’extension [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) pour effectuer un fondu dans le contenu de la page.

L’exemple [**SlidingEntrance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SlidingEntrance) utilise la méthode d’extension [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) pour faire glisser les côtés du contenu de la page.

L’exemple [**SwingingEntrance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingingEntrance) utilise la méthode d’extension [`RotateYTo`](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) pour animer la propriété `RotationY`. Une méthode [`RotateXTo`](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) est également disponible.

### <a name="forever-animations"></a>Animations indéfiniment

À l’autre extrême, « forever » les animations s’exécutent jusqu'à ce que le programme se termine. Elles sont généralement destinées à des fins de démonstration.

L’exemple [**FadingTextAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingTextAnimation) utilise [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) animation pour estomper deux parties de texte.

[**PalindromeAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/PalindromeAnimation) affiche un palindrome, puis fait pivoter séquentiellement les lettres individuelles de 180 degrés afin qu’elles soient toutes à l’envers. Puis la chaîne entière est retournée par 180 degrés pour lire la même que la chaîne d’origine.

L’exemple [**CopterAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CopterAnimation) fait pivoter un simple `BoxView` hélicoptère tout en le faisant tourner au centre de l’écran.

[**RotatingSpokes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotatingSpokes) fait tourner `BoxView` rayons au centre de l’écran, puis fait pivoter chaque rayon pour créer des modèles intéressants :

[![Capture d’écran triple des rayons rotatifs](images/ch22fg21-small.png "Rotation des rayons")](images/ch22fg21-large.png#lightbox "Rotation des rayons")

Toutefois, l’extension progressive de la propriété `Rotation` d’un élément peut ne pas fonctionner à long terme, comme le montre l’exemple [**RotationBreakdown**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotationBreakdown) .

L’exemple [**SpinningImage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpinningImage) utilise [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)), [`RotateXTo`](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))et [`RotateYTo`](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) pour le faire apparaître comme si une bitmap est en rotation dans l’espace 3D.

### <a name="animating-the-bounds-property"></a>Animer la propriété bounds

La seule méthode d’extension de `ViewExtensions` pas encore démontrée est [`LayoutTo`](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing)), qui anime efficacement la propriété en lecture seule [`Bounds`](xref:Xamarin.Forms.VisualElement.Bounds) en appelant la méthode [`Layout`](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) . Cette méthode est normalement appelée par `Layout` dérivés, comme décrit dans le [**chapitre 26. CustomLayouts**](chapter26.md).

La méthode `LayoutTo` doit être limitée à des fins spéciales. Le programme [**BouncingBox**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BouncingBox) l’utilise pour compresser et développer une `BoxView` lorsqu’il rebondit sur les côtés d’une page.

L’exemple [**XamagonXuzzle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle) utilise `LayoutTo` pour déplacer des vignettes dans une implémentation du puzzle 15-16 classique qui affiche une image brouillée plutôt que des vignettes numérotées :

[![Capture d’écran triple de Xamarin Xuzzle](images/ch22fg26-small.png "Jeu de puzzle Xuzzle")](images/ch22fg26-large.png#lightbox "Jeu de puzzle Xuzzle")

### <a name="your-own-awaitable-animations"></a>Vos propres animations pouvant être attendues

L’exemple [**TryAwaitableAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/TryAwaitableAnimation) crée une animation pouvant être attendue. La classe cruciale qui peut retourner un objet `Task` à partir de la méthode et signal lorsque l’animation est terminée est [`TaskCompletionSource`](xref:System.Threading.Tasks.TaskCompletionSource`1).

## <a name="deeper-into-animations"></a>Plus profondément dans les animations

Le système d’animation Xamarin.Forms peut être un peu déroutant. En plus de la classe `Easing`, le système d’animation comprend les classes `ViewExtensions`, `Animation`et `AnimationExtension`.

### <a name="viewextensions-class"></a>Classe de ViewExtensions

Vous avez déjà vu [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions). Il définit neuf méthodes qui retournent `Task<bool>` et [`CancelAnimations`](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement)). Sept de la cible de neuf méthodes propriétés de transformation. Les deux autres sont [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)), qui cible la propriété [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) et [`LayoutTo`](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing)), qui appelle la méthode [`Layout`](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) .

### <a name="animation-class"></a>Classe d’animation

La classe [`Animation`](xref:Xamarin.Forms.AnimationExtensions) a un [constructeur](xref:Xamarin.Forms.Animation.%23ctor(System.Action{System.Double},System.Double,System.Double,Xamarin.Forms.Easing,System.Action)) avec cinq arguments pour définir le rappel et les méthodes terminées, ainsi que les paramètres de l’animation.

Les animations enfants peuvent être ajoutées avec [`Add`](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation)), [`Insert`](xref:Xamarin.Forms.Animation.Insert(System.Double,System.Double,Xamarin.Forms.Animation)), [`WithConcurrent`](xref:Xamarin.Forms.Animation.WithConcurrent(Xamarin.Forms.Animation,System.Double,System.Double))et la surcharge de [`WithConcurrent`](xref:Xamarin.Forms.Animation.WithConcurrent(System.Action{System.Double},System.Double,System.Double,Xamarin.Forms.Easing,System.Double,System.Double)).

L’objet d’animation est ensuite démarré avec un appel à la méthode [`Commit`](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean})) .

### <a name="animationextensions-class"></a>Classe de AnimationExtensions

La classe [`AnimationExtensions`](xref:Xamarin.Forms.AnimationExtensions) contient principalement des méthodes d’extension. Il existe plusieurs versions d’une méthode `Animate` et la méthode [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) générique est tellement polyvalente qu’il s’agit vraiment de la seule fonction d’animation dont vous avez besoin.

## <a name="working-with-the-animation-class"></a>Utilisation de la classe d’Animation

L’exemple [**ConcurrentAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) illustre la classe [`Animation`](xref:Xamarin.Forms.Animation) avec plusieurs animations différentes.

### <a name="child-animations"></a>Animations enfants

L’exemple [**ConcurrentAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) illustre également les animations enfants, qui utilisent les méthodes (très similaires) [`Add`](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation)) et [`Insert`](xref:Xamarin.Forms.Animation.Insert(System.Double,System.Double,Xamarin.Forms.Animation)) .

### <a name="beyond-the-high-level-animation-methods"></a>Au-delà des méthodes de haut niveau d’animation

L’exemple [**ConcurrentAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) montre également comment exécuter des animations qui dépassent les propriétés ciblées par les méthodes de `ViewExtensions`. Dans un exemple, une série de périodes est plus longue ; dans un autre exemple, une propriété `BackgroundColor` est animée.

### <a name="more-of-your-own-awaitable-methods"></a>Plusieurs de vos propres méthodes pouvant être attendus

La méthode [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) de `ViewExtensions` ne fonctionne pas avec la fonction [`Easing.SpringOut`](xref:Xamarin.Forms.Easing.SpringOut) . Il s’arrête lorsque la sortie accélération obtient supérieures à 1.

La bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contient une [classe`MoreViewExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) avec des méthodes d’extension [`TranslateXTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L12) et [`TranslateYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L49) qui n’ont pas ce problème, ainsi que des méthodes [`CancelTranslateXTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L44) et [`CancelTranslateYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L71) pour annuler ces animations.

Le [**SpringSlidingEntrance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpringSlidingEntrance) illustre la méthode `TranslateXTo`.

La classe `MoreExtensions` contient également une méthode d’extension [`TranslateXYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L76) qui combine la traduction X et Y et une méthode [`CancelTranslateXYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L113) .

### <a name="implementing-a-bezier-animation"></a>Implémentation d’une animation de Bézier

Il est également possible de développer une animation qui déplace un élément le long du tracé d’une spline de Bézier. La bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contient une structure [`BezierSpline`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierSpline.cs) qui encapsule une spline de Bézier et une énumération [`BezierTangent`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierTangent.cs) pour contrôler l’orientation.

La classe [`MoreViewExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) contient une méthode d’extension [`BezierPathTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L118) et une méthode [`CancelBezierPathTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L161) .

L’exemple [**BezierLoop**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BezierLoop) montre comment animer un élément le long d’un chemin d’accès Beizer.

## <a name="working-with-animationextensions"></a>Utilisation de AnimationExtensions

Un seul type d’animation manquant à partir de la collection standard est une animation de couleur. Le problème est qu’il n’existe aucun moyen d’interpoler entre deux valeurs `Color`. Il est possible d’interpoler les valeurs RVB individuelles, mais simplement comme étant valides interpoler les valeurs TSL.

C’est la raison pour laquelle la classe [`MoreViewExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) de la bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contient deux méthodes d’animation `Color` : [`RgbColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L166) et [`HslColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L188). (Il existe également deux méthodes d’annulation : [`CancelRgbColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L183) et [`CancelHslColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L206)).

Les deux méthodes utilisent [`ColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L211), qui effectue l’animation en appelant la méthode de [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) générique étendue dans [`AnimationExtensions`](xref:Xamarin.Forms.AnimationExtensions).

L’exemple [**ColorAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ColorAnimations) illustre l’utilisation de ces deux types d’animations de couleurs.

## <a name="structuring-your-animations"></a>Structuration de vos animations

Il est parfois utile exprimer des animations dans XAML et les utiliser conjointement avec MVVM. Ce sujet est abordé dans le chapitre [**23 suivant. Déclencheurs et comportements**](chapter23.md).

## <a name="related-links"></a>Liens connexes

- [Chapitre 22 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)
- [Exemples du chapitre 22](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)
- [Animation](~/xamarin-forms/user-interface/animation/index.md)
