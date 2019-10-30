---
title: Ajout d’une seconde barre d’outils
ms.prod: xamarin
ms.assetid: FCE0AD27-8B6B-47C6-AD19-2B1C12E1BBBF
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/15/2018
ms.openlocfilehash: 43030d4a221ceadf68c30df2e37449e7f996fa6d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029107"
---
# <a name="adding-a-second-toolbar"></a>Ajout d’une seconde barre d’outils

## <a name="overview"></a>Vue d'ensemble 

La `Toolbar` peut faire plus que remplacer la barre d’action &ndash; elle peut être utilisée plusieurs fois dans une activité, elle peut être personnalisée pour un placement n’importe où sur l’écran, et elle peut être configurée pour couvrir uniquement une largeur partielle de l’écran. Les exemples ci-dessous montrent comment créer un deuxième `Toolbar` et le placer en bas de l’écran. Ce `Toolbar` implémente les éléments de menu **copier**, **couper**et **coller** . 

## <a name="define-the-second-toolbar"></a>Définir la deuxième barre d’outils 

Modifiez le fichier de disposition **main. AXML** et remplacez son contenu par le code XML suivant :

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <include
        android:id="@+id/toolbar"
        layout="@layout/toolbar" />
    <LinearLayout
        android:orientation="vertical"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:id="@+id/main_content"
        android:layout_below="@id/toolbar">
      <ImageView
          android:layout_width="fill_parent"
          android:layout_height="0dp"
          android:layout_weight="1" />
      <Toolbar
          android:id="@+id/edit_toolbar"
          android:minHeight="?android:attr/actionBarSize"
          android:background="?android:attr/colorAccent"
          android:theme="@android:style/ThemeOverlay.Material.Dark.ActionBar"
          android:layout_width="match_parent"
          android:layout_height="wrap_content" />
    </LinearLayout>
</RelativeLayout>
```

Ce code XML ajoute une deuxième `Toolbar` en bas de l’écran avec un `ImageView` vide qui remplit le milieu de l’écran. La hauteur de cette `Toolbar` est définie sur la hauteur d’une barre d’action : 

```xml
android:minHeight="?android:attr/actionBarSize"
```

La couleur d’arrière-plan de cette `Toolbar` est définie sur une couleur d’accentuation qui sera définie ensuite :

```xml
android:background="?android:attr/colorAccent
```

Notez que ce `Toolbar` est basé sur un autre thème (**ThemeOverlay. Material. Dark. barre**) que celui utilisé par le `Toolbar` créé lors du [remplacement de l’barre d’action](~/android/user-interface/controls/tool-bar/replacing-the-action-bar.md) &ndash; il n’est pas lié à la fenêtre décor de l’activité ou au thème utilisé dans premier `Toolbar`.

Modifiez les **ressources/valeurs/styles. xml** et ajoutez la couleur d’accentuation suivante à la définition de style : 

```xml
<item name="android:colorAccent">#C7A935</item>
```

Cela donne à la barre d’outils inférieure une couleur orange foncée. La génération et l’exécution de l’application affichent une deuxième barre d’outils vide au bas de l’écran : 

[![capture d’écran de l’application avec une deuxième barre d’outils jaune en bas de l’écran](adding-a-second-toolbar-images/01-second-toolbar-sml.png)](adding-a-second-toolbar-images/01-second-toolbar.png#lightbox)

## <a name="add-edit-menu-items"></a>Ajouter des éléments de menu Edition 

Cette section explique comment ajouter des éléments de menu Edition au bas `Toolbar`. 

Pour ajouter des éléments de menu à un `Toolbar`secondaire : 

1. Ajoutez des icônes de menu aux dossiers `mipmap-` du projet d’application (si nécessaire).

2. Définissez le contenu des éléments de menu en ajoutant un fichier de ressources de menu supplémentaire à **ressources/menu**. 

3. Dans la méthode `OnCreate` de l’activité, recherchez le `Toolbar` (en appelant `FindViewById`) et Gonflez les menus de l' `Toolbar`.

4. Implémentez un gestionnaire de clic dans `OnCreate` pour les nouveaux éléments de menu. 

Les sections suivantes illustrent ce processus en détail : les éléments de menu **couper**, **copier**et **coller** sont ajoutés au bas `Toolbar`. 

### <a name="define-the-edit-menu-resource"></a>Définir la ressource du menu Edition

Dans le sous-répertoire **ressources/menu** , créez un nouveau fichier XML appelé **edit_menus. xml** et remplacez le contenu par le code XML suivant :

```xml
<?xml version="1.0" encoding="utf-8" ?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item
       android:id="@+id/menu_cut"
       android:icon="@mipmap/ic_menu_cut_holo_dark"
       android:showAsAction="ifRoom"
       android:title="Cut" />
  <item
       android:id="@+id/menu_copy"
       android:icon="@mipmap/ic_menu_copy_holo_dark"
       android:showAsAction="ifRoom"
       android:title="Copy" />
  <item
       android:id="@+id/menu_paste"
       android:icon="@mipmap/ic_menu_paste_holo_dark"
       android:showAsAction="ifRoom"
       android:title="Paste" />
