---
title: Provisionnement gratuit pour les applications Xamarin.iOS
description: Ce document décrit comment les développeurs Xamarin.iOS peuvent tester leur application sur un appareil physique sans avoir besoin de s’inscrire au programme de développement payant d’Apple.
ms.prod: xamarin
ms.assetid: A5CE2ECF-8057-49ED-8393-EB0C5977FE4C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/16/2018
ms.openlocfilehash: 951b019a4a0bf503629b6ac63853bd5b1c818bce
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022758"
---
# <a name="free-provisioning-for-xamarinios-apps"></a>Provisionnement gratuit pour les applications Xamarin.iOS

Le provisionnement gratuit permet aux développeurs Xamarin.iOS de déployer et tester leurs applications sur des appareils iOS **sans** devoir faire partie du programme **Developer** d’Apple.
Bien que les tests sur simulateurs soient utiles et pratiques, il est également essentiel de tester les applications sur des appareils iOS physiques pour vérifier qu’elles fonctionnent correctement dans le cadre de contraintes réelles liées à la mémoire, au stockage et à la connectivité réseau.

Pour utiliser le provisionnement gratuit et déployer une application sur un appareil :

- Utilisez Xcode pour créer l’*identité de signature* nécessaire (certificat de développeur et clé privée), ainsi que le *profil de provisionnement* (contenant un ID d’application explicite et l’UDID d’un appareil iOS connecté).
- Utilisez l’identité de signature et le profil de provisionnement créés par Xcode dans Visual Studio pour Mac ou Visual Studio 2019 afin de déployer votre application Xamarin.iOS.

> [!IMPORTANT]
> Le [provisionnement automatique](~/ios/get-started/installation/device-provisioning/automatic-provisioning.md) permet à Visual Studio pour Mac ou Visual Studio 2019 de configurer automatiquement un appareil pour permettre aux développeurs d’effectuer des tests. Toutefois, le provisionnement automatique n’est pas compatible avec le provisionnement gratuit. Pour pouvoir utiliser le provisionnement automatique, vous devez avoir un compte payant du programme Developer d’Apple.

## <a name="requirements"></a>spécifications

Pour déployer vos applications Xamarin.iOS sur un appareil à l’aide du provisionnement gratuit :

