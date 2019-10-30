---
title: Achat de produits consommables dans Xamarin. iOS
description: Ce document décrit les produits consommables dans Xamarin. iOS. Les produits consommables sont des éléments de fonctionnalité à usage unique, tels que la monnaie en jeu.
ms.prod: xamarin
ms.assetid: E0CB4A0F-C3FA-3933-58A7-13246971D677
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 5af4ba8057070481728948635352e1ec2484a0d4
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032337"
---
# <a name="purchasing-consumable-products-in-xamarinios"></a>Achat de produits consommables dans Xamarin. iOS

Les produits consommables sont les plus simples à implémenter, car il n’existe aucune exigence de « restauration ». Ils sont utiles pour les produits tels que la monnaie en jeu ou une fonctionnalité à usage unique. Les utilisateurs peuvent réacheter des produits consommables plus ou plus tard.

## <a name="built-in-product-delivery"></a>Livraison de produit intégrée

L’exemple de code accompagnant ce document illustre les produits intégrés. les ID de produit sont codés en dur dans l’application, car ils sont étroitement couplés au code qui « déverrouille » la fonctionnalité après le paiement. Le processus d’achat peut être visualisé comme suit :   
   
[![la visualisation du processus d’achat](purchasing-consumable-products-images/image26.png)](purchasing-consumable-products-images/image26.png#lightbox)     
   
 Le flux de travail de base est le suivant :   
   
 1. L’application ajoute un `SKPayment` à la file d’attente. Si nécessaire, l’utilisateur sera invité à entrer son ID Apple et a demandé à confirmer le paiement.   
   
 2. StoreKit envoie la demande au serveur pour traitement.   
   
 3. Une fois la transaction terminée, le serveur répond avec un accusé de réception de la transaction.   
   
 4. La sous-classe `SKPaymentTransactionObserver` reçoit la réception et la traite.   
   
 5. L’application active le produit (en mettant à jour `NSUserDefaults` ou un autre mécanisme), puis appelle le `FinishTransaction`de StoreKit.

Il existe un autre type de flux de travail : *produits fournis* par le serveur, décrits plus loin dans le document (voir la section *vérification de la réception et produits fournis par le serveur*).

## <a name="consumable-products-example"></a>Exemple de produits consommables

Le [code InAppPurchaseSample](https://docs.microsoft.com/samples/xamarin/ios-samples/storekit) contient un projet appelé *consommables* qui implémente une monnaie « in-game » de base (appelée « crédits de singe »). L’exemple montre comment implémenter deux produits d’achat dans l’application pour permettre à l’utilisateur d’acheter le nombre de « crédits de singe » qu’il souhaite, dans une application réelle, il faudrait également un moyen de les passer !   

L’application est présentée dans ces captures d’écran : chaque achat ajoute plus de « crédits de singes » à l’équilibre de l’utilisateur :   

 [![chaque achat ajoute plus de crédits de singe au solde des utilisateurs](purchasing-consumable-products-images/image27.png)](purchasing-consumable-products-images/image27.png#lightbox)   

Les interactions entre les classes personnalisées, StoreKit et l’App Store se présentent comme suit :   

 [![les interactions entre les classes personnalisées, StoreKit et l’App Store](purchasing-consumable-products-images/image28.png)](purchasing-consumable-products-images/image28.png#lightbox)

### <a name="viewcontroller-methods"></a>Méthodes ViewController

Outre les propriétés et les méthodes requises pour récupérer les informations sur les produits, le contrôleur d’affichage exige que d’autres observateurs de notification écoutent les notifications relatives à l’achat. Il s’agit simplement de `NSObjects` qui seront enregistrés et supprimés dans `ViewWillAppear` et `ViewWillDisappear` respectivement.

```csharp
NSObject succeededObserver, failedObserver;
```

Le constructeur crée également la sous-classe `SKProductsRequestDelegate` (`InAppPurchaseManager`) qui, à son tour, crée et inscrit le `SKPaymentTransactionObserver` (`CustomPaymentObserver`).   

La première partie du traitement d’une transaction d’achat dans une application consiste à gérer l’enfoncement du bouton lorsque l’utilisateur souhaite acheter un élément, comme illustré dans le code suivant de l’exemple d’application :

```csharp
buy5Button.TouchUpInside += (sender, e) => {
   iap.PurchaseProduct (Buy5ProductId);
};
buy10Button.TouchUpInside += (sender, e) => {
   iap.PurchaseProduct (Buy10ProductId);
};
```

La deuxième partie de l’interface utilisateur gère la notification de réussite de la transaction. dans ce cas, il met à jour le solde affiché :

```csharp
priceObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerTransactionSucceededNotification,
(notification) => {
   balanceLabel.Text = CreditManager.Balance() + " monkey credits";
});
```

La dernière partie de l’interface utilisateur affiche un message si une transaction est annulée pour une raison quelconque. Dans l’exemple de code, un message est simplement écrit dans la fenêtre Sortie :

```csharp
failedObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerTransactionFailedNotification,
(notification) => {
   Console.WriteLine ("Transaction Failed");
});
```

En plus de ces méthodes sur le contrôleur d’affichage, une transaction d’achat de produit consommable requiert également du code sur le `SKProductsRequestDelegate` et le `SKPaymentTransactionObserver`.

### <a name="inapppurchasemanager-methods"></a>Méthodes InAppPurchaseManager

L’exemple de code implémente un certain nombre de méthodes d’achat sur la classe InAppPurchaseManager, y compris la méthode `PurchaseProduct` qui crée une instance `SKPayment` et l’ajoute à la file d’attente à des fins de traitement :

```csharp
public void PurchaseProduct(string appStoreProductId)
{
   SKPayment payment = SKPayment.PaymentWithProduct (appStoreProductId);
   SKPaymentQueue.DefaultQueue.AddPayment (payment);
}
```

L’ajout du paiement à la file d’attente est une opération asynchrone. L’application regagne le contrôle pendant que StoreKit traite la transaction et l’envoie aux serveurs Apple. À ce stade, iOS vérifie que l’utilisateur est connecté à l’App Store et lui invite à entrer un ID et un mot de passe Apple, le cas échéant.   

En supposant que l’utilisateur s’authentifie avec succès auprès de l’App Store et accepte la transaction, le `SKPaymentTransactionObserver` reçoit la réponse de StoreKit et appelle la méthode suivante pour exécuter la transaction et la finaliser.

```csharp
public void CompleteTransaction (SKPaymentTransaction transaction)
{
   var productId = transaction.Payment.ProductIdentifier;
   // Register the purchase, so it is remembered for next time
   PhotoFilterManager.Purchase(productId);
   FinishTransaction(transaction, true);
}
```

La dernière étape consiste à vous assurer que vous indiquez à StoreKit que vous avez correctement rempli la transaction en appelant `FinishTransaction`:

```csharp
public void FinishTransaction(SKPaymentTransaction transaction, bool wasSuccessful)
{
   // remove the transaction from the payment queue.
   SKPaymentQueue.DefaultQueue.FinishTransaction(transaction);  // THIS IS IMPORTANT - LET'S APPLE KNOW WE'RE DONE !!!!
   using (var pool = new NSAutoreleasePool()) {
       NSDictionary userInfo = NSDictionary.FromObjectsAndKeys(new NSObject[] {transaction},new NSObject[] {new NSString("transaction")});
       if (wasSuccessful) {
           // send out a notification that we've finished the transaction
           NSNotificationCenter.DefaultCenter.PostNotificationName (InAppPurchaseManagerTransactionSucceededNotification, this, userInfo);
       } else {
           // send out a notification for the failed transaction
           NSNotificationCenter.DefaultCenter.PostNotificationName (InAppPurchaseManagerTransactionFailedNotification, this, userInfo);
       }
   }
}
```

Une fois le produit remis, `SKPaymentQueue.DefaultQueue.FinishTransaction` doit être appelé pour supprimer la transaction de la file d’attente de paiement.

### <a name="skpaymenttransactionobserver-custompaymentobserver-methods"></a>Méthodes SKPaymentTransactionObserver (CustomPaymentObserver)

StoreKit appelle la méthode `UpdatedTransactions` lorsqu’il reçoit une réponse des serveurs d’Apple, puis passe un tableau d’objets `SKPaymentTransaction` à inspecter pour votre code. La méthode parcourt chaque transaction et exécute une fonction différente en fonction de l’état de la transaction (comme indiqué ici) :

```csharp
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
           default:
               break;
       }
   }
}
```

La méthode `CompleteTransaction` a été traitée plus haut dans cette section. elle enregistre les détails de l’achat dans `NSUserDefaults`, finalise la transaction avec StoreKit et enfin notifie l’interface utilisateur à mettre à jour.

### <a name="purchasing-multiple-products"></a>Achat de plusieurs produits

S’il est logique que votre application achète plusieurs produits, utilisez la classe `SKMutablePayment` et définissez le champ Quantity :

```csharp
public void PurchaseProduct(string appStoreProductId)
{
   SKMutablePayment payment = SKMutablePayment.PaymentWithProduct (appStoreProductId);
   payment.Quantity = 4; // hardcoded as an example
   SKPaymentQueue.DefaultQueue.AddPayment (payment);
}
```

Le code qui gère la transaction terminée doit également interroger la propriété Quantity pour exécuter correctement l’achat :

```csharp
public void CompleteTransaction (SKPaymentTransaction transaction)
{
   var productId = transaction.Payment.ProductIdentifier;
   var qty = transaction.Payment.Quantity;
   if (productId == ConsumableViewController.Buy5ProductId)
       CreditManager.Add(5 * qty);
   else if (productId == ConsumableViewController.Buy10ProductId)
       CreditManager.Add(10 * qty);
   else
       Console.WriteLine ("Shouldn't happen, there are only two products");
   FinishTransaction(transaction, true);
}
```

Lorsque l’utilisateur achète plusieurs quantités, l’alerte de confirmation de StoreKit reflète la quantité, le prix unitaire et le prix total, comme indiqué dans la capture d’écran suivante :

[![confirmation d’un achat](purchasing-consumable-products-images/image30.png)](purchasing-consumable-products-images/image30.png#lightbox)

## <a name="handling-network-outages"></a>Gestion des pannes de réseau

Les achats dans l’application nécessitent une connexion réseau opérationnelle pour que StoreKit communique avec les serveurs Apple. Si aucune connexion réseau n’est disponible, l’achat dans l’application n’est pas disponible.

### <a name="product-requests"></a>Demandes de produits

Si le réseau n’est pas disponible lors de la création d’une `SKProductRequest`, la méthode `RequestFailed` de la sous-classe `SKProductsRequestDelegate` (`InAppPurchaseManager`) sera appelée, comme indiqué ci-dessous :

```csharp
public override void RequestFailed (SKRequest request, NSError error)
{
   using (var pool = new NSAutoreleasePool()) {
       NSDictionary userInfo = NSDictionary.FromObjectsAndKeys(new NSObject[] {error},new NSObject[] {new NSString("error")});
       // send out a notification for the failed transaction
       NSNotificationCenter.DefaultCenter.PostNotificationName (InAppPurchaseManagerRequestFailedNotification, this, userInfo);
   }
}
```

Le ViewController écoute ensuite la notification et affiche un message dans les boutons d’achat :

```csharp
requestObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerRequestFailedNotification,
(notification) => {
   Console.WriteLine ("Request Failed");
   buy5Button.SetTitle ("Network down?", UIControlState.Disabled);
   buy10Button.SetTitle ("Network down?", UIControlState.Disabled);
});
```

Étant donné qu’une connexion réseau peut être temporaire sur des appareils mobiles, les applications peuvent souhaiter surveiller l’état du réseau à l’aide de l’infrastructure SystemConfiguration, puis effectuer une nouvelle tentative lorsqu’une connexion réseau est disponible. Reportez-vous à Apple ou au qui l’utilise.

### <a name="purchase-transactions"></a>Transactions d’achat

La file d’attente de paiement StoreKit stocke et transfère les demandes d’achat si possible, de sorte que l’effet d’une panne réseau varie selon le moment où le réseau a échoué pendant le processus d’achat.   

Si une erreur se produit pendant une transaction, la `SKPaymentTransactionObserver` sous-classe (`CustomPaymentObserver`) aura la méthode `UpdatedTransactions` appelée et la classe `SKPaymentTransaction` sera dans l’État failed.

```csharp
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
           default:
               break;
       }
   }
}
```

La méthode `FailedTransaction` détecte si l’erreur est due à l’annulation par l’utilisateur, comme illustré ici :

```csharp
public void FailedTransaction (SKPaymentTransaction transaction)
{
   //SKErrorPaymentCancelled == 2
   if (transaction.Error.Code == 2) // user cancelled
       Console.WriteLine("User CANCELLED FailedTransaction Code=" + transaction.Error.Code + " " + transaction.Error.LocalizedDescription);
   else // error!
       Console.WriteLine("FailedTransaction Code=" + transaction.Error.Code + " " + transaction.Error.LocalizedDescription);
   FinishTransaction(transaction,false);
}
```

Même si une transaction échoue, la méthode `FinishTransaction` doit être appelée pour supprimer la transaction de la file d’attente de paiement :

```csharp
SKPaymentQueue.DefaultQueue.FinishTransaction(transaction);
```

L’exemple de code envoie ensuite une notification afin que le ViewController puisse afficher un message. Les applications ne doivent pas afficher un message supplémentaire si l’utilisateur a annulé la transaction. Voici d’autres codes d’erreur qui peuvent se produire :

```csharp
FailedTransaction Code=0 Cannot connect to iTunes Store
FailedTransaction Code=5002 An unknown error has occurred
FailedTransaction Code=5020 Forget Your Password?
Applications may detect and respond to specific error codes, or handle them in the same way.
```

## <a name="handling-restrictions"></a>Gestion des restrictions

Les **paramètres > fonctionnalité de restrictions générales des >** d’iOS permettent aux utilisateurs de verrouiller certaines fonctionnalités de leur appareil.   

Vous pouvez demander si l’utilisateur est autorisé à effectuer des achats dans l’application via la méthode `SKPaymentQueue.CanMakePayments`. Si la valeur renvoyée est false, l’utilisateur ne peut pas accéder aux achats dans l’application. StoreKit affiche automatiquement un message d’erreur à l’utilisateur en cas de tentative d’achat. En vérifiant cette valeur, votre application peut à la place masquer les boutons d’achat ou entreprendre une autre action pour aider l’utilisateur.   

Dans le fichier `InAppPurchaseManager.cs`, la méthode `CanMakePayments` encapsule la fonction StoreKit comme suit :

```csharp
public bool CanMakePayments()
{
   return SKPaymentQueue.CanMakePayments;
}
```

Pour tester cette méthode, utilisez la fonctionnalité de **restrictions** d’IOS pour désactiver les **achats dans l’application**:   

 [![utiliser la fonctionnalité restrictions d’iOS pour désactiver les achats dans l’application](purchasing-consumable-products-images/image31.png)](purchasing-consumable-products-images/image31.png#lightbox)   

Cet exemple de code de `ConsumableViewController` réagit à `CanMakePayments` retourner la valeur false en affichant le texte **AppStore désactivé** sur les boutons désactivés.

```csharp
// only if we can make payments, request the prices
if (iap.CanMakePayments()) {
   // now go get prices, if we don't have them already
   if (!pricesLoaded)
       iap.RequestProductData(products); // async request via StoreKit -> App Store
} else {
   // can't make payments (purchases turned off in Settings?)
   // the buttons are disabled by default, and only enabled when prices are retrieved
   buy5Button.SetTitle ("AppStore disabled", UIControlState.Disabled);
   buy10Button.SetTitle ("AppStore disabled", UIControlState.Disabled);
}
```

L’application se présente comme suit quand la fonctionnalité **d’achats dans l’application** est limitée : les boutons d’achat sont désactivés.   

 [![l’application ressemble à ce qui suit lorsque la fonctionnalité d’achats dans l’application est limitée, les boutons d’achat sont désactivés](purchasing-consumable-products-images/image32.png)](purchasing-consumable-products-images/image32.png#lightbox)   

Les informations sur le produit peuvent toujours être demandées lorsque `CanMakePayments` a la valeur false, de sorte que l’application peut toujours récupérer et afficher les prix. Cela signifie que si nous avons supprimé la vérification du `CanMakePayments` du code, les boutons d’achat sont toujours actifs. Toutefois, lorsqu’un achat est tenté, l’utilisateur voit un message indiquant que les **achats dans l’application ne sont pas autorisés** (générés par StoreKit lorsque la file d’attente de paiement est accès) :   

 [![les achats dans l’application ne sont pas autorisés](purchasing-consumable-products-images/image33.png)](purchasing-consumable-products-images/image33.png#lightbox)   

Les applications réelles peuvent adopter une approche différente pour gérer la restriction, comme le masquage des boutons et éventuellement l’offre d’un message plus détaillé que l’alerte que StoreKit affiche automatiquement.
