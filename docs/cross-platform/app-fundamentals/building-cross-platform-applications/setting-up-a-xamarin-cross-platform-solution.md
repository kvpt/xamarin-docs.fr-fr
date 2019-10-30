---
title: 'Partie 3 : configuration d’une solution multiplateforme Xamarin'
description: Ce document explique comment configurer une solution multiplateforme dans Xamarin. Il aborde les différentes stratégies de partage de code, telles que les projets partagés et les .NET Standard.
ms.prod: xamarin
ms.assetid: 4139A6C2-D477-C563-C1AB-98CCD0D10A93
author: davidortinau
ms.author: daortin
ms.date: 03/27/2017
ms.openlocfilehash: 843887282c9a5af671d46699ae2f601fd32902e0
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016870"
---
# <a name="part-3---setting-up-a-xamarin-cross-platform-solution"></a>Partie 3 : configuration d’une solution multiplateforme Xamarin

Quelles que soient les plateformes utilisées, les projets Xamarin utilisent tous le même format de fichier de solution (le format de fichier de Visual Studio **. sln** ). Les solutions peuvent être partagées entre les environnements de développement, même si des projets individuels ne peuvent pas être chargés (par exemple, un projet Windows dans Visual Studio pour Mac).

Lors de la création d’une nouvelle application multiplateforme, la première étape consiste à créer une solution vide. Cette section explique ce qui se passe ensuite : configuration des projets pour la création d’applications mobiles multiplateforme.

 <a name="Sharing_Code" />

## <a name="sharing-code"></a>Partage de code

Reportez-vous au document [options de partage de code](~/cross-platform/app-fundamentals/code-sharing.md) pour obtenir une description détaillée de l’implémentation du partage de code entre les plateformes.

 <a name="Shared_Asset_Projects" />

### <a name="shared-projects"></a>Projets partagés

L’approche la plus simple pour partager des fichiers de code consiste à utiliser un [projet partagé](~/cross-platform/app-fundamentals/shared-projects.md).

Cette méthode vous permet de partager le même code entre différents projets de plateforme, et d’utiliser des directives de compilateur pour inclure différents chemins de code spécifiques à la plateforme.

 <a name="Portable_Class_Libraries" />

### <a name="portable-class-libraries-pcl"></a>Bibliothèques de classes portables (PCL)

Historiquement, un fichier projet .NET (et l’assembly qui en résulte) a été ciblé sur une version de Framework spécifique. Cela empêche le projet ou l’assembly d’être partagé par différents frameworks.

Une bibliothèque de classes portables (PCL) est un type spécial de projet qui peut être utilisé sur des plateformes d’interface CLI disparates, telles que Xamarin. iOS et Xamarin. Android, ainsi que WPF, plateforme Windows universelle et Xbox. La bibliothèque ne peut utiliser qu’un sous-ensemble du .NET Framework complet, limité par les plateformes ciblées.

Vous pouvez en savoir plus sur la [prise en charge de Xamarin pour les bibliothèques de classes portables](~/cross-platform/app-fundamentals/pcl.md) et suivre les instructions qui s’affichent pour voir comment fonctionne l' [exemple TaskyPortable](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable) .

### <a name="net-standard"></a>.NET Standard

Introduite dans 2016, les projets [.NET standard](~/cross-platform/app-fundamentals/net-standard.md) offrent un moyen simple de partager du code entre les plateformes et de produire des assemblys qui peuvent être utilisés sur Windows, les plateformes Xamarin (iOS, Android, Mac) et Linux.

Les bibliothèques de .NET Standard peuvent être créées et utilisées comme classes portables, à ceci près que les API disponibles dans chaque version (de 1,0 à 1,6) sont plus faciles à découvrir et que chaque version offre une compatibilité descendante avec des numéros de version inférieurs.

 <a name="Populating_the_Solution" />

## <a name="populating-the-solution"></a>Remplissage de la solution

Quelle que soit la méthode utilisée pour partager du code, la structure globale de la solution doit implémenter une architecture en couches qui encourage le partage de code.
L’approche Xamarin consiste à regrouper le code en deux types de projets :

- **Projet de base** : écrivez du code réutilisable à un seul endroit, pour le partager entre différentes plateformes. Utilisez les principes de l’encapsulation pour masquer les détails de l’implémentation dans la mesure du possible.
- **Projets d’application spécifiques** à la plateforme : consomment le code réutilisable avec le moins de couplage possible. Les fonctionnalités spécifiques à la plateforme sont ajoutées à ce niveau, basées sur les composants exposés dans le projet de base.

 <a name="Core_Project" />

### <a name="core-project"></a>Projet de base

Les projets de code partagé doivent uniquement faire référence à des assemblys disponibles sur toutes les plateformes, par ex. les espaces de noms Common Framework, comme `System`, `System.Core` et `System.Xml`.

Les projets partagés doivent implémenter autant de fonctionnalités non-interface utilisateur que possible, qui peut inclure les couches suivantes :

