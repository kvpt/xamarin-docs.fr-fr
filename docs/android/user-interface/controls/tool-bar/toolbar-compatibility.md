---
title: Compatibilité de la barre d’outils
ms.prod: xamarin
ms.assetid: A0798CA1-2C7D-43B6-9E91-4435CC7B6683
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/15/2018
ms.openlocfilehash: 809dc8ec8fd1106b8ad8631c0c506067abdf0d97
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029068"
---
# <a name="toolbar-compatibility"></a>Compatibilité de la barre d’outils

## <a name="overview"></a>Vue d'ensemble

Cette section explique comment utiliser `Toolbar` sur les versions d’Android antérieures à l’interface Lollipop Android 5,0. Si votre application ne prend pas en charge les versions d’Android antérieures à Android 5,0, vous pouvez ignorer cette section. 

Étant donné que `Toolbar` fait partie de la bibliothèque de prise en charge Android v7, il peut être utilisé sur les appareils exécutant Android 2,1 (niveau d’API 7) et versions ultérieures. Toutefois, le NuGet de la [bibliothèque de prise en charge Android](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) doit être installé et le code modifié pour qu’il utilise l’implémentation `Toolbar` fournie dans cette bibliothèque. Cette section explique comment installer ce NuGet et modifier l’application **ToolbarFun** à partir de l' [Ajout d’une deuxième barre d’outils](~/android/user-interface/controls/tool-bar/adding-a-second-toolbar.md) afin qu’elle s’exécute sur les versions d’Android antérieures à la version Lollipop 5,0.

Pour modifier une application afin d’utiliser la version AppCompat de la barre d’outils : 

1. Définissez les versions d’Android minimale et cible pour l’application.

2. Installez le package NuGet AppCompat.

3. Utilisez un thème AppCompat au lieu d’un thème Android intégré.

4. Modifiez `MainActivity` pour qu’il sous-classe `AppCompatActivity` plutôt que `Activity`. 

Chacune de ces étapes est décrite en détail dans les sections suivantes.

## <a name="set-the-minimum-and-target-android-version"></a>Définir la version Android minimale et cible

Le Framework cible de l’application doit être défini sur l’API de niveau 21 ou supérieur, sinon l’application ne sera pas déployée correctement. Si une erreur telle qu' **aucun identificateur de ressource trouvé pour l’attribut’tileModeX’dans le package’Android'** s’affiche lors du déploiement de l’application, cela est dû au fait que la version cible de .NET Framework n’est pas définie sur **Android 5,0 (niveau d’API 21-Lollipop)** ou supérieur. 

Affectez au niveau de l’infrastructure cible le niveau d’API 21 ou supérieur et définissez les paramètres du projet au niveau de l’API Android sur la version Android minimale que l’application doit prendre en charge. Pour plus d’informations sur la définition des niveaux d’API Android, consultez [Présentation des niveaux d’API Android](~/android/app-fundamentals/android-api-levels.md). Dans l’exemple `ToolbarFun`, la version minimale d’Android est définie sur KitKat (niveau d’API 4,4). 

## <a name="install-the-appcompat-nuget-package"></a>Installer le package NuGet AppCompat

Ensuite, ajoutez le package de la [bibliothèque de prise en charge Android v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) au projet. Dans Visual Studio, cliquez avec le bouton droit sur **références** , puis sélectionnez **gérer les packages NuGet...** . Cliquez sur **Parcourir** et recherchez **bibliothèque de prise en charge Android v7 AppCompat**. Sélectionnez **Xamarin. Android. support. v7. AppCompat** , puis cliquez sur **installer**: 

[Capture d’écran ![du package AppCompat v7 sélectionné dans gérer les packages NuGet](toolbar-compatibility-images/01-appcompat-nuget-sml.png)](toolbar-compatibility-images/01-appcompat-nuget.png#lightbox)

Quand ce NuGet est installé, plusieurs autres packages NuGet sont également installés s’ils ne sont pas déjà présents (tels que **Xamarin. Android. support. animated. Vector. Drawable**, **Xamarin. Android. support. v4**et  **Xamarin. Android. support. Vector. Drawable**). Pour plus d’informations sur l’installation des packages NuGet, consultez [procédure pas à pas : inclusion d’un NuGet dans votre projet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough). 

## <a name="use-an-appcompat-theme-and-toolbar"></a>Utiliser un thème et une barre d’outils AppCompat

La bibliothèque AppCompat est fournie avec plusieurs thèmes de `Theme.AppCompat` qui peuvent être utilisés sur n’importe quelle version d’Android prise en charge par la bibliothèque AppCompat. Le thème de l’exemple d’application `ToolbarFun` est dérivé de `Theme.Material.Light.DarkActionBar`, qui n’est pas disponible sur les versions d’Android antérieures à Lollipop. Par conséquent, `ToolbarFun` doit être adapté pour utiliser l’équivalent AppCompat de ce thème, `Theme.AppCompat.Light.DarkActionBar`. En outre, étant donné que `Toolbar` n’est pas disponible sur les versions d’Android antérieures à Lollipop, nous devons utiliser la version AppCompat de `Toolbar`. Par conséquent, les dispositions doivent utiliser `android.support.v7.widget.Toolbar` au lieu de `Toolbar`. 

### <a name="update-layouts"></a>Mettre à jour les dispositions

Modifiez **Resources/layout/main. AXML** et remplacez l’élément `Toolbar` par le code XML suivant : 

```xml
<android.support.v7.widget.Toolbar
    android:id="@+id/edit_toolbar"
    android:minHeight="?attr/actionBarSize"
    android:background="?attr/colorAccent"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

Modifiez **ressources/disposition/Toolbar. xml** et remplacez son contenu par le code XML suivant : 

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

Notez que les valeurs de `?attr` ne sont plus précédées de `android:` (Rappelez-vous que la notation `?` référence une ressource dans le thème actuel). Si `?android:attr` étaient toujours utilisés ici, Android référencerait la valeur d’attribut à partir de la plateforme en cours d’exécution plutôt qu’à partir de la bibliothèque AppCompat. Étant donné que cet exemple utilise la `actionBarSize` définie par la bibliothèque AppCompat, le préfixe `android:` est supprimé. De même, `@android:style` est remplacé par `@style` afin que l’attribut `android:theme` soit défini sur un thème de la bibliothèque AppCompat &ndash; le thème `ThemeOverlay.AppCompat.Dark.ActionBar` est utilisé ici plutôt que `ThemeOverlay.Material.Dark.ActionBar`. 

### <a name="update-the-style"></a>Mettre à jour le style

Modifiez le fichier **Resources/values/styles. xml** et remplacez son contenu par le code XML suivant : 

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

Les noms d’éléments et le thème parent de cet exemple ne sont plus précédés de `android:`, car nous utilisons la bibliothèque AppCompat. En outre, le thème parent est remplacé par la version AppCompat de `Light.DarkActionBar`. 

### <a name="update-menus"></a>Mettre à jour les menus

Pour prendre en charge les versions antérieures d’Android, la bibliothèque AppCompat utilise des attributs personnalisés qui reflètent les attributs de l’espace de noms `android:`. Toutefois, certains attributs (tels que l’attribut `showAsAction` utilisé dans la balise `<menu>`) n’existent pas dans l’infrastructure Android sur les appareils plus anciens &ndash; `showAsAction` a été introduit dans Android API 11, mais n’est pas disponible dans Android API 7. Pour cette raison, un espace de noms personnalisé doit être utilisé pour préfixer tous les attributs définis par la bibliothèque de prise en charge. Dans les fichiers de ressources de menu, un espace de noms appelé `local` est défini pour le préfixe de l’attribut `showAsAction`. 

Modifiez les **ressources/menu/top_menus. xml** et remplacez son contenu par le code XML suivant :

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

L’espace de noms `local` est ajouté avec cette ligne :

```xml
xmlns:local="http://schemas.android.com/apk/res-auto">
```

L’attribut `showAsAction` est précédé de cet espace de noms de `local:` plutôt que `android:` 

```csharp
local:showAsAction="ifRoom"
```

De même, modifiez les **ressources/menu/edit_menus. xml** et remplacez son contenu par le code XML suivant :

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

Comment ce commutateur d’espace de noms assure-t-il la prise en charge de l’attribut `showAsAction` sur les versions d’Android antérieures au niveau d’API 11 ? L’attribut personnalisé `showAsAction` et toutes ses valeurs possibles sont inclus dans l’application lorsque le NuGet AppCompat est installé. 

## <a name="subclass-appcompatactivity"></a>Sous-classe AppCompatActivity

La dernière étape de la conversion consiste à modifier `MainActivity` afin qu’il s’agit d’une sous-classe de `AppCompactActivity`. Modifiez **MainActivity.cs** et ajoutez les instructions `using` suivantes : 

```csharp
using Android.Support.V7.App;
using Toolbar = Android.Support.V7.Widget.Toolbar;
```

Cela déclare `Toolbar` comme étant la version AppCompat de `Toolbar`. Ensuite, modifiez la définition de classe de `MainActivity`: 

```csharp
public class MainActivity : AppCompatActivity
```

Pour définir la barre d’action sur la version AppCompat de `Toolbar`, remplacez l’appel à `SetActionBar` par `SetSupportActionBar`. Dans cet exemple, le titre est également modifié pour indiquer que la version AppCompat de `Toolbar` est utilisée :

```csharp
SetSupportActionBar (toolbar);
SupportActionBar.Title = "My AppCompat Toolbar";
```

Enfin, remplacez le niveau Android minimal par la valeur de pré-Lollipop qui doit être prise en charge (par exemple, l’API 19). 

Générez l’application et exécutez-la sur un appareil pre-Lollipop ou un émulateur Android. La capture d’écran suivante montre la version AppCompat de **ToolbarFun** sur un point de contrôle 4 exécutant KitKat (API 19) : 

[![capture d’écran complète de l’application exécutée sur un appareil KitKat, les deux barres d’outils sont affichées](toolbar-compatibility-images/02-running-on-kitkat-sml.png)](toolbar-compatibility-images/02-running-on-kitkat.png#lightbox)

Lorsque la bibliothèque AppCompat est utilisée, les thèmes n’ont pas besoin d’être basculés en fonction de la version d’Android &ndash; la bibliothèque AppCompat permet d’offrir une expérience utilisateur cohérente sur toutes les versions Android prises en charge. 

## <a name="related-links"></a>Liens associés

- [Barre d’outils Lollipop (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-toolbar)
- [Barre d’outils AppCompat (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar)
