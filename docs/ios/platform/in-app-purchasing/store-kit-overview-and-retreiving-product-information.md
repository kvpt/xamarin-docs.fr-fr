---
title: Vue d’ensemble de StoreKit et de récupération des informations sur les produits dans Xamarin.iOS
description: Ce document fournit une vue d’ensemble de StoreKit. Il décrit les classes utilisées avec StoreKit, test StoreKit interactions, affichage des produits pour la vente, gestion des produits non valides et affichage des prix localisées.
ms.prod: xamarin
ms.assetid: FC21192E-6325-4389-C060-E92DBB5EBD87
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 0dcda2e4fd1ca7773668a0a6fdf46e01f2f0841d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118524"
---
# <a name="storekit-overview-and-retrieving-product-info-in-xamarinios"></a>Vue d’ensemble de StoreKit et de récupération des informations sur les produits dans Xamarin.iOS

L’interface utilisateur pour un achat dans l’application est indiqué dans les captures d’écran ci-dessous.
Avant toute transaction ait lieu, l’application doit extraire des prix du produit et une description pour l’affichage. Lorsque l’utilisateur appuie sur **acheter**, l’application effectue une demande à StoreKit qui gère la boîte de dialogue de confirmation, la connexion de l’ID Apple. En supposant que la transaction puis réussit, StoreKit notifie le code d’application, qui doit stocker le résultat de la transaction et lui fournir un accès à leur achat.   

   
 [![](store-kit-overview-and-retreiving-product-information-images/image14.png "StoreKit notifie le code d’application, qui doit stocker le résultat de la transaction et l’utilisateur ayant accès à leur achat")](store-kit-overview-and-retreiving-product-information-images/image14.png#lightbox)

## <a name="classes"></a>Classes

L’implémentation des achats dans l’application requiert les classes suivantes à partir de l’infrastructure StoreKit :   
   
 **SKProductsRequest** – une demande à StoreKit pour des produits approuvés à vendre (App Store). Peut être configuré avec un numéro d’ID de produit.

-   **SKProductsRequestDelegate** – déclare des méthodes pour gérer les demandes de produit et les réponses. 
-   **SKProductsResponse** – renvoyées au délégué à partir de StoreKit (App Store). Contient le SKProducts qui correspond au produit ID envoyées avec la demande. 
-   **SKProduct** – un produit récupérées à partir de StoreKit (que vous avez configuré dans iTunes Connect). Contient des informations sur le produit, telles que l’ID de produit, titre, Description et prix. 
-   **SKPayment** – créé avec un ID de produit et ajouté à la file d’attente de paiement pour effectuer un achat. 
-   **SKPaymentQueue** – en file d’attente des demandes de paiement pour être envoyée à Apple. Les notifications sont déclenchées à la suite de chaque paiement en cours de traitement. 
-   **SKPaymentTransaction** – représente une transaction terminée (une demande d’achat qui a été traitée par l’App Store et renvoyée à votre application via StoreKit). La transaction peut être acheté, restaurée ou échec. 
-   **SKPaymentTransactionObserver** – sous-classe personnalisée qui répond aux événements générés par la file d’attente de paiement StoreKit. 
-   **Les opérations StoreKit sont asynchrones** – après un SKProductRequest est démarré ou un SKPayment est ajouté à la file d’attente, le contrôle est retourné à votre code. StoreKit s’appeler des méthodes sur votre sous-classe SKProductsRequestDelegate ou SKPaymentTransactionObserver lorsqu’il reçoit des données à partir de serveurs Apple. 


Le diagramme suivant illustre les relations entre les différentes classes de StoreKit (les classes abstraites doivent être implémentées dans votre application) :   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image15.png "Les relations entre les différentes classes abstraites de classes de StoreKit doivent être implémentées dans l’application")](store-kit-overview-and-retreiving-product-information-images/image15.png#lightbox)   
   
   
   
 Ces classes sont expliquées plus en détail plus loin dans ce document.

## <a name="testing"></a>Test

La plupart des opérations StoreKit nécessitent un périphérique réel pour le test. Récupération des informations de produit (p. ex. prix &amp; description) fonctionne dans le simulateur mais l’achat et les opérations de restauration renvoie une erreur (tel que le Code de FailedTransaction = 5002 une erreur inconnue s’est produite).

Remarque : StoreKit ne fonctionne pas dans le simulateur iOS. Lorsque vous exécutez votre application dans iOS Simulator, StoreKit consigne un avertissement si votre application tente de récupérer la file d’attente de paiement. Le magasin de test doit être effectuée sur des appareils réels.   
   
   
   
 Important : Ne vous connectez pas avec votre compte de test dans l’application paramètres. Vous pouvez utiliser l’application paramètres pour se connecter à n’importe quel compte ID Apple existant, puis vous devez attendre pour être invité *au sein de votre séquence de In-App Purchase* pour vous connecter à l’aide d’un test ID Apple.   
   
   
   

Si vous tentez de connexion dans la banque réelle avec un compte de test, il sera automatiquement converti à un ID de Apple réel. Ce compte ne sera plus utilisable pour le test.

Pour tester le code de StoreKit, vous devez déconnecter de votre compte de test d’iTunes régulière et d’une connexion avec un compte de test spéciales (créé dans iTunes Connect) qui est lié au magasin de test. Se déconnecter de la visite de compte actuel **Paramètres > iTunes et App Store** comme indiqué ici :

 [![](store-kit-overview-and-retreiving-product-information-images/image16.png "Se déconnecter de la visite du compte actuel paramètres iTunes et App Store")](store-kit-overview-and-retreiving-product-information-images/image16.png#lightbox)
 
puis connectez-vous avec un compte de test *lorsque demandé par StoreKit au sein de votre application*:



Pour créer les utilisateurs de test dans iTunes Connect cliquent sur **utilisateurs et rôles** dans la page principale.

 [![](store-kit-overview-and-retreiving-product-information-images/image17.png "Pour créer des utilisateurs de test dans iTunes Connect cliquez sur utilisateurs et rôles dans la page principale")](store-kit-overview-and-retreiving-product-information-images/image17.png#lightbox)

Sélectionnez **testeurs de bac à sable**

 [![](store-kit-overview-and-retreiving-product-information-images/image18.png "En sélectionnant les testeurs de bac à sable")](store-kit-overview-and-retreiving-product-information-images/image18.png#lightbox)

La liste des utilisateurs existants s’affiche. Vous pouvez ajouter un nouvel utilisateur ou supprimer un enregistrement existant. Le portail n’effectue pas (actuellement) vous permettent d’afficher ou modifier les existants de tester les utilisateurs, il est recommandé de conserver un enregistrement de bon de chaque utilisateur de test qui est créé (en particulier ce mot de passe). Une fois que vous supprimez un utilisateur de test de l’adresse de messagerie ne peut pas être utilisée à nouveau pour un autre compte de test.  
   
 [![](store-kit-overview-and-retreiving-product-information-images/image19.png "La liste des utilisateurs existants s’affiche.")](store-kit-overview-and-retreiving-product-information-images/image19.png#lightbox)   
   
 Nouveaux utilisateurs de test ont des attributs similaires à un ID Apple réel (par exemple, nom, mot de passe, question secrète et la réponse). Conserver un enregistrement de toutes les informations entrées ici. Le **sélectionnez iTunes Store** champ déterminera la devise et la langue achats dans l’application utilisera quand connecté en tant que cet utilisateur.

 [![](store-kit-overview-and-retreiving-product-information-images/image20.png "Le champ Sélectionnez iTunes Store déterminera la devise et la langue pour leurs achats dans l’application de l’utilisateur")](store-kit-overview-and-retreiving-product-information-images/image20.png#lightbox)

## <a name="retrieving-product-information"></a>Récupération des informations produit

La première étape de vente d’un produit d’achat dans l’application est l’affichage : récupérer le prix actuel et la description de l’App Store pour l’affichage.   
   
   
   
 Quel que soit le type de produits une application vend (consommables, Non consommable ou un type d’abonnement), le processus de récupération des informations de produit pour l’affichage est le même. Le code InAppPurchaseSample qui accompagne cet article contient un projet nommé *consommables* qui montre comment récupérer des informations de production pour l’affichage. Il montre comment :

-  Créer une implémentation de `SKProductsRequestDelegate` et implémenter la `ReceivedResponse` méthode abstraite. L’exemple de code appelle cela la `InAppPurchaseManager` classe. 
-  Vérifiez auprès de StoreKit pour voir si les paiements sont autorisées (à l’aide de `SKPaymentQueue.CanMakePayments` ). 
-  Instancier un `SKProductsRequest` avec les ID de produit qui ont été définis dans iTunes Connect. Pour cela dans l’exemple `InAppPurchaseManager.RequestProductData` (méthode). 
-  Appelez la méthode Start sur le `SKProductsRequest` . Cela déclenche un appel asynchrone pour les serveurs App Store. Le délégué ( `InAppPurchaseManager` ) sera de retour appelée avec les résultats. 
-  Le délégué ( `InAppPurchaseManager` ) `ReceivedResponse` méthode met à jour l’interface utilisateur avec les données retournées par le Store de l’application (les prix des produits & descriptions ou messages sur les produits non valides). 

L’interaction globale ressemble à ceci ( **StoreKit** est intégré à iOS et le **App Store** représentent des serveurs d’Apple) :

 [![](store-kit-overview-and-retreiving-product-information-images/image21.png "Graphique d’informations sur les produits lors de la récupération")](store-kit-overview-and-retreiving-product-information-images/image21.png#lightbox)

### <a name="displaying-product-information-example"></a>Afficher l’exemple des informations de produit

Le [InAppPurchaseSample](https://developer.xamarin.com/samples/monotouch/StoreKit/) *consommables* exemple de code montre comment les informations de produit peuvent être récupérées. L’écran principal de l’exemple affiche des informations pour les deux produits qui sont récupérées à partir de l’App Store :   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image23.png "L’écran principal affiche les produits informations récupérées à partir de l’App Store")](store-kit-overview-and-retreiving-product-information-images/image23.png#lightbox)   
   
   
   
 L’exemple de code pour récupérer et afficher des informations sur le produit est expliquée plus en détail ci-dessous.


#### <a name="viewcontroller-methods"></a>Méthodes ViewController

Le `ConsumableViewController` classe gère l’affichage des prix pour les deux produits dont les ID produit sont codées en dur dans la classe.

```csharp
public static string Buy5ProductId = "com.xamarin.storekit.testing.consume5credits",
   Buy10ProductId = "com.xamarin.storekit.testing.consume10credits";
List<string> products;
InAppPurchaseManager iap;
public ConsumableViewController () : base()
{
   // two products for sale on this page
   products = new List<string>() {Buy5ProductId, Buy10ProductId};
   iap = new InAppPurchaseManager();
}
```

La classe niveau doit être un NSObject déclaré qui sera utilisé pour le programme d’installation un `NSNotificationCenter` Observateur :

```csharp
NSObject priceObserver;
```

Dans la méthode ViewWillAppear l’observateur est créé et affecté à l’aide du centre de notification par défaut :

```csharp
priceObserver = NSNotificationCenter.DefaultCenter.AddObserver (
  InAppPurchaseManager.InAppPurchaseManagerProductsFetchedNotification,
(notification) => {
   // display code goes here, to handle the response from the App Store
}
```

   
   
 À la fin de la `ViewWillAppear` méthode, appelez le `RequestProductData` méthode pour lancer la requête StoreKit. Une fois que cette demande a été faite, StoreKit contactera asynchrone serveurs Apple pour obtenir les informations et alimentez-le à votre application. Pour cela, le `SKProductsRequestDelegate` sous-classe ( `InAppPurchaseManager`) qui est expliqué dans la section suivante.

```csharp
iap.RequestProductData(products);
```

Le code pour afficher le prix et la description récupère les informations de la SKProduct et lui attribue les contrôles UIKit (Notez que nous affichons le `LocalizedTitle` et `LocalizedDescription` – StoreKit résout automatiquement le texte approprié et les prix en fonction de les paramètres du compte utilisateur). Le code suivant appartienne dans la notification que nous avons créé ci-dessus :

```csharp
priceObserver = NSNotificationCenter.DefaultCenter.AddObserver (
  InAppPurchaseManager.InAppPurchaseManagerProductsFetchedNotification,
(notification) => {
   // display code goes here, to handle the response from the App Store
   var info = notification.UserInfo;
   if (info.ContainsKey(NSBuy5ProductId)) {
       var product = (SKProduct) info.ObjectForKey(NSBuy5ProductId);
       buy5Button.Enabled = true;
       buy5Title.Text = product.LocalizedTitle;
       buy5Description.Text = product.LocalizedDescription;
       buy5Button.SetTitle("Buy " + product.Price, UIControlState.Normal); // price display should be localized
   }
}
```

Enfin, le `ViewWillDisappear` méthode doit garantir l’observateur est supprimé :

```csharp
NSNotificationCenter.DefaultCenter.RemoveObserver (priceObserver);
```

#### <a name="skproductrequestdelegate-inapppurchasemanager-methods"></a>Méthodes SKProductRequestDelegate (InAppPurchaseManager)

Le `RequestProductData` méthode est appelée lorsque l’application souhaite récupérer les prix des produits et autres informations. Il analyse la collection d’ID de produit dans le type de données approprié, puis crée un `SKProductsRequest` avec ces informations. Appel de la méthode Start entraîne une demande réseau à apporter aux serveurs Apple. La demande sera exécuté de façon asynchrone et appeler le `ReceivedResponse` méthode du délégué lorsqu’il se termine avec succès.

```csharp
public void RequestProductData (List<string> productIds)
{
   var array = new NSString[productIds.Count];
   for (var i = 0; i < productIds.Count; i++) {
       array[i] = new NSString(productIds[i]);
   }
   NSSet productIdentifiers = NSSet.MakeNSObjectSet<NSString>(array);
   productsRequest = new SKProductsRequest(productIdentifiers);
   productsRequest.Delegate = this; // for SKProductsRequestDelegate.ReceivedResponse
   productsRequest.Start();
}
```

iOS route automatiquement la demande vers la version « bac à sable » ou « production » de l’App Store en fonction de l’application s’exécute avec – quel profil d’approvisionnement ainsi quand vous développez ou testez votre application la demande ont accès à chaque produit configuré dans iTunes Connect (même ceux qui n’ont pas encore été soumis ni approuvées par Apple). Lorsque votre application est en production, les demandes StoreKit retournera uniquement les informations pour **Approved** produits.   
   
   
   
 Le `ReceivedResponse` méthode substituée est appelée une fois que les serveurs Apple ayant répondu avec des données. Car il est appelé en arrière-plan, le code doit analyser les données valides et une notification pour envoyer les informations de produit à n’importe quel ViewControllers « écoute » pour cette notification. Le code pour recueillir des informations de produit valide et envoyer une notification est indiqué ci-dessous :

```csharp
public override void ReceivedResponse (SKProductsRequest request, SKProductsResponse response)
{
   SKProduct[] products = response.Products;
   NSDictionary userInfo = null;
   if (products.Length > 0) {
       NSObject[] productIdsArray = new NSObject[response.Products.Length];
       NSObject[] productsArray = new NSObject[response.Products.Length];
       for (int i = 0; i < response.Products.Length; i++) {
           productIdsArray[i] = new NSString(response.Products[i].ProductIdentifier);
           productsArray[i] = response.Products[i];
       }
       userInfo = NSDictionary.FromObjectsAndKeys (productsArray, productIdsArray);
   }
   NSNotificationCenter.DefaultCenter.PostNotificationName (InAppPurchaseManagerProductsFetchedNotification, this, userInfo);
}
```

Bien que n’apparaisse ne pas dans le diagramme, le `RequestFailed` méthode doit également être remplacée afin que vous pouvez fournir des commentaires à l’utilisateur au cas où les serveurs App Store ne sont pas accessibles (ou une autre erreur se produit). L’exemple de code écrit simplement dans la console, mais une application réelle peut choisir d’interroger à `error.Code` propriété et implémenter un comportement personnalisé (par exemple, une alerte à l’utilisateur).

```csharp
public override void RequestFailed (SKRequest request, NSError error)
{
   Console.WriteLine (" ** InAppPurchaseManager RequestFailed() " + error.LocalizedDescription);
}
```

Cette capture d’écran montre l’exemple d’application immédiatement après le chargement (lorsque aucune information de produit est disponible) :

 [![](store-kit-overview-and-retreiving-product-information-images/image24.png "L’exemple d’application immédiatement après le chargement lorsque aucune information de produit est disponible")](store-kit-overview-and-retreiving-product-information-images/image24.png#lightbox)

## <a name="invalid-products"></a>Produits non valides

Un `SKProductsRequest` peut également retourner une liste d’ID de produit non valide. Les produits non valides sont généralement retournés en raison d’une des opérations suivantes :   
   
   
   
 **ID de produit a été saisi correctement** – uniquement les ID produit valides sont acceptées.   
   
 **Produit n’a pas été approuvé** – lors du test, tous les produits qui sont effacés de vente doivent être retournées par une `SKProductsRequest`; toutefois en production uniquement les produits approuvés sont retournés.   
   
 **ID d’application n’est pas explicit** – Wildcard App IDs (avec un astérisque) n’autorisent pas les achats dans l’application.   
   
 **Incorrect de profil de provisionnement** : Si vous apportez des modifications à la configuration de votre application dans le portail de provisionnement (telles que l’activation achats dans l’application), n’oubliez pas de nouveau générer et utiliser le profil de provisionnement approprié lors de la création de l’application.   
   
 **contrat de payé des Applications iOS n’est pas en place** – StoreKit fonctionnalités ne fonctionneront pas du tout sauf s’il existe un contrat valid pour votre compte de développeur Apple.   
   
 **Le fichier binaire est dans l’état Rejected** : si l’état Rejected (soit par l’équipe App Store, ou par le développeur) est un fichier binaire précédemment soumis StoreKit fonctionnalités ne fonctionnera pas.

Le `ReceivedResponse` méthode dans l’exemple de code génère les produits non valides à la console :

```csharp
public override void ReceivedResponse (SKProductsRequest request, SKProductsResponse response)
{
   // code removed for clarity
   foreach (string invalidProductId in response.InvalidProducts) {
       Console.WriteLine("Invalid product id: " + invalidProductId );
   }
}
```

## <a name="displaying-localized-prices"></a>Affichage des prix localisées

Les niveaux de prix spécifient un prix spécifique pour chaque produit pour tous les magasins d’applications internationales. Pour garantir les prix sont correctement affichées pour chaque devise, utilisez la méthode d’extension suivantes (définies dans `SKProductExtension.cs`) au lieu de la propriété de prix de chaque `SKProduct`:

```csharp
public static class SKProductExtension {
   public static string LocalizedPrice (this SKProduct product)
   {
       var formatter = new NSNumberFormatter ();
       formatter.FormatterBehavior = NSNumberFormatterBehavior.Version_10_4;  
       formatter.NumberStyle = NSNumberFormatterStyle.Currency;
       formatter.Locale = product.PriceLocale;
       var formattedString = formatter.StringFromNumber(product.Price);
       return formattedString;
   }
}
```

Le code qui définit le titre du bouton utilise la méthode d’extension comme suit :

```csharp
string Buy = "Buy {0}"; // or a localizable string
buy5Button.SetTitle(String.Format(Buy, product.LocalizedPrice()), UIControlState.Normal);
```

À l’aide de deux comptes de test iTunes différents (un pour le magasin American) et un pour le japonais magasin des résultats dans les captures d’écran suivante :   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image25.png "Deux iTunes différents tests comptabilité de langue des résultats spécifiques")](store-kit-overview-and-retreiving-product-information-images/image25.png#lightbox)   
   
   
   
 Notez que le magasin affecte la langue utilisée pour la devise d’informations et le prix de produit, tandis que le paramètre de langue de l’appareil affecte les étiquettes et autres contenus localisés.   
   
   
   
 Rappelez-vous que d’utiliser un autre magasin de test compte, vous devez **se déconnecter** dans le **Paramètres > iTunes et App Store** , puis redémarrez l’application pour se connecter avec un autre compte. Pour modifier la langue de l’appareil accédez à **Paramètres > Général > International > langue**.

