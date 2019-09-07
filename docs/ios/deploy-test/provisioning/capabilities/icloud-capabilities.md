---
title: Fonctionnalités iCloud dans Xamarin.iOS
description: L’ajout de fonctionnalités à une application nécessite souvent une configuration supplémentaire du provisionnement. Ce guide explique la configuration requise pour les fonctionnalités iCloud.
ms.prod: xamarin
ms.assetid: 3CBAC982-D8DE-48DD-97CD-32B551D9DB85
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/15/2017
ms.openlocfilehash: 3fd3ec0207ac3ae1e5fcaa7d11d91247cd73666d
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70762904"
---
# <a name="icloud-capabilities-in-xamarinios"></a>Fonctionnalités iCloud dans Xamarin.iOS

_L’ajout de fonctionnalités à une application nécessite souvent une configuration plus poussée de l’approvisionnement. Ce guide explique la configuration requise pour les fonctionnalités iCloud._

iCloud offre aux utilisateurs d’appareil iOS un moyen simple et pratique de stocker leur contenu et de le partager entre plusieurs appareils. Les développeurs peuvent utiliser iCloud de quatre façons différentes pour fournir un moyen de stockage à leurs utilisateurs : stockage clé-valeur, stockage UIDocument, CoreData et l’utilisation de CloudKit directement pour assurer le stockage de fichiers et répertoires individuels. Pour plus d’informations, consultez le guide [Présentation d’iCloud](~/ios/data-cloud/introduction-to-icloud.md).

L’ajout d’iCloud à une application est un peu plus difficile que d’autres services d’applications à cause des _conteneurs_. Les conteneurs utilisés dans iCloud servent à stocker les informations d’une application et permettent la séparation de toutes les informations contenues dans un compte iCloud unique, à l’image du sandboxing sur l’appareil iOS d’un utilisateur. Pour plus d’informations sur les conteneurs, consultez le guide [Présentation de CloudKit](~/ios/data-cloud/intro-to-cloudkit.md).

> [!IMPORTANT]
> Apple [fournit des outils](https://developer.apple.com/support/allowing-users-to-manage-data/) pour aider les développeurs à gérer correctement le Règlement général sur la protection des données (RGPD) de l’Union européenne.

<a name="icloud-developer-center" />

## <a name="developer-center"></a>Developer Center

Le provisionnement d’une nouvelle application par le biais du developer center s’effectue en trois étapes :

1. Créez un conteneur.
2. Créez un ID d’application avec la fonctionnalité iCloud et ajoutez-lui le conteneur.
3. Créez un profil de provisionnement qui contient cet ID d’application.

Pour créer les éléments nécessaires, suivez les procédures pas à pas ci-dessous :

1. Accédez au [developer center d’Apple](https://developer.apple.com/account/) et sélectionnez la section « Certificates, Identifiers & Profiles » : 
    
     ![Page principale du developer center d’Apple](icloud-capabilities-images/image22.png)

2. Sous **Identifiers**, sélectionnez **iCloud Containers**, puis sélectionnez le bouton **+** pour créer un conteneur :  
    
    ![Écran des conteneurs iCloud](icloud-capabilities-images/image23.png)

3. Entrez une **description** et un **identificateur** unique pour le conteneur iCloud : 
    
    ![Écran d’inscription d’un conteneur iCloud](icloud-capabilities-images/image24.png)

4. Appuyez sur **Continue**, vérifiez que les informations sont correctes, puis appuyez sur **Register** pour créer le conteneur iCloud :  
    
    ![Écran d’inscription d’un conteneur iCloud](icloud-capabilities-images/image25.png)

Pour créer un ID d’application et lui ajouter un conteneur, effectuez les étapes suivantes :

1. Dans le [developer center](https://developer.apple.com/account/), cliquez sur **App IDs** sous **Identifiers** : 
    
    ![Section Identifiers dans le developer center](icloud-capabilities-images/image26.png)

2. Sélectionnez le bouton **+** pour ajouter un nouvel ID d’application : 
    
    ![Bouton d’ajout d’un nouvel ID d’application](icloud-capabilities-images/image27.png)

3. Entrez un **nom** pour l’ID d’application et attribuez-lui un **ID d’application explicite** :
    
    ![Entrer les détails du nouvel ID d’application](icloud-capabilities-images/image28.png)

4. Sous **App Services**, sélectionnez **iCloud** et choisissez **Include CloudKit support** :
    
    ![Sélectionner iCloud dans les services d’application](icloud-capabilities-images/image29.png)

5. Sélectionnez **Continue**, puis **Register**. Notez que, dans l’écran de confirmation, l’état actuel d’iCloud est Configurable (avec un symbole jaune) :   
    
    ![Écran de confirmation](icloud-capabilities-images/image30.png)

6. Revenez à la liste des ID d’application et sélectionnez celui que vous venez de créer : 
    
    ![Écran de sélection de l’ID d’application](icloud-capabilities-images/image31.png)

7. Faites défiler cette section développée jusqu’en bas et cliquez sur **Edit** :
    
    ![Modifier l’ID d’application](icloud-capabilities-images/image32.png)

8. Faites défiler la liste pour afficher iCloud et cliquez sur le bouton **Edit** :  
    
    ![Modifier l’ID d’application d’iCloud](icloud-capabilities-images/image33.png)

9. Sélectionnez le conteneur à utiliser avec cet ID d’application :  
    
    ![Écran de sélection des conteneurs](icloud-capabilities-images/image34.png)

10. Confirmez les attributions de conteneur, puis appuyez sur **Assign**.

Cet ID d’application peut maintenant être utilisé pour générer ou regénérer un nouveau profil de provisionnement, comme décrit dans le guide [Utilisation des fonctionnalités](~/ios/deploy-test/provisioning/capabilities/index.md). 

Pour plus d’informations sur l’utilisation d’iCloud, consultez les guides suivants :

* [Présentation d’iCloud](~/ios/data-cloud/introduction-to-icloud.md)
* [Présentation de CloudKit](~/ios/data-cloud/intro-to-cloudkit.md)
* [Présentation de Document Picker](~/ios/platform/document-picker.md)

## <a name="next-steps"></a>Étapes suivantes

S’il y a lieu, effectuez les étapes supplémentaires décrites ci-après :

* Utilisez l’espace de noms de framework dans votre application.
* Ajoutez les droits nécessaires à votre application. Pour obtenir des informations détaillées sur les droits nécessaires et leur ajout, consultez le guide [Utilisation de droits](~/ios/deploy-test/provisioning/entitlements.md).
* Dans la section  **Signature du bundle iOS** de l’application, vérifiez que le paramètre  **Droits personnalisés** a la valeur **Entitlements.plist**. Il ne s’agit  _pas_ du paramètre par défaut pour les builds Debug et iOS Simulator.

Si vous rencontrez des problèmes avec les services d’application, consultez la section [Dépannage](~/ios/deploy-test/provisioning/capabilities/index.md) du guide principal.
