---
title: Création d’un package NuGet à partir de projets de bibliothèque existants
description: Ce document décrit comment créer un package NuGet à partir d’un projet de bibliothèque existant, ce qui permet le code à partager avec d’autres développeurs.
ms.prod: xamarin
ms.assetid: EDAC3E5E-DB7D-40A9-AE28-45C52ADA854E
author: asb3993
ms.author: amburns
ms.date: 04/20/2017
ms.openlocfilehash: 6e043334d3ca45a573423ebdfdf1ec9149167b55
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864691"
---
# <a name="creating-a-nuget-from-existing-library-projects"></a>Création d’un package NuGet à partir de projets de bibliothèque existants

Les bibliothèques .NET Standard ou de bibliothèque de classes portable peuvent être transformées en packages NuGet via le **Options du projet** fenêtre :

1. Avec le bouton droit sur le projet de bibliothèque dans le **panneau solutions** et choisissez **Options**.

2. Accédez à la **NuGet Package > métadonnées** section et entrez toutes le [informations requises](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) dans le **général** onglet :

   [![](existing-library-images/existing-metadata-sml.png "Entrez les métadonnées requises")](existing-library-images/existing-metadata.png#lightbox)

3. Si vous le souhaitez, [ajouter des métadonnées supplémentaires](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) dans le **détails** onglet.

4. Une fois que les métadonnées sont configurée, vous pouvez avec le bouton droit sur le projet et choisissez **création d’un NuGet Package** et **.nupkg** fichier de package NuGet est enregistré dans le **/bin/** dossier (Debug ou Release, selon la configuration).

   ![](existing-library-images/create-nuget-package.png "Choisissez de créer un NuGet Package dans le menu contextuel")

5. Pour créer le package NuGet sur _chaque_ générer ou déployer, accédez à la **NuGet Package > Générer** section et des graduations **créer un NuGet Package lors de la génération du projet**:

    [![](existing-library-images/existing-tickbox-sml.png "Graduation pour créer un package NuGet")](existing-library-images/existing-tickbox.png#lightbox)

> [!NOTE]
> Création du package NuGet package peut ralentir le processus de génération. Si cette case n’est pas cochée, vous pouvez toujours générer manuellement un package NuGet à tout moment dans le menu contextuel du projet (indiqué à l’étape 4 ci-dessus).

## <a name="verifying-the-output"></a>Vérification de la sortie

Les packages NuGet sont également des fichiers ZIP, il est donc possible d’inspecter la structure interne du package généré.

Cette capture d’écran montre le contenu d’un package NuGet basé sur la bibliothèque de classes portable – un seul assembly de bibliothèque de classes portable est inclus :

![](existing-library-images/nuget-output.png "Fichiers contenus dans le package NuGet")


## <a name="related-links"></a>Liens associés

- [Guide sur les métadonnées](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
