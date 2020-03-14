---
title: Personnaliser l’apparence de cellule de ListView
description: Cet article explore les options de présentation des données dans les applications Xamarin.Forms, tout en tirant parti de la commodité du contrôle ListView.
ms.prod: xamarin
ms.assetid: FD45CB91-1A8F-46FB-B432-6BC20492E456
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/12/2019
ms.openlocfilehash: ab54b54c9f2f7d6d7748137ea079439b7c3ddfca
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305450"
---
# <a name="customizing-listview-cell-appearance"></a>Personnaliser l’apparence de cellule de ListView

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)

La classe Xamarin. Forms [`ListView`](xref:Xamarin.Forms.ListView) est utilisée pour présenter des listes de défilement, qui peuvent être personnalisées par le biais de l’utilisation d’éléments `ViewCell`. Un élément `ViewCell` peut afficher du texte et des images, indiquer un État vrai/faux et recevoir une entrée d’utilisateur.

## <a name="built-in-cells"></a>Intégrées dans les cellules
Xamarin. Forms est fourni avec des cellules intégrées qui fonctionnent pour de nombreuses applications :

- les contrôles [`TextCell`](#textcell) sont utilisés pour afficher du texte avec une deuxième ligne facultative pour le texte de détail.
- les contrôles [`ImageCell`](#imagecell) sont similaires aux `TextCell`s mais incluent une image à gauche du texte.
- les contrôles `SwitchCell` sont utilisés pour présenter et capturer les États activé/désactivé ou vrai/faux.
- les contrôles `EntryCell` sont utilisés pour présenter des données texte que l’utilisateur peut modifier.

Les contrôles [`SwitchCell`](~/xamarin-forms/user-interface/tableview.md#switchcell) et [`EntryCell`](~/xamarin-forms/user-interface/tableview.md#entrycell) sont plus couramment utilisés dans le contexte d’un [`TableView`](~/xamarin-forms/user-interface/tableview.md).

### <a name="textcell"></a>TextCell

[`TextCell`](xref:Xamarin.Forms.TextCell) est une cellule permettant d’afficher du texte, éventuellement avec une deuxième ligne comme texte de détail. La capture d’écran suivante montre `TextCell` éléments sur iOS et Android :

![](customizing-cell-appearance-images/text-cell-default.png "Default TextCell Example")

Les TextCells sont rendus en tant que contrôles natifs au moment de l’exécution. par conséquent, les performances sont très bonnes comparées à une `ViewCell`personnalisée. Les TextCells sont personnalisables, ce qui vous permet de définir les propriétés suivantes :

- `Text` &ndash; le texte affiché sur la première ligne, en grande police.
- `Detail` &ndash; le texte affiché sous la première ligne, dans une police plus petite.
- `TextColor` &ndash; la couleur du texte.
- `DetailColor` &ndash; la couleur du texte de détail

La capture d’écran suivante montre `TextCell` éléments avec des propriétés de couleur personnalisées :

![](customizing-cell-appearance-images/text-cell-custom.png "Custom TextCell Example")

### <a name="imagecell"></a>ImageCell

[`ImageCell`](xref:Xamarin.Forms.ImageCell), comme `TextCell`, peut être utilisé pour afficher du texte et des détails secondaires, et il offre de meilleures performances en utilisant les contrôles natifs de chaque plateforme. `ImageCell` diffère de `TextCell` en ce qu’il affiche une image à gauche du texte.

La capture d’écran suivante montre `ImageCell` éléments sur iOS et Android : ![« exemple ImageCell par défaut »](customizing-cell-appearance-images/image-cell-default.png "Exemple de ImageCell par défaut")

`ImageCell` est utile lorsque vous devez afficher une liste de données avec un aspect visuel, par exemple une liste de contacts ou de films. `ImageCell`s sont personnalisables, ce qui vous permet de définir les éléments suivants :

- `Text` &ndash; le texte affiché sur la première ligne, dans une grande police
- `Detail` &ndash; le texte affiché sous la première ligne, dans une police plus petite
- `TextColor` &ndash; la couleur du texte
- `DetailColor` &ndash; la couleur du texte de détail
- `ImageSource` &ndash; l’image à afficher en regard du texte

La capture d’écran suivante montre `ImageCell` éléments avec des propriétés de couleur personnalisées : ![« exemple de ImageCell personnalisé »](customizing-cell-appearance-images/image-cell-custom.png "Exemple de ImageCell personnalisé")

## <a name="custom-cells"></a>Cellules personnalisées
Les cellules personnalisées vous permettent de créer des dispositions de cellules qui ne sont pas prises en charge par les cellules intégrées. Par exemple, vous souhaiterez présenter une cellule avec deux étiquettes qui ont le même poids. Une `TextCell` serait insuffisante, car la `TextCell` a une étiquette qui est plus petite. La plupart des personnalisations de cellule ajouter des données en lecture seule supplémentaires (par exemple, des étiquettes supplémentaires, images ou d’autres informations d’affichage).

Toutes les cellules personnalisées doivent dériver de [`ViewCell`](xref:Xamarin.Forms.ViewCell), la même classe de base que tous les types de cellules intégrés utilisent.

Xamarin. Forms offre un [comportement de mise en cache](~/xamarin-forms/user-interface/listview/performance.md#caching-strategy) sur le contrôle `ListView` qui peut améliorer les performances de défilement pour certains types de cellules personnalisées.

La capture d’écran suivante montre un exemple de cellule personnalisée :

![« Exemple de cellule personnalisée »](customizing-cell-appearance-images/custom-cell.png "Exemple de cellule personnalisée")

### <a name="xaml"></a>XAML
La cellule personnalisée indiquée dans la capture d’écran précédente peut être créée avec le code XAML suivant :

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="demoListView.ImageCellPage">
    <ContentPage.Content>
        <ListView  x:Name="listView">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <StackLayout BackgroundColor="#eee"
                        Orientation="Vertical">
                            <StackLayout Orientation="Horizontal">
                                <Image Source="{Binding image}" />
                                <Label Text="{Binding title}"
                                TextColor="#f35e20" />
                                <Label Text="{Binding subtitle}"
                                HorizontalOptions="EndAndExpand"
                                TextColor="#503026" />
                            </StackLayout>
                        </StackLayout>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </ContentPage.Content>
</ContentPage>
```

Le code XAML fonctionne comme suit :

- La cellule personnalisée est imbriquée à l’intérieur d’un `DataTemplate`, à l’intérieur `ListView.ItemTemplate`. Il s’agit du même processus que l’utilisation d’une cellule intégrée.
- `ViewCell` est le type de la cellule personnalisée. L’enfant de l’élément `DataTemplate` doit être ou dériver de la classe `ViewCell`.
- À l’intérieur de la `ViewCell`, la disposition peut être gérée par n’importe quelle disposition Xamarin. Forms. Dans cet exemple, la disposition est gérée par un `StackLayout`, ce qui permet de personnaliser la couleur d’arrière-plan.

> [!NOTE]
> Toute propriété de `StackLayout` pouvant être liée peut être liée à l’intérieur d’une cellule personnalisée. Toutefois, cette fonctionnalité n’est pas affichée dans l’exemple XAML.

### <a name="code"></a>Code

Une cellule personnalisée peut également être créée dans le code. Tout d’abord, une classe personnalisée qui dérive de `ViewCell` doit être créée :

```csharp
public class CustomCell : ViewCell
    {
        public CustomCell()
        {
            //instantiate each of our views
            var image = new Image ();
            StackLayout cellWrapper = new StackLayout ();
            StackLayout horizontalLayout = new StackLayout ();
            Label left = new Label ();
            Label right = new Label ();

            //set bindings
            left.SetBinding (Label.TextProperty, "title");
            right.SetBinding (Label.TextProperty, "subtitle");
            image.SetBinding (Image.SourceProperty, "image");

            //Set properties for desired design
            cellWrapper.BackgroundColor = Color.FromHex ("#eee");
            horizontalLayout.Orientation = StackOrientation.Horizontal;
            right.HorizontalOptions = LayoutOptions.EndAndExpand;
            left.TextColor = Color.FromHex ("#f35e20");
            right.TextColor = Color.FromHex ("503026");

            //add views to the view hierarchy
            horizontalLayout.Children.Add (image);
            horizontalLayout.Children.Add (left);
            horizontalLayout.Children.Add (right);
            cellWrapper.Children.Add (horizontalLayout);
            View = cellWrapper;
        }
    }
```

Dans le constructeur de page, la propriété `ItemTemplate` de ListView est définie sur une `DataTemplate` avec le type de `CustomCell` spécifié :

```csharp
public partial class ImageCellPage : ContentPage
    {
        public ImageCellPage ()
        {
            InitializeComponent ();
            listView.ItemTemplate = new DataTemplate (typeof(CustomCell));
        }
    }
```

### <a name="binding-context-changes"></a>Changements de contexte de liaison

Lors de la liaison à des instances de [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) d’un type de cellule personnalisé, les contrôles d’interface utilisateur qui affichent les valeurs `BindableProperty` doivent utiliser la substitution [`OnBindingContextChanged`](xref:Xamarin.Forms.Cell.OnBindingContextChanged) pour définir les données à afficher dans chaque cellule, plutôt que dans le constructeur de cellule, comme illustré dans l’exemple de code suivant :

```csharp
public class CustomCell : ViewCell
{
    Label nameLabel, ageLabel, locationLabel;

    public static readonly BindableProperty NameProperty =
        BindableProperty.Create ("Name", typeof(string), typeof(CustomCell), "Name");
    public static readonly BindableProperty AgeProperty =
        BindableProperty.Create ("Age", typeof(int), typeof(CustomCell), 0);
    public static readonly BindableProperty LocationProperty =
        BindableProperty.Create ("Location", typeof(string), typeof(CustomCell), "Location");

    public string Name
    {
        get { return(string)GetValue (NameProperty); }
        set { SetValue (NameProperty, value); }
    }

    public int Age
    {
        get { return(int)GetValue (AgeProperty); }
        set { SetValue (AgeProperty, value); }
    }

    public string Location
    {
        get { return(string)GetValue (LocationProperty); }
        set { SetValue (LocationProperty, value); }
    }
    ...

    protected override void OnBindingContextChanged ()
    {
        base.OnBindingContextChanged ();

        if (BindingContext != null)
        {
            nameLabel.Text = Name;
            ageLabel.Text = Age.ToString ();
            locationLabel.Text = Location;
        }
    }
}
```

La [`OnBindingContextChanged`](xref:Xamarin.Forms.Cell.OnBindingContextChanged) substitution sera appelée lorsque l’événement [`BindingContextChanged`](xref:Xamarin.Forms.BindableObject.BindingContextChanged) se déclenche, en réponse à la valeur de la propriété [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) modifiée. Par conséquent, lorsque l' `BindingContext` change, les contrôles d’interface utilisateur qui affichent les valeurs [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) doivent définir leurs données. Notez que la `BindingContext` doit être vérifiée pour obtenir une valeur de `null`, car cela peut être défini par Xamarin. Forms pour garbage collection, qui à son tour entraînera l’appel de la substitution `OnBindingContextChanged`.

Les contrôles d’interface utilisateur peuvent également être liés aux instances [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) pour afficher leurs valeurs, ce qui évite d’avoir à substituer la méthode `OnBindingContextChanged`.

> [!NOTE]
> Lors de la substitution de `OnBindingContextChanged`, assurez-vous que la méthode `OnBindingContextChanged` de la classe de base est appelée afin que les délégués inscrits reçoivent l’événement `BindingContextChanged`.

Dans XAML, le type de cellule personnalisé de liaison aux données peut être obtenue comme indiqué dans l’exemple de code suivant :

```xaml
<ListView x:Name="listView">
    <ListView.ItemTemplate>
        <DataTemplate>
            <local:CustomCell Name="{Binding Name}" Age="{Binding Age}" Location="{Binding Location}" />
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

Cela lie le `Name`, `Age`et `Location` propriétés pouvant être liées dans l’instance de `CustomCell`, aux propriétés `Name`, `Age`et `Location` de chaque objet de la collection sous-jacente.

La liaison équivalente en c# est illustrée dans l’exemple de code suivant :

```csharp
var customCell = new DataTemplate (typeof(CustomCell));
customCell.SetBinding (CustomCell.NameProperty, "Name");
customCell.SetBinding (CustomCell.AgeProperty, "Age");
customCell.SetBinding (CustomCell.LocationProperty, "Location");

var listView = new ListView
{
    ItemsSource = people,
    ItemTemplate = customCell
};
```

Sur iOS et Android, si le [`ListView`e](xref:Xamarin.Forms.ListView) des éléments de recyclage et que la cellule personnalisée utilise un convertisseur personnalisé, le convertisseur personnalisé doit implémenter correctement la notification de modification de propriété. Lorsque des cellules sont réutilisées, leurs valeurs de propriété changent lorsque le contexte de liaison est mis à jour avec celui d’une cellule disponible, avec `PropertyChanged` événements déclenchés. Pour plus d’informations, consultez [Personnalisation d’un ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md). Pour plus d’informations sur le recyclage des cellules, consultez [stratégie de mise en cache](~/xamarin-forms/user-interface/listview/performance.md#caching-strategy).

## <a name="related-links"></a>Liens connexes

- [Cellules intégrées (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-builtincells)
- [Cellules personnalisées (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)
- [Contexte de liaison modifié (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-bindingcontextchanged)
