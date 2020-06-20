---
title: Xamarin.FormsSélection de CollectionView
description: Par défaut, la sélection CollectionView est désactivée. Toutefois, une sélection unique et une sélection multiple peuvent être activées.
ms.prod: xamarin
ms.assetid: 423D91C7-1E58-4735-9E80-58F11CDFD953
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 39f118d7073fc551923f891681c8c6cf6a4c5ddd
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137382"
---
# <a name="xamarinforms-collectionview-selection"></a>Xamarin.FormsSélection de CollectionView

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)définit les propriétés suivantes qui contrôlent la sélection des éléments :

- [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode), de type [`SelectionMode`](xref:Xamarin.Forms.SelectionMode) , le mode de sélection.
- [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem), de type `object` , l’élément sélectionné dans la liste. Cette propriété a un mode de liaison par défaut de `TwoWay` et a une `null` valeur quand aucun élément n’est sélectionné.
- [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems), de type `IList<object>` , des éléments sélectionnés dans la liste. Cette propriété a un mode de liaison par défaut de `OneWay` et a une `null` valeur quand aucun élément n’est sélectionné.
- [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand), de type `ICommand` , qui est exécuté lorsque l’élément sélectionné change.
- [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter), de type `object` , qui est le paramètre passé à l' `SelectionChangedCommand` .

Toutes ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie que les propriétés peuvent être des cibles de liaisons de données.

Par défaut, la [`CollectionView`](xref:Xamarin.Forms.CollectionView) sélection est désactivée. Toutefois, ce comportement peut être modifié en affectant [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) à la valeur de la propriété l’un des membres de l' [`SelectionMode`](xref:Xamarin.Forms.SelectionMode) énumération :

- `None`: indique que les éléments ne peuvent pas être sélectionnés. Il s’agit de la valeur par défaut.
- `Single`: indique qu’un seul élément peut être sélectionné, avec l’élément sélectionné mis en surbrillance.
- `Multiple`: indique que plusieurs éléments peuvent être sélectionnés et que les éléments sélectionnés sont mis en surbrillance.

