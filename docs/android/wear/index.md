---
title: Android Wear
description: Création d’applications pour les appareils Android Android.
ms.prod: xamarin
ms.assetid: 3BE4A128-2D88-4500-9E48-20375EA99A49
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/16/2018
ms.openlocfilehash: 63fb9c3b1484026cb390a8a475c711562dfd0771
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028591"
---
# <a name="android-wear"></a>Android Wear

Android usure est une version d’Android conçue pour des appareils mobiles tels que des espions intelligents. Cette section contient des instructions sur la façon d’installer et de configurer les outils requis pour l’usure du développement, une procédure pas à pas pour la création de votre premier appareil d’usure, ainsi qu’une liste d’exemples auxquels vous pouvez vous référer pour créer vos propres applications d’usure.

## <a name="getting-startedandroidwearget-startedindexmd"></a>[Bien démarrer](~/android/wear/get-started/index.md)

Présente l’usure d’Android, explique comment installer et configurer votre ordinateur pour le développement de l’usure et fournit des étapes pour vous aider à créer et exécuter votre première application Android d’usure sur un émulateur ou un appareil d’usure.

## <a name="user-interfaceandroidwearuser-interfaceindexmd"></a>[Interface utilisateur](~/android/wear/user-interface/index.md)

Explique les contrôles spécifiques à Android usure et fournit des liens vers des exemples qui illustrent l’utilisation de ces contrôles.

## <a name="platform-featuresandroidwearplatformindexmd"></a>[Fonctionnalités de la plateforme](~/android/wear/platform/index.md)

Les documents de cette section couvrent les fonctionnalités spécifiques à Android usure. Vous trouverez ici une rubrique qui décrit comment créer un WatchFace.

## <a name="screen-sizesandroidwearscreen-sizesmd"></a>[Tailles d'écran](~/android/wear/screen-sizes.md)

Affichez l’aperçu et optimisez votre interface utilisateur pour les tailles d’écran disponibles.

## <a name="deployment--testingandroidweardeploy-testindexmd"></a>[Déploiement et test](~/android/wear/deploy-test/index.md)

Explique comment déployer votre application Android usure sur un appareil Android ou sur un émulateur Android configuré pour l’usure. Il comprend également des conseils de débogage et des informations sur la configuration d’une connexion Bluetooth entre votre ordinateur de développement et un appareil Android.

## <a name="wear-apishttpsdeveloperandroidcomreferenceandroidsupportwearable"></a>[API d’usure](https://developer.android.com/reference/android/support/wearable)

Le site de développement Android fournit des informations détaillées sur les API clé d’usure, telles que l' [activité portable](https://developer.android.com/reference/android/support/wearable/activity/package-summary.html), les [intentions](https://developer.android.com/reference/com/google/android/wearable/intent/package-summary.html), [l’authentification](https://developer.android.com/reference/android/support/wearable/authentication/package-summary.html), les [complications](https://developer.android.com/reference/android/support/wearable/complications/package-summary.html), le [rendu des complications](https://developer.android.com/reference/android/support/wearable/complications/rendering/package-summary.html), les [notifications](https://developer.android.com/reference/android/support/wearable/notifications/package-summary.html), [ Views](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)et [WatchFace](https://developer.android.com/reference/android/support/wearable/watchface/package-summary.html).

## <a name="samples"></a>Exemples

Vous pouvez trouver un certain nombre d' [exemples](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Android+wear) utilisant Android usure (ou accéder directement à [GitHub](https://github.com/xamarin/monodroid-samples/tree/master/wear)).

|Exemple|Description|Capture d'écran|
|--- |--- |--- |
|[SkeletonWear](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-skeletonwear)|Exemple simple des principes de base des projets à porter, y compris GridViewPager et les notifications interactives.|![Capture d’écran de Skeletonwear](images/skeleton.png)|
|[WatchViewStub](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-watchviewstub)|Démonstration simple du contrôle WatchViewStub qui détecte la forme d’écran et charge automatiquement la disposition correcte. Consultez fonctionnement de WatchViewStub dans la disposition **Resources/layout/main_activity. xml** .|![Capture d’écran de WatchViewStub](images/watchview.png)|
|[RecipeAssistant](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-recipeassistant)|Démonstration des pages de notification d’usure, sous forme d’étapes de recette. Les notifications sont créées dans RecipeService.cs.|![Capture d’écran de RecipeAssistant](images/recipeassist.png)|
|[ElizaChat](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-elizachat)|Exemple amusant d’interaction avec un « assistant personnel » appelé Eliza, en utilisant des notifications interactives pour créer une conversation à l’aide de réponses préparées.|![Capture d’écran de ElizaChat](images/eliza.png)|
|[GridViewPager](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-gridviewpager)|GridViewPager implémente le modèle de navigation 2D, où l’utilisateur fait défiler verticalement, puis horizontalement pour parcourir les options et le contenu.|![Capture d’écran de GridViewPager](images/gridviewpager.png)|
|[WatchFace](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-watchface)|WatchFace est un visage de montre personnalisé avec des mains d’heures, de minutes et de secondes de style analogique. Cet exemple montre comment créer un service de facette Watch qui dessine l’heure actuelle et gère les événements de modification de la visibilité et du mode ambiant. Il comprend un récepteur de diffusion qui écoute les modifications de fuseau horaire et met à jour automatiquement le temps en conséquence.|![Capture d’écran de WatchFace](images/gridviewpager.png)|

## <a name="videos"></a>Vidéos

Consultez ces liens vidéo qui décrivent Xamarin. Android avec prise en charge de l’usure :

|Description|Capture d'écran|
|--- |--- |
|[Android l et bien plus encore](https://blog.xamarin.com/webinar-recording-android-l-and-so-much-more/) &ndash; la version préliminaire des développeurs Android a introduit une multitude de nouvelles API qui permettent aux développeurs de tirer parti de, y compris la conception de matériaux, les notifications et les nouvelles animations, pour n’en nommer que quelques-uns.|![Capture vidéo de la présentation](images/video-android-l.png)|
|[est dans mes oreilles et dans mes yeux : Google Glass et Android usure&ndash;l’informatique portable peut paraître un peu comme un exemple du futur (ou un épisode de gadget inspecteur), mais de nombreuses personnes adoptent déjà le futur aujourd’hui. C# ](https://www.youtube.com/watch?v=80H8tXByZQc) C#les développeurs connaissent ce point et disposent déjà des outils et des compétences nécessaires pour tirer parti de la puissance des appareils mobiles (de l’évolution de 2014).|![Capture vidéo de la présentation](images/video-eyes-ears.png)|
|[Nouveautés de Xamarin. android](https://www.youtube.com/watch?v=Gpqc2XZIQfU) &ndash; Android L, Android usure, Android TV, Android auto, Material Design et art ; qu’est-ce que cela signifie pour vous en tant que développeur Xamarin ? de l’évolution de 2014.|![Capture vidéo de la présentation](Images/video-whats-new.png)|

<!--

March 18
https://blog.xamarin.com/android-wear/

August 14
https://blog.xamarin.com/android-l-developer-preview-android-wear-support/

August 27
https://blog.xamarin.com/tips-for-your-first-android-wear-app/

Watch Face
https://github.com/Redth/Xamarin.Wear.WatchFace
-->
