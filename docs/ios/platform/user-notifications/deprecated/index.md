---
title: Technologies de notification déconseillées dans Xamarin. iOS
description: Ce document décrit les technologies de notification iOS qui ont été dépréciées en faveur de l’infrastructure de notifications utilisateur, introduite dans iOS 10.
ms.prod: xamarin
ms.assetid: 20C4F6E5-56DF-4A85-BBF0-E38C88586307
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/07/2016
ms.openlocfilehash: de9a46587a5d1de6f12dd54122b27e53694cdeb8
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031393"
---
# <a name="deprecated-notification-technologies-in-xamarinios"></a>Technologies de notification déconseillées dans Xamarin. iOS

Cette section montre comment implémenter des notifications push et locales dans Xamarin. iOS. Il explique les différents éléments d’interface utilisateur d’une notification iOS et discute des API impliquées dans la création et l’affichage d’une notification.

> [!IMPORTANT]
> Les informations contenues dans cette section concernent iOS 9 et les versions antérieures, elles ont été laissées ici pour prendre en charge les anciennes versions d’iOS. Pour iOS 10 et versions ultérieures, consultez le Guide de l' [infrastructure de notification utilisateur](~/ios/platform/user-notifications/index.md) pour prendre en charge la notification locale et distante sur un appareil iOS.

## <a name="sections"></a>Sections

<a name="Local Notifications In iOS" />

## <a name="local-notifications-in-ioslocal-notifications-in-iosmd"></a>[Notifications locales dans iOS](local-notifications-in-ios.md)

Cette section explique comment implémenter des notifications locales dans Xamarin. iOS. Il explique les différents éléments d’interface utilisateur d’une notification iOS et discute des API impliquées dans la création et l’affichage d’une notification.

<a name="Local Notifications Walkthrough" />

## <a name="walkthrough---using-local-notifications-in-xamarinioslocal-notifications-in-ios-walkthroughmd"></a>[Procédure pas à pas : utilisation des notifications locales dans Xamarin.iOS](local-notifications-in-ios-walkthrough.md)

Dans cette section, nous allons découvrir comment utiliser des notifications locales dans une application Xamarin. iOS. Il illustre les principes de base de la création et de la publication d’une notification qui contiendra une alerte lorsqu’elle sera reçue par l’application.

<a name="Remote Notifications In iOS" />

## <a name="remote-notifications-in-iosremote-notifications-in-iosmd"></a>[Notifications distantes dans iOS](remote-notifications-in-ios.md)

Cette section couvre les notifications push dans iOS. Il présente le service de passerelle de notifications push Apple (APNS) et le rôle qu’il joue pour la publication des notifications sur les applications iOS. Il explique comment créer les certificats de sécurité nécessaires pour activer les notifications push et les aborder. Enfin, cette section présente certaines des tâches de maintenance que les serveurs d’applications doivent effectuer pour effectuer le suivi des appareils mobiles clients.

## <a name="related-links"></a>Liens associés

- [Notifications (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/notifications)
