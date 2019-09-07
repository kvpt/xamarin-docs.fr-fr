---
title: watchOS Notifications dans Xamarin
description: Ce document décrit comment utiliser des notifications de watchOS dans Xamarin. Il aborde la création de notification contrôleurs, générer des notifications et test des notifications.
ms.prod: xamarin
ms.assetid: 0BC1306E-0713-4592-996E-7530CCF281E7
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: ae6a4fb45eb53c514c888d671780a5ceaeba6624
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768611"
---
# <a name="watchos-notifications-in-xamarin"></a>watchOS Notifications dans Xamarin

Regardez les applications peuvent recevoir des notifications si l’application iOS contenant les prend en charge. Il est intégrée notification vous donc pas de gestion des *devez* pour ajouter la prise en charge de la notification supplémentaire décrite ci-dessous, cependant si vous souhaitez personnaliser le comportement de notification et d’apparence, lisez la suite.

Reportez-vous à la [Notifications iOS](~/ios/platform/user-notifications/deprecated/index.md) doc pour plus d’informations sur l’ajout de prise en charge de la notification à l’application iOS dans votre solution.

## <a name="creating-notification-controllers"></a>Création de contrôleurs de Notification

Sur le plan conceptuel contrôleurs de notifications ont un type spécial de segue leur déclenchement. Lorsque vous faites glisser un nouveau **contrôleur d’Interface de Notification** sur une table de montage séquentiel, elle aura automatiquement un segue attaché :

![](notifications-images/notification-storyboard1.png "Un nouveau contrôleur d’Interface de Notification avec un segue attaché")

Lorsque la notification segue est sélectionnée. vous pouvez modifier ses propriétés :

![](notifications-images/notification-storyboard2.png "La notification segue sélectionné")

Une fois que vous avez personnalisé le contrôleur, il peut ressembler à cet exemple à partir de la WatchKitCatalog :

![](notifications-images/notifications-segue.png "Les propriétés de Notification")

Il existe deux types de notification :

- Affichage statique de type « non défilant » **concis** défini par le système.

- **Présentation de long** - déroulable, vue personnalisable définie par vous ! Une version plus simple, statique et une version dynamique plus complexe peuvent être spécifiés.

### <a name="short-look-notification-controller"></a>Contrôleur de Notification de courte-aperçu

L’interface utilisateur courte apparence se compose de l’icône d’application, nom de l’application et la chaîne de titre de notification.

Si l’utilisateur n’ignore pas la notification, le système passe automatiquement à une notification de l’aspect de longue durée qui fournit plus d’informations.

### <a name="long-look-notification-controller"></a>Contrôleur de Notification de l’aspect de longue durée

Le système d’exploitation décide s’il faut afficher la vue statique ou dynamique selon un certain nombre de facteurs. Vous devez fournir une interface statique et peut éventuellement inclure également une interface dynamique pour les notifications.

#### <a name="static"></a>Statique

La vue statique doit être simple et rapide à afficher.

![](notifications-images/notification-static.png "La vue statique")

#### <a name="dynamic"></a>dynamique

La vue dynamique peut afficher plus de données et fournir plus d’interactivité.

![](notifications-images/notification-dynamic.png "L’affichage dynamique")

## <a name="generating-notifications"></a>Génération des Notifications

Notifications peuvent provenir d’un serveur distant ([Service de Notifications Push Apple](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html), ou APNS) ou peut être généré localement dans l’application iOS.

