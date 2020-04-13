---
title: Est-il possible de se connecter à des émulateurs Android exécutés sur un Mac à partir d’une machine virtuelle Windows ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7B6752BB-8E4C-4690-B275-7E425A051F45
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/21/2018
ms.openlocfilehash: 49d1eea60f766f4cb61484a6e441506cf8f046ff
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78292892"
---
# <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vm"></a>Est-il possible de se connecter à des émulateurs Android exécutés sur un Mac à partir d’une machine virtuelle Windows ?

Pour vous connecter à l’émulateur Android fonctionnant sur un Mac à partir d’une machine virtuelle Windows, utilisez les étapes suivantes :

1. Démarrez l’émulateur sur le Mac.

2. Tuer `adb` le serveur sur le Mac:

    ```bash
    adb kill-server
    ```

3. Notez que l’émulateur est à l’écoute sur 2 ports TCP sur l’interface réseau loopback:

    ```bash
    lsof -iTCP -sTCP:LISTEN -P | grep 'emulator\|qemu'

    emulator6 94105 macuser   20u  IPv4 0xa8dacfb1d4a1b51f      0t0  TCP localhost:5555 (LISTEN)
    emulator6 94105 macuser   21u  IPv4 0xa8dacfb1d845a51f      0t0  TCP localhost:5554 (LISTEN)
    ```

    Le port impair est celui utilisé pour `adb`se connecter à . Voir [https://developer.android.com/tools/devices/emulator.html#emulatornetworking](https://developer.android.com/tools/devices/emulator.html#emulatornetworking)aussi .

4. _Option 1_ `nc` : Utilisez pour transmettre les paquets TCP entrants reçus à l’extérieur sur le port 5555 (ou tout autre port que vous aimez) au port impair sur l’interface loopback **(127.0.0.1 5555** dans cet exemple), et pour transmettre les paquets sortants dans l’autre sens :

    ```bash
    cd /tmp
    mkfifo backpipe
    nc -kl 5555 0<backpipe | nc 127.0.0.1 5555 > backpipe
    ```

    Tant que `nc` les commandes resteront en cours d’exécution dans une fenêtre terminale, les paquets seront transmis comme prévu. Vous pouvez taper Control-C dans la `nc` fenêtre Terminal pour quitter les commandes une fois que vous avez terminé à l’aide de l’émulateur.

    (L’option 1 est généralement plus facile que l’option 2, surtout si **les préférences du système > sécurité & la protection de la vie privée > pare-feu** est activée.)

    _Option 2_ `pfctl` : Utilisez pour rediriger les paquets TCP depuis le port `5555` (ou tout autre port que vous aimez) sur l’interface de [réseautage partagé](https://kb.parallels.com/en/4948) vers le port impair sur l’interface loopback (dans`127.0.0.1:5555` cet exemple) :

    ```bash
    sed '/rdr-anchor/a rdr pass on vmnet8 inet proto tcp from any to any port 5555 -> 127.0.0.1 port 5555' /etc/pf.conf | sudo pfctl -ef -
    ```

    Cette commande met en place `pf packet filter` l’endage de port utilisant le service de système. Les sauts de ligne sont importants. Assurez-vous de les garder intacts lors de la copie-coller. Vous devrez également ajuster le nom de l’interface de *vmnet8* si vous utilisez Parallels. `vmnet8`est le nom de *l’appareil spécial NAT* pour le mode *réseautage partagé* dans VMWare Fusion. L’interface réseau appropriée dans Parallels est probablement [vnic0](https://download.parallels.com/doc/psbm/en/Parallels_Server_Bare_Metal_Users_Guide/29258.htm).

5. Connectez-vous à l’émulateur de la machine Windows :

    ```cmd
    C:\> adb connect ip-address-of-the-mac:5555
    ```

    Remplacer "ip-address-of-the-mac" avec l’adresse IP du Mac, par exemple comme indiqué par `ifconfig vmnet8 | grep 'inet '`. Si nécessaire, `5555` remplacez par l’autre port que vous aimez de l’étape 4\. (Remarque : une façon d’obtenir `adb` un accès en ligne de commande est via [**Outils > Android > Android Adb Command Prompt**](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat) in Visual Studio.)

### <a name="alternate-technique-using-ssh"></a>Technique alternative utilisant`ssh`

Si vous avez activé _Remote Login_ sur `ssh` le Mac, vous pouvez utiliser l’endage du port pour vous connecter à l’émulateur.

1. Installez un client SSH sur Windows. Une option est d’installer [Git pour Windows](https://git-for-windows.github.io/). La `ssh` commande sera alors disponible dans l’invite de commande **Git Bash.**

2. Suivez les étapes 1-3 d’en haut `adb` pour démarrer l’émulateur, tuer le serveur sur le Mac et identifier les ports d’émulateur.

3. Exécutez `ssh` windows pour configurer l’avant-port bidirectionnel entre un port local sur Windows (dans`localhost:15555` cet exemple) et`127.0.0.1:5555` le port d’émulateur impair numéroté sur l’interface en boucle du Mac (dans cet exemple):

    ```cmd
    C:\> ssh -L localhost:15555:127.0.0.1:5555 mac-username@ip-address-of-the-mac
    ```

    Remplacez-le par `mac-username` votre nom `whoami`d’utilisateur Mac tel que répertorié par . Remplacez-le par `ip-address-of-the-mac` l’adresse IP du Mac.

4. Connectez-vous à l’émulateur à l’aide du port local sur Windows :

    ```cmd
    C:\> adb connect localhost:15555
    ```

    (Remarque : un moyen facile d’obtenir un accès de commande-ligne à `adb` est via outils > Android > Android [ **Adb Command Prompt** in Visual Studio](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat).)

Une petite mise en `5555` garde: si `adb` vous utilisez le port pour le port local, pensera que l’émulateur fonctionne localement sur Windows. Cela ne cause aucun problème dans Visual Studio, mais dans Visual Studio pour Mac, il provoque la sortie de l’application immédiatement après le lancement.

### <a name="alternate-technique-using-adb--h-is-not-yet-supported"></a>La technique `adb -H` alternative utilisant n’est pas encore prise en charge

En théorie, une autre `adb`approche serait d’utiliser la `adb` capacité intégrée de se connecter [https://stackoverflow.com/a/18551325](https://stackoverflow.com/a/18551325)à un serveur fonctionnant sur une machine à distance (voir par exemple ).
Mais les extensions IDE Xamarin.Android ne fournissent pas actuellement un moyen de configurer cette option.

## <a name="contact-information"></a>Informations de contact

Ce document traite du comportement actuel en mars 2016. La technique décrite dans ce document ne fait pas partie de la suite d’essai stable pour Xamarin, de sorte qu’il pourrait se briser à l’avenir.

Si vous remarquez que la technique ne fonctionne plus, ou si vous remarquez d’autres erreurs [http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm](https://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm)dans le document, n’hésitez pas à ajouter à la discussion sur le fil de forum suivant: .
Merci !
