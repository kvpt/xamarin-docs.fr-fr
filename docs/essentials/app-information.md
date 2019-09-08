---
title: "Xamarin.Essentials: Informations de l'application"
description: La classe **AppInfo** fournit des informations générales sur votre application. Par exemple, il expose le nom de l’application et la version. Par exemple, il expose le nom et la version de l’application.
ms.assetid: 15924FCB-19E0-45B2-944E-E94FD7AE12FA
author: jamesmontemagno
ms.author: jamont
ms.date: 01/29/2019
ms.custom: video
ms.openlocfilehash: 69d0cb503d329ccfb4c29fb6cc4a589bef97e893
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756990"
---
# <a name="xamarinessentials-app-information"></a>Xamarin.Essentials: Informations de l'application

La classe **AppInfo** fournit des informations générales sur votre application.

## <a name="get-started"></a>Prise en main

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-appinfo"></a>Utilisation d’AppInfo

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

## <a name="obtaining-application-information"></a>Obtenir des informations sur l’application

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

## <a name="displaying-application-settings"></a>Afficher les paramètres de l'application

La classe **AppInfo** peut également afficher la page des paramètres systèmes gérés par le système d’exploitation spécifiquement pour l’application :

```csharp
// Display settings page
AppInfo.ShowSettingsUI();
```

Cette page de paramètres permet à l’utilisateur de changer les autorisations de l’application et d’effectuer d’autres tâches spécifiques à la plateforme.

## <a name="platform-implementation-specifics"></a>Caractéristiques de mise en œuvre de la plateforme

# <a name="androidtabandroid"></a>[Android](#tab/android)

Les informations d’application sont tirées de `AndroidManifest.xml` pour les champs suivants :

- **Build** – `android:versionCode` dans le nœud `manifest`
- **Name** - `android:label` dans le nœud `application`
- **PackageName** : `package` dans le nœud `manifest`
- **VersionString** – `android:versionName` dans le nœud `application`

# <a name="iostabios"></a>[iOS](#tab/ios)

Les informations d’application sont tirées de `Info.plist` pour les champs suivants :

- **Build** – `CFBundleVersion`
- **Name** - `CFBundleDisplayName` si défini, sinon `CFBundleName`
- **PackageName** : `CFBundleIdentifier`
- **VersionString** – `CFBundleShortVersionString`

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Les informations d’application sont tirées de `Package.appxmanifest` pour les champs suivants :

- **Build** – Utilise la valeur `Build` de la `Version` sur le nœud `Identity`
- **Name** - `DisplayName` sur le nœud `Properties`
- **PackageName** : `Name` sur le nœud `Identity`
- **VersionString** – `Version` sur le nœud `Identity`

--------------

## <a name="api"></a>API

- [Code source d’AppInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [Documentation sur l’API d’AppInfo](xref:Xamarin.Essentials.AppInfo)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/App-Information-Essential-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