Reportez-vous à la [iOS procédure pas à pas de Notifications](~/ios/platform/user-notifications/deprecated/local-notifications-in-ios-walkthrough.md) pour obtenir un exemple montrant comment générer des notifications locales et le [WatchNotifications exemple](https://docs.microsoft.com/samples/xamarin/ios-samples/watchkit-watchnotifications) pour obtenir un exemple.

Notifications locales doivent avoir le `AlertTitle` définie pour être affichée sur l’Apple Watch - le `AlertTitle` chaîne est affichée dans l’interface courte-coup de œil. À la fois le `AlertTitle` et `AlertBody` sont affichés dans la liste de notifications ; et le `AlertBody` est affichée dans l’interface de l’aspect de longue durée.

Cette capture d’écran montre le `AlertTitle` affiché dans la liste de notifications et le `AlertBody` affichée dans l’interface de l’aspect de longue durée (à l’aide de la [exemple de code](https://docs.microsoft.com/samples/xamarin/ios-samples/watchkit-watchnotifications)) :

![](notifications-images/watch-notificationslist-sml.png "Cette capture d’écran montre le AlertTitle affiché dans la liste de notifications") ![](notifications-images/watch-notificationcontroller-sml.png "AlertBody l’affichée dans l’interface de l’aspect de longue durée")

## <a name="testing-notifications"></a>Test des Notifications

Notifications (locales et distantes) peuvent uniquement être correctement testées sur un appareil, mais ils peuvent être simulés à l’aide un **.json** fichier dans le simulateur iOS.

### <a name="testing-on-apple-watch"></a>Tests sur Apple Watch

Lorsque vous testez les notifications sur une Apple Watch, n’oubliez pas que [documentation d’Apple](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/BasicSupport.html) indique ce qui suit :

> Lorsqu’une des notifications de local ou distant de votre application arrive sur iPhone de l’utilisateur, iOS décide s’il faut afficher cette notification sur l’iPhone ou sur l’Apple Watch.

Cela est alluding au fait que l’option iOS décide si une notification s’affichera sur l’iPhone ou sur la surveillance. Si l’iPhone apparié est actif lorsqu’une notification est reçue, la notification est susceptible d’être affiché sur l’iPhone et *pas* acheminé vers la montre.

Pour garantir que la notification s’affiche sur la surveillance, désactivez l’écran de l’iPhone (appuyant une seule fois sur le bouton d’alimentation) ou laissez-le se mettre en veille. Si le Watch jumelée est dans la plage, dispose d’une puissance et est portée sur votre poignet, la notification sera routé il et apparaissent sur l’observation (accompagnée d’un subtiles).

### <a name="testing-on-the-ios-simulator"></a>Test sur le simulateur iOS

Vous *doit* fournir une charge utile JSON de test lorsque vous testez le mode de notification dans le simulateur iOS. Définir le chemin d’accès dans le **Arguments d’exécution personnalisé** fenêtre dans Visual Studio pour Mac.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Visual Studio pour Mac affiche des options supplémentaires quand une extension watch est définie comme le **projet de démarrage**.
Avec le bouton droit sur le projet d’extension watch et choisissez **exécuter avec > les paramètres personnalisés...** :

[![](notifications-images/runwith-customparams-sml.png "En cours d’exécution avec des propriétés personnalisées")](notifications-images/runwith-customparams.png#lightbox)

Cette opération ouvre le **Arguments d’exécution** fenêtre qui contient un **WatchKit** onglet. Sélectionnez **Notification** et fournir une charge utile JSON, puis appuyez sur **Execute** pour démarrer l’application de surveillance dans le simulateur :

[![](notifications-images/runwith-execargs-sml.png "Sélectionnez la valeur par défaut de charge utile de Notification")](notifications-images/runwith-execargs.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Pour définir la charge utile de notification test dans avec le bouton droit de Visual Studio sur l’extension watch pour modifier le **propriétés de projet**. Accédez à la **déboguer** section et sélectionnez un fichier JSON de notifications à partir de la liste (elle sera automatiquement tous les fichiers JSON inclus dans le projet).

[![](notifications-images/runwith-execargs-sml-vs.png "Sélectionnez un fichier JSON de notifications")](notifications-images/runwith-execargs-vs.png#lightbox)

Lorsque l’extension watch est la **projet de démarrage**, Visual Studio affichera des options supplémentaires, comme indiqué ci-dessous. Choisissez une de la **Notification** options pour démarrer l’application watch dans **Notification** mode (en utilisant le fichier JSON sélectionné dans la fenêtre Propriétés) :

![](notifications-images/runwith-vs.png "Le menu de l’appareil")

-----

Lorsque vous testez sur le simulateur avec le fichier JSON de charge utile par défaut, le contrôleur de notification par défaut se présente comme suit :

![](notifications-images/notification-debug-sml.png "Un exemple de notification")

Il est également possible d’utiliser le [ligne de commande](~/ios/watchos/troubleshooting.md#command_line) pour démarrer le simulateur iOS.

### <a name="example-notification-payload"></a>Exemple charge utile de Notification

Dans le [espion Kit catalogue](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog) exemple il est un exemple de fichier charge utile JSON **NotificationPayload.json** (répertoriés ci-dessous).

```json
{
    "aps": {
        "alert": "Test message content",
        "title": "Optional title",
        "category": "myCategory"
        },

        "WatchKit Simulator Actions": [
        {
            "title": "First Button",
            "identifier": "firstButtonAction"
        }
        ],

        "customKey": "Use this file to define a testing payload for your notifications. The aps dictionary specifies the category, alert text and title. The WatchKit Simulator Actions array can provide info for one or more action buttons in addition to the standard Dismiss button. Any other top level keys are custom payload. If you have multiple such JSON files in your project, you'll be able to choose between them in when selecting to debug the notification interface of your Watch App."
    }
```

## <a name="related-links"></a>Liens associés

- [WatchNotifications (notifications locales) (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchkit-watchnotifications)
- [WatchKitCatalog (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Documentation de Notifications de Kit d’espion d’Apple](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/BasicSupport.html)
