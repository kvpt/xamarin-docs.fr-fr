---
title: Notions de base de l’application Xamarin. Android
description: Concepts de l’application principale
ms.prod: xamarin
ms.assetid: 935B8BFE-23B7-4239-5C87-F4A503B889CB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: af7ba83b9026a91028f4ffa9894d564d5ff13eb8
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70755298"
---
# <a name="xamarinandroid-application-fundamentals"></a>Notions de base de l’application Xamarin. Android

Cette section fournit un guide sur les tâches ou concepts les plus courants que les développeurs doivent connaître lors du développement d’applications Android.

## <a name="accessibilityandroidapp-fundamentalsaccessibilitymd"></a>[Accessibilité](~/android/app-fundamentals/accessibility.md)

Cette page explique comment utiliser les API d’accessibilité Android pour créer des applications en fonction de la [liste de contrôle d’accessibilité](~/cross-platform/app-fundamentals/accessibility.md).

## <a name="understanding-android-api-levelsandroidapp-fundamentalsandroid-api-levelsmd"></a>[Fonctionnement des niveaux d’API Android](~/android/app-fundamentals/android-api-levels.md)

Ce guide décrit comment Android utilise les niveaux d’API pour gérer la compatibilité des applications entre les différentes versions d’Android, et explique comment configurer les paramètres de projet Xamarin. Android pour déployer ces niveaux d’API dans votre application. En outre, ce guide explique comment écrire du code d’exécution qui gère différents niveaux d’API et fournit une liste de référence de tous les niveaux de l’API Android, des numéros de version (par exemple, Android 8,0), des noms de code Android (tels que Oreo) et des codes de version de Build.

## <a name="resources-in-androidandroidapp-fundamentalsresources-in-androidindexmd"></a>[Ressources dans Android](~/android/app-fundamentals/resources-in-android/index.md)

Cet article présente le concept de ressources Android dans Xamarin. Android et décrit comment les utiliser. Il explique comment utiliser des ressources dans votre application Android pour prendre en charge la localisation d’applications et plusieurs appareils, y compris des tailles d’écran et des densités différentes.

## <a name="activity-lifecycleandroidapp-fundamentalsactivity-lifecycleindexmd"></a>[Cycle de vie des activités](~/android/app-fundamentals/activity-lifecycle/index.md)

Les activités sont un bloc de construction fondamental des applications Android qui peuvent exister dans différents États. Le cycle de vie de l’activité commence par instanciation et se termine par la destruction, et comprend de nombreux États entre eux. Lorsqu’une activité change d’État, la méthode d’événement de cycle de vie appropriée est appelée, en avertissant l’activité du changement d’État imminent et en lui permettant d’exécuter du code pour s’adapter à cette modification. Cet article examine le cycle de vie des activités et explique la responsabilité d’une activité au cours de chacun de ces changements d’État pour faire partie d’une application fiable et avec un comportement correct.

## <a name="localizationandroidapp-fundamentalslocalizationmd"></a>[Localisation](~/android/app-fundamentals/localization.md)

Cet article explique comment localiser un Xamarin. Android dans d’autres langues en traduisant des chaînes et en fournissant d’autres images.

## <a name="servicesandroidapp-fundamentalsservicesindexmd"></a>[Services](~/android/app-fundamentals/services/index.md)

Cet article traite des services Android, qui sont des composants Android qui permettent d’effectuer des tâches en arrière-plan. Il explique les différents scénarios pour lesquels les services sont adaptés et montre comment les implémenter pour effectuer des tâches en arrière-plan longues, ainsi que pour fournir une interface pour les appels de procédure distante.

## <a name="broadcast-receiversandroidapp-fundamentalsbroadcast-receiversmd"></a>[Récepteurs de diffusion](~/android/app-fundamentals/broadcast-receivers.md)

Ce guide explique comment créer et utiliser des récepteurs de diffusion, un composant Android qui répond aux diffusions à l’ensemble du système, dans Xamarin. Android.

