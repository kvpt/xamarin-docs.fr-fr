---
title: Abonnements et les rapports dans Xamarin.iOS
description: Ce document décrit non renouvellement des abonnements, les abonnements gratuit, abonnements renouvelables automatiquement et à l’aide d’iTunes Connect pour créer des rapports sur ces éléments.
ms.prod: xamarin
ms.assetid: 27EE4234-07F5-D2CD-DC1C-86E27C20141E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 44dd2b2eea31f47ed53d8ef25ff7b0667286179d
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67674557"
---
# <a name="subscriptions-and-reporting-in-xamarinios"></a>Abonnements et les rapports dans Xamarin.iOS

## <a name="about-non-renewing-subscriptions"></a>Sur le Non-renouvellement des abonnements

Non-renouvellement des abonnements sont conçues pour les produits qui représentent la vente d’un service avec une restriction de temps telles que (une semaine pour une application de navigation) ou limitées dans le temps accès à une archive de données.   
   
Principales différences entre les abonnements non renouvellement et d’autres types de produit :

-  La définition de produit dans iTunes Connect n’inclut pas le terme. Le code d’application doit être en mesure de déduire la période de validité de l’ID de produit. 
-  Ils peuvent être achetés plusieurs fois (par exemple, un produit consommable). Les applications sont requises pour gérer l’abonnement terme/expiration et le renouvellement et empêcher l’utilisateur de l’achat d’abonnements qui se chevauchent. 
-  Les achats ne sont pas pris en charge par la fonction StoreKit restaurer. Si l’abonnement doit être disponible sur les appareils de tous les l’utilisateur, l’application aura concevoir et implémenter cette fonctionnalité en conjonction avec un serveur distant. Les applications sont également responsables de sauvegarde de l’état de l’abonnement pour le cas quand un appareil est sauvegardé puis restauré à partir de-sauvegarde. 
-  Vue d’ensemble de l’implémentation
-  Abonnements non-renouvellement doit normalement être implémentées à l’aide du flux de travail assurée par le serveur et gérés comme des produits consommables. 


## <a name="about-free-subscriptions"></a>À propos des abonnements gratuits

Abonnements gratuits permettent aux développeurs de placer le contenu libre dans les applications kiosque (ils ne peuvent pas être utilisés dans les applications non-Newsstand). Une fois un abonnement gratuit est démarré, il sera disponible sur tous les appareils de l’utilisateur. Abonnements gratuits ne jamais expirent ; qu’elles se terminent uniquement lorsque l’application est désinstallée.

### <a name="implementation-overview"></a>Vue d’ensemble de l’implémentation

Abonnements gratuits se comportent beaucoup comme les abonnements renouvelables automatiquement. L’application doit disposer d’un produit d’abonnement gratuit disponible pour « purchase » dans iTunes Connect. Lors de l’achat par l’utilisateur, l’achat d’abonnement gratuit doit être validé comme un produit de l’abonnement automatique renouvelable. Transactions d’abonnement gratuit peuvent être restaurées.


## <a name="about-auto-renewable-subscriptions"></a>À propos des abonnements renouvelables automatiquement

Abonnements renouvelables automatiquement sont principalement utilisés dans les applications kiosque. Elles représentent un produit qui accorde l’accès au contenu dynamique pour une période donnée, qui est configuré dans iTunes Connect (définir des périodes allant de 7 jours à 1 an). Abonnements renouvellent automatiquement, l’ID Apple aux utilisateurs de chargement à la fin de chaque période d’abonnement, sauf si l’utilisateur choisit à la sortie. Ce type de produit fonctionne bien pour les abonnements magazine ou news, où l’utilisateur peut alors accéder à chaque problème publiée alors que leur abonnement est valide.

### <a name="implementation-overview"></a>Vue d’ensemble de l’implémentation

Abonnements renouvelable automatique doit être implémentées à l’aide du flux de travail Server-Delivered produits (reportez-vous à la *vérification de la réception et les produits Server-Delivered* section).

#### <a name="shared-secret"></a>Secret partagé

Le Secret partagé de In-App Purchase doit être utilisé dans la requête JSON lors de la vérification des abonnements renouvelables automatiquement sur votre serveur. Le secret partagé est créé/accessibles via iTunes Connect.

