---
title: Introduction à watchOS
description: Ce document fournit une vue d’ensemble de Watchos, décrivant le cycle de vie de l’application, les types d’interfaces utilisateur, les tailles d’écran, les limitations et bien plus encore.
ms.prod: xamarin
ms.assetid: 99c316d6-6707-40f6-bec9-801d05888759
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/13/2016
ms.openlocfilehash: b3c2908d8ae9a68189fbff4d47afa49da21b88a5
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030194"
---
# <a name="introduction-to-watchos"></a>Introduction à watchOS

> [!NOTE]
> Consultez la [Présentation de Watchos 3](~/ios/watchos/platform/introduction-to-watchos3/index.md) pour obtenir une vue d’ensemble des fonctionnalités les plus récentes.

## <a name="about-watchos"></a>À propos de Watchos

Une solution d’application Watchos a 3 projets :

- **Extension Watch** : projet qui contient le code de l’application Watch.
- **Application Watch** : contient le Storyboard et les ressources de l’interface utilisateur.
- application **parente iOS** : cette application est une application iPhone normale. L’application Watch et l’extension sont regroupées dans l’application iPhone en vue de leur remise à la montre de l’utilisateur.

Dans les applications Watchos 1, le code de l’extension s’exécute sur l’iPhone : le Apple Watch est effectivement un affichage externe. les applications Watchos 2 et 3 s’exécutent entièrement sur le Apple Watch. Cette différence est indiquée dans le diagramme ci-dessous :

