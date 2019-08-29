---
title: Personnaliser l’apparence de ListView
description: Cet article explique comment personnaliser les ListViews dans les applications Xamarin.Forms à l’aide des en-têtes, des pieds de page, des groupes et des cellules de hauteur variable.
ms.prod: xamarin
ms.assetid: DC8009B0-4371-4D60-885A-5362FC7EE3E5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/13/2018
ms.openlocfilehash: 1355f65a192cf8f7f119a2ee77f334c115650578
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70121635"
---
# <a name="customizing-listview-appearance"></a>Personnaliser l’apparence de ListView

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-grouping)

[`ListView`](xref:Xamarin.Forms.ListView)peut contrôler la présentation de la liste, en plus [`ViewCell`](xref:Xamarin.Forms.ViewCell) des instances de chaque ligne de la liste.

<a name="Grouping" />

## <a name="grouping"></a>Regroupement
Souvent, les jeux de données volumineux peut devenir complexe lorsque présentée dans une liste déroulante en permanence. L’activation de regroupement peut améliorer l’expérience utilisateur dans ces cas en mieux organiser le contenu et en activant des contrôles spécifiques à la plateforme qui facilitent la navigation des données.

Lorsque le regroupement est activé pour un `ListView`, une ligne d’en-tête est ajoutée pour chaque groupe.

Pour activer le regroupement :

- Créer une liste de listes (une liste de groupes, chaque groupe en cours d’une liste d’éléments).
- Définir le `ListView`de `ItemsSource` à cette liste.
- Définissez `IsGroupingEnabled` sur true.
- Définissez [ `GroupDisplayBinding` ](xref:Xamarin.Forms.ListView.GroupDisplayBinding) à lier à des groupes de la propriété qui est utilisée comme titre du groupe.
- [Facultatif] Définissez [ `GroupShortNameBinding` ](xref:Xamarin.Forms.ListView.GroupShortNameBinding) à lier à la propriété des groupes qui est utilisée en tant que le nom court pour le groupe. Le nom court est utilisé pour les listes de raccourcis (colonne de droite sur iOS).

Commencez par créer une classe pour les groupes :

```csharp
public class PageTypeGroup : List<PageModel>
    {
        public string Title { get; set; }
        public string ShortName { get; set; } //will be used for jump lists
        public string Subtitle { get; set; }
        private PageTypeGroup(string title, string shortName)
        {
            Title = title;
            ShortName = shortName;
        }

        public static IList<PageTypeGroup> All { private set; get; }
    }
```

Dans le code ci-dessus, `All` est la liste qui sera accordée à notre ListView comme source de liaison. `Title` et `ShortName` sont les propriétés qui seront utilisées pour les en-têtes de groupe.

À ce stade, `All` est une liste vide. Ajoutez un constructeur statique afin que la liste contiendra au lancement du programme :

```csharp
static PageTypeGroup()
{
    List<PageTypeGroup> Groups = new List<PageTypeGroup> {
            new PageTypeGroup ("Alfa", "A"){
                new PageModel("Amelia", "Cedar", new switchCellPage(),""),
                new PageModel("Alfie", "Spruce", new switchCellPage(), "grapefruit.jpg"),
                new PageModel("Ava", "Pine", new switchCellPage(), "grapefruit.jpg"),
                new PageModel("Archie", "Maple", new switchCellPage(), "grapefruit.jpg")
            },
            new PageTypeGroup ("Bravo", "B"){
                new PageModel("Brooke", "Lumia", new switchCellPage(),""),
                new PageModel("Bobby", "Xperia", new switchCellPage(), "grapefruit.jpg"),
                new PageModel("Bella", "Desire", new switchCellPage(), "grapefruit.jpg"),
                new PageModel("Ben", "Chocolate", new switchCellPage(), "grapefruit.jpg")
            }
        }
        All = Groups; //set the publicly accessible list
}
```

Dans le code ci-dessus, nous pouvons `Add` également appeler sur `groups`des éléments de, qui sont `PageTypeGroup`des instances de type. Cela est possible, car `PageTypeGroup` hérite `List<PageModel>`. Il s’agit d’un exemple de la liste du modèle de listes indiqué ci-dessus.

Voici le XAML pour l’affichage de la liste groupée :

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="DemoListView.GroupingViewPage"
    <ContentPage.Content>
        <ListView  x:Name="GroupedView"
        GroupDisplayBinding="{Binding Title}"
        GroupShortNameBinding="{Binding ShortName}"
        IsGroupingEnabled="true">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Title}"
                     Detail="{Binding Subtitle}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </ContentPage.Content>
</ContentPage>
```

Voici le résultat :

![](customizing-list-appearance-images/grouping-depth.png "Exemple de regroupement de ListView")

Notez que nous avons :

- Définissez `GroupShortNameBinding` à la `ShortName` propriété définie dans notre classe de groupe
- Définissez `GroupDisplayBinding` à la `Title` propriété définie dans notre classe de groupe
- Définissez `IsGroupingEnabled` sur true
- Modifié le `ListView`de `ItemsSource` à la liste groupée

### <a name="customizing-grouping"></a>Personnalisation de regroupement

Si le regroupement a été activé dans la liste, l’en-tête de groupe peut également être personnalisé.

Similaire à la façon dont le `ListView` a un `ItemTemplate` pour définir la façon dont les lignes sont affichées, `ListView` a un `GroupHeaderTemplate`.

Voici un exemple de personnalisation de l’en-tête de groupe dans XAML :

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="DemoListView.GroupingViewPage">
    <ContentPage.Content>
        <ListView x:Name="GroupedView"
         GroupDisplayBinding="{Binding Title}"
         GroupShortNameBinding="{Binding ShortName}"
         IsGroupingEnabled="true">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Title}"
                    Detail="{Binding Subtitle}"
                    TextColor="#f35e20"
                    DetailColor="#503026" />
                </DataTemplate>
            </ListView.ItemTemplate>
            <!-- Group Header Customization-->
            <ListView.GroupHeaderTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Title}"
                    Detail="{Binding ShortName}"
                    TextColor="#f35e20"
                    DetailColor="#503026" />
                </DataTemplate>
            </ListView.GroupHeaderTemplate>
            <!-- End Group Header Customization -->
        </ListView>
    </ContentPage.Content>
</ContentPage>
```

<a name="Headers_and_Footers" />

