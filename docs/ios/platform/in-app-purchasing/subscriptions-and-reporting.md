---
title: Abonnements et rapports dans Xamarin. iOS
description: Ce document décrit les abonnements qui ne sont pas renouvelés, les abonnements gratuits, les abonnements auto-renouvelés et l’utilisation d’iTunes Connect pour signaler ces éléments.
ms.prod: xamarin
ms.assetid: 27EE4234-07F5-D2CD-DC1C-86E27C20141E
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/18/2017
ms.openlocfilehash: 7f455d2164573d68db0a9c764f2b2cef5cc6d739
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70284041"
---
# <a name="subscriptions-and-reporting-in-xamarinios"></a>Abonnements et rapports dans Xamarin. iOS

## <a name="about-non-renewing-subscriptions"></a>À propos des abonnements qui ne sont pas renouvelés

Les abonnements qui ne sont pas renouvelés sont destinés aux produits qui représentent la vente d’un service avec une restriction de temps (par exemple, un accès d’une semaine à une application de navigation ou un accès limité dans le temps à une archive de données).   
   
Principales différences entre les abonnements qui ne sont pas renouvelés et d’autres types de produits :

- La définition du produit dans iTunes Connect n’inclut pas le terme. Le code d’application doit être en mesure de déduire la période de validité de l’ID de produit. 
- Ils peuvent être achetés plusieurs fois (comme un produit consommable). Les applications sont requises pour gérer la durée/l’expiration de l’abonnement et le renouvellement, et empêcher l’utilisateur d’acheter des abonnements qui se chevauchent. 
- Les achats ne sont pas pris en charge par la fonction de restauration StoreKit. Si l’abonnement doit être disponible sur tous les appareils d’un utilisateur, l’application doit concevoir et implémenter cette fonctionnalité conjointement à un serveur distant. Les applications sont également responsables de la sauvegarde de l’état de l’abonnement lorsqu’un appareil est sauvegardé, puis restauré à partir de la sauvegarde. 
- Vue d’ensemble de l’implémentation
- Les abonnements qui ne sont pas renouvelés doivent normalement être implémentés à l’aide du flux de travail fourni par le serveur et de produits consommables managés. 


## <a name="about-free-subscriptions"></a>À propos des abonnements gratuits

Les abonnements gratuits permettent aux développeurs de placer du contenu gratuit dans les applications Newsstand (ils ne peuvent pas être utilisés dans des applications non-Newsstand). Une fois qu’un abonnement gratuit est démarré, il est disponible sur tous les appareils de l’utilisateur. Les abonnements gratuits n’expirent jamais ; ils se terminent uniquement lorsque l’application est désinstallée.

### <a name="implementation-overview"></a>Vue d’ensemble de l’implémentation

Les abonnements gratuits se comportent comme des abonnements renouvelés automatiquement. L’application doit disposer d’un produit d’abonnement gratuit disponible pour l’achat dans iTunes Connect. Une fois acheté par l’utilisateur, l’achat d’abonnement gratuit doit être validé comme un produit d’abonnement auto-renouvelable. Les transactions d’abonnement gratuites peuvent être restaurées.


## <a name="about-auto-renewable-subscriptions"></a>À propos des abonnements renouvelés automatiquement

Les abonnements automatiquement renouvelés sont principalement utilisés dans les applications Newsstand. Ils représentent un produit qui accorde à l’utilisateur l’accès au contenu dynamique pendant une période donnée, qui est configurée dans iTunes Connect (définir des périodes allant de 7 jours à 1 an). Les abonnements sont renouvelés automatiquement, en facturant l’ID Apple des utilisateurs à la fin de chaque période d’abonnement, à moins que l’utilisateur ne le fasse. Ce type de produit fonctionne bien pour les abonnements au magazine ou aux actualités, où l’utilisateur a accès à chaque problème publié pendant que son abonnement est valide.

### <a name="implementation-overview"></a>Vue d’ensemble de l’implémentation

Les abonnements renouvelés automatiquement doivent être implémentés à l’aide du flux de travail des produits fournis par le serveur (reportez-vous à la section *vérification du reçu et produits fournis par le serveur* ).

#### <a name="shared-secret"></a>Secret partagé

Le secret partagé de l’achat dans l’application doit être utilisé dans la demande JSON lors de la vérification des abonnements renouvelés automatiquement sur votre serveur. Le secret partagé est créé/accessible via iTunes Connect.

