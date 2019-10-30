---
title: Appairer avec un Mac pour le développement Xamarin.iOS
description: Ce guide décrit comment utiliser Appairer avec un Mac pour connecter Visual Studio 2019 à un hôte de build Mac. Il explique notamment comment activer la connexion à distance sur le Mac, se connecter au Mac à partir de Visual Studio 2019 et ajouter manuellement un hôte de build Mac à l’ordinateur Windows.
ms.prod: xamarin
ms.assetid: 39DD7B3F-3E69-4E2A-B743-4C26AF613025
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/29/2018
ms.openlocfilehash: b8020ae8539e12640cde0d565ccbd2eb0c01fc30
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022495"
---
# <a name="pair-to-mac-for-xamarinios-development"></a>Appairer avec un Mac pour le développement Xamarin.iOS

_Ce guide explique comment utiliser la paire vers Mac pour connecter Visual Studio 2019 à un hôte de build Mac. Les mêmes instructions s’appliquent à Visual Studio 2017._

## <a name="overview"></a>Vue d'ensemble

La génération d’applications iOS natives nécessite l’accès aux outils de génération d’Apple, qui s’exécutent seulement sur un Mac. Pour cette raison, Visual Studio 2019 doit se connecter à un Mac accessible par le biais du réseau pour générer des applications Xamarin.iOS.

La fonctionnalité Appairer avec un Mac de Visual Studio 2019 permet de découvrir les hôtes de build Mac, de s’y connecter, de s’authentifier auprès d’eux et de les mémoriser afin que les développeurs iOS Windows puissent travailler de manière productive.

Avec Appairer avec un Mac, le flux de travail de développement est le suivant :

- Les développeurs peuvent écrire du code Xamarin.iOS dans Visual Studio 2019.

- Visual Studio 2019 ouvre une connexion réseau à un hôte de build Mac et utilise les outils de génération sur cet ordinateur pour compiler et signer l’application iOS.

- Il n’est pas nécessaire d’exécuter une application distincte sur le Mac : Visual Studio 2019 appelle les builds Mac en toute sécurité via SSH.

- Visual Studio 2019 est averti des modifications dès qu’elles se produisent. Par exemple, quand un appareil iOS est branché sur le Mac ou devient disponible sur le réseau, la barre d’outils iOS est instantanément mise à jour.

- Plusieurs instances de Visual Studio 2019 peuvent se connecter simultanément au Mac.

- Il est possible d’utiliser la ligne de commande Windows pour générer des applications iOS.

