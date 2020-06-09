---
title : " Xamarin.Forms TableView" Description : "cet article explique comment utiliser la Xamarin.Forms classe TableView pour présenter des menus, des paramètres et des formulaires de saisie de défilement dans des applications.
ms. Prod : xamarin ms. AssetID : D1619D19-A74F-40DF-8E53-B1B7DFF7A3FB ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 09/25/2019 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-tableview"></a>Xamarin.FormsTableView

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-tableview)

[`TableView`](xref:Xamarin.Forms.TableView)est une vue qui permet d’afficher des listes déroulantes de données ou des choix où des lignes ne partagent pas le même modèle. Contrairement à [ListView](~/xamarin-forms/user-interface/listview/index.md), `TableView` n’a pas le concept de `ItemsSource` , de sorte que les éléments doivent être ajoutés manuellement comme enfants.

![Exemple TableView](tableview-images/tableview-all-sml.png)

## <a name="use-cases"></a>Cas d'utilisation

[`TableView`](xref:Xamarin.Forms.TableView)est utile dans les cas suivants :

- présentation d’une liste de paramètres
- la collecte de données dans un formulaire, ou
- affiche les données présentées différemment d’une ligne à l’autre (par exemple, nombres, pourcentages et images).

[`TableView`](xref:Xamarin.Forms.TableView)gère le défilement et la disposition des lignes dans des sections attrayantes, ce qui est souvent nécessaire pour les scénarios ci-dessus. Le `TableView` contrôle utilise la vue équivalente sous-jacente de chaque plateforme, le cas échéant, en créant une apparence native pour chaque plateforme.

## <a name="structure"></a>Structure

Les éléments d’un [`TableView`](xref:Xamarin.Forms.TableView) sont organisés en sections. À la racine du `TableView` est le [`TableRoot`](xref:Xamarin.Forms.TableRoot) , qui est parent à une ou plusieurs [`TableSection`](xref:Xamarin.Forms.TableSection) instances. Chaque [`TableSection`](xref:Xamarin.Forms.TableSection) se compose d’un en-tête et d’une ou plusieurs [`ViewCell`](xref:Xamarin.Forms.ViewCell) instances :

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

Le code C# équivalent est :

```csharp
Content = new TableView
{
    Root = new TableRoot
    {
        new TableSection("Ring")
        {
          // TableSection constructor takes title as an optional parameter
          new SwitchCell { Text = "New Voice Mail" },
          new SwitchCell { Text = "New Mail", On = true }
        }
    },
    Intent = TableIntent.Settings
};
```

## <a name="appearance"></a>Apparence

[`TableView`](xref:Xamarin.Forms.TableView)expose la [`Intent`](xref:Xamarin.Forms.TableView.Intent) propriété, qui peut être définie sur l’un des [`TableIntent`](xref:Xamarin.Forms.TableIntent) membres de l’énumération :

- `Data`: à utiliser lors de l’affichage des entrées de données. Notez que [ListView](~/xamarin-forms/user-interface/listview/index.md) peut être une meilleure option pour faire défiler des listes de données.
- `Form`: à utiliser lorsque l’TableView fait office de formulaire.
- `Menu`: à utiliser lors de la présentation d’un menu de sélections.
- `Settings`: à utiliser lors de l’affichage d’une liste de paramètres de configuration.

La [`TableIntent`](xref:Xamarin.Forms.TableIntent) valeur que vous choisissez peut avoir un impact sur la façon dont le [`TableView`](xref:Xamarin.Forms.TableView) s’affiche sur chaque plateforme. Même s’il n’y a pas de différences claires, il est recommandé de sélectionner le qui correspond le mieux à la `TableIntent` façon dont vous envisagez d’utiliser la table.

En outre, la couleur du texte affiché pour chaque [`TableSection`](xref:Xamarin.Forms.TableSection) peut être modifiée en affectant à la propriété la valeur `TextColor` [`Color`](xref:Xamarin.Forms.Color) .

