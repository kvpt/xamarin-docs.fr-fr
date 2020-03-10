---
title: Interaction CarouselView Xamarin. Forms
description: L’élément actuellement affiché dans un CarouselView est accessible via les propriétés CurrentItem et position.
ms.prod: xamarin
ms.assetid: 854D97E5-D119-4BE2-AE7C-BD428792C992
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/11/2020
ms.openlocfilehash: 150c358346f90a513e1558dc847ad7eb6dd6e6e2
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78918394"
---
# <a name="xamarinforms-carouselview-interaction"></a>Interaction CarouselView Xamarin. Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView) définit les propriétés suivantes qui contrôlent l’interaction de l’utilisateur :

- `CurrentItem`, de type `object`, l’élément en cours d’affichage. Cette propriété a un mode de liaison par défaut de `TwoWay`et a une valeur de `null` lorsqu’il n’y a aucune donnée à afficher.
- `CurrentItemChangedCommand`, de type `ICommand`, qui est exécuté lorsque l’élément actuel change.
- `CurrentItemChangedCommandParameter`, de type `object` : paramètre passé à la commande `CurrentItemChangedCommand`.
- `IsBounceEnabled`, de type `bool`, qui spécifie si le `CarouselView` rebondit au niveau d’une limite de contenu. La valeur par défaut est `true`.
- `IsSwipeEnabled`, de type `bool`, qui détermine si un mouvement de balayage modifie l’élément affiché. La valeur par défaut est `true`.
- `Position`, de type `int`, index de l’élément actuel dans la collection sous-jacente. Cette propriété a un mode de liaison par défaut de `TwoWay`et a une valeur 0 lorsqu’il n’y a aucune donnée à afficher.
- `PositionChangedCommand`, de type `ICommand`, qui est exécuté lorsque la position change.
- `PositionChangedCommandParameter`, de type `object` : paramètre passé à la commande `PositionChangedCommand`.
- `VisibleViews`, de type `ObservableCollection<View>`, qui est une propriété en lecture seule qui contient les objets pour les éléments qui sont actuellement visibles.

Toutes ces propriétés sont soutenues par des objets [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), ce qui signifie qu’elles peuvent être des cibles de liaisons de données.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) définit un événement `CurrentItemChanged` qui est déclenché lorsque la propriété `CurrentItem` change, en raison du défilement de l’utilisateur ou lorsqu’une application définit la propriété. L’objet `CurrentItemChangedEventArgs` qui accompagne l’événement `CurrentItemChanged` a deux propriétés, de type `object`:

- `PreviousItem` : élément précédent, après la modification de la propriété.
- `CurrentItem` : élément actuel, après la modification de la propriété.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) définit également un événement `PositionChanged` qui est déclenché lorsque la propriété `Position` change, en raison du défilement de l’utilisateur ou lorsqu’une application définit la propriété. L’objet `PositionChangedEventArgs` qui accompagne l’événement `PositionChanged` a deux propriétés, de type `int`:

- `PreviousPosition` : position précédente, après la modification de la propriété.
- `CurrentPosition` : position actuelle, après la modification de la propriété.

## <a name="respond-to-the-current-item-changing"></a>Répondre à la modification de l’élément actuel

Lorsque l’élément actuellement affiché change, la propriété `CurrentItem` est définie sur la valeur de l’élément. Lorsque cette propriété change, l' `CurrentItemChangedCommand` est exécutée avec la valeur de la `CurrentItemChangedCommandParameter` passée à la `ICommand`. La propriété `Position` est ensuite mise à jour et l’événement `CurrentItemChanged` se déclenche.

> [!IMPORTANT]
> La propriété `Position` change lorsque la propriété `CurrentItem` change. Cela entraînera l’exécution de la `PositionChangedCommand` et le déclenchement de l’événement `PositionChanged`.

### <a name="event"></a>Événement

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

Dans cet exemple, le gestionnaire d’événements `OnCurrentItemChanged` est exécuté lorsque l’événement `CurrentItemChanged` se déclenche :

```csharp
void OnCurrentItemChanged(object sender, CurrentItemChangedEventArgs e)
{
    Monkey previousItem = e.PreviousItem as Monkey;
    Monkey currentItem = e.CurrentItem as Monkey;
}
```

Dans cet exemple, le gestionnaire d’événements `OnCurrentItemChanged` expose les éléments précédents et actuels :

