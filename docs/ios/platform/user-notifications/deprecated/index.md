---
title: Technologies de Notification déconseillées dans Xamarin.iOS
description: Ce document décrit les technologies de notification iOS qui ont été déconseillées en faveur de l’infrastructure de Notifications à l’utilisateur, introduite dans iOS 10.
ms.prod: xamarin
ms.assetid: 20C4F6E5-56DF-4A85-BBF0-E38C88586307
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/07/2016
ms.openlocfilehash: 63134298e437e7ac9b99ac4d716f6265752651c3
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865686"
---
# <a name="deprecated-notification-technologies-in-xamarinios"></a>Technologies de Notification déconseillées dans Xamarin.iOS

Cette section montre comment implémenter local et les notifications push dans Xamarin.iOS. Il explique les différents éléments d’interface utilisateur d’une notification d’iOS et traitent de l’API d’impliquée dans la création et affichage d’une notification.

> [!IMPORTANT]
> Les informations contenues dans cette section se rapporte à iOS 9 et antérieures, il a été laissé ici pour prendre en charge les anciennes versions d’iOS. Pour iOS 10 et versions ultérieures, consultez le [guide de l’infrastructure de Notification utilisateur](~/ios/platform/user-notifications/index.md) pour prendre en charge locale et une Notification à distance sur un appareil iOS.

## <a name="sections"></a>Sections

<a name="Local Notifications In iOS" />

## <a name="local-notifications-in-ioslocal-notifications-in-iosmd"></a>[Notifications locales dans iOS](local-notifications-in-ios.md)

Cette section explique comment implémenter des notifications locales dans Xamarin.iOS. Il explique les différents éléments d’interface utilisateur d’une notification d’iOS et traitent de l’API d’impliquée dans la création et affichage d’une notification.

<a name="Local Notifications Walkthrough" />

## <a name="walkthrough---using-local-notifications-in-xamarinioslocal-notifications-in-ios-walkthroughmd"></a>[Procédure pas à pas : utilisation des notifications locales dans Xamarin.iOS](local-notifications-in-ios-walkthrough.md)

Dans cette section, nous allons via l’utilisation des notifications locales dans une application Xamarin.iOS. Il va vous montrer les principes fondamentaux de création et publication d’une notification s’affiche une alerte lors de la réception par l’application.

<a name="Remote Notifications In iOS" />

## <a name="remote-notifications-in-iosremote-notifications-in-iosmd"></a>[Notifications à distance dans iOS](remote-notifications-in-ios.md)

Cette section décrit les notifications push dans iOS. Il introduit le Apple Push Notifications passerelle de Service (APNS) et le rôle qu’il joue dans la publication des notifications à des applications iOS. Il explique comment créer les certificats de sécurité nécessaires pour activer les notifications push et en discuter. Enfin cette section décrit certaines des tâches de gestion interne que les serveurs d’applications doivent effectuer pour assurer le suivi des périphériques mobiles clients.

## <a name="related-links"></a>Liens associés

- [Notifications (exemple)](https://developer.xamarin.com/samples/monotouch/Notifications/)
