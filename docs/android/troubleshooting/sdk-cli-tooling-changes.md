---
title: Modifications des outils du kit Android SDK
description: Modifications de la façon dont l’Android SDK gère les niveaux d’API installés et les AD.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5AC61C00-0FF6-4C2D-80E7-D67A3EE30A5A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/21/2018
ms.openlocfilehash: 4c559a76d7354fd957d065717ef14d91591d1be0
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73019497"
---
# <a name="changes-to-the-android-sdk-tooling"></a>Modifications des outils du kit Android SDK

_Modifications de la façon dont l’Android SDK gère les niveaux d’API installés et les AD._

## <a name="changes-to-android-sdk-tooling"></a>Modifications apportées à l’outillage Android SDK

Dans les versions récentes des outils SDK pour Android, Google a supprimé les gestionnaires EXISTANTs AVD et SDK en faveur de nouveaux CLI (Command Line Interface) outillage. Le programme **Android** a été supprimé et les gestionnaires de Google GUI (Graphical User Interface) dans Visual Studio pour Mac et les anciennes versions de Visual Studio Tools pour Xamarin ne fonctionneront plus après la version 25.2.5 d’Android SDK Tools. Par exemple, essayer d’utiliser le programme **Android** via la ligne de commande se traduira par un message d’erreur comme ce qui suit:

```shell
The "android" command is deprecated.
For manual SDK, AVD, and project management, please use Android Studio.
For command-line tools, use tools\bin\sdkmanager.bat
and tools\bin\avdmanager.bat
```

Les sections suivantes expliquent comment gérer les appareils virtuels Android SDK et Android à l’aide d’Android SDK 25.3.0 et plus tard.

### <a name="ui-tools"></a>Outils d’interface utilisateur

Visual Studio et Visual Studio pour Mac fournissent maintenant des remplacements Xamarin pour les gestionnaires abandonnés basés sur Google GUI:

- Pour télécharger les outils, plates-formes et autres composants Android SDK dont vous avez besoin pour développer des applications Xamarin.Android, utilisez le [gestionnaire Xamarin Android SDK](~/android/get-started/installation/android-sdk.md) au lieu de l’héritage Google SDK Manager.

- Pour créer et configurer des appareils virtuels Android, utilisez le [gestionnaire d’appareils Android](~/android/get-started/installation/android-emulator/device-manager.md) au lieu de l’ancien gestionnaire d’emulator google.

Ces outils sont fonctionnellement équivalents aux gestionnaires google GUI qu’ils remplacent.

### <a name="cli-tools"></a>Outils CLI

Alternativement, vous pouvez utiliser des outils CLI pour gérer et mettre à jour vos émulateurs et Android SDK. Les programmes suivants constituent maintenant l’interface de la ligne de commande pour les outils Android SDK :

#### <a name="sdkmanager"></a>sdkmanager

**Ajouté dans:** Android SDK Tools 25.2.3 (Novembre, 2016) et plus.

Il ya un nouveau programme appelé **sdkmanager** dans le dossier **outils / bin** de votre SDK Android. Cet outil est utilisé pour maintenir l’Android SDK à la ligne de commande. Pour plus d’informations sur l’utilisation de cet outil, voir [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html).

#### <a name="avdmanager"></a>avdmanager

**Ajouté dans:** Android SDK Tools 25.3.0 (Mars, 2017) et plus.

Il ya un nouveau programme appelé **avdmanager** dans le dossier **outils / bin** de votre SDK Android. Cet outil est utilisé pour maintenir les AD POUR l’émulateur Android. Pour plus d’informations sur l’utilisation de cet outil, voir [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html).

### <a name="downgrading"></a>Déclassement

Vous pouvez déclasser votre version **Android SDK Tools** en installant une version précédente de l’Android SDK à partir du [site Web Android Developer](https://developer.android.com/studio/index.html).

### <a name="using-the-old-gui"></a>Utilisation de l’ancienne GUI

Vous pouvez toujours utiliser l’interface utilisateur d’origine en exécutant le programme **Android** à l’intérieur de votre dossier **d’outils** tant que vous êtes sur **Android SDK Tools** version **25.2.5** ou plus bas.

## <a name="related-links"></a>Liens connexes

- [Configuration du kit Android SDK](~/android/get-started/installation/android-sdk.md)
- [Gestionnaire d’appareils Android](~/android/get-started/installation/android-emulator/device-manager.md)
- [Présentation des niveaux d’API d’Android](~/android/app-fundamentals/android-api-levels.md)
- [Notes de publication d’Android SDK Tools (Google)](https://developer.android.com/studio/releases/sdk-tools.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
