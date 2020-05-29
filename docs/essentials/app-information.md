---
title : " Xamarin.Essentials : informations sur l’application" Description : "ce document décrit la classe AppInfo dans Xamarin.Essentials , qui fournit des informations sur votre application. Par exemple, il expose le nom et la version de l’application.»
ms. AssetID : 15924FCB-19E0-45B2-944E-E94FD7AE12FA auteur : jamesmontemagno ms. Author : Jamont ms. Date : 01/29/2019 ms. Custom : Video No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinessentials-app-information"></a>Xamarin.Essentials: Informations sur l’application

La classe **AppInfo** fournit des informations sur votre application.

## <a name="get-started"></a>Bien démarrer

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-appinfo"></a>Utilisation d’AppInfo

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

## <a name="obtaining-application-information"></a>Obtention d’informations sur l’application :

Les informations suivantes sont exposées via l’API :

```csharp
// Application Name
var appName = AppInfo.Name;

// Package Name/Application Identifier (com.microsoft.testapp)
var packageName = AppInfo.PackageName;

// Application Version (1.0.0)
var version = AppInfo.VersionString;

// Application Build Number (1)
var build = AppInfo.BuildString;
```

## <a name="displaying-application-settings"></a>Affichage des paramètres de l’application

La classe **AppInfo** peut également afficher une page de paramètres gérés par le système d’exploitation pour l’application :

```csharp
// Display settings page
AppInfo.ShowSettingsUI();
```

Cette page de paramètres permet à l’utilisateur de changer les autorisations de l’application et d’effectuer d’autres tâches spécifiques à la plateforme.

## <a name="platform-implementation-specifics"></a>Caractéristiques de mise en œuvre de la plateforme

# <a name="android"></a>[Android](#tab/android)

Les informations d’application sont tirées de `AndroidManifest.xml` pour les champs suivants :

- **Build** : `android:versionCode` dans le `manifest` nœud
- **Name**  -  Nom `android:label` dans le `application` nœud
- **PackageName**: `package` dans le `manifest` nœud
- **VersionString** – `android:versionName` dans le `application` nœud

# <a name="ios"></a>[iOS](#tab/ios)

Les informations d’application sont tirées de `Info.plist` pour les champs suivants :

- **Build** :`CFBundleVersion`
- **Name**  -  Nom `CFBundleDisplayName` s’il est défini, sinon`CFBundleName`
- **PackageName**:`CFBundleIdentifier`
- **VersionString** –`CFBundleShortVersionString`

# <a name="uwp"></a>[UWP](#tab/uwp)

Les informations d’application sont tirées de `Package.appxmanifest` pour les champs suivants :

- **Build** – Utilise la valeur `Build` de la `Version` sur le nœud `Identity`
- **Name**  -  Nom `DisplayName` sur le `Properties` nœud
- **PackageName**: `Name` sur le `Identity` nœud
- **VersionString** – `Version` sur le `Identity` nœud

--------------

## <a name="api"></a>API

- [Code source d’AppInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [Documentation sur l’API d’AppInfo](xref:Xamarin.Essentials.AppInfo)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/App-Information-Essential-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
