---
title: Comment collecter les piles d’appels du processus Visual Studio ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 64c24b09-2c4a-43ad-b94d-6cd05a1aee44
author: davidortinau
ms.author: daortin
ms.date: 03/30/2017
ms.openlocfilehash: aa8ce8791c598fa4891257b3d832478ecc5ee136
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86938799"
---
# <a name="how-do-i-collect-the-current-call-stacks-of-the-visual-studio-process"></a>Comment collecter les piles d’appels du processus Visual Studio ?

Lorsque l’interface utilisateur graphique se bloque (se bloque) dans Visual Studio, un élément important d’informations de diagnostic à collecter est l’ensemble des piles d’appels de tous les threads du processus Visual Studio. Pour enregistrer ces informations pour une instance suspendue de Visual Studio, vous pouvez utiliser une deuxième instance de Visual Studio :

1. Démarrez une deuxième instance (une nouvelle fenêtre) de Visual Studio.

2. Fermez toutes les solutions ouvertes dans la nouvelle instance de Visual Studio.

3. Sélectionnez **Déboguer > attacher au processus**.

   ![Sélectionnez Déboguer > attacher au processus](vs-callstack-images/image1.png)

4. Sélectionnez l’instance bloquée d’origine de `devenv.exe` dans la liste des **processus disponibles**.

5. Sélectionnez **Déboguer > arrêter tout**.

   ![Sélectionner déboguer > arrêter tout](vs-callstack-images/image2.png)

6. Sélectionnez **Déboguer > enregistrer le dump sous**.

   ![Sélectionnez Déboguer > enregistrer le dump sous](vs-callstack-images/image3.png)

7. Remplacez le **type de fichier** par **Minidump ( \* . DMP)**. Cela génère un fichier bien plus petit que **Minidump avec segment de mémoire**, et le segment de mémoire n’est généralement pas pertinent pour le diagnostic des gèles.

   ![Cela génère un fichier bien plus petit que Minidump avec segment de mémoire, et le segment de mémoire n’est généralement pas pertinent pour le diagnostic des gèles](vs-callstack-images/image4.png)

8. Enregistrez le fichier dump. Si vous envoyez le fichier en ligne, vous pouvez le compresser pour réduire sa taille.
