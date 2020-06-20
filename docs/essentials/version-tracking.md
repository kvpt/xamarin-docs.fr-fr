---
title: 'Xamarin.Essentials: Suivi des versions'
description: La classe VersionTracking dans vous Xamarin.Essentials permet de vérifier la version des applications et les numéros de build, ainsi que d’afficher des informations supplémentaires, par exemple si c’est la première fois que l’application est lancée ou pour la version actuelle, obtenez les informations de build précédentes, et bien plus encore.
ms.assetid: 670C7E8A-E882-4AC0-97D2-A53D90ADD6A3
author: jamesmontemagno
ms.author: jamont
ms.date: 05/28/2019
ms.custom: video
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 20819d76c23ca43f60073bcc2cd762abda280374
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84802035"
---
# <a name="xamarinessentials-version-tracking"></a>Xamarin.Essentials: Suivi des versions

La classe **VersionTracking** vous permet de connaître les numéros de version et de build des applications. Elle vous permet également de voir des informations supplémentaires, par exemple le premier lancement de l’application ou, pour la version actuelle, d’obtenir les informations de build précédentes, etc.

## <a name="get-started"></a>Bien démarrer

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-version-tracking"></a>Utilisation du suivi des versions

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

La première fois que vous utilisez la classe **VersionTracking**, elle commence par effectuer le suivi de la version actuelle. Vous devez appeler `Track` tôt et uniquement dans votre application à chaque chargement pour vérifier que les informations de la version actuelle font l’objet d’un suivi :

```csharp
VersionTracking.Track();
```

Une fois le `Track` initial appelé, les informations de version peuvent être lues :

```csharp

// First time ever launched application
var firstLaunch = VersionTracking.IsFirstLaunchEver;

// First time launching current version
var firstLaunchCurrent = VersionTracking.IsFirstLaunchForCurrentVersion;

// First time launching current build
var firstLaunchBuild = VersionTracking.IsFirstLaunchForCurrentBuild;

// Current app version (2.0.0)
var currentVersion = VersionTracking.CurrentVersion;

// Current build (2)
var currentBuild = VersionTracking.CurrentBuild;

// Previous app version (1.0.0)
var previousVersion = VersionTracking.PreviousVersion;

// Previous app build (1)
var previousBuild = VersionTracking.PreviousBuild;

// First version of app installed (1.0.0)
var firstVersion = VersionTracking.FirstInstalledVersion;

// First build of app installed (1)
var firstBuild = VersionTracking.FirstInstalledBuild;

// List of versions installed (1.0.0, 2.0.0)
var versionHistory = VersionTracking.VersionHistory;

// List of builds installed (1, 2)
var buildHistory = VersionTracking.BuildHistory;
```

## <a name="platform-implementation-specifics"></a>Caractéristiques de mise en œuvre de la plateforme

Toutes les informations de version sont stockées à l’aide de l’API de [Préférences](preferences.md) dans Xamarin.Essentials et sont stockées avec le nom de fichier **[Your-App-Package-ID]. xamarinessentials. versiontracking** et suivent la même persistance des données que celle décrite dans la documentation des [Préférences](preferences.md#persistence) .

## <a name="api"></a>API

- [Code source de la fonctionnalité de suivi des versions](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/VersionTracking)
- [Documentation sur l’API de suivi des versions](xref:Xamarin.Essentials.VersionTracking)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Version-Tracking-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
