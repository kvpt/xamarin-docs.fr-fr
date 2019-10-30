---
title: Création de services Android
description: Ce guide décrit les services Xamarin. Android, qui sont des composants Android qui permettent d’effectuer des tâches sans interface utilisateur active. Les services sont très couramment utilisés pour les tâches exécutées en arrière-plan, telles que les calculs longs, le téléchargement de fichiers, la musique, etc. Il explique les différents scénarios pour lesquels les services sont adaptés et montre comment les implémenter pour effectuer des tâches en arrière-plan longues, ainsi que pour fournir une interface pour les appels de procédure distante.
ms.prod: xamarin
ms.assetid: BA371A59-6F7A-F62A-02FC-28253504ACC9
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/19/2018
ms.openlocfilehash: b427bbadc72ca557a37c652e853674fdf849c2c8
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73024557"
---
# <a name="creating-android-services"></a>Création de services Android

_Ce guide décrit les services Xamarin. Android, qui sont des composants Android qui permettent d’effectuer des tâches sans interface utilisateur active. Les services sont très couramment utilisés pour les tâches exécutées en arrière-plan, telles que les calculs longs, le téléchargement de fichiers, la musique, etc. Il explique les différents scénarios pour lesquels les services sont adaptés et montre comment les implémenter pour effectuer des tâches en arrière-plan longues, ainsi que pour fournir une interface pour les appels de procédure distante._

## <a name="android-services-overview"></a>Vue d’ensemble des services Android

Les applications mobiles ne sont pas similaires aux applications de bureau. Les ordinateurs de bureau ont des quantités une multitude de ressources, telles que l’espace d’écran, la mémoire, l’espace de stockage et une alimentation connectée, mais pas les appareils mobiles. Ces contraintes forcent les applications mobiles à se comporter différemment. Par exemple, le petit écran sur un appareil mobile signifie généralement qu’une seule application (par exemple, activité) est visible à la fois. Les autres activités sont déplacées vers l’arrière-plan et transmises dans un état suspendu dans lequel elles ne peuvent effectuer aucun travail. Toutefois, la seule raison pour laquelle une application Android est en arrière-plan ne signifie pas qu’il est impossible pour l’application de continuer à fonctionner. 

Les applications Android sont constituées d’au moins l’un des quatre composants principaux suivants : les _activités_, les _récepteurs de diffusion_, les _fournisseurs de contenu_et les _services_. Les activités sont la pierre angulaire de nombreuses applications Android exceptionnelles, car elles fournissent l’interface utilisateur qui permet à un utilisateur d’interagir avec l’application. Toutefois, lorsqu’il s’agit d’effectuer des tâches simultanées ou en arrière-plan, les activités ne sont pas toujours le meilleur choix.

Le mécanisme principal pour le travail en arrière-plan dans Android est le _service_. Un service Android est un composant conçu pour effectuer des tâches sans interface utilisateur. Un service peut télécharger un fichier, écouter de la musique ou appliquer un filtre à une image. Les services peuvent également être utilisés pour la communication interprocessus (_IPC_) entre les applications Android. Par exemple, une application Android peut utiliser le service de lecteur de musique provenant d’une autre application ou une application peut exposer des données (telles que les informations de contact d’une personne) à d’autres applications via un service. 

Les services et leur capacité à effectuer des tâches en arrière-plan sont essentiels pour fournir une interface utilisateur fluide et fluide. Toutes les applications Android ont un _thread principal_ (également appelé _thread d’interface utilisateur_) sur lequel les activités sont exécutées. Pour que l’appareil reste réactif, Android doit pouvoir mettre à jour l’interface utilisateur au tarif de 60 images par seconde. Si une application Android effectue trop de travail sur le thread principal, Android supprimera des frames, ce qui a pour effet d’afficher l’interface utilisateur saccadée (également parfois appelée _Janky_). Cela signifie que tout travail effectué sur le thread d’interface utilisateur doit se terminer dans l’intervalle de temps entre deux trames, soit environ 16 millisecondes (1 seconde toutes les trames 60). 

Pour résoudre ce problème, un développeur peut utiliser des threads dans une activité pour effectuer un travail qui bloquerait l’interface utilisateur. Toutefois, cela peut entraîner des problèmes. Il est tout à fait possible qu’Android détruise et recrée les instances multiples de l’activité. Toutefois, Android ne détruit pas automatiquement les threads, ce qui peut entraîner des fuites de mémoire. Voici un exemple : lorsque l' [appareil est pivoté](~/android/app-fundamentals/handling-rotation.md) &ndash; Android tente de détruire l’instance de l’activité, puis de recréer un nouveau :

