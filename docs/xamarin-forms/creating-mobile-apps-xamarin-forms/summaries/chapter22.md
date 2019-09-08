---
title: Résumé du chapitre 22. Animation
description: 'Création d’Mobile Apps avec Xamarin. Forms : Résumé du chapitre 22. Animation'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 47C2B9AB-E688-4412-8AF5-9F633B3DA695
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 935be5bd6696600644463eb4ec26410b546f42a0
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770993"
---
# <a name="summary-of-chapter-22-animation"></a>Résumé du chapitre 22. Animation

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)

Vous avez vu que vous pouvez créer vos propres animations à l’aide de la minuterie Xamarin.Forms ou `Task.Delay`, mais il est généralement plus simple, avec les fonctionnalités d’animation fournies par Xamarin.Forms. Trois classes implémentent ces animations :

- [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions), l’approche de haut niveau
- [`Animation`](xref:Xamarin.Forms.Animation), plus polyvalent, mais il est plus difficile
- [`AnimationExtension`](xref:Xamarin.Forms.AnimationExtensions), le plus polyvalente plus bas niveau approche

En règle générale, les animations cibler les propriétés qui sont assorties de propriétés pouvant être liées. Cela n’est pas obligatoire, mais ce sont les seules propriétés dynamiquement réagissent aux modifications.

Il n’existe aucune interface XAML pour ces animations, mais vous pouvez intégrer des animations dans XAML à l’aide des techniques présentées dans [ **chapitre 23. Déclencheurs et comportements**](chapter23.md).

## <a name="exploring-basic-animations"></a>Exploration des animations de base

Les fonctions d’animation de base sont des méthodes d’extension trouvées dans le [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) classe. Ces méthodes s’appliquent à n’importe quel objet qui dérive de `VisualElement`. Les animations de la plus simple ciblent les propriétés décrites dans les transformations [ `Chapter 21. Transforms` ](chapter21.md).

Le [ **AnimationTryout** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/AnimationTryout) montre comment la `Clicked` Gestionnaire d’événements pour un `Button` peut appeler le [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) méthode d’extension pour mettre en place le bouton dans un cercle.

Le `RotateTo` de la méthode change le `Rotation` propriété de la `Button` comprise entre 0 et 360 au cours de la seconde un quart (par défaut). Si le `Button` l’appui sur là encore, toutefois, elle ne fait rien, car le `Rotation` propriété est déjà 360 degrés.

### <a name="setting-the-animation-duration"></a>Définition de la durée de l’animation

Le deuxième argument de `RotateTo` est la durée en millisecondes. Si la valeur est une valeur élevée, en appuyant sur la `Button` pendant une animation démarre un nouveau commencement d’animation à l’angle actuel.

### <a name="relative-animations"></a>Animations relatives

Le `RelRotateTo` méthode effectue une rotation relative en ajoutant une valeur spécifiée à la valeur existante. Cette méthode permet le `Button` à drainer plusieurs fois et pour chaque heure augmente la `Rotation` propriété de 360 degrés.

### <a name="awaiting-animations"></a>En attente d’animations

Toutes les méthodes d’animation dans `ViewExtensions` retourner `Task<bool>` objets. Cela signifie que vous pouvez définir une série d’animations séquentielles à l’aide de `ContinueWith` ou `await`. Le `bool` achèvement valeur de retour est `false` si l’animation est terminée sans interruption ou `true` si elle a été annulée par le [ `CancelAnimation` ](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement)) (méthode), ce qui annule toutes les animations initiées par le autre méthode dans `ViewExtensions` qui sont définies sur le même élément.

### <a name="composite-animations"></a>Animations composite

Vous pouvez combiner des animations attendues et non attendu pour créer des animations composites. Voici les animations dans `ViewExtensions` qui ciblent le `TranslationX`, `TranslationY`, et `Scale` propriétés de transformation :

- [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing))
- [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))
- [`RelScaleTo`](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))

Notez que `TranslateTo` affecte potentiellement tous deux le `TranslationX` et `TranslationY` propriétés.

### <a name="taskwhenall-and-taskwhenany"></a>Task.WhenAll et Task.WhenAny

Il est également possible de gérer des animations simultanées à l’aide de [ `Task.WhenAll` ](xref:System.Threading.Tasks.Task.WhenAll*), ce qui signale lorsque plusieurs tâches tous terminés, et [ `Task.WhenAny` ](xref:System.Threading.Tasks.Task.WhenAny*), qui signale quand la première de plusieurs tâches a terminé.

### <a name="rotation-and-anchors"></a>Rotation et les points d’ancrage

