---
title: Fonctionnalités Apple Pay dans Xamarin.iOS
description: L’ajout de fonctionnalités à une application nécessite souvent une configuration supplémentaire du provisionnement. Ce guide explique la configuration requise pour les fonctionnalités Apple Pay.
ms.prod: xamarin
ms.assetid: 735CC916-16A4-471B-87F7-0535E24288D7
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/15/2017
ms.openlocfilehash: 856ed31cd2ee8fdc657cd1437f21052e1d7a79f2
ms.sourcegitcommit: 3d21bb1a6d9b78b65aa49917b545c39d44aa3e3c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70065495"
---
# <a name="apple-pay-capabilities-in-xamarinios"></a>Fonctionnalités Apple Pay dans Xamarin.iOS

_L’ajout de fonctionnalités à une application nécessite souvent une configuration plus poussée de l’approvisionnement. Ce guide explique la configuration requise pour les fonctionnalités Apple Pay._

Apple Pay permet aux utilisateurs de payer des marchandises physiques à partir de leur appareil iOS. Cette section décrit comment créer tous les composants nécessaires pour Apple Pay dans le developer center d’Apple.

Le provisionnement d’une nouvelle application par le biais du developer center s’effectue en trois étapes :

1. Créez un ID de marchand.
2. Créez un ID d’application avec la fonctionnalité Apple Pay et ajoutez-lui le marchand.
3. Générez un certificat pour l’ID de marchand.

Pour créer les éléments nécessaires, suivez les procédures pas à pas ci-dessous :

<a name="merchantid" />

## <a name="create-merchant-id"></a>Créer un ID de marchand

Un ID de marchand permet à Apple Pay de savoir que vous acceptez les paiements. Cet ID est passé à la méthode `PaymentRequest` de PassKit et est utilisé dans le droit Apple Pay :

