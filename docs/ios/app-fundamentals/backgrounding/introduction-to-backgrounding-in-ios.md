---
title: Introduction au backgrounding dans iOS
description: 'Ce document décrit l’arrière-plan dans iOS : les États d’application, les méthodes de cycle de vie des applications et l’actualisation des applications en arrière-plan.'
ms.prod: xamarin
ms.assetid: E214F2C7-E74E-46C7-B5BA-080B30D61250
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/24/2018
ms.openlocfilehash: 9ae1860d127ea87e4db830d8a9d299a66fdd0f67
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70766594"
---
# <a name="introduction-to-backgrounding-in-ios"></a>Introduction au backgrounding dans iOS

iOS régule très étroitement le traitement en arrière-plan et offre trois approches pour l’implémenter :

- **Inscrire une tâche en arrière-plan** : si une application doit effectuer une tâche importante, elle peut demander à IOS de ne pas interrompre la tâche lorsque l’application passe en arrière-plan. Par exemple, une application peut avoir besoin de terminer la journalisation d’un utilisateur ou de terminer le téléchargement d’un fichier volumineux.
- **S’inscrire en tant qu’application requise en arrière-plan** : une application peut s’inscrire sous la forme d’un type spécifique d’application qui a des exigences connues et spécifiques en matière d’arrière-plan, telles que *audio* , *VoIP* , *accessoire externe* , *Newsstand* , et l' *emplacement* . Ces applications sont des privilèges de traitement en arrière-plan continus, à condition qu’ils effectuent des tâches qui se trouvent dans les paramètres du type d’application inscrit.
- **Activer les mises à jour en arrière-plan** : les applications peuvent déclencher des mises à jour en arrière-plan avec la *surveillance des régions* ou en écoutant des *modifications d’emplacement importantes* . Depuis iOS 7, les applications peuvent également s’inscrire pour mettre à jour le contenu en arrière-plan à l’aide d’une *extraction en arrière-plan* ou de *notifications à distance* .

## <a name="application-states-and-application-delegate-methods"></a>États d’application et méthodes déléguées d’application

Avant de plonger dans le code pour le traitement en arrière-plan dans iOS, nous devons comprendre comment l’arrière-plan affecte le cycle de vie d’une application iOS.

