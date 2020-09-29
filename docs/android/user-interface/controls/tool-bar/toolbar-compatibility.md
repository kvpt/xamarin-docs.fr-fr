---
title: Compatibilité de la barre d’outils
ms.prod: xamarin
ms.assetid: A0798CA1-2C7D-43B6-9E91-4435CC7B6683
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/15/2018
ms.openlocfilehash: 8d5f5ff1cfe7876862371a9732f0ab8186bbeeba
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457622"
---
# <a name="toolbar-compatibility"></a>Compatibilité de la barre d’outils

## <a name="overview"></a>Vue d’ensemble

Cette section explique comment utiliser `Toolbar` sur les versions d’Android antérieures à l’interface Lollipop android 5,0. Si votre application ne prend pas en charge les versions d’Android antérieures à Android 5,0, vous pouvez ignorer cette section. 

Dans `Toolbar` la mesure où fait partie de la bibliothèque de prise en charge Android v7, il peut être utilisé sur des appareils exécutant android 2,1 (niveau d’API 7) et ultérieur. Toutefois, le NuGet de la [bibliothèque de prise en charge Android](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) doit être installé et le code modifié pour qu’il utilise l' `Toolbar` implémentation fournie dans cette bibliothèque. Cette section explique comment installer ce NuGet et modifier l’application **ToolbarFun** à partir de l' [Ajout d’une deuxième barre d’outils](~/android/user-interface/controls/tool-bar/adding-a-second-toolbar.md) afin qu’elle s’exécute sur les versions d’Android antérieures à la version Lollipop 5,0.

Pour modifier une application afin d’utiliser la version AppCompat de la barre d’outils : 

1. Définissez les versions d’Android minimale et cible pour l’application.

2. Installez le package NuGet AppCompat.

3. Utilisez un thème AppCompat au lieu d’un thème Android intégré.

4. Modifiez `MainActivity` afin qu’il sous-classe `AppCompatActivity` plutôt que `Activity` . 

Chacune de ces étapes est décrite en détail dans les sections suivantes.

## <a name="set-the-minimum-and-target-android-version"></a>Définir la version Android minimale et cible

Le Framework cible de l’application doit être défini sur l’API de niveau 21 ou supérieur, sinon l’application ne sera pas déployée correctement. Si une erreur telle qu' **aucun identificateur de ressource trouvé pour l’attribut’tileModeX’dans le package’Android'** s’affiche lors du déploiement de l’application, cela est dû au fait que la version cible de .NET Framework n’est pas définie sur **Android 5,0 (niveau d’API 21-Lollipop)** ou supérieur. 

Affectez au niveau de l’infrastructure cible le niveau d’API 21 ou supérieur et définissez les paramètres du projet au niveau de l’API Android sur la version Android minimale que l’application doit prendre en charge. Pour plus d’informations sur la définition des niveaux d’API Android, consultez [Présentation des niveaux d’API Android](~/android/app-fundamentals/android-api-levels.md). Dans l' `ToolbarFun` exemple, la version minimale d’Android est définie sur KitKat (niveau d’API 4,4). 

## <a name="install-the-appcompat-nuget-package"></a>Installer le package NuGet AppCompat

Ensuite, ajoutez le package de la [bibliothèque de prise en charge Android v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) au projet. Dans Visual Studio, cliquez avec le bouton droit sur **références** , puis sélectionnez **gérer les packages NuGet...**. Cliquez sur **Parcourir** et recherchez **bibliothèque de prise en charge Android v7 AppCompat**. Sélectionnez **Xamarin. Android. support. v7. AppCompat** , puis cliquez sur **installer**: 

