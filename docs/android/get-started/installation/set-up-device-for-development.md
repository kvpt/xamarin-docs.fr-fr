---
title: Configurer un appareil pour le développement
description: Cet article explique comment configurer un appareil Android et le connecter à un ordinateur en vue de l’utiliser pour exécuter et déboguer des applications Xamarin.Android.
ms.prod: xamarin
ms.assetid: 9116A3AA-EA00-56AF-AE70-BAEEC045EF11
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/22/2018
ms.openlocfilehash: 0b0bfc650ffa271a7616d7c6e6a436fafa2664c8
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86932598"
---
# <a name="set-up-device-for-development"></a>Configurer un appareil pour le développement

_Cet article explique comment configurer un appareil Android et le connecter à un ordinateur en vue de l’utiliser pour exécuter et déboguer des applications Xamarin.Android._

Après avoir effectué des tests sur un émulateur Android, vous voudrez voir comment vos applications s’exécutent sur un appareil Android. Vous devez activer le débogage et connecter l’appareil à l’ordinateur.

Chacune de ces étapes est abordée plus en détail dans les sections ci-dessous.

## <a name="enable-debugging-on-the-device"></a>Activer le débogage sur l’appareil

Pour pouvoir tester une application Android, vous devez activer un appareil pour le débogage. Les options de développeur sur Android ont été masquées par défaut depuis la version 4,2, et leur activation peut varier en fonction de la version d’Android.

### <a name="android-90"></a>Android 9.0 +

Pour Android 9,0 et versions ultérieures, le débogage est activé en procédant comme suit :

1. Accédez à l’écran **Paramètres**.
2. Sélectionnez **à propos de téléphone** .
3. Appuyez sur **numéro de build** 7 fois jusqu’à ce que **vous soyez maintenant développeur !** est visible.

### <a name="android-80-and-android-81"></a>Android 8,0 et Android 8,1

1. Accédez à l’écran **paramètres** .
2. Sélectionnez **système**.
3. Sélectionner **à propos du téléphone**
4. Appuyez sur **numéro de build** 7 fois jusqu’à ce que **vous soyez maintenant développeur !** est visible.

### <a name="android-71-and-lower"></a>Android 7,1 et versions antérieures

1. Accédez à l’écran **paramètres** .
2. Sélectionnez **à propos de téléphone**.
3. Appuyez sur **numéro de build** 7 fois jusqu’à ce que **vous soyez maintenant développeur !** est visible.