Dans la page d’hébergement iTunes Connect, sélectionnez **mes applications**:   
   
 [![](subscriptions-and-reporting-images/image2.png "Sélectionner Mes apps")](subscriptions-and-reporting-images/image2.png#lightbox)  
 
Sélectionnez une application, puis cliquez sur l’onglet **achats dans l’application** :

[![](subscriptions-and-reporting-images/image6.png "Cliquer sur l’onglet achats dans l’application")](subscriptions-and-reporting-images/image6.png#lightbox)

En bas de la page, sélectionnez **afficher ou générer un secret partagé**:
   
 [![](subscriptions-and-reporting-images/image40.png "Sélectionnez Afficher ou générer un secret partagé")](subscriptions-and-reporting-images/image40.png#lightbox)

 [![](subscriptions-and-reporting-images/image41.png "Générer un secret partagé")](subscriptions-and-reporting-images/image41.png#lightbox)   
   
   
   
 Pour utiliser le secret partagé, incluez-le dans la charge utile JSON envoyée aux serveurs d’Apple lors de la validation d’un accusé de réception d’achat dans l’application pour un abonnement renouvelable automatiquement, comme suit :

```csharp
{
   "receipt-data" : "(receipt bytes here)",
   "password"     : "(shared secret bytes here)"
}
```

Le champ État de la réponse est égal à zéro si l’achat est valide, comme avec d’autres types de produits.

#### <a name="downloading-items-after-the-initial-subscription-term"></a>Téléchargement des éléments après la période d’abonnement initiale

Dans le cadre de la livraison de produits d’abonnement, le code doit fréquemment vérifier la dernière réception connue sur les serveurs Apple. Si un abonnement a renouvelé automatiquement depuis la dernière vérification, la réponse JSON contient des champs supplémentaires qui informent l’application de la transaction qui s’est produite (qui doit étendre la validité des abonnements). La réponse JSON contient les éléments suivants :

```csharp
{
   "status" : 0,
   "receipt" : { (receipt here) },
   "latest_receipt" : "(base-64 encoded receipt here)",
   "latest_receipt_info" : { (latest receipt info here) }
}
```

Si l’État est égal à zéro, l’abonnement est toujours valide et les autres champs contiennent des données valides. Si l’État est 21006, cela signifie que l’abonnement a expiré. Consultez la documentation [vérification d’un accusé de réception d’abonnement auto-renouvelable](https://developer.apple.com/library/ios/releasenotes/General/ValidateAppStoreReceipt/Chapters/ValidateRemotely.html) pour d’autres codes d’erreur.

#### <a name="restoring-auto-renewable-subscriptions"></a>Restauration des abonnements auto-renouvelés

Vous bénéficiez de plusieurs transactions : la transaction d’achat d’origine et une transaction distincte pour chaque période de temps pendant laquelle l’abonnement a été renouvelé. Vous devez suivre les dates et les termes de début pour comprendre la période de validité.   
   
   
   
 L’objet SKPaymentTransaction n’inclut pas le terme d’abonnement : vous devez utiliser un ID de produit différent pour chaque terme et écrire du code qui peut extrapoler la période d’abonnement à partir de la date d’achat de la transaction.

#### <a name="testing-auto-renewal"></a>Test de renouvellement automatique

Pour faciliter le test des abonnements, leurs durées sont compressées lors des tests dans le bac à sable (sandbox). les abonnements d’une semaine sont renouvelés toutes les 3 minutes, les abonnements de 1 an par heure. Les abonnements sont renouvelés automatiquement un maximum de 6 fois lors du test dans le bac à sable (sandbox).

## <a name="reporting"></a>Reporting

iTunes Connect ( [iTunesConnect.Apple.com](http://itunesconnect.apple.com)) fournit les éléments suivants :   
   
 **Ventes et tendances** : affiche les détails des téléchargements d’applications, des mises à jour et des achats dans l’application.   
   
 **Paiements et rapports financiers** : détaillent le revenu obtenu par vos applications, ainsi que la liste des paiements qui vous ont été effectués et de la somme de vos dettes.

Vous trouverez ci-dessous un exemple de rapport sur les ventes et les tendances :   

 [![](subscriptions-and-reporting-images/image42.png "Exemple de rapport de ventes et de tendances")](subscriptions-and-reporting-images/image42.png#lightbox)   
   
 Il y a également une [application mobile iOS de l' **ITC Connect**(lien iTunes)](http://itunes.apple.com/us/app/itunes-connect-mobile/id376771144?mt=8).
les captures d’écran iPhone pour certaines des statistiques disponibles sont indiquées ici :   
   
 [![](subscriptions-and-reporting-images/image43.png "captures d’écran iPhone pour certaines des statistiques disponibles")](subscriptions-and-reporting-images/image43.png#lightbox)
