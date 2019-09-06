---
title: Introduction à Xamarin.iOS pour Visual Studio
description: Ce document décrit comment générer et tester des applications Xamarin.iOS à l’aide de Visual Studio. Il traite de la création d’un projet, de l’exécution et du débogage d’une application, ainsi que de la connexion à un hôte de build Mac à partir de Windows.
ms.prod: xamarin
ms.assetid: bf3c779f-959f-428d-babb-428f363f7e4e
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 05/02/2018
ms.openlocfilehash: 45c7c3a90fa0dedfed8f5f325a96b4c7aa7a5a9e
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292033"
---
# <a name="introduction-to-xamarinios-for-visual-studio"></a>Introduction à Xamarin.iOS pour Visual Studio

Xamarin pour Windows permet que des applications iOS soient écrites et testées dans Visual Studio, avec un Mac en réseau qui fournit le service de build et de déploiement.

Cet article décrit les étapes d’installation et de configuration des outils Xamarin.iOS sur chaque ordinateur pour générer des applications iOS à l’aide de Visual Studio.

Développer du contenu pour iOS dans Visual Studio offre de nombreux avantages :

- Création de solutions multiplateformes pour applications iOS, Android et Windows.
- Utilisation de vos outils Visual Studio favoris (comme **ReSharper** et **Team Foundation Server**) pour tous vos projets multiplateformes, dont le code source iOS.
- Travailler avec un environnement IDE familier, tout en tirant parti des liaisons Xamarin.iOS de toutes les API d’Apple.

## <a name="requirements-and-installation"></a>Exigences et installation

Il existe quelques exigences à respecter lors du développement pour iOS dans Visual Studio. Comme indiqué brièvement dans la vue d’ensemble, un Mac est exigé pour compiler des fichiers IPA, et il n’est pas possible de déployer des applications sur un appareil sans les certificats et outils de signature de code d’Apple.

Un certain nombre d’options de configuration sont disponibles pour vous permettre de déterminer laquelle fonctionne le mieux pour vos besoins de développement. Elles sont notamment les suivantes :

