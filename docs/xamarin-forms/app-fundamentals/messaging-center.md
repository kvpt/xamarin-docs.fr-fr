---
title: Centre de messagerie Xamarin.Forms
description: La classe Centre de messagerie Xamarin.Forms implémente le modèle publier-s’abonner, permettant une communication basée sur les messages entre les composants qui sont peu pratiques à lier par références d’objet et de type.
ms.prod: xamarin
ms.assetid: EDFE7B19-C5FD-40D5-816C-FAE56532E885
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/30/2019
ms.openlocfilehash: a4d246419c7449c2395759cf5a8b04469e7a2309
ms.sourcegitcommit: 266e75fa6893d3732e4e2c0c8e79c62be2804468
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68821015"
---
# <a name="xamarinforms-messagingcenter"></a>Centre de messagerie Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingmessagingcenter)

Le modèle publier-s’abonner est un modèle de messagerie dans lequel les serveurs de publication envoient des messages sans avoir connaissance des destinataires, appelés des abonnés. De même, les abonnés écoutent des messages spécifiques, sans avoir connaissance des serveurs de publication.

La classe [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) Xamarin.Forms implémente le modèle publier-s’abonner, permettant une communication basée sur les messages entre les composants qui sont peu pratiques à lier par références d’objet et de type. Ce mécanisme permet aux serveurs de publication et aux abonnés de communiquer sans référence entre eux, ce qui contribue à réduire les dépendances entre eux.

La classe [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) fournit la fonctionnalité publier-s’abonner de multidiffusion. Cela signifie que plusieurs serveurs de publication peuvent publient un seul message, et que plusieurs abonnés peuvent écouter le même message :

![](messaging-center-images/messaging-center.png "Fonctionnalité publier-s’abonner de multidiffusion")

Les serveurs de publication envoient des messages à l’aide de la méthode [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*), alors que les abonnés écoutent les messages à l’aide de la méthode [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*). En outre, les abonnés peuvent également se désabonner des abonnements aux messages, si nécessaire, avec la méthode [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*).

> [!IMPORTANT]
> En interne, la classe [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) utilise des références faibles. Cela signifie qu’elle ne conserve pas les objets actifs et leur permet d’être récupérés de la mémoire. Par conséquent, il ne doit être nécessaire de se désabonner d’un message que lorsqu’une classe ne souhaite plus recevoir le message.

## <a name="publish-a-message"></a>Publier un message

Les messages [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) sont des chaînes. Les serveurs de publication informent les abonnés d’un message avec l’une des surcharges [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*). L’exemple de code suivant publie un message `Hi` :

```csharp
MessagingCenter.Send<MainPage>(this, "Hi");
```

Dans cet exemple, la méthode [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) spécifie un argument générique qui représente l’expéditeur. Pour recevoir le message, un abonné doit également spécifier le même argument générique, indiquant qu’il écoute un message de cet expéditeur. Cet exemple spécifie également deux arguments de méthode :

- Le premier argument spécifie l’instance de l’expéditeur.
- Le deuxième argument spécifie le message.

Les données de charge utile peuvent également être envoyées avec un message :

```csharp
MessagingCenter.Send<MainPage, string>(this, "Hi", "John");
```

Dans cet exemple, la méthode [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) spécifie deux arguments génériques. Le premier est le type qui envoie le message, tandis que le second est le type des données de charge utile envoyées. Pour recevoir le message, un abonné doit également spécifier les mêmes arguments génériques. Ainsi, plusieurs messages qui partagent une identité de message, mais qui envoient des types de données de charge utile différents, peuvent être reçus par différents abonnés. Cet exemple spécifie également un troisième argument de méthode qui représente les données de la charge utile à envoyer à l’abonné. Dans ce cas, les données de charge utile sont un `string`.

La méthode [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) publie le message, ainsi que toutes les données de charge utile, à l’aide d’une approche « Fire and Forget ». Le message est donc envoyé, même si aucun abonné n’est inscrit pour recevoir le message. Dans ce cas, le message envoyé est ignoré.

## <a name="subscribe-to-a-message"></a>S’abonner à un message

Les abonnés peuvent s’abonner pour recevoir un message à l’aide de l’une des surcharges [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*). L’exemple de code ci-dessous en est un exemple :

```csharp
MessagingCenter.Subscribe<MainPage> (this, "Hi", (sender) =>
{
    // Do something whenever the "Hi" message is received
});
```

Dans cet exemple, la méthode [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) abonne l’objet `this` aux messages `Hi` envoyés par le type `MainPage` et exécute un délégué de rappel en réponse à la réception du message. Le délégué de rappel, spécifié sous forme d’expression lambda, peut être du code qui met à jour l’interface utilisateur, enregistre des données ou déclenche une autre opération.

> [!NOTE]
> Un abonné n’a peut-être pas besoin de gérer chaque instance d’un message publié, ce qui peut être contrôlé par les arguments de type générique spécifiés sur la méthode [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*).

L’exemple suivant montre comment s’abonner à un message qui contient des données de charge utile :

```csharp
MessagingCenter.Subscribe<MainPage, string>(this, "Hi", async (sender, arg) =>
{
    await DisplayAlert("Message received", "arg=" + arg, "OK");
});
```

Dans cet exemple, la méthode [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) abonne aux messages `Hi` envoyés par le type `MainPage`, dont les données de charge utile sont un `string`. Un délégué de rappel est exécuté en réponse à la réception d’un tel message, qui affiche les données de charge utile dans une alerte.

## <a name="unsubscribe-from-a-message"></a>Se désabonner d’un message

Les abonnés peuvent se désinscrire des messages qu’ils ne souhaitent plus recevoir. Cela est possible avec l’une des surcharges [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) :

```csharp
MessagingCenter.Unsubscribe<MainPage>(this, "Hi");
```

Dans cet exemple, la méthode [`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) désabonne l’objet `this` du message `Hi` envoyé par le type `MainPage`.

Les messages contenant des données de charge utile doivent être désabonnés de l’utilisation de la surcharge [`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) qui spécifie deux arguments génériques :

```csharp
MessagingCenter.Unsubscribe<MainPage, string>(this, "Hi");
```

Dans cet exemple, la méthode [`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) désabonne l’objet `this` du message `Hi` envoyé par le type `MainPage`, dont les données de charge utile sont un `string`.

## <a name="related-links"></a>Liens connexes

- [MessagingCenterSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingmessagingcenter)
