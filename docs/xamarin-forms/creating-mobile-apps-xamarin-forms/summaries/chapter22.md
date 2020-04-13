---
title: Résumé du chapitre 22. Animation
description: 'Création d’applications mobiles avec Xamarin.Forms: Résumé du chapitre 22. Animation'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 47C2B9AB-E688-4412-8AF5-9F633B3DA695
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 935be5bd6696600644463eb4ec26410b546f42a0
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70770993"
---
# <a name="summary-of-chapter-22-animation"></a>Résumé du chapitre 22. Animation

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)

Vous avez vu que vous pouvez créer vos propres animations en `Task.Delay`utilisant le minuteur Xamarin.Forms ou , mais il est généralement plus facile en utilisant les installations d’animation fournies par Xamarin.Forms. Trois classes mettent en œuvre ces animations :

- [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions), l’approche de haut niveau
- [`Animation`](xref:Xamarin.Forms.Animation), plus polyvalent mais plus difficile
- [`AnimationExtension`](xref:Xamarin.Forms.AnimationExtensions), l’approche la plus polyvalente et la plus basse

En général, les animations ciblent les propriétés qui sont soutenues par des propriétés liantes. Ce n’est pas une exigence, mais ce sont les seules propriétés qui réagissent dynamiquement aux changements.

Il n’y a pas d’interface XAML pour ces animations, mais vous pouvez intégrer des animations dans XAML en utilisant des techniques discutées dans [**le chapitre 23. Déclencheurs et comportements**](chapter23.md).

## <a name="exploring-basic-animations"></a>Explorer les animations de base

Les fonctions d’animation de [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) base sont des méthodes d’extension trouvées dans la classe. Ces méthodes s’appliquent à `VisualElement`tout objet qui dérive de . Les animations les plus simples ciblent les propriétés de transformation discutées en [`Chapter 21. Transforms`](chapter21.md).

[**L’AnimationTryout**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/AnimationTryout) montre `Clicked` comment le `Button` gestionnaire d’événements pour un peut appeler la [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) méthode d’extension pour faire tourner le bouton dans un cercle.

La `RotateTo` méthode `Rotation` change la `Button` propriété de la 0 à 360 au cours d’un quart de seconde (par défaut). Si `Button` le est exploité à nouveau, cependant, il ne fait rien parce que la `Rotation` propriété est déjà 360 degrés.

### <a name="setting-the-animation-duration"></a>Définir la durée de l’animation

Le deuxième `RotateTo` argument à est une durée en millisecondes. Si réglé à une grande `Button` valeur, en appuyant sur le lors d’une animation commence une nouvelle animation à partir de l’angle actuel.

### <a name="relative-animations"></a>Animations relatives

La `RelRotateTo` méthode effectue une rotation relative en ajoutant une valeur spécifiée à la valeur existante. Cette méthode `Button` permet d’être exploité plusieurs fois, `Rotation` et chaque fois augmente la propriété de 360 degrés.

### <a name="awaiting-animations"></a>En attente d’animations

Toutes les méthodes `ViewExtensions` `Task<bool>` d’animation en retour des objets. Cela signifie que vous pouvez définir une série `ContinueWith` d’animations séquentielles à l’aide ou `await`. La `bool` valeur de `false` retour d’achèvement `true` est si l’animation terminée sans interruption ou si elle a été annulée par la [`CancelAnimation`](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement)) méthode, qui annule toutes les animations initiées par l’autre méthode qui `ViewExtensions` sont définies sur le même élément.

### <a name="composite-animations"></a>Animations composites

Vous pouvez mélanger des animations attendues et non attendues pour créer des animations composites. Ce sont les `ViewExtensions` animations `TranslationX`dans `TranslationY`cette `Scale` cible de la , , et transformer les propriétés:

- [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing))
- [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))
- [`RelScaleTo`](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))

Remarquez `TranslateTo` qui affecte `TranslationX` `TranslationY` potentiellement à la fois les propriétés et les propriétés.

### <a name="taskwhenall-and-taskwhenany"></a>Task.WhenAll et Task.WhenAny

Il est également possible de [`Task.WhenAll`](xref:System.Threading.Tasks.Task.WhenAll*)gérer des animations simultanées à [`Task.WhenAny`](xref:System.Threading.Tasks.Task.WhenAny*)l’aide , qui signale lorsque plusieurs tâches ont toutes conclu, et , qui signale lorsque la première de plusieurs tâches a pris fin.

### <a name="rotation-and-anchors"></a>Rotation et ancres