## <a name="headers-and-footers"></a>En-têtes et pieds de page
Il est possible pour un ListView permet de présenter un en-tête et pied de page que faire défiler les éléments de la liste. L’en-tête et le pied de page peuvent être des chaînes de texte ou une mise en page plus complexe. Notez que ceci est distinct de [section groupes](#Grouping).

Vous pouvez définir le `Header` et/ou `Footer` une chaîne simple valeur, ou vous pouvez les définir à une mise en page plus complexe.
Il existe également `HeaderTemplate` et `FooterTemplate` propriétés qui vous permettent de créent des dispositions plus complexes pour l’en-tête et le pied de page cette prise en charge la liaison de données.

Pour créer un en-tête/pied de page simple, venez de définir les propriétés d’en-tête ou pied de page pour le texte que vous souhaitez afficher. En code :

```csharp
ListView HeaderList = new ListView() {
    Header = "Header",
    Footer = "Footer"
    };
```

Dans XAML :

```xaml
<ListView  x:Name="HeaderList"  Header="Header" Footer="Footer"></ListView>
```

![](customizing-list-appearance-images/header-default.png "ListView avec en-tête et pied de page")

Pour créer un en-tête personnalisé et le pied de page, définissez les vues de l’en-tête et pied de page :

```xaml
<ListView.Header>
    <StackLayout Orientation="Horizontal">
        <Label Text="Header"
        TextColor="Olive"
        BackgroundColor="Red" />
    </StackLayout>
</ListView.Header>
<ListView.Footer>
    <StackLayout Orientation="Horizontal">
        <Label Text="Footer"
        TextColor="Gray"
        BackgroundColor="Blue" />
    </StackLayout>
</ListView.Footer>
```

![](customizing-list-appearance-images/header-custom.png "ListView personnalisé d’en-tête et pied de page")

## <a name="scrollbar-visibility"></a>Visibilité de la barre de défilement

[`ListView`](xref:Xamarin.Forms.ListView)possède `HorizontalScrollBarVisibility` les `VerticalScrollBarVisibility` propriétés et, qui obtiennent ou définissent une [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) valeur qui représente le moment où la barre de défilement horizontale ou verticale est visible. Les deux propriétés peuvent être définies avec les valeurs suivantes:

- [`Default`](xref:Xamarin.Forms.ScrollBarVisibility)indique le comportement par défaut de la barre de défilement pour la plateforme, et est la `HorizontalScrollBarVisibility` valeur `VerticalScrollBarVisibility` par défaut pour les propriétés et.
- [`Always`](xref:Xamarin.Forms.ScrollBarVisibility)indique que les barres de défilement sont visibles, même lorsque le contenu s’ajuste à la vue.
- [`Never`](xref:Xamarin.Forms.ScrollBarVisibility)indique que les barres de défilement ne sont pas visibles, même si le contenu ne tient pas dans la vue.

<a name="Row_Separators" />

## <a name="row-separators"></a>Séparateurs de lignes
Lignes du séparateur sont affichés entre `ListView` éléments par défaut sur iOS et Android. Si vous préférez masquer les lignes du séparateur sur iOS et Android, définissez le `SeparatorVisibility` propriété sur votre ListView. Les options de `SeparatorVisibility` sont :

- **Par défaut** -affiche une ligne de séparation sur iOS et Android.
- **Aucun** -masque le séparateur sur toutes les plateformes.

Visibilité par défaut :

C# :

```csharp
SepratorDemoListView.SeparatorVisibility = SeparatorVisibility.Default;
```

XAML :

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorVisibility="Default" />
```

![](customizing-list-appearance-images/separator-default.png "ListView avec séparateurs de ligne par défaut")

None :

C# :

```csharp
SepratorDemoListView.SeparatorVisibility = SeparatorVisibility.None;
```

XAML :

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorVisibility="None" />
```

![](customizing-list-appearance-images/separator-none.png "ListView sans les séparateurs de lignes")

Vous pouvez également définir la couleur de la ligne de séparation via le `SeparatorColor` propriété :

C# :

```csharp
SepratorDemoListView.SeparatorColor = Color.Green;
```

XAML :

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorColor="Green" />
```

![](customizing-list-appearance-images/separator-custom.png "ListView avec séparateurs de lignes de vert")

> [!NOTE]
> Définition d’une de ces propriétés sur Android après le chargement du `ListView` entraîne une baisse des performances.

<a name="Row_Heights" />

## <a name="row-heights"></a>Les hauteurs de ligne
Toutes les lignes dans un ListView ont la même hauteur par défaut. ListView a deux propriétés qui peuvent être utilisées pour modifier ce comportement :

- `HasUnevenRows` &ndash; `true`/`false` valeur, les lignes ont des hauteurs variables si la valeur `true`. La valeur par défaut est `false`.
- `RowHeight` &ndash; définit la hauteur de chaque ligne lorsque `HasUnevenRows` est `false`.

Vous pouvez définir la hauteur de toutes les lignes en définissant le `RowHeight` propriété sur le `ListView`.

### <a name="custom-fixed-row-height"></a>Hauteur de ligne fixe personnalisé

C# :

```csharp
RowHeightDemoListView.RowHeight = 100;
```

XAML :

```xaml
<ListView x:Name="RowHeightDemoListView" RowHeight="100" />
```

![](customizing-list-appearance-images/height-custom.png "ListView avec la hauteur de ligne fixe")


### <a name="uneven-rows"></a>Lignes inégale

Si vous souhaitez disposer de différentes hauteurs des lignes individuelles, vous pouvez définir le `HasUnevenRows` propriété `true`.
Notez que les hauteurs de lignes ne doivent être défini manuellement une fois `HasUnevenRows` a été défini sur `true`, étant donné que les hauteurs seront calculées automatiquement par Xamarin.Forms.


C# :

```csharp
RowHeightDemoListView.HasUnevenRows = true;
```

XAML :

```xaml
<ListView x:Name="RowHeightDemoListView" HasUnevenRows="true" />
```

![](customizing-list-appearance-images/height-uneven.png "ListView avec des lignes inégale")

### <a name="runtime-resizing-of-rows"></a>Runtime redimensionnement des lignes

Individuels `ListView` lignes peuvent être redimensionnées par programme lors de l’exécution, à condition que le `HasUnevenRows` propriété est définie sur `true`. Le [ `Cell.ForceUpdateSize` ](xref:Xamarin.Forms.Cell.ForceUpdateSize) méthode met à jour taille d’une cellule, même lorsqu’il n’est pas actuellement visible, comme illustré dans l’exemple de code suivant :

```csharp
void OnImageTapped (object sender, EventArgs args)
{
    var image = sender as Image;
    var viewCell = image.Parent.Parent as ViewCell;

    if (image.HeightRequest < 250) {
        image.HeightRequest = image.Height + 100;
        viewCell.ForceUpdateSize ();
    }
}
```

Le `OnImageTapped` Gestionnaire d’événements est exécuté en réponse à une [ `Image` ](xref:Xamarin.Forms.Image) dans une cellule qui est activé par un clic et augmente la taille de la `Image` affiché dans la cellule afin qu’il est facilement affiché.

![](customizing-list-appearance-images/dynamic-row-resizing.png "ListView avec redimensionnement de la ligne de Runtime")

Notez qu’il existe un fort risque de dégradation des performances si cette fonctionnalité est utilisée de manière excessive.



## <a name="related-links"></a>Liens associés

- [Regroupement (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-grouping)
- [Affichage de convertisseur personnalisé (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)
- [Redimensionnement des lignes dynamiques (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-dynamicunevenlistcells)
- [notes de version 1.4](http://forums.xamarin.com/discussion/35451/xamarin-forms-1-4-0-released/)
- [notes de version 1.3](http://forums.xamarin.com/discussion/29934/xamarin-forms-1-3-0-released/)
