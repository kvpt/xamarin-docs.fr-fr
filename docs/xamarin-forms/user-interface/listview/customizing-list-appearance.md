---
title: Apparence de ListView
description: Cet article explique comment personnaliser les ListViews dans les applications Xamarin.Forms à l’aide des en-têtes, des pieds de page, des groupes et des cellules de hauteur variable.
ms.prod: xamarin
ms.assetid: DC8009B0-4371-4D60-885A-5362FC7EE3E5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/13/2018
ms.openlocfilehash: 62073f624c37a48baa49453d7bdd1e0b849013cd
ms.sourcegitcommit: a5ef4497db04dfa016865bc7454b3de6ff088554
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2019
ms.locfileid: "70998169"
---
# <a name="listview-appearance"></a>Apparence de ListView

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-grouping)

Xamarin. Forms [`ListView`](xref:Xamarin.Forms.ListView) vous permet de personnaliser la présentation de la liste, en plus [`ViewCell`](xref:Xamarin.Forms.ViewCell) des instances de chaque ligne de la liste.

## <a name="grouping"></a>Regroupement

Les jeux de données volumineux peuvent devenir difficiles à manier lorsqu’ils sont présentés dans une liste de défilement continue. L’activation de regroupement peut améliorer l’expérience utilisateur dans ces cas en mieux organiser le contenu et en activant des contrôles spécifiques à la plateforme qui facilitent la navigation des données.

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
        }
        All = Groups; //set the publicly accessible list
}
```

Dans le code ci-dessus, nous pouvons `Add` également appeler sur `Groups`des éléments de, qui sont `PageTypeGroup`des instances de type. Cette méthode est possible, `PageTypeGroup` car hérite `List<PageModel>`de.

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

- Définissez `GroupShortNameBinding` à la `ShortName` propriété définie dans notre classe de groupe
- Définissez `GroupDisplayBinding` à la `Title` propriété définie dans notre classe de groupe
- Définissez `IsGroupingEnabled` sur true
- Modifié le `ListView`de `ItemsSource` à la liste groupée

La capture d’écran suivante montre l’interface utilisateur obtenue :

![](customizing-list-appearance-images/grouping-depth.png "Exemple de regroupement de ListView")

### <a name="customizing-grouping"></a>Personnalisation du regroupement

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

## <a name="headers-and-footers"></a>En-têtes et pieds de page

Il est possible pour un ListView permet de présenter un en-tête et pied de page que faire défiler les éléments de la liste. L’en-tête et le pied de page peuvent être des chaînes de texte ou une mise en page plus complexe. Ce comportement est indépendant des [groupes de sections](#grouping).

Vous pouvez définir les `Header` valeurs et/ `Footer` ou sur `string` une valeur, ou vous pouvez les définir sur une disposition plus complexe. Il existe également `HeaderTemplate` et `FooterTemplate` propriétés qui vous permettent de créent des dispositions plus complexes pour l’en-tête et le pied de page cette prise en charge la liaison de données.

Pour créer un en-tête/pied de page de base, il vous suffit de définir les propriétés d’en-tête ou de pied de page sur le texte que vous souhaitez afficher. En code :

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

La [`ListView`](xref:Xamarin.Forms.ListView) classe possède `HorizontalScrollBarVisibility` des `VerticalScrollBarVisibility` propriétés et, qui obtiennent ou définissent une [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) valeur qui représente le moment où la barre de défilement horizontale ou verticale est visible. Les deux propriétés peuvent être définies avec les valeurs suivantes :

- [`Default`](xref:Xamarin.Forms.ScrollBarVisibility)indique le comportement par défaut de la barre de défilement pour la plateforme, et est la `HorizontalScrollBarVisibility` valeur `VerticalScrollBarVisibility` par défaut pour les propriétés et.
- [`Always`](xref:Xamarin.Forms.ScrollBarVisibility)indique que les barres de défilement sont visibles, même lorsque le contenu s’ajuste à la vue.
- [`Never`](xref:Xamarin.Forms.ScrollBarVisibility)indique que les barres de défilement ne sont pas visibles, même si le contenu ne tient pas dans la vue.

## <a name="row-separators"></a>Séparateurs de lignes

Lignes du séparateur sont affichés entre `ListView` éléments par défaut sur iOS et Android. Si vous préférez masquer les lignes du séparateur sur iOS et Android, définissez le `SeparatorVisibility` propriété sur votre ListView. Les options de `SeparatorVisibility` sont :

- **Par défaut** -affiche une ligne de séparation sur iOS et Android.
- **Aucun** -masque le séparateur sur toutes les plateformes.

Visibilité par défaut :

C# :

```csharp
SeparatorDemoListView.SeparatorVisibility = SeparatorVisibility.Default;
```

XAML :

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorVisibility="Default" />
```