[![Capture d’écran du package AppCompat v7 sélectionné dans gérer les packages NuGet](toolbar-compatibility-images/01-appcompat-nuget-sml.png)](toolbar-compatibility-images/01-appcompat-nuget.png#lightbox)

Quand ce NuGet est installé, plusieurs autres packages NuGet sont également installés s’ils ne sont pas déjà présents (tels que **Xamarin. Android. support. animated. Vector. Drawing**, **Xamarin. Android. support. v4**et **Xamarin. Android. support. Vector. Drawable**). Pour plus d’informations sur l’installation des packages NuGet, consultez [procédure pas à pas : inclusion d’un NuGet dans votre projet](/visualstudio/mac/nuget-walkthrough). 

## <a name="use-an-appcompat-theme-and-toolbar"></a>Utiliser un thème et une barre d’outils AppCompat

La bibliothèque AppCompat est fournie avec plusieurs `Theme.AppCompat` thèmes qui peuvent être utilisés sur n’importe quelle version d’Android prise en charge par la bibliothèque AppCompat. L' `ToolbarFun` exemple de thème d’application est dérivé de `Theme.Material.Light.DarkActionBar` , ce qui n’est pas disponible sur les versions d’Android antérieures à Lollipop. Par conséquent, `ToolbarFun` doit être adapté pour utiliser l’équivalent AppCompat de ce thème, `Theme.AppCompat.Light.DarkActionBar` . En outre, étant donné que `Toolbar` n’est pas disponible sur les versions d’Android antérieures à Lollipop, nous devons utiliser la version AppCompat de `Toolbar` . Par conséquent, les dispositions doivent utiliser à la `android.support.v7.widget.Toolbar` place de `Toolbar` . 

### <a name="update-layouts"></a>Mettre à jour les dispositions

Modifiez **Resources/layout/main. AXML** et remplacez l' `Toolbar` élément par le code XML suivant : 

```xml
<android.support.v7.widget.Toolbar
    android:id="@+id/edit_toolbar"
    android:minHeight="?attr/actionBarSize"
    android:background="?attr/colorAccent"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

Modifiez les **ressources/disposition/toolbar.xml** et remplacez son contenu par le code XML suivant : 

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v7.widget.Toolbar xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:minHeight="?attr/actionBarSize"
    android:background="?attr/colorPrimary"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"/>
```

Notez que les `?attr` valeurs ne sont plus précédées de `android:` (Rappelez-vous que la `?` notation fait référence à une ressource dans le thème actuel). Si vous `?android:attr` étiez toujours utilisé ici, Android fait référence à la valeur d’attribut de la plateforme en cours d’exécution plutôt qu’à partir de la bibliothèque AppCompat. Étant donné que cet exemple utilise le `actionBarSize` défini par la bibliothèque AppCompat, le `android:` préfixe est supprimé. De même, `@android:style` est remplacé par `@style` afin que l' `android:theme` attribut soit défini sur un thème de la bibliothèque AppCompat &ndash; `ThemeOverlay.AppCompat.Dark.ActionBar` . le thème est utilisé ici plutôt que `ThemeOverlay.Material.Dark.ActionBar` . 

### <a name="update-the-style"></a>Mettre à jour le style

Modifiez les **ressources/valeurs/styles.xml** et remplacez son contenu par le code XML suivant : 

```xml
<?xml version="1.0" encoding="utf-8" ?>
<resources>
  <style name="MyTheme" parent="MyTheme.Base"> </style>
  <style name="MyTheme.Base" parent="Theme.AppCompat.Light.DarkActionBar">
    <item name="windowNoTitle">true</item>
    <item name="windowActionBar">false</item>
    <item name="colorPrimary">#5A8622</item>
    <item name="colorAccent">#A88F2D</item>
  </style>
</resources>
```

Les noms d’éléments et le thème parent de cet exemple ne sont plus précédés de `android:` , car nous utilisons la bibliothèque AppCompat. En outre, le thème parent est remplacé par la version AppCompat de `Light.DarkActionBar` . 

### <a name="update-menus"></a>Mettre à jour les menus

Pour prendre en charge les versions antérieures d’Android, la bibliothèque AppCompat utilise des attributs personnalisés qui reflètent les attributs de l' `android:` espace de noms. Toutefois, certains attributs (tels que l' `showAsAction` attribut utilisé dans la `<menu>` balise) n’existent pas dans l’infrastructure Android sur les appareils plus anciens &ndash; `showAsAction` ont été introduits dans l’API Android 11, mais ils ne sont pas disponibles dans Android API 7. Pour cette raison, un espace de noms personnalisé doit être utilisé pour préfixer tous les attributs définis par la bibliothèque de prise en charge. Dans les fichiers de ressources de menu, un espace de noms appelé `local` est défini pour le préfixe de l' `showAsAction` attribut. 

Modifiez les **ressources/menu/top_menus.xml** et remplacez son contenu par le code XML suivant :

