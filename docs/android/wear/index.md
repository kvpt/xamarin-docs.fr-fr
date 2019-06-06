---
title: Android Wear
description: Création d’applications pour les appareils Android et portable.
ms.prod: xamarin
ms.assetid: 3BE4A128-2D88-4500-9E48-20375EA99A49
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/16/2018
ms.openlocfilehash: dda00760399572d714300f1487391212c6fa0998
ms.sourcegitcommit: d3f48bfe72bfe03aca247d47bc64bfbfad1d8071
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66740862"
---
# <a name="android-wear"></a>Android Wear

Android Wear est une version d’Android est conçu pour les appareils wearable comme les montres actives. Cette section inclut des instructions sur la façon d’installer et configurer les outils nécessaires pour le développement de l’usure, une procédure pas à pas détaillées pour la création de votre premier appareil Wear et une liste d’exemples que vous pouvez consulter pour porter des applications afin de créer votre propre.

##  <a name="getting-startedandroidwearget-startedindexmd"></a>[Prise en main](~/android/wear/get-started/index.md)

Introduit Android Wear, explique comment installer et configurer votre ordinateur pour le développement de l’usure et fournit les étapes pour vous aider à créer et exécuter votre première application Android Wear sur un émulateur ou un appareil Wear.

##  <a name="user-interfaceandroidwearuser-interfaceindexmd"></a>[Interface utilisateur](~/android/wear/user-interface/index.md)

Explique Android Wear contrôle et fournit des liens vers des exemples qui montrent comment utiliser ces contrôles.

##  <a name="platform-featuresandroidwearplatformindexmd"></a>[Fonctionnalités de la plateforme](~/android/wear/platform/index.md)

Documents dans cette section couvrent les fonctionnalités spécifiques à Android Wear. Vous trouverez ici une rubrique qui explique comment créer un WatchFace.

##  <a name="screen-sizesandroidwearscreen-sizesmd"></a>[Tailles d’écran](~/android/wear/screen-sizes.md)

Afficher un aperçu et optimiser votre interface utilisateur pour les tailles d’écran disponible.

##  <a name="deployment--testingandroidweardeploy-testindexmd"></a>[Déploiement et test](~/android/wear/deploy-test/index.md)

Explique comment déployer votre application Android Wear sur un appareil Android Wear ou émulateur Android configuré pour une usure. Il inclut également le débogage des conseils et des informations pour savoir comment configurer une connexion Bluetooth entre votre ordinateur de développement et d’un appareil Android.

##  <a name="wear-apishttpsdeveloperandroidcomreferenceandroidsupportwearable"></a>[API d’usure](https://developer.android.com/reference/android/support/wearable)

Le site de développement Android fournit des informations détaillées sur la clé API Wear comme [activité portable](https://developer.android.com/reference/android/support/wearable/activity/package-summary.html), [intentions](https://developer.android.com/reference/com/google/android/wearable/intent/package-summary.html), [authentification](https://developer.android.com/reference/android/support/wearable/authentication/package-summary.html), [ Complications](https://developer.android.com/reference/android/support/wearable/complications/package-summary.html), [Complications rendu](https://developer.android.com/reference/android/support/wearable/complications/rendering/package-summary.html), [Notifications](https://developer.android.com/reference/android/support/wearable/notifications/package-summary.html), [vues](https://developer.android.com/reference/android/support/wearable/view/package-summary.html), et [WatchFace](https://developer.android.com/reference/android/support/wearable/watchface/package-summary.html).



## <a name="samples"></a>Exemples

Vous pouvez trouver un nombre de [exemples](https://developer.xamarin.com/samples/android/Android%20Wear/) à l’aide de Android Wear (ou accédez directement à [github](https://github.com/xamarin/monodroid-samples/tree/master/wear)).

|Exemple|Description|Capture d'écran|
|--- |--- |--- |
|[SkeletonWear](https://developer.xamarin.com/samples/monodroid/wear/SkeletonWear/)|Un exemple simple des principes fondamentaux de projets portable, y compris les GridViewPager et notifications interactives.|![Capture d’écran de Skeletonwear](images/skeleton.png)|
|[WatchViewStub](https://developer.xamarin.com/samples/monodroid/wear/WatchViewStub/)|Une démonstration simple du contrôle WatchViewStub qui détecte la forme de l’écran et charge automatiquement la disposition correcte. Voir comment WatchViewStub fonctionne le **Resources/layout/main_activity.xml** mise en page.|![Capture d’écran de WatchViewStub](images/watchview.png)|
|[RecipeAssistant](https://developer.xamarin.com/samples/monodroid/wear/RecipeAssistant/)|Démonstration de pages de notification de l’usure, sous la forme d’étapes de recette. Notifications sont créées dans RecipeService.cs.|![Capture d’écran de RecipeAssistant](images/recipeassist.png)|
|[ElizaChat](https://developer.xamarin.com/samples/monodroid/wear/ElizaChat/)|Exemple amusant de l’interaction avec un « assistant » appelé Eliza, à l’aide de notifications interactives usure pour créer une conversation à l’aide de réponses toutes.|![Capture d’écran de ElizaChat](images/eliza.png)|
|[GridViewPager](https://developer.xamarin.com/samples/monodroid/wear/GridViewPager/)|GridViewPager implémente le modèle de navigation 2D, où l’utilisateur passe verticalement puis horizontalement pour naviguer dans les options et le contenu.|![Capture d’écran de GridViewPager](images/gridviewpager.png)|
|[WatchFace](https://developer.xamarin.com/samples/monodroid/wear/WatchFace)|WatchFace est un personnalisé cadran de montre avec analogique-style heure, minute et secondes mains. Cet exemple montre comment créer un service de visage espion qui dessine l’heure actuelle et de visibilité et mode ambiante gère les événements de modification. Il inclut un récepteur de diffusion qui écoute les modifications de fuseau horaire et met automatiquement à jour l’heure en conséquence.|![Capture d’écran de WatchFace](images/gridviewpager.png)|


##  <a name="videos"></a>Vidéos

Découvrez ces vidéo liens qui traitent de Xamarin.Android avec usure prennent en charge :

|Description|Capture d'écran|
|--- |--- |
|[Android L et bien plus encore](https://blog.xamarin.com/webinar-recording-android-l-and-so-much-more/) &ndash; Android L Developer Preview introduit une multitude de nouvelles API permettant aux développeurs de tirer parti de, y compris les documents de conception, les notifications et les nouvelles animations, pour citer que quelques.|![Capture d’écran vidéo de présentation](images/video-android-l.png)|
|[C#est dans Mes oreilles et mes yeux : Google Glass et Android Wear](https://www.youtube.com/watch?v=80H8tXByZQc) &ndash; informatique portable peut sembler quelque chose à partir de l’avenir (ou un épisode de l’Inspecteur Gadget), mais beaucoup de gens déjà adoptent le futur dès aujourd'hui ! C#les développeurs savent et ont déjà les outils et les compétences d’exploiter la puissance d’appareils wearable (à partir de l’évolution 2014).|![Capture d’écran vidéo de présentation](images/video-eyes-ears.png)|
|[Quelles sont les nouveautés dans Xamarin.Android](https://www.youtube.com/watch?v=Gpqc2XZIQfU) &ndash; L Android, Android Wear, Android TV, Android Auto, Material Design et ART ; ce que fait cette moyenne pour vous en tant qu’un développeur Xamarin ? à partir de l’évolution 2014.|![Capture d’écran vidéo de présentation](Images/video-whats-new.png)|


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
