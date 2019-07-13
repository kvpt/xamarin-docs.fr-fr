---
title: Création d’une nouvelle bibliothèque multiplateforme pour NuGet
description: Ce document décrit comment créer une bibliothèque multiplateforme pour une utilisation avec NuGet. Cette technique est adaptée à la logique métier et les algorithmes qui peuvent être exprimées entièrement dans la bibliothèque de classes de Base .NET et seront exécutera donc sur toutes les plateformes cibles sans code spécifique à la plateforme.
ms.prod: xamarin
ms.assetid: E7B55354-9BBE-4122-BCE3-3506B79090DD
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 6371c2af15eab9c5124212eefd9cf70d07b945d4
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864719"
---
# <a name="creating-a-new-multiplatform-library-for-nuget"></a>Création d’une nouvelle bibliothèque multiplateforme pour NuGet

Création d’un projet de bibliothèque de multiplateforme qui utilise PCL ou .NET Standard signifie que le package NuGet qui en résulte peut être ajouté à tout projet .NET qui prend en charge le profil cible, y compris les projets ASP.NET, ou les applications de bureau à l’aide de WinForms, WPF ou UWP.

La bibliothèque peut contenir uniquement du code pris en charge par le profil sélectionné de bibliothèque de classes portable ou .NET Standard, ainsi que les autres packages NuGet qui est ajoutés.
Cela est adapté à la logique métier et des algorithmes qui peuvent être exprimées entièrement dans la bibliothèque de classes de base .NET.

Un assembly unique est créé et intégré à un package NuGet.

Si vous devez ultérieurement des fonctionnalités spécifiques à la plateforme, [projets propres à la plateforme peuvent être ajoutés](#add-platforms).

## <a name="steps-to-create-a-multiplatform-library-nuget"></a>Étapes de création d’un package NuGet bibliothèque multiplateforme

1. Sélectionnez **fichier > Nouvelle Solution** (ou cliquez avec le bouton droit sur une solution existante et choisissez **Ajouter > Nouveau projet**).

2. Choisissez **multiplateforme bibliothèque** à partir de la **multiplateforme > bibliothèque** section :

   [![](single-codebase-images/mulitplatform-library-sml.png "Configurer la bibliothèque de multi-plateforme pour une seule base de code")](single-codebase-images/mulitplatform-library.png#lightbox)

3. Entrez un **nom** et **Description**, puis choisissez **unique pour toutes les plateformes**:

   [![](single-codebase-images/single-configure-sml.png "Configurer la bibliothèque de multi-plateforme pour une seule base de code")](single-codebase-images/single-configure.png#lightbox)

4. Effectuez toutes les étapes de l'Assistant. Un projet de bibliothèque unique est créé dans la solution.

5. Avec le bouton droit sur le nouveau projet de bibliothèque, puis sélectionnez **Options**. Le **Générer > Général** section permet la **Framework cible** à définir : choisissez un profil de bibliothèque de classes portable .NET Portable ou une version .NET Standard :

   [![](single-codebase-images/single-choose-type-sml.png "Choisissez bibliothèque de classes portable ou .NET Standard pour le type de bibliothèque")](single-codebase-images/single-choose-type.png#lightbox)

6. Également dans le **Options du projet** fenêtre, ouvrez le **NuGet Package > métadonnées** section et entrez le [métadonnées requises](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) (ainsi que toutes les métadonnées facultative) :

   [![](single-codebase-images/single-metadata-sml.png "Entrez les métadonnées requises")](single-codebase-images/single-metadata.png#lightbox)

7. Avec le bouton droit sur le projet de bibliothèque et choisissez **création d’un NuGet Package** (générer ou déployer la solution) et le **.nupkg** fichier de package NuGet est enregistré dans le **/bin/** dossier (Debug ou Release, en fonction de la configuration) :

   ![](single-codebase-images/create-nuget-package.png "Le fichier de package NuGet est être enregistré dans le dossier bin Debug ou Release, selon la configuration")


## <a name="verifying-the-output"></a>Vérification de la sortie

Les packages NuGet sont également des fichiers ZIP, il est donc possible d’inspecter la structure interne du package généré.

Cette capture d’écran montre le contenu d’un package NuGet basé sur la bibliothèque de classes portable – un seul assembly de bibliothèque de classes portable est inclus :

![](single-codebase-images/nuget-output.png "Fichiers contenus dans le package NuGet")

<a name="add-platforms" />

## <a name="adding-platform-specific-code"></a>Ajout de Code spécifique à la plateforme

Les projets bibliothèque de classes portable et projets basés sur .NET Standard ne peut pas contenir de références spécifiques à la plateforme (par exemple, iOS ou Android fonctionnalités).

Si un projet de bibliothèque de classes portable existant ou d’un projet .NET Standard doit être développé pour inclure le code spécifique à la plateforme, cela est possible en effectuant un clic droit sur le projet et en sélectionnant **Ajouter > Ajouter une implémentation de plateforme...** :

[![](single-codebase-images/add-later-sml.png "Ajouter un menu d’implémentation de plateforme")](single-codebase-images/add-later.png#lightbox)

Un ou plusieurs projets de plateforme peuvent être ajoutés à la solution, et la bibliothèque de classes portable ou .NET Standard existante peut éventuellement être convertie en un projet partagé :

[![](single-codebase-images/add-later-platforms-sml.png "Ajouter des options de plateforme tels qu’iOS, Android et projet partagé")](single-codebase-images/add-later-platforms-sml.png#lightbox)

Après la conversion en un projet partagé, visitez le **Options du projet > NuGet Package > assemblys de référence**
[section](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/platform-specific.md) et vous assurer que toute requise profils sont sélectionnés (afin que le NuGet continue d’être compatible avec les projets qu’il a été utilisé précédemment dans).


## <a name="related-links"></a>Liens associés

- [Guide sur les métadonnées](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
