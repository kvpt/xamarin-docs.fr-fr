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
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027022"
---
# <a name="where-can-i-set-my-android-sdk-locations"></a>Où définir les emplacements de mon kit Android SDK ?

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Dans Visual Studio, accédez à **outils > Options > les paramètres Xamarin > Android** pour afficher et définir l’emplacement du Android SDK :

[onglet exemples d’emplacements de ![dans préférences](android-sdk-location-images/win/01-locations-sml.png)](android-sdk-location-images/win/01-locations.png#lightbox)

L’emplacement par défaut de chaque chemin d’accès est le suivant :

- Emplacement du kit de développement Java : 

    **C :\\Program Files\\Java\\JDK 1.8.0 _131**

- Emplacement du Android SDK : 

    **C:\\Program Files (x86)\\Android\\android-sdk**

- Emplacement NDK Android : 

    **C :\\ProgramData\\Microsoft\\AndroidNDK64\\Android-NDK-R13b**

Notez que le numéro de version du NDK peut varier. Par exemple, au lieu d' **Android-NDK-R13b**, il peut s’agir d’une version antérieure telle qu' **Android-NDK-r10e**.

Pour définir l’emplacement du Android SDK, entrez le chemin d’accès complet du répertoire de Android SDK dans la zone **emplacement du Android SDK** . Vous pouvez accéder à l’emplacement de Android SDK dans l’Explorateur de fichiers, copier le chemin d’accès à partir de la barre d’adresses et coller ce chemin dans la zone **emplacement du Android SDK** .
Par exemple, si votre Android SDK emplacement est **C :\\utilisateurs\\nom d’utilisateur\\AppData\\Local\\Android\\SDK**, effacez l’ancien chemin d’accès dans la zone **emplacement du Android SDK** , collez ce chemin , puis cliquez sur **OK**.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Dans Visual Studio pour Mac, accédez à **préférences > projets > emplacements du kit de développement logiciel (SDK) > Android**. Dans la page **Android** , cliquez sur l’onglet **locations** pour afficher et définir l’emplacement du kit de développement logiciel (SDK) :

[onglet exemples d’emplacements de![dans préférences](android-sdk-location-images/mac/01-locations-sml.png)](android-sdk-location-images/mac/01-locations.png#lightbox)

L’emplacement par défaut de chaque chemin d’accès est le suivant :

- Emplacement du Android SDK : 

    **~/Library/Developer/Xamarin/android-sdk-macosx**

- Emplacement NDK Android : 

    **~/Library/Developer/Xamarin/android-ndk/android-ndk-r14b**

- Emplacement du kit de développement logiciel Java (JDK) : 

    **/usr**

Notez que le numéro de version du NDK peut varier. Par exemple, au lieu d' **Android-NDK-r14b**, il peut s’agir d’une version antérieure telle qu' **Android-NDK-r10e**.

Pour définir l’emplacement du Android SDK, entrez le chemin d’accès complet du répertoire de Android SDK dans la zone **emplacement du Android SDK** . Vous pouvez sélectionner le dossier Android SDK dans le Finder, appuyez sur **CTRL&#8984;+ + I** pour afficher les informations du dossier, cliquez et faites glisser le chemin d’accès à droite de **où :** , copiez-le, puis collez-le dans la zone **emplacement du Android SDK** de l’onglet **emplacements** . Par exemple, si votre Android SDK emplacement est à **~/Library/Developer/Android/SDK**, effacez l’ancien chemin d’accès dans la zone **emplacement du Android SDK** , collez-le dans ce chemin d’accès, puis cliquez sur **OK**.

-----
