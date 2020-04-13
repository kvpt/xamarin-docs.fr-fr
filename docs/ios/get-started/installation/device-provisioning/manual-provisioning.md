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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "79304862"
---
# <a name="manual-provisioning-for-xamarinios"></a>Provisionnement manuel pour Xamarin.iOS

_Une fois que Xamarin.iOS a été installé avec succès, la prochaine étape dans le développement d’iOS est de fournir votre appareil iOS. Ce guide explore l’utilisation de la provisionnement manuel pour mettre en place des certificats de développement et des profils._

> [!NOTE]
> Les instructions de cette page s’adressent aux développeurs qui ont payé l’accès au programme Developer d’Apple. Si vous disposez d’un compte gratuit, consultez le guide [Provisionnement gratuit](~/ios/get-started/installation/device-provisioning/free-provisioning.md) pour plus d’informations sur le test des appareils.

## <a name="create-a-development-certificate"></a>Créer un certificat de développement

La première étape dans la mise en place d’un dispositif de développement est de créer un certificat de signature. Un certificat de signature se compose de deux choses :

- Un certificat de développement
- Une clé privée

Les certificats de développement et leurs [clés](#understanding-certificate-key-pairs) associées sont primordiales pour un développeur iOS : ensemble, ils établissent votre identité auprès d’Apple et vous associent à un appareil et à un profil donnés pour le développement, ce qui revient à placer votre signature numérique sur vos applications. Apple vérifie les certificats pour contrôler l’accès aux appareils que vous êtes autorisé à déployer.

Vous pouvez gérer les équipes de développement, les certificats et les profils en accédant à la section [Certificats, identificateurs et profils](https://developer.apple.com/account/resources/certificates/list) (connexion obligatoire) du centre des membres Apple. Apple exige que vous ayez une identité de signature pour générer votre code pour un simulateur ou appareil.  

> [!IMPORTANT]
> Il est important de noter que vous ne pouvez avoir que deux certificats de développement iOS en même temps. Si vous avez besoin d’en créer un autre, vous devez révoquer un existant. Tout ordinateur qui utilise un certificat révoqué n’est pas en mesure de signer son application.

Avant de commencer le processus de provisionnement manuel, vous devez vous assurer que vous avez un compte de développeur Apple ajouté dans Visual Studio, tel que décrit dans le guide [Apple Account Management.](~/cross-platform/macios/apple-account-management.md) Une fois que vous avez ajouté votre compte de développeur Apple, faites ce qui suit pour générer un certificat de signature :

1. Rendez-vous sur la fenêtre des comptes des développeurs Apple dans Visual Studio.
    1. Mac: **Visual Studio > Préférences > compte de développeur Apple**
    2. Windows: **Outils > Options > Xamarin > comptes Apple**

2. Sélectionnez une équipe et cliquez sur **Détails De vue...**
3. Cliquez **sur Create Certificate** et sélectionnez Apple **Development** ou **iOS Development**. Si vous avez les autorisations correctes, une nouvelle identité de signature apparaîtra après quelques secondes.

### <a name="understanding-certificate-key-pairs"></a>Présentation des paires clé/certificat

Le profil de développeur contient des certificats, leurs clés associées et tous les profils de provisionnement associés au compte. Il existe en fait deux versions d’un profil de développeur, une première sur le portail des développeurs et l’autre sur un Mac local. La différence entre les deux est le type de clés qu’elles contiennent : _le profil sur le portail contient toutes les clés publiques associées à vos certificats, tandis que la copie sur votre Mac local contient toutes les clés privées_. Pour que les certificats soient valides, les paires de clés doivent correspondre.

> [!WARNING]
> Perdre le certificat et les clés associées peut être incroyablement perturbateur, car il faudra révoquer les certificats existants et réapprovisionnement de tous les appareils associés, y compris ceux enregistrés pour le déploiement ad hoc. Une fois les certificats de développement correctement configurés, exportez-en une copie de sauvegarde et stockez-les dans un endroit sûr. Pour plus d’informations sur la façon de procéder, reportez-vous à la section Exportation et importation de certificats et profils du guide de [maintenance des certificats](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/MaintainingCertificates/MaintainingCertificates.html) dans la documentation d’Apple.

<a name="provisioning" />

## <a name="provision-an-ios-device-for-development"></a>Fournir un dispositif iOS pour le développement

Maintenant que vous avez établi votre identité avec Apple et que vous avez un certificat de développement, vous devez configurer un profil de provisionnement et les entités requises afin qu’il soit possible de déployer une application sur un appareil Apple. L’appareil doit exécuter une version d’iOS prise en charge par Xcode. Vous devrez peut-être mettre à jour l’appareil, Xcode ou les deux.

<a name="adddevice" />

## <a name="add-a-device"></a>Ajout d’un appareil

Lorsque vous créez un profil de provisionnement pour le développement, vous devez indiquer quels appareils peuvent exécuter l’application. Pour ce faire, vous pouvez ajouter jusqu’à 100 appareils par année civile à notre portail des développeurs et depuis ce dernier, vous pouvez sélectionner les appareils à ajouter à un profil de provisionnement particulier. Suivez les étapes ci-dessous sur votre Mac pour ajouter un appareil au portail des développeurs.

1. Connectez l’appareil à provisionner au Mac à l’aide de son câble USB fourni.
2. Ouvrez Xcode, et allez à **Window > Devices and Simulators**.
3. Sous l’onglet **Appareils,** sélectionnez l’appareil dans le menu du côté gauche.
4. Mettez en surbrillance la chaîne **Identificateur** et copiez-la dans le Presse-papiers :

   ![Les appareils Xcode et la fenêtre de simulateur avec l’emplacement de la chaîne d’identification iOS mis en évidence.](manual-provisioning-images/xcode-devices.png)

5. Dans un navigateur Web, rendez-vous sur la [section Appareils dans le portail des développeurs](https://developer.apple.com/account/resources/devices/list) et cliquez sur le **+** bouton :

   ![Capture d’écran de la page des appareils sur le site Apple Developer avec le bouton ajouter mis en surbrillance.](manual-provisioning-images/developer-portal-devices.png)

6. Définissez la **plate-forme** correcte et fournissez un nom pour le nouvel appareil. Coller l’identifiant copié plus tôt dans le champ **d’identification de l’appareil** :

    ![Capture d’écran de la nouvelle page d’enregistrement de l’appareil avec les champs correctement peuplés.](manual-provisioning-images/new-device-info.png)

7. Cliquez sur **Continuer**.
8. Examinez les informations, puis cliquez sur **Inscrivez-vous**.

Répétez les étapes ci-dessus pour tous les appareils iOS utilisés pour tester ou déboguer une application Xamarin.iOS.

<a name="provisioningprofile" />

## <a name="create-a-development-provisioning-profile"></a>Créer un profil de provisionnement de développement

Après avoir ajouté l’appareil au portail des développeurs, il est nécessaire de créer un profil de provisionnement et d’y ajouter l’appareil. 

Avant de créer un profil de provisionnement, un *ID d’application* doit être créé. Un ID d’application est une chaîne de style DNS inverse qui identifie une application de manière unique. Les étapes ci-dessous montrent comment créer un **ID d’application générique**, utilisable pour générer et installer la plupart des applications. Les **ID d’application explicites** autorisent uniquement l’installation d’une seule application (avec l’ID de bundle correspondant) et servent généralement à certaines fonctionnalités iOS comme Apple Pay et HealthKit. Pour plus d’informations sur la création d’ID d’application explicites, reportez-vous au guide d’[utilisation des fonctionnalités](~/ios/deploy-test/provisioning/capabilities/index.md).

### <a name="new-wildcard-app-id"></a>Nouvelle wildcard App ID

1. Rendez-vous à la [section Identifiers dans le portail du développeur](https://developer.apple.com/account/resources/identifiers/list) et cliquez sur le **+** bouton.
2. Sélectionnez **les ID app** et cliquez sur **Continuer**.
3. Fournir une **description**. Ensuite, définissez **l’ID Bundle** sur `com.[DomainName].*` **Wildcard** et saisissez une pièce d’identité dans le format :

   ![Capture d’écran de la nouvelle page d’enregistrement App ID avec les champs requis peuplés.](manual-provisioning-images/new-app-id.png)

4. Cliquez sur **Continuer**.
5. Examinez les informations, puis cliquez sur **Inscrivez-vous**.

### <a name="new-provisioning-profile"></a>Nouveau profil de provisionnement

Une fois l’ID App créé, le profil de provisionnement peut être créé. Ce profil de provisionnement contient des informations sur *quelle* application (ou applications, s’il s’agit d’un identifiant d’application wildcard) ce profil se rapporte à, *qui* peut utiliser le profil (selon les certificats de développeur sont ajoutés), et *quels* appareils peuvent installer l’application.

Pour créer manuellement un profil de provisionnement pour le développement, faites ce qui suit :

1. Rendez-vous dans la [section Profils dans le portail du développeur](https://developer.apple.com/account/resources/profiles/list) et cliquez sur le **+** bouton.

2. En **cours de développement**, sélectionnez **iOS App Development** et cliquez sur **Continuer**.

3. Sélectionnez l’ID App à utiliser à partir du menu de décrochage, puis cliquez sur **Continuer**.

4. Sélectionnez le certificat pour inclure dans le profil de provisionnement, puis cliquez sur **Continuer**.

5. Sélectionnez tous les appareils sur lequel l’application sera installée puis cliquez sur **Continuer**.

6. Fournir **le nom de profil de provisionnement** puis cliquez sur **Générer**.

7. Vous pouvez cliquer sur **Télécharger** sur la page suivante pour télécharger le profil de provisionnement sur le Mac.

<a name="download" />

## <a name="download-provisioning-profiles-in-visual-studio"></a>Télécharger des profils de provisionnement dans Visual Studio

Après avoir créé un nouveau profil de provisionnement dans le portail des développeurs Apple, utilisez Visual Studio pour le télécharger afin qu’il soit disponible pour la signature de faisceau dans votre application.

1. Rendez-vous sur la fenêtre des comptes des développeurs Apple dans Visual Studio.
    1. Mac: **Visual Studio > Préférences > compte de développeur Apple**
    2. Windows: **Outils > Options > Xamarin > comptes Apple**

2. Sélectionnez l’équipe et cliquez sur **Détails de vue...**
3. Vérifiez que le nouveau profil apparaît dans la liste **des profils de provisionnement.** Vous devrez peut-être redémarrer Visual Studio pour actualiser la liste. 
4. Cliquez **sur Télécharger tous les profils**.

Le nouveau profil de provisionnement sera désormais disponible en Visual Studio et prêt à l’emploi.

## <a name="deploy-to-a-device"></a>Déployer l’application sur un appareil

À ce stade, le provisionnement doit être terminé et l’application prête à être déployée sur l’appareil. Pour ce faire, procédez comme suit :

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

1. Connectez votre appareil au Mac.
2. Ouvrez **Info.plist** et assurez-vous que **l’identifiant bundle** correspond à l’ID App créé plus tôt (sauf si l’ID App est une wildcard).
3. Dans la section **Signature,** sélectionnez **l’provisionnement manuel** comme **régime**:

    ![Capture d’écran de Info.plist dans Visual Studio pour Mac avec provisionnement manuel sélectionné](manual-provisioning-images/vsm-info-plist.png)

4. Cliquez sur **Les options de signature de faisceau...**
5. Assurez-vous que la configuration de construction est définie sur **Debug-iPhone**. Ouvrez les menus **d’abandon de l’identité** et **de provisionnement** pour vérifier que les certificats et profils de provisionnement corrects sont énumérés : 

   ![Page de propriété de signature de faisceau d’iOS avec le menu de décrochage de profil de provisionnement ouvert énumérant tous les profils de provisionnement disponibles pour l’application.](manual-provisioning-images/vsm-bundle-signing.png)

6. Sélectionnez une identité et un profil spécifiques à utiliser, ou laissez-le comme **automatique**. Lorsqu’il est **configuré**à Automatic , Visual Studio pour Mac sélectionnera l’identité et le profil basé sur **l’identifiant bundle** dans **Info.plist**. 
7. Cliquez sur **OK**.
8. Cliquez **sur Run** pour déployer l’application sur votre appareil.


# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Connectez votre appareil à l’hôte de construction Mac.
2. Ouvrez **Info.plist** et assurez-vous que **l’identifiant bundle** correspond à l’ID App créé plus tôt (sauf si l’ID App est une wildcard).
3. Dans **l’Explorer Solution**, cliquez à droite sur le nom du projet iOS, sélectionnez **les propriétés** et naviguez vers l’onglet **signature du forfait iOS.**
4. Assurez-vous que la configuration de construction est définie sur **Debug-iPhone**. Dans le cadre **de la signature de faisceau**, sélectionnez la **provisionnement manuel** comme le **régime**:

    ![Capture d’écran de Info.plist dans Visual Studio pour Mac avec provisionnement manuel sélectionné](manual-provisioning-images/vs-bundle-signing.png)

5. Ouvrez les menus **d’abandon de l’identité** et **de la provision pour** vérifier que les certificats et profils de provisionnement corrects sont répertoriés.
6. Sélectionnez une identité et un profil spécifiques à utiliser, ou laissez-le comme **automatique**. Lorsqu’il est configuré sur **Automatic,** Visual Studio sélectionnera l’identité et le profil basés sur **l’identifiant bundle** dans **Info.plist**. 
7. Cliquez **sur Run** pour déployer l’application sur votre appareil.

-----

## <a name="provisioning-for-application-services"></a>Provisionnement des services d’application

Apple propose une sélection de services d’application spéciaux, également appelés fonctionnalités, que vous pouvez activer pour une application Xamarin.iOS. Ces services d’application doivent être configurés à la fois sur le portail de provisionnement iOS quand l’**ID d’application** est créé et dans le fichier **Entitlements.plist** qui fait partie du projet d’application Xamarin.iOS. Pour plus d’informations sur l’ajout de services d’application à votre application, reportez-vous au guide de [présentation des fonctionnalités](~/ios/deploy-test/provisioning/capabilities/index.md) et au guide d’[utilisation des droits](~/ios/deploy-test/provisioning/entitlements.md).

- Créez un ID d’application avec les services d’application requis.
- Créez un [profil de provisionnement](#provisioningprofile) qui contient cet ID d’application.
- Définir des droits dans le projet Xamarin.iOS

## <a name="related-links"></a>Liens connexes

- [Provisionnement libre](~/ios/get-started/installation/device-provisioning/free-provisioning.md)
- [Distribution d’applications](~/ios/deploy-test/app-distribution/index.md)
- [Dépannage](~/ios/deploy-test/troubleshooting.md)
- [Apple - Guide de distribution d’applications](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
