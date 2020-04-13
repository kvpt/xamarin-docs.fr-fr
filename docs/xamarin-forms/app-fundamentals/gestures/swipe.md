---
title: Ajout d’un module de reconnaissance des mouvements de balayage
description: Cet article explique comment reconnaître un mouvement de balayage qui se produit sur une vue.
ms.prod: xamarin
ms.assetid: 164976C2-1429-49FB-9EB6-621E2681C19B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/14/2018
ms.openlocfilehash: ae9b5eb5b768b50ddcbc199040074de855f220de
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "68649455"
---
# <a name="adding-a-swipe-gesture-recognizer"></a>Ajout d’un module de reconnaissance des mouvements de balayage

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-swipegesture)

_Un geste de balayage se produit quand un doigt est déplacé à travers l’écran dans une direction horizontale ou verticale, et est souvent utilisé pour initier la navigation à travers le contenu. Les exemples de code de cet article sont tirés de l’échantillon [Swipe Gesture.](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-swipegesture)_

Pour faire [`View`](xref:Xamarin.Forms.View) reconnaître un geste [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) de balayage, [`Direction`](xref:Xamarin.Forms.SwipeGestureRecognizer.Direction) créer [`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection) une instance,`Left`définir `Right` `Up`la `Down`propriété à une [`Threshold`](xref:Xamarin.Forms.SwipeGestureRecognizer.Threshold) valeur de [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) recensement ( , , , [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) ou ), optionnellement définir la propriété, gérer l’événement, et ajouter le nouveau gestuelle reconnaissance à la collection sur la vue. L’exemple de `SwipeGestureRecognizer` code suivant [`BoxView`](xref:Xamarin.Forms.BoxView)montre un joint à un :

```xaml
<BoxView Color="Teal" ...>
    <BoxView.GestureRecognizers>
        <SwipeGestureRecognizer Direction="Left" Swiped="OnSwiped"/>
    </BoxView.GestureRecognizers>
</BoxView>
```

Voici le code C# équivalent :

```csharp
var boxView = new BoxView { Color = Color.Teal, ... };
var leftSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Left };
leftSwipeGesture.Swiped += OnSwiped;

boxView.GestureRecognizers.Add(leftSwipeGesture);
```

La [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) classe comprend [`Threshold`](xref:Xamarin.Forms.SwipeGestureRecognizer.Threshold) également une propriété, qui `uint` peut être réglé en option à une valeur qui représente la distance minimale de balayage qui doit être atteint pour un balayage à reconnaître, dans les unités indépendantes de l’appareil. La valeur par défaut de cette propriété est 100, ce qui signifie qu’un balayage inférieur à 100 unités indépendantes de l’appareil est ignoré.

## <a name="recognizing-the-swipe-direction"></a>Reconnaissance du sens du balayage

Dans les exemples [`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction) ci-dessus, la propriété [`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection) est définie à une seule valeur de l’énumération. Cependant, il est également possible de définir cette `SwipeDirection` propriété à plusieurs [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) valeurs de l’énumération, de sorte que l’événement est tiré en réponse à un balayage dans plus d’une direction. Cependant, la contrainte est [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) qu’un seul ne peut reconnaître que les balayages qui se produisent sur le même axe. Par conséquent, les balayages qui se produisent sur l’axe horizontal peuvent être reconnus en définissant la propriété `Direction` sur `Left` et `Right` :

```xaml
<SwipeGestureRecognizer Direction="Left,Right" Swiped="OnSwiped"/>
```

De même, les balayages qui se produisent [`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction) sur `Up` l’axe vertical peuvent être reconnus en définissant la propriété à et `Down`:

```csharp
var swipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Up | SwipeDirection.Down };
```

Alternativement, [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) une direction pour chaque balayage peut être créée pour reconnaître les balayages dans toutes les directions :

```xaml
<BoxView Color="Teal" ...>
    <BoxView.GestureRecognizers>
        <SwipeGestureRecognizer Direction="Left" Swiped="OnSwiped"/>
        <SwipeGestureRecognizer Direction="Right" Swiped="OnSwiped"/>
        <SwipeGestureRecognizer Direction="Up" Swiped="OnSwiped"/>
        <SwipeGestureRecognizer Direction="Down" Swiped="OnSwiped"/>
    </BoxView.GestureRecognizers>
</BoxView>
```

Voici le code C# équivalent :

```csharp
var boxView = new BoxView { Color = Color.Teal, ... };
var leftSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Left };
leftSwipeGesture.Swiped += OnSwiped;
var rightSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Right };
rightSwipeGesture.Swiped += OnSwiped;
var upSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Up };
upSwipeGesture.Swiped += OnSwiped;
var downSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Down };
downSwipeGesture.Swiped += OnSwiped;

boxView.GestureRecognizers.Add(leftSwipeGesture);
boxView.GestureRecognizers.Add(rightSwipeGesture);
boxView.GestureRecognizers.Add(upSwipeGesture);
boxView.GestureRecognizers.Add(downSwipeGesture);
```

> [!NOTE]
> Dans les exemples ci-dessus, le [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) même gestionnaire d’événement répond au tir de l’événement. Cependant, [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) chaque instance peut utiliser un gestionnaire d’événement différent si nécessaire.

## <a name="responding-to-the-swipe"></a>Réponse au balayage

Un gestionnaire d’événements pour l’événement [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) est montré dans l’exemple suivant :

```csharp
void OnSwiped(object sender, SwipedEventArgs e)
{
    switch (e.Direction)
    {
        case SwipeDirection.Left:
            // Handle the swipe
            break;
        case SwipeDirection.Right:
            // Handle the swipe
            break;
        case SwipeDirection.Up:
            // Handle the swipe
            break;
        case SwipeDirection.Down:
            // Handle the swipe
            break;
    }
}
```