</menu>
```

Ce code XML crée les éléments de menu **couper**, **copier**et **coller** (à l’aide des icônes qui ont été ajoutées aux dossiers `mipmap-` lors du [remplacement du barre d’action](~/android/user-interface/controls/tool-bar/replacing-the-action-bar.md)).

### <a name="inflate-the-menus"></a>Gonfler les menus

À la fin de la méthode `OnCreate` dans **MainActivity.cs**, ajoutez les lignes de code suivantes : 

```csharp
var editToolbar = FindViewById<Toolbar>(Resource.Id.edit_toolbar);
editToolbar.Title = "Editing";
editToolbar.InflateMenu (Resource.Menu.edit_menus);
editToolbar.MenuItemClick += (sender, e) => {
    Toast.MakeText(this, "Bottom toolbar tapped: " + e.Item.TitleFormatted, ToastLength.Short).Show();
};
```

Ce code localise la vue de `edit_toolbar` définie dans **main. AXML**, affecte à son titre la valeur **Editing**et déflate ses éléments de menu (définis dans **edit_menus. xml**). Il définit un gestionnaire de clic de menu qui affiche un toast pour indiquer l’icône de modification qui a été exploitée. 

Générez et exécutez l’application. Lorsque l’application s’exécute, le texte et les icônes ajoutés ci-dessus s’affichent comme indiqué ici : 

[Diagramme de ![de la barre d’outils inférieure avec les icônes couper, copier et coller](adding-a-second-toolbar-images/02-bottom-toolbar-sml.png)](adding-a-second-toolbar-images/02-bottom-toolbar.png#lightbox)

Si vous appuyez sur l’icône de menu **couper** , le Toast suivant s’affiche : 

[Capture d’écran ![de Toast indiquant que l’icône de menu couper a été frappée](adding-a-second-toolbar-images/03-bottom-tapped-sml.png)](adding-a-second-toolbar-images/03-bottom-tapped.png#lightbox)

Le fait de cliquer sur les éléments de menu de l’une des barres d’outils affiche les toasts résultants : 

[![des captures d’écran de toasts pour les éléments de menu enregistrer, copier et coller faisant l’objet d’un clic](adding-a-second-toolbar-images/04-menu-action-sml.png)](adding-a-second-toolbar-images/04-menu-action.png#lightbox)

## <a name="the-up-button"></a>Bouton haut 

La plupart des applications Android s’appuient sur le bouton **précédent** pour la navigation dans les applications. le fait d’appuyer sur le bouton **précédent** amène l’utilisateur à l’écran précédent.
Toutefois, vous pouvez également fournir un bouton **haut** qui permet aux utilisateurs de naviguer facilement vers l’écran principal de l’application. Lorsque l’utilisateur sélectionne le bouton **haut** , l’utilisateur passe à un niveau supérieur dans la hiérarchie de l’application &ndash; autrement dit, l’application dépile l’utilisateur de plusieurs activités dans la pile d’arrière-plan plutôt que de revenir à l’activité précédemment visitée. 

Pour activer le bouton **haut** dans une deuxième activité qui utilise une `Toolbar` comme barre d’action, appelez les méthodes `SetDisplayHomeAsUpEnabled` et `SetHomeButtonEnabled` dans la méthode `OnCreate` de la deuxième activité :

```csharp
SetActionBar (toolbar);
...
ActionBar.SetDisplayHomeAsUpEnabled (true);
ActionBar.SetHomeButtonEnabled (true);
```

L’exemple de code de la [barre d’outils support v7](https://docs.microsoft.com/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar) illustre le bouton **haut** en action. Cet exemple (qui utilise la bibliothèque AppCompat décrite ci-après) implémente une deuxième activité qui utilise le bouton **haut** de la barre d’outils pour la navigation hiérarchique vers l’activité précédente. Dans cet exemple, le `DetailActivity` bouton d’origine active le bouton **haut** en effectuant les appels de méthode `SupportActionBar` suivants : 

```csharp
SetSupportActionBar (toolbar);
...
SupportActionBar.SetDisplayHomeAsUpEnabled (true);
SupportActionBar.SetHomeButtonEnabled (true);
```

Quand l’utilisateur navigue d' `MainActivity` à `DetailActivity`, le `DetailActivity` affiche un bouton vers le **haut** (flèche pointant vers la gauche), comme indiqué dans la capture d’écran :

[Capture d’écran ![exemple de flèche gauche dans la barre d’outils](adding-a-second-toolbar-images/05-up-button-sml.png)](adding-a-second-toolbar-images/05-up-button.png#lightbox)

Si vous appuyez **sur ce bouton** , l’application revient à `MainActivity`. Dans une application plus complexe avec plusieurs niveaux de hiérarchie, le fait d’appuyer sur ce bouton permet de ramener l’utilisateur au niveau le plus élevé suivant dans l’application plutôt qu’à l’écran précédent. 

## <a name="related-links"></a>Liens associés

- [Barre d’outils Lollipop (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-toolbar)
- [Barre d’outils AppCompat (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar)
