---
title: Introduction au développement d’applications Enterprise
description: Ce chapitre fournit une introduction au développement d’applications enterprise et présente l’application mobile eShopOnContainers.
ms.prod: xamarin
ms.assetid: cbce0659-fa03-447a-86ec-140438143230
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 4fbb4047b95fd70f829cd79e4ea26b2958273297
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831182"
---
# <a name="introduction-to-enterprise-app-development"></a>Introduction au développement d’applications Enterprise

Quelle que soit la plateforme, les développeurs d’applications d’entreprise sont confrontés à plusieurs défis :

-   Exigences de l’application qui peuvent changer au fil du temps.
-   Défis et nouvelles opportunités commerciales.
-   Commentaires en continu pendant le développement qui peut affecter de manière significative la portée et les conditions de l’application.

Avec ces pratiques, il est important de créer des applications qui peuvent être facilement modifiées ou étendues au fil du temps. Conception pour une telle capacité d’adaptation peut être difficile car il nécessite une architecture qui permet des parties individuelles de l’application à être développé indépendamment et de tester de manière isolée sans affecter le reste de l’application.

De nombreuses applications d’entreprise sont suffisamment complexes pour exiger plusieurs développeurs. Il peut être un défi pour décider comment concevoir une application afin que plusieurs développeurs peuvent travailler efficacement sur différentes parties de l’application indépendamment, tout en garantissant que les éléments sont combinent en toute transparence quand intégré dans l’application.

L’approche traditionnelle pour concevoir et générer une application entraîne ce qui est appelé un *monolithique* application, où les composants sont étroitement couplées avec aucune séparation claire entre eux. En règle générale, cette approche monolithique conduit à des applications qui sont difficiles et inefficace à gérer, car il peut être difficile à résoudre les bogues sans interrompre les autres composants dans l’application, et il peut être difficile d’ajouter de nouvelles fonctionnalités ou de remplacer les fonctionnalités existantes.

Une solution efficace pour relever ces défis consiste à partitionner une application en composants discrètes, faiblement couplés qui peuvent être facilement intégrés ensemble dans une application. Une telle approche offre les avantages suivants :

-   Il permet des fonctionnalités individuelles être développés, testés, étendue et gérées par différents utilisateurs ou équipes.
-   Il favorise la réutilisation et une séparation claire des responsabilités entre horizontale des fonctionnalités l’application, telles que l’authentification et accès aux données et la verticale, telles que des fonctionnalités d’entreprise spécifiques application. Ainsi, les dépendances et les interactions entre les composants d’application à être plus faciles à gérer.
-   Il permet de maintenir une séparation des rôles en permettant à différents utilisateurs ou équipes, à vous concentrer sur une tâche spécifique ou une partie des fonctionnalités en fonction de leur expertise. En particulier, il fournit une séparation claire entre l’interface utilisateur et la logique métier de l’application.

Toutefois, il existe de nombreux problèmes qui doivent être résolus lors du partitionnement d’une application en composants discrètes et faiblement couplées. Elles incluent notamment :

-   Décidez comment fournir une séparation claire des responsabilités entre les contrôles d’interface utilisateur et leur logique. Une des décisions plus importantes lors de la création d’une application d’entreprise Xamarin.Forms est s’il faut placer une logique métier dans les fichiers code-behind, ou s’il faut créer une séparation claire des responsabilités entre les contrôles d’interface utilisateur et leur logique, pour rendre l’application plus testable et facile à gérer. Pour plus d’informations, consultez [Model-View-ViewModel](~/xamarin-forms/enterprise-application-patterns/mvvm.md).
-   Déterminer s’il faut utiliser un conteneur d’injection de dépendance. Conteneurs d’injection de dépendance réduisent la dépendance entre les objets en fournissant une fonctionnalité permettant de construire des instances de classes avec leurs dépendances injectés de couplage et de gérer leur durée de vie basée sur la configuration du conteneur. Pour plus d’informations, consultez [l’Injection de dépendances](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md).
-   Choix entre les événements de plateforme fourni et faiblement couplées basées sur des messages de communication entre les composants qui n’est pas pratique lier par des références d’objet et le type. Pour plus d’informations, consultez Introduction à [communiquant entre faiblement couplés composants](~/xamarin-forms/enterprise-application-patterns/communicating-between-loosely-coupled-components.md).
-   Décidez comment naviguer entre les pages, y compris l’appel de navigation, et où la logique de navigation doit résider. Pour plus d’informations, consultez [Navigation](~/xamarin-forms/enterprise-application-patterns/navigation.md).
-   Déterminer comment valider l’entrée utilisateur est correcte. La décision doit inclure la validation des entrées d’utilisateur et comment avertir l’utilisateur sur les erreurs de validation. Pour plus d’informations, consultez [Validation](~/xamarin-forms/enterprise-application-patterns/validation.md).
-   Décider comment effectuer l’authentification et comment protéger les ressources disposant d’autorisations. Pour plus d’informations, consultez [authentification et autorisation](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md).
-   Déterminer comment accéder aux données distantes à partir du web services, y compris comment récupérer des données de manière fiable et comment mettre en cache des données. Pour plus d’informations, consultez [l’accès à des données distantes](~/xamarin-forms/enterprise-application-patterns/accessing-remote-data.md).
-   Vous décidez de tester l’application. Pour plus d’informations, consultez [Unit Testing](~/xamarin-forms/enterprise-application-patterns/unit-testing.md).