## <a name="built-in-cells"></a>Cellules intégrées

Xamarin.Formsest fourni avec des cellules intégrées pour la collecte et l’affichage d’informations. Bien que [`ListView`](xref:Xamarin.Forms.ListView) et [`TableView`](xref:Xamarin.Forms.TableView) puissent utiliser toutes les mêmes cellules, [`SwitchCell`](xref:Xamarin.Forms.SwitchCell) et [`EntryCell`](xref:Xamarin.Forms.EntryCell) sont les plus pertinents pour un `TableView` scénario.

Consultez [apparence des cellules ListView](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md) pour obtenir une description détaillée de [TextCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#textcell) et [ImageCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#imagecell).

### <a name="switchcell"></a>SwitchCell

[`SwitchCell`](xref:Xamarin.Forms.SwitchCell)est le contrôle à utiliser pour la présentation et la capture d’une activation ou d’une désactivation ou d’un `true` / `false` État. Il définit les propriétés suivantes :

- `Text`: texte à afficher à côté du commutateur.
- `On`: indique si le commutateur s’affiche comme activé ou désactivé.
- `OnColor`: le [`Color`](xref:Xamarin.Forms.Color) du commutateur lorsqu’il se trouve à l’emplacement.

Toutes ces propriétés peuvent être liées.

[`SwitchCell`](xref:Xamarin.Forms.SwitchCell)expose également l' `OnChanged` événement, ce qui vous permet de répondre aux modifications de l’état de la cellule.

![Exemple SwitchCell](tableview-images/switch-cell.png)

### <a name="entrycell"></a>EntryCell

[`EntryCell`](xref:Xamarin.Forms.EntryCell)est utile lorsque vous devez afficher des données texte que l’utilisateur peut modifier. Il définit les propriétés suivantes :

- `Keyboard`: Clavier à afficher pendant la modification. Il existe des options pour les valeurs numériques, les courriers électroniques, les numéros de téléphone, etc., [consultez les documents de l’API](xref:Xamarin.Forms.Keyboard).
- `Label`: Texte de l’étiquette à afficher à gauche du champ d’entrée de texte.
- `LabelColor`: Couleur du texte de l’étiquette.
- `Placeholder`: Texte à afficher dans le champ d’entrée lorsqu’il est null ou vide. Ce texte disparaît à la début de la saisie de texte.
- `Text`: Texte du champ d’entrée.
- `HorizontalTextAlignment`: Alignement horizontal du texte. Les valeurs sont centrées, gauches ou alignées à droite. [Consultez les documents de l’API](xref:Xamarin.Forms.TextAlignment).
- `VerticalTextAlignment`: Alignement vertical du texte. Les valeurs sont `Start` , `Center` ou `End` .

[`EntryCell`](xref:Xamarin.Forms.EntryCell)expose également l' `Completed` événement, qui est déclenché quand l’utilisateur appuie sur le bouton « Done » sur le clavier lors de la modification de texte.

![Exemple EntryCell](tableview-images/entry-cell.png)

## <a name="custom-cells"></a>Cellules personnalisées

Lorsque les cellules intégrées ne sont pas suffisantes, des cellules personnalisées peuvent être utilisées pour présenter et capturer des données de la manière la plus appropriée pour votre application. Par exemple, vous pouvez présenter un curseur pour permettre à un utilisateur de choisir l’opacité d’une image.

Toutes les cellules personnalisées doivent dériver de [`ViewCell`](xref:Xamarin.Forms.ViewCell) , la même classe de base que tous les types de cellules intégrés utilisent.

Voici un exemple de cellule personnalisée :

![Exemple de cellule personnalisée](tableview-images/custom-cell.png)

L’exemple suivant montre le code XAML utilisé pour créer [`TableView`](xref:Xamarin.Forms.TableView) dans les captures d’écran ci-dessus :

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DemoTableView.TablePage"
             Title="TableView">
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
</ContentPage>
```

Le code C# équivalent est :

```csharp
var table = new TableView();
table.Intent = TableIntent.Settings;
var layout = new StackLayout() { Orientation = StackOrientation.Horizontal };
layout.Children.Add (new Image() { Source = "bulb.png"});
layout.Children.Add (new Label()
{
    Text = "left",
    TextColor = Color.FromHex("#f35e20"),
    VerticalOptions = LayoutOptions.Center
});
layout.Children.Add (new Label ()
{
    Text = "right",
    TextColor = Color.FromHex ("#503026"),
    VerticalOptions = LayoutOptions.Center,
    HorizontalOptions = LayoutOptions.EndAndExpand
});
table.Root = new TableRoot ()
{
    new TableSection("Getting Started")
    {
        new ViewCell() {View = layout}
    }
};
Content = table;
```

L’élément racine sous [`TableView`](xref:Xamarin.Forms.TableView) est le [`TableRoot`](xref:Xamarin.Forms.TableRoot) , et il est [`TableSection`](xref:Xamarin.Forms.TableSection) immédiatement sous `TableRoot` . [`ViewCell`](xref:Xamarin.Forms.ViewCell)Est défini directement sous le `TableSection` , et un [`StackLayout`](xref:Xamarin.Forms.StackLayout) est utilisé pour gérer la disposition de la cellule personnalisée, bien que toute disposition puisse être utilisée ici.

> [!NOTE]
> Contrairement [`ListView`](xref:Xamarin.Forms.ListView) [`TableView`](xref:Xamarin.Forms.TableView) à, ne nécessite pas que les cellules personnalisées (ou any) soient définies dans un `ItemTemplate` .

## <a name="row-height"></a>Hauteur de ligne

La [`TableView`](xref:Xamarin.Forms.TableView) classe a deux propriétés qui peuvent être utilisées pour modifier la hauteur de ligne des cellules :

- [`RowHeight`](xref:Xamarin.Forms.TableView.RowHeight): définit la hauteur de chaque ligne sur un `int` .
- [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows): les lignes ont des hauteurs variables si la valeur est `true` . Notez que lors de l’affectation de la valeur à cette propriété `true` , les hauteurs de lignes seront automatiquement calculées et appliquées par Xamarin.Forms .

Lorsque la hauteur du contenu dans une cellule d’un [`TableView`](xref:Xamarin.Forms.TableView) est modifiée, la hauteur de ligne est implicitement mise à jour sur Android et le plateforme Windows universelle (UWP). Toutefois, sur iOS, la mise à jour doit être forcée en affectant à la propriété la valeur [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows) `true` et en appelant la [`Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize) méthode.

