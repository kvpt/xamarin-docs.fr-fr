---
title: Provisionnement manuel pour Xamarin.iOS
description: Après l’installation de Xamarin.iOS, l’étape suivante dans le développement iOS consiste à provisionner votre appareil iOS. Ce guide décrit comment configurer des profils et des certificats de développement par le biais du provisionnement manuel.
ms.prod: xamarin
ms.assetid: E26ACC94-F4A5-4FF5-B7D4-BE596745A665
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/06/2020
ms.openlocfilehash: 04cb1b9303e571b2a10cdfa621dcd312162e2893
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304862"
---
# <a name="manual-provisioning-for-xamarinios"></a>Provisionnement manuel pour Xamarin.iOS

_Une fois Xamarin. iOS installé avec succès, l’étape suivante du développement iOS consiste à approvisionner votre appareil iOS. Ce guide explore l’utilisation de l’approvisionnement manuel pour configurer des profils et des certificats de développement._

> [!NOTE]
> Les instructions de cette page s’adressent aux développeurs qui ont payé l’accès au programme Developer d’Apple. Si vous disposez d’un compte gratuit, consultez le guide [Provisionnement gratuit](~/ios/get-started/installation/device-provisioning/free-provisioning.md) pour plus d’informations sur le test des appareils.

## <a name="create-a-development-certificate"></a>Créer un certificat de développement

La première étape de la configuration d’un appareil de développement consiste à créer un certificat de signature. Un certificat de signature se compose de deux éléments :

- Un certificat de développement
- Une clé privée

