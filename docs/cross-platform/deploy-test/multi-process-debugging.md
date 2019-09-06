---
title: Débogage multiprocessus
description: Ce document décrit comment utiliser Visual Studio pour Mac afin de déboguer plusieurs processus en cours d’exécution en même temps. Cette fonctionnalité peut par exemple être utilisée pour déboguer une application mobile et un projet de service web simultanément.
ms.prod: xamarin
ms.assetid: 852F8AB1-F9E2-4126-9C8A-12500315C599
author: conceptdev
ms.author: crdun
ms.date: 03/24/2017
ms.openlocfilehash: db5d2dfcf96cdc1a89c0ecb2192b86f564e584ed
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290444"
---
# <a name="multi-process-debugging"></a>Débogage multiprocessus

Il est très courant que les solutions modernes développées dans Visual Studio pour Mac contiennent des projets qui ciblent des plateformes différentes. Par exemple, une solution peut contenir un projet d’application mobile qui s’appuie sur des données fournies par un projet de service web. Pendant le développement de la solution, un développeur peut avoir besoin que les deux projets soient exécutés simultanément afin de résoudre des erreurs. Avec la version [Cycle 9 de Xamarin](https://releases.xamarin.com/stable-release-cycle-9/), Visual Studio pour Mac peut désormais déboguer des processus qui sont exécutés en même temps. Il est donc possible de définir des points d’arrêt, d’inspecter des variables et d’afficher les threads de plusieurs projets en cours d’exécution. C’est ce que l’on appelle le _débogage multiprocessus_. 

Ce guide aborde certaines des modifications qui ont été apportées à Visual Studio pour Mac en vue de prendre en charge le débogage de plusieurs processus à la fois. Il explique également comment configurer les solutions pour le débogage multiprocessus, et comment joindre le débogueur à des processus existants avec Visual Studio pour Mac.

## <a name="requirements"></a>Configuration requise

Le débogage simultané de plusieurs processus nécessite l’utilisation de Visual Studio pour Mac.

## <a name="ide-changes"></a>Modifications de l’IDE

Pour aider les développeurs à déboguer plusieurs processus à la fois, Visual Studio pour Mac a apporté des modifications à son interface utilisateur. La **barre d’outils de débogage** a subi des changements, et la nouvelle section **Configuration de la solution** a été ajoutée au dossier **Options de la solution**. En outre, le **panneau Threads** affiche désormais les processus en cours d’exécution et les threads de chaque processus. Visual Studio pour Mac affiche également plusieurs panneaux de débogage (un pour chaque processus) pour certains éléments tels que la **sortie de l’application**.

### <a name="solution-configurations"></a>Configurations de solutions

Par défaut, Visual Studio pour Mac affiche un seul projet dans la zone **Configuration de la solution** de la barre d’outils de débogage. Au démarrage d’une session de débogage, c’est ce projet que Visual Studio pour Mac va lancer et joindre au débogueur.

Pour démarrer et déboguer plusieurs processus dans Visual Studio pour Mac, vous devez créer une _configuration de solution_. Une configuration de solution décrit les projets d’une solution qui doivent être inclus lorsqu’une session de débogage est démarrée après un clic de l’utilisateur sur le bouton **Démarrer**, ou lorsque l’utilisateur appuie sur les touches &#8984;&#8617; (**Cmd-Entrée**). Dans la capture d’écran suivante, un exemple de solution avec plusieurs configurations de solution est ouvert dans Visual Studio pour Mac :

![](multi-process-debugging-images/mpd01-xs.png "Solution avec plusieurs configurations de solution")

### <a name="parts-of-the-debug-toolbar"></a>Éléments de la barre d’outils de débogage

La barre d’outils de débogage permet désormais de sélectionner une configuration de solution via un menu contextuel. Cette capture d’écran montre les différents éléments qui composent la barre d’outils de débogage :

![](multi-process-debugging-images/mpd02-xs.png "Éléments de la barre d’outils de débogage")

1. **Configuration de la solution** : Vous pouvez cliquer sur la solution dans la barre d’outils de débogage et sélectionner la configuration dans le menu contextuel :

    ![](multi-process-debugging-images/mpd03-xs.png "Exemple de fenêtre contextuelle avec plusieurs configurations de solution")

2. **Cible de génération** : Identifie la cible de génération des projets. Cela reste inchangé par rapport aux versions antérieures de Visual Studio pour Mac.
3. **Appareils cibles** : Permet de sélectionner les appareils sur lesquels la solution va être exécutée. Il est possible de sélectionner un appareil ou un émulateur pour chaque projet :

    ![](multi-process-debugging-images/mpd04-xs.png "Fenêtre contextuelle montrant les appareils d’un projet")

### <a name="multiple-debug-pads"></a>Panneaux de débogage multiprocessus

Lorsque la configuration à plusieurs solutions est activée, certains panneaux Visual Studio pour Mac s’affichent plusieurs fois, c’est-à-dire, une fois pour chaque processus. La capture d’écran suivante montre deux panneaux **Sortie de l’application** pour une solution exécutant deux projets :

![](multi-process-debugging-images/mpd05-xs.png "Panneau de sortie d’application pour une configuration de solution")

### <a name="multiple-processes-and-the-_active-thread_"></a>Options Multiprocessus et _Thread actif_

Lorsque vous rencontrez un point d’arrêt dans un processus, celui-ci suspend l’exécution, pendant que les autres processus continuent de s’exécuter. Dans un scénario à processus unique, Visual Studio pour Mac peut facilement afficher des informations, telles que les threads, les variables locales et la sortie de l’application, dans un même ensemble de panneaux. Toutefois, lorsqu’il existe plusieurs processus avec plusieurs points d’arrêt et potentiellement plusieurs threads, le développeur peut se retrouver submergé par les informations d’une même session de débogage, où les informations de tous les threads et de tous les processus s’affichent en même temps.

Pour résoudre ce problème, Visual Studio pour Mac n’affiche qu’un thread à la fois. C’est ce qu’on appelle le _thread actif_. Le premier thread qui s’arrête à un point d’arrêt est considéré comme le _thread actif_. Le thread actif est le thread dont s’occupe actuellement le développeur. Les commandes de débogage, telles que **Pas à pas principal** &#8679;&#8984;O (Maj-Cmd-O), seront exécutées pour le thread actif.

Le **panneau Thread** affiche des informations sur tous les processus et tous les threads examinés dans la configuration de solution, et donne des indices visuels permettant de déterminer quel thread est actif :

![](multi-process-debugging-images/mpd06-xs.png "Panneau Thread pour une configuration de solution")

Les threads sont regroupés selon le processus qui les héberge. Le nom du projet et l’ID du thread actif sont affichés en gras, et une flèche pointant vers la droite s’affiche en regard du thread actif. Dans la capture d’écran précédente, le **thread n°1** sous l’**ID de processus 48703** (**FirstProject**) est le thread actif.

Lorsque vous déboguez plusieurs processus, vous pouvez basculer le thread actif pour afficher les informations de débogage de ce processus (ou thread) à l’aide du **panneau Thread**. Pour basculer le thread actif, sélectionnez le thread souhaité dans le **panneau Thread**, puis double-cliquez dessus.

#### <a name="stepping-through-code-when-multiple-projects-are-stopped"></a>Exécution pas à pas du code lorsque plusieurs projets sont arrêtés

Lorsque plusieurs projets ont des points d’arrêt, Visual Studio pour Mac suspend tous les processus. Vous pouvez uniquement **exécuter du code pas à pas** dans le thread actif. Les autres processus seront suspendus jusqu’à ce qu’une modification de l’étendue permette au débogueur de déplacer le focus du thread actif vers un autre thread. Regardez la capture d’écran suivante, où Visual Studio pour Mac débogue deux projets :

![](multi-process-debugging-images/mpd09-xs.png  "Débogage de deux projets dans Visual Studio pour Mac")

Dans cette capture d’écran, chaque solution a son propre point d’arrêt. Lorsque le débogage commence, le premier point d’arrêt rencontré se trouve sur la **ligne 10** de `MainClass` dans **SecondProject**. Étant donné que les deux projets ont des points d’arrêt, chaque processus est arrêté. Une fois que le point d’arrêt est rencontré, Visual Studio pour Mac exécute du code pas à pas dans le thread actif après chaque appel de l’**exécution pas à pas**.

L’exécution pas à pas du code est limitée au thread actif. Par conséquent, Visual Studio pour Mac exécute le code ligne par ligne, pendant que les autres processus sont suspendus.

Si l’on reprend la capture d’écran précédente comme exemple, lorsque la boucle `for` est terminée, Visual Studio pour Mac permet à **FirstProject** de s’exécuter jusqu’à ce qu’il rencontre le point d’arrêt à **ligne 11** dans `MainClass`. Pour chaque commande **Pas à pas principal**, le débogueur avance ligne par ligne dans **FirstProject**, jusqu’à ce que les algorithmes heuristiques internes de Visual Studio pour Mac rebasculent le thread actif vers  **SecondProject**.

Si seul un des projets a un point d’arrêt défini, alors seul ce processus sera suspendu. Les autres projets continuent de s’exécuter jusqu’à ce qu’ils soient suspendus par le développeur ou qu’un point d’arrêt soit ajouté.

### <a name="pausing-and-resuming-a-processes"></a>Suspension et reprise d’un processus

Pour suspendre et reprendre un processus, cliquez avec le bouton droit sur le processus, puis sélectionnez **Suspendre** ou **Reprendre** dans le menu contextuel :

![](multi-process-debugging-images/mpd08-xs.png "Options Suspendre et Reprendre dans le panneau Thread")

L’apparence de la barre d’outils de débogage change en fonction de l’état des projets en cours de débogage. Lorsque plusieurs projets sont en cours d’exécution, la barre d’outils de débogage affiche les deux boutons **Suspendre** et **Reprendre**, lorsqu’il y a au moins un projet en cours d’exécution et un projet suspendu :

![](multi-process-debugging-images/mpd07-xs.png  "Barre d’outils de débogage")

Quand vous cliquez sur le bouton **Suspendre** de la **barre d’outils de débogage**, vous suspendez tous les processus en cours de débogage. Quand vous cliquez sur le bouton **Reprendre**, vous relancez l’exécution de tous les processus suspendus.

### <a name="debugging-a-second-project"></a>Débogage d’un deuxième projet

Vous pouvez également déboguer un deuxième projet une fois que le premier projet a été démarré par Visual Studio pour Mac. Une fois le premier projet démarré, **cliquez avec le bouton droit* sur le projet dans le **Panneau Solutions**, puis sélectionnez **Démarrer le débogage de l’élément** :

![](multi-process-debugging-images/mpd13-xs.png  "Démarrer le débogage de l’élément")

## <a name="creating-a-solution-configuration"></a>Création d’une configuration de solution

Une _configuration de solution_ indique à Visual Studio pour Mac quel projet doit être exécuté lorsqu’une session de débogage est démarrée à l’aide du bouton **Démarrer**. Une même solution peut avoir plusieurs configurations. Vous pouvez ainsi spécifier les projets qui doivent être exécutés lors du débogage du projet.

Pour créer une configuration de solution dans Xamarin Studio :

1. Ouvrez la boîte de dialogue **Options de la solution** dans Visual Studio pour Mac, puis sélectionnez **Exécuter > Configurations** :

    ![](multi-process-debugging-images/mpd10-xs.png "Configuration de solution dans la boîte de dialogue Options de la solution")

2. Cliquez sur le bouton **Nouveau**, entrez le nom de la nouvelle configuration de solution, puis cliquez sur **Créer**. La nouvelle configuration de solution s’affiche dans la fenêtre **Configurations** :

    ![](multi-process-debugging-images/mpd11-xs.png  "Nommage d’une nouvelle configuration de solution")

3. Sélectionnez la nouvelle configuration de série de tests dans la liste de configurations. La boîte de dialogue **Options de la solution** affiche tous les projets de la solution. Cochez les projets qui doivent être démarrés lorsqu’une session de débogage est lancée :

    ![](multi-process-debugging-images/mpd12-xs.png "Sélection des projets qui doivent être démarrés")

La configuration de solution **MultipleProjects** s’affiche désormais dans la **barre d’outils de débogage**, ce qui permet au développeur de déboguer simultanément les deux projets.

## <a name="summary"></a>Récapitulatif

Ce guide a abordé le débogage de plusieurs processus dans Visual Studio pour Mac. Il a abordé également certaines des modifications apportées à l’IDE pour permettre le débogage simultané de plusieurs processus, ainsi que les changements de comportement qui découlent de ces modifications.

## <a name="related-links"></a>Liens associés

- [Notes de mise en production Xamarin Cycle 9](https://releases.xamarin.com/stable-release-cycle-9/)
