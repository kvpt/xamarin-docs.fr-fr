---
title: watchOS Notifications dans Xamarin
description: Ce document décrit comment utiliser des notifications de watchOS dans Xamarin. Il aborde la création de notification contrôleurs, générer des notifications et test des notifications.
ms.prod: xamarin
ms.assetid: 0BC1306E-0713-4592-996E-7530CCF281E7
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 85a55967446da5cf89e8ce19dadf88d0de16d80a
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78291761"
---
# <a name="watchos-notifications-in-xamarin"></a>watchOS Notifications dans Xamarin

Regardez les applications peuvent recevoir des notifications si l’application iOS contenant les prend en charge. La gestion des notifications est intégrée. vous n’avez donc pas *besoin* d’ajouter la prise en charge des notifications supplémentaires décrite ci-dessous. Toutefois, si vous souhaitez personnaliser l’apparence et le comportement de la notification, lisez la suite.

Reportez-vous au document sur les [notifications iOS](~/ios/platform/user-notifications/deprecated/index.md) pour plus d’informations sur l’ajout de la prise en charge des notifications à l’application iOS dans votre solution.

## <a name="creating-notification-controllers"></a>Création de contrôleurs de Notification

Sur le plan conceptuel contrôleurs de notifications ont un type spécial de segue leur déclenchement. Lorsque vous faites glisser un nouveau **contrôleur d’interface de notification** vers un Storyboard, un segue est automatiquement attaché :

![](notifications-images/notification-storyboard1.png "A new Notification Interface Controller with a segue attached")

Lorsque la notification segue est sélectionnée. vous pouvez modifier ses propriétés :

![](notifications-images/notification-storyboard2.png "The notification segue selected")

Une fois que vous avez personnalisé le contrôleur, il peut ressembler à cet exemple à partir de la WatchKitCatalog :

![](notifications-images/notifications-segue.png "The Notification Properties")

Il existe deux types de notification :

- Affichage statique de type « non défilant » **concis** défini par le système.

- Vue personnalisable avec défilement **long** et personnalisable, définie par vous-même ! Une version plus simple, statique et une version dynamique plus complexe peuvent être spécifiés.

### <a name="short-look-notification-controller"></a>Contrôleur de Notification de courte-aperçu

L’interface utilisateur courte apparence se compose de l’icône d’application, nom de l’application et la chaîne de titre de notification.

Si l’utilisateur n’ignore pas la notification, le système passe automatiquement à une notification de l’aspect de longue durée qui fournit plus d’informations.

### <a name="long-look-notification-controller"></a>Contrôleur de Notification de l’aspect de longue durée

Le système d’exploitation décide s’il faut afficher la vue statique ou dynamique selon un certain nombre de facteurs. Vous devez fournir une interface statique et peut éventuellement inclure également une interface dynamique pour les notifications.

#### <a name="static"></a>statique

La vue statique doit être simple et rapide à afficher.

![](notifications-images/notification-static.png "The static view")

#### <a name="dynamic"></a>Dynamique

La vue dynamique peut afficher plus de données et fournir plus d’interactivité.

![](notifications-images/notification-dynamic.png "The dynamic view")

## <a name="generating-notifications"></a>Génération des Notifications

Les notifications peuvent provenir d’un serveur distant ou être générées localement dans l’application iOS.

Reportez-vous à la [procédure pas à pas sur les notifications iOS](~/ios/platform/user-notifications/deprecated/local-notifications-in-ios-walkthrough.md) pour obtenir un exemple de génération de notifications locales.

Les notifications locales doivent avoir la `AlertTitle` définie pour être affichées sur la Apple Watch-la chaîne de `AlertTitle` s’affiche dans l’interface abrégée. Les `AlertTitle` et `AlertBody` sont affichés dans la liste notifications. et le `AlertBody` s’affiche dans l’interface longue.

Cette capture d’écran montre les `AlertTitle` affichées dans la liste des notifications et les `AlertBody` affichées dans l’interface longue :

![](notifications-images/watch-notificationslist-sml.png "Cette capture d’écran montre les AlertTitle affichés dans la liste des notifications") ![](notifications-images/watch-notificationcontroller-sml.png "AlertBody affiché dans l’interface à long terme")

