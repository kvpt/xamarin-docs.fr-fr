---
title: Interaction CarouselView Xamarin. Forms
description: L’élément actuellement affiché dans un CarouselView est accessible via les propriétés CurrentItem et position.
ms.prod: xamarin
ms.assetid: 854D97E5-D119-4BE2-AE7C-BD428792C992
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/14/2019
ms.openlocfilehash: fd85876b38c21c7ff1ea85d7a61c1449395d56f5
ms.sourcegitcommit: e71474f91639bb43159b22f5d534325c3270ba93
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72749790"
---
# <a name="xamarinforms-carouselview-interaction"></a>Interaction CarouselView Xamarin. Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView) définit les propriétés suivantes qui contrôlent l’interaction de l’utilisateur :

- `CurrentItem`, de type `object`, l’élément en cours d’affichage. Cette propriété a un mode de liaison par défaut de `TwoWay` et a une valeur de `null` lorsqu’il n’y a aucune donnée à afficher.
- `CurrentItemChangedCommand`, de type `ICommand`, qui est exécuté lorsque l’élément actuel change.
- `CurrentItemChangedCommandParameter`, de type `object` : paramètre passé à la commande `CurrentItemChangedCommand`.
- `IsBounceEnabled`, de type `bool`, qui spécifie si le `CarouselView` rebondit au niveau d’une limite de contenu. La valeur par défaut est `true`.
- `IsSwipeEnabled`, de type `bool`, qui détermine si un mouvement de balayage modifie l’élément affiché. La valeur par défaut est `true`.
- `Position`, de type `int`, index de l’élément actuel dans la collection sous-jacente. Cette propriété a un mode de liaison par défaut de `TwoWay` et a une valeur 0 lorsqu’il n’y a aucune donnée à afficher.
- `PositionChangedCommand`, de type `ICommand`, qui est exécuté lorsque la position change.
- `PositionChangedCommandParameter`, de type `object` : paramètre passé à la commande `PositionChangedCommand`.

Toutes ces propriétés s’appuient sur des objets [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), ce qui signifie qu’elles peuvent être des cibles de liaisons de données.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) définit un événement `CurrentItemChanged` qui est déclenché lorsque la propriété `CurrentItem` change, en raison du défilement de l’utilisateur ou lorsqu’une application définit la propriété. L’objet `CurrentItemChangedEventArgs` qui accompagne l’événement `CurrentItemChanged` a deux propriétés, de type `object` :

- `PreviousItem` : élément précédent, après la modification de la propriété.
- `CurrentItem` : élément actuel, après la modification de la propriété.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) définit également un événement `PositionChanged` qui est déclenché lorsque la propriété `Position` change, en raison du défilement de l’utilisateur ou lorsqu’une application définit la propriété. L’objet `PositionChangedEventArgs` qui accompagne l’événement `PositionChanged` a deux propriétés, de type `int` :

- `PreviousPosition` : position précédente, après la modification de la propriété.
- `CurrentPosition` : position actuelle, après la modification de la propriété.

## <a name="respond-to-the-current-item-changing"></a>Répondre à la modification de l’élément actuel

Lorsque l’élément actuellement affiché change, la propriété `CurrentItem` est définie sur la valeur de l’élément. Lorsque cette propriété change, l' `CurrentItemChangedCommand` est exécutée avec la valeur de la `CurrentItemChangedCommandParameter` passée à la `ICommand`. La propriété `Position` est ensuite mise à jour et l’événement `CurrentItemChanged` se déclenche.

> [!IMPORTANT]
> La propriété `Position` change lorsque la propriété `CurrentItem` change. Cela entraînera l’exécution de la `PositionChangedCommand` et le déclenchement de l’événement `PositionChanged`.

### <a name="event"></a>événement

L’exemple de code XAML suivant montre une [`CarouselView`](xref:Xamarin.Forms.CarouselView) qui utilise un gestionnaire d’événements pour répondre à la modification de l’élément actuel :

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              CurrentItemChanged="OnCurrentItemChanged">
    ...
</CarouselView>
```

Le code C# équivalent est :

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.CurrentItemChanged += OnCurrentItemChanged;
```