Lorsque vous appelez le `ScaleTo`, `RelScaleTo`, `RotateTo`, et `RelRotateTo` méthodes, vous pouvez définir le [ `AnchorX` ](xref:Xamarin.Forms.VisualElement.AnchorX) et [ `AnchorY` ](xref:Xamarin.Forms.VisualElement.AnchorY) propriétés pour indiquer le Centre de mise à l’échelle et la rotation.

Le [ **CircleButton** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CircleButton) illustre cette technique par révolution un `Button` autour du centre de la page.

### <a name="easing-functions"></a>Fonctions d’accélération

En règle générale, les animations sont linéaires à partir d’une valeur de début pour une valeur de fin. Fonctions d’accélération peuvent provoquer des animations accélérer ou ralentir leur cours. Le dernier argument facultatif pour les méthodes d’animation est de type [ `Easing` ](xref:Xamarin.Forms.Easing), une classe qui définit les 11 en lecture seule les champs statiques de type `Easing`:

- [`Linear`](xref:Xamarin.Forms.Easing.Linear), la valeur par défaut
- [`SinIn`](xref:Xamarin.Forms.Easing.SinIn), [ `SinOut` ](xref:Xamarin.Forms.Easing.SinOut), et [`SinInOut`](xref:Xamarin.Forms.Easing.SinInOut)
- [`CubicIn`](xref:Xamarin.Forms.Easing.CubicIn), [ `CubicOut` ](xref:Xamarin.Forms.Easing.CubicOut), et [`CubicInOut`](xref:Xamarin.Forms.Easing.CubicInOut)
- [`BounceIn`](xref:Xamarin.Forms.Easing.BounceIn) et [`BounceOut`](xref:Xamarin.Forms.Easing.BounceOut)
- [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn) et [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut)

Le `In` suffixe indique que l’effet se trouve au début de l’animation, `Out` signifie à la fin, et `InOut` signifie qu’il est au début et à la fin de l’animation.

Le [ **BounceButton** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BounceButton) exemple illustre l’utilisation de fonctions d’accélération.

### <a name="your-own-easing-functions"></a>Vos propres fonctions d’accélération

