---
title: Communication entre les composants faiblement couplés
description: 'Ce chapitre explique comment l’application mobile eShopOnContainers implémente le modèle publication-abonnement, ce qui permet la communication basée sur les messages entre les composants qui sont peu pratiques à lier par objet et les références de type '
ms.prod: xamarin
ms.assetid: 1194af33-8a91-48d2-88b5-b84d77f2ce69
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c35cd6e30e7843cda0431581025aa7440a21cc29
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84140047"
---
# <a name="communicating-between-loosely-coupled-components"></a>Communication entre les composants faiblement couplés

Le modèle publier-s’abonner est un modèle de messagerie dans lequel les serveurs de publication envoient des messages sans avoir connaissance des destinataires, appelés des abonnés. De même, les abonnés écoutent des messages spécifiques, sans avoir connaissance des serveurs de publication.

Les événements dans .NET implémentent le modèle de publication-abonnement, et constituent l’approche la plus simple et directe pour une couche de communication entre des composants si le couplage faible n’est pas requis, tel qu’un contrôle et la page qui le contient. Toutefois, les durées de vie du serveur de publication et de l’abonné sont associées par des références d’objet les unes aux autres, et le type d’abonné doit avoir une référence au type de serveur de publication. Cela peut créer des problèmes de gestion de la mémoire, en particulier lorsqu’il existe des objets éphémères qui s’abonnent à un événement d’un objet statique ou à long terme. Si le gestionnaire d’événements n’est pas supprimé, l’abonné est maintenu actif par la référence à celui-ci dans le serveur de publication, ce qui empêchera ou retarder la garbage collection de l’abonné.

## <a name="introduction-to-messagingcenter"></a>Présentation de MessagingCenter

La Xamarin.Forms [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) classe implémente le modèle de publication-abonnement, ce qui permet la communication basée sur les messages entre les composants qui sont peu pratiques à lier par objet et les références de type. Ce mécanisme permet aux éditeurs et aux abonnés de communiquer sans avoir une référence entre eux, ce qui contribue à réduire les dépendances entre les composants, tout en permettant aux composants d’être développés et testés indépendamment.

La [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) classe fournit la fonctionnalité de publication-abonnement multidiffusion. Cela signifie qu’il peut y avoir plusieurs serveurs de publication qui publient un seul message, et il peut y avoir plusieurs abonnés qui écoutent le même message. La figure 4-1 illustre cette relation :

![](communicating-between-loosely-coupled-components-images/messagingcenter.png "Multicast publish-subscribe functionality")

**Figure 4-1 :** Fonctionnalité de publication/abonnement multidiffusion

Les éditeurs envoient des messages à l’aide de la [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*) méthode, alors que les abonnés écoutent les messages à l’aide de la [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) méthode. En outre, les abonnés peuvent également se désabonner des abonnements aux messages, si nécessaire, avec la [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) méthode.

En interne, la [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) classe utilise des références faibles. Cela signifie qu’elle ne conserve pas les objets actifs et leur permet d’être récupérés de la mémoire. Par conséquent, il ne doit être nécessaire de se désabonner d’un message que lorsqu’une classe ne souhaite plus recevoir le message.

L’application mobile eShopOnContainers utilise la [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) classe pour communiquer entre les composants faiblement couplés. L’application définit trois messages :

- Le `AddProduct` message est publié par la `CatalogViewModel` classe lorsqu’un élément est ajouté au panier d’achat. En retour, la `BasketViewModel` classe s’abonne au message et incrémente le nombre d’éléments dans le panier d’achat en réponse. En outre, la `BasketViewModel` classe se désabonne également de ce message.
- Le `Filter` message est publié par la `CatalogViewModel` classe lorsque l’utilisateur applique un filtre ou un filtre de type aux éléments affichés à partir du catalogue. En retour, la `CatalogView` classe s’abonne au message et met à jour l’interface utilisateur afin que seuls les éléments qui correspondent aux critères de filtre soient affichés.
- Le `ChangeTab` message est publié par la `MainViewModel` classe lorsque le `CheckoutViewModel` accède à la `MainViewModel` suite de la création et de l’envoi d’une nouvelle commande. En retour, la `MainView` classe s’abonne au message et met à jour l’interface utilisateur afin que l’onglet **mon profil** soit actif, afin d’afficher les commandes de l’utilisateur.

> [!NOTE]
> Bien que la [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) classe autorise la communication entre les classes faiblement couplées, elle n’offre pas la seule solution architecturale à ce problème. Par exemple, la communication entre un modèle de vue et une vue peut également être effectuée par le moteur de liaison et par le biais de notifications de modification de propriété. En outre, la communication entre deux modèles de vue peut également être obtenue en passant des données pendant la navigation.

Dans l’application mobile eShopOnContainers, [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) est utilisé pour effectuer une mise à jour dans l’interface utilisateur en réponse à une action qui se produit dans une autre classe. Par conséquent, les messages sont publiés sur le thread d’interface utilisateur, les abonnés recevant le message sur le même thread.

