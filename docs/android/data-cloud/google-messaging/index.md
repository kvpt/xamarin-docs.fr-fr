---
title: Messagerie Google
description: Cette section contient des guides qui décrivent comment implémenter des applications Xamarin. Android à l’aide des services de messagerie Google.
ms.prod: xamarin
ms.assetid: 85E8DF92-D160-4763-A7D3-458B4C31635F
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/12/2018
ms.openlocfilehash: e0d257cd5d443c77fd0a8132d9cd31ec572182aa
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73023532"
---
# <a name="google-messaging"></a>Messagerie Google

_Cette section contient des guides qui décrivent comment implémenter des applications Xamarin. Android à l’aide des services de messagerie Google._

## <a name="firebase-cloud-messagingfirebase-cloud-messagingmd"></a>[Messagerie cloud Firebase](firebase-cloud-messaging.md)

Firebase Cloud Messaging (FCM) est un service qui facilite la messagerie entre les applications mobiles et les applications serveur. FCM est le successeur de Google pour Google Cloud Messaging. Cet article fournit une vue d’ensemble du fonctionnement de FCM et fournit une procédure pas-à-pas pour acquérir des informations d’identification afin que votre application puisse utiliser les services FCM.

## <a name="remote-notifications-with-firebase-cloud-messagingremote-notifications-with-fcmmd"></a>[Notifications distantes avec Firebase Cloud Messaging](remote-notifications-with-fcm.md)

Cette procédure pas à pas fournit une explication pas à pas de l’utilisation de Firebase Cloud Messaging pour implémenter des notifications distantes (également appelées notifications push) dans une application Xamarin. Android. Il illustre comment implémenter les différentes classes nécessaires pour les communications avec Firebase Cloud Messaging (FCM), fournit des exemples de configuration du manifeste Android pour l’accès à FCM et illustre la messagerie en aval à l’aide de Firebase Console.

## <a name="google-cloud-messaginggoogle-cloud-messagingmd"></a>[Messagerie cloud Google](google-cloud-messaging.md)

Cette section fournit une vue d’ensemble de la façon dont Google Cloud Messaging (GCM) route les messages entre votre application et un serveur d’applications, et fournit une procédure pas-à-pas pour acquérir des informations d’identification afin que votre application puisse utiliser les services GCM. (Notez que GCM a été remplacé par FCM.)

> [!NOTE]
> GCM a été remplacé par [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) (FCM).
> Les API de serveur et de client GCM [sont dépréciées](https://firebase.googleblog.com/2018/04/time-to-upgrade-from-gcm-to-fcm.html) et ne seront plus disponibles dès le 11 avril 2019.

## <a name="remote-notifications-with-google-cloud-messagingremote-notifications-with-gcmmd"></a>[Notifications distantes avec Google Cloud Messaging](remote-notifications-with-gcm.md)

Cette section fournit une explication pas à pas de la façon d’implémenter des notifications distantes dans Xamarin. Android à l’aide de Google Cloud Messaging.
Il explique les différents composants qui doivent être exploités pour permettre l’Google Cloud Messaging dans une application Android.