```xml
<?xml version="1.0" encoding="utf-8" ?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:local="http://schemas.android.com/apk/res-auto">
  <item
       android:id="@+id/menu_edit"
       android:icon="@mipmap/ic_action_content_create"
       local:showAsAction="ifRoom"
       android:title="Edit" />
  <item
       android:id="@+id/menu_save"
       android:icon="@mipmap/ic_action_content_save"
       local:showAsAction="ifRoom"
       android:title="Save" />
  <item
       android:id="@+id/menu_preferences"
       local:showAsAction="never"
       android:title="Preferences" />
</menu>
```

L' `local` espace de noms est ajouté avec cette ligne :

```xml
xmlns:local="http://schemas.android.com/apk/res-auto">
```

L' `showAsAction` attribut est précédé de cet `local:` espace de noms plutôt que `android:` 

```csharp
local:showAsAction="ifRoom"
```

De même, modifiez les **ressources/menu/edit_menus.xml** et remplacez son contenu par le code XML suivant :

```xml
<?xml version="1.0" encoding="utf-8" ?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:local="http://schemas.android.com/apk/res-auto">
  <item
       android:id="@+id/menu_cut"
       android:icon="@mipmap/ic_menu_cut_holo_dark"
       local:showAsAction="ifRoom"
       android:title="Cut" />
  <item
       android:id="@+id/menu_copy"
       android:icon="@mipmap/ic_menu_copy_holo_dark"
       local:showAsAction="ifRoom"
       android:title="Copy" />
  <item
       android:id="@+id/menu_paste"
       android:icon="@mipmap/ic_menu_paste_holo_dark"
       local:showAsAction="ifRoom"
       android:title="Paste" />
</menu>
```

Comment ce commutateur d’espace de noms assure-t-il la prise en charge de l' `showAsAction` attribut sur les versions d’Android antérieures au niveau d’API 11 ? L’attribut personnalisé `showAsAction` et toutes ses valeurs possibles sont inclus dans l’application lorsque le NuGet AppCompat est installé. 

## <a name="subclass-appcompatactivity"></a>Sous-classe AppCompatActivity

La dernière étape de la conversion consiste à modifier `MainActivity` afin qu’il s’agit d’une sous-classe de `AppCompactActivity` . Modifiez **MainActivity.cs** et ajoutez les `using` instructions suivantes : 

```csharp
using Android.Support.V7.App;
using Toolbar = Android.Support.V7.Widget.Toolbar;
```

Cela déclare qu’il `Toolbar` s’agit de la version AppCompat de `Toolbar` . Ensuite, modifiez la définition de classe de `MainActivity` : 

```csharp
public class MainActivity : AppCompatActivity
```

Pour définir la barre d’action sur la version AppCompat de `Toolbar` , remplacez l’appel à `SetActionBar` par `SetSupportActionBar` . Dans cet exemple, le titre est également modifié pour indiquer que la version AppCompat de `Toolbar` est en cours d’utilisation :

```csharp
SetSupportActionBar (toolbar);
SupportActionBar.Title = "My AppCompat Toolbar";
```

Enfin, remplacez le niveau Android minimal par la valeur de pré-Lollipop qui doit être prise en charge (par exemple, l’API 19). 

Générez l’application et exécutez-la sur un appareil pre-Lollipop ou un émulateur Android. La capture d’écran suivante montre la version AppCompat de **ToolbarFun** sur un point de contrôle 4 exécutant KitKat (API 19) : 

[![Capture d’écran complète de l’application exécutée sur un appareil KitKat, les deux barres d’outils sont affichées](toolbar-compatibility-images/02-running-on-kitkat-sml.png)](toolbar-compatibility-images/02-running-on-kitkat.png#lightbox)

Lorsque la bibliothèque AppCompat est utilisée, les thèmes n’ont pas besoin d’être basculés en fonction de la version Android &ndash; . la bibliothèque AppCompat permet de fournir une expérience utilisateur cohérente sur toutes les versions d’Android prises en charge. 

## <a name="related-links"></a>Liens associés

- [Barre d’outils Lollipop (exemple)](/samples/xamarin/monodroid-samples/android50-toolbar)
- [Barre d’outils AppCompat (exemple)](/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar)