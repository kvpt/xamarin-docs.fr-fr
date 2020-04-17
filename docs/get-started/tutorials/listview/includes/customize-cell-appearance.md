---
ms.openlocfilehash: 04d2a244482c92a615c3eec2130cec1df8aaaf1f
ms.sourcegitcommit: 89b3e383a37db5b940f0c63bbfe9cb806dc7d5d1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81389945"
---
[`ListView`](xref:Xamarin.Forms.ListView) était auparavant rempli avec des données à l’aide d’un mécanisme de liaison de données. Cependant, malgré la liaison de données à une collection, où chaque objet de la collection définissait plusieurs éléments de données, un seul élément de données était affiché par objet (la propriété `Name` de l’objet `Monkey`).

Dans cet exercice, vous allez modifier le projet **ListViewTutorial** pour que [`ListView`](xref:Xamarin.Forms.ListView) affiche plusieurs éléments de données dans chaque ligne.

# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. Dans **MainPage.xaml**, modifiez la déclaration [`ListView`](xref:Xamarin.Forms.ListView) pour personnaliser l’apparence de chaque ligne :

    ```xaml
    <ListView ItemsSource="{Binding Monkeys}"
              HasUnevenRows="true"
              ItemSelected="OnListViewItemSelected"
              ItemTapped="OnListViewItemTapped">
        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <Grid Padding="10">
                        <Grid.RowDefinitions>
                            <RowDefinition Height="Auto" />
                            <RowDefinition Height="*" />
                        </Grid.RowDefinitions>
                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="Auto" />
                            <ColumnDefinition Width="*" />
                        </Grid.ColumnDefinitions>
                        <Image Grid.RowSpan="2"
                               Source="{Binding ImageUrl}"
                               Aspect="AspectFill"
                               HeightRequest="60"
                               WidthRequest="60" />
                        <Label Grid.Column="1"
                               Text="{Binding Name}"
                               FontAttributes="Bold" />
                        <Label Grid.Row="1"
                               Grid.Column="1"
                               Text="{Binding Location}"
                               VerticalOptions="End" />
                    </Grid>
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
    ```

    Ce code définit la propriété [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) sur [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), qui définit l’apparence de chaque ligne dans [`ListView`](xref:Xamarin.Forms.ListView). L’enfant de `DataTemplate` doit être du type [`Cell`](xref:Xamarin.Forms.Cell) ou en être dérivé. Ce code utilise [`ViewCell`](xref:Xamarin.Forms.ViewCell), dérivé de `Cell`, pour créer une mise en page personnalisée pour chaque ligne `ListView`. La mise en page à l’intérieur de `ViewCell` est gérée par [`Grid`](xref:Xamarin.Forms.Grid), qui contient un objet [`Image`](xref:Xamarin.Forms.Image) et deux objets [`Label`](xref:Xamarin.Forms.Label). Les données de l’objet `Image` relient leur propriété [`Source`](xref:Xamarin.Forms.Image.Source) à la propriété `ImageUrl` de chaque objet `Monkey`, tandis que les objets `Label` relient leurs propriétés [`Text`](xref:Xamarin.Forms.Label.Text) aux propriétés `Name` et `Location` de chaque objet `Monkey`.

    Par défaut, toutes les lignes de [`ListView`](xref:Xamarin.Forms.ListView) ont la même hauteur. Toutefois, ce code définit la propriété [`ListView.HasUnevenRows`](xref:Xamarin.Forms.ListView.HasUnevenRows) sur `true` afin que la hauteur de ligne puisse être dimensionnée en fonction de son contenu. Ceci permet de prendre en charge les propriétés `Name` et `Location` de chaque objet `Monkey` ayant des chaînes de longueur variable.

    Pour plus d’informations sur l’apparence d’une cellule [`ListView`](xref:Xamarin.Forms.ListView), consultez [Personnaliser l’apparence de cellule de ListView](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md). Pour plus d’informations sur les modèles de données, consultez [Modèles de données Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

1. Dans la barre d’outils Visual Studio, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application à l’intérieur du simulateur iOS distant ou de l’émulateur Android de votre choix :

    [![Capture d’écran d’une vue Liste dont les éléments sont basés sur un modèle de données](../images/customize-cell-appearance.png "Vue Liste comprenant des données basées sur un modèle")](../images/customize-cell-appearance-large.png#lightbox "Vue Liste comprenant des données basées sur un modèle")

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/vsmac)

1. Dans **MainPage.xaml**, modifiez la déclaration [`ListView`](xref:Xamarin.Forms.ListView) pour personnaliser l’apparence de chaque ligne :

    ```xaml
    <ListView ItemsSource="{Binding Monkeys}"
              HasUnevenRows="true"
              ItemSelected="OnListViewItemSelected"
              ItemTapped="OnListViewItemTapped">
        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <Grid Padding="10">
                        <Grid.RowDefinitions>
                            <RowDefinition Height="Auto" />
                            <RowDefinition Height="*" />
                        </Grid.RowDefinitions>
                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="Auto" />
                            <ColumnDefinition Width="*" />
                        </Grid.ColumnDefinitions>
                        <Image Grid.RowSpan="2"
                               Source="{Binding ImageUrl}"
                               Aspect="AspectFill"
                               HeightRequest="60"
                               WidthRequest="60" />
                        <Label Grid.Column="1"
                               Text="{Binding Name}"
                               FontAttributes="Bold" />
                        <Label Grid.Row="1"
                               Grid.Column="1"
                               Text="{Binding Location}"
                               VerticalOptions="End" />
                    </Grid>
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
    ```

    Ce code définit la propriété [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) sur [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), qui définit l’apparence de chaque ligne dans [`ListView`](xref:Xamarin.Forms.ListView). L’enfant de `DataTemplate` doit être du type [`Cell`](xref:Xamarin.Forms.Cell) ou en être dérivé. Ce code utilise [`ViewCell`](xref:Xamarin.Forms.ViewCell), dérivé de `Cell`, pour créer une mise en page personnalisée pour chaque ligne `ListView`. La mise en page à l’intérieur de `ViewCell` est gérée par [`Grid`](xref:Xamarin.Forms.Grid), qui contient un objet [`Image`](xref:Xamarin.Forms.Image) et deux objets [`Label`](xref:Xamarin.Forms.Label). Les données de l’objet `Image` relient leur propriété [`Source`](xref:Xamarin.Forms.Image.Source) à la propriété `ImageUrl` de chaque objet `Monkey`, tandis que les objets `Label` relient leurs propriétés [`Text`](xref:Xamarin.Forms.Label.Text) aux propriétés `Name` et `Location` de chaque objet `Monkey`.

    Par défaut, toutes les lignes de [`ListView`](xref:Xamarin.Forms.ListView) ont la même hauteur. Toutefois, ce code définit la propriété [`ListView.HasUnevenRows`](xref:Xamarin.Forms.ListView.HasUnevenRows) sur `true` afin que la hauteur de ligne puisse être dimensionnée en fonction de son contenu. Ceci permet de prendre en charge les propriétés `Name` et `Location` de chaque objet `Monkey` ayant des chaînes de longueur variable.

    Pour plus d’informations sur l’apparence d’une cellule [`ListView`](xref:Xamarin.Forms.ListView), consultez [Personnaliser l’apparence de cellule de ListView](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md). Pour plus d’informations sur les modèles de données, consultez [Modèles de données Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

1. Dans la barre d’outils Visual Studio pour Mac, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application dans le simulateur iOS ou dans l’émulateur Android de votre choix :

    [![Capture d’écran d’une vue Liste dont les éléments sont basés sur un modèle de données](../images/customize-cell-appearance.png "Vue Liste comprenant des données basées sur un modèle")](../images/customize-cell-appearance-large.png#lightbox "Vue Liste comprenant des données basées sur un modèle")