- Utilisez un Mac comme ordinateur de développement principal et exécutez une machine virtuelle Windows sur laquelle Visual Studio est installé. Nous recommandons d’utiliser un logiciel de machine virtuelle comme [Parallels](http://www.parallels.com/products/desktop/) ou [VMWare](http://www.vmware.com/products/fusion/).
- Utilisez un Mac exactement comme un hôte de build. Dans ce scénario, il serait connecté au même réseau qu’un ordinateur Windows sur lequel les outils [nécessaires](~/get-started/installation/windows.md#installation) sont installés.

Dans les deux cas, vous devez effectuer les étapes suivantes :

- [Installer Visual Studio pour Mac](https://docs.microsoft.com/visualstudio/mac/installation)
- [Installer les outils Xamarin sur Windows](~/get-started/installation/windows.md)

## <a name="connecting-to-the-mac"></a>Connexion au Mac

Pour connecter Visual Studio à votre hôte de build Mac, suivez les instructions du guide [Appairer avec un Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

## <a name="visual-studio-toolbar-overview"></a>Vue d’ensemble de la barre d’outils de Visual Studio

Xamarin iOS pour Visual Studio ajoute des éléments à la barre d’outils standard et à la nouvelle barre d’outils iOS.
Les fonctions de ces barres d’outils sont décrites ci-dessous.

### <a name="standard-toolbar"></a>Barre d’outils standard

Les contrôles qui concernent le développement Xamarin iOS sont entourés en rouge :

[![](introduction-to-xamarin-ios-for-visual-studio-images/03.png "Les contrôles qui concernent le développement Xamarin iOS sont entourés en rouge")](introduction-to-xamarin-ios-for-visual-studio-images/03.png#lightbox "Les contrôles qui concernent le développement Xamarin iOS sont entourés en rouge")

- **Démarrer** : démarre le débogage ou l’exécution de l’application sur la plateforme sélectionnée. Un Mac doit être connecté (voir l’indicateur d’état dans la barre d’outils iOS).
- **Configurations de solutions** : vous permet de sélectionner la configuration à utiliser (par exemple, Debug, Release).
- **Plateformes solution** : vous permet de sélectionner iPhone ou iPhoneSimulator pour le déploiement.

### <a name="ios-toolbar"></a>Barre d’outils iOS

La barre d’outils iOS dans Visual Studio est la même dans chaque version de Visual Studio. La voici :

[![](introduction-to-xamarin-ios-for-visual-studio-images/iostoolbar.png "Barre d’outils iOS")](introduction-to-xamarin-ios-for-visual-studio-images/iostoolbar.png#lightbox)

Chaque élément est décrit ci-dessous :

- **Mac Agent/Gestionnaire des connexions** : affiche la boîte de dialogue Mac Agent Xamarin. Cette icône s’affiche en *orange* pendant que la connexion est en cours, et en *vert* une fois la connexion établie.
- **Afficher le simulateur iOS** : fait passer la fenêtre du simulateur iOS au premier plan sur le Mac.
- **Afficher le fichier IPA sur le serveur de builds** : ouvre Finder sur le Mac à l’emplacement du fichier de sortie IPA de l’application.

## <a name="ios-output-options"></a>Options de sortie iOS

### <a name="output-window"></a>Fenêtre Sortie

Le volet *Sortie* contient des options que vous pouvez examiner pour découvrir les messages et erreurs de build, de déploiement et de connexion.

La capture d’écran ci-dessous montre les fenêtres Sortie disponibles. Elles peuvent varier en fonction du type de votre projet :

[![](introduction-to-xamarin-ios-for-visual-studio-images/output-sml.png "Les fenêtres de sortie disponibles")](introduction-to-xamarin-ios-for-visual-studio-images/output-large.png#lightbox)

- **Xamarin** : cette fenêtre contient des informations concernant uniquement Xamarin, comme la connexion au Mac et l’état de l’activation.

  [![](introduction-to-xamarin-ios-for-visual-studio-images/output3-sml.png "Informations concernant uniquement Xamarin, comme la connexion au Mac et l’état d’activation")](introduction-to-xamarin-ios-for-visual-studio-images/output3-large.png#lightbox)

- **Diagnostics Xamarin** : cette fenêtre affiche des informations plus détaillées sur votre projet Xamarin, comme l’interaction avec et pour Android.

  [![](introduction-to-xamarin-ios-for-visual-studio-images/output4-sml.png "Informations détaillées sur le projet Xamarin")](introduction-to-xamarin-ios-for-visual-studio-images/output3-large.png#lightbox)

D’autres volets de sortie de Visual Studio par défaut, comme les volets de débogage et de génération, sont toujours disponibles dans la vue Sortie. Ils sont utilisés pour la sortie de débogage et pour la sortie MSBuild :

- **Déboguer**

  [![](introduction-to-xamarin-ios-for-visual-studio-images/output2-sml.png "Sortie du débogage")](introduction-to-xamarin-ios-for-visual-studio-images/output2-large.png#lightbox)

- **Génération** & **Ordre de génération**

  [![](introduction-to-xamarin-ios-for-visual-studio-images/output1-sml.png "Sortie MSBuild")](introduction-to-xamarin-ios-for-visual-studio-images/output1-large.png#lightbox)

## <a name="ios-project-properties"></a>Propriétés de projet iOS

Pour accéder aux propriétés de projet de Visual Studio, cliquez avec le bouton droit sur le nom du projet, puis sélectionnez *Propriétés* dans le menu contextuel. Vous pouvez alors configurer votre application iOS, comme l’illustre la capture d’écran ci-dessous :

![](introduction-to-xamarin-ios-for-visual-studio-images/iosproperties.png "Configuration d’une application iOS")

- *Signature du bundle iOS* : établit une connexion au Mac pour remplir les identités de signature de code et les profils de provisionnement :

  ![Remplir les identités de signature de code et les profils de provisionnement](introduction-to-xamarin-ios-for-visual-studio-images/bundlesigning.png)

- *Options IPA iOS* : le fichier IPA sera enregistré sur le système de fichiers du Mac :

  ![Options IPA iOS](introduction-to-xamarin-ios-for-visual-studio-images/ipaoptions.png)

- *Options d’exécution iOS* : permet de configurer des paramètres supplémentaires :

  ![Options d’exécution iOS](introduction-to-xamarin-ios-for-visual-studio-images/iosrunoptions.png)

## <a name="creating-a-new-project-for-ios-applications"></a>Création d’un projet pour des applications iOS

Pour créer un projet iOS dans Visual Studio, procédez exactement comme pour n’importe quel autre type de projet. Quand vous sélectionnez **Fichier > Nouveau projet**, la boîte de dialogue affichée ci-dessous s’ouvre, avec des types de projet disponibles pour créer un projet iOS :

![Création d'un projet](introduction-to-xamarin-ios-for-visual-studio-images/newproject.w157.png)

Sélectionnez **Application iOS (Xamarin)** pour afficher les modèles suivants à partir desquels vous pouvez créer une application Xamarin.iOS :

![Sélection du modèle pour une application iOS](introduction-to-xamarin-ios-for-visual-studio-images/newproject-2.w157.png)

Les fichiers de plan conceptuel et .xib peuvent être modifiés dans Visual Studio à l’aide du Concepteur iOS. Pour créer un plan conceptuel, choisissez l’un des modèles de plan conceptuel. Cette opération génère un fichier **Main.storyboard** dans l’**Explorateur de solutions**, comme l’illustre la capture d’écran ci-dessous :

![Fichier Main.storyboard dans l’Explorateur de solutions](introduction-to-xamarin-ios-for-visual-studio-images/solution-explorer-new.w157.png)

Pour commencer à créer ou à modifier votre plan conceptuel, double-cliquez sur `Main.storyboard` pour l’ouvrir dans le Concepteur iOS :

![](introduction-to-xamarin-ios-for-visual-studio-images/iosdesigner.png "Main.storyboard dans le Concepteur iOS")

Pour ajouter des objets à votre vue, utilisez le volet **Boîte à outils** pour faire glisser et déposer des éléments sur votre aire de conception. Pour ajouter la boîte à outils, si ce n’est pas déjà fait, sélectionnez **Affichage > Boîte à outils**. Il est possible de modifier les propriétés des objets, d’ajuster leur disposition et de créer des événements à l’aide du volet **Propriétés**, comme illustré ci-dessous :

![](introduction-to-xamarin-ios-for-visual-studio-images/properties.png "Volet Propriétés")

 Pour plus d’informations sur l’utilisation du Concepteur iOS, reportez-vous aux guides relatifs au [Concepteur](~/ios/user-interface/designer/index.md).

## <a name="running--debugging-ios-applications"></a>Exécution et débogage d’applications iOS

### <a name="device-logging"></a>Journalisation des appareils

Dans Visual Studio 2017, les blocs de journaux iOS et Android sont unifiés.

La nouvelle fenêtre Journal de l’appareil pour Visual Studio permet d’afficher les journaux des appareils Android et iOS. Pour l’afficher, exécutez l’une des commandes suivantes :

- **Affichage > Autres fenêtres > Journal de l’appareil**
- **Outils > iOS > Journal de l’appareil**
- **Barre d’outils iOS > Journal de l’appareil**

Une fois la fenêtre Outil affichée, l’utilisateur peut sélectionner l’appareil physique dans la liste déroulante des appareils. Quand un appareil est sélectionné, les journaux sont automatiquement ajoutés à la table. Le passage d’un appareil à l’autre arrête et démarre la journalisation des appareils.

Pour que les appareils apparaissent dans la zone de liste déroulante, un projet iOS doit être chargé. De plus, pour iOS, Visual Studio doit être [connecté au serveur Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) pour détecter les appareils iOS connectés au Mac.

Cette fenêtre Outil fournit les éléments suivants : une table des entrées de journal, une liste déroulante pour sélectionner l’appareil, un moyen d’effacer des entrées de journal, une zone de recherche et des boutons de lecture/d’arrêt/de pause.

### <a name="set-debugging-stops"></a>Définir des arrêts de débogage

Il est possible de définir des points d’arrêt à tout moment dans votre application pour indiquer au débogueur d’interrompre temporairement l’exécution du programme. Pour définir un point d’arrêt dans votre Visual Studio, cliquez sur la zone de marge de votre éditeur, en regard du numéro de ligne du code où vous voulez marquer un arrêt :

![](introduction-to-xamarin-ios-for-visual-studio-images/image18.png "Définition d’un point de débogage")

Démarrez le débogage, puis utilisez le simulateur ou l’appareil pour parcourir votre application jusqu’à un point d’arrêt. Quand un point d’arrêt est atteint, la ligne est mise en surbrillance et le comportement de débogage normal de Visual Studio est activé : vous pouvez effectuer un pas à pas détaillé, principal ou sortant du code, examiner les variables locales ou utiliser la fenêtre Exécution.

Cette capture d’écran présente le simulateur iOS en cours d’exécution à côté de Visual Studio à l’aide de Parallels sur macOS :

![Cette capture d’écran présente le simulateur iOS en cours d’exécution à côté de Visual Studio à l’aide de Parallels sur macOS](introduction-to-xamarin-ios-for-visual-studio-images/image19.png)

### <a name="examine-local-variables"></a>Examiner les variables locales

![Examen des variables locales avec le débogage](introduction-to-xamarin-ios-for-visual-studio-images/image20.png)

## <a name="summary"></a>Récapitulatif

Cet article a expliqué comment utiliser Xamarin iOS pour Visual Studio. Il a répertorié les différentes fonctionnalités disponibles pour la création, la génération et le test d’une application iOS à partir de Visual Studio, et a décrit en détail la génération et le débogage d’une application iOS simple.

## <a name="related-links"></a>Liens associés

- [Installation de Xamarin.iOS](~/ios/get-started/installation/windows/index.md)
- [Provisionnement des appareils](~/ios/get-started/installation/device-provisioning/index.md)
- [Création d’une interface utilisateur iOS dans le code](~/ios/app-fundamentals/ios-code-only.md)
- [Connecter un Mac à un environnement Visual Studio avec XMA (vidéo)](https://university.xamarin.com/lightninglectures/xamarin-mac-agent)
