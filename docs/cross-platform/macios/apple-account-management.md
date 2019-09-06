---
title: Gestion des comptes Apple
description: Ce document explique comment utiliser les fonctionnalités de gestion des comptes Apple dans Visual Studio pour Mac et Visual Studio 2019.
ms.prod: xamarin
ms.assetid: 71388B83-699B-4E42-8CBF-8557A4A3CABF
author: conceptdev
ms.author: crdun
ms.date: 05/06/2018
ms.openlocfilehash: 9629d775b45951279178dffa3600e7cd5073dd38
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290630"
---
# <a name="apple-account-management"></a>Gestion des comptes Apple

L’interface de gestion des comptes Apple offre un moyen d’afficher toutes les équipes de développement associées à un ID Apple. Elle vous permet également d’afficher plus de détails sur chaque équipe en affichant une liste d' _identités de signature_ et de _profils de provisionnement_ installés sur votre ordinateur.

L’authentification de votre ID Apple est effectuée sur la ligne de commande avec [Fastlane](https://fastlane.tools/). FastLane doit être installé sur votre ordinateur pour que vous soyez authentifié avec succès. Pour plus d’informations sur Fastlane et sur son installation, reposez-vous dans les guides [Fastlane](~/ios/deploy-test/provisioning/fastlane/index.md) .

La boîte de dialogue compte Apple vous permet d’effectuer les opérations suivantes :

- **Créer et gérer des certificats**
- **Créer et gérer des profils de provisionnement**

Pour plus d’informations sur la procédure à suivre, voir ce guide.

> [!NOTE]
> Les outils de Xamarin pour la gestion des comptes Apple affichent uniquement des informations sur les comptes de développeurs Apple payants. Pour savoir comment tester une application sur un appareil sans compte de développeur Apple payant, consultez le guide [d’approvisionnement gratuit pour les applications Xamarin. iOS](~/ios/get-started/installation/device-provisioning/free-provisioning.md) .

Vous pouvez également utiliser les outils d’approvisionnement automatique iOS pour créer et gérer automatiquement vos identités de signature, ID d’application et profils de provisionnement. Pour plus d’informations sur l’utilisation de ces fonctionnalités, reportez-vous au Guide de [provisionnement des appareils](~/ios/get-started/installation/device-provisioning/index.md) .

## <a name="requirements"></a>Configuration requise

La gestion des comptes Apple est disponible sur Visual Studio pour Mac, Visual Studio 2019 et Visual Studio 2017 (version 15,7 et ultérieure).

Pour utiliser cette fonctionnalité, vous devez disposer d’un compte de développeur Apple. Vous trouverez plus d’informations sur les comptes de développeurs Apple dans le Guide de [provisionnement des appareils](~/ios/get-started/installation/device-provisioning/index.md) .

- Vérifiez que vous êtes connecté à Internet. Cela est dû au fait que Fastlane communique directement avec le portail des développeurs Apple.
- Vérifiez que les [Outils Fastlane sont installés](~/ios/deploy-test/provisioning/fastlane/index.md#Installation).
- Vérifiez que vous disposez des derniers outils Fastlane [https://download.fastlane.tools](https://download.fastlane.tools)de.
- Avant de commencer, veillez à accepter tous les contrats de licence utilisateur dans le [portail des développeurs](https://developer.apple.com/account/).

## <a name="adding-an-apple-developer-account"></a>Ajout d’un compte de développeur Apple

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Pour ouvrir la boîte de dialogue gestion des comptes, accédez à **Visual Studio > préférences > compte de développeur Apple**:

    ![Options du compte de développeur Apple](apple-account-management-images/image1.png)

2. Appuyez sur **+** le bouton pour afficher la boîte de dialogue de connexion, comme illustré ci-dessous : 

    ![boîte de dialogue Fastlane.](apple-account-management-images/image2.png)

3. Entrez votre ID Apple et votre mot de passe, puis cliquez sur le bouton **se connecter** . Cela permet d’enregistrer vos informations d’identification dans le trousseau sécurisé sur cet ordinateur. [Fastlane](~/ios/deploy-test/provisioning/fastlane/index.md) est utilisé pour gérer vos informations d’identification en toute sécurité et les transmettre au portail des développeurs d’Apple.
 
4. Sélectionnez **toujours autoriser** dans la boîte de dialogue d’alerte pour permettre à Visual Studio d’utiliser vos informations d’identification :

    ![Toujours autoriser la boîte de dialogue d’alerte](apple-account-management-images/image4.png)

5. Une fois que votre compte a été ajouté avec succès, vous voyez votre ID Apple et toutes les équipes dont votre ID Apple fait partie.

    ![Boîte de dialogue compte de développeur Apple avec comptes ajoutés](apple-account-management-images/image5.png)

6. Sélectionnez une équipe et appuyez sur **afficher les détails...** disproportionnée. Cette opération affiche une liste de toutes les identités de signature et des profils de provisionnement installés sur votre ordinateur :

    ![Écran afficher les détails présentant les identités de signature et les profils de provisionnement sur votre ordinateur](apple-account-management-images/image6.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Avant de commencer à ajouter votre ID Apple à Visual Studio 2019, assurez-vous que votre environnement de développement est [associé à un hôte de build Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Pour ouvrir la fenêtre de gestion des comptes, accédez à **outils > Options > Xamarin > comptes Apple**:

    ![Écran Options de comptes Apple](apple-account-management-images/prov1.png)

1. Sélectionnez le bouton **Ajouter** et entrez votre ID Apple et votre mot de passe :

    ![boîte de dialogue nom d’utilisateur et mot de passe](apple-account-management-images/prov1a.png)

1. Une fois que votre compte a été ajouté avec succès, vous voyez votre ID Apple et toutes les équipes dont votre ID Apple fait partie.
 
1. Sélectionnez une équipe et appuyez sur **afficher les détails...** disproportionnée. Cette opération affiche une liste de toutes les identités de signature et des profils de provisionnement installés sur votre ordinateur :

    ![boîte de dialogue nom d’utilisateur et mot de passe](apple-account-management-images/prov2.png)

-----


## <a name="managing-signing-identities-and-provisioning-profiles"></a>Gestion des identités de signature et des profils de provisionnement

La boîte de dialogue détails de l’équipe affiche la liste des identités de signature, classées par type. La colonne **État** vous conseille si le certificat est : 

- **Valide** : l’identité de signature (à la fois le certificat et la clé privée) est installée sur votre ordinateur et n’a pas expiré.

- **Pas dans le trousseau** : il existe une identité de signature valide sur le serveur d’Apple. Pour l’installer sur votre ordinateur, celle-ci doit être exportée à partir d’un autre ordinateur. Vous ne pouvez pas télécharger l’identité de signature à partir du portail des développeurs Apple, car elle ne contient pas la clé privée.

- **Clé privée manquante** : un certificat sans clé privée est installé dans le trousseau.

- **Expiré** – le certificat a expiré. Vous devez le supprimer de votre trousseau.

  ![informations de la boîte de dialogue détails de l’équipe](apple-account-management-images/image7.png)

## <a name="create-a-signing-identities"></a>Créer des identités de signature

Pour créer une identité de signature, sélectionnez le bouton déroulant **créer un certificat** et sélectionnez le type dont vous avez besoin. Si vous disposez des autorisations appropriées, une nouvelle identité de signature s’affiche après quelques secondes.

Si une option dans la liste déroulante est grisée et désactivée, cela signifie que vous ne disposez pas des autorisations d’équipe appropriées pour créer ce type de certificat.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

![créer des options de certificat](apple-account-management-images/image8.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![créer des options de certificat](apple-account-management-images/prov3.png)

-----

## <a name="download-provisioning-profiles"></a>Télécharger les profils de provisionnement

La boîte de dialogue détails de l’équipe affiche également une liste de tous les profils d’approvisionnement connectés à votre compte de développeur. Vous pouvez télécharger tous les profils de provisionnement sur votre ordinateur local en appuyant sur le bouton **Télécharger tous les profils**

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

![Télécharger la section des profils de provisionnement](apple-account-management-images/image9.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Télécharger la section des profils de provisionnement](apple-account-management-images/prov4.png)

-----

## <a name="ios-bundle-signing"></a>Signature d’offre groupée iOS

Pour plus d’informations sur le déploiement de votre application sur un appareil, reportez-vous au Guide de [provisionnement des appareils](~/ios/get-started/installation/device-provisioning/index.md) .

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="view-details-dialog-is-empty"></a>La boîte de dialogue Afficher les détails est vide

Il s’agit actuellement d’un problème connu, relatif au bogue [#53906](https://bugzilla.xamarin.com/show_bug.cgi?id=53906). Assurez-vous que vous utilisez la dernière version stable de Visual Studio pour Mac

### <a name="if-you-are-experiencing-issues-logging-in-your-account-please-try-the-following"></a>Si vous rencontrez des problèmes de journalisation dans votre compte, veuillez essayer les éléments suivants :

- Ouvrez l’application Trousseau et, sous catégorie, sélectionnez *mots de passe*. `deliver.`Recherchez et supprimez toutes les entrées.

### <a name="error-adding-account-please-sign-in-with-an-app-specific-password"></a>«Erreur lors de l’ajout du compte. Connectez-vous avec un mot de passe spécifique à l’application»

Cela est dû au fait que l’authentification à 2 facteurs est activée sur votre compte. Assurez-vous que vous utilisez la dernière version stable de Visual Studio pour Mac

### <a name="failed-to-create-new-certificate"></a>Échec de la création du nouveau certificat
« Vous avez atteint la limite pour les certificats de ce type »

![boîte de dialogue limite de certificat](apple-account-management-images/image10.png)

Le nombre maximal de certificats autorisés a été généré. Pour résoudre ce problème, accédez au [Centre de développement Apple](https://developer.apple.com/account/ios/certificate/distribution) et Révoquez l’un des certificats de production.

## <a name="known-issues"></a>Problèmes connus

- Par défaut, les profils d’approvisionnement de distribution ciblent l’App Store. Vous devez créer les profils internes ou ad hoc manuellement.
