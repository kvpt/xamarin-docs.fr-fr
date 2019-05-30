---
title: 'Partie 3 : configurer une Solution multiplateforme Xamarin'
description: Ce document décrit comment configurer une solution multiplateforme dans Xamarin. Il aborde des stratégies telles que les projets partagés et .NET Standard de partage de code différents.
ms.prod: xamarin
ms.assetid: 4139A6C2-D477-C563-C1AB-98CCD0D10A93
author: asb3993
ms.author: amburns
ms.date: 03/27/2017
ms.openlocfilehash: d20275bab4e4ce90f902a5e72321701d94b1d416
ms.sourcegitcommit: 4a1520dee7759f8355ea65c8bb3d1bac8ba58122
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66354072"
---
# <a name="part-3---setting-up-a-xamarin-cross-platform-solution"></a>Partie 3 : configurer une Solution multiplateforme Xamarin

Quelle que soit la Quelles plateformes sont utilisées, tous les projets Xamarin utilisent le même format de fichier de solution (Visual Studio **.sln** format de fichier). Les solutions peuvent être partagées entre les environnements de développement, même lorsque des projets individuels ne peut pas être chargés (par exemple, un projet Windows dans Visual Studio pour Mac).



Lorsque vous créez une nouvelle application multiplateforme, la première étape consiste à créer une solution vide. Cette section explique ce qui se passe ensuite : configurer les projets pour créer des applications mobiles multiplateforme.

 <a name="Sharing_Code" />


## <a name="sharing-code"></a>Partage de code

Reportez-vous à la [Options de partage de Code](~/cross-platform/app-fundamentals/code-sharing.md) document pour une description détaillée de l’implémentation de partage de code entre plateformes.

 <a name="Shared_Asset_Projects" />


### <a name="shared-projects"></a>Projets partagés

À l’aide de l’approche la plus simple pour le partage de fichiers de code est un [projet partagé](~/cross-platform/app-fundamentals/shared-projects.md).

Cette méthode vous permet de partager le même code entre les projets de plateforme différents et utiliser des directives de compilateur pour inclure les chemins de code différents, spécifiques à la plateforme.

 <a name="Portable_Class_Libraries" />


### <a name="portable-class-libraries-pcl"></a>Bibliothèques de classes portables (PCL)

Historiquement un fichier de projet .NET (et l’assembly résultant) a été ciblés vers une version de framework spécifique. Cela empêche le projet ou l’assembly partagé par différentes infrastructures.

Une bibliothèque de classes Portable (PCL) est un type spécial de projet qui peut être utilisé sur différentes plates-formes CLI, Xamarin.iOS et Xamarin.Android, ainsi que WPF plateforme Windows universelle et Xbox. La bibliothèque peut utiliser uniquement un sous-ensemble du .NET framework complet, limité par les plateformes ciblées.

Vous trouverez plus d’informations à propos de Xamarin [prise en charge des bibliothèques de classes portables](~/cross-platform/app-fundamentals/pcl.md) et suivez les instructions pour voir comment la [TaskyPortable exemple](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable) fonctionne.


### <a name="net-standard"></a>.NET Standard

Introduites en 2016, [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md) Projects est un moyen simple de partager du code entre plateformes, les assemblys qui peuvent être utilisées sur Windows, Xamarin plateformes (iOS, Android, Mac) et Linux.

Bibliothèques .NET standard peuvent être créées et utilisées comme les bibliothèques de classes portables, à ceci près que les API disponibles dans chaque version (à partir de 1.0 à 1.6) sont plus facilement découverts et chaque version est rétrocompatible avec les numéros de version inférieure.



 <a name="Populating_the_Solution" />


## <a name="populating-the-solution"></a>Remplissage de la Solution

Quelle que soit la méthode qui est utilisée pour partager du code, la structure globale de la solution doit implémenter une architecture en couches qui encourage le partage de code.
L’approche Xamarin consiste à code de groupe dans les deux types de projets :

-   **Projet Core** : écrire du code réutilisable au même endroit, pour être partagés entre différentes plateformes. Utilisez le principe d’encapsulation pour masquer les détails d’implémentation dans la mesure du possible.
-   **Projets d’application spécifique à la plateforme** – consomment le code réutilisable avec en tant que peu de couplage que possible. Fonctionnalités spécifiques aux plateformes sont ajoutées à ce niveau, basé sur des composants exposées dans le projet Core.


 <a name="Core_Project" />


### <a name="core-project"></a>Projet Core

Projets de code partagé doivent référencer uniquement les assemblys qui sont disponibles sur toutes les plateformes – ie. comme les espaces de noms framework common `System`, `System.Core` et `System.Xml`.

Projets partagés doivent implémenter autant de fonctionnalités non-interface comme est possible, ce qui peut inclure les couches suivantes :

