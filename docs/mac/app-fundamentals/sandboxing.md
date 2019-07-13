---
title: Sandboxing d’une application Xamarin.Mac
description: Cet article traite de sandboxing une application Xamarin.Mac relatives à la version sur l’App Store. Il couvre tous les éléments qui entrent dans le bac à sable, tels que les répertoires de conteneur, droits, déterminées par l’utilisateur des autorisations, séparation de privilèges et mise en œuvre du noyau.
ms.prod: xamarin
ms.assetid: 06A2CA8D-1E46-410F-8C31-00EA36F0735D
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: e38ca07aeef1cbd8e121421ebcbad2207a1bb823
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865987"
---
# <a name="sandboxing-a-xamarinmac-app"></a>Sandboxing d’une application Xamarin.Mac

_Cet article traite de sandboxing une application Xamarin.Mac relatives à la version sur l’App Store. Il couvre tous les éléments qui entrent dans le bac à sable, tels que les répertoires de conteneur, droits, déterminées par l’utilisateur des autorisations, séparation de privilèges et mise en œuvre du noyau._

## <a name="overview"></a>Vue d'ensemble

Lorsque vous travaillez avec c# et .NET dans une application Xamarin.Mac, vous avez la possibilité de même bac à sable d’une application comme vous le faites lorsque vous travaillez avec Objective-C ou Swift.

