---
title: Animations personnalisées dans Xamarin.Forms
description: Cet article montre comment utiliser la classe d’Animation de Xamarin.FOrms pour créer et d’annuler des animations, de synchroniser plusieurs animations et créer des animations personnalisées animer des propriétés qui ne sont pas animées par les méthodes d’animation existantes.
ms.prod: xamarin
ms.assetid: 03B2E3FC-E720-4D45-B9A0-711081FC1907
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/10/2019
ms.openlocfilehash: 405d7990b622b890aa3d66bd632662f086441666
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2020
ms.locfileid: "78292139"
---
# <a name="custom-animations-in-xamarinforms"></a>Animations personnalisées dans Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-custom)

_La classe d’animation est le bloc de construction de toutes les animations Xamarin. Forms, avec les méthodes d’extension de la classe ViewExtensions qui créent un ou plusieurs objets d’animation. Cet article montre comment utiliser la classe d’animation pour créer et annuler des animations, synchroniser plusieurs animations et créer des animations personnalisées qui animent des propriétés qui ne sont pas animées par les méthodes d’animation existantes._

Un certain nombre de paramètres doivent être spécifiés lors de la création d’un objet `Animation`, y compris les valeurs de début et de fin de la propriété animée et un rappel qui modifie la valeur de la propriété. Un objet `Animation` peut également gérer une collection d’animations enfants qui peuvent être exécutées et synchronisées. Pour plus d’informations, consultez [animations enfants](#child).

L’exécution d’une animation créée avec la classe [`Animation`](xref:Xamarin.Forms.Animation) , qui peut ou non inclure des animations enfants, est obtenue en appelant la méthode [`Commit`](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean})) . Cette méthode spécifie la durée de l’animation, entre autres choses, un rappel qui contrôle s’il faut répéter l’animation.

En outre, la classe [`Animation`](xref:Xamarin.Forms.Animation) a une propriété `IsEnabled` qui peut être examinée pour déterminer si les animations ont été désactivées par le système d’exploitation, par exemple lorsque le mode d’économie d’énergie est activé.

## <a name="create-an-animation"></a>Créer une animation

Lorsque vous créez un objet [`Animation`](xref:Xamarin.Forms.Animation) , en général, un minimum de trois paramètres sont requis, comme illustré dans l’exemple de code suivant :

```csharp
var animation = new Animation (v => image.Scale = v, 1, 2);
```

Ce code définit une animation de la propriété [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) d’une instance [`Image`](xref:Xamarin.Forms.Image) à partir d’une valeur de 1 à une valeur de 2. La valeur animée, qui est dérivée par Xamarin. Forms, est passée au rappel spécifié comme premier argument, où elle est utilisée pour modifier la valeur de la propriété `Scale`.

L’animation est démarrée avec un appel à la méthode [`Commit`](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean})) , comme illustré dans l’exemple de code suivant :

```csharp
animation.Commit (this, "SimpleAnimation", 16, 2000, Easing.Linear, (v, c) => image.Scale = 1, () => true);
```

Notez que la méthode [`Commit`](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean})) ne retourne pas d’objet `Task`. Au lieu de cela, les notifications sont fournies par le biais des méthodes de rappel.

Les arguments suivants sont spécifiés dans la méthode `Commit` :

- Le premier argument (*owner*) identifie le propriétaire de l’animation. Cela peut être l’élément visuel sur lequel l’animation est appliquée, ou un autre élément visuel, telles que la page.
- Le deuxième argument (*Name*) identifie l’animation avec un nom. Le nom est combiné avec le propriétaire pour identifier de manière unique l’animation. Cette identification unique peut ensuite être utilisée pour déterminer si l’animation est en cours d’exécution ([`AnimationIsRunning`](xref:Xamarin.Forms.AnimationExtensions.AnimationIsRunning(Xamarin.Forms.IAnimatable,System.String))), ou pour l’annuler ([`AbortAnimation`](xref:Xamarin.Forms.AnimationExtensions.AbortAnimation(Xamarin.Forms.IAnimatable,System.String))).
- Le troisième argument (*rate*) indique le nombre de millisecondes entre chaque appel à la méthode de rappel définie dans le constructeur [`Animation`](xref:Xamarin.Forms.Animation)
- Le quatrième argument (*Length*) indique la durée de l’animation, en millisecondes.
- Le cinquième argument (*accélération*) définit la fonction d’accélération à utiliser dans l’animation. La fonction d’accélération peut également être spécifiée en tant qu’argument pour le constructeur [`Animation`](xref:Xamarin.Forms.Animation) . Pour plus d’informations sur les fonctions d’accélération, consultez [fonctions d’accélération](~/xamarin-forms/user-interface/animation/easing.md).
- Le sixième argument (*terminé*) est un rappel qui est exécuté lorsque l’animation est terminée. Ce rappel accepte deux arguments, avec le premier argument indiquant une valeur finale, et le deuxième argument étant un `bool` qui a la valeur `true` si l’animation a été annulée. Le rappel *terminé* peut également être spécifié en tant qu’argument au constructeur [`Animation`](xref:Xamarin.Forms.Animation) . Toutefois, avec une seule animation, si les rappels *terminés* sont spécifiés dans le constructeur `Animation` et la méthode `Commit`, seul le rappel spécifié dans la méthode `Commit` sera exécuté.
- Le septième argument (*REPEAT*) est un rappel qui permet de répéter l’animation. Elle est appelée à la fin de l’animation et retourne `true` indique que l’animation doit être répétée.

L’effet global consiste à créer une animation qui augmente la propriété [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) d’une [`Image`](xref:Xamarin.Forms.Image) de 1 à 2, en plus de 2 secondes (2000 millisecondes), à l’aide de la fonction d’accélération [`Linear`](xref:Xamarin.Forms.Easing.Linear) . À chaque fois que l’animation est terminée, sa `Scale` propriété est réinitialisée à 1 et l’animation se répète.

> [!NOTE]
> Les animations simultanées, qui s’exécutent indépendamment les unes des autres, peuvent être construites en créant un objet `Animation` pour chaque animation, puis en appelant la méthode `Commit` sur chaque animation.

<a name="child" />

### <a name="child-animations"></a>Animations enfants

La classe [`Animation`](xref:Xamarin.Forms.Animation) prend également en charge les animations enfants, ce qui implique la création d’un objet `Animation` auquel d’autres objets `Animation` sont ajoutés. Cela permet une série d’animations doit exécuter et synchronisé. L’exemple de code suivant montre comment créer et exécuter des animations enfants :

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

Vous pouvez également l’exemple de code peut être écrite de manière plus concise, comme illustré dans l’exemple de code suivant :

```csharp
new Animation {
    { 0, 0.5, new Animation (v => image.Scale = v, 1, 2) },
    { 0, 1, new Animation (v => image.Rotation = v, 0, 360) },
    { 0.5, 1, new Animation (v => image.Scale = v, 2, 1) }
    }.Commit (this, "ChildAnimations", 16, 4000, null, (v, c) => SetIsEnabledButtonState (true, false));
```

Dans les deux exemples de code, un objet [`Animation`](xref:Xamarin.Forms.Animation) parent est créé, auquel des objets `Animation` supplémentaires sont ajoutés. Les deux premiers arguments de la méthode [`Add`](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation)) spécifient quand commencer et finir l’animation enfant. Les valeurs d’argument doivent être comprise entre 0 et 1 et représentent la période relative au sein de l’animation parent que l’animation de l’enfant spécifié sera active. Par conséquent, dans cet exemple, la `scaleUpAnimation` est active pour la première moitié de l’animation, la `scaleDownAnimation` est active pour la deuxième moitié de l’animation et la `rotateAnimation` est active pendant toute la durée.

L’effet global est que l’animation se produit plus de 4 secondes (4000 millisecondes). Le `scaleUpAnimation` anime la propriété [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) de 1 à 2, en plus de 2 secondes. Le `scaleDownAnimation` anime ensuite la propriété `Scale` de 2 à 1, en plus de 2 secondes. Tandis que les deux animations d’échelle se produisent, le `rotateAnimation` anime la propriété [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) de 0 à 360, en plus de 4 secondes. Notez que les animations de mise à l’échelle utilisent également des fonctions d’accélération. La fonction d’accélération [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn) entraîne la réduction initiale du [`Image`](xref:Xamarin.Forms.Image) avant son agrandissement, et la fonction d’accélération [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut) entraîne une diminution de la taille de la `Image` par rapport à sa taille réelle vers la fin de l’animation complète.