Lorsque vous `ScaleTo` `RelScaleTo`appelez `RotateTo`le `RelRotateTo` , , , [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) et les méthodes, vous pouvez définir le et les propriétés pour indiquer le centre de mise à l’échelle et la rotation.

Le [**CircleButton**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CircleButton) démontre cette technique `Button` en tournant autour du centre de la page.

### <a name="easing-functions"></a>Fonctions d’accélération

En général, les animations sont linéaires à partir d’une valeur de départ à une valeur de fin. L’assouplissement des fonctions peut entraîner une accélération ou un ralentissement des animations au cours de leur cours. Le dernier argument facultatif pour [`Easing`](xref:Xamarin.Forms.Easing)les méthodes d’animation est de type , `Easing`une classe qui définit 11 domaines statiques de type :

- [`Linear`](xref:Xamarin.Forms.Easing.Linear), par défaut
- [`SinIn`](xref:Xamarin.Forms.Easing.SinIn), [`SinOut`](xref:Xamarin.Forms.Easing.SinOut)et[`SinInOut`](xref:Xamarin.Forms.Easing.SinInOut)
- [`CubicIn`](xref:Xamarin.Forms.Easing.CubicIn), [`CubicOut`](xref:Xamarin.Forms.Easing.CubicOut)et[`CubicInOut`](xref:Xamarin.Forms.Easing.CubicInOut)
- [`BounceIn`](xref:Xamarin.Forms.Easing.BounceIn)Et[`BounceOut`](xref:Xamarin.Forms.Easing.BounceOut)
- [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn)Et[`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut)

Le `In` suffixe indique que l’effet est `Out` au début de `InOut` l’animation, signifie à la fin, et signifie que c’est au début et à la fin de l’animation.

[**L’échantillon BounceButton**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BounceButton) démontre l’utilisation de fonctions d’assouplissement.

### <a name="your-own-easing-functions"></a>Vos propres fonctions d’assouplissement

Vous pouvez également définir vos propres [`Func<double, double>`](xref:System.Func`2) fonctions d’assouplissement en passant un au [ `Easing` constructeur](xref:Xamarin.Forms.Easing.%23ctor(System.Func{System.Double,System.Double})). `Easing`définit également une conversion `Func<double, double>` `Easing`implicite de . L’argument de la fonction d’assouplissement est toujours de l’ordre de 0 à 1 que l’animation procède linéairement du début à la fin. La fonction retourne *habituellement* une valeur dans la gamme de 0 à 1, mais pourrait `SpringIn` être `SpringOut` brièvement négative ou supérieure à 1 (comme c’est le cas avec le et les fonctions) ou pourrait enfreindre les règles si vous savez ce que vous faites.

[**L’échantillon De MalasyScale**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/UneasyScale) démontre une fonction d’assouplissement personnalisé, et [**CustomCubicEase**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CustomCubicEase) en démontre une autre.

[**L’échantillon SwingButton**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingButton) démontre également une fonction d’assouplissement `AnchorX` personnalisé, ainsi qu’une technique de modification de la et `AnchorY` des propriétés dans une séquence d’animations de rotation.

La bibliothèque [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) dispose d’une [`JiggleButton`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/JiggleButton.cs) classe qui utilise une fonction d’assouplissement personnalisé pour agiter un bouton lorsqu’elle est cliqué. [**L’échantillon JiggleButtonDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/JiggleButtonDemo) le démontre.

### <a name="entrance-animations"></a>Animations d’entrée

Un type populaire d’animation se produit lorsqu’une page apparaît pour la première fois. Une telle animation peut [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) être commencée dans la dérogation de la page. Pour ces animations, il est préférable de configurer le XAML pour la façon dont vous voulez que la page *apparaisse après* l’animation, puis d’initialiser et d’animer la mise en page à partir du code.

[**L’échantillon FadingEntrance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingEntrance) utilise la [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) méthode d’extension pour s’estomper dans le contenu de la page.

[**L’échantillon SlidingEntrance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SlidingEntrance) utilise la [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) méthode d’extension pour glisser dans le contenu de la page sur les côtés.

[**L’échantillon SwingingEntrance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingingEntrance) utilise la [`RotateYTo`](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) méthode `RotationY` d’extension pour animer la propriété. Une [`RotateXTo`](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) méthode est également disponible.

### <a name="forever-animations"></a>Animations pour toujours

À l’autre extrême, les animations « pour toujours » s’exécutent jusqu’à ce que le programme soit terminé. Ceux-ci sont généralement destinés à des fins de démonstration.

