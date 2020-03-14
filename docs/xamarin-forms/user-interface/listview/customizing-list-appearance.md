---
title: Apparence de ListView
description: Cet article explique comment personnaliser les ListViews dans les applications Xamarin.Forms à l’aide des en-têtes, des pieds de page, des groupes et des cellules de hauteur variable.
ms.prod: xamarin
ms.assetid: DC8009B0-4371-4D60-885A-5362FC7EE3E5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/13/2018
ms.openlocfilehash: 90b0e0f3802ce766decb802c9406d72b5966360e
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304008"
---
# <a name="listview-appearance"></a>Apparence de ListView

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-grouping)

L' [`ListView`](xref:Xamarin.Forms.ListView) Xamarin. Forms vous permet de personnaliser la présentation de la liste, en plus des instances [`ViewCell`](xref:Xamarin.Forms.ViewCell) pour chaque ligne de la liste.

## <a name="grouping"></a>Regroupement

Les jeux de données volumineux peuvent devenir difficiles à manier lorsqu’ils sont présentés dans une liste de défilement continue. L’activation de regroupement peut améliorer l’expérience utilisateur dans ces cas en mieux organiser le contenu et en activant des contrôles spécifiques à la plateforme qui facilitent la navigation des données.

Lorsque le regroupement est activé pour un `ListView`, une ligne d’en-tête est ajoutée pour chaque groupe.

Pour activer le regroupement :

- Créer une liste de listes (une liste de groupes, chaque groupe en cours d’une liste d’éléments).
- Définissez la `ItemsSource` du `ListView`sur cette liste.
- Définissez `IsGroupingEnabled` sur True.
- Définissez [`GroupDisplayBinding`](xref:Xamarin.Forms.ListView.GroupDisplayBinding) pour qu’elle soit liée à la propriété des groupes qui est utilisée comme titre du groupe.
- Facultatif Définissez [`GroupShortNameBinding`](xref:Xamarin.Forms.ListView.GroupShortNameBinding) pour créer une liaison avec la propriété des groupes qui est utilisée comme nom abrégé du groupe. Le nom court est utilisé pour les listes de raccourcis (colonne de droite sur iOS).

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

Dans le code ci-dessus, `All` est la liste qui sera donnée à notre ListView comme source de liaison. `Title` et `ShortName` sont les propriétés qui seront utilisées pour les en-têtes de groupe.

À ce niveau, `All` est une liste vide. Ajoutez un constructeur statique afin que la liste contiendra au lancement du programme :

```csharp
static PageTypeGroup()
{
    List<PageTypeGroup> Groups = new List<PageTypeGroup> {
            new PageTypeGroup ("Alpha", "A"){
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
        };
        All = Groups; //set the publicly accessible list
}
```

Dans le code ci-dessus, nous pouvons également appeler `Add` sur des éléments de `Groups`, qui sont des instances de type `PageTypeGroup`. Cette méthode est possible, car `PageTypeGroup` hérite de `List<PageModel>`.

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

Ce code XAML effectue les actions suivantes :

- Définissez `GroupShortNameBinding` sur la propriété `ShortName` définie dans notre classe de groupe.
- Définissez `GroupDisplayBinding` sur la propriété `Title` définie dans notre classe de groupe.
- Affectez la valeur true à `IsGroupingEnabled`
- Modification du `ItemsSource` de l' `ListView`en liste groupée

La capture d’écran suivante montre l’interface utilisateur obtenue :

![](customizing-list-appearance-images/grouping-depth.png "ListView Grouping Example")

### <a name="customizing-grouping"></a>Personnalisation du regroupement

Si le regroupement a été activé dans la liste, l’en-tête de groupe peut également être personnalisé.

À l’instar de la façon dont le `ListView` a un `ItemTemplate` pour définir la manière dont les lignes sont affichées, `ListView` a un `GroupHeaderTemplate`.

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

## <a name="headers-and-footers"></a>En-têtes et pieds de page

Il est possible pour un ListView permet de présenter un en-tête et pied de page que faire défiler les éléments de la liste. L’en-tête et le pied de page peuvent être des chaînes de texte ou une mise en page plus complexe. Ce comportement est indépendant des [groupes de sections](#grouping).

Vous pouvez définir les `Header` et/ou `Footer` sur une valeur de `string`, ou vous pouvez les définir sur une disposition plus complexe. Il existe également des propriétés `HeaderTemplate` et `FooterTemplate` qui vous permettent de créer des dispositions plus complexes pour l’en-tête et le pied de page qui prennent en charge la liaison de données.

Pour créer un en-tête/pied de page de base, il vous suffit de définir les propriétés d’en-tête ou de pied de page sur le texte que vous souhaitez afficher. Dans le code :

```csharp
ListView HeaderList = new ListView()
{
    Header = "Header",
    Footer = "Footer"
};
```

Dans XAML :

```xaml
<ListView x:Name="HeaderList" 
          Header="Header"
          Footer="Footer">
    ...
</ListView>
```

![](customizing-list-appearance-images/header-default.png "ListView with Header and Footer")

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

![](customizing-list-appearance-images/header-custom.png "ListView with Customized Header and Footer")

## <a name="scrollbar-visibility"></a>Visibilité de la barre de défilement

La classe [`ListView`](xref:Xamarin.Forms.ListView) a des propriétés `HorizontalScrollBarVisibility` et `VerticalScrollBarVisibility`, qui obtiennent ou définissent une valeur [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) qui représente le moment où la barre de défilement horizontale ou verticale est visible. Les deux propriétés peuvent être définies avec les valeurs suivantes :

- [`Default`](xref:Xamarin.Forms.ScrollBarVisibility) indique le comportement par défaut de la barre de défilement pour la plateforme, et est la valeur par défaut pour les propriétés `HorizontalScrollBarVisibility` et `VerticalScrollBarVisibility`.
- [`Always`](xref:Xamarin.Forms.ScrollBarVisibility) indique que les barres de défilement sont visibles, même lorsque le contenu s’ajuste à la vue.
- [`Never`](xref:Xamarin.Forms.ScrollBarVisibility) indique que les barres de défilement ne sont pas visibles, même si le contenu ne tient pas dans la vue.

## <a name="row-separators"></a>Séparateurs de lignes

Les lignes de séparation sont affichées par défaut entre `ListView` éléments sur iOS et Android. Si vous préférez masquer les lignes de séparation sur iOS et Android, définissez la propriété `SeparatorVisibility` sur votre ListView. Les options pour `SeparatorVisibility` sont les suivantes :

- **Default** : affiche une ligne de séparation sur iOS et Android.
- **None** : masque le séparateur sur toutes les plateformes.

Visibilité par défaut :

C# :

```csharp
SeparatorDemoListView.SeparatorVisibility = SeparatorVisibility.Default;
```

XAML :

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorVisibility="Default" />
```

![](customizing-list-appearance-images/separator-default.png "ListView with Default Row Separators")

Aucune :

C# :

```csharp
SeparatorDemoListView.SeparatorVisibility = SeparatorVisibility.None;
```

XAML :

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorVisibility="None" />
```

![](customizing-list-appearance-images/separator-none.png "ListView without Row Separators")

Vous pouvez également définir la couleur de la ligne de séparation par le biais de la propriété `SeparatorColor` :

C# :

```csharp
SeparatorDemoListView.SeparatorColor = Color.Green;
```

XAML :

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorColor="Green" />
```

![](customizing-list-appearance-images/separator-custom.png "ListView with Green Row Separators")

> [!NOTE]
> La définition de l’une de ces propriétés sur Android après le chargement de la `ListView` entraîne une baisse importante des performances.

## <a name="row-height"></a>Hauteur de ligne

Toutes les lignes dans un ListView ont la même hauteur par défaut. ListView a deux propriétés qui peuvent être utilisées pour modifier ce comportement :

- `HasUnevenRows` &ndash; `true`/`false` valeur, les lignes ont des hauteurs variables si la valeur `true`est définie. La valeur par défaut est `false`.
- `RowHeight` &ndash; définit la hauteur de chaque ligne lorsque `HasUnevenRows` est `false`.

Vous pouvez définir la hauteur de toutes les lignes en définissant la propriété `RowHeight` sur l' `ListView`.

### <a name="custom-fixed-row-height"></a>Hauteur de ligne fixe personnalisée

C# :

```csharp
RowHeightDemoListView.RowHeight = 100;
```

XAML :

```xaml
<ListView x:Name="RowHeightDemoListView" RowHeight="100" />
```

![](customizing-list-appearance-images/height-custom.png "ListView with Fixed Row Height")

### <a name="uneven-rows"></a>Lignes irrégulières

Si vous souhaitez que les lignes individuelles aient des hauteurs différentes, vous pouvez définir la propriété `HasUnevenRows` sur `true`. Les hauteurs de lignes n’ont pas besoin d’être définies manuellement une fois `HasUnevenRows` a été défini sur `true`, car les hauteurs seront automatiquement calculées par Xamarin. Forms.

C# :

```csharp
RowHeightDemoListView.HasUnevenRows = true;
```

XAML :

```xaml
<ListView x:Name="RowHeightDemoListView" HasUnevenRows="true" />
```

![](customizing-list-appearance-images/height-uneven.png "ListView with Uneven Rows")

### <a name="resize-rows-at-runtime"></a>Redimensionner les lignes au moment de l’exécution

Les lignes de `ListView` individuelles peuvent être redimensionnées par programmation au moment de l’exécution, à condition que la propriété `HasUnevenRows` soit définie sur `true`. La méthode [`Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize) met à jour la taille d’une cellule, même lorsqu’elle n’est pas visible actuellement, comme illustré dans l’exemple de code suivant :

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

Le gestionnaire d’événements `OnImageTapped` est exécuté en réponse à une [`Image`](xref:Xamarin.Forms.Image) dans une cellule qui fait l’objet d’un clic, et augmente la taille de la `Image` affichée dans la cellule afin qu’elle soit facilement affichée.

![](customizing-list-appearance-images/dynamic-row-resizing.png "ListView with Runtime Row Resizing")

> [!WARNING]
> Une utilisation abusive du redimensionnement des lignes du runtime peut entraîner une dégradation des performances.

## <a name="related-links"></a>Liens connexes

- [Regroupement (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-grouping)
- [Vue du convertisseur personnalisé (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)
- [Redimensionnement dynamique des lignes (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-dynamicunevenlistcells)
- [Notes de publication 1,4](https://forums.xamarin.com/discussion/35451/xamarin-forms-1-4-0-released/)
- [Notes de publication 1,3](https://forums.xamarin.com/discussion/29934/xamarin-forms-1-3-0-released/)
