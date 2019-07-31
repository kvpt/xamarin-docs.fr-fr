---
title: Introduction à watchOS
description: Ce document fournit une vue d’ensemble de watchOS, qui décrit le cycle de vie d’application, des types d’interface utilisateur, des tailles d’écran, des limitations et bien plus encore.
ms.prod: xamarin
ms.assetid: 99c316d6-6707-40f6-bec9-801d05888759
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/13/2016
ms.openlocfilehash: 364e10b8b59fcc8d640799ab6a0f11dcf4ded818
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644510"
---
# <a name="introduction-to-watchos"></a>Introduction à watchOS

> [!NOTE]
> Découvrez le [Introduction à watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md) pour une vue d’ensemble des fonctionnalités plus récentes.

## <a name="about-watchos"></a>À propos de watchOS

Une solution d’application watchOS a 3 projets :

- **Regardez l’Extension** – un projet qui contient le code de l’application watch.
- **Regardez l’application** – contient les ressources et l’animation de l’interface utilisateur.
- **iOS application parente** – cette application est une application iPhone normal. L’application watch et extension sont regroupés dans l’application iPhone pour la remise pour la surveillance de l’utilisateur.

Dans les applications watchOS 1, le code dans l’extension s’exécute sur l’iPhone : l’Apple Watch est effectivement un moniteur externe. applications watchOS 2 et 3 s’exécutent entièrement sur l’Apple Watch. Cette différence est indiquée dans le diagramme ci-dessous :

