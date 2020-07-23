---
title: Transactions et vérification dans Xamarin. iOS
description: Ce document explique comment autoriser la restauration des achats antérieurs dans une application Xamarin. iOS. Il aborde également les méthodes de sécurisation des achats et des produits fournis par le serveur.
ms.prod: xamarin
ms.assetid: 84EDD2B9-3FAA-B3C7-F5E8-C1E5645B7C77
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 605f82c90f98bb4b50e5b630a53721d186ff35a1
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86935757"
---
# <a name="transactions-and-verification-in-xamarinios"></a>Transactions et vérification dans Xamarin. iOS

## <a name="restoring-past-transactions"></a>Restauration de transactions passées

Si votre application prend en charge les types de produits pouvant être restaurés, vous devez inclure des éléments d’interface utilisateur pour permettre aux utilisateurs de restaurer ces achats.
Cette fonctionnalité permet à un client d’ajouter le produit à des appareils supplémentaires ou de restaurer le produit sur le même appareil après avoir été réinitialisé ou supprimé et réinstallé l’application. Les types de produits suivants peuvent être restaurés :

- Produits non consommables
- Abonnements renouvelés automatiquement
- Abonnements gratuits

Le processus de restauration doit mettre à jour les enregistrements que vous conservez sur l’appareil pour exécuter vos produits. Le client peut choisir de restaurer à tout moment, sur l’un de ses appareils. Le processus de restauration envoie à nouveau toutes les transactions précédentes pour cet utilisateur. le code d’application doit ensuite déterminer l’action à entreprendre avec ces informations (par exemple, vérifier s’il existe déjà un enregistrement de cet achat sur l’appareil et, si ce n’est pas le cas, créer un enregistrement de l’achat et activer le produit pour l’utilisateur).

### <a name="implementing-restore"></a>Implémentation de la restauration

Le bouton **restauration** de l’interface utilisateur appelle la méthode suivante, qui déclenche RestoreCompletedTransactions sur le `SKPaymentQueue` .

```csharp
public void Restore()
{
   // theObserver will be notified of when the restored transactions start arriving <- AppStore
   SKPaymentQueue.DefaultQueue.RestoreCompletedTransactions();
}
```

StoreKit enverra la demande de restauration aux serveurs Apple de manière asynchrone.   
   
Étant donné que le `CustomPaymentObserver` est inscrit en tant qu’observateur de transaction, il reçoit des messages lorsque les serveurs d’Apple répondent. La réponse contiendra toutes les transactions que cet utilisateur a déjà effectuées dans cette application (sur tous ses appareils). Le code parcourt chaque transaction, détecte l’état restauré et appelle la `UpdatedTransactions` méthode pour le traiter comme indiqué ci-dessous :

```csharp
// called when the transaction status is updated
public override void UpdatedTransactions (SKPaymentQueue queue, SKPaymentTransaction[] transactions)
{
   foreach (SKPaymentTransaction transaction in transactions)
   {
       switch (transaction.TransactionState)
       {
       case SKPaymentTransactionState.Purchased:
          theManager.CompleteTransaction(transaction);
           break;
       case SKPaymentTransactionState.Failed:
          theManager.FailedTransaction(transaction);
           break;
       case SKPaymentTransactionState.Restored:
           theManager.RestoreTransaction(transaction);
           break;
default:
           break;
       }
   }
}
```

S’il n’existe aucun produit pouvant être restauré pour l’utilisateur, `UpdatedTransactions` n’est pas appelé.   
   
Le code le plus simple possible pour restaurer une transaction donnée dans l’exemple effectue les mêmes actions que lorsqu’un achat a lieu, sauf que la `OriginalTransaction` propriété est utilisée pour accéder à l’ID de produit :

```csharp
public void RestoreTransaction (SKPaymentTransaction transaction)
{
   // Restored Transactions always have an 'original transaction' attached
   var productId = transaction.OriginalTransaction.Payment.ProductIdentifier;
   // Register the purchase, so it is remembered for next time
   PhotoFilterManager.Purchase(productId); // it's as though it was purchased again
   FinishTransaction(transaction, true);
}
```

Une implémentation plus sophistiquée peut vérifier `transaction.OriginalTransaction` d’autres propriétés, telles que la date d’origine et le numéro de réception. Ces informations seront utiles pour certains types de produits (tels que les abonnements).

#### <a name="restore-completion"></a>Restauration automatique

Le `CustomPaymentObserver` a deux méthodes supplémentaires qui seront appelées par StoreKit lorsque le processus de restauration est terminé (avec succès ou avec un échec), comme indiqué ci-dessous :

```csharp
public override void PaymentQueueRestoreCompletedTransactionsFinished (SKPaymentQueue queue)
{
   Console.WriteLine(" ** RESTORE Finished ");
}
public override void RestoreCompletedTransactionsFailedWithError (SKPaymentQueue queue, NSError error)
{
   Console.WriteLine(" ** RESTORE FailedWithError " + error.LocalizedDescription);
}
```