Le [`SwipedEventArgs`](xref:Xamarin.Forms.SwipedEventArgs) peut être examiné pour déterminer la direction du balayage, avec la logique personnalisée répondant au balayage au besoin. La direction du balayage peut être [`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction) obtenue à partir de la propriété des arguments [`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection) de l’événement, qui sera définie à l’une des valeurs de l’énumération. En outre, les arguments [`Parameter`](xref:Xamarin.Forms.SwipedEventArgs.Parameter) de l’événement ont également [`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter) une propriété qui sera définie à la valeur de la propriété, si elle est définie.

## <a name="using-commands"></a>Utilisation de commandes

La [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) classe [`Command`](xref:Xamarin.Forms.SwipeGestureRecognizer.Command) comprend [`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter) également et les propriétés. Ces propriétés sont généralement utilisées dans les applications qui utilisent le modèle modèle-vue-vue modèle (MVVM). La `Command` propriété définit `ICommand` la manière d’être invoquée lorsqu’un geste de balayage `CommandParameter` `ICommand.` est reconnu, la propriété `Command` définissant `ICommand` un objet à transmettre au code [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)suivant montre comment lier la propriété à un modèle défini dans le modèle de vue dont l’instance est définie comme la page :

```csharp
var boxView = new BoxView { Color = Color.Teal, ... };
var leftSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Left, CommandParameter = "Left" };
leftSwipeGesture.SetBinding(SwipeGestureRecognizer.CommandProperty, "SwipeCommand");
boxView.GestureRecognizers.Add(leftSwipeGesture);
```

Le code XAML équivalent est :

```xaml
<BoxView Color="Teal" ...>
    <BoxView.GestureRecognizers>
        <SwipeGestureRecognizer Direction="Left" Command="{Binding SwipeCommand}" CommandParameter="Left" />
    </BoxView.GestureRecognizers>
</BoxView>
```

`SwipeCommand`est une propriété `ICommand` de type définie dans l’instance [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)du modèle de vue qui est définie comme la page . Quand un mouvement de balayage est reconnu, la méthode `Execute` de l’objet `SwipeCommand` est exécutée. L’argument `Execute` de la méthode [`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter) est la valeur de la propriété. Pour plus d’informations sur les commandes, consultez [Interface de commande](~/xamarin-forms/app-fundamentals/data-binding/commanding.md).

## <a name="creating-a-swipe-container"></a>Création d’un conteneur de balayage

La `SwipeContainer` classe, qui est montrée dans l’exemple de code suivant, [`View`](xref:Xamarin.Forms.View) est une classe de reconnaissance de balayage généralisée qui soit enroulée autour d’une reconnaissance de geste de balayage pour effectuer :

```csharp
public class SwipeContainer : ContentView
{
    public event EventHandler<SwipedEventArgs> Swipe;

    public SwipeContainer()
    {
        GestureRecognizers.Add(GetSwipeGestureRecognizer(SwipeDirection.Left));
        GestureRecognizers.Add(GetSwipeGestureRecognizer(SwipeDirection.Right));
        GestureRecognizers.Add(GetSwipeGestureRecognizer(SwipeDirection.Up));
        GestureRecognizers.Add(GetSwipeGestureRecognizer(SwipeDirection.Down));
    }

    SwipeGestureRecognizer GetSwipeGestureRecognizer(SwipeDirection direction)
    {
        var swipe = new SwipeGestureRecognizer { Direction = direction };
        swipe.Swiped += (sender, e) => Swipe?.Invoke(this, e);
        return swipe;
    }
}
```

La `SwipeContainer` classe [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) crée des objets pour les `Swipe` quatre directions de balayage, et attache les gestionnaires d’événements. Ces gestionnaires d’événements appellent l’événement `Swipe` défini par le `SwipeContainer`.

L’exemple de code XAML suivant montre la `SwipeContainer` classe enveloppant un [`BoxView`](xref:Xamarin.Forms.BoxView):

```xaml
<ContentPage ...>
    <StackLayout>
        <local:SwipeContainer Swipe="OnSwiped" ...>
            <BoxView Color="Teal" ... />
        </local:SwipeContainer>
    </StackLayout>
</ContentPage>
```

L’exemple de code `SwipeContainer` suivant [`BoxView`](xref:Xamarin.Forms.BoxView) montre comment les enveloppements d’une page C :

```csharp
public class SwipeContainerPageCS : ContentPage
{
    public SwipeContainerPageCS()
    {
        var boxView = new BoxView { Color = Color.Teal, ... };
        var swipeContainer = new SwipeContainer { Content = boxView, ... };
        swipeContainer.Swipe += (sender, e) =>
        {
          // Handle the swipe
        };

        Content = new StackLayout
        {
            Children = { swipeContainer }
        };
    }
}
```

Lorsque [`BoxView`](xref:Xamarin.Forms.BoxView) le reçoit un [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) geste de [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) balayage, l’événement dans le est tiré. Cet événement est traité par la classe `SwipeContainer` qui déclenche son propre événement `Swipe`. Cet événement `Swipe` est traité dans la page. Le [`SwipedEventArgs`](xref:Xamarin.Forms.SwipedEventArgs) peut ensuite être examiné pour déterminer la direction du balayage, avec la logique personnalisée répondant au balayage au besoin.

## <a name="related-links"></a>Liens connexes

- [Mouvement de balayage (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-swipegesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [SwipeGestureRecognizer](xref:Xamarin.Forms.SwipeGestureRecognizer)