![](customizing-list-appearance-images/separator-default.png "ListView avec séparateurs de ligne par défaut")

None :

C# :

```csharp
SeparatorDemoListView.SeparatorVisibility = SeparatorVisibility.None;
```

XAML :

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorVisibility="None" />
```

![](customizing-list-appearance-images/separator-none.png "ListView sans les séparateurs de lignes")

Vous pouvez également définir la couleur de la ligne de séparation via le `SeparatorColor` propriété :

C# :

```csharp
SeparatorDemoListView.SeparatorColor = Color.Green;
```

XAML :

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorColor="Green" />
```

![](customizing-list-appearance-images/separator-custom.png "ListView avec séparateurs de lignes de vert")

> [!NOTE]
> Définition d’une de ces propriétés sur Android après le chargement du `ListView` entraîne une baisse des performances.

## <a name="row-height"></a>Hauteur de ligne

Toutes les lignes dans un ListView ont la même hauteur par défaut. ListView a deux propriétés qui peuvent être utilisées pour modifier ce comportement :

- `HasUnevenRows` &ndash; `true`/`false` valeur, les lignes ont des hauteurs variables si la valeur `true`. La valeur par défaut est `false`.
- `RowHeight` &ndash; définit la hauteur de chaque ligne lorsque `HasUnevenRows` est `false`.

Vous pouvez définir la hauteur de toutes les lignes en définissant le `RowHeight` propriété sur le `ListView`.

### <a name="custom-fixed-row-height"></a>Hauteur de ligne fixe personnalisée

C# :

```csharp
RowHeightDemoListView.RowHeight = 100;
```

XAML :

```xaml
<ListView x:Name="RowHeightDemoListView" RowHeight="100" />
```

![](customizing-list-appearance-images/height-custom.png "ListView avec la hauteur de ligne fixe")

### <a name="uneven-rows"></a>Lignes irrégulières

Si vous souhaitez disposer de différentes hauteurs des lignes individuelles, vous pouvez définir le `HasUnevenRows` propriété `true`. Les hauteurs de lignes n’ont pas besoin `HasUnevenRows` d’être définies manuellement `true`une fois définie sur, car les hauteurs seront automatiquement calculées par Xamarin. Forms.

C# :

```csharp
RowHeightDemoListView.HasUnevenRows = true;
```

XAML :

```xaml
<ListView x:Name="RowHeightDemoListView" HasUnevenRows="true" />
```

![](customizing-list-appearance-images/height-uneven.png "ListView avec des lignes inégale")

### <a name="resize-rows-at-runtime"></a>Redimensionner les lignes au moment de l’exécution

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

> [!WARNING]
> Une utilisation abusive du redimensionnement des lignes du runtime peut entraîner une dégradation des performances.

## <a name="related-links"></a>Liens connexes

- [Regroupement (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-grouping)
- [Affichage de convertisseur personnalisé (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)
- [Redimensionnement des lignes dynamiques (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-dynamicunevenlistcells)
- [notes de version 1.4](http://forums.xamarin.com/discussion/35451/xamarin-forms-1-4-0-released/)
- [notes de version 1.3](http://forums.xamarin.com/discussion/29934/xamarin-forms-1-3-0-released/)
