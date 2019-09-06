---
title: Utilisation de Jenkins avec Xamarin
description: Ce document explique comment utiliser Jenkins pour l’intégration continue avec les applications Xamarin. Il aborde l’installation, la configuration et l’utilisation de Jenkins.
ms.prod: xamarin
ms.assetid: 1E6825DF-1254-4FCB-B94D-ADD33D1B5309
author: conceptdev
ms.author: crdun
ms.date: 03/23/2017
ms.openlocfilehash: 40f3443fb7c6fc6240e016106d9b6bbe0e0b666d
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290819"
---
# <a name="using-jenkins-with-xamarin"></a>Utilisation de Jenkins avec Xamarin

_Ce guide illustre la configuration de Jenkins en tant que serveur d’intégration continue et l’automatisation de la compilation d’applications mobiles créées avec Xamarin. Il décrit comment installer Jenkins sur OS X, le configurer et configurer des travaux pour compiler les applications Xamarin. iOS et Xamarin. Android lorsque des modifications sont validées dans le système de gestion du code source._

[Introduction à l’intégration continue avec Xamarin](~/tools/ci/intro-to-ci.md) présente l’intégration continue comme une pratique de développement de logiciels utile qui fournit un avertissement anticipé de code endommagé ou incompatible. L’intégration continue permet aux développeurs de résoudre les problèmes et les problèmes au fur et à mesure qu’ils surviennent, et de conserver le logiciel dans un État adapté au déploiement. Cette procédure pas à pas explique comment utiliser le contenu des deux documents ensemble.

Ce guide montre comment installer Jenkins sur un ordinateur dédié exécutant OS X et le configurer pour qu’il s’exécute automatiquement au démarrage de l’ordinateur. Une fois Jenkins installé, nous allons installer des plug-ins supplémentaires pour prendre en charge MS Build. Jenkins prend en charge git prêt à l’emploi. Si TFS est utilisé pour le contrôle de code source, un plug-in et des utilitaires de ligne de commande supplémentaires doivent également être installés.

Une fois que Jenkins est configuré et que tous les plug-ins nécessaires ont été installés, nous allons créer un ou plusieurs travaux pour compiler les projets Xamarin. Android et Xamarin. iOS. Un travail est un ensemble d’étapes et de métadonnées nécessaires à l’exécution d’un travail. Un travail se compose généralement des éléments suivants :

- **Gestion du code source (SCM)** : il s’agit d’une entrée de métadonnées dans les fichiers de configuration Jenkins qui contient des informations sur la façon de se connecter au contrôle de code source et les fichiers à récupérer.
- **Déclencheurs** : les déclencheurs sont utilisés pour démarrer un travail en fonction de certaines actions, par exemple lorsqu’un développeur valide les modifications apportées au référentiel de code source.
- **Instructions de génération** : il s’agit d’un plug-in ou d’un script qui compilera le code source et produira un fichier binaire pouvant être installé sur les périphériques mobiles.
- **Actions de génération facultatives** : cela peut inclure l’exécution de tests unitaires, l’exécution d’analyses statiques sur le code, la signature de code ou le démarrage d’un autre travail lié à la génération.
- **Notifications** : un travail peut envoyer une notification sur l’état d’une build.
- **Sécurité** : bien que facultative, il est vivement recommandé d’activer également les fonctionnalités de sécurité Jenkins.

Ce guide explique comment configurer un serveur Jenkins qui couvre chacun de ces points. À la fin de celle-ci, nous devrions avoir une bonne compréhension de la configuration et de la configuration de Jenkins pour créer des APK pour nos projets mobiles Xamarin.

## <a name="requirements"></a>Configuration requise

Le serveur de builds idéal est un ordinateur autonome dédié à l’unique objectif de la création et éventuellement du test de l’application. Un ordinateur dédié garantit que les artefacts qui peuvent être requis pour d’autres rôles (tels que ceux d’un serveur Web) ne contaminent pas la Build. Par exemple, si le serveur de builds joue également le rôle de serveur Web, le serveur Web peut nécessiter une version conflictuelle de la bibliothèque commune. En raison de ce conflit, le serveur Web peut ne pas fonctionner correctement ou Jenkins peut créer des builds qui ne fonctionnent pas lorsqu’elles sont déployées pour les utilisateurs.

Le serveur de builds pour Xamarin Mobile Apps est configuré de façon très similaire à la station de travail d’un développeur. Il possède un compte d’utilisateur dans lequel Jenkins, Visual Studio pour Mac et Xamarin. iOS et Xamarin. Android seront installés. Tous les certificats de signature de code, les profils de provisionnement et les magasins de clés doivent également être installés. *En général, le compte d’utilisateur du serveur de builds est séparé de vos comptes de développeur. Veillez à installer et à configurer tous les logiciels, clés et certificats quand vous êtes connecté avec le compte d’utilisateur du serveur de builds.*

Le diagramme suivant illustre tous ces éléments sur un serveur de builds Jenkins type :

