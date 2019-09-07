---
title: Accélération matérielle pour les performances de l’émulateur (Hyper-V et HAXM)
description: Cet article explique comment utiliser les fonctionnalités d’accélération matérielle de votre ordinateur pour optimiser les performances de l’émulateur Android.
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: 915874C3-2F0F-4D83-9C39-ED6B90BB2C8E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/27/2018
ms.openlocfilehash: add9c602f4c04f1d95db4cee578fdadf0b41cf33
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70758006"
---
# <a name="hardware-acceleration-for-emulator-performance-hyper-v--haxm"></a>Accélération matérielle pour les performances de l’émulateur (Hyper-V et HAXM)

_Cet article explique comment utiliser les fonctionnalités d’accélération matérielle de votre ordinateur pour optimiser les performances de l’émulateur Android._

Visual Studio permet aux développeurs de tester et déboguer facilement leurs applications Xamarin.Android à l’aide de l’émulateur Android quand un appareil Android n’est pas disponible ou n’est pas pratique à utiliser.
Toutefois, l’émulateur Android s’exécute trop lentement si l’accélération matérielle n’est pas disponible sur l’ordinateur qui l’exécute. Vous pouvez améliorer considérablement les performances de l’émulateur Android en utilisant des images spéciales d’appareils virtuels x86, conjointement avec les fonctionnalités de virtualisation de votre ordinateur.

::: zone pivot="windows"

## <a name="accelerating-android-emulators-on-windows"></a>Accélération des émulateurs Android sur Windows

Les technologies de virtualisation suivantes sont disponibles pour accélérer l’émulateur Android :

1. **Microsoft Hyper-V et la Plateforme de l’hyperviseur**.
   [Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/) est une fonctionnalité de virtualisation de Windows qui permet d’exécuter des systèmes informatiques virtualisés sur un ordinateur hôte physique.

2. **Intel Hardware Accelerated Execution Manager (HAXM)** .
   HAXM est un moteur de virtualisation pour les ordinateurs exécutant des processeurs Intel.

Pour des performances optimales, il est recommandé d’utiliser Hyper-V afin d’accélérer l’émulateur Android. Si Hyper-V n’est pas disponible sur votre ordinateur, vous pouvez utiliser HAXM. L’émulateur Android utilise automatiquement l’accélération matérielle si les critères suivants sont remplis :

- L’accélération matérielle est disponible et activée sur votre ordinateur de développement.

- L’émulateur exécute une image système créée pour un appareil virtuel **x86**.

> [!IMPORTANT]
> Vous ne pouvez pas exécuter un émulateur accéléré par machine virtuelle dans une autre machine virtuelle telle qu’une machine virtuelle hébergée par VirtualBox, VMWare ou Docker. Vous devez exécuter l’émulateur Android [directement sur votre matériel système](https://developer.android.com/studio/run/emulator-acceleration.html#extensions).

Pour plus d’informations sur le lancement de l’émulateur Android, et sur le débogage à l’aide de ce dernier, consultez [Débogage sur l’émulateur Android](~/android/deploy-test/debugging/debug-on-emulator.md).

<a name="hyper-v-win" />

## <a name="accelerating-with-hyper-v"></a>Accélération avec Hyper-V

Il est recommandé d’utiliser Hyper-V pour accélérer l’émulateur Android.
Avant d’activer Hyper-V, lisez la section suivante pour vérifier que votre ordinateur prend en charge Hyper-V.

### <a name="verifying-support-for-hyper-v"></a>Vérification de la prise en charge d’Hyper-V

Hyper-V s’exécute sur la plateforme d’hyperviseur Windows. Pour permettre l’utilisation de l’émulateur Android avec Hyper-V, l’ordinateur doit répondre aux critères suivants afin de prendre en charge la plateforme d’hyperviseur Windows :

- Votre matériel informatique doit répondre aux exigences suivantes :

  - UC Intel ou AMD Ryzen 64 bits avec SLAT (traduction d’adresse de second niveau)
  - Prise en charge par l’UC de l’extension du mode de supervision de machine virtuelle (VT-c sur les UC Intel)
  - 4 Go de mémoire au minimum

- Dans le BIOS de votre ordinateur, les éléments suivants doivent être activés :

  - Technologie de virtualisation (peut avoir une autre appellation selon le fabricant de la carte mère).
  - Prévention de l’exécution des données matérielle.

- Vous devez appliquer à votre ordinateur la mise à jour d’avril 2018 de Windows 10 (build 1803) ou une version ultérieure. Vous pouvez vérifier que votre version de Windows est à jour en effectuant les étapes suivantes :

  1. Dans la zone de recherche Windows, entrez **À propos de**.
  2. Sélectionnez **À propos de votre PC** dans les résultats de recherche.
  3. Faites défiler vers le bas la boîte de dialogue **À propos de** jusqu’à la section **Spécifications Windows**.
  4. Vérifiez que vous disposez au moins de la **version** 1803 :

      [![Spécifications Windows](hardware-acceleration-images/win/01-about-windows-w10-sml.png)](hardware-acceleration-images/win/01-about-windows-w10.png#lightbox)

Pour vérifier que le matériel et les logiciels de votre ordinateur sont compatibles avec Hyper-V, ouvrez une invite de commandes et tapez la commande suivante :

```cmd
systeminfo
```

Si toutes les exigences listées relatives à Hyper-V ont la valeur **Oui**, votre ordinateur peut prendre en charge Hyper-V. Par exemple :

[![Exemple de sortie de systeminfo](hardware-acceleration-images/win/02-systeminfo-w158-sml.png)](hardware-acceleration-images/win/02-systeminfo-w158.png#lightbox)

### <a name="enabling-hyper-v-acceleration"></a>Activation de l’accélération avec Hyper-V

Si votre ordinateur répond aux critères ci-dessus, effectuez les étapes suivantes pour accélérer l’émulateur Android avec Hyper-V :

1. Entrez **fonctionnalités Windows** dans la zone de recherche Windows, puis sélectionnez **Activer ou désactiver les fonctionnalités Windows** dans les résultats de la recherche. Dans la boîte de dialogue **Fonctionnalités Windows**, activez **Hyper-V** et la **plateforme d’hyperviseur Windows** :

    [![Activation d’Hyper-V et de la plateforme d’hyperviseur Windows](hardware-acceleration-images/win/03-hyper-v-settings-w158-sml.png)](hardware-acceleration-images/win/03-hyper-v-settings-w158.png#lightbox)

   Une fois que vous avez apporté ces changements, redémarrez l’ordinateur.

2. **Installez [Visual Studio 15.8 ou une version ultérieure](https://visualstudio.microsoft.com/vs/)** (dans cette version de Visual Studio, l’IDE prend en charge l’exécution de l’émulateur Android avec Hyper-V).

3. **Installez le package de l’émulateur Android 27.2.7 ou ultérieur**. Pour installer ce package, accédez à **Outils > Android > Android SDK Manager** dans Visual Studio. Sélectionnez l’onglet **Outils** et vérifiez que la version de l’émulateur Android correspond au moins à la version 27.2.7. Vérifiez également que la version d’Android SDK Tools est 26.1.1 ou ultérieure :

    [![Boîte de dialogue des kits Android SDK et outils](hardware-acceleration-images/win/04-sdk-manager-w158-sml.png)](hardware-acceleration-images/win/04-sdk-manager-w158.png#lightbox)

Quand vous créez un appareil virtuel (consultez [Gestion des appareils virtuels avec Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)), veillez à sélectionner une image système **x86**. Si vous utilisez une image système ARM, l’appareil virtuel ne sera pas accéléré et fonctionnera lentement.

## <a name="accelerating-with-haxm"></a>Accélération avec HAXM

Utilisez HAXM pour accélérer l’émulateur Android si votre ordinateur ne prend pas en charge Hyper-V. Vous devez [désactiver Device Guard](~/android/get-started/installation/android-emulator/troubleshooting.md?tabs=vswin#disable-devguard) pour pouvoir utiliser HAXM.

### <a name="verifying-haxm-support"></a>Vérification de la prise en charge d’HAXM

Pour déterminer si votre matériel prend en charge HAXM, suivez les étapes indiquées dans [Mon processeur prend-il en charge la technologie de virtualisation Intel ?](https://www.intel.com/content/www/us/en/support/processors/000005486.html).
Si votre matériel prend en charge HAXM, vous pouvez vérifier si HAXM est déjà installé en effectuant les étapes suivantes :

1. Ouvrez une fenêtre d’invite de commandes, puis entrez la commande suivante :

    ```cmd
    sc query intelhaxm
    ```

2. Examinez la sortie pour voir si le processus HAXM est en cours d’exécution. Si tel est le cas, vous devez voir la sortie listant l’état de `intelhaxm` comme étant `RUNNING`. Par exemple :

    ![Sortie de la commande sc query quand HAXM est disponible](hardware-acceleration-images/win/05-sc_query-w158.png)

   Si `STATE` n’a pas la valeur `RUNNING`, HAXM n’est pas installé.

Si votre ordinateur peut prendre en charge HAXM mais que ce dernier n’est pas installé, suivez les étapes de la prochaine section pour installer HAXM.

<a name="install-haxm-win" />

### <a name="installing-haxm"></a>Installation d’HAXM

Les paquets d’installation HAXM pour Windows sont disponibles sur la page [Gestionnaire d’exécution avec accélération matérielle d’Intel](https://software.intel.com/android/articles/intel-hardware-accelerated-execution-manager). Effectuez les étapes suivantes pour télécharger et installer HAXM :

1. Depuis le site web Intel, téléchargez la dernière version du programme d’installation du [moteur de virtualisation HAXM](https://software.intel.com/android/articles/intel-hardware-accelerated-execution-manager/) pour Windows. En téléchargeant le programme d’installation d’HAXM directement depuis le site web Intel, vous avez l’assurance d’utiliser la version la plus récente.

2. Exécutez **intelhaxm-android.exe** pour démarrer le programme d’installation d’HAXM. Acceptez les valeurs par défaut dans les boîtes de dialogue du programme d’installation :

   ![Fenêtre Intel Hardware Accelerated Execution Manager (HAXM)](hardware-acceleration-images/win/06-haxm-installer.png)

Quand vous créez un appareil virtuel (consultez [Gestion des appareils virtuels avec Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)), veillez à sélectionner une image système **x86**. Si vous utilisez une image système ARM, l’appareil virtuel ne sera pas accéléré et fonctionnera lentement.

## <a name="troubleshooting"></a>Résolution des problèmes

Pour obtenir de l’aide sur la résolution des problèmes d’accélération matérielle, consultez le guide [Résolution des problèmes](~/android/get-started/installation/android-emulator/troubleshooting.md?tabs=vswin#accel-issues-win) de l’émulateur Android.

::: zone-end
::: zone pivot="macos"

## <a name="accelerating-android-emulators-on-macos"></a>Accélération des émulateurs Android sur macOS

Les technologies de virtualisation suivantes sont disponibles pour accélérer l’émulateur Android :

1. **Framework Hypervisor d’Apple**.
   [Hypervisor](https://developer.apple.com/documentation/hypervisor) est une fonctionnalité de macOS 10.10 et version ultérieure, qui permet d’exécuter des machines virtuelles sur un Mac.

2. **Intel Hardware Accelerated Execution Manager (HAXM)** .
   [HAXM](https://software.intel.com/articles/intel-hardware-accelerated-execution-manager-intel-haxm) est un moteur de virtualisation pour les ordinateurs dotés d’UC Intel.

Pour des performances optimales, il est recommandé d’utiliser le framework Hypervisor afin d’accélérer l’émulateur Android. Si le framework Hypervisor n’est pas disponible sur votre Mac, vous pouvez utiliser HAXM. L’émulateur Android utilise automatiquement l’accélération matérielle si les critères suivants sont remplis :

- L’accélération matérielle est disponible et activée sur l’ordinateur de développement.

- L’émulateur exécute une image système créée pour un appareil virtuel **x86**.

> [!IMPORTANT]
>
> Vous ne pouvez pas exécuter un émulateur accéléré par une machine virtuelle dans une autre machine virtuelle, par exemple une machine virtuelle hébergée par VirtualBox, VMWare ou Docker. Vous devez exécuter l’émulateur Android [directement sur votre matériel système](https://developer.android.com/studio/run/emulator-acceleration.html#extensions).

Pour plus d’informations sur le lancement de l’émulateur Android, et sur le débogage à l’aide de ce dernier, consultez [Débogage sur l’émulateur Android](~/android/deploy-test/debugging/debug-on-emulator.md).

<a name="hypervisor" />

## <a name="accelerating-with-the-hypervisor-framework"></a>Accélération avec le framework Hypervisor

Pour permettre l’utilisation de l’émulateur Android avec le framework Hypervisor, votre Mac doit répondre aux critères suivants :

- Votre Mac doit exécuter macOS 10.10 ou une version ultérieure.

- L’UC de votre Mac doit pouvoir prendre en charge le framework Hypervisor.

Si votre Mac répond à ces critères, l’émulateur Android utilise automatiquement le framework Hypervisor pour l’accélération (même si HAXM est installé). Si vous ne savez pas si le framework Hypervisor est pris en charge sur votre Mac, consultez le guide [Résolution des problèmes](~/android/get-started/installation/android-emulator/troubleshooting.md?tabs=vsmac#hypervisor-issues) pour vérifier que votre Mac prend en charge Hypervisor.

Si le framework Hypervisor n’est pas pris en charge par votre Mac, vous pouvez utiliser HAXM pour accélérer l’émulateur Android (décrit ci-après).

<a name="haxm-mac" />

## <a name="accelerating-with-haxm"></a>Accélération avec HAXM

Si votre Mac ne prend pas en charge le framework Hypervisor (ou si vous utilisez une version de macOS antérieure à la version 10.10), vous pouvez vous servir du **Gestionnaire d’exécution avec accélération matérielle d’Intel** ([HAXM](https://software.intel.com/articles/intel-hardware-accelerated-execution-manager-intel-haxm)) pour accélérer l’émulateur Android.

Avant d’utiliser l’émulateur Android avec HAXM pour la première fois, pensez à vérifier qu’HAXM est installé et disponible pour l’émulateur Android.

### <a name="verifying-haxm-support"></a>Vérification de la prise en charge d’HAXM

Vous pouvez vérifier si HAXM est déjà installé en effectuant les étapes suivantes :

1. Ouvrez une fenêtre Terminal, puis entrez la commande suivante :

    ```bash
    ~/Library/Developer/Xamarin/android-sdk-macosx/tools/emulator -accel-check
    ```

   Cette commande suppose que le kit Android SDK est installé à l’emplacement par défaut : **~/Library/Developer/Xamarin/android-sdk-macosx**. Sinon, modifiez le chemin ci-dessus pour indiquer l’emplacement du kit Android SDK sur votre Mac.

2. Si HAXM est installé, la commande ci-dessus retourne un message similaire au résultat suivant :

    ```bash
    HAXM version 7.2.0 (3) is installed and usable.
    ```

   Si HAXM n’est *pas* installé, un message similaire à la sortie suivante est retourné :

    ```bash
    HAXM is not installed on this machine (/dev/HAX is missing).
    ```

Si HAXM n’est pas installé, effectuez les étapes de la section suivante pour l’installer.

<a name="install-haxm-mac" />

### <a name="installing-haxm"></a>Installation d’HAXM

Les paquets d’installation HAXM pour macOS sont disponibles sur la page [Gestionnaire d’exécution avec accélération matérielle d’Intel](https://software.intel.com/android/articles/intel-hardware-accelerated-execution-manager). Effectuez les étapes suivantes pour télécharger et installer HAXM :

1. Depuis le site web Intel, téléchargez la dernière version du programme d’installation du [moteur de virtualisation HAXM](https://software.intel.com/android/articles/intel-hardware-accelerated-execution-manager/) pour macOS.

2. Exécutez le programme d’installation de HAXM. Acceptez les valeurs par défaut dans les boîtes de dialogue du programme d’installation :

   [![Fenêtre Intel Hardware Accelerated Execution Manager Setup](hardware-acceleration-images/mac/01-haxm-installer-sml.png)](hardware-acceleration-images/mac/01-haxm-installer.png#lightbox)

## <a name="troubleshooting"></a>Résolution des problèmes

Pour obtenir de l’aide sur la résolution des problèmes d’accélération matérielle, consultez le guide [Résolution des problèmes](~/android/get-started/installation/android-emulator/troubleshooting.md?tabs=vsmac#accel-issues-mac) de l’émulateur Android.

::: zone-end

## <a name="related-links"></a>Liens associés

- [Exécuter des applications sur l’émulateur Android](https://developer.android.com/studio/run/emulator)