1. Accédez au [developer center d’Apple](https://developer.apple.com/account/) et sélectionnez la section « Certificates, Identifiers & Profiles » : 
 
    ![Sélection de l’ID de marchand dans le developer center](apple-pay-capabilities-images/image57.png)

2. Sous **Identifiers**, sélectionnez **Merchant IDs**, puis sélectionnez le bouton **+** pour créer un ID de marchand :  

3. Dans le formulaire illustré ci-dessous, entrez une description et un identificateur pour le nouvel ID de marchand. La description vous permet d’identifier plus facilement cet ID ; elle peut être modifiée ultérieurement. L’identificateur doit être unique et commencer par la chaîne `merchant`. Apple recommande d’utiliser un identificateur au format `merchant.com.[Your-App-Name]` :
   
    ![Détails du nouvel ID de marchand](apple-pay-capabilities-images/image58.png)

4. Vérifiez les informations, puis **inscrivez** votre ID : 
    
    ![Confirmation de l’ID de marchand](apple-pay-capabilities-images/image59.png)

<a name="appid" />

## <a name="create-an-app-id-with-the-apple-pay-capability-that-includes-the-merchant-id"></a>Créer un ID d’application avec la fonctionnalité Apple Pay contenant l’ID de marchand

1. Dans le [developer center](https://developer.apple.com/account/), cliquez sur **App IDs** sous **Identifiers** : 
    
    ![Sélectionner l’ID d’application dans le developer center](apple-pay-capabilities-images/image6.png)

2. Sélectionnez le bouton **+** pour ajouter un nouvel ID d’application : 
   
    ![Bouton d’ajout d’un nouvel ID d’application](apple-pay-capabilities-images/image27.png)

3. Entrez un nom pour l’ID d’application et attribuez-lui un ID d’application explicite :    
   
    ![Détails de l’ID d’application](apple-pay-capabilities-images/image35.png)

4. Sous App Services, sélectionnez Apple Pay :    
  
    ![Apple Pay sous App Services](apple-pay-capabilities-images/image36.png)

5. Sélectionnez **Continue**, puis **Register**. Notez que, dans l’écran de confirmation, l’état actuel d’Apple Pay est Configurable (avec un symbole jaune) : 
   
    ![Écran de confirmation pour Apple Pay](apple-pay-capabilities-images/image37.png)

6. Revenez à la liste des ID d’application et sélectionnez celui que vous venez de créer :  
   
    ![Modifier l’ID d’application](apple-pay-capabilities-images/image38.png)

7. Faites défiler cette section développée jusqu’en bas et cliquez sur **Edit**.
8. Faites défiler la liste pour afficher Apple Pay et cliquez sur le bouton **Edit** :  
    
    ![Détails de la modification de l’ID d’application pour Apple Pay](apple-pay-capabilities-images/image39.png)

9. Sélectionnez l’ID de marchand à utiliser avec cet ID d’application, puis cliquez sur **Continue** :  
    
    ![Sélectionner l’ID de marchand à utiliser pour l’ID d’application](apple-pay-capabilities-images/image40.png)

10. Confirmez les attributions d’ID de marchand, puis appuyez sur **Assign** :  
    
    ![Écran de confirmation](apple-pay-capabilities-images/image41.png)

Cet ID d’application peut maintenant être utilisé pour générer ou regénérer un nouveau profil de provisionnement, comme cela est décrit dans le guide [Utilisation des fonctionnalités](~/ios/deploy-test/provisioning/capabilities/index.md). 

<a name="certificate" />

## <a name="create-a-certificate-for-your-merchant-id"></a>Créer un certificat pour l’ID de marchand

Apple a besoin d’un certificat pour chiffrer les données sensibles associées à la transaction. Chaque nouvel ID de marchand doit avoir son propre certificat. 

Pour créer un certificat, suivez les étapes ci-dessous :

1. Sélectionnez l’ID de marchand créé précédemment et appuyez sur **Edit** : 
    
    ![Boîte de dialogue de modification de l’ID de marchand](apple-pay-capabilities-images/image42.png)

2. Dans l’écran iOS Merchant ID Settings, cliquez sur **Create Certificate** : 
   
    ![Créer un certificat pour le traitement des paiements](apple-pay-capabilities-images/image43.png)

3. Répondez à la question suivante : 

    ![Indiquer si les paiements seront traités exclusivement en Chine](apple-pay-capabilities-images/image44.png)

4. Vous êtes maintenant invité à créer une _demande de signature de certificat_ (CSR) : 

    ![Création d’une demande de signature de certificat (CSR)](apple-pay-capabilities-images/image45.png)
    
    > [!IMPORTANT]
    > Si vous utilisez un fournisseur de service de paiement pour Apple Pay, tel que JudoPay ou Stripe, vous pourrez obtenir une RSC correctement mise en forme à utiliser à cette étape. Pour plus d’informations sur l’obtention d’une CSR, consultez les sites de [JudoPay](https://www.judopay.com/docs/version-52/apple-pay/getting-started/#create-an-apple-pay-certificate) et [Stripe](https://stripe.com/docs/apple-pay/apps#csr). Pour créer votre propre CSR, suivez les étapes 5 à 8 ci-dessous. Une fois que vous avez une CSR, passez à l’étape 9.

5. Ouvrez l’application Keychain Access et accédez à **Keychain Access > Certificate Assistant > Request a Certificate from a Certificate Authority** : 

     ![Créer une CSR à l’aide de Keychain sur un Mac](apple-pay-capabilities-images/image46.png)

6. Entrez votre adresse e-mail, entrez un nom de clé privée, laissez le champ « CA Email Address » vide, sélectionnez l’option **Save to Disk**, puis sélectionnez **Let me specify key pair information** :

     ![Boîte de dialogue d’informations sur le certificat](apple-pay-capabilities-images/image47.png)

7. Enregistrez la CSR à un emplacement approprié : 

     ![Enregistrement de la CSR sur une machine locale](apple-pay-capabilities-images/image48.png)

8. Dans l’écran Key Pair information, définissez **Key Size** sur **256 bits** et **Algorithm** sur **ECC**, puis cliquez sur **Continue** :

     ![Boîte de dialogue d’entrée des informations sur la paire de clés](apple-pay-capabilities-images/image49.png)

9. Dans le developer center, cliquez sur **Continue** pour charger la CSR : 

     ![Préparation du chargement de la CSR sur le developer center](apple-pay-capabilities-images/image50.png)

10. Cliquez sur **Choisir un fichier…** pour sélectionner la CSR, puis appuyez sur **Continue** pour la charger sur le portail des développeurs : 

     ![Chargement de la CSR sur le developer center](apple-pay-capabilities-images/image51.png)

11. Une fois que le certificat a été généré, téléchargez-le, puis double-cliquez dessus pour l’installer dans votre trousseau.

Pour plus d’informations sur l’utilisation d’Apple Pay, consultez le guide suivant :

* [Présentation d’Apple Pay](~/ios/platform/apple-pay.md)

## <a name="next-steps"></a>Étapes suivantes
 
S’il y a lieu, effectuez les étapes supplémentaires décrites ci-après :

* Utilisez l’espace de noms de framework dans votre application.
* Ajoutez les droits nécessaires à votre application. Pour obtenir des informations détaillées sur les droits nécessaires et leur ajout, consultez le guide [Utilisation de droits](~/ios/deploy-test/provisioning/entitlements.md).
* Dans la section  **Signature du bundle iOS** de l’application, vérifiez que le paramètre  **Droits personnalisés** a la valeur **Entitlements.plist**. Il ne s’agit  _pas_ du paramètre par défaut pour les builds Debug et iOS Simulator.

Si vous rencontrez des problèmes avec les services d’application, consultez la section [Dépannage](~/ios/deploy-test/provisioning/capabilities/index.md) du guide principal.