[![](jenkins-walkthrough-images/image1.png "Ce diagramme illustre tous ces éléments sur un serveur de builds Jenkins classique")](jenkins-walkthrough-images/image1.png#lightbox)

les applications iOS peuvent uniquement être générées et signées sur un ordinateur exécutant macOS. Une mini-Mac est une option de faible coût raisonnable, mais tout ordinateur apte à exécuter OS X 10,10 (Yosemite) ou une version ultérieure suffit.

Si TFS est utilisé pour le contrôle de code source, vous devez installer [Team Explorer Everywhere](https://docs.microsoft.com/azure/devops/java/download-eclipse-plug-in/). Team Explorer Everywhere fournit un accès multiplateforme à TFS sur le terminal dans macOS.

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="installing-jenkins"></a>Installation de Jenkins

La première tâche à utiliser Jenkins consiste à l’installer. Il existe trois façons d’exécuter Jenkins sur OS X :

- En tant que démon, en cours d’exécution en arrière-plan.
- À l’intérieur d’un conteneur de servlet tel que Tomcat, jet ou JBoss.
- En tant que processus normal s’exécutant sous un compte d’utilisateur.

La plupart des applications d’intégration continue traditionnelles s’exécutent en arrière-plan, soit en tant \*que démon (sur OS X ou Nix), soit en tant que service (sur Windows). Cela convient pour les scénarios où aucune interaction de l’interface utilisateur n’est requise et où la configuration de l’environnement de génération peut être facilement effectuée. Les applications mobiles nécessitent également des magasins de clés et des certificats de signature qui peuvent être problématiques à l’accès lorsque Jenkins s’exécute en tant que démon. En raison de ces préoccupations, ce document met l’accent sur le troisième scénario : l’exécution de Jenkins sous un compte d’utilisateur sur le serveur de builds.

Jenkins. app est un moyen pratique d’installer Jenkins. Il s’agit d’un wrapper AppleScript qui simplifie le démarrage et l’arrêt d’un serveur Jenkins. Au lieu de s’exécuter dans un interpréteur de commandes bash, Jenkins s’exécute comme une application avec une icône dans le Dock, comme illustré dans la capture d’écran suivante :

[![](jenkins-walkthrough-images/image2.png "Au lieu de s’exécuter dans un interpréteur de commandes bash, Jenkins s’exécute en tant qu’application avec une icône dans le Dock, comme illustré dans cette capture d’écran.")](jenkins-walkthrough-images/image2.png#lightbox)

Le démarrage ou l’arrêt de Jenkins est aussi simple que de démarrer ou d’arrêter Jenkins. app.

Pour installer Jenkins. app, téléchargez la dernière version à partir de la page de téléchargement du projet, illustrée dans la capture d’écran ci-dessous :

[![](jenkins-walkthrough-images/image3.png ", Téléchargez la dernière version à partir de la page de téléchargement de projets, illustrée dans cette capture d’écran.")](jenkins-walkthrough-images/image3.png#lightbox)

Extrayez le fichier zip `/Applications` dans le dossier sur votre serveur de builds et démarrez-le comme n’importe quelle autre application OS X.
La première fois que vous démarrez Jenkins. app, une boîte de dialogue s’affiche vous informant qu’elle va télécharger Jenkins :

[![](jenkins-walkthrough-images/image4.png "L’application affiche une boîte de dialogue vous informant qu’elle va télécharger Jenkins")](jenkins-walkthrough-images/image4.png#lightbox)

Une fois le téléchargement de Jenkins. app terminé, une autre boîte de dialogue s’affiche pour vous demander si vous souhaitez personnaliser le démarrage de Jenkins, comme indiqué dans la capture d’écran suivante :

[![](jenkins-walkthrough-images/image5.png "Le téléchargement de l’application est terminé, une autre boîte de dialogue s’affiche pour vous demander si vous souhaitez personnaliser le démarrage Jenkins, comme illustré dans cette capture d’écran.")](jenkins-walkthrough-images/image5.png#lightbox)

La personnalisation de Jenkins est facultative et n’a pas besoin d’être effectuée à chaque démarrage de l’application : les paramètres par défaut pour Jenkins fonctionnent dans la plupart des cas.

S’il est nécessaire de personnaliser Jenkins, cliquez sur le bouton **modifier les valeurs par défaut** . Cela vous présentera deux boîtes de dialogue consécutives : une qui demande des paramètres de ligne de commande Java et une autre qui demande des paramètres de ligne de commande Jenkins. Les deux captures d’écran suivantes illustrent ces deux boîtes de dialogue :

[![](jenkins-walkthrough-images/image6.png "Cette capture d’écran montre les boîtes de dialogue")](jenkins-walkthrough-images/image6.png#lightbox)

[![](jenkins-walkthrough-images/image7.png "Cette capture d’écran montre les boîtes de dialogue")](jenkins-walkthrough-images/image7.png#lightbox)

Une fois que Jenkins est en cours d’exécution, vous pouvez le définir en tant qu’élément de connexion afin qu’il démarre chaque fois que l’utilisateur se connecte à l’ordinateur. Pour ce faire, cliquez avec le bouton droit sur l’icône Jenkins dans le Dock, puis choisissez **options... > Ouvrez à la connexion**, comme illustré dans la capture d’écran suivante :

[![](jenkins-walkthrough-images/image8.png "Pour ce faire, cliquez avec le bouton droit sur l’icône Jenkins dans le Dock et sélectionnez OptionsOpen at login, comme illustré dans cette capture d’écran.")](jenkins-walkthrough-images/image8.png#lightbox)

En conséquence, Jenkins. app se lance automatiquement chaque fois que l’utilisateur se connecte, mais pas au démarrage de l’ordinateur. Il est possible de spécifier un compte d’utilisateur que OS X utilisera pour se connecter automatiquement avec au moment du démarrage. Ouvrez les **Préférences système**, puis sélectionnez l’icône **utilisateurs & les groupes** comme indiqué dans cette capture d’écran :

[![](jenkins-walkthrough-images/image9.png "Ouvrez les préférences système, puis sélectionnez l’icône des groupes d’utilisateurs comme illustré dans cette capture d’écran.")](jenkins-walkthrough-images/image9.png#lightbox)

Cliquez sur le bouton **options de connexion** , puis choisissez le compte que OS X utilisera pour la connexion au moment du démarrage.

À ce stade, Jenkins a été installé. Toutefois, si nous voulons créer des applications mobiles Xamarin, nous aurons besoin d’installer certains plug-ins.

### <a name="installing-plugins"></a>Installation des plug-ins

Quand le programme d’installation de Jenkins. app est terminé, il démarre Jenkins et lance le navigateur Web avec http://localhost:8080 l’URL, comme indiqué dans la capture d’écran ci-dessous :

[![](jenkins-walkthrough-images/image10.png "8080, comme illustré dans cette capture d’écran")](jenkins-walkthrough-images/image10.png#lightbox)

Dans cette page, sélectionnez **Jenkins > gérer Jenkins > gérer les plug-ins** dans le menu situé dans l’angle supérieur gauche, comme indiqué dans la capture d’écran ci-dessous :

[![](jenkins-walkthrough-images/image11.png "Dans cette page, sélectionnez Jenkins gérer Jenkins gérer les plug-ins dans le menu situé dans le coin supérieur gauche")](jenkins-walkthrough-images/image11.png#lightbox)

La page Gestionnaire de **plug-ins Jenkins** s’affiche. Si vous cliquez sur l’onglet Available (disponible), vous verrez une liste de plus de 600 plug-ins pouvant être téléchargés et installés. Cette image est illustrée dans la capture d’écran ci-dessous :

[![](jenkins-walkthrough-images/image12.png "Si vous cliquez sur l’onglet Available (disponible), vous verrez une liste de plus de 600 plug-ins pouvant être téléchargés et installés")](jenkins-walkthrough-images/image12.png#lightbox)

Le fait de faire défiler tous les plug-ins 600 pour en trouver quelques peut être fastidieux et sujet aux erreurs. Jenkins fournit un champ de recherche de filtre dans le coin supérieur droit de l’interface. L’utilisation de ce champ de filtre pour la recherche simplifiera la localisation et l’installation d’un ou de tous les plug-ins suivants :

- **Plug-in MSBuild Jenkins** : ce plug-in permet de créer des projets Visual Studio et Visual Studio pour Mac (. sln) et des projets (. csproj).
- **Plug-in d’injecteur d’environnement** : il s’agit d’un plug-in facultatif mais utile qui permet de définir des variables d’environnement au niveau du travail et de la Build. Il offre également une protection supplémentaire pour les variables telles que les mots de passe utilisés pour signer le code de l’application. Il est parfois abrégé en tant que *plug-in EnvInject* .
- **Plug** -in Team Foundation Server : il s’agit d’un plug-in facultatif qui n’est requis que si vous utilisez Team Foundation Server ou Team Foundation services pour le contrôle de code source.

Jenkins prend en charge git sans plug-ins supplémentaires.

Après avoir installé tous les plug-ins, redémarrez Jenkins et configurez les paramètres globaux pour chaque plug-in. Vous pouvez trouver les paramètres globaux d’un plug-in en sélectionnant **Jenkins > gérer Jenkins > configurer le système** dans l’angle supérieur gauche, comme illustré dans la capture d’écran ci-dessous :

[![](jenkins-walkthrough-images/image13.png "Vous pouvez trouver les paramètres globaux d’un plug-in en sélectionnant Jenkins/Manage Jenkins/configure System dans l’angle supérieur gauche")](jenkins-walkthrough-images/image13.png#lightbox)

Lorsque vous sélectionnez cette option de menu, vous êtes dirigé vers la page **configurer le système [Jenkins]** . Cette page contient des sections permettant de configurer Jenkins et de définir certaines valeurs de plug-in globales.  La capture d’écran ci-dessous illustre un exemple de cette page :

[![](jenkins-walkthrough-images/image14.png "Cette capture d’écran illustre un exemple de cette page")](jenkins-walkthrough-images/image14.png#lightbox)

#### <a name="configuring-the-msbuild-plugin"></a>Configuration du plug-in MSBuild

Le plug-in MSBuild doit être configuré pour utiliser **/Library/Frameworks/mono.Framework/Commands/xbuild** pour compiler Visual Studio pour Mac fichiers de solution et de projet. Faites défiler la page **configurer le système [Jenkins]** jusqu’à ce que le bouton **Ajouter MSBuild** apparaisse, comme indiqué dans la capture d’écran ci-dessous :

 [![](jenkins-walkthrough-images/image15.png "Faites défiler la page Configurer le Jenkins du système jusqu’à ce que le bouton Ajouter MSBuild apparaisse")](jenkins-walkthrough-images/image15.png#lightbox)

Cliquez sur ce bouton et renseignez les champs **nom** et **chemin d’accès** aux champs **MSBuild** sur le formulaire qui s’affiche. Le nom de votre installation **MSBuild** doit être significatif, tandis que le **chemin d’accès à MSBuild** doit être `xbuild`le chemin d’accès à, qui est généralement **/Library/Frameworks/mono.Framework/Commands/xbuild**. Une fois que vous avez enregistré les modifications en cliquant sur le bouton enregistrer ou appliquer au bas de la page, Jenkins peut utiliser `xbuild` pour compiler vos solutions.

#### <a name="configuring-the-tfs-plugin"></a>Configuration du plug-in TFS

Cette section est obligatoire si vous envisagez d’utiliser TFS pour votre contrôle de code source.

Pour qu’une station de travail macOS puisse interagir avec un serveur TFS, [Team Explorer Everywhere](https://docs.microsoft.com/azure/devops/java/download-eclipse-plug-in/) doit être installé sur la station de travail. Team Explorer Everywhere est un ensemble d’outils de Microsoft qui comprend un client de ligne de commande multiplateforme pour l’accès à TFS. Team Explorer Everywhere peut être téléchargée à partir de Microsoft et installée en trois étapes :

1. Décompressez le fichier d’archive dans un répertoire accessible au compte d’utilisateur. Par exemple, vous pouvez décompresser le fichier vers **~/tee**.
2. Configurez l’interpréteur de commandes ou le chemin d’accès système pour inclure le dossier qui contient les fichiers qui ont été décompressés à l’étape 1 ci-dessus. Par exemple,

    ```
    echo export PATH~/tee/:$PATH' >> ~/.bash_profile
    ```

3. Pour confirmer que Team Explorer Everywhere est installé, ouvrez une session de terminal et exécutez la `tf` commande. Si TF est correctement configuré, la sortie suivante s’affiche dans votre session Terminal :

    ```
    $ tf
    Team Explorer Everywhere Command Line Client (version 11.0.0.201306181526)

    Available commands and their options:
    ```

Une fois le client de ligne de commande pour TFS installé, Jenkins doit être configuré avec le chemin d' `tf` accès complet au client de ligne de commande. Faites défiler la page **configurer le système [Jenkins]** jusqu’à ce que vous trouviez la section Team Foundation Server, comme illustré dans la capture d’écran suivante :

[![](jenkins-walkthrough-images/image17.png "Faites défiler la page Configurer le Jenkins du système jusqu’à ce que vous trouviez la section Team Foundation Server")](jenkins-walkthrough-images/image17.png#lightbox)

Entrez le chemin d’accès complet `tf` à la commande, puis cliquez sur le bouton **Enregistrer** .

### <a name="configure-jenkins-security"></a>Configurer la sécurité Jenkins

Lors de sa première installation, la sécurité de Jenkins est désactivée. il est donc possible pour n’importe quel utilisateur de configurer et d’exécuter n’importe quel type de travail de manière anonyme. Cette section explique comment configurer la sécurité à l’aide de la base de données utilisateur Jenkins pour configurer l’authentification et l’autorisation.

Vous pouvez trouver les paramètres de sécurité en sélectionnant **Jenkins > gérer Jenkins > configurer la sécurité globale**, comme illustré dans cette capture d’écran :

[![](jenkins-walkthrough-images/image18.png "Vous pouvez trouver les paramètres de sécurité en sélectionnant Jenkins/Manage Jenkins/configure Global Security.")](jenkins-walkthrough-images/image18.png#lightbox)

Dans la page **configurer la sécurité globale** , activez la case à cocher **activer la sécurité** et le formulaire **Access Control** doit apparaître, comme dans la capture d’écran suivante :

[![](jenkins-walkthrough-images/image19.png "Dans la page Configurer la sécurité globale, activez la case à cocher Activer la sécurité et le formulaire Access Control doit apparaître, comme dans cette capture d’écran.")](jenkins-walkthrough-images/image19.png#lightbox)

Activez ou désactivez la case d’option pour la **base de données utilisateur de Jenkins** dans la **section domaine de sécurité**et vérifiez que l’option **autoriser les utilisateurs à s’inscrire** est également activée, comme illustré dans la capture d’écran suivante :

[![](jenkins-walkthrough-images/image20.png "Activez ou désactivez la case d’option pour Jenkins propre base de données utilisateur dans la section domaine de sécurité et vérifiez que l’option autoriser les utilisateurs à s’inscrire est également cochée.")](jenkins-walkthrough-images/image20.png#lightbox)

Enfin, redémarrez Jenkins et créez un nouveau compte. Le premier compte créé est le compte racine et ce compte sera automatiquement promu en tant qu’administrateur. Revenez à la page **configurer la sécurité globale** , puis activez la case d’option **sécurité basée sur une matrice** . Le compte racine doit disposer d’un accès complet et le compte anonyme doit recevoir un accès en lecture seule, comme illustré dans la capture d’écran suivante :

[![](jenkins-walkthrough-images/image21.png "Le compte racine doit disposer d’un accès complet et le compte anonyme doit recevoir un accès en lecture seule")](jenkins-walkthrough-images/image21.png#lightbox)

Une fois ces paramètres enregistrés et Jenkins redémarré, la sécurité est activée.

#### <a name="disabling-security"></a>Désactivation de la sécurité

Dans le cas d’un mot de passe oublié ou d’un verrouillage Jenkins, il est possible de désactiver la sécurité en procédant comme suit :

1. Arrêtez Jenkins. Si vous utilisez Jenkins. app, vous pouvez effectuer cette opération en cliquant avec le bouton droit sur l’icône Jenkins. app dans le Dock et en sélectionnant quitter dans le menu qui s’affiche :

    ![Icône d’application dans le Dock, puis en sélectionnant quitter dans le menu qui s’affiche](jenkins-walkthrough-images/image19.png)

2. Ouvrez le fichier **~/.Jenkins/config.xml** dans un éditeur de texte.
3. Remplacez la valeur de l' `<usesecurity></usesecurity>` élément `false`par `true` .
4. Supprimez `<authorizationstrategy></authorizationstrategy>` les `<securityrealm></securityrealm>` éléments et du fichier.
5. Redémarrez Jenkins.

## <a name="setting-up-a-job"></a>Configuration d’un travail

Au niveau supérieur, Jenkins organise toutes les tâches nécessaires à la création de logiciels dans un *travail*. Un travail est également associé à des métadonnées, en fournissant des informations sur la build, telles que l’obtention du code source, la fréquence d’exécution de la génération, les variables spéciales nécessaires à la génération et la notification des développeurs en cas d’échec de la génération.

Pour créer des travaux, sélectionnez **Jenkins > nouveau travail** dans le menu dans le coin supérieur droit, comme illustré dans la capture d’écran suivante :

![](jenkins-walkthrough-images/image22.png "Les travaux sont créés en sélectionnant Jenkins New Job dans le menu situé dans l’angle supérieur droit")

La page **nouveau travail [Jenkins]** s’affiche. Entrez un nom pour le travail, puis sélectionnez la case d’option **créer un projet de logiciel de style libre** . La capture d’écran suivante montre un exemple :

![](jenkins-walkthrough-images/image23.png "Entrez un nom pour le travail et sélectionnez la case d’option créer un projet de logiciel de style libre")

Si vous cliquez sur le bouton **OK** , la page de configuration du travail est présente. Cela doit ressembler à la capture d’écran suivante :

![](jenkins-walkthrough-images/image24.png "Cela doit ressembler à cette capture d’écran")

Jenkins organise les travaux dans un répertoire sur le disque dur à l’emplacement suivant : **~/.Jenkins/Jobs/[job name]**

Ce dossier contient tous les fichiers et artefacts spécifiques au travail, tels que les journaux, les fichiers de configuration et le code source qui doit être compilé.

Une fois la tâche initiale créée, elle doit être configurée avec un ou plusieurs des éléments suivants :

- Le système de gestion du code source doit être spécifié.
- Une ou plusieurs *actions de génération* doivent être ajoutées au projet. Il s’agit des étapes ou des tâches nécessaires à la génération de l’application.
- Le travail doit être affecté à un *déclencheur de build* . il s’agit d’un ensemble d’instructions qui informent Jenkins de la fréquence à laquelle récupérer le code et générer le projet final.

### <a name="configuring-source-code-control"></a>Configuration du contrôle de code source

La première tâche Jenkins récupère le code source à partir du système de gestion du code source. Jenkins prend en charge de nombreux systèmes de gestion de code source populaires disponibles dès aujourd’hui. Cette section aborde deux systèmes populaires, git et Team Foundation Server. Chacun de ces systèmes de gestion du code source est abordé plus en détail dans les sections ci-dessous.

#### <a name="using-git-for-source-code-control"></a>Utilisation de Git pour le contrôle de code source

Si vous utilisez TFS pour le contrôle de code source, [ignorez](#using-tfs-for-source-code-management) cette section et passez à la section suivante à l’aide de TFS.

Jenkins prend en charge git prêt à l’emploi : aucun plug-in supplémentaire n’est nécessaire. Pour utiliser Git, cliquez sur la case d’option **git** , puis entrez l’URL du référentiel git, comme indiqué dans la capture d’écran suivante :

![](jenkins-walkthrough-images/image25.png "Pour utiliser Git, cliquez sur la case d’option git, puis entrez l’URL du référentiel git.")

Une fois les modifications enregistrées, la configuration git est terminée.

#### <a name="using-tfs-for-source-code-management"></a>Utilisation de TFS pour la gestion du code source

Cette section s’applique uniquement aux utilisateurs TFS.

Cliquez sur la case d’option **Team Foundation Server** et la section Configuration de TFS doit apparaître, comme dans la capture d’écran suivante :

![](jenkins-walkthrough-images/image26.png "Cliquez sur la case d’option Team Foundation Server et la section Configuration de TFS doit apparaître.")

Fournissez les informations nécessaires pour TFS. La capture d’écran suivante montre un exemple du formulaire terminé :

![](jenkins-walkthrough-images/image27.png "Cette capture d’écran montre un exemple de formulaire terminé")

#### <a name="testing-the-source-code-control-configuration"></a>Test de la configuration du contrôle de code source

Une fois que le contrôle de code source approprié a été configuré, cliquez sur **Enregistrer** pour enregistrer les modifications. Vous revenez alors à la page d’hébergement du travail, qui ressemble à la capture d’écran suivante :

![](jenkins-walkthrough-images/image28.png "Vous êtes alors revenez à la page d’hébergement du travail, qui ressemble à cette capture d’écran.")

La façon la plus simple de valider le fait que le contrôle de code source est correctement configuré consiste à déclencher une génération manuellement, même si aucune action de génération n’est spécifiée. Pour démarrer une génération manuellement, la page d’accueil du travail comporte un lien **générer maintenant** dans le menu de gauche, comme illustré dans la capture d’écran ci-dessous :

![](jenkins-walkthrough-images/image29.png "Pour démarrer une génération manuellement, la page d’accueil du travail comporte un lien générer maintenant dans le menu de gauche")

Quand une build a été démarrée, la boîte de dialogue historique de la build affiche un cercle bleu clignotant, une barre de progression, le numéro de build et l’heure de début de la génération, comme dans la capture d’écran suivante :

![](jenkins-walkthrough-images/image30.png "Quand une build a été démarrée, la boîte de dialogue historique de la build affiche un cercle bleu clignotant, une barre de progression, le numéro de build et l’heure de début de la génération")

Si la tâche est réussie, un cercle bleu s’affiche. Si le travail échoue, un cercle rouge s’affiche.

Pour faciliter la résolution des problèmes qui peuvent survenir dans le cadre de la génération, Jenkins capture l’ensemble de la sortie de la console pour le travail. Pour afficher la sortie de la console, cliquez sur le travail dans l' **historique de build**, puis sur le lien sortie de la **console** dans le menu de gauche. La capture d’écran suivante montre le lien de sortie de la **console** , ainsi que la sortie d’une tâche réussie :

![](jenkins-walkthrough-images/image31.png "Cette capture d’écran montre le lien de sortie de la console, ainsi que la sortie d’une tâche réussie.")

#### <a name="location-of-build-artifacts"></a>Emplacement des artefacts de build

Jenkins récupère l’intégralité du code source dans un dossier spécial appelé *espace de travail*. Ce répertoire se trouve dans le dossier à l’emplacement suivant :

```
~/.jenkins/jobs/[JOB NAME]/workspace
```

Le chemin d’accès à l’espace de travail sera stocké dans `$WORKSPACE`une variable d’environnement nommée.

Il est possible de parcourir le dossier de l’espace de travail dans Jenkins en accédant à la page d’accueil d’un travail, puis en cliquant sur le lien de l' **espace de travail** dans le menu de gauche. La capture d’écran suivante montre un exemple de l’espace de travail pour un travail nommé **HelloWorld**:

![](jenkins-walkthrough-images/image32.png "Cette capture d’écran montre un exemple de l’espace de travail pour un travail nommé HelloWorld")

### <a name="build-triggers"></a>Déclencheurs de build

Il existe plusieurs stratégies pour lancer des builds dans Jenkins : celles-ci sont appelées *déclencheurs de build*. Un déclencheur de build aide Jenkins à déterminer quand démarrer un travail et générer le projet. Deux des déclencheurs de génération les plus courants sont les suivants :

- **Générer régulièrement** : ce déclencheur force Jenkins à démarrer un travail à des intervalles spécifiés, par exemple toutes les deux heures ou à minuit les jours de la semaine. La build démarre, qu’il y ait eu des modifications dans le référentiel de code source.
- **Interrogation SCM** : ce déclencheur interroge le contrôle de code source régulièrement. Si des modifications ont été validées dans le référentiel de code source, Jenkins démarre une nouvelle Build.

Le SCM d’interrogation est un déclencheur populaire, car il fournit des commentaires rapides quand un développeur valide des modifications qui provoquent l’arrêt de la Build. Cela est utile pour avertir les équipes que certains codes récemment validés sont à l’origine de problèmes, et permet aux développeurs de résoudre le problème, tandis que les modifications sont toujours à l’esprit.

Les builds périodiques sont souvent utilisées pour créer une version de l’application qui peut être distribuée aux testeurs. Par exemple, une build périodique peut être planifiée le vendredi soir afin que les membres de l’équipe AQ puissent tester le travail de la semaine précédente.

### <a name="compiling-a-xamarinios-applications"></a>Compilation d’applications Xamarin. iOS
Les projets Xamarin. iOS peuvent être compilés à partir de `xbuild` la `msbuild`ligne de commande à l’aide de ou de. La commande d’interpréteur de commandes s’exécute dans le contexte du compte d’utilisateur qui exécute Jenkins. Il est important que le compte d’utilisateur ait accès au profil de provisionnement afin que l’application puisse être correctement packagée pour la distribution. Il est possible d’ajouter cette commande d’interpréteur de commandes à la page de configuration du travail.

Faites défiler jusqu’à la section **Build** . Cliquez sur le bouton **Ajouter une étape de génération** , puis sélectionnez Exécuter l' **interpréteur**de commandes, comme illustré dans la capture d’écran suivante :

![](jenkins-walkthrough-images/image33.png "Cliquez sur le bouton Ajouter une étape de génération, puis sélectionnez Exécuter l’interpréteur de commandes")

[!include[](~/tools/ci/includes/commandline-compile-of-xamarin-ios-ipa.md)]

### <a name="building-a-xamarinandroid-project"></a>Génération d’un projet Xamarin. Android

La création d’un projet Xamarin. Android est très similaire en ce qui concerne la création d’un projet Xamarin. iOS. Pour créer un APK à partir d’un projet Xamarin. Android, Jenkins doit être configuré de façon à effectuer les deux étapes suivantes :

- Compiler le projet à l’aide du plug-in MSBuild
- Sign et zip alignent le APK avec un magasin de clés de version valide.

Ces deux étapes seront abordées plus en détail dans les deux sections suivantes.

### <a name="creating-the-apk"></a>Création du APK

Cliquez sur le bouton **Ajouter une étape de génération** , puis sélectionnez **générer un projet ou une solution Visual Studio à l’aide de MSBuild**, comme indiqué dans la capture d’écran ci-dessous :

![](jenkins-walkthrough-images/image36.png "Création du APK cliquez sur le bouton Ajouter une étape de génération, puis sélectionnez générer un projet Visual Studio ou une solution à l’aide de MSBuild")

Une fois l’étape de build ajoutée au projet, renseignez les champs de formulaire qui s’affichent. La capture d’écran suivante est un exemple de la forme terminée :

![](jenkins-walkthrough-images/image37.png "Une fois l’étape de build ajoutée au projet, renseignez les champs de formulaire qui s’affichent")

Cette étape de génération s' `xbuild` exécutera dans le dossier **$Workspace** . Le fichier de build MSBuild est défini sur le fichier **Xamarin. Android. csproj** . Les **arguments de ligne de commande** spécifient une version Release du **PackageForAndroid**cible. Le produit de cette étape sera un APK qui se trouve à l’emplacement suivant :

```
$WORKSPACE/[PROJECT NAME]/bin/Release
```

La capture d’écran suivante montre un exemple de ce APK :

![](jenkins-walkthrough-images/image38.png "Cette capture d’écran montre un exemple de ce APK")

Ce APK n’est pas prêt pour le déploiement, car il n’a pas été signé avec un magasin de clés privé et doit être aligné en mode zip.

#### <a name="signing-and-zipaligning-the-apk-for-release"></a>Signature et Zipaligning du APK pour la mise en version

La signature et la zipaligning du APK sont techniquement deux tâches distinctes qui sont effectuées par deux outils en ligne de commande distincts à partir du Android SDK. Toutefois, il est pratique de les exécuter dans une seule action de génération. Pour plus d’informations sur la signature et la zipaligning d’un APK, consultez la documentation de Xamarin sur la préparation d’une application Android en vue de sa mise en production.

Ces deux commandes requièrent des paramètres de ligne de commande qui peuvent varier d’un projet à l’autre. En outre, certains de ces paramètres de ligne de commande sont des mots de passe qui ne doivent pas apparaître dans la sortie de la console lorsque la build est en cours d’exécution. Nous allons stocker certains de ces paramètres de ligne de commande dans les variables d’environnement. Les variables d’environnement requises pour la signature et/ou l’alignement zip sont décrites dans le tableau ci-dessous :

|Variable d’environnement|Description|
|--- |--- |
|KEYSTORE_FILE|Il s’agit du chemin d’accès au magasin de clés pour la signature du APK|
|KEYSTORE_ALIAS|Clé dans le magasin de clés qui sera utilisée pour signer le APK.|
|INPUT_APK|APK créé par `xbuild`.|
|SIGNED_APK|APK signé produit par `jarsigner`.|
|FINAL_APK|Il s’agit du APK aligné zip qui est produit `zipalign`par.|
|STORE_PASS|Il s’agit du mot de passe qui est utilisé pour accéder au contenu du magasin de clés pour le déranger du fichier.|

Comme décrit dans la section Configuration requise, ces variables d’environnement peuvent être définies lors de la génération à l’aide du plug-in EnvInject. Une nouvelle étape de génération doit être ajoutée au travail en fonction des variables d’environnement Inject, comme indiqué dans la capture d’écran suivante :

![](jenkins-walkthrough-images/image39.png "Une nouvelle étape de génération doit être ajoutée au travail en fonction des variables d’environnement Inject")

Dans le champ de formulaire de **contenu des propriétés** qui s’affiche, les variables d’environnement sont ajoutées, une par ligne, au format suivant :

```
ENVIRONMENT_VARIABLE_NAME = value
```

La capture d’écran suivante montre les variables d’environnement requises pour la signature de APK :

![](jenkins-walkthrough-images/image40.png "Cette capture d’écran montre les variables d’environnement requises pour la signature du APK")

Notez que certaines variables d’environnement pour les fichiers APK sont générées sur la `WORKSPACE` variable d’environnement.

La dernière variable d’environnement est le mot de passe permettant d’accéder au contenu du `STORE_PASS`magasin de clés :. Les mots de passe sont des valeurs sensibles qui doivent être masquées ou omises dans les fichiers journaux. Le plug-in EnvInject peut être configuré pour protéger ces valeurs afin qu’elles ne s’affichent pas dans les journaux.

Juste avant la section **Build** de la configuration du travail est une section **environnement de génération** . Lorsque la case à cocher **injecter des mots de passe** est activée, certains champs de formulaire s’affichent. Ces champs de formulaire sont utilisés pour capturer le nom et la valeur de la variable d’environnement. La capture d’écran suivante est un exemple d' `STORE_PASS` ajout de la variable d’environnement :

![](jenkins-walkthrough-images/image41.png "Cette capture d’écran est un exemple d’ajout de la variable d’environnement STOREPASS")

Une fois les variables d’environnement initialisées, l’étape suivante consiste à ajouter une étape de génération pour la signature et l’alignement du fichier zip APK. Immédiatement après l’étape de génération pour insérer les variables d’environnement, une autre version de commande d’exécution `jarsigner` de `zipalign`l' **interpréteur** de commandes qui exécute et. Chaque commande utilise une ligne, comme illustré dans l’extrait de code suivant :

```
jarsigner -verbose -sigalg MD5withRSA -digestalg SHA1 -keystore $KEYSTORE_FILE -storepass $STORE_PASS -signedjar $SIGNED_APK $INPUT_APK $KEYSTORE_ALIAS
zipalign -f -v 4 $SIGNED_APK $FINAL_APK
```

La capture d’écran suivante montre un exemple d’entrée `jarsigner` des commandes et `zipalign` dans l’étape :

![](jenkins-walkthrough-images/image42.png "Cette capture d’écran montre un exemple de la façon d’entrer les commandes jarsigner et zipalign dans l’étape")

Une fois que toutes les actions de génération sont en place, il est recommandé de déclencher une build manuelle pour vérifier que tout fonctionne. Si la génération échoue, la **sortie** de la console doit être examinée pour obtenir des informations sur ce qui a provoqué l’échec de la Build.

### <a name="submitting-tests-to-test-cloud"></a>Envoi de tests à Test Cloud

Les tests automatisés peuvent être soumis à Test Cloud à l’aide de commandes shell. Pour plus d’informations sur la configuration d’une série de tests dans Xamarin Test Cloud, consultez ce guide d’utilisation de [Xamarin. UITest](/appcenter/test-cloud/preparing-for-upload/uitest/).

## <a name="summary"></a>Récapitulatif

Dans ce guide, nous avons introduit Jenkins comme serveur de builds sur macOS et l’avons configuré pour compiler et préparer les applications mobiles Xamarin pour la mise en service. Nous avons installé Jenkins sur un ordinateur macOS avec plusieurs plug-ins pour prendre en charge le processus de génération. Nous avons créé et configuré un travail qui extrait le code à partir de TFS ou git, puis il compile ce code dans une application prête à l’emploi. Nous avons également abordé deux façons de planifier le moment où les tâches doivent être exécutées.

## <a name="related-links"></a>Liens associés

- [Intégration continue](~/tools/ci/index.md)
- [Test App Center](https://docs.microsoft.com/appcenter/test-cloud/)
