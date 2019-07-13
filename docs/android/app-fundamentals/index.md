---
title: Principes de base Application Xamarin.Android
description: Concepts d’Application de base
ms.prod: xamarin
ms.assetid: 935B8BFE-23B7-4239-5C87-F4A503B889CB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 3ed616340dbb6dd64d85998d84e340ec381f6b54
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864091"
---
# <a name="xamarinandroid-application-fundamentals"></a>Principes de base Application Xamarin.Android

Cette section fournit un guide sur certaines des tâches de choses ou concepts que les développeurs doivent connaître lors du développement d’applications Android plus courant.

## <a name="accessibilityandroidapp-fundamentalsaccessibilitymd"></a>[Accessibilité](~/android/app-fundamentals/accessibility.md)

Cette page décrit comment utiliser les API d’accessibilité Android pour créer des applications en fonction de la [liste de contrôle d’accessibilité](~/cross-platform/app-fundamentals/accessibility.md).

## <a name="understanding-android-api-levelsandroidapp-fundamentalsandroid-api-levelsmd"></a>[Présentation des niveaux d’API Android](~/android/app-fundamentals/android-api-levels.md)

Ce guide décrit comment Android utilise les niveaux d’API pour gérer la compatibilité des applications entre les différentes versions d’Android, et elle explique comment configurer les paramètres de projet Xamarin.Android pour déployer ces niveaux d’API dans votre application. En outre, ce guide explique comment écrire du code de runtime qui porte sur différents niveaux d’API, et il fournit une liste de référence de tous les niveaux d’API Android, numéros de version (par exemple, Android 8.0), les noms de code Android (par exemple, Oreo) et les codes de version de build.



## <a name="resources-in-androidandroidapp-fundamentalsresources-in-androidindexmd"></a>[Ressources dans Android](~/android/app-fundamentals/resources-in-android/index.md)

Cet article présente le concept de ressources Android dans Xamarin.Android et les documents sur leur utilisation. Il explique comment utiliser des ressources dans votre application Android pour prendre en charge la localisation d’applications et plusieurs appareils, y compris les densités et tailles d’écran.




## <a name="activity-lifecycleandroidapp-fundamentalsactivity-lifecycleindexmd"></a>[Cycle de vie des activités](~/android/app-fundamentals/activity-lifecycle/index.md)

Les activités sont un bloc de construction fondamental des Applications Android, et ils peuvent exister dans un nombre d’états différents. Le cycle de vie d’activité commence par instanciation se termine par la destruction et inclut de nombreux états entre les deux. Lorsqu’une activité change d’état, la méthode d’événement du cycle de vie appropriée est appelée pour informer de l’activité de la modification d’état imminente et ce qui lui permet d’exécuter du code pour s’adapter à cette modification. Cet article examine le cycle de vie des activités et explique la responsabilité qu’une activité a lors de chacune de ces modifications d’état à faire partie d’une application fiable se comportant bien.

## <a name="localizationandroidapp-fundamentalslocalizationmd"></a>[Localisation](~/android/app-fundamentals/localization.md)

Cet article explique comment localiser une application Xamarin.Android dans d’autres langues en traduisant les chaînes et en fournissant des images de remplacement.

## <a name="servicesandroidapp-fundamentalsservicesindexmd"></a>[Services](~/android/app-fundamentals/services/index.md)

Cet article traite des services Android, qui sont des composants Android qui permettent le travail à faire en arrière-plan. Il explique les différents scénarios de services sont adaptés à et montre comment les implémenter à la fois pour les tâches en arrière-plan longue ainsi que pour fournir une interface pour les appels de procédure distante.

## <a name="broadcast-receiversandroidapp-fundamentalsbroadcast-receiversmd"></a>[Récepteurs de diffusion](~/android/app-fundamentals/broadcast-receivers.md)

Ce guide explique comment créer et utiliser des récepteurs de diffusion, un composant Android qui répond aux diffusions de l’échelle du système, dans Xamarin.Android.