L’exemple de code XAML suivant montre un [`TableView`](xref:Xamarin.Forms.TableView) qui contient un [`ViewCell`](xref:Xamarin.Forms.ViewCell) :

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

Lorsque le [`ViewCell`](xref:Xamarin.Forms.ViewCell) est frappé, le `OnViewCellTapped` Gestionnaire d’événements est exécuté :

```csharp
void OnViewCellTapped(object sender, EventArgs e)
{
    _target.IsVisible = !_target.IsVisible;
    _viewCell.ForceUpdateSize();
}
```

Le `OnViewCellTapped` Gestionnaire d’événements affiche ou masque le deuxième [`Label`](xref:Xamarin.Forms.Label) dans le [`ViewCell`](xref:Xamarin.Forms.ViewCell) et met à jour explicitement la taille de la cellule en appelant la [`Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize) méthode.

Les captures d’écran suivantes montrent la cellule avant d’être exploitée :

![ViewCell avant le redimensionnement](tableview-images/cell-beforeresize.png)

Les captures d’écran suivantes montrent la cellule après avoir frappé sur :

![ViewCell après avoir été redimensionné](tableview-images/cell-afterresize.png)

> [!IMPORTANT]
> Si cette fonctionnalité est surutilisée, il existe une forte probabilité de dégradation des performances.

## <a name="related-links"></a>Liens connexes

- [TableView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-tableview)
