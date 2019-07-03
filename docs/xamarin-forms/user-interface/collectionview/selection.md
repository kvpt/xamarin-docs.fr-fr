---
title: Sélection de CollectionView de Xamarin.Forms
description: Par défaut, la sélection de CollectionView est désactivée. Toutefois, la sélection unique et multiples peut être activée.
ms.prod: xamarin
ms.assetid: 423D91C7-1E58-4735-9E80-58F11CDFD953
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: dc01cf6bea9fe614cbfb53dcc4417ffb0e602c6f
ms.sourcegitcommit: 0fd04ea3af7d6a6d6086525306523a5296eec0df
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67512759"
---
# <a name="xamarinforms-collectionview-selection"></a>Sélection de CollectionView de Xamarin.Forms

![](~/media/shared/preview.png "Cette API est actuellement en préversion")

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CollectionViewDemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) définit les propriétés suivantes qui contrôlent la sélection d’éléments :

- [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode), de type [ `SelectionMode` ](xref:Xamarin.Forms.SelectionMode), le mode de sélection.
- [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem), de type `object`, l’élément sélectionné dans la liste. Cette propriété a un mode de liaison par défaut de `TwoWay`et a une `null` valeur lorsque aucun élément n’est sélectionné.
- [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems), de type `IList<object>`, les éléments sélectionnés dans la liste. Cette propriété a un mode de liaison par défaut de `OneWay`et a une `null` valeur lorsque aucun élément n’est sélectionnées.
- [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand), de type `ICommand`, qui est exécuté lorsque l’élément sélectionné change.
- [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter), de type `object`, qui est le paramètre qui est passé à la `SelectionChangedCommand`.

Toutes ces propriétés s’appuient sur des objets [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), ce qui signifie qu’elles peuvent être des cibles de liaisons de données.

Par défaut, [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) sélection est désactivée. Toutefois, ce comportement peut être modifié en définissant le [ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) valeur de propriété à une de le [ `SelectionMode` ](xref:Xamarin.Forms.SelectionMode) membres de l’énumération :

- `None` – Indique que les éléments ne peuvent pas être sélectionnés. Valeur par défaut.
- `Single` : indique qu’un seul élément peut être sélectionné avec l’élément sélectionné est mis en surbrillance.
- `Multiple` – Indique que plusieurs éléments peuvent être sélectionnés, avec les éléments sélectionnés en cours de mise en surbrillance.

[`CollectionView`](xref:Xamarin.Forms.CollectionView) définit un [ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) événement est déclenché quand le [ `SelectedItem` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) modifications apportées aux propriétés, soit parce que l’utilisateur en sélectionnant un élément dans la liste, ou lorsqu’une application définit la propriété. En outre, cet événement est également déclenché quand la [ `SelectedItems` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) les modifications de propriété. Le [ `SelectionChangedEventArgs` ](xref:Xamarin.Forms.SelectionChangedEventArgs) objet qui accompagne le `SelectionChanged` événement a deux propriétés, tous deux de type `IReadOnlyList<object>`:

- `PreviousSelection` – la liste des éléments qui ont été sélectionnés, avant la modification de la sélection.
- `CurrentSelection` – la liste des éléments qui sont sélectionnés, après le changement de sélection.

## <a name="single-selection"></a>Sélection unique

