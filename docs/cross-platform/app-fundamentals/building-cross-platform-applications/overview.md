---
title: Vue d’ensemble de la création d’applications multiplateformes
description: Ce document fournit une vue d’ensemble de la création d’applications multiplateformes. Elle traite de la valeur de C#, des modèles de conception tels que MVC/MVVM et des interfaces utilisateur natives.
ms.prod: xamarin
ms.assetid: E442EEFB-FA9C-40E9-9668-5A3F915C8400
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 7d839e0141f14f4ba86897b128bf2a8c0a79548d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016911"
---
# <a name="building-cross-platform-applications-overview"></a>Vue d’ensemble de la création d’applications multiplateformes

Ce guide présente la plate-forme Xamarin et explique comment concevoir une application multiplateforme pour optimiser la réutilisation du code et offrir une expérience native de haute qualité sur toutes les plateformes mobiles principales : iOS, Android et Windows Phone.

L’approche utilisée dans ce document s’applique généralement aux applications de productivité et aux applications de jeu. Toutefois, l’accent est mis sur la productivité et l’utilitaire (applications sans jeu). Pour obtenir des conseils sur le développement de jeux interplateformes, consultez le [document Introduction aux documents monojeu](~/graphics-games/monogame/introduction/index.md) ou à l' [outils Visual Studio pour Unity](https://docs.microsoft.com/visualstudio/cross-platform/visual-studio-tools-for-unity) .

L’expression « écriture unique, exécuter partout » est souvent utilisée pour chantent les termes d’un code base unique qui s’exécute sans modification sur plusieurs plateformes. Bien qu’il présente l’avantage d’une réutilisation de code, cette approche entraîne souvent des applications qui possèdent un ensemble de fonctionnalités à dénominateur commun plus faible et une interface utilisateur générique qui ne s’intègre pas parfaitement aux plateformes cibles.

Xamarin n’est pas simplement une plateforme « écriture unique, exécution partout », car l’un de ses atouts est la possibilité d’implémenter des interfaces utilisateur natives spécifiquement pour chaque plateforme. Toutefois, avec une conception réfléchie, il est toujours possible de partager la majeure partie du code d’interface utilisateur et d’obtenir le meilleur des deux mondes : écrire le code de votre logique métier et le stockage de données une seule fois, et présenter des interfaces utilisateur natives sur chaque plateforme. Ce document décrit une approche architecturale générale pour atteindre cet objectif.

Voici un résumé des points clés pour la création d’applications multiplateformes Xamarin :

- **Utilisez C#**  -écrivez vos applications dans C#. Le code existant écrit C# dans peut être porté à iOS et Android à l’aide de Xamarin très facilement, et évidemment utilisé dans les applications Windows.
- **Utiliser les modèles de conception MVC ou MVVM** -développez l’interface utilisateur de votre application à l’aide du modèle modèle/vue/contrôleur. Concevez votre application à l’aide d’une approche de modèle/vue/contrôleur ou d’une approche de modèle/vue/ViewModel dans laquelle il existe une séparation claire entre le « modèle » et le reste. Identifiez les parties de votre application qui utiliseront des éléments d’interface utilisateur natifs de chaque plateforme (iOS, Android, Windows, Mac) et utilisez-la comme indication pour fractionner votre application en deux composants : « Core » et « interface utilisateur ».
- **Créer des interfaces utilisateur natives** : chaque application spécifique au système d’exploitation fournit une couche d’interface C# utilisateur différente (implémentée dans avec l’assistance des outils de conception d’interface utilisateur natifs) :

1. Sur iOS, utilisez les API UIKit pour créer des applications à l’aspect natif, en utilisant éventuellement le concepteur iOS de Xamarin pour créer votre interface utilisateur visuellement.
1. Sur Android, utilisez Android. views pour créer des applications à l’aspect natif, en tirant parti du concepteur d’interface utilisateur de Xamarin.
1. Sur Windows, vous utiliserez le XAML pour la couche de présentation, créé dans Visual Studio ou le concepteur d’interface utilisateur de Blend.
1. Sur Mac, vous allez utiliser des storyboards pour la couche de présentation créée dans Xcode.

Les projets Xamarin. Forms sont pris en charge sur toutes les plateformes et vous permettent de créer des interfaces utilisateur qui peuvent être partagées entre les plateformes à l’aide du langage XAML Xamarin. Forms. 

La quantité de réutilisation de code dépend en grande partie de la quantité de code conservé dans le noyau partagé et de la quantité de code spécifique à l’interface utilisateur. Le code principal est tout ce qui n’interagit pas directement avec l’utilisateur, mais il fournit à la place des services pour les parties de l’application qui collectent et affichent ces informations.

Pour augmenter la quantité de code à réutiliser, vous pouvez adopter des composants multiplateformes qui fournissent des services communs à l’ensemble de ces systèmes, tels que :

1. [SQLite-net pour le](https://www.nuget.org/packages/sqlite-net-pcl/) stockage SQL local,
1. Les [plug-ins Xamarin](https://xamarin.com/plugins) permettant d’accéder aux fonctionnalités spécifiques aux appareils, notamment l’appareil photo, les contacts et la géolocalisation,
1. Les [packages NuGet](https://nuget.org) compatibles avec les projets Xamarin, tels que [JSON.net](https://www.nuget.org/packages/Newtonsoft.Json/),
1. Utilisation des fonctionnalités du .NET Framework pour la mise en réseau, les services Web, les e/s et bien plus encore.

Certains de ces composants sont implémentés dans l’étude de cas *Tasky* .

 <a name="Separate_Reusable_Code_into_a_Core_Library" />

## <a name="separate-reusable-code-into-a-core-library"></a>Séparer le code réutilisable dans une bibliothèque principale

En suivant le principe de séparation de responsabilité en baparant l’architecture de votre application, puis en déplaçant les fonctionnalités principales indépendantes des plateformes dans une bibliothèque principale réutilisable, vous pouvez maximiser le partage du code entre les plateformes, comme illustré ci-dessous illustre

 ![](overview-images/layers2.png "By following the principle of separation of responsibility by layering your application architecture and then moving core functionality that is platform agnostic into a reusable core library, you can maximize code sharing across platforms")

 <a name="Case_Studies" />

## <a name="case-studies"></a>Études de cas

Il existe une étude de cas qui accompagne ce document : *Tasky Pro*. Chaque étude de cas traite de l’implémentation des concepts présentés dans ce document dans un exemple concret. Le code est open source et disponible sur [GitHub](https://github.com/xamarin/mobile-samples/).
