---
title: 'Xamarin.Essentials : suivi des versions'
description: La classe VersionTracking de Xamarin.Essentials vous permet de connaître les numéros de version et de build des applications. Elle vous permet également de voir des informations supplémentaires, par exemple le premier lancement de l’application ou, pour la version actuelle, d’obtenir les informations de build précédentes, etc.
ms.assetid: 670C7E8A-E882-4AC0-97D2-A53D90ADD6A3
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 1eae0bf7c21dd7efa7655633896bdb2897f9d782
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674858"
---
# <a name="xamarinessentials-version-tracking"></a>Xamarin.Essentials : suivi des versions

![Préversion NuGet](~/media/shared/pre-release.png)

La classe **VersionTracking** vous permet de connaître les numéros de version et de build des applications. Elle vous permet également de voir des informations supplémentaires, par exemple le premier lancement de l’application ou, pour la version actuelle, d’obtenir les informations de build précédentes, etc.

## <a name="get-started"></a>Prise en main

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

Toutes les informations de version sont stockées à l’aide de l’API de [préférences](preferences.md) de Xamarin.Essentials. Elles sont également stockées avec un nom de fichier de type **[VOTRE-PAQUET-D’APPLICATION-ID].xamarinessentials.versiontracking** et suivent la même persistance de données décrite dans la documentation sur [Preferences](preferences.md#persistence).

## <a name="api"></a>API

- [Code source de la fonctionnalité de suivi des versions](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/VersionTracking)
- [Documentation sur l’API de suivi des versions](xref:Xamarin.Essentials.VersionTracking)