À partir de la page d’accueil de connexion, sélectionnez iTunes **mes applications**:   
   
 [![](subscriptions-and-reporting-images/image2.png "Sélectionner Mes apps")](subscriptions-and-reporting-images/image2.png#lightbox)  
 
Sélectionnez une application et cliquez sur le **achats dans l’application** onglet :

[![](subscriptions-and-reporting-images/image6.png "Cliquez sur l’onglet Achats In-App")](subscriptions-and-reporting-images/image6.png#lightbox)

En bas de la page, sélectionnez **vue ou générer un secret partagé**:
   
 [![](subscriptions-and-reporting-images/image40.png "Sélectionnez la vue ou de générer un secret partagé")](subscriptions-and-reporting-images/image40.png#lightbox)

 [![](subscriptions-and-reporting-images/image41.png "Générer un secret partagé")](subscriptions-and-reporting-images/image41.png#lightbox)   
   
   
   
 Pour utiliser le secret partagé, vous devez l’inclure dans la charge utile JSON qui est envoyée aux serveurs de d’Apple lors de la validation d’un bon d’achat dans l’application pour un abonnement renouvelables automatiquement, comme suit :

```csharp
{
   "receipt-data" : "(receipt bytes here)",
   "password"     : "(shared secret bytes here)"
}
```

Champ d’état de la réponse sera égal à zéro si l’achat est valide, comme avec d’autres types de produit.

#### <a name="downloading-items-after-the-initial-subscription-term"></a>Téléchargement d’éléments après la période d’abonnement initiale

Dans le cadre de la fourniture de produits d’abonnement, le code doit vérifier fréquemment de la dernière réception connue sur les serveurs d’Apple. Si un abonnement est automatiquement renouvelé depuis la dernière vérification, la réponse JSON contient des champs supplémentaires qui notifient l’application de la transaction s’est produite (ce qui est souhaitable d’étendre la validité des abonnements). Contient la réponse JSON :

```csharp
{
   "status" : 0,
   "receipt" : { (receipt here) },
   "latest_receipt" : "(base-64 encoded receipt here)",
   "latest_receipt_info" : { (latest receipt info here) }
}
```

Si l’état est égal à zéro l’abonnement est toujours valide, puis les autres champs contiennent des données valides. Si l’état est 21006 l’abonnement a expiré. Consultez le [vérification d’un bon abonnement renouvelables automatiquement](https://developer.apple.com/library/ios/releasenotes/General/ValidateAppStoreReceipt/Chapters/ValidateRemotely.html) documentation pour les autres codes d’erreur.

#### <a name="restoring-auto-renewable-subscriptions"></a>Restauration automatique renouvelable abonnements

Vous recevez plusieurs transactions : la transaction d’achat d’origine plus une transaction distincte pour chaque période de temps que l’abonnement a été renouvelé. Vous devez suivre les dates de début et les termes du contrat pour comprendre ce qui est la période de validité.   
   
   
   
 L’objet SKPaymentTransaction n’inclut pas la période d’abonnement : vous devez utiliser un ID de produit différent pour chaque terme et écrire du code qui peut extrapoler la période d’abonnement à partir de la date d’achat de la transaction.

#### <a name="testing-auto-renewal"></a>Test de renouvellement automatique

Pour le rendre plus facile de tester des abonnements, leurs durées sont compressées lors du test dans le bac à sable. 1 semaine abonnements renouvellement toutes les 3 minutes, toutes les heures de renouvellement des abonnements de 1 an. Les abonnements seront se renouvellent automatiquement un maximum de 6 fois lors du test dans le bac à sable.

## <a name="reporting"></a>Rapports

iTunes Connect ( [itunesconnect.apple.com](http://itunesconnect.apple.com)) fournit :   
   
 **Ventes et les tendances** – affiche des détails de l’application des téléchargements, mises à jour et les achats dans l’application.   
   
 **Paiements et des rapports financiers** – décrit en détail les revenus gagnés par vos applications, ainsi que les paiements de liste qui ont été apportées à vous et combien vous sont dus.

Vous trouverez ci-dessous un exemple de rapport de ventes et les tendances :   

 [![](subscriptions-and-reporting-images/image42.png "Un exemple de rapport de ventes et les tendances")](subscriptions-and-reporting-images/image42.png#lightbox)   
   
 Il existe également un [ **Mobile de se connecter de CTI**application iOS (lien iTunes)](http://itunes.apple.com/us/app/itunes-connect-mobile/id376771144?mt=8).
captures d’écran de l’iPhone pour les statistiques disponibles sont affichés ici :   
   
 [![](subscriptions-and-reporting-images/image43.png "captures d’écran de l’iPhone pour les statistiques disponibles")](subscriptions-and-reporting-images/image43.png#lightbox)
