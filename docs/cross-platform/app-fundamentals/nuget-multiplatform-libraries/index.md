---
title: Projets de bibliothèque multiplateforme NuGet (également appelé Nugetizer 3000)
description: Ce document explique comment utiliser l’outil Nugetizer 3000 pour créer automatiquement des packages NuGet pour partager du code entre les plateformes.
ms.prod: xamarin
ms.assetid: F0A5A9BB-86CD-44C9-8EE8-74D1E5E74A30
author: davidortinau
ms.author: daortin
ms.date: 07/25/2018
ms.openlocfilehash: f0d1195d9159623ec865b1ea1fec26d9969925ca
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456598"
---
# <a name="nuget-multiplatform-library-projects-nugetizer-3000"></a>Projets de bibliothèque multiplateforme NuGet (Nugetizer 3000)

_Créer automatiquement des packages NuGet pour partager du code entre les plateformes à l’aide du « Nugetizer 3000 » !_

Il est possible de créer automatiquement des packages NuGet pour partager du code entre les plateformes à l’aide du _Nugetizer 3000_. Il est ainsi possible de créer des packages NuGet à partir de projets de bibliothèque existants ou en créant un nouveau **projet de bibliothèque multiplateforme**.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Le Nugetizer 3000 est inclus dans Visual Studio pour Mac &ndash; recherchez la **bibliothèque >** type de projet de bibliothèque Mulitplatform dans le **fichier > nouvelle** fenêtre :

[![Fenêtre créer une nouvelle bibliothèque multiplateforme](images/mulitplatform-library-sml.png)](images/mulitplatform-library.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Pour utiliser Nugetizer 3000 dans Visual Studio, [Téléchargez et exécutez le programme d’installation VSIX](https://bit.ly/nugetizer-2017).

-----

## <a name="building-nuget-packages"></a>Génération de packages NuGet

Une fois configuré, chaque Build du projet génère un package NuGet complet, qui peut être utilisé pour partager du code en interne avec d’autres applications ou téléchargé vers [NuGet.org](https://www.nuget.org).

Il existe trois scénarios d’utilisation de cette fonctionnalité :

- [Projets de bibliothèque existants](existing-library.md)

  Créez un package NuGet à partir de projets PCL (ou .NET Standard) existants.

- [Création d’un projet de bibliothèque multiplateforme](single-codebase.md)

  Créer une bibliothèque pour partager du code commun via NuGet, à l’aide d’un PCL ou d’un .NET Standard.

- [Création de projets de bibliothèque spécifiques à la plateforme](platform-specific.md)

  Créez une bibliothèque et NuGet qui incluent du code spécifique à la plateforme pour iOS et Android, et qui utilise un projet partagé pour contenir le code commun et des projets spécifiques à la plateforme pour prendre en charge des fonctionnalités spécifiques à iOS ou Android.

Reportez-vous au [Guide des métadonnées](metadata.md) pour plus d’informations sur les métadonnées obligatoires et facultatives qui doivent être ajoutées à un package NuGet.

## <a name="further-nuget-information"></a>Informations supplémentaires sur NuGet

En savoir plus sur la [Création manuelle de packages NuGet pour Xamarin](~/cross-platform/app-fundamentals/nuget-manual.md) et sur l' [inclusion d’un package NuGet dans une application](/visualstudio/mac/nuget-walkthrough).

La [documentation NuGet](/nuget/) de Microsoft contient des informations plus détaillées sur le format **. nupkg** et l’utilisation de packages NuGet dans Visual Studio.

La discussion de conception pour les projets de package NuGet (également appelé NuGetizer 3000) est disponible dans le [dépôt github NuGet](https://github.com/NuGet/Home/wiki/NuGetizer-3000).

## <a name="related-links"></a>Liens associés

- [Cas d’usage NuGetizer-3000](https://github.com/NuGet/Home/wiki/NuGetizer-Core-Scenarios)
- [Créer manuellement des packages NuGet pour Xamarin](~/cross-platform/app-fundamentals/nuget-manual.md)
- [Documentation de NuGet](/nuget/)