![Lorsque l’appareil pivote, l’instance 1 est détruite et l’instance 2 est créée](images/image-01.png)

Il s’agit d’une fuite de mémoire potentielle &ndash; le thread créé par la première instance de l’activité sera toujours en cours d’exécution. Si le thread a une référence à la première instance de l’activité, cela empêchera Android de garbage collection de l’objet. Toutefois, la deuxième instance de l’activité est toujours créée (qui peut à son tour créer un nouveau thread). La rotation de l’appareil à plusieurs moments à la suite rapide peut épuiser toute la RAM et forcer Android à mettre fin à l’application entière pour récupérer de la mémoire.

En règle générale, si le travail à exécuter doit mettre en service une activité, un service doit être créé pour effectuer ce travail. Toutefois, si le travail n’est applicable que dans le contexte d’une activité, la création d’un thread pour effectuer le travail peut être plus appropriée. Par exemple, la création d’une miniature pour une photo qui vient d’être ajoutée à une application de la galerie photo devrait probablement se produire dans un service. Toutefois, un thread peut être plus approprié pour lire une musique qui ne doit être entendue que lorsqu’une activité est au premier plan.

Le travail en arrière-plan peut être divisé en deux classifications générales :

1. **Tâche à long terme** &ndash; il s’agit d’un travail en cours jusqu’à ce qu’il soit explicitement arrêté. Un exemple de _tâche à long terme_ est une application qui diffuse en continu de la musique ou qui doit surveiller les données collectées à partir d’un capteur. Ces tâches doivent s’exécuter même si l’application n’a pas d’interface utilisateur visible.
2. **Tâches périodiques** &ndash; (parfois appelées _travail_) une tâche périodique est de durée relativement brève (plusieurs secondes) et est exécutée selon une planification (par exemple, une fois par jour pendant une semaine ou peut-être une seule fois au cours des 60 prochaines secondes). Par exemple, vous téléchargez un fichier à partir d’Internet ou générez une miniature pour une image.

Il existe quatre types différents de services Android :

* Le service **lié** &ndash; un _service lié_ est un service qui est lié à un autre composant (généralement une activité). Un service lié fournit une interface qui permet au composant lié et au service d’interagir entre eux. Une fois qu’il n’y a plus de clients liés au service, Android arrête le service. 

* **`IntentService`** &ndash; un _`IntentService`_ est une sous-classe spécialisée de la classe `Service` qui simplifie la création et l’utilisation du service. Un `IntentService` est destiné à gérer des appels autonomes individuels. Contrairement à un service, qui peut gérer simultanément plusieurs appels, une `IntentService` est plus similaire à un _processeur de file d’attente de travail_ &ndash; le travail est mis en file d’attente et un `IntentService` traite chaque travail un par un sur un seul thread de travail. En règle générale, un`IntentService` n’est pas lié à une activité ou à un fragment. 

* **Service démarré** &ndash; un service _démarré_ est un service qui a été démarré par un autre composant Android (par exemple, une activité) et qui est exécuté en continu en arrière-plan jusqu’à ce qu’un élément indique explicitement au service de s’arrêter. Contrairement à un service lié, un service démarré n’a aucun client directement lié à celui-ci. Pour cette raison, il est important de concevoir des services démarrés afin qu’ils puissent être redémarrés correctement en fonction des besoins.

* Le service **hybride** &ndash; un service _hybride_ est un service qui a les caractéristiques d’un _service démarré_ et d’un _service lié_. Un service hybride peut être démarré par lorsqu’un composant y est lié ou qu’il peut être démarré par un événement. Un composant client peut ou ne peut pas être lié au service hybride. Un service hybride continue de s’exécuter jusqu’à ce qu’il soit explicitement invité à s’arrêter, ou jusqu’à ce qu’il n’y ait plus de clients liés.

Le type de service à utiliser dépend largement des exigences de l’application. En règle générale, un `IntentService` ou un service lié suffit pour la plupart des tâches qu’une application Android doit exécuter. par conséquent, il convient de donner la préférence à l’un de ces deux types de services. Une `IntentService` est un bon choix pour les tâches à « une seule capture », telles que le téléchargement d’un fichier, tandis qu’un service lié peut convenir lorsque des interactions fréquentes avec une activité/un fragment sont requises. 

