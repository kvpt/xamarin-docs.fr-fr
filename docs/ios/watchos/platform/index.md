---
title: fonctionnalités de la plateforme Watchos
description: Ce document contient des liens vers divers guides qui décrivent les fonctionnalités de la plateforme Watchos, telles que Apple Pay, les notifications, les complications, les suggestions proactives, les applications d’entraînement, etc.
ms.prod: xamarin
ms.assetid: 13F23E01-BAED-43EB-A70E-3B30EF53D379
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: e571132b5f1e30bececb8302f2dacfcd908ad42e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028292"
---
# <a name="watchos-platform-features"></a>fonctionnalités de la plateforme Watchos

## <a name="introduction-to-watchos-5introduction-to-watchos5indexmd"></a>[Introduction à watchOS 5](introduction-to-watchos5/index.md)

Ce document fournit une vue d’ensemble de haut niveau des fonctionnalités nouvelles et mises à jour dans Watchos 5, qui peuvent être utilisées lors de la création d’applications Watchos avec Xamarin.

## <a name="introduction-to-watchos-4introduction-to-watchos4md"></a>[Introduction à watchOS 4](introduction-to-watchos4.md)

Ce document fournit une vue d’ensemble de haut niveau des fonctionnalités ajoutées et mises à jour dans Watchos 4.

## <a name="introduction-to-watchos-3introduction-to-watchos3indexmd"></a>[Introduction à watchOS 3](introduction-to-watchos3/index.md)

Cet article décrit les API nouvelles et mises à jour dans Watchos 3.

## <a name="apple-pay-enhancementsioswatchosplatformapple-paymd"></a>[Améliorations apportées à Apple Pay](~/ios/watchos/platform/apple-pay.md)

Dans Watchos 3, l’infrastructure PassKit a été développée pour permettre la prise en charge des paiements dans l’application sécurisés (des biens et services physiques) pour les applications qui s’exécutent sur le Apple Watch.

## <a name="background-tasksioswatchosplatformbackground-tasksmd"></a>[Tâches en arrière-plan](~/ios/watchos/platform/background-tasks.md)

Watchos 3 introduit plusieurs tâches en arrière-plan qu’une application peut utiliser pour mettre à jour ses informations afin de s’assurer qu’elle dispose du contenu dont l’utilisateur a besoin avant de l’ouvrir.

## <a name="notificationsnotificationsmd"></a>[Notifications](notifications.md)

Découvrez comment fournir une gestion personnalisée des notifications dans votre application Watch.

## <a name="complicationscomplicationsmd"></a>[Complications](complications.md)

Ajoutez une prise en charge de la complication pour afficher des données à jour sur le visage de la montre.

## <a name="proactive-suggestionsioswatchosplatformproactive-suggestionsmd"></a>[Suggestions proactives](~/ios/watchos/platform/proactive-suggestions.md)

Watchos 3 permet à l’application de présenter de manière proactive des informations à l’utilisateur dans des contextes donnés. Pour prendre en charge cette fonctionnalité, [NSUserActivity](https://developer.apple.com/reference/foundation/nsuseractivity) comprend désormais la propriété `MapItem` qui permet à l’application de fournir des informations d’emplacement pour une utilisation ultérieure par d’autres applications.

## <a name="quick-interaction-techniquesioswatchosplatformquick-interaction-techniquesmd"></a>[Techniques d’interaction rapide](~/ios/watchos/platform/quick-interaction-techniques.md)

Il est essentiel de fournir des interactions utilisateur rapides pour créer des applications de Apple Watch attrayantes et des complications. Nouveauté de Watchos 3, Apple a ajouté la prise en charge des détecteurs de mouvement, l’accès aux Digital Crown et aux nouvelles techniques de notification et de navigation utilisateur. Cela, ainsi que la prise en charge supplémentaire pour SceneKit et SpriteKit, permettent au développeur de créer facilement des interfaces riches et faciles à utiliser, à la fois rapides et réactives.

## <a name="workout-app-enhancementsioswatchosplatformworkout-appsmd"></a>[Améliorations apportées aux applications de l’entraînement](~/ios/watchos/platform/workout-apps.md)

Nouveauté de Watchos 3, les applications associées à l’entraînement ont la possibilité de s’exécuter en arrière-plan sur le Apple Watch. Pour activer cette fonctionnalité (et accéder aux données HealthKit), l’application doit inclure la clé `WKBackgroundModes` dans le fichier `Info.plist` avec la valeur `workout-processing`.