## <a name="permissionsandroidapp-fundamentalspermissionsmd"></a>[autorisations](~/android/app-fundamentals/permissions.md)

Vous pouvez utiliser la prise en charge des outils intégré à Visual Studio pour Mac ou Visual Studio pour créer et ajouter des autorisations pour le manifeste Android. Ce document décrit comment ajouter des autorisations dans Visual Studio et Xamarin Studio.



## <a name="graphics-and-animationandroidapp-fundamentalsgraphics-and-animationmd"></a>[Graphismes et animation](~/android/app-fundamentals/graphics-and-animation.md)

Android fournit une infrastructure extrêmement riche et divers prenant en charge des graphiques 2D et des animations. Ce document présente ces infrastructures et explique comment créer des animations et des graphiques personnalisés et les utiliser dans une application Xamarin.Android.


## <a name="cpu-architecturesandroidapp-fundamentalscpu-architecturesmd"></a>[Architectures de processeur](~/android/app-fundamentals/cpu-architectures.md)

Xamarin.Android prend en charge plusieurs architectures d’UC, notamment les périphériques 32 bits et 64 bits. Cet article explique comment cibler une application à un ou plusieurs architectures d’UC Android pris en charge.




## <a name="handling-rotationandroidapp-fundamentalshandling-rotationmd"></a>[Rotation de la gestion](~/android/app-fundamentals/handling-rotation.md)

Cet article décrit comment gérer les changements d’orientation de périphérique dans Xamarin.Android. Il explique comment travailler avec le système de ressources Android pour charger automatiquement les ressources pour une orientation de périphérique particulier, ainsi que la manière dont pour gérer par programme l’orientation des modifications. Puis il décrit les techniques pour la gestion d’état lors de la rotation d’un appareil.



## <a name="android-audioandroidapp-fundamentalsandroid-audiomd"></a>[Android Audio](~/android/app-fundamentals/android-audio.md)

Le système d’exploitation Android fournit une prise en charge complète multimédias, englobant ainsi l’audio et vidéo. Ce guide se concentre sur audio dans Android et couvre la lecture et l’enregistrement audio à l’aide du lecteur audio intégré et les classes de l’enregistreur, ainsi que l’API de bas niveau audio. Il aborde également l’utilisation des événements Audio de diffusion par d’autres applications, afin que les développeurs peuvent créer des applications conformes.




## <a name="notificationsandroidapp-fundamentalsnotificationsindexmd"></a>[Notifications](~/android/app-fundamentals/notifications/index.md)

Cette section explique comment implémenter des notifications locales et distantes dans Xamarin.Android. Il décrit les différents éléments d’interface utilisateur d’une notification d’Android et traite des API d’impliquée dans la création et affichage d’une notification. Pour les notifications à distance, à la fois Google Cloud Messaging et Firebase Cloud Messaging sont expliquées. Exemples de code et procédures pas à pas détaillées sont incluses.



## <a name="touchandroidapp-fundamentalstouchindexmd"></a>[Entrées tactiles](~/android/app-fundamentals/touch/index.md)

Cette section explique les concepts et les détails d’implémentation les gestes tactiles sur Android. API de tactiles sont introduites et expliqué suivie d’une exploration des modules de reconnaissance de mouvement.



## <a name="httpclient-stack-and-ssltlsandroidapp-fundamentalshttp-stackmd"></a>[Pile HttpClient et SSL/TLS](~/android/app-fundamentals/http-stack.md)

Cette section explique les sélecteurs de pile HttpClient et d’implémentation de SSL/TLS pour Android. Ces paramètres déterminent l’implémentation de HttpClient et SSL/TLS qui sera utilisée par vos applications Xamarin.Android.


## <a name="writing-responsive-applicationswriting-responsive-appsmd"></a>[Écriture d’Applications réactives](writing-responsive-apps.md)

Cet article explique comment utiliser les threads pour maintenir la réactivité une application Xamarin.Android en déplaçant des tâches longues une session sur un thread d’arrière-plan.
