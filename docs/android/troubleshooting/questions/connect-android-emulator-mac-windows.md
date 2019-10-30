---
title: Est-il possible de se connecter à des émulateurs Android exécutés sur un Mac à partir d’une machine virtuelle Windows ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7B6752BB-8E4C-4690-B275-7E425A051F45
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/21/2018
ms.openlocfilehash: 2c1f571efb9ec3fb726912eb1e30496bc51fe26e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73026984"
---
# <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vm"></a>Est-il possible de se connecter à des émulateurs Android exécutés sur un Mac à partir d’une machine virtuelle Windows ?

Pour vous connecter au Émulateur Android s’exécutant sur un Mac à partir d’une machine virtuelle Windows, procédez comme suit :

1. Démarrez l’émulateur sur le Mac.

2. Arrêtez le serveur `adb` sur le Mac :

    ```bash
    adb kill-server
    ```

3. Notez que l’émulateur écoute sur 2 ports TCP sur l’interface réseau de bouclage :

    ```bash
    lsof -iTCP -sTCP:LISTEN -P | grep 'emulator\|qemu'

    emulator6 94105 macuser   20u  IPv4 0xa8dacfb1d4a1b51f      0t0  TCP localhost:5555 (LISTEN)
    emulator6 94105 macuser   21u  IPv4 0xa8dacfb1d845a51f      0t0  TCP localhost:5554 (LISTEN)
    ```

    Le port impair est celui utilisé pour se connecter à `adb`. Voir aussi [https://developer.android.com/tools/devices/emulator.html#emulatornetworking](https://developer.android.com/tools/devices/emulator.html#emulatornetworking).

4. _Option 1_: utiliser [`nc`](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/nc.1.html)
    pour transférer les paquets TCP entrants reçus en externe sur le port 5555 (ou tout autre port de votre choix) vers le port impair de l’interface de bouclage (**127.0.0.1 5555** dans cet exemple) et pour transférer les paquets sortants de l’autre façon :

    ```bash
    cd /tmp
    mkfifo backpipe
    nc -kl 5555 0<backpipe | nc 127.0.0.1 5555 > backpipe
    ```

    Tant que les commandes `nc` restent en cours d’exécution dans une fenêtre de terminal, les paquets sont transférés comme prévu. Vous pouvez taper le contrôle-C dans la fenêtre de terminal pour quitter les commandes `nc` une fois que vous avez fini d’utiliser l’émulateur.

    (L’option 1 est généralement plus facile que l’option 2, en particulier si les **Préférences système > la sécurité & confidentialité > pare-feu** est activé.) 

    _Option 2_: utiliser [`pfctl`](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man8/pfctl.8.html)
    pour rediriger les paquets TCP à partir du port `5555` (ou de tout autre port de votre choix) sur l’interface de [mise en réseau partagée](https://kb.parallels.com/en/4948) vers le port impair de l’interface de bouclage (`127.0.0.1:5555` dans cet exemple) :

    ```bash
    sed '/rdr-anchor/a rdr pass on vmnet8 inet proto tcp from any to any port 5555 -> 127.0.0.1 port 5555' /etc/pf.conf | sudo pfctl -ef -
    ```

    Cette commande configure le transfert de port à l’aide du service système `pf packet filter`. Les sauts de ligne sont importants. Veillez à les conserver intactes lorsque vous collez des copies. Vous devrez également ajuster le nom de l’interface à partir de *VMnet8* si vous utilisez des parallèles. `vmnet8` est le nom du *périphérique NAT* spécial pour le mode de *mise en réseau partagé* dans VMware Fusion. L’interface réseau appropriée en parallèle est probablement [vnic0](https://download.parallels.com/doc/psbm/en/Parallels_Server_Bare_Metal_Users_Guide/29258.htm).

5. Connectez-vous à l’émulateur à partir de l’ordinateur Windows :

    ```cmd
    C:\> adb connect ip-address-of-the-mac:5555
    ```

    Remplacez « IP-address-of-the-Mac » par l’adresse IP du Mac, par exemple, comme indiqué par `ifconfig vmnet8 | grep 'inet '`. Si nécessaire, remplacez `5555` par l’autre port que vous aimez de l’étape 4\. (Remarque : l’un des moyens d’accéder à `adb` via les [**outils > l’invite de commandes Android ADB android >** ](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat) dans Visual Studio.)

### <a name="alternate-technique-using-ssh"></a>Autre technique à l’aide de `ssh`

Si vous avez activé _session à distance_ sur le Mac, vous pouvez utiliser `ssh` le transfert de port pour vous connecter à l’émulateur.

1. Installez un client SSH sur Windows. L’une des options consiste à installer [git pour Windows](https://git-for-windows.github.io/). La commande `ssh` est ensuite disponible dans l’invite de commandes **git bash** .

2. Suivez les étapes 1-3 ci-dessus pour démarrer l’émulateur, arrêtez le serveur `adb` sur le Mac, puis identifiez les ports de l’émulateur.

3. Exécutez `ssh` sur Windows pour configurer le réacheminement de port bidirectionnel entre un port local sur Windows (`localhost:15555` dans cet exemple) et le port d’émulateur impair sur l’interface de bouclage du Mac (`127.0.0.1:5555` dans cet exemple) :

    ```cmd 
    C:\> ssh -L localhost:15555:127.0.0.1:5555 mac-username@ip-address-of-the-mac
    ```

    Remplacez `mac-username` par votre nom d’utilisateur Mac, comme indiqué par `whoami`. Remplacez `ip-address-of-the-mac` par l’adresse IP du Mac.

4. Connectez-vous à l’émulateur à l’aide du port local sur Windows :

    ```cmd
    C:\> adb connect localhost:15555
    ```

    (Remarque : un moyen simple d’accéder à `adb` à l’aide de la ligne de commande consiste à utiliser les [ **Outils > invite de commandes Android Adb Android >** dans Visual Studio](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat).)

Petite prudence : Si vous utilisez le port `5555` pour le port local, `adb` pensez que l’émulateur s’exécute localement sur Windows. Cela n’entraîne aucun problème dans Visual Studio, mais dans Visual Studio pour Mac, l’application se ferme immédiatement après son lancement.

### <a name="alternate-technique-using-adb--h-is-not-yet-supported"></a>Une autre technique utilisant `adb -H` n’est pas encore prise en charge

En théorie, une autre approche consiste à utiliser la fonctionnalité intégrée de `adb`pour se connecter à un serveur `adb` exécuté sur un ordinateur distant (voir par exemple [https://stackoverflow.com/a/18551325](https://stackoverflow.com/a/18551325)).
Toutefois, les extensions IDE Xamarin. Android ne fournissent pas actuellement un moyen de configurer cette option.

## <a name="contact-information"></a>Informations de contact

Ce document décrit le comportement actuel à compter du 2016 mars. La technique décrite dans ce document ne fait pas partie de la suite de tests stable pour Xamarin. elle pourrait donc s’arrêter à l’avenir.

Si vous remarquez que la technique ne fonctionne plus, ou si vous remarquez d’autres erreurs dans le document, n’hésitez pas à ajouter à la discussion sur le thème de Forum suivant : [http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm](https://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm).
Merci.
