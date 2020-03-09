---
title: Xamarin. Forms CollectionView, sélection
description: Par défaut, la sélection CollectionView est désactivée. Toutefois, une sélection unique et une sélection multiple peuvent être activées.
ms.prod: xamarin
ms.assetid: 423D91C7-1E58-4735-9E80-58F11CDFD953
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: a4cc237ef738edeccf66f1a91a010e4831c1c72f
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78918615"
---
# <a name="xamarinforms-collectionview-selection"></a>Xamarin. Forms CollectionView, sélection

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) définit les propriétés suivantes qui contrôlent la sélection des éléments :

- [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode), de type [`SelectionMode`](xref:Xamarin.Forms.SelectionMode), le mode de sélection.
- [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem), de type `object`, l’élément sélectionné dans la liste. Cette propriété a un mode de liaison par défaut de `TwoWay`, et a une valeur de `null` quand aucun élément n’est sélectionné.
- [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems), de type `IList<object>`, les éléments sélectionnés dans la liste. Cette propriété a un mode de liaison par défaut de `OneWay`et a une valeur de `null` quand aucun élément n’est sélectionné.
- [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand), de type `ICommand`, qui est exécuté lorsque l’élément sélectionné change.
- [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter), de type `object`, qui est le paramètre passé à la `SelectionChangedCommand`.

Toutes ces propriétés sont soutenues par des objets [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), ce qui signifie qu’elles peuvent être des cibles de liaisons de données.

Par défaut, [`CollectionView`](xref:Xamarin.Forms.CollectionView) sélection est désactivée. Toutefois, ce comportement peut être modifié en définissant la valeur de la propriété [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) sur l’un des membres de l’énumération [`SelectionMode`](xref:Xamarin.Forms.SelectionMode) :

- `None` : indique que les éléments ne peuvent pas être sélectionnés. Il s’agit de la valeur par défaut.
- `Single` : indique qu’un seul élément peut être sélectionné, avec l’élément sélectionné mis en surbrillance.
- `Multiple` : indique que plusieurs éléments peuvent être sélectionnés et que les éléments sélectionnés sont mis en surbrillance.

[`CollectionView`](xref:Xamarin.Forms.CollectionView) définit un événement [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) qui est déclenché lorsque la propriété [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) change, soit parce que l’utilisateur a sélectionné un élément dans la liste, soit lorsqu’une application définit la propriété. En outre, cet événement est également déclenché lorsque la propriété [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) change. L’objet [`SelectionChangedEventArgs`](xref:Xamarin.Forms.SelectionChangedEventArgs) qui accompagne l’événement `SelectionChanged` a deux propriétés, de type `IReadOnlyList<object>`:

- `PreviousSelection` : liste des éléments qui ont été sélectionnés avant la modification de la sélection.
- `CurrentSelection` : liste des éléments sélectionnés, après la modification de la sélection.

En outre, [`CollectionView`](xref:Xamarin.Forms.CollectionView) a une méthode `UpdateSelectedItems` qui met à jour la propriété [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) avec une liste d’éléments sélectionnés, tout en déclenchant une seule notification de modification.

## <a name="single-selection"></a>Sélection unique

Lorsque la propriété [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) est définie sur `Single`, vous pouvez sélectionner un seul élément dans le [`CollectionView`](xref:Xamarin.Forms.CollectionView) . Lorsqu’un élément est sélectionné, la propriété [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) est définie sur la valeur de l’élément sélectionné. Lorsque cette propriété change, l' [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand) est exécutée (avec la valeur de la [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter) passée à la `ICommand`) et l’événement [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) se déclenche.

L’exemple de code XAML suivant montre une [`CollectionView`](xref:Xamarin.Forms.CollectionView) qui peut répondre à la sélection d’un seul élément :

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

