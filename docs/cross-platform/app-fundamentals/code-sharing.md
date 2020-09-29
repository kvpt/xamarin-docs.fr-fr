---
title: Vue d’ensemble du partage de code
description: 'Ce document compare les différentes méthodes de partage de code entre les projets multiplateformes : les projets partagés, les bibliothèques de classes portables et les .NET Standard, y compris les avantages et inconvénients de chacun d’entre eux.'
ms.prod: xamarin
ms.assetid: B73675D2-09A3-14C1-E41E-20352B819B53
author: davidortinau
ms.author: daortin
ms.date: 08/06/2018
ms.openlocfilehash: b097a0549db6178576d9e10eb3282c88e96b9ddb
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453296"
---
# <a name="sharing-code-overview"></a>Vue d’ensemble du partage de code

_Ce document compare les différentes méthodes de partage de code entre les projets multiplateforme : .NET Standard, les projets partagés et les bibliothèques de classes portables, y compris les avantages et inconvénients de chacun._

Il existe trois méthodes pour partager du code entre des applications multiplateformes :

- [**Bibliothèques de .NET standard**](#Net_Standard) : les projets .NET standard peuvent implémenter du code à partager entre plusieurs plateformes et peuvent accéder à un grand nombre d’API .net (selon la version). .NET Standard 1,0-1,6 implémentent progressivement des ensembles d’API plus volumineux, tandis que .NET Standard 2,0 offre la meilleure couverture de la BCL .NET (y compris les API .NET disponibles dans les applications Xamarin).
- [**Projets partagés**](#Shared_Projects) : utilisez le type de projet ressource partagée pour organiser votre code source, et utilisez les `#if` directives de compilateur nécessaires pour gérer les exigences spécifiques à la plateforme.
- [**Bibliothèques de classes portables**](#Portable_Class_Libraries) (obsolètes) : les bibliothèques de classes portables (classes portables) peuvent cibler plusieurs plateformes avec une surface d’API commune et utiliser des interfaces pour fournir des fonctionnalités spécifiques à la plateforme. Les classes portables sont déconseillés dans les dernières versions de Visual Studio utilisent à la &ndash; place .NET standard.

L’objectif d’une stratégie de partage de code est de prendre en charge l’architecture illustrée dans ce diagramme, où un seul code base peut être utilisé par plusieurs plateformes.

 ![Architecture d’application de code partagé](code-sharing-images/conceptualarchitecture.png "Architecture d’application de code partagé")

Cet article compare les méthodes disponibles pour vous aider à choisir le type de projet adapté à vos applications.

<a name="Net_Standard"></a>

## <a name="net-standard-libraries"></a>Bibliothèques .NET Standard

Les bibliothèques de [.NET standard](~/cross-platform/app-fundamentals/net-standard.md) fournissent un ensemble bien défini de bibliothèques de classes de base qui peuvent être référencées dans différents types de projets, y compris des projets multiplateformes tels que Xamarin. Android et Xamarin. iOS. .NET Standard 2,0 est recommandé pour une compatibilité maximale avec le code d' .NET Framework existant.

![Diagramme de .NET Standard](code-sharing-images/netstandard.png "Diagramme de .NET Standard")

### <a name="benefits"></a>Avantages

- Vous permet de partager du code entre plusieurs projets.
- Les opérations de refactorisation mettent toujours à jour toutes les références affectées.
- Une plus grande surface de la bibliothèque de classes de base .NET (BCL) est disponible que les profils PCL. En particulier, .NET Standard 2,0 a presque la même surface d’API que le .NET Framework et est recommandé pour les nouvelles applications et le portage des classes portables existants.

### <a name="disadvantages"></a>Inconvénients

- Impossible d’utiliser des directives de compilateur comme `#if __IOS__` .

### <a name="remarks"></a>Remarques

.NET Standard est [semblable à la bibliothèque PCL](/dotnet/standard/net-standard#comparison-to-portable-class-libraries), mais avec un modèle plus simple pour la prise en charge des plateformes et un plus grand nombre de classes de la BCL.

<a name="Shared_Projects"></a>

## <a name="shared-projects"></a>Projets partagés

Les [projets partagés](~/cross-platform/app-fundamentals/shared-projects.md) contiennent des fichiers de code et des ressources qui sont incluses dans tout projet qui les référence. Les projets de partage ne produisent pas leur propre sortie compilée.

Cette capture d’écran montre un fichier solution contenant trois projets d’application (pour Android, iOS et Windows), avec un projet **partagé** qui contient des fichiers de code source C# courants :

![Solution de projet partagé](code-sharing-images/sharedsolution.png "Solution de projet partagé")

L’architecture conceptuelle est présentée dans le diagramme suivant, où chaque projet comprend tous les fichiers sources partagés :

![Diagramme de projet partagé](code-sharing-images/sharedassetproject.png "Diagramme de projet partagé")

### <a name="example"></a>Exemple

Une application multiplateforme qui prend en charge iOS, Android et Windows nécessiterait un projet d’application pour chaque plateforme. Le code commun réside dans le projet partagé.

Un exemple de solution contient les dossiers et projets suivants (les noms de projet ont été choisis pour l’expressivité, vos projets n’ont pas besoin de suivre ces instructions d’affectation de noms) :

- Projet **Shared –** Shared contenant le code commun à tous les projets.
- Projet d’application **AppAndroid** – Xamarin. Android.
- Projet d’application **AppiOS** – Xamarin. iOS.
- **AppWindows** – projet d’application Windows.

Ainsi, les trois projets d’application partagent le même code source (les fichiers C# dans Shared). Toutes les modifications apportées au code partagé sont partagées entre les trois projets.

### <a name="benefits"></a>Avantages

- Vous permet de partager du code entre plusieurs projets.
- Le code partagé peut être une branche basée sur la plateforme à l’aide de directives de compilateur (par exemple, à l’aide de  `#if __ANDROID__` , comme indiqué dans le document  [Building Cross Platform applications](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) (en anglais).
- Les projets d’application peuvent inclure des références spécifiques à la plateforme que le code partagé peut utiliser (comme l’utilisation  `Community.CsharpSqlite.WP7` de dans l’exemple Tasky pour Windows Phone).

### <a name="disadvantages"></a>Inconvénients

- Les refactorisations qui affectent le code à l’intérieur des directives du compilateur « inactive » ne mettent pas à jour le code à l’intérieur de ces directives.
- Contrairement à la plupart des autres types de projets, un projet partagé n’a pas d’assembly de « sortie ». Pendant la compilation, les fichiers sont traités comme faisant partie du projet de référence et compilés dans cet assembly. Si vous souhaitez partager votre code en tant qu’assembly, .NET Standard ou les bibliothèques de classes portables constituent une meilleure solution.

<a name="Shared_Remarks"></a>

### <a name="remarks"></a>Remarques

Une bonne solution pour les développeurs d’applications qui écrivent du code destiné uniquement au partage dans leur application (et non distribuant à d’autres développeurs).

<a name="Portable_Class_Libraries"></a>

## <a name="portable-class-libraries"></a>Bibliothèques de classes portables

> [!TIP]
> .NET Standard bibliothèques 2,0 sont recommandées sur les bibliothèques de classes portables.

Les bibliothèques de classes portables sont [présentées en détail ici](~/cross-platform/app-fundamentals/pcl.md).

![Diagramme de la bibliothèque de classes portables](code-sharing-images/portableclasslibrary.png "Diagramme de la bibliothèque de classes portables")

### <a name="benefits"></a>Avantages

- Vous permet de partager du code entre plusieurs projets.
- Les opérations de refactorisation mettent toujours à jour toutes les références affectées.

### <a name="disadvantages"></a>Inconvénients

- Obsolètes dans les dernières versions de Visual Studio, les bibliothèques .NET Standard sont recommandées à la place. Reportez-vous à cette [explication des différences](/dotnet/standard/net-standard#comparison-to-portable-class-libraries) entre PCL et .NET standard.
- Impossible d’utiliser les directives du compilateur.
- Seul un sous-ensemble du .NET Framework peut être utilisé, déterminé par le profil sélectionné (pour plus d’informations, consultez  [Introduction à PCL](~/cross-platform/app-fundamentals/pcl.md) ).

### <a name="remarks"></a>Remarques

Le modèle PCL est considéré comme déconseillé dans les dernières versions de Visual Studio.

## <a name="summary"></a>Résumé

La stratégie de partage de code que vous choisissez sera pilotée par les plateformes que vous ciblez. Choisissez une méthode qui convient le mieux à votre projet.

.NET Standard est le meilleur choix pour la création de bibliothèques de code pouvant être partagées (notamment la publication sur NuGet). Les projets partagés fonctionnent bien pour les développeurs d’applications qui envisagent d’utiliser un grand nombre de fonctionnalités spécifiques à la plateforme dans leurs applications multiplateformes.

Bien que les projets PCL continuent à être pris en charge dans Visual Studio, .NET Standard est recommandé pour les nouveaux projets.

## <a name="related-links"></a>Liens connexes

- [Création d’applications multiplateformes (document principal)](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [Bibliothèques de classes portables](~/cross-platform/app-fundamentals/pcl.md)
- [Projets partagés](~/cross-platform/app-fundamentals/shared-projects.md)
- [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)
- [Étude de cas : Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)
- [Exemple Tasky (GitHub)](https://github.com/xamarin/mobile-samples/tree/master/Tasky)
- [Exemple Tasky utilisant PCL (GitHub)](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)