Lorsque le [ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriété est définie sur `Single`, un élément unique dans le [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) peuvent être sélectionnés. Lorsqu’un élément est sélectionné, le [ `SelectedItem` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propriété est définie à la valeur de l’élément sélectionné. Lorsque cette propriété change, le [ `SelectionChangedCommand` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand) est exécutée (avec la valeur de la [ `SelectionChangedCommandParameter` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter) passé à la `ICommand`) et le [ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) événement est déclenché.

L’exemple XAML suivant montre un [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) qui peut répondre à la sélection d’élément unique :

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                SelectionMode="Single"
                SelectionChanged="OnCollectionViewSelectionChanged">
    ...
</CollectionView>
```

Le code C# équivalent est :

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Single
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SelectionChanged += OnCollectionViewSelectionChanged;
```

Dans cet exemple de code, le `OnCollectionViewSelectionChanged` Gestionnaire d’événements est exécuté lorsque la [ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) se déclenche des événements, avec le Gestionnaire d’événements extrait l’élément précédemment sélectionné et l’élément actuellement sélectionné :

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    string previous = (e.PreviousSelection.FirstOrDefault() as Monkey)?.Name;
    string current = (e.CurrentSelection.FirstOrDefault() as Monkey)?.Name;
    ...
}
```

> [!IMPORTANT]
> Le [ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) événement peut être déclenché par les modifications qui se produisent après le passage du [ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriété.

Les captures d’écran suivantes montrent la sélection d’élément unique dans un [ `CollectionView` ](xref:Xamarin.Forms.CollectionView):

[![Capture d’écran de liste verticale CollectionView avec une sélection unique, sur iOS et Android](selection-images/single-selection.png "CollectionView de liste verticale avec une sélection unique")](selection-images/single-selection-large.png#lightbox "CollectionView de liste verticale avec unique sélection")

## <a name="multiple-selection"></a>Sélection multiple

Lorsque le [ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriété est définie sur `Multiple`, plusieurs éléments dans le [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) peuvent être sélectionnés. Lorsque les éléments sont sélectionnés, le [ `SelectedItems` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) propriété sera définie pour les éléments sélectionnés. Lorsque cette propriété change, le [ `SelectionChangedCommand` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand) est exécutée (avec la valeur de la [ `SelectionChangedCommandParameter` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter) passé à la `ICommand`) et le [ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) événement est déclenché.

L’exemple XAML suivant montre un [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) qui peut répondre à la sélection de plusieurs éléments :

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                SelectionMode="Multiple"
                SelectionChanged="OnCollectionViewSelectionChanged">
    ...
</CollectionView>
```

Le code C# équivalent est :

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Multiple
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SelectionChanged += OnCollectionViewSelectionChanged;
```

Dans cet exemple de code, le `OnCollectionViewSelectionChanged` Gestionnaire d’événements est exécuté lorsque la [ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) se déclenche des événements, avec le Gestionnaire d’événements extrait les éléments précédemment sélectionnés et les éléments actuellement sélectionnés :

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    var previous = e.PreviousSelection;
    var current = e.CurrentSelection;
    ...
}
```

> [!IMPORTANT]
> Le [ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) événement peut être déclenché par les modifications qui se produisent après le passage du [ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriété.

Les captures d’écran suivantes montrent la sélection de plusieurs éléments dans un [ `CollectionView` ](xref:Xamarin.Forms.CollectionView):

[![Capture d’écran d’une liste verticale CollectionView avec la sélection multiple, sur iOS et Android](selection-images/multiple-selection.png "CollectionView de liste verticale avec la sélection multiple")](selection-images/multiple-selection-large.png#lightbox "CollectionView de liste verticale avec sélection multiple")

## <a name="single-pre-selection"></a>Présélection unique

Lorsque le [ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriété est définie sur `Single`, un élément unique dans le [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) peut être présélectionnée en définissant le [ `SelectedItem` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propriété à l’élément. L’exemple XAML suivant montre un `CollectionView` qui présélectionne un seul élément :

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                SelectionMode="Single"
                SelectedItem="{Binding SelectedMonkey}">
    ...
</CollectionView>
```

Le code C# équivalent est :

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Single
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SetBinding(SelectableItemsView.SelectedItemProperty, "SelectedMonkey");
```

> [!NOTE]
> Le [ `SelectedItem` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propriété a un mode de liaison par défaut de `TwoWay`.

Le [ `SelectedItem` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) lie les données de propriété pour le `SelectedMonkey` propriété du modèle de vue connectée, ce qui est de type `Monkey`. Par défaut, un `TwoWay` liaison est utilisée afin qu’en cas l’utilisateur modifie l’élément sélectionné, la valeur de la `SelectedMonkey` propriété sera définie au `Monkey` objet. Le `SelectedMonkey` propriété est définie dans le `MonkeysViewModel` classe et est défini sur le quatrième élément de la `Monkeys` collection :

```csharp
public class MonkeysViewModel : INotifyPropertyChanged
{
    ...
    public ObservableCollection<Monkey> Monkeys { get; private set; }

    Monkey selectedMonkey;
    public Monkey SelectedMonkey
    {
        get
        {
            return selectedMonkey;
        }
        set
        {
            if (selectedMonkey != value)
            {
                selectedMonkey = value;
            }
        }
    }

    public MonkeysViewModel()
    {
        ...
        selectedMonkey = Monkeys.Skip(3).FirstOrDefault();
    }
    ...
}
```

Par conséquent, lorsque le [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) s’affiche, le quatrième élément dans la liste est sélectionné au préalable :

[![Capture d’écran d’une liste verticale CollectionView avec présélection unique, sur iOS et Android](selection-images/single-pre-selection.png "CollectionView de liste verticale avec présélection unique")](selection-images/single-pre-selection-large.png#lightbox "liste verticale CollectionView avec la présélection unique")

## <a name="multiple-pre-selection"></a>Avant la sélection multiple

Lorsque le [ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriété est définie sur `Multiple`, plusieurs éléments dans le [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) peut être présélectionnée. L’exemple XAML suivant montre un `CollectionView` qui permettra la présélection de plusieurs éléments :

```xaml
<CollectionView x:Name="collectionView"
                ItemsSource="{Binding Monkeys}"
                SelectionMode="Multiple"
                SelectedItems="{Binding SelectedMonkeys}">
    ...
</CollectionView>
```

Le code C# équivalent est :

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Multiple
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SetBinding(SelectableItemsView.SelectedItemsProperty, "SelectedMonkeys");
```

> [!NOTE]
> Le [ `SelectedItems` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) propriété a un mode de liaison par défaut de `OneWay`.

Le [ `SelectedItems` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) lie les données de propriété pour le `SelectedMonkeys` propriété du modèle de vue connectée, ce qui est de type `ObservableCollection<object>`. Le `SelectedMonkeys` propriété est définie dans le `MonkeysViewModel` classe et est défini sur la seconde, la quatrième et cinquième éléments dans le `Monkeys` collection :

```csharp
namespace CollectionViewDemos.ViewModels
{
    public class MonkeysViewModel : INotifyPropertyChanged
    {
        ...
        ObservableCollection<object> selectedMonkeys;
        public ObservableCollection<object> SelectedMonkeys
        {
            get
            {
                return selectedMonkeys;
            }
            set
            {
                if (selectedMonkeys != value)
                {
                    selectedMonkeys = value;
                }
            }
        }

        public MonkeysViewModel()
        {
            ...
            SelectedMonkeys = new ObservableCollection<object>()
            {
                Monkeys[1], Monkeys[3], Monkeys[4]
            };
        }
        ...
    }
}
```

Par conséquent, lorsque le [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) s’affiche, la deuxième, quatrième et cinquième éléments dans la liste sont présélectionnées :

[![Capture d’écran d’une liste verticale CollectionView avec plusieurs présélection sur iOS et Android](selection-images/multiple-pre-selection.png "CollectionView de liste verticale avec plusieurs présélection")](selection-images/multiple-pre-selection-large.png#lightbox "CollectionView vertical liste avec plusieurs présélection")

## <a name="clearing-selections"></a>Effacer les sélections

Le [ `SelectedItem` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) et [ `SelectedItems` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) propriétés peuvent être désactivées en définissant, ou que les objets qu’ils lient, à `null`.

## <a name="change-selected-item-color"></a>Modifier la couleur de l’élément sélectionné

[`CollectionView`](xref:Xamarin.Forms.CollectionView) a un `Selected` [ `VisualState` ](xref:Xamarin.Forms.VisualState) qui peut être utilisé pour lancer un plus grand changement pour l’élément sélectionné dans le `CollectionView`. Cas d’usage commun pour ce `VisualState` consiste à modifier la couleur d’arrière-plan de l’élément sélectionné, ce qui est illustré dans l’exemple XAML suivant :

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <Style TargetType="Grid">
            <Setter Property="VisualStateManager.VisualStateGroups">
                <VisualStateGroupList>
                    <VisualStateGroup x:Name="CommonStates">
                        <VisualState x:Name="Normal" />
                        <VisualState x:Name="Selected">
                            <VisualState.Setters>
                                <Setter Property="BackgroundColor"
                                        Value="LightSkyBlue" />
                            </VisualState.Setters>
                        </VisualState>
                    </VisualStateGroup>
                </VisualStateGroupList>
            </Setter>
        </Style>
    </ContentPage.Resources>
    <StackLayout Margin="20">
        <CollectionView ItemsSource="{Binding Monkeys}"
                        SelectionMode="Single">
            <CollectionView.ItemTemplate>
                <DataTemplate>
                    <Grid Padding="10">
                        ...
                    </Grid>
                </DataTemplate>
            </CollectionView.ItemTemplate>
        </CollectionView>
    </StackLayout>
</ContentPage>
```

> [!IMPORTANT]
> Le [ `Style` ](xref:Xamarin.Forms.Style) qui contient le `Selected` `VisualState` doit avoir un [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) valeur de propriété qui est le type de l’élément racine de la [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate), qui est définie comme étant le `ItemTemplate` valeur de propriété.

Dans cet exemple, le [ `Style.TargetType` ](xref:Xamarin.Forms.Style.TargetType) propriété a la valeur `Grid` , car l’élément racine de la [ `ItemTemplate` ](xref:Xamarin.Forms.ItemsView.ItemTemplate) est un [ `Grid` ](xref:Xamarin.Forms.Grid). Le `Selected` [ `VisualState` ](xref:Xamarin.Forms.VisualState) Spécifie que lorsqu’un élément dans le [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) est sélectionnée, le [ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor) de l’élément a la valeur `LightSkyBlue`:

[![Capture d’écran de liste verticale CollectionView avec une couleur de sélection unique personnalisée sur iOS et Android](selection-images/single-selection-color.png "CollectionView de liste verticale avec une couleur de sélection unique personnalisée") ] (selection-images/single-selection-color-large.png#lightbox " Liste verticale de CollectionView avec une couleur de sélection unique personnalisée")

Pour plus d’informations sur les états visuels, consultez [Gestionnaire d’état visuel Xamarin.Forms](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="disable-selection"></a>Désactiver la sélection

[`CollectionView`](xref:Xamarin.Forms.CollectionView) sélection est désactivée par défaut. Toutefois, si un `CollectionView` a sélection activée, il peut être désactivé en définissant le [ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriété `None`:

```xaml
<CollectionView ...
                SelectionMode="None" />
```

Le code C# équivalent est :

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    SelectionMode = SelectionMode.None
};
```

Lorsque le [ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriété est définie sur `None`, les éléments dans le [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) ne peut pas être sélectionné, le [ `SelectedItem` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propriété dont la valeur rester `null`et le [ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) événement ne sera pas déclenché.

> [!NOTE]
> Lorsqu’un élément a été sélectionné et le [ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriété est modifiée à partir de `Single` à `None`, le [ `SelectedItem` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propriété sera définie `null` et le [ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) événement est déclenché avec vide `CurrentSelection` propriété.

## <a name="related-links"></a>Liens connexes

- [CollectionView (exemple)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CollectionViewDemos/)
- [Gestionnaire d’état visuel de Xamarin.Forms](~/xamarin-forms/user-interface/visual-state-manager.md)
