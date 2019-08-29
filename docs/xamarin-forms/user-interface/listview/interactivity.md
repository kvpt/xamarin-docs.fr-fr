---
title: Interactivité de ListView
description: Cet article explique comment ajouter de l’interactivité à un ListView Xamarin.Forms en implémentant les sélections, les actions de contexte et les extraire pour actualiser.
ms.prod: xamarin
ms.assetid: CD14EB90-B08C-4E8F-A314-DA0EEC76E647
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/27/2019
ms.openlocfilehash: 3949dd85492a8181ee53e23b3ba2e986e59f8f47
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70121627"
---
# <a name="listview-interactivity"></a>Interactivité de ListView

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)

[`ListView`](xref:Xamarin.Forms.ListView)prend en charge l’interaction avec les données qu’il présente.

<a name="selectiontaps" />

## <a name="selection--taps"></a>Sélection & tap

Le [ `ListView` ](xref:Xamarin.Forms.ListView) en mode de sélection est contrôlé en définissant le [ `ListView.SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode) propriété une valeur de la [ `ListViewSelectionMode` ](xref:Xamarin.Forms.ListViewSelectionMode) énumération :

- [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single) Indique qu’un seul élément peut être sélectionné, avec l’élément sélectionné est mis en surbrillance. Valeur par défaut.
- [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None) Indique que les éléments ne peuvent pas être sélectionnés.

Lorsqu’un utilisateur appuie sur un élément, deux événements sont déclenchés :

- [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) se déclenche lorsqu’un nouvel élément est sélectionné.
- [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) se déclenche lorsque l’utilisateur appuie sur un élément.

En appuyant sur le même élément à deux reprises activeront toutes deux [ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped) les événements, mais sera uniquement déclencher un seul [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) événement.

> [!NOTE]
> La [`ItemTappedEventArgs`](xref:Xamarin.Forms.ItemTappedEventArgs) classe, qui contient les arguments d’événement pour [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) l’événement, [`Group`](xref:Xamarin.Forms.ItemTappedEventArgs.Group) possède [`Item`](xref:Xamarin.Forms.ItemTappedEventArgs.Item) les propriétés et et `ItemIndex` une propriété dont la valeur représente l’index [`ListView`](xref:Xamarin.Forms.ListView) dans le de l’élément taraudé. De même, la [`SelectedItemChangedEventArgs`](xref:Xamarin.Forms.SelectedItemChangedEventArgs) classe, qui contient les arguments d’événement pour [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) l’événement, possède [`SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem) une propriété et une `SelectedItemIndex` propriété dont la valeur représente l’index dans `ListView` le de l’élément sélectionné.

Lorsque le [ `SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode) propriété est définie sur [ `Single` ](xref:Xamarin.Forms.ListViewSelectionMode.Single), les éléments dans le [ `ListView` ](xref:Xamarin.Forms.ListView) peut être sélectionné, le [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) et [ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped) événement n’est déclenché et le [ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem) propriété est définie à la valeur de l’élément sélectionné.

Lorsque le [ `SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode) propriété est définie sur [ `None` ](xref:Xamarin.Forms.ListViewSelectionMode.None), les éléments dans le [ `ListView` ](xref:Xamarin.Forms.ListView) ne peut pas être sélectionné, le [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) événement ne sera pas déclenché et le [ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem) propriété restera `null`. Toutefois, [ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped) événements seront toujours déclenchés et l’élément sur lequel apparaît en surbrillance brièvement pendant le drainage.

