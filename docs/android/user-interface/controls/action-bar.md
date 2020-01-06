---
title: Barre pour Xamarin. Android
ms.prod: xamarin
ms.assetid: 84A79F1F-9E73-4E3E-80FA-B72E5686900B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: f64b57e73b69b3111087ca1352f5fb9536f855e5
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75488957"
---
# <a name="actionbar-for-xamarinandroid"></a>Barre pour Xamarin. Android

Lorsque vous utilisez `TabActivity`, le code permettant de créer les icônes d’onglet n’a aucun effet lorsqu’il est exécuté sur l’infrastructure Android 4,0. Bien qu’elle fonctionne comme elle le faisait dans les versions d’Android antérieures à 2,3, la classe `TabActivity` elle-même a été dépréciée dans 4,0. Une nouvelle façon de créer une interface avec onglets a été introduite et utilise la Barre d’action, que nous aborderons ensuite.

## <a name="action-bar-tabs"></a>Onglets Barre d’action

Le Barre d’action prend en charge l’ajout d’interfaces à onglets dans Android 4,0.
La capture d’écran suivante montre un exemple d’une telle interface.

[![capture d’écran de l’application en cours d’exécution dans un émulateur ; deux onglets sont affichés](action-bar-images/25-actionbartabs.png)](action-bar-images/25-actionbartabs.png#lightbox)

Pour créer des onglets dans la Barre d’action, nous devons tout d’abord définir sa propriété `NavigationMode` pour prendre en charge les onglets. Dans Android 4, une propriété `ActionBar` est disponible sur la classe Activity, que nous pouvons utiliser pour définir la `NavigationMode` comme suit :

```csharp
this.ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
```

Une fois cette opération effectuée, nous pouvons créer un onglet en appelant la méthode `NewTab` sur le Barre d’action. Avec cette instance de tabulation, nous pouvons appeler les méthodes `SetText` et `SetIcon` pour définir le texte et l’icône de l’étiquette de l’onglet. ces appels sont effectués dans l’ordre indiqué dans le code ci-dessous :

```csharp
var tab = this.ActionBar.NewTab ();
tab.SetText (tabText);
tab.SetIcon (Resource.Drawable.ic_tab_white);
```

Avant de pouvoir ajouter l’onglet, nous devons gérer l’événement `TabSelected`. Dans ce gestionnaire, nous pouvons créer le contenu de l’onglet. Barre d’action onglets sont conçus pour fonctionner avec des *fragments*, qui sont des classes qui représentent une partie de l’interface utilisateur dans une activité. Pour cet exemple, la vue du fragment contient une seule `TextView`, que nous gonflons dans notre sous-classe `Fragment` comme suit :

```csharp
class SampleTabFragment: Fragment
{           
    public override View OnCreateView (LayoutInflater inflater,
        ViewGroup container, Bundle savedInstanceState)
    {
        base.OnCreateView (inflater, container, savedInstanceState);

        var view = inflater.Inflate (
            Resource.Layout.Tab, container, false);

        var sampleTextView =
            view.FindViewById<TextView> (Resource.Id.sampleTextView);            
        sampleTextView.Text = "sample fragment text";

        return view;
    }
}
```

L’argument d’événement passé dans le `TabSelected` événement est de type `TabEventArgs`, qui comprend une propriété `FragmentTransaction` que nous pouvons utiliser pour ajouter le fragment comme indiqué ci-dessous :

```csharp
tab.TabSelected += delegate(object sender, ActionBar.TabEventArgs e) {             
    e.FragmentTransaction.Add (Resource.Id.fragmentContainer,
        new SampleTabFragment ());
};
```

Enfin, nous pouvons ajouter l’onglet au Barre d’action en appelant la méthode `AddTab` comme indiqué dans ce code :

```csharp
this.ActionBar.AddTab (tab);
```

Pour obtenir un exemple complet, consultez le projet *HelloTabsICS* dans l’exemple de code de ce document.

## <a name="shareactionprovider"></a>ShareActionProvider

La classe `ShareActionProvider` permet à une action de partage d’être exécutée à partir d’un Barre d’action. Il s’occupe de créer une vue d’action avec une liste d’applications qui peuvent gérer une intention de partage et conserver un historique des applications précédemment utilisées pour y accéder plus tard à partir de la Barre d’action. Cela permet aux applications de partager des données via une expérience utilisateur cohérente dans Android.

### <a name="image-sharing-example"></a>Exemple de partage d’image

Par exemple, voici une capture d’écran d’un Barre d’action avec un élément de menu pour partager une image (issue de l’exemple [ShareActionProvider](https://docs.microsoft.com/samples/xamarin/monodroid-samples/shareactionproviderdemo) ). Quand l’utilisateur clique sur l’élément de menu de la Barre d’action, ShareActionProvider charge l’application pour gérer une intention associée à l' `ShareActionProvider`. Dans cet exemple, l’application de messagerie a été utilisée précédemment. elle est donc présentée sur la Barre d’action.

[![capture d’écran de l’icône de l’application de messagerie dans le Barre d’action](action-bar-images/09-shareactionprovider.png)](action-bar-images/09-shareactionprovider.png#lightbox)

Lorsque l’utilisateur clique sur l’élément dans la Barre d’action, l’application de messagerie qui contient l’image partagée est lancée, comme indiqué ci-dessous :

[Capture d’écran ![de l’application de messagerie affichant l’image du singe](action-bar-images/10-messagewithimage.png)](action-bar-images/10-messagewithimage.png#lightbox)

### <a name="specifying-the-action-provider-class"></a>Spécification de la classe de fournisseur d’action

Pour utiliser le `ShareActionProvider`, définissez l’attribut `android:actionProviderClass` sur un élément de menu dans le code XML pour le menu du Barre d’action comme suit :

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:id="@+id/shareMenuItem"
      android:showAsAction="always"
      android:title="@string/sharePicture"
      android:actionProviderClass="android.widget.ShareActionProvider" />
</menu>
```

### <a name="inflating-the-menu"></a>Personnalisation du menu

Pour augmenter le menu, nous remplaçons `OnCreateOptionsMenu` dans la sous-classe d’activité. Une fois que nous avons une référence au menu, nous pouvons obtenir le `ShareActionProvider` à partir de la propriété `ActionProvider` de l’élément de menu, puis utiliser la méthode SetShareIntent pour définir l’intention de l' `ShareActionProvider`, comme indiqué ci-dessous :

```csharp
public override bool OnCreateOptionsMenu (IMenu menu)
{
    MenuInflater.Inflate (Resource.Menu.ActionBarMenu, menu);       

    var shareMenuItem = menu.FindItem (Resource.Id.shareMenuItem);           
    var shareActionProvider =
       (ShareActionProvider)shareMenuItem.ActionProvider;
    shareActionProvider.SetShareIntent (CreateIntent ());
}
```

### <a name="creating-the-intent"></a>Création de l’intention

La `ShareActionProvider` utilise l’intention, transmise à la méthode `SetShareIntent` dans le code ci-dessus, pour lancer l’activité appropriée. Dans ce cas, nous créons une intention d’envoyer une image à l’aide du code suivant :

```csharp
Intent CreateIntent ()
{  
    var sendPictureIntent = new Intent (Intent.ActionSend);
    sendPictureIntent.SetType ("image/*");
    var uri = Android.Net.Uri.FromFile (GetFileStreamPath ("monkey.png"));          
    sendPictureIntent.PutExtra (Intent.ExtraStream, uri);
    return sendPictureIntent;
}
```

L’image de l’exemple de code ci-dessus est incluse en tant que ressource avec l’application et copiée dans un emplacement accessible publiquement lorsque l’activité est créée. elle sera donc accessible à d’autres applications, telles que l’application de messagerie. L’exemple de code qui accompagne cet article contient la source complète de cet exemple, illustrant son utilisation.

## <a name="related-links"></a>Liens associés

- [Hello tabs ICS (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/hellotabsics)
- [Démonstration ShareActionProvider (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/shareactionproviderdemo)
