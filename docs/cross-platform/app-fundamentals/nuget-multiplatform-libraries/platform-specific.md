---
title: Création de projets de bibliothèque spécifiques à la plateforme pour NuGet
description: Ce document décrit comment créer un package NuGet unique contenant du code spécifique à la plateforme pour plusieurs plateformes.
ms.prod: xamarin
ms.assetid: D8BC4906-805F-4AFB-8D1A-88B7BF87E17F
author: conceptdev
ms.author: crdun
ms.date: 03/23/2017
ms.openlocfilehash: d3f756b1a551c7b6bcbe48129235d537312edff6
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70282149"
---
# <a name="creating-new-platform-specific-library-projects-for-nuget"></a>Création de projets de bibliothèque spécifiques à la plateforme pour NuGet

Les projets de bibliothèque multiplateforme qui ciblent des plateformes spécifiques, comme iOS et Android, fonctionnent mieux avec les projets partagés.

NuGet peut contenir du code spécifique à iOS et Android, ainsi que du code .NET commun aux deux.

Plusieurs assemblys sont créés et générés dans un seul package NuGet. Les normes NuGet garantissent que le package peut être ajouté à tous les types de projets pris en charge, tels que les projets Xamarin. iOS et Android.

## <a name="steps-to-create-a-cross-platform-library-nuget"></a>Étapes de création d’une bibliothèque multiplateforme NuGet

1. Sélectionnez **fichier > nouvelle solution** (ou cliquez avec le bouton droit sur une solution existante, puis choisissez **Ajouter > nouveau projet**).

2. Choisissez **bibliothèque multiplateforme** dans la section **bibliothèque de > multiplateformes** :

    [![](platform-specific-images/mulitplatform-library-sml.png "Configurer une bibliothèque multiplateforme pour une seule base de code")](platform-specific-images/multiplatform-library.png#lightbox)

3. Entrez un **nom** et une **Description**, puis choisissez spécifique à la **plateforme**:

    [![](platform-specific-images/specific-configure-sml.png "Configurer une bibliothèque spécifique à une plateforme pour iOS et Android")](platform-specific-images/specific-configure.png#lightbox)

4. Effectuez toutes les étapes de l'Assistant. Les projets suivants sont ajoutés à la solution :

    - **Projet Android** : le code spécifique à Android peut éventuellement être ajouté à ce projet.
    - **projet iOS** : le code spécifique à IOS peut éventuellement être ajouté à ce projet.
    - **Projet NuGet** : aucun code n’est ajouté à ce projet. Elle fait référence aux autres projets et contient la configuration des métadonnées pour la sortie du package NuGet.
    - **Projet partagé** : le code commun doit être ajouté à ce projet, y compris le code spécifique `#if` à la plateforme dans les directives de compilateur.

5. Cliquez avec le bouton droit sur le projet NuGet, choisissez **options**, puis ouvrez la section de **métadonnées du package NuGet >** et entrez les [métadonnées requises](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) (ainsi que les métadonnées facultatives) :

    [![](platform-specific-images/specific-metadata-sml.png "Entrer les métadonnées requises")](platform-specific-images/specific-metadata.png#lightbox)

6. En outre, dans la fenêtre **Options du projet** , ouvrez la section **assemblys de référence** et choisissez les profils PCL que la bibliothèque partagée prendra en charge via « appât et commutateur » :

    ![](platform-specific-images/specific-reference-assemblies.png "De même, dans la fenêtre Options du projet, ouvrez la section assemblys de référence et choisissez les profils PCL que la bibliothèque partagée prendra en charge via l’appât et le commutateur.")

    > [!NOTE]
    > « Appât et commutateur » signifie que les assemblys PCL ne contiennent que l’API exposée par la bibliothèque (il ne peut pas contenir de code spécifique à la plateforme). Lorsque NuGet est ajouté à un projet Xamarin, les bibliothèques partagées sont compilées par rapport à la bibliothèque PCL, mais les assemblys spécifiques à la plateforme contiennent le code qui est réellement utilisé par le projet iOS ou Android.

7. Cliquez avec le bouton droit sur le projet et choisissez **créer un package NuGet** (ou générer ou déployer la solution) et le fichier de package NuGet **. nupkg** sera enregistré dans le dossier **/bin/** (Debug ou Release, en fonction de la configuration).

    ![](platform-specific-images/create-nuget-package.png "Le fichier de package NuGet sera enregistré dans le dossier bin debug ou Release, en fonction de la configuration")


## <a name="verifying-the-output"></a>Vérification de la sortie

Les packages NuGet sont également des fichiers ZIP. il est donc possible d’inspecter la structure interne du package généré.

Cette capture d’écran montre le contenu d’un NuGet spécifique à la plateforme qui prend en charge iOS et Android, et a deux assemblys de référence sélectionnés :

![](platform-specific-images/nuget-output.png "Fichiers contenus dans le package NuGet")


## <a name="related-links"></a>Liens associés

- [Guide sur les métadonnées](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
