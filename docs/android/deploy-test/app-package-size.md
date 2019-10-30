---
title: Taille des packages d'application
description: Cet article examine les éléments constitutifs des packages d’application Xamarin.Android, ainsi que les stratégies associées permettant de les déployer avec efficacité au cours des phases de débogage et de mise en production.
ms.prod: xamarin
ms.assetid: 8D70CDDD-3D3C-9949-8045-AB8F93D18E74
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/05/2018
ms.openlocfilehash: ff1bc56ab1cf02e9e5354da94bebd0661da34bc5
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028155"
---
# <a name="application-package-size"></a>Taille des packages d'application

_Cet article examine les éléments constitutifs des packages d’application Xamarin.Android, ainsi que les stratégies associées permettant de les déployer avec efficacité au cours des phases de débogage et de mise en production._

## <a name="overview"></a>Vue d'ensemble

Xamarin.Android utilise une variété de mécanismes pour réduire la taille des package tout en conservant un débogage et un processus de déploiement de mise en production efficaces. Dans cet article, nous examinons la mise en production de Xamarin.Android et le flux de travail du déploiement de débogage, ainsi que la façon dont la plateforme Xamarin.Android assure la création et la mise en production par nos soins de petits packages d’application.

## <a name="release-packages"></a>Mettre des packages en production

Pour expédier une application entièrement contenue, le package doit inclure l’application, les bibliothèques associées, le contenu, le runtime Mono et les assemblys de bibliothèque de classes de Base (BCL) requis. Par exemple, si nous prenons le modèle par défaut de « Hello World », le contenu d’une version de package complète ressemble à ceci :

[![Taille du package avant l’éditeur de liens](app-package-size-images/hello-world-package-size-before-linker.png)](app-package-size-images/hello-world-package-size-before-linker.png#lightbox)

15,8 Mo est une taille de téléchargement supérieure à celle souhaitée. Les bibliothèques BCL sont le problème. En effet, elles incluent mscorlib, System et Mono.Android, qui fournissent un grand nombre des composants nécessaires pour exécuter votre application. Toutefois, elles fournissent également des fonctionnalités que vous n’utilisez peut-être pas dans votre application. Il peut donc être préférable d’exclure ces composants.

Lorsque nous générons une application pour la distribuer, nous exécutons un processus appelé liaison, qui examine l’application et supprime tout code qui n’est pas utilisé directement. Ce processus est similaire à la fonctionnalité fournie par le [nettoyage de la mémoire](~/android/internals/garbage-collection.md) pour la mémoire allouée par tas. Mais, au lieu d’agir sur des objets, la liaison agit sur votre code. Par exemple, il y a un espace de noms entier dans System.dll pour envoyer et recevoir du courrier électronique, mais si votre application n’utilise pas cette fonctionnalité, ce code occupe inutilement de l’espace. Après l’exécution de l’éditeur de liens sur l’application Hello World, notre package ressemble maintenant à ça :

[![Taille du package après l’éditeur de liens](app-package-size-images/hello-world-package-size-after-linker.png)](app-package-size-images/hello-world-package-size-after-linker.png#lightbox)

Nous voyons que cette opération supprime une quantité non négligeable de la bibliothèque BCL qui n’était pas utilisée. Notez que la taille finale de la bibliothèque BCL dépend de ce que l’application utilise réellement. Par exemple, si nous examinons un exemple d’application plus importante appelée ApiDemo, nous pouvons voir que la taille du composant BCL a augmenté, car ApiDemo utilise plus de bibliothèque BCL que Hello World :

![taille du package ApiDemo après l’éditeur de liens](app-package-size-images/api-demo-package-size-after-linker.png)

Comme vous le voyez ici, taille de votre package d’application sera généralement d’environ 2,9 Mo de plus que celle de votre application et de ses dépendances.

## <a name="debug-packages"></a>Déboguer des Packages

Les choses sont un peu différentes pour les versions debug. Lors du déploiement répété sur un appareil, une application doit être la plus rapide possible. C’est pourquoi nous optimisons les packages de débogage en misant sur la vitesse de déploiement plutôt que sur la taille.

Android est relativement lent pour copier et installer un package. Nous voulons donc que le package soit le plus petit possible. Comme expliqué précédemment, l’éditeur de liens est une possibilité pour réduire la taille du package. Toutefois, la liaison est lente et nous souhaitons généralement ne déployer que les parties de l’application qui ont été modifiés depuis le dernier déploiement. Pour ce faire, nous séparons notre application des composants Xamarin.Android essentiels.

La première fois que nous déboguons sur un appareil, nous copions deux packages volumineux appelés *Runtime partagé* et *Plateforme partagée*. Runtime partagé contient le Runtime Mono et la bibliothèque BCL, tandis que Plateforme partagée contient des assemblys spécifiques au niveau d’API Android :

[![Taille du package de runtime partagé](app-package-size-images/shared-runtime-package-size.png)](app-package-size-images/shared-runtime-package-size.png#lightbox)

La copie de ces composants essentiels n’est effectuée qu’une fois, car elle prend un certain temps. Elle permet cependant à des applications ultérieures de s’exécuter en mode débogage pour les utiliser. Enfin, nous copions l’application réelle, ce qui est petite et rapide :

![l’application réelle est faible](app-package-size-images/hello-world-debug-application-no-link.png)

### <a name="fast-assembly-deployment"></a>Déploiement d’assembly rapide

L’option de build de *déploiement d’assembly rapide* peut être utilisée pour réduire encore plus la taille du package d’installation de débogage en n’incluant pas les assemblys dans le package de l’application, en installant une seule fois les assemblys directement sur l’appareil et en ne copiant que les fichiers modifiés depuis le dernier déploiement.

Pour activer *le déploiement d’assembly rapide*, procédez comme suit :

1. Cliquez avec le bouton droit sur le projet Android dans l’Explorateur de solutions et sélectionnez **Options**.

2. Dans la boîte de dialogue Options du projet, sélectionnez **Build Android** :  

    ![Options de projet Android](app-package-size-images/fastdev0.png)

3. Cochez les cases **case runtime partagé Mono** et **Déploiement d’assembly rapide** :  

    ![cases à cocher sélectionnées sous l’onglet Packages](app-package-size-images/fastdev.png)

4. Cliquez sur le bouton **OK** pour enregistrer les modifications et fermez la boîte de dialogue Options du projet.

La prochaine fois que l’application sera générée pour le débogage, les assemblys seront installés directement sur l’appareil (s’ils ne l’ont pas déjà été) et un plus petit package d’application (qui n’inclut pas les assemblys) sera installé sur l’appareil. Cela réduit le temps nécessaire pour que les modifications apportées à l’application s’exécutent pour le test.

Après avoir surmonté le premier déploiement long du runtime partagé et de la plateforme partagée, à chaque fois que nous modifions l’application, nous pouvons déployer rapidement et facilement la nouvelle version, ce qui nous permet de bénéficier d’un cycle rapide de modification/de déploiement/d’exécution.

## <a name="summary"></a>Récapitulatif

Dans cet article, nous avons examiné les facettes de la mise en production de Xamarin.Android et des packages de profil de débogage. En outre, nous avons étudié les stratégies que Mono pour la plateforme Android utilise pour faciliter un déploiement efficace des packages lors des étapes de débogage et de mise en production du développement.
