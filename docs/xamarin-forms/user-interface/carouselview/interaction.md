---
title: Xamarin.Forms Interaction CarouselView
description: L’élément actuellement affiché dans un CarouselView est accessible via les propriétés CurrentItem et position.
ms.prod: xamarin
ms.assetid: 854D97E5-D119-4BE2-AE7C-BD428792C992
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d8187c82033a872752a314b03950793cad4ac0d1
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97939158"
---
# <a name="no-locxamarinforms-carouselview-interaction"></a>Xamarin.Forms Interaction CarouselView

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView) définit les propriétés suivantes qui contrôlent l’interaction de l’utilisateur :

- `CurrentItem`, de type `object` , l’élément en cours d’affichage. Cette propriété a un mode de liaison par défaut de `TwoWay` et a une `null` valeur lorsqu’il n’y a aucune donnée à afficher.
- `CurrentItemChangedCommand`, de type `ICommand` , qui est exécuté lorsque l’élément actuel change.
- `CurrentItemChangedCommandParameter`, de type `object` : paramètre passé à la commande `CurrentItemChangedCommand`.
- `IsBounceEnabled`, de type `bool` , qui spécifie si le `CarouselView` rebondit au niveau d’une limite de contenu. La valeur par défaut est `true`.
- `IsSwipeEnabled`, de type `bool` , qui détermine si un mouvement de balayage va modifier l’élément affiché. La valeur par défaut est `true`.
- `Loop`, de type `bool` , qui détermine si le `CarouselView` fournit un accès en boucle à sa collection d’éléments. La valeur par défaut est `true`.
- `Position`, de type `int` , l’index de l’élément actuel dans la collection sous-jacente. Cette propriété a un mode de liaison par défaut de `TwoWay` et a une valeur 0 lorsqu’il n’y a aucune donnée à afficher.
- `PositionChangedCommand`, de type `ICommand` , qui est exécuté lorsque la position change.
- `PositionChangedCommandParameter`, de type `object` : paramètre passé à la commande `PositionChangedCommand`.
- `VisibleViews`, de type `ObservableCollection<View>` , qui est une propriété en lecture seule qui contient les objets pour les éléments qui sont actuellement visibles.

Toutes ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie que les propriétés peuvent être des cibles de liaisons de données.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) définit un `CurrentItemChanged` événement qui est déclenché lorsque la `CurrentItem` propriété change, en raison du défilement de l’utilisateur ou lorsqu’une application définit la propriété. L' `CurrentItemChangedEventArgs` objet qui accompagne l' `CurrentItemChanged` événement a deux propriétés, de type `object` :

- `PreviousItem` : élément précédent, après la modification de la propriété.
- `CurrentItem` : élément actuel, après la modification de la propriété.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) définit également un `PositionChanged` événement qui est déclenché lorsque la `Position` propriété change, en raison du défilement de l’utilisateur ou lorsqu’une application définit la propriété. L' `PositionChangedEventArgs` objet qui accompagne l' `PositionChanged` événement a deux propriétés, de type `int` :

- `PreviousPosition` : position précédente, après la modification de la propriété.
- `CurrentPosition` : position actuelle, après la modification de la propriété.

## <a name="respond-to-the-current-item-changing"></a>Répondre à la modification de l’élément actuel

Lorsque l’élément actuellement affiché change, la `CurrentItem` propriété est définie sur la valeur de l’élément. Lorsque cette propriété change, le `CurrentItemChangedCommand` est exécuté avec la valeur du `CurrentItemChangedCommandParameter` passé à l' `ICommand` . La `Position` propriété est ensuite mise à jour et l' `CurrentItemChanged` événement se déclenche.

> [!IMPORTANT]
> La `Position` propriété change lorsque la `CurrentItem` propriété change. Cela entraînera l' `PositionChangedCommand` exécution du et le déclenchement de `PositionChanged` l’événement.

### <a name="event"></a>Événement

L’exemple de code XAML suivant montre un [`CarouselView`](xref:Xamarin.Forms.CarouselView) qui utilise un gestionnaire d’événements pour répondre à la modification de l’élément actuel :

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

Dans cet exemple, le `OnCurrentItemChanged` Gestionnaire d’événements est exécuté lorsque l' `CurrentItemChanged` événement se déclenche :

```csharp
void OnCurrentItemChanged(object sender, CurrentItemChangedEventArgs e)
{
    Monkey previousItem = e.PreviousItem as Monkey;
    Monkey currentItem = e.CurrentItem as Monkey;
}
```

Dans cet exemple, le `OnCurrentItemChanged` Gestionnaire d’événements expose les éléments précédents et actuels :

