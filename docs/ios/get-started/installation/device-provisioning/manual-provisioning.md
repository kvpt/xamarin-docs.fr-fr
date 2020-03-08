---
title: Provisionnement manuel pour Xamarin.iOS
description: Après l’installation de Xamarin.iOS, l’étape suivante dans le développement iOS consiste à provisionner votre appareil iOS. Ce guide décrit comment configurer des profils et des certificats de développement par le biais du provisionnement manuel.
ms.prod: xamarin
ms.assetid: E26ACC94-F4A5-4FF5-B7D4-BE596745A665
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/15/2017
ms.openlocfilehash: 945a42485486dbfddfd023a72e88d9127651c71f
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78911502"
---
# <a name="manual-provisioning-for-xamarinios"></a>Provisionnement manuel pour Xamarin.iOS

_Une fois Xamarin. iOS installé avec succès, l’étape suivante du développement iOS consiste à approvisionner votre appareil iOS. Ce guide explore l’utilisation de l’approvisionnement manuel pour configurer des profils et des certificats de développement._

> [!NOTE]
> Les instructions de cette page s’adressent aux développeurs qui ont payé l’accès au programme Developer d’Apple. Si vous disposez d’un compte gratuit, consultez le guide [Provisionnement gratuit](~/ios/get-started/installation/device-provisioning/free-provisioning.md) pour plus d’informations sur le test des appareils.

## <a name="creating-a-signing-identity"></a>Création d’une identité de signature

La première étape dans la configuration d’un appareil de développement consiste à créer une identité de signature. Une identité de signature comprend deux choses :

- Un certificat de développement
- Une clé privée