Dans l’exemple, ces méthodes ne font rien, mais une application réelle peut choisir d’implémenter un message à l’utilisateur ou d’autres fonctionnalités.

## <a name="securing-purchases"></a>Sécurisation des achats

Les deux exemples de ce document utilisent `NSUserDefaults` pour effectuer le suivi des achats :   
   
 **Consommables** : le « solde » des achats de crédit est une `NSUserDefaults` valeur entière simple qui est incrémentée à chaque achat.   
   
 **Non-consommables** : chaque achat de filtre photo est stocké sous la forme d’une paire clé-valeur dans `NSUserDefaults` .

L’utilisation de `NSUserDefaults` permet de simplifier l’exemple de code, mais n’offre pas une solution très sécurisée, car il est possible pour les utilisateurs de l’esprit technique de mettre à jour les paramètres (en ignorant le mécanisme de paiement).   
   
Remarque : les applications réelles doivent adopter un mécanisme sécurisé pour stocker le contenu acheté qui n’est pas soumis aux falsifications de l’utilisateur. Cela peut impliquer le chiffrement et/ou d’autres techniques, y compris l’authentification du serveur distant.   
   
 Le mécanisme doit également être conçu pour tirer parti des fonctionnalités de sauvegarde et de récupération intégrées d’iOS, iTunes et iCloud. Cela garantit qu’une fois qu’un utilisateur a restauré une sauvegarde, ses achats précédents sont immédiatement disponibles.   
   
Reportez-vous au Guide de codage sécurisé d’Apple pour plus d’informations sur iOS.

## <a name="receipt-verification-and-server-delivered-products"></a>Vérification de réception et produits fournis par le serveur

Les exemples de ce document jusqu’à présent ont constitué la seule application qui communique directement avec les serveurs de l’App Store pour effectuer des transactions d’achat, qui déverrouillent des fonctionnalités ou des fonctionnalités déjà codées dans l’application.   
   
Apple offre un niveau supplémentaire de sécurité d’achat en permettant aux bons de commande d’être vérifiés indépendamment par un autre serveur, ce qui peut être utile pour valider une demande avant de fournir du contenu numérique dans le cadre d’un achat (tel qu’un livre numérique ou un magazine).   
   
 **Produits intégrés** : comme les exemples de ce document, le produit en cours d’achat existe en tant que fonctionnalité fournie avec l’application. Un achat dans l’application permet à l’utilisateur d’accéder aux fonctionnalités.
Les ID de produit sont codés en dur.   
   
 **Produits fournis par le serveur** : le produit se compose d’un contenu téléchargeable stocké sur un serveur distant jusqu’à ce qu’une transaction réussie provoque le téléchargement du contenu.
Il peut s’agir par exemple de livres ou de problèmes de magazines. Les ID de produit sont généralement issus d’un serveur externe (où le contenu du produit est également hébergé). Les applications doivent implémenter un moyen fiable d’enregistrer lorsqu’une transaction est terminée, de sorte que si le téléchargement du contenu échoue, elle peut être tentée à nouveau sans déroutant l’utilisateur.

### <a name="server-delivered-products"></a>Produits fournis par le serveur

Le contenu de certains produits, tels que des livres et des magazines (ou même un niveau de jeu), doit être téléchargé à partir d’un serveur distant au cours du processus d’achat. Cela signifie qu’un serveur supplémentaire est requis pour stocker et livrer le contenu du produit après son achat.

#### <a name="getting-prices-for-server-delivered-products"></a>Obtention de prix pour les produits fournis par le serveur

Étant donné que les produits sont remis à distance, il est également possible d’ajouter d’autres produits au fil du temps (sans mettre à jour le code de l’application), tels que l’ajout de livres ou de nouveaux problèmes à un magazine. Pour que l’application puisse découvrir ces produits de News et les afficher à l’utilisateur, le serveur supplémentaire doit stocker et fournir ces informations.   
   
