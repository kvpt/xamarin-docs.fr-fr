---
title: ''
description: Cet article explique comment ajouter une interactivité à un Xamarin.Forms ListView en implémentant des sélections, des actions de contexte et une extraction à l’aide d’une extraction.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5142965216b328172ae7fa04cdc0c13590f5ff38
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139884"
---
# <a name="listview-interactivity"></a>Interactivité de ListView

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)

La Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) classe prend en charge l’interaction de l’utilisateur avec les données qu’il présente.

## <a name="selection-and-taps"></a>Sélection et clics

Le [`ListView`](xref:Xamarin.Forms.ListView) mode de sélection est contrôlé en affectant [`ListView.SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) à la propriété une valeur de l' [`ListViewSelectionMode`](xref:Xamarin.Forms.ListViewSelectionMode) énumération :

- [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single)indique qu’un seul élément peut être sélectionné, avec l’élément sélectionné mis en surbrillance. Il s’agit de la valeur par défaut.
- [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None)indique que les éléments ne peuvent pas être sélectionnés.

Lorsqu’un utilisateur clique sur un élément, deux événements sont déclenchés :

- [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected)se déclenche lorsqu’un nouvel élément est sélectionné.
- [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped)se déclenche lorsqu’un élément est frappé.

Le double-clic sur le même élément déclenche deux [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) événements, mais ne déclenche qu’un seul [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) événement.

> [!NOTE]
> La [`ItemTappedEventArgs`](xref:Xamarin.Forms.ItemTappedEventArgs) classe, qui contient les arguments d’événement pour l' [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) événement, possède les [`Group`](xref:Xamarin.Forms.ItemTappedEventArgs.Group) Propriétés et et [`Item`](xref:Xamarin.Forms.ItemTappedEventArgs.Item) une `ItemIndex` propriété dont la valeur représente l’index dans le [`ListView`](xref:Xamarin.Forms.ListView) de l’élément taraudé. De même, la [`SelectedItemChangedEventArgs`](xref:Xamarin.Forms.SelectedItemChangedEventArgs) classe, qui contient les arguments d’événement pour l' [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) événement, possède une [`SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem) propriété et une `SelectedItemIndex` propriété dont la valeur représente l’index dans le `ListView` de l’élément sélectionné.

Lorsque la [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) propriété a la valeur [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single) , les éléments dans [`ListView`](xref:Xamarin.Forms.ListView) peuvent être sélectionnés, les [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) événements et sont déclenchés, et la [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) propriété est définie sur la valeur de l’élément sélectionné.

Lorsque la [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) propriété a la valeur [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None) , les éléments dans [`ListView`](xref:Xamarin.Forms.ListView) ne peuvent pas être sélectionnés, l’événement n’est [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) pas déclenché et la [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) propriété est conservée `null` . Toutefois, [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) les événements sont toujours déclenchés et l’élément taraudé est brièvement mis en surbrillance pendant le TAP.

Lorsqu’un élément a été sélectionné et que la [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) propriété est modifiée de [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single) en [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None) , la [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) propriété aura la valeur `null` et l' [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) événement sera déclenché avec un `null` élément.

Les captures d’écran suivantes montrent un [`ListView`](xref:Xamarin.Forms.ListView) avec le mode de sélection par défaut :

![](interactivity-images/selection-default.png "ListView with Selection Enabled")

### <a name="disable-selection"></a>Désactiver la sélection

Pour désactiver [`ListView`](xref:Xamarin.Forms.ListView) la sélection, affectez à la propriété la valeur [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None) :

```xaml
<ListView ... SelectionMode="None" />
```

```csharp
var listView = new ListView { ... SelectionMode = ListViewSelectionMode.None };
```

## <a name="context-actions"></a>Actions contextuelles

Souvent, les utilisateurs souhaitent agir sur un élément dans un `ListView` . Par exemple, considérez une liste d’e-mails dans l’application de messagerie. Sur iOS, vous pouvez balayer pour supprimer un message :

![](interactivity-images/context-default.png "ListView with Context Actions")

