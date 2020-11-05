---
title: Personnalisation de l’apparence des cellules ListView
description: Cet article explore les options de présentation des données dans Xamarin.Forms les applications, tout en tirant parti de la commodité du contrôle ListView.
ms.prod: xamarin
ms.assetid: FD45CB91-1A8F-46FB-B432-6BC20492E456
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/12/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 18ab6b42753cefbcb8b69deeba9a76c37dc5b9b0
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93369231"
---
# <a name="customizing-listview-cell-appearance"></a>Personnalisation de l’apparence des cellules ListView

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)

La Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) classe est utilisée pour présenter des listes de défilement, qui peuvent être personnalisées par le biais de l’utilisation d' `ViewCell` éléments. Un `ViewCell` élément peut afficher du texte et des images, indiquer un État vrai/faux et recevoir une entrée d’utilisateur.

## <a name="built-in-cells"></a>Cellules intégrées
Xamarin.Forms est fourni avec des cellules intégrées qui fonctionnent pour de nombreuses applications :

- [`TextCell`](#textcell) les contrôles sont utilisés pour afficher du texte avec une deuxième ligne facultative pour le texte de détail.
- [`ImageCell`](#imagecell) les contrôles sont similaires à `TextCell` s, mais incluent une image à gauche du texte.
- `SwitchCell` les contrôles sont utilisés pour présenter et capturer les États activé/désactivé ou vrai/faux.
- `EntryCell` les contrôles sont utilisés pour présenter des données texte que l’utilisateur peut modifier.

Les [`SwitchCell`](~/xamarin-forms/user-interface/tableview.md#switchcell) [`EntryCell`](~/xamarin-forms/user-interface/tableview.md#entrycell) contrôles et sont plus couramment utilisés dans le contexte d’un [`TableView`](~/xamarin-forms/user-interface/tableview.md) .

### <a name="textcell"></a>TextCell

[`TextCell`](xref:Xamarin.Forms.TextCell) est une cellule permettant d’afficher du texte, éventuellement avec une deuxième ligne comme texte de détail. La capture d’écran suivante montre `TextCell` des éléments sur iOS et Android :

![Exemple de TextCell par défaut](customizing-cell-appearance-images/text-cell-default.png)

Les TextCells sont rendus en tant que contrôles natifs au moment de l’exécution. par conséquent, les performances sont très bonnes comparées à un personnalisé `ViewCell` . Les TextCells sont personnalisables, ce qui vous permet de définir les propriétés suivantes :

- `Text`&ndash;texte affiché sur la première ligne, en grande police.
- `Detail`&ndash;texte affiché sous la première ligne, dans une police plus petite.
- `TextColor`&ndash;couleur du texte.
- `DetailColor`&ndash;couleur du texte de détail

La capture d’écran suivante montre les `TextCell` éléments avec des propriétés de couleur personnalisées :

![Exemple de TextCell personnalisé](customizing-cell-appearance-images/text-cell-custom.png)

### <a name="imagecell"></a>ImageCell

[`ImageCell`](xref:Xamarin.Forms.ImageCell), comme `TextCell` , peut être utilisé pour afficher du texte et des détails secondaires, et offre de bonnes performances en utilisant les contrôles natifs de chaque plateforme. `ImageCell` diffère de `TextCell` en ce qu’il affiche une image à gauche du texte.

La capture d’écran suivante montre `ImageCell` des éléments sur iOS et Android : ![« exemple ImageCell par défaut »](customizing-cell-appearance-images/image-cell-default.png "Exemple de ImageCell par défaut")

`ImageCell` est utile lorsque vous devez afficher une liste de données avec un aspect visuel, par exemple une liste de contacts ou de films. `ImageCell`les s sont personnalisables, ce qui vous permet de définir les éléments suivants :

- `Text`&ndash;texte affiché sur la première ligne, en grande police
- `Detail`&ndash;texte affiché sous la première ligne, dans une police plus petite
- `TextColor`&ndash;couleur du texte
- `DetailColor`&ndash;couleur du texte de détail
- `ImageSource`&ndash;image à afficher à côté du texte

La capture d’écran suivante montre les `ImageCell` éléments avec des propriétés de couleur personnalisées : ![« exemple de ImageCell personnalisé »](customizing-cell-appearance-images/image-cell-custom.png "Exemple de ImageCell personnalisé")

## <a name="custom-cells"></a>Cellules personnalisées
Les cellules personnalisées vous permettent de créer des dispositions de cellules qui ne sont pas prises en charge par les cellules intégrées. Par exemple, vous pouvez présenter une cellule avec deux étiquettes dont le poids est égal. Un est `TextCell` insuffisant, car `TextCell` a une étiquette qui est plus petite. La plupart des personnalisations de cellule ajoutent des données en lecture seule (telles que des étiquettes supplémentaires, des images ou d’autres informations d’affichage).

Toutes les cellules personnalisées doivent dériver de [`ViewCell`](xref:Xamarin.Forms.ViewCell) , la même classe de base que tous les types de cellules intégrés utilisent.

Xamarin.Forms offre un [comportement de mise en cache](~/xamarin-forms/user-interface/listview/performance.md#caching-strategy) sur le `ListView` contrôle, ce qui peut améliorer les performances de défilement pour certains types de cellules personnalisées.

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

- La cellule personnalisée est imbriquée dans un `DataTemplate` , qui est à l’intérieur de `ListView.ItemTemplate` . Il s’agit du même processus que l’utilisation d’une cellule intégrée.
- `ViewCell` est le type de la cellule personnalisée. L’enfant de l' `DataTemplate` élément doit être de la classe, ou dériver de celle-ci `ViewCell` .
- Dans `ViewCell` , la disposition peut être gérée par n’importe quelle Xamarin.Forms disposition. Dans cet exemple, la disposition est gérée par un `StackLayout` , ce qui permet de personnaliser la couleur d’arrière-plan.

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

Dans le constructeur de la page, la `ItemTemplate` propriété du ListView est définie sur un `DataTemplate` avec le `CustomCell` type spécifié :

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

### <a name="binding-context-changes"></a>Modifications du contexte de liaison

Lors de la liaison à des instances d’un type de cellule personnalisé [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , les contrôles d’interface utilisateur qui affichent les `BindableProperty` valeurs doivent utiliser la [`OnBindingContextChanged`](xref:Xamarin.Forms.Cell.OnBindingContextChanged) substitution pour définir les données à afficher dans chaque cellule, plutôt que le constructeur de cellule, comme illustré dans l’exemple de code suivant :

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

La [`OnBindingContextChanged`](xref:Xamarin.Forms.Cell.OnBindingContextChanged) substitution sera appelée lorsque l' [`BindingContextChanged`](xref:Xamarin.Forms.BindableObject.BindingContextChanged) événement se déclenche, en réponse à la valeur de la [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) propriété qui change. Par conséquent, lorsque les `BindingContext` modifications sont apportées, les contrôles d’interface utilisateur qui affichent les [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) valeurs doivent définir leurs données. Notez que la `BindingContext` valeur de doit être vérifiée `null` , car elle peut être définie par Xamarin.Forms pour garbage collection, qui à son tour entraîne l’appel de la `OnBindingContextChanged` substitution.

Les contrôles d’interface utilisateur peuvent également être liés aux [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) instances pour afficher leurs valeurs, ce qui évite d’avoir à substituer la `OnBindingContextChanged` méthode.

> [!NOTE]
> Lors de `OnBindingContextChanged` la substitution, vérifiez que la méthode de la classe de base `OnBindingContextChanged` est appelée afin que les délégués inscrits reçoivent l' `BindingContextChanged` événement.

En XAML, la liaison du type de cellule personnalisé aux données peut être obtenue comme indiqué dans l’exemple de code suivant :

```xaml
<ListView x:Name="listView">
    <ListView.ItemTemplate>
        <DataTemplate>
            <local:CustomCell Name="{Binding Name}" Age="{Binding Age}" Location="{Binding Location}" />
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

Cela lie les `Name` Propriétés, `Age` et `Location` pouvant être liées dans l' `CustomCell` instance, aux `Name` `Age` Propriétés, et `Location` de chaque objet de la collection sous-jacente.

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

Sur iOS et Android, si le [`ListView`](xref:Xamarin.Forms.ListView) est en recyclage d’éléments et que la cellule personnalisée utilise un convertisseur personnalisé, le convertisseur personnalisé doit implémenter correctement la notification de modification de propriété. Lorsque des cellules sont réutilisées, leurs valeurs de propriété changent lorsque le contexte de liaison est mis à jour avec celui d’une cellule disponible, avec des `PropertyChanged` événements déclenchés. Pour plus d’informations, consultez [Personnalisation d’un ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md). Pour plus d’informations sur le recyclage des cellules, consultez [stratégie de mise en cache](~/xamarin-forms/user-interface/listview/performance.md#caching-strategy).

## <a name="related-links"></a>Liens associés

- [Cellules intégrées (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-listview-builtincells)
- [Cellules personnalisées (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)
- [Contexte de liaison modifié (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-listview-bindingcontextchanged)