> [!NOTE]
>
> Avant de suivre les instructions fournies dans ce guide, effectuez les étapes ci-dessous :
>
> - Sur un ordinateur Windows, [installez Visual Studio 2019](~/get-started/installation/windows.md)
> - Sur un Mac, [installez Xcode](https://itunes.apple.com/us/app/xcode/id497799835?mt=12) et [Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/installation)
>   - _Vous devez ouvrir manuellement Xcode après l’installation_ afin que celui-ci puisse ajouter des composants supplémentaires.
>
> Si vous préférez ne pas installer Visual Studio pour Mac, Visual Studio 2019 peut configurer automatiquement l’hôte de build Mac avec Xamarin.iOS et Mono.
> Vous devez quand même installer et exécuter Xcode.
> Pour plus d’informations, consultez [Provisionnement Mac automatique](#automatic-mac-provisioning).

## <a name="enable-remote-login-on-the-mac"></a>Activer la session à distance sur le Mac

Pour configurer l’hôte de build Mac, activez tout d’abord la session à distance :

1. Sur le Mac, ouvrez les préférences système et accédez au volet **Partage**.

2. Cochez **Session à distance** dans la liste **Service**.

    ![Activation de Session à distance](images/sharing.png "Activation de Session à distance")

    Vérifiez qu’elle est configurée afin d’autoriser l’accès pour **Tous les utilisateurs**, ou que votre nom d’utilisateur ou groupe Mac figure dans la liste des utilisateurs autorisés.

3. Si vous y êtes invité, configurez le pare-feu macOS.

    Si le pare-feu macOS est défini pour bloquer les connexions entrantes, vous devrez peut-être autoriser `mono-sgen` à les recevoir. Une alerte s’affiche pour vous demander si c’est le cas.

4. S’il se trouve sur le même réseau que l’ordinateur Windows, le Mac doit maintenant pouvoir être découvert par Visual Studio 2019. Dans le cas contraire, essayez [d’ajouter manuellement un Mac](#manually-add-a-mac) ou examinez le [guide de résolution des problèmes](~/ios/get-started/installation/windows/connecting-to-mac/troubleshooting.md).

## <a name="connect-to-the-mac-from-visual-studio-2019"></a>Se connecter au Mac à partir de Visual Studio 2019

Maintenant que la session à distance est activée, connectez Visual Studio 2019 au Mac.

1. Dans Visual Studio 2019, ouvrez un projet iOS existant ou créez-en un en choisissant **Fichier > Nouveau > Projet**, puis en sélectionnant un modèle de projet iOS.

2. Ouvrez la boîte de dialogue **Appairer avec un Mac**.

    - Utilisez le bouton **Appairer avec un Mac** de la barre d’outils iOS :

      ![La barre d’outils iOS, avec le bouton « coupler à Mac » mis en surbrillance](images/ios-toolbar.png "La barre d’outils iOS, avec le bouton « coupler à Mac » mis en surbrillance")

    - Vous pouvez aussi sélectionner **Outils > iOS > Appairer avec un Mac**.

    - La boîte de dialogue **Appairer avec un Mac** affiche une liste de tous les hôtes de build Mac précédemment connectés et actuellement disponibles :

      ![Boîte de dialogue appairer avec un Mac](images/pairtomac.png "Boîte de dialogue appairer avec un Mac")

3. Sélectionnez un Mac dans la liste. Cliquez sur **Connexion**.

4. Entrez vos nom d’utilisateur et mot de passe.

    - La première fois que vous vous connectez à un Mac spécifique, vous êtes invité à entrer vos nom d’utilisateur et mot de passe pour cet ordinateur :

      ![Saisie d’un nom d’utilisateur et d’un mot de passe pour le Mac](images/auth.png "Saisie d’un nom d’utilisateur et d’un mot de passe pour le Mac")

      > [!TIP]
      > Lors de la connexion, utilisez votre nom d’utilisateur système plutôt que votre nom complet.

    - Appairer avec un Mac utilise ces informations d’identification pour créer une connexion SSH au Mac. En cas de réussite, une clé est ajoutée au fichier **authorized_keys** sur le Mac. Les connexions ultérieures au même Mac seront établies automatiquement.

5. Appairer avec un Mac configure automatiquement le Mac.

    [À compter de Visual Studio 2019 version 15.6](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes#automatic-macos-provisioning), Visual Studio 2019 installe ou met à jour Mono et Xamarin.iOS sur un hôte de build Mac connecté si nécessaire (notez que Xcode doit néanmoins être installé manuellement). Pour plus d’informations, consultez [Provisionnement Mac automatique](#automatic-mac-provisioning).

6. Recherchez l’icône d’état de connexion.

    - Quand Visual Studio 2019 est connecté à un Mac, l’élément de ce Mac dans la boîte de dialogue **Appairer avec un Mac** affiche une icône indiquant qu’il est actuellement connecté :

      ![Un Mac connecté](images/connected.png "Un Mac connecté")

      Il ne peut y avoir qu’un seul Mac connecté à la fois.

      > [!TIP]
      > Un clic droit sur n’importe quel Mac dans la liste **Appairer avec un Mac** permet d’afficher un menu contextuel dans lequel vous pouvez choisir **Se connecter...** , **Oublier ce Mac** ou **Se déconnecter**  :
      >
      > ![Les menus contextuels de paire à Mac](images/contextmenu.png "Les menus contextuels de paire à Mac")
      >
      > Si vous choisissez **Oublier ce Mac**, vos informations d’identification pour le Mac sélectionné seront oubliées. Pour vous reconnecter à ce Mac, vous devez entrer à nouveau vos nom d’utilisateur et mot de passe.

Si vous vous êtes correctement associé à un hôte de build Mac, vous êtes prêt à générer des applications Xamarin.iOS dans Visual Studio 2019. Examinez le guide [Introduction à Xamarin.iOS pour Visual Studio](~/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio.md).

Si vous n’avez pas réussi à vous associer à un Mac, essayez [d’ajouter manuellement un Mac](#manually-add-a-mac) ou examinez le [guide de résolution des problèmes](~/ios/get-started/installation/windows/connecting-to-mac/troubleshooting.md).

## <a name="manually-add-a-mac"></a>Ajouter manuellement un Mac

Si vous ne voyez pas un Mac particulier répertorié dans la boîte de dialogue **Appairer avec un Mac**, ajoutez-le manuellement :

1. Recherchez l’adresse IP de votre Mac.

    - Ouvrez **Préférences système > Partage > Session à distance** sur votre Mac :

      [![Adresse IP du Mac dans Préférences système > partage](images/sharing-ipaddress.png "Adresse IP du Mac dans Préférences système > partage")](images/sharing.png#lightbox)

    - Vous pouvez également utiliser la ligne de commande. Dans Terminal, exécutez cette commande :

      ```bash
      $ ipconfig getifaddr en0
      196.168.1.8
      ```

      Selon votre configuration réseau, vous devrez peut-être utiliser un nom d’interface autre que `en0`. Par exemple : `en1`, `en2`, etc.

2. Dans la boîte de dialogue **Appairer avec un Mac** de Visual Studio 2019, sélectionnez **Ajouter un Mac...**  :

    [![Bouton Ajouter un Mac dans la boîte de dialogue coupler au Mac](images/addtomac.png "Bouton Ajouter un Mac dans la boîte de dialogue coupler au Mac")](images/addtomac-large.png#lightbox)

3. Entrez l’adresse IP du Mac et cliquez sur **Ajouter** :

    ![Saisie de l’adresse IP du Mac](images/enteripaddress.png "Saisie de l’adresse IP du Mac")

4. Entrez vos nom d’utilisateur et mot de passe pour le Mac :

    ![Entrez un nom d’utilisateur et un mot de passe](images/auth.png "Saisie d’un nom d’utilisateur et d’un mot de passe")

   > [!TIP]
   > Lors de la connexion, utilisez votre nom d’utilisateur système plutôt que votre nom complet.

5. Cliquez sur **Connexion** pour connecter Visual Studio 2019 au Mac par le biais de SSH et l’ajouter à la liste des ordinateurs connus.

## <a name="automatic-mac-provisioning"></a>Provisionnement Mac automatique

À compter de [Visual Studio 2019 version 15,6](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes#automatic-macos-provisioning), coupler à Mac approvisionne automatiquement un Mac avec les logiciels nécessaires pour générer des applications Xamarin. iOS : mono, Xamarin. iOS (l’infrastructure logicielle, et non l’IDE Visual Studio pour Mac) et diverses Outils liés à Xcode (mais pas au Xcode lui-même).

> [!IMPORTANT]
>
> - Appairer avec un Mac ne peut pas installer Xcode ; vous devez l’installer manuellement sur l’hôte de build Mac. Il est requis pour le développement Xamarin.iOS.
> - Le provisionnement Mac automatique nécessite que la session à distance soit activée sur le Mac, et le Mac doit être accessible via le réseau à l’ordinateur Windows. Pour plus d’informations, consultez [Activer la session à distance sur le Mac](#enable-remote-login-on-the-mac).
> - Le provisionnement Mac automatique nécessite 3 Go d’espace libre sur le Mac pour l’installation de Xamarin.iOS.

Appairer avec un Mac effectue les installations/mises à jour de logiciels nécessaires quand Visual Studio 2017 se [connecte au Mac](#connect-to-the-mac-from-visual-studio-2019).

### <a name="mono"></a>Mono

Appairer avec un Mac vérifie que Mono est installé. S’il n’est pas installé, Appairer avec un Mac télécharge et installe la dernière version stable de Mono sur le Mac.

La progression est indiquée par différentes invites, comme illustré par les captures d’écran suivantes (cliquez pour effectuer un zoom) :

||Vérification de l’installation|Téléchargement|Installation de
|---|---|---|---|
|Mono|[![Installation de mono manquante](images/mono-missing.png "Installation de mono manquante")](images/mono-missing-large.png#lightbox)|[![Téléchargement de mono](images/mono-downloading.png "Téléchargement de mono")](images/mono-downloading-large.png#lightbox)|[![Installation de mono](images/mono-installing.png "Installation de mono")](images/mono-installing-large.png#lightbox)|

### <a name="xamarinios"></a>Xamarin.iOS

Appairer avec un Mac met à niveau Xamarin.iOS sur le Mac pour correspondre à la version installée sur l’ordinateur Windows.

> [!IMPORTANT]
> Appairer avec un Mac ne rétrograde pas Xamarin.iOS sur le Mac de la version alpha/bêta à la version stable. Si Visual Studio pour Mac est installé, définissez votre [canal des versions](https://docs.microsoft.com/visualstudio/mac/update) comme suit :
>
> - Si vous utilisez Visual Studio 2019, sélectionnez le canal des mises à jour **Stable** dans Visual Studio pour Mac.
> - Si vous utilisez Visual Studio 2019 Preview, sélectionnez le canal des mises à jour **Alpha** dans Visual Studio pour Mac.

La progression est indiquée par différentes invites, comme illustré par les captures d’écran suivantes (cliquez pour effectuer un zoom) :

||Vérification de l’installation|Téléchargement|Installation de
|---|---|---|---|
|Xamarin.iOS|[![Installation de Xamarin. iOS manquante](images/xamios-missing.png "Installation de Xamarin. iOS manquante")](images/xamios-missing-large.png#lightbox)|[![Téléchargement de Xamarin. iOS](images/xamios-downloading.png "Téléchargement de Xamarin. iOS")](images/xamios-downloading-large.png#lightbox)|[![Installation de Xamarin. iOS](images/xamios-installing.png "Installation de Xamarin.iOS")](images/xamios-installing-large.png#lightbox)|

### <a name="xcode-tools-and-license"></a>Licence et outils Xcode

Appairer avec un Mac vérifie également si Xcode a été installé et sa licence acceptée. Même si Appairer avec un Mac n’installe pas Xcode, il demande l’acceptation de la licence, comme illustré par les captures d’écran suivantes (cliquez pour effectuer un zoom) :

||Vérification de l’installation|Acceptation de la licence|
|---|---|---|
|Xcode|[![Installation de Xcode manquante](images/xcode-missing.png "Installation de Xcode manquante")](images/xcode-missing-large.png#lightbox)|[![Licence Xcode](images/xcode-license.png "Licence Xcode")](images/xcode-license-large.png#lightbox)|

En outre, Appairer avec un Mac installe ou met à jour différents packages distribués avec Xcode. Exemple :

- **MobileDeviceDevelopment.pkg**
- **XcodeExtensionSupport.pkg**
- **MobileDevice.pkg**
- **XcodeSystemResources.pkg**

L’installation de ces packages se produit rapidement et sans invite.

> [!NOTE]
> Ces outils sont distincts des outils en ligne de commande Xcode qui, à compter de macOS 10.9, sont [installés avec Xcode](https://developer.apple.com/library/content/technotes/tn2339/_index.html).

### <a name="troubleshooting-automatic-mac-provisioning"></a>Résolution des problèmes de provisionnement Mac automatique

Si vous rencontrez des problèmes d’utilisation du provisionnement Mac automatique, examinez les journaux IDE Visual Studio 2019, stockés dans **%LOCALAPPDATA%\Xamarin\Logs\15.0**. Ces journaux peuvent contenir des messages d’erreur pour vous aider à mieux diagnostiquer l’échec ou obtenir un support technique.

## <a name="build-ios-apps-from-the-windows-command-line"></a>Générer des applications iOS à partir de la ligne de commande Windows

Appairer avec un Mac prend en charge la génération d’applications Xamarin.iOS à partir de la ligne de commande. Exemple :

```bash
C:\samples\App1>msbuild App1.sln /p:ServerAddress=10.211.55.2 /p:ServerUser=xamUser /p:Platform=iPhoneSimulator /p:ServerPassword=mypassword
```

Les paramètres transmis à `msbuild` dans l’exemple ci-dessus sont :

- `ServerAddress` : adresse IP de l’hôte de build Mac.
- `ServerUser` : nom d’utilisateur à employer pour la connexion à l’hôte de build Mac.
  Utilisez votre nom d’utilisateur système plutôt que votre nom complet.
- `ServerPassword` : mot de passe à utiliser lors de la connexion à l’hôte de build Mac.

> [!NOTE]
> Visual Studio 2019 stocke `msbuild` dans le répertoire suivant : **C:\Program Files (x86) \Microsoft Visual Studio\2017\\&lt;Version&gt;\MSBuild\15.0\Bin**

La première fois que la fonctionnalité Appairer avec un Mac se connecte à un hôte de build Mac particulier à partir de Visual Studio 2019 ou de la ligne de commande, elle configure les clés SSH. Avec ces clés, les connexions ultérieures ne nécessitent pas de nom d’utilisateur ni de mot de passe. Les clés nouvellement créées sont stockées dans **%LOCALAPPDATA%\Xamarin\MonoTouch**.

Si le paramètre `ServerPassword` est omis à partir d’un appel de build de ligne de commande, Appairer avec un Mac tente de se connecter à l’hôte de build Mac à l’aide des clés SSH enregistrées.

## <a name="summary"></a>Récapitulatif

Cet article a décrit comment utiliser Appairer avec un Mac pour connecter Visual Studio 2019 à un hôte de build Mac, ce qui permet aux développeurs Visual Studio 2019 de générer des applications iOS natives avec Xamarin.iOS.

## <a name="next-steps"></a>Étapes suivantes

- [Résolution des problèmes de connexion](~/ios/get-started/installation/windows/connecting-to-mac/troubleshooting.md)
- [Vidéo sur l’agent de build Mac Xamarin](https://www.youtube.com/watch?v=MBAPBtxkjFQ)
- [Introduction à Xamarin.iOS pour Visual Studio](~/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio.md)
- [Remoted iOS Simulator pour Windows](~/tools/ios-simulator/index.md)
- [Déploiement sans fil](~/ios/deploy-test/wireless-deployment.md)