[**L’échantillon FadingTextAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingTextAnimation) utilise l’animation [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) pour effacer deux morceaux de texte dans et hors.

[**PalindromeAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/PalindromeAnimation) affiche un palindrome, puis tourne séquentiellement les lettres individuelles de 180 degrés afin qu’ils soient tous à l’envers. Ensuite, la chaîne entière est retournée à 180 degrés pour lire la même chose que la corde d’origine.

[**L’échantillon CopterAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CopterAnimation) fait `BoxView` pivoter un hélicoptère simple tout en le tournant autour du centre de l’écran.

[**RotatingSpokes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotatingSpokes) tourne `BoxView` les rayons autour du centre de l’écran, puis tourne chacun se parlait pour créer des modèles intéressants:

[![Triple capture d’écran de Rotating Spokes](images/ch22fg21-small.png "Rayons rotatifs")](images/ch22fg21-large.png#lightbox "Rayons rotatifs")

Toutefois, l’augmentation `Rotation` progressive des biens d’un élément pourrait ne pas fonctionner à long terme, comme le démontre l’échantillon [**RotationBreakdown.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotationBreakdown)

[**L’échantillon SpinningImage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpinningImage) [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))utilise, [`RotateXTo`](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)), et [`RotateYTo`](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) pour faire croire qu’un bitmap tourne dans l’espace 3D.

### <a name="animating-the-bounds-property"></a>Animer la propriété des limites

La seule méthode `ViewExtensions` d’extension [`LayoutTo`](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing))dans pas encore démontré est [`Bounds`](xref:Xamarin.Forms.VisualElement.Bounds) , qui [`Layout`](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) anime effectivement la propriété de lecture seulement en appelant la méthode. Cette méthode est normalement `Layout` appelée par les dérivés comme il sera discuté dans [**le chapitre 26. CustomLayouts**](chapter26.md).

La `LayoutTo` méthode doit être limitée à des fins spéciales. Le programme [**BouncingBox**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BouncingBox) l’utilise pour `BoxView` compresser et étendre un pendant qu’il rebondit sur les côtés d’une page.

[**L’échantillon XamagonXuzzle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle) utilise `LayoutTo` pour déplacer les tuiles dans une mise en œuvre du puzzle classique 15-16 qui affiche une image brouillée plutôt que des tuiles numérotées:

[![Triple capture d’écran de Xamarin Xuzzle](images/ch22fg26-small.png "Jeu de Xuzzle Puzzle")](images/ch22fg26-large.png#lightbox "Jeu de Xuzzle Puzzle")

### <a name="your-own-awaitable-animations"></a>Vos propres animations en attente

[**L’échantillon TryAwaitableAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/TryAwaitableAnimation) crée une animation en attente. La classe cruciale qui `Task` peut retourner un objet de la [`TaskCompletionSource`](xref:System.Threading.Tasks.TaskCompletionSource`1)méthode et le signal lorsque l’animation est terminée est .

## <a name="deeper-into-animations"></a>Plus profondément dans les animations

Le système d’animation Xamarin.Forms peut être un peu déroutant. En plus `Easing` de la classe, le `ViewExtensions` `Animation`système `AnimationExtension` d’animation comprend le , , et les classes.

### <a name="viewextensions-class"></a>VueExtensions classe

Vous avez déjà [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions)vu . Il définit neuf méthodes `Task<bool>` [`CancelAnimations`](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement))qui reviennent et . Sept des neuf méthodes ciblent les propriétés de transformation. Les deux [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))autres sont [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) , qui [`LayoutTo`](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing))cible la [`Layout`](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) propriété, et , qui appelle la méthode.

### <a name="animation-class"></a>Cours d’animation

La [`Animation`](xref:Xamarin.Forms.AnimationExtensions) classe dispose [d’un constructeur](xref:Xamarin.Forms.Animation.%23ctor(System.Action{System.Double},System.Double,System.Double,Xamarin.Forms.Easing,System.Action)) avec cinq arguments pour définir les méthodes de rappel et de finition, et les paramètres de l’animation.

Des animations pour [`Add`](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation))enfants [`Insert`](xref:Xamarin.Forms.Animation.Insert(System.Double,System.Double,Xamarin.Forms.Animation)) [`WithConcurrent`](xref:Xamarin.Forms.Animation.WithConcurrent(Xamarin.Forms.Animation,System.Double,System.Double))peuvent être ajoutées avec , , , et la surcharge de [`WithConcurrent`](xref:Xamarin.Forms.Animation.WithConcurrent(System.Action{System.Double},System.Double,System.Double,Xamarin.Forms.Easing,System.Double,System.Double)).

L’objet d’animation est alors [`Commit`](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean})) commencé par un appel à la méthode.

### <a name="animationextensions-class"></a>Classe AnimationExtensions

La [`AnimationExtensions`](xref:Xamarin.Forms.AnimationExtensions) classe contient principalement des méthodes d’extension. Il existe plusieurs versions d’une `Animate` méthode et la méthode générique [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) est si polyvalente que c’est vraiment la seule fonction d’animation dont vous avez besoin.

## <a name="working-with-the-animation-class"></a>Travailler avec la classe Animation

[**L’échantillon ConcurrentAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) [`Animation`](xref:Xamarin.Forms.Animation) démontre la classe avec plusieurs animations différentes.

### <a name="child-animations"></a>Animations pour enfants

[**L’échantillon De ConcurrentAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) présente également des animations pour [`Add`](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation)) enfants, qui utilisent les méthodes et [`Insert`](xref:Xamarin.Forms.Animation.Insert(System.Double,System.Double,Xamarin.Forms.Animation)) les méthodes (très similaires).

### <a name="beyond-the-high-level-animation-methods"></a>Au-delà des méthodes d’animation de haut niveau

[**L’échantillon De ConcurrentAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) montre également comment effectuer des animations qui vont au-delà des propriétés visées par les `ViewExtensions` méthodes. Dans un exemple, une série de périodes s’allonge; dans un autre `BackgroundColor` exemple, une propriété est animée.

### <a name="more-of-your-own-awaitable-methods"></a>Plus de vos propres méthodes en attente

La [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) méthode `ViewExtensions` de ne fonctionne [`Easing.SpringOut`](xref:Xamarin.Forms.Easing.SpringOut) pas avec la fonction. Il s’arrête lorsque la sortie d’assouplissement dépasse 1.

La bibliothèque [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contient [`MoreViewExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) une classe avec [`TranslateXTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L12) [`TranslateYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L49) et des méthodes d’extension qui n’ont pas ce problème, ainsi que [`CancelTranslateXTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L44) des [`CancelTranslateYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L71) méthodes pour annuler ces animations.

Le [**SpringSlidingEntrance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpringSlidingEntrance) démontre `TranslateXTo` la méthode.

La `MoreExtensions` classe contient [`TranslateXYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L76) également une méthode d’extension [`CancelTranslateXYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L113) qui combine la traduction X et Y, et une méthode.

### <a name="implementing-a-bezier-animation"></a>Mise en œuvre d’une animation Bézier

Il est également possible de développer une animation qui déplace un élément le long du chemin d’un éclat de Bézier. La bibliothèque [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contient une [`BezierSpline`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierSpline.cs) structure qui encapsule [`BezierTangent`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierTangent.cs) un éclat de Bézier et un recensement pour contrôler l’orientation.

La [`MoreViewExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) classe [`BezierPathTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L118) contient une [`CancelBezierPathTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L161) méthode d’extension et une méthode.

[**L’échantillon de BezierLoop**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BezierLoop) montre l’animation d’un élément le long d’un chemin Beizer.

## <a name="working-with-animationextensions"></a>Travailler avec AnimationExtensions

Un type d’animation manquant dans la collection standard est une animation couleur. Le problème est qu’il n’y `Color` a pas de bonne façon d’interpoler entre deux valeurs. Il est possible d’interpoler les valeurs RGB individuelles, mais tout aussi valable est interpolant les valeurs HSL.

Pour cette [`MoreViewExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) raison, la classe de la bibliothèque [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contient deux `Color` méthodes d’animation: [`RgbColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L166) et [`HslColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L188). (Il existe également deux [`CancelRgbColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L183) [`CancelHslColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L206)méthodes d’annulation: et ).

Les deux méthodes [`ColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L211)font usage de , qui [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) effectue [`AnimationExtensions`](xref:Xamarin.Forms.AnimationExtensions)l’animation en appelant la méthode générique étendue dans .

[**L’échantillon ColorAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ColorAnimations) démontre l’utilisation de ces deux types d’animations couleur.

## <a name="structuring-your-animations"></a>Structurer vos animations

Il est parfois utile d’exprimer des animations dans XAML et de les utiliser en collaboration avec MVVM. Ceci est couvert dans le chapitre suivant, [**Chapitre 23. Déclencheurs et comportements**](chapter23.md).

## <a name="related-links"></a>Liens connexes

- [Chapitre 22 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)
- [Échantillons du chapitre 22](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)
- [Animation](~/xamarin-forms/user-interface/animation/index.md)
