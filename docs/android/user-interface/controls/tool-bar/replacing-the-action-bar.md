---
title: Remplacement de la barre d’action
ms.prod: xamarin
ms.assetid: 5341D28E-B203-478D-8464-6FAFDC3A4110
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/27/2018
ms.openlocfilehash: 1339833c9971c70ccb855dc340b12eb60bf22350
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457664"
---
# <a name="replacing-the-action-bar"></a>Remplacement de la barre d’action

## <a name="overview"></a>Vue d’ensemble

L’une des utilisations les plus courantes de `Toolbar` est de remplacer la barre d’action par défaut par une valeur personnalisée `Toolbar` (lorsqu’un nouveau projet Android est créé, elle utilise la barre d’action par défaut). Étant donné que le `Toolbar` offre la possibilité d’ajouter des logos, des titres, des éléments de menu, des boutons de navigation et même des vues personnalisées à la section de la barre d’application de l’interface utilisateur d’une activité, il offre une mise à niveau significative sur la barre d’action par défaut.

Pour remplacer la barre d’action par défaut d’une application par un `Toolbar` : 

1. Créez un nouveau thème personnalisé et modifiez les propriétés de l’application afin qu’elle utilise ce nouveau thème. 

2. Désactivez l' `windowActionBar` attribut dans le thème personnalisé et activez l' `windowNoTitle` attribut.

3. Définissez une disposition pour le `Toolbar` .

4. Incluez la `Toolbar` disposition dans le fichier de disposition **principal. AXML** de l’activité. 

5. Ajoutez du code à la méthode de l’activité `OnCreate` pour rechercher `Toolbar` et appeler `SetActionBar` pour installer le `ToolBar` comme barre d’action.

Les sections suivantes expliquent ce processus en détail. Une application simple est créée et sa barre d’action est remplacée par une application personnalisée `Toolbar` . 

## <a name="start-an-app-project"></a>Démarrer un projet d’application

Créez un nouveau projet Android appelé **ToolbarFun** (consultez [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md) pour plus d’informations sur la création d’un projet Android). Une fois ce projet créé, définissez les niveaux d’API Android cible et minimum sur **android 5,0 (niveau d’API 21-Lollipop)** ou version ultérieure. Pour plus d’informations sur la définition des niveaux de version Android, consultez [Présentation des niveaux d’API Android](~/android/app-fundamentals/android-api-levels.md). Lorsque l’application est générée et exécutée, elle affiche la barre d’actions par défaut comme illustré dans cette capture d’écran :

