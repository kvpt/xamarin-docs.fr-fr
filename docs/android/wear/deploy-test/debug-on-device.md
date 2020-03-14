---
title: Déboguer sur un appareil Wear
description: Cet article explique comment déboguer une application Xamarin. Android usure sur un appareil d’usure.
ms.prod: xamarin
ms.assetid: 01668E4B-BB83-4C26-B23A-F788173FB823
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 965ed4e802c05f8450192c0fec17fe31e464c779
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304533"
---
# <a name="debug-on-a-wear-device"></a>Déboguer sur un appareil Wear

_Cet article explique comment déboguer une application Xamarin. Android usure sur un appareil d’usure._

## <a name="overview"></a>Vue d’ensemble

Si vous avez un appareil Android d’usure comme un SmartWatch Android, vous pouvez exécuter l’application sur l’appareil au lieu d’utiliser un émulateur. (Si vous n’êtes pas encore familiarisé avec le processus de déploiement et d’exécution d’applications Android, consultez [Hello, usure](~/android/wear/get-started/hello-wear.md).)

## <a name="prepare-the-wear-device"></a>Préparer l’appareil à l’usure :

Procédez comme suit pour activer le débogage sur l’appareil Android usure :

1. Ouvrez le menu **paramètres** sur l’appareil Android.

2. Faites défiler le menu vers le bas et appuyez **sur à propos**de.

3. Appuyez sur le numéro de build 7 fois.

4. Dans le menu **paramètres** , appuyez sur **Options du développeur**.

5. Vérifiez que le **Débogage ADB** est activé.

## <a name="debugging-over-usb"></a>Débogage sur USB

Si votre appareil d’usure dispose d’un port USB, vous pouvez connecter le périphérique d’usure à votre ordinateur, le déployer et exécuter/déboguer l’application comme vous le feriez avec un téléphone Android (pour plus d’informations, consultez [Déboguer sur un appareil](~/android/deploy-test/debugging/debug-on-device.md)).

## <a name="debugging-over-bluetooth"></a>Débogage sur Bluetooth

Si votre appareil d’usure n’a pas de port USB, vous pouvez déployer l’application sur le périphérique d’usure via Bluetooth en acheminant la sortie de débogage de l’application sur un téléphone Android connecté à votre ordinateur. 

### <a name="prepare-your-phone"></a>Préparer votre téléphone

Procédez comme suit pour préparer votre téléphone pour établir des connexions Bluetooth à l’appareil d’usure : 

1. Si vous ne l’avez pas déjà fait, configurez votre téléphone pour le développement Xamarin. Android, comme expliqué dans [configurer l’appareil pour le développement](~/android/get-started/installation/set-up-device-for-development.md).

2. Téléchargez et installez l’application [Android d’usure](https://play.google.com/store/apps/details?id=com.google.android.wearable.app) gratuite à partir du Google Play Store.

### <a name="connect-the-device"></a>Connecter l’appareil

Suivez les étapes ci-dessous pour connecter votre appareil d’usure à votre téléphone :

1. Sur le téléphone qui fera office d’intermédiaire Bluetooth (configuré ci-dessus), démarrez l’application Android usure. 

2. Appuyez sur l’icône des **paramètres** .

3. Activez le **débogage sur Bluetooth**. L’état suivant doit s’afficher à l’écran de l’application Android usure :

    ```
    Host: disconnected
    Target: connected
    ```

4. Connectez le téléphone à votre ordinateur via USB. Sur votre ordinateur, entrez les commandes suivantes :

    ```shell
    adb forward tcp:4444 localabstract:/adb-hub
    adb connect 127.0.0.1:4444
    ```

    Si le port 4444 n’est pas disponible, vous pouvez utiliser n’importe quel autre port disponible auquel vous avez accès. 

    > [!NOTE]
    > Si vous redémarrez Visual Studio ou Visual Studio pour Mac, vous devez réexécuter ces commandes pour configurer une connexion à l’appareil d’usure.

5. Lorsque l’appareil d’usure vous invite, confirmez que vous autorisez le **Débogage ADB**. Dans l’application Android usure, vous devez voir le changement d’État sur :

    ```
    Host: connected
    Target: connected
    ```

6. Une fois les étapes ci-dessus terminées, l’exécution de `adb devices` indique l’état du téléphone et de l’appareil Android :

    ```
    List of devices attached
    127.0.0.1:4444    device
    019ad61df0a69399  device
    ```

À ce stade, vous pouvez déployer votre application sur l’appareil d’usure.

<a name="screenshots" />

### <a name="taking-screenshots"></a>Captures d’écran

Vous pouvez prendre une capture d’écran du périphérique d’usure en entrant la commande suivante : 

```shell
adb -s 127.0.0.1:4444 shell screencap -p /sdcard/DCIM/screencap.png
```

Copiez la capture d’écran sur votre ordinateur en entrant la commande suivante :

```shell
adb -s 127.0.0.1:4444 pull /sdcard/DCIM/screencap.png
```

Supprimez la capture d’écran sur l’appareil en entrant la commande suivante :

```shell
adb -s 127.0.0.1:4444 shell rm /sdcard/DCIM/screencap.png
```

### <a name="uninstalling-an-app"></a>Désinstallation d’une application

Vous pouvez désinstaller une application à partir de l’appareil d’usure en entrant la commande suivante :

```shell
adb -s 127.0.0.1:4444 uninstall <package name>
```

Par exemple, pour supprimer l’application avec le nom du package `com.xamarin.weartest`, entrez la commande suivante :

```shell
adb -s 127.0.0.1:4444 uninstall com.xamarin.weartest
```

Pour plus d’informations sur le débogage des appareils Android usure sur Bluetooth, voir [débogage sur Bluetooth](https://developer.android.com/training/wearables/apps/bt-debugging.html).

## <a name="debugging-a-wear-app-with-a-companion-phone-app"></a>Débogage d’une application d’usure avec une application de téléphone auxiliaire

Les applications d’usure Android sont empaquetées avec une application de téléphone Android associée pour la distribution sur Google Play (pour plus d’informations, consultez [utilisation de l’empaquetage](~/android/wear/deploy-test/packaging.md)). Toutefois, vous continuez à développer l’application d’usure et son application auxiliaire séparément. Lorsque vous publiez votre application via le Google Play Store, l’application d’usure est empaquetée avec l’application auxiliaire et installée automatiquement si possible.

Pour déboguer l’application d’usure avec une application auxiliaire : 

1. Générez et déployez l’application auxiliaire sur le téléphone.

2. Cliquez avec le bouton droit sur le projet usure et définissez-le comme projet de démarrage par défaut.

3. Déployez le projet usure sur l’appareil portable.

4. Exécutez et déboguez l’application usure sur l’appareil.

## <a name="summary"></a>Résumé

Cet article a expliqué comment configurer un appareil Android d’usure pour l’usure du débogage à partir de Visual Studio via Bluetooth et comment déboguer une application d’usure avec une application de téléphone auxiliaire. Elle a également fourni des conseils de débogage courants pour déboguer une application d’usure via Bluetooth.
