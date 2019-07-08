---
title: Introduction au développement mobile
description: Ce document fournit une introduction au développement mobile. Il traite de Xamarin, de son fonctionnement et des applications qu’il génère.
ms.prod: xamarin
ms.assetid: 33C83E13-F3E5-17B4-6512-207F3D3C5AB6
author: asb3993
ms.author: amburns
ms.date: 03/28/2017
ms.openlocfilehash: 3b75ef6b0937248a43aa2e2ff3fc13a578d25d3c
ms.sourcegitcommit: 5f48dbd99a33acbb376a1703485c7b659df2111b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67467848"
---
# <a name="introduction-to-mobile-development"></a>Introduction au développement mobile

La création d’applications mobiles peut être aussi simple qu’ouvrir l’IDE, écrire et tester une application et soumettre le résultat à un App Store, le tout en une seule après-midi. Cela peut aussi consister en un processus extrêmement complexe impliquant une conception préalable rigoureuse, des tests d’utilisabilité, des tests d’assurance qualité sur des milliers d’appareils, un cycle de vie complet avec des versions bêta, puis un déploiement effectué de plusieurs façons différentes.

Ce document présente la plateforme Xamarin. Pour plus d’informations sur le *processus* de création d’applications mobiles, de la conception à la phase de tests, consultez [Introduction au cycle de vie de développement des logiciels mobiles](~/cross-platform/get-started/introduction-to-mobile-sdlc.md).

Consultez [Configuration système requise](~/cross-platform/get-started/requirements.md#macos-requirements) pour confirmer votre système.

## <a name="introduction-to-xamarin"></a>Introduction à Xamarin

Lorsqu’ils pensent à la création d’applications iOS et Android, la plupart des gens croient que les langages natifs, tels qu’Objective-C, Swift et Java, sont les seuls choix possibles. Pourtant, ces dernières années ont vu émerger un tout nouvel écosystème de plateformes pour la création d’applications mobiles.

Xamarin est unique en son genre. En effet, il propose un seul langage (C#), une bibliothèque de classes et un runtime qui fonctionne sur les plateformes mobiles iOS, Android et Windows Phone (notez que le langage natif de Windows Phone est déjà C#), tout en compilant des applications natives (non interprétées) dont les performances suffisent même aux jeux les plus exigeants.

Chacune de ces plateformes a son propre ensemble de fonctionnalités, et chacune d’elle varie dans sa capacité à écrire des applications natives, c’est-à-dire les applications qui sont compilées en code natif et qui interagissent de manière fluide avec le sous-système Java sous-jacent. Par exemple, certaines plateformes permettent uniquement la création d’applications en HTML et en JavaScript, alors que d’autres permettent uniquement de créer des applications à l’aide de langages de bas niveau tels que C ou C++. Certaines plateformes n’utilisent même pas de kit de ressources de contrôle natif.

Xamarin est unique, car il possède toute la puissance des plateformes natives, et propose en plus ses propres fonctionnalités, notamment :

1.   **Une liaison complète pour les SDK sous-jacents** : Xamarin contient des liaisons pour quasiment tous les SDK de plateforme sous-jacents, à la fois pour iOS et pour Android. En outre, ces liaisons sont fortement typées, ce qui signifie qu’il est facile de les parcourir et de les utiliser. En outre, elles fournissent un contrôle de type robuste au moment de la compilation et pendant le développement. De cette façon, vous obtenez moins d’erreurs d’exécution et des applications de meilleure qualité.
1.   **Objective-C, Java, C et C++ Interop** : Xamarin fournit des fonctionnalités qui permettent d’appeler directement les bibliothèques Objective-C, Java, C et C++. Ainsi, vous pouvez utiliser une large gamme de code tiers déjà écrit. En outre, cela vous permet de tirer parti des bibliothèques iOS et Android existantes qui sont écrites en C/C++, Java ou Objective-C. Xamarin fournit des projets de liaison qui permettent de lier facilement des bibliothèques Objective-C et Java natives à l’aide d’une syntaxe déclarative.
1.   **Des constructions de langage modernes** : les applications Xamarin sont écrites en C#. Ce langage moderne inclut des améliorations significatives par rapport à Objective-C et à Java, telles que les *fonctionnalités de langage dynamique, les *constructions fonctionnelles* comme les *expressions lambda, *LINQ et la *programmation parallèle*, des *génériques sophistiqués, et bien plus encore.
1.   **Une bibliothèque de classes de base très riche** : Les applications Xamarin utilisent la bibliothèque de classes de base .NET. Ce très grand ensemble de classes comprend des fonctionnalités complètes, simplifiées et puissantes, telles que la prise en charge du langage XML, des bases de données, de la sérialisation, des E/S, des chaînes et de la mise en réseau, et bien plus encore. Le code C# existant peut être compilé pour être utilisé dans une application. Ainsi, vous pouvez accéder à des milliers de bibliothèques qui vous fourniront des fonctionnalités dont ne dispose pas la bibliothèque de classes de base.
1.   **Un environnement IDE moderne** : Xamarin utilise Visual Studio pour Mac sur macOS et Visual Studio sur Windows. Ces deux environnements IDE modernes incluent des fonctionnalités telles que la saisie semi-automatique, un système de gestion sophistiqué pour les projets et les solutions, une bibliothèque complète de modèles de projets, un contrôle de code source intégré, et bien d’autres.
1.   **Une prise en charge mobile multiplateforme** : Xamarin offre une prise en charge multiplateforme élaborée pour les trois principales plateformes mobiles que sont iOS, Android et Windows Phone. Les applications peuvent être écrites pour partager jusqu’à 90 % de leur code. En outre, la bibliothèque Xamarin.Mobile fournit une API unifiée qui permet d’accéder aux ressources qui sont communes aux trois plateformes. Les développeurs mobiles qui ciblent ces trois plateformes mobiles peuvent ainsi réduire considérablement les coûts de développement et accélérer la mise sur le marché.

Les fonctionnalités de Xamarin viennent répondre aux besoins des développeurs qui souhaitent utiliser un langage et une plateforme modernes pour développer des applications mobiles multiplateformes.

> [!NOTE]
> Cette série « Bien démarrer » est axée sur la prise en main de la génération d’applications iOS et Android. Microsoft propose des informations sur le [développement de la plateforme Windows universelle (UWP)](https://docs.microsoft.com/windows/uwp/develop/) pour les tablettes et ordinateurs de bureau. Pour plus d’informations sur le développement multiplateforme avec Xamarin (y compris des applications UWP pour Windows), lisez le guide [Création d’applications multiplateformes](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md).

## <a name="how-does-xamarin-work"></a>Fonctionnement de Xamarin

Xamarin propose deux produits : Xamarin.iOS et Xamarin.Android. Ils s’appuient tous les deux sur *Mono*, qui est une version open source du .NET Framework, basée sur les normes ECMA de .NET. Mono est apparu quasiment en même temps que .NET Framework. Il peut être exécuté sur quasiment toutes les plateformes, telles que Linux, Unix, FreeBSD et macOS.

Sur iOS, le compilateur *AOT* (*Ahead-of-Time*) de Xamarin compile les applications Xamarin.iOS directement en code d’assembly ARM natif. Sur Android, le compilateur de Xamarin compile en *langage intermédiaire* (*IL*). Ensuite, au démarrage de l’application, une compilation *juste-à-temps* (*JIT*) est effectuée en assembly natif.

Dans les deux cas, les applications Xamarin utilisent un runtime qui gère automatiquement les tâches telles que l’allocation de mémoire, le nettoyage de la mémoire, l’interopérabilité des plateformes sous-jacentes, etc.

### <a name="xamariniosdll-and-monoandroiddll"></a>Xamarin.iOS.dll et Mono.Android.dll

Les applications Xamarin sont basées sur un sous-ensemble de la bibliothèque de classes de base .NET, appelé profil Xamarin Mobile. Ce profil a été créé spécialement pour les applications mobiles, et il est inclus dans MonoTouch.dll et Mono.Android.dll (pour iOS et Android respectivement). Ceci est très similaire à la façon dont les applications Silverlight (et Moonlight) sont basées sur le profil .NET Silverlight/Moonlight. En effet, le profil Xamarin Mobile est équivalent au profil Silverlight 4.0, mais contient des classes supplémentaires issues de la bibliothèque de classes de base.

Pour obtenir la liste complète des classes et des assemblys disponibles, consultez la [liste des assemblys Xamarin.iOS](~/cross-platform/internals/available-assemblies.md?context=xamarin/ios) et la [liste des assemblys Xamarin.Android](~/cross-platform/internals/available-assemblies.md?context=xamarin/android).

Outre la bibliothèque de classes de base, ces fichiers .dll incluent des wrappers pour quasiment tout le SDK iOS et Android SDK, ce qui permet aux API de SDK sous-jacentes d’être appelées directement à partir du code C#.

### <a name="application-output"></a>Sortie de l’application

Lorsque les applications Xamarin sont compilées, un paquet d’application est créé (un fichier .app dans iOS ou un fichier .apk dans Android). Ces fichiers ne se distinguent pas des paquets d’application générés à l’aide des IDE par défaut de la plateforme, et peuvent être déployés exactement de la même façon.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert le principe de fonctionnement de Xamarin, l’étape suivante consiste à commencer à créer une application à l’aide de l’un de ces guides :

- [**Bien démarrer avec Xamarin.Forms**](~/get-started/index.yml)
- [**Bien démarrer avec Xamarin.iOS**](~/ios/get-started/hello-ios/index.md)
- [**Bien démarrer avec Xamarin.Android**](~/android/get-started/hello-android/index.md)
- [**Bien démarrer avec Xamarin.Mac**](~/mac/get-started/hello-mac.md)

