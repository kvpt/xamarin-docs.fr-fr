---
title: Gestion des comptes Apple
description: Ce document décrit comment utiliser les fonctionnalités de gestion de compte Apple dans Visual Studio pour Mac et Visual Studio 2019.
ms.prod: xamarin
ms.assetid: 71388B83-699B-4E42-8CBF-8557A4A3CABF
author: asb3993
ms.author: amburns
ms.date: 05/06/2018
ms.openlocfilehash: 28a7d299e78a7fa6837ba9285795d380760f13b7
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864642"
---
# <a name="apple-account-management"></a>Gestion des comptes Apple

L’interface de gestion de compte Apple fournit un moyen pour afficher toutes les équipes de développement associés à un ID Apple. Il vous permet également d’afficher plus de détails à propos de chaque équipe en affichant une liste de _d’identités de signature_ et _profils de provisionnement_ qui sont installés sur votre ordinateur.

L’authentification de votre ID Apple est effectuée sur la ligne de commande avec [fastlane](https://fastlane.tools/). FastLane doit être installé sur votre ordinateur pour pouvoir être authentifié avec succès. Plus d’informations sur fastlane et son installation sont détaillées dans le [fastlane](~/ios/deploy-test/provisioning/fastlane/index.md) guides.

La boîte de dialogue compte Apple vous permet de procéder comme suit :

* **Créer et gérer des certificats**
* **Créer et gérer des profils de provisionnement**

Pour plus d’informations sur comment effectuer cette opération est décrite dans ce guide.

> [!NOTE]
> Outils de Xamarin pour la gestion des comptes Apple affichent uniquement les informations sur les comptes de développeur Apple payants. Pour savoir comment tester une application sur un appareil sans un compte de développeur Apple payant, veuillez consulter la [provisionnement libre pour les applications Xamarin.iOS](~/ios/get-started/installation/device-provisioning/free-provisioning.md) guide.

Vous pouvez également utiliser les outils de l’approvisionnement automatique iOS automatiquement pour créer et gérer vos identités de signature, les ID d’application et les profils de provisionnement. Pour plus d’informations sur l’utilisation de ces fonctionnalités, reportez-vous à la [Device Provisioning](~/ios/get-started/installation/device-provisioning/index.md) guide.

## <a name="requirements"></a>Configuration requise

Gestion des comptes Apple est disponible dans Visual Studio pour Mac, Visual Studio 2019 et Visual Studio 2017 (Version 15.7 et versions ultérieures).

Vous devez disposer d’un compte de développeur Apple pour utiliser cette fonctionnalité. Plus d’informations sur les comptes de développeur Apple sont disponibles dans le [Device Provisioning](~/ios/get-started/installation/device-provisioning/index.md) guide.

- Vérifiez que vous êtes connecté à internet. Il s’agit, car fastlane communique directement avec le portail des développeurs Apple.
- Assurez-vous d’avoir [outils fastlane installés](~/ios/deploy-test/provisioning/fastlane/index.md#Installation).
- Assurez-vous d’avoir les derniers outils fastlane à partir de [ https://download.fastlane.tools ](https://download.fastlane.tools).
- Avant de commencer, veillez à accepter les contrats de licence utilisateur dans le [portail des développeurs](https://developer.apple.com/account/).

## <a name="adding-an-apple-developer-account"></a>Ajout d’un compte de développeur Apple

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Pour ouvrir la boîte de dialogue de gestion de compte, rendez-vous à **Visual Studio > Préférences > compte de développeur Apple**:

    ![Options de compte de développeur Apple](apple-account-management-images/image1.png)

2. Appuyez sur la **+** bouton pour afficher la connexion dans la boîte de dialogue, comme illustré ci-dessous : 

    ![boîte de dialogue de FastLane.](apple-account-management-images/image2.png)

3. Entrez votre ID Apple et le mot de passe et cliquez sur le **Sign In** bouton. Ceci enregistre vos informations d’identification dans le trousseau de clés sécurisé sur cet ordinateur. [FastLane](~/ios/deploy-test/provisioning/fastlane/index.md) est utilisé pour gérer vos informations d’identification en toute sécurité et de les transmettre au portail des développeurs d’Apple.
 
4. Sélectionnez **toujours autoriser** dans la boîte de dialogue alerte pour autoriser Visual Studio à utiliser vos informations d’identification :

    ![Toujours autoriser la boîte de dialogue alerte](apple-account-management-images/image4.png)

5. Une fois que votre compte a été ajouté avec succès, vous verrez votre ID Apple et les équipes faisant partie de votre ID Apple.

    ![Boîte de dialogue compte de développeur Apple avec les comptes ajoutés](apple-account-management-images/image5.png)

6. Sélectionnez une équipe, puis appuyez sur la **afficher les détails...** disproportionnée. Cela affiche une liste de toutes les identités de signature et les profils de provisionnement qui sont installés sur votre ordinateur :

    ![Vue Détails de l’écran d’illustrant identités de signature et de profils sur votre ordinateur d’approvisionnement](apple-account-management-images/image6.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Avant d’ajouter votre ID Apple pour Visual Studio 2019, assurez-vous que votre environnement de développement est [associé à un hôte de build Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Pour ouvrir la fenêtre de gestion de compte, accédez à **Outils > Options > Xamarin > comptes Apple**:

    ![Écran des options de comptes Apple](apple-account-management-images/prov1.png)

1. Sélectionnez le **ajouter** bouton et entrez votre ID Apple et le mot de passe :

    ![boîte de dialogue Nom d’utilisateur et mot de passe](apple-account-management-images/prov1a.png)

1. Une fois que votre compte a été ajouté avec succès, vous verrez votre ID Apple et les équipes faisant partie de votre ID Apple.
 
1. Sélectionnez une équipe, puis appuyez sur la **afficher les détails...** disproportionnée. Cela affiche une liste de toutes les identités de signature et les profils de provisionnement qui sont installés sur votre ordinateur :

    ![boîte de dialogue Nom d’utilisateur et mot de passe](apple-account-management-images/prov2.png)

-----


## <a name="managing-signing-identities-and-provisioning-profiles"></a>La gestion des identités de signature et profils de provisionnement

La boîte de dialogue Détails de l’équipe affiche une liste d’identités de signature, organisé par type. Le **état** colonne vous indique si le certificat est : 

* **Valide** : l’identité de signature (le certificat et la clé privée) est installée sur votre ordinateur et il n’a pas expiré.

* **Pas dans le trousseau** – sur site est une identité de signature valide d’Apple. Pour l’installer sur votre ordinateur, il doit être exporté à partir d’un autre ordinateur. Comme il ne contiendra pas la clé privée, vous ne pouvez télécharger l’identité de signature à partir du portail des développeurs Apple.

* **Clé privée est manquante** – un certificat sans clé privée est installé dans le trousseau.

* **Expiré** – le certificat a expiré. Vous devez le supprimer de votre trousseau de clés.

  ![informations de boîte de dialogue Détails de l’équipe](apple-account-management-images/image7.png)

## <a name="create-a-signing-identities"></a>Créer une identité de signature

Pour créer une nouvelle identité de signature, sélectionnez le **Create Certificate** bouton de liste déroulante et sélectionnez le type dont vous avez besoin. Si vous avez les autorisations appropriées pour une nouvelle signature identité s’affiche après quelques secondes.

Si une option dans la liste déroulante est grisée et désactivée, cela signifie que vous n’avez pas les autorisations de l’équipe appropriée pour créer ce type de certificat.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

![créer des options de certificat](apple-account-management-images/image8.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![créer des options de certificat](apple-account-management-images/prov3.png)

-----

## <a name="download-provisioning-profiles"></a>Télécharger des profils de provisionnement

La boîte de dialogue Détails de l’équipe affiche également une liste de tous les profils de provisionnement connecté à votre compte de développeur. Vous pouvez télécharger tous les profils de provisionnement sur votre ordinateur local en appuyant sur la **télécharger tous les profils** bouton

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

![Télécharger la section de profils de provisionnement](apple-account-management-images/image9.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Télécharger la section de profils de provisionnement](apple-account-management-images/prov4.png)

-----

## <a name="ios-bundle-signing"></a>Signature d’offre groupée iOS

Pour plus d’informations sur le déploiement de votre application sur un appareil, reportez-vous à la [provisionnement des appareils](~/ios/get-started/installation/device-provisioning/index.md) guide.

## <a name="troubleshooting"></a>Résolution de problèmes

### <a name="view-details-dialog-is-empty"></a>Boîte de dialogue Vue détails est vide

Il s’agit actuellement un problème connu, relatives à un bogue [#53906](https://bugzilla.xamarin.com/show_bug.cgi?id=53906). Assurez-vous que vous utilisez la dernière version stable de Visual Studio pour Mac

### <a name="if-you-are-experiencing-issues-logging-in-your-account-please-try-the-following"></a>Si vous rencontrez des problèmes de connexion dans votre compte, essayez les opérations suivantes :

* Ouvrez l’application trousseau et sous la catégorie sélectionnez *les mots de passe*. Recherchez `deliver.`, puis supprimez toutes les entrées.

### <a name="error-adding-account-please-sign-in-with-an-app-specific-password"></a>« Erreur d’ajout du compte. Connectez-vous avec un mot de passe spécifiques de l’application »

Il s’agit, car l’authentification à 2 facteurs est activée sur votre compte. Assurez-vous que vous utilisez la dernière version stable de Visual Studio pour Mac

### <a name="failed-to-create-new-certificate"></a>Impossible de créer un nouveau certificat
« Vous avez atteint la limite pour les certificats de ce type »

![boîte de dialogue certificat limite](apple-account-management-images/image10.png)

Le nombre maximal de certificats autorisés ont été généré. Pour résoudre ce problème, accédez à la [Developer Center d’Apple](https://developer.apple.com/account/ios/certificate/distribution) et révoquer le certificat de Production.

## <a name="known-issues"></a>Problèmes connus

* Par défaut, les profils d’approvisionnement de distribution ciblent l’App Store. Vous devez créer les profils internes ou ad hoc manuellement.
