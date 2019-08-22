---
title: Présentation de fastlane pour iOS
description: Ce guide présente les différents outils fastlane à votre disposition pour signer le code des applications iOS. Il décrit comment mettre à jour, installer et utiliser les outils fastlane.
ms.prod: xamarin
ms.assetid: 8202C57D-22FF-4224-A5B1-AAEF12B7C106
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: becf9696a13dbd1ae96693729618d70b043d1c3a
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69527419"
---
# <a name="introduction-to-fastlane-for-ios"></a>Présentation de fastlane pour iOS

fastlane est un projet open source, créé pour simplifier le processus compliqué et souvent fastidieux de la mise en production d’applications Android et iOS. Il comprend plusieurs utilitaires, chacun traitant l’un des aspects suivants de la mise en production d’une application :

- [deliver](https://github.com/fastlane/fastlane/tree/master/deliver#readme) : gère les captures d’écran, les métadonnées et les bundles d’application, et les charge sur iTunes Connect.
- [produce](https://github.com/fastlane/fastlane/tree/master/produce#readme) : crée une application dans iTunes Connect et le portail des développeurs (souvent appelé AppID). Il inclut également la prise en charge des groupes d’applications et des services d’application.
- [pem](https://github.com/fastlane/fastlane/tree/master/pem#readme) : crée et gère les profils de provisionnement de notification Push.
- [gym](https://github.com/fastlane/fastlane/tree/master/gym#readme) : permet de générer et signer une application iOS. (Les applications Xamarin utilisent déjà MSBuild pour générer, signer et archiver les applications.)
- [cert](https://github.com/fastlane/fastlane/tree/master/cert#readme) : crée et gère les certificats de signature de code. 
- [sigh](https://github.com/fastlane/fastlane/tree/master/sigh#readme) : crée et gère les profils de provisionnement.
- [match](https://github.com/fastlane/fastlane/tree/master/match#readme) : crée et gère les profils et les certificats, et les stocke dans un référentiel Git pour permettre leur synchronisation entre les membres d’une équipe de développement.

fastlane peut s’utiliser de différentes façons : à l’aide de commandes de terminal, de méthodes basées sur des fichiers, ou de variables d’environnement pour les builds d’intégration continue. 

Ce guide traite spécifiquement de la configuration d’un appareil pour le développement d’applications iOS et, en particulier, des utilitaires **cert**, **sigh** et **match**. 

Les informations qu’il contient peuvent vous aider à bien démarrer avec la distribution d’applications, y compris pour automatiser entièrement le processus sur un serveur d’intégration continue. Toutefois, il est important de noter que fastlane est un produit tiers qui crée des outils pour prendre en charge les projets Xcode. Par conséquent, certains outils ou commandes comme `fastlane init` peuvent ne pas fonctionner comme prévu avec les fichiers csproj. Pour plus d’informations sur l’utilisation de Fastlane, des outils supplémentaires ou sur la libération pour Android à l’aide de Fastlane, reportez-vous à [https://fastlane.tools/](https://fastlane.tools/)

<a name="Installation" />

## <a name="installation"></a>Installation

1. Assurez-vous que les outils de ligne de commande Xcode sont installés sur la machine macOS. Pour installer les outils, exécutez la commande `xcode-select --install` dans le terminal. S’ils sont déjà installés, le message d’erreur suivant s’affiche :

    ```bash
    error: command line tools are already installed, use "Software Update" to install updates
    ```

2. Télécharger les outils Fastlane à partir de [https://download.fastlane.tools](https://download.fastlane.tools). 

    > [!NOTE]
    > Il est possible d’installer les outils fastlane sur Homebrew avec la commande `brew cask install fastlane` ou sur Rubygems (version 2.0 ou ultérieure) avec la commande `sudo gem install fastlane –NV`. Toutefois, l’utilisation du programme d’installation fourni garantit que les dépendances appropriées seront disponibles. 

3. Installez fastlane en décompressant le fichier et en double-cliquant sur l’exécutable `install`. Si vous obtenez une erreur indiquant que le fichier ne peut pas être ouvert, car il provient d’un développeur non identifié, appuyez sur OK et procédez comme suit :
    - Maintenez enfoncée la touche Ctrl en cliquant sur l’exécutable `install`. La boîte de dialogue ci-dessous s’affiche :

     ![](images/fastlane-image12.png "Boîte de dialogue d’installation")

    - Appuyez sur OK pour démarrer l’installation des outils fastlane.

4. Le terminal affiche la boîte de dialogue illustrée ci-dessous. Appuyez sur `y` :

   ![](images/fastlane-image13.png "Invite du terminal")

5. Exécutez `which fastlane` avant d’utiliser fastlane pour la première fois. Le chemin retourné doit être similaire à ceci : 

    ```bash
    /Users/[user]/.fastlane/bin
    ```

6. Si le chemin correspond au chemin indiqué ci-dessus, vous êtes prêt à commencer.

     Sinon, procédez ainsi :  Sur macOS, ouvrez `.bash_profile`, qui est un fichier texte en clair masqué dans le répertoire de base, avec la commande suivante :

    ```bash
    open ~/.bash_profile
    ```

7. Ajoutez la variable d’environnement PATH suivante, puis enregistrez-la : 

    ```bash
    export PATH="$HOME/.fastlane/bin:$PATH"
    ```

8. Réexécutez `which fastlane` pour vérifier que le chemin est similaire à `/Users/[user]/.fastlane/bin`


## <a name="updating-fastlane"></a>Mise à jour de fastlane

fastlane est un projet open source très actif qui envoie (push) régulièrement de nouvelles versions. Quand une nouvelle version de fastlane est disponible, vous en êtes averti dès que vous exécutez une commande fastlane :

[![](images/fastlane-image0.png "Invite de mise à jour de fastlane")](images/fastlane-image0.png#lightbox)


Pour mettre à jour votre version avec une nouvelle version de fastlane, téléchargez le dernier paquet [ici](https://download.fastlane.tools) et double-cliquez sur le paquet d’installation pour l’exécuter :

[![](images/fastlane-image0a.png "Exécution des packages d’installation")](images/fastlane-image0a.png#lightbox)


## <a name="contents"></a>Sommaire

Cette série de guides présente quelques-uns des outils que fastlane utilise pour signer le code d’une application iOS préalablement à son développement ou sa distribution. Les guides couvrent les outils suivants :

- [cert](~/ios/deploy-test/provisioning/fastlane/cert.md)
- [sigh](~/ios/deploy-test/provisioning/fastlane/sigh.md)
- [match](~/ios/deploy-test/provisioning/fastlane/match.md)

cert et sigh créent et gèrent les certificats de signature et les profils de provisionnement sur une machine locale. match va un peu plus loin dans ce processus. Il crée et gère les certificats et les profils de provisionnement, et les stocke dans un référentiel Git pour les mettre à la disposition de tous les membres d’une équipe de développement. Lisez chaque section pour savoir comment ces utilitaires fonctionnent et comment les utiliser.

## <a name="using-fastlane-tools-with-xamarin"></a>Utilisation des outils fastlane avec Xamarin

Une fois que vous avez créé une identité de signature et des profils de provisionnement avec fastlane, vous pouvez normalement définir facilement les options de signature de bundle dans Visual Studio pour Mac. Pour cela, les certificats et les clés privées doivent être dans le trousseau macOS et les profils de provisionnement doivent être dans le dossier `~/Library/MobileDevice/Provisioning Profiles`.

Pour définir les options de signature de code pour une application Xamarin.iOS, cliquez avec le bouton droit sur le nom du projet, sélectionnez **Options du projet > Build > Signature du bundle iOS** et définissez explicitement les options Identité de signature et Profil de provisionnement, comme illustré ci-dessous :

[![](images/fastlane-image11.png "Définir explicitement les options Identité de signature et Profil de provisionnement")](images/fastlane-image11.png#lightbox)

## <a name="related-links"></a>Liens associés

- [Documents fastlane](https://fastlane.tools/)
- [Documents sur la signature de code fastlane](https://docs.fastlane.tools/codesigning/getting-started/)
- [Guide sur la signature de code](https://codesigning.guide/)