[![](intro-to-watchos-images/arch-sml.png "La différence entre Watchos 1 et Watchos 2 (et versions ultérieures) est indiquée dans ce diagramme")](intro-to-watchos-images/arch.png#lightbox)

Quelle que soit la version de watchOS est ciblée, dans Visual Studio pour Solution pavé Mac une solution complète sera ressembler à ceci :

[![](intro-to-watchos-images/projectstructure-sml.png "Le panneau Solution")](intro-to-watchos-images/projectstructure.png#lightbox)

Le *application parente* dans un watchOS solution est une application iOS normales. Il s’agit du seul projet dans la solution est visible **sur le téléphone**. Cas d’utilisation pour cette application inclut des didacticiels, des écrans d’administration et filtrage, mise en mémoire cache, etc. de couche intermédiaire. Toutefois, il est possible pour l’utilisateur installer et exécuter l’application/extension watch sans **jamais** avoir ouvert l’application parente, par conséquent, si vous avez besoin de l’application Parent à exécuter pour l’initialisation unique ou de l’administration, vous devez programmer votre montre application/extension pour indiquer à l’utilisateur qui.

Bien que l’application parente fournit l’application watch et une extension, ils s’exécuter dans les bacs à sable différents.

Sur watchOS 1, ils peuvent partager des données via un groupe de l’application partagée ou par le biais de la fonction statique `WKInterfaceController.OpenParentApplication`, ce qui déclenchera le `UIApplicationDelegate.HandleWatchKitExtensionRequest` méthode dans votre application parent `AppDelegate` (consultez [utilisation de l’application parente](~/ios/watchos/app-fundamentals/parent-app.md)).

Sur watchOS 2 ou version ultérieure, l’infrastructure de connectivité de surveillance est utilisé pour communiquer avec l’application parente, à l’aide de la `WCSession` classe.

## <a name="application-lifecycle"></a>Cycle de vie de l'application

Dans l’extension watch, une sous-classe de la `WKInterfaceController` classe est créée pour chaque séquence de la table de montage séquentiel.

Ces `WKInterfaceController` classes sont analogues au `UIViewController` objets dans la programmation d’iOS mais n’ont pas le même niveau d’accès à la vue.
Par exemple, vous ne peut pas ajouter des contrôles à dynamiquement ou restructurer votre interface utilisateur.
Vous pouvez, toutefois, masquer et afficher des contrôles et, avec certains contrôles, modifier leur taille, la transparence et options d’apparence.

Le cycle de vie d’un `WKInterfaceController` objet implique les appels suivants :

- En [éveil](xref:WatchKit.WKInterfaceController.Awake*) : Vous devez effectuer la plus grande partie de votre initialisation dans cette méthode.
- [WillActivate](xref:WatchKit.WKInterfaceController.WillActivate) : Appelé juste avant que l’application Watch apparaisse à l’utilisateur. Utilisez cette méthode pour effectuer l’initialisation du moment de la dernière, animations, etc. de début.
- À ce stade, l’application Apple Watch s’affiche et l’Extension commence à répondre aux entrées utilisateur et affichage de l’application Watch par votre logique d’application de la mise à jour.
- [DidDeactivate](xref:WatchKit.WKInterfaceController.DidDeactivate) après l’application de surveillance a été ignorée par l’utilisateur, cette méthode est appelée. Une fois que cette méthode est retournée, contrôles d’interface utilisateur ne peut pas être modifiés jusqu'à la prochaine `WillActivate` est appelée. Cette méthode sera également être appelée si la connexion pour l’iPhone est interrompue.
- Une fois que l’extension a été désactivée, il n’est pas accessible à votre programme. En attente de fonctions asynchrones **ne sera pas** être appelée. Regardez que les Extensions de Kit peut ne pas utiliser les modes de traitement en arrière-plan. Si le programme est réactivé par l’utilisateur, mais l’application n’a pas été terminée par le système d’exploitation, la première méthode appelée sera `WillActivate`.

![](intro-to-watchos-images/wkinterfacecontrollerlifecycle.png "Vue d’ensemble du cycle de vie des applications")

## <a name="types-of-user-interface"></a>Types d’Interface utilisateur

Il existe trois types d’interaction de que l’utilisateur peut avoir avec votre application watch.
Toutes les sont programmées à l’aide de sous-classes personnalisés de `WKInterfaceController`, de sorte que la séquence de cycle de vie abordés précédemment s’applique universellement (notifications sont programmées avec des sous-classes de `WKUserNotificationController`, qui est lui-même une sous-classe de `WKInterfaceController`) :

### <a name="normal-interaction"></a>Interaction normale

La majorité d’interaction d’extension d’application watch sera effectuée avec des sous-classes de `WKInterfaceController` que vous écrivez pour qu’elles correspondent à des scènes dans votre application Apple watch **Interface.storyboard**. Cela est couvert en détail dans le [Installation](~/ios/watchos/get-started/installation.md) et [mise en route](~/ios/watchos/get-started/index.md) articles.
L’illustration suivante montre une partie de la [espion Kit catalogue](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog) plan conceptuel de l’exemple. Pour chaque scène a montré ici, il existe un personnalisé correspondant `WKInterfaceController` (`LabelDetailController`, `ButtonDetailController`, `SwitchDetailController`, etc.) dans le projet d’extension.

![](intro-to-watchos-images/scenes.png "Exemples d’Interaction de la normale")

### <a name="notifications"></a>Notifications

[Notifications](~/ios/watchos/platform/notifications.md) sont un cas d’usage principaux pour l’Apple Watch. Notifications locales et distantes sont prises en charge. Interaction avec les notifications se produit en deux étapes, appelées aperçu court et Long.

Recherche courte s’affichent brièvement et afficher l’icône d’application espion, son nom et le titre (comme spécifié par `WKInterfaceController.SetTitle`).

La zone de recherche Long combine fournie par le système **châssis** zone et un bouton Dismiss avec votre contenu personnalisé basé sur la table de montage séquentiel.

`WKUserNotificationInterfaceController` étend `WKInterfaceController` avec les méthodes `DidReceiveLocalNotification` et `DidReceiveRemoteNotification`.
Substituer ces méthodes pour réagir aux événements de notification.

Pour plus d’informations sur la conception de l’interface utilisateur de Notification, reportez-vous à la [Apple Watch Human Interface Guidelines pour](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/Notifications.html#//apple_ref/doc/uid/TP40014992-CH20-SW1)

![](intro-to-watchos-images/notifications.png "Exemples de notifications")

## <a name="screen-sizes"></a>Tailles d’écran

Le Apple Watch a deux tailles de visage: 38mm et Watch 42mm, avec un ratio d’affichage de 5:4 et un écran de retine. Leur taille utilisable est :

- 38mm 136 x 170 pixels logiques (272 x 340 pixels physiques)
- Watch 42mm 156 x 195 pixels logiques (312 x 390 pixels physiques).

Utilisez `WKInterfaceDevice.ScreenBounds` pour déterminer sur l’affichage qui s’exécute votre application Watch.

En règle générale, il est plus facile à développer votre conception de texte et la disposition avec l’affichage de 38mm plus limité et puis monter en puissance.
Si vous démarrez avec l’environnement, mise à l’échelle vers le bas peut entraîner horrible chevauchement ou de troncation de texte.

En savoir plus sur [utilisation de tailles d’écran](~/ios/watchos/app-fundamentals/screen-sizes.md).


## <a name="limitations-of-watchos"></a>Limitations de watchOS

Il existe certaines limitations de watchOS à connaître lors du développement d’applications watchOS :

- Appareils d’Apple Watch ont une mémoire limitée : n’oubliez pas de l’espace disponible avant de télécharger des fichiers volumineux (par exemple). fichiers audio ou vidéo).

- Nombreux watchOS [contrôles](~/ios/watchos/user-interface/index.md) se retrouvent dans UIKit, mais différentes classes (`WKInterfaceButton` plutôt que `UIButton`, `WKInterfaceSwitch` pour `UISwitch`, etc.) possèdent un ensemble limité de méthodes par rapport à leur UIKit équivalents. En outre, watchOS a certains contrôles tels que `WKInterfaceDate` ce UIKit n’a pas (pour afficher une date et heure).

  - Vous ne peut pas acheminer les notifications pour l’observation uniquement, ou uniquement l’iPhone (le type de contrôle de l’utilisateur dispose sur le routage n’a pas été annoncé par Apple).

Certains autres limitations connues / Forum aux questions :

- Apple n’autorise pas les visages de suivi personnalisé 3 rd-party.

- Les API qui permettent la surveillance contrôler iTunes sur le téléphone connecté sont privés.


## <a name="further-reading"></a>informations supplémentaires

Consultez la documentation d’Apple :

* [Développement pour le Kit d’espion](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/index.html#//apple_ref/doc/uid/TP40014969-CH8-SW1)

* [Regardez le Guide de programmation de Kit](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/DesigningaWatchKitApp.html)

* [Indications de l’Interface humaine Apple Watch](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/index.html#//apple_ref/doc/uid/TP40014992-CH3-SW1)


## <a name="related-links"></a>Liens associés

- [watchOS 3 catalogue (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [watchOS 1 catalogue (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Configuration et installation](~/ios/watchos/get-started/installation.md)
- [Première vidéo de l’application Apple Watch](https://blog.xamarin.com/your-first-watch-kit-app/)
- [Apple du développement pour le guide du Kit d’espion](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/index.html)
- [Conseils de WatchKit d’Apple](https://developer.apple.com/watchkit/tips/)
- [Introduction à watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md)
