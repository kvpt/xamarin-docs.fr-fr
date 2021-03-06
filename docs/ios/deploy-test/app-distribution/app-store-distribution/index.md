---
title: Distribution dans l’App Store
description: Ce document décrit comment distribuer une application Xamarin.iOS sur l’App Store. Il explique comment créer un certificat de distribution, comment créer un profil de provisionnement de distribution, et comment configurer iTunes Connect et soumettre l’application.
ms.prod: xamarin
ms.assetid: B07E2C1F-A6DF-43CB-BFB0-0252A5558467
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/23/2017
ms.openlocfilehash: c9882944938b2d5136d36304c4a400208a68663a
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86937330"
---
# <a name="app-store-distribution"></a>Distribution dans l’App Store

Une fois l’application Xamarin.iOS développée, la prochaine étape du cycle de vie du développement du logiciel consiste à la distribuer aux utilisateurs à l’aide de l’App Store d’iTunes. Il s’agit de la manière la plus courante de distribuer des applications. En publiant une application sur l’App Store d’Apple, vous la mettez à la disposition des consommateurs du monde entier.

> [!IMPORTANT]
> Apple [a indiqué](https://developer.apple.com/ios/submit/) qu’à partir de mars 2019 toutes les applications et mises à jour envoyées à l’App Store doivent avoir été créées avec le kit SDK d’iOS 12.1 ou ultérieur, inclus dans Xcode 10.1 ou ultérieur.
> Les applications doivent aussi prendre en charge les tailles d’écran de l’iPhone XS et de l’iPad Pro 12,9".

> [!IMPORTANT]
> Si vous recherchez des ressources en ce qui concerne l' `UIWebView` avertissement de désapprobation (iTMS-90809) lors de l’utilisation de Xamarin. Forms, reportez-vous à la documentation de [Xamarin. Forms WebView](~/xamarin-forms/user-interface/webview.md#uiwebview-deprecation-and-app-store-rejection-itms-90809) .

La distribution d’une application, tout comme son développement, nécessite le provisionnement d’applications à l’aide du *profil de provisionnement* approprié. Les profils de provisionnement sont des fichiers qui contiennent les informations sur le code de signature, ainsi que l’identité de l’application et le mécanisme de distribution prévu. Ils contiennent également des informations relatives aux appareils sur lesquels l’application peut être déployée pour la distribution hors App Store.

> [!IMPORTANT]
> Pour pouvoir utiliser iTunes Connect et donc publier une application sur l’App Store, vous **devez** faire partie d’un programme pour les développeurs d’Apple, en tant qu’individu ou qu’organisation. Vous ne pouvez pas suivre les étapes de cette page si vous êtes membre d’un programme Developer **Enterprise** d’Apple.

<a name="provisioning"></a>

## <a name="provisioning-an-app-for-app-store-distribution"></a>Provisionnement d’une application pour sa distribution via l’App Store

Quelle que soit la manière dont vous comptez publier une application Xamarin.iOS, vous devez créer un *profil de provisionnement de distribution* spécifique. Ce profil permet à l’application d’être publiée avec une signature numérique pour qu’elle puisse être installée sur un appareil iOS. Le profil de provisionnement de distribution, qui est similaire au profil de provisionnement de développement, contient les éléments suivants :

- ID d’application
- Certificat de distribution

Vous pouvez sélectionner le même **ID d’application** et les mêmes **appareils** que ceux que vous avez utilisés pour votre profil de provisionnement de développement. Toutefois, si vous n’en avez pas déjà, vous devez créer un certificat de distribution pour identifier votre organisation quand vous soumettez l’application à l’App Store. Les étapes à suivre pour créer un certificat de distribution sont décrites dans la section ci-dessous.

> [!NOTE]
> Seuls les agents et les administrateurs d’équipe peuvent créer des certificats de distribution et des profils d’approvisionnement.

<a name="creatingcertificate"></a>

## <a name="creating-a-distribution-certificate"></a>Création d’un certificat de distribution

1. Accédez à la section *Certificates, Identifiers & Profiles* (Certificats, identificateurs et profils) du Member Center du programme Developer d’Apple.
2. Sous *Certificates*, sélectionnez **Production**.
3. Cliquez sur le **+** bouton pour créer un nouveau certificat.
4. Sous le titre *Production*, sélectionnez **App Store and Ad Hoc (App Store et ad hoc)**  :

    [![Sélectionner App Store et ad hoc](images/createcertmanually01.png)](images/createcertmanually01.png#lightbox)
5. Cliquez sur **Continuer**, puis suivez les instructions pour créer une demande de signature de certificat via le trousseau d’accès :

    [![Créer une demande de signature de certificat via un accès au trousseau](images/createcertmanually02.png)](images/createcertmanually02.png#lightbox)
6. Une fois que vous avez créé la CSR comme indiqué, cliquez sur **Continuer**, puis chargez la CSR dans le Centre des membres :

    [![Charger la CSR dans le Centre des membres](images/createcertmanually03.png)](images/createcertmanually03.png#lightbox)

7. Cliquez sur **Generate** pour créer le certificat.
8. Enfin, **Téléchargez** le certificat terminé et double-cliquez sur le fichier pour l’installer.
9. À ce stade, le certificat doit être installé sur l’ordinateur, mais vous devrez peut-être [Actualiser vos profils](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download)pour vous assurer qu’ils sont visibles dans Xcode.

Sinon, il est possible de demander un certificat via la boîte de dialogue Preferences de Xcode. Pour ce faire, procédez comme suit :

1. Sélectionnez votre équipe, puis cliquez sur **gérer les certificats...**: [ ![ Sélectionnez l’équipe et affichez les détails](images/selectteam.png)](images/selectteam.png#lightbox)

2. Ensuite, cliquez sur le bouton **créer** en regard de **certificat de distribution iOS**: [ ![ créer un certificat de distribution iOS](images/selectcert.png)](images/selectcert.png#lightbox)

3. En fonction des privilèges de l’équipe, l’identité de signature sera générée, comme indiqué ci-dessous, ou vous devrez peut-être attendre qu’un agent ou un administrateur d’équipe l’approuve : [ ![ l’identité de signature sera générée et une boîte de dialogue s’affichera](images/generated.png)](images/generated.png#lightbox) .

<a name="creatingprofile"></a>

## <a name="creating-a-distribution-profile"></a>Création d’un profil de distribution

<a name="creatingappid"></a>

### <a name="creating-an-app-id"></a>Création d’un ID d’application

Comme pour tout profil de provisionnement que vous créez, un ID d’application est nécessaire pour identifier l’application que vous distribuez sur l’appareil de l’utilisateur. Si vous ne l’avez pas déjà créé, suivez les étapes ci-dessous pour en créer un :

1. Dans [Apple Developer Center](https://developer.apple.com/account/overview.action), accédez à la section *Certificates, Identifiers & Profiles* (Certificats, identificateurs et profils). Sélectionnez **App IDs** sous **Identifiers**.
2. Cliquez sur le **+** bouton et fournissez un **nom** qui l’identifiera dans le portail.
3. Le préfixe d’application doit déjà être défini à l’aide de votre ID d’équipe. Il est impossible de le changer. Sélectionnez un ID d’application explicite ou avec des caractères génériques, puis entrez un ID de bundle au format DNS inversé, par exemple :
    - **Explicite** : com.[DomainName].[AppName]
    - **Avec des caractères génériques** : com.[DomainName].*
4. Sélectionnez les [services d’application](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioning-for-application-services) nécessaires à l’application.
5. Cliquez sur le bouton **Continuer** et suivez les instructions à l’écran pour créer l’ID d’application.

### <a name="creating-a-provisioning-profile"></a>Création d’un profil de provisionnement

Une fois que vous avez les composants nécessaires pour créer un profil de distribution, suivez les étapes ci-dessous pour le créer :

1. Revenez au portail de provisionnement Apple et sélectionnez distribution de **provisionnement**  >  **Distribution**:

    [![Sélectionner approvisionnement de la distribution de >](images/distribute01.png)](images/distribute01.png#lightbox)

2. Cliquez sur le **+** bouton et sélectionnez le type de profil de distribution que vous souhaitez créer comme **App Store**:

    [![Créer un profil de distribution App Store](images/distribute02.png)](images/distribute02.png#lightbox)

3. Cliquez sur le bouton **Continue**, puis dans la liste déroulante, sélectionnez l’ID d’application pour lequel vous souhaitez créer un profil de distribution :

    [![Sélectionner l’ID d’application dans la liste déroulante](images/distribute03.png)](images/distribute03.png#lightbox)

4. Cliquez sur le bouton **Continue**, puis sélectionnez le certificat nécessaire pour signer l’application :

    [![Sélectionner le certificat requis pour signer l’application](images/distribute04.png)](images/distribute04.png#lightbox)

5. Cliquez sur le bouton **Continue**, puis sélectionnez les appareils iOS sur lesquels l’application Xamarin.iOS est autorisée à s’exécuter :

    [![Sélectionner les appareils iOS sur lesquels l’application sera autorisée à s’exécuter](images/distribute05.png)](images/distribute05.png#lightbox)

6. Cliquez sur le bouton **Continue**, puis entrez un **Name (Nom)** pour le nouveau profil de distribution :

    [![Entrer un nom pour le nouveau profil de distribution](images/distribute06.png)](images/distribute06.png#lightbox)

7. Cliquez sur le bouton **Generate** pour créer le profil et finaliser le processus.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

 Vous devrez peut-être quitter Visual Studio pour Mac et obliger Xcode à actualiser sa liste d’identités de signature et de profils de provisionnement disponibles (en suivant les instructions de la section [Demande d’identités de signature](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download)) pour qu’un nouveau profil de distribution soit disponible dans Visual Studio pour Mac.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

 Vous devrez peut-être quitter Visual Studio et demander à Xcode (sur le Mac de l’hôte de Build) d’actualiser sa liste d’identités de signature et de profils de provisionnement disponibles (en suivant les instructions de la section [demande d’identités de signature](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download) ) avant qu’un nouveau profil de distribution ne soit disponible dans Visual Studio.

-----

<a name="selectprofile"></a>

## <a name="selecting-a-distribution-profile-in-a-xamarinios-project"></a>Sélection d’un profil de distribution dans un projet Xamarin.iOS

Une fois que vous êtes prêt à effectuer une build finale d’une application Xamarin.iOS pour la commercialiser dans l’App Store d’iTunes, sélectionnez le profil de distribution créé ci-dessus.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

 Dans Visual Studio pour Mac, effectuez les tâches suivantes :

1. Dans l’**Explorateur de solutions**, double-cliquez sur le nom du projet pour l’ouvrir et le modifier.
2. Sélectionnez **Signature du bundle iOS** et **Mise en production | iPhone** dans la liste déroulante **Configuration** :

    ![Sélectionner Mise en production | iPhone dans la liste déroulante Configuration](images/releasexs01.png)
3. Dans la plupart des cas, les options **Identité de signature** et **Profil de provisionnement** peuvent garder leurs valeurs par défaut **Automatique**. Visual Studio pour Mac choisit le profil approprié en fonction de l’identificateur de bundle dans Info.plist :

    ![Identité de signature et profil de provisionnement avec la valeur par défaut Automatique](images/releasexs02.png)
4. Le cas échéant, sélectionnez l’identité de signature et le profil de distribution (créé ci-dessus) dans les listes déroulantes :

    ![Sélectionner l’identité de signature et le profil de distribution](images/releasexs03.png)
5. Cliquez sur le bouton **OK** pour enregistrer les changements.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

 Dans Visual Studio, procédez comme suit :

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **Propriétés** pour l’ouvrir et le modifier.
2. Sélectionnez **Signature du bundle iOS** et **Mise en production | iPhone** dans la liste déroulante **Configuration** :

    ![Sélectionner Mise en production | iPhone dans la liste déroulante Configuration](images/releasevs01.png)
3. Dans la plupart des cas, les options **Identité de signature** et **Profil de provisionnement** peuvent garder leurs valeurs par défaut **Automatique**. Visual Studio choisit le profil approprié en fonction de l’identificateur de bundle dans Info.plist

    ![Identité de signature et profil de provisionnement avec la valeur par défaut Automatique](images/releasevs02.png)
4. Le cas échéant, sélectionnez l’identité de signature et le profil de distribution (créé ci-dessus) dans les listes déroulantes :

    ![Sélectionner l’identité de signature et le profil de distribution](images/releasevs03.png)
5. Enregistrez les changements apportés aux propriétés du projet.

-----

<a name="itunesconnect"></a>

## <a name="configuring-your-application-in-itunes-connect"></a>Configuration de votre application dans iTunes Connect

Une fois l’application correctement provisionnée, l’étape suivante consiste à la configurer dans [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa), une suite d’outils web qui vous permettent, entre autres choses, de gérer vos applications iOS dans l’App Store.

Vous devez préparer correctement votre application Xamarin.iOS dans iTunes Connect avant de pouvoir la soumettre pour évaluation à Apple, et ensuite la mettre en vente ou la proposer gratuitement dans l’App Store.

Pour plus d’informations, consultez [Configuration d’une application dans iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md) dans notre documentation.

<a name="submitting"></a>

## <a name="submitting-an-app-to-itunes-connect"></a>Soumission d’une application à iTunes Connect

Une fois que l’application a été signée à l’aide du profil de provisionnement de distribution et qu’elle a été créée dans iTunes Connect, son fichier binaire est chargé sur Apple pour évaluation. Si l’évaluation faite par Apple est positive, l’application est disponible dans l’App Store.

Pour plus d’informations sur la publication d’applications sur l’App Store, consultez [Publication sur l’App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md).

<a name="windows"></a>

## <a name="automatically-copy-app-bundles-back-to-windows"></a>Copier automatiquement les bundles .app dans Windows

[!include[](~/ios/includes/copy-app-bundle-to-windows.md)]

## <a name="summary"></a>Résumé

Cet article a présenté les composants clés de la préparation d’une application Xamarin.iOS pour sa distribution dans l’App Store.

## <a name="related-links"></a>Liens associés

- [Configuration d’une application dans iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [Publication sur l’App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
- [Distribution en interne](~/ios/deploy-test/app-distribution/in-house-distribution.md)
- [Distribution ad hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)
- [Le fichier iTunesMetadata. plist](~/ios/deploy-test/app-distribution/itunesmetadata.md)
- [Support de la Loi](~/ios/deploy-test/app-distribution/ipa-support.md)
