---
title: Comment mettre à jour la version du kit JDK Java ?
description: Cet article illustre comment mettre à jour la version Java Development Kit (JDK) sur Windows et Mac.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 4b3ac51d-18dd-4034-87b4-4365194e4ece
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 09/07/2018
ms.openlocfilehash: 0f7499551db7d86d7978b9c3e1f562a2f054c202
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73019531"
---
# <a name="how-do-i-update-the-java-development-kit-jdk-version"></a>Comment mettre à jour la version du kit JDK Java ?

_Cet article illustre comment mettre à jour la version Java Development Kit (JDK) sur Windows et Mac._

## <a name="overview"></a>Vue d’ensemble

Xamarin.Android utilise le kit de développement Java (JDK) pour s’intégrer à l’Android SDK pour la construction d’applications Android et l’exécution du concepteur Android. Les dernières versions de l’Android SDK (API 24 et plus) nécessitent JDK 8 (1,8). Alternativement, vous pouvez installer le [Microsoft Mobile OpenJDK Preview](~/android/get-started/installation/openjdk.md). Le Microsoft Mobile OpenJDK remplacera éventuellement JDK 8 pour le développement Xamarin.Android.

Pour mettre à jour le Microsoft Mobile OpenJDK, voir [Microsoft Mobile OpenJDK Preview](~/android/get-started/installation/openjdk.md). Pour mettre à jour JDK 8, suivez ces étapes :

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Télécharger JDK 8 (1.8) sur le [site Oracle](https://www.oracle.com/technetwork/java/javase/downloads/index.html):

    ![Capture d’écran de la page de téléchargement JDK sur le site Oracle](update-jdk-images/image1.png)

2. Choisissez la version 64 bits pour permettre le rendu des [commandes personnalisées](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/vs/xamarin.vs_4/xamarin.vs_4.2/index.md#androiddesignercustomcontrols) dans le concepteur Xamarin Android:

    ![Sélection du forfait Windows x64 JDK à télécharger à partir de la page de téléchargement JDK](update-jdk-images/image2.png)

3. Exécuter le .exe et installer les **outils de développement**:

    ![Installation des outils de développement dans l’installateur JDK](update-jdk-images/image3.png)

4. Ouvrez Visual Studio et mettez à jour **l’emplacement Java Development Kit** pour indiquer le nouveau JDK sous Outils > Options > **Xamarin > Paramètres Android > Java Development Kit Location**:

    [![Réglage de voie pour le JDK dans la page Paramètres Android](update-jdk-images/image4-sml.png)](update-jdk-images/image4.png#lightbox)

Assurez-vous de redémarrer Visual Studio après la mise à jour de l’emplacement.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

1. Télécharger JDK 8 (1.8) sur le [site Oracle](https://www.oracle.com/technetwork/java/javase/downloads/index.html):

    ![Capture d’écran de la page de téléchargement JDK sur le site Oracle](update-jdk-images/image1.png)

2. Ouvrez le fichier .dmg et exécutez l’installateur .pkg :

    ![Exécution de l’installateur JDK sur macOS](update-jdk-images/image5.png)

Mac OS définira automatiquement la nouvelle version JDK par défaut en mettant à jour **/System/Library/Frameworks/JavaVM.framework/Versions/Current**. Vous pouvez ensuite vérifier que l’emplacement **Java SDK (JDK)** est réglé à la valeur par défaut prévue de **/usr** sous **Visual Studio pour Mac > Préférences > Projets > SDK Emplacements > Android > Emplacements > Java SDK (JDK) Emplacement**:

[![Définir l’emplacement JDK dans l’onglet Emplacements Android](update-jdk-images/image6-sml.png)](update-jdk-images/image6.png#lightbox)

-----
