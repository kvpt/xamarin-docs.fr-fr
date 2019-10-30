---
title: Création d’un NuGet à partir de projets de bibliothèque existants
description: Ce document explique comment créer un package NuGet à partir d’un projet de bibliothèque existant, ce qui permet au code d’être partagé avec d’autres développeurs.
ms.prod: xamarin
ms.assetid: EDAC3E5E-DB7D-40A9-AE28-45C52ADA854E
author: davidortinau
ms.author: daortin
ms.date: 04/20/2017
ms.openlocfilehash: 30158056b8adbdd5aba4cc311220a22502ea9968
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016782"
---
# <a name="creating-a-nuget-from-existing-library-projects"></a>Création d’un NuGet à partir de projets de bibliothèque existants

Les bibliothèques de .NET Standard ou PCL existantes peuvent être transformées en packages NuGet via la fenêtre **Options du projet** :

1. Cliquez avec le bouton droit sur le projet de bibliothèque dans le **panneau solutions** , puis choisissez **options**.

2. Accédez à la section **métadonnées du package NuGet >** et entrez toutes les [informations requises](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) sous l’onglet **général** :

   [![](existing-library-images/existing-metadata-sml.png "Enter required metadata")](existing-library-images/existing-metadata.png#lightbox)

3. Si vous le souhaitez, [Ajoutez des métadonnées supplémentaires](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) dans l’onglet **Détails** .

4. Une fois les métadonnées configurées, vous pouvez cliquer avec le bouton droit sur le projet et choisir **créer un package NuGet** et le fichier de package NuGet **. nupkg** sera enregistré dans le dossier **/bin/** (Debug ou Release, en fonction de la configuration).

   ![](existing-library-images/create-nuget-package.png "Choose Create NuGet Package from the right-click menu")

5. Pour créer le package NuGet à _chaque_ Build ou déploiement, accédez à la section **package NuGet > Build** et cochez la case **créer un package NuGet lors de la génération du projet**:

    [![](existing-library-images/existing-tickbox-sml.png "Tick to create a NuGet package")](existing-library-images/existing-tickbox.png#lightbox)

> [!NOTE]
> La génération du package NuGet peut ralentir le processus de génération. Si cette case à cocher n’est pas cochée, vous pouvez toujours générer un package NuGet manuellement à tout moment à partir du menu contextuel du projet (indiqué à l’étape 4 ci-dessus).

## <a name="verifying-the-output"></a>Vérification de la sortie

Les packages NuGet sont également des fichiers ZIP. il est donc possible d’inspecter la structure interne du package généré.

Cette capture d’écran montre le contenu d’un NuGet basé sur PCL : un seul assembly PCL est inclus :

![](existing-library-images/nuget-output.png "Files contained in the NuGet package")

## <a name="related-links"></a>Liens associés

- [Guide sur les métadonnées](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