Lorsqu’un élément a été sélectionné et le [ `SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode) propriété est modifiée à partir de [ `Single` ](xref:Xamarin.Forms.ListViewSelectionMode.Single) à [ `None` ](xref:Xamarin.Forms.ListViewSelectionMode.None), le [ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem) propriété sera définie `null` et [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) événement est déclenché avec un `null` élément.

Le montrent les captures d’écran suivants un [ `ListView` ](xref:Xamarin.Forms.ListView) avec le mode de sélection par défaut :

![](interactivity-images/selection-default.png "ListView avec sélection activée")

### <a name="disabling-selection"></a>La désactivation de la sélection

Pour désactiver [ `ListView` ](xref:Xamarin.Forms.ListView) du jeu de sélection le [ `SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode) propriété [ `None` ](xref:Xamarin.Forms.ListViewSelectionMode.None):

```xaml
<ListView ... SelectionMode="None" />
```

```csharp
var listView = new ListView { ... SelectionMode = ListViewSelectionMode.None };
```

<a name="Context_Actions" />

## <a name="context-actions"></a>Actions contextuelles

Souvent, les utilisateurs souhaitent effectuer une opération sur un élément dans un `ListView`. Par exemple, considérez une liste d’e-mails dans l’application de messagerie. Sur iOS, vous pouvez balayer pour supprimer un message ::

![](interactivity-images/context-default.png "ListView avec des Actions contextuelles")

Actions de contexte peuvent être implémentées en C# et XAML. Vous trouverez ci-dessous des guides pour les deux, mais tout d’abord nous allons examiner certains détails d’implémentation de la clé pour les deux.

Actions contextuelles sont créées à l’aide de `MenuItem`s. Appuyez sur pour MenuItems sont déclenchés par le MenuItem lui-même, pas le ListView. Cela est différent à partir de la gestion des événements de clic pour les cellules, où le ListView déclenche l’événement plutôt que la cellule. Étant donné que le ListView déclenche l’événement, son gestionnaire d’événements reçoit des informations de clé, à laquelle l’élément a été sélectionné ou appuyé sur.

Par défaut, un MenuItem n’a aucun moyen de savoir quelle cellule auquel il appartient. `CommandParameter` est disponible sur `MenuItem` pour stocker les objets, tels que l’objet derrière l’écran de MenuItem ViewCell. `CommandParameter` peut être définie en XAML et C#.

### <a name="c"></a>C\#

Actions de contexte peuvent être implémentées dans un `Cell` sous-classe (à condition qu’il n’est pas utilisé comme un en-tête de groupe) en créant `MenuItem`s et en les ajoutant à la `ContextActions` collection pour la cellule. Les éléments suivants sont propriétés peuvent être configurées pour l’action de contexte :

- **Texte** &ndash; la chaîne qui apparaît dans l’élément de menu.
- **Un clic sur** &ndash; l’événement lorsque l’élément est activé.
- **IsDestructive** &ndash; (facultatif) true lors de l’élément est rendu de différemment sur iOS.

Plusieurs actions de contexte peuvent être ajoutées à une cellule, mais seul l’un doit avoir `IsDestructive` défini sur `true`. Le code suivant illustre la façon dont les actions contextuelles étaient ajoutées à un `ViewCell`:

```csharp
var moreAction = new MenuItem { Text = "More" };
moreAction.SetBinding (MenuItem.CommandParameterProperty, new Binding ("."));
moreAction.Clicked += async (sender, e) => {
    var mi = ((MenuItem)sender);
    Debug.WriteLine("More Context Action clicked: " + mi.CommandParameter);
};

var deleteAction = new MenuItem { Text = "Delete", IsDestructive = true }; // red background
deleteAction.SetBinding (MenuItem.CommandParameterProperty, new Binding ("."));
deleteAction.Clicked += async (sender, e) => {
    var mi = ((MenuItem)sender);
    Debug.WriteLine("Delete Context Action clicked: " + mi.CommandParameter);
};
// add to the ViewCell's ContextActions property
ContextActions.Add (moreAction);
ContextActions.Add (deleteAction);
```

### <a name="xaml"></a>XAML

`MenuItem`s peut également être créé dans une collection XAML déclarative. Le XAML ci-dessous illustre une cellule personnalisée avec deux actions contextuelles implémentées :

