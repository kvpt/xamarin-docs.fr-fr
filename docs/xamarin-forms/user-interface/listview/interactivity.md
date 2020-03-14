---
title: Interactivité de ListView
description: Cet article explique comment ajouter de l’interactivité à un ListView Xamarin.Forms en implémentant les sélections, les actions de contexte et les extraire pour actualiser.
ms.prod: xamarin
ms.assetid: CD14EB90-B08C-4E8F-A314-DA0EEC76E647
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/25/2019
ms.openlocfilehash: aa717792bdaefe24d957c9781934933b67aaf92b
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305331"
---
# <a name="listview-interactivity"></a>Interactivité de ListView

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)

La classe Xamarin. Forms [`ListView`](xref:Xamarin.Forms.ListView) prend en charge l’interaction de l’utilisateur avec les données qu’il présente.

## <a name="selection-and-taps"></a>Sélection et clics

Le mode de sélection [`ListView`](xref:Xamarin.Forms.ListView) est contrôlé en affectant à la propriété [`ListView.SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) la valeur de l’énumération [`ListViewSelectionMode`](xref:Xamarin.Forms.ListViewSelectionMode) :

- [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single) indique qu’un seul élément peut être sélectionné, avec l’élément sélectionné mis en surbrillance. Il s’agit de la valeur par défaut.
- [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None) indique que les éléments ne peuvent pas être sélectionnés.

Lorsqu’un utilisateur appuie sur un élément, deux événements sont déclenchés :

- [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) se déclenche lorsqu’un nouvel élément est sélectionné.
- [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) se déclenche lorsqu’un élément est frappé.

Le double-clic sur le même élément déclenche deux événements [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) , mais ne déclenche qu’un seul événement [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) .

> [!NOTE]
> La classe [`ItemTappedEventArgs`](xref:Xamarin.Forms.ItemTappedEventArgs) , qui contient les arguments d’événement pour l’événement [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) , a des propriétés [`Group`](xref:Xamarin.Forms.ItemTappedEventArgs.Group) et [`Item`](xref:Xamarin.Forms.ItemTappedEventArgs.Item) , et une propriété `ItemIndex` dont la valeur représente l’index dans le [`ListView`](xref:Xamarin.Forms.ListView) de l’élément taraudé. De même, la classe [`SelectedItemChangedEventArgs`](xref:Xamarin.Forms.SelectedItemChangedEventArgs) , qui contient les arguments d’événement pour l’événement [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) , a une propriété [`SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem) et une propriété `SelectedItemIndex` dont la valeur représente l’index dans le `ListView` de l’élément sélectionné.

Lorsque la propriété [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) est définie sur [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single), les éléments de la [`ListView`](xref:Xamarin.Forms.ListView) peuvent être sélectionnés, les événements [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) et [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) sont déclenchés, et la propriété [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) est définie sur la valeur de l’élément sélectionné.

Lorsque la propriété [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) est définie sur [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None), les éléments de la [`ListView`](xref:Xamarin.Forms.ListView) ne peuvent pas être sélectionnés, l’événement [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) n’est pas déclenché et la propriété [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) reste `null`. Toutefois, [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) événements seront toujours déclenchés et l’élément taraudé sera brièvement mis en surbrillance pendant le TAP.

Lorsqu’un élément a été sélectionné et que la propriété [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) passe de [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single) à [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None), la propriété [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) est définie sur `null` et l’événement [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) est déclenché avec un élément `null`.

Les captures d’écran suivantes montrent un [`ListView`](xref:Xamarin.Forms.ListView) avec le mode de sélection par défaut :

![](interactivity-images/selection-default.png "ListView with Selection Enabled")

### <a name="disable-selection"></a>Désactiver la sélection

Pour désactiver [`ListView`](xref:Xamarin.Forms.ListView) sélection, affectez à la propriété [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) la valeur [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None):

```xaml
<ListView ... SelectionMode="None" />
```

```csharp
var listView = new ListView { ... SelectionMode = ListViewSelectionMode.None };
```

## <a name="context-actions"></a>Actions contextuelles

Souvent, les utilisateurs souhaitent agir sur un élément dans un `ListView`. Par exemple, considérez une liste d’e-mails dans l’application de messagerie. Sur iOS, vous pouvez balayer pour supprimer un message :

![](interactivity-images/context-default.png "ListView with Context Actions")

Actions de contexte peuvent être implémentées en c# et XAML. Vous trouverez ci-dessous des guides pour les deux, mais tout d’abord nous allons examiner certains détails d’implémentation de la clé pour les deux.

Les actions de contexte sont créées à l’aide d’éléments `MenuItem`. Les événements TAP pour `MenuItems` objets sont déclenchés par le `MenuItem` lui-même, et non par le `ListView`. Cela diffère de la façon dont les événements TAP sont gérés pour les cellules, où le `ListView` déclenche l’événement plutôt que la cellule. Étant donné que le `ListView` déclenche l’événement, son gestionnaire d’événements reçoit des informations clés, comme l’élément sélectionné ou taraudé.

