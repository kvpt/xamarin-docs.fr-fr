---
title: Création de nouveaux projets de bibliothèque spécifique à la plateforme pour NuGet
description: Ce document décrit comment créer un seul package NuGet qui contient le code spécifique à la plateforme pour plusieurs plateformes.
ms.prod: xamarin
ms.assetid: D8BC4906-805F-4AFB-8D1A-88B7BF87E17F
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 4be010448d963462ccf06c263ddfad7ba1d9feae
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832039"
---
# <a name="creating-new-platform-specific-library-projects-for-nuget"></a>Création de nouveaux projets de bibliothèque spécifique à la plateforme pour NuGet

Les projets de bibliothèque multiplateformes qui ciblent des plateformes spécifiques, telles qu’iOS et Android, fonctionnent mieux avec les projets partagés.

Le package NuGet peut contenir de code spécifiques iOS et Android, ainsi que de code .NET commun aux deux.

Plusieurs assemblys sont créés et intégrés à un seul package NuGet. Normes de NuGet garantissent que le package peut être ajouté à tous les types de projet pris en charge, tels que les projets Xamarin.iOS et Android.

## <a name="steps-to-create-a-cross-platform-library-nuget"></a>Étapes de création d’un package NuGet bibliothèque multiplateforme

1. Sélectionnez **fichier > Nouvelle Solution** (ou cliquez avec le bouton droit sur une solution existante et choisissez **Ajouter > Nouveau projet**).

2. Choisissez **multiplateforme bibliothèque** à partir de la **multiplateforme > bibliothèque** section :

    [![](platform-specific-images/mulitplatform-library-sml.png "Configurer la bibliothèque de multi-plateforme pour une seule base de code")](platform-specific-images/multiplatform-library.png#lightbox)

3. Entrez un **nom** et **Description**, puis choisissez **spécifique à la plateforme**:

    [![](platform-specific-images/specific-configure-sml.png "Configurer la bibliothèque spécifique à la plateforme pour iOS et Android")](platform-specific-images/specific-configure.png#lightbox)

4. Effectuez toutes les étapes de l'Assistant. Les projets suivants sont ajoutés à la solution :

    - **Projet Android** – spécifique à Android code peut éventuellement être ajouté à ce projet.
    - **Projet iOS** – code spécifique à iOS peut éventuellement être ajouté à ce projet.
    - **Projet NuGet** – aucun code n’est ajouté à ce projet. Il fait référence à d’autres projets et contient la configuration de métadonnées pour la sortie du package NuGet.
    - **Projet partagé** – code commun doit être ajouté à ce projet, y compris le code spécifique à la plateforme à l’intérieur `#if` directives de compilateur.

5. Avec le bouton droit sur le projet NuGet et choisissez **Options**, puis ouvrez le **NuGet Package > métadonnées** section et entrez le [métadonnées requises](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) (comme facultatifs ainsi que toutes métadonnées) :

    [![](platform-specific-images/specific-metadata-sml.png "Entrez les métadonnées requises")](platform-specific-images/specific-metadata.png#lightbox)

6. Également dans le **Options du projet** fenêtre, ouvrez le **assemblys de référence** section et choisissez les profils de bibliothèque de classes portable prend en charge la bibliothèque partagée par le biais de « appât and commutateur » :

    ![](platform-specific-images/specific-reference-assemblies.png "Également dans la fenêtre Options du projet, ouvrez la section d’assemblys de référence et choisissez les profils de bibliothèque de classes portable prend en charge la bibliothèque partagée par le biais soit")

    > [!NOTE]
    > « Bait and commutateur » signifie que les assemblys de bibliothèque de classes portable ne contiennent que le API exposée par la bibliothèque (il ne peut pas contenir le code spécifique à la plateforme). Lorsque le package NuGet est ajouté à un projet Xamarin, des bibliothèques partagées seront compilés par rapport à la bibliothèque PCL, mais les assemblys spécifiques à la plateforme contiennent le code qui est réellement utilisé par l’iOS ou Android projet.

7. Avec le bouton droit sur le projet et choisissez **création d’un NuGet Package** (générer ou déployer la solution) et le **.nupkg** fichier de package NuGet est enregistré dans le **/bin/** dossier ( Debug ou Release, selon la configuration).

    ![](platform-specific-images/create-nuget-package.png "NuGet package fichier sera enregistré dans le dossier bin Debug ou Release, selon la configuration")


## <a name="verifying-the-output"></a>Vérification de la sortie

Les packages NuGet sont également des fichiers ZIP, il est donc possible d’inspecter la structure interne du package généré.

Cette capture d’écran montre le contenu d’un package NuGet spécifique à la plateforme qui prend en charge iOS et Android et ont dû deux assemblys de référence sélectionnés :

![](platform-specific-images/nuget-output.png "Fichiers contenus dans le package NuGet")


## <a name="related-links"></a>Liens associés

- [Guide sur les métadonnées](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