Vous pouvez également définir vous propres fonctions d’accélération en passant un [ `Func<double, double>` ](xref:System.Func`2) à la [ `Easing` constructeur](xref:Xamarin.Forms.Easing.%23ctor(System.Func{System.Double,System.Double})). `Easing` définit également une conversion implicite de `Func<double, double>` à `Easing`. L’argument à la fonction d’accélération est toujours dans la plage de 0 à 1 comme l’animation se poursuit de façon linéaire à partir du début à la fin. La fonction *généralement* retourne une valeur dans la plage de 0 à 1, mais peut être brièvement négatif ou supérieur à 1 (comme c’est le cas avec le `SpringIn` et `SpringOut` fonctions) ou endommage les règles si vous savez ce que vous faites.

Le [ **UneasyScale** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/UneasyScale) exemple illustre une fonction d’accélération personnalisée, et [ **CustomCubicEase** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CustomCubicEase) montre un autre.

Le [ **SwingButton** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingButton) montre également une fonction d’accélération personnalisée et également une technique consistant à changer la `AnchorX` et `AnchorY` propriétés au sein d’une séquence d’animations de rotation.

Le [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) bibliothèque possède un [ `JiggleButton` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/JiggleButton.cs) la fonction de classe qui utilise une accélération personnalisée à jiggle un bouton lorsque vous cliquez dessus. Le [ **JiggleButtonDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/JiggleButtonDemo) illustre cela.

### <a name="entrance-animations"></a>Animations d’entrée

Un seul type populaires d’animation se produit lorsqu’une page s’affiche tout d’abord. Une animation de ce type peut être démarrée le [ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) la substitution de la page. Pour ces animations, de son mieux pour configurer le XAML de la manière dont la page affichage *après* l’animation, initialiser et animer la disposition à partir du code.

Le [ **FadingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingEntrance) exemple utilise le [ `FadeTo` ](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) méthode d’extension pour le fondu dans le contenu de la page.

Le [ **SlidingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SlidingEntrance) exemple utilise le [ `TranslateTo` ](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) méthode d’extension à la diapositive dans le contenu de la page sur les côtés.

Le [ **SwingingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingingEntrance) exemple utilise le [ `RotateYTo` ](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) méthode d’extension pour animer la `RotationY` propriété. Un [ `RotateXTo` ](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) méthode est également disponible.

### <a name="forever-animations"></a>Animations indéfiniment

À l’autre extrême, « forever » les animations s’exécutent jusqu'à ce que le programme se termine. Elles sont généralement destinées à des fins de démonstration.

Le [ **FadingTextAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingTextAnimation) exemple utilise [ `FadeTo` ](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) animation à deux parties de texte disparaître en fondu.

[**PalindromeAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/PalindromeAnimation) affiche un palindrome et ensuite séquentiellement fait pivoter les lettres individuelles à 180 degrés afin qu’elles soient toutes envers. Puis la chaîne entière est retournée par 180 degrés pour lire la même que la chaîne d’origine.

Le [ **CopterAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CopterAnimation) exemple fait pivoter une simple `BoxView` hélicoptère lors de la révolution il autour du centre de l’écran.

[**RotatingSpokes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotatingSpokes) tourne `BoxView` rayons autour du centre de l’écran, puis fait pivoter chaque membre spoke lui-même pour créer des modèles intéressants :

[![Capture d’écran triple de rotation des rayons](images/ch22fg21-small.png "rotation rayons")](images/ch22fg21-large.png#lightbox "rotation rayons")

Toutefois, augmentant progressivement la `Rotation` propriété d’un élément peut faillir à long terme, comme le [ **RotationBreakdown** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotationBreakdown) montre des exemples.

Le [ **SpinningImage** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpinningImage) exemple utilise [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)), [ `RotateXTo` ](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)), et [ `RotateYTo` ](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) pour donner l’impression que si une image bitmap fait tourner dans l’espace 3D.

### <a name="animating-the-bounds-property"></a>Animer la propriété bounds

La seule méthode d’extension de `ViewExtensions` pas encore montré est [ `LayoutTo` ](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing)), qui anime efficacement en lecture seule [ `Bounds` ](xref:Xamarin.Forms.VisualElement.Bounds) propriété en appelant le [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) (méthode). Cette méthode est appelée normalement par `Layout` dérivés que nous aborderons dans [ **chapitre 26. CustomLayouts**](chapter26.md).

Le `LayoutTo` méthode doit être limitée à des usages spéciaux. Le [ **BouncingBox** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BouncingBox) programme l’utilise pour compresser et décompresser un `BoxView` comme il rebondit sur les côtés d’une page.

Le [ **XamagonXuzzle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle) exemple utilise `LayoutTo` pour déplacer des vignettes disponibles dans une implémentation de classique puzzle de 15-16 qui affiche une image brouillée plutôt que les vignettes numérotées :

[![Capture d’écran triple de Xamarin Xuzzle](images/ch22fg26-small.png "jeu de Puzzle Xuzzle")](images/ch22fg26-large.png#lightbox "jeu de Puzzle Xuzzle")

### <a name="your-own-awaitable-animations"></a>Vos propres animations pouvant être attendues

Le [ **TryAwaitableAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/TryAwaitableAnimation) exemple crée une animation pouvant être attendue. La classe essentielle qui peut retourner un `Task` objet à partir de la méthode et le signal lorsque l’animation est terminée est [ `TaskCompletionSource` ](xref:System.Threading.Tasks.TaskCompletionSource`1).

## <a name="deeper-into-animations"></a>Plus profondément dans les animations

Le système d’animation Xamarin.Forms peut être un peu déroutant. Outre le `Easing` (classe), le système d’animation comprend le `ViewExtensions`, `Animation`, et `AnimationExtension` classes.

### <a name="viewextensions-class"></a>Classe de ViewExtensions

Vous avez déjà vu [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions). Il définit neuf méthodes qui retournent `Task<bool>` et [ `CancelAnimations` ](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement)). Sept de la cible de neuf méthodes propriétés de transformation. Les deux autres sont [ `FadeTo` ](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)), les cibles le [ `Opacity` ](xref:Xamarin.Forms.VisualElement.Opacity) propriété, et [ `LayoutTo` ](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing)), qui appelle le [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) (méthode).

### <a name="animation-class"></a>Classe d’animation

Le [ `Animation` ](xref:Xamarin.Forms.AnimationExtensions) classe a un [constructeur](xref:Xamarin.Forms.Animation.%23ctor(System.Action{System.Double},System.Double,System.Double,Xamarin.Forms.Easing,System.Action)) avec cinq arguments pour définir le rappel et les méthodes terminés et les paramètres de l’animation.

Animations enfants peuvent être ajoutées avec [ `Add` ](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation)), [ `Insert` ](xref:Xamarin.Forms.Animation.Insert(System.Double,System.Double,Xamarin.Forms.Animation)), [ `WithConcurrent` ](xref:Xamarin.Forms.Animation.WithConcurrent(Xamarin.Forms.Animation,System.Double,System.Double))et la surcharge de [ `WithConcurrent` ](xref:Xamarin.Forms.Animation.WithConcurrent(System.Action{System.Double},System.Double,System.Double,Xamarin.Forms.Easing,System.Double,System.Double)).

L’objet d’animation est ensuite démarré avec un appel à la [ `Commit` ](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean})) (méthode).

### <a name="animationextensions-class"></a>Classe de AnimationExtensions

Le [ `AnimationExtensions` ](xref:Xamarin.Forms.AnimationExtensions) classe contient principalement des méthodes d’extension. Il existe plusieurs versions d’un `Animate` (méthode) et les génériques [ `Animate` ](xref:Xamarin.Forms.AnimationExtensions.Animate*) méthode est donc polyvalente pour qu’il est vraiment de la fonction d’animation uniquement que vous avez besoin.

## <a name="working-with-the-animation-class"></a>Utilisation de la classe d’Animation

Le [ **ConcurrentAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) exemple illustre la [ `Animation` ](xref:Xamarin.Forms.Animation) classe avec plusieurs animations différents.

### <a name="child-animations"></a>Animations enfants

Le [ **ConcurrentAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) exemple illustre également l’enfant d’animations qui utilisent le (similaire) [ `Add` ](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation)) et [ `Insert` ](xref:Xamarin.Forms.Animation.Insert(System.Double,System.Double,Xamarin.Forms.Animation)) méthodes.

### <a name="beyond-the-high-level-animation-methods"></a>Au-delà des méthodes de haut niveau d’animation

Le [ **ConcurrentAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) exemple montre également comment exécuter des animations qui dépassent les propriétés ciblées par le `ViewExtensions` méthodes. Dans un exemple, une série de périodes d’obtenir plus de temps ; dans un autre exemple, un `BackgroundColor` propriété est animée.

### <a name="more-of-your-own-awaitable-methods"></a>Plusieurs de vos propres méthodes pouvant être attendus

Le [ `TranslateTo` ](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) méthode de `ViewExtensions` ne fonctionne pas avec le [ `Easing.SpringOut` ](xref:Xamarin.Forms.Easing.SpringOut) (fonction). Il s’arrête lorsque la sortie accélération obtient supérieures à 1.

Le [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) bibliothèque contient un [ `MoreViewExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) classe avec [ `TranslateXTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L12) et [ `TranslateYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L49) les méthodes d’extension qui n’ont pas ce problème, ainsi que [ `CancelTranslateXTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L44) et [ `CancelTranslateYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L71) méthodes d’annulation ceux animations.

Le [ **SpringSlidingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpringSlidingEntrance) montre le `TranslateXTo` (méthode).

Le `MoreExtensions` classe contient également un [ `TranslateXYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L76) méthode d’extension qui combine la traduction de X et Y, et un [ `CancelTranslateXYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L113) (méthode).

### <a name="implementing-a-bezier-animation"></a>Implémentation d’une animation de Bézier

Il est également possible de développer une animation qui déplace un élément le long du tracé d’une spline de Bézier. Le [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) bibliothèque contient un [ `BezierSpline` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierSpline.cs) structure qui encapsule une spline de Bézier et un [ `BezierTangent` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierTangent.cs) énumération pour contrôler l’orientation.

Le [ `MoreViewExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) classe contient un [ `BezierPathTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L118) méthode d’extension et un [ `CancelBezierPathTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L161) (méthode).

Le [ **BezierLoop** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BezierLoop) exemple illustre l’animation d’un élément le long d’un chemin d’accès Beizer.

## <a name="working-with-animationextensions"></a>Utilisation de AnimationExtensions

Un seul type d’animation manquant à partir de la collection standard est une animation de couleur. Le problème est qu’il n’existe aucun moyen de droite pour interpoler entre deux `Color` valeurs. Il est possible d’interpoler les valeurs RVB individuelles, mais simplement comme étant valides interpoler les valeurs TSL.

Pour cette raison, le [ `MoreViewExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) classe dans le [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) bibliothèque contient deux `Color` méthodes d’animation : [ `RgbColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L166)et [ `HslColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L188). (Il existe également deux méthodes d’annulation : [ `CancelRgbColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L183) et [ `CancelHslColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L206)).

Les deux méthodes utilisent [ `ColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L211), qui effectue l’animation en appelant le générique étendu [ `Animate` ](xref:Xamarin.Forms.AnimationExtensions.Animate*) méthode dans [ `AnimationExtensions` ](xref:Xamarin.Forms.AnimationExtensions).

Le [ **ColorAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ColorAnimations) exemple illustre l’utilisation de ces deux types d’animations de couleur.

## <a name="structuring-your-animations"></a>Structuration de vos animations

Il est parfois utile exprimer des animations dans XAML et les utiliser conjointement avec MVVM. Cela est couvert dans le chapitre suivant, [ **chapitre 23. Déclencheurs et comportements**](chapter23.md).

## <a name="related-links"></a>Liens connexes

- [Chapitre 22 de texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)
- [Exemples de chapitre 22](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)
- [Animation](~/xamarin-forms/user-interface/animation/index.md)