Bien que la plupart des services s’exécutent en arrière-plan, il existe une sous-catégorie spéciale appelée _service de premier plan_. Il s’agit d’un service qui reçoit une priorité plus élevée (par rapport à un service normal) pour effectuer des tâches pour l’utilisateur (par exemple, écouter de la musique). 

Il est également possible d’exécuter un service dans son propre processus sur le même appareil, ce qui est parfois désigné sous le terme de service _distant_ ou de _service hors processus_. Cela nécessite plus d’efforts pour créer, mais peut être utile quand une application doit partager des fonctionnalités avec d’autres applications et peut, dans certains cas, améliorer l’expérience utilisateur d’une application. 

### <a name="background-execution-limits-in-android-80"></a>Limites d’exécution en arrière-plan dans Android 8,0

À partir de la 8,0 Android (niveau d’API 26), une application Android n’a plus la possibilité de s’exécuter librement en arrière-plan. Dans le premier plan, une application peut démarrer et exécuter des services sans restriction. Quand une application passe en arrière-plan, Android accorde à l’application un certain temps pour démarrer et utiliser les services. Une fois ce délai écoulé, l’application ne peut plus démarrer aucun service et les services qui ont été démarrés sont arrêtés. À ce stade, il n’est pas possible pour l’application d’effectuer des tâches. Android considère qu’une application est en premier plan si l’une des conditions suivantes est remplie :

* Il y a une activité visible (démarrée ou suspendue).
* L’application a démarré un service de premier plan.
* Une autre application est au premier plan et utilise des composants d’une application qui seraient autrement en arrière-plan. C’est le cas, par exemple, si l’application A, qui est au premier plan, est liée à un service fourni par l’application B. l’application B est alors également considérée au premier plan et n’est pas terminée par Android pour être en arrière-plan.

Dans certains cas, même si une application est en arrière-plan, Android met l’application en éveil et assouplit ces restrictions pendant quelques minutes, ce qui permet à l’application d’effectuer des tâches :

* Un message de Cloud Firebase de priorité élevée est reçu par l’application.
* L’application reçoit une diffusion. 
* L’application reçoit et exécute une `PendingIntent` en réponse à une notification.

Les applications Xamarin. Android existantes devront peut-être modifier la manière dont elles exécutent le travail en arrière-plan afin d’éviter tout problème susceptible de survenir sur Android 8,0. Voici quelques alternatives pratiques à un service Android :

* **Planifier le travail à exécuter en arrière-plan à l’aide du planificateur de travaux Android ou du [répartiteur de tâches Firebase](~/android/platform/firebase-job-dispatcher.md)**  &ndash; ces deux bibliothèques fournissent un Framework permettant aux applications de séparer le travail en arrière-plan dans les _travaux_, une unité de travail discrète. Les applications peuvent ensuite planifier le travail avec le système d’exploitation, ainsi que certains critères concernant le moment où le travail peut s’exécuter.
* **Démarrez le service au premier plan** &ndash; un service de premier plan est utile lorsque l’application doit effectuer une tâche en arrière-plan et que l’utilisateur peut avoir besoin d’interagir régulièrement avec cette tâche. Le service de premier plan affiche une notification persistante afin que l’utilisateur soit conscient que l’application exécute une tâche en arrière-plan et fournit également un moyen de surveiller ou d’interagir avec la tâche. Il peut s’agir, par exemple, d’une application de podcasting qui lit un podcast auprès de l’utilisateur ou peut-être en téléchargeant un épisode de podcast afin qu’il puisse être apprécié plus tard. 
* **Utilisez un message Firebase Cloud FCM (High priority message)** &ndash; lorsque Android reçoit un FCM de priorité élevée pour une application, cela permet à cette application d’exécuter des services en arrière-plan pendant une brève période. Il s’agit d’une bonne alternative à la présence d’un service d’arrière-plan qui interroge une application en arrière-plan. 
* **Différer le travail lorsque l’application arrive au premier plan** &ndash; si aucune des solutions précédentes n’est viable, les applications doivent développer leur propre façon de suspendre et de reprendre le travail lorsque l’application passe au premier plan.

## <a name="related-links"></a>Liens associés

* [Limites d’exécution en arrière-plan Android Oreo](https://www.youtube.com/watch?v=Pumf_4yjTMc)
