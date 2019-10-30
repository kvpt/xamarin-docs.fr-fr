---
title: Présentation de StoreKit et récupération des informations sur le produit dans Xamarin. iOS
description: Ce document fournit une vue d’ensemble de StoreKit. Il décrit les classes utilisées avec StoreKit, les tests d’interactions StoreKit, l’affichage des produits à la vente, le traitement des produits non valides et l’affichage des prix localisés.
ms.prod: xamarin
ms.assetid: FC21192E-6325-4389-C060-E92DBB5EBD87
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 086dcb87f03ed4abbf3b82dc10add0f5698d52b3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032333"
---
# <a name="storekit-overview-and-retrieving-product-info-in-xamarinios"></a>Présentation de StoreKit et récupération des informations sur le produit dans Xamarin. iOS

L’interface utilisateur d’un achat dans l’application est présentée dans les captures d’écran ci-dessous.
Avant qu’une transaction ait lieu, l’application doit récupérer le prix et la description du produit pour l’affichage. Ensuite, lorsque l’utilisateur appuie sur **acheter**, l’application envoie une requête à StoreKit qui gère la boîte de dialogue de confirmation et l’ID Apple login. En supposant que la transaction aboutisse, StoreKit notifie le code de l’application, qui doit stocker le résultat de la transaction et permet à l’utilisateur d’accéder à son achat.   

 [![](store-kit-overview-and-retreiving-product-information-images/image14.png "StoreKit notifies the application code, which must store the transaction result and provide the user with access to their purchase")](store-kit-overview-and-retreiving-product-information-images/image14.png#lightbox)

## <a name="classes"></a>Classes

L’implémentation des achats dans l’application requiert les classes suivantes de l’infrastructure StoreKit :   
   
 **SKProductsRequest** – demande à StoreKit pour les produits approuvés à vendre (App Store). Peut être configuré avec un nombre d’ID de produit.

- **SKProductsRequestDelegate** : déclare des méthodes pour gérer les demandes de produits et les réponses. 
- **SKProductsResponse** – renvoyé au délégué à partir de StoreKit (App Store). Contient les SKProducts qui correspondent aux ID de produit envoyés avec la demande. 
- **SKProduct** : produit récupéré à partir de StoreKit (que vous avez configuré dans iTunes Connect). Contient des informations sur le produit, telles que l’ID de produit, le titre, la description et le prix. 
- **SKPayment** : créé avec un ID de produit et ajouté à la file d’attente de paiement pour effectuer un achat. 
- **SKPaymentQueue** : demandes de paiement mises en file d’attente à envoyer à Apple. Les notifications sont déclenchées à la suite de chaque paiement en cours de traitement. 
- **SKPaymentTransaction** – représente une transaction terminée (demande d’achat traitée par l’App Store et renvoyée à votre application via StoreKit). La transaction peut être achetée, restaurée ou en échec. 
- **SKPaymentTransactionObserver** – sous-classe personnalisée qui répond aux événements générés par la file d’attente de paiement StoreKit. 
- Les **opérations StoreKit sont asynchrones** : après le démarrage d’un SKProductRequest ou l’ajout d’un SKPayment à la file d’attente, le contrôle est retourné à votre code. StoreKit appellera les méthodes sur votre sous-classe SKProductsRequestDelegate ou SKPaymentTransactionObserver lorsqu’il reçoit des données des serveurs d’Apple. 

Le diagramme suivant montre les relations entre les différentes classes StoreKit (les classes abstraites doivent être implémentées dans votre application) :   

 [![](store-kit-overview-and-retreiving-product-information-images/image15.png "The relationships between the various StoreKit classes abstract classes must be implemented in the app")](store-kit-overview-and-retreiving-product-information-images/image15.png#lightbox)   

Ces classes sont expliquées plus en détail plus loin dans ce document.

## <a name="testing"></a>Test

La plupart des opérations StoreKit requièrent un appareil réel pour les tests. Récupération des informations sur le produit (par ex. le prix &amp; Description) fonctionnera dans le simulateur, mais les opérations d’achat et de restauration renverront une erreur (par exemple, FailedTransaction code = 5002 une erreur inconnue s’est produite).

Remarque : StoreKit ne fonctionne pas dans iOS Simulator. Lors de l’exécution de votre application dans iOS Simulator, StoreKit enregistre un avertissement si votre application tente de récupérer la file d’attente de paiement. Le test du magasin doit être effectué sur les appareils réels.   

Important : ne vous connectez pas avec votre compte de test dans l’application paramètres. Vous pouvez utiliser l’application paramètres pour vous déconnecter de n’importe quel compte d’ID Apple existant, puis vous devez attendre qu’une invite s’affiche *dans votre séquence d’achat dans l’application* pour vous connecter à l’aide d’un ID Apple de test.   

Si vous tentez de vous connecter au magasin réel avec un compte de test, il est automatiquement converti en un ID Apple réel. Ce compte ne sera plus utilisable pour le test.

Pour tester le code StoreKit, vous devez vous déconnecter de votre compte de test iTunes standard et vous connecter avec un compte de test spécial (créé dans iTunes Connect) qui est lié au magasin de test. Pour vous déconnecter des paramètres de visite de compte actuels **> iTunes et App Store** comme indiqué ici :

 [![](store-kit-overview-and-retreiving-product-information-images/image16.png "To sign out of the current account visit Settings iTunes and App Store")](store-kit-overview-and-retreiving-product-information-images/image16.png#lightbox)

Ensuite, connectez-vous avec un compte de test lorsqu’il est *demandé par StoreKit dans votre application*:

Pour créer des utilisateurs de test dans iTunes Connect, cliquez sur **utilisateurs et rôles** dans la page principale.

 [![](store-kit-overview-and-retreiving-product-information-images/image17.png "To create test users in iTunes Connect click on Users and Roles on the main page")](store-kit-overview-and-retreiving-product-information-images/image17.png#lightbox)

Sélectionner les **testeurs sandbox**

 [![](store-kit-overview-and-retreiving-product-information-images/image18.png "Selecting Sandbox Testers")](store-kit-overview-and-retreiving-product-information-images/image18.png#lightbox)

La liste des utilisateurs existants s’affiche. Vous pouvez ajouter un nouvel utilisateur ou supprimer un enregistrement existant. Le portail ne vous permet pas (actuellement) d’afficher ou de modifier les utilisateurs de test existants. il est donc recommandé de conserver un enregistrement correct de chaque utilisateur de test créé (en particulier le mot de passe que vous attribuez). Une fois que vous avez supprimé un utilisateur de test, l’adresse de messagerie ne peut pas être réutilisée pour un autre compte de test.  
   
 [![](store-kit-overview-and-retreiving-product-information-images/image19.png "The list of existing users is displayed")](store-kit-overview-and-retreiving-product-information-images/image19.png#lightbox)   
   
 Les nouveaux utilisateurs de test ont des attributs similaires à un ID Apple réel (par exemple, le nom, le mot de passe, la question secrète et la réponse). Conservez un enregistrement de tous les détails saisis ici. Le champ **Sélectionner le magasin iTunes** détermine la devise et la langue utilisées par les achats dans l’application lors de la connexion à cet utilisateur.

 [![](store-kit-overview-and-retreiving-product-information-images/image20.png "The Select iTunes Store field will determine the user's currency and language for their in-app purchases")](store-kit-overview-and-retreiving-product-information-images/image20.png#lightbox)

## <a name="retrieving-product-information"></a>Récupération des informations sur le produit

La première étape de la vente d’un produit d’achat dans l’application est l’affichage : récupération du prix et de la description actuels à partir du magasin d’applications pour l’affichage.   

Quel que soit le type de produits vendus par une application (consommable, non consommable ou un type d’abonnement), le processus de récupération des informations sur le produit pour l’affichage est le même. Le code InAppPurchaseSample qui accompagne cet article contient un projet nommé *consommables* qui montre comment récupérer des informations de production à afficher. Il montre comment :

- Créez une implémentation de `SKProductsRequestDelegate` et implémentez la méthode abstraite `ReceivedResponse`. L’exemple de code appelle cette classe `InAppPurchaseManager`. 
- Vérifiez avec StoreKit pour voir si les paiements sont autorisés (à l’aide de `SKPaymentQueue.CanMakePayments`). 
- Instanciez un `SKProductsRequest` avec les ID de produit qui ont été définis dans iTunes Connect. Cette opération est effectuée dans la méthode `InAppPurchaseManager.RequestProductData` de l’exemple. 
- Appelez la méthode Start sur la `SKProductsRequest`. Cela déclenche un appel asynchrone aux serveurs de l’App Store. Le délégué (`InAppPurchaseManager`) sera rappelé avec les résultats. 
- La méthode de `ReceivedResponse` (`InAppPurchaseManager`) du délégué met à jour l’interface utilisateur avec les données retournées à partir de l’App Store (prix du produit & descriptions ou messages sur les produits non valides). 

L’interaction globale ressemble à ceci ( **StoreKit** est intégré à iOS, et l' **App Store** représente les serveurs d’Apple) :

 [![](store-kit-overview-and-retreiving-product-information-images/image21.png "Retrieving Product Information graph")](store-kit-overview-and-retreiving-product-information-images/image21.png#lightbox)

### <a name="displaying-product-information-example"></a>Exemple d’affichage des informations sur le produit

L’exemple de *consommables* [InAppPurchaseSample](https://docs.microsoft.com/samples/xamarin/ios-samples/storekit) illustre le mode de récupération des informations sur les produits. L’écran principal de l’exemple affiche des informations pour deux produits récupérés à partir de l’App Store :   

 [![](store-kit-overview-and-retreiving-product-information-images/image23.png "The main screen displays information products  retrieved from the App Store")](store-kit-overview-and-retreiving-product-information-images/image23.png#lightbox)   

L’exemple de code permettant de récupérer et d’afficher les informations sur les produits est expliqué plus en détail ci-dessous.

#### <a name="viewcontroller-methods"></a>Méthodes ViewController

La classe `ConsumableViewController` gère l’affichage des prix de deux produits dont les ID produit sont codés en dur dans la classe.

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

Au niveau de la classe, il doit également exister un NSObject déclaré qui sera utilisé pour configurer un observateur `NSNotificationCenter` :

```csharp
NSObject priceObserver;
```

Dans la méthode ViewWillAppear, l’observateur est créé et affecté à l’aide du centre de notifications par défaut :

```csharp
priceObserver = NSNotificationCenter.DefaultCenter.AddObserver (
  InAppPurchaseManager.InAppPurchaseManagerProductsFetchedNotification,
(notification) => {
   // display code goes here, to handle the response from the App Store
}
```

À la fin de la méthode `ViewWillAppear`, appelez la méthode `RequestProductData` pour lancer la requête StoreKit. Une fois cette demande effectuée, StoreKit contacte de manière asynchrone les serveurs d’Apple pour obtenir les informations et les renvoyer à votre application. Cela est réalisé par la sous-classe `SKProductsRequestDelegate` (`InAppPurchaseManager`) qui est expliquée dans la section suivante.

```csharp
iap.RequestProductData(products);
```

Le code permettant d’afficher le prix et la description récupère simplement les informations du SKProduct et les affecte aux contrôles UIKit (Notez que nous affichons les `LocalizedTitle` et que `LocalizedDescription` – StoreKit résout automatiquement le texte et les prix appropriés en fonction de la valeur de l’utilisateur paramètres du compte). Le code suivant appartient à la notification que nous avons créée ci-dessus :

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

Enfin, la méthode `ViewWillDisappear` doit s’assurer que l’observateur est supprimé :

```csharp
NSNotificationCenter.DefaultCenter.RemoveObserver (priceObserver);
```

#### <a name="skproductrequestdelegate-inapppurchasemanager-methods"></a>Méthodes SKProductRequestDelegate (InAppPurchaseManager)

La méthode `RequestProductData` est appelée lorsque l’application souhaite récupérer les prix des produits et d’autres informations. Il analyse la collection d’ID de produit dans le type de données correct, puis crée un `SKProductsRequest` avec ces informations. L’appel de la méthode Start provoque l’établissement d’une demande réseau aux serveurs Apple. La demande s’exécute de façon asynchrone et appelle la méthode `ReceivedResponse` du délégué lorsqu’elle s’est terminée avec succès.

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

iOS achemine automatiquement la requête vers la version « sandbox » ou « production » de l’App Store en fonction du profil de provisionnement avec lequel l’application s’exécute. par conséquent, lorsque vous développez ou testez votre application, la demande aura accès à tous les produits configuré dans iTunes Connect (même ceux qui n’ont pas encore été soumis ou approuvés par Apple). Lorsque votre application est en production, les demandes StoreKit ne retournent que les informations des produits **approuvés** .   

La méthode substituée `ReceivedResponse` est appelée après que les serveurs d’Apple ont répondu aux données. Étant donné que cette méthode est appelée en arrière-plan, le code doit analyser les données valides et utiliser une notification pour envoyer les informations sur le produit à tous les ViewControllers « qui écoutent » pour cette notification. Le code permettant de collecter des informations sur les produits valides et d’envoyer une notification est illustré ci-dessous :

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

Bien que cela ne soit pas indiqué dans le diagramme, la méthode `RequestFailed` doit également être remplacée pour que vous puissiez fournir des commentaires à l’utilisateur au cas où les serveurs App Store ne seraient pas accessibles (ou une autre erreur se produit). L’exemple de code écrit simplement dans la console, mais une application réelle peut choisir d’effectuer une requête sur `error.Code` propriété et d’implémenter un comportement personnalisé (par exemple, une alerte pour l’utilisateur).

```csharp
public override void RequestFailed (SKRequest request, NSError error)
{
   Console.WriteLine (" ** InAppPurchaseManager RequestFailed() " + error.LocalizedDescription);
}
```

Cette capture d’écran montre l’exemple d’application immédiatement après le chargement (quand aucune information sur le produit n’est disponible) :

 [![](store-kit-overview-and-retreiving-product-information-images/image24.png "The sample app immediately after loading when no product information is available")](store-kit-overview-and-retreiving-product-information-images/image24.png#lightbox)

## <a name="invalid-products"></a>Produits non valides

Une `SKProductsRequest` peut également retourner une liste d’ID de produit non valides. Les produits non valides sont généralement retournés en raison de l’un des éléments suivants :   

L' **ID de produit a été tapé** de façon incorrecte : seuls les ID de produit valides sont acceptés.   
   
 Le **produit n’a pas été approuvé** : pendant le test, tous les produits dont la vente est désactivée doivent être retournés par un `SKProductsRequest`; Toutefois, en production, seuls les produits approuvés sont retournés.   
   
 L' **ID d’application n’est pas explicite** : les ID d’application génériques (avec un astérisque) n’autorisent pas les achats dans l’application.   
   
 **Profil de provisionnement incorrect** : Si vous apportez des modifications à la configuration de votre application dans le portail de configuration (par exemple, en activant les achats dans l’application), n’oubliez pas de régénérer et d’utiliser le profil de provisionnement approprié lors de la création de l’application.   
   
 le **contrat d’applications payantes IOS n’est pas en place** : les fonctionnalités de StoreKit ne fonctionnent pas du tout, à moins qu’il existe un contrat valide pour votre compte de développeur Apple.   
   
 **L’état du fichier binaire est rejeté** . s’il existe déjà un fichier binaire soumis à l’État rejeté (par l’équipe de l’App Store ou par le développeur), les fonctionnalités StoreKit ne fonctionneront pas.

La méthode `ReceivedResponse` dans l’exemple de code génère les produits non valides dans la console :

```csharp
public override void ReceivedResponse (SKProductsRequest request, SKProductsResponse response)
{
   // code removed for clarity
   foreach (string invalidProductId in response.InvalidProducts) {
       Console.WriteLine("Invalid product id: " + invalidProductId );
   }
}
```

## <a name="displaying-localized-prices"></a>Affichage des prix localisés

Les niveaux de prix spécifient un tarif spécifique pour chaque produit dans l’ensemble des magasins d’applications internationaux. Pour vous assurer que les prix sont affichés correctement pour chaque devise, utilisez la méthode d’extension suivante (définie dans `SKProductExtension.cs`) plutôt que la propriété Price de chaque `SKProduct`:

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

L’utilisation de deux comptes de test iTunes différents (un pour l’American Store et un pour le magasin japonais) génère les captures d’écran suivantes :   

 [![](store-kit-overview-and-retreiving-product-information-images/image25.png "Two different iTunes test accounts showing language specific results")](store-kit-overview-and-retreiving-product-information-images/image25.png#lightbox)   

Notez que le magasin affecte la langue utilisée pour les informations sur le produit et la devise du prix, tandis que le paramètre de langue de l’appareil affecte les étiquettes et le contenu localisé.   

N’oubliez pas que pour utiliser un autre compte de test de magasin, vous devez vous **déconnecter** des **paramètres > iTunes et App Store** , puis redémarrez l’application pour vous connecter avec un autre compte. Pour changer la langue de l’appareil, accédez à **paramètres > général > International > Language**.