-   **Couche données** – Code qui s’occupe du stockage de données physique, par exemple.  [SQLite-NET](https://github.com/praeclarum/sqlite-net), une autre base de données comme [Realm.io](https://realm.io/products/realm-mobile-database/) ou même des fichiers XML. Les classes de la couche données sont normalement utilisés uniquement par la couche d’accès aux données.
-   **Couche d’accès aux données** – définit une API qui prend en charge les opérations de données requises pour la fonctionnalité d’application, telles que les méthodes pour accéder aux listes de données, des éléments de données et pour créer, modifier, puis supprimez-les.
-   **Couche d’accès aux service** – une couche facultative pour fournir des cloud services à l’application. Contient le code qui accède aux ressources réseau à distance (services web, téléchargements d’images, etc.) et éventuellement la mise en cache des résultats.
-   **Couche métier** – définition des classes Façade ou le gestionnaire qui exposent des fonctionnalités aux applications spécifiques à la plateforme et les classes de modèle.


 <a name="Platform-Specific_Application_Projects" />


### <a name="platform-specific-application-projects"></a>Projets d’Application spécifique à la plateforme

Projets propres à la plate-forme doivent référencer les assemblys nécessaires pour lier à Kit de développement logiciel du chaque plateforme (Xamarin.iOS, Xamarin.Android, Xamarin.Mac ou Windows), ainsi que le projet de code partagé Core.

Les projets spécifiques à la plateforme doivent implémenter :

-   **Couche d’application** – des fonctionnalités spécifiques de plateforme et la liaison/conversion entre les objets de la couche métier et l’interface utilisateur.
-   **Couche d’Interface utilisateur** – écrans, contrôles d’interface utilisateur personnalisée, présentation de la logique de validation.


<a name="Example" />


### <a name="example"></a>Exemple

L’architecture d’application est illustré dans ce diagramme :

 [ ![](setting-up-a-xamarin-cross-platform-solution-images/conceptualarchitecture.png "L’architecture d’application est illustré dans ce diagramme")](setting-up-a-xamarin-cross-platform-solution-images/conceptualarchitecture.png#lightbox)

Cette capture d’écran montre une configuration de solution avec le projet Core partagé, iOS et les projets d’application Android. Le projet partagé contient un code relatif à chacune des couches architecturales (code d’entreprise, Service, données et accès aux données) :

 ![](setting-up-a-xamarin-cross-platform-solution-images/core-solution-example.png "Le projet partagé contient un code relatif à chacune des couches architecturales (code d’entreprise, Service, données et accès aux données)")


 <a name="Project_References" />


## <a name="project-references"></a>Références de projets

Références de projet reflètent les dépendances pour un projet. Projets Core limitent leurs références aux assemblys communs afin que le code est facile à partager.
Projets d’application spécifique à la plateforme de référence le code partagé, ainsi que tous les autres assemblys spécifiques à la plateforme que dont ils ont besoin pour tirer parti de la plateforme cible.

Les projets d’application chaque référencent de projet partagé et contiennent le code d’interface utilisateur nécessaire aux fonctionnalités présente à l’utilisateur, comme indiqué dans ces captures d’écran :

![](setting-up-a-xamarin-cross-platform-solution-images/solution-android.png "Les projets de l’application de chaque référence de projet partagé") ![](setting-up-a-xamarin-cross-platform-solution-images/solution-ios.png "l’application projette chaque référence de projet partagé")


Des exemples spécifiques de la façon dont les projets doivent être structurées figurent dans les études de cas.

 <a name="Adding_Files" />


## <a name="adding-files"></a>Ajout de fichiers

 <a name="Build_Action" />


### <a name="build-action"></a>Action de génération

Il est important de définir l’action de génération correcte pour certains types de fichiers. Cette liste affiche l’action de génération pour certains types de fichiers courants :

-  **Tous les C# fichiers** : Action de génération : Compile
-   **Images dans Xamarin.iOS & Windows** : Action de génération : Contenu
-   **Les fichiers XIB ou Storyboard dans Xamarin.iOS** – Action de génération : InterfaceDefinition
-   **Images et dispositions AXML dans Android** – Action de génération : AndroidResource
-  **Fichiers XAML dans les projets Windows** : Action de génération : Page
-  **Les fichiers XAML de Xamarin.Forms** : Action de génération : EmbeddedResource


En règle générale l’IDE détecte le type de fichier et suggérer l’action de génération correcte.

 <a name="Case_Sensitivity" />


### <a name="case-sensitivity"></a>Respect de la casse

Enfin, n’oubliez pas que certaines plates-formes de systèmes de fichiers respectant la casse (par exemple).
iOS et Android) par conséquent, veillez à utiliser une norme d’affectation de noms de fichiers cohérentes et assurez-vous que les noms de fichiers que vous utilisez dans le code correspondent exactement au système de fichiers. Cela est particulièrement important pour les images et d’autres ressources que vous référencez dans le code.