Le cycle de vie des applications iOS est une collection d’États et de méthodes d’application pour se déplacer entre eux. Une application passe d’un État à l’autre, en fonction du comportement de l’utilisateur et des exigences en matière d’arrière-plan de l’application. Le déplacement est illustré par le diagramme suivant :

 [![](introduction-to-backgrounding-in-ios-images/applicationlifecycle-.png "Diagramme des États de l’application et des méthodes de délégué d’application")](introduction-to-backgrounding-in-ios-images/applicationlifecycle-.png#lightbox)

- **Pas en cours d’exécution** -l’application n’a pas encore été lancée sur l’appareil.
- **Running/active** -l’application est à l’écran et exécute du code au premier plan.
- **Inactif** -l’application est interrompue par un appel téléphonique, du texte ou une autre interruption entrante.
- En **arrière-plan** : l’application passe en arrière-plan et poursuit l’exécution du code en arrière-plan.
- **Suspendu** : si l’application n’a pas de code à exécuter en arrière-plan, ou si l’ensemble du code est terminé, l’application est *suspendue* par le système d’exploitation. Le processus d’une application interrompue est maintenu actif, mais l’application n’est pas en mesure d’exécuter du code dans cet État.
- **Revenir à ne pas exécuter/arrêter (rare)** -parfois, le processus de l’application est détruit et l’application revient à l’état *non exécuté* . Cela se produit en cas de mémoire insuffisante ou si l’utilisateur arrête manuellement l’application.

Depuis l’introduction de la prise en charge de la multitâche, iOS met rarement fin aux applications inactives, et conserve à la place leurs processus *suspendus* en mémoire. Le maintien de l’état actif d’une application garantit que l’application est lancée rapidement la prochaine fois que l’utilisateur l’ouvre. Cela signifie également que les applications peuvent passer librement de l’état *suspendu* à l’État en *arrière-plan* sans dessiner sur les ressources système. iOS 7 exploite cette fonctionnalité avec les nouvelles API qui permettent aux applications de suspendre les tâches en arrière-plan lorsque l’appareil passe en mode veille, à mettre à jour le contenu directement à partir de l’arrière-plan sans intervention de l’utilisateur, et bien plus encore. Nous aborderons les nouvelles API dans les [techniques d’arrière-plan iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/index.md).

## <a name="application-lifecycle-methods"></a>Méthodes de cycle de vie des applications

Quand une application change d’État, iOS notifie l’application via les méthodes d' `AppDelegate` événement de la classe :

- `OnActivated`: Cette méthode est appelée la première fois que l’application est lancée, et chaque fois que l’application revient au premier plan. Il s’agit de l’endroit où placer le code qui doit s’exécuter chaque fois que l’application est ouverte.
- `OnResignActivation`-Si l’utilisateur reçoit une interruption comme un SMS ou un appel téléphonique, cette méthode est appelée et l’application est temporairement désactivée. Si l’utilisateur accepte l’appel téléphonique, l’application est envoyée en arrière-plan.
- `DidEnterBackground`-Appelé lorsque l’application passe à l’État en arrière-plan, cette méthode donne à une application environ cinq secondes pour se préparer à un arrêt possible. Utilisez cette fois pour enregistrer les données et les tâches utilisateur et supprimer les informations sensibles de l’écran.
- `WillEnterForeground`-Lorsqu’un utilisateur retourne à une application en arrière-plan ou suspendue, et le lance au `WillEnterForeground` premier plan, est appelée. Il s’agit du temps nécessaire pour préparer l’application à prendre le premier plan en réhydratation tout `DidEnterBackground` état enregistré pendant la.  `OnActivated`est appelé immédiatement après la fin de cette méthode.
- `WillTerminate`-L’application est arrêtée et son processus est détruit. Cette méthode est appelée uniquement si le multitâche n’est pas disponible sur l’appareil ou la version du système d’exploitation, si la mémoire est insuffisante ou si l’utilisateur termine manuellement une application en arrière-plan. Notez que les applications suspendues qui sont terminées n' `WillTerminate` appellent pas.

Le diagramme suivant illustre la façon dont les États d’application et les méthodes de cycle de vie s’imbriquent :

 [![](introduction-to-backgrounding-in-ios-images/image2.png "Ce diagramme illustre comment les États de l’application et les méthodes de cycle de vie s’imbriquent")](introduction-to-backgrounding-in-ios-images/image2.png#lightbox)

## <a name="user-controls-for-backgrounding-in-ios"></a>Contrôles utilisateur pour l’arrière-plan dans iOS

iOS 7 a introduit plusieurs fonctionnalités qui permettent aux utilisateurs de mieux contrôler l’État en arrière-plan d’une application. Le commutateur d’application et le paramètre d’actualisation de l’application en arrière-plan affectent le cycle de vie de l’application.

### <a name="app-switcher"></a>Sélecteur d’applications

Le sélecteur d’application est une fonctionnalité de contrôle importante introduite dans iOS 7. Elle est lancée en double-appuyant sur le bouton d' **hébergement** et montre les applications dont les processus sont actifs :

 [![](introduction-to-backgrounding-in-ios-images/app-switcher-.png "Passage d’une application à l’autre à l’aide du sélecteur d’application")](introduction-to-backgrounding-in-ios-images/app-switcher-.png#lightbox)

À l’aide du sélecteur d’application, les utilisateurs peuvent faire défiler des instantanés de toutes les applications en arrière-plan et suspendues. Si vous appuyez sur une application, celle-ci est lancée au premier plan. Le balayage vers le haut supprime l’application de l’arrière-plan, terminant son processus. Nous examinerons de plus près le sélecteur d’application dans la [démonstration du cycle de vie des applications iOS](~/ios/app-fundamentals/backgrounding/application-lifecycle-demo.md) dans la section suivante.

> [!IMPORTANT]
> Le sélecteur d’application n’affiche pas de différence entre les applications en arrière-plan et celles suspendues.

### <a name="background-app-refresh-settings"></a>Paramètres d’actualisation des applications en arrière-plan

iOS 7 augmente le contrôle utilisateur sur le cycle de vie de l’application en permettant aux utilisateurs de refuser l’arrière-plan pour les applications [inscrites pour le traitement en arrière-plan](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/registering-applications-to-run-in-background.md). *Cela n’empêche pas les applications d’exécuter des tâches en arrière-plan*.

Les utilisateurs peuvent modifier ce paramètre en accédant à **paramètres > général > l’actualisation de l’application en arrière-plan** et en modifiant les privilèges d’arrière-plan d’une application sélectionnée. Si l’option actualisation de l’application en arrière-plan est désactivée, l’application est suspendue immédiatement après avoir entré l’arrière-plan et n’a pas pu effectuer de traitement en arrière-plan :

 [![](introduction-to-backgrounding-in-ios-images/settings-.png "Paramètres d’actualisation des applications en arrière-plan")](introduction-to-backgrounding-in-ios-images/settings-.png#lightbox)

Les développeurs peuvent vérifier l’état de l’application d' `BackgroundRefreshStatus` actualisation en arrière-plan avec l’API. Pour obtenir un exemple, reportez-vous à la [recette vérifier le paramètre d’actualisation en arrière-plan](https://github.com/xamarin/recipes/tree/master/Recipes/ios/multitasking/check_background_refresh_setting).

Nous avons abordé les principes de base du cycle de vie des applications iOS et les fonctionnalités de contrôle du cycle de vie des applications. Nous allons maintenant voir le cycle de vie de l’application iOS en action.
