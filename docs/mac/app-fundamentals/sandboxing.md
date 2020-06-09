---
title: Bac à sable (sandbox) d’une application Xamarin. Mac
description: Cet article traite de la mise en sandbox d’une application Xamarin. Mac pour la publication sur l’App Store. Il couvre tous les éléments qui entrent dans le sandboxing, tels que les répertoires de conteneurs, les habilitations, les autorisations déterminées par l’utilisateur, la séparation des privilèges et la mise en œuvre du noyau.
ms.prod: xamarin
ms.assetid: 06A2CA8D-1E46-410F-8C31-00EA36F0735D
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: eab0fbb6be313a2ca193b50a484b48d5b34f5fde
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84566058"
---
# <a name="sandboxing-a-xamarinmac-app"></a>Bac à sable (sandbox) d’une application Xamarin. Mac

_Cet article traite de la mise en sandbox d’une application Xamarin. Mac pour la publication sur l’App Store. Il couvre tous les éléments qui entrent dans le sandboxing, tels que les répertoires de conteneurs, les habilitations, les autorisations déterminées par l’utilisateur, la séparation des privilèges et la mise en œuvre du noyau._

## <a name="overview"></a>Vue d’ensemble

Lorsque vous travaillez avec C# et .NET dans une application Xamarin. Mac, vous avez la même possibilité de bac à sable (sandbox) d’une application que lorsque vous travaillez avec Objective-C ou SWIFT.

