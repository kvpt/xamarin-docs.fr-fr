---
title: Modifications des outils du kit Android SDK
description: Modifications apportées à comment le Kit Android SDK gère les niveaux d’API et AVD installés.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5AC61C00-0FF6-4C2D-80E7-D67A3EE30A5A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/21/2018
ms.openlocfilehash: dbd3287e7c646be7fd969699eab685906a1c6c1a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61093932"
---
# <a name="changes-to-the-android-sdk-tooling"></a>Modifications des outils du kit Android SDK

_Modifications apportées à comment le Kit Android SDK gère les niveaux d’API et AVD installés._

## <a name="changes-to-android-sdk-tooling"></a>Modifications des outils du Kit de développement logiciel Android

Dans les versions récentes de la SDK Tools pour Android, Google a supprimé les gestionnaires d’AVD et le kit SDK existants en faveur de nouveaux outils CLI (Interface de ligne de commande). Le **android** programme a été supprimé et les gestionnaires de Google GUI (Interface utilisateur graphique) dans Visual Studio pour Mac et les versions antérieures de Visual Studio Tools pour Xamarin ne fonctionnera plus après la version 25.2.5 d’Android SDK Tools. Par exemple, essayez d’utiliser le **android** programme par le biais de la ligne de commande génère un message d’erreur similaire à celui-ci :

```shell
The "android" command is deprecated.
For manual SDK, AVD, and project management, please use Android Studio.
For command-line tools, use tools\bin\sdkmanager.bat
and tools\bin\avdmanager.bat
```

Les sections suivantes expliquent comment gérer le Kit Android SDK et les appareils virtuels Android à l’aide du Kit de développement logiciel Android 25.3.0 et versions ultérieures.

### <a name="ui-tools"></a>Outils d’interface utilisateur

Visual Studio et Visual Studio pour Mac fournissent désormais des remplacements de Xamarin pour les gestionnaires d’interface utilisateur graphique de Google supprimées :

-   Pour télécharger les outils du Kit Android SDK, les plateformes et les autres composants dont vous avez besoin pour développer des applications Xamarin.Android, utilisez le [Gestionnaire Xamarin Android SDK](~/android/get-started/installation/android-sdk.md) plutôt que le gestionnaire SDK de Google hérité.

-   Pour créer et configurer des appareils virtuels Android, utilisez le [Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md) plutôt que le Gestionnaire d’émulateur Google hérité.

Ces outils sont fonctionnellement équivalents à l’interface utilisateur graphique de Google en fonction du gestionnaires elles remplacent.

### <a name="cli-tools"></a>Outils CLI

Alternativement, vous pouvez utiliser des outils CLI pour gérer et mettre à jour votre émulateurs Android SDK. Les programmes suivants composent maintenant l’interface de ligne de commande pour les outils du Kit Android SDK :

#### <a name="sdkmanager"></a>sdkmanager

**Ajouté dans :** Android SDK Tools 25.2.3 (novembre 2016) et versions ultérieures.

Il existe un nouveau programme appelé **sdkmanager** dans le **outils/bin** dossier de votre Kit de développement logiciel Android. Cet outil est utilisé pour mettre à jour le SDK Android à la ligne de commande. Pour plus d’informations sur l’utilisation de cet outil, consultez [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html).

#### <a name="avdmanager"></a>avdmanager

**Ajouté dans :** Android SDK Tools 25.3.0 (mars 2017) et versions ultérieures.

Il existe un nouveau programme appelé **avdmanager** dans le **outils/bin** dossier de votre Kit de développement logiciel Android. Cet outil est utilisé pour maintenir l’AVD pour l’émulateur Android. Pour plus d’informations sur l’utilisation de cet outil, consultez [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html).

### <a name="downgrading"></a>La rétrogradation

Vous pouvez réduire votre **Android SDK Tools** version en installant une version précédente du SDK Android à partir de la [site Web de développement Android](https://developer.android.com/studio/index.html).

### <a name="using-the-old-gui"></a>À l’aide de l’ancienne interface graphique utilisateur

Vous pouvez toujours utiliser l’interface utilisateur graphique d’origine en exécutant le **android** programmer à l’intérieur de votre **outils** dossier tant que vous êtes sur **Android SDK Tools** version **25.2.5**  ou inférieure.


## <a name="related-links"></a>Liens associés

- [Configuration du kit Android SDK](~/android/get-started/installation/android-sdk.md)
- [Gestionnaire d’appareils Android](~/android/get-started/installation/android-emulator/device-manager.md)
- [Présentation des niveaux d’API d’Android](~/android/app-fundamentals/android-api-levels.md)
- [Notes de publication de SDK Tools (Google)](https://developer.android.com/studio/releases/sdk-tools.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