[![Écran des options de développeur sur Android 9,0](set-up-device-for-development-images/build-version-sml.png)](set-up-device-for-development-images/build-version.png#lightbox)

### <a name="verify-that-usb-debugging-is-enabled"></a>Vérifier que le débogage USB est activé

Après avoir activé le mode développeur sur votre appareil, vous devez vous assurer que le débogage USB est activé sur l’appareil. Cela varie également en fonction de la version d’Android.

### <a name="android-90"></a>Android 9.0 +

Accédez à **paramètres > système > avancé > options du développeur** et activez le **débogage USB**.

### <a name="android-80-and-android-81"></a>Android 8,0 et Android 8,1

Accédez à **paramètres > les options de développeur système >** et activez le **débogage USB**.

### <a name="android-71-and-lower"></a>Android 7,1 et versions antérieures

Accédez à **paramètres > options de développement** et activez le **débogage USB**.

Une fois que l’onglet **Options développeurs**s’affiche sous **Paramètres > Système**, ouvrez-le pour afficher les paramètres développeurs :

[![Écran des options de développeur sur Android 9,0](set-up-device-for-development-images/usb-debugging-sml.png)](set-up-device-for-development-images/usb-debugging.png#lightbox)

C’est là que vous pouvez activer des options développeurs, telles que le débogage USB ou le mode Rester activé.

## <a name="connect-the-device-to-the-computer"></a>Connecter l’appareil à l’ordinateur

La dernière étape consiste à connecter l’appareil à l’ordinateur. La méthode la plus simple et la plus fiable consiste à le faire sur USB.

Vous recevrez une invite pour faire confiance à l’ordinateur sur votre appareil si vous ne l’avez pas utilisé pour le débogage. Vous pouvez également cocher la case **toujours autoriser à partir de cet ordinateur** pour empêcher l’exécution de cette invite chaque fois que vous connectez l’appareil.

![Google USB](set-up-device-for-development-images/trust-computer-for-usb-debugging.png)

## <a name="alternate-connection-via-wifi"></a>Autre connexion via WiFi

Il est possible de connecter un appareil Android à un ordinateur sans utiliser de câble USB, par le biais du Wi-Fi. Cette technique nécessite plus d’efforts, mais elle peut être utile lorsque l’appareil est trop éloigné de l’ordinateur pour rester connecté en permanence via un câble. 

### <a name="connecting-over-wifi"></a>Connexion via le Wi-Fi

Par défaut, [Android Debug Bridge](https://developer.android.com/tools/help/adb.html) (*ADB*) est configuré pour communiquer avec les appareils Android via une connexion USB. Il est possible de le reconfigurer pour qu’il utilise le protocole TCP/IP au lieu d’une connexion USB. Pour ce faire, l’appareil et l’ordinateur doivent utiliser le même réseau Wi-Fi. Pour configurer votre environnement pour déboguer sur WiFi, effectuez les étapes suivantes à partir de la ligne de commande :

1. Déterminez l’adresse IP de votre appareil Android. Pour déterminer l’adresse IP, vous pouvez consulter les **paramètres > réseau & internet > Wi-Fi**, puis appuyer sur le réseau Wi-Fi auquel l’appareil est connecté, puis appuyer sur **avancé**. Cela ouvre une liste déroulante contenant des informations sur la connexion réseau, comme dans la capture d’écran ci-dessous :

    [![Adresse IP](set-up-device-for-development-images/ip-settings-sml.png)](set-up-device-for-development-images/ip-settings.png#lightbox)

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

    Une fois cette commande terminée, l’appareil Android est connecté à l’ordinateur via WiFi.

    Lorsque vous avez terminé le débogage via WiFi, il est possible de réinitialiser ADB en mode USB à l’aide de la commande suivante :
    
    ```command
    adb usb
    ```
    
    Il est possible de demander à ADB de répertorier les périphériques qui sont connectés à l’ordinateur. Quelle que soit la façon dont les appareils sont connectés, vous pouvez exécuter la commande suivante à l’invite de commandes, pour voir tous les appareils connectés :
    
    ```command
    adb devices
    ```

## <a name="troubleshooting"></a>Dépannage

Dans certains cas, vous pouvez constater que votre appareil ne peut pas se connecter à l’ordinateur. Dans ce cas, vous souhaiterez peut-être vérifier que les pilotes USB sont installés.

## <a name="install-usb-drivers"></a>Installer les pilotes USB

Cette étape n’est pas nécessaire pour macOS. Connectez simplement l’appareil au Mac à l’aide d’un câble USB.

D’autres pilotes seront peut-être nécessaires pour que l’ordinateur Windows puisse reconnaître l’appareil Android connecté par un câble USB.

> [!NOTE]
> Les étapes qui suivent correspondent à la configuration d’un appareil Google Nexus et sont fournies à titre de référence. Les étapes peuvent varier en fonction de l’appareil, toutefois, la procédure reste essentiellement la même. Si vous rencontrez des problèmes, recherchez sur Internet la procédure propre à votre appareil.

Exécutez l’application **android.bat** dans le répertoire **[chemin d’installation du kit Android SDK]\tools**. Sur les ordinateurs Windows, le programme d’installation Xamarin.Android place Android SDK à l’endroit suivant, par défaut :

`C:\Users\[username]\AppData\Local\Android\android-sdk`

### <a name="download-the-usb-drivers"></a>Télécharger les pilotes USB

Les appareils Google Nexus (à l’exception du Galaxy Nexus) nécessitent le pilote Google USB. Le pilote dont vous avez besoin pour le Galaxy Nexus est [distribué par Samsung](https://www.samsung.com/us/support/downloads/).
Pour tous les autres appareils Android, vous devez utiliser le [pilote USB fourni par le fabricant](https://developer.android.com/tools/extras/oem-usb.html#Drivers).

Pour installer le paquet **Google USB Driver**, démarrez le Gestionnaire Android SDK, puis développez le dossier **Extras**, comme indiqué dans la capture d’écran suivante :

![Pilote USB Google sélectionné](set-up-device-for-development-images/google-usb-driver.png)

Cochez la case **pilote USB Google** , puis cliquez sur le bouton **appliquer les modifications** .
Les fichiers du pilote sont téléchargés à l’emplacement suivant :

`[Android SDK install path]\extras\google\usb\_driver`

Le chemin par défaut de l’installation Xamarin.Android est le suivant :

`C:\Users\[username]\AppData\Local\Android\android-sdk\extras\google\usb_driver`

### <a name="installing-the-usb-driver"></a>Installation du pilote USB

Une fois les pilotes USB téléchargés, vous devez les installer.
Pour installer les pilotes sur Windows 7 :

1. Connectez votre appareil à l’ordinateur avec un câble USB.

2. Sur le Bureau ou dans l’Explorateur Windows, cliquez avec le bouton droit sur Ordinateur, puis sélectionnez **Gérer**.

3. Sélectionnez **Appareils** dans le panneau de gauche.

4. Dans le volet droit, développez **Autres appareils**.

5. Cliquez avec le bouton droit sur le nom de l’appareil, puis sélectionnez **Mettre à jour le pilote**.
    Cette action lance l’Assistant Mise à jour du matériel.

6. Sélectionnez **Rechercher un pilote sur mon ordinateur**, puis cliquez sur **Suivant**.

7. Cliquez sur **Parcourir**, puis accédez au dossier du pilote USB (le pilote Google USB se trouve ici : **[chemin d’installation du kit Android SDK]\extras\google\usb_driver**).

8. Cliquez sur **Suivant** pour installer le pilote.

## <a name="summary"></a>Résumé

Vous venez de voir comment configurer un appareil Android pour le développement, en activant le débogage sur celui-ci. Vous avez également vu comment connecter l’appareil à un ordinateur à l’aide d’une connexion USB ou Wi-Fi.

## <a name="related-links"></a>Liens associés

- [Android Debug Bridge](https://developer.android.com/tools/help/adb.html)
- [Utilisation des périphériques matériels](https://developer.android.com/tools/device.html)
- [Téléchargements des pilotes Samsung](https://www.samsung.com/us/support/downloads/)
- [Pilotes USB OEM](https://developer.android.com/tools/extras/oem-usb.html#Drivers)
- [Pilote USB Google](https://developer.android.com/sdk/win-usb.html)
- [Développeurs XDA : Windows 8 - Problème de pilote ADB/Fastboot résolu](https://forum.xda-developers.com/showthread.php?t=1583801)