Par défaut, une `MenuItem` n’a aucun moyen de savoir à quelle cellule elle appartient. La propriété `CommandParameter` est disponible sur `MenuItem` pour stocker des objets, tels que l’objet sous-jacent du `ViewCell`de `MenuItem`. La propriété `CommandParameter` peut être définie dans XAML et C#.

### <a name="xaml"></a>XAML

`MenuItem` éléments peuvent être créés dans une collection XAML. Le XAML ci-dessous illustre une cellule personnalisée avec deux actions contextuelles implémentées :

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

Dans le fichier code-behind, assurez-vous que les méthodes de `Clicked` sont implémentées :

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
> La `NavigationPageRenderer` pour Android a une méthode substituable `UpdateMenuItemIcon` qui peut être utilisée pour charger des icônes à partir d’un `Drawable`personnalisé. Ce remplacement permet d’utiliser des images SVG comme icônes sur des instances de `MenuItem` sur Android.

### <a name="code"></a>Code

Les actions de contexte peuvent être implémentées dans n’importe quelle sous-classe `Cell` (tant qu’elles ne sont pas utilisées comme en-tête de groupe) en créant des instances `MenuItem` et en les ajoutant à la collection `ContextActions` pour la cellule. Les éléments suivants sont propriétés peuvent être configurées pour l’action de contexte :

- **Texte** &ndash; la chaîne qui s’affiche dans l’élément de menu.
- **Clic** &ndash; l’événement lors d’un clic sur l’élément.
- **IsDestructive** &ndash; (facultatif) lorsque la valeur est true, l’élément est rendu différemment sur iOS.

Plusieurs actions de contexte peuvent être ajoutées à une cellule, mais une seule `IsDestructive` doit avoir la valeur `true`. Le code suivant montre comment ajouter des actions de contexte à une `ViewCell`:

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

## <a name="pull-to-refresh"></a>Extraire pour actualiser

Les utilisateurs s’attendent que l’extraction vers le bas de la liste de données actualisera cette liste. Le contrôle de [`ListView`](xref:Xamarin.Forms.ListView) prend en charge ce qui est prêt à l’emploi. Pour activer la fonctionnalité d’extraction vers l’actualisation, définissez [`IsPullToRefreshEnabled`](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled) sur `true`:

```xaml
<ListView ...
          IsPullToRefreshEnabled="true" />
```

Le code C# équivalent est :

```csharp
listView.IsPullToRefreshEnabled = true;
```

Un compteur s’affiche au cours de l’actualisation, qui est noir par défaut. Toutefois, vous pouvez modifier la couleur du compteur sur iOS et Android en affectant à la propriété `RefreshControlColor` la valeur [`Color`](xref:Xamarin.Forms.Color):

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

Les captures d’écran suivantes montrent l’extraction à actualiser une fois que l’utilisateur a libéré l’extraction, le compteur étant affiché pendant la mise à jour de l' [`ListView`](xref:Xamarin.Forms.ListView) :

![](interactivity-images/refresh-in-progress.png "ListView Pull to Refresh Complete")

[`ListView`](xref:Xamarin.Forms.ListView) déclenche l’événement [`Refreshing`](xref:Xamarin.Forms.ListView.Refreshing) pour lancer l’actualisation, et la propriété [`IsRefreshing`](xref:Xamarin.Forms.ListView.IsRefreshing) est définie sur `true`. Le code requis pour actualiser le contenu du `ListView` doit ensuite être exécuté par le gestionnaire d’événements pour l’événement `Refreshing`, ou par la méthode exécutée par le [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand). Une fois que le `ListView` est actualisé, la propriété `IsRefreshing` doit avoir la valeur `false`, ou la méthode [`EndRefresh`](xref:Xamarin.Forms.ListView.EndRefresh) doit être appelée pour indiquer que l’actualisation est terminée.

> [!NOTE]
> Lors de la définition d’une [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand), la méthode `CanExecute` de la commande peut être spécifiée pour activer ou désactiver la commande.

## <a name="detect-scrolling"></a>Détecter le défilement

[`ListView`](xref:Xamarin.Forms.ListView) définit un événement `Scrolled` déclenché pour indiquer que le défilement s’est produit. L’exemple de code XAML suivant montre une `ListView` qui définit un gestionnaire d’événements pour l’événement `Scrolled` :

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

Dans cet exemple de code, le gestionnaire d’événements `OnListViewScrolled` est exécuté lorsque l’événement `Scrolled` se déclenche :

```csharp
void OnListViewScrolled(object sender, ScrolledEventArgs e)
{
    Debug.WriteLine("ScrollX: " + e.ScrollX);
    Debug.WriteLine("ScrollY: " + e.ScrollY);  
}
```

Le gestionnaire d’événements `OnListViewScrolled` génère les valeurs de l’objet `ScrolledEventArgs` qui accompagne l’événement.

## <a name="related-links"></a>Liens connexes

- [Interactivité de ListView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)
