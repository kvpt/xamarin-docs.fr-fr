---
title: Résolution des problèmes de l’émulateur Android
description: Cet article explique comment diagnostiquer et résoudre les problèmes qui peuvent se produire quand vous utilisez l’émulateur Android.
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: 4F053CC9-9378-47CB-8002-978A6558C4D0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/27/2018
ms.openlocfilehash: d73c72fa70a22bacf122f5c3957b789914dfd765
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "79304050"
---
# <a name="android-emulator-troubleshooting"></a>Résolution des problèmes de l’émulateur Android

_Cet article décrit les messages d’avertissement les plus courants et les problèmes qui se produisent lors de la configuration et l’exécution de l’émulateur Android. En outre, il décrit des solutions pour résoudre ces erreurs ainsi que divers conseils de dépannage pour vous aider à diagnostiquer les problèmes d’émulateur._

::: zone pivot="windows"

## <a name="deployment-issues-on-windows"></a>Problèmes de déploiement sur Windows

L’émulateur peut afficher certains messages d’erreur quand vous déployez votre application. Les erreurs et les solutions les plus courantes sont expliquées ici.

### <a name="deployment-errors"></a>Erreurs de déploiement

Si vous voyez s’afficher un message d’erreur indiquant l’échec de l’installation de l’APK sur l’émulateur ou l’échec de l’exécution d’**adb** (Android Debug Bridge), vérifiez que le kit Android SDK peut se connecter à votre émulateur. Pour vérifier la connectivité de l’émulateur, effectuez les étapes suivantes :

1. Lancez l’émulateur à partir d’**Android Device Manager** (sélectionnez votre appareil virtuel et cliquez sur **Démarrer**).

2. Ouvrez une invite de commande et rendez-vous au dossier où **une bad est** installée. Si le kit Android SDK est installé à son emplacement par défaut, **adb** se trouve sur **C:\\Program Files (x86)\\Android\\android-sdk\\platform-tools\\adb.exe**. Sinon, modifiez ce chemin pour indiquer l’emplacement du kit Android SDK sur votre ordinateur.

3. Tapez la commande suivante :

   ```shell
   adb devices
   ```

4. Si l’émulateur est accessible à partir du kit Android SDK, l’émulateur doit apparaître dans la liste des appareils connectés. Par exemple :

   ```shell
   List of devices attached
   emulator-5554   device
   ```

5. Si l’émulateur n’apparaît pas dans cette liste, démarrez le **Gestionnaire du kit Android SDK**, appliquez les mises à jour, puis essayez à nouveau de lancer l’émulateur.

### <a name="mmio-access-error"></a>Erreur d’accès MMIO

Si vous voyez s’afficher un message indiquant qu’**une erreur d’accès MMIO s’est produite**, redémarrez l’émulateur.

<a name="gps-win" />

## <a name="missing-google-play-services"></a>Google Play Services manquant

Si Google Play Services ou Google Play Store n’est pas installé sur l’appareil virtuel que vous exécutez dans l’émulateur, cela signifie souvent que l’appareil virtuel a été créé sans que ces paquets soient inclus. Quand vous créez un appareil virtuel (consultez [Gestion des appareils virtuels avec Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)), veillez à sélectionner l’une des options suivantes, ou les deux :

- **** API Google&ndash; : inclut Google Play Services dans l’appareil virtuel.
- **** Google Play Store&ndash; : inclut Google Play Store dans l’appareil virtuel.

Par exemple, cet appareil virtuel inclut Google Play Services et Google Play Store :

