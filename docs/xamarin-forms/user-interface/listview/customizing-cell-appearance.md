---
title: Personnaliser l’apparence de cellule de ListView
description: Cet article explore les options de présentation des données dans les applications Xamarin.Forms, tout en tirant parti de la commodité du contrôle ListView.
ms.prod: xamarin
ms.assetid: FD45CB91-1A8F-46FB-B432-6BC20492E456
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/07/2016
ms.openlocfilehash: ecb9e84473778f5185276a854bfbf2fdfcbb6528
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68654846"
---
# <a name="customizing-listview-cell-appearance"></a>Personnaliser l’apparence de cellule de ListView

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)

[`ListView`](xref:Xamarin.Forms.ListView)présente les listes déroulantes, qui peuvent être personnalisées à `ViewCell`l’aide de. `ViewCells` peut être utilisé pour afficher du texte et des images, indiquant un état true/false et recevoir des entrées d’utilisateur.

<a name="Built_in_Cells" />

## <a name="built-in-cells"></a>Intégrées dans les cellules
Xamarin.Forms est fourni avec des cellules intégrés qui fonctionnent pour de nombreuses applications simples :

- **TextCell** &ndash; pour afficher du texte
- **ImageCell** &ndash; pour afficher une image avec du texte.

Deux autres cellules, [ `SwitchCell` ](~/xamarin-forms/user-interface/tableview.md#switchcell) et [ `EntryCell` ](~/xamarin-forms/user-interface/tableview.md#entrycell) sont disponibles, mais ils ne sont pas couramment utilisés avec `ListView`. Consultez [ `TableView` ](~/xamarin-forms/user-interface/tableview.md) pour plus d’informations sur ces cellules.

<a name="TextCell" />

### <a name="textcell"></a>TextCell

[`TextCell`](xref:Xamarin.Forms.TextCell) est une cellule pour afficher du texte, éventuellement avec une deuxième ligne sous forme de texte de détail.

TextCells sont rendus sous forme de contrôles natifs à l’exécution, donc les performances sont très bon par rapport à un personnalisé `ViewCell`. TextCells sont personnalisables, ce qui vous permet de définir :

- `Text` &ndash; le texte qui est affiché sur la première ligne, de grande taille de police.
- `Detail` &ndash; le texte qui s’affiche en dessous de la première ligne, dans une police plus petite.
- `TextColor` &ndash; la couleur du texte.
- `DetailColor` &ndash; la couleur du texte en détail

![](customizing-cell-appearance-images/text-cell-default.png "Exemple de TextCell par défaut")

![](customizing-cell-appearance-images/text-cell-custom.png "Exemple de TextCell personnalisé")

<a name="ImageCell" />

### <a name="imagecell"></a>ImageCell

[`ImageCell`](xref:Xamarin.Forms.ImageCell), tels que `TextCell`, peut être utilisé pour l’affichage de texte et des détails secondaires, et il offre des performances exceptionnelles à l’aide de contrôles natifs de chaque plateforme. `ImageCell` diffère `TextCell` car il affiche une image à gauche du texte.

`ImageCell` est utile lorsque vous avez besoin afficher une liste de données avec un aspect visuel, telles qu’une liste de contacts ou de films. ImageCells sont personnalisables, ce qui vous permet de définir :

- `Text` &ndash; le texte qui est affiché sur la première ligne, de grande taille de police
- `Detail` &ndash; le texte qui s’affiche en dessous de la première ligne, dans une police plus petite
- `TextColor` &ndash; la couleur du texte
- `DetailColor` &ndash; la couleur du texte en détail
- `ImageSource` &ndash; l’image à afficher en regard du texte

![](customizing-cell-appearance-images/image-cell-default.png "Exemple de ImageCell par défaut")

![](customizing-cell-appearance-images/image-cell-custom.png "Exemple de ImageCell personnalisé")

<a name="customcells" />

## <a name="custom-cells"></a>Cellules personnalisées
Lorsque les cellules intégrées ne fournissent pas la disposition requise, cellules personnalisés implémenté la disposition requise. Par exemple, vous souhaiterez présenter une cellule avec deux étiquettes qui ont le même poids. Un `TextCell` serait insuffisant, car le `TextCell` a un nom qui est plus petit. La plupart des personnalisations de cellule ajouter des données en lecture seule supplémentaires (par exemple, des étiquettes supplémentaires, images ou d’autres informations d’affichage).

Toutes les cellules personnalisés doivent dériver de [ `ViewCell` ](xref:Xamarin.Forms.ViewCell), la même classe de base que tous de la cellule intégrée types utilisent.

Xamarin.Forms 2 a introduit un nouveau [comportement de mise en cache](~/xamarin-forms/user-interface/listview/performance.md#cachingstrategy) sur la `ListView` contrôle qui peut être défini pour améliorer les performances de défilement pour certains types de cellules personnalisés.

Il s’agit d’un exemple d’une cellule personnalisée :

![](customizing-cell-appearance-images/custom-cell.png "Exemple de cellule personnalisée")

### <a name="xaml"></a>XAML
Le XAML pour créer la disposition ci-dessus est la suivante :

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

Le XAML ci-dessus fait beaucoup. Nous allons décomposer :

- La cellule personnalisée est imbriquée dans une `DataTemplate`, ce qui se trouve dans `ListView.ItemTemplate`. Ceci est le même processus qu’à l’aide de n’importe quelle autre cellule.
- `ViewCell` est le type de la cellule personnalisée. L’enfant de la `DataTemplate` élément doit être d’ou dériver du type `ViewCell`.
- Notez qu’à l’intérieur du `ViewCell`, disposition est gérée par un `StackLayout`. Cette disposition permet de personnaliser la couleur d’arrière-plan. Notez que n’importe quelle propriété de `StackLayout` qui est peut être liée peut être lié à l’intérieur d’une cellule personnalisée, mais qui n’est pas indiqué ici.
- Dans, `ViewCell`la disposition peut être gérée par n’importe quelle disposition Xamarin. Forms. 

### <a name="cnum"></a>C&num;

La spécification d’une cellule personnalisée en C# est un peu plus détaillée que son équivalent XAML. Étudions cela d’un peu plus près :

Tout d’abord, définissez une classe de cellule personnalisée, avec `ViewCell` comme classe de base :

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

Dans votre constructeur de la page avec le `ListView`, définissez le ListView `ItemTemplate` à une nouvelle propriété `DataTemplate`:

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

Notez que le constructeur de `DataTemplate` prend un type. L’opérateur typeof Obtient le type CLR pour `CustomCell`.

<a name="binding-context-changes" />

### <a name="binding-context-changes"></a>Changements de contexte de liaison

Lors de la liaison à un type de cellule personnalisée [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) instances, les contrôles d’interface utilisateur affichant le `BindableProperty` valeurs doivent utiliser le [ `OnBindingContextChanged` ](xref:Xamarin.Forms.Cell.OnBindingContextChanged) méthode override pour définir les données à afficher dans chaque cellule, plutôt que le constructeur de cellule, comme illustré dans l’exemple de code suivant :

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

    public string Name {
        get { return(string)GetValue (NameProperty); }
        set { SetValue (NameProperty, value); }
    }

    public int Age {
        get { return(int)GetValue (AgeProperty); }
        set { SetValue (AgeProperty, value); }
    }

    public string Location {
        get { return(string)GetValue (LocationProperty); }
        set { SetValue (LocationProperty, value); }
    }
    ...

    protected override void OnBindingContextChanged ()
    {
        base.OnBindingContextChanged ();

        if (BindingContext != null) {
            nameLabel.Text = Name;
            ageLabel.Text = Age.ToString ();
            locationLabel.Text = Location;
        }
    }
}
```

Le [ `OnBindingContextChanged` ](xref:Xamarin.Forms.Cell.OnBindingContextChanged) de remplacement sera appelée lorsque le [ `BindingContextChanged` ](xref:Xamarin.Forms.BindableObject.BindingContextChanged) événement est déclenché en réponse à la valeur de la [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) modification de propriété. Par conséquent, lorsque le `BindingContext` change, les contrôles d’interface utilisateur affichant le [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) valeurs doivent définir leurs données. Notez que le `BindingContext` doit être vérifiée pour un `null` valeur, comme cela peut être défini par Xamarin.Forms pour le garbage collection, ce qui à son tour entraîne le `OnBindingContextChanged` remplacer appelée.

Vous pouvez également lier les contrôles d’interface utilisateur la [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) instances pour afficher leurs valeurs, ce qui supprime la nécessité de remplacer le `OnBindingContextChanged` (méthode).

> [!NOTE]
> Lors de la substitution `OnBindingContextChanged`, vérifiez que la classe de base `OnBindingContextChanged` méthode est appelée afin que les délégués inscrits reçoivent le `BindingContextChanged` événement.

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

Cette opération lie les `Name`, `Age`, et `Location` propriétés pouvant être liées dans le `CustomCell` de l’instance, à la `Name`, `Age`, et `Location` propriétés de chaque objet dans la collection sous-jacente.

La liaison équivalente en C# est illustrée dans l’exemple de code suivant :

```csharp
var customCell = new DataTemplate (typeof(CustomCell));
customCell.SetBinding (CustomCell.NameProperty, "Name");
customCell.SetBinding (CustomCell.AgeProperty, "Age");
customCell.SetBinding (CustomCell.LocationProperty, "Location");

var listView = new ListView {
    ItemsSource = people,
    ItemTemplate = customCell
};
```

Sur iOS et Android, si le [ `ListView` ](xref:Xamarin.Forms.ListView) est recyclé des éléments et que la cellule personnalisée utilise un convertisseur personnalisé, le convertisseur personnalisé doit implémenter correctement la notification de modification de propriété. Lorsque les cellules sont réutilisées leurs valeurs de propriété change le contexte de liaison est mise à jour à celle d’une cellule disponible, avec `PropertyChanged` événements déclenchés. Pour plus d’informations, consultez [personnalisation d’une ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md). Pour plus d’informations sur le recyclage de cellule, consultez [stratégie de mise en cache](~/xamarin-forms/user-interface/listview/performance.md#cachingstrategy).

## <a name="related-links"></a>Liens associés

- [Intégrées dans les cellules (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-builtincells)
- [Cellules personnalisés (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)
- [Contexte de modifier la liaison (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-bindingcontextchanged)
