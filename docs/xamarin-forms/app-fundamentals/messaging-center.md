---
title: Centre de messagerie Xamarin.Forms
description: Cet article explique comment utiliser le centre de messagerie Xamarin.Forms pour envoyer et recevoir des messages, afin de réduire le couplage entre les classes, telles que les modèles de vue.
ms.prod: xamarin
ms.assetid: EDFE7B19-C5FD-40D5-816C-FAE56532E885
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2016
ms.openlocfilehash: b40617dc9ed2054540ce04d5527fae8de6e2285b
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644900"
---
# <a name="xamarinforms-messagingcenter"></a>Centre de messagerie Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingmessagingcenter)

_Xamarin.Forms inclut un service de messagerie simple pour envoyer et recevoir des messages._

<a name="Overview" />

## <a name="overview"></a>Vue d'ensemble

Le `MessagingCenter` de Xamarin.Forms permet aux modèles de vue et aux autres composants de communiquer sans avoir aucun lien entre eux autre qu’un simple contrat de message.

<a name="How_the_MessagingCenter_Works" />

## <a name="how-the-messagingcenter-works"></a>Fonctionnement du centre de messagerie

`MessagingCenter` est constitué de deux parties :

-  **Subscribe** (Abonnement) : pour écouter les messages ayant une certaine signature et effectuer une action lors de leur réception. Plusieurs abonnés peuvent écouter le même message.
-  **Send** (Envoi) : pour publier un message sur lequel les écouteurs peuvent agir. Si aucun écouteur n’est abonné, le message est ignoré.

`MessagingCenter` est une classe statique qui comprend les méthodes `Subscribe` et `Send` qui sont utilisées dans l’ensemble de la solution.

Les messages ont un paramètre de chaîne `message` qui est utilisé pour *adresser* les messages. Les méthodes `Subscribe` et `Send` utilisent des paramètres génériques pour contrôler la façon dont les messages sont remis. Si deux messages comprennent le même texte `message` mais des arguments de type générique différents, ils ne seront pas remis au même abonné.

L’API pour `MessagingCenter` est simple :

- `Subscribe<TSender> (object subscriber, string message, Action<TSender> callback, TSender source = null)`
- `Subscribe<TSender, TArgs> (object subscriber, string message, Action<TSender, TArgs> callback, TSender source = null)`
- `Send<TSender> (TSender sender, string message)`
- `Send<TSender, TArgs> (TSender sender, string message, TArgs args)`
- `Unsubscribe<TSender, TArgs> (object subscriber, string message)`
- `Unsubscribe<TSender> (object subscriber, string message)`

Ces méthodes sont expliquées ci-dessous.

<a name="Using_the_MessagingCenter" />

## <a name="using-the-messagingcenter"></a>Utilisation du centre de messagerie

Les messages peuvent être envoyés après une interaction utilisateur (par exemple, un clic de bouton), un événement système (comme les contrôles qui changent d’état) ou un autre type d’événement (par exemple, un téléchargement asynchrone qui se termine). Les abonnés peuvent écouter pour modifier l’apparence de l’interface utilisateur, enregistrer des données ou déclencher une opération.

Pour plus d’informations sur l’utilisation de la classe `MessagingCenter`, consultez [Communication entre les composants faiblement couplés](~/xamarin-forms/enterprise-application-patterns/communicating-between-loosely-coupled-components.md).

### <a name="simple-string-message"></a>Message simple de type chaîne

Le message le plus simple comprend simplement une chaîne dans le paramètre `message`. Une méthode `Subscribe` qui *écoute* un message simple de type chaîne est illustrée ci-dessous. Notez que le type générique qui spécifie l’expéditeur est censé être de type `MainPage`. Toutes les classes de la solution peuvent s’abonner au message à l’aide de cette syntaxe :

```csharp
MessagingCenter.Subscribe<MainPage> (this, "Hi", (sender) => {
    // do something whenever the "Hi" message is sent
});
```

Dans la classe `MainPage`, le code suivant *envoie* le message. Le paramètre `this` est une instance de `MainPage`.

```csharp
MessagingCenter.Send<MainPage> (this, "Hi");
```

La chaîne ne change pas. Elle indique le *type de message* et est utilisée pour déterminer quels abonnés doivent recevoir une notification. Ce type de message est utilisé pour indiquer qu’un événement s’est produit (par exemple « chargement terminé »), et qu’aucune information supplémentaire n’est nécessaire.

### <a name="passing-an-argument"></a>Passage d’un argument

Pour passer un argument avec le message, spécifiez l’argument Type dans les arguments génériques `Subscribe` et dans la signature Action.

```csharp
MessagingCenter.Subscribe<MainPage, string> (this, "Hi", (sender, arg) => {
    // do something whenever the "Hi" message is sent
    // using the 'arg' parameter which is a string
});
```

Pour envoyer le message avec l’argument, incluez le paramètre générique Type et la valeur de l’argument dans l’appel de méthode `Send`.

```csharp
MessagingCenter.Send<MainPage, string> (this, "Hi", "John");
```

Cet exemple simple utilise un argument `string`. Toutefois, n’importe quel objet C# peut être passé.

### <a name="unsubscribe"></a>Se désabonner

Un objet peut se désabonner d’une signature de message pour qu’aucun autre message ne soit remis. La syntaxe de méthode `Unsubscribe` doit refléter la signature du message (par conséquent, vous devrez peut-être inclure le paramètre de type générique pour l’argument de message).

```csharp
MessagingCenter.Unsubscribe<MainPage> (this, "Hi");
MessagingCenter.Unsubscribe<MainPage, string> (this, "Hi");
```

<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Le centre de messagerie constitue un moyen simple de réduire le couplage, en particulier entre les modèles de vue. Il peut être utilisé pour envoyer et recevoir des messages simples ou pour passer un argument entre les classes. Les classes doivent se désinscrire des messages qu’elles ne souhaitent plus recevoir.


## <a name="related-links"></a>Liens associés

- [MessagingCenterSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingmessagingcenter)
- [Exemples Xamarin.Forms](https://github.com/xamarin/xamarin-forms-samples)
- [Communication entre les composants faiblement couplés](~/xamarin-forms/enterprise-application-patterns/communicating-between-loosely-coupled-components.md)
