---
title: Comment collecter les piles d’appels du processus Visual Studio ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 64c24b09-2c4a-43ad-b94d-6cd05a1aee44
author: conceptdev
ms.author: crdun
ms.date: 03/30/2017
ms.openlocfilehash: f07bc4437293bdc49e4811c0104fd7245ccf9738
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70282956"
---
# <a name="how-do-i-collect-the-current-call-stacks-of-the-visual-studio-process"></a>Comment collecter les piles d’appels du processus Visual Studio ?

Lorsque l’interface utilisateur graphique se bloque (se bloque) dans Visual Studio, un élément important d’informations de diagnostic à collecter est l’ensemble des piles d’appels de tous les threads du processus Visual Studio. Pour enregistrer ces informations pour une instance suspendue de Visual Studio, vous pouvez utiliser une deuxième instance de Visual Studio :

1. Démarrez une deuxième instance (une nouvelle fenêtre) de Visual Studio.

2. Fermez toutes les solutions ouvertes dans la nouvelle instance de Visual Studio.

3. Sélectionnez **Déboguer > Attacher au processus**.

   ![](vs-callstack-images/image1.png "Sélectionnez Déboguer > attacher au processus")

4. Sélectionnez l’instance bloquée d' `devenv.exe` origine de dans la liste des **processus disponibles**.

5. Sélectionnez **Déboguer > arrêter tout**.

   ![](vs-callstack-images/image2.png "Sélectionner Déboguer > arrêter tout")

6. Sélectionnez **Déboguer > enregistrer le dump sous**.

   ![](vs-callstack-images/image3.png "Sélectionnez Déboguer > enregistrer le dump sous")

7. Remplacez le **type de fichier** par **Minidump\*(. DMP)** . Cela génère un fichier bien plus petit que **Minidump avec segment de mémoire**, et le segment de mémoire n’est généralement pas pertinent pour le diagnostic des gèles.

   ![](vs-callstack-images/image4.png "Cela génère un fichier bien plus petit que Minidump avec segment de mémoire, et le segment de mémoire n’est généralement pas pertinent pour le diagnostic des gèles")

8. Enregistrez le fichier dump. Si vous envoyez le fichier en ligne, vous pouvez le compresser pour réduire sa taille.