- L’identifiant Apple utilisé ne doit pas être connecté au programme Developer d’Apple.
- Votre application Xamarin.iOS doit utiliser un ID d’application explicite, et non un ID d’application générique.
- L’identificateur de bundle utilisé dans votre application Xamarin.iOS doit être unique. Il ne doit pas avoir été utilisé dans une autre application. Vous ne **pouvez** pas réutiliser un identificateur de bundle avec le provisionnement gratuit.
- Si vous avez déjà distribué une application, vous ne pouvez pas la déployer à l’aide du provisionnement gratuit.
- Si votre application utilise App Services, vous devez créer un profil de provisionnement comme indiqué dans le guide de [provisionnement des appareils](~/ios/get-started/installation/device-provisioning/index.md#provisioning-for-application-services). 

Pour plus d’informations sur les limitations associées au provisionnement gratuit, consultez la section [Limitations](#limitations) de ce document. Consultez également les [guides de distribution d’applications](~/ios/deploy-test/app-distribution/index.md) pour plus d’informations sur la distribution d’applications iOS.

## <a name="testing-on-device-with-free-provisioning"></a>Test d’appareil à l’aide du provisionnement gratuit

Suivez les étapes ci-dessous pour tester votre application Xamarin.iOS à l’aide du provisionnement gratuit.

### <a name="use-xcode-to-create-a-signing-identity-and-provisioning-profile"></a>Utiliser Xcode pour créer une identité de signature et un profil de provisionnement

1. Si vous n’avez pas d’identifiant Apple, [créez-en un](https://appleid.apple.com).
2. Ouvrez Xcode et accédez à **Xcode > Préférences**.
3. Sous **comptes**, utilisez le bouton **+** pour ajouter votre ID Apple existant. Celui-ci doit ressembler à la capture d’écran suivante :

    ![Préférences de Xcode-comptes](free-provisioning-images/launchapp1.png "Préférences de Xcode-comptes")

4. Fermez les préférences Xcode.
5. Branchez l’appareil iOS sur lequel vous souhaitez déployer votre application.
6. Dans Xcode, créez un projet. Choisissez **Fichier > Nouveau > Projet**, puis sélectionnez **Application avec affichage unique**.
7. Dans la boîte de dialogue Nouveau projet, affectez à **Équipe** l’identifiant Apple que vous venez d’ajouter. Dans la liste déroulante, cela doit ressembler à **Votre nom (équipe personnelle)**  :

    ![Créer une application](free-provisioning-images/launchapp2.png "Créer une application")

8. Une fois le projet créé, choisissez un schéma de build Xcode qui cible votre appareil iOS (plutôt qu’un simulateur).

    ![Sélectionner un schéma de génération Xcode](free-provisioning-images/xcodescheme.png "Sélectionner un schéma de génération Xcode")

9. Ouvrez les paramètres de projet de votre application en sélectionnant son nœud de niveau supérieur dans **Navigation dans le projet** au sein de Xcode.
10. Sous **Général > Identité**, vérifiez que l’**identificateur de bundle** _correspond exactement_ à l’identificateur de bundle de votre application Xamarin.iOS.

    ![Définir un identificateur de Bundle](free-provisioning-images/launchapp5.png "Définir un identificateur de Bundle")

    > [!IMPORTANT]
    > Xcode va créer uniquement un profil de provisionnement pour un ID d’application explicite. Celui-ci doit être identique à l’ID d’application de votre application Xamarin.iOS.
    > S’ils diffèrent, vous ne pourrez pas utiliser le provisionnement gratuit pour déployer votre application Xamarin.iOS.

11. Sous **Informations de déploiement**, vérifiez que la version de la cible de déploiement est égale ou inférieure à la version d’iOS installée sur votre appareil iOS connecté.
12. Sous **Signature**, sélectionnez **Gérer automatiquement la signature**, puis sélectionnez votre équipe dans la liste déroulante :

    ![Gérer automatiquement la signature](free-provisioning-images/launchapp6.png "Gérer automatiquement la signature")

    Xcode génère automatiquement un profil de provisionnement et une identité de signature qui vous sont destinés. Cliquez sur l’icône d’information située en regard du profil de provisionnement pour l’afficher :

    ![Afficher le profil de provisionnement](free-provisioning-images/launchapp7.png "Afficher le profil de provisionnement")

    > [!TIP]
    > En cas d’échec, quand Xcode tente de générer un profil de provisionnement, vérifiez que le schéma de build sélectionné par Xcode cible l’appareil iOS connecté plutôt qu’un simulateur.

13. Pour tester dans Xcode, déployez l’application vide sur votre appareil en cliquant sur le bouton d’exécution.

### <a name="deploy-your-xamarinios-app"></a>Déployer votre application Xamarin.iOS

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Connectez votre appareil iOS à l’hôte de build Mac via une connexion USB ou [sans fil](~/ios/deploy-test/wireless-deployment.md).
2. Dans le **Panneau Solutions** de Visual Studio pour Mac, double-cliquez sur **Info.plist**.
3. Dans **Signature**, sélectionnez **Provisionnement manuel**.
4. Cliquez sur **Signature du bundle iOS** disproportionnée.
5. Pour **Configuration**, sélectionnez **Debug**.
6. Pour **Plateforme**, sélectionnez **iPhone**.
7. Sélectionnez l’**identité de signature** créée par Xcode.
8. Sélectionnez le **Profil de provisionnement** créé par Xcode.

    ![Définir l’identité de signature et le profil de provisionnement](free-provisioning-images/launchapp8.png "Définir l’identité de signature et le profil de provisionnement")

    > [!TIP]
    > Si vous ne voyez pas votre identité de signature ou le profil de provisionnement approprié, vous devrez éventuellement redémarrer Visual Studio pour Mac.

9. Cliquez sur **OK** pour enregistrer et fermer les **Options de projet**.
10. Sélectionnez votre appareil iOS, et exécutez l’application.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Vérifiez que Visual Studio 2019 ou Visual Studio 2017 a été [appairé à un hôte de build Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).
2. Connectez votre appareil iOS à l’hôte de build Mac via une connexion USB ou [sans fil](~/ios/deploy-test/wireless-deployment.md).
3. Dans l’**Explorateur de solutions** de Visual Studio 2019 ou Visual Studio 2017, cliquez avec le bouton droit sur votre projet Xamarin.iOS, puis sélectionnez **Propriétés**.
4. Accédez à **Signature du bundle iOS**.
5. Pour **Configuration**, sélectionnez **Debug**.
6. Pour **Plateforme**, sélectionnez **iPhone**.
7. Sélectionnez **Provisionnement manuel**.
8. Sélectionnez l’**identité de signature** créée par Xcode.
9. Sélectionnez le **Profil de provisionnement** créé par Xcode.
    
    ![Définir l’identité de signature et le profil de provisionnement](free-provisioning-images/setprofile-w157.png "Définir l’identité de signature et le profil de provisionnement")

    > [!TIP]
    > Xcode a créé cette identité de signature et ce profil de provisionnement, puis les a stockés sur votre hôte de build Mac. Ils sont accessibles à Visual Studio 2019 ou Visual Studio 2017, car ils ont été [appairés](~/ios/get-started/installation/windows/connecting-to-mac/index.md) à l’hôte de build Mac. S’ils ne sont pas listés, vous devrez peut-être redémarrer Visual Studio 2019 ou Visual Studio 2017.

10. Enregistrez et fermez les propriétés du projet.
11. Sélectionnez votre appareil iOS, et exécutez l’application.

-----

## <a name="limitations"></a>Limitations

Apple a imposé un certain nombre de limitations qui définissent quand et comment utiliser le provisionnement gratuit pour exécuter votre application sur un appareil iOS. Ainsi, vous pouvez uniquement effectuer un déploiement sur *votre* appareil :

- Dans la mesure où l’accès à iTunes Connect est limité, les services tels que la publication sur l’App Store et TestFlight ne sont pas disponibles pour les développeurs qui provisionnent gratuitement leurs applications. Un compte de développeur Apple (Entreprise ou Personnel) est exigé pour distribuer par le biais de moyens ad hoc et internes.
- Les profils de provisionnement créés à l’aide du provisionnement gratuit expirent au bout d’une semaine, et les identités de signature au bout d’un an. 
- Dans la mesure où Xcode crée uniquement des profils de provisionnement pour les ID d’application explicites, vous devez suivre les [instructions ci-dessus](#testing-on-device-with-free-provisioning) pour chaque application à installer.
- Le provisionnement de la plupart des services d’application n’est pas possible avec le provisionnement gratuit. Cela inclut Apple Pay, Game Center, iCloud, les achats in-app, les notifications Push et Wallet. Apple fournit une liste complète des fonctionnalités dans le guide des [Fonctionnalités prises en charge (iOS)](https://help.apple.com/developer-account/#/dev21218dfd6). Pour provisionner votre application afin de l’utiliser avec des services d’application, consultez les guides d’[utilisation des fonctionnalités](~/ios/deploy-test/provisioning/capabilities/index.md).

## <a name="summary"></a>Récapitulatif

Ce guide a exploré les avantages et les limites de l’utilisation du provisionnement gratuit pour l’installation d’applications sur un appareil iOS. Il a également fourni une procédure étape par étape, qui montre comment utiliser le provisionnement gratuit pour installer une application Xamarin.iOS.

## <a name="related-links"></a>Liens connexes

- [Provisionnement des appareils](~/ios/get-started/installation/device-provisioning/index.md)
- [Provisionnement des services d’application](~/ios/get-started/installation/device-provisioning/index.md#provisioning-for-application-services)
