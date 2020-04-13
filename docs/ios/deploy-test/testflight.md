---
title: Utilisation de TestFlight pour distribuer des applications Xamarin.iOS
description: TestFlight est un outil racheté par Apple. Il constitue le principal moyen de tester les versions bêta de vos applications Xamarin.iOS. Cet article vous guide à travers toutes les étapes du processus TestFlight, depuis le chargement de votre application jusqu’à l’utilisation d’iTunes Connect.
ms.prod: xamarin
ms.assetid: BA880768-2BC8-41E4-B57E-A56F8EED4690
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 2f5d04b49658abe9d366e264d8f1a659732a2c33
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "79304204"
---
# <a name="using-testflight-to-distribute-xamarinios-apps"></a>Utilisation de TestFlight pour distribuer des applications Xamarin.iOS

_TestFlight est maintenant détenue par Apple, et est le principal moyen de tester bêta vos applications Xamarin.iOS. Cet article vous guidera à travers toutes les étapes du processus TestFlight - de télécharger votre application, à travailler avec iTunes Connect._

Le bêta testing fait partie intégrante du cycle de développement des logiciels. Il existe de nombreuses applications multiplateformes conçues pour simplifier ce processus, parmi lesquelles [HockeyApp](https://hockeyapp.net/features/), [Applause](https://www.applause.com/mobile-app-testing)et, bien sûr, l’outil de bêta testing natif de Google Play pour les applications Android. Ce document couvre exclusivement l’outil TestFlight d’Apple.

TestFlight est un service Apple de bêta testing pour les applications iOS. Il est uniquement accessible à partir d’[iTunes Connect](https://itunesconnect.apple.com/), et disponible pour les applications iOS 8.0 et versions ultérieures. TestFlight permet de faire tester les versions bêta d’une application par des utilisateurs internes et externes. De plus, la revue de l’application bêta effectuée par des testeurs externes simplifie nettement le processus de revue finale avant la publication sur l’App Store.

Auparavant, le binaire devait être généré dans Visual Studio pour Mac, puis chargé sur le site web TestFlightApp pour être distribué aux testeurs. Ce nouveau processus apporte plusieurs améliorations qui vous permettront de diffuser des applications testées et de grande qualité sur l’App Store. Par exemple :

- La revue d’application bêta, qui est obligatoire dans le cadre de tests externes, augmente les chances de validation de l’application lors de la revue finale dans l’App Store, car ces deux revues sont soumises au respect des règles d’Apple.
- Pour pouvoir être chargée, l’application doit avoir été préalablement enregistrée dans iTunes Connect. Cela garantit la correspondance entre les certificats, les noms et les profils de provisionnement.
- L’application TestFlight est maintenant une réelle application iOS. Elle s’exécute donc plus rapidement.
- À la fin du bêta testing, la soumission de l’application pour revue s’effectue rapidement et simplement, en un clic.

## <a name="requirements"></a>Spécifications

TestFlight permet uniquement de tester des applications iOS 8.0 ou version ultérieure.

Tous les testeurs doivent tester l’application au moins sur un appareil iOS 8. Toutefois, la bonne pratique est de tester l’application sur toutes les versions d’iOS.

## <a name="provisioning"></a>Approvisionnement

Pour tester vos builds avec TestFlight, vous devez créer un *profil de distribution sur l’App Store* qui intègre le nouveau droit bêta. Ce droit active le bêta testing dans TestFlight ; il est automatiquement inclus dans chaque **nouveau** profil de distribution sur l’App Store. Pour générer un nouveau profil, vous pouvez suivre les instructions pas à pas fournies dans [Création d’un profil de distribution](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioningprofile).

Vous pouvez vérifier si votre profil de distribution contient le droit bêta au moment de la [validation de votre build dans Xcode](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md), comme illustré ci-dessous :

[![](testflight-images/validate-build.png "Submitting the App to Apple")](testflight-images/validate-build.png#lightbox)

## <a name="testflight-workflow"></a>Workflow TestFlight

Le workflow suivant décrit les étapes à effectuer pour commencer à utiliser TestFlight afin de tester les versions bêta de votre application :

1. Pour les nouvelles applications, créez un [enregistrement iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md).
2. [Archivez et publiez](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md) votre application dans iTunes Connect.
3. Gérez le bêta testing :
    - Ajoutez les métadonnées.
    - Ajoutez les utilisateurs internes :
      - Le maximum est de 25 utilisateurs.
    - Ajoutez les utilisateurs externes :
      - Le maximum est de 1 000 utilisateurs.
      - Nécessite une revue de test bêta, qui doit respecter les règles d’Apple.
4. Collectez les commentaires des utilisateurs, prenez les actions requises, puis recommencez l’étape 2.

## <a name="create-an-itunes-connect-record"></a>Créer un enregistrement iTunes Connect

1. Connectez-vous au [portail iTunes Connect](https://itunesconnect.apple.com/) avec vos identifiants développeur Apple.
2. Sélectionnez **Mes applications**:

    [![](testflight-images/my-apps.png "Select My Apps")](testflight-images/my-apps.png#lightbox)

3. Dans l’écran **Mes apps**, cliquez sur le bouton **+** en haut à gauche de l’écran pour ajouter une nouvelle application. Si vous avez des comptes de développeur Mac et iOS, vous êtes invité à choisir le type de la nouveau application.

La fenêtre de soumission **Nouvelle app iOS** qui s’affiche doit contenir exactement les mêmes informations que celles figurant dans le fichier Info.plist de votre application

Pour plus d’informations sur la création d’un enregistrement iTunes Connect, consultez le guide [Création d’un enregistrement iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md).

### <a name="completing-the-new-ios-app-submission-form"></a>Renseigner le formulaire de soumission de la nouvelle application iOS

Le formulaire doit comporter exactement les mêmes informations que celles du fichier Info.plist de votre application, comme illustré ci-dessous :

[![](testflight-images/infoplist.png "The app's Info.plist")](testflight-images/infoplist.png#lightbox)
[![](testflight-images/newiosapp.png "The form on iTunes Connect")](testflight-images/newiosapp.png#lightbox)

- **Nom** : nom descriptif entré au moment de la création du bundle d’application. Ce nom doit être strictement identique au **nom d’application** entré dans votre fichier `Info.plist`.
- **Langue principale** : langue par défaut utilisée dans l’application. Il s’agit généralement de la langue dans laquelle vous parlez.
- **Identifiant de lot** : liste déroulante répertoriant tous les ID d’application créés dans votre compte de développeur.
  - **Suffixe de l’identifiant de lot** : si vous avez sélectionné un identifiant de lot générique (par exemple, qui se termine par un *, comme dans l’exemple ci-dessus), vous êtes invité à entrer le suffixe dans la zone supplémentaire qui s’affiche. Dans l’exemple, l’**identifiant de lot** est `mobi.chkn.*` et le suffixe est **PageView**. Ces deux éléments composent l’**identificateur de lot** spécifié dans le fichier `Info.plist`.
- **Version** : numéro de version de l’application chargée. La version est choisie par le développeur.
- **SKU** : la référence SKU est un identificateur unique pour votre application. Elle n’est pas visible par les utilisateurs. Elle peut être considérée comme un ID produit. Dans l’exemple ci-dessus, j’ai choisi la date avec un numéro de version pour cette date.

## <a name="upload-your-app"></a>Charger votre application

Une fois que vous avez créé l’enregistrement iTunes Connect, vous pouvez charger les nouvelles builds. N’oubliez pas que les builds doivent avoir le nouveau droit bêta.

Tout d’abord, générez votre [distribuable final](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md) dans l’IDE, puis [soumettez votre application à Apple](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md) par le biais du programme Application Loader ou de la fonction Archiver dans Xcode.

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

### <a name="create-an-archive"></a>Créer une archive

 Pour générer un binaire dans Visual Studio pour Mac, utilisez la fonction _Archiver_. Cliquez avec le bouton droit sur le projet, puis sélectionnez **Archiver pour publication**, comme illustré ci-dessous :

 [![](testflight-images/new-archive.png "Select Archive for Publishing")](testflight-images/new-archive.png#lightbox)

 Pour plus d’informations, consultez le guide [Génération du distribuable](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md).

### <a name="sign-and-distribute-your-app"></a>Signer et distribuer votre application

 La création d’une archive ouvre automatiquement la **vue Archives**, dans laquelle sont affichés tous les projets archivés, regroupés par solution. Pour signer votre application et la préparer pour la distribution, sélectionnez **Signer et distribuer...**, comme ci-dessous :

[![](testflight-images/archive-view.png "Creating an archive will automatically open the Archives View")](testflight-images/archive-view.png#lightbox)

 Cela entraîne l’ouverture de l’Assistant Publication. Sélectionnez le canal de distribution **app Store** pour créer un package et ouvrez chargeur d’applications. Dans l’écran Profil de provisionnement, sélectionnez votre identité de signature et le profil de provisionnement, ou resignez avec une autre identité. Vérifiez les détails de votre package, puis cliquez sur **Publier** pour enregistrer votre fichier `.ipa`

[![](testflight-images/group.png "Select your signing identity and provisioning profile, or re-sign with another identity")](testflight-images/group.png#lightbox)

 Pour plus d’informations sur ces étapes, consultez la section [Soumission de votre application à Apple](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md).

### <a name="submitting-your-build"></a>Soumission de votre build
 L’Assistant Publication ouvre le programme Application Loader pour vous permettre de charger votre build dans iTunes Connect. Sélectionnez l’option **Deliver Your App** et chargez le fichier `.ipa` créé plus haut. Application Loader valide votre build, puis la charge dans iTunes Connect.

 Pour plus d’informations sur ces étapes, consultez la section [Soumission de votre application à Apple](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md).

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

### <a name="building-your-final-distributable"></a>Génération de votre distribuable final
 Le plug-in Xamarin pour Visual Studio ne prend pas en charge l’archivage des applications Xamarin.iOS pour la publication sur l’App Store. Pour publier une application iOS à partir de Visual Studio, vous avez le choix entre les Ces règles sont les suivantes :

1. Charger un IPA créé avec la commande Générer un paquet ad-hoc (IPA).
1. Charger un bundle `.app` zippé.

 Le guide [Génération du distribuable](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md) fournit des instructions pour ces deux options.

### <a name="submitting-your-build"></a>Soumission de votre build
 Pour soumettre votre application à Apple, vous devez utiliser le programme Application Loader, qui est installé sur l’hôte de build avec Xcode. Pour plus d’informations sur l’accès à Application Loader, consultez le guide [Accéder à Application Loader](https://help.apple.com/itc/apploader/#/apdATD1E927-D1E1A1303-D1E927A1126) d’Apple.

Une fois le programme ouvert, sélectionnez l’option **Deliver Your App**, puis chargez le fichier zip ou `.ipa` créé plus haut. Application Loader valide votre build, puis la charge dans iTunes Connect.

 Pour plus d’informations sur ces étapes, consultez la section [Soumission de votre application à Apple](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md).

-----

Le guide [Publication sur l’App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md) décrit toutes les étapes ci-dessus plus en détail. Consultez-le si vous avez besoin d’informations plus complètes sur le processus de soumission à l’App Store.

Revenez à la section **Mes apps** dans iTunes Connect. Vous devez normalement voir que votre application a bien été chargée. Vous êtes maintenant prêt à commencer le bêta testing !

## <a name="manage-beta-testing"></a>Gérer le bêta testing

### <a name="add-metadata"></a>Ajouter des métadonnées

Pour commencer à utiliser TestFlight, accédez à l’onglet **Préversion** de votre application. Vous devez voir trois onglets affichant une liste de builds, de testeurs internes et de testeurs externes, comme illustré ci-dessous :

[![](testflight-images/app-uploaded.png "Builds, Internal Testers, and External Testers tabs")](testflight-images/app-uploaded.png#lightbox)

Pour ajouter les métadonnées à votre application, cliquez sur le numéro de build, puis sur TestFlight :

[![](testflight-images/metadata.png "Add Metadata")](testflight-images/metadata.png#lightbox)

Sous **Test Information**, entrez les informations relatives à votre application dont peuvent avoir besoin les testeurs. Par exemple :

- Les éléments à tester
- La description de votre application
- L’URL marketing, pour obtenir des informations sur l’application ajoutée
- L’URL de la politique de confidentialité, pour obtenir des informations sur la politique de confidentialité de votre entreprise
- Un e-mail pour les commentaires

Notez que ces métadonnées **ne sont pas** obligatoires pour les testeurs internes, mais qu’elles le **sont** pour les testeurs externes.

<a name="beta-testing" />

### <a name="enable-beta-testing"></a>Activer le bêta testing

Quand vous êtes prêt à démarrer les tests de votre application, activez le bouton **TestFlight Beta Testing** pour votre version :

[![](testflight-images/turn-on-testing.png "Turn on the TestFlight Beta Testing switch")](testflight-images/turn-on-testing.png#lightbox)

Chaque build reste active pendant **60 jours** à partir de la date d’activation du bouton TestFlight Beta Testing. Vous pouvez voir le nombre de jours restants pour chaque build dans la page **Test information** :

[![](testflight-images/daysleft.png "The Test Information page")](testflight-images/daysleft.png#lightbox)

Vous pouvez désactiver les tests à tout moment.

### <a name="internal-testers"></a>Testeurs internes

Les testeurs internes sont des membres de votre équipe de développement auxquels a été attribué l’un des rôles suivants dans iTunes Connect :

- **Administrateur** : un administrateur est responsable de l’ajout et de la gestion des nouveaux utilisateurs dans iTunes Connect.
- **Juridique** : l’Agent de l’équipe est le seul utilisateur administrateur qui peut avoir ce rôle. Il a en charge la signature des contrats.
- **Technique** : un utilisateur technique peut modifier la plupart des propriétés d’une application. Par exemple, il peut modifier les informations de l’application, charger un binaire et soumettre une application pour revue.

Chaque build peut être partagée par 25 membres au maximum.

Pour ajouter des testeurs, accédez à la section **Utilisateurs et rôles** dans l’écran iTunes Connect :

[![](testflight-images/users-and-roles.png "Users and Roles on the main iTunes Connect screen")](testflight-images/users-and-roles.png#lightbox)

Les utilisateurs iTunes Connect existants sont affichés dans la liste. Pour les sélectionner, cliquez sur leur nom, activez le bouton **Testeur interne**, puis cliquez sur **Enregistrer** :

[![](testflight-images/internal-tester.png "Turn on the Internal Tester switch")](testflight-images/internal-tester.png#lightbox)

Pour ajouter un utilisateur qui n’est **+** pas sur la liste, sélectionnez le bouton à côté *des utilisateurs,* et fournissez un prénom, un nom de famille et une adresse e-mail pour créer un compte. L’utilisateur doit confirmer son adresse e-mail pour activer son compte :

[![](testflight-images/add-new-user.png "Adding a user")](testflight-images/add-new-user.png#lightbox)

Si vous revenez à l’écran **Mes apps > Préversion > Testeurs internes**, vous voyez maintenant tous les utilisateurs qui ont été ajoutés comme bêta testeurs internes dans TestFlight :

[![](testflight-images/select-users.png "A list of users that have been added for TestFlight Internal beta testing")](testflight-images/select-users.png#lightbox)

Vous pouvez inviter ces testeurs en sélectionnant leur nom et en cliquant sur le bouton **Inviter**. Les testeurs recevront une invitation par e-mail pour tester votre application.

Vous pouvez voir l’état de leur invitation dans la colonne d’état de la page Testeurs internes :

[![](testflight-images/status-added.png "The invitation status")](testflight-images/status-added.png#lightbox)

### <a name="external-testers"></a>Testeurs externes

Avant d’inviter des testeurs externes à tester votre application en version bêta, vous devez soumettre l’application à une revue d’application bêta pour vérifier qu’elle est conforme aux règles expliquées dans le document [App Store Review Guidelines](https://developer.apple.com/app-store/review/guidelines/).

Pour soumettre votre application à une revue, cliquez sur le texte **Submit For Beta App Review** à côté de votre build, comme indiqué dans l’image ci-dessous :

[![](testflight-images/beta-app-review.png "Submit For Beta App Review")](testflight-images/beta-app-review.png#lightbox)

Pour préparer la revue de votre application, vous devez entrer toutes les métadonnées demandées dans la page TestFlight Beta Information.

Vous pouvez maintenant commencer à préparer les invitations et ajouter jusqu’à 2 000 testeurs externes dans l’onglet correspondant, en entrant leur adresse e-mail, leur prénom et leur nom, comme illustré dans la capture d’écran suivante. L’adresse e-mail n’est pas obligatoirement leur ID Apple. C’est simplement l’e-mail utilisé pour l’envoi de l’invitation.

[![](testflight-images/add-external.png "Invite testers")](testflight-images/add-external.png#lightbox)

Si vous avez beaucoup de testeurs externes, vous pouvez utiliser le lien **Importer un fichier** pour importer un fichier `CSV` au format de ligne suivant :

``` 
first name, last name, email address
```

Vous pouvez également ajouter les testeurs externes dans des groupes différents afin d’avoir une organisation plus claire.

Une fois que vous avez entré les détails des testeurs externes, cliquez sur **Ajouter** et confirmez que vous avez obtenu le consentement des utilisateurs pour les inviter :

[![](testflight-images/confirm-consent.png "Confirm you have the users consent to invite them")](testflight-images/confirm-consent.png#lightbox)

Si votre application est validée après la phase de revue d’application bêta, vous pouvez ensuite envoyer des invitations aux testeurs externes. Le texte sous **External** dans la page de build se change alors en **Send Invites**. Cliquez sur ce texte pour envoyer les invitations à tous les testeurs que vous avez déjà ajoutés.

Si votre application a été rejetée, vous devez corriger les problèmes décrits dans la page **Resolution Center**, puis resoumettre le binaire corrigé pour revue.

## <a name="as-a-beta-tester"></a>Bêta testeur

Les testeurs que vous avez invités reçoivent un e-mail similaire à celui-ci :

[![](testflight-images/tester-email.png "An example invite email")](testflight-images/tester-email.png#lightbox)

Quand les testeurs cliquent sur le bouton **Open in TestFlight**, votre application s’ouvre dans l’application TestFlight ou, si elle n’a pas encore été téléchargée, les testeurs sont redirigés vers l’App Store pour pouvoir la télécharger.

Une fois que votre application est ouverte dans TestFlight, elle affiche les informations relatives au test et invite les testeurs à installer l’application sur leur appareil iOS 8.0 ou version ultérieure :

[![](testflight-images/install-app.png "TestFlight will show details of what to test for")](testflight-images/install-app.png#lightbox)

Dans l’écran d’accueil de l’appareil, les builds en test sont signalées par une pastille orange, affichée devant le nom de l’application.

Les testeurs peuvent vous envoyer des commentaires via l’application TestFlight, à l’adresse e-mail indiquée dans les métadonnées.

### <a name="beta-testing-complete"></a>Fin du bêta testing

Quand le bêta testing est terminé, vous pouvez soumettre votre application sur l’App Store pour revue par Apple. Ce processus est très simple dans iTunes Connect. Il vous suffit de cliquer sur le bouton **Submit for Review**, comme illustré ci-dessous :

[![](testflight-images/submit-for-review.png "Click the Submit for Review button")](testflight-images/submit-for-review.png#lightbox)

## <a name="summary"></a>Récapitulatif

Cet article a montré comment utiliser l’outil de bêta testing TestFlight d’Apple à partir d’iTunes Connect. Il a expliqué comment charger une nouvelle build dans iTunes Connect, et comment inviter des bêta testeurs internes et externes à utiliser notre application.

## <a name="related-links"></a>Liens connexes

- [Création d’un enregistrement iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#creating)
- [Publication dans l’App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
- [Provisionnement d’une application pour sa distribution via l’App Store](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#provisioning)
- [Utilisation de l’outil TestFlight (bêta) d’Apple](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/Chapters/BetaTestingTheApp.html#//apple_ref/doc/uid/TP40011225-CH35-SW2)