Les actions de contexte peuvent être implémentées en C# et XAML. Vous trouverez ci-dessous des guides spécifiques pour les deux, mais commençons par examiner certains détails d’implémentation clés pour les deux.

Les actions de contexte sont créées à l’aide d' `MenuItem` éléments. Les événements TAP pour les `MenuItems` objets sont déclenchés par le `MenuItem` lui-même, et non par `ListView` . Cela diffère de la façon dont les événements TAP sont gérés pour les cellules, où le `ListView` déclenche l’événement plutôt que la cellule. Étant donné que le `ListView` déclenche l’événement, son gestionnaire d’événements reçoit des informations clés, comme l’élément sélectionné ou taraudé.

Par défaut, un n' `MenuItem` a aucun moyen de savoir à quelle cellule il appartient. La `CommandParameter` propriété est disponible sur `MenuItem` pour stocker des objets, tels que l’objet qui se trouve derrière le `MenuItem` `ViewCell` . La `CommandParameter` propriété peut être définie à la fois en XAML et en C#.

### <a name="xaml"></a>XAML

`MenuItem`les éléments peuvent être créés dans une collection XAML. Le code XAML ci-dessous illustre une cellule personnalisée avec deux actions de contexte implémentées :

```xaml
<ListView x:Name="ContextDemoList">
  <ListView.ItemTemplate>
    <DataTemplate>
      <ViewCell>
         <ViewCell.ContextActions>
            <MenuItem Clicked="OnMore"
                      CommandParameter="{Binding .}"
                      Text="More" />
            <MenuItem Clicked="OnDelete"
                      CommandParameter="{Binding .}"
                      Text="Delete" IsDestructive="True" />
         </ViewCell.ContextActions>
         <StackLayout Padding="15,0">
              <Label Text="{Binding title}" />
         </StackLayout>
      </ViewCell>
    </DataTemplate>
  </ListView.ItemTemplate>
</ListView>
```

Dans le fichier code-behind, vérifiez que les `Clicked` méthodes sont implémentées :

```csharp
public void OnMore (object sender, EventArgs e)
{
    var mi = ((MenuItem)sender);
    DisplayAlert("More Context Action", mi.CommandParameter + " more context action", "OK");
}

public void OnDelete (object sender, EventArgs e)
{
    var mi = ((MenuItem)sender);
    DisplayAlert("Delete Context Action", mi.CommandParameter + " delete context action", "OK");
}
```

> [!NOTE]
> Le `NavigationPageRenderer` pour Android a une méthode substituable `UpdateMenuItemIcon` qui peut être utilisée pour charger des icônes à partir d’un personnalisé `Drawable` . Ce remplacement permet d’utiliser des images SVG comme icônes sur des `MenuItem` instances sur Android.

### <a name="code"></a>Code

Les actions de contexte peuvent être implémentées dans n’importe quelle sous `Cell` -classe (à condition qu’elles ne soient pas utilisées comme en-tête de groupe) en créant des `MenuItem` instances et en les ajoutant à la `ContextActions` collection de la cellule. Vous pouvez configurer les propriétés suivantes pour l’action de contexte :

- **Texte** &ndash; chaîne qui s’affiche dans l’élément de menu.
- **Clic** &ndash; événement lorsque l’utilisateur clique sur l’élément.
- **IsDestructive** &ndash; (facultatif) lorsque la valeur est true, l’élément est rendu différemment sur iOS.

Plusieurs actions de contexte peuvent être ajoutées à une cellule, mais une seule doit avoir la `IsDestructive` valeur `true` . Le code suivant montre comment ajouter des actions de contexte à un `ViewCell` :

```csharp
var moreAction = new MenuItem { Text = "More" };
moreAction.SetBinding (MenuItem.CommandParameterProperty, new Binding ("."));
moreAction.Clicked += async (sender, e) =>
{
    var mi = ((MenuItem)sender);
    Debug.WriteLine("More Context Action clicked: " + mi.CommandParameter);
};

var deleteAction = new MenuItem { Text = "Delete", IsDestructive = true }; // red background
deleteAction.SetBinding (MenuItem.CommandParameterProperty, new Binding ("."));
deleteAction.Clicked += async (sender, e) =>
{
    var mi = ((MenuItem)sender);
    Debug.WriteLine("Delete Context Action clicked: " + mi.CommandParameter);
};
// add to the ViewCell's ContextActions property
ContextActions.Add (moreAction);
ContextActions.Add (deleteAction);
```

## <a name="pull-to-refresh"></a>Tirer pour actualiser

Les utilisateurs s’attendent à ce que l’extraction d’une liste de données actualise cette liste. Le [`ListView`](xref:Xamarin.Forms.ListView) contrôle prend en charge ce qui est prêt à l’emploi. Pour activer la fonctionnalité d’extraction vers l’actualisation, définissez [`IsPullToRefreshEnabled`](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled) sur `true` :

```xaml
<ListView ...
          IsPullToRefreshEnabled="true" />
```

Le code C# équivalent est :

```csharp
listView.IsPullToRefreshEnabled = true;
```

Un compteur s’affiche au cours de l’actualisation, qui est noir par défaut. Toutefois, vous pouvez modifier la couleur du compteur sur iOS et Android en affectant à la propriété la valeur `RefreshControlColor` a [`Color`](xref:Xamarin.Forms.Color) :

```xaml
<ListView ...
          IsPullToRefreshEnabled="true"
          RefreshControlColor="Red" />
```

Le code C# équivalent est :

```csharp
listView.RefreshControlColor = Color.Red;
```

Les captures d’écran suivantes montrent une extraction à l’utilisation de l’utilisateur :

![](interactivity-images/refresh-start.png "ListView Pull to Refresh In-Progress")

Les captures d’écran suivantes montrent l’extraction à actualiser une fois que l’utilisateur a libéré l’extraction, le compteur étant affiché pendant la [`ListView`](xref:Xamarin.Forms.ListView) mise à jour de :

![](interactivity-images/refresh-in-progress.png "ListView Pull to Refresh Complete")

[`ListView`](xref:Xamarin.Forms.ListView)déclenche l' [`Refreshing`](xref:Xamarin.Forms.ListView.Refreshing) événement pour lancer l’actualisation, et la [`IsRefreshing`](xref:Xamarin.Forms.ListView.IsRefreshing) propriété aura la valeur `true` . Quel que soit le code requis pour actualiser le contenu du `ListView` , doit être exécuté par le gestionnaire d’événements pour l' `Refreshing` événement ou par la méthode exécutée par le [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand) . Une fois le `ListView` est actualisé, la `IsRefreshing` propriété doit avoir la valeur `false` , ou la [`EndRefresh`](xref:Xamarin.Forms.ListView.EndRefresh) méthode doit être appelée pour indiquer que l’actualisation est terminée.

> [!NOTE]
> Lors de la définition d’un [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand) , la `CanExecute` méthode de la commande peut être spécifiée pour activer ou désactiver la commande.

## <a name="detect-scrolling"></a>Détecter le défilement

[`ListView`](xref:Xamarin.Forms.ListView)définit un `Scrolled` événement qui est déclenché pour indiquer que le défilement s’est produit. L’exemple de code XAML suivant montre un `ListView` qui définit un gestionnaire d’événements pour l' `Scrolled` événement :

```xaml
<ListView Scrolled="OnListViewScrolled">
    ...
</ListView>
```

Le code C# équivalent est :

```csharp
ListView listView = new ListView();
listView.Scrolled += OnListViewScrolled;
```

Dans cet exemple de code, le `OnListViewScrolled` Gestionnaire d’événements est exécuté lorsque l' `Scrolled` événement se déclenche :

```csharp
void OnListViewScrolled(object sender, ScrolledEventArgs e)
{
    Debug.WriteLine("ScrollX: " + e.ScrollX);
    Debug.WriteLine("ScrollY: " + e.ScrollY);  
}
```

Le `OnListViewScrolled` Gestionnaire d’événements génère les valeurs de l' `ScrolledEventArgs` objet qui accompagne l’événement.

## <a name="related-links"></a>Liens connexes

- [Interactivité de ListView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)