> [!TIP]
> Marshaler vers le thread d’interface utilisateur lors de l’exécution des mises à jour de l’interface utilisateur. Si un message envoyé à partir d’un thread d’arrière-plan est requis pour mettre à jour l’interface utilisateur, traitez le message sur le thread d’interface utilisateur de l’abonné en appelant la [`Device.BeginInvokeOnMainThread`](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) méthode.

Pour plus d’informations sur [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) , consultez [MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md).

## <a name="defining-a-message"></a>Définition d’un message

[`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter)les messages sont des chaînes utilisées pour identifier des messages. L’exemple de code suivant montre les messages définis dans l’application mobile eShopOnContainers :

```csharp
public class MessengerKeys  
{  
    // Add product to basket  
    public const string AddProduct = "AddProduct";  

    // Filter  
    public const string Filter = "Filter";  

    // Change selected Tab programmatically  
    public const string ChangeTab = "ChangeTab";  
}
```

Dans cet exemple, les messages sont définis à l’aide de constantes. L’avantage de cette approche est qu’elle assure la sécurité des types au moment de la compilation et la prise en charge de la refactorisation.

## <a name="publishing-a-message"></a>Publication d’un message

Les éditeurs notifient les abonnés d’un message avec l’une des [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*) surcharges. L’exemple de code suivant illustre la publication du `AddProduct` message :

```csharp
MessagingCenter.Send(this, MessengerKeys.AddProduct, catalogItem);
```

Dans cet exemple, la [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) méthode spécifie trois arguments :

- Le premier argument spécifie la classe sender. La classe sender doit être spécifiée par tous les abonnés qui souhaitent recevoir le message.
- Le deuxième argument spécifie le message.
- Le troisième argument spécifie les données de la charge utile à envoyer à l’abonné. Dans ce cas, les données de la charge utile sont une `CatalogItem` instance.

La [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) méthode publiera le message et ses données de charge utile à l’aide d’une approche d’incendie et d’oubli. Le message est donc envoyé, même si aucun abonné n’est inscrit pour recevoir le message. Dans ce cas, le message envoyé est ignoré.

> [!NOTE]
> La [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*) méthode peut utiliser des paramètres génériques pour contrôler la façon dont les messages sont remis. Par conséquent, plusieurs messages qui partagent une identité de message, mais qui envoient des types de données de charge utile différents peuvent être reçus par différents abonnés.

## <a name="subscribing-to-a-message"></a>Abonnement à un message

Les abonnés peuvent s’inscrire pour recevoir un message à l’aide de l’une des [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) surcharges. L’exemple de code suivant montre comment l’application mobile eShopOnContainers s’abonne à, et traite, le `AddProduct` message :

```csharp
MessagingCenter.Subscribe<CatalogViewModel, CatalogItem>(  
    this, MessageKeys.AddProduct, async (sender, arg) =>  
{  
    BadgeCount++;  

    await AddCatalogItemAsync(arg);  
});
```

Dans cet exemple, la [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) méthode s’abonne au `AddProduct` message et exécute un délégué de rappel en réponse à la réception du message. Ce délégué de rappel, spécifié en tant qu’expression lambda, exécute le code qui met à jour l’interface utilisateur.

> [!TIP]
> Envisagez d’utiliser des données de charge utile immuables. N’essayez pas de modifier les données de la charge utile à partir d’un délégué de rappel, car plusieurs threads peuvent accéder simultanément aux données reçues. Dans ce scénario, les données de la charge utile doivent être immuables pour éviter les erreurs d’accès concurrentiel.

Un abonné n’a peut-être pas besoin de gérer chaque instance d’un message publié, ce qui peut être contrôlé par les arguments de type générique spécifiés sur la [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) méthode. Dans cet exemple, l’abonné reçoit uniquement les `AddProduct` messages envoyés à partir de la `CatalogViewModel` classe, dont les données de la charge utile sont une `CatalogItem` instance.

## <a name="unsubscribing-from-a-message"></a>Annulation d’un abonnement à un message

Les abonnés peuvent se désinscrire des messages qu’ils ne souhaitent plus recevoir. Cela est possible avec l’une des [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) surcharges, comme illustré dans l’exemple de code suivant :

```csharp
MessagingCenter.Unsubscribe<CatalogViewModel, CatalogItem>(this, MessengerKeys.AddProduct);
```

Dans cet exemple, la [`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) syntaxe de la méthode reflète les arguments de type spécifiés lors de l’abonnement pour recevoir le `AddProduct` message.

## <a name="summary"></a>Résumé

La Xamarin.Forms [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) classe implémente le modèle de publication-abonnement, ce qui permet la communication basée sur les messages entre les composants qui sont peu pratiques à lier par objet et les références de type. Ce mécanisme permet aux éditeurs et aux abonnés de communiquer sans avoir une référence entre eux, ce qui contribue à réduire les dépendances entre les composants, tout en permettant aux composants d’être développés et testés indépendamment.

## <a name="related-links"></a>Liens connexes

- [Télécharger le livre électronique (PDF de 2 Mo)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (exemple)](https://github.com/dotnet-architecture/eShopOnContainers)
