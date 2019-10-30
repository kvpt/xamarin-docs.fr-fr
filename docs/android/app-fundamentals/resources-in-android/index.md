---
title: Ressources Android
description: Cet article présente le concept de ressources Android dans Xamarin. Android et explique comment les utiliser. Il explique comment utiliser des ressources dans votre application Android pour prendre en charge la localisation d’applications et plusieurs appareils, y compris des tailles d’écran et des densités différentes.
ms.prod: xamarin
ms.assetid: C0DCC856-FA36-04CD-443F-68D26075649E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/01/2018
ms.openlocfilehash: 1ff4d9d896aaa5f290402a49aa4b4bd1f1e00aaf
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025063"
---
# <a name="android-resources"></a>Ressources Android

_Cet article présente le concept de ressources Android dans Xamarin. Android et explique comment les utiliser. Il explique comment utiliser des ressources dans votre application Android pour prendre en charge la localisation d’applications et plusieurs appareils, y compris des tailles d’écran et des densités différentes._

## <a name="overview"></a>Vue d'ensemble

Une application Android est rarement simplement le code source. Il existe souvent de nombreux autres fichiers qui composent une application : des vidéos, des images, des polices et des fichiers audio, juste pour n’en nommer que quelques-uns. Collectivement, ces fichiers de code non source sont considérés comme des ressources et sont compilés (avec le code source) pendant le processus de génération et empaquetés en tant que APK pour la distribution et l’installation sur les appareils :

![Diagramme de Packaging](images/packaging-diagram.png)

Les ressources offrent plusieurs avantages pour une application Android :

- La **séparation de code** &ndash; sépare le code source des images, des chaînes, des menus, des animations, des couleurs, etc. Ces ressources peuvent vous aider considérablement lors de la localisation.

- **Cibler plusieurs appareils** &ndash; fournit une prise en charge plus simple de différentes configurations d’appareils sans modification du code.

- La **vérification au moment de la compilation** &ndash; ressources sont statiques et compilées dans l’application. Cela permet de vérifier l’utilisation des ressources au moment de la compilation, lorsqu’il est facile de détecter et de corriger les erreurs, par opposition à l’exécution lorsqu’il est plus difficile à localiser et coûteux à corriger.

Lorsqu’un nouveau projet Xamarin. Android est démarré, un répertoire spécial appelé ressources est créé, ainsi que certains sous-répertoires :

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Dossier et contenu des ressources](images/resources-folder-vs.png)

Dans l’image ci-dessus, les ressources d’application sont organisées en fonction de leur type dans ces sous-répertoires : les images seront placées dans le répertoire de **dessin** ; les affichages sont placés dans le sous-répertoire **Layout** , etc.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

![Dossier et contenu des ressources](images/resources-folder-xs.png)

Dans l’image ci-dessus, les ressources d’application sont organisées en fonction de leur type dans ces sous-répertoires : les images sont placées dans le répertoire **mipmap** ; les affichages sont placés dans le sous-répertoire **Layout** , etc.

-----

Il existe deux façons d’accéder à ces ressources dans une application Xamarin. Android : *par programme* dans le code et de *façon déclarative* dans XML à l’aide d’une syntaxe XML spéciale.

Ces ressources sont appelées *ressources par défaut* et sont utilisées par tous les appareils, sauf si une correspondance plus spécifique est spécifiée. En outre, chaque type de ressource peut éventuellement posséder d' *autres ressources* qu’Android peut utiliser pour cibler des appareils spécifiques. Par exemple, des ressources peuvent être fournies pour cibler les paramètres régionaux de l’utilisateur, la taille de l’écran, ou si l’appareil subit une rotation de 90 degrés du portrait vers le paysage, etc. Dans chacun de ces cas, Android chargera les ressources destinées à être utilisées par l’application sans effort de codage supplémentaire par le développeur.

Vous pouvez spécifier d’autres ressources en ajoutant une chaîne abrégée, appelée *qualificateur*, à la fin du répertoire contenant un type donné de ressources.

Par exemple, les **ressources/dessinables-de** spécifient les images pour les appareils qui sont définis sur des paramètres régionaux allemands, tandis que **ressources/dessinables-fr** contiendront des images pour les appareils définis sur des paramètres régionaux français. Vous pouvez voir un exemple de ressources de remplacement dans l’image ci-dessous, dans laquelle la même application est exécutée avec uniquement les paramètres régionaux de l’appareil qui changent :

![Exemples d’écrans pour différents paramètres régionaux](images/localized-screenshots.png)

Cet article aborde l’utilisation des ressources et aborde les sujets suivants :

- Les **concepts de base des ressources Android** &ndash; l’utilisation des ressources par défaut par programmation et de manière déclarative, en ajoutant des types de ressources tels que des images et des polices à une application.

- Les **configurations spécifiques** à l’appareil &ndash; prendre en charge les différentes résolutions et densités d’écran dans une application.

- La **localisation** &ndash; à l’aide de ressources pour prendre en charge les différentes régions dans lesquelles une application peut être utilisée.

## <a name="related-links"></a>Liens associés

- [Utilisation de ressources Android](~/android/app-fundamentals/resources-in-android/android-assets.md)
- [Notions de base de l’application](https://developer.android.com/guide/topics/fundamentals.html)
- [Ressources d'application](https://developer.android.com/guide/topics/resources/index.html)
- [Prise en charge de plusieurs écrans](https://developer.android.com/guide/practices/screens_support.html)