[`CollectionView`](xref:Xamarin.Forms.CollectionView)définit un [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) événement qui est déclenché lorsque la [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propriété change, soit parce que l’utilisateur a sélectionné un élément dans la liste, soit lorsqu’une application définit la propriété. En outre, cet événement est également déclenché lorsque la [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) propriété change. L' [`SelectionChangedEventArgs`](xref:Xamarin.Forms.SelectionChangedEventArgs) objet qui accompagne l' `SelectionChanged` événement a deux propriétés, de type `IReadOnlyList<object>` :

- `PreviousSelection`: liste des éléments qui ont été sélectionnés avant la modification de la sélection.
- `CurrentSelection`: liste des éléments sélectionnés, après la modification de la sélection.

En outre, [`CollectionView`](xref:Xamarin.Forms.CollectionView) dispose d’une `UpdateSelectedItems` méthode qui met à jour la [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) propriété avec une liste d’éléments sélectionnés, tout en déclenchant une seule notification de modification.

## <a name="single-selection"></a>Sélection unique

Lorsque la [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriété a la valeur `Single` , un seul élément dans [`CollectionView`](xref:Xamarin.Forms.CollectionView) peut être sélectionné. Lorsqu’un élément est sélectionné, la [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propriété est définie sur la valeur de l’élément sélectionné. Lorsque cette propriété change, le [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand) est exécuté (avec la valeur de l' [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter) objet qui est passé à `ICommand` ) et l' [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) événement se déclenche.

L’exemple de code XAML suivant montre un [`CollectionView`](xref:Xamarin.Forms.CollectionView) qui peut répondre à la sélection d’un seul élément :

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

Dans cet exemple de code, le `OnCollectionViewSelectionChanged` Gestionnaire d’événements est exécuté lorsque l' [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) événement se déclenche, avec le gestionnaire d’événements qui récupère l’élément sélectionné précédemment et l’élément actuellement sélectionné :

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    string previous = (e.PreviousSelection.FirstOrDefault() as Monkey)?.Name;
    string current = (e.CurrentSelection.FirstOrDefault() as Monkey)?.Name;
    ...
}
```

> [!IMPORTANT]
> L' [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) événement peut être déclenché par des modifications qui se produisent suite à la modification de la [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriété.

Les captures d’écran suivantes montrent la sélection d’un élément unique dans un [`CollectionView`](xref:Xamarin.Forms.CollectionView) :

[![Capture d’écran d’une liste verticale CollectionView avec une sélection unique, sur iOS et Android](selection-images/single-selection.png "CollectionView liste verticale avec une seule sélection")](selection-images/single-selection-large.png#lightbox "CollectionView liste verticale avec une seule sélection")

## <a name="multiple-selection"></a>Sélection multiple

Lorsque la [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriété a la valeur `Multiple` , plusieurs éléments dans [`CollectionView`](xref:Xamarin.Forms.CollectionView) peuvent être sélectionnés. Lorsque les éléments sont sélectionnés, la [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) propriété est définie sur les éléments sélectionnés. Lorsque cette propriété change, le [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand) est exécuté (avec la valeur de l' [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter) objet qui est passé à `ICommand` ) et l' [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) événement se déclenche.

L’exemple de code XAML suivant montre un [`CollectionView`](xref:Xamarin.Forms.CollectionView) qui peut répondre à la sélection de plusieurs éléments :

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

Dans cet exemple de code, le `OnCollectionViewSelectionChanged` Gestionnaire d’événements est exécuté lorsque l' [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) événement se déclenche, avec le gestionnaire d’événements qui récupère les éléments sélectionnés précédemment et les éléments actuellement sélectionnés :

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    var previous = e.PreviousSelection;
    var current = e.CurrentSelection;
    ...
}
```

> [!IMPORTANT]
> L' [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) événement peut être déclenché par des modifications qui se produisent suite à la modification de la [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriété.

Les captures d’écran suivantes montrent la sélection de plusieurs éléments dans un [`CollectionView`](xref:Xamarin.Forms.CollectionView) :

[![Capture d’écran d’une liste verticale CollectionView avec plusieurs sélections, sur iOS et Android](selection-images/multiple-selection.png "CollectionView liste verticale avec plusieurs sélections")](selection-images/multiple-selection-large.png#lightbox "CollectionView liste verticale avec plusieurs sélections")

## <a name="single-pre-selection"></a>Présélection unique

Lorsque la [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriété a la valeur `Single` , un élément unique dans [`CollectionView`](xref:Xamarin.Forms.CollectionView) peut être présélectionné en affectant à la [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propriété l’élément. L’exemple de code XAML suivant montre une `CollectionView` présélection d’un seul élément :

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
> La [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propriété a un mode de liaison par défaut de `TwoWay` .

Les [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) données de propriété sont liées à la `SelectedMonkey` propriété du modèle de vue connectée, qui est de type `Monkey` . Par défaut, une `TwoWay` liaison est utilisée de sorte que si l’utilisateur modifie l’élément sélectionné, la valeur de la `SelectedMonkey` propriété est définie sur l' `Monkey` objet sélectionné. La `SelectedMonkey` propriété est définie dans la `MonkeysViewModel` classe et est définie sur le quatrième élément de la `Monkeys` collection :

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

Lorsque la [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriété a la valeur `Multiple` , plusieurs éléments de la [`CollectionView`](xref:Xamarin.Forms.CollectionView) peuvent être présélectionnés. L’exemple de code XAML suivant montre un `CollectionView` qui active la présélection de plusieurs éléments :

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
> La [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) propriété a un mode de liaison par défaut de `OneWay` .

Les [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) données de propriété sont liées à la `SelectedMonkeys` propriété du modèle de vue connectée, qui est de type `ObservableCollection<object>` . La `SelectedMonkeys` propriété est définie dans la `MonkeysViewModel` classe et est définie sur le deuxième, le quatrième et le cinquième élément de la `Monkeys` collection :

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

Les [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) Propriétés et peuvent être effacées en les définissant, ou les objets auxquels elles sont liées, à `null` .

## <a name="change-selected-item-color"></a>Modifier la couleur de l’élément sélectionné

[`CollectionView`](xref:Xamarin.Forms.CollectionView)a un `Selected` [`VisualState`](xref:Xamarin.Forms.VisualState) qui peut être utilisé pour initier une modification visuelle à l’élément sélectionné dans le `CollectionView` . Un cas d’usage courant `VisualState` est de modifier la couleur d’arrière-plan de l’élément sélectionné, qui est illustrée dans l’exemple XAML suivant :

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
> Le [`Style`](xref:Xamarin.Forms.Style) qui contient `Selected` `VisualState` doit avoir une [`TargetType`](xref:Xamarin.Forms.Style.TargetType) valeur de propriété qui est le type de l’élément racine de [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , qui est défini en tant que `ItemTemplate` valeur de propriété.

Dans cet exemple, la [`Style.TargetType`](xref:Xamarin.Forms.Style.TargetType) valeur de la propriété est définie sur `Grid` , car l’élément racine du [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) est un [`Grid`](xref:Xamarin.Forms.Grid) . `Selected` [`VisualState`](xref:Xamarin.Forms.VisualState) Spécifie que lorsqu’un élément de [`CollectionView`](xref:Xamarin.Forms.CollectionView) est sélectionné, la [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) valeur de l’élément est définie sur `LightSkyBlue` :

[![Capture d’écran d’une liste verticale CollectionView avec une couleur de sélection unique personnalisée, sur iOS et Android](selection-images/single-selection-color.png "CollectionView liste verticale avec une couleur de sélection unique personnalisée")](selection-images/single-selection-color-large.png#lightbox "CollectionView liste verticale avec une couleur de sélection unique personnalisée")

Pour plus d’informations sur les États visuels, consultez [ Xamarin.Forms Gestionnaire d’état visuel](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="disable-selection"></a>Désactiver la sélection

[`CollectionView`](xref:Xamarin.Forms.CollectionView)la sélection est désactivée par défaut. Toutefois, si `CollectionView` la sélection est activée, elle peut être désactivée en affectant à la propriété la valeur [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) `None` :

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

Lorsque la [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriété a la valeur `None` , les éléments de [`CollectionView`](xref:Xamarin.Forms.CollectionView) ne peuvent pas être sélectionnés, la [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propriété est conservée `null` et l’événement n’est [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) pas déclenché.

> [!NOTE]
> Lorsqu’un élément a été sélectionné et que la [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriété est modifiée de `Single` en `None` , la [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propriété aura la valeur `null` et l' [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) événement sera déclenché avec une propriété vide `CurrentSelection` .

## <a name="related-links"></a>Liens connexes

- [CollectionView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Xamarin.FormsGestionnaire d’état visuel](~/xamarin-forms/user-interface/visual-state-manager.md)
