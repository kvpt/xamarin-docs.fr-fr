---
title: 'Partie 3 : configuration d’une solution multiplateforme Xamarin'
description: Ce document explique comment configurer une solution multiplateforme dans Xamarin. Il aborde les différentes stratégies de partage de code, telles que les projets partagés et les .NET Standard.
ms.prod: xamarin
ms.assetid: 4139A6C2-D477-C563-C1AB-98CCD0D10A93
author: davidortinau
ms.author: daortin
ms.date: 03/27/2017
ms.openlocfilehash: e7cde22115830a845ed82aa907195521f36b6866
ms.sourcegitcommit: d8af612b6b3218fea396d2f180e92071c4d4bf92
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2020
ms.locfileid: "75663271"
---
# <a name="part-3---setting-up-a-xamarin-cross-platform-solution"></a>Partie 3 : configuration d’une solution multiplateforme Xamarin

Quelles que soient les plateformes utilisées, les projets Xamarin utilisent tous le même format de fichier de solution (le format de fichier de Visual Studio **. sln** ). Les solutions peuvent être partagées entre les environnements de développement, même si des projets individuels ne peuvent pas être chargés (par exemple, un projet Windows dans Visual Studio pour Mac).

Lors de la création d’une nouvelle application multiplateforme, la première étape consiste à créer une solution vide. Cette section explique ce qui se passe ensuite : configuration des projets pour la création d’applications mobiles multiplateforme.

## <a name="sharing-code"></a>Partage de code

Reportez-vous au document [options de partage de code](~/cross-platform/app-fundamentals/code-sharing.md) pour obtenir une description détaillée de l’implémentation du partage de code entre les plateformes.

### <a name="net-standard"></a>.NET Standard

Les projets [.NET standard](~/cross-platform/app-fundamentals/net-standard.md) offrent un moyen simple de partager du code entre les plateformes et de produire des assemblys qui peuvent être utilisés sur des plateformes Windows, Xamarin (iOS, Android, Mac) et Linux.
Il s’agit de la méthode recommandée pour partager du code pour les solutions Xamarin.

### <a name="other-options"></a>Autres options

Historiquement Xamarin utilisé les [bibliothèques de classes portables (classes portables)](~/cross-platform/app-fundamentals/pcl.md)] et les [projets partagés](~/cross-platform/app-fundamentals/shared-projects.md). Aucun de ces deux n’est recommandé pour les nouveaux projets. et vous devez envisager de migrer des applications existantes pour utiliser .NET Standard.

## <a name="populating-the-solution"></a>Remplissage de la solution

Quelle que soit la méthode utilisée pour partager du code, la structure globale de la solution doit implémenter une architecture en couches qui encourage le partage de code.
L’approche Xamarin consiste à regrouper le code en deux types de projets :

- **Projet principal (ou « partagé »)** : écrivez du code réutilisable dans un emplacement unique, à partager sur différentes plateformes. Utilisez les principes de l’encapsulation pour masquer les détails de l’implémentation dans la mesure du possible.
- **Projets d’application spécifiques** à la plateforme : consomment le code réutilisable avec le moins de couplage possible. Les fonctionnalités spécifiques à la plateforme sont ajoutées à ce niveau, basées sur les composants exposés dans le projet de base.

### <a name="core-project"></a>Projet de base

Les projets de base qui partagent du code doivent être .NET Standard et des assemblys de référence disponibles sur toutes les plateformes, par ex. les espaces de noms Common Framework, comme `System`, `System.Core` et `System.Xml`.

Les projets de base doivent implémenter autant de fonctionnalités non-interface utilisateur que possible, qui peut inclure les couches suivantes :

- **Couche de données** : code qui prend en charge le stockage de données physiques par exemple. [SQLite-net](https://www.nuget.org/packages/sqlite-net-pcl/) ou même des fichiers XML. Les classes de la couche de données sont normalement utilisées uniquement par la couche d’accès aux données.
- **Couche d’accès aux données** : définit une API qui prend en charge les opérations de données requises pour les fonctionnalités de l’application, telles que les méthodes d’accès aux listes de données, les éléments de données individuels et également les créer, les modifier et les supprimer.
- **Couche d’accès aux services** : couche facultative permettant de fournir des services Cloud à l’application. Contient le code qui accède aux ressources réseau distantes (services Web, téléchargements d’images, etc.) et éventuellement à la mise en cache des résultats.
- **Couche métier** : définition des classes de modèle et des classes facade ou Manager qui exposent les fonctionnalités aux applications spécifiques à la plateforme.

### <a name="platform-specific-application-projects"></a>Projets d’application spécifiques à la plateforme

Les projets spécifiques à la plateforme doivent faire référence aux assemblys requis pour la liaison au kit de développement logiciel (SDK) de chaque plateforme (Xamarin. iOS, Xamarin. Android, Xamarin. Mac ou Windows), ainsi qu’au projet .NET Standard.

Les projets spécifiques à la plateforme doivent implémenter :

- **Couche application** : fonctionnalités spécifiques à la plateforme et liaison/conversion entre les objets de la couche métier et l’interface utilisateur.
- **Couche d’interface utilisateur** : écrans, contrôles d’interface utilisateur personnalisés, présentation de la logique de validation.

## <a name="project-references"></a>Références de projets

Les références de projet reflètent les dépendances d’un projet. Les projets de base limitent leurs références aux assemblys communs afin que le code soit facile à partager.
Les projets d’application spécifiques à la plateforme font référence au projet .NET Standard, ainsi qu’à tous les autres assemblys spécifiques à la plateforme dont ils ont besoin pour tirer parti de la plateforme cible.

Des exemples spécifiques de la façon dont les projets doivent être structurés sont fournis dans les études de cas.

## <a name="adding-files"></a>Ajout de fichiers

### <a name="build-action"></a>Action de génération

Il est important de définir l’action de génération correcte pour certains types de fichiers. Cette liste affiche l’action de génération pour certains types de fichiers courants :

- **Tous C# les fichiers** -action de génération : compiler
- **Images dans Xamarin. iOS & Windows** – action de génération : contenu
- **Fichiers XIB et Storyboard dans Xamarin. iOS** – action de génération : InterfaceDefinition
- **Images et dispositions XML dans Android** – action de génération : AndroidResource
- **Fichiers XAML dans les projets Windows** – action de génération : page
- **Fichiers XAML Xamarin. Forms** – action de génération : EmbeddedResource

En général, l’IDE détecte le type de fichier et suggère l’action de génération correcte.

### <a name="case-sensitivity"></a>Respect de la casse

Enfin, n’oubliez pas que certaines plateformes ont des systèmes de fichiers sensibles à la casse (par exemple,
iOS et Android) Assurez-vous d’utiliser une norme d’affectation de noms de fichier cohérente et assurez-vous que les noms de fichiers que vous utilisez dans le code correspondent exactement au système de fichiers. Cela est particulièrement important pour les images et autres ressources que vous référencez dans le code.