Il existe un certain nombre de différences entre un objet [`Animation`](xref:Xamarin.Forms.Animation) qui utilise des animations enfants et un autre qui ne :

- Lors de l’utilisation d’animations enfants, le rappel *terminé* sur une animation enfant indique quand l’enfant est terminé, et le rappel *terminé* passé à la méthode `Commit` indique le moment où l’animation entière est terminée.
- Lorsque vous utilisez des animations enfants, le retour d' `true` à partir du rappel *REPEAT* sur la méthode `Commit` n’entraîne pas la répétition de l’animation, mais l’animation continue de s’exécuter sans nouvelles valeurs.
- Lorsque vous incluez une fonction d’accélération dans la méthode `Commit`, et que la fonction d’accélération retourne une valeur supérieure à 1, l’animation sera arrêtée. Si la fonction d’accélération retourne une valeur inférieure à 0, la valeur est ancrée à 0. Pour utiliser une fonction d’accélération qui retourne une valeur inférieure à 0 ou supérieure à 1, elle doit être spécifiée dans l’une des animations enfants, plutôt que dans la méthode `Commit`.

La classe [`Animation`](xref:Xamarin.Forms.Animation) comprend également [`WithConcurrent`](xref:Xamarin.Forms.Animation.WithConcurrent(Xamarin.Forms.Animation,System.Double,System.Double)) méthodes qui peuvent être utilisées pour ajouter des animations enfants à un objet `Animation` parent. Toutefois, les valeurs des arguments *Begin* et *Finish* ne sont pas limitées à 0 à 1, mais seule la partie de l’animation enfant qui correspond à une plage de 0 à 1 est active. Par exemple, si un `WithConcurrent` appel de méthode définit une animation enfant qui cible une [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) propriété de 1 à 6, mais avec les valeurs de *début* et de *fin* de-2 et 3, la valeur de *début* de-2 correspond à une valeur de `Scale` de 1 et la valeur de *fin* 3 correspond à une valeur de `Scale` de 6. Étant donné que les valeurs situées en dehors de la plage 0 et 1 ne lisent aucune partie dans une animation, la propriété `Scale` n’est animée que de 3 à 6.

## <a name="cancel-an-animation"></a>Annuler une animation

Une application peut annuler une animation avec un appel à la méthode d’extension [`AbortAnimation`](xref:Xamarin.Forms.AnimationExtensions.AbortAnimation(Xamarin.Forms.IAnimatable,System.String)) , comme illustré dans l’exemple de code suivant :

```csharp
this.AbortAnimation ("SimpleAnimation");
```

Notez que les animations sont identifiées de manière unique par une combinaison du propriétaire d’animation et le nom de l’animation. Par conséquent, le propriétaire et le nom spécifié lorsque l’animation en cours d’exécution doit être spécifié pour annuler l’animation. Par conséquent, l’exemple de code annule immédiatement l’animation nommée `SimpleAnimation` qui appartient à la page.

## <a name="create-a-custom-animation"></a>Créer une animation personnalisée

