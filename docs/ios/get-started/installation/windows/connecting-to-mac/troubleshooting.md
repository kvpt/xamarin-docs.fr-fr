---
title: Résolution des problèmes de connexion pour un hôte de build Xamarin.iOS
description: Ce guide fournit des étapes de résolution des problèmes qui peuvent être rencontrés lors de l’utilisation du nouveau gestionnaire de connexions, notamment les problèmes de connectivité et SSH.
ms.prod: xamarin
ms.assetid: A1508A15-1997-4562-B537-E4A9F3DD1F06
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/19/2017
ms.openlocfilehash: 41a5a30cdd5017046415f1d678abf1d44e83e678
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70753674"
---
# <a name="connection-troubleshooting-for-a-xamarinios-build-host"></a>Résolution des problèmes de connexion pour un hôte de build Xamarin.iOS

_Ce guide fournit des étapes de résolution des problèmes qui peuvent être rencontrés au cours de l’utilisation du nouveau gestionnaire de connexions, notamment les problèmes de connectivité et SSH._

## <a name="log-file-location"></a>Emplacement des fichiers journaux

- **Mac** : ~/Library/Logs/Xamarin-[MAJOR.MINOR]
- **Windows** : %LOCALAPPDATA%\Xamarin\Logs

Les fichiers journaux peuvent être localisés en accédant à **Aide &gt; Xamarin &gt; Journaux Zip** dans Visual Studio.

## <a name="wheres-the-xamarin-build-host-app"></a>Où se trouve l’application hôte de build Xamarin ?

L’hôte de build Xamarin des versions antérieures de Xamarin.iOS n’est plus nécessaire. Visual Studio déploie maintenant automatiquement l’agent sur une session à distance et l’exécute en arrière-plan. Aucune application supplémentaire n’est exécutée sur le Mac ou les ordinateurs Windows.

## <a name="troubleshooting-remote-login"></a>Résolution des problèmes de sessions à distance

