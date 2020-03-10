---
title: Gestion des comptes Apple
description: Ce document explique comment utiliser les fonctionnalités de gestion des comptes Apple dans Visual Studio pour Mac et Visual Studio 2019.
ms.prod: xamarin
ms.assetid: 71388B83-699B-4E42-8CBF-8557A4A3CABF
author: davidortinau
ms.author: daortin
ms.date: 03/05/2020
ms.openlocfilehash: 17607e09a141fd29cd81cde93d812b20e62a9af8
ms.sourcegitcommit: 60d2243809d8e980fca90b9f771e72f8c0e64d71
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "78946248"
---
# <a name="apple-account-management"></a>Gestion des comptes Apple

L’interface de gestion des comptes Apple dans Visual Studio offre un moyen d’afficher des informations pour les équipes de développement associées à un ID Apple. Elle vous permet d’effectuer les opérations suivantes :

- **Ajouter des comptes de développeurs Apple**
- **Afficher les certificats de signature et les profils de provisionnement**
- **Créer des certificats de signature**
- **Télécharger les profils de provisionnement existants**

> [!IMPORTANT]
> Les outils de Xamarin pour la gestion des comptes Apple affichent uniquement des informations sur les comptes de développeurs Apple payants. Pour savoir comment tester une application sur un appareil sans compte de développeur Apple payant, consultez le guide [d’approvisionnement gratuit pour les applications Xamarin. iOS](~/ios/get-started/installation/device-provisioning/free-provisioning.md) .

## <a name="requirements"></a>Spécifications

La gestion des comptes Apple est disponible sur Visual Studio pour Mac, Visual Studio 2019 et Visual Studio 2017 (version 15,7 et ultérieure). Vous devez également avoir un compte de développeur Apple payant pour utiliser cette fonctionnalité. Vous trouverez plus d’informations sur les comptes de développeurs Apple dans le Guide de [provisionnement des appareils](~/ios/get-started/installation/device-provisioning/index.md) .