[![Exemple AVD avec Google Play Services et Google Play Store activé](troubleshooting-images/win/00-add-gps-w158-sml.png)](troubleshooting-images/win/00-add-gps-w158.png#lightbox)

> [!NOTE]
> Les images Google Play Store sont disponibles uniquement pour certains types d’appareil de base tels que Pixel, Pixel 2, Nexus 5 et Nexus 5X.

<a name="perf-win" />

## <a name="performance-issues"></a>Problèmes de performance

Les problèmes de performances sont généralement causés par l’un des facteurs suivants :

- L’émulateur s’exécute sans accélération matérielle

- L’appareil virtuel exécuté dans l’émulateur n’utilise pas une image système x86

Les sections suivantes décrivent ces scénarios de façon plus détaillée.

### <a name="hardware-acceleration-is-not-enabled"></a>Accélération matérielle non activée

Si l’accélération matérielle n’est pas activée, le démarrage d’un appareil virtuel à partir de Device Manager entraîne l’affichage d’une boîte de dialogue avec un message d’erreur indiquant que la plateforme WHPX (plateforme d’hyperviseur Windows) n’est pas configurée correctement :

![Exemple d’avertissement de Device Manager](troubleshooting-images/win/01-dev-mgr-warning-w158.png)

Si ce message d’erreur s’affiche, consultez [Problèmes d’accélération matérielle](#accel-issues-win) ci-dessous afin de connaître les étapes à suivre pour vérifier et activer l’accélération matérielle.

### <a name="acceleration-is-enabled-but-the-emulator-runs-too-slowly"></a>L’accélération est activée mais l’émulateur s’exécute trop lentement 

Bien souvent, ce problème se produit si vous n’utilisez pas une image x86 dans votre AVD (appareil virtuel). Lorsque vous créez un appareil virtuel (voir [Gérer les périphériques virtuels avec le gestionnaire d’appareils Android),](~/android/get-started/installation/android-emulator/device-manager.md)assurez-vous de sélectionner une image système x86 :

[![Sélection d’une image système x86 pour un appareil virtuel](troubleshooting-images/win/02-x86-virtual-device-w158-sml.png)](troubleshooting-images/win/02-x86-virtual-device-w158.png#lightbox)

<a name="accel-issues-win" />

## <a name="hardware-acceleration-issues"></a>Problèmes d’accélération matérielle

Que vous utilisiez Hyper-V ou HAXM pour l’accélération matérielle, vous risquez d’être confronté à des problèmes de configuration ou des conflits avec les autres logiciels de votre ordinateur. Vous pouvez vérifier si l’accélération matérielle est activée (ainsi que la méthode d’accélération utilisée par l’émulateur) en ouvrant une invite de commandes et en entrant la commande suivante :

```cmd
"C:\Program Files (x86)\Android\android-sdk\emulator\emulator-check.exe" accel
```

Cette commande suppose que le kit Android SDK est installé à l’emplacement par défaut : **C:\\Program Files (x86)\\Android\\android-sdk**. Sinon, modifiez le chemin ci-dessus pour indiquer l’emplacement du kit Android SDK sur votre ordinateur.

### <a name="hardware-acceleration-not-available"></a>Accélération matérielle non disponible

Si Hyper-V est disponible, un message similaire à l’exemple suivant est retourné à partir de la commande **emulator-check.exe accel** :

```cmd
HAXM is not installed, but Windows Hypervisor Platform is available.
```

Si HAXM est disponible, un message similaire à l’exemple suivant est retourné :

```cmd
HAXM version 6.2.1 (4) is installed and usable.
```

Si l’accélération matérielle n’est pas disponible, un message similaire à l’exemple suivant s’affiche (l’émulateur recherche HAXM, s’il ne parvient pas à trouver Hyper-V) :

```cmd
HAXM is not installed on this machine
```

Si l’accélération matérielle n’est pas disponible, consultez la section [Accélération avec Hyper-V](~/android/get-started/installation/android-emulator/hardware-acceleration.md?tabs=vswin#hyper-v-win) pour savoir comment activer l’accélération matérielle sur votre ordinateur.

### <a name="incorrect-bios-settings"></a>Paramètres du BIOS incorrects

Si le BIOS n’a pas été configuré correctement pour prendre en charge l’accélération matérielle, un message similaire à l’exemple suivant s’affiche quand vous exécutez la commande **emulator-check.exe accel** :

```cmd
VT feature disabled in BIOS/UEFI
```

Pour corriger ce problème, redémarrez le BIOS de l’ordinateur et activez les options suivantes :

- Technologie de virtualisation (peut avoir une autre appellation selon le fabricant de la carte mère).
- Prévention de l’exécution des données matérielle.

Si l’accélération matérielle est activée et si le BIOS est configuré de manière appropriée, l’émulateur doit s’exécuter correctement en tirant parti de l’accélération matérielle.
Toutefois, des problèmes spécifiques à Hyper-V et HAXM peuvent se produire, comme expliqué ci-après.

### <a name="hyper-v-issues"></a>Problèmes liés à Hyper-V

Dans certains cas, l’activation d’**Hyper-V** et de la **Plateforme d’hyperviseur Windows** dans la boîte de dialogue **Activer ou désactiver des fonctionnalités Windows** peut poser des problèmes pour Hyper-V. Pour vérifier si Hyper-V est activé, effectuez les étapes suivantes :

1. Dans la zone de recherche Windows, entrez **powershell**.

2. Cliquez avec le bouton droit sur **Windows PowerShell** dans les résultats de la recherche, puis sélectionnez **Exécuter en tant qu’administrateur**.

3. Dans la console PowerShell, entrez la commande suivante :

    ```powershell
    Get-WindowsOptionalFeature -FeatureName Microsoft-Hyper-V-All -Online
    ```

    Si Hyper-V n’est pas activé, un message similaire à l’exemple suivant s’affiche pour indiquer que l’état d’Hyper-V est **Désactivé** :

    ```
    FeatureName      : Microsoft-Hyper-V-All
    DisplayName      : Hyper-V
    Description      : Provides services and management tools for creating and running virtual machines and their resources.
    RestartRequired  : Possible
    State            : Disabled
    CustomProperties : 
    ```

4. Dans la console PowerShell, entrez la commande suivante :

    ```powershell
    Get-WindowsOptionalFeature -FeatureName HypervisorPlatform -Online
    ```

    Si l’hyperviseur n’est pas activé, un message similaire à l’exemple suivant s’affiche pour indiquer que l’état de la plateforme d’hyperviseur est **Désactivé** :

    ```
    FeatureName      : HypervisorPlatform
    DisplayName      : Windows Hypervisor Platform
    Description      : Enables virtualization software to run on the Windows hypervisor
    RestartRequired  : Possible
    State            : Disabled
    CustomProperties : 
    ```

Si Hyper-V et/ou la plateforme d’hyperviseur ne sont pas activés, utilisez les commandes PowerShell suivantes pour les activer :

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
Enable-WindowsOptionalFeature -Online -FeatureName HypervisorPlatform -All
```

Une fois ces commandes exécutées, redémarrez. 

Pour plus d’informations sur l’activation d’Hyper-V (notamment sur les techniques permettant d’activer Hyper-V à l’aide de l’outil Gestion et maintenance des images de déploiement), consultez [Installer Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v).

### <a name="haxm-issues"></a>Problèmes liés à HAXM

Les problèmes liés à HAXM sont souvent le résultat de conflits avec d’autres technologies de virtualisation, de paramètres incorrects ou d’un pilote HAXM obsolète.

### <a name="haxm-process-is-not-running"></a>Le processus HAXM n’est pas en cours d’exécution

Si HAXM est installé, vous pouvez vérifier que le processus HAXM est en cours d’exécution en ouvrant une invite de commandes et en entrant la commande suivante :

```cmd
sc query intelhaxm
```

Si le processus HAXM est en cours d’exécution, vous devez voir s’afficher une sortie similaire au résultat suivant :

```cmd
SERVICE_NAME: intelhaxm
    TYPE               : 1  KERNEL_DRIVER
    STATE              : 4  RUNNING
                            (STOPPABLE, NOT_PAUSABLE, IGNORES_SHUTDOWN)
    WIN32_EXIT_CODE    : 0  (0x0)
    SERVICE_EXIT_CODE  : 0  (0x0)
    CHECKPOINT         : 0x0
    WAIT_HINT          : 0x0
```

Si `STATE` n’a pas la valeur `RUNNING`, consultez [How to Use the Intel Hardware Accelerated Execution Manager](https://software.intel.com/android/articles/how-to-use-the-intel-hardware-accelerated-execution-manager-intel-haxm-android-emulator) pour résoudre le problème.

<a name="virt-conflicts" />

#### <a name="haxm-virtualization-conflicts"></a>Conflits de virtualisation HAXM

HAXM peut entrer en conflit avec d’autres technologies qui utilisent la virtualisation, comme Hyper-V, Windows Device Guard et certains logiciels antivirus :

- **Hyper-V** &ndash; Si vous utilisez une version de Windows antérieure à la **mise à jour de Windows du 10 avril 2018 (build 1803)** et si Hyper-V est activé, suivez les étapes indiquées dans [Désactivation d’Hyper-V](#disable-hyperv) pour permettre l’activation d’HAXM.

- **Device Guard** &ndash; Device Guard et Credential Guard peuvent empêcher la désactivation d’Hyper-V sur les ordinateurs Windows. Pour désactiver Device Guard et Credential Guard, consultez [Désactivation de Device Guard](#disable-devguard).

- **Logiciel antivirus** &ndash; Si vous exécutez un logiciel antivirus qui utilise l’assistance matérielle à la virtualisation (comme Avast), désactivez ou désinstallez ce logiciel, redémarrez, puis réessayez d’exécuter l’émulateur Android.

#### <a name="incorrect-bios-settings"></a>Paramètres du BIOS incorrects

Si vous utilisez HAXM sur un PC Windows, HAXM ne fonctionne pas sauf si la technologie de virtualisation (Intel VT-x) est activée dans le BIOS. Si VT-x est désactivé, une erreur similaire à ce qui suit s’affiche quand vous essayez de démarrer l’émulateur Android :

**Cet ordinateur répond à la configuration requise pour HAXM, mais la technologie Intel de virtualisation (VT-x) n’est pas activée.**

Pour corriger cette erreur, démarrez l’ordinateur dans le BIOS, activez VT-x et SLAT (traduction d’adresse de second niveau), puis redémarrez l’ordinateur dans Windows.

<a name="disable-hyperv" />

#### <a name="disabling-hyper-v"></a>Désactivation d'Hyper-V

Si vous utilisez une version de Windows antérieure à la **mise à jour de Windows du 10 avril 2018 (build 1803)** et qu’Hyper-V est activé, vous devez désactiver Hyper-V et redémarrer votre ordinateur pour installer et utiliser HAXM. Si vous utilisez la **mise à jour de Windows du 10 avril 2018 (build 1803)** ou une version ultérieure, l’émulateur Android version 27.2.7 ou ultérieure peut utiliser Hyper-V (au lieu de HAXM) pour l’accélération matérielle ; il n’est donc pas nécessaire de désactiver Hyper-V.

Vous pouvez désactiver Hyper-V à partir du Panneau de configuration en effectuant les étapes suivantes :

1. Entrez **fonctionnalités Windows** dans la zone de recherche Windows, puis sélectionnez **Activer ou désactiver les fonctionnalités Windows** dans les résultats de la recherche.

2. Décochez **Hyper-V** :

    ![Désactivation d’Hyper-V dans la boîte de dialogue Fonctionnalités de Windows](troubleshooting-images/win/03-uncheck-hyper-v.png)

3. Redémarrez l'ordinateur.

Vous pouvez également utiliser la commande PowerShell suivante pour désactiver l’hyperviseur Hyper-V :

`Disable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V-Hypervisor`

Intel HAXM et Microsoft Hyper-V ne peuvent pas être actifs en même temps. Malheureusement, il n’existe aucun moyen de passer d’Hyper-V à HAXM, et inversement, sans avoir à redémarrer l’ordinateur. 

Dans certains cas, la procédure ci-dessus ne permet pas de désactiver Hyper-V si Device Guard et Credential Guard sont activés. Si vous ne parvenez pas à désactiver Hyper-V (ou s’il semble être désactivé mais que l’installation d’HAXM échoue toujours), utilisez la procédure de la section suivante pour désactiver Device Guard et Credential Guard.

<a name="disable-devguard" />

#### <a name="disabling-device-guard"></a>Désactivation de Device Guard

Device Guard et Credential Guard peuvent empêcher la désactivation d’Hyper-V sur les ordinateurs Windows. Cette situation est souvent un problème pour les machines jointes à un domaine, qui sont configurées et contrôlées par une organisation propriétaire. Sur Windows 10, effectuez les étapes suivantes pour savoir si **Device Guard** est en cours d’exécution :

1. Entrez **Info système** dans la zone de recherche Windows, puis sélectionnez **Informations système** dans les résultats de la recherche.

2. Dans **Résumé système**, vérifiez si **Sécurité basée sur la virtualisation de Device Guard** est présent et **En cours d’exécution** :

   [![Device Guard est présent et en cours d’exécution](troubleshooting-images/win/04-device-guard-sml.png)](troubleshooting-images/win/04-device-guard.png#lightbox)

Si Device Guard est activé, effectuez les étapes suivantes pour le désactiver :

1. Vérifiez qu’**Hyper-V** est désactivé (sous **Activer ou désactiver des fonctionnalités Windows**) comme décrit dans la section précédente.

2. Dans la zone de recherche Windows, entrez **gpedit.msc**, puis sélectionnez le résultat de la recherche **Modifier la stratégie de groupe**. Ces étapes permettent de lancer l’**Éditeur d’objets de stratégie de groupe**.

3. Dans l’**Éditeur de stratégie de groupe locale**, accédez à **Configuration ordinateur > Modèles d’administration > Système > Device Guard** :

   [![Device Guard in Local Group Policy Editor](troubleshooting-images/win/05-group-policy-editor-sml.png)](troubleshooting-images/win/05-group-policy-editor.png#lightbox)

4. Modifiez **Activer la sécurité basée sur la virtualisation** en le passant à **Désactivé** (comme illustré ci-dessus) et quittez l’**Éditeur de stratégie de groupe locale**.

5. Dans la zone de recherche Windows, entrez **cmd**. Lorsque **l’invite de commande** apparaît dans les résultats de recherche, cliquez à droite Sur **l’invite de commande** et **sélectionnez Exécuter en tant qu’administrateur**.

6. Copiez et collez les commandes suivantes dans la fenêtre d’invite de commandes (si lecteur **Z:** est utilisé, choisissez une lettre de lecteur non utilisée) :

    ```cmd
    mountvol Z: /s
    copy %WINDIR%\System32\SecConfig.efi Z:\EFI\Microsoft\Boot\SecConfig.efi /Y
    bcdedit /create {0cb3b571-2f2e-4343-a879-d86a476d7215} /d "DebugTool" /application osloader
    bcdedit /set {0cb3b571-2f2e-4343-a879-d86a476d7215} path "\EFI\Microsoft\Boot\SecConfig.efi"
    bcdedit /set {bootmgr} bootsequence {0cb3b571-2f2e-4343-a879-d86a476d7215}
    bcdedit /set {0cb3b571-2f2e-4343-a879-d86a476d7215} loadoptions DISABLE-LSA-ISO,DISABLE-VBS
    bcdedit /set {0cb3b571-2f2e-4343-a879-d86a476d7215} device partition=Z:
    mountvol Z: /d
    ```

7. Redémarrez votre ordinateur. Dans l’écran de démarrage, vous devez voir s’afficher une invite similaire au message suivant :

   **Voulez-vous désactiver Credential Guard ?**

   Appuyez sur la touche indiquée pour désactiver Credential Guard, comme il vous est demandé.

8. Une fois que l’ordinateur a redémarré, vérifiez de nouveau qu’Hyper-V est désactivé (comme décrit dans les étapes précédentes).

Si Hyper-V n’est toujours pas désactivé, les stratégies de votre ordinateur joint au domaine peuvent vous empêcher de désactiver Device Guard ou Credential Guard. Dans ce cas, vous pouvez demander à votre administrateur de domaine de faire une exception en vous autorisant à ne pas utiliser Credential Guard. Sinon, vous pouvez également utiliser un ordinateur non joint à un domaine pour utiliser HAXM.

## <a name="additional-troubleshooting-tips"></a>Conseils supplémentaires pour la résolution des problèmes

Les suggestions suivantes sont souvent utiles pour diagnostiquer les problèmes d’émulateur Android.

### <a name="starting-the-emulator-from-the-command-line"></a>Démarrage de l’émulateur à partir de la ligne de commande

Si l’émulateur n’est pas déjà en cours d’exécution, vous pouvez le démarrer à partir de la ligne de commande (au lieu de Visual Studio) pour voir sa sortie. En règle générale, les images AVD de l’émulateur Android sont stockées à l’emplacement suivant (remplacez *nom_utilisateur* par votre nom d’utilisateur Windows) :

**C:\\Users\\*nom_utilisateur*\\.android\\avd**

Vous pouvez lancer l’émulateur avec une image AVD à partir de cet emplacement en passant le nom de dossier de l’AVD. Par exemple, cette commande permet de lancer un fichier AVD nommé **Pixel_API_27** :

```cmd
"C:\Program Files (x86)\Android\android-sdk\emulator\emulator.exe" -partition-size 512 -no-boot-anim -verbose -feature WindowsHypervisorPlatform -avd Pixel_API_27 -prop monodroid.avdname=Pixel_API_27
```

Cet exemple suppose que le kit Android SDK est installé à l’emplacement par défaut : **C:\\Program Files (x86)\\Android\\android-sdk**. Sinon, modifiez le chemin ci-dessus pour indiquer l’emplacement du kit Android SDK sur votre ordinateur.

Quand vous exécutez cette commande, elle génère de nombreuses lignes de sortie au démarrage de l’émulateur. En particulier, les lignes telles que celles de l’exemple suivant s’affichent si l’accélération matérielle est activée et qu’elle fonctionne correctement (dans cet exemple, HAXM est utilisé pour l’accélération matérielle) :

```cmd
emulator: CPU Acceleration: working
emulator: CPU Acceleration status: HAXM version 6.2.1 (4) is installed and usable.
```

### <a name="viewing-device-manager-logs"></a>Affichage des journaux de Device Manager

Bien souvent, vous pouvez diagnostiquer les problèmes d’émulateur en consultant les journaux de Device Manager. Ces journaux sont écrits à l’emplacement suivant :

**C:\\Users\\*nom_utilisateur*\\AppData\\Roaming\\XamarinDeviceManager**

Vous pouvez afficher chaque fichier **DeviceManager.log** à l’aide d’un éditeur de texte tel que le Bloc-notes. L’exemple suivant d’entrée de journal indique que HAXM est introuvable sur l’ordinateur :

```cmd
Component Intel x86 Emulator Accelerator (HAXM installer) r6.2.1 [Extra: (Intel Corporation)] not present on the system
```

::: zone-end
::: zone pivot="macos"

## <a name="deployment-issues-on-macos"></a>Problèmes de déploiement sur macOS

L’émulateur peut afficher certains messages d’erreur quand vous déployez votre application. Les erreurs et les solutions les plus courantes sont expliquées ci-dessous.

### <a name="deployment-errors"></a>Erreurs de déploiement

Si vous voyez s’afficher un message d’erreur indiquant l’échec de l’installation de l’APK sur l’émulateur ou l’échec de l’exécution d’**adb** (Android Debug Bridge), vérifiez que le kit Android SDK peut se connecter à votre émulateur. Pour vérifier la connectivité, effectuez les étapes suivantes :

1. Lancez l’émulateur à partir d’**Android Device Manager** (sélectionnez votre appareil virtuel et cliquez sur **Démarrer**).

2. Ouvrez une invite de commande et rendez-vous au dossier où **une bad est** installée. Si le kit Android SDK est installé à son emplacement par défaut, **adb** se trouve sur **~/Library/Developer/Xamarin/android-sdk-macosx/platform-tools/adb**. Sinon, modifiez ce chemin pour indiquer l’emplacement du kit Android SDK sur votre ordinateur.

3. Tapez la commande suivante :

   ```shell
   adb devices
   ```

4. Si l’émulateur est accessible à partir du kit Android SDK, l’émulateur doit apparaître dans la liste des appareils connectés. Par exemple :

   ```shell
   List of devices attached
   emulator-5554   device
   ```

5. Si l’émulateur n’apparaît pas dans cette liste, démarrez le **Gestionnaire du kit Android SDK**, appliquez les mises à jour, puis essayez à nouveau de lancer l’émulateur.

### <a name="mmio-access-error"></a>Erreur d’accès MMIO

Si vous voyez s’afficher le message indiquant qu’**une erreur d’accès MMIO s’est produite**, redémarrez l’émulateur.

<a name="gps-mac" />

## <a name="missing-google-play-services"></a>Google Play Services manquant

Si Google Play Services ou Google Play Store n’est pas installé sur l’appareil virtuel que vous exécutez dans l’émulateur, cela signifie généralement que l’appareil virtuel a été créé sans que ces paquets soient inclus. Quand vous créez un appareil virtuel (consultez [Gestion des appareils virtuels avec Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)), veillez à sélectionner l’une des options suivantes, ou les deux :

- **** API Google&ndash; : inclut Google Play Services dans l’appareil virtuel.
- **** Google Play Store&ndash; : inclut Google Play Store dans l’appareil virtuel.

Par exemple, cet appareil virtuel inclut Google Play Services et Google Play Store :

[![Exemple AVD avec Google Play Services et Google Play Store activé](troubleshooting-images/mac/01-google-play-services-m75-sml.png)](troubleshooting-images/mac/01-google-play-services-m75.png#lightbox)

> [!NOTE]
> Les images Google Play Store sont disponibles uniquement pour certains types d’appareil de base tels que Pixel, Pixel 2, Nexus 5 et Nexus 5X.

<a name="perf-mac" />

## <a name="performance-issues"></a>Problèmes de performance

Les problèmes de performances sont généralement causés par l’un des facteurs suivants :

- L’émulateur s’exécute sans accélération matérielle

- L’appareil virtuel exécuté dans l’émulateur n’utilise pas une image système x86

Les sections suivantes décrivent ces scénarios de façon plus détaillée.

### <a name="hardware-acceleration-is-not-enabled"></a>Accélération matérielle non activée

Si l’accélération matérielle n’est pas activée, une boîte de dialogue peut s’afficher en indiquant que l’**appareil va s’exécuter sans accélération** quand vous aurez déployé votre application sur l’émulateur Android. Si vous n’êtes pas certain que l’accélération matérielle est activée sur votre ordinateur (ou si vous souhaitez savoir quelle technologie fournit l’accélération), consultez [Problèmes d’accélération matérielle](#accel-issues-mac) ci-dessous afin de connaître les étapes à suivre pour vérifier et activer l’accélération matérielle.

### <a name="acceleration-is-enabled-but-the-emulator-runs-too-slowly"></a>L’accélération est activée mais l’émulateur s’exécute trop lentement 

Bien souvent, ce problème se produit si vous n’utilisez pas une image x86 dans votre appareil virtuel. Quand vous créez un appareil virtuel (consultez [Gestion des appareils virtuels avec Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)), veillez à sélectionner une image système x86 :

[![Sélection d’une image système x86 pour un appareil virtuel](troubleshooting-images/mac/02-x86-virtual-device-m75-sml.png)](troubleshooting-images/mac/02-x86-virtual-device-m75.png#lightbox)

<a name="accel-issues-mac" />

## <a name="hardware-acceleration-issues"></a>Problèmes d’accélération matérielle

Que vous utilisiez le framework Hypervisor ou HAXM pour activer l’accélération matérielle de l’émulateur, vous pouvez être confronté à des problèmes d’installation ou à des problèmes d’obsolescence d’une version de macOS. Les sections suivantes peuvent vous aider à résoudre ce problème.

<a name="hypervisor-issues" />

### <a name="hypervisor-framework-issues"></a>Problèmes liés au framework Hypervisor

Si vous utilisez macOS 10.10 ou une version ultérieure sur un Mac récent, l’émulateur Android utilise automatiquement le framework Hypervisor pour l’accélération matérielle. Toutefois, certains Macs plus anciens ou exécutant une version de macOS antérieure à la version 10.10 risquent de ne pas prendre en charge le framework Hypervisor.

Pour déterminer si votre Mac prend en charge ou non le framework Hypervisor, ouvrez une fenêtre Terminal, puis entrez la commande suivante :

```bash
sysctl kern.hv_support
```

Si votre Mac prend en charge le framework Hypervisor, la commande ci-dessus retourne le résultat suivant :

```bash
kern.hv_support: 1
```

Si le framework Hypervisor n’est pas disponible sur votre Mac, vous pouvez suivre les étapes décrites dans [Accélération avec HAXM](~/android/get-started/installation/android-emulator/hardware-acceleration.md?tabs=vsmac#haxm-mac) pour utiliser HAXM à la place.

### <a name="haxm-issues"></a>Problèmes liés à HAXM

Si l’émulateur Android ne démarre pas correctement, cela est souvent dû à des problèmes liés à HAXM. Les problèmes liés à HAXM sont souvent le résultat de conflits avec d’autres technologies de virtualisation, de paramètres incorrects ou d’un pilote HAXM obsolète. Essayez de réinstaller le pilote HAXM, en utilisant la procédure décrite dans [Installation d’HAXM](~/android/get-started/installation/android-emulator/hardware-acceleration.md?tabs=vsmac#install-haxm-mac).

## <a name="additional-troubleshooting-tips"></a>Conseils supplémentaires pour la résolution des problèmes

Les suggestions suivantes sont souvent utiles pour diagnostiquer les problèmes d’émulateur Android.

### <a name="starting-the-emulator-from-the-command-line"></a>Démarrage de l’émulateur à partir de la ligne de commande

Si l’émulateur n’est pas déjà en cours d’exécution, vous pouvez le démarrer à partir de la ligne de commande (au lieu de Visual Studio pour Mac) pour voir sa sortie. En règle générale, les images AVD de l’émulateur Android sont stockées à l’emplacement suivant :

**~/.android/avd**

Vous pouvez lancer l’émulateur avec une image AVD à partir de cet emplacement en passant le nom de dossier de l’AVD. Par exemple, cette commande permet de lancer un fichier AVD nommé **Pixel_2_API_28** :

```cmd
~/Library/Developer/Xamarin/android-sdk-macosx/emulator/emulator -partition-size 512 -no-boot-anim -verbose -feature WindowsHypervisorPlatform -avd Pixel_2_API_28 -prop monodroid.avdname=Pixel_2_API_28
```

Si le kit Android SDK est installé à son emplacement par défaut, l’émulateur se trouve dans le répertoire **~/Library/Developer/Xamarin/android-sdk-macosx/emulator**. Sinon, modifiez ce chemin pour indiquer l’emplacement du kit Android SDK sur votre Mac.

Quand vous exécutez cette commande, elle génère de nombreuses lignes de sortie au démarrage de l’émulateur. En particulier, les lignes telles que celles de l’exemple suivant s’affichent si l’accélération matérielle est activée et qu’elle fonctionne correctement (dans cet exemple, le framework Hypervisor est utilisé pour l’accélération matérielle) :

```cmd
emulator: CPU Acceleration: working
emulator: CPU Acceleration status: Hypervisor.Framework OS X Version 10.13
```

### <a name="viewing-device-manager-logs"></a>Affichage des journaux de Device Manager

Bien souvent, vous pouvez diagnostiquer les problèmes d’émulateur en consultant les journaux de Device Manager. Ces journaux sont écrits à l’emplacement suivant :

**~/Library/Logs/XamarinDeviceManager**

Vous pouvez afficher chaque fichier **Android Devices.log** en double-cliquant dessus pour l’ouvrir dans l’application Console. L’exemple suivant d’entrée de journal indique que HAXM est introuvable :

```cmd
Component Intel x86 Emulator Accelerator (HAXM installer) r6.2.1 [Extra: (Intel Corporation)] not present on the system
```

::: zone-end
