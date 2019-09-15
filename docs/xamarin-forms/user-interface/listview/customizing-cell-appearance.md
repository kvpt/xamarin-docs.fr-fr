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
ms.sourcegitcommit: a5ef4497db04dfa016865bc7454b3de6ff088554
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2019
ms.locfileid: "70998126"
---
# <a name="customizing-listview-cell-appearance"></a>Personnaliser l’apparence de cellule de ListView

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)

La classe Xamarin. [`ListView`](xref:Xamarin.Forms.ListView) Forms est utilisée pour présenter des listes de défilement, qui peuvent être personnalisées par `ViewCell` le biais de l’utilisation d’éléments. Un `ViewCell` élément peut afficher du texte et des images, indiquer un État vrai/faux et recevoir une entrée d’utilisateur.

## <a name="built-in-cells"></a>Intégrées dans les cellules
Xamarin. Forms est fourni avec des cellules intégrées qui fonctionnent pour de nombreuses applications :

- [`TextCell`](#textcell)les contrôles sont utilisés pour afficher du texte avec une deuxième ligne facultative pour le texte de détail.
- [`ImageCell`](#imagecell)les contrôles sont similaires `TextCell`à s, mais incluent une image à gauche du texte.
- `SwitchCell`les contrôles sont utilisés pour présenter et capturer les États activé/désactivé ou vrai/faux.
- `EntryCell`les contrôles sont utilisés pour présenter des données texte que l’utilisateur peut modifier.

Les [`SwitchCell`](~/xamarin-forms/user-interface/tableview.md#switchcell) contrôles [`EntryCell`](~/xamarin-forms/user-interface/tableview.md#entrycell) et sont plus couramment utilisés dans le contexte d’un [`TableView`](~/xamarin-forms/user-interface/tableview.md).

### <a name="textcell"></a>TextCell

[`TextCell`](xref:Xamarin.Forms.TextCell) est une cellule pour afficher du texte, éventuellement avec une deuxième ligne sous forme de texte de détail. La capture d’écran `TextCell` suivante montre des éléments sur iOS et Android :

![](customizing-cell-appearance-images/text-cell-default.png "Exemple de TextCell par défaut")

TextCells sont rendus sous forme de contrôles natifs à l’exécution, donc les performances sont très bon par rapport à un personnalisé `ViewCell`. Les TextCells sont personnalisables, ce qui vous permet de définir les propriétés suivantes :

- `Text` &ndash; le texte qui est affiché sur la première ligne, de grande taille de police.
- `Detail` &ndash; le texte qui s’affiche en dessous de la première ligne, dans une police plus petite.
- `TextColor` &ndash; la couleur du texte.
- `DetailColor` &ndash; la couleur du texte en détail

La capture d’écran `TextCell` suivante montre les éléments avec des propriétés de couleur personnalisées :

![](customizing-cell-appearance-images/text-cell-custom.png "Exemple de TextCell personnalisé")

### <a name="imagecell"></a>ImageCell

[`ImageCell`](xref:Xamarin.Forms.ImageCell), tels que `TextCell`, peut être utilisé pour l’affichage de texte et des détails secondaires, et il offre des performances exceptionnelles à l’aide de contrôles natifs de chaque plateforme. `ImageCell` diffère `TextCell` car il affiche une image à gauche du texte.

La capture d’écran `ImageCell` suivante montre des éléments sur iOS et Android : ![« Exemple ImageCell par défaut »](customizing-cell-appearance-images/image-cell-default.png "Exemple de ImageCell par défaut")

`ImageCell` est utile lorsque vous avez besoin afficher une liste de données avec un aspect visuel, telles qu’une liste de contacts ou de films. `ImageCell`les s sont personnalisables, ce qui vous permet de définir les éléments suivants :

- `Text` &ndash; le texte qui est affiché sur la première ligne, de grande taille de police
- `Detail` &ndash; le texte qui s’affiche en dessous de la première ligne, dans une police plus petite
- `TextColor` &ndash; la couleur du texte
- `DetailColor` &ndash; la couleur du texte en détail
- `ImageSource` &ndash; l’image à afficher en regard du texte

La capture d’écran `ImageCell` suivante montre les éléments avec des propriétés de couleur personnalisées : ![« Exemple de ImageCell personnalisé »](customizing-cell-appearance-images/image-cell-custom.png "Exemple de ImageCell personnalisé")

## <a name="custom-cells"></a>Cellules personnalisées
Les cellules personnalisées vous permettent de créer des dispositions de cellules qui ne sont pas prises en charge par les cellules intégrées. Par exemple, vous souhaiterez présenter une cellule avec deux étiquettes qui ont le même poids. Un `TextCell` serait insuffisant, car le `TextCell` a un nom qui est plus petit. La plupart des personnalisations de cellule ajouter des données en lecture seule supplémentaires (par exemple, des étiquettes supplémentaires, images ou d’autres informations d’affichage).

Toutes les cellules personnalisés doivent dériver de [ `ViewCell` ](xref:Xamarin.Forms.ViewCell), la même classe de base que tous de la cellule intégrée types utilisent.

Xamarin. Forms offre un [comportement de mise en cache](~/xamarin-forms/user-interface/listview/performance.md#caching-strategy) sur le `ListView` contrôle, ce qui peut améliorer les performances de défilement pour certains types de cellules personnalisées.

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

- La cellule personnalisée est imbriquée dans une `DataTemplate`, ce qui se trouve dans `ListView.ItemTemplate`. Il s’agit du même processus que l’utilisation d’une cellule intégrée.
- `ViewCell` est le type de la cellule personnalisée. L’enfant de l' `DataTemplate` élément doit être de la classe, ou dériver `ViewCell` de celle-ci.
- Dans, `ViewCell`la disposition peut être gérée par n’importe quelle disposition Xamarin. Forms. Dans cet exemple, la disposition est gérée `StackLayout`par un, ce qui permet de personnaliser la couleur d’arrière-plan.

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

Dans le constructeur de la page, la `ItemTemplate` propriété du ListView est définie `DataTemplate` sur un `CustomCell` avec le type spécifié :

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

var listView = new ListView
{
    ItemsSource = people,
    ItemTemplate = customCell
};
```

Sur iOS et Android, si le [ `ListView` ](xref:Xamarin.Forms.ListView) est recyclé des éléments et que la cellule personnalisée utilise un convertisseur personnalisé, le convertisseur personnalisé doit implémenter correctement la notification de modification de propriété. Lorsque les cellules sont réutilisées leurs valeurs de propriété change le contexte de liaison est mise à jour à celle d’une cellule disponible, avec `PropertyChanged` événements déclenchés. Pour plus d’informations, consultez [personnalisation d’une ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md). Pour plus d’informations sur le recyclage de cellule, consultez [stratégie de mise en cache](~/xamarin-forms/user-interface/listview/performance.md#caching-strategy).

## <a name="related-links"></a>Liens associés

- [Intégrées dans les cellules (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-builtincells)
- [Cellules personnalisés (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)
- [Contexte de modifier la liaison (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-bindingcontextchanged)
