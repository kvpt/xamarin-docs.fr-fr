---
title: Où définir les emplacements de mon kit Android SDK ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 6A9DE6E9-3E27-4DD2-87D2-34E95E5D401C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 11/16/2017
ms.openlocfilehash: 8685be4bb1cc45ff04dc8d9f7d8e64e7b1483b60
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027022"
---
# <a name="where-can-i-set-my-android-sdk-locations"></a>Où définir les emplacements de mon kit Android SDK ?

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Dans Visual Studio, naviguez vers **Tools > Options > Xamarin > Paramètres Android** pour afficher et définir l’emplacement Android SDK :

[![Exemple Emplacements onglet dans préférences](android-sdk-location-images/win/01-locations-sml.png)](android-sdk-location-images/win/01-locations.png#lightbox)

L’emplacement par défaut pour chaque chemin est le suivant :

- Java Development Kit Emplacement: 

    **C:\\Fichiers\\\\de programme Java jdk1.8.0-131**

- Emplacement Android SDK: 

    **C:\\Fichiers de programme\\(x86) Android\\android-sdk**

- Emplacement Android NDK: 

    **c:\\\\ProgramData\\Microsoft AndroidNDK64\\android-ndk-r13b**

Notez que le numéro de version du NDK peut varier. Par exemple, au lieu **d’Android-ndk-r13b**, il pourrait être une version antérieure comme **Android-ndk-r10e**.

Pour définir l’emplacement Android SDK, entrez le chemin complet de l’annuaire Android SDK dans la boîte **de localisation Android SDK.** Vous pouvez naviguer vers l’emplacement Android SDK dans File Explorer, copier le chemin de la barre d’adresse, et coller ce chemin dans la boîte **de localisation Android SDK.**
Par exemple, si votre emplacement Android SDK est à **C:\\\\Users nom d’utilisateur\\AppData\\Local\\Android\\Sdk**, effacer l’ancien chemin dans la boîte de localisation Android **SDK,** coller dans ce chemin, et cliquez sur **OK**.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Dans Visual Studio for Mac, naviguez vers **Préférences > Projets > SDK Locations > Android**. Dans la page **Android,** cliquez sur l’onglet **Emplacements** pour afficher et définir l’emplacement SDK :

[![Exemple Emplacements onglet dans préférences](android-sdk-location-images/mac/01-locations-sml.png)](android-sdk-location-images/mac/01-locations.png#lightbox)

L’emplacement par défaut pour chaque chemin est le suivant :

- Emplacement Android SDK: 

    **/Bibliothèque/Développeur/Xamarin/android-sdk-macosx**

- Emplacement Android NDK: 

    **/Bibliothèque/Développeur/Xamarin/android-ndk/android-ndk-r14b**

- Java SDK (JDK) Emplacement: 

    **/usr**

Notez que le numéro de version du NDK peut varier. Par exemple, au lieu **d’Android-ndk-r14b**, il pourrait être une version antérieure comme **Android-ndk-r10e**.

Pour définir l’emplacement Android SDK, entrez le chemin complet de l’annuaire Android SDK dans la boîte **de localisation Android SDK.** Vous pouvez sélectionner le dossier Android SDK dans le Finder, appuyez sur **CTRLMD&#8984;'Je** pour afficher les informations de dossier, cliquez et faites glisser le chemin vers la droite de **Where:**, copiez, puis collez-le à la boîte **de localisation Android SDK** dans **l’onglet Emplacements.** Par exemple, si votre emplacement Android SDK est à **l’adresse suivante : '/Bibliothèque/Développeur/Android/Sdk,** effacez l’ancien chemin dans la boîte **de localisation Android SDK,** collez-vous dans ce chemin, et cliquez **sur OK**.

-----