## <a name="testing-notifications"></a>Test des Notifications

Les notifications (locales et distantes) peuvent uniquement être testées correctement sur un appareil, mais elles peuvent être simulées à l’aide d’un fichier **. JSON** dans le simulateur iOS.

### <a name="testing-on-apple-watch"></a>Tests sur Apple Watch

Lorsque vous testez des notifications sur un Apple Watch, n’oubliez pas que la [documentation d’Apple](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/BasicSupport.html) indique les éléments suivants :

> Lorsqu’une des notifications de local ou distant de votre application arrive sur iPhone de l’utilisateur, iOS décide s’il faut afficher cette notification sur l’iPhone ou sur l’Apple Watch.

Cela est alluding au fait que l’option iOS décide si une notification s’affichera sur l’iPhone ou sur la surveillance. Si l’iPhone couplé est actif lors de la réception d’une notification, la notification est susceptible d’être affichée sur l’iPhone et *n’est pas* acheminée vers la montre.

Pour garantir que la notification s’affiche sur la surveillance, désactivez l’écran de l’iPhone (appuyant une seule fois sur le bouton d’alimentation) ou laissez-le se mettre en veille. Si le Watch jumelée est dans la plage, dispose d’une puissance et est portée sur votre poignet, la notification sera routé il et apparaissent sur l’observation (accompagnée d’un subtiles).

### <a name="testing-on-the-ios-simulator"></a>Test sur le simulateur iOS

Vous *devez* fournir une charge utile JSON de test lors du test du mode de notification dans le simulateur iOS. Définissez le chemin d’accès dans la fenêtre **arguments d’exécution personnalisée** dans Visual Studio pour Mac.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Visual Studio pour Mac affiche des options supplémentaires quand une extension Watch est définie comme **projet de démarrage**.
Cliquez avec le bouton droit sur le projet d’extension Watch et choisissez **exécuter avec > paramètres personnalisés...** :

[![](notifications-images/runwith-customparams-sml.png "Running with Custom Properties")](notifications-images/runwith-customparams.png#lightbox)

Cela ouvre la fenêtre **arguments d’exécution** qui contient un onglet **WatchKit** . Sélectionnez **notification** et fournissez une charge utile JSON, puis appuyez sur **exécuter** pour démarrer l’application espion dans le simulateur :

[![](notifications-images/runwith-execargs-sml.png "Select Notification Payload Default")](notifications-images/runwith-execargs.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Pour définir la charge utile de notification de test dans Visual Studio, cliquez avec le bouton droit sur l’extension Watch pour modifier les **Propriétés du projet**. Accédez à la section **Déboguer** et sélectionnez un fichier de notifications JSON dans la liste (il répertorie automatiquement tous les fichiers JSON inclus dans le projet).

[![](notifications-images/runwith-execargs-sml-vs.png "Select a notifications JSON file")](notifications-images/runwith-execargs-vs.png#lightbox)

Lorsque l’extension Watch est le **projet de démarrage**, Visual Studio affiche des options supplémentaires, comme indiqué ci-dessous. Choisissez l’une des options de **notification** pour démarrer l’application espion en mode **notification** (à l’aide du fichier JSON sélectionné dans la fenêtre Propriétés) :

![](notifications-images/runwith-vs.png "The Device menu")

-----

Lorsque vous testez sur le simulateur avec le fichier JSON de charge utile par défaut, le contrôleur de notification par défaut se présente comme suit :

![](notifications-images/notification-debug-sml.png "An example notification")

Il est également possible d’utiliser la [ligne de commande](~/ios/watchos/troubleshooting.md#command_line) pour démarrer le simulateur iOS.

### <a name="example-notification-payload"></a>Exemple charge utile de Notification

Dans l’exemple de [catalogue du kit de surveillance](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog) , il y a un exemple de fichier JSON de charge utile **NotificationPayload. JSON** (listé ci-dessous).

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

## <a name="related-links"></a>Liens connexes

- [WatchKitCatalog (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Documentation sur les notifications du kit de surveillance Apple](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/BasicSupport.html)
