---
title: Notifications dans Xamarin. Android
ms.prod: xamarin
ms.assetid: 2E54F1D0-45F4-43A7-B3A3-4F483B7150CB
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 5d91904e35a658b03d4602567e5a123cafd6926c
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025532"
---
# <a name="notifications-in-xamarinandroid"></a>Notifications dans Xamarin. Android

Cette section explique comment implémenter des notifications dans Xamarin. Android. Il décrit les différents éléments d’interface utilisateur d’une notification Android et présente les API impliquées dans la création et l’affichage d’une notification.

## <a name="local-notifications-in-androidlocal-notificationsmd"></a>[Notifications locales dans Android](local-notifications.md)

Cette section explique comment implémenter des notifications locales dans Xamarin. Android. Il décrit les différents éléments d’interface utilisateur d’une notification Android et discute des API impliquées dans la création et l’affichage d’une notification.

## <a name="walkthrough---using-local-notifications-in-xamarinandroidlocal-notifications-walkthroughmd"></a>[Procédure pas à pas : utilisation de notifications locales dans Xamarin. Android](local-notifications-walkthrough.md)  

Cette procédure pas à pas explique comment utiliser des notifications locales dans une application Xamarin. Android. Il montre les principes fondamentaux de la création et de la publication d’une notification. Quand l’utilisateur clique sur la notification dans le tiroir de notification, il démarre une deuxième activité. 

## <a name="further-reading"></a>Compléments de lecture

[Firebase Cloud messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) &ndash; Firebase Cloud Messaging (FCM) est un service qui facilite la messagerie entre les applications mobiles et les applications serveur. Firebase Cloud Messaging peut être utilisé pour implémenter des notifications distantes (également appelées notifications push) dans des applications Xamarin. Android.

[Notifications](https://developer.android.com/guide/topics/ui/notifiers/notifications.html) &ndash; cette rubrique de développeur Android est le guide définitif pour les notifications Android. Il comprend une section consacrée à la conception qui vous aide à concevoir vos notifications afin qu’elles soient conformes aux instructions de l’interface utilisateur Android. Il fournit des informations générales sur la navigation preserviing lors du démarrage d’une activité, et explique comment afficher la progression dans une notification et contrôler la lecture du média sur l’écran de verrouillage.

[NotificationListenerService](xref:Android.Service.Notification.NotificationListenerService) &ndash; ce service Android permet à votre application d’écouter (et d’interagir avec) toutes les notifications publiées sur l’appareil Android, et pas seulement les notifications que votre application est inscrite à recevoir.
Notez que l’utilisateur doit explicitement accorder à votre application l’autorisation d’écouter les notifications sur l’appareil.

## <a name="related-links"></a>Liens connexes

- [Notifications locales (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/localnotifications)
- [Notifications distantes (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/remotenotifications)
