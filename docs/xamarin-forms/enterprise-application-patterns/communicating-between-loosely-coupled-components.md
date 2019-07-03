---
title: Communication entre les composants faiblement couplés
description: 'Ce chapitre explique comment l’application mobile eShopOnContainers implémente la publier-s’abonner de modèle, autorisant basée sur message de communication entre les composants qui n’est pas pratique lier par des références d’objet et le type '
ms.prod: xamarin
ms.assetid: 1194af33-8a91-48d2-88b5-b84d77f2ce69
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 9848d2b832990032bc7eb7f2e3a93c896457134c
ms.sourcegitcommit: e95296f9e516975f5f32d822c323a71fd84007b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538694"
---
# <a name="communicating-between-loosely-coupled-components"></a>Communication entre les composants faiblement couplés

La publication-abonnement modèle est un modèle de messagerie dans lequel les éditeurs envoient des messages sans avoir connaissance de n’importe quel récepteurs, appelés abonnés. De même, les abonnés écoutent les messages spécifiques, sans avoir connaissance d’aucun serveur de publication.

Événements dans .NET implémentent la publier-s’abonner de modèle, et sont le plus simple et directe approche pour une couche de communication entre les composants si le couplage n’est pas requis, par exemple, un contrôle et la page qui la contient. Toutefois, les durées de vie de serveur de publication et l’abonné sont associées par des références d’objet entre eux, et le type de l’abonné doit avoir une référence au type de serveur de publication. Cela peut créer des problèmes de gestion, de mémoire en particulier lorsqu’il existe des objets à courte qui s’abonnent à un événement d’un objet statique ou durable. Si le Gestionnaire d’événements ne sont pas supprimé, l’abonné reste actif par la référence à celui-ci dans le serveur de publication, et ceci empêcher ou différer le garbage collection de l’abonné.

## <a name="introduction-to-messagingcenter"></a>Introduction à MessagingCenter

Xamarin.Forms [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) classe implémente la publier-s’abonner de modèle, autorisant basée sur message de communication entre les composants qui n’est pas pratique lier par des références d’objet et le type. Ce mécanisme permet des éditeurs et les abonnés de communiquer sans avoir une référence à d’autres, qui contribue à réduire les dépendances entre les composants, tout en autorisant également les composants soient indépendamment développées et testées.

Le [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) classe fournit la multidiffusion de publication-abonnement fonctionnalités. Cela signifie qu’il peut y avoir plusieurs serveurs de publication qui publient un seul message et qu’il peut y avoir plusieurs abonnés à l’écoute pour le même message. Figure 4-1 illustre cette relation :

![](communicating-between-loosely-coupled-components-images/messagingcenter.png "Multidiffusion de publication-abonnement fonctionnalités")

**Figure 4-1 :** Multidiffusion de publication-abonnement fonctionnalités

Les éditeurs envoient des messages à l’aide de la [ `MessagingCenter.Send` ](xref:Xamarin.Forms.MessagingCenter.Send*) (méthode), tandis que les abonnés écoutent les messages à l’aide de la [ `MessagingCenter.Subscribe` ](xref:Xamarin.Forms.MessagingCenter.Subscribe*) (méthode). En outre, les abonnés peuvent également annuler votre abonnement à partir des abonnements aux messages, si nécessaire, avec le [ `MessagingCenter.Unsubscribe` ](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) (méthode).

En interne, le [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) classe utilise des références faibles. Cela signifie qu’il ne garde pas les objets actifs et leur permettra d’être nettoyées. Par conséquent, il doit uniquement être nécessaire de se désabonner d’un message quand une classe ne souhaite plus recevoir le message.

L’application mobile d’eShopOnContainers utilise la [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) composants de classe pour la communication entre faiblement couplés. L’application définit trois messages :

-   Le `AddProduct` message est publié par le `CatalogViewModel` classe lorsqu’un élément est ajouté au panier d’achat. En retour, le `BasketViewModel` classe s’abonne au message et incrémente le nombre d’éléments dans le panier d’achat en réponse. En outre, la `BasketViewModel` classe annule également l’abonnement à partir de ce message.
-   Le `Filter` message est publié par le `CatalogViewModel` classe lorsque l’utilisateur applique un filtre de type ou de marque pour les éléments affichés dans le catalogue. En retour, le `CatalogView` classe s’abonne au message et met à jour de l’interface utilisateur afin que seuls les éléments qui correspondent aux critères de filtre sont affichés.
-   Le `ChangeTab` message est publié par le `MainViewModel` classe lorsque les `CheckoutViewModel` accède à la `MainViewModel` suivant la création réussie et la soumission d’une nouvelle commande. En retour, le `MainView` classe s’abonne à du message et de mises à jour l’interface utilisateur ainsi que la **mon profil** onglet est actif, pour afficher les commandes de l’utilisateur.

> [!NOTE]
> Bien que le [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) classe autorise les communications entre les classes faiblement couplées, elle n’offre pas la solution d’architecture uniquement à ce problème. Par exemple, la communication entre un modèle de vue et une vue est également possible par le moteur de liaison et via les notifications de modification de propriété. En outre, la communication entre les deux modèles de vue également est possible en passant les données lors de la navigation.

Dans l’application mobile eShopOnContainers, [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) est utilisé pour mettre à jour dans l’interface utilisateur en réponse à une action qui se produisent dans une autre classe. Par conséquent, les messages sont publiés sur le thread d’interface utilisateur, avec les abonnés qui reçoivent le message sur le même thread.

