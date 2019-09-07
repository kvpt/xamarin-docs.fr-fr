---
title: Modifications des outils du kit Android SDK
description: Modifications apportées à la façon dont le Android SDK gère les niveaux d’API installés et AVD.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5AC61C00-0FF6-4C2D-80E7-D67A3EE30A5A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/21/2018
ms.openlocfilehash: 897daef3aba1166018a0ac796e9c7956c5f0c711
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70761886"
---
# <a name="changes-to-the-android-sdk-tooling"></a>Modifications des outils du kit Android SDK

_Modifications apportées à la façon dont le Android SDK gère les niveaux d’API installés et AVD._

## <a name="changes-to-android-sdk-tooling"></a>Modifications apportées aux outils de Android SDK

Dans les versions récentes de la SDK Tools pour Android, Google a supprimé les gestionnaires AVD et SDK existants en faveur de nouvelles outils CLI (interface de ligne de commande). Le programme **Android** a été supprimé et les gestionnaires de l’interface utilisateur graphique (GUI) de Google dans Visual Studio pour Mac et les versions antérieures de Visual Studio Tools pour Xamarin ne fonctionnent plus avec la version antérieure du 25.2.5 de Android SDK Tools. Par exemple, si vous tentez d’utiliser le programme **Android** via la ligne de commande, un message d’erreur semblable au suivant s’affiche :

```shell
The "android" command is deprecated.
For manual SDK, AVD, and project management, please use Android Studio.
For command-line tools, use tools\bin\sdkmanager.bat
and tools\bin\avdmanager.bat
```

Les sections suivantes expliquent comment gérer les Android SDK et les appareils virtuels Android à l’aide d’Android SDK 25.3.0 et versions ultérieures.

### <a name="ui-tools"></a>Outils d’interface utilisateur

Visual Studio et Visual Studio pour Mac fournissent désormais des remplacements Xamarin pour les gestionnaires Google basés sur l’interface graphique utilisateur abandonnés :

- Pour télécharger des outils Android SDK, des plateformes et d’autres composants dont vous avez besoin pour développer des applications Xamarin. Android, utilisez le [Gestionnaire de Android SDK Xamarin](~/android/get-started/installation/android-sdk.md) au lieu de l’ancien gestionnaire du SDK Google.

- Pour créer et configurer des appareils virtuels Android, utilisez la [Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md) au lieu du gestionnaire d’émulateur Google hérité.

Ces outils sont fonctionnellement équivalents aux gestionnaires Google basés sur l’interface graphique utilisateur qu’ils remplacent.

### <a name="cli-tools"></a>Outils CLI

Vous pouvez également utiliser les outils de l’interface de commande CLI pour gérer et mettre à jour vos émulateurs et Android SDK. Les programmes suivants composent désormais l’interface de ligne de commande pour les outils de Android SDK :

#### <a name="sdkmanager"></a>sdkmanager

**Ajouté dans :** Android SDK Tools 25.2.3 (novembre, 2016) et versions ultérieures.

Il existe un nouveau programme appelé **sdkmanager** dans le dossier **Tools/bin** de votre Android SDK. Cet outil est utilisé pour conserver les Android SDK au niveau de la ligne de commande. Pour plus d’informations sur l’utilisation de cet outil, consultez [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html).

#### <a name="avdmanager"></a>avdmanager

**Ajouté dans :** Android SDK Tools 25.3.0 (mars, 2017) et versions ultérieures.

Il existe un nouveau programme appelé **avdmanager** dans le dossier **Tools/bin** de votre Android SDK. Cet outil est utilisé pour gérer le AVD pour le Émulateur Android. Pour plus d’informations sur l’utilisation de cet outil, consultez [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html).

### <a name="downgrading"></a>Rétrogradation

Vous pouvez rétrograder votre version de **Android SDK Tools** en installant une version précédente du Android SDK à partir du [site Web Android Developer](https://developer.android.com/studio/index.html).

### <a name="using-the-old-gui"></a>Utilisation de l’ancienne interface graphique utilisateur

Vous pouvez toujours utiliser l’interface graphique utilisateur d’origine en exécutant le programme **Android** à l’intérieur de votre dossier d' **Outils** à condition que vous soyez sur **Android SDK Tools** version **25.2.5** ou antérieure.

## <a name="related-links"></a>Liens associés

- [Configuration du kit Android SDK](~/android/get-started/installation/android-sdk.md)
- [Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)
- [Présentation des niveaux d’API d’Android](~/android/app-fundamentals/android-api-levels.md)
- [Notes de publication de SDK Tools (Google)](https://developer.android.com/studio/releases/sdk-tools.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
