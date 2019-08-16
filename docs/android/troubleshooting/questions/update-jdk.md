---
title: Comment mettre à jour la version du kit JDK Java ?
description: Cet article explique comment mettre à jour la version du kit de développement Java (JDK) sur Windows et Mac.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 4b3ac51d-18dd-4034-87b4-4365194e4ece
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/07/2018
ms.openlocfilehash: 193f9f92f112608908d2feb50f815a670d48b39c
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69523401"
---
# <a name="how-do-i-update-the-java-development-kit-jdk-version"></a>Comment mettre à jour la version du kit JDK Java ?

_Cet article explique comment mettre à jour la version du kit de développement Java (JDK) sur Windows et Mac._

## <a name="overview"></a>Présentation

Xamarin. Android utilise le kit de développement Java (JDK) pour s’intégrer au Android SDK pour la création d’applications Android et l’exécution du concepteur Android. Les versions les plus récentes des Android SDK (API 24 et ultérieures) requièrent JDK 8 (1,8). Vous pouvez également installer la version [préliminaire de Microsoft Mobile openjdk](~/android/get-started/installation/openjdk.md). Le OpenJDK Microsoft Mobile va finalement remplacer JDK 8 pour le développement Xamarin. Android.

Pour effectuer une mise à jour vers Microsoft Mobile OpenJDK, consultez la version [préliminaire de Microsoft Mobile openjdk](~/android/get-started/installation/openjdk.md). Pour effectuer une mise à jour vers JDK 8, procédez comme suit:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Téléchargez JDK 8 (1,8) à partir du [site Web Oracle](https://www.oracle.com/technetwork/java/javase/downloads/index.html):

    ![Capture d’écran de la page de téléchargement du JDK sur le site Web Oracle](update-jdk-images/image1.png)

2. Choisissez la version 64 bits pour autoriser le rendu des [contrôles personnalisés](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/vs/xamarin.vs_4/xamarin.vs_4.2/index.md#androiddesignercustomcontrols) dans Xamarin Android Designer:

    ![Sélection du package JDK Windows x64 à télécharger à partir de la page de téléchargement de JDK](update-jdk-images/image2.png)

3. Exécutez le fichier. exe et installez les **outils de développement**:

    ![Installation des outils de développement dans le programme d’installation de JDK](update-jdk-images/image3.png)

4. Ouvrez Visual Studio et mettez à jour l' **emplacement du kit de développement Java** pour pointer vers le nouveau JDK sous **outils > Options > Xamarin > paramètres Android > emplacement du kit de développement Java**:

    [![Paramètre Path du JDK dans la page des paramètres Android](update-jdk-images/image4-sml.png)](update-jdk-images/image4.png#lightbox)

Veillez à redémarrer Visual Studio après la mise à jour de l’emplacement.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Téléchargez JDK 8 (1,8) à partir du [site Web Oracle](https://www.oracle.com/technetwork/java/javase/downloads/index.html):

    ![Capture d’écran de la page de téléchargement du JDK sur le site Web Oracle](update-jdk-images/image1.png)

2. Ouvrez le fichier. dmg et exécutez le programme d’installation. pkg:

    ![Exécution du programme d’installation de JDK sur macOS](update-jdk-images/image5.png)

Mac OS définira automatiquement la nouvelle version de JDK comme version par défaut en mettant à jour **/System/Library/Frameworks/JavaVM.Framework/versions/Current**. Vous pouvez ensuite double-vérifier que l’emplacement du **Kit de développement logiciel (SDK) Java** est défini sur la valeur par défaut de **/usr** dans **Visual Studio pour Mac > préférences > projets > emplacements du SDK > emplacements des > Android > emplacement du kit de développement logiciel Java (JDK)** :

[![Définition de l’emplacement du JDK sous l’onglet emplacements Android](update-jdk-images/image6-sml.png)](update-jdk-images/image6.png#lightbox)

-----

