---
title: Configurer un appareil pour le développement
description: Cet article explique comment configurer un appareil Android et le connecter à un ordinateur en vue de l’utiliser pour exécuter et déboguer des applications Xamarin.Android.
ms.prod: xamarin
ms.assetid: 9116A3AA-EA00-56AF-AE70-BAEEC045EF11
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/22/2018
ms.openlocfilehash: f5b92bc202cbf9765a63219ad653442152c3eb24
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73020717"
---
# <a name="set-up-device-for-development"></a>Configurer un appareil pour le développement

_Cet article explique comment configurer un appareil Android et le connecter à un ordinateur en vue de l’utiliser pour exécuter et déboguer des applications Xamarin.Android._

Après avoir effectué des tests sur un émulateur Android, vous voudrez voir comment vos applications s’exécutent sur un appareil Android. Voici les étapes à suivre pour connecter un appareil à un ordinateur pour le débogage :

1. **Activer le débogage sur l’appareil** : par défaut, il n’est pas possible de déboguer des applications sur un appareil Android.

2. **Installer les pilotes USB** : cette étape n’est pas nécessaire pour les ordinateurs macOS. Les ordinateurs Windows peuvent nécessiter l’installation de pilotes USB.

3. **Connecter l’appareil à l’ordinateur** : cette dernière étape consiste à connecter l’appareil à l’ordinateur à l’aide d’une connexion USB ou d’une connexion Wi-Fi.

Chacune de ces étapes est abordée plus en détail dans les sections ci-dessous.

## <a name="enable-debugging-on-the-device"></a>Activer le débogage sur l’appareil

Vous pouvez utiliser n’importe quel appareil Android pour tester les applications Android. Toutefois, l’appareil doit être correctement configuré pour permettre le débogage. Les étapes nécessaires peuvent varier légèrement, selon la version d’Android exécutée sur l’appareil.

### <a name="android-40-to-android-41"></a>Android 4.0 - Android 4.1

Pour activer le débogage sur les appareils exécutant Android 4.0.x à Android 4.1.x, effectuez les étapes suivantes :

1. Accédez à l’écran **Paramètres**.
2. Sélectionnez **Options développeurs**.
3. Désactivez l’option **Débogage USB**.

Cette capture d’écran montre l’écran **Options développeurs** sur un appareil exécutant Android 4.0.3 :