Dans cet exemple de code, le gestionnaire d’événements `OnCollectionViewSelectionChanged` est exécuté lorsque l’événement [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) se déclenche, avec le gestionnaire d’événements qui récupère l’élément sélectionné précédemment et l’élément actuellement sélectionné :

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    string previous = (e.PreviousSelection.FirstOrDefault() as Monkey)?.Name;
    string current = (e.CurrentSelection.FirstOrDefault() as Monkey)?.Name;
    ...
}
```

> [!IMPORTANT]
> L’événement [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) peut être déclenché par des modifications qui se produisent suite à la modification de la propriété [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) .

Les captures d’écran suivantes montrent la sélection d’un élément unique dans un [`CollectionView`](xref:Xamarin.Forms.CollectionView):

[![Capture d’écran d’une liste verticale CollectionView avec une sélection unique, sur iOS et Android](selection-images/single-selection.png "CollectionView liste verticale avec une seule sélection")](selection-images/single-selection-large.png#lightbox "CollectionView liste verticale avec une seule sélection")

## <a name="multiple-selection"></a>Sélection multiple

Lorsque la propriété [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) est définie sur `Multiple`, plusieurs éléments de la [`CollectionView`](xref:Xamarin.Forms.CollectionView) peuvent être sélectionnés. Lorsque les éléments sont sélectionnés, la propriété [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) est définie sur les éléments sélectionnés. Lorsque cette propriété change, l' [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand) est exécutée (avec la valeur de la [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter) passée à la `ICommand`) et l’événement [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) se déclenche.

L’exemple de code XAML suivant montre une [`CollectionView`](xref:Xamarin.Forms.CollectionView) qui peut répondre à plusieurs sélections d’éléments :

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

Dans cet exemple de code, le gestionnaire d’événements `OnCollectionViewSelectionChanged` est exécuté lorsque l’événement [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) se déclenche, avec le gestionnaire d’événements qui récupère les éléments sélectionnés précédemment et les éléments actuellement sélectionnés :

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    var previous = e.PreviousSelection;
    var current = e.CurrentSelection;
    ...
}
```

> [!IMPORTANT]
> L’événement [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) peut être déclenché par des modifications qui se produisent suite à la modification de la propriété [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) .

Les captures d’écran suivantes montrent la sélection de plusieurs éléments dans un [`CollectionView`](xref:Xamarin.Forms.CollectionView):