## <a name="permissionsandroidapp-fundamentalspermissionsmd"></a>[autorisations](~/android/app-fundamentals/permissions.md)

Vous pouvez utiliser la prise en charge des outils intégrée à Visual Studio pour Mac ou Visual Studio pour créer et ajouter des autorisations au manifeste Android. Ce document décrit comment ajouter des autorisations dans Visual Studio et Xamarin Studio.

## <a name="graphics-and-animationandroidapp-fundamentalsgraphics-and-animationmd"></a>[Graphismes et animation](~/android/app-fundamentals/graphics-and-animation.md)

Android fournit une infrastructure très riche et diversifiée pour la prise en charge des graphiques 2D et des animations. Ce document présente ces frameworks et explique comment créer des graphiques et des animations personnalisés et les utiliser dans une application Xamarin. Android.

## <a name="cpu-architecturesandroidapp-fundamentalscpu-architecturesmd"></a>[Architectures de processeur](~/android/app-fundamentals/cpu-architectures.md)

Xamarin. Android prend en charge plusieurs architectures d’UC, y compris les appareils 32 bits et 64 bits. Cet article explique comment cibler une application vers une ou plusieurs architectures de processeur compatibles Android.

## <a name="handling-rotationandroidapp-fundamentalshandling-rotationmd"></a>[Rotation de la gestion](~/android/app-fundamentals/handling-rotation.md)

Cet article explique comment gérer les modifications d’orientation des appareils dans Xamarin. Android. Il explique comment utiliser le système de ressources Android pour charger automatiquement des ressources pour une orientation d’appareil particulière et comment gérer les modifications d’orientation par programmation. Il décrit ensuite les techniques de gestion de l’état lors de la rotation d’un appareil.

## <a name="android-audioandroidapp-fundamentalsandroid-audiomd"></a>[Audio Android](~/android/app-fundamentals/android-audio.md)

Le système d’exploitation Android offre une prise en charge complète du multimédia, qui englobe à la fois l’audio et la vidéo. Ce guide se concentre sur l’audio dans Android et traite de la lecture et de l’enregistrement audio à l’aide des classes de lecteur et d’enregistreur audio intégrées, ainsi que de l’API audio de bas niveau. Il traite également de l’utilisation des événements audio diffusés par d’autres applications, afin que les développeurs puissent créer des applications bien connues.

## <a name="notificationsandroidapp-fundamentalsnotificationsindexmd"></a>[Notifications](~/android/app-fundamentals/notifications/index.md)

Cette section explique comment implémenter des notifications locales et distantes dans Xamarin. Android. Il décrit les différents éléments d’interface utilisateur d’une notification Android et présente les API impliquées dans la création et l’affichage d’une notification. Pour les notifications distantes, Google Cloud Messaging et Firebase Cloud Messaging sont expliqués. Des procédures pas à pas et des exemples de code sont inclus.

## <a name="touchandroidapp-fundamentalstouchindexmd"></a>[Entrées tactiles](~/android/app-fundamentals/touch/index.md)

Cette section explique les concepts et les détails de l’implémentation des gestes tactiles sur Android. Les API tactiles sont introduites et expliquées, suivies par une exploration des détecteurs de mouvement.

## <a name="httpclient-stack-and-ssltlsandroidapp-fundamentalshttp-stackmd"></a>[Pile HttpClient et SSL/TLS](~/android/app-fundamentals/http-stack.md)

Cette section décrit la pile HttpClient et les sélecteurs d’implémentation SSL/TLS pour Android. Ces paramètres déterminent les implémentations HttpClient et SSL/TLS qui seront utilisées par vos applications Xamarin. Android.

## <a name="writing-responsive-applicationswriting-responsive-appsmd"></a>[Écriture d’applications réactives](writing-responsive-apps.md)

Cet article explique comment utiliser le Threading pour maintenir la réactivité d’une application Xamarin. Android en déplaçant des tâches de longue durée sur un thread d’arrière-plan.