[![Options pour développeurs](set-up-device-for-development-images/developer-options-sml.png)](set-up-device-for-development-images/developer-options.png#lightbox)

### <a name="android-42-and-higher"></a>Android 4.2 et ultérieur

Dans Android 4.2 et ultérieur, l’écran **Options développeurs** est masqué par défaut. Pour l’afficher, accédez à **Paramètres > À propos du téléphone**, puis appuyez sept fois sur **Numéro de build** pour faire apparaître l’onglet **Options développeurs** :

[![Numéro de build](set-up-device-for-development-images/about-phone-sml.png)](set-up-device-for-development-images/about-phone.png#lightbox)

Une fois que l’onglet **Options développeurs**s’affiche sous **Paramètres > Système**, ouvrez-le pour afficher les paramètres développeurs :

[![Écran des paramètres développeurs](set-up-device-for-development-images/developer3.png)](set-up-device-for-development-images/developer3.png#lightbox)

C’est là que vous pouvez activer des options développeurs, telles que le débogage USB ou le mode Rester activé.

## <a name="install-usb-drivers"></a>Installer les pilotes USB

Cette étape n’est pas nécessaire pour les systèmes macOS. Il suffit de connecter l’appareil au Mac à l’aide d’un câble USB.

D’autres pilotes seront peut-être nécessaires pour que l’ordinateur Windows puisse reconnaître l’appareil Android connecté par un câble USB.

> [!NOTE]
> Les étapes qui suivent correspondent à la configuration d’un appareil Google Nexus et sont fournies à titre de référence. Les étapes peuvent varier en fonction de l’appareil, toutefois, la procédure reste essentiellement la même. Si vous rencontrez des problèmes, recherchez sur Internet la procédure propre à votre appareil.

Exécutez l’application **android.bat** dans le répertoire **[chemin d’installation du kit Android SDK]\tools**. Sur les ordinateurs Windows, le programme d’installation Xamarin.Android place Android SDK à l’endroit suivant, par défaut :

`C:\Users\[username]\AppData\Local\Android\android-sdk`

### <a name="download-the-usb-drivers"></a>Télécharger les pilotes USB

Les appareils Google Nexus (à l’exception du Galaxy Nexus) nécessitent le pilote Google USB. Le pilote dont vous avez besoin pour le Galaxy Nexus est [distribué par Samsung](https://www.samsung.com/us/support/downloads/).
Pour tous les autres appareils Android, vous devez utiliser le [pilote USB fourni par le fabricant](https://developer.android.com/tools/extras/oem-usb.html#Drivers).

Pour installer le paquet **Google USB Driver**, démarrez le Gestionnaire Android SDK, puis développez le dossier **Extras**, comme indiqué dans la capture d’écran suivante :

[![Sélection du paquet Google USB Driver](set-up-device-for-development-images/usbdriverpackage.png)](set-up-device-for-development-images/usbdriverpackage.png#lightbox)

Cochez la case **Google USB Driver**, puis cliquez sur le bouton **Install**.
Les fichiers du pilote sont téléchargés à l’emplacement suivant :

`[Android SDK install path]\extras\google\usb\_driver`

Le chemin par défaut de l’installation Xamarin.Android est le suivant :

`C:\Users\[username]\AppData\Local\Android\android-sdk\extras\google\usb_driver`

### <a name="installing-the-usb-driver"></a>Installation du pilote USB

Une fois les pilotes USB téléchargés, vous devez les installer.
Pour installer les pilotes sur Windows 7 :

1. Connectez votre appareil à l’ordinateur avec un câble USB.

2. Sur le Bureau ou dans l’Explorateur Windows, cliquez avec le bouton droit sur Ordinateur, puis sélectionnez **Gérer**.

3. Dans le volet gauche, sélectionnez **Appareils**.

4. Dans le volet droit, développez **Autres appareils**.

5. Cliquez avec le bouton droit sur le nom de l’appareil, puis sélectionnez **Mettre à jour le pilote**.
    Cette action lance l’Assistant Mise à jour du matériel.

6. Sélectionnez **Rechercher un pilote sur mon ordinateur**, puis cliquez sur **Suivant**.

7. Cliquez sur **Parcourir**, puis accédez au dossier du pilote USB (le pilote Google USB se trouve ici : **[chemin d’installation du kit Android SDK]\extras\google\usb_driver**).

8. Cliquez sur **Suivant** pour installer le pilote.

### <a name="installing-unverified-drivers-in-windows-8"></a>Installation de pilotes non vérifiés dans Windows 8

Des étapes supplémentaires peuvent être nécessaires pour installer un pilote non vérifié sous Windows 8. Les étapes suivantes permettent d’installer les pilotes nécessaires pour un appareil Galaxy Nexus :

1. **Accéder aux options de démarrage avancées de Windows 8** : Cette étape implique le redémarrage de l’ordinateur pour accéder aux options de démarrage avancées. Ouvrez l’invite de commandes, puis redémarrez l’ordinateur à l’aide de la commande suivante :

    ```command
    shutdown.exe /r /o
    ```

2. **Connecter l’appareil** : Connectez l’appareil à l’ordinateur.

3. **Démarrer le Gestionnaire de périphériques** : Exécutez **devmgmt.msc**. Un appareil doit s’afficher, avec un triangle jaune au-dessus de lui.

4. **Installer les pilotes d’appareil** : installez les pilotes d’appareil, comme décrit ci-dessus.

## <a name="connect-the-device-to-the-computer"></a>Connecter l’appareil à l’ordinateur

La dernière étape consiste à connecter l’appareil à l’ordinateur. Pour cela, il existe deux méthodes :

- **Câble USB** : Il s’agit de la méthode la plus simple et la plus courante. Il vous suffit de brancher un câble USB entre l’appareil et l’ordinateur.

- **Wi-Fi** : Vous pouvez connecter un appareil Android à un ordinateur sans l’aide d’un câble USB, en utilisant une connexion Wi-Fi. Cette technique nécessite un peu plus d’efforts, mais elle peut être utile quand l’appareil est trop loin de l’ordinateur pour être constamment branché via un câble. La connexion via le Wi-Fi est abordée dans la section suivante.

### <a name="connecting-over-wifi"></a>Connexion via le Wi-Fi

Par défaut, [Android Debug Bridge](https://developer.android.com/tools/help/adb.html) (*ADB*) est configuré pour communiquer avec les appareils Android via une connexion USB. Il est possible de le reconfigurer pour qu’il utilise le protocole TCP/IP au lieu d’une connexion USB. Pour ce faire, l’appareil et l’ordinateur doivent utiliser le même réseau Wi-Fi. Pour configurer votre environnement de manière à permettre le débogage via une connexion Wi-Fi, procédez comme suit à partir de la ligne de commande :

1. Déterminez l’adresse IP de votre appareil Android. L’une des méthodes possibles pour déterminer l’adresse IP de l’appareil est de regarder sous **Paramètres > Wi-Fi**, et d’appuyer sur le réseau Wi-Fi auquel est connecté l’appareil. L’écran Paramètres s’ouvre et affiche des informations sur la connexion réseau, comme dans la capture d’écran ci-dessous :

    ![Adresse IP](set-up-device-for-development-images/ip-settings.png)

    Dans certaines versions Android, l’adresse IP est indiquée ailleurs, sous **Paramètres > À propos du téléphone > État**.

2. Connectez votre appareil Android à votre ordinateur avec un câble USB.

3. Ensuite, redémarrez ADB pour qu’il utilise le protocole TCP sur le port 5555. Dans l’invite de commandes, tapez la commande suivante :

    ```command
    adb tcpip 5555
    ```

    Une fois la commande exécutée, votre ordinateur ne pourra plus écouter les appareils qui sont connectés via USB.

4. Déconnectez le câble USB qui relie votre appareil à votre ordinateur.

5. Configurez ADB pour qu’il se connecte à votre appareil Android via le port spécifié à l’étape 1 :

    ```command
    adb connect 192.168.1.28:5555
    ```

    Une fois cette commande exécutée, l’appareil Android est connecté à l’ordinateur via le Wi-Fi.

Lorsque vous avez terminé le débogage via la connexion Wi-Fi, vous pouvez réinitialiser ADB pour qu’il réutilise l’option USB par défaut. Pour cela, exécutez la commande suivante :

```command
adb usb
```

Vous pouvez demander à ADB de répertorier les appareils qui sont connectés à l’ordinateur. Quelle que soit la façon dont les appareils sont connectés, vous pouvez exécuter la commande suivante à l’invite de commandes, pour voir tous les appareils connectés :

```command
adb devices
```

## <a name="summary"></a>Récapitulatif

Vous venez de voir comment configurer un appareil Android pour le développement, en activant le débogage sur celui-ci. Vous avez également vu comment connecter l’appareil à un ordinateur à l’aide d’une connexion USB ou Wi-Fi.

## <a name="related-links"></a>Liens associés

- [Android Debug Bridge](https://developer.android.com/tools/help/adb.html)
- [Utilisation des périphériques matériels](https://developer.android.com/tools/device.html)
- [Téléchargements des pilotes Samsung](https://www.samsung.com/us/support/downloads/)
- [Pilotes USB OEM](https://developer.android.com/tools/extras/oem-usb.html#Drivers)
- [Pilote USB Google](https://developer.android.com/sdk/win-usb.html)
- [Développeurs XDA : Windows 8 - Problème de pilote ADB/Fastboot résolu](https://forum.xda-developers.com/showthread.php?t=1583801)