> [!NOTE]
> Avant de commencer, veillez à accepter d’abord tous les contrats de licence utilisateur dans le [portail des développeurs Apple](https://developer.apple.com/account/).

## <a name="add-an-apple-developer-account"></a>Ajouter un compte de développeur Apple

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

1. Accédez à **Visual Studio > préférences > compte de développeur Apple** , puis cliquez sur le bouton **+** pour ouvrir la boîte de dialogue de connexion :

    ![AScreenshot de la page des comptes de développeurs Apple dans Visual Studio pour Mac préférences.](apple-account-management-images/add-account-vsm.png)

2. Entrez votre ID Apple et votre mot de passe, puis cliquez sur **se connecter**. Cela permet d’enregistrer vos informations d’identification dans le trousseau sécurisé sur cet ordinateur.

3. Sélectionnez **toujours autoriser** dans la boîte de dialogue d’alerte pour permettre à Visual Studio d’utiliser vos informations d’identification :

    ![Toujours autoriser la boîte de dialogue d’alerte](apple-account-management-images/image4.png)

4. Une fois que votre compte a été ajouté avec succès, vous verrez votre ID Apple et toutes les équipes dont votre ID Apple fait partie :

    ![Boîte de dialogue compte de développeur Apple avec comptes ajoutés](apple-account-management-images/image5.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

> [!NOTE]
> Si vous utilisez Visual Studio 2017 ou Visual Studio 2019 (version 16,4 et antérieure), vous devez être [associé à un hôte de build Mac avant de](~/ios/get-started/installation/windows/connecting-to-mac/index.md) continuer.

1. Accédez à **outils > Options > Xamarin > comptes Apple** , puis cliquez sur **Ajouter**:

    ![Capture d’écran de la page comptes Apple dans les options de Visual Studio.](apple-account-management-images/add-account-vsw.png)

2. Entrez votre ID Apple et votre mot de passe, puis cliquez sur **connexion**.

3. Une fois que votre compte a été ajouté avec succès, vous voyez votre ID Apple et toutes les équipes dont votre ID Apple fait partie :

    ![Capture d’écran de la page compte de développeur avec comptes ajoutés.](apple-account-management-images/accounts-vsw.png)

-----

## <a name="view-signing-certificates-and-provisioning-profiles"></a>Afficher les certificats de signature et les profils de provisionnement

Sélectionnez une équipe, puis cliquez sur **afficher les détails...** pour ouvrir une boîte de dialogue qui affiche la liste des identités de signature et des profils de provisionnement installés sur votre ordinateur.

La boîte de dialogue détails de l’équipe affiche la liste des identités de signature, classées par type. La colonne **État** vous conseille si le certificat est : 

- **Valide** : l’identité de signature (à la fois le certificat et la clé privée) est installée sur votre ordinateur et n’a pas expiré.

- **Pas dans le trousseau** : il existe une identité de signature valide sur le serveur d’Apple. Pour l’installer sur votre ordinateur, celle-ci doit être exportée à partir d’un autre ordinateur. Vous ne pouvez pas télécharger l’identité de signature à partir du portail des développeurs Apple, car elle ne contient pas la clé privée.

- **Clé privée manquante** : un certificat sans clé privée est installé dans le trousseau.

- **Expiré** – le certificat a expiré. Vous devez le supprimer de votre trousseau.

  ![informations de la boîte de dialogue détails de l’équipe](apple-account-management-images/image7.png)

## <a name="create-a-signing-certificate"></a>Créer un certificat de signature

Pour créer une identité de signature, cliquez sur **créer un certificat** pour ouvrir le menu déroulant et sélectionnez le type de [certificat](https://help.apple.com/xcode/mac/current/#/dev80c6204ec) que vous souhaitez créer. Si vous disposez des autorisations appropriées, une nouvelle identité de signature s’affiche après quelques secondes.

Si une option dans la liste déroulante est grisée et désactivée, cela signifie que vous ne disposez pas des autorisations d’équipe appropriées pour créer ce type de certificat.

## <a name="download-provisioning-profiles"></a>Télécharger les profils de provisionnement

La boîte de dialogue détails de l’équipe affiche également une liste de tous les profils d’approvisionnement connectés à votre compte de développeur. Vous pouvez télécharger tous les profils de provisionnement sur votre ordinateur local en cliquant sur **Télécharger tous les profils**.


## <a name="troubleshoot"></a>Dépanner

- L’approbation d’un nouveau compte de développeur Apple peut prendre plusieurs heures. Vous ne pouvez pas activer l’approvisionnement automatique tant que le compte n’a pas été approuvé.

- Si l’ajout d’un compte de développeur Apple échoue avec le message `Authentication Error: Xcode 7.3 or later is required to continue developing with your Apple ID.`, assurez-vous que l’ID Apple que vous utilisez a une appartenance active au programme pour développeurs Apple. Pour utiliser un compte de développeur Apple payant, consultez le guide [d’approvisionnement gratuit pour les applications Xamarin. iOS](~/ios/get-started/installation/device-provisioning/free-provisioning.md) .

- Si la tentative de création d’un nouveau certificat de signature échoue avec l’erreur `You have reached the limit for certificates of this type`, le nombre maximal de certificats autorisés a été généré. Pour résoudre ce problème, accédez au [Centre de développement Apple](https://developer.apple.com/account/ios/certificate/distribution) et Révoquez l’un des certificats de production.

- Si vous rencontrez des problèmes de journalisation dans votre compte sur Visual Studio pour Mac, il est possible d’ouvrir l’application Trousseau et, sous **catégorie** , sélectionner des **mots de passe**. Recherchez `deliver.` et supprimez toutes les entrées trouvées.

## <a name="known-issues"></a>Problèmes connus

- Par défaut, les profils d’approvisionnement de distribution ciblent l’App Store. Vous devez créer les profils internes ou ad hoc manuellement.
