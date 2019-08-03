---
title: Xamarin. Forms CollectionView, sélection
description: Par défaut, la sélection CollectionView est désactivée. Toutefois, une sélection unique et une sélection multiple peuvent être activées.
ms.prod: xamarin
ms.assetid: 423D91C7-1E58-4735-9E80-58F11CDFD953
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: f1a3e8bb8959588e64339f70268370440f356be9
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2019
ms.locfileid: "68738971"
---
# <a name="xamarinforms-collectionview-selection"></a>Xamarin. Forms CollectionView, sélection

![](~/media/shared/preview.png "Cette API est actuellement en préversion")

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)définit les propriétés suivantes qui contrôlent la sélection des éléments:

- [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode), de type [`SelectionMode`](xref:Xamarin.Forms.SelectionMode), le mode de sélection.
- [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem), de type `object`, l’élément sélectionné dans la liste. Cette propriété a un mode de liaison par `TwoWay`défaut de et a `null` une valeur quand aucun élément n’est sélectionné.
- [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems), de type `IList<object>`, des éléments sélectionnés dans la liste. Cette propriété a un mode de liaison par `OneWay`défaut de et a `null` une valeur quand aucun élément n’est sélectionné.
- [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand), de type `ICommand`, qui est exécuté lorsque l’élément sélectionné change.
- [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter), de type `object`, qui est le paramètre passé à l `SelectionChangedCommand`'.

Toutes ces propriétés s’appuient sur des objets [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), ce qui signifie qu’elles peuvent être des cibles de liaisons de données.

Par défaut, [`CollectionView`](xref:Xamarin.Forms.CollectionView) la sélection est désactivée. Toutefois, ce comportement peut être modifié en affectant [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) à la valeur de la propriété [`SelectionMode`](xref:Xamarin.Forms.SelectionMode) l’un des membres de l’énumération:

- `None`: indique que les éléments ne peuvent pas être sélectionnés. Valeur par défaut.
- `Single`: indique qu’un seul élément peut être sélectionné, avec l’élément sélectionné mis en surbrillance.
- `Multiple`: indique que plusieurs éléments peuvent être sélectionnés et que les éléments sélectionnés sont mis en surbrillance.