[![](intro-to-watchos-images/arch-sml.png "The difference between watchOS 1 and watchOS 2 (and greater) is shown in this diagram")](intro-to-watchos-images/arch.png#lightbox)

Quelle que soit la version de Watchos ciblée, Visual Studio pour Mac Panneau Solutions une solution complète ressemble à ceci :

[![](intro-to-watchos-images/projectstructure-sml.png "The Solution Pad")](intro-to-watchos-images/projectstructure.png#lightbox)

L' *application parente* dans une solution Watchos est une application iOS standard. Il s’agit du seul projet de la solution qui est visible **sur le téléphone**. Les cas d’utilisation de cette application incluent des didacticiels, des écrans d’administration et un filtrage de niveau intermédiaire, une mise en cache, etc. Toutefois, il est possible pour l’utilisateur d’installer et d’exécuter l’application/extension Watch sans **jamais** ouvrir l’application parente. par conséquent, si vous avez besoin que l’application parente s’exécute pour une initialisation ou une administration unique, vous devez programmer votre application/extension Watch pour indiquer à utilisateur qui.

Bien que l’application parente remette l’application Watch et l’extension, elles s’exécutent dans différents bacs à sable.

Sur Watchos 1, ils peuvent partager des données via un groupe d’applications partagé ou via la fonction statique `WKInterfaceController.OpenParentApplication`, qui déclenchera la méthode `UIApplicationDelegate.HandleWatchKitExtensionRequest` dans le `AppDelegate` de votre application parente (voir [utilisation de l’application parente](~/ios/watchos/app-fundamentals/parent-app.md)).

Dans Watchos 2 ou ultérieur, l’infrastructure de connectivité Watch est utilisée pour communiquer avec l’application parente, à l’aide de la classe `WCSession`.

## <a name="application-lifecycle"></a>Cycle de vie de l'application

Dans l’extension Watch, une sous-classe de la classe `WKInterfaceController` est créée pour chaque scène de table de montage séquentiel.

Ces classes `WKInterfaceController` sont analogues aux objets `UIViewController` dans la programmation iOS, mais n’ont pas le même niveau d’accès à la vue.
Par exemple, vous ne pouvez pas ajouter dynamiquement des contrôles à votre interface utilisateur ou la restructurer.
Toutefois, vous pouvez masquer et afficher les contrôles et, avec certains contrôles, modifier leurs options de taille, de transparence et d’apparence.

Le cycle de vie d’un objet `WKInterfaceController` implique les appels suivants :

- [Éveillé](xref:WatchKit.WKInterfaceController.Awake*) : vous devez effectuer la plus grande partie de votre initialisation dans cette méthode.
- [WillActivate](xref:WatchKit.WKInterfaceController.WillActivate) : appelé peu avant que l’application Watch apparaisse à l’utilisateur. Utilisez cette méthode pour effectuer l’initialisation du dernier moment, démarrer les animations, etc.
- À ce stade, l’application Watch apparaît et l’extension commence à répondre à l’entrée utilisateur et à mettre à jour l’affichage de l’application Watch selon la logique de votre application.
- [DidDeactivate](xref:WatchKit.WKInterfaceController.DidDeactivate) Une fois que l’application Watch a été fermée par l’utilisateur, cette méthode est appelée. Après le retour de cette méthode, les contrôles d’interface utilisateur ne peuvent pas être modifiés tant que la prochaine `WillActivate` n’est pas appelée. Cette méthode est également appelée si la connexion à l’iPhone est interrompue.
- Une fois l’extension désactivée, elle est inaccessible à votre programme. Les fonctions asynchrones en attente **ne seront pas** appelées. Les extensions du kit espion peuvent ne pas utiliser les modes de traitement en arrière-plan. Si le programme est réactivé par l’utilisateur, mais que l’application n’a pas été arrêtée par le système d’exploitation, la première méthode appelée sera `WillActivate`.

![](intro-to-watchos-images/wkinterfacecontrollerlifecycle.png "Application Lifecycle overview")

## <a name="types-of-user-interface"></a>Types d’interface utilisateur

L’utilisateur peut avoir trois types d’interaction avec votre application Watch.
Tous sont programmés à l’aide de sous-classes personnalisées de `WKInterfaceController`, de sorte que la séquence de cycle de vie précédemment traitée s’applique de façon universelle (les notifications sont programmées avec des sous-classes de `WKUserNotificationController`, qui est elle-même une sous-classe de `WKInterfaceController`) :

### <a name="normal-interaction"></a>Interaction normale

La majorité des interactions entre l’application et l’extension de la surveillance sont les sous-classes de `WKInterfaceController` que vous écrivez pour correspondre à des scènes dans l’interface de votre application de surveillance **. Storyboard**. Ce sujet est abordé en détail dans les articles [installation](~/ios/watchos/get-started/installation.md) et [prise en main](~/ios/watchos/get-started/index.md) .
L’illustration suivante montre une partie de la table de montage séquentiel de l’exemple de [catalogue du kit de surveillance](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog) . Pour chaque scène illustrée ici, il existe une `WKInterfaceController` personnalisée correspondante (`LabelDetailController`, `ButtonDetailController`, `SwitchDetailController`, etc.) dans le projet d’extension.

![](intro-to-watchos-images/scenes.png "Normal Interaction examples")

### <a name="notifications"></a>Notifications

Les [notifications](~/ios/watchos/platform/notifications.md) sont un cas d’utilisation majeur pour le Apple Watch. Les notifications locales et distantes sont prises en charge. L’interaction avec les notifications se produit en deux étapes, appelées de type « short » et « long ».

Les points de recherche courts s’affichent brièvement et indiquent l’icône de l’application espion, son nom et le titre (comme spécifié avec `WKInterfaceController.SetTitle`).

Le long look combine une zone de **châssis** fournie par le système et ignore le bouton avec votre contenu personnalisé basé sur le Storyboard.

`WKUserNotificationInterfaceController` étend `WKInterfaceController` avec les méthodes `DidReceiveLocalNotification` et `DidReceiveRemoteNotification`.
Remplacez ces méthodes pour réagir aux événements de notification.

Pour plus d’informations sur la conception de l’interface utilisateur des notifications, reportez-vous aux [instructions d’interface utilisateur Apple Watch](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/Notifications.html#//apple_ref/doc/uid/TP40014992-CH20-SW1)

![](intro-to-watchos-images/notifications.png "Sample notifications")

## <a name="screen-sizes"></a>Tailles d’écran

Le Apple Watch a deux tailles de visage : 38mm et Watch 42mm, avec un ratio d’affichage 5:4 et un écran de retine. Les tailles utilisables sont les suivantes :

- 38mm : 136 x 170 pixels logiques (272 x 340 pixels physiques)
- Watch 42mm : 156 x 195 pixels logiques (312 x 390 pixels physiques).

Utilisez `WKInterfaceDevice.ScreenBounds` pour déterminer l’affichage de votre application Watch.

En règle générale, il est plus facile de développer votre conception de texte et de mise en page avec l’affichage 38mm plus restreint, puis de monter en puissance.
Si vous démarrez avec l’environnement le plus grand, la montée en puissance verticale peut entraîner un chevauchement ou une troncation de texte.

En savoir plus sur l' [utilisation des tailles d’écran](~/ios/watchos/app-fundamentals/screen-sizes.md).

## <a name="limitations-of-watchos"></a>Limitations de Watchos

Il existe certaines limitations de Watchos à connaître lors du développement d’applications Watchos :

- Apple Watch appareils ont un stockage limité : Tenez compte de l’espace disponible avant de télécharger des fichiers volumineux (par exemple, fichiers audio ou vidéo).

- De nombreux [contrôles](~/ios/watchos/user-interface/index.md) Watchos ont des analogues dans UIKit, mais sont des classes différentes (`WKInterfaceButton` au lieu de `UIButton`, `WKInterfaceSwitch` pour `UISwitch`, etc.) et ont un ensemble limité de méthodes par rapport à leurs équivalents uikit. En outre, Watchos comporte des contrôles tels que `WKInterfaceDate` (pour l’affichage d’une date et d’une heure) dont UIKit n’a pas.

  - Vous ne pouvez pas acheminer les notifications uniquement vers l’espion ou l’iPhone uniquement (le type de contrôle de l’utilisateur sur le routage n’a pas été annoncé par Apple).

D’autres limitations connues/questions fréquemment posées :

- Apple n’autorise pas les visages de espion personnalisé tiers.

- Les API qui permettent à Watch de contrôler iTunes sur le téléphone connecté sont privées.

## <a name="further-reading"></a>informations supplémentaires

Consultez la documentation d’Apple :

- [Développement pour le kit de surveillance](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/index.html#//apple_ref/doc/uid/TP40014969-CH8-SW1)

- [Guide de programmation du kit de surveillance](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/DesigningaWatchKitApp.html)

- [Instructions relatives à l’interface utilisateur Apple Watch](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/index.html#//apple_ref/doc/uid/TP40014992-CH3-SW1)

## <a name="related-links"></a>Liens associés

- [Catalogue Watchos 3 (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Catalogue Watchos 1 (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Configuration et installation](~/ios/watchos/get-started/installation.md)
- [Vidéo de la première application Watch](https://blog.xamarin.com/your-first-watch-kit-app/)
- [Guide du kit de développement pour Watch d’Apple](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/index.html)
- [Conseils WatchKit d’Apple](https://developer.apple.com/watchkit/tips/)
- [Introduction à watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md)
