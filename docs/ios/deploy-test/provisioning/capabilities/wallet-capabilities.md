---
title: Fonctionnalités Wallet dans Xamarin.iOS
description: L’ajout de fonctionnalités à une application nécessite souvent une configuration supplémentaire du provisionnement. Ce guide explique la configuration requise pour les fonctionnalités Wallet.
ms.prod: xamarin
ms.assetid: BD9475E6-F586-488C-93D4-8A2A1629B99B
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/15/2017
ms.openlocfilehash: 1844136bb62930341cf00ec871fdd2ff8ff8fe1f
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70762740"
---
# <a name="wallet-capabilities-in-xamarinios"></a>Fonctionnalités Wallet dans Xamarin.iOS

_L’ajout de fonctionnalités à une application nécessite souvent une configuration plus poussée de l’approvisionnement. Ce guide explique la configuration requise pour les fonctionnalités Wallet._

Wallet est une application qui stocke et affiche les codes-barres et autres contenus permettant aux utilisateurs de présenter leurs billets, cartes d’embarquement et coupons directement à partir de leur appareil. Ces informations sont stockées dans un _passe_. Par exemple, une carte d’embarquement ou un billet constitue chacun un passe. 

Les développeurs peuvent utiliser Wallet de diverses façons :

* Pour créer un passe, une application n’a pas besoin d’être générée. Un Passfile est une archive zippée qui contient des fichiers JSON et les fichiers de métadonnées facultatifs. Au préalable, un [ID de type de passe](~/ios/platform/passkit.md) et un [certificat de passe ](~/ios/platform/passkit.md) doivent avoir été créés. Ces informations sont ensuite déclarées dans un fichier JSON. Pour plus d’informations sur le provisionnement d’un Passfile, consultez le guide [Présentation de PassKit](~/ios/platform/passkit.md).

* Des applications compagnes sont écrites pour distribuer les passes. Elles permettent également de créer, modifier et mettre à jour les passes, puis de les ajouter à l’application Wallet. Une application cinéma est un bon exemple de ce type d’application : quand un utilisateur achète un billet via l’application, ce billet est ensuite directement ajouté au service Wallet à partir de l’application. Pour utiliser une application compagne, votre profil de provisionnement doit contenir un ID d’application avec les fonctionnalités Wallet, comme cela est fait dans les étapes ci-dessous. Votre application doit également inclure les droits requis.

* Les applications Conduit sont des applications qui ne manipulent pas de passes directement. Leur interaction avec les passes consiste principalement à les recevoir et à permettre à l’utilisateur de les ajouter au Wallet. Ces applications n’ont pas besoin de provisionnement ou droits particuliers, mais elles utilisent certaines méthodes du framework PassKit.

## <a name="developer-center"></a>Developer Center

Pour créer un profil de provisionnement pour une utilisation avec Wallet, effectuez les étapes suivantes :

1. Accédez à la section [Certificates, Identifiers & Profiles](https://developer.apple.com/account/ios/certificate/) dans le portail des développeurs d’Apple.
2. Sous **Identifiers**, sélectionnez **App IDs** : 
    
    ![Sélection de l’ID d’application](wallet-capabilities-images/image17.png)

3. Cliquez sur l’icône **+** dans le coin supérieur droit de la page.
4. Inscrivez un nouvel ID d’application en lui attribuant un **nom** et un identificateur de bundle. (Notez que cet identificateur de bundle doit correspondre à l’ID de bundle défini dans votre projet) :
   
    ![Ajouter les détails de l’ID d’application](wallet-capabilities-images/image18.png)

5. Sélectionnez le service d’application **Wallet** dans la liste des services :
    
    ![Écran de sélection du service](wallet-capabilities-images/image19.png)

6. Appuyez sur **Continue**, puis sur **Register** pour créer l’ID d’application.

Si nécessaire, vous pouvez modifier des ID d’application existants pour ajouter la fonctionnalité Wallet.

Cet ID d’application peut maintenant être utilisé pour générer ou regénérer un nouveau profil de provisionnement, comme cela est décrit dans le guide [Utilisation des fonctionnalités](~/ios/deploy-test/provisioning/capabilities/index.md) :

![Utilisation du nouvel ID d’application pour créer le profil de provisionnement](wallet-capabilities-images/image20.png)

Pour plus d’informations sur l’utilisation de Wallet, consultez le guide suivant :

* [Présentation de PassKit](~/ios/platform/passkit.md)

## <a name="next-steps"></a>Étapes suivantes

S’il y a lieu, effectuez les étapes supplémentaires décrites ci-après :

* Utilisez l’espace de noms de framework dans votre application.
* Ajoutez les droits nécessaires à votre application. Pour obtenir des informations détaillées sur les droits nécessaires et leur ajout, consultez le guide [Utilisation de droits](~/ios/deploy-test/provisioning/entitlements.md).
* Assurez-vous que, dans la section **Signature du bundle iOS** de l’application, le paramètre **Droits personnalisés** est défini sur **Entitlements.plist**. Ce n’est _pas_ le paramètre par défaut pour les builds Debug et iOS Simulator.

Si vous rencontrez des problèmes avec les services d’application, consultez la section [Dépannage](~/ios/deploy-test/provisioning/capabilities/index.md) du guide principal.