Dans cet exemple, le gestionnaire d’événements `OnCurrentItemChanged` est exécuté lorsque l’événement `CurrentItemChanged` se déclenche, avec le gestionnaire d’événements exposant les éléments précédents et actuels :

```csharp
void OnCurrentItemChanged(object sender, CurrentItemChangedEventArgs e)
{
    Monkey previousItem = e.PreviousItem as Monkey;
    Monkey currentItem = e.CurrentItem as Monkey;
}
```

### <a name="command"></a>Commande

L’exemple de code XAML suivant montre une [`CarouselView`](xref:Xamarin.Forms.CarouselView) qui utilise une commande pour répondre à la modification de l’élément actuel :

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              CurrentItemChangedCommand="{Binding ItemChangedCommand}"
              CurrentItemChangedCommandParameter="{Binding Source={RelativeSource Self}, Path=CurrentItem}">
    ...
</CarouselView>
```

Le code C# équivalent est :

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.SetBinding(CarouselView.CurrentItemChangedCommandProperty, "ItemChangedCommand");
carouselView.SetBinding(CarouselView.CurrentItemChangedCommandParameterProperty, new Binding("CurrentItem", source: RelativeBindingSource.Self));
```

Dans cet exemple, la propriété `CurrentItemChangedCommand` est liée à la propriété `ItemChangedCommand`, en passant la valeur de la propriété `CurrentItem` à celle-ci en tant qu’argument. La `ItemChangedCommand` peut ensuite répondre à la modification de l’élément actuel, selon les besoins :

```csharp
public ICommand ItemChangedCommand => new Command<Monkey>((item) =>
{
    PreviousMonkey = CurrentMonkey;
    CurrentMonkey = item;
});
```

Dans cet exemple, le `ItemChangedCommand` met à jour les objets qui stockent les éléments précédents et actuels.

## <a name="respond-to-the-position-changing"></a>Répondre au changement de position

Lorsque l’élément actuellement affiché change, la propriété `Position` est définie sur l’index de l’élément actuel dans la collection sous-jacente. Lorsque cette propriété change, l' `PositionChangedCommand` est exécutée avec la valeur de la `PositionChangedCommandParameter` passée à la `ICommand`. L’événement `PositionChanged` se déclenche ensuite. Si la propriété `Position` a été modifiée par programme, la [`CarouselView`](xref:Xamarin.Forms.CarouselView) défilera vers l’élément qui correspond à la valeur `Position`.

> [!NOTE]
> Si vous affectez la valeur 0 à la propriété `Position`, le premier élément de la collection sous-jacente est affiché.

### <a name="event"></a>événement

L’exemple de code XAML suivant montre une [`CarouselView`](xref:Xamarin.Forms.CarouselView) qui utilise un gestionnaire d’événements pour répondre à la modification de la propriété `Position` :

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"              
              PositionChanged="OnPositionChanged">
    ...
</CarouselView>
```

Le code C# équivalent est :

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.PositionChanged += OnPositionChanged;
```

Dans cet exemple, le gestionnaire d’événements `OnPositionChanged` est exécuté lorsque l’événement `PositionChanged` se déclenche, avec le gestionnaire d’événements exposant les positions précédente et actuelle :

```csharp
void OnPositionChanged(object sender, PositionChangedEventArgs e)
{
    int previousItemPosition = e.PreviousPosition;
    int currentItemPosition = e.CurrentPosition;
}
```

### <a name="command"></a>Commande

L’exemple de code XAML suivant montre une [`CarouselView`](xref:Xamarin.Forms.CarouselView) qui utilise une commande pour répondre à la modification de la propriété `Position` :

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              PositionChangedCommand="{Binding PositionChangedCommand}"
              PositionChangedCommandParameter="{Binding Source={RelativeSource Self}, Path=Position}">
    ...
</CarouselView>
```

Le code C# équivalent est :

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.SetBinding(CarouselView.PositionChangedCommandProperty, "PositionChangedCommand");
carouselView.SetBinding(CarouselView.PositionChangedCommandParameterProperty, new Binding("Position", source: RelativeBindingSource.Self));
```

Dans cet exemple, la propriété `PositionChangedCommand` est liée à la propriété `PositionChangedCommand`, en passant la valeur de la propriété `Position` à celle-ci en tant qu’argument. Le `PositionChangedCommand` peut ensuite répondre à la modification de la position, selon les besoins :

```csharp
public ICommand PositionChangedCommand => new Command<int>((position) =>
{
    PreviousPosition = CurrentPosition;
    CurrentPosition = position;
});
```

Dans cet exemple, le `PositionChangedCommand` met à jour les objets qui stockent les positions précédentes et actuelles.

## <a name="preset-the-current-item"></a>Prédéfinir l’élément actuel

L’élément actif dans un [`CarouselView`](xref:Xamarin.Forms.CarouselView) peut être défini par programmation en affectant à la propriété `CurrentItem` la valeur de l’élément. L’exemple de code XAML suivant montre une `CarouselView` qui préchoisit l’élément actuel :

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              CurrentItem="{Binding CurrentItem}">
    ...
</CarouselView>
```

Le code C# équivalent est :

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.SetBinding(CarouselView.CurrentItemProperty, "CurrentItem");
```

> [!NOTE]
> La propriété `CurrentItem` a un mode de liaison par défaut de `TwoWay`.

Les données de la propriété `CarouselView.CurrentItem` sont liées à la propriété `CurrentItem` du modèle de vue connecté, qui est de type `Monkey`. Par défaut, une liaison de `TwoWay` est utilisée afin que, si l’utilisateur modifie l’élément actuel, la valeur de la propriété `CurrentItem` soit définie sur l’objet `Monkey` actuel. La propriété `CurrentItem` est définie dans la classe `MonkeysViewModel` et est définie sur le quatrième élément de la collection `Monkeys` :

```csharp
public class MonkeysViewModel : INotifyPropertyChanged
{
    // ...
    public ObservableCollection<Monkey> Monkeys { get; private set; }

    public Monkey CurrentItem { get; set; }

    public MonkeysViewModel()
    {
        // ...
        CurrentItem = Monkeys.Skip(3).FirstOrDefault();
        OnPropertyChanged("CurrentItem");
    }
}
```

## <a name="preset-the-position"></a>Prédéfinir la position

L’élément affiché [`CarouselView`](xref:Xamarin.Forms.CarouselView) peut être défini par programmation en affectant à la propriété `Position` l’index de l’élément dans la collection sous-jacente. L’exemple de code XAML suivant montre une `CarouselView` qui définit l’élément affiché :

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              Position="{Binding Position}">
    ...
</CarouselView>
```

Le code C# équivalent est :

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.SetBinding(CarouselView.PositionProperty, "Position");
```

> [!NOTE]
> La propriété `Position` a un mode de liaison par défaut de `TwoWay`.

Les données de la propriété `CarouselView.Position` sont liées à la propriété `Position` du modèle de vue connecté, qui est de type `int`. Par défaut, une liaison de `TwoWay` est utilisée afin que, si l’utilisateur fait défiler le [`CarouselView`](xref:Xamarin.Forms.CarouselView), la valeur de la propriété `Position` sera définie sur l’index de l’élément affiché. La propriété `Position` est définie dans la classe `MonkeysViewModel` et est définie sur le quatrième élément de la collection `Monkeys` :

```csharp
public class MonkeysViewModel : INotifyPropertyChanged
{
    // ...
    public int Position { get; set; }

    public MonkeysViewModel()
    {
        // ...
        Position = 3;
        OnPropertyChanged("Position");
    }
}
```

## <a name="clear-the-current-item"></a>Effacer l’élément actuel

La propriété `CurrentItem` peut être effacée en la définissant, ou l’objet auquel elle est liée, pour `null`.

## <a name="disable-bounce"></a>Désactiver le rebond

Par défaut, [`CarouselView`](xref:Xamarin.Forms.CarouselView) rebondit les éléments aux limites du contenu. Vous pouvez désactiver cette option en affectant à la propriété `IsBounceEnabled` la valeur `false`.

## <a name="disable-swipe-interaction"></a>Désactiver l’interaction de balayage

Par défaut, [`CarouselView`](xref:Xamarin.Forms.CarouselView) permet aux utilisateurs de se déplacer entre les éléments à l’aide d’un mouvement de balayage. Cette interaction de balayage peut être désactivée en affectant à la propriété `IsSwipeEnabled` la valeur `false`.

## <a name="related-links"></a>Liens connexes

- [CarouselView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