```xaml
<ListView x:Name="ContextDemoList">
  <ListView.ItemTemplate>
    <DataTemplate>
      <ViewCell>
         <ViewCell.ContextActions>
            <MenuItem Clicked="OnMore" CommandParameter="{Binding .}"
               Text="More" />
            <MenuItem Clicked="OnDelete" CommandParameter="{Binding .}"
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

Dans le fichier code-behind, vérifiez que le `Clicked` méthodes sont implémentées :

```csharp
public void OnMore (object sender, EventArgs e) {
    var mi = ((MenuItem)sender);
    DisplayAlert("More Context Action", mi.CommandParameter + " more context action", "OK");
}

public void OnDelete (object sender, EventArgs e) {
    var mi = ((MenuItem)sender);
    DisplayAlert("Delete Context Action", mi.CommandParameter + " delete context action", "OK");
}
```

> [!NOTE]
> Le `NavigationPageRenderer` pour Android propose un substituable `UpdateMenuItemIcon` méthode qui peut être utilisé pour charger des icônes à partir d’un personnalisé `Drawable`. Ce remplacement permet d’utiliser les images SVG sous forme d’icônes sur `MenuItem` instances sur Android.

<a name="Pull_to_Refresh" />

## <a name="pull-to-refresh"></a>Extraire pour actualiser

Les utilisateurs s’attendent que l’extraction vers le bas de la liste de données actualisera cette liste. [`ListView`](xref:Xamarin.Forms.ListView)prend en charge cette boîte aux attente. Pour activer la fonctionnalité d’extraction vers l’actualisation, [`IsPullToRefreshEnabled`](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled) définissez `true`sur:

```xaml
<ListView ...
          IsPullToRefreshEnabled="true" />
```

Le code C# équivalent est :

```csharp
listView.IsPullToRefreshEnabled = true;
```

Un compteur s’affiche au cours de l’actualisation, qui est noir par défaut. Toutefois, vous pouvez modifier la couleur du compteur sur iOS et Android en affectant à la [`Color`](xref:Xamarin.Forms.Color)propriété la `RefreshControlColor` valeur a:

```xaml
<ListView ...
          IsPullToRefreshEnabled="true"
          RefreshControlColor="Red" />
```

Le code C# équivalent est :

```csharp
listView.RefreshControlColor = Color.Red;
```

Les captures d’écran suivantes montrent une extraction à l’utilisation de l’utilisateur:

![](interactivity-images/refresh-start.png "Extraction de ListView pour actualiser en cours d’exécution")

Les captures d’écran suivantes montrent l’extraction à actualiser une fois que l’utilisateur a libéré l’extraction, le compteur étant [`ListView`](xref:Xamarin.Forms.ListView) affiché pendant la mise à jour de:

![](interactivity-images/refresh-in-progress.png "Extraction de ListView à actualiser terminée")

[`ListView`](xref:Xamarin.Forms.ListView)déclenche l' [`Refreshing`](xref:Xamarin.Forms.ListView.Refreshing) événement pour lancer l’actualisation, et la [`IsRefreshing`](xref:Xamarin.Forms.ListView.IsRefreshing) `true`propriété aura la valeur. Quel que soit le code requis pour actualiser le `ListView` contenu du, doit être exécuté par le gestionnaire d' `Refreshing` événements pour l’événement ou par la méthode exécutée par le [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand). Une fois `ListView` le est actualisé, la `IsRefreshing` `false`propriété doit avoir la valeur, ou la [`EndRefresh`](xref:Xamarin.Forms.ListView.EndRefresh) méthode doit être appelée pour indiquer que l’actualisation est terminée.

> [!NOTE]
> Lors de la [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand)définition d' `CanExecute` un, la méthode de la commande peut être spécifiée pour activer ou désactiver la commande.

## <a name="related-links"></a>Liens associés

- [Interactivité de ListView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)
