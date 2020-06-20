---
title: Xamarin.FormsMessagingCenter
description: La Xamarin.Forms classe MessagingCenter implémente le modèle publication-abonnement, ce qui permet la communication basée sur les messages entre les composants qui sont peu pratiques à lier par objet et les références de type.
ms.prod: xamarin
ms.assetid: EDFE7B19-C5FD-40D5-816C-FAE56532E885
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/08/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d1407c3c948afe764854cbd99f5c29332f7041aa
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137538"
---
# <a name="xamarinforms-messagingcenter"></a>Xamarin.FormsMessagingCenter

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingmessagingcenter)

Le modèle publier-s’abonner est un modèle de messagerie dans lequel les serveurs de publication envoient des messages sans avoir connaissance des destinataires, appelés des abonnés. De même, les abonnés écoutent des messages spécifiques, sans avoir connaissance des serveurs de publication.

Les événements dans .NET implémentent le modèle de publication-abonnement, et constituent l’approche la plus simple et directe pour une couche de communication entre des composants si le couplage faible n’est pas requis, tel qu’un contrôle et la page qui le contient. Toutefois, les durées de vie du serveur de publication et de l’abonné sont associées par des références d’objet les unes aux autres, et le type d’abonné doit avoir une référence au type de serveur de publication. Cela peut créer des problèmes de gestion de la mémoire, en particulier lorsqu’il existe des objets éphémères qui s’abonnent à un événement d’un objet statique ou à long terme. Si le gestionnaire d’événements n’est pas supprimé, l’abonné est maintenu actif par la référence à celui-ci dans le serveur de publication, ce qui empêchera ou retarder la garbage collection de l’abonné.

La Xamarin.Forms [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) classe implémente le modèle de publication-abonnement, ce qui permet la communication basée sur les messages entre les composants qui sont peu pratiques à lier par objet et les références de type. Ce mécanisme permet aux serveurs de publication et aux abonnés de communiquer sans référence entre eux, ce qui contribue à réduire les dépendances entre eux.

La [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) classe fournit la fonctionnalité de publication-abonnement multidiffusion. Cela signifie que plusieurs serveurs de publication peuvent publient un seul message, et que plusieurs abonnés peuvent écouter le même message :

![](messaging-center-images/messaging-center.png "Multicast publish-subscribe functionality")

Les éditeurs envoient des messages à l’aide de la [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*) méthode, alors que les abonnés écoutent les messages à l’aide de la [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) méthode. En outre, les abonnés peuvent également se désabonner des abonnements aux messages, si nécessaire, avec la [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) méthode.

> [!IMPORTANT]
> En interne, la [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) classe utilise des références faibles. Cela signifie qu’elle ne conserve pas les objets actifs et leur permet d’être récupérés de la mémoire. Par conséquent, il ne doit être nécessaire de se désabonner d’un message que lorsqu’une classe ne souhaite plus recevoir le message.

## <a name="publish-a-message"></a>Publier un message

[`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter)les messages sont des chaînes. Les éditeurs notifient les abonnés d’un message avec l’une des [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*) surcharges. L’exemple de code suivant publie un message `Hi` :

```csharp
MessagingCenter.Send<MainPage>(this, "Hi");
```

Dans cet exemple, la [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) méthode spécifie un argument générique qui représente l’expéditeur. Pour recevoir le message, un abonné doit également spécifier le même argument générique, indiquant qu’il écoute un message de cet expéditeur. Cet exemple spécifie également deux arguments de méthode :

- Le premier argument spécifie l’instance de l’expéditeur.
- Le deuxième argument spécifie le message.

Les données de charge utile peuvent également être envoyées avec un message :

```csharp
MessagingCenter.Send<MainPage, string>(this, "Hi", "John");
```

Dans cet exemple, la [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) méthode spécifie deux arguments génériques. Le premier est le type qui envoie le message, tandis que le second est le type des données de charge utile envoyées. Pour recevoir le message, un abonné doit également spécifier les mêmes arguments génériques. Ainsi, plusieurs messages qui partagent une identité de message, mais qui envoient des types de données de charge utile différents, peuvent être reçus par différents abonnés. Cet exemple spécifie également un troisième argument de méthode qui représente les données de la charge utile à envoyer à l’abonné. Dans ce cas, les données de charge utile sont un `string`.

La [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) méthode publie le message, ainsi que toutes les données de charge utile, à l’aide d’une approche d’incendie et d’oubli. Le message est donc envoyé, même si aucun abonné n’est inscrit pour recevoir le message. Dans ce cas, le message envoyé est ignoré.

## <a name="subscribe-to-a-message"></a>S’abonner à un message

Les abonnés peuvent s’inscrire pour recevoir un message à l’aide de l’une des [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) surcharges. L’exemple de code ci-dessous en est un exemple :

```csharp
MessagingCenter.Subscribe<MainPage> (this, "Hi", (sender) =>
{
    // Do something whenever the "Hi" message is received
});
```

Dans cet exemple, la [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) méthode abonne l' `this` objet aux `Hi` messages envoyés par le `MainPage` type et exécute un délégué de rappel en réponse à la réception du message. Le délégué de rappel, spécifié sous forme d’expression lambda, peut être du code qui met à jour l’interface utilisateur, enregistre des données ou déclenche une autre opération.

> [!NOTE]
> Un abonné n’a peut-être pas besoin de gérer chaque instance d’un message publié, ce qui peut être contrôlé par les arguments de type générique spécifiés sur la [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) méthode.

L’exemple suivant montre comment s’abonner à un message qui contient des données de charge utile :

```csharp
MessagingCenter.Subscribe<MainPage, string>(this, "Hi", async (sender, arg) =>
{
    await DisplayAlert("Message received", "arg=" + arg, "OK");
});
```

Dans cet exemple, la [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) méthode s’abonne aux `Hi` messages envoyés par le `MainPage` type, dont les données de la charge utile sont `string` . Un délégué de rappel est exécuté en réponse à la réception d’un tel message, qui affiche les données de charge utile dans une alerte.

> [!IMPORTANT]
> Le délégué exécuté par la `Subscribe` méthode sera exécuté sur le thread qui publie le message à l’aide de la `Send` méthode.

## <a name="unsubscribe-from-a-message"></a>Se désabonner d’un message

Les abonnés peuvent se désinscrire des messages qu’ils ne souhaitent plus recevoir. Cela est possible avec l’une des [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) surcharges suivantes :

```csharp
MessagingCenter.Unsubscribe<MainPage>(this, "Hi");
```

Dans cet exemple, la [`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) méthode annule l’abonnement `this` de l’objet au `Hi` message envoyé par `MainPage` le type.

Les messages contenant des données de charge utile doivent être désabonnés de l’utilisation de la [`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) surcharge qui spécifie deux arguments génériques :

```csharp
MessagingCenter.Unsubscribe<MainPage, string>(this, "Hi");
```

Dans cet exemple, la [`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) méthode annule l’abonnement `this` de l’objet au `Hi` message envoyé par `MainPage` le type, dont les données de la charge utile sont `string` .

## <a name="related-links"></a>Liens connexes

- [MessagingCenterSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingmessagingcenter)
