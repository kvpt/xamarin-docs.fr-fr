---
title: Création de projets de bibliothèque spécifiques à la plateforme pour NuGet
description: Ce document décrit comment créer un package NuGet unique contenant du code spécifique à la plateforme pour plusieurs plateformes.
ms.prod: xamarin
ms.assetid: D8BC4906-805F-4AFB-8D1A-88B7BF87E17F
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 925e08c600c695640c927ada26df376a252b3927
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016721"
---
# <a name="creating-new-platform-specific-library-projects-for-nuget"></a>Création de projets de bibliothèque spécifiques à la plateforme pour NuGet

Les projets de bibliothèque multiplateforme qui ciblent des plateformes spécifiques, comme iOS et Android, fonctionnent mieux avec les projets partagés.

NuGet peut contenir du code spécifique à iOS et Android, ainsi que du code .NET commun aux deux.

Plusieurs assemblys sont créés et générés dans un seul package NuGet. Les normes NuGet garantissent que le package peut être ajouté à tous les types de projets pris en charge, tels que les projets Xamarin. iOS et Android.

## <a name="steps-to-create-a-cross-platform-library-nuget"></a>Étapes de création d’une bibliothèque multiplateforme NuGet

1. Sélectionnez **fichier > nouvelle solution** (ou cliquez avec le bouton droit sur une solution existante, puis choisissez **Ajouter > nouveau projet**).

2. Choisissez **bibliothèque multiplateforme** dans la section **bibliothèque de > multiplateformes** :

    [![](platform-specific-images/mulitplatform-library-sml.png "Configure multi-platform library for a single code base")](platform-specific-images/multiplatform-library.png#lightbox)

3. Entrez un **nom** et une **Description**, puis choisissez spécifique à la **plateforme**:

    [![](platform-specific-images/specific-configure-sml.png "Configure platform-specific library for iOS and Android")](platform-specific-images/specific-configure.png#lightbox)

4. Effectuez toutes les étapes de l'Assistant. Les projets suivants sont ajoutés à la solution :

    - **Projet Android** : le code spécifique à Android peut éventuellement être ajouté à ce projet.
    - **projet iOS** : le code spécifique à IOS peut éventuellement être ajouté à ce projet.
    - **Projet NuGet** : aucun code n’est ajouté à ce projet. Elle fait référence aux autres projets et contient la configuration des métadonnées pour la sortie du package NuGet.
    - **Projet partagé** : le code commun doit être ajouté à ce projet, y compris le code spécifique à la plateforme dans `#if` directives du compilateur.

5. Cliquez avec le bouton droit sur le projet NuGet, choisissez **options**, puis ouvrez la section de **métadonnées du package NuGet >** et entrez les [métadonnées requises](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) (ainsi que les métadonnées facultatives) :

    [![](platform-specific-images/specific-metadata-sml.png "Enter required metadata")](platform-specific-images/specific-metadata.png#lightbox)

6. En outre, dans la fenêtre **Options du projet** , ouvrez la section **assemblys de référence** et choisissez les profils PCL que la bibliothèque partagée prendra en charge via « appât et commutateur » :

    ![](platform-specific-images/specific-reference-assemblies.png "Also in the Project Options window, open the Reference Assemblies section and choose   which PCL profiles the shared library will support via bait and switch")

    > [!NOTE]
    > « Appât et commutateur » signifie que les assemblys PCL ne contiennent que l’API exposée par la bibliothèque (il ne peut pas contenir de code spécifique à la plateforme). Lorsque NuGet est ajouté à un projet Xamarin, les bibliothèques partagées sont compilées par rapport à la bibliothèque PCL, mais les assemblys spécifiques à la plateforme contiennent le code qui est réellement utilisé par le projet iOS ou Android.

7. Cliquez avec le bouton droit sur le projet et choisissez **créer un package NuGet** (ou générer ou déployer la solution) et le fichier de package NuGet **. nupkg** sera enregistré dans le dossier **/bin/** (Debug ou Release, en fonction de la configuration).

    ![](platform-specific-images/create-nuget-package.png "NuGet package file will be saved in the bin folder either Debug or Release, depending on configuration")

## <a name="verifying-the-output"></a>Vérification de la sortie

Les packages NuGet sont également des fichiers ZIP. il est donc possible d’inspecter la structure interne du package généré.

Cette capture d’écran montre le contenu d’un NuGet spécifique à la plateforme qui prend en charge iOS et Android, et a deux assemblys de référence sélectionnés :

![](platform-specific-images/nuget-output.png "Files contained in the NuGet package")

## <a name="related-links"></a>Liens associés

- [Guide sur les métadonnées](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