[`CollectionView`](xref:Xamarin.Forms.CollectionView)définit un [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) événement qui est déclenché lorsque la [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propriété change, soit parce que l’utilisateur a sélectionné un élément dans la liste, soit lorsqu’une application définit la propriété. En outre, cet événement est également déclenché lorsque la [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) propriété change. L' [`SelectionChangedEventArgs`](xref:Xamarin.Forms.SelectionChangedEventArgs) objet qui accompagne l' `SelectionChanged` événement a deux propriétés, de type `IReadOnlyList<object>`:

- `PreviousSelection`: liste des éléments qui ont été sélectionnés avant la modification de la sélection.
- `CurrentSelection`: liste des éléments sélectionnés, après la modification de la sélection.

## <a name="single-selection"></a>Sélection unique

Lorsque la [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriété a la `Single`valeur, [`CollectionView`](xref:Xamarin.Forms.CollectionView) un seul élément dans peut être sélectionné. Lorsqu’un élément est sélectionné, la [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propriété est définie sur la valeur de l’élément sélectionné. Lorsque cette propriété change, le [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand) est exécuté (avec la valeur de l' [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter) objet qui est passé `ICommand`à) et l' [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) événement se déclenche.

L’exemple de code XAML suivant [`CollectionView`](xref:Xamarin.Forms.CollectionView) montre un qui peut répondre à la sélection d’un seul élément:

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

Dans cet exemple de code, `OnCollectionViewSelectionChanged` le gestionnaire d’événements est exécuté [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) lorsque l’événement se déclenche, avec le gestionnaire d’événements qui récupère l’élément sélectionné précédemment et l’élément actuellement sélectionné:

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    string previous = (e.PreviousSelection.FirstOrDefault() as Monkey)?.Name;
    string current = (e.CurrentSelection.FirstOrDefault() as Monkey)?.Name;
    ...
}
```

> [!IMPORTANT]
> L' [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) événement peut être déclenché par des modifications qui se produisent suite à la modification [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) de la propriété.

Les captures d’écran suivantes montrent la sélection d' [`CollectionView`](xref:Xamarin.Forms.CollectionView)un élément unique dans un:

[ ![Capture d’écran d’une liste verticale CollectionView avec sélection unique, sur iOS et Android](selection-images/single-selection.png "CollectionView liste verticale avec sélection unique") ] (selection-images/single-selection-large.png#lightbox "CollectionView liste verticale avec une seule sélection")

## <a name="multiple-selection"></a>Sélection multiple

Lorsque la [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriété a la `Multiple`valeur [`CollectionView`](xref:Xamarin.Forms.CollectionView) , plusieurs éléments dans peuvent être sélectionnés. Lorsque les éléments sont sélectionnés, [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) la propriété est définie sur les éléments sélectionnés. Lorsque cette propriété change, le [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand) est exécuté (avec la valeur de l' [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter) objet qui est passé `ICommand`à) et l' [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) événement se déclenche.

L’exemple de code XAML suivant [`CollectionView`](xref:Xamarin.Forms.CollectionView) montre un qui peut répondre à la sélection de plusieurs éléments:

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

Dans cet exemple de code, `OnCollectionViewSelectionChanged` le gestionnaire d’événements est exécuté [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) lorsque l’événement se déclenche, avec le gestionnaire d’événements qui récupère les éléments sélectionnés précédemment et les éléments actuellement sélectionnés:

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    var previous = e.PreviousSelection;
    var current = e.CurrentSelection;
    ...
}
```

> [!IMPORTANT]
> L' [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) événement peut être déclenché par des modifications qui se produisent suite à la modification [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) de la propriété.

Les captures d’écran suivantes montrent la sélection de [`CollectionView`](xref:Xamarin.Forms.CollectionView)plusieurs éléments dans un:

[ ![Capture d’écran d’une liste verticale CollectionView avec sélection multiple, sur iOS et Android](selection-images/multiple-selection.png "CollectionView liste verticale avec sélection multiple") ] (selection-images/multiple-selection-large.png#lightbox "CollectionView liste verticale avec plusieurs sélections")

## <a name="single-pre-selection"></a>Présélection unique

Lorsque la [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriété a la `Single`valeur, [`CollectionView`](xref:Xamarin.Forms.CollectionView) un élément unique dans peut être présélectionné en affectant à la [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propriété l’élément. L’exemple de code XAML suivant `CollectionView` montre une présélection d’un seul élément:

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
> La [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propriété a un mode de liaison par `TwoWay`défaut de.

Le [ `SelectedItem` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) lie les données de propriété pour le `SelectedMonkey` propriété du modèle de vue connectée, ce qui est de type `Monkey`. Par défaut, une `TwoWay` liaison est utilisée de sorte que si l’utilisateur modifie l’élément sélectionné, la valeur de `SelectedMonkey` la propriété est définie sur l’objet `Monkey` sélectionné. La `SelectedMonkey` propriété est définie dans la `MonkeysViewModel` classe et est définie sur le quatrième élément de la `Monkeys` collection:

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

Par conséquent, lorsque [`CollectionView`](xref:Xamarin.Forms.CollectionView) le s’affiche, le quatrième élément de la liste est présélectionné:

[ ![Capture d’écran d’une liste verticale CollectionView avec présélection unique, sur iOS et Android](selection-images/single-pre-selection.png "CollectionView liste verticale avec présélection unique") ] (selection-images/single-pre-selection-large.png#lightbox "CollectionView liste verticale avec présélection unique")

## <a name="multiple-pre-selection"></a>Pré-sélection multiple

Lorsque la [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriété a la `Multiple`valeur, plusieurs éléments de la [`CollectionView`](xref:Xamarin.Forms.CollectionView) peuvent être présélectionnés. L’exemple de code XAML suivant `CollectionView` montre un qui active la présélection de plusieurs éléments:

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
> La [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) propriété a un mode de liaison par `OneWay`défaut de.

Le [ `SelectedItems` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) lie les données de propriété pour le `SelectedMonkeys` propriété du modèle de vue connectée, ce qui est de type `ObservableCollection<object>`. La `SelectedMonkeys` propriété est définie dans la `MonkeysViewModel` classe et est définie sur le deuxième, le quatrième et le cinquième élément de la `Monkeys` collection:

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

Par conséquent, lorsque [`CollectionView`](xref:Xamarin.Forms.CollectionView) le s’affiche, les deuxième, quatrième et cinquième éléments de la liste sont présélectionnés:

[ ![Capture d’écran d’une liste verticale CollectionView avec plusieurs présélections, sur iOS et Android](selection-images/multiple-pre-selection.png "CollectionView liste verticale avec plusieurs") ] présélections (selection-images/multiple-pre-selection-large.png#lightbox "CollectionView liste verticale avec plusieurs") présélections

## <a name="clearing-selections"></a>Effacement des sélections

Les [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propriétés [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) et peuvent être effacées en les définissant, ou les objets auxquels elles sont `null`liées, à.

## <a name="change-selected-item-color"></a>Modifier la couleur de l’élément sélectionné

[`CollectionView`](xref:Xamarin.Forms.CollectionView)a un `Selected` [`VisualState`](xref:Xamarin.Forms.VisualState) qui peut être utilisé pour initier une modification visuelle à l’élément sélectionné dans `CollectionView`le. Un cas d' `VisualState` usage courant est de modifier la couleur d’arrière-plan de l’élément sélectionné, qui est illustrée dans l’exemple XAML suivant:

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
> Le [`Style`](xref:Xamarin.Forms.Style) qui `ItemTemplate` [contientdoitavoir`DataTemplate`](xref:Xamarin.Forms.DataTemplate)une [`TargetType`](xref:Xamarin.Forms.Style.TargetType) valeur de propriété qui est le type de l’élément racine de, qui est défini en tant que valeur de propriété. `Selected` `VisualState`

Dans cet exemple, la [`Style.TargetType`](xref:Xamarin.Forms.Style.TargetType) valeur de la propriété est `Grid` définie sur, car [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) l’élément racine du [`Grid`](xref:Xamarin.Forms.Grid)est un. `Selected` `LightSkyBlue`Spécifie [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) [que`CollectionView`](xref:Xamarin.Forms.CollectionView) lorsqu’un élément de est sélectionné, la valeur de l’élément est définie sur: [`VisualState`](xref:Xamarin.Forms.VisualState)

[ ![Capture d’écran d’une liste verticale CollectionView avec une couleur de sélection unique personnalisée, sur iOS et Android](selection-images/single-selection-color.png "CollectionView liste verticale avec une couleur de sélection unique personnalisée") ] (selection-images/single-selection-color-large.png#lightbox "CollectionView liste verticale avec une couleur de sélection unique personnalisée")

Pour plus d’informations sur les États visuels, consultez [Xamarin. Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="disable-selection"></a>Désactiver la sélection

[`CollectionView`](xref:Xamarin.Forms.CollectionView)la sélection est désactivée par défaut. Toutefois, si `CollectionView` la sélection est activée, elle peut être désactivée en affectant `None`à la propriété la [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) valeur:

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

Lorsque la [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriété a la `None`valeur, les éléments de [`CollectionView`](xref:Xamarin.Forms.CollectionView) ne peuvent pas être sélectionnés [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) , la propriété `null`est conservée [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) et l’événement n’est pas déclenché.

> [!NOTE]
> Lorsqu’un élément a été sélectionné et que [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) la propriété est modifiée `Single` de `None`en, [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) la propriété aura la valeur `null` et l' [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) événement sera déclenché avec une propriété vide `CurrentSelection` .

## <a name="related-links"></a>Liens connexes

- [CollectionView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Gestionnaire d’état visuel Xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md)