Jusqu’à présent, les exemples présentés ici ont démontré des animations qui peuvent être obtenues de la même manière avec les méthodes de la classe [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) . Toutefois, l’avantage de la classe [`Animation`](xref:Xamarin.Forms.Animation) est qu’elle a accès à la méthode de rappel, qui est exécutée lorsque la valeur animée change. Cela permet au rappel d’implémenter une animation que souhaitée. Par exemple, l’exemple de code suivant anime la propriété [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) d’une page en la définissant sur [`Color`](xref:Xamarin.Forms.Color) valeurs créées par la méthode [`Color.FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double)) , avec des valeurs de teinte comprises entre 0 et 1 :

```csharp
new Animation (callback: v => BackgroundColor = Color.FromHsla (v, 1, 0.5),
  start: 0,
  end: 1).Commit (this, "Animation", 16, 4000, Easing.Linear, (v, c) => BackgroundColor = Color.Default);
```

L’animation qui en résulte fournit l’apparence d’avancer l’arrière-plan de la page via les couleurs de l’arc-en-ciel.

Pour obtenir plus d’exemples de création d’animations complexes, y compris une animation de courbe de Bézier, consultez le [Chapitre 22](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf) sur la [création d’Mobile Apps avec Xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md).

## <a name="create-a-custom-animation-extension-method"></a>Créer une méthode d’extension d’animation personnalisée

Les méthodes d’extension de la classe [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) animent une propriété à partir de sa valeur actuelle jusqu’à une valeur spécifiée. Cela complique la création, par exemple, d’une méthode d’animation `ColorTo` qui peut être utilisée pour animer une couleur d’une valeur à une autre, car :

- La seule propriété [`Color`](xref:Xamarin.Forms.Color) définie par la classe [`VisualElement`](xref:Xamarin.Forms.VisualElement) est [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor), qui n’est pas toujours la propriété `Color` souhaitée à animer.
- Souvent, la valeur actuelle d’une propriété [`Color`](xref:Xamarin.Forms.Color) est [`Color.Default`](xref:Xamarin.Forms.Color.Default), qui n’est pas une véritable couleur et qui ne peut pas être utilisée dans les calculs d’interpolation.

La solution à ce problème consiste à ne pas faire en sorte que la méthode `ColorTo` cible une propriété [`Color`](xref:Xamarin.Forms.Color) particulière. Au lieu de cela, elle peut être écrite à l’aide d’une méthode de rappel qui transmet à l’appelant la valeur interpolée `Color`. En outre, la méthode prendra les arguments de début et de fin `Color`.

La méthode `ColorTo` peut être implémentée en tant que méthode d’extension qui utilise la méthode [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) dans la classe [`AnimationExtensions`](xref:Xamarin.Forms.AnimationExtensions) pour fournir ses fonctionnalités. Cela est dû au fait que la méthode `Animate` peut être utilisée pour cibler des propriétés qui ne sont pas de type `double`, comme illustré dans l’exemple de code suivant :

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

La méthode [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) nécessite un argument de *transformation* , qui est une méthode de rappel. L’entrée de ce rappel est toujours une `double` comprise entre 0 et 1. Par conséquent, la méthode `ColorTo` définit sa propre `Func` de transformation qui accepte un `double` allant de 0 à 1, et qui retourne une valeur [`Color`](xref:Xamarin.Forms.Color) correspondant à cette valeur. La valeur `Color` est calculée en interpolant les valeurs [`R`](xref:Xamarin.Forms.Color.R), [`G`](xref:Xamarin.Forms.Color.G), [`B`](xref:Xamarin.Forms.Color.B)et [`A`](xref:Xamarin.Forms.Color.A) des deux arguments `Color` fournis. La valeur `Color` est ensuite transmise à la méthode de rappel pour l’application vers une propriété particulière.

Cette approche permet à la méthode `ColorTo` d’animer n’importe quelle propriété [`Color`](xref:Xamarin.Forms.Color) , comme illustré dans l’exemple de code suivant :

```csharp
await Task.WhenAll(
  label.ColorTo(Color.Red, Color.Blue, c => label.TextColor = c, 5000),
  label.ColorTo(Color.Blue, Color.Red, c => label.BackgroundColor = c, 5000));
await this.ColorTo(Color.FromRgb(0, 0, 0), Color.FromRgb(255, 255, 255), c => BackgroundColor = c, 5000);
await boxView.ColorTo(Color.Blue, Color.Red, c => boxView.Color = c, 4000);
```

Dans cet exemple de code, la méthode `ColorTo` anime les propriétés [`TextColor`](xref:Xamarin.Forms.Label.TextColor) et [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) d’un [`Label`](xref:Xamarin.Forms.Label), la propriété `BackgroundColor` d’une page et la propriété [`Color`](xref:Xamarin.Forms.BoxView.Color) d’un [`BoxView`](xref:Xamarin.Forms.BoxView).

## <a name="related-links"></a>Liens connexes

- [Animations personnalisées (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-custom)
- [API d’animation](xref:Xamarin.Forms.Animation)
- [API AnimationExtensions](xref:Xamarin.Forms.AnimationExtensions)
