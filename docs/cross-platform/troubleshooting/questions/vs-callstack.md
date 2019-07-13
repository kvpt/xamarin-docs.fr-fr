---
title: Comment collecter les piles d’appels du processus Visual Studio ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 64c24b09-2c4a-43ad-b94d-6cd05a1aee44
author: asb3993
ms.author: amburns
ms.date: 03/30/2017
ms.openlocfilehash: 6d9cabdb22425bdc057f6d52de5aa943537dd1ca
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864478"
---
# <a name="how-do-i-collect-the-current-call-stacks-of-the-visual-studio-process"></a>Comment collecter les piles d’appels du processus Visual Studio ?

Lors de l’interface graphique utilisateur verrouille se fige (se bloque) dans Visual Studio, une partie des informations de diagnostic à collecter importante est l’ensemble de piles d’appels de tous les threads du processus Visual Studio. Pour enregistrer ces informations pour une instance suspendue de Visual Studio, vous pouvez utiliser une deuxième instance de Visual Studio :

1. Démarrez une deuxième instance (une nouvelle fenêtre) de Visual Studio.

2. Fermez les solutions ouvertes dans la nouvelle instance de Visual Studio.

3. Sélectionnez **Déboguer > Attacher au processus**.

   ![](vs-callstack-images/image1.png "Sélectionnez Déboguer > Attacher au processus")

4. Sélectionnez l’instance d’origine suspendu `devenv.exe` dans la liste des **processus disponibles**.

5. Sélectionnez **Déboguer > Interrompre tout**.

   ![](vs-callstack-images/image2.png "Sélectionnez Déboguer > Interrompre tout")

6. Sélectionnez **Déboguer > Enregistrer le Dump sous**.

   ![](vs-callstack-images/image3.png "Sélectionnez Déboguer > Enregistrer le Dump sous")

7. Modification **enregistrer en tant que type** à **Minidump (\*.dmp)** . Cela génère un fichier beaucoup plus petit que **Minidump avec segment mémoire**, et le tas ne concerne généralement pas de diagnostic se fige.

   ![](vs-callstack-images/image4.png "Cela génère un fichier beaucoup plus petit que Minidump avec segment mémoire, et le tas est généralement pas pertinent pour le diagnostic se fige")

8. Enregistrez le fichier de vidage. Si l’envoi du fichier en ligne, vous pouvez le zip pour réduire la taille.