[![Un exemple de l’application en cours d’exécution](sandboxing-images/intro01.png "un exemple de l’application en cours d’exécution")](sandboxing-images/intro01-large.png#lightbox)

Dans cet article, nous aborderons les principes fondamentaux de l’utilisation de bac à sable dans une application Xamarin.Mac et tous les éléments qui entrent dans le bac à sable : répertoires de conteneur, droits, déterminées par l’utilisateur des autorisations, séparation de privilèges et mise en œuvre du noyau. Il est fortement recommandé que vous travaillez via le [Hello, Mac](~/mac/get-started/hello-mac.md) article tout d’abord, en particulier le [Introduction à Xcode et Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) et [Outlets et Actions](~/mac/get-started/hello-mac.md#outlets-and-actions) sections, tel qu’il couvre les principaux concepts et techniques que nous utilisons dans cet article.

Vous pouvez souhaiter de jeter un coup de œil à la [C# exposition de classes / méthodes vers Objective-C](~/mac/internals/how-it-works.md) section de la [éléments internes de Xamarin.Mac](~/mac/internals/how-it-works.md) de document, il explique le `Register` et `Export` attributs utilisé pour structurer vos classes c# pour les objets Objective-C et de l’interface utilisateur éléments.

## <a name="about-the-app-sandbox"></a>Sur le bac à sable d’application

Le bac à sable App fournit solide défense contre les dommages qui peuvent être dû à une application malveillante en cours d’exécution sur un Mac en limitant l’accès d’une application aux ressources système.

Une application non sandbox a tous les droits de l’utilisateur qui exécute l’application et peut accéder ou faire tout ce que l’utilisateur peut. Si l’application contient des failles de sécurité (ou n’importe quelle infrastructure qu’il utilise), un pirate peut potentiellement exploiter ces faiblesses et permet de prendre le contrôle du Mac qu’elle s’exécute sur l’application.

En limitant l’accès aux ressources sur chaque application, une application en sandbox fournit une ligne de défense contre le vol, corruption ou une intention malveillante part une application exécutée sur l’ordinateur de l’utilisateur.

Le bac à sable d’application est une technologie de contrôle d’accès intégrée à Mac OS (appliqué au niveau du noyau) qui offre une stratégie de double :

1. Le bac à sable d’application permet au développeur décrire _comment_ une application interagit avec le système d’exploitation et, de cette façon, il est accordé uniquement les droits d’accès qui sont nécessaires pour faire leur travail et pas plus.
2. Le bac à sable d’application permet à l’utilisateur à en toute transparence accorder l’accès au système via l’ouvrir et enregistrer des boîtes de dialogue, faites glisser les opérations et les interactions utilisateur communs.

### <a name="preparing-to-implement-the-app-sandbox"></a>Préparation à l’implémentation de l’application Sandbox

Les éléments de l’application bac à sable qui seront abordés en détail dans l’article sont les suivantes :

- Répertoires de conteneur
- Droits
- Autorisations déterminées par l’utilisateur
- Séparation des privilèges
- Mise en œuvre du noyau

Une fois que vous avez compris ces détails, vous serez en mesure de créer un plan d’adoption le bac à sable d’application dans votre application Xamarin.Mac.

Tout d’abord, vous devez déterminer si votre application est un bon candidat pour le bac à sable (la plupart des applications sont). Ensuite, vous devez résoudre les incompatibilités d’API et de déterminer laquelle des éléments de l’application Sandbox, vous aurez besoin. Enfin, envisager d’optimiser le niveau de défense de l’application à l’aide de la séparation des privilèges.

Lors de l’adoption d’application bac à sable, certains emplacements de système de fichiers qui utilise votre application sera différentes. En particulier, votre application aura un répertoire de conteneur qui sera utilisé pour les fichiers de prise en charge d’application, les bases de données, les caches et tous les fichiers qui ne sont pas des documents utilisateur. MacOS et Xcode prennent en charge pour migrer de ces types de fichiers à partir de leurs emplacements hérités au conteneur.

## <a name="sandboxing-quick-start"></a>Démarrage rapide de sandboxing

Dans cette section, nous allons créer une application Xamarin.Mac simple qui utilise un affichage Web (qui nécessite une connexion de réseau est limitée sous sandboxing, sauf si spécifiquement demandées) en tant qu’un exemple de mise en route avec le bac à sable de l’application.

Nous allons vérifier que l’application est en fait sable et apprenez à dépanner et résoudre des erreurs d’application Sandbox.

### <a name="creating-the-xamarinmac-project"></a>Création du projet Xamarin.Mac

Nous allons, procédez comme suit pour créer notre exemple de projet :

1. Démarrez Visual Studio pour Mac, puis cliquez sur le **nouvelle Solution...** compte.
2. À partir de la **nouveau projet** boîte de dialogue, sélectionnez **Mac** > **application** > **application Cocoa**: 

    [![Création d’une application Cocoa](sandboxing-images/sample01.png "création d’une application Cocoa")](sandboxing-images/sample01-large.png#lightbox)
3. Cliquez sur le **suivant** , entrez `MacSandbox` pour le nom du projet et cliquez sur le **créer** bouton : 

    [![Entrer le nom de l’application](sandboxing-images/sample02.png "entrer le nom de l’application")](sandboxing-images/sample02-large.png#lightbox)
4. Dans le **panneau solutions**, double-cliquez sur le **Main.storyboard** fichier à ouvrir pour modification dans Xcode : 

    [![Modification de la table de montage séquentiel principal](sandboxing-images/sample03.png "modification le storyboard principal")](sandboxing-images/sample03-large.png#lightbox)
5. Faites glisser un **affichage Web** vers la fenêtre, de taille pour remplir la zone de contenu et configurez-le pour augmenter ou diminuer la fenêtre : 

    [![Ajout d’une vue web](sandboxing-images/sample04.png "Ajout d’une vue web")](sandboxing-images/sample04-large.png#lightbox)
6. Créer un outlet pour l’affichage web appelée `webView`: 

    [![Création d’un nouveau magasin](sandboxing-images/sample05.png "création d’un nouveau magasin")](sandboxing-images/sample05-large.png#lightbox)
7. Retournez dans Visual Studio pour Mac et double-cliquez sur le **ViewController.cs** de fichiers dans le **panneau solutions** à ouvrir pour modification.
8. Ajoutez le code suivant à l’aide d’instruction : `using WebKit;`
9. Rendre le `ViewDidLoad` méthode ressemble ce qui suit : 

    ```csharp
    public override void AwakeFromNib ()
    {
        base.AwakeFromNib ();
        webView.MainFrame.LoadRequest(new NSUrlRequest(new NSUrl("http://www.apple.com")));
    }
    ```

10. Enregistrez les modifications apportées.

Vous exécutez l’application et vérifiez que le site Web Apple est affiché dans la fenêtre comme suit :

[![Affiche un exemple d’application exécution](sandboxing-images/sample06.png "montrant un exemple d’application exécution")](sandboxing-images/sample06-large.png#lightbox)

<a name="Signing_and_Provisioning_the_App" />

### <a name="signing-and-provisioning-the-app"></a>Signature et provisionnement de l’application

Avant que nous pouvons activer le bac à sable d’application, nous devons d’abord configurer et connecter notre application Xamarin.Mac.

Permettent d’effectuer les opérations suivantes :

1. Connectez-vous au portail de développeur Apple : 

    [![Journalisation dans le portail des développeurs Apple](sandboxing-images/sign01.png "journalisation dans le portail des développeurs Apple")](sandboxing-images/sign01-large.png#lightbox)
2. Sélectionnez **certificats, identificateurs et profils**: 

    [![Sélection de certificats, d’identificateurs et de profils](sandboxing-images/sign02.png "Sélection de certificats, d’identificateurs et de profils")](sandboxing-images/sign02-large.png#lightbox)
3. Sous **applications Mac**, sélectionnez **identificateurs**: 

    [![Sélection d’identificateurs](sandboxing-images/sign03.png "en sélectionnant des identificateurs")](sandboxing-images/sign03-large.png#lightbox)
4. Créer un nouvel ID d’application : 

    [![Création d’un nouvel ID d’application](sandboxing-images/sign04.png "création d’un nouvel ID d’application")](sandboxing-images/sign04-large.png#lightbox)
5. Sous **profils de provisionnement**, sélectionnez **développement**: 

    [![Sélection de développement](sandboxing-images/sign05.png "en sélectionnant le développement")](sandboxing-images/sign05-large.png#lightbox)
6. Créer un nouveau profil et sélectionnez **développement d’applications Mac**: 

    [![Création d’un profil](sandboxing-images/sign06.png "création d’un profil")](sandboxing-images/sign06-large.png#lightbox)
7. Sélectionnez l’ID d’application que nous avons créé ci-dessus : 

    [![Sélection de l’ID d’application](sandboxing-images/sign07.png "sélection de l’ID d’application")](sandboxing-images/sign07-large.png#lightbox)
8. Sélectionnez les développeurs pour ce profil : 

    [![Les développeurs ajout](sandboxing-images/sign08.png "aux développeurs d’ajout")](sandboxing-images/sign08-large.png#lightbox)
9. Sélectionnez les ordinateurs pour ce profil : 

    [![Sélection des ordinateurs autorisés](sandboxing-images/sign09.png "sélection des ordinateurs autorisés")](sandboxing-images/sign09-large.png#lightbox)
10. Nommez le profil : 

    [![Attribution d’un nom du profil](sandboxing-images/sign10.png "donnant le profil d’un nom")](sandboxing-images/sign10-large.png#lightbox)
11. Cliquez sur le **fait** bouton.

> [!IMPORTANT]
> Dans certains cas vous devrez peut-être télécharger le nouveau profil de provisionnement à partir du portail des développeurs d’Apple directement et double-cliquez dessus pour l’installer. Vous devrez peut-être également arrêter et redémarrer Visual Studio pour Mac avant d’être en mesure d’accéder au nouveau profil.

Ensuite, nous devons charger le nouvel ID d’application et le même profil sur l’ordinateur de développement. Nous allons effectuer les opérations suivantes :

1. Démarrez Xcode et sélectionnez **préférences** à partir de la **Xcode** menu : 

    ![Modification des comptes dans Xcode](sandboxing-images/sign11.png "modification des comptes dans Xcode")
2. Cliquez sur le **afficher les détails...**  bouton : 

    ![En cliquant sur le bouton Afficher les détails](sandboxing-images/sign12.png "en cliquant sur le bouton Afficher les détails")
3. Cliquez sur le **Actualiser** bouton (dans le coin inférieur gauche).
4. Cliquez sur le **fait** bouton.

Ensuite, nous devons sélectionner le nouvel ID d’application et le même profil de provisionnement dans notre projet Xamarin.Mac. Nous allons effectuer les opérations suivantes :

1. Dans le **panneau solutions**, double-cliquez sur le **Info.plist** fichier à ouvrir pour modification.
2. Vérifiez que le **identificateur de Bundle** correspond à notre ID d’application que nous avons créé ci-dessus (exemple : `com.appracatappra.MacSandbox`) : 

    [![Modification de l’identificateur de Bundle](sandboxing-images/sign13.png "modification de l’identificateur de Bundle")](sandboxing-images/sign13-large.png#lightbox)
3. Ensuite, double-cliquez sur le **Entitlements.plist** de fichiers et de vous assurer notre **iCloud clé-valeur Store** et **conteneurs iCloud** correspondent tous notre ID d’application que nous avons créé ci-dessus (exemple : `com.appracatappra.MacSandbox`): 

    [![Modification du fichier Entitlements.plist](sandboxing-images/sign17.png "modifiant le fichier Entitlements.plist")](sandboxing-images/sign17-large.png#lightbox)
4. Enregistrez les modifications apportées.
5. Dans le **panneau solutions**, double-cliquez sur le fichier projet pour ouvrir ses Options pour la modification :  

    ![Editign les options de la solution](sandboxing-images/sign14.png "Editign les options de la solution")
6. Sélectionnez **signature Mac**, puis vérifiez **signer le bundle d’application** et **signer le package de programme d’installation**. Sous **profil de provisionnement**, sélectionnez celui que nous avons créé ci-dessus : 

    ![Définition du profil de provisionnement](sandboxing-images/sign15.png "définissant le profil d’approvisionnement")
7. Cliquez sur le **fait** bouton.

> [!IMPORTANT]
> Vous devrez peut-être quitter et redémarrer Visual Studio pour Mac pour l’obtenir afin qu’il reconnaisse le nouvel ID d’application et profil de provisionnement qui a été installée par Xcode apparaissent.

#### <a name="troubleshooting-provisioning-issues"></a>Résolution des problèmes d’approvisionnement

À ce stade, vous devez essayer exécuter l’application et vous assurer que tout est connecté et correctement configuré. Si l’application continue de s’exécuter comme avant, tout fonctionne correctement. En cas de défaillance, vous risquez d’obtenir une boîte de dialogue semblable à celui-ci :

[![Un exemple de mise en service de la boîte de dialogue problème](sandboxing-images/sign16.png "un exemple de boîte de dialogue de problème d’approvisionnement")](sandboxing-images/sign16-large.png#lightbox)

Voici les causes les plus courantes de configuration et de problèmes de signature :

- L’ID d’offre groupée ne correspond pas à l’ID d’application du profil sélectionné.
- L’ID de développeur ne correspond pas à l’ID de développeur du profil sélectionné.
- L’UUID du testées sur Mac n’est pas inscrit dans le cadre du profil sélectionné.

Dans le cas d’un problème, corrigez le problème sur le portail des développeurs Apple, actualiser les profils dans Xcode et effectuer un nettoyage de la build dans Visual Studio pour Mac.

### <a name="enable-the-app-sandbox"></a>Activer le bac à sable d’application

Vous activez le bac à sable d’application en sélectionnant une case à cocher dans vos options projets. Effectuez ce qui suit :

1. Dans le **panneau solutions**, double-cliquez sur le **Entitlements.plist** fichier à ouvrir pour modification.
2. Vérifiez les deux **activer les droits** et **permettre le Sandboxing d’application**: 

    [![Modification des droits et l’activation de sandboxing](sandboxing-images/sign17.png "droits de modification et l’activation de sandboxing")](sandboxing-images/sign17-large.png#lightbox)
3. Enregistrez les modifications apportées.

À ce stade, vous avez activé le bac à sable d’application, mais vous n’avez pas fourni l’accès réseau requise pour l’affichage Web. Si vous exécutez l’application maintenant, vous devez obtenir une fenêtre vide :

[![Montrant l’accès web bloqué](sandboxing-images/sample08.png "montrant l’accès web bloqué")](sandboxing-images/sample08-large.png#lightbox)

### <a name="verifying-that-the-app-is-sandboxed"></a>Vérification de l’application sable

À part la ressource de blocage de comportement, il existe trois méthodes principales pour indiquer si une application Xamarin.Mac a été correctement sable :

1. Dans le Finder, vérifier le contenu de la `~/Library/Containers/` dossier - si l’application est sable, il y aura un dossier nommé comme identificateur de Bundle de votre application (exemple : `com.appracatappra.MacSandbox`) : 

    [![Ouverture du bundle de l’application](sandboxing-images/sample09.png "ouvrant le bundle de l’application")](sandboxing-images/sample09-large.png#lightbox)
2. Le système considère l’application comme sandbox dans le moniteur d’activité :
    - Lancer le moniteur d’activité (sous `/Applications/Utilities`). 
    - Choisissez **vue** > **colonnes** et vérifiez que le **bac à sable** élément de menu est activé.
    - Vérifiez que la colonne de bac à sable lit `Yes` pour votre application : 

    [![La vérification de l’application dans le moniteur d’activité](sandboxing-images/sample10.png "la vérification de l’application dans le moniteur d’activité")](sandboxing-images/sample10-large.png#lightbox)
3. Vérifiez que l’application binaire est sable :
    - Démarrez l’application Terminal.
    - Accédez aux applications `bin` directory.
    - Exécutez cette commande : `codesign -dvvv --entitlements :- executable_path` (où `executable_path` est le chemin d’accès à votre application) : 

    [![La vérification de l’application sur la ligne de commande](sandboxing-images/sample11.png "la vérification de l’application sur la ligne de commande")](sandboxing-images/sample11-large.png#lightbox)

### <a name="debugging-a-sandboxed-app"></a>Débogage d’une application en sandbox

Le débogueur se connecte aux applications Xamarin.Mac via TCP, ce qui signifie que par défaut lorsque vous activez le sandboxing, il est impossible de se connecter à l’application, donc si vous essayez d’exécuter l’application sans les autorisations appropriées activées, vous obtenez une erreur *« Impossible de se connecter à le débogueur »* . 

[![Définir les options requises](sandboxing-images/debug01.png "définissant les options requises")](sandboxing-images/debug01-large.png#lightbox)

Le **autoriser les connexions réseau sortantes (Client)** autorisation est nécessaire pour que le débogueur, l’activation de celle-ci permet le débogage normalement. Étant donné que vous ne pouvez pas déboguer sans lui, nous avons mis à jour le `CompileEntitlements` cibler pour `msbuild` pour ajouter automatiquement cette autorisation pour les droits pour les builds de n’importe quelle application est sable pour le débogage uniquement. Versions Release doivent utiliser les droits spécifiés dans le fichier de droits, tels quels.

### <a name="resolving-an-app-sandbox-violation"></a>Résoudre une violation d’application Sandbox

Une Violation de bac à sable d’application se produit si une application a essayé d’accéder à une ressource qui a de Xamarin.Mac sandbox ne pas explicitement autorisé. Par exemple, notre vue Web n’est plus la possibilité d’afficher le site Web Apple.

La plus courante de Violations de bac à sable d’application se produit lorsque les paramètres de droits spécifiés dans Visual Studio pour Mac ne correspondent pas à la configuration requise de l’application. Là encore, en retour à notre exemple, la connexion réseau manquant droits qui empêchent l’affichage Web de fonctionner.

#### <a name="discovering-app-sandbox-violations"></a>Détection des violations de l’application Sandbox

Si vous suspectez une Violation de bac à sable d’application se produit dans votre application Xamarin.Mac, le moyen le plus rapide pour découvrir le problème est à l’aide de la **Console** application.

Effectuez ce qui suit :

1. Compilez l’application en question et exécutez-le à partir de Visual Studio pour Mac.
2. Ouvrez le **Console** application (à partir de `/Applications/Utilties/`).
3. Sélectionnez **tous les Messages** dans la barre latérale et entrez `sandbox` dans la recherche : 

    [![Un exemple d’un problème de sandboxing dans la console](sandboxing-images/resolve01.png "un exemple d’un problème de sandboxing dans la console")](sandboxing-images/resolve01-large.png#lightbox)

Pour notre exemple d’application ci-dessus, vous pouvez voir que le noyau ne bloque le `network-outbound` le trafic en raison de l’application bac à sable, étant donné que nous n’avons pas demandé ce droit.

#### <a name="fixing-app-sandbox-violations-with-entitlements"></a>Résolution des violations de droits App Sandbox

Maintenant que nous avons vu comment rechercher des Violations de bac à sable d’application, nous allons voir comment ils peuvent être résolus en ajustant les droits de notre application.

Effectuez ce qui suit :

1. Dans le **panneau solutions**, double-cliquez sur le **Entitlements.plist** fichier à ouvrir pour modification.
2. Sous le **droits** section, vérifiez le **autoriser les connexions réseau sortantes (Client)** case à cocher : 

    [![Modification des droits](sandboxing-images/sign17.png "modification des droits")](sandboxing-images/sign17-large.png#lightbox)
3. Enregistrez les modifications dans l’application.

Si nous effectuer les opérations ci-dessus pour notre exemple d’application, puis générer et l’exécuter, le contenu web apparaîtront désormais comme prévu.

## <a name="the-app-sandbox-in-depth"></a>Application bac à sable approfondie

Les mécanismes de contrôle d’accès fournis par le bac à sable d’application sont peu et facile à comprendre. Toutefois, la façon que le bac à sable d’application sera adoptée par chaque application est unique et en fonction des besoins de l’application.

Compte tenu des efforts pour protéger votre application Xamarin.Mac d’être attaqué par du code malveillant, est nécessaire uniquement une seule vulnérabilité dans soit l’application (ou une des bibliothèques ou des infrastructures qu’il consomme) pour contrôler les interactions de l’application avec le système.

Le bac à sable de l’application a été conçu pour empêcher la prise de contrôle (ou limiter les dommages que cela peut provoquer) en vous permettant de spécifier des interactions prévue de l’application avec le système. Le système accorde uniquement l’accès à la ressource dont votre application a besoin pour faire son travail et rien de plus.

Lorsque vous concevez pour le bac à sable d’application, vous concevez pour le pire des scénarios. Si l’application compromise par du code malveillant, il est limité à l’accès aux fichiers et aux ressources dans un bac à sable de l’application uniquement.

### <a name="entitlements-and-system-resource-access"></a>Droits et accès aux ressources de système

Comme nous l’avons vu, une application Xamarin.Mac qui n’a pas été sable est accordée les droits d’accès complets et l’accès de l’utilisateur qui exécute l’application. Si compromis par du code malveillant, une application non protégés peut agir en tant qu’agent pour le comportement hostile, avec une échelle allant potentiels pour causer des dommages.

En activant le bac à sable d’application, vous supprimez tous sauf un ensemble minimal de privilèges, que vous puis réactivez selon le besoin uniquement à l’aide de droits de votre application Xamarin.Mac. 

Vous modifiez des ressources de bac à sable d’application de votre application en modifiant son **Entitlements.plist** de fichiers et la vérification ou en sélectionnant les droits requis dans les zones de liste déroulante d’éditeurs :

[![Modification des droits](sandboxing-images/sign17.png "modification des droits")](sandboxing-images/sign17-large.png#lightbox)

### <a name="container-directories-and-file-system-access"></a>Répertoires de conteneur et d’accès au système de fichiers

Lorsque votre application Xamarin.Mac adopte la Sandbox de l’application, il a accès aux emplacements suivants :

- **Le répertoire de conteneur d’application** -lors de la première exécution, le système d’exploitation crée un spécial _répertoire conteneur_ où toutes ses ressources vont, que seulement il peut accéder. L’application aura un accès complet en lecture/écriture à ce répertoire.
- **Répertoires de conteneur de groupe application** -votre application peut accéder à un ou plusieurs _groupe conteneurs_ qui sont partagés entre applications dans le même groupe.
- **Fichiers spécifié par l’utilisateur** -votre application obtient automatiquement l’accès aux fichiers qui sont explicitement ouverte ou par glisser- déposer l’application par l’utilisateur.
- **Les éléments liés aux** -avec les droits appropriés, votre application peut avoir accès à un fichier portant le même nom mais une autre extension. Par exemple, un document qui a été enregistré à la fois comme un `.txt` fichier et un `.pdf`.
- **Répertoires temporaires, les répertoires de l’outil de ligne de commande et les emplacements de lecture spécifiques** -votre application a différents degrés de l’accès aux fichiers dans d’autres emplacements bien définis comme spécifié par le système.

#### <a name="the-app-container-directory"></a>Le répertoire de conteneur d’application

Répertoire de conteneur d’application d’une application Xamarin.Mac présente les caractéristiques suivantes :

- Il se trouve dans un emplacement masqué dans le répertoire de base de l’utilisateur (généralement `~Library/Containers`) et est accessible avec la `NSHomeDirectory` (fonction) (voir ci-dessous) au sein de votre application. Comme il est dans le répertoire d’accueil, chaque utilisateur obtient leur propre conteneur pour l’application.
- L’application dispose d’un accès en lecture/écriture illimité dans le répertoire de conteneur et tous ses sous-répertoires et les fichiers qu’il contient.
- La plupart des API de recherche de chemin d’accès de macOS est relatives à conteneur de l’application. Par exemple, le conteneur possède sa propre **bibliothèque** (accessible via `NSLibraryDirectory`), **prise en charge de l’Application** et **préférences** sous-répertoires.
- macOS établit et applique la connexion entre et application et son conteneur via la signature de code. Même est une autre application tente d’usurper l’identité de l’application à l’aide de son **identificateur de Bundle**, il ne sera pas en mesure d’accéder au conteneur en raison de la signature de code.
- Le conteneur n’est pas pour les fichiers générés par l’utilisateur. Au lieu de cela, il correspond aux fichiers que votre application utilise telles que les bases de données, les caches ou les autres types de données spécifiques.
- Pour _shoebox_ types d’applications (par exemple, application de photos d’Apple), contenu de l’utilisateur passe dans le conteneur.

> [!IMPORTANT]
> Malheureusement, Xamarin.Mac n’a aucun couverture d’API de 100 % encore (contrairement à Xamarin.iOS), par conséquent le `NSHomeDirectory` API n’a pas été mappé dans la version actuelle de Xamarin.Mac.

En tant que solution de contournement temporaire, vous pouvez utiliser le code suivant :

```csharp
[System.Runtime.InteropServices.DllImport("/System/Library/Frameworks/Foundation.framework/Foundation")]
public static extern IntPtr NSHomeDirectory();

public static string ContainerDirectory {
    get {
        return ((NSString)ObjCRuntime.Runtime.GetNSObject(NSHomeDirectory())).ToString ();
        }
}
```

#### <a name="the-app-group-container-directory"></a>Le répertoire de conteneur de groupe application

À partir de Mac, macOS 10.7.5 (et version ultérieure), une application peut utiliser le `com.apple.security.application-groups` habilité à accéder à un conteneur partagé qui est commun à toutes les applications dans le groupe. Vous pouvez utiliser ce conteneur partagé pour le contenu en vis-à-vis de non-utilisateur telles que la base de données ou d’autres types de fichiers de prise en charge (tels que les caches).

Les conteneurs de groupe sont automatiquement ajoutés à bac à sable conteneur chaque application (si elles font partie d’un groupe) et sont stockés à `~/Library/Group Containers/<application-group-id>`. L’ID de groupe _doit_ commencer avec votre ID d’équipe de développement et d’une période, par exemple :

```plist
<team-id>.com.company.<group-name>
```

Pour plus d’informations, consultez le site d’Apple [Ajout d’une Application à un groupe d’applications](https://developer.apple.com/library/prerelease/mac/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19) dans [clé de référence sur les droits](https://developer.apple.com/library/prerelease/mac/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/AboutEntitlements.html#//apple_ref/doc/uid/TP40011195).

#### <a name="powerbox-and-file-system-access-outside-of-the-app-container"></a>Powerbox et l’accès au système en dehors du conteneur d’application

Une application Xamarin.Mac sandbox peut accéder aux emplacements de système de fichiers en dehors de son conteneur comme suit :

- À l’initiative spécifique de l’utilisateur (via Open et boîtes de dialogue Enregistrer ou autres méthodes telles que la fonction glisser- déposer).
- À l’aide de droits pour les emplacements de système de fichier spécifique (tel que `/bin` ou `/usr/lib`).
* Lorsque l’emplacement de système de fichiers est dans certains répertoires sont world accessible en lecture (par exemple, le partage).

_Powerbox_ est une technologie de sécurité qui interagit avec l’utilisateur pour développer des droits d’accès de votre application Xamarin.Mac sandbox fichier macOS. Powerbox n’a aucune API, mais il est activé de manière transparente lorsque l’application appelle une `NSOpenPanel` ou `NSSavePanel`. Accès de Powerbox est activé via les droits que vous définissez pour votre application Xamarin.Mac.

Lorsqu’une application en sandbox affiche une ouverture ou la boîte de dialogue Enregistrer, la fenêtre est présentée par Powerbox (et pas AppKit) et a donc accès à n’importe quel fichier ou le répertoire que l’utilisateur a accès à.

Lorsqu’un utilisateur sélectionne un fichier ou répertoire à partir de l’ouverture ou boîte de dialogue Enregistrer (ou fait glisser soit sur l’icône de l’application), Powerbox ajoute le chemin d’accès associé au bac à sable de l’application.

En outre, le système autorise automatiquement le code suivant à une application en sandbox :

- Connexion à une méthode d’entrée du système.
- Appeler des services sélectionnés par l’utilisateur à partir de la **Services** menu (uniquement pour les services marqués en tant que _sécurisés pour les applications de bac à sable_ par le fournisseur de services).
- Choisissez des fichiers ouverts par l’utilisateur à partir de la **Open récente** menu.
- Utilisez la copier et coller entre les autres applications.
- Lire des fichiers à partir des emplacements de lecture suivantes :
    - `/bin`
    - `/sbin`
    - `/usr/bin`
    - `/usr/lib`
    - `/usr/sbin`
    - `/usr/share`
    - `/System`
- Lire et écrire des fichiers dans les répertoires créés par `NSTemporaryDirectory`.

Comme valeur par défaut, les fichiers ouverts ou enregistrés par une application Xamarin.Mac sable restent accessibles jusqu'à ce que l’application s’arrête (sauf si le fichier a été ouvert lors de l’application se ferme). Fichiers ouverts va être restaurés automatiquement au bac à sable de l’application via la fonctionnalité de reprise de macOS au prochain que démarrage de l’application.

Pour assurer la persistance aux fichiers situés en dehors du conteneur d’une application Xamarin.Mac, utiliser des signets étendue de sécurité (voir ci-dessous).

#### <a name="related-items"></a>Éléments connexes

Le bac à sable d’application permet à une application à accéder aux éléments associés qui ont le même nom de fichier, mais différentes extensions. La fonctionnalité comporte deux parties : a) une liste des extensions associées dans l’application `Info.plst` fichier, b) du code pour indiquer le bac à sable à ce que l’application va faire avec ces fichiers.

Il existe deux scénarios où cela s’applique :

1. L’application doit être en mesure d’enregistrer une version différente du fichier (avec une nouvelle extension). Par exemple, exporter un `.txt` de fichiers à un `.pdf` fichier. Pour gérer cette situation, vous devez utiliser un `NSFileCoordinator` pour accéder au fichier. Vous devez appeler la `WillMove(fromURL, toURL)` méthode tout d’abord, déplacez le fichier vers la nouvelle extension, puis appelez `ItemMoved(fromURL, toURL)`.
2. L’application doit ouvrir un fichier avec une extension principal et plusieurs fichiers de prise en charge avec différentes extensions. Par exemple, un film et un fichier de sous-titres. Utilisez un `NSFilePresenter` pour accéder au fichier secondaire. Fournissez le fichier principal pour le `PrimaryPresentedItemURL` propriété et le fichier secondaire pour le `PresentedItemURL` propriété. Lorsque le fichier principal est ouvert, appelez le `AddFilePresenter` méthode de la `NSFileCoordinator` classe pour enregistrer le fichier secondaire.

Dans des deux scénarios, l’application **Info.plist** fichier doit déclarer les Types de documents pouvant être ouvertes par l’application. Pour tout type de fichier, ajoutez le `NSIsRelatedItemType` (avec la valeur `YES`) à son entrée dans le `CFBundleDocumentTypes` tableau.

#### <a name="open-and-save-dialog-behavior-with-sandboxed-apps"></a>Ouvrir et enregistrer le comportement de la boîte de dialogue avec les applications sable

Les limites suivantes sont placés sur le `NSOpenPanel` et `NSSavePanel` lorsque vous les appelez à partir d’une application Xamarin.Mac sable :

- Vous ne pouvez pas appeler par programme la **OK** bouton.
- Vous ne pouvez pas modifier par programmation de sélection d’un utilisateur dans un `NSOpenSavePanelDelegate`.

En outre, les modifications de l’héritage suivantes sont en place :

- **Application non sandbox** - `NSOpenPanel` `NSSavePanel``NSPanel``NSWindow``NSResponder``NSObject``NSOpenPanel``NSSavePanel``NSObject``NSOpenPanel``NSSavePanel`

### <a name="security-scoped-bookmarks-and-persistent-resource-access"></a>Signets étendue de sécurité et accès aux ressources persistants

Comme indiqué ci-dessus, une application Xamarin.Mac sandbox. vous pouvez accéder à un fichier ou une ressource en dehors de son conteneur au moyen d’interaction directe de l’utilisateur (comme indiqué par PowerBox). Toutefois, accès à ces ressources n’est pas automatiquement persistante entre les lancements d’applications ou le système redémarre.

À l’aide de _Security-Scoped signets_, une application Xamarin.Mac sandbox. vous pouvez conserver l’intention de l’utilisateur et conserver l’accès au compte tenu des ressources après une application redémarrer.

#### <a name="security-scoped-bookmark-types"></a>Types de sécurité à portée de signet

Lorsque vous travaillez avec des signets de Security-Scoped et de l’accès aux ressources persistantes, il existe deux cas d’utilisation sistine :

- **Un signet App-Scoped fournit un accès permanent à un fichier spécifié par l’utilisateur ou un dossier.** 

    Par exemple, si l’application Xamarin.Mac sandbox. vous autorise à utiliser pour ouvrir un document externe pour la modification (à l’aide un `NSOpenPanel`), l’application peut créer un signet App-Scoped afin qu’il puisse accéder à l’avenir le même fichier.
- **Un signet Document-Scoped fournit un accès permanent d’un document spécifique dans un fichier secondaire.** 

Par exemple, une application d’édition vidéo qui crée un fichier projet qui a accès à des images individuelles, des clips vidéo et des fichiers audio qui seront plus tard être combinées en une séquence unique.

Lorsque l’utilisateur importe un fichier de ressources dans le projet (via un `NSOpenPanel`), l’application crée un signet Document-Scoped pour l’élément qui est stocké dans le projet, afin que le fichier est toujours accessible à l’application.

Un signet Document-Scoped peut être résolu par n’importe quelle application capable d’ouvrir les données de signet et le document lui-même. Cela prend en charge la portabilité, permettant à l’utilisateur envoyer les fichiers de projet à un autre utilisateur et avoir tous les signets fonctionnent aussi bien pour eux.

> [!IMPORTANT]
> Un signet Document-Scoped pouvez _uniquement_ pointe vers un fichier unique et non un dossier et que ce fichier ne peut pas être dans un emplacement que celui utilisé par le système (tel que `/private` ou `/Library`).

#### <a name="using-security-scoped-bookmarks"></a>À l’aide de la sécurité à portée de signets

À l’aide de ces deux types de Security-Scoped signet, vous oblige à effectuer les étapes suivantes :

1. **Définir les droits appropriés dans l’application Xamarin.Mac que doit utiliser des signets de Security-Scoped** -For App-Scoped signets, définissez le `com.apple.security.files.bookmarks.app-scope` clé droit à `true`. Pour les signets Document-Scoped, définissez le `com.apple.security.files.bookmarks.document-scope` clé droit à `true`.
2. **Créer un signet Security-Scoped** -vous effectuerez cette pour tout fichier ou dossier fournis par l’utilisateur a accès à (via `NSOpenPanel` par exemple), que l’application a besoin d’un accès permanent aux. Utilisez le `public virtual NSData CreateBookmarkData (NSUrlBookmarkCreationOptions options, string[] resourceValues, NSUrl relativeUrl, out NSError error)` méthode de la `NSUrl` classe pour créer le signet.
3. **Résoudre le signet Security-Scoped** - lorsque l’application doit accéder à la ressource à nouveau (par exemple, après redémarrage) elle devra résoudre le signet vers une URL de l’étendue de sécurité. Utilisez le `public static NSUrl FromBookmarkData (NSData data, NSUrlBookmarkResolutionOptions options, NSUrl relativeToUrl, out bool isStale, out NSError error)` méthode de la `NSUrl` classe pour résoudre le signet.
4. **Notifier explicitement le système que vous souhaitez accéder au fichier à partir de l’URL Security-Scoped** : cette étape doit être effectuée immédiatement après l’obtention de l’URL Security-Scoped ci-dessus ou, lorsque vous décidez de récupérer l’accès à la ressource après avoir libérées votre accès à celui-ci. Appelez le `StartAccessingSecurityScopedResource ()` méthode de la `NSUrl` classe pour accéder à une URL Security-Scoped.
5. **Notifier explicitement le système que vous avez terminé l’accès au fichier à partir de l’URL Security-Scoped** -dès que possible, vous devez informer le système lors de l’application n’a plus besoin accès au fichier (par exemple, si l’utilisateur la ferme). Appelez le `StopAccessingSecurityScopedResource ()` méthode de la `NSUrl` classe pour arrêter l’accès à une URL Security-Scoped.

Une fois que vous avez annulé l’accès à une ressource, vous devrez revenir à l’étape 4 pour rétablir l’accès. Si l’application Xamarin.Mac est redémarrée, vous devez revenir à l’étape 3 et de résoudre le signet.

> [!IMPORTANT]
> Libérer l’accès aux ressources Security-Scoped URL provoquera une application Xamarin.Mac une fuite des ressources du noyau. Par conséquent, l’application sera n’est plus en mesure d’ajouter des emplacements de système de fichiers à son conteneur jusqu'à ce qu’il soit redémarré.

### <a name="the-app-sandbox-and-code-signing"></a>L’application Sandbox et la signature de code

Après avoir activé le bac à sable d’application et que vous activez les exigences spécifiques de votre application Xamarin.Mac (par le biais de droits), vous devez signer le projet pour le sandboxing entrent en vigueur le code. Vous devez effectuer, car les droits nécessaires pour l’application sandbox sont liés à la signature de l’application de la signature de code. 

macOS applique un lien entre conteneur d’une application et sa signature de code de cette façon, aucune autre application ne peut accéder à ce conteneur, même s’il falsifie l’ID d’offre groupée d’applications Ce mécanisme fonctionne comme suit :

1. Lorsque le système crée conteneur l’application, il définit un _liste de contrôle d’accès_ (ACL) sur ce conteneur. L’entrée de contrôle d’accès initial dans la liste contient l’application _exigence désigné_ (DR), qui décrit comment les futures versions de l’application peut être reconnu (lorsqu’il a été mis à niveau).
2. Chaque fois une application avec le même ID de Bundle démarre, le système vérifie que signature de code de l’application correspond aux critères désigné spécifiée dans une des entrées dans l’ACL du conteneur. Si le système ne trouve pas de correspondance, il empêche le lancement de l’application.

Code de signature fonctionne comme suit :

1. Avant de créer le projet Xamarin.Mac, obtenir un certificat de développement, un certificat de Distribution et un certificat d’ID de développeur à partir du portail des développeurs Apple.
2. Lorsque le Store d’application Mac distribue l’application Xamarin.Mac, il est signé avec une signature de code d’Apple.

Lorsque le test et de débogage, vous allez utiliser une version de l’application Xamarin.Mac que vous êtes connecté (qui sera utilisé pour créer le conteneur d’application). Ultérieurement, si vous souhaitez tester ou d’installer la version à partir du Store d’application Apple, il est signée avec la signature Apple et ne parviendra pas à lancer (dans la mesure où il n’a pas la même signature de code que le conteneur d’application d’origine). Dans ce cas, vous obtenez un rapport d’incidents similaires à ce qui suit :

```csharp
Exception Type:  EXC_BAD_INSTRUCTION (SIGILL)
```

Pour résoudre ce problème, vous devrez ajuster l’entrée de liste ACL pour pointer vers la version signée Apple de l’application.

Pour plus d’informations sur la création et téléchargement des profils de provisionnement nécessaires pour le bac à sable, consultez le [signature et provisionnement de l’application](#Signing_and_Provisioning_the_App) section ci-dessus.

#### <a name="adjusting-the-acl-entry"></a>Réglage de l’entrée ACL

Pour permettre à la version signée Apple de l’exécution de l’application Xamarin.Mac, procédez comme suit :

1. Ouvrez l’application Terminal (dans `/Applications/Utilities`).
2. Ouvrez une fenêtre de recherche pour la version signée Apple de l’application Xamarin.Mac.
3. Type `asctl container acl add -file` dans la fenêtre de Terminal.
4. Faites glisser l’icône d’application Xamarin.Mac à partir de la fenêtre du Finder et déposez-la sur la fenêtre de Terminal.
5. Le chemin d’accès complet au fichier sera ajouté à la commande dans le Terminal.
6. Appuyez sur **entrée** pour exécuter la commande.

L’ACL du conteneur contient désormais les exigences de code désigné pour les deux versions de l’application Xamarin.Mac et macOS autorisent désormais des versions à exécuter.

#### <a name="display-a-list-of-acl-code-requirements"></a>Afficher une liste ACL d’exigences de code

Vous pouvez afficher une liste des exigences de code dans la liste ACL d’un conteneur en procédant comme suit :

1. Ouvrez l’application Terminal (dans `/Applications/Utilities`).
2. Tapez `asctl container acl list -bundle <container-name>`.
3. Appuyez sur **entrée** pour exécuter la commande.

Le `<container-name>` est généralement l’identificateur de Bundle de l’application Xamarin.Mac.

## <a name="designing-a-xamarinmac-app-for-the-app-sandbox"></a>Conception d’une application Xamarin.Mac pour le bac à sable d’application

Il existe un flux de travail courant qui doit être suivie lors de la conception d’une application Xamarin.Mac pour le bac à sable de l’application. Ceci dit, les spécificités de l’implémentation de sandboxing dans une application vont être unique à la fonctionnalité de l’application donnée.

### <a name="six-steps-for-adopting-the-app-sandbox"></a>Six étapes pour adopter le bac à sable d’application

Conception d’une application Xamarin.Mac pour le bac à sable App généralement se compose des étapes suivantes :

1. Déterminer si l’application est adaptée à sandboxing.
2. Concevoir une stratégie de développement et de distribution.
3. Résoudre les incompatibilités d’API.
4. Appliquer les droits de bac à sable d’application requis pour le projet Xamarin.Mac.
5. Ajoutez la séparation des privilèges à l’aide de XPC.
6. Implémenter une stratégie de migration.

> [!IMPORTANT]
> Vous devez non seulement le sandbox du fichier exécutable principal dans vous offre groupée d’applications, mais également chaque helper inclus application ou un outil de cette offre groupée. Cela est requis pour toutes les applications distribuées à partir du Store d’applications Mac et, si possible, doit être effectuée pour toute autre forme de distribution d’applications.

Pour obtenir la liste de tous les fichiers binaires exécutables dans une offre groupée d’une application Xamarin.Mac, tapez la commande suivante dans Terminal :

```bash
find -H [Your-App-Bundle].app -print0 | xargs -0 file | grep "Mach-O .*executable"
```

Où `[Your-App-Bundle]` est le nom et le chemin d’accès au bundle de l’application.

### <a name="determine-whether-a-xamarinmac-app-is-suitable-for-sandboxing"></a>Déterminer si une application Xamarin.Mac peut être sandboxing

La plupart des applications Xamarin.Mac sont entièrement compatibles avec le bac à sable d’application et par conséquent, approprié pour le bac à sable. Si l’application requiert un comportement qui n’autorise pas le bac à sable d’application, vous devez envisager une approche alternative.

Si votre application nécessite l’une des comportements suivants, il est incompatible avec le bac à sable d’application :

- **Services d’autorisation** -avec l’application bac à sable, vous ne fonctionne pas avec les fonctions décrites dans [d’autorisation Services C référence](https://developer.apple.com/library/prerelease/mac/documentation/Security/Reference/authorization_ref/index.html#//apple_ref/doc/uid/TP30000826).
- **API d’accessibilité** -vous ne pouvez pas les applications d’assistance de bac à sable tels que les lecteurs d’écran ou des applications qui contrôlent les autres applications.
- **Envoyer des événements Apple pour les applications arbitraires** -si l’application requiert l’envoi d’événements Apple pour une application inconnue, arbitraire, il ne peut pas être sandbox. Pour une liste d’applications appelées, l’application peut toujours être sable et les droits doit inclure la liste d’applications appelé.
- **Envoyer des dictionnaires des informations utilisateur dans les Notifications distribuées à d’autres tâches** -avec l’application bac à sable, vous ne pouvez pas inclure un `userInfo` dictionnaire lors de la validation à un `NSDistributedNotificationCenter` objet pour d’autres tâches de messagerie.
- **Chargement des Extensions de noyau** -le chargement des Extensions de noyau est interdite par le bac à sable de l’application.
- **Simulation d’entrée utilisateur dans ouvrir et enregistrer les boîtes de dialogue** : par programmation manipulation ouvrir ou enregistrer des boîtes de dialogue pour simuler ou de modifier l’entrée utilisateur est interdite par le bac à sable d’application.
- **Accès ou la définition des préférences dans d’autres applications** -manipuler les paramètres d’autres applications est interdite par le bac à sable de l’application.
- **Configuration des paramètres réseau** -manipuler les paramètres de réseau est interdite par le bac à sable de l’application.
- **Arrêt d’autres applications** -le bac à sable d’application interdit à l’aide de `NSRunningApplication` pour terminer d’autres applications.

### <a name="resolving-api-incompatibilities"></a>Résolution des incompatibilités de l’API

Lorsque vous concevez une application Xamarin.Mac pour le bac à sable d’application, vous pouvez rencontrer des incompatibilités avec l’utilisation de certaines API macOS.

Voici quelques problèmes courants et choses que vous pouvez faire pour résoudre ces problèmes :

- **Ouverture, l’enregistrement et le suivi de Documents** : Si vous gérez des documents à l’aide de n’importe quelle technologie autre que `NSDocument`, vous devez basculer vers elle en raison de la prise en charge intégrée pour le bac à sable App. `NSDocument` fonctionne avec PowerBox et prend en charge la conservation des documents au sein de votre bac à sable si l’utilisateur les déplace dans le Finder automatiquement.
- **Conserver l’accès aux ressources de système de fichiers** : si le Xamarin.Mac application dépend d’un accès permanent aux ressources en dehors de son conteneur, utilisez des signets étendue de sécurité pour maintenir l’accès.
- **Créer un élément de connexion pour une application** -avec l’application bac à sable, vous ne pouvez créer un élément à l’aide de compte de connexion `LSSharedFileList` ni vous pouvez manipuler l’état des services de lancement à l’aide de `LSRegisterURL`. Utilisez le `SMLoginItemSetEnabled` comme décrit dans les pommes [Ajout d’éléments de la connexion à l’aide de l’infrastructure de gestion de Service](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingLoginItems.html#//apple_ref/doc/uid/10000172i-SW5-SW1) documentation.
- **Accès aux données utilisateur** : Si vous utilisez des fonctions POSIX tel que `getpwuid` pour obtenir le répertoire de base à partir des services d’annuaire, envisagez d’utiliser des symboles Cocoa ou Core Foundation comme `NSHomeDirectory`.
- **Les préférences d’autres applications de l’accès à** - parce que le bac à sable App dirige le chemin d’accès-recherche des API au conteneur de l’application, modification les préférences intervient dans ce conteneur et d’accéder à un autre préférences d’applications dans ne pas autorisé. 
- **À l’aide de la vidéo incorporée de HTML5 dans des vues Web** -si l’application Xamarin.Mac utilise WebKit pour lire des vidéos HTML5 incorporées, vous devez également lier l’application par rapport à l’infrastructure AV Foundation. Le bac à sable application empêchera CoreMedia play ces vidéos sinon.

### <a name="applying-required-app-sandbox-entitlements"></a>Application des droits de bac à sable d’application requis

Vous devez modifier les droits pour n’importe quelle application Xamarin.Mac que vous souhaitez exécuter dans le bac à sable d’application et vérifier le **permettre le Sandboxing application** case à cocher.

Selon les fonctionnalités de l’application, vous devrez peut-être activer d’autres droits accéder aux ressources ou des fonctionnalités de système d’exploitation. Application Sandboxing fonctionne mieux lorsque vous réduisez les droits que vous demander à ce au minimum requis pour exécuter votre application activent donc au hasard de droits.

Pour déterminer les droits nécessite une application Xamarin.Mac, procédez comme suit :

1. Activer le bac à sable d’application et exécuter l’application Xamarin.Mac.
2. Exécuter via les fonctionnalités de l’application.
3. Ouvrez l’application de Console (disponible dans `/Applications/Utilities`) et recherchez `sandboxd` violations dans les **tous les Messages** journal.
4. Pour chaque `sandboxd` violation, résoudre le problème en utilisant le conteneur d’application plutôt que d’autres emplacements de système de fichiers ou appliquer des droits de l’application Sandbox pour activer l’accès aux fonctionnalités de système d’exploitation restreintes.
5. Exécutez de nouveau et tester de nouveau toutes les fonctionnalités d’application Xamarin.Mac.
6. Répétition jusqu'à ce que tous `sandboxd` violations ont été résolues.

### <a name="add-privilege-separation-using-xpc"></a>Ajouter la séparation des privilèges à l’aide de XPC

Lorsque vous développez une application Xamarin.Mac pour le bac à sable d’application, examinez les comportements de l’application dans les conditions d’accès et de privilèges, puis prendre en compte la séparation des opérations à haut risque dans leurs propres services XPC.

Pour plus d’informations, consultez d’Apple [création de Services XPC](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingXPCServices.html#//apple_ref/doc/uid/10000172i-SW6) et [Guide de programmation de Services et les démons](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/Introduction.html#//apple_ref/doc/uid/10000172i).

### <a name="implement-a-migration-strategy"></a>Implémenter une stratégie de migration

Si vous lancez une nouvelle version d’une application Xamarin.Mac qui n’était pas précédemment sable sable, vous devez vous assurer que les utilisateurs actuels ont un chemin d’accès de mise à niveau sans heurts. 

 Pour plus d’informations sur la façon d’implémenter un manifeste de migration du conteneur, lisez d’Apple [migration d’une application vers un bac à sable](https://developer.apple.com/library/prerelease/mac/documentation/Security/Conceptual/AppSandboxDesignGuide/MigratingALegacyApp/MigratingAnAppToASandbox.html#//apple_ref/doc/uid/TP40011183-CH6-SW1) documentation.

## <a name="summary"></a>Récapitulatif

Cet article a examiné en détail à sandboxing une application Xamarin.Mac. Tout d’abord, nous avons créé une application Xamarin.Mac simplement pour afficher les principes fondamentaux de l’application Sandbox. Ensuite, nous vous avons montré comment résoudre les violations de bac à sable. Ensuite, nous avons explique en détail examiner le bac à sable d’application et enfin, nous avons étudié la conception d’une application Xamarin.Mac pour le bac à sable de l’application.



## <a name="related-links"></a>Liens associés

- [Publication dans l’App Store](~/mac/deploy-test/publishing-to-the-app-store/index.md)
- [À propos de l’application Sandbox](https://developer.apple.com/library/content/documentation/Security/Conceptual/AppSandboxDesignGuide/AboutAppSandbox/AboutAppSandbox.html)
- [Problèmes courants de Sandboxing d’application](https://developer.apple.com/library/content/qa/qa1773/_index.html)
