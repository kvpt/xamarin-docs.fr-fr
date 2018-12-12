---
title: Xamarin.Forms TableView
description: Cet article explique comment utiliser la classe Xamarin.Forms TableView pour présenter des menus déroulants, les paramètres et les formulaires de saisie dans les applications.
ms.prod: xamarin
ms.assetid: D1619D19-A74F-40DF-8E53-B1B7DFF7A3FB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/04/2018
ms.openlocfilehash: 0561c2a08aa96755d6d6caa9a3c683a5ce83ae10
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53053984"
---
# <a name="xamarinforms-tableview"></a>Xamarin.Forms TableView

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/TableView)

[TableView](xref:Xamarin.Forms.TableView) est une vue pour afficher des listes permettant le défilement de données ou des choix où il existe des lignes qui ne partagent pas le même modèle. Contrairement aux [ListView](~/xamarin-forms/user-interface/listview/index.md), TableView n’a pas le concept d’un `ItemsSource`, de sorte que les éléments doivent être ajoutés manuellement en tant qu’enfants.

Ce guide est composé des sections suivantes :

- **[Cas d’usage](#Use_Cases)**  &ndash; quand utiliser TableView au lieu de ListView ou une vue personnalisée.
- **[Structure de TableView](#TableView_Structure)**  &ndash; la hiérarchie des vues qui est nécessaire au sein d’un objet TableView.
- **[Apparence de TableView](#TableView_Appearance)**  &ndash; les options de personnalisation pour TableView.
- **[Cellules intégrés](#Built-In_Cells)**  &ndash; options cellule intégrés, y compris [EntryCell](#EntryCell) et [SwitchCell](#SwitchCell).
- **[Cellules personnalisés](#Custom_Cells)**  &ndash; comment rendre votre propre cellules personnalisés.

![](tableview-images/tableview-all-sml.png "Exemple de TableView")

<a name="Use_Cases" />

## <a name="use-cases"></a>Cas d’usage

TableView est utile dans les cas suivants :

- présentation d’une liste de paramètres,
- collecte des données dans un formulaire, ou
- affichage des données présentées différemment à partir de la ligne à ligne (par exemple, nombres, des pourcentages et images).

TableView gère le défilement et la disposition des lignes dans les sections attrayantes, un besoin commun pour les scénarios ci-dessus. Le `TableView` contrôle utilise de chaque plateforme sous-jacent vue équivalente lorsqu’il est disponible, la création d’une apparence native pour chaque plateforme.

<a name="TableView_Structure" />

## <a name="tableview-structure"></a>Structure de TableView

Éléments dans un `TableView` sont organisées en sections. À la racine de la `TableView` est la `TableRoot`, qui est parent à un ou plusieurs `TableSections`:

```csharp
Content = new TableView {
    Root = new TableRoot {
        new TableSection...
    },
    Intent = TableIntent.Settings
};
```

Chaque `TableSection` se compose d’un titre et un ou plusieurs ViewCells. Nous voyons ici le `TableSection`de `Title` propriété définie sur *« Anneau »* dans le constructeur :

```csharp
var section = new TableSection ("Ring") { //TableSection constructor takes title as an optional parameter
    new SwitchCell {Text = "New Voice Mail"},
    new SwitchCell {Text = "New Mail", On = true}
};
```

Pour accomplir la même disposition dans la configuration ci-dessus dans XAML :

```xaml
<TableView Intent="Settings">
    <TableRoot>
        <TableSection Title="Ring">
            <SwitchCell Text="New Voice Mail" />
      <SwitchCell Text="New Mail" On="true" />
        </TableSection>
    </TableRoot>
</TableView>
```

<a name="TableView_Appearance" />

## <a name="tableview-appearance"></a>TableView apparence

TableView expose le `Intent` propriété, qui est une énumération des options suivantes :

- **Données** &ndash; pour une utilisation lors de l’affichage des entrées de données. Notez que [ListView](~/xamarin-forms/user-interface/listview/index.md) peut être une meilleure option pour le défilement des listes de données.
- **Formulaire** &ndash; à utiliser lorsque le TableView agit comme un formulaire.
- **Menu** &ndash; pour une utilisation lors de la présentation d’un menu des sélections.
- **Paramètres** &ndash; pour une utilisation lors de l’affichage d’une liste de paramètres de configuration.

Le `TableIntent` vous choisissez peut affecter la `TableView` s’affiche sur chaque plateforme. Même s’il existe n'efface pas les différences, il est recommandé de sélectionner le `TableIntent` qui correspond le mieux à comment vous avez l’intention d’utiliser la table.

<a name="Built-In_Cells" />

## <a name="built-in-cells"></a>Cellules intégrés

Xamarin.Forms est fourni avec les cellules intégrées pour collecter et afficher des informations. Bien que ListView et TableView peuvent utiliser toutes les cellules de mêmes, les éléments suivants sont les plus pertinentes pour un scénario TableView :

- **SwitchCell** &ndash; de présentation et de capturer un état true/false, ainsi que d’une étiquette de texte.
- **EntryCell** &ndash; de présentation et de capturer le texte.

Consultez [apparence d’une cellule ListView](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md) pour une description détaillée de [TextCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#TextCell) et [ImageCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#ImageCell).

<a name="switchcell" />

### <a name="switchcell"></a>SwitchCell
[`SwitchCell`](xref:Xamarin.Forms.SwitchCell) est le contrôle à utiliser pour présenter et capture un activé/désactivé ou `true` / `false` état.

SwitchCells ont une seule ligne de texte à modifier et d’une propriété activé/désactivé. Ces deux propriétés peuvent être liés.

- `Text` &ndash; texte à afficher à côté du commutateur.
- `On` &ndash; Indique si le commutateur est affiché en tant qu’on ou off.

Notez que le `SwitchCell` expose le `OnChanged` événement, ce qui vous permet de répondre aux modifications de l’état de la cellule.

![](tableview-images/switch-cell.png "Exemple de SwitchCell")

<a name="entrycell" />

### <a name="entrycell"></a>EntryCell
[`EntryCell`](xref:Xamarin.Forms.EntryCell) est utile lorsque vous avez besoin afficher des données de texte que l’utilisateur peut modifier. `EntryCell`s offrent les propriétés suivantes qui peuvent être personnalisées :

- `Keyboard` &ndash; Le clavier à afficher lors de la modification. Il existe des options pour les éléments tels que des valeurs numériques, e-mail, numéros de téléphone, etc. [Consultez la documentation sur les API](xref:Xamarin.Forms.Keyboard).
- `Label` &ndash; Le texte de l’étiquette à afficher à droite de la zone de texte.
- `LabelColor` &ndash; La couleur du texte d’étiquette.
- `Placeholder` &ndash; Texte à afficher dans le champ d’entrée lorsqu’il est null ou vide. Ce texte disparaît lorsque l’entrée de texte commence.
- `Text` &ndash; Le texte dans le champ d’entrée.
- `HorizontalTextAlignment` &ndash; L’alignement horizontal du texte. Peut être, gauche ou droite centré. [Consultez la documentation sur les API](xref:Xamarin.Forms.TextAlignment).

Notez que `EntryCell` expose le `Completed` événement, qui est déclenché quand l’utilisateur appuie sur « terminé » sur le clavier lors de la modification de texte.

![](tableview-images/entry-cell.png "Exemple de EntryCell")

<a name="Custom_Cells" />

## <a name="custom-cells"></a>Cellules personnalisées
Les cellules intégrées ne suffisent pas, les cellules personnalisés permet de présenter et de capturer des données de la façon appropriée pour votre application. Par exemple, vous souhaiterez présenter un curseur pour autoriser un utilisateur de choisir l’opacité d’une image.

Toutes les cellules personnalisés doivent dériver de [ `ViewCell` ](xref:Xamarin.Forms.ViewCell), la même classe de base que tous de la cellule intégrée types utilisent.

Il s’agit d’un exemple d’une cellule personnalisée :

![](tableview-images/custom-cell.png "Exemple de cellule personnalisée")

### <a name="xaml"></a>XAML
Le XAML pour créer la disposition ci-dessus est la suivante :

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="DemoTableView.TablePage" Title="TableView">
    <ContentPage.Content>
        <TableView Intent="Settings">
            <TableRoot>
                <TableSection Title="Getting Started">
                    <ViewCell>
                        <StackLayout Orientation="Horizontal">
                            <Image Source="bulb.png" />
                            <Label Text="left"
                              TextColor="#f35e20" />
                            <Label Text="right"
                              HorizontalOptions="EndAndExpand"
                              TextColor="#503026" />
                        </StackLayout>
                    </ViewCell>
                </TableSection>
            </TableRoot>
        </TableView>
    </ContentPage.Content>
</ContentPage>

```

Le XAML ci-dessus fait beaucoup. Nous allons décomposer :

- L’élément racine sous le `TableView` est le `TableRoot`.
- Il existe un `TableSection` immédiatement en dessous de la `TableRoot`.
- Le `ViewCell` est défini directement sous la valeur TableSection. Contrairement aux `ListView`, `TableView` ne nécessite pas que personnalisé (ou toute) cellules sont définies dans un `ItemTemplate`.
- Un StackLayout est utilisé pour gérer la disposition de la cellule personnalisée. Toute disposition peut être utilisée ici.

### <a name="cnum"></a>C&num;

Étant donné que `TableView` fonctionne avec les données statiques ou des données sont modifiées manuellement, il n’a pas le concept d’un modèle d’élément. Au lieu de cela, les cellules personnalisées peuvent être manuellement créés et placés dans la table. Notez que la technique de création personnalisé de cellule qui hérite de `ViewCell`, puis en ajoutant à la `TableView` comme vous le feriez une cellule intégrée, est également pris en charge.
Voici le code c# pour accomplir la disposition ci-dessus :

```csharp
var table = new TableView();
table.Intent = TableIntent.Settings;
var layout = new StackLayout() { Orientation = StackOrientation.Horizontal };
layout.Children.Add (new Image() {Source = "bulb.png"});
layout.Children.Add (new Label() {
    Text = "left",
    TextColor = Color.FromHex("#f35e20"),
    VerticalOptions = LayoutOptions.Center
});
layout.Children.Add (new Label () {
    Text = "right",
    TextColor = Color.FromHex ("#503026"),
    VerticalOptions = LayoutOptions.Center,
    HorizontalOptions = LayoutOptions.EndAndExpand
});
table.Root = new TableRoot () {
    new TableSection("Getting Started") {
        new ViewCell() {View = layout}
    }
};

Content = table;
```

C# ci-dessus fait beaucoup. Nous allons décomposer :

- L’élément racine sous le `TableView` est le `TableRoot`.
- Il existe un `TableSection` immédiatement en dessous de la `TableRoot`.
- Le `ViewCell` est défini directement sous la valeur TableSection. Contrairement aux `ListView`, `TableView` ne nécessite pas que personnalisé (ou toute) cellules sont définies dans un `ItemTemplate`.
- Un StackLayout est utilisé pour gérer la disposition de la cellule personnalisée. Toute disposition peut être utilisée ici.

Notez qu’une classe pour la cellule personnalisée n’est jamais définie. Au lieu de cela, le `ViewCell`de propriété de la vue est définie pour une instance particulière de `ViewCell`.

## <a name="row-height"></a>Hauteur de ligne

Le [ `TableView` ](xref:Xamarin.Forms.TableView) classe a deux propriétés qui peuvent être utilisées pour modifier la hauteur des cellules :

- [`RowHeight`](xref:Xamarin.Forms.TableView.RowHeight) : définit la hauteur de chaque ligne à un `int`.
- [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows) – les lignes ont différentes hauteurs si la valeur `true`. Notez que lorsque vous définissez cette propriété pour `true`, hauteurs de lignes seront automatiquement calculées et appliqués par Xamarin.Forms.

Lorsque la hauteur du contenu dans une cellule dans un [ `TableView` ](xref:Xamarin.Forms.TableView) est modifié, la ligne hauteur est implicitement mis à jour sur Android et la plateforme universelle Windows (UWP). Toutefois, sur iOS il doit être forcé à mettre à jour en définissant le [ `HasUnevenRows` ](xref:Xamarin.Forms.TableView.HasUnevenRows) propriété `true` et en appelant le [ `Cell.ForceUpdateSize` ](xref:Xamarin.Forms.Cell.ForceUpdateSize) (méthode).

L’exemple XAML suivant montre un [ `TableView` ](xref:Xamarin.Forms.TableView) qui contient un [ `ViewCell` ](xref:Xamarin.Forms.ViewCell):

```xaml
<ContentPage ...>
    <TableView ...
               HasUnevenRows="true">
        <TableRoot>
            ...
            <TableSection ...>
                ...
                <ViewCell x:Name="_viewCell"
                          Tapped="OnViewCellTapped">
                    <Grid Margin="15,0">
                        <Grid.RowDefinitions>
                            <RowDefinition Height="Auto" />
                            <RowDefinition Height="Auto" />
                        </Grid.RowDefinitions>
                        <Label Text="Tap this cell." />
                        <Label x:Name="_target"
                               Grid.Row="1"
                               Text="The cell has changed size."
                               IsVisible="false" />
                    </Grid>
                </ViewCell>
            </TableSection>
        </TableRoot>
    </TableView>
</ContentPage>
```

Lorsque le [ `ViewCell` ](xref:Xamarin.Forms.ViewCell) est activé par un clic, le `OnViewCellTapped` Gestionnaire d’événements est exécuté :

```csharp
void OnViewCellTapped(object sender, EventArgs e)
{
    _target.IsVisible = !_target.IsVisible;
    _viewCell.ForceUpdateSize();
}
```

Le `OnViewCellTapped` Gestionnaire d’événements affiche ou masque la seconde [ `Label` ](xref:Xamarin.Forms.Label) dans le [ `ViewCell` ](xref:Xamarin.Forms.ViewCell)et met à jour explicitement de taille de la cellule en appelant le [ `Cell.ForceUpdateSize` ](xref:Xamarin.Forms.Cell.ForceUpdateSize) (méthode).

Les captures d’écran suivantes montrent la cellule avant d’être mis sur écoute sur :

![](tableview-images/cell-beforeresize.png "ViewCell avant d’en cours de redimensionnement")

Les captures d’écran suivantes montrent la cellule après être mis sur écoute sur :

![](tableview-images/cell-afterresize.png "ViewCell après redimensionnement")

> [!IMPORTANT]
> Il est fort probable de dégradation des performances si cette fonctionnalité est utilisée de manière excessive.

## <a name="related-links"></a>Liens associés

- [TableView (exemple)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/TableView)