- **Couche de données** : code qui prend en charge le stockage de données physiques par exemple.  [SQLite-net](https://github.com/praeclarum/sqlite-net), une autre base de données comme [Realm.IO](https://realm.io/products/realm-mobile-database/) ou même des fichiers XML. Les classes de la couche de données sont normalement utilisées uniquement par la couche d’accès aux données.
- **Couche d’accès aux données** : définit une API qui prend en charge les opérations de données requises pour les fonctionnalités de l’application, telles que les méthodes d’accès aux listes de données, les éléments de données individuels et également les créer, les modifier et les supprimer.
- **Couche d’accès aux services** : couche facultative permettant de fournir des services Cloud à l’application. Contient le code qui accède aux ressources réseau distantes (services Web, téléchargements d’images, etc.) et éventuellement à la mise en cache des résultats.
- **Couche métier** : définition des classes de modèle et des classes facade ou Manager qui exposent les fonctionnalités aux applications spécifiques à la plateforme.

 <a name="Platform-Specific_Application_Projects" />

### <a name="platform-specific-application-projects"></a>Projets d’application spécifiques à la plateforme

Les projets spécifiques à la plateforme doivent faire référence aux assemblys requis pour la liaison au kit de développement logiciel (SDK) de chaque plateforme (Xamarin. iOS, Xamarin. Android, Xamarin. Mac ou Windows), ainsi qu’au projet de code partagé principal.

Les projets spécifiques à la plateforme doivent implémenter :

- **Couche application** : fonctionnalités spécifiques à la plateforme et liaison/conversion entre les objets de la couche métier et l’interface utilisateur.
- **Couche d’interface utilisateur** : écrans, contrôles d’interface utilisateur personnalisés, présentation de la logique de validation.

<a name="Example" />

### <a name="example"></a>Exemple

L’architecture de l’application est illustrée dans ce diagramme :

 [![](setting-up-a-xamarin-cross-platform-solution-images/conceptualarchitecture.png "The application architecture is illustrated in this diagram")](setting-up-a-xamarin-cross-platform-solution-images/conceptualarchitecture.png#lightbox)

Cette capture d’écran illustre la configuration d’une solution avec le projet de base partagé, iOS et les projets d’application Android. Le projet partagé contient le code relatif à chacune des couches architecturales (entreprise, service, données et code d’accès aux données) :

 ![](setting-up-a-xamarin-cross-platform-solution-images/core-solution-example.png "The Shared Project contains code relating to each of the architectural layers (Business, Service, Data and Data Access code)")

 <a name="Project_References" />

## <a name="project-references"></a>Références de projets

Les références de projet reflètent les dépendances d’un projet. Les projets de base limitent leurs références aux assemblys communs afin que le code soit facile à partager.
Les projets d’application spécifiques à la plateforme référencent le code partagé, ainsi que tous les assemblys spécifiques à la plateforme dont ils ont besoin pour tirer parti de la plateforme cible.

L’application projette chaque projet partagé de référence et contient le code de l’interface utilisateur requis pour présenter les fonctionnalités à l’utilisateur, comme indiqué dans les captures d’écran suivantes :

![](setting-up-a-xamarin-cross-platform-solution-images/solution-android.png "L’application projette chaque projet partagé de référence") ![](setting-up-a-xamarin-cross-platform-solution-images/solution-ios.png "L’application projette chaque projet partagé de référence")

Des exemples spécifiques de la façon dont les projets doivent être structurés sont fournis dans les études de cas.

 <a name="Adding_Files" />

## <a name="adding-files"></a>Ajout de fichiers

 <a name="Build_Action" />

### <a name="build-action"></a>Action de génération

Il est important de définir l’action de génération correcte pour certains types de fichiers. Cette liste affiche l’action de génération pour certains types de fichiers courants :

- **Tous C# les fichiers** -action de génération : compiler
- **Images dans Xamarin. iOS & Windows** – action de génération : contenu
- **Fichiers XIB et Storyboard dans Xamarin. iOS** – action de génération : InterfaceDefinition
- **Images et dispositions AXML dans Android** – action de génération : AndroidResource
- **Fichiers XAML dans les projets Windows** – action de génération : page
- **Fichiers XAML Xamarin. Forms** – action de génération : EmbeddedResource

En général, l’IDE détecte le type de fichier et suggère l’action de génération correcte.

 <a name="Case_Sensitivity" />

### <a name="case-sensitivity"></a>Respect de la casse

Enfin, n’oubliez pas que certaines plateformes ont des systèmes de fichiers sensibles à la casse (par exemple,
iOS et Android) Assurez-vous d’utiliser une norme d’affectation de noms de fichier cohérente et assurez-vous que les noms de fichiers que vous utilisez dans le code correspondent exactement au système de fichiers. Cela est particulièrement important pour les images et autres ressources que vous référencez dans le code.