Les certificats de développement et leurs [clés](#understanding-certificate-key-pairs) associées sont primordiales pour un développeur iOS : ensemble, ils établissent votre identité auprès d’Apple et vous associent à un appareil et à un profil donnés pour le développement, ce qui revient à placer votre signature numérique sur vos applications. Apple vérifie les certificats pour contrôler l’accès aux appareils que vous êtes autorisé à déployer.

Vous pouvez gérer les équipes de développement, les certificats et les profils en accédant à la section [Certificats, identificateurs et profils](https://developer.apple.com/account/overview.action) (connexion obligatoire) du centre des membres Apple. Apple exige que vous ayez une identité de signature pour générer votre code pour un simulateur ou appareil.  

> [!IMPORTANT]
> Il est important de noter que vous ne pouvez avoir que deux certificats de développement iOS en même temps. Si vous avez besoin d’en créer un autre, vous devez révoquer un existant. Tout ordinateur qui utilise un certificat révoqué n’est pas en mesure de signer son application.

Pour générer une identité de signature, effectuez les étapes suivantes :

1. Accédez à la [section Certificats, identificateurs et profils du portail des développeurs](https://developer.apple.com/account/overview.action) et sélectionnez la section **Certificats** dans la colonne **Applications iOS**. Ensuite, appuyez sur **+** pour créer un certificat :

    [![](manual-provisioning-images/cert-plus.png "Click the + to create a new certificate")](manual-provisioning-images/cert-plus.png#lightbox)

2. Sélectionnez l’option **Développement d’applications iOS** pour le type de certificat, puis cliquez sur **Continuer**. Cet écran peut différer en fonction des privilèges de votre compte :

    [![](manual-provisioning-images/cert-first.png "Select the iOS App Development option for the certificate type")](manual-provisioning-images/cert-first.png#lightbox)

3. Faites une demande de signature de certificat, que vous allez charger pour générer un certificat manuellement. Pour cela, lancez **Keychain Access** sur un Mac. Accédez au menu principal, puis sélectionnez **Assistant Certificat** et **Demander un certificat auprès d’une autorité de certification**, comme illustré ci-dessous :

      [![](manual-provisioning-images/key-first.png "Request a Certificate Signing Request")](manual-provisioning-images/key-first.png#lightbox)

4. Complétez les informations et sélectionnez l’option **Enregistrer sur disque** :

    [![](manual-provisioning-images/key-second.png "Fill in your information")](manual-provisioning-images/key-second.png#lightbox)

5. Enregistrez la demande de signature de certificat à un emplacement facile d’accès :

    [![](manual-provisioning-images/cert-third.png "Save the CSR")](manual-provisioning-images/cert-third.png#lightbox)

6. Revenez au portail de provisionnement, chargez le certificat sur le portail et envoyez-le :

    [![](manual-provisioning-images/cert-second.png "Upload the Certificate to the portal")](manual-provisioning-images/cert-second.png#lightbox)

    Si vous n’avez pas de privilèges d’administration, le certificat doit être approuvé par un administrateur ou un agent de l’équipe.

7. Une fois que le certificat est approuvé, téléchargez-le à partir du portail de provisionnement :

    [![](manual-provisioning-images/status-dev.png "Download the Certificate from the Provisioning Portal")](manual-provisioning-images/status-dev.png#lightbox)

8. Double-cliquez sur le certificat téléchargé pour lancer Keychain Access et ouvrir le panneau **Mes certificats**, qui présente les nouveaux certificats et la clé privée associée :

    [![](manual-provisioning-images/keychain.png "The Certificate in Keychain Access")](manual-provisioning-images/keychain.png#lightbox)

### <a name="understanding-certificate-key-pairs"></a>Présentation des paires clé/certificat

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Le profil de développeur contient des certificats, leurs clés associées et tous les profils de provisionnement associés au compte. Il existe en fait deux versions d’un profil de développeur, une première sur le portail des développeurs et l’autre sur un Mac local. La différence entre les deux est le type de clés qu’elles contiennent : _le profil sur le portail contient toutes les clés publiques associées à vos certificats, tandis que la copie sur votre Mac local contient toutes les clés privées_. Pour que les certificats soient valides, les paires de clés doivent correspondre. Conservez une sauvegarde du profil de développeur sur le Mac local, car en cas de perte des clés privées, tous les certificats et profils de provisionnement doivent être regénérés.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Le profil de développeur contient des certificats, leurs clés associées et tous les profils de provisionnement associés au compte. Il existe en fait deux versions d’un profil de développeur, une première sur le portail des développeurs et l’autre sur un Mac. La différence entre les deux est le type de clés qu’elles contiennent : _le profil sur le portail contient toutes les clés publiques associées à vos certificats, tandis que la copie sur le Mac contient toutes les clés privées_. Pour que les certificats soient valides, les paires de clés doivent correspondre. Conservez une sauvegarde du profil de développeur sur le Mac de l’hôte de build Xamarin, car en cas de perte des clés privées, tous les certificats et profils de provisionnement doivent être regénérés.

-----

> [!WARNING]
> La perte du certificat et des clés associées peut être extrêmement perturbatrice, car elle requiert la révocation des certificats existants et le réapprovisionnement d’appareils associés, y compris ceux enregistrés pour le déploiement ad-hoc. Une fois les certificats de développement correctement configurés, exportez-en une copie de sauvegarde et stockez-les dans un endroit sûr. Pour plus d’informations sur la façon de procéder, reportez-vous à la section Exportation et importation de certificats et profils du guide de [maintenance des certificats](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/MaintainingCertificates/MaintainingCertificates.html) dans la documentation d’Apple.

<a name="provisioning" />

## <a name="provisioning-an-ios-device-for-development"></a>Provisionnement d’un appareil iOS pour le développement

Maintenant que vous avez établi votre identité auprès d’Apple et que vous avez un certificat de développement, vous devez configurer un profil de provisionnement et les entités requises pour qu’il soit possible de déployer une application sur un appareil Apple. L’appareil doit exécuter une version d’iOS prise en charge par Xcode. Vous devrez peut-être mettre à jour l’appareil, Xcode ou les deux.

<a name="adddevice" />

## <a name="add-a-device"></a>Ajout d’un appareil

Lorsque vous créez un profil de provisionnement pour le développement, vous devez indiquer quels appareils peuvent exécuter l’application. Pour ce faire, vous pouvez ajouter jusqu’à 100 appareils par année civile à notre portail des développeurs et depuis ce dernier, vous pouvez sélectionner les appareils à ajouter à un profil de provisionnement particulier. Suivez les étapes ci-dessous sur votre Mac pour ajouter un appareil au portail des développeurs.

1. Démarrez Xcode.
2. Connectez l’appareil à provisionner au Mac à l’aide de son câble USB fourni.
3. Dans le menu **Fenêtres**, sélectionnez **Appareils** :

   [![](manual-provisioning-images/add01.png "From the Windows menu select Devices")](manual-provisioning-images/add01.png#lightbox)

4. Sélectionnez l’appareil iOS souhaité dans la liste **APPAREILS** située dans la partie gauche de la fenêtre Appareils.
5. Mettez en surbrillance la chaîne **Identificateur** et copiez-la dans le Presse-papiers :

   [![](manual-provisioning-images/add02.png "Highlight the Identifier string")](manual-provisioning-images/add02.png#lightbox)

6. Dans Safari, accédez au [centre des développeurs Apple](https://developer.apple.com/membercenter/index.action) et connectez-vous.
7. Cliquez sur le lien **Certificats, identificateurs et profils** :

   [![](manual-provisioning-images/add03.png "Click the Certificates, Identifiers  Profiles link")](manual-provisioning-images/add03.png#lightbox)

8. Cliquez sur le lien **Appareils** :

   [![](manual-provisioning-images/add04.png "Click on the Devices link")](manual-provisioning-images/add04.png#lightbox)

9. Cliquez sur le bouton **+**  :

   [![](manual-provisioning-images/add05.png "Click the + button")](manual-provisioning-images/add05.png#lightbox)

10. Nommez le nouvel appareil et collez l’**identificateur** que vous avez copié ci-dessus dans le champ **UUID** :

    [![](manual-provisioning-images/add06.png "Provide a name for the new device and the device Identifier")](manual-provisioning-images/add06.png#lightbox)

11. Cliquez sur le bouton **Continuer**.
12. Enfin, examinez les informations et cliquez sur le bouton **Inscrire** :

    [![](manual-provisioning-images/add07.png "Review the information")](manual-provisioning-images/add07.png#lightbox)

Répétez les étapes ci-dessus pour tous les appareils iOS utilisés pour tester ou déboguer une application Xamarin.iOS.

Après avoir ajouté l’appareil au portail des développeurs, il est nécessaire de créer un profil de provisionnement et d’y ajouter l’appareil.

<a name="provisioningprofile" />

## <a name="creating-a-development-provisioning-profile"></a>Création d’un profil de provisionnement de développement

À l’instar des certificats de développement, il est possible de créer manuellement les profils de provisionnement par le biais de la section [Certificats, identificateurs et profils](https://developer.apple.com/account/overview.action) du centre des membres Apple.

Avant de créer un profil de provisionnement, un *ID d’application* doit être créé. Un ID d’application est une chaîne de style DNS inverse qui identifie une application de manière unique. Les étapes ci-dessous montrent comment créer un **ID d’application générique**, utilisable pour générer et installer la plupart des applications. Les **ID d’application explicites** autorisent uniquement l’installation d’une seule application (avec l’ID de bundle correspondant) et servent généralement à certaines fonctionnalités iOS comme Apple Pay et HealthKit. Pour plus d’informations sur la création d’ID d’application explicites, reportez-vous au guide d’[utilisation des fonctionnalités](~/ios/deploy-test/provisioning/capabilities/index.md).

### <a name="app-id"></a>ID d'application

1. Dans le [portail des développeurs](https://developer.apple.com/account/overview.action), accédez à la section *Certificats, identificateurs et profils* du centre des développeurs Apple. Sélectionnez **App IDs** sous **Identifiers**.
2. Cliquez sur le bouton **+** et fournissez un **nom** :

    [![](manual-provisioning-images/appid05a.png "Provide a Name")](manual-provisioning-images/appid05a.png#lightbox)
3. Le préfixe de l’application doit être préréglé. Sélectionnez **ID d’application générique** pour le suffixe de l’application. Entrez un ID de bundle au format `com.[DomainName].*` :

   [![](manual-provisioning-images/appid05b.png "Enter a Bundle ID")](manual-provisioning-images/appid05b.png#lightbox)

4. Cliquez sur le bouton **Continuer** et suivez les instructions à l’écran pour créer l’ID d’application.

### <a name="provisioning-profile"></a>Profil de provisionnement

Une fois que l’ID d’application a été créé, le profil de provisionnement peut être généré. Ce profil de provisionnement indique à *quelle* application (ou quelles applications, s’il s’agit d’un ID d’application générique) il se réfère, *qui* peut l’utiliser (en fonction des certificats de développeur ajoutés) et *quels* appareils peuvent installer l’application.

Pour créer manuellement un profil de provisionnement pour le développement, effectuez les étapes suivantes :

1. Utilisez Safari pour accéder au [centre des développeurs Apple](https://developer.apple.com/membercenter/index.action), et sous la section *Certificats, identificateurs et profils*, sélectionnez Profils de provisionnement.
2. Cliquez sur le bouton **+** dans le coin supérieur droit pour créer un profil.
3. Dans la section **Développement**, activez la case d’option située en regard de **Développement d’applications iOS**, puis appuyez sur **Continuer** :

    [![](manual-provisioning-images/provisioning-profile01.png "Select the type of profile to create")](manual-provisioning-images/provisioning-profile01.png#lightbox)
4. Dans le menu déroulant, sélectionnez l’ID d’application à utiliser :

    [![](manual-provisioning-images/provisioning-profile02.png "Select the App ID that to use")](manual-provisioning-images/provisioning-profile02.png#lightbox)
5. Sélectionnez le ou les certificats à inclure dans le profil de provisionnement, puis appuyez sur **Continuer** :

    [![](manual-provisioning-images/provisioning-profile03.png "Select the Certificates to include in the provisioning profile")](manual-provisioning-images/provisioning-profile03.png#lightbox)
6. Sélectionnez tous les appareils sur lesquels l’application sera installée.

    [![](manual-provisioning-images/provisioning-profile04.png "Select all the devices that the app will be installed on")](manual-provisioning-images/provisioning-profile04.png#lightbox)
7. Donnez un nom au profil de provisionnement, puis appuyez sur **Continuer** pour créer le profil :

    [![](manual-provisioning-images/provisioning-profile05.png "Provide the Provisioning Profile with an identifiable a name")](manual-provisioning-images/provisioning-profile05.png#lightbox)
8. Appuyez sur **Télécharger** pour télécharger le profil de provisionnement sur un Mac :

    [![](manual-provisioning-images/provisioning-profile06.png "Download the provisioning profile")](manual-provisioning-images/provisioning-profile06.png#lightbox)

9. Double-cliquez sur le fichier pour installer le profil de provisionnement dans Xcode. Notez que Xcode risque de ne pas présenter d’indication visuelle de l’installation du profil sauf s’il l’ouvre. Vous pouvez le vérifier en accédant à **Xcode > fenêtre > appareils et simulateurs**. Cliquez avec le bouton droit sur votre appareil, puis sélectionnez **afficher les profils de provisionnement**...

      [![](manual-provisioning-images/provisioning-profile07-sml.png "Viewing the profile in Xcode")](manual-provisioning-images/provisioning-profile07.png#lightbox)

Une fois le profil de provisionnement correctement créé, il peut être nécessaire d’actualiser Xcode pour que tous les certificats de développement soient disponibles pour Visual Studio pour Mac et Visual Studio.

<a name="download" />

## <a name="downloading-profiles-and-certificates-in-xcode"></a>Téléchargement de profils et de certificats dans Xcode

Les certificats et les profils de provisionnement qui ont été créés dans le portail des développeurs Apple peuvent ne pas apparaître automatiquement dans Xcode. Par conséquent, il peut être nécessaire de les télécharger pour qu’ils soient accessibles par Visual Studio pour Mac et Visual Studio. Pour mettre à jour et télécharger des certificats créés dans le portail des développeurs Apple, effectuez les étapes suivantes :

1. Quittez Visual Studio pour Mac ou Visual Studio.
2. Démarrez Xcode.
3. Choisissez **Menu Xcode > Préférences...**
4. Cliquez sur l’onglet **Comptes**.
5. Sélectionnez une équipe, puis cliquez sur le bouton **Télécharger les profils manuels** :

    [![le téléchargement de profils manuels](manual-provisioning-images/selectteam1.png)](manual-provisioning-images/selectteam1.png#lightbox)

6. Quittez Xcode.
7. Démarrez Visual Studio pour Mac ou Visual Studio.

Les nouveaux certificats ou profils de provisionnement sont alors disponibles dans Visual Studio pour Mac ou Visual Studio et prêts à être utilisés.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

> [!IMPORTANT]
> Il peut être nécessaire d’arrêter et de redémarrer Visual Studio pour Mac afin d’afficher des certificats ou des profils nouveaux ou modifiés mis à jour par Xcode.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

> [!IMPORTANT]
> Il peut être nécessaire d’arrêter et de redémarrer Visual Studio afin d’afficher des certificats ou des profils nouveaux ou modifiés mis à jour par Xcode.

-----

## <a name="provisioning-for-application-services"></a>Provisionnement des services d’application

Apple propose une sélection de services d’application spéciaux, également appelés fonctionnalités, que vous pouvez activer pour une application Xamarin.iOS. Ces services d’application doivent être configurés à la fois sur le portail de provisionnement iOS quand l’**ID d’application** est créé et dans le fichier **Entitlements.plist** qui fait partie du projet d’application Xamarin.iOS. Pour plus d’informations sur l’ajout de services d’application à votre application, reportez-vous au guide de [présentation des fonctionnalités](~/ios/deploy-test/provisioning/capabilities/index.md) et au guide d’[utilisation des droits](~/ios/deploy-test/provisioning/entitlements.md).

- Créez un ID d’application avec les services d’application requis.
- Créez un [profil de provisionnement](#provisioningprofile) qui contient cet ID d’application.
- Définir des droits dans le projet Xamarin.iOS

## <a name="deploying-to-a-device"></a>Déploiement sur un appareil

À ce stade, le provisionnement doit être terminé et l’application prête à être déployée sur l’appareil. Pour ce faire, procédez comme suit :

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

> [!IMPORTANT]
> Avant de commencer, veillez à sélectionner **Attribution manuelle** dans **Info.plist**.

1. Connectez l’appareil à un Mac.
2. Dans le fichier **Info.plist** du projet, vérifiez que l’identificateur de bundle correspond à l’ID d’application (sauf si l’ID d’application est générique) :

   ![Saisie d’un identificateur](manual-provisioning-images/deploydevice01xs.png)

3. Cliquez avec le bouton droit sur le projet pour afficher la boîte de dialogue Options du projet et accédez à **Générer > Signature du bundle iOS**. Dans la liste déroulante située en regard de **Identité de signature** et **Profil de provisionnement**, vérifiez que Visual Studio pour Mac peut voir les profils appropriés et sélectionnez une identité et un profil spécifiques :

   ![Sélectionner une identité spécifique & profil](manual-provisioning-images/deploydevice02xs.png)

   Si la valeur est **Automatique**, Visual Studio pour Mac sélectionne l’identité et le profil selon l’ID de bundle qui a été défini à l’étape 2.

4. Veillez à définir la configuration de build sur **iPhone** / **iPad**, plutôt que sur le simulateur.
5. Cliquez sur **Exécuter** dans Visual Studio pour Mac et regardez l’application s’exécuter sur l’appareil.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

> [!IMPORTANT]
> Avant de commencer, sélectionnez **Provisionnement manuel** dans **Projet > Propriétés de provisionnement...** .

1. Connectez l’appareil à l’hôte de build Mac.
2. Dans le fichier **Info.plist** du projet, vérifiez que l’identificateur de bundle correspond à l’ID d’application :

   ![Saisie d’un identificateur](manual-provisioning-images/servicevs01.png)

3. Cliquez avec le bouton droit sur le projet pour afficher la boîte de dialogue Options du projet et accédez à **Générer > Signature du bundle iOS**. Dans la liste déroulante située en regard de **Identité de signature** et **Profil de provisionnement**, vérifiez que Visual Studio peut voir les profils appropriés et sélectionnez une identité et un profil spécifiques.

    Si la valeur est **Automatique**, Visual Studio sélectionne l’identité et le profil selon l’ID de bundle qui a été défini à l’étape 2.

4. Veillez à définir la configuration de build sur **iPhone** ou **iPad**, plutôt que sur le simulateur.
5. Cliquez sur **Exécuter** dans Visual Studio et regardez l’application s’exécuter sur l’appareil.

-----

## <a name="summary"></a>Résumé

Ce guide a décrit les étapes requises pour configurer l’environnement de développement nécessaire à Xamarin.iOS. Il a expliqué la façon dont le code d’une application est signé à l’aide d’informations sur le développeur, son équipe, les appareils sur lesquels l’application peut s’exécuter et son ID d’application individuel.

## <a name="related-links"></a>Liens connexes

- [Provisionnement libre](~/ios/get-started/installation/device-provisioning/free-provisioning.md)
- [Distribution d’une application](~/ios/deploy-test/app-distribution/index.md)
- [Dépannage](~/ios/deploy-test/troubleshooting.md)
- [Apple - Guide de distribution d’applications](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
