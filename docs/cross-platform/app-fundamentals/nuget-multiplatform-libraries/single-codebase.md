---
title: Création d’une nouvelle bibliothèque multiplateforme pour NuGet
description: Ce document décrit comment créer une bibliothèque multiplateforme à utiliser avec NuGet. Cette technique est adaptée à la logique métier et aux algorithmes qui peuvent être exprimés entièrement dans la bibliothèque de classes de base .NET et s’exécutera donc sur toutes les plateformes cibles sans code spécifique à la plateforme.
ms.prod: xamarin
ms.assetid: E7B55354-9BBE-4122-BCE3-3506B79090DD
author: conceptdev
ms.author: crdun
ms.date: 03/23/2017
ms.openlocfilehash: 966d7a21da0112383c08e862a357c3c1d4fb9c22
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70289733"
---
# <a name="creating-a-new-multiplatform-library-for-nuget"></a>Création d’une nouvelle bibliothèque multiplateforme pour NuGet

La création d’un projet de bibliothèque multiplateforme qui utilise PCL ou .NET Standard signifie que le NuGet qui en résulte peut être ajouté à n’importe quel projet .NET qui prend en charge le profil cible, y compris les projets ASP.NET ou les applications de bureau utilisant WinForms, WPF ou UWP.

La bibliothèque peut uniquement contenir du code pris en charge par le pilote PCL ou .NET Standard sélectionné, ainsi que tout autre packages NuGet ajouté.
Cela est adapté à la logique métier et aux algorithmes qui peuvent être exprimés entièrement dans la bibliothèque de classes de base .NET.

Un assembly unique est créé et intégré à un package NuGet.

Si vous avez besoin d’une fonctionnalité spécifique à la plateforme, vous [pouvez ajouter des projets spécifiques à la plateforme](#add-platforms).

## <a name="steps-to-create-a-multiplatform-library-nuget"></a>Étapes de création d’une bibliothèque multiplateforme NuGet

1. Sélectionnez **fichier > nouvelle solution** (ou cliquez avec le bouton droit sur une solution existante, puis choisissez **Ajouter > nouveau projet**).

2. Choisissez **bibliothèque multiplateforme** dans la section **bibliothèque de > multiplateformes** :

   [![](single-codebase-images/mulitplatform-library-sml.png "Configurer une bibliothèque multiplateforme pour une seule base de code")](single-codebase-images/mulitplatform-library.png#lightbox)

3. Entrez un **nom** et une **Description**, puis choisissez **unique pour toutes les plateformes**:

   [![](single-codebase-images/single-configure-sml.png "Configurer une bibliothèque multiplateforme pour une seule base de code")](single-codebase-images/single-configure.png#lightbox)

4. Effectuez toutes les étapes de l'Assistant. Un projet de bibliothèque unique est créé dans la solution.

5. Cliquez avec le bouton droit sur le nouveau projet de bibliothèque, puis sélectionnez **options**. La section **Build > General** permet de définir la version cible du .NET **Framework** – choisissez un profil PCL portable .net ou une version .NET standard :

   [![](single-codebase-images/single-choose-type-sml.png "Choisir PCL ou .NET Standard pour le type de bibliothèque")](single-codebase-images/single-choose-type.png#lightbox)

6. En outre, dans la fenêtre **Options du projet** , ouvrez la section **> de métadonnées du package NuGet** et entrez les [métadonnées requises](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) (ainsi que les métadonnées facultatives) :

   [![](single-codebase-images/single-metadata-sml.png "Entrer les métadonnées requises")](single-codebase-images/single-metadata.png#lightbox)

7. Cliquez avec le bouton droit sur le projet de bibliothèque et choisissez **créer un package NuGet** (ou générer ou déployer la solution) et le fichier de package NuGet **. nupkg** sera enregistré dans le dossier **/bin/** (Debug ou Release, en fonction de la configuration) :

   ![](single-codebase-images/create-nuget-package.png "Le fichier de package NuGet sera enregistré dans le dossier bin debug ou Release, en fonction de la configuration")


## <a name="verifying-the-output"></a>Vérification de la sortie

Les packages NuGet sont également des fichiers ZIP. il est donc possible d’inspecter la structure interne du package généré.

Cette capture d’écran montre le contenu d’un NuGet basé sur PCL : un seul assembly PCL est inclus :

![](single-codebase-images/nuget-output.png "Fichiers contenus dans le package NuGet")

<a name="add-platforms" />

## <a name="adding-platform-specific-code"></a>Ajout de code spécifique à la plateforme

Les projets basés sur PCL et les projets basés sur des .NET Standard ne peuvent pas contenir de références spécifiques à la plateforme (telles que les fonctionnalités iOS ou Android).

Si vous devez développer un projet .NET Standard ou un projet PCL existant pour inclure du code spécifique à la plateforme, vous pouvez effectuer cette opération en cliquant avec le bouton droit sur le projet et en sélectionnant **ajouter > ajouter une implémentation de plateforme...** :

[![](single-codebase-images/add-later-sml.png "Menu Ajouter une implémentation de plateforme")](single-codebase-images/add-later.png#lightbox)

Un ou plusieurs projets de plateforme peuvent être ajoutés à la solution, et la bibliothèque PCL ou .NET Standard existante peut éventuellement être convertie en projet partagé :

[![](single-codebase-images/add-later-platforms-sml.png "Ajouter des options de plateforme telles qu’iOS, Android et Project Shared")](single-codebase-images/add-later-platforms-sml.png#lightbox)

Après la conversion en projet partagé, accédez à la[section](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/platform-specific.md) **options de projet > package NuGet > des assemblys**
de référence et assurez-vous que tous les profils requis sont sélectionnés (afin que NuGet continue à être compatible avec les projets Il a été utilisé précédemment dans).


## <a name="related-links"></a>Liens associés

- [Guide sur les métadonnées](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
