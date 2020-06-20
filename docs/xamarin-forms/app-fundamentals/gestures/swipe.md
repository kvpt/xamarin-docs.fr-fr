---
title: Ajout d’un module de reconnaissance des mouvements de balayage
description: Cet article explique comment reconnaître un mouvement de balayage qui se produit sur une vue.
ms.prod: xamarin
ms.assetid: 164976C2-1429-49FB-9EB6-621E2681C19B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/14/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 03663803313c870a3361c6e1ffc85cf1f8999068
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137868"
---
# <a name="adding-a-swipe-gesture-recognizer"></a>Ajout d’un module de reconnaissance des mouvements de balayage

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-swipegesture)

_Un mouvement de balayage se produit lorsqu’un doigt est déplacé sur l’écran dans un sens horizontal ou vertical, et est souvent utilisé pour lancer la navigation dans le contenu. Les exemples de code présentés dans cet article sont tirés de l’exemple de [mouvement de balayage](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-swipegesture) ._

Pour faire en sorte qu’un [`View`](xref:Xamarin.Forms.View) mouvement de balayage soit reconnu, créez une [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) instance, affectez [`Direction`](xref:Xamarin.Forms.SwipeGestureRecognizer.Direction) à la propriété une [`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection) valeur d’énumération ( `Left` ,, `Right` `Up` ou `Down` ), définissez éventuellement la [`Threshold`](xref:Xamarin.Forms.SwipeGestureRecognizer.Threshold) propriété, gérez l' [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) événement et ajoutez le nouveau module de reconnaissance de mouvement à la [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) collection sur la vue. L’exemple de code suivant montre un `SwipeGestureRecognizer` attaché à un [`BoxView`](xref:Xamarin.Forms.BoxView) :

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

La [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) classe comprend également une [`Threshold`](xref:Xamarin.Forms.SwipeGestureRecognizer.Threshold) propriété, qui peut être définie éventuellement sur une `uint` valeur qui représente la distance de balayage minimale qui doit être obtenue pour qu’un balayage soit reconnu, en unités indépendantes du périphérique. La valeur par défaut de cette propriété est 100, ce qui signifie qu’un balayage inférieur à 100 unités indépendantes de l’appareil est ignoré.

## <a name="recognizing-the-swipe-direction"></a>Reconnaissance du sens du balayage

Dans les exemples ci-dessus, la [`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction) propriété est définie sur une valeur unique à partir de l' [`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection) énumération. Toutefois, il est également possible de définir cette propriété sur plusieurs valeurs de l' `SwipeDirection` énumération, afin que l' [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) événement soit déclenché en réponse à un balayage dans plus d’une direction. Toutefois, la contrainte est qu’un seul [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) peut reconnaître uniquement les balayages qui se produisent sur le même axe. Par conséquent, les balayages qui se produisent sur l’axe horizontal peuvent être reconnus en définissant la propriété `Direction` sur `Left` et `Right` :

```xaml
<SwipeGestureRecognizer Direction="Left,Right" Swiped="OnSwiped"/>
```

De même, les balayages qui se produisent sur l’axe vertical peuvent être reconnus en affectant à la propriété la valeur [`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction) `Up` et `Down` :

```csharp
var swipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Up | SwipeDirection.Down };
```

Vous [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) pouvez également créer un pour chaque sens de balayage pour reconnaître les balayages dans chaque direction :

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
> Dans les exemples ci-dessus, le même gestionnaire d’événements répond au déclenchement de l' [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) événement. Toutefois, chaque [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) instance peut utiliser un gestionnaire d’événements différent si nécessaire.

## <a name="responding-to-the-swipe"></a>Réponse au balayage

Un gestionnaire d’événements pour l' [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) événement est illustré dans l’exemple suivant :

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

Le [`SwipedEventArgs`](xref:Xamarin.Forms.SwipedEventArgs) peut être examiné pour déterminer la direction du balayage, avec une logique personnalisée répondant au balayage selon les besoins. La direction du balayage peut être obtenue à partir de la [`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction) propriété des arguments d’événement, qui sera définie sur l’une des valeurs de l' [`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection) énumération. En outre, les arguments d’événement ont également une [`Parameter`](xref:Xamarin.Forms.SwipedEventArgs.Parameter) propriété qui sera définie sur la valeur de la [`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter) propriété, si elle est définie.

## <a name="using-commands"></a>Utilisation de commandes

La [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) classe comprend également [`Command`](xref:Xamarin.Forms.SwipeGestureRecognizer.Command) les [`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter) Propriétés et. Ces propriétés sont généralement utilisées dans les applications qui utilisent le modèle modèle-vue-vue modèle (MVVM). La `Command` propriété définit le `ICommand` à appeler lorsqu’un mouvement de balayage est reconnu, avec la `CommandParameter` propriété qui définit un objet à passer à l' `ICommand.` exemple de code suivant montre comment lier la `Command` propriété à un `ICommand` défini dans le modèle de vue dont l’instance est définie en tant que page [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) :

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

`SwipeCommand`est une propriété de type `ICommand` définie dans l’instance de modèle de vue qui est définie comme page [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) . Quand un mouvement de balayage est reconnu, la méthode `Execute` de l’objet `SwipeCommand` est exécutée. L’argument de la `Execute` méthode est la valeur de la [`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter) propriété. Pour plus d’informations sur les commandes, consultez [Interface de commande](~/xamarin-forms/app-fundamentals/data-binding/commanding.md).

## <a name="creating-a-swipe-container"></a>Création d’un conteneur de balayage

La `SwipeContainer` classe, qui est illustrée dans l’exemple de code suivant, est une classe de reconnaissance de balayage généralisée qui est entourée d’un [`View`](xref:Xamarin.Forms.View) pour effectuer la reconnaissance de mouvement de balayage :

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

La `SwipeContainer` classe crée [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) des objets pour les quatre directions de balayage et attache des `Swipe` gestionnaires d’événements. Ces gestionnaires d’événements appellent l’événement `Swipe` défini par le `SwipeContainer`.

L’exemple de code XAML suivant illustre la `SwipeContainer` classe qui encapsule un [`BoxView`](xref:Xamarin.Forms.BoxView) :

```xaml
<ContentPage ...>
    <StackLayout>
        <local:SwipeContainer Swipe="OnSwiped" ...>
            <BoxView Color="Teal" ... />
        </local:SwipeContainer>
    </StackLayout>
</ContentPage>
```

L’exemple de code suivant montre comment le `SwipeContainer` encapsule [`BoxView`](xref:Xamarin.Forms.BoxView) dans un wrapper dans une page C# :

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

Lorsque le [`BoxView`](xref:Xamarin.Forms.BoxView) reçoit un mouvement de balayage, l' [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) événement dans [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) est déclenché. Cet événement est traité par la classe `SwipeContainer` qui déclenche son propre événement `Swipe`. Cet événement `Swipe` est traité dans la page. Le [`SwipedEventArgs`](xref:Xamarin.Forms.SwipedEventArgs) peut ensuite être examiné pour déterminer la direction du balayage, avec une logique personnalisée qui répond au balayage en fonction des besoins.

## <a name="related-links"></a>Liens connexes

- [Mouvement de balayage (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-swipegesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [SwipeGestureRecognizer](xref:Xamarin.Forms.SwipeGestureRecognizer)
