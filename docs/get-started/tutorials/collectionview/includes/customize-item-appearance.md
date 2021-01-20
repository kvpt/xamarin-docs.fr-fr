---
ms.openlocfilehash: 4464b5cc4220072de3d55d76f568dab938d3d805
ms.sourcegitcommit: b75c369adb8e02a429b6c0fed8ba4a855099bf01
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98559057"
---
[`CollectionView`](xref:Xamarin.Forms.CollectionView) était auparavant rempli avec des données à l’aide d’un mécanisme de liaison de données. Cependant, malgré la liaison de données à une collection, où chaque objet de la collection définissait plusieurs éléments de données, un seul élément de données était affiché par objet (la propriété `Name` de l’objet `Monkey`).

Dans cet exercice, vous allez modifier le projet **CollectionViewTutorial** pour que [`CollectionView`](xref:Xamarin.Forms.CollectionView) affiche plusieurs éléments de données dans chaque ligne.

# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. Dans **MainPage.xaml**, modifiez la déclaration [`CollectionView`](xref:Xamarin.Forms.CollectionView) pour personnaliser l’apparence de chaque élément de données :

    ```xaml
    <CollectionView ItemsSource="{Binding Monkeys}"
                    SelectionMode="Single"
                    SelectionChanged="OnSelectionChanged">
        <CollectionView.ItemTemplate>
            <DataTemplate>
                <Grid Padding="10"
                      RowDefinitions="Auto, *"
                      ColumnDefinitions="Auto, *">
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
            </DataTemplate>
        </CollectionView.ItemTemplate>
    </CollectionView>
    ```

    Ce code définit la propriété [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) sur un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), qui définit l’apparence de chaque élément dans la [`CollectionView`](xref:Xamarin.Forms.CollectionView). L’enfant du `DataTemplate` est une [`Grid`](xref:Xamarin.Forms.Grid), qui contient un objet [`Image`](xref:Xamarin.Forms.Image) et deux objets [`Label`](xref:Xamarin.Forms.Label). Les données de l’objet `Image` relient leur propriété [`Source`](xref:Xamarin.Forms.Image.Source) à la propriété `ImageUrl` de chaque objet `Monkey`, tandis que les objets `Label` relient leurs propriétés [`Text`](xref:Xamarin.Forms.Label.Text) aux propriétés `Name` et `Location` de chaque objet `Monkey`.

    Pour plus d’informations sur l’apparence des éléments d’une [`CollectionView`](xref:Xamarin.Forms.CollectionView), consultez [Définir l’apparence des éléments](~/xamarin-forms/user-interface/collectionview/populate-data.md#define-item-appearance). Pour plus d’informations sur les modèles de données, consultez [Modèles de données Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

1. Dans la barre d’outils Visual Studio, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application à l’intérieur du simulateur iOS distant ou de l’émulateur Android de votre choix :

    [![Capture d’écran d’une CollectionView dont les éléments sont basés sur un modèle de données](../images/customize-item-appearance.png "CollectionView affichant des données basées sur un modèle")](../images/customize-item-appearance-large.png#lightbox "CollectionView affichant des données basées sur un modèle")

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/vsmac)

1. Dans **MainPage.xaml**, modifiez la déclaration [`CollectionView`](xref:Xamarin.Forms.CollectionView) pour personnaliser l’apparence de chaque élément de données :

    ```xaml
    <CollectionView ItemsSource="{Binding Monkeys}"
                    SelectionMode="Single"
                    SelectionChanged="OnSelectionChanged">
        <CollectionView.ItemTemplate>
            <DataTemplate>
                <Grid Padding="10"
                      RowDefinitions="Auto, *"
                      ColumnDefinitions="Auto, *">
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
            </DataTemplate>
        </CollectionView.ItemTemplate>
    </CollectionView>
    ```

    Ce code définit la propriété [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) sur un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), qui définit l’apparence de chaque élément dans la [`CollectionView`](xref:Xamarin.Forms.CollectionView). L’enfant du `DataTemplate` est une [`Grid`](xref:Xamarin.Forms.Grid), qui contient un objet [`Image`](xref:Xamarin.Forms.Image) et deux objets [`Label`](xref:Xamarin.Forms.Label). Les données de l’objet `Image` relient leur propriété [`Source`](xref:Xamarin.Forms.Image.Source) à la propriété `ImageUrl` de chaque objet `Monkey`, tandis que les objets `Label` relient leurs propriétés [`Text`](xref:Xamarin.Forms.Label.Text) aux propriétés `Name` et `Location` de chaque objet `Monkey`.

    Pour plus d’informations sur l’apparence des éléments d’une [`CollectionView`](xref:Xamarin.Forms.CollectionView), consultez [Définir l’apparence des éléments](~/xamarin-forms/user-interface/collectionview/populate-data.md#define-item-appearance). Pour plus d’informations sur les modèles de données, consultez [Modèles de données Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

1. Dans la barre d’outils Visual Studio pour Mac, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application dans le simulateur iOS ou dans l’émulateur Android de votre choix :

    [![Capture d’écran d’une CollectionView dont les éléments sont basés sur un modèle de données](../images/customize-item-appearance.png "CollectionView affichant des données basées sur un modèle")](../images/customize-item-appearance-large.png#lightbox "CollectionView affichant des données basées sur un modèle")