Les certificats de développement et leurs [clés](#understanding-certificate-key-pairs) associées sont primordiales pour un développeur iOS : ensemble, ils établissent votre identité auprès d’Apple et vous associent à un appareil et à un profil donnés pour le développement, ce qui revient à placer votre signature numérique sur vos applications. Apple vérifie les certificats pour contrôler l’accès aux appareils que vous êtes autorisé à déployer.

Vous pouvez gérer les équipes de développement, les certificats et les profils en accédant à la section [Certificats, identificateurs et profils](https://developer.apple.com/account/resources/certificates/list) (connexion obligatoire) du centre des membres Apple. Apple exige que vous ayez une identité de signature pour générer votre code pour un simulateur ou appareil.  

> [!IMPORTANT]
> Il est important de noter que vous ne pouvez avoir que deux certificats de développement iOS en même temps. Si vous avez besoin d’en créer un autre, vous devez révoquer un existant. Tout ordinateur qui utilise un certificat révoqué n’est pas en mesure de signer son application.

Avant de commencer le processus d’approvisionnement manuel, vous devez vous assurer que vous avez ajouté un compte de développeur Apple dans Visual Studio, comme décrit dans le Guide de [gestion des comptes Apple](~/cross-platform/macios/apple-account-management.md) . Une fois que vous avez ajouté votre compte de développeur Apple, procédez comme suit pour générer un certificat de signature :

1. Accédez à la fenêtre comptes de développeurs Apple dans Visual Studio.
    1. Mac : **Préférences Visual Studio > > compte de développeur Apple**
    2. Windows : **outils > Options > Xamarin > comptes Apple**

2. Sélectionnez une équipe, puis cliquez sur **afficher les détails...**
3. Cliquez sur **créer un certificat** et sélectionnez développement **Apple** ou **développement iOS**. Si vous disposez des autorisations appropriées, une nouvelle identité de signature s’affiche après quelques secondes.

### <a name="understanding-certificate-key-pairs"></a>Présentation des paires clé/certificat

Le profil de développeur contient des certificats, leurs clés associées et tous les profils de provisionnement associés au compte. Il existe en fait deux versions d’un profil de développeur, une première sur le portail des développeurs et l’autre sur un Mac local. La différence entre les deux est le type de clés qu’elles contiennent : _le profil sur le portail contient toutes les clés publiques associées à vos certificats, tandis que la copie sur votre Mac local contient toutes les clés privées_. Pour que les certificats soient valides, les paires de clés doivent correspondre.

> [!WARNING]
> La perte du certificat et des clés associées peut être extrêmement perturbante, car elle nécessite la révocation des certificats existants et la nouvelle configuration de tous les appareils associés, y compris ceux inscrits pour un déploiement ad hoc. Une fois les certificats de développement correctement configurés, exportez-en une copie de sauvegarde et stockez-les dans un endroit sûr. Pour plus d’informations sur la façon de procéder, reportez-vous à la section Exportation et importation de certificats et profils du guide de [maintenance des certificats](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/MaintainingCertificates/MaintainingCertificates.html) dans la documentation d’Apple.

<a name="provisioning" />

## <a name="provision-an-ios-device-for-development"></a>Approvisionner un appareil iOS pour le développement

Maintenant que vous avez établi votre identité auprès d’Apple et que vous disposez d’un certificat de développement, vous devez configurer un profil de provisionnement et les entités requises afin qu’il soit possible de déployer une application sur un appareil Apple. L’appareil doit exécuter une version d’iOS prise en charge par Xcode. Vous devrez peut-être mettre à jour l’appareil, Xcode ou les deux.

<a name="adddevice" />

## <a name="add-a-device"></a>Ajout d’un appareil

Lorsque vous créez un profil de provisionnement pour le développement, vous devez indiquer quels appareils peuvent exécuter l’application. Pour ce faire, vous pouvez ajouter jusqu’à 100 appareils par année civile à notre portail des développeurs et depuis ce dernier, vous pouvez sélectionner les appareils à ajouter à un profil de provisionnement particulier. Suivez les étapes ci-dessous sur votre Mac pour ajouter un appareil au portail des développeurs.

1. Connectez l’appareil à provisionner au Mac à l’aide de son câble USB fourni.
2. Ouvrez Xcode, puis accédez à **windows > appareils et simulateurs**.
3. Sous l’onglet **appareils** , sélectionnez l’appareil dans le menu de gauche.
4. Mettez en surbrillance la chaîne **Identificateur** et copiez-la dans le Presse-papiers :

   ![Les appareils Xcode et la fenêtre du simulateur avec l’emplacement de chaîne d’identificateur iOS mis en surbrillance.](manual-provisioning-images/xcode-devices.png)

5. Dans un navigateur Web, accédez à la [section périphériques dans le portail des développeurs](https://developer.apple.com/account/resources/devices/list) , puis cliquez sur le bouton **+** :

   ![Capture d’écran de la page appareils sur le site de développement Apple avec le bouton Ajouter mis en surbrillance.](manual-provisioning-images/developer-portal-devices.png)

6. Définissez la **plateforme** appropriée et indiquez un nom pour le nouvel appareil. Collez l’identificateur copié précédemment dans le champ ID de l' **appareil** :

    ![Capture d’écran de la nouvelle page d’inscription de l’appareil avec les champs remplis correctement.](manual-provisioning-images/new-device-info.png)

7. Cliquez sur **Continuer**.
8. Passez en revue les informations, puis cliquez sur **inscrire**.

Répétez les étapes ci-dessus pour tous les appareils iOS utilisés pour tester ou déboguer une application Xamarin.iOS.

<a name="provisioningprofile" />

## <a name="create-a-development-provisioning-profile"></a>Créer un profil de provisionnement de développement

Après avoir ajouté l’appareil au portail des développeurs, il est nécessaire de créer un profil de provisionnement et d’y ajouter l’appareil. 

Avant de créer un profil de provisionnement, un *ID d’application* doit être créé. Un ID d’application est une chaîne de style DNS inverse qui identifie une application de manière unique. Les étapes ci-dessous montrent comment créer un **ID d’application générique**, utilisable pour générer et installer la plupart des applications. Les **ID d’application explicites** autorisent uniquement l’installation d’une seule application (avec l’ID de bundle correspondant) et servent généralement à certaines fonctionnalités iOS comme Apple Pay et HealthKit. Pour plus d’informations sur la création d’ID d’application explicites, reportez-vous au guide d’[utilisation des fonctionnalités](~/ios/deploy-test/provisioning/capabilities/index.md).

### <a name="new-wildcard-app-id"></a>Nouvel ID d’application générique

1. Accédez à la [section identificateurs dans le portail des développeurs](https://developer.apple.com/account/resources/identifiers/list) et cliquez sur le bouton **+** .
2. Sélectionnez **ID d’application** , puis cliquez sur **Continuer**.
3. Entrez une **Description**. Définissez ensuite **ID** d’ensemble à **caractères génériques** et entrez un ID au format `com.[DomainName].*`:

   ![Capture d’écran de la nouvelle page d’inscription de l’ID d’application avec les champs obligatoires remplis.](manual-provisioning-images/new-app-id.png)

4. Cliquez sur **Continuer**.
5. Passez en revue les informations, puis cliquez sur **inscrire**.

### <a name="new-provisioning-profile"></a>Nouveau profil de provisionnement

Une fois l’ID d’application créé, le profil de provisionnement peut être créé. Ce profil de provisionnement contient des *informations sur l'* application (ou les applications, s’il s’agit d’un ID d’application générique) auquel ce profil est associé, *qui* peut utiliser le profil (en fonction des certificats de développeur ajoutés) et *Quels* appareils peuvent installer l’application.

Pour créer manuellement un profil de provisionnement pour le développement, procédez comme suit :

1. Accédez à la [section des profils dans le portail des développeurs](https://developer.apple.com/account/resources/profiles/list) et cliquez sur le bouton **+** .

2. Sous **développement**, sélectionnez **développement d’application iOS** , puis cliquez sur **Continuer**.

3. Dans le menu déroulant, sélectionnez l’ID d’application à utiliser, puis cliquez sur **Continuer**.

4. Sélectionnez le ou les certificats à inclure dans le profil de provisionnement, puis cliquez sur **Continuer**.

5. Sélectionnez tous les appareils sur lesquels l’application sera installée, puis cliquez sur **Continuer**.

6. Indiquez le **nom du profil de provisionnement** , puis cliquez sur **générer**.

7. Vous pouvez éventuellement cliquer sur **Télécharger** sur la page suivante pour télécharger le profil de provisionnement sur le Mac.

<a name="download" />

## <a name="download-provisioning-profiles-in-visual-studio"></a>Télécharger des profils de provisionnement dans Visual Studio

Après avoir créé un profil de provisionnement dans le portail des développeurs Apple, utilisez Visual Studio pour le télécharger afin qu’il soit disponible pour la signature de Bundle dans votre application.

1. Accédez à la fenêtre comptes de développeurs Apple dans Visual Studio.
    1. Mac : **Préférences Visual Studio > > compte de développeur Apple**
    2. Windows : **outils > Options > Xamarin > comptes Apple**

2. Sélectionnez l’équipe, puis cliquez sur **afficher les détails...**
3. Vérifiez que le nouveau profil apparaît dans la liste des **profils de provisionnement** . Vous devrez peut-être redémarrer Visual Studio pour actualiser la liste. 
4. Cliquez sur **Télécharger tous les profils**.

Le nouveau profil de provisionnement est désormais disponible dans Visual Studio et prêt à être utilisé.

## <a name="deploy-to-a-device"></a>Déployer l’application sur un appareil

À ce stade, le provisionnement doit être terminé et l’application prête à être déployée sur l’appareil. Pour ce faire, procédez comme suit :

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

1. Connectez votre appareil au Mac.
2. Ouvrez le fichier **info. plist** et assurez-vous que l' **identificateur de Bundle** correspond à l’ID d’application créé précédemment (sauf si l’ID d’application est un caractère générique).
3. Dans la section **signature** , sélectionnez **approvisionnement manuel** comme **modèle**:

    ![Capture d’écran d’info. plist dans Visual Studio pour Mac avec l’approvisionnement manuel sélectionné](manual-provisioning-images/vsm-info-plist.png)

4. Cliquez sur **options de signature de bundle...**
5. Assurez-vous que la configuration de build est définie sur **Debug | iPhone**. Ouvrez les menus déroulants **identité de signature** et **profil de provisionnement** pour vérifier que les certificats et profils de provisionnement corrects sont répertoriés : 

   ![la page de propriétés signature d’offre groupée iOS avec le menu déroulant Profil d’approvisionnement s’ouvre et affiche la liste de tous les profils de configuration disponibles pour l’application.](manual-provisioning-images/vsm-bundle-signing.png)

6. Sélectionnez une identité et un profil spécifiques à utiliser, ou laissez-le comme **automatique**. Lorsque cette option est définie sur **automatique**, Visual Studio pour Mac sélectionne l’identité et le profil en fonction de l' **identificateur de Bundle** dans **info. plist**. 
7. Cliquez sur **OK**.
8. Cliquez sur **exécuter** pour déployer l’application sur votre appareil.


# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Connectez votre appareil à l’hôte de build Mac.
2. Ouvrez le fichier **info. plist** et assurez-vous que l' **identificateur de Bundle** correspond à l’ID d’application créé précédemment (sauf si l’ID d’application est un caractère générique).
3. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le nom du projet iOS, sélectionnez **Propriétés** , puis accédez à l’onglet **signature du bundle iOS** .
4. Assurez-vous que la configuration de build est définie sur **Debug | iPhone**. Sous **signature du bundle**, sélectionnez **approvisionnement manuel** comme **modèle**:

    ![Capture d’écran d’info. plist dans Visual Studio pour Mac avec l’approvisionnement manuel sélectionné](manual-provisioning-images/vs-bundle-signing.png)

5. Ouvrez les menus déroulants **identité de signature** et **profil de provisionnement** pour vérifier que les certificats et profils de provisionnement corrects sont répertoriés.
6. Sélectionnez une identité et un profil spécifiques à utiliser, ou laissez-le comme **automatique**. Lorsque cette option est définie sur **automatique**, Visual Studio sélectionne l’identité et le profil en fonction de l' **identificateur de Bundle** dans **info. plist**. 
7. Cliquez sur **exécuter** pour déployer l’application sur votre appareil.

-----

## <a name="provisioning-for-application-services"></a>Provisionnement des services d’application

Apple propose une sélection de services d’application spéciaux, également appelés fonctionnalités, que vous pouvez activer pour une application Xamarin.iOS. Ces services d’application doivent être configurés à la fois sur le portail de provisionnement iOS quand l’**ID d’application** est créé et dans le fichier **Entitlements.plist** qui fait partie du projet d’application Xamarin.iOS. Pour plus d’informations sur l’ajout de services d’application à votre application, reportez-vous au guide de [présentation des fonctionnalités](~/ios/deploy-test/provisioning/capabilities/index.md) et au guide d’[utilisation des droits](~/ios/deploy-test/provisioning/entitlements.md).

- Créez un ID d’application avec les services d’application requis.
- Créez un [profil de provisionnement](#provisioningprofile) qui contient cet ID d’application.
- Définir des droits dans le projet Xamarin.iOS

## <a name="related-links"></a>Liens connexes

- [Provisionnement libre](~/ios/get-started/installation/device-provisioning/free-provisioning.md)
- [Distribution d’une application](~/ios/deploy-test/app-distribution/index.md)
- [Dépannage](~/ios/deploy-test/troubleshooting.md)
- [Apple - Guide de distribution d’applications](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
