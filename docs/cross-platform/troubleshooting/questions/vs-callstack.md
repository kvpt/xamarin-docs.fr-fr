---
title: Comment collecter les piles d’appels du processus Visual Studio ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 64c24b09-2c4a-43ad-b94d-6cd05a1aee44
author: davidortinau
ms.author: daortin
ms.date: 03/30/2017
ms.openlocfilehash: 9ed79b2273758b8051a96169d4c9b53870de1fb1
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73013022"
---
# <a name="how-do-i-collect-the-current-call-stacks-of-the-visual-studio-process"></a>Comment collecter les piles d’appels du processus Visual Studio ?

Lorsque l’interface utilisateur graphique se bloque (se bloque) dans Visual Studio, un élément important d’informations de diagnostic à collecter est l’ensemble des piles d’appels de tous les threads du processus Visual Studio. Pour enregistrer ces informations pour une instance suspendue de Visual Studio, vous pouvez utiliser une deuxième instance de Visual Studio :

1. Démarrez une deuxième instance (une nouvelle fenêtre) de Visual Studio.

2. Fermez toutes les solutions ouvertes dans la nouvelle instance de Visual Studio.

3. Sélectionnez **Déboguer > Attacher au processus**.

   ![](vs-callstack-images/image1.png "Select Debug > Attach to Process")

4. Sélectionnez l’instance bloquée d’origine de `devenv.exe` dans la liste des **processus disponibles**.

5. Sélectionnez **Déboguer > arrêter tout**.

   ![](vs-callstack-images/image2.png "Select Debug > Break All")

6. Sélectionnez **Déboguer > enregistrer le dump sous**.

   ![](vs-callstack-images/image3.png "Select Debug > Save Dump As")

7. Remplacez le **type de fichier** par **minidump (\*. DMP)** . Cela génère un fichier bien plus petit que **Minidump avec segment de mémoire**, et le segment de mémoire n’est généralement pas pertinent pour le diagnostic des gèles.

   ![](vs-callstack-images/image4.png "This will produce a much smaller file than Minidump with Heap, and the heap is usually not relevant for diagnosing freezes")

8. Enregistrez le fichier dump. Si vous envoyez le fichier en ligne, vous pouvez le compresser pour réduire sa taille.