[![Capture d’écran de la barre d’action par défaut](replacing-the-action-bar-images/01-before-sml.png)](replacing-the-action-bar-images/01-before.png#lightbox)

## <a name="create-a-custom-theme"></a>Créer un thème personnalisé

Ouvrez le répertoire **Resources/values** et créez un nouveau fichier appelé **styles.xml**. Remplacez son contenu par le code XML suivant : 

```xml
<?xml version="1.0" encoding="utf-8" ?>
<resources>
  <style name="MyTheme" parent="@android:style/Theme.Material.Light.DarkActionBar">
    <item name="android:windowNoTitle">true</item>
    <item name="android:windowActionBar">false</item>
    <item name="android:colorPrimary">#5A8622</item>
  </style>
</resources>
```

Ce code XML définit un nouveau thème personnalisé appelé **mytheme** basé sur le thème **Theme. Material. Light. DarkActionBar** dans Lollipop. L' `windowNoTitle` attribut a la valeur `true` pour masquer la barre de titre : 

```xml
<item name="android:windowNoTitle">true</item>
```

Pour afficher la barre d’outils personnalisée, la valeur par défaut `ActionBar` doit être désactivée : 

```xml
<item name="android:windowActionBar">false</item>
```

Un paramètre vert olive `colorPrimary` est utilisé pour la couleur d’arrière-plan de la barre d’outils : 

```xml
<item name="android:colorPrimary">#5A8622</item>
```

## <a name="apply-the-custom-theme"></a>Appliquer le thème personnalisé

Modifiez les **Propriétés/AndroidManifest.xml** et ajoutez l' `android:theme` attribut suivant à l' `<application>` élément afin que l’application utilise le `MyTheme` thème personnalisé : 

```xml
<application android:label="@string/app_name" android:theme="@style/MyTheme"></application>
```

Pour plus d’informations sur l’application d’un thème personnalisé à une application, consultez [utilisation de thèmes personnalisés](~/android/user-interface/material-theme.md#customtheme). 

## <a name="define-a-toolbar-layout"></a>Définir une disposition de barre d’outils

Dans le répertoire **Resources/layout** , créez un nouveau fichier appelé **toolbar.xml**. Remplacez son contenu par le code XML suivant : 

```xml
<?xml version="1.0" encoding="utf-8"?>
<Toolbar xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:minHeight="?android:attr/actionBarSize"
    android:background="?android:attr/colorPrimary"
    android:theme="@android:style/ThemeOverlay.Material.Dark.ActionBar"/>
```

Ce code XML définit le personnalisé `Toolbar` qui remplace la barre d’action par défaut. La hauteur minimale du `Toolbar` est définie sur la taille de la barre d’action qu’elle remplace : 

```csharp
android:minHeight="?android:attr/actionBarSize"
```

La couleur d’arrière-plan du `Toolbar` est définie sur la couleur vert olive définie précédemment dans **styles.xml**:

```csharp
android:background="?android:attr/colorPrimary"
```

À partir de l’interface Lollipop, l' `android:theme` attribut peut être utilisé pour appliquer un style à une vue individuelle. Les `ThemeOverlay.Material` thèmes introduits dans le symbole Lollipop permettent de superposer les thèmes par défaut `Theme.Material` , en remplaçant les attributs appropriés pour les rendre clairs ou sombres. Dans cet exemple, le `Toolbar` utilise un thème sombre pour que son contenu soit de couleur claire : 

```csharp
android:theme="@android:style/ThemeOverlay.Material.Dark.ActionBar"
```

Ce paramètre est utilisé afin que les éléments de menu contrastent avec la couleur d’arrière-plan la plus sombre.

## <a name="include-the-toolbar-layout"></a>Inclure la disposition de la barre d’outils

Modifiez le fichier de disposition **Resources/layout/main. AXML** et remplacez son contenu par le code XML suivant :

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <include
        android:id="@+id/toolbar"
        layout="@layout/toolbar" />
</RelativeLayout>
```

Cette disposition comprend le `Toolbar` défini dans **toolbar.xml** et utilise un `RelativeLayout` pour spécifier que le `Toolbar` doit être placé en haut de l’interface utilisateur (au-dessus du bouton). 

## <a name="find-and-activate-the-toolbar"></a>Rechercher et activer la barre d’outils

Modifiez **MainActivity.cs** et ajoutez l’instruction using suivante :

```csharp
using Android.Views;
```

Ajoutez également les lignes de code suivantes à la fin de la `OnCreate` méthode :

```csharp
var toolbar = FindViewById<Toolbar>(Resource.Id.toolbar);
SetActionBar(toolbar);
ActionBar.Title = "My Toolbar";
```

Ce code recherche les `Toolbar` appels et `SetActionBar` afin que l' `Toolbar` prenne les caractéristiques de la barre d’action par défaut. Le titre de la barre d’outils est remplacé par **mon barre d’outils**. Comme illustré dans cet exemple de code, le `ToolBar` peut être directement référencé comme une barre d’action. Compiler et exécuter cette application &ndash; le personnalisé `Toolbar` s’affiche à la place de la barre d’action par défaut : 

[![Capture d’écran de la barre d’outils personnalisée avec le modèle de couleurs vert](replacing-the-action-bar-images/02-after-sml.png)](replacing-the-action-bar-images/02-after.png#lightbox)

Notez que le `Toolbar` style est appliqué indépendamment du `Theme.Material.Light.DarkActionBar` thème qui est appliqué au reste de l’application. 

Si une exception se produit lors de l’exécution de l’application, consultez la section [résolution des problèmes](#troubleshooting) ci-dessous.

## <a name="add-menu-items"></a>Ajouter des éléments de menu 

Dans cette section, les menus sont ajoutés au `Toolbar` . La zone supérieure droite de la `ToolBar` est réservée aux éléments de menu &ndash; chaque élément de menu (également appelé *élément d’action*) peut exécuter une action dans l’activité en cours ou elle peut effectuer une action pour le compte de l’application entière. 

Pour ajouter des menus à `Toolbar` : 

1. Ajoutez des icônes de menu (si nécessaire) aux `mipmap-` dossiers du projet d’application. Google fournit un ensemble d’icônes de menu gratuites sur la page [icônes de matériau](https://design.google.com/icons/) . 

2. Définissez le contenu des éléments de menu en ajoutant un nouveau fichier de ressources de menu sous **ressources/menu**. 

3. Implémentez la `OnCreateOptionsMenu` méthode de l’activité que &ndash;  cette méthode fixe les éléments de menu. 

4. Implémentez la `OnOptionsItemSelected` méthode de l’activité dont  &ndash; cette méthode effectue une action lorsqu’un élément de menu est frappé. 

Les sections suivantes illustrent ce processus en détail en ajoutant des éléments de menu **modifier** et **Enregistrer** au personnalisé `Toolbar` . 

### <a name="install-menu-icons"></a>Icônes du menu d’installation

En poursuivant avec l' `ToolbarFun` exemple d’application, ajoutez des icônes de menu au projet d’application. Téléchargez les [icônes de barre d’outils](https://github.com/xamarin/monodroid-samples/blob/master/Supportv7/AppCompat/Toolbar/Resources/toolbar-icons-plus.zip?raw=true), décompressez et copiez le contenu des dossiers *mipmap-* Folders extraits dans le projet *mipmap-* Folders sous **ToolbarFun/Resources** et incluez chaque fichier d’icône ajouté dans le projet.

### <a name="define-a-menu-resource"></a>Définir une ressource de menu

Créez un sous-répertoire de **menu** sous **ressources**. Dans le sous-répertoire du **menu** , créez un fichier de ressources de menu appelé **top_menus.xml** et remplacez son contenu par le code XML suivant : 

```xml
<?xml version="1.0" encoding="utf-8" ?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item
       android:id="@+id/menu_edit"
       android:icon="@mipmap/ic_action_content_create"
       android:showAsAction="ifRoom"
       android:title="Edit" />
  <item
       android:id="@+id/menu_save"
       android:icon="@mipmap/ic_action_content_save"
       android:showAsAction="ifRoom"
       android:title="Save" />
  <item
       android:id="@+id/menu_preferences"
       android:showAsAction="never"
       android:title="Preferences" />
</menu>
```

Ce code XML crée trois éléments de menu :

- Élément de menu **Edition** qui utilise l' `ic_action_content_create.png` icône (crayon). 

- Un élément de menu **Enregistrer** qui utilise l' `ic_action_content_save.png` icône (disquette). 

- Élément de menu **Préférences** qui n’a pas d’icône.

Les `showAsAction` attributs des éléments de menu **modifier** et **Enregistrer** ont la valeur de `ifRoom` &ndash; ce paramètre, ces éléments de menu s’affichent dans le `Toolbar` s’il y a suffisamment d’espace pour s’afficher. L’élément de menu **Préférences** définit cette option pour `showAsAction` `never` &ndash; afficher le menu **Préférences** dans le menu de *dépassement de capacité* (trois points verticaux). 

### <a name="implement-oncreateoptionsmenu"></a>Implémenter OnCreateOptionsMenu

Ajoutez la méthode suivante à **MainActivity.cs**:

```csharp
public override bool OnCreateOptionsMenu(IMenu menu)
{
    MenuInflater.Inflate(Resource.Menu.top_menus, menu);
    return base.OnCreateOptionsMenu(menu);
}
```

Android appelle la `OnCreateOptionsMenu` méthode afin que l’application puisse spécifier la ressource de menu d’une activité. Dans cette méthode, la ressource **top_menus.xml** est augmentée dans le passé `menu` . Ce code fait apparaître les nouveaux éléments de menu **modifier**, **Enregistrer**et **Préférences** dans le `Toolbar` . 

### <a name="implement-onoptionsitemselected"></a>Implémenter OnOptionsItemSelected

Ajoutez la méthode suivante à **MainActivity.cs**:

```csharp
public override bool OnOptionsItemSelected(IMenuItem item)
{
    Toast.MakeText(this, "Action selected: " + item.TitleFormatted,
        ToastLength.Short).Show();
    return base.OnOptionsItemSelected(item);
}
```

Quand un utilisateur clique sur un élément de menu, Android appelle la `OnOptionsItemSelected` méthode et passe dans l’élément de menu sélectionné. Dans cet exemple, l’implémentation affiche simplement un toast pour indiquer l’élément de menu qui a été frappé. 

Générez et exécutez `ToolbarFun` pour afficher les nouveaux éléments de menu dans la barre d’outils. `Toolbar`Affiche désormais trois icônes de menu, comme illustré dans cette capture d’écran : 

[![Diagramme illustrant les emplacements des éléments de menu modifier, enregistrer et déborder](replacing-the-action-bar-images/04-menu-items-sml.png)](replacing-the-action-bar-images/04-menu-items.png#lightbox)

Lorsqu’un utilisateur clique sur l’élément de menu **modifier** , un toast s’affiche pour indiquer que la `OnOptionsItemSelected` méthode a été appelée : 

[![Capture d’écran du Toast affiché lorsque la modification de l’élément est exploitée](replacing-the-action-bar-images/05-toast-displayed-sml.png)](replacing-the-action-bar-images/05-toast-displayed.png#lightbox)

Quand un utilisateur clique sur le menu de dépassement de capacité, l’élément de menu **Préférences** s’affiche. En règle générale, les actions moins courantes doivent être placées dans le menu de dépassement de capacité &ndash; . cet exemple utilise le menu de dépassement de capacité pour les **Préférences** , car il n’est pas utilisé aussi souvent que **modifier** et **Enregistrer**: 

[![Capture d’écran de l’élément de menu Préférences qui apparaît dans le menu de dépassement de capacité](replacing-the-action-bar-images/06-preferences-sml.png)](replacing-the-action-bar-images/06-preferences.png#lightbox)

Pour plus d’informations sur les menus Android, consultez la rubrique sur les [menus](https://developer.android.com/guide/topics/ui/menus.html) Android Developer. 

## <a name="troubleshooting"></a>Résolution des problèmes

Les conseils suivants peuvent vous aider à déboguer des problèmes qui peuvent se produire lors du remplacement de la barre d’action par une barre d’outils.

### <a name="activity-already-has-an-action-bar"></a>L’activité a déjà un Barre d’action

Si l’application n’est pas correctement configurée pour utiliser un thème personnalisé comme expliqué dans [appliquer le thème personnalisé](#apply-the-custom-theme), l’exception suivante peut se produire lors de l’exécution de l’application :

![Erreur qui peut se produire lorsque le thème personnalisé n’est pas utilisé](replacing-the-action-bar-images/03-theme-not-defined.png)

En outre, un message d’erreur tel que le suivant peut être produit : _java. lang. IllegalStateException : cette activité a déjà une barre d’action fournie par la fenêtre décor._ 

Pour corriger cette erreur, vérifiez que l' `android:theme` attribut du thème personnalisé est ajouté à `<application>` (dans **Properties/AndroidManifest.xml**) comme décrit précédemment dans [appliquer le thème personnalisé](#apply-the-custom-theme). En outre, cette erreur peut se produire si la `Toolbar` disposition ou le thème personnalisé n’est pas configuré correctement.

## <a name="related-links"></a>Liens associés

- [Barre d’outils Lollipop (exemple)](/samples/xamarin/monodroid-samples/android50-toolbar)
- [Barre d’outils AppCompat (exemple)](/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar)