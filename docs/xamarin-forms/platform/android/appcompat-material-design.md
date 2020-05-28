---
title: ''
description: Cet article explique comment convertir Xamarin.Forms des applications Android existantes pour utiliser une conception AppCompat et matérielle.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 24206f6d6764c73f13a4b06fb44fa746f9d353af
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84135705"
---
# <a name="adding-appcompat-and-material-design"></a>Ajout de AppCompat et de la conception de matériau

_Suivez les étapes ci-dessous pour convertir Xamarin.Forms des applications Android existantes afin d’utiliser AppCompat et la conception de matériaux_

<!-- source https://gist.github.com/jassmith/a3b2a543f99126782936
https://blog.xamarin.com/material-design-for-your-xamarin-forms-android-apps/ -->

## <a name="overview"></a>Vue d'ensemble

Ces instructions expliquent comment mettre à jour vos Xamarin.Forms applications Android existantes pour utiliser la bibliothèque AppCompat et activer la conception de matériaux dans la version Android de vos Xamarin.Forms applications.

### <a name="1-update-xamarinforms"></a>1. mettre à jourXamarin.Forms

Assurez-vous que la solution utilise Xamarin.Forms 2,0 ou une version plus récente. Mettez à jour leXamarin.Forms
  Package NuGet à 2,0, si nécessaire.

### <a name="2-check-android-version"></a>2. Vérifiez la version d’Android

Vérifiez que le Framework cible du projet Android est Android 6,0 (Marshmallow). Vérifiez les **Options > du projet Android > générer > paramètres généraux** pour vous assurer que l’infrastructure Corrent est sélectionnée :

 ![](appcompat-images/target-android-6-sml.png "Android General Build Configuration")

### <a name="3-add-new-themes-to-support-material-design"></a>3. ajouter de nouveaux thèmes pour prendre en charge la conception de matériel

Créez les trois fichiers suivants dans votre projet Android et collez le contenu ci-dessous. Google fournit un [Guide de style](https://www.google.com/design/spec/style/color.html#color-color-palette) et un [Générateur de palettes de couleurs](https://www.materialpalette.com/) pour vous aider à choisir un autre jeu de couleurs pour celui spécifié.

**Ressources/valeurs/couleurs. Xml**

```xml
<resources>
  <color name="primary">#2196F3</color>
  <color name="primaryDark">#1976D2</color>
  <color name="accent">#FFC107</color>
  <color name="window_background">#F5F5F5</color>
</resources>
```

**Ressources/valeurs/style. Xml**

```xml
<resources>
  <style name="MyTheme" parent="MyTheme.Base">
  </style>
  <style name="MyTheme.Base" parent="Theme.AppCompat.Light.NoActionBar">
    <item name="colorPrimary">@color/primary</item>
    <item name="colorPrimaryDark">@color/primaryDark</item>
    <item name="colorAccent">@color/accent</item>
    <item name="android:windowBackground">@color/window_background</item>
    <item name="windowActionModeOverlay">true</item>
  </style>
</resources>
```

Un style supplémentaire doit être inclus dans le dossier **values-v21** pour appliquer des propriétés spécifiques lors de l’exécution sur Android Lollipop et versions ultérieures.

**Ressources/values-v21/style. Xml**

```xml
<resources>
  <style name="MyTheme" parent="MyTheme.Base">
    <!--If you are using MasterDetailPage you will want to set these, else you can leave them out-->
    <!--<item name="android:windowDrawsSystemBarBackgrounds">true</item>
    <item name="android:statusBarColor">@android:color/transparent</item>-->
  </style>
</resources>
```

### <a name="4-update-androidmanifestxml"></a>4. mettre à jour fichier AndroidManifest. Xml

Pour vous assurer que ces informations sur les nouveaux thèmes sont utilisées, définissez le thème dans le fichier **fichier AndroidManifest** en ajoutant `android:theme="@style/MyTheme"` (laissez le reste du code XML tel qu’il était).

**Propriétés/fichier AndroidManifest. Xml**

```xml
...
<application android:label="AppName" android:icon="@drawable/icon"
  android:theme="@style/MyTheme">
...
```

### <a name="5-provide-toolbar-and-tab-layouts"></a>5. fournir une barre d’outils et des dispositions d’onglet

Créez les fichiers **TabBar. AXML** et **ToolBar. AXML** dans le répertoire **Resources/layout** , puis collez-les dans le contenu ci-dessous :

**Ressources/mise en page/TabBar. AXML**

```xml
<android.support.design.widget.TabLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/sliding_tabs"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:background="?attr/colorPrimary"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    app:tabIndicatorColor="@android:color/white"
    app:tabGravity="fill"
    app:tabMode="fixed" />
```

Certaines propriétés des onglets ont été définies, y compris la gravité et le mode de l’onglet `fill` `fixed` .
Si vous avez un grand nombre d’onglets, vous souhaiterez peut-être faire défiler la [documentation Android TabLayout](https://developer.android.com/reference/android/support/design/widget/TabLayout.html) pour en savoir plus.

**Ressources/mise en page/barre d’outils. AXML**

```xml
<android.support.v7.widget.Toolbar
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="?attr/actionBarSize"
    android:minHeight="?attr/actionBarSize"
    android:background="?attr/colorPrimary"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    app:popupTheme="@style/ThemeOverlay.AppCompat.Light"
    app:layout_scrollFlags="scroll|enterAlways" />
```

Dans ces fichiers, nous créons un thème spécifique pour la barre d’outils qui peut varier pour votre application.
Pour en savoir plus, consultez le billet de blog de la [barre d’outils Hello](https://blog.xamarin.com/android-tips-hello-toolbar-goodbye-action-bar/) .

### <a name="6-update-the-mainactivity"></a>6. Mettez à jour le`MainActivity`

Dans Xamarin.Forms les applications existantes, la classe **MainActivity.cs** héritera de `FormsApplicationActivity` . Ce doit être remplacé par `FormsAppCompatActivity` pour activer les nouvelles fonctionnalités.

**MainActivity.cs**

```csharp
public class MainActivity : FormsAppCompatActivity  // was FormsApplicationActivity
```

Enfin, « connectez-vous » aux nouvelles dispositions de l’étape 5 de la `OnCreate` méthode, comme illustré ici :

```csharp
protected override void OnCreate(Bundle bundle)
{
  // set the layout resources first
  FormsAppCompatActivity.ToolbarResource = Resource.Layout.Toolbar;
  FormsAppCompatActivity.TabLayoutResource = Resource.Layout.Tabbar;

  // then call base.OnCreate and the Xamarin.Forms methods
  base.OnCreate(bundle);
  Forms.Init(this, bundle);
  LoadApplication(new App());
}
```
