---
title: Déboguer sur un appareil Wear
description: Cet article explique comment déboguer une application Xamarin.Android usure sur un appareil Wear.
ms.prod: xamarin
ms.assetid: 01668E4B-BB83-4C26-B23A-F788173FB823
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 816ec5c861b5889e1735eab6293ed10318c53644
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831881"
---
# <a name="debug-on-a-wear-device"></a>Déboguer sur un appareil Wear

_Cet article explique comment déboguer une application Xamarin.Android usure sur un appareil Wear._


## <a name="overview"></a>Présentation

Si vous avez un appareil Android Wear, par exemple un Smartwatch Android Wear, vous pouvez exécuter l’application sur l’appareil au lieu d’utiliser un émulateur. (Si vous n’êtes pas encore familiarisé avec le processus de déploiement et l’exécution d’applications de Android Wear, consultez [Wear Hello,](~/android/wear/get-started/hello-wear.md).)

## <a name="prepare-the-wear-device"></a>Préparer l’usure du périphérique :

Pour activer le débogage sur l’appareil Android Wear, utilisez les étapes suivantes :

1.  Ouvrez le **paramètres** menu sur l’appareil Android Wear.

2.  Faites défiler vers le bas du menu et appuyez sur **sur**.

3.  Appuyez sur le numéro de build 7 fois.

4.  Sur le **paramètres** menu, appuyez sur **Options développeurs**.

5.  Vérifiez que **ADB débogage** est activé.


## <a name="debugging-over-usb"></a>Débogage via USB

Si votre appareil Wear a un port USB, vous pouvez connecter l’appareil de l’usure sur votre ordinateur, déployer et exécuter/déboguer l’application comme vous le feriez avec un téléphone Android (pour plus d’informations, consultez [déboguer sur un appareil](~/android/deploy-test/debugging/debug-on-device.md)).


## <a name="debugging-over-bluetooth"></a>Débogage via Bluetooth

Si votre appareil Wear n’a pas d’un port USB, vous pouvez déployer l’application à l’appareil Wear via Bluetooth par le routage de sortie de débogage de l’application sur un téléphone Android qui est connecté à votre ordinateur. 

### <a name="prepare-your-phone"></a>Préparer votre téléphone

Utilisez les étapes suivantes pour préparer votre téléphone pour l’établissement de connexions Bluetooth sur l’appareil Wear : 

1.  Si vous ne le n'avez pas déjà fait, de votre téléphone pour le développement Xamarin.Android configurer comme expliqué dans [configurer un appareil pour le développement](~/android/get-started/installation/set-up-device-for-development.md).

2.  Téléchargez et installez la version gratuite [Android Wear](https://play.google.com/store/apps/details?id=com.google.android.wearable.app) application à partir du Store Play Google.

### <a name="connect-the-device"></a>Connectez l’appareil

Utilisez les étapes suivantes pour connecter votre appareil d’usure à votre téléphone :

1.  Sur le téléphone qui sera agissent comme intermédiaire de Bluetooth (configuré ci-dessus), démarrez l’application Android Wear. 

2.  Appuyez sur la **paramètres** icône.

3.  Activer **débogage via Bluetooth**. Vous devez voir l’état suivant affiché sur l’écran de l’application Android Wear :

        Host: disconnected
        Target: connected

4.  Connectez le téléphone à votre ordinateur via USB. Sur votre ordinateur, entrez les commandes suivantes :

    ```shell
    adb forward tcp:4444 localabstract:/adb-hub
    adb connect 127.0.0.1:4444
    ```

    Si le port 4444 n’est pas disponible, vous pouvez utiliser n’importe quel autre port disponible auquel vous avez accès. 

    > [!NOTE]
    > Si vous redémarrez Visual Studio ou Visual Studio pour Mac, vous devez exécuter ces commandes pour configurer une connexion à l’appareil de l’usure.

5.  Lorsque l’appareil Wear vous y invite, confirmez que vous autorisez **ADB débogage**. Dans l’application Android Wear, vous devriez voir l’état changer à :

        Host: connected
        Target: connected

6.  Après avoir terminé les étapes ci-dessus, en cours d’exécution `adb devices` affiche l’état du téléphone et l’appareil Android Wear :

        List of devices attached
        127.0.0.1:4444    device
        019ad61df0a69399  device

À ce stade, vous pouvez déployer votre application à l’appareil de l’usure.

<a name="screenshots" />

### <a name="taking-screenshots"></a>Prendre des captures d’écran

Vous pouvez effectuer une capture d’écran de l’appareil Wear en entrant la commande suivante : 

```shell
adb -s 127.0.0.1:4444 shell screencap -p /sdcard/DCIM/screencap.png
```

Copier la capture d’écran sur votre ordinateur en entrant la commande suivante :

```shell
adb -s 127.0.0.1:4444 pull /sdcard/DCIM/screencap.png
```

Supprimer la capture d’écran sur l’appareil en entrant la commande suivante :

```shell
adb -s 127.0.0.1:4444 shell rm /sdcard/DCIM/screencap.png
```


### <a name="uninstalling-an-app"></a>Désinstaller une application

Vous pouvez désinstaller une application à partir de l’appareil wear en entrant la commande suivante :

```shell
adb -s 127.0.0.1:4444 uninstall <package name>
```

Par exemple, pour supprimer l’application avec le nom du package `com.xamarin.weartest`, entrez la commande suivante :

```shell
adb -s 127.0.0.1:4444 uninstall com.xamarin.weartest
```

Pour plus d’informations sur le débogage des appareils Android Wear via Bluetooth, consultez [débogage via Bluetooth](https://developer.android.com/training/wearables/apps/bt-debugging.html).


## <a name="debugging-a-wear-app-with-a-companion-phone-app"></a>Débogage d’une application Wear avec une application de téléphone complément

Les applications Android Wear sont empaquetées avec une application de téléphone Android Compagnon pour la distribution sur Google Play (pour plus d’informations, consultez [fonctionne avec l’empaquetage](~/android/wear/deploy-test/packaging.md)). Toutefois, vous toujours développer l’application de l’usure et son application Compagnon séparément. Lorsque vous relâchez votre application via le Store Play Google, l’application de l’usure est empaquetée avec l’application Compagnon et automatiquement installée si possible.

Pour déboguer l’application Wear avec une application compagnon : 

1.  Générez et déployez l’application complément au numéro de téléphone.

2.  Cliquez sur le projet de l’usure et définissez-le comme projet de démarrage par défaut.

3.  Déployer le projet de l’usure sur l’appareil portable.

4.  Exécuter et déboguer l’application Wear sur l’appareil.

 
## <a name="summary"></a>Récapitulatif

Cet article a expliqué comment configurer un appareil Android Wear pour le débogage d’usure à partir de Visual Studio via Bluetooth et comment déboguer une application Wear avec une application de téléphone Compagnon. Elle également fourni des conseils de débogage courants pour déboguer une application Wear via Bluetooth.