> [!IMPORTANT]
> Ces étapes de résolution de problèmes s’appliquent principalement aux problèmes qui se produisent pendant la configuration initiale sur un nouveau système.  Si vous avez déjà utilisé la connexion avec succès dans un environnement particulier et que celle-ci ne fonctionne plus soudainement ou par intermittence, vous pouvez, dans la plupart des cas, passer directement à vérifier si l’un des points suivants peut vous aider à résoudre le problème : 
> - Arrêtez les processus restants comme décrit ci-dessous dans la section [Erreurs dues à des processus hôtes de build existants](#errors). 
> - Désactivez les agents, comme indiqué dans la section [Désactivation des agents Broker, IDB, de build et Designer](#clearing), puis utilisez une connexion Internet câblée et connectez-vous directement via l’adresse IP, comme indiqué dans la section [Connexion impossible à MacBuildHost.local. Veuillez réessayer.](#tryagain).  
> Si aucune de ces options ne résout le problème, suivez les instructions de l’[étape 9](#stepnine) pour envoyer un nouveau rapport de bogues.

1. Vérifiez que des versions compatibles de Xamarin.iOS sont installées sur votre Mac. Pour effectuer cette opération avec Visual Studio 2017, vérifiez que vous vous trouvez sur le canal de distribution **Stable** dans Visual Studio pour Mac. Dans Visual Studio 2015 et les versions antérieures, vérifiez que vous vous trouvez sur le même canal de distribution sur les deux IDE.
    - Dans Visual Studio pour Mac, accédez à **Visual Studio pour Mac > Rechercher les mises à jour...** pour afficher ou changer le **Canal de mise à jour**.
    - Dans Visual Studio 2015 et versions antérieures, vérifiez le canal de distribution sous **Outils > Options > Xamarin > Autre**.

2. Vérifiez que l’option **Session à distante** est activée sur le Mac. Choisissez **Uniquement ces utilisateurs** comme type d’accès, puis vérifiez que l’utilisateur de votre Mac est inclus dans la liste ou le groupe :

    [![](troubleshooting-images/troubleshooting-image1.png "Définir l’accès pour Uniquement ces utilisateurs")](troubleshooting-images/troubleshooting-image1.png#lightbox)

3. Vérifiez que votre pare-feu autorise les connexions entrantes via le port 22 (valeur par défaut pour SSH) :

    [![](troubleshooting-images/troubleshooting-image2.png "Vérifier que le pare-feu autorise les connexions entrantes via le port 22")](troubleshooting-images/troubleshooting-image2.png#lightbox)

    Si vous avez désactivé **Autoriser automatiquement les logiciels signés à recevoir des connexions entrantes**, OS X affiche une boîte de dialogue pendant le processus de couplage, demandant d’autoriser `mono-sgen` ou `mono-sgen32` à recevoir des connexions entrantes. Cliquez sur **Autoriser** dans cette boîte de dialogue :

    [![](troubleshooting-images/troubleshooting-image4a.png "Cliquer sur Autoriser dans cette boîte de dialogue")](troubleshooting-images/troubleshooting-image4a.png#lightbox)

4. Vérifiez que vous êtes connecté au compte d’utilisateur sur ce Mac et que vous avez une session d’interface graphique utilisateur active.

5. Veillez à vous connecter au Mac avec le _nom d’utilisateur_ plutôt qu’avec le _nom complet_. Cela évite une limitation connue relative aux noms complets incluant des caractères accentués.

    Pour trouver votre _nom d’utilisateur_, exécutez la commande `whoami` dans **Terminal.app**.

    Par exemple, dans la capture d’écran ci-dessous, le nom du compte sera **amyb** et non **Amy Burns** :

    [![](troubleshooting-images/troubleshooting-image5a.png "Obtenir le nom du compte à partir de l’application Terminal")](troubleshooting-images/troubleshooting-image5a.png#lightbox)

6. Vérifiez que l’adresse IP que vous utilisez pour le Mac est correcte. Vous trouverez l’adresse IP sous **Préférences Système > Partage > Session à distance** sur le Mac.

    [![](troubleshooting-images/troubleshooting-image17.png "Adresse IP du Mac dans l’application Préférences système")](troubleshooting-images/troubleshooting-image17.png#lightbox)

7. Une fois que vous avez confirmé l’adresse IP du Mac, effectuez un test `ping` à cette adresse dans `cmd.exe` sur Windows :

    ```
    ping 10.1.8.95
    ```
    
    Si le test ping échoue, le Mac n’est pas _routable_ à partir de l’ordinateur Windows. Ce problème doit être résolu au niveau de la configuration du réseau local entre les deux ordinateurs. Vérifiez que les deux ordinateurs se trouvent sur le même réseau local.

8. Effectuez ensuite un test pour savoir si le client `ssh` d’OpenSSH peut se connecter au Mac à partir de Windows. Une façon d’installer ce programme consiste à installer [Git pour Windows](https://git-for-windows.github.io/). Vous pouvez ensuite démarrer une invite de commandes **Git Bash** et essayer une commande `ssh` sur le Mac avec votre nom d’utilisateur et votre adresse IP :

    ```bash
    ssh amyb@10.1.8.95
    ```

    <a name="stepnine" />

9. Si l’**étape 8 réussit**, vous pouvez essayer d’exécuter une commande simple comme `ls` via la connexion :

    ```bash
    ssh amyb@10.1.8.95 'ls'
    ```
    
    Cette opération permet de répertorier le contenu de votre répertoire de base sur le Mac. Si la commande `ls` fonctionne correctement, mais que la connexion Visual Studio échoue toujours, vous pouvez vérifier la section [Problèmes connus et limitations](#knownissues) relative aux complications spécifiques à Xamarin. Si votre problème n’y figure pas, veuillez soumettre un nouveau rapport de bogue dans la Communauté de développeurs en accédant à **Aide > Envoyer des commentaires > Signaler un problème** dans Visual Studio et joindre les journaux décrits sous [Consulter les fichiers journaux détaillés](#verboselogs).

10. Si l’**étape 8 échoue**, vous pouvez exécuter la commande suivante dans Terminal sur le Mac pour voir si le serveur SSH accepte _toutes_ les connexions :

    ```bash
    ssh localhost
    ```
    
11. Si l’étape 8 échoue mais que l’**étape 10 réussit**, le problème est probablement que le port 22 sur l’hôte de build Mac n’est pas accessible à partir de Windows en raison de la configuration du réseau. Les problèmes de configuration possibles sont les suivants :

    - Les paramètres de pare-feu OS X interdisent la connexion. Vérifier soigneusement l’étape 3.

        Parfois, la configuration par application pour le pare-feu OS X peut également finir dans un état non valide dans lequel les paramètres affichés dans les Préférences Système ne reflètent pas le comportement réel. La suppression du fichier de configuration ( **/Library/Preferences/com.apple.alf.plist**) et le redémarrage de l’ordinateur peuvent permettre de restaurer le comportement par défaut. Une façon de supprimer le fichier consiste à entrer **/Library/Preferences** sous **Atteindre &gt;Atteindre le dossier** dans Finder, puis à déplacer le fichier **com.apple.alf.plist** vers la Corbeille.

    - Les paramètres de pare-feu de l’un des routeurs entre le Mac et l’ordinateur Windows bloquent la connexion.

    - Windows lui-même interdit les connexions sortantes vers le port distant 22. Cela serait inhabituel. Il est possible de configurer le Pare-feu Windows pour interdire les connexions sortantes, mais le paramètre par défaut consiste à autoriser toutes les connexions sortantes.

    - L’hôte de build Mac interdit l’accès au port 22 à partir de tous les hôtes externes à l’aide d’une règle `pfctl`. Cela est peu probable, sauf si vous savez que vous avez configuré `pfctl` dans le passé.

12. Si l’étape 8 échoue et que l’**étape 10 échoue** également, il est probable que le processus du serveur SSH sur le Mac n’est pas en cours d’exécution ou n’est pas configuré pour autoriser l’utilisateur actuel à se connecter. Dans ce cas, veillez à vérifier soigneusement les paramètres de session à distance définis à l’étape 2 avant d’examiner des possibilités plus complexes.

<a name="knownissues" />

### <a name="known-issues-and-limitations"></a>Problèmes connus et limitations

> [!NOTE]
> Cette section s’applique uniquement si vous vous êtes déjà connecté avec succès à l’hôte de build Mac avec votre nom d’utilisateur et mot de passe Mac à l’aide du client SSH OpenSSH, comme indiqué aux étapes 8 et 9 ci-dessus.

#### <a name="invalid-credentials-please-try-again"></a>« Informations d’identification non valides. Réessayez. »

Causes connues :

- **Limitation** : Cette erreur peut survenir lors d’une tentative de connexion à l’hôte de build à l’aide du _nom complet_ du compte si ce nom comprend un caractère accentué. Il s’agit d’une limitation de la [bibliothèque SSH.NET](https://sshnet.codeplex.com/) que Xamarin utilise pour la connexion SSH. **Solution de contournement** : Consultez l’étape 5 ci-dessus.

#### <a name="unable-to-authenticate-with-ssh-keys-please-try-to-log-in-with-credentials-first"></a>« Authentification impossible avec des clés SSH. Essayez d’abord de vous connecter avec des informations d’identification »

Cause connue :

- **Restriction de sécurité SSH** : Ce message signifie généralement que l’un des fichiers ou répertoires dans le chemin d’accès complet **$HOME/.ssh/authorized\_keys** sur le Mac dispose d’autorisations d’écriture activées pour d’_autres_ membres ou des membres du _groupe_. **Correctif courant** : Exécutez `chmod og-w "$HOME"` dans une invite de commandes de Terminal sur le Mac. Pour plus d’informations sur le fichier ou répertoire particulier à l’origine du problème, exécutez `grep sshd /var/log/system.log > "$HOME/Desktop/sshd.log"` dans Terminal, puis ouvrez le fichier **sshd.log** à partir de votre bureau et recherchez « Authentication refused: bad ownership or modes » (Authentification refusée : propriété ou modes incorrects).

#### <a name="trying-to-connect-never-completes"></a>« Tentative de connexion... » ne s’arrête jamais

- **Bogue [n° 52264](https://bugzilla.xamarin.com/show_bug.cgi?id=52264)**  : Ce problème peut se produire sur Xamarin 4.1 si l’**environnement de démarrage** dans le menu contextuel **Options avancées** de l’utilisateur Mac dans **Préférences Système &gt; Utilisateurs et groupes** est défini sur une valeur autre que **/bin/bash**. (À compter de Xamarin 4.2, ce scénario entraîne le message d’erreur « Connexion impossible ».) **Solution de contournement** : Rétablissez l’**environnement de démarrage** à la valeur par défaut d’origine **/bin/bash**.

<a name="tryagain" />

#### <a name="couldnt-connect-to-macbuildhostlocal-please-try-again"></a>« Connexion impossible à MacBuildHost.local. Réessayez. »

Causes signalées :

- **Bogue** : Certains utilisateurs ont vu, avec une erreur plus détaillée dans les fichiers journaux, le message d’erreur « Une erreur inattendue s’est produite pendant la configuration de SSH pour l’utilisateur... L’opération de session a expiré » lors d’une tentative de connexion à l’hôte de build à l’aide d’Active Directory ou d’un autre compte d’utilisateur de domaine de service d’annuaire. **Solution de contournement :** Connectez-vous à l’hôte de build à l’aide d’un compte d’utilisateur local.

- **Bogue** : Certains utilisateurs ont vu cette erreur lors d’une tentative de connexion à l’hôte de build en double-cliquant sur le nom du Mac dans la boîte de dialogue de connexion. **Solution de contournement possible** : [Ajoutez manuellement le Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md#manually-add-a-mac) à l’aide de l’adresse IP.

- **Bogue [n° 35971](https://bugzilla.xamarin.com/show_bug.cgi?id=35971)**  : Certains utilisateurs ont rencontré cette erreur lors de l’utilisation d’une connexion réseau sans fil entre l’hôte de build Mac et Windows. **Solution de contournement possible** : Reliez les deux ordinateurs à une connexion réseau câblée.

- **Bogue [n° 36642](https://bugzilla.xamarin.com/show_bug.cgi?id=36642)**  : Sur Xamarin 4.0, ce message s’affiche chaque fois que le fichier **$HOME/.bashrc** sur le Mac contient une erreur. (À compter de Xamarin 4.1, les erreurs contenues dans le fichier **.bashrc** n’affectent plus le processus de connexion.) **Solution de contournement** : Déplacez le fichier **.bashrc** vers un emplacement de sauvegarde (ou supprimez-le si vous savez qu’il est inutile).

- **Bogue [n° 52264](https://bugzilla.xamarin.com/show_bug.cgi?id=52264)**  : Cette erreur peut survenir si l’**environnement de démarrage** dans le menu contextuel **Options avancées** de l’utilisateur Mac dans **Préférences Système > Utilisateurs et groupes** est défini sur une valeur autre que **/bin/bash**. **Solution de contournement** : Rétablissez l’**environnement de démarrage** à la valeur par défaut d’origine **/bin/bash**.

- **Limitation** : Cette erreur peut survenir si l’hôte de build Mac est connecté à un routeur qui n’a pas accès à Internet (ou si le Mac utilise un serveur DNS qui expire quand la recherche DNS inversée de l’ordinateur Windows est demandée). Visual Studio met environ 30 secondes pour récupérer l’empreinte digitale SSH et risque de ne pas parvenir à se connecter.

    **Solution de contournement possible** : Ajoutez « UseDNS no » au fichier **sshd\_config**. Veillez à lire les informations relatives à ce paramètre SSH avant de le modifier. Par exemple, consultez [unix.stackexchange.com/questions/56941/what-is-the-point-of-sshd-usedns-option](http://unix.stackexchange.com/questions/56941/what-is-the-point-of-sshd-usedns-option).

    Les étapes suivantes décrivent une façon de changer le paramètre. Vous devez être connecté à un compte administrateur sur le Mac pour les effectuer.

    1. Vérifier l’emplacement du fichier **sshd\_config** en exécutant `ls /etc/ssh/sshd_config` et `ls /etc/sshd_config` dans une invite de commandes Terminal. Pour toutes les étapes restantes, veillez à utiliser l’emplacement qui ne retourne _pas_ « Aucun fichier ou répertoire de ce type ».

        [![](troubleshooting-images/troubleshooting-image18.png "Exécution de « ls /etc/ssh/sshd_config » et de « ls /etc/sshd_config » dans le Terminal")](troubleshooting-images/troubleshooting-image18.png#lightbox)

    2. Exécutez `cp /etc/ssh/sshd_config "$HOME/Desktop/"` dans Terminal pour copier le fichier sur votre bureau.

    3. Ouvrez le fichier à partir de votre bureau dans un éditeur de texte. Par exemple, vous pouvez exécuter `open -a TextEdit "$HOME/Desktop/sshd_config"` dans Terminal.

    4. Ajoutez la ligne suivante au bas du fichier :

        ```
        UseDNS no
        ```

    5. Supprimez toutes les lignes qui indiquent `UseDNS yes` pour garantir que le nouveau paramètre prend effet.

    6. Enregistrez le fichier.

    7. Exécutez `sudo cp "$HOME/Desktop/sshd_config" /etc/ssh/sshd_config` dans Terminal pour copier le fichier modifié à son emplacement d’origine. Entrez votre mot de passe si vous y êtes invité.

    8. Désactivez et réactivez la **Session à distance** sous **Préférences Système &gt; Partage &gt; Session à distance** pour redémarrer le serveur SSH.

<a name="clearing" />

#### <a name="clearing-the-broker-idb-build-and-designer-agents-on-the-mac"></a>Désactivation des agents Broker, IDB, de build et Designer sur le Mac

Si vos fichiers journaux indiquent un problème lors des étapes « Installation », « Chargement » ou « Démarrage » pour n’importe lequel des agents de Mac, essayez de supprimer le dossier de cache **XMA** pour forcer Visual Studio à les charger à nouveau.

1. Exécutez la commande suivante dans Terminal sur le Mac :

    ```bash
    open "$HOME/Library/Caches/Xamarin"
    ```
    
2. Cliquez sur le dossier **XMA** en maintenant la touche Ctrl enfoncée, puis sélectionnez **Placer dans la Corbeille** :

    [![](troubleshooting-images/troubleshooting-image8.png "Placer le dossier XMA dans la Corbeille")](troubleshooting-images/troubleshooting-image8.png#lightbox)

3. Il existe également un cache sur Windows qu’il peut être efficace de désactiver. Ouvrez une invite de commandes en tant qu’administrateur sur Windows :

    ```
    del %localappdata%\Temp\Xamarin\XMA
    ```
    
### <a name="warning-messages"></a>Messages d’avertissement

Cette section décrit quelques messages qui peuvent s’afficher dans les fenêtres et journaux de Sortie et que vous pouvez généralement ignorer.

#### <a name="there-is-a-mismatch-between-the-installed-xamarinios--and-the-local-xamarinios"></a>« Il existe une incompatibilité entre la version installée de Xamarin.iOS ... et la version locale de Xamarin.iOS »

À partir du moment où vous avez vérifié que Mac et Windows sont tous les deux mis à jour avec le même canal de distribution Xamarin, cet avertissement peut être ignoré.

#### <a name="failed-to-execute-ls-usrbinmono-exitstatus1"></a>"Failed to execute 'ls /usr/bin/mono': ExitStatus=1"

Ce message peut être ignoré tant que le Mac exécute OS X 10.11 (El Capitan) ou une version plus récente. Ce message n’est pas un problème sur OS X 10.11, car Xamarin vérifie également **/usr/local/bin/mono**, qui est l’emplacement attendu correct pour `mono` sur OS X 10.11.

#### <a name="bonjour-service-macbuildhost-did-not-respond-with-its-ip-address"></a>« Le service Bonjour 'MacBuildHost' n’a pas répondu avec son adresse IP. »

Ce message peut être ignoré sauf si vous remarquez que la boîte de dialogue de connexion n’affiche pas l’adresse IP de l’hôte de build Mac. Si l’adresse IP ne figure _pas_ dans cette boîte de dialogue, vous pouvez toujours [ajouter le Mac manuellement](~/ios/get-started/installation/windows/connecting-to-mac/index.md#manually-add-a-mac).

#### <a name="invalid-user-a-from-101895-and-input_userauth_request-invalid-user-a-preauth"></a>« Utilisateur a non valide à partir de 10.1.8.95 » et « input\_userauth\_request : utilisateur a non valide [autorisation préalable] »

Vous pouvez remarquer ce message si vous consultez le fichier **sshd.log**. Ces messages font partie du processus de connexion normal. Ils s’affichent car Xamarin utilise temporairement le nom d’utilisateur **a** lors de la récupération de l’_empreinte digitale SSH_.

### <a name="output-window-and-log-files"></a>Fenêtre Sortie et fichiers journaux

Si Visual Studio rencontre une erreur lors de la connexion à l’hôte de build, il y a deux emplacements dans lesquels rechercher les messages supplémentaires : la fenêtre Sortie et les fichiers journaux.

#### <a name="output-window"></a>Fenêtre Sortie

La fenêtre Sortie est le meilleur endroit pour commencer. Elle affiche des messages relatifs aux principales étapes et erreurs de connexion. Pour afficher les messages Xamarin dans la fenêtre Sortie :

1. Sélectionnez **Affichage > Sortie** dans les menus ou cliquez sur l’onglet **Sortie**.
2. Cliquez sur le menu déroulant **Afficher la sortie à partir de**.
3. Sélectionnez **Xamarin**.

[![](troubleshooting-images/troubleshooting-image11.png "Sélectionner Xamarin sous l’onglet Sortie")](troubleshooting-images/troubleshooting-image11.png#lightbox)

#### <a name="log-files"></a>Fichiers journaux

Si la fenêtre Sortie n’inclut pas suffisamment d’informations pour diagnostiquer le problème, les fichiers journaux constituent l’emplacement suivant à consulter. Les fichiers journaux contiennent des messages de diagnostic supplémentaires qui n’apparaissent pas dans la fenêtre Sortie. Pour consulter les fichiers journaux :

1. Démarrez Visual Studio.

    > [!IMPORTANT]
    > Notez que les fichiers **.svclogs** ne sont pas activés par défaut. Pour y accéder, vous devez démarrer Visual Studio avec les journaux détaillés, comme expliqué dans le guide des [journaux des versions](~/cross-platform/troubleshooting/questions/version-logs.md#visual-studio-startup-verbose-logs). Pour plus d’informations, reportez-vous au blog [Troubleshooting Extensions with the Activity Log](https://blogs.msdn.microsoft.com/visualstudio/2010/02/24/troubleshooting-extensions-with-the-activity-log/).

2. Tentez de vous connecter à l’hôte de build.

3. Une fois que Visual Studio a rencontré l’erreur de connexion, collectez les journaux à partir d’**Aide > Xamarin > Journaux Zip** :

    [![](troubleshooting-images/troubleshooting-image12.png "Collecter les journaux à partir d’Aide > Xamarin > Journaux Zip")](troubleshooting-images/troubleshooting-image12.png#lightbox)

4. Quand vous ouvrez le fichier .zip, une liste de fichiers semblable à l’exemple ci-dessous s’affiche. Concernant les erreurs de connexion, les fichiers les plus importants sont les fichiers **\*Ide.log** et **\*Ide.svclog**. Ces fichiers contiennent les mêmes messages dans deux formats légèrement différents. Le fichier **.svclog** est au format XML. Il est utile si vous voulez parcourir les messages. Le fichier **.log** est du texte brut. Il est utile si vous voulez filtrer les messages à l’aide d’outils en ligne de commande.

    Pour parcourir tous les messages, sélectionnez et ouvrez le fichier  **.svclog** :

    [![](troubleshooting-images/troubleshooting-image13.png "Sélectionner le fichier svclog")](troubleshooting-images/troubleshooting-image13.png#lightbox)

5. Le fichier  **.svclog** s’ouvre dans **Microsoft Service Trace Viewer**. Vous pouvez parcourir les messages par thread pour voir les groupes connexes de messages. Pour parcourir par thread, sélectionnez tout d’abord l’onglet **Graphe**, puis cliquez sur le menu déroulant **Mode disposition** et sélectionnez **Thread** :

    [![](troubleshooting-images/troubleshooting-image14.png "Cliquer sur le menu déroulant Mode disposition et sélectionner Thread")](troubleshooting-images/troubleshooting-image14.png#lightbox)

<a name="verboselogs" />

#### <a name="verbose-log-files"></a>Fichiers journaux détaillés

Si les fichiers journaux normaux ne fournissent toujours pas suffisamment d’informations pour diagnostiquer le problème, une dernière technique à essayer consiste à activer la journalisation détaillée. Les journaux détaillés sont également privilégiés sur les rapports de bogue.

1. Quittez Visual Studio.

2. Démarrez une [**invite de commandes développeur**](https://msdn.microsoft.com/library/ms229859(v=vs.110).aspx).

3. Exécutez la commande suivante dans l’invite de commandes pour lancer Visual Studio avec la journalisation détaillée :

    ```bash
    devenv /log
    ```

4. Tentez de vous connecter à l’hôte de build à partir de Visual Studio.

5. Une fois que Visual Studio a rencontré l’erreur de connexion, collectez les journaux à partir d’**Aide > Xamarin > Journaux Zip**.

6. Exécutez la commande suivante dans Terminal sur le Mac pour copier tous les messages de journal récents à partir du serveur SSH dans un fichier sur votre bureau :

    ```bash
    grep sshd /var/log/system.log > "$HOME/Desktop/sshd.log"
   ```

Si ces fichiers journaux détaillés ne fournissent pas suffisamment d’indices pour résoudre le problème directement, [envoyez un nouveau rapport de bogue](https://bugzilla.xamarin.com/newbug) en joignant le fichier .zip de l’étape 5 et le fichier .log de l’étape 6.

## <a name="troubleshooting-automatic-mac-provisioning"></a>Résolution des problèmes de provisionnement Mac automatique

### <a name="ide-log-files"></a>Fichiers journaux IDE

Si vous rencontrez des problèmes d’utilisation du [provisionnement Mac automatique](~/ios/get-started/installation/windows/connecting-to-mac/index.md#automatic-mac-provisioning), examinez les journaux IDE Visual Studio 2017, stockés dans **%LOCALAPPDATA%\Xamarin\Logs\15.0**.

## <a name="troubleshooting-build-and-deployment-errors"></a>Résolution des erreurs de build et de déploiement

Cette section aborde quelques problèmes qui peuvent se produire une fois Visual Studio correctement connecté à l’hôte de build.

### <a name="unable-to-connect-to-address1921681222-with-usermacuser"></a>« Impossible de se connecter à Address='192.168.1.2:22' avec User='macuser' »

Causes connues :

- **Fonctionnalité de sécurité Xamarin 4.1** : Cette erreur _se produit_ si vous revenez à la version antérieure Xamarin 4.0 après avoir utilisé Xamarin 4.1 ou une version ultérieure. Dans ce cas, l’erreur est associée à l’avertissement « La clé privée est chiffrée mais la phrase secrète est vide ». Il s’agit d’un changement _intentionnel_ en raison d’une nouvelle fonctionnalité de sécurité de Xamarin 4.1. **Correctif recommandé** : Supprimez **id\_rsa** et **id\_rsa.pub** de **%LOCALAPPDATA%\Xamarin\MonoTouch**, puis reconnectez l’hôte de build Mac.

- **Restriction de sécurité SSH** : quand ce message est associé à l’avertissement supplémentaire « Impossible d’authentifier l’utilisateur avec les clés SSH », il signifie généralement que l’un des fichiers ou répertoires du chemin complet **$HOME/.ssh/authorized\_keys** sur le Mac dispose d’autorisations d’écriture activées pour d’_autres_ membres ou des membres du _groupe_. **Correctif courant** : Exécutez `chmod og-w "$HOME"` dans une invite de commandes de Terminal sur le Mac. Pour plus d’informations sur le fichier ou répertoire particulier à l’origine du problème, exécutez `grep sshd /var/log/system.log > "$HOME/Desktop/sshd.log"` dans Terminal, puis ouvrez le fichier **sshd.log** à partir de votre bureau et recherchez « Authentication refused: bad ownership or modes » (Authentification refusée : propriété ou modes incorrects).

### <a name="solutions-cannot-be-loaded-from-a-network-share"></a>Impossible de charger des solutions à partir d’un partage réseau

Les solutions ne sont compilées que si elles se trouvent sur le système de fichiers Windows local ou sur un lecteur mappé.

Les solutions enregistrées dans un partage réseau peuvent lever des erreurs ou purement refuser de se compiler. Tous les fichiers **.sln** utilisés dans Visual Studio doivent être enregistrés sur le système de fichiers Windows local.

L’erreur suivante est levée en raison de ce problème :

```bash
error : Building from a network share path is not supported at the moment. Please map a network drive to '\\SharedSources\HelloWorld\HelloWorld' or copy the source to a local directory.
```

Bogue associé : [n° 36195](https://bugzilla.xamarin.com/show_bug.cgi?id=36195)

### <a name="missing-provisioning-profiles-or-failed-to-create-the-a-fat-library-error"></a>Profils de provisionnement manquants ou erreur « Failed to create the a fat library »

Lancez Xcode sur le Mac, puis vérifiez que votre compte de développeur Apple est connecté et que votre profil de développement iOS est téléchargé :

[![](troubleshooting-images/troubleshooting-image7.png "Vérifier que le compte de développeur Apple est connecté et que le profil de développement iOS est téléchargé")](troubleshooting-images/troubleshooting-image7.png#lightbox)

### <a name="a-socket-operation-was-attempted-to-an-unreachable-network"></a>« Une opération de socket a été tentée sur un réseau impossible à atteindre »

Causes signalées :

- **Amélioration [n° 36118](https://bugzilla.xamarin.com/show_bug.cgi?id=36118)**  : Cette erreur peut entraîner l’échec de builds quand Visual Studio utilise une adresse IPv6 pour se connecter à l’hôte de build. (La connexion de l’hôte de build ne prend pas encore en charge les adresses IPv6.)

### <a name="xamarinios-visual-studio-plugin-fails-to-load-after-reinstallation-of-betaalpha-channel"></a>Le chargement du plug-in Xamarin.iOS Visual Studio échoue après la réinstallation d’un canal bêta/alpha

Bogue pertinent : [n° 40781](https://bugzilla.xamarin.com/show_bug.cgi?id=40781).

Ce problème peut se produire quand Visual Studio ne parvient pas à actualiser le cache du composant MEF. Si tel est le cas, il peut vous être utile d’installer cette extension de Visual Studio : [https://visualstudiogallery.msdn.microsoft.com/22b94661-70c7-4a93-9ca3-8b6dd45f47cd](https://visualstudiogallery.msdn.microsoft.com/22b94661-70c7-4a93-9ca3-8b6dd45f47cd).

Cette opération efface le cache du composant MEF Visual Studio pour résoudre les problèmes liés à l’endommagement du cache.

<a name="errors" />

### <a name="errors-due-to-existing-build-host-processes-on-the-mac"></a>Erreurs dues à des processus hôtes de build existants sur le Mac

Des processus de connexions d’hôtes de build précédentes peuvent parfois interférer avec le comportement de la connexion active actuelle. Pour rechercher d’éventuels processus existants, fermez Visual Studio, puis exécutez les commandes suivantes dans Terminal sur le Mac :

```bash
ps -A | grep mono
```

[![](troubleshooting-images/troubleshooting-image10.png "Exécution de commandes dans Terminal sur le Mac")](troubleshooting-images/troubleshooting-image10.png#lightbox)

Pour arrêter les processus existants, utilisez la commande suivante :

```bash
killall mono
```

### <a name="clearing-the-mac-build-cache"></a>Effacement du cache de build Mac

Si vous résolvez un problème de génération et que vous voulez être certain que le comportement n’est pas lié à des fichiers de build temporaires stockés sur le Mac, vous pouvez supprimer le dossier du cache de build.

1. Exécutez la commande suivante dans Terminal sur le Mac :

    ```bash
    open "$HOME/Library/Caches/Xamarin"
    ```

2. Cliquez sur le dossier **mtbs** en maintenant la touche Ctrl enfoncée, puis sélectionnez **Placer dans la Corbeille** :

    [![](troubleshooting-images/troubleshooting-image9.png "Placer le dossier mtbs dans la Corbeille")](troubleshooting-images/troubleshooting-image9.png#lightbox)

## <a name="related-links"></a>Liens associés

- [Appairer avec un Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md)
- [Vidéo sur l’agent de build Mac Xamarin](https://www.youtube.com/watch?v=MBAPBtxkjFQ)