> [!TIP]
> Marshaler le thread d’interface utilisateur lors de l’exécution de l’interface utilisateur met à jour. Si un message est envoyé à partir d’un thread d’arrière-plan est nécessaire pour mettre à jour de l’interface utilisateur, traiter le message sur le thread d’interface utilisateur dans l’abonné en appelant le [ `Device.BeginInvokeOnMainThread` ](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) (méthode).

Pour plus d’informations sur [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter), consultez [MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md).

## <a name="defining-a-message"></a>Définition d’un Message

[`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) les messages sont des chaînes qui sont utilisées pour identifier les messages. L’exemple de code suivant montre les messages définis dans l’application mobile eShopOnContainers :

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

Dans cet exemple, les messages sont définis à l’aide de constantes. L’avantage de cette approche est qu’elle fournit la sécurité de type de compilation et la prise en charge la refactorisation.

## <a name="publishing-a-message"></a>Publication d’un Message

Les serveurs de publication notifier les abonnés d’un message avec l’un de le [ `MessagingCenter.Send` ](xref:Xamarin.Forms.MessagingCenter.Send*) surcharges. L’exemple de code suivant montre la publication la `AddProduct` message :

```csharp
MessagingCenter.Send(this, MessengerKeys.AddProduct, catalogItem);
```

Dans cet exemple, le [ `Send` ](xref:Xamarin.Forms.MessagingCenter.Send*) méthode spécifie trois arguments :

-   Le premier argument spécifie la classe de l’expéditeur. La classe de l’expéditeur doit être spécifiée par les abonnés qui souhaitent recevoir le message.
-   Le deuxième argument spécifie le message.
-   Le troisième argument spécifie les données de charge utile à envoyer à l’abonné. Dans ce cas les données de la charge utile sont un `CatalogItem` instance.

Le [ `Send` ](xref:Xamarin.Forms.MessagingCenter.Send*) méthode publie le message et ses données de charge utile, en utilisant une approche « fire-et-forget ». Par conséquent, le message est envoyé même si aucun abonné inscrit pour recevoir le message. Dans ce cas, le message envoyé est ignoré.

> [!NOTE]
> Le [ `MessagingCenter.Send` ](xref:Xamarin.Forms.MessagingCenter.Send*) méthode peut utiliser des paramètres génériques pour contrôler la façon dont les messages sont remis. Par conséquent, plusieurs messages qui partagent une identité de message, mais envoyer les types de données de charge utile différente peuvent être reçus par différents abonnés.

## <a name="subscribing-to-a-message"></a>S’abonner à un Message

Abonnés peuvent s’inscrire pour recevoir un message à l’aide d’un de le [ `MessagingCenter.Subscribe` ](xref:Xamarin.Forms.MessagingCenter.Subscribe*) surcharges. L’exemple de code suivant montre comment l’application mobile eShopOnContainers s’abonne à et traite, le `AddProduct` message :

```csharp
MessagingCenter.Subscribe<CatalogViewModel, CatalogItem>(  
    this, MessageKeys.AddProduct, async (sender, arg) =>  
{  
    BadgeCount++;  

    await AddCatalogItemAsync(arg);  
});
```

Dans cet exemple, le [ `Subscribe` ](xref:Xamarin.Forms.MessagingCenter.Subscribe*) méthode s’abonne à la `AddProduct` du message et exécute un délégué de rappel en réponse à la réception du message. Ce délégué de rappel, spécifié comme une expression lambda, exécute le code qui met à jour de l’interface utilisateur.

> [!TIP]
> Envisagez d’utiliser des données de charge utile immuable. N’essayez pas de modifier les données de charge utile à partir d’un délégué de rappel parce que plusieurs threads Impossible d’accéder simultanément aux données reçues. Dans ce scénario, les données de charge utile doivent être immuables pour éviter les erreurs d’accès concurrentiel.

Un abonné n’est peut-être pas nécessaire de gérer chaque instance d’un message publié, et cela peut être contrôlé par les arguments de type générique sont spécifiés dans le [ `Subscribe` ](xref:Xamarin.Forms.MessagingCenter.Subscribe*) (méthode). Dans cet exemple, l’abonné reçoit uniquement `AddProduct` les messages envoyés à partir de la `CatalogViewModel` (classe), dont les données charge utile sont un `CatalogItem` instance.

## <a name="unsubscribing-from-a-message"></a>Annulation d’un Message

Les abonnés peuvent vous désabonner qu’ils ne souhaitent plus recevoir de messages. Cela est possible avec un de le [ `MessagingCenter.Unsubscribe` ](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) surcharges, comme illustré dans l’exemple de code suivant :

```csharp
MessagingCenter.Unsubscribe<CatalogViewModel, CatalogItem>(this, MessengerKeys.AddProduct);
```

Dans cet exemple, le [ `Unsubscribe` ](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) syntaxe de méthode reflète les arguments de type spécifiés lors de l’abonnement pour recevoir le `AddProduct` message.

## <a name="summary"></a>Récapitulatif

Xamarin.Forms [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) classe implémente la publier-s’abonner de modèle, autorisant basée sur message de communication entre les composants qui n’est pas pratique lier par des références d’objet et le type. Ce mécanisme permet des éditeurs et les abonnés de communiquer sans avoir une référence à d’autres, qui contribue à réduire les dépendances entre les composants, tout en autorisant également les composants soient indépendamment développées et testées.


## <a name="related-links"></a>Liens associés

- [Téléchargez le livre électronique (PDF de 2 Mo)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