[![Capture d’écran d’une liste verticale CollectionView avec plusieurs sélections, sur iOS et Android](selection-images/multiple-selection.png "CollectionView liste verticale avec plusieurs sélections")](selection-images/multiple-selection-large.png#lightbox "CollectionView liste verticale avec plusieurs sélections")

## <a name="single-pre-selection"></a>Présélection unique

Lorsque la propriété [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) est définie sur `Single`, un seul élément de la [`CollectionView`](xref:Xamarin.Forms.CollectionView) peut être présélectionné en affectant à la propriété [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) la valeur de l’élément. L’exemple de code XAML suivant montre une `CollectionView` qui présélectionne un seul élément :

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
> La propriété [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) a un mode de liaison par défaut de `TwoWay`.

Les données de la propriété [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) sont liées à la propriété `SelectedMonkey` du modèle de vue connecté, qui est de type `Monkey`. Par défaut, une liaison de `TwoWay` est utilisée afin que, si l’utilisateur modifie l’élément sélectionné, la valeur de la propriété `SelectedMonkey` soit définie sur l’objet `Monkey` sélectionné. La propriété `SelectedMonkey` est définie dans la classe `MonkeysViewModel` et est définie sur le quatrième élément de la collection `Monkeys` :

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

Par conséquent, lorsque le [`CollectionView`](xref:Xamarin.Forms.CollectionView) s’affiche, le quatrième élément de la liste est présélectionné :

[![Capture d’écran d’une liste verticale CollectionView avec une présélection unique, sur iOS et Android](selection-images/single-pre-selection.png "CollectionView liste verticale avec présélection unique")](selection-images/single-pre-selection-large.png#lightbox "CollectionView liste verticale avec présélection unique")

## <a name="multiple-pre-selection"></a>Pré-sélection multiple

Lorsque la propriété [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) est définie sur `Multiple`, plusieurs éléments de la [`CollectionView`](xref:Xamarin.Forms.CollectionView) peuvent être présélectionnés. L’exemple de code XAML suivant montre une `CollectionView` qui permet la présélection de plusieurs éléments :

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
> La propriété [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) a un mode de liaison par défaut de `OneWay`.

Les données de la propriété [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) sont liées à la propriété `SelectedMonkeys` du modèle de vue connecté, qui est de type `ObservableCollection<object>`. La propriété `SelectedMonkeys` est définie dans la classe `MonkeysViewModel` et est définie sur le deuxième, le quatrième et le cinquième élément de la collection `Monkeys` :

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

Par conséquent, lorsque le [`CollectionView`](xref:Xamarin.Forms.CollectionView) s’affiche, les deuxième, quatrième et cinquième éléments de la liste sont présélectionnés :

[![Capture d’écran d’une liste verticale CollectionView avec plusieurs présélections, sur iOS et Android](selection-images/multiple-pre-selection.png "CollectionView liste verticale avec plusieurs présélections")](selection-images/multiple-pre-selection-large.png#lightbox "CollectionView liste verticale avec plusieurs présélections")

## <a name="clear-selections"></a>Effacer les sélections

Les propriétés [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) et [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) peuvent être effacées en les définissant, ou les objets auxquels elles sont liées, pour `null`.

## <a name="change-selected-item-color"></a>Modifier la couleur de l’élément sélectionné

[`CollectionView`](xref:Xamarin.Forms.CollectionView) a un [`VisualState`](xref:Xamarin.Forms.VisualState) `Selected` qui peut être utilisé pour initier une modification visuelle de l’élément sélectionné dans le `CollectionView`. Un cas d’usage courant pour cette `VisualState` consiste à modifier la couleur d’arrière-plan de l’élément sélectionné, qui est illustrée dans l’exemple XAML suivant :

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
> La [`Style`](xref:Xamarin.Forms.Style) qui contient le `VisualState` `Selected` doit avoir une valeur de propriété [`TargetType`](xref:Xamarin.Forms.Style.TargetType) qui est le type de l’élément racine de la [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), qui est définie comme valeur de propriété `ItemTemplate`.

Dans cet exemple, la valeur de la propriété [`Style.TargetType`](xref:Xamarin.Forms.Style.TargetType) est définie sur `Grid`, car l’élément racine de la [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) est un [`Grid`](xref:Xamarin.Forms.Grid). L' `Selected` [`VisualState`](xref:Xamarin.Forms.VisualState) spécifie que lorsqu’un élément de l' [`CollectionView`](xref:Xamarin.Forms.CollectionView) est sélectionné, la [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) de l’élément est définie sur `LightSkyBlue`:

[![Capture d’écran d’une liste verticale CollectionView avec une couleur de sélection unique personnalisée, sur iOS et Android](selection-images/single-selection-color.png "CollectionView liste verticale avec une couleur de sélection unique personnalisée")](selection-images/single-selection-color-large.png#lightbox "CollectionView liste verticale avec une couleur de sélection unique personnalisée")

Pour plus d’informations sur les États visuels, consultez [Xamarin. Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="disable-selection"></a>Désactiver la sélection

[`CollectionView`](xref:Xamarin.Forms.CollectionView) sélection est désactivée par défaut. Toutefois, si la sélection est activée pour une `CollectionView`, elle peut être désactivée en affectant à la propriété [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) la valeur `None`:

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

Lorsque la propriété [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) est définie sur `None`, les éléments de la [`CollectionView`](xref:Xamarin.Forms.CollectionView) ne peuvent pas être sélectionnés, la propriété [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) reste `null`, et l’événement [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) n’est pas déclenché.

> [!NOTE]
> Lorsqu’un élément a été sélectionné et que la propriété [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) passe de `Single` à `None`, la propriété [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) est définie sur `null` et l’événement [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) est déclenché avec une propriété `CurrentSelection` vide.

## <a name="related-links"></a>Liens connexes

- [CollectionView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Gestionnaire d’état visuel Xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md)