[![Exemple d’application en cours d’exécution](sandboxing-images/intro01.png "Exemple d’application en cours d’exécution")](sandboxing-images/intro01-large.png#lightbox)

Dans cet article, nous allons aborder les bases de l’utilisation de la mise en sandbox dans une application Xamarin. Mac et tous les éléments qui entrent dans le sandboxing : répertoires de conteneurs, droits, autorisations déterminées par l’utilisateur, séparation des privilèges et application du noyau. Nous vous recommandons vivement d’utiliser l’article [Hello, Mac](~/mac/get-started/hello-mac.md) , en particulier la [Présentation de Xcode et Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) et les sections [actions et actions](~/mac/get-started/hello-mac.md#outlets-and-actions) , car il aborde les concepts et les techniques clés que nous allons utiliser dans cet article.

Vous pouvez également jeter un coup d’œil sur la section [exposition des classes/méthodes C# à Objective-C](~/mac/internals/how-it-works.md) du document [Internals Xamarin. Mac.](~/mac/internals/how-it-works.md) elle explique également les `Register` `Export` attributs et utilisés pour relier vos classes C# aux objets objective-c et aux éléments d’interface utilisateur.

## <a name="about-the-app-sandbox"></a>À propos du bac à sable (sandbox) d’application

Le bac à sable de l’application fournit une protection renforcée contre les dommages qui peuvent être causés par l’exécution d’une application malveillante sur un Mac en limitant l’accès des ressources système à une application.

Une application non bac à sable (sandbox) possède les droits complets de l’utilisateur qui exécute l’application et peut accéder à ou faire tout ce que l’utilisateur peut. Si l’application contient des failles de sécurité (ou toute infrastructure qu’elle utilise), un pirate peut potentiellement exploiter ces faiblesses et utiliser l’application pour prendre le contrôle du Mac sur lequel elle s’exécute.

En limitant l’accès aux ressources pour chaque application, une application bac à sable (sandbox) fournit une ligne de défense contre le vol, l’endommagement ou l’intention malveillante de la part d’une application exécutée sur l’ordinateur de l’utilisateur.

Le bac à sable de l’application est une technologie de contrôle d’accès intégrée à macOS (appliquée au niveau du noyau) qui fournit une stratégie double :

1. Le bac à sable de l’application permet au développeur de décrire la _manière dont_ une application interagit avec le système d’exploitation et, de cette façon, il ne reçoit que les droits d’accès nécessaires pour effectuer le travail, ni plus.
2. Le bac à sable de l’application permet à l’utilisateur d’accorder en toute transparence un accès au système via les boîtes de dialogue Ouvrir et enregistrer, les opérations de glisser-déplacer et d’autres, les interactions utilisateur courantes.

### <a name="preparing-to-implement-the-app-sandbox"></a>Préparation de l’implémentation du bac à sable (sandbox) d’application

Les éléments du bac à sable de l’application qui seront abordés en détail dans l’article sont les suivants :

- Répertoires de conteneurs
- Droits
- Autorisations déterminées par l’utilisateur
- Séparation des privilèges
- Application du noyau

Une fois que vous avez compris ces détails, vous pouvez créer un plan pour l’adoption du bac à sable (sandbox) de l’application dans votre application Xamarin. Mac.

Tout d’abord, vous devez déterminer si votre application est un bon candidat pour le sandboxing (la plupart des applications sont). Ensuite, vous devrez résoudre toutes les incompatibilités d’API et déterminer les éléments du bac à sable (sandbox) de l’application dont vous aurez besoin. Enfin, examinez l’utilisation de la séparation des privilèges pour maximiser le niveau de protection de votre application.

Lors de l’adoption du bac à sable de l’application, certains emplacements de système de fichiers utilisés par votre application seront différents. En particulier, votre application dispose d’un répertoire de conteneur qui sera utilisé pour les fichiers de prise en charge des applications, les bases de données, les caches et tous les autres fichiers qui ne sont pas des documents utilisateur. MacOS et Xcode prennent tous deux en charge la migration de ces types de fichiers depuis leurs emplacements hérités vers le conteneur.

## <a name="sandboxing-quick-start"></a>Démarrage rapide sandboxing

Dans cette section, nous allons créer une application Xamarin. Mac simple qui utilise une vue Web (qui requiert une connexion réseau limitée dans le cadre de la mise en sandbox, sauf demande spécifique) comme exemple de prise en main du bac à sable (sandbox) de l’application.

Nous allons vérifier que l’application est en fait bac à sable (sandbox) et comment résoudre et résoudre les erreurs d’application courantes du bac à sable (sandbox).

### <a name="creating-the-xamarinmac-project"></a>Création du projet Xamarin. Mac

Procédez comme suit pour créer notre exemple de projet :

1. Démarrez Visual Studio pour Mac, puis cliquez sur la **nouvelle solution.** .
2. Dans la boîte de dialogue **nouveau projet** , sélectionnez application **Mac**  >  **application**  >  **cacao**:

    [![Création d’une application de cacao](sandboxing-images/sample01.png "Création d’une application de cacao")](sandboxing-images/sample01-large.png#lightbox)
3. Cliquez sur le bouton **suivant** , entrez `MacSandbox` pour le nom du projet et cliquez sur le bouton **créer** :

    [![Saisie du nom de l’application](sandboxing-images/sample02.png "Saisie du nom de l’application")](sandboxing-images/sample02-large.png#lightbox)
4. Dans la **panneau solutions**, double-cliquez sur le fichier **main. Storyboard** pour l’ouvrir en vue de le modifier dans Xcode :

    [![Modification du storyboard principal](sandboxing-images/sample03.png "Modification du storyboard principal")](sandboxing-images/sample03-large.png#lightbox)
5. Faites glisser une **vue Web** dans la fenêtre, dimensionnez-la pour remplir la zone de contenu, puis définissez-la sur agrandir et réduire avec la fenêtre :

    [![Ajout d’une vue Web](sandboxing-images/sample04.png "Ajout d’une vue Web")](sandboxing-images/sample04-large.png#lightbox)
6. Créez une prise pour l’affichage Web appelée `webView` :

    [![Création d’une nouvelle prise](sandboxing-images/sample05.png "Création d’une nouvelle prise")](sandboxing-images/sample05-large.png#lightbox)
7. Revenez à Visual Studio pour Mac et double-cliquez sur le fichier **ViewController.cs** dans le **panneau solutions** pour l’ouvrir et le modifier.
8. Ajoutez l’instruction using suivante :`using WebKit;`
9. Faites en sorte que la méthode ressemble à `ViewDidLoad` ce qui suit :

    ```csharp
    public override void AwakeFromNib ()
    {
        base.AwakeFromNib ();
        webView.MainFrame.LoadRequest(new NSUrlRequest(new NSUrl("http://www.apple.com")));
    }
    ```

10. Enregistrez vos modifications.

Exécutez votre application et assurez-vous que le site Web Apple est affiché dans la fenêtre comme suit :

[![Présentation d’un exemple d’exécution d’application](sandboxing-images/sample06.png "Présentation d’un exemple d’exécution d’application")](sandboxing-images/sample06-large.png#lightbox)

<a name="Signing_and_Provisioning_the_App"></a>

### <a name="signing-and-provisioning-the-app"></a>Signature et approvisionnement de l’application

Avant de pouvoir activer le bac à sable (sandbox) de l’application, nous devons d’abord approvisionner et signer notre application Xamarin. Mac.

Procédez comme suit :

1. Connectez-vous au portail des développeurs Apple :

    [![Connexion au portail des développeurs Apple](sandboxing-images/sign01.png "Connexion au portail des développeurs Apple")](sandboxing-images/sign01-large.png#lightbox)
2. Sélectionnez **certificats, identificateurs & profils**:

    [![Sélection de certificats, d’identificateurs et de profils](sandboxing-images/sign02.png "Sélection de certificats, identificateurs & profils")](sandboxing-images/sign02-large.png#lightbox)
3. Sous **applications Mac**, sélectionnez **identificateurs**:

    [![Sélection des identificateurs](sandboxing-images/sign03.png "Sélection des identificateurs")](sandboxing-images/sign03-large.png#lightbox)
4. Créez un nouvel ID pour l’application :

    [![Création d’un ID d’application](sandboxing-images/sign04.png "Création d’un ID d’application")](sandboxing-images/sign04-large.png#lightbox)
5. Sous **profils de provisionnement**, sélectionnez **développement**:

    [![Sélection du développement](sandboxing-images/sign05.png "Sélection du développement")](sandboxing-images/sign05-large.png#lightbox)
6. Créer un nouveau profil et sélectionner le **développement d’applications Mac**:

    [![Création d'un profil](sandboxing-images/sign06.png "Création d'un profil")](sandboxing-images/sign06-large.png#lightbox)
7. Sélectionnez l’ID d’application que nous avons créé ci-dessus :

    [![Sélection de l’ID d’application](sandboxing-images/sign07.png "Sélection de l’ID d’application")](sandboxing-images/sign07-large.png#lightbox)
8. Sélectionnez les développeurs pour ce profil :

    [![Ajout de développeurs](sandboxing-images/sign08.png "Ajout de développeurs")](sandboxing-images/sign08-large.png#lightbox)
9. Sélectionnez les ordinateurs pour ce profil :

    [![Sélection des ordinateurs autorisés](sandboxing-images/sign09.png "Sélection des ordinateurs autorisés")](sandboxing-images/sign09-large.png#lightbox)
10. Donnez un nom au profil :

    [![Attribution d’un nom au profil](sandboxing-images/sign10.png "Attribution d’un nom au profil")](sandboxing-images/sign10-large.png#lightbox)
11. Cliquez sur le bouton **Terminé**.

> [!IMPORTANT]
> Dans certains cas, vous devrez peut-être télécharger directement le nouveau profil de provisionnement à partir du portail des développeurs d’Apple, puis double-cliquer dessus pour l’installer. Vous devrez peut-être également arrêter et redémarrer Visual Studio pour Mac pour pouvoir accéder au nouveau profil.

Ensuite, nous devons charger le nouvel ID d’application et le nouveau profil sur l’ordinateur de développement. Procédez comme suit :

1. Démarrez Xcode et sélectionnez **Préférences** dans le menu **Xcode** :

    ![Modification des comptes dans Xcode](sandboxing-images/sign11.png "Modification des comptes dans Xcode")
2. Cliquez sur le bouton **afficher les détails.** .. :

    ![Cliquant sur le bouton afficher les détails](sandboxing-images/sign12.png "Cliquant sur le bouton afficher les détails")
3. Cliquez sur le bouton **Actualiser** (dans le coin inférieur gauche).
4. Cliquez sur le bouton **terminé** .

Ensuite, nous devons sélectionner le nouvel ID d’application et le profil de provisionnement dans notre projet Xamarin. Mac. Procédez comme suit :

1. Dans la **panneau solutions**, double-cliquez sur le fichier **info. plist** pour l’ouvrir et le modifier.
2. Assurez-vous que l' **identificateur de Bundle** correspond à l’ID d’application que nous avons créé ci-dessus (exemple : `com.appracatappra.MacSandbox` ) :

    [![Modification de l’identificateur de Bundle](sandboxing-images/sign13.png "Modification de l’identificateur de Bundle")](sandboxing-images/sign13-large.png#lightbox)
3. Ensuite, double-cliquez sur le fichier **habilitations. plist** et vérifiez que notre **magasin clé-valeur icloud** et que les **conteneurs icloud** correspondent tous à notre ID d’application que nous avons créé ci-dessus (exemple : `com.appracatappra.MacSandbox` ) :

    [![Modification du fichier habilitations. plist](sandboxing-images/sign17.png "Modification du fichier habilitations. plist")](sandboxing-images/sign17-large.png#lightbox)
4. Enregistrez vos modifications.
5. Dans la **panneau solutions**, double-cliquez sur le fichier projet pour ouvrir les options de modification :

    ![Editign les options de la solution](sandboxing-images/sign14.png "Editign les options de la solution")
6. Sélectionnez **signature Mac**, puis cochez **la case signer le bundle d’applications** et **signer le package d’installation**. Sous **profil de provisionnement**, sélectionnez celui que nous avons créé ci-dessus :

    ![Définition du profil de provisionnement](sandboxing-images/sign15.png "Définition du profil de provisionnement")
7. Cliquez sur le bouton **Terminé**.

> [!IMPORTANT]
> Vous devrez peut-être quitter et redémarrer Visual Studio pour Mac pour qu’il reconnaisse le nouvel ID d’application et le profil de configuration qui a été installé par Xcode.

#### <a name="troubleshooting-provisioning-issues"></a>Résolution des problèmes de provisionnement

À ce stade, vous devez essayer d’exécuter l’application et vous assurer que tout est signé et configuré correctement. Si l’application s’exécute toujours comme avant, tout est correct. En cas d’échec, vous pouvez obtenir une boîte de dialogue similaire à celle-ci :

[![Exemple de boîte de dialogue de problème d’approvisionnement](sandboxing-images/sign16.png "Exemple de boîte de dialogue de problème d’approvisionnement")](sandboxing-images/sign16-large.png#lightbox)

Voici les causes les plus courantes des problèmes de configuration et de signature :

- L’ID d’ensemble d’applications ne correspond pas à l’ID d’application du profil sélectionné.
- L’ID de développeur ne correspond pas à l’ID de développeur du profil sélectionné.
- L’UUID du Mac testé sur n’est pas inscrit dans le cadre du profil sélectionné.

Dans le cas d’un problème, corrigez le problème sur le portail des développeurs Apple, actualisez les profils dans Xcode et effectuez une génération propre dans Visual Studio pour Mac.

### <a name="enable-the-app-sandbox"></a>Activer le bac à sable de l’application

Vous activez le bac à sable (sandbox) de l’application en activant une case à cocher dans les options de vos projets. Effectuez les actions suivantes :

1. Dans la **panneau solutions**, double-cliquez sur le fichier **Entitlements. plist** pour l’ouvrir et le modifier.
2. Cochez la case **activer les droits** et **activer le sandboxing d’application**:

    [![Modification des droits et activation du sandboxing](sandboxing-images/sign17.png "Modification des droits et activation du sandboxing")](sandboxing-images/sign17-large.png#lightbox)
3. Enregistrez vos modifications.

À ce stade, vous avez activé le bac à sable (sandbox) de l’application, mais vous n’avez pas fourni l’accès réseau requis pour l’affichage Web. Si vous exécutez l’application maintenant, vous devez obtenir une fenêtre vide :

[![Indication de l’accès Web bloqué](sandboxing-images/sample08.png "Indication de l’accès Web bloqué")](sandboxing-images/sample08-large.png#lightbox)

### <a name="verifying-that-the-app-is-sandboxed"></a>Vérification que l’application est en mode bac à sable (sandbox)

Hormis le comportement de blocage des ressources, il existe trois façons de savoir si une application Xamarin. Mac a été correctement bac à sable (sandbox) :

1. Dans Finder, vérifiez le contenu du `~/Library/Containers/` dossier : si l’application est bac à sable (sandbox), il y aura un dossier nommé comme l’identificateur de Bundle de votre application (exemple : `com.appracatappra.MacSandbox` ) :

    [![Ouverture de l’offre groupée de l’application](sandboxing-images/sample09.png "Ouverture de l’offre groupée de l’application")](sandboxing-images/sample09-large.png#lightbox)
2. Le système voit l’application comme bac à sable (sandbox) dans le moniteur d’activité :
    - Lancez le moniteur d’activité (sous `/Applications/Utilities` ).
    - Choisissez **Afficher**  >  les**colonnes** et vérifiez que l’option de menu **sandbox** est cochée.
    - Assurez-vous que la colonne sandbox lit `Yes` pour votre application :

    [![Vérification de l’application dans le moniteur d’activité](sandboxing-images/sample10.png "Vérification de l’application dans le moniteur d’activité")](sandboxing-images/sample10-large.png#lightbox)
3. Vérifiez que le fichier binaire de l’application est en mode bac à sable (sandbox) :
    - Démarrez l’application Terminal.
    - Accédez au répertoire des applications `bin` .
    - Exécutez cette commande : `codesign -dvvv --entitlements :- executable_path` (où `executable_path` est le chemin d’accès à votre application) :

    [![Vérification de l’application sur la ligne de commande](sandboxing-images/sample11.png "Vérification de l’application sur la ligne de commande")](sandboxing-images/sample11-large.png#lightbox)

### <a name="debugging-a-sandboxed-app"></a>Débogage d’une application bac à sable (sandbox)

Le débogueur se connecte aux applications Xamarin. Mac via TCP, ce qui signifie que par défaut, lorsque vous activez le sandboxing, il ne peut pas se connecter à l’application. par conséquent, si vous essayez d’exécuter l’application sans que les autorisations appropriées soient activées, vous recevez une erreur *« Impossible de se connecter au débogueur »*.

[![Définition des options requises](sandboxing-images/debug01.png "Définition des options requises")](sandboxing-images/debug01-large.png#lightbox)

L’autorisation **autoriser les connexions réseau sortantes (client)** est celle qui est requise pour le débogueur, l’activation de celle-ci permet le débogage normal. Étant donné que vous ne pouvez pas effectuer de débogage sans cela, nous avons mis à jour la `CompileEntitlements` cible pour pour `msbuild` Ajouter automatiquement cette autorisation aux droits pour toute application qui est bac à sable (sandbox) pour les versions de débogage uniquement. Les versions release doivent utiliser les droits spécifiés dans le fichier de droits, non modifié.

### <a name="resolving-an-app-sandbox-violation"></a>Résolution d’une violation de bac à sable (sandbox) d’application

Une violation du bac à sable (sandbox) d’application se produit si une application Xamarin. Mac bac à sable (sandbox) a tenté d’accéder à une ressource qui n’est pas explicitement autorisée. Par exemple, notre vue Web n’est plus en mesure d’afficher le site Web Apple.

La source la plus courante de violations de bac à sable (sandbox) d’application se produit lorsque les paramètres de droits spécifiés dans Visual Studio pour Mac ne correspondent pas aux exigences de l’application. Là encore, revenons à notre exemple, les droits de connexion réseau manquants qui empêchent le fonctionnement de l’affichage Web.

#### <a name="discovering-app-sandbox-violations"></a>Détection des violations du bac à sable de l’application

Si vous pensez qu’une violation du bac à sable (sandbox) d’application se produit dans votre application Xamarin. Mac, le moyen le plus rapide de découvrir le problème consiste à utiliser l’application **console** .

Effectuez les actions suivantes :

1. Compilez l’application en question et exécutez-la à partir de Visual Studio pour Mac.
2. Ouvrez l’application **console** (à partir de `/Applications/Utilties/` ).
3. Sélectionnez **tous les messages** dans la barre latérale, puis entrez `sandbox` dans la recherche :

    [![Exemple de problème de sandbox dans la console](sandboxing-images/resolve01.png "Exemple de problème de sandbox dans la console")](sandboxing-images/resolve01-large.png#lightbox)

Pour notre exemple d’application ci-dessus, vous pouvez voir que le crénage bloque le `network-outbound` trafic en raison du bac à sable (sandbox) de l’application, puisque nous n’avons pas demandé ce droit.

#### <a name="fixing-app-sandbox-violations-with-entitlements"></a>Correction des violations du bac à sable (sandbox) d’application avec les droits

Maintenant que nous avons vu comment trouver des violations de sandboxing d’application, voyons comment les résoudre en ajustant les droits de l’application.

Effectuez les actions suivantes :

1. Dans la **panneau solutions**, double-cliquez sur le fichier **Entitlements. plist** pour l’ouvrir et le modifier.
2. Sous la section **droits** , activez la case à cocher **autoriser les connexions réseau sortantes (client)** :

    [![Modification des droits](sandboxing-images/sign17.png "Modification des droits")](sandboxing-images/sign17-large.png#lightbox)
3. Enregistrez les modifications apportées à l’application.

Si nous procédons comme indiqué ci-dessus pour notre exemple d’application, puis le générez et l’exécutez, le contenu Web s’affichera à présent comme prévu.

## <a name="the-app-sandbox-in-depth"></a>Présentation détaillée du bac à sable (sandbox) de l’application

Les mécanismes de contrôle d’accès fournis par le bac à sable de l’application sont peu nombreux et faciles à comprendre. Toutefois, la façon dont le bac à sable (sandbox) de l’application sera adopté par chaque application est unique et dépend des exigences de l’application.

Étant donné votre meilleur effort pour empêcher votre application Xamarin. Mac d’être exploitée par du code malveillant, il ne doit y avoir qu’une seule vulnérabilité dans l’application (ou dans l’une des bibliothèques ou infrastructures qu’elle consomme) pour prendre le contrôle des interactions de l’application avec le système.

Le bac à sable de l’application a été conçu pour empêcher la prise en charge (ou limiter les dommages qu’il peut provoquer) en vous permettant de spécifier les interactions prévues de l’application avec le système. Le système octroie uniquement l’accès à la ressource dont votre application a besoin pour faire son travail et rien de plus.

Lorsque vous concevez pour le bac à sable de l’application, vous concevez pour un scénario le plus défavorable. Si l’application est compromise par du code malveillant, elle est limitée à l’accès uniquement aux fichiers et aux ressources du bac à sable (sandbox) de l’application.

### <a name="entitlements-and-system-resource-access"></a>Droits et accès aux ressources système

Comme nous l’avons vu plus haut, une application Xamarin. Mac qui n’a pas été bac à sable (sandbox) reçoit les droits et l’accès complets de l’utilisateur qui exécute l’application. Si elles sont compromises par du code malveillant, une application non protégée peut agir en tant qu’agent pour un comportement hostile, avec un grand nombre d’attaques potentielles.

En activant le bac à sable (sandbox) de l’application, vous supprimez tous les privilèges sauf un ensemble minimal de privilèges, que vous réactivez ensuite en fonction des besoins uniquement à l’aide de vos droits d’application Xamarin. Mac.

Vous modifiez les ressources du bac à sable (sandbox) de votre application en modifiant son fichier **habilitations. plist** et en vérifiant ou en sélectionnant les droits requis dans les zones de liste déroulante éditeurs :

[![Modification des droits](sandboxing-images/sign17.png "Modification des droits")](sandboxing-images/sign17-large.png#lightbox)

### <a name="container-directories-and-file-system-access"></a>Répertoires de conteneurs et accès au système de fichiers

Lorsque votre application Xamarin. Mac adopte le bac à sable (sandbox) de l’application, elle a accès aux emplacements suivants :

- **Le répertoire du conteneur d’application** : lors de la première exécution, le système d’exploitation crée un _Répertoire de conteneur_ spécial dans lequel toutes ses ressources vont, auxquelles seul il peut accéder. L’application dispose d’un accès complet en lecture/écriture à ce répertoire.
- **Répertoires de conteneur du groupe d’applications** : votre application peut être autorisée à accéder à un ou plusieurs _conteneurs de groupe_ partagés entre les applications du même groupe.
- **Fichiers spécifiés** par l’utilisateur : votre application obtient automatiquement l’accès aux fichiers qui sont explicitement ouverts ou déplacés vers l’application par l’utilisateur.
- **Éléments connexes** : avec les droits appropriés, votre application peut avoir accès à un fichier portant le même nom, mais avec une extension différente. Par exemple, un document qui a été enregistré en tant que `.txt` fichier et `.pdf` .
- **Répertoires temporaires, répertoires d’outils en ligne de commande et emplacements lisibles spécifiques** : votre application dispose de différents degrés d’accès aux fichiers dans d’autres emplacements bien définis, comme spécifié par le système.

#### <a name="the-app-container-directory"></a>Répertoire du conteneur d’application

Le répertoire du conteneur d’applications d’une application Xamarin. Mac présente les caractéristiques suivantes :

- Il se trouve dans un emplacement masqué dans le répertoire de démarrage de l’utilisateur (généralement `~Library/Containers` ) et est accessible à l’aide de la `NSHomeDirectory` fonction (voir ci-dessous) dans votre application. Étant donné qu’il se trouve dans le répertoire de départ, chaque utilisateur obtient son propre conteneur pour l’application.
- L’application dispose d’un accès illimité en lecture/écriture au répertoire du conteneur et à tous ses sous-répertoires et fichiers qu’il contient.
- La plupart des API de recherche de chemins d’accès macOS sont relatives au conteneur de l’application. Par exemple, le conteneur aura sa propre **bibliothèque** (accessible via `NSLibraryDirectory` ), les sous-répertoires de **prise en charge des applications** et de **Préférences** .
- macOS établit et applique la connexion entre et l’application et son conteneur via la signature de code. Même si une autre application tente d’usurper l’application à l’aide de son **identificateur de Bundle**, elle ne pourra pas accéder au conteneur en raison de la signature de code.
- Le conteneur n’est pas destiné aux fichiers générés par l’utilisateur. Au lieu de cela, il s’agit des fichiers que votre application utilise, tels que des bases de données, des caches ou d’autres types de données spécifiques.
- Pour les types d’applications _Shoebox_ (comme l’application photo d’Apple), le contenu de l’utilisateur sera placé dans le conteneur.

> [!IMPORTANT]
> Malheureusement, Xamarin. Mac ne dispose pas encore d’une couverture d’API de 100% (contrairement à Xamarin. iOS). par conséquent, l' `NSHomeDirectory` API n’a pas été mappée dans la version actuelle de Xamarin. Mac.

En guise de solution de contournement temporaire, vous pouvez utiliser le code suivant :

```csharp
[System.Runtime.InteropServices.DllImport("/System/Library/Frameworks/Foundation.framework/Foundation")]
public static extern IntPtr NSHomeDirectory();

public static string ContainerDirectory {
    get {
        return ((NSString)ObjCRuntime.Runtime.GetNSObject(NSHomeDirectory())).ToString ();
        }
}
```

#### <a name="the-app-group-container-directory"></a>Répertoire du conteneur du groupe d’applications

À partir de Mac macOS 10.7.5 (et versions ultérieures), une application peut utiliser le `com.apple.security.application-groups` droit d’accéder à un conteneur partagé qui est commun à toutes les applications du groupe. Vous pouvez utiliser ce conteneur partagé pour le contenu non-utilisateur, tel que la base de données ou d’autres types de fichiers de prise en charge (tels que les caches).

Les conteneurs de groupe sont automatiquement ajoutés au conteneur sandbox de chaque application (s’ils font partie d’un groupe) et sont stockés dans `~/Library/Group Containers/<application-group-id>` . L’ID de groupe _doit_ commencer par l’ID de votre équipe de développement et un point, par exemple :

```plist
<team-id>.com.company.<group-name>
```

Pour plus d’informations, consultez Ajout d' [une application à un groupe d’applications](https://developer.apple.com/library/prerelease/mac/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19) dans [référence de clé](https://developer.apple.com/library/prerelease/mac/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/AboutEntitlements.html#//apple_ref/doc/uid/TP40011195)de l’autorisation.

#### <a name="powerbox-and-file-system-access-outside-of-the-app-container"></a>Powerbox et accès au système de fichiers en dehors du conteneur d’application

Une application Xamarin. Mac bac à sable (sandbox) peut accéder aux emplacements du système de fichiers en dehors de son conteneur des manières suivantes :

- À la direction spécifique de l’utilisateur (par le biais de boîtes de dialogue d’ouverture et d’enregistrement ou d’autres méthodes telles que le glisser-déplacer).
- En utilisant des droits pour des emplacements spécifiques du système de fichiers (tels que `/bin` ou `/usr/lib` ).
- Lorsque l’emplacement du système de fichiers se trouve dans certains répertoires accessibles au monde entier (par exemple, le partage).

_Powerbox_ est la technologie de sécurité MacOS qui interagit avec l’utilisateur pour étendre les droits d’accès aux fichiers de votre application Xamarin. Mac en mode bac à sable (sandbox). Powerbox n’a pas d’API, mais est activé de manière transparente quand l’application appelle `NSOpenPanel` ou `NSSavePanel` . L’accès à Powerbox est activé via les droits que vous définissez pour votre application Xamarin. Mac.

Quand une application bac à sable (sandbox) affiche une boîte de dialogue Ouvrir ou enregistrer, la fenêtre est présentée par Powerbox (et non AppKit) et a donc accès aux fichiers ou répertoires auxquels l’utilisateur a accès.

Lorsqu’un utilisateur sélectionne un fichier ou un répertoire dans la boîte de dialogue Ouvrir ou enregistrer (ou qu’il fait glisser sur l’icône de l’application), Powerbox ajoute le chemin d’accès associé au bac à sable (sandbox) de l’application.

En outre, le système autorise automatiquement les éléments suivants dans une application bac à sable (sandbox) :

- Connexion à une méthode d’entrée système.
- Appelez les services sélectionnés par l’utilisateur à partir du menu **services** (uniquement pour les services marqués comme _sécurisés pour les applications sandbox_ par le fournisseur de services).
- Ouvrez fichiers choisir par l’utilisateur dans le menu **ouvrir récemment** .
- Utilisez copier & coller entre d’autres applications.
- Lire les fichiers à partir des emplacements lisibles dans le monde :
  - `/bin`
  - `/sbin`
  - `/usr/bin`
  - `/usr/lib`
  - `/usr/sbin`
  - `/usr/share`
  - `/System`
- Lire et écrire des fichiers dans les répertoires créés par `NSTemporaryDirectory` .

Par défaut, les fichiers ouverts ou enregistrés par une application Xamarin. Mac bac à sable (sandbox) restent accessibles jusqu’à ce que l’application se termine (à moins que le fichier ne soit encore ouvert à la fermeture de l’application). Les fichiers ouverts sont automatiquement restaurés dans le bac à sable (sandbox) de l’application via la fonctionnalité de reprise macOS lors du prochain démarrage de l’application.

Pour assurer la persistance aux fichiers situés en dehors du conteneur d’une application Xamarin. Mac, utilisez des signets de portée de sécurité (voir ci-dessous).

#### <a name="related-items"></a>Éléments connexes

Le bac à sable de l’application permet à une application d’accéder à des éléments associés portant le même nom de fichier, mais avec des extensions différentes. La fonctionnalité comporte deux parties : a) une liste d’extensions associées dans le fichier de l’application `Info.plst` , b) pour indiquer au bac à sable (sandbox) ce que l’application fera avec ces fichiers.

Il existe deux scénarios dans lesquels cela est logique :

1. L’application doit être en mesure d’enregistrer une version différente du fichier (avec une nouvelle extension). Par exemple, l’exportation `.txt` d’un fichier dans un `.pdf` fichier. Pour gérer cette situation, vous devez utiliser un `NSFileCoordinator` pour accéder au fichier. Vous devez `WillMove(fromURL, toURL)` d’abord appeler la méthode, déplacer le fichier vers la nouvelle extension, puis appeler `ItemMoved(fromURL, toURL)` .
2. L’application doit ouvrir un fichier principal avec une extension et plusieurs fichiers de prise en charge avec des extensions différentes. Par exemple, un film et un fichier de sous-titre. Utilisez un `NSFilePresenter` pour accéder au fichier secondaire. Fournissez le fichier principal à la `PrimaryPresentedItemURL` propriété et le fichier secondaire à la `PresentedItemURL` propriété. Lorsque le fichier principal est ouvert, appelez la `AddFilePresenter` méthode de la `NSFileCoordinator` classe pour inscrire le fichier secondaire.

Dans les deux scénarios, le fichier **info. plist** de l’application doit déclarer les types de documents que l’application peut ouvrir. Pour tout type de fichier, ajoutez `NSIsRelatedItemType` (avec une valeur de `YES` ) à son entrée dans le `CFBundleDocumentTypes` tableau.

#### <a name="open-and-save-dialog-behavior-with-sandboxed-apps"></a>Ouvrir et enregistrer le comportement de la boîte de dialogue avec les applications sandbox

Les limites suivantes sont placées sur `NSOpenPanel` et `NSSavePanel` lors de leur appel à partir d’une application Xamarin. Mac bac à sable (sandbox) :

- Vous ne pouvez pas appeler par programmation le bouton **OK** .
- Vous ne pouvez pas modifier par programmation la sélection d’un utilisateur dans un `NSOpenSavePanelDelegate` .

En outre, les modifications d’héritage suivantes sont en place :

- Application non bac **à sable (sandbox)**  -  `NSOpenPanel``NSSavePanel``NSPanel``NSWindow``NSResponder``NSObject``NSOpenPanel``NSSavePanel``NSObject``NSOpenPanel``NSSavePanel`

### <a name="security-scoped-bookmarks-and-persistent-resource-access"></a>Signets avec étendue de sécurité et accès aux ressources persistant

Comme indiqué ci-dessus, une application Xamarin. Mac bac à sable (sandbox) peut accéder à un fichier ou à une ressource en dehors de son conteneur par le biais d’une interaction directe de l’utilisateur (fournie par PowerBox). Toutefois, l’accès à ces ressources n’est pas automatiquement conservé entre les lancements d’applications ou les redémarrages du système.

En utilisant des _signets de portée de sécurité_, une application Xamarin. Mac bac à sable (sandbox) peut préserver l’intention de l’utilisateur et conserver l’accès aux ressources données après le redémarrage d’une application.

#### <a name="security-scoped-bookmark-types"></a>Types de signets de portée sécurité

Lorsque vous travaillez avec des signets de portée de sécurité et un accès permanent aux ressources, il existe deux cas d’utilisation de Sistine :

- **Un signet à portée d’application fournit un accès permanent à un fichier ou à un dossier spécifié par l’utilisateur.**

    Par exemple, si l’application Xamarin. Mac bac à sable (sandbox) autorise à utiliser pour ouvrir un document externe en vue de sa modification (à l’aide d’un `NSOpenPanel` ), l’application peut créer un signet à portée d’application afin qu’elle puisse à nouveau accéder au même fichier.
- **Un signet d’étendue de document fournit un accès permanent à un document spécifique à un sous-fichier.**

Par exemple, une application de modification vidéo qui crée un fichier projet qui a accès aux images individuelles, aux clips vidéo et aux fichiers audio qui seront ultérieurement combinés en un seul film.

Lorsque l’utilisateur importe un fichier de ressources dans le projet (via un `NSOpenPanel` ), l’application crée un signet d’étendue de document pour l’élément stocké dans le projet, afin que le fichier soit toujours accessible à l’application.

Un signet d’étendue de document peut être résolu par toute application qui peut ouvrir les données de signet et le document lui-même. Cela prend en charge la portabilité, permettant à l’utilisateur d’envoyer les fichiers projet à un autre utilisateur et de faire en sorte que tous les signets fonctionnent également pour eux.

> [!IMPORTANT]
> Un signet d’étendue de document ne peut pointer _que_ vers un seul fichier et non un dossier, et ce fichier ne peut pas se trouver dans un emplacement utilisé par le système (tel que `/private` ou `/Library` ).

#### <a name="using-security-scoped-bookmarks"></a>Utilisation des signets de portée de sécurité

À l’aide de l’un des deux types de signets de sécurité, vous devez effectuer les étapes suivantes :

1. **Définir les droits appropriés dans l’application Xamarin. Mac qui doit utiliser des signets de sécurité** -pour les signets de portée application, définissez la `com.apple.security.files.bookmarks.app-scope` clé d’habilitation sur `true` . Pour les signets d’étendue de document, affectez la valeur `com.apple.security.files.bookmarks.document-scope` à la clé habilitation `true` .
2. **Créer un signet d’étendue de sécurité** : pour tout fichier ou dossier auquel l’utilisateur a donné l’accès (par `NSOpenPanel` exemple, par exemple), l’application doit disposer d’un accès permanent à. Utilisez la `public virtual NSData CreateBookmarkData (NSUrlBookmarkCreationOptions options, string[] resourceValues, NSUrl relativeUrl, out NSError error)` méthode de la `NSUrl` classe pour créer le signet.
3. **Résoudre le signet d’étendue de sécurité** -lorsque l’application a besoin d’accéder à nouveau à la ressource (par exemple, après le redémarrage), elle doit résoudre le signet en une URL d’étendue de sécurité. Utilisez la `public static NSUrl FromBookmarkData (NSData data, NSUrlBookmarkResolutionOptions options, NSUrl relativeToUrl, out bool isStale, out NSError error)` méthode de la `NSUrl` classe pour résoudre le signet.
4. **Informez explicitement le système auquel vous souhaitez accéder au fichier à partir de l’URL d’étendue de sécurité** . cette étape doit être effectuée immédiatement après l’obtention de l’URL d’étendue de sécurité ci-dessus ou, lorsque vous souhaitez récupérer ultérieurement l’accès à la ressource après avoir libéré votre accès à celle-ci. Appelez la `StartAccessingSecurityScopedResource ()` méthode de la `NSUrl` classe pour commencer à accéder à une URL d’étendue de sécurité.
5. Informez **explicitement le système que vous avez fini d’accéder au fichier à partir de l’URL d’étendue de sécurité** -dès que possible, vous devez informer le système lorsque l’application n’a plus besoin d’accéder au fichier (par exemple, si l’utilisateur la ferme). Appelez la `StopAccessingSecurityScopedResource ()` méthode de la `NSUrl` classe pour arrêter d’accéder à une URL d’étendue de sécurité.

Une fois que vous avez libéré l’accès à une ressource, vous devez repasser à l’étape 4 pour rétablir l’accès. Si l’application Xamarin. Mac est redémarrée, vous devez revenir à l’étape 3 et résoudre à nouveau le signet.

> [!IMPORTANT]
> Si vous ne libérez pas l’accès aux ressources d’URL d’étendue de sécurité, une application Xamarin. Mac risque de provoquer des fuites de ressources de noyau. Par conséquent, l’application ne sera plus en mesure d’ajouter des emplacements de système de fichiers à son conteneur tant qu’il n’aura pas redémarré.

### <a name="the-app-sandbox-and-code-signing"></a>Le bac à sable de l’application et la signature du code

Une fois que vous avez activé le bac à sable (sandbox) de l’application et activé les exigences spécifiques de votre application Xamarin. Mac (par le biais des droits), vous devez coder le projet pour que le bac à sable soit pris en compte. Vous devez effectuer la signature du code, car les droits requis pour le sandbox d’application sont liés à la signature de l’application.

macOS applique un lien entre le conteneur d’une application et sa signature de code, de cette façon aucune autre application ne peut accéder à ce conteneur, même s’il usurpe l’ID d’ensemble d’applications. Ce mécanisme fonctionne de la façon suivante :

1. Lorsque le système crée le conteneur de l’application, il définit une _liste de Access Control_ (ACL) sur ce conteneur. L’entrée de contrôle d’accès initiale dans la liste contient la _spécification désignée_ de l’application (Dr), qui décrit comment les futures versions de l’application peuvent être reconnues (lorsqu’elles ont été mises à niveau).
2. À chaque fois qu’une application avec le même ID de Bundle démarre, le système vérifie que la signature du code de l’application correspond aux exigences spécifiées dans l’une des entrées de la liste de contrôle d’accès du conteneur. Si le système ne trouve pas de correspondance, il empêche le lancement de l’application.

La signature de code fonctionne des manières suivantes :

1. Avant de créer le projet Xamarin. Mac, obtenez un certificat de développement, un certificat de distribution et un certificat d’ID de développeur à partir du portail des développeurs Apple.
2. Quand le Mac App Store distribue l’application Xamarin. Mac, il est signé avec une signature de code Apple.

Lors du test et du débogage, vous utiliserez une version de l’application Xamarin. Mac que vous avez signée (qui sera utilisée pour créer le conteneur d’application). Plus tard, si vous souhaitez tester ou installer la version à partir de l’Apple App Store, celle-ci sera signée avec la signature Apple et ne pourra pas être lancée (car elle n’a pas la même signature de code que le conteneur d’application d’origine). Dans ce cas, vous obtiendrez un rapport d’incident similaire à ce qui suit :

```csharp
Exception Type:  EXC_BAD_INSTRUCTION (SIGILL)
```

Pour résoudre ce problème, vous devez ajuster l’entrée de liste de contrôle d’accès de façon à ce qu’elle pointe vers la version signée Apple de l’application.

Pour plus d’informations sur la création et le téléchargement des profils de provisionnement requis pour le sandboxing, consultez la section [signature et approvisionnement de l’application](#Signing_and_Provisioning_the_App) ci-dessus.

#### <a name="adjusting-the-acl-entry"></a>Ajustement de l’entrée de liste de contrôle d’accès

Pour autoriser l’exécution de la version signée par Apple de l’application Xamarin. Mac, procédez comme suit :

1. Ouvrez l’application Terminal (dans `/Applications/Utilities` ).
2. Ouvrez une fenêtre de recherche à la version signée Apple de l’application Xamarin. Mac.
3. Tapez `asctl container acl add -file` dans la fenêtre de terminal.
4. Faites glisser l’icône de l’application Xamarin. Mac à partir de la fenêtre Finder et déposez-la dans la fenêtre de terminal.
5. Le chemin d’accès complet au fichier est ajouté à la commande dans Terminal.
6. Appuyez sur **entrée** pour exécuter la commande.

La liste de contrôle d’accès du conteneur contient maintenant les spécifications de code indiquées pour les deux versions de l’application Xamarin. Mac et macOS autorise désormais l’exécution de l’une ou l’autre version.

#### <a name="display-a-list-of-acl-code-requirements"></a>Afficher une liste des exigences de code ACL

Vous pouvez afficher la liste des spécifications du code dans la liste de contrôle d’accès d’un conteneur en procédant comme suit :

1. Ouvrez l’application Terminal (dans `/Applications/Utilities` ).
2. Tapez `asctl container acl list -bundle <container-name>`.
3. Appuyez sur **entrée** pour exécuter la commande.

`<container-name>`Est généralement l’identificateur de Bundle pour l’application Xamarin. Mac.

## <a name="designing-a-xamarinmac-app-for-the-app-sandbox"></a>Conception d’une application Xamarin. Mac pour le bac à sable de l’application

Il existe un flux de travail commun à suivre lors de la conception d’une application Xamarin. Mac pour le bac à sable de l’application. Cela dit, les spécificités de l’implémentation de la mise en sandbox dans une application vont être uniques à la fonctionnalité de l’application donnée.

### <a name="six-steps-for-adopting-the-app-sandbox"></a>Six étapes pour l’adoption du bac à sable de l’application

La conception d’une application Xamarin. Mac pour le bac à sable de l’application comprend généralement les étapes suivantes :

1. Déterminez si l’application est adaptée au sandbox.
2. Concevoir une stratégie de développement et de distribution.
3. Résolvez toutes les incompatibilités d’API.
4. Appliquez les droits du bac à sable (sandbox) d’application requis au projet Xamarin. Mac.
5. Ajoutez la séparation des privilèges à l’aide de XPC.
6. Implémentez une stratégie de migration.

> [!IMPORTANT]
> Vous devez non seulement bac à sable (sandbox) de l’exécutable principal dans votre offre groupée d’applications, mais aussi chaque application ou outil d’assistance inclus dans cette offre groupée. Cela est nécessaire pour toute application distribuée à partir de l’App Store Mac et, si possible, doit être effectuée pour toute autre forme de distribution d’applications.

Pour obtenir la liste de tous les fichiers binaires exécutables dans l’offre groupée d’une application Xamarin. Mac, tapez la commande suivante dans le terminal :

```bash
find -H [Your-App-Bundle].app -print0 | xargs -0 file | grep "Mach-O .*executable"
```

Où `[Your-App-Bundle]` est le nom et le chemin d’accès à l’offre groupée de l’application.

### <a name="determine-whether-a-xamarinmac-app-is-suitable-for-sandboxing"></a>Déterminer si une application Xamarin. Mac convient pour le sandboxing

La plupart des applications Xamarin. Mac sont entièrement compatibles avec le bac à sable (sandbox) de l’application et, par conséquent, appropriées pour le sandboxing. Si l’application requiert un comportement que le bac à sable (sandbox) de l’application ne permet pas, vous devez envisager une autre approche.

Si votre application requiert l’un des comportements suivants, elle est incompatible avec le bac à sable (sandbox) de l’application :

- **Services d’autorisation** : avec le bac à sable de l’application, vous ne pouvez pas utiliser les fonctions décrites dans informations de référence sur les [services d’autorisation C](https://developer.apple.com/library/prerelease/mac/documentation/Security/Reference/authorization_ref/index.html#//apple_ref/doc/uid/TP30000826).
- **API d’accessibilité** : vous ne pouvez pas Sandboxer des applications d’assistance telles que des lecteurs d’écran ou des applications qui contrôlent d’autres applications.
- **Envoyer des événements Apple à des applications arbitraires** : si l’application requiert l’envoi d’événements Apple à une application inconnue et arbitraire, elle ne peut pas être bac à sable (sandbox). Pour obtenir une liste connue des applications appelées, l’application peut toujours être bac à sable (sandbox) et les droits doivent inclure la liste des applications appelées.
- **Envoyer des dictionnaires d’informations utilisateur dans des notifications distribuées à d’autres tâches** : avec le bac à sable (sandbox) de l’application, vous ne pouvez pas inclure un `userInfo` dictionnaire lors de la publication dans un `NSDistributedNotificationCenter` objet pour d’autres tâches de messagerie.
- **Charger les extensions du noyau** : le chargement des extensions du noyau est interdit par le bac à sable (sandbox) de l’application.
- **Simulation de l’entrée d’utilisateur dans les boîtes de dialogue Ouvrir et enregistrer** -la manipulation par programmation de boîtes de dialogue d’ouverture ou d’enregistrement pour simuler ou modifier une entrée utilisateur est interdite par le bac à sable de l’application.
- L' **accès ou la définition des préférences sur d’autres applications** -la manipulation des paramètres d’autres applications est interdite par le bac à sable de l’application.
- **Configuration des paramètres réseau** -la manipulation des paramètres réseau est interdite par le bac à sable (sandbox) de l’application.
- **Arrêt d’autres applications** : le bac à sable de l’application interdit l’utilisation `NSRunningApplication` de pour mettre fin à d’autres applications.

### <a name="resolving-api-incompatibilities"></a>Résolution des incompatibilités d’API

Lors de la conception d’une application Xamarin. Mac pour le bac à sable de l’application, vous pouvez rencontrer des incompatibilités avec l’utilisation de certaines API macOS.

Voici quelques problèmes courants que vous pouvez effectuer pour les résoudre :

- **Ouverture, enregistrement et suivi de documents** : Si vous gérez des documents à l’aide d’une technologie autre que `NSDocument` , vous devez basculer vers celui-ci en raison de la prise en charge intégrée du bac à sable (sandbox) de l’application. `NSDocument`fonctionne automatiquement avec PowerBox et prend en charge la conservation de documents dans votre bac à sable (sandbox) si l’utilisateur les déplace dans Finder.
- **Conserver l’accès aux ressources du système de fichiers** : si l’application Xamarin. Mac dépend de l’accès permanent aux ressources en dehors de son conteneur, utilisez des signets de portée sécurité pour conserver l’accès.
- **Créer un élément de connexion pour une application** : avec le bac à sable (sandbox) de l’application, vous ne pouvez pas créer un élément de connexion à l’aide `LSSharedFileList` de ni manipuler l’état des services de lancement à l’aide de `LSRegisterURL` . Utilisez la `SMLoginItemSetEnabled` fonction comme décrit dans apples, [Ajout d’éléments de connexion à l’aide de la documentation de service Management Framework](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingLoginItems.html#//apple_ref/doc/uid/10000172i-SW5-SW1) .
- **Accès aux données utilisateur** -si vous utilisez des fonctions POSIX, telles que `getpwuid` pour obtenir le répertoire de base de l’utilisateur à partir des services d’annuaire, envisagez d’utiliser des symboles de cacao ou Core Foundation tels que `NSHomeDirectory` .
- **Accès aux préférences d’autres applications** : étant donné que le bac à sable (sandbox) de l’application dirige les API de recherche de chemin vers le conteneur de l’application, la modification des préférences s’effectue dans ce conteneur et l’accès aux autres préférences des applications n’est pas autorisé.
- **Utilisation d’une vidéo HTML5 incorporée dans les vues Web** : si l’application Xamarin. Mac utilise WebKit pour lire des vidéos HTML5 incorporées, vous devez également lier l’application à l’infrastructure de Fondation av. Le bac à sable de l’application empêchera le coremédier de lire ces vidéos dans le cas contraire.

### <a name="applying-required-app-sandbox-entitlements"></a>Application des droits du bac à sable (sandbox) d’application requis

Vous devrez modifier les droits de toute application Xamarin. Mac que vous souhaitez exécuter dans le bac à sable de l’application et activer la case à cocher **activer le sandbox** de l’application.

Selon les fonctionnalités de l’application, vous devrez peut-être activer d’autres droits pour accéder aux fonctionnalités ou ressources du système d’exploitation. Le sandboxing d’application fonctionne mieux lorsque vous réduisez les droits que vous demandez au minimum requis pour exécuter votre application. vous devez donc activer les droits uniquement de façon aléatoire.

Pour déterminer les droits requis par une application Xamarin. Mac, procédez comme suit :

1. Activez le bac à sable (sandbox) de l’application et exécutez l’application Xamarin. Mac.
2. Exécutez les fonctionnalités de l’application.
3. Ouvrez l’application console (disponible dans `/Applications/Utilities` ) et recherchez `sandboxd` violations dans le journal **tous les messages** .
4. Pour chaque `sandboxd` violation, résolvez le problème à l’aide du conteneur d’application au lieu d’autres emplacements de système de fichiers ou appliquez des droits d’application sandbox pour permettre l’accès aux fonctionnalités de système d’exploitation limitées.
5. Réexécutez et testez à nouveau toutes les fonctionnalités de l’application Xamarin. Mac.
6. Répétez l’opération jusqu’à ce que toutes les `sandboxd` violations aient été résolues.

### <a name="add-privilege-separation-using-xpc"></a>Ajouter une séparation des privilèges à l’aide de XPC

Lors du développement d’une application Xamarin. Mac pour le bac à sable de l’application, examinez les comportements de l’application dans les termes des privilèges et de l’accès, puis envisagez de séparer les opérations à haut risque dans leurs propres services XPC.

Pour plus d’informations, consultez le Guide de programmation [création de services](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingXPCServices.html#//apple_ref/doc/uid/10000172i-SW6) et [démons et services](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/Introduction.html#//apple_ref/doc/uid/10000172i)d’Apple XPC.

### <a name="implement-a-migration-strategy"></a>Implémenter une stratégie de migration

Si vous publiez une nouvelle version bac à sable (sandbox) d’une application Xamarin. Mac qui n’était pas précédemment bac à sable (sandbox), vous devez vous assurer que les utilisateurs actuels disposent d’un chemin de mise à niveau fluide.

 Pour plus d’informations sur la façon d’implémenter un manifeste de migration de conteneur, consultez la rubrique [migration d’une application vers un bac à sable (sandbox)](https://developer.apple.com/library/prerelease/mac/documentation/Security/Conceptual/AppSandboxDesignGuide/MigratingALegacyApp/MigratingAnAppToASandbox.html#//apple_ref/doc/uid/TP40011183-CH6-SW1) .

## <a name="summary"></a>Résumé

Cet article a décrit en détail la mise en sandbox d’une application Xamarin. Mac. Tout d’abord, nous avons créé une application Xamarin. Mac simple pour afficher les bases du bac à sable (sandbox) de l’application. Nous avons ensuite montré comment résoudre les violations du bac à sable (sandbox). Ensuite, nous avons examiné en profondeur le bac à sable (sandbox) de l’application et enfin, nous avons examiné la conception d’une application Xamarin. Mac pour le bac à sable de l’application.

## <a name="related-links"></a>Liens connexes

- [Publication sur l’App Store](~/mac/deploy-test/publishing-to-the-app-store/index.md)
- [À propos de l’application sandbox](https://developer.apple.com/library/content/documentation/Security/Conceptual/AppSandboxDesignGuide/AboutAppSandbox/AboutAppSandbox.html)
- [Problèmes courants de sandboxing d’application](https://developer.apple.com/library/content/qa/qa1773/_index.html)
