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
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68649455"
---
# <a name="adding-a-swipe-gesture-recognizer"></a>Ajout d’un module de reconnaissance des mouvements de balayage

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-swipegesture)

_Un mouvement de balayage se produit quand un doigt est déplacé verticalement ou horizontalement sur l’écran. Il est souvent utilisé pour lancer l’exploration du contenu. Les exemples de code dans cet article sont tirés de l’exemple [Mouvement de balayage](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-swipegesture)._

Pour qu’un [`View`](xref:Xamarin.Forms.View) reconnaisse un mouvement de balayage, créez une instance [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer), définissez la propriété [`Direction`](xref:Xamarin.Forms.SwipeGestureRecognizer.Direction) sur une valeur d’énumération [`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection) (`Left`, `Right`, `Up` ou `Down`), définissez éventuellement la propriété [`Threshold`](xref:Xamarin.Forms.SwipeGestureRecognizer.Threshold), traitez l’événement [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped), et ajoutez le nouveau module de reconnaissance de geste sur la collection [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) sur la vue. L'exemple de code suivant montre un `SwipeGestureRecognizer` attaché à un [`BoxView`](xref:Xamarin.Forms.BoxView) :

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

La classe [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) inclut également une propriété [`Threshold`](xref:Xamarin.Forms.SwipeGestureRecognizer.Threshold), qui peut éventuellement être définie sur une valeur `uint` qui représente la distance de balayage minimale devant être parcourue pour qu’un balayage soit reconnu, en unités indépendantes de l’appareil. La valeur par défaut de cette propriété est 100, ce qui signifie qu’un balayage inférieur à 100 unités indépendantes de l’appareil est ignoré.

## <a name="recognizing-the-swipe-direction"></a>Reconnaissance du sens du balayage

Dans les exemples ci-dessus, la propriété [`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction) est définie sur une seule valeur de l’énumération [`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection). Toutefois, il est également possible de définir cette propriété sur plusieurs valeurs de l’énumération `SwipeDirection`, pour que l’événement [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) soit déclenché en réponse à un balayage dans plusieurs directions. Toutefois, la contrainte est qu’un seul [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) peut seulement reconnaître les balayages qui se produisent sur le même axe. Par conséquent, les balayages qui se produisent sur l’axe horizontal peuvent être reconnus en définissant la propriété `Direction` sur `Left` et `Right` :

```xaml
<SwipeGestureRecognizer Direction="Left,Right" Swiped="OnSwiped"/>
```

De même, les balayages qui se produisent sur l’axe vertical peuvent être reconnus en définissant la propriété [`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction) sur `Up` et `Down` :

```csharp
var swipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Up | SwipeDirection.Down };
```

Sinon, un [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) pour chaque direction de balayage peut être créé afin de reconnaître les balayages dans toutes les directions :

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
> Dans les exemples ci-dessus, le même gestionnaire d’événements répond au déclenchement de l’événement [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped). Toutefois, chaque instance [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) peut utiliser un autre gestionnaire d’événements si nécessaire.

## <a name="responding-to-the-swipe"></a>Réponse au balayage

Un gestionnaire d'événements pour l’événement [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) est illustré dans l’exemple suivant :

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

Les [`SwipedEventArgs`](xref:Xamarin.Forms.SwipedEventArgs) peuvent être examinés pour déterminer le sens du balayage, avec une logique personnalisée répondant au balayage en fonction des besoins. Vous pouvez obtenir le sens du balayage à partir de la propriété [`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction) des arguments d’événement, définie sur une des valeurs de l’énumération [`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection). Par ailleurs, les arguments d’événement ont également une propriété [`Parameter`](xref:Xamarin.Forms.SwipedEventArgs.Parameter) définie sur la valeur de la propriété [`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter), le cas échéant.

## <a name="using-commands"></a>Utilisation de commandes

La classe [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) inclut également les propriétés [`Command`](xref:Xamarin.Forms.SwipeGestureRecognizer.Command) et [`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter). Ces propriétés sont généralement utilisées dans les applications qui utilisent le modèle modèle-vue-vue modèle (MVVM). La propriété `Command` définit le `ICommand` à appeler quand un mouvement de balayage est reconnu, avec la propriété `CommandParameter` définissant un objet à passer à `ICommand.`. L’exemple de code suivant montre comment lier la propriété `Command` à un `ICommand` défini dans le modèle de vue dont l’instance est définie comme page [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) :

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

`SwipeCommand` est une propriété de type `ICommand` définie dans l’instance de modèle de vue qui est définie comme page [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext). Quand un mouvement de balayage est reconnu, la méthode `Execute` de l’objet `SwipeCommand` est exécutée. L’argument de la méthode `Execute` est la valeur de la propriété [`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter). Pour plus d’informations sur les commandes, consultez [Interface de commande](~/xamarin-forms/app-fundamentals/data-binding/commanding.md).

## <a name="creating-a-swipe-container"></a>Création d’un conteneur de balayage

La classe `SwipeContainer`, illustrée dans l’exemple de code suivant, est une classe de reconnaissance de balayage généralisée encapsulable autour d’un [`View`](xref:Xamarin.Forms.View) pour la reconnaissance de mouvement de balayage :

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

La classe `SwipeContainer` crée des objets [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) pour les 4 directions de balayage et attache les gestionnaires d'événements `Swipe`. Ces gestionnaires d’événements appellent l’événement `Swipe` défini par le `SwipeContainer`.

L’exemple de code XAML suivant montre la classe `SwipeContainer` qui encapsule un [`BoxView`](xref:Xamarin.Forms.BoxView) :

```xaml
<ContentPage ...>
    <StackLayout>
        <local:SwipeContainer Swipe="OnSwiped" ...>
            <BoxView Color="Teal" ... />
        </local:SwipeContainer>
    </StackLayout>
</ContentPage>
```

L’exemple de code suivant montre comment `SwipeContainer` encapsule un [`BoxView`](xref:Xamarin.Forms.BoxView) dans une page C# :

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

Quand [`BoxView`](xref:Xamarin.Forms.BoxView) reçoit un mouvement de balayage, l’événement [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) dans [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) est déclenché. Cet événement est traité par la classe `SwipeContainer` qui déclenche son propre événement `Swipe`. Cet événement `Swipe` est traité dans la page. Les [`SwipedEventArgs`](xref:Xamarin.Forms.SwipedEventArgs) peuvent ensuite être examinés pour déterminer le sens du balayage, avec une logique personnalisée répondant au balayage en fonction des besoins.

## <a name="related-links"></a>Liens connexes

- [Mouvement de balayage (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-swipegesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [SwipeGestureRecognizer](xref:Xamarin.Forms.SwipeGestureRecognizer)
