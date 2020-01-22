---
title: Qu’est-ce que Xamarin ?
description: Cet article présente Xamarin et les bibliothèques associées.
ms.prod: xamarin
ms.assetid: 33C83E13-F3E5-17B4-6512-207F3D3C5AB6
ms.custom: video
author: profexorgeek
ms.author: jusjohns
ms.date: 09/16/2019
ms.openlocfilehash: 34763804e9833224721ea32f9c7e6200dd5faba7
ms.sourcegitcommit: ba83c107c87b015dbcc9db13964fe111a0573dca
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2020
ms.locfileid: "75607878"
---
# <a name="what-is-xamarin"></a>Qu’est-ce que Xamarin ?

[![des captures d’écran de l’exemple d’application Xamarin dans iOS et Android](what-is-xamarin-images/xamarin-app-cropped.png)](what-is-xamarin-images/xamarin-app.png#lightbox)

Xamarin est une plateforme open source permettant de créer des applications modernes et performantes pour iOS, Android et Windows avec .NET. Xamarin est une couche d’abstraction qui gère la communication entre le code partagé et le code de la plateforme sous-jacente. Xamarin s’exécute dans un environnement géré qui fournit des commodités, telles que l’allocation de mémoire et les garbage collection.

Xamarin permet aux développeurs de partager une moyenne de 90% de leur application entre les plateformes. Ce modèle permet aux développeurs d’écrire toute leur logique métier dans une seule langue (ou de réutiliser le code d’application existant), mais d’obtenir des performances natives, d’avoir une apparence et une convivialité sur chaque plateforme.

Les applications Xamarin peuvent être écrites sur PC ou Mac et être compilées dans des packages d’application natifs, tels qu’un fichier **. apk** sur Android ou un fichier **. Loi** sur iOS.

> [!NOTE]
> La compilation et le déploiement d’applications pour iOS nécessitent actuellement un ordinateur MacOS. Pour plus d’informations sur les conditions requises pour le développement, consultez [Configuration système requise](~/cross-platform/get-started/requirements.md#macos-requirements).

## <a name="who-xamarin-is-for"></a>À qui Xamarin est destiné

Xamarin est destiné aux développeurs qui ont les objectifs suivants :

- Partagez du code, des tests et de la logique métier entre les plateformes.
- Écrire des applications multiplateformes C# dans avec Visual Studio.

## <a name="how-xamarin-works"></a>Fonctionnement de Xamarin

![Diagramme de l’architecture Xamarin](what-is-xamarin-images/xamarin-architecture.png)

Le diagramme illustre l’architecture globale d’une application Xamarin multiplateforme. Xamarin vous permet de créer une interface utilisateur native sur chaque plateforme et d’écrire C# une logique métier dans qui est partagée entre les plateformes. Dans la plupart des cas, 80% du code d’application peut être partagé à l’aide de Xamarin.

Xamarin repose sur **mono**, une version open source de la .NET Framework basée sur les normes ECMA .net. Mono a existé pour presque autant que le .NET Framework lui-même et s’exécute sur la plupart des plateformes, notamment Linux, UNIX, FreeBSD et macOS. L’environnement d’exécution mono gère automatiquement des tâches telles que l’allocation de mémoire, les garbage collection et l’interopérabilité avec les plateformes sous-jacentes.

Pour plus d’informations sur l’architecture spécifique à une plateforme, consultez [Xamarin. Android](#xamarinandroid) et [Xamarin. iOS](#xamarinios).

### <a name="added-features"></a>Fonctionnalités ajoutées

Xamarin combine les capacités des plateformes natives et ajoute un certain nombre de fonctionnalités, notamment :

1. **Liaison complète pour les kits de développement logiciel (SDK) sous-jacents** – Xamarin contient des liaisons pour presque l’ensemble des kits de développement logiciel de la plateforme sous-jacente dans iOS et Android. En outre, ces liaisons sont fortement typées, ce qui signifie qu’il est facile de les parcourir et de les utiliser. En outre, elles fournissent un contrôle de type robuste au moment de la compilation et pendant le développement. Les liaisons fortement typées conduisent à moins d’erreurs d’exécution et d’applications de qualité supérieure.
1. **Objective-C, Java, C et C++ Interop** – Xamarin fournit des fonctionnalités permettant d’appeler directement objective-c, Java, c C++ et Libraries, ce qui vous donne la possibilité d’utiliser un grand tableau de code tiers. Cette fonctionnalité vous permet d’utiliser les bibliothèques iOS et Android existantes écrites en Objective-C, Java ouC++C/. En outre, Xamarin propose des projets de liaison qui vous permettent de lier des bibliothèques objective-C et Java natives à l’aide d’une syntaxe déclarative.
1. **Constructions de langage modernes** : les applications Xamarin sont écrites C#dans, un langage moderne qui comprend des améliorations significatives par rapport à Objective-C et Java, telles que les fonctionnalités de langage dynamique, les constructions fonctionnelles telles que les expressions lambda, LINQ, la programmation parallèle, les génériques, et bien plus encore.
1. **Bibliothèque de classes de base (BCL) robuste** : les applications Xamarin utilisent la BCL .net, une vaste collection de classes qui ont des fonctionnalités complètes et rationalisées, telles que des fonctionnalités puissantes de XML, de base de données, de sérialisation, d’e/s, de chaîne et de mise en réseau, et bien plus encore. Le C# code existant peut être compilé pour une utilisation dans une application, ce qui permet d’accéder à des milliers de bibliothèques qui ajoutent des fonctionnalités au-delà de la BCL.
1. **Environnement de développement intégré (IDE) moderne** : Xamarin utilise Visual Studio, un IDE moderne qui comprend des fonctionnalités telles que la saisie semi-automatique de code, un système sophistiqué de gestion de projets et de solutions, une bibliothèque de modèles de projet complète, un contrôle de code source intégré, et bien plus encore.
1. **Prise en charge multiplateforme mobile** : Xamarin offre une prise en charge multiplateforme sophistiquée pour les trois principales plateformes d’iOS, Android et Windows. Les applications peuvent être écrites pour partager jusqu’à 90% de leur code, et Xamarin. Essentials offre une API unifiée pour accéder aux ressources communes sur les trois plateformes. Le code partagé peut réduire de manière significative les coûts de développement et le délai de mise sur le marché pour les développeurs mobiles.

### <a name="xamarinandroid"></a>Xamarin.Android

[![diagramme d’architecture Xamarin. Android](what-is-xamarin-images/android-architecture-cropped.png)](what-is-xamarin-images/android-architecture.png#lightbox)

Les applications Xamarin. Android se C# compilent en **langage intermédiaire (il)** qui est ensuite compilé **juste-à-temps (JIT)** dans un assembly natif au lancement de l’application. Les applications Xamarin. Android s’exécutent dans l’environnement d’exécution mono, côte à côte avec la machine virtuelle Android Runtime (ART). Xamarin fournit des liaisons .NET aux espaces de noms Android. * et Java. *. L’environnement d’exécution mono appelle ces espaces de noms via des **wrappers pouvant être appelés par Managed Callable (MCW)** et fournit à l’art des **wrappers pouvant être appelés par Android (ACW)** , ce qui permet aux deux environnements d’appeler du code entre eux.

Pour plus d’informations, consultez [architecture Xamarin. Android](~/android/internals/architecture.md).

### <a name="xamarinios"></a>Xamarin.iOS

[![diagramme d’architecture Xamarin. iOS](what-is-xamarin-images/ios-architecture-cropped.png)](what-is-xamarin-images/ios-architecture.png#lightbox)

Les applications Xamarin. iOS sont **entièrement** compilées à partir du C# code assembleur ARM natif. Xamarin utilise des **sélecteurs** pour exposer objective-c C# à Managed et C# les **registraires** afin d’exposer le code managé à objective-c. Les sélecteurs et les bureaux d’enregistrement sont appelés des « liaisons » et permettent à C# objective-C et de communiquer.

Pour plus d’informations, consultez [architecture Xamarin. iOS](~/ios/internals/architecture.md).

### <a name="xamarinessentials"></a>Xamarin.Essentials

Xamarin. Essentials est une bibliothèque qui fournit des API multiplateforme pour les fonctionnalités d’appareils natives. Comme Xamarin lui-même, Xamarin. Essentials est une abstraction qui simplifie le processus d’accès aux fonctionnalités natives. Voici quelques exemples de fonctionnalités fournies par Xamarin. Essentials :

- Informations sur l’appareil
- Système de fichiers
- Accéléromètre
- Numéroteur téléphonique
- Conversion de texte par synthèse vocale
- Verrouillage de l’écran

Pour plus d’informations, consultez [Xamarin. Essentials](~/essentials/index.md).

### <a name="xamarinforms"></a>Xamarin.Forms

Xamarin. Forms est une infrastructure d’interface utilisateur Open source. Xamarin. Forms permet aux développeurs de créer des applications iOS, Android et Windows à partir d’un code base partagé unique. Xamarin. Forms permet aux développeurs de créer des interfaces utilisateur en XAML avec code C#-behind dans. Ces interfaces utilisateur sont rendues sous la forme de contrôles natifs performants sur chaque plateforme. Voici quelques exemples de fonctionnalités fournies par Xamarin. Forms :

- Langage de l’interface utilisateur XAML
- Liaison de données
- Mouvements
- Effects (Effets)
- Styles

Pour plus d’informations, consultez [Xamarin. Forms](~/xamarin-forms/index.yml).

## <a name="get-started"></a>Prise en main

Les guides suivants vous aideront à créer votre première application à l’aide de Xamarin :

- [Prise en main de Xamarin. Forms](~/xamarin-forms/index.yml)
- [Prise en main de Xamarin. Android](~/android/index.yml)
- [Prise en main de Xamarin. iOS](~/ios/index.yml)
- [Prise en main de Xamarin. Mac](~/mac/index.yml)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Series/Xamarin-101/What-is-Xamarin-1-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
