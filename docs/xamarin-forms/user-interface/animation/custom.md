---
title: Animations personnalisées dansXamarin.Forms
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4fb9c94c39823e4ce6d60be6b9dbef1294321a63
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137226"
---
# <a name="custom-animations-in-xamarinforms"></a>Animations personnalisées dansXamarin.Forms

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-custom)

_La classe d’animation est le bloc de construction de toutes les Xamarin.Forms animations, avec les méthodes d’extension de la classe ViewExtensions qui créent un ou plusieurs objets d’animation. Cet article montre comment utiliser la classe d’animation pour créer et annuler des animations, synchroniser plusieurs animations et créer des animations personnalisées qui animent des propriétés qui ne sont pas animées par les méthodes d’animation existantes._

Un certain nombre de paramètres doivent être spécifiés lors `Animation` de la création d’un objet, y compris les valeurs de début et de fin de la propriété animée et un rappel qui modifie la valeur de la propriété. Un `Animation` objet peut également gérer une collection d’animations enfants qui peuvent être exécutées et synchronisées. Pour plus d’informations, consultez [animations enfants](#child).

L’exécution d’une animation créée avec la [`Animation`](xref:Xamarin.Forms.Animation) classe, qui peut ou non inclure des animations enfants, est obtenue en appelant la [ `Commit` ] (XREF : Xamarin.Forms . Animation. Commit ( Xamarin.Forms . IAnimatable, System. String, System. UInt32, System. UInt32, Xamarin.Forms . Méthode d’accélération, System. Action {System. double, System. Boolean}, System. Func {System. Boolean})). Cette méthode spécifie la durée de l’animation, et entre autres éléments, un rappel qui contrôle si l’animation doit être répétée.

En outre, la [`Animation`](xref:Xamarin.Forms.Animation) classe a une `IsEnabled` propriété qui peut être examinée pour déterminer si les animations ont été désactivées par le système d’exploitation, par exemple lorsque le mode d’économie d’énergie est activé.

## <a name="create-an-animation"></a>Créer une animation

Lorsque vous créez un [`Animation`](xref:Xamarin.Forms.Animation) objet, en général, un minimum de trois paramètres sont requis, comme illustré dans l’exemple de code suivant :

```csharp
var animation = new Animation (v => image.Scale = v, 1, 2);
```

Ce code définit une animation de la [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) propriété d’une [`Image`](xref:Xamarin.Forms.Image) instance d’une valeur de 1 à une valeur de 2. La valeur animée, dérivée de Xamarin.Forms , est passée au rappel spécifié comme premier argument, où elle est utilisée pour modifier la valeur de la `Scale` propriété.

L’animation est démarrée avec un appel à [ `Commit` ] (XREF : Xamarin.Forms . Animation. Commit ( Xamarin.Forms . IAnimatable, System. String, System. UInt32, System. UInt32, Xamarin.Forms . Méthode d’accélération, System. Action {System. double, System. Boolean}, System. Func {System. Boolean})), comme illustré dans l’exemple de code suivant :

```csharp
animation.Commit (this, "SimpleAnimation", 16, 2000, Easing.Linear, (v, c) => image.Scale = 1, () => true);
```

Notez que [ `Commit` ] (XREF : Xamarin.Forms . Animation. Commit ( Xamarin.Forms . IAnimatable, System. String, System. UInt32, System. UInt32, Xamarin.Forms . La méthode d’accélération, System. Action {System. double, System. Boolean}, System. Func {System. Boolean})) ne retourne pas d' `Task` objet. Au lieu de cela, les notifications sont fournies par le biais de méthodes de rappel.

Les arguments suivants sont spécifiés dans la `Commit` méthode :

- Le premier argument (*owner*) identifie le propriétaire de l’animation. Il peut s’agir de l’élément visuel sur lequel l’animation est appliquée, ou d’un autre élément visuel, tel que la page.
- Le deuxième argument (*Name*) identifie l’animation avec un nom. Le nom est combiné avec le propriétaire pour identifier l’animation de manière unique. Cette identification unique peut ensuite être utilisée pour déterminer si l’animation est en cours d’exécution ([ `AnimationIsRunning` ] (XREF : Xamarin.Forms . AnimationExtensions. AnimationIsRunning ( Xamarin.Forms . IAnimatable, System. String)), ou pour l’annuler ([ `AbortAnimation` ] (XREF : Xamarin.Forms . AnimationExtensions. AbortAnimation ( Xamarin.Forms . IAnimatable, System. String))).
- Le troisième argument (*rate*) indique le nombre de millisecondes entre chaque appel à la méthode de rappel définie dans le [`Animation`](xref:Xamarin.Forms.Animation) constructeur
- Le quatrième argument (*Length*) indique la durée de l’animation, en millisecondes.
- Le cinquième argument (*accélération*) définit la fonction d’accélération à utiliser dans l’animation. La fonction d’accélération peut également être spécifiée en tant qu’argument pour le [`Animation`](xref:Xamarin.Forms.Animation) constructeur. Pour plus d’informations sur les fonctions d’accélération, consultez [fonctions d’accélération](~/xamarin-forms/user-interface/animation/easing.md).
- Le sixième argument (*terminé*) est un rappel qui est exécuté lorsque l’animation est terminée. Ce rappel prend deux arguments, le premier argument indiquant une valeur finale et le deuxième argument ayant la valeur `bool` `true` si l’animation a été annulée. Le rappel *terminé* peut également être spécifié en tant qu’argument pour le [`Animation`](xref:Xamarin.Forms.Animation) constructeur. Toutefois, avec une seule animation, si les rappels *terminés* sont spécifiés dans le `Animation` constructeur et la `Commit` méthode, seul le rappel spécifié dans la `Commit` méthode est exécuté.
- Le septième argument (*REPEAT*) est un rappel qui permet de répéter l’animation. Elle est appelée à la fin de l’animation et le retour `true` indique que l’animation doit être répétée.

L’effet global consiste à créer une animation qui augmente la [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) propriété d’un [`Image`](xref:Xamarin.Forms.Image) de 1 à 2, en plus de 2 secondes (2000 millisecondes), à l’aide de la [`Linear`](xref:Xamarin.Forms.Easing.Linear) fonction d’accélération. À chaque fois que l’animation est terminée, sa `Scale` propriété est réinitialisée à 1 et l’animation se répète.

> [!NOTE]
> Les animations simultanées, qui s’exécutent indépendamment les unes des autres, peuvent être construites en créant un `Animation` objet pour chaque animation, puis en appelant la `Commit` méthode sur chaque animation.

<a name="child" />

### <a name="child-animations"></a>Animations enfants

La [`Animation`](xref:Xamarin.Forms.Animation) classe prend également en charge les animations enfants, ce qui implique la création d’un `Animation` objet auquel d’autres `Animation` objets sont ajoutés. Cela permet d’exécuter et de synchroniser une série d’animations. L’exemple de code suivant illustre la création et l’exécution d’animations enfants :

```csharp
var parentAnimation = new Animation ();
var scaleUpAnimation = new Animation (v => image.Scale = v, 1, 2, Easing.SpringIn);
var rotateAnimation = new Animation (v => image.Rotation = v, 0, 360);
var scaleDownAnimation = new Animation (v => image.Scale = v, 2, 1, Easing.SpringOut);

parentAnimation.Add (0, 0.5, scaleUpAnimation);
parentAnimation.Add (0, 1, rotateAnimation);
parentAnimation.Add (0.5, 1, scaleDownAnimation);

parentAnimation.Commit (this, "ChildAnimations", 16, 4000, null, (v, c) => SetIsEnabledButtonState (true, false));
```

L’exemple de code peut également être écrit de façon plus concise, comme illustré dans l’exemple de code suivant :

```csharp
new Animation {
    { 0, 0.5, new Animation (v => image.Scale = v, 1, 2) },
    { 0, 1, new Animation (v => image.Rotation = v, 0, 360) },
    { 0.5, 1, new Animation (v => image.Scale = v, 2, 1) }
    }.Commit (this, "ChildAnimations", 16, 4000, null, (v, c) => SetIsEnabledButtonState (true, false));
```

Dans les deux exemples de code, un [`Animation`](xref:Xamarin.Forms.Animation) objet parent est créé, auquel des `Animation` objets supplémentaires sont ensuite ajoutés. Les deux premiers arguments de la [ `Add` ] (XREF : Xamarin.Forms . Animation. Add (System. double, System. double, Xamarin.Forms . Animation)) spécifiez quand commencer et finir l’animation enfant. Les valeurs d’argument doivent être comprises entre 0 et 1, et représentent la période relative dans l’animation parente pendant laquelle l’animation enfant spécifiée sera active. Par conséquent, dans cet exemple, l' `scaleUpAnimation` est actif pour la première moitié de l’animation, l' `scaleDownAnimation` est actif pour la seconde moitié de l’animation et l’est `rotateAnimation` actif pendant toute la durée.

L’effet global est que l’animation a lieu plus de 4 secondes (4000 millisecondes). Le `scaleUpAnimation` anime la [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) propriété de 1 à 2, en plus de 2 secondes. Le `scaleDownAnimation` anime ensuite la `Scale` propriété de 2 à 1, plus de 2 secondes. Tandis que les deux animations d’échelle se produisent, le `rotateAnimation` anime la [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) propriété de 0 à 360, en plus de 4 secondes. Notez que les animations de mise à l’échelle utilisent également des fonctions d’accélération. La [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn) fonction d’accélération force le [`Image`](xref:Xamarin.Forms.Image) à se réduire initialement avant de s’agrandir, et la [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut) fonction d’accélération entraîne une diminution de la taille de la `Image` valeur réelle par rapport à la fin de l’animation complète.

Il existe plusieurs différences entre un [`Animation`](xref:Xamarin.Forms.Animation) objet qui utilise des animations enfants et une autre qui ne l’est pas :

- Lors de l’utilisation d’animations enfants, le rappel *terminé* sur une animation enfant indique quand l’enfant est terminé et que le rappel *terminé* passé à la `Commit` méthode indique le moment où l’animation entière est terminée.
- Lors de l’utilisation d’animations enfants, le retour `true` du rappel *REPEAT* sur la `Commit` méthode n’entraîne pas la répétition de l’animation, mais l’animation continue de s’exécuter sans nouvelles valeurs.
- Lors de l’inclusion d’une fonction d’accélération dans la `Commit` méthode, et si la fonction d’accélération retourne une valeur supérieure à 1, l’animation sera arrêtée. Si la fonction d’accélération retourne une valeur inférieure à 0, la valeur est ancrée à 0. Pour utiliser une fonction d’accélération qui retourne une valeur inférieure à 0 ou supérieure à 1, elle doit être spécifiée dans l’une des animations enfants, plutôt que dans la `Commit` méthode.

La [`Animation`](xref:Xamarin.Forms.Animation) classe comprend également [ `WithConcurrent` ] (XREF : Xamarin.Forms . Animation. WithConcurrent ( Xamarin.Forms . Les méthodes d’animation, System. double, System. double)) qui peuvent être utilisées pour ajouter des animations enfants à un `Animation` objet parent. Toutefois, les valeurs des arguments *Begin* et *Finish* ne sont pas limitées à 0 à 1, mais seule la partie de l’animation enfant qui correspond à une plage de 0 à 1 est active. Par exemple, si un `WithConcurrent` appel de méthode définit une animation enfant qui cible une [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) propriété de 1 à 6, mais avec les valeurs de *début* et de *fin* de-2 et 3, la valeur de *début* de-2 correspond à la `Scale` valeur 1 et la valeur de *fin* 3 correspond à une `Scale` valeur de 6. Étant donné que les valeurs situées en dehors de la plage 0 et 1 ne lisent aucune partie dans une animation, la propriété n’est `Scale` animée que de 3 à 6.

## <a name="cancel-an-animation"></a>Annuler une animation

Une application peut annuler une animation avec un appel à [ `AbortAnimation` ] (XREF : Xamarin.Forms . AnimationExtensions. AbortAnimation ( Xamarin.Forms . IAnimatable, System. String)), comme illustré dans l’exemple de code suivant :

```csharp
this.AbortAnimation ("SimpleAnimation");
```

Notez que les animations sont identifiées de manière unique par une combinaison du propriétaire de l’animation et du nom de l’animation. Par conséquent, le propriétaire et le nom spécifiés lors de l’exécution de l’animation doivent être spécifiés pour annuler l’animation. Par conséquent, l’exemple de code annule immédiatement l’animation nommée `SimpleAnimation` qui appartient à la page.

## <a name="create-a-custom-animation"></a>Créer une animation personnalisée

Jusqu’à présent, les exemples présentés ici ont démontré des animations qui peuvent être obtenues avec les méthodes de la [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) classe. Toutefois, l’avantage de la [`Animation`](xref:Xamarin.Forms.Animation) classe est qu’elle a accès à la méthode de rappel, qui est exécutée lorsque la valeur animée change. Cela permet au rappel d’implémenter toute animation souhaitée. Par exemple, l’exemple de code suivant anime la [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) propriété d’une page en lui affectant des [`Color`](xref:Xamarin.Forms.Color) valeurs créées par la [`Color.FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double)) méthode, avec des valeurs de teinte comprises entre 0 et 1 :

```csharp
new Animation (callback: v => BackgroundColor = Color.FromHsla (v, 1, 0.5),
  start: 0,
  end: 1).Commit (this, "Animation", 16, 4000, Easing.Linear, (v, c) => BackgroundColor = Color.Default);
```

L’animation qui en résulte donne l’impression d’avancer l’arrière-plan de la page dans les couleurs de l’arc-en-ciel.

Pour obtenir plus d’exemples de création d’animations complexes, y compris une animation de courbe de Bézier, consultez le [Chapitre 22](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf) de [création d’Mobile Apps Xamarin.Forms avec ](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md).

## <a name="create-a-custom-animation-extension-method"></a>Créer une méthode d’extension d’animation personnalisée

Les méthodes d’extension dans la [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) classe animent une propriété à partir de sa valeur actuelle jusqu’à une valeur spécifiée. Cela complique la création, par exemple, d’une `ColorTo` méthode d’animation qui peut être utilisée pour animer une couleur d’une valeur à une autre, car :

- La seule [`Color`](xref:Xamarin.Forms.Color) propriété définie par la [`VisualElement`](xref:Xamarin.Forms.VisualElement) classe est [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) , qui n’est pas toujours la `Color` propriété souhaitée à animer.
- Souvent, la valeur actuelle d’une [`Color`](xref:Xamarin.Forms.Color) propriété est [`Color.Default`](xref:Xamarin.Forms.Color.Default) , qui n’est pas une véritable couleur et qui ne peut pas être utilisée dans les calculs d’interpolation.

La solution à ce problème consiste à ne pas faire en sorte que la `ColorTo` méthode cible une [`Color`](xref:Xamarin.Forms.Color) propriété particulière. Au lieu de cela, il peut être écrit avec une méthode de rappel qui renvoie la valeur interpolée `Color` à l’appelant. En outre, la méthode prend des arguments de début et de fin `Color` .

La `ColorTo` méthode peut être implémentée en tant que méthode d’extension qui utilise la [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) méthode dans la [`AnimationExtensions`](xref:Xamarin.Forms.AnimationExtensions) classe pour fournir ses fonctionnalités. Cela est dû au fait que la `Animate` méthode peut être utilisée pour cibler des propriétés qui ne sont pas de type `double` , comme illustré dans l’exemple de code suivant :

```csharp
public static class ViewExtensions
{
  public static Task<bool> ColorTo(this VisualElement self, Color fromColor, Color toColor, Action<Color> callback, uint length = 250, Easing easing = null)
  {
    Func<double, Color> transform = (t) =>
      Color.FromRgba(fromColor.R + t * (toColor.R - fromColor.R),
                     fromColor.G + t * (toColor.G - fromColor.G),
                     fromColor.B + t * (toColor.B - fromColor.B),
                     fromColor.A + t * (toColor.A - fromColor.A));
    return ColorAnimation(self, "ColorTo", transform, callback, length, easing);
  }

  public static void CancelAnimation(this VisualElement self)
  {
    self.AbortAnimation("ColorTo");
  }

  static Task<bool> ColorAnimation(VisualElement element, string name, Func<double, Color> transform, Action<Color> callback, uint length, Easing easing)
  {
    easing = easing ?? Easing.Linear;
    var taskCompletionSource = new TaskCompletionSource<bool>();

    element.Animate<Color>(name, transform, callback, 16, length, easing, (v, c) => taskCompletionSource.SetResult(c));
    return taskCompletionSource.Task;
  }
}
```

La [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) méthode requiert un argument de *transformation* , qui est une méthode de rappel. L’entrée de ce rappel est toujours `double` comprise entre 0 et 1. Par conséquent, la `ColorTo` méthode définit sa propre transformation `Func` qui accepte une `double` valeur comprise entre 0 et 1, et qui retourne une [`Color`](xref:Xamarin.Forms.Color) valeur correspondant à cette valeur. La `Color` valeur est calculée en interpolant les [`R`](xref:Xamarin.Forms.Color.R) [`G`](xref:Xamarin.Forms.Color.G) valeurs,, [`B`](xref:Xamarin.Forms.Color.B) et [`A`](xref:Xamarin.Forms.Color.A) des deux `Color` arguments fournis. La `Color` valeur est ensuite transmise à la méthode de rappel pour l’application vers une propriété particulière.

Cette approche permet `ColorTo` à la méthode d’animer n’importe quelle [`Color`](xref:Xamarin.Forms.Color) propriété, comme illustré dans l’exemple de code suivant :

```csharp
await Task.WhenAll(
  label.ColorTo(Color.Red, Color.Blue, c => label.TextColor = c, 5000),
  label.ColorTo(Color.Blue, Color.Red, c => label.BackgroundColor = c, 5000));
await this.ColorTo(Color.FromRgb(0, 0, 0), Color.FromRgb(255, 255, 255), c => BackgroundColor = c, 5000);
await boxView.ColorTo(Color.Blue, Color.Red, c => boxView.Color = c, 4000);
```

Dans cet exemple de code, la `ColorTo` méthode anime les [`TextColor`](xref:Xamarin.Forms.Label.TextColor) [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) Propriétés et d’un [`Label`](xref:Xamarin.Forms.Label) , la `BackgroundColor` propriété d’une page et la [`Color`](xref:Xamarin.Forms.BoxView.Color) propriété d’un [`BoxView`](xref:Xamarin.Forms.BoxView) .

## <a name="related-links"></a>Liens connexes

- [Animations personnalisées (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-custom)
- [API d’animation](xref:Xamarin.Forms.Animation)
- [API AnimationExtensions](xref:Xamarin.Forms.AnimationExtensions)