[![Capture d’écran d’un CarouselView avec des éléments précédents et actuels, sur iOS et Android](interaction-images/current-item-events.png "CarouselView avec les éléments actuels et précédents")](interaction-images/current-item-events-large.png#lightbox "CarouselView avec les éléments actuels et précédents")

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

### <a name="event"></a>Événement

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

Dans cet exemple, le gestionnaire d’événements `OnPositionChanged` est exécuté lorsque l’événement `PositionChanged` se déclenche :

```csharp
void OnPositionChanged(object sender, PositionChangedEventArgs e)
{
    int previousItemPosition = e.PreviousPosition;
    int currentItemPosition = e.CurrentPosition;
}
```

Dans cet exemple, le gestionnaire d’événements `OnCurrentItemChanged` expose les positions précédente et actuelle :

[![Capture d’écran d’un CarouselView avec positions précédentes et actuelles, sur iOS et Android](interaction-images/current-position-events.png "CarouselView avec positions actuelles et précédentes")](interaction-images/current-position-events-large.png#lightbox "CarouselView avec positions actuelles et précédentes")

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

Les données de la propriété `CarouselView.CurrentItem` sont liées à la propriété `CurrentItem` du modèle de vue connecté, qui est de type `Monkey`. Par défaut, une liaison de `TwoWay` est utilisée afin que, si l’utilisateur modifie l’élément actuel, la valeur de la propriété `CurrentItem` soit définie sur l’objet `Monkey` actuel. La propriété `CurrentItem` est définie dans la classe `MonkeysViewModel` :

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

Dans cet exemple, la propriété `CurrentItem` est définie sur le quatrième élément de la collection `Monkeys` :

[![Capture d’écran d’un CarouselView avec un élément prédéfini, sur iOS et Android](interaction-images/preset-item.png "CarouselView avec élément prédéfini")](interaction-images/preset-item-large.png#lightbox "CarouselView avec élément prédéfini")

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

Les données de la propriété `CarouselView.Position` sont liées à la propriété `Position` du modèle de vue connecté, qui est de type `int`. Par défaut, une liaison de `TwoWay` est utilisée afin que, si l’utilisateur fait défiler le [`CarouselView`](xref:Xamarin.Forms.CarouselView), la valeur de la propriété `Position` sera définie sur l’index de l’élément affiché. La propriété `Position` est définie dans la classe `MonkeysViewModel` :

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

Dans cet exemple, la propriété `Position` est définie sur le quatrième élément de la collection `Monkeys` :

[![Capture d’écran d’un CarouselView avec la position prédéfinie, sur iOS et Android](interaction-images/preset-position.png "CarouselView avec la position prédéfinie")](interaction-images/preset-position-large.png#lightbox "CarouselView avec la position prédéfinie")

## <a name="define-visual-states"></a>Définir les États visuels

[`CarouselView`](xref:Xamarin.Forms.CarouselView) définit quatre états visuels :

- `CurrentItem` représente l’état visuel de l’élément actuellement affiché.
- `PreviousItem` représente l’état visuel de l’élément précédemment affiché.
- `NextItem` représente l’état visuel de l’élément suivant.
- `DefaultItem` représente l’état visuel pour le reste des éléments.

Ces états visuels peuvent être utilisés pour initier des modifications visuelles sur les éléments affichés par l' [`CarouselView`](xref:Xamarin.Forms.CarouselView).

L’exemple de code XAML suivant montre comment définir les États visuels `CurrentItem`, `PreviousItem`, `NextItem`et `DefaultItem` :

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              PeekAreaInsets="100">
    <CarouselView.ItemTemplate>
        <DataTemplate>
            <StackLayout>
                <VisualStateManager.VisualStateGroups>
                    <VisualStateGroup x:Name="CommonStates">
                        <VisualState x:Name="CurrentItem">
                            <VisualState.Setters>
                                <Setter Property="Scale"
                                        Value="1.1" />
                            </VisualState.Setters>
                        </VisualState>
                        <VisualState x:Name="PreviousItem">
                            <VisualState.Setters>
                                <Setter Property="Opacity"
                                        Value="0.5" />
                            </VisualState.Setters>
                        </VisualState>
                        <VisualState x:Name="NextItem">
                            <VisualState.Setters>
                                <Setter Property="Opacity"
                                        Value="0.5" />
                            </VisualState.Setters>
                        </VisualState>
                        <VisualState x:Name="DefaultItem">
                            <VisualState.Setters>
                                <Setter Property="Opacity"
                                        Value="0.25" />
                            </VisualState.Setters>
                        </VisualState>
                    </VisualStateGroup>
                </VisualStateManager.VisualStateGroups>

                <!-- Item template content -->
                <Frame HasShadow="true">
                    ...
                </Frame>
            </StackLayout>
        </DataTemplate>
    </CarouselView.ItemTemplate>
</CarouselView>
```

Dans cet exemple, l’état visuel `CurrentItem` spécifie que la valeur par défaut de la propriété de l’élément actuel affiché par le [`CarouselView`](xref:Xamarin.Forms.CarouselView) [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) sera modifiée de 1 à 1,1. Les États visuels `PreviousItem` et `NextItem` spécifient que les éléments qui entourent l’élément actuel seront affichés avec une valeur [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) de 0,5. L’état visuel de la `DefaultItem` spécifie que le reste des éléments affichés par le `CarouselView` sera affiché avec une valeur `Opacity` de 0,25.

> [!NOTE]
> En guise d’alternative, les États visuels peuvent être définis dans un [`Style`](xref:Xamarin.Forms.Style) qui a une valeur de propriété [`TargetType`](xref:Xamarin.Forms.Style.TargetType) qui est le type de l’élément racine de l' [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), qui est défini en tant que valeur de propriété `ItemTemplate`.

Les captures d’écran suivantes montrent les États visuels `CurrentItem`, `PreviousItem`et `NextItem` :

[![Capture d’écran d’un CarouselView utilisant des États visuels, sur iOS et Android](interaction-images/visual-states.png "CarouselView les États visuels")](interaction-images/visual-states-large.png#lightbox "CarouselView les États visuels")

Pour plus d’informations sur les États visuels, consultez [Xamarin. Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="clear-the-current-item"></a>Effacer l’élément actuel

La propriété `CurrentItem` peut être effacée en la définissant, ou l’objet auquel elle est liée, pour `null`.

## <a name="disable-bounce"></a>Désactiver le rebond

Par défaut, [`CarouselView`](xref:Xamarin.Forms.CarouselView) rebondit les éléments aux limites du contenu. Vous pouvez désactiver cette option en affectant à la propriété `IsBounceEnabled` la valeur `false`.

## <a name="disable-swipe-interaction"></a>Désactiver l’interaction de balayage

Par défaut, [`CarouselView`](xref:Xamarin.Forms.CarouselView) permet aux utilisateurs de se déplacer entre les éléments à l’aide d’un mouvement de balayage. Cette interaction de balayage peut être désactivée en affectant à la propriété `IsSwipeEnabled` la valeur `false`.

## <a name="related-links"></a>Liens connexes

- [CarouselView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
- [Gestionnaire d’état visuel Xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md)