[![Obtention de prix pour les produits fournis par le serveur](transactions-and-verification-images/image38.png)](transactions-and-verification-images/image38.png#lightbox)   
   
1. Les informations sur les produits doivent être stockées à plusieurs emplacements : sur votre serveur et dans iTunes Connect. En outre, chaque produit est associé à des fichiers de contenu. Ces fichiers seront remis après un achat réussi.   
   
2. Lorsque l’utilisateur souhaite acheter un produit, l’application doit déterminer les produits disponibles. Ces informations peuvent être mises en cache, mais doivent être fournies à partir d’un serveur distant où la liste principale des produits est stockée.   
   
3. Le serveur renvoie une liste d’ID de produit pour l’application à analyser.   
   
4. L’application détermine ensuite l’ID de produit à envoyer à StoreKit pour récupérer les tarifs et les descriptions.   
   
5. StoreKit envoie la liste des ID de produit aux serveurs Apple.   
   
6. Les serveurs iTunes répondent avec des informations de produit valides (description et prix actuel).   
   
7. Les informations sur le `SKProductsRequestDelegate` produit pour l’utilisateur sont transmises à l’application.

#### <a name="purchasing-server-delivered-products"></a>Achat de produits fournis par le serveur

Étant donné que le serveur distant requiert un moyen de valider qu’une demande de contenu est valide (c’est-à-dire qu’elle a été payée pour), les informations de réception sont transmises pour l’authentification. Le serveur distant transmet ces données à iTunes pour vérification et, en cas de réussite, intègre le contenu du produit dans la réponse à l’application.   
   
 [![Achat de produits fournis par le serveur](transactions-and-verification-images/image39.png)](transactions-and-verification-images/image39.png#lightbox)   
   
1. L’application ajoute un `SKPayment` à la file d’attente. Si nécessaire, l’utilisateur sera invité à entrer son ID Apple et a demandé à confirmer le paiement.   
   
2. StoreKit envoie la demande au serveur pour traitement.   
   
3. Une fois la transaction terminée, le serveur répond avec un accusé de réception de la transaction.   
   
4. La `SKPaymentTransactionObserver` sous-classe reçoit l’accusé de réception et le traite. Étant donné que le produit doit être téléchargé à partir d’un serveur, l’application envoie une demande réseau au serveur distant.   
   
5. La demande de téléchargement est accompagnée des données de réception afin que le serveur distant puisse vérifier qu’il est autorisé à accéder au contenu. Le client réseau de l’application attend une réponse à cette demande.   
   
6. Lorsque le serveur reçoit une requête de contenu, il analyse les données de réception et envoie une demande directement aux serveurs iTunes pour vérifier que l’accusé de réception est destiné à une transaction valide. Le serveur doit utiliser une certaine logique pour déterminer si la demande doit être envoyée à l’URL de production ou de bac à sable (sandbox). Apple suggère toujours d’utiliser l’URL de production et de basculer vers le bac à sable (sandbox) si votre état de réception 21007 (réception bac à sable envoyée au serveur de production). Pour plus d’informations, consultez le [Guide de programmation de validation de tickets](https://developer.apple.com/library/archive/releasenotes/General/ValidateAppStoreReceipt/Chapters/ValidateRemotely.html) d’Apple.
   
7. iTunes vérifie l’accusé de réception et retourne l’état zéro s’il est valide.   
   
8. Le serveur attend une réponse iTunes. S’il reçoit une réponse valide, le code doit localiser le fichier de contenu du produit associé à inclure dans la réponse à l’application.   
  
9. L’application reçoit et analyse la réponse, en enregistrant le contenu du produit dans le système de fichiers de l’appareil.   
   
10. L’application active le produit, puis appelle StoreKit `FinishTransaction` . L’application peut éventuellement afficher le contenu acheté (par exemple, afficher la première page d’un livre ou d’un numéro de magazine acheté).

Une autre implémentation pour les fichiers de contenu de très grande taille du produit pourrait impliquer simplement le stockage de la réception de la transaction à l’étape #9 afin que la transaction puisse être terminée rapidement et fournir une interface utilisateur permettant à l’utilisateur de télécharger le contenu réel du produit ultérieurement. La demande de téléchargement suivante peut renvoyer la réception stockée pour accéder au fichier de contenu du produit requis.

### <a name="writing-server-side-receipt-verification-code"></a>Écriture du code de vérification de l’accusé de réception côté serveur

La validation d’un accusé de réception dans le code côté serveur peut être effectuée à l’aide d’une simple requête/réponse HTTP POSTÉRIEURe qui englobe les étapes #5 par #8 dans le diagramme du flux de travail.   
   
Extrayez la `SKPaymentTansaction.TransactionReceipt` propriété dans l’application. Il s’agit des données qui doivent être envoyées à iTunes pour vérification (étape #5).

Coder en base64 les données de réception de la transaction (à l’étape #5 ou #6).

Créez une charge utile JSON simple comme suit :

```csharp
{
   "receipt-data" : "(base-64 encoded receipt here)"
}
```

HTTP PUBLIEz le JSON à [https://buy.itunes.apple.com/verifyReceipt](https://buy.itunes.apple.com/verifyReceipt) pour production ou [https://sandbox.itunes.apple.com/verifyReceipt](https://sandbox.itunes.apple.com/verifyReceipt) à des fins de test.   
   
 La réponse JSON contiendra les clés suivantes :

```csharp
{
   "status" : 0,
   "receipt" : { (receipt repeated here) }
}
```

L’état zéro indique un accusé de réception valide. Votre serveur peut traiter le contenu du produit acheté. La clé de réception contient un dictionnaire JSON avec les mêmes propriétés que l' `SKPaymentTransaction` objet qui a été reçu par l’application, de sorte que le code serveur peut interroger ce dictionnaire pour récupérer des informations telles que le product_id et la quantité de l’achat.

Pour plus d’informations, consultez la documentation relative au [Guide de programmation de validation de tickets](https://developer.apple.com/library/archive/releasenotes/General/ValidateAppStoreReceipt/Introduction.html) d’Apple.
