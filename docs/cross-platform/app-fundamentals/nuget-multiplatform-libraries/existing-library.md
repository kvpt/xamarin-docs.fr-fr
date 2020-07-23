---
title: Création d’un NuGet à partir de projets de bibliothèque existants
description: Ce document explique comment créer un package NuGet à partir d’un projet de bibliothèque existant, ce qui permet au code d’être partagé avec d’autres développeurs.
ms.prod: xamarin
ms.assetid: EDAC3E5E-DB7D-40A9-AE28-45C52ADA854E
author: davidortinau
ms.author: daortin
ms.date: 04/20/2017
ms.openlocfilehash: 058904236e100a670e8a38417dcaec805178d6d2
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86936794"
---
# <a name="creating-a-nuget-from-existing-library-projects"></a>Création d’un NuGet à partir de projets de bibliothèque existants

Les bibliothèques de .NET Standard ou PCL existantes peuvent être transformées en packages NuGet via la fenêtre **Options du projet** :

1. Cliquez avec le bouton droit sur le projet de bibliothèque dans le **panneau solutions** , puis choisissez **options**.

2. Accédez à la section **métadonnées du package NuGet >** et entrez toutes les [informations requises](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) sous l’onglet **général** :

   [![Entrer les métadonnées requises](existing-library-images/existing-metadata-sml.png)](existing-library-images/existing-metadata.png#lightbox)

3. Si vous le souhaitez, [Ajoutez des métadonnées supplémentaires](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) dans l’onglet **Détails** .

4. Une fois les métadonnées configurées, vous pouvez cliquer avec le bouton droit sur le projet et choisir **créer un package NuGet** et le fichier de package NuGet **. nupkg** sera enregistré dans le dossier **/bin/** (Debug ou Release, en fonction de la configuration).

   ![Choisissez créer un package NuGet dans le menu contextuel.](existing-library-images/create-nuget-package.png)

5. Pour créer le package NuGet à _chaque_ Build ou déploiement, accédez à la section **package NuGet > Build** et cochez la case **créer un package NuGet lors de la génération du projet**:

    [![Cycle pour créer un package NuGet](existing-library-images/existing-tickbox-sml.png)](existing-library-images/existing-tickbox.png#lightbox)

> [!NOTE]
> La génération du package NuGet peut ralentir le processus de génération. Si cette case à cocher n’est pas cochée, vous pouvez toujours générer un package NuGet manuellement à tout moment à partir du menu contextuel du projet (indiqué à l’étape 4 ci-dessus).

## <a name="verifying-the-output"></a>Vérification de la sortie

Les packages NuGet sont également des fichiers ZIP. il est donc possible d’inspecter la structure interne du package généré.

Cette capture d’écran montre le contenu d’un NuGet basé sur PCL : un seul assembly PCL est inclus :

![Fichiers contenus dans le package NuGet](existing-library-images/nuget-output.png)

## <a name="related-links"></a>Liens associés

- [Guide des métadonnées](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