[![Capture d’écran d’un CarouselView avec des éléments précédents et actuels, sur iOS et Android](interaction-images/current-item-events.png "CarouselView avec les éléments actuels et précédents")](interaction-images/current-item-events-large.png#lightbox "CarouselView avec les éléments actuels et précédents")

### <a name="command"></a>Commande

L’exemple de code XAML suivant montre un [`CarouselView`](xref:Xamarin.Forms.CarouselView) qui utilise une commande pour répondre à la modification de l’élément actuel :

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

Dans cet exemple, la `CurrentItemChangedCommand` propriété est liée à la `ItemChangedCommand` propriété, en lui passant la `CurrentItem` valeur de la propriété comme argument. `ItemChangedCommand`Peut ensuite répondre à la modification de l’élément actuel, selon les besoins :

```csharp
public ICommand ItemChangedCommand => new Command<Monkey>((item) =>
{
    PreviousMonkey = CurrentMonkey;
    CurrentMonkey = item;
});
```

Dans cet exemple, `ItemChangedCommand` met à jour les objets qui stockent les éléments précédents et actuels.

## <a name="respond-to-the-position-changing"></a>Répondre au changement de position

Lorsque l’élément actuellement affiché change, la `Position` propriété est définie sur l’index de l’élément actuel dans la collection sous-jacente. Lorsque cette propriété change, le `PositionChangedCommand` est exécuté avec la valeur du `PositionChangedCommandParameter` passé à l' `ICommand` . L' `PositionChanged` événement se déclenche ensuite. Si la `Position` propriété a été modifiée par programme, le [`CarouselView`](xref:Xamarin.Forms.CarouselView) défilera vers l’élément qui correspond à la `Position` valeur.

> [!NOTE]
> L’affectation `Position` de la valeur 0 à la propriété entraîne l’affichage du premier élément de la collection sous-jacente.

### <a name="event"></a>Événement

L’exemple de code XAML suivant montre un [`CarouselView`](xref:Xamarin.Forms.CarouselView) qui utilise un gestionnaire d’événements pour répondre à la modification de la `Position` propriété :

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

Dans cet exemple, le `OnPositionChanged` Gestionnaire d’événements est exécuté lorsque l' `PositionChanged` événement se déclenche :

```csharp
void OnPositionChanged(object sender, PositionChangedEventArgs e)
{
    int previousItemPosition = e.PreviousPosition;
    int currentItemPosition = e.CurrentPosition;
}
```

Dans cet exemple, le `OnCurrentItemChanged` Gestionnaire d’événements expose les positions précédente et actuelle :

[![Capture d’écran d’un CarouselView avec positions précédentes et actuelles, sur iOS et Android](interaction-images/current-position-events.png "CarouselView avec positions actuelles et précédentes")](interaction-images/current-position-events-large.png#lightbox "CarouselView avec positions actuelles et précédentes")

### <a name="command"></a>Commande

L’exemple de code XAML suivant montre un [`CarouselView`](xref:Xamarin.Forms.CarouselView) qui utilise une commande pour répondre à la modification de la `Position` propriété :

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

Dans cet exemple, la `PositionChangedCommand` propriété est liée à la `PositionChangedCommand` propriété, en lui passant la `Position` valeur de la propriété comme argument. Le `PositionChangedCommand` peut ensuite répondre à la modification de la position, selon les besoins :

```csharp
public ICommand PositionChangedCommand => new Command<int>((position) =>
{
    PreviousPosition = CurrentPosition;
    CurrentPosition = position;
});
```

Dans cet exemple, `PositionChangedCommand` met à jour les objets qui stockent les positions précédentes et actuelles.

## <a name="preset-the-current-item"></a>Prédéfinir l’élément actuel

L’élément actuel dans un [`CarouselView`](xref:Xamarin.Forms.CarouselView) peut être défini par programmation en affectant `CurrentItem` à la propriété l’élément. L’exemple de code XAML suivant montre un `CarouselView` qui préchoisit l’élément actuel :

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
> La `CurrentItem` propriété a un mode de liaison par défaut de `TwoWay` .

Les `CarouselView.CurrentItem` données de propriété sont liées à la `CurrentItem` propriété du modèle de vue connectée, qui est de type `Monkey` . Par défaut, une `TwoWay` liaison est utilisée de sorte que si l’utilisateur modifie l’élément actuel, la valeur de la `CurrentItem` propriété est définie sur l' `Monkey` objet actuel. La `CurrentItem` propriété est définie dans la `MonkeysViewModel` classe :

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

Dans cet exemple, la `CurrentItem` propriété est définie sur le quatrième élément de la `Monkeys` collection :

[![Capture d’écran d’un CarouselView avec un élément prédéfini, sur iOS et Android](interaction-images/preset-item.png "CarouselView avec élément prédéfini")](interaction-images/preset-item-large.png#lightbox "CarouselView avec élément prédéfini")

## <a name="preset-the-position"></a>Prédéfinir la position

L’élément affiché [`CarouselView`](xref:Xamarin.Forms.CarouselView) peut être défini par programmation en affectant `Position` à la propriété l’index de l’élément dans la collection sous-jacente. L’exemple de code XAML suivant montre un `CarouselView` qui définit l’élément affiché :

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
> La `Position` propriété a un mode de liaison par défaut de `TwoWay` .

Les `CarouselView.Position` données de propriété sont liées à la `Position` propriété du modèle de vue connectée, qui est de type `int` . Par défaut, une `TwoWay` liaison est utilisée de sorte que si l’utilisateur fait défiler le [`CarouselView`](xref:Xamarin.Forms.CarouselView) , la valeur de la `Position` propriété est définie sur l’index de l’élément affiché. La `Position` propriété est définie dans la `MonkeysViewModel` classe :

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

Dans cet exemple, la `Position` propriété est définie sur le quatrième élément de la `Monkeys` collection :

[![Capture d’écran d’un CarouselView avec la position prédéfinie, sur iOS et Android](interaction-images/preset-position.png "CarouselView avec la position prédéfinie")](interaction-images/preset-position-large.png#lightbox "CarouselView avec la position prédéfinie")

## <a name="define-visual-states"></a>Définir les États visuels

[`CarouselView`](xref:Xamarin.Forms.CarouselView) définit quatre états visuels :

- `CurrentItem` représente l’état visuel de l’élément actuellement affiché.
- `PreviousItem` représente l’état visuel de l’élément précédemment affiché.
- `NextItem` représente l’état visuel de l’élément suivant.
- `DefaultItem` représente l’état visuel pour le reste des éléments.

Ces états visuels peuvent être utilisés pour initier des modifications visuelles sur les éléments affichés par le [`CarouselView`](xref:Xamarin.Forms.CarouselView) .

L’exemple de code XAML suivant montre comment définir `CurrentItem` les `PreviousItem` `NextItem` États visuels,, et `DefaultItem` :

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

Dans cet exemple, l' `CurrentItem` état visuel spécifie que la propriété de l’élément actuel affiché par le [`CarouselView`](xref:Xamarin.Forms.CarouselView) sera [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) remplacée par sa valeur par défaut de 1 à 1,1. Les `PreviousItem` `NextItem` États visuels et spécifient que les éléments qui entourent l’élément actuel seront affichés avec une [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) valeur de 0,5. L' `DefaultItem` état visuel spécifie que le reste des éléments affichés par le `CarouselView` sera affiché avec une `Opacity` valeur de 0,25.

> [!NOTE]
> En guise d’alternative, les États visuels peuvent être définis dans un [`Style`](xref:Xamarin.Forms.Style) qui a une [`TargetType`](xref:Xamarin.Forms.Style.TargetType) valeur de propriété qui est le type de l’élément racine de [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , qui est défini en tant que `ItemTemplate` valeur de propriété.

Les captures d’écran suivantes montrent les `CurrentItem` `PreviousItem` `NextItem` États visuels, et :

[![Capture d’écran d’un CarouselView utilisant des États visuels, sur iOS et Android](interaction-images/visual-states.png "CarouselView les États visuels")](interaction-images/visual-states-large.png#lightbox "CarouselView les États visuels")

Pour plus d’informations sur les États visuels, consultez [ Xamarin.Forms Gestionnaire d’état visuel](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="clear-the-current-item"></a>Effacer l’élément actuel

La `CurrentItem` propriété peut être effacée en la définissant, ou l’objet auquel elle est liée, à `null` .

## <a name="disable-bounce"></a>Désactiver le rebond

Par défaut, [`CarouselView`](xref:Xamarin.Forms.CarouselView) rebondit les éléments aux limites du contenu. Vous pouvez désactiver cette option en affectant à la propriété la valeur `IsBounceEnabled` `false` .

## <a name="disable-loop"></a>Désactiver la boucle

Par défaut, [`CarouselView`](xref:Xamarin.Forms.CarouselView) fournit un accès en boucle à sa collection d’éléments. Par conséquent, le balayage vers l’arrière à partir du premier élément de la collection affiche le dernier élément de la collection. De même, le balayage par progression à partir du dernier élément de la collection retourne au premier élément de la collection. Ce comportement peut être désactivé en affectant `Loop` à la propriété la valeur `false` .

## <a name="disable-swipe-interaction"></a>Désactiver l’interaction de balayage

Par défaut, [`CarouselView`](xref:Xamarin.Forms.CarouselView) permet aux utilisateurs de se déplacer entre les éléments à l’aide d’un mouvement de balayage. Cette interaction de balayage peut être désactivée en affectant à la propriété la valeur `IsSwipeEnabled` `false` .

## <a name="related-links"></a>Liens connexes

- [CarouselView (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
- [Xamarin.Forms Gestionnaire d’état visuel](~/xamarin-forms/user-interface/visual-state-manager.md)