Ce guide fournit des conseils sur ces problèmes et se concentre sur les modèles de base et l’architecture pour la création d’une application d’entreprise d’inter-plateformes à l’aide de Xamarin.Forms. Les directives visent à aider à produire du code testable, facile à gérer et adaptable, en résolvant les scénarios de développement courants Xamarin.Forms enterprise application et en séparant les problèmes de présentation, logique de présentation et d’entités via la prise en charge pour le Modèle Model-View-ViewModel (MVVM).

## <a name="sample-application"></a>Exemple d’Application

Ce guide comprend un exemple d’application eShopOnContainers, qui est un magasin en ligne qui inclut les fonctionnalités suivantes :

-   Authentification et autorisation par rapport à un service principal.
-   Parcourt un catalogue de chemises, tasses à café et autres articles marketing.
-   Filtrage du catalogue.
-   Organisation des éléments à partir du catalogue.
-   Affichage de l’historique des commandes de l’utilisateur.
-   Configuration des paramètres.

### <a name="sample-application-architecture"></a>Exemple d’Application Architecture

Figure 1-1 fournit une vue d’ensemble de l’architecture de l’exemple d’application.

![](introduction-images/architecture.png "architecture de haut niveau eShopOnContainers")

**Figure 1-1**: architecture de haut niveau eShopOnContainers

L’exemple d’application est fourni avec trois applications clientes :

-   Une application MVC développée avec ASP.NET Core.
-   Une Application à Page unique (SPA) développé avec Angular 2 et Typescript. Cette approche pour les applications web évite d’effectuer un aller-retour au serveur avec chaque opération.
-   Une application mobile développée avec Xamarin.Forms, qui prend en charge d’iOS, Android et la plateforme universelle Windows (UWP).

Pour plus d’informations sur les applications web, consultez [création d’une architecture et développement d’Applications Web modernes avec ASP.NET Core et Microsoft Azure](https://aka.ms/WebAppEbook).

L’exemple d’application inclut les services principaux suivants :

-   Un microservice d’identité, qui utilise ASP.NET Core Identity et IdentityServer.
-   Un microservice de catalogue, qui est une création piloté par les données, lire, mettre à jour, supprimer (CRUD) des services qui utilise une base de données SQL Server à l’aide d’Entity Framework Core.
-   Un microservice de passation de, qui est un service pilotée par domaine qui utilise des modèles de conception pilotée par domaine.
-   Un microservice de panier d’achat, qui est un service CRUD pilotée par les données qui utilise le Cache Redis.

Ces services back-end sont implémentées en tant que microservices à l’aide d’ASP.NET Core MVC et sont déployés en tant que conteneurs uniques au sein d’un seul hôte Docker. Collectivement, ces services back-end sont appelés pour application de référence eShopOnContainers. Les applications clientes communiquent avec les services principaux via une interface web de Representational State Transfer (REST). Pour plus d’informations sur les microservices et Docker, consultez [Microservices en conteneur](~/xamarin-forms/enterprise-application-patterns/containerized-microservices.md).

Pour plus d’informations sur l’implémentation de services principaux, consultez [.NET Microservices : Architecture pour les applications .NET en conteneurs](https://aka.ms/microservicesebook).

### <a name="mobile-app"></a>Application mobile

Ce guide se concentre sur la création d’applications d’entreprise d’inter-plateformes à l’aide de Xamarin.Forms et utilise l’application mobile eShopOnContainers comme exemple. Figure 1-2 montre les pages de l’application mobile eShopOnContainers qui fournissent les fonctionnalités décrites précédemment.

[![](introduction-images/screenshots.png "L’application mobile eShopOnContainers")](introduction-images/screenshots-large.png#lightbox "l’application mobile eShopOnContainers")

**Figure 1-2**: L’application mobile eShopOnContainers

L’application mobile consomme les services principaux fournies par l’application de référence eShopOnContainers. Toutefois, il peut être configuré pour consommer des données à partir des services fictifs pour ceux qui souhaitent éviter de déployer les services principaux.

L’application mobile eShopOnContainers utilise les fonctionnalités de Xamarin.Forms suivantes :

-   XAML
-   Contrôles
-   Liaisons
-   Convertisseurs
-   Styles
-   Animations
-   Commandes
-   comportements
-   Déclencheurs
-   Effects (Effets)
-   Renderers personnalisés
-   MessagingCenter
-   Contrôles personnalisés

Pour plus d’informations sur cette fonctionnalité, consultez le [Xamarin.Forms documentation](~/xamarin-forms/index.yml), et [création d’applications mobiles avec Xamarin.Forms](https://aka.ms/xamebook).

En outre, les tests unitaires sont fournies pour certaines des classes dans l’application mobile eShopOnContainers.

#### <a name="mobile-app-solution"></a>Solution d’application mobile

La solution d’application mobile eShopOnContainers organise le code source et autres ressources dans des projets. Tous les projets utilisent des dossiers pour organiser le code source et autres ressources en catégories. Le tableau suivant présente les projets qui composent l’application mobile eShopOnContainers :

|Projet|Description|
|--- |--- |
|eShopOnContainers.Core|Ce projet est le projet de bibliothèque (PCL) de classes portable qui contient le code partagé et l’interface utilisateur partagée.|
|eShopOnContainers.Droid|Ce projet conserve le code spécifique à Android et constitue le point d’entrée pour l’application Android.|
|eShopOnContainers.iOS|Ce projet conserve le code spécifique à iOS et constitue le point d’entrée pour l’application iOS.|
|eShopOnContainers.UWP|Ce projet conserve le code spécifique de Universal Windows Platform (UWP) et est le point d’entrée pour l’application Windows.|
|eShopOnContainers.TestRunner.Droid|Ce projet est l’exécuteur de test Android pour le projet eShopOnContainers.UnitTests.|
|eShopOnContainers.TestRunner.iOS|Ce projet est le test runner iOS pour le projet eShopOnContainers.UnitTests.|
|eShopOnContainers.TestRunner.Windows|Ce projet est le testeur de plateforme Windows universelle pour le projet eShopOnContainers.UnitTests.|
|eShopOnContainers.UnitTests|Ce projet contient des tests unitaires pour le projet eShopOnContainers.Core.|

Les classes de l’application mobile eShopOnContainers peuvent être réutilisés dans n’importe quelle application Xamarin.Forms avec peu ou aucune modification.

##### <a name="eshoponcontainerscore-project"></a>eShopOnContainers.Core projet

Le projet de bibliothèque de classes portable eShopOnContainers.Core contient les dossiers suivants :

|Dossier|Description|
|--- |--- |
|Animations|Contient des classes qui permettent d’animations être utilisées dans XAML.|
|comportements|Contient les comportements qui sont exposées pour l’affichage de classes.|
|Contrôles|Contient des contrôles utilisés par l’application.|
|Convertisseurs|Contient les convertisseurs de valeurs qui s’appliquent une logique personnalisée à une liaison.|
|Effects (Effets)|Contient le `EntryLineColorEffect` (classe), qui est utilisé pour modifier la couleur de bordure de spécifique `Entry` contrôles.|
|Exceptions|Contient le personnalisé `ServiceAuthenticationException`.|
|Extensions|Contient des méthodes d’extension pour le `VisualElement` et `IEnumerable` classes.|
|Programmes d’assistance|Contient des classes d’assistance pour l’application.|
|Modèles|Contient les classes de modèle pour l’application.|
|Properties|Contient `AssemblyInfo.cs`, un fichier de métadonnées d’assembly .NET.|
|Services|Contient des interfaces et classes qui implémentent les services qui sont fournies à l’application.|
|Déclencheurs|Contient le `BeginAnimation` déclencheur, qui est utilisé pour appeler une animation dans XAML.|
|Validations|Contient des classes impliquées dans la validation des entrées de données.|
|ViewModels|Contient la logique d’application qui est exposée aux pages.|
|Affichages|Contient les pages de l’application.|

##### <a name="platform-projects"></a>Projets de plateforme

Les projets de plateforme contiennent les implémentations d’effet, les implémentations de convertisseur personnalisé et autres ressources spécifiques à la plateforme.

## <a name="summary"></a>Récapitulatif

Plateformes et les outils de développement d’application mobile multiplateforme de Xamarin fournissent une solution complète pour B2E, B2B et B2C client mobile apps et fournit la possibilité de partager du code entre toutes les plateformes cibles (iOS, Android et Windows) et qui contribue à réduire le coût total de possession. Applications peuvent partager leur code de logique utilisateur interface et l’application, tout en conservant l’apparence plateforme native.

Les développeurs d’applications d’entreprise sont confrontés à plusieurs difficultés pouvant modifier l’architecture de l’application pendant le développement. Par conséquent, il est important créer une application afin qu’il peut être modifié ou étendu au fil du temps. Conception pour une telle capacité d’adaptation peut s’avérer difficile, mais implique généralement le partitionnement d’une application en composants discrètes, faiblement couplés qui peuvent être facilement intégrés ensemble dans une application.


## <a name="related-links"></a>Liens associés

- [Téléchargez le livre électronique (PDF de 2 Mo)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
