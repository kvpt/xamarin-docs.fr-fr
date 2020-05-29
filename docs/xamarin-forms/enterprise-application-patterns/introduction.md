---
titre : « introduction au développement d’applications d’entreprise » Description : « ce chapitre fournit une introduction au développement d’applications d’entreprise et présente l’application mobile eShopOnContainers ».
ms. Prod : xamarin ms. AssetID : cbce0659-FA03-447A-86ec-140438143230 ms. Technology : xamarin-Forms auteur : davidbritch ms. Author : dabritch ms. Date : 08/07/2017 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="introduction-to-enterprise-app-development"></a>Présentation du développement d’applications d’entreprise

Quelle que soit la plateforme, les développeurs d’applications d’entreprise sont confrontés à plusieurs défis :

- Spécifications de l’application qui peuvent changer au fil du temps.
- Nouvelles opportunités commerciales et défis.
- Commentaires en cours pendant le développement qui peuvent affecter considérablement l’étendue et les exigences de l’application.

À l’esprit, il est important de créer des applications qui peuvent être facilement modifiées ou étendues au fil du temps. La conception pour une telle adaptation peut être difficile, car elle nécessite une architecture qui permet de développer et de tester indépendamment des parties individuelles de l’application, sans affecter le reste de l’application.

De nombreuses applications d’entreprise sont suffisamment complexes pour nécessiter plus d’un développeur. Il peut s’agir d’un défi important à prendre pour décider comment concevoir une application afin que plusieurs développeurs puissent travailler de manière efficace sur différentes parties de l’application, tout en veillant à ce que les pièces s’intègrent de manière transparente lorsqu’elles sont intégrées à l’application.

L’approche traditionnelle de la conception et de la création d’une application est la même que celle d’une application *monolithique* , où les composants sont étroitement couplés sans séparation claire entre eux. En règle générale, cette approche monolithique génère des applications qui sont difficiles et inefficaces à gérer, car il peut être difficile de résoudre les bogues sans endommager les autres composants de l’application, et il peut être difficile d’ajouter de nouvelles fonctionnalités ou de remplacer des fonctionnalités existantes.

Une solution efficace pour relever ces défis consiste à partitionner une application en composants discrets et faiblement couplés qui peuvent être facilement intégrés dans une application. Une telle approche offre plusieurs avantages :

- Il permet de développer, de tester, d’étendre et de maintenir des fonctionnalités individuelles par différentes personnes ou équipes.
- Il encourage la réutilisation et une séparation nette des préoccupations entre les fonctions horizontales de l’application, telles que l’authentification et l’accès aux données, ainsi que les fonctionnalités verticales, telles que les fonctionnalités métier spécifiques aux applications. Cela permet de gérer plus facilement les dépendances et les interactions entre les composants de l’application.
- Il permet de conserver une séparation des rôles en permettant à différentes personnes ou équipes de se concentrer sur une tâche ou une fonctionnalité spécifique, en fonction de leur expertise. En particulier, il fournit une séparation plus propre entre l’interface utilisateur et la logique métier de l’application.

Toutefois, il existe de nombreux problèmes qui doivent être résolus lors du partitionnement d’une application en composants discrets et faiblement couplés. notamment :

- Déterminer comment fournir une séparation nette des problèmes entre les contrôles d’interface utilisateur et leur logique. L’une des décisions les plus importantes lors de la création d’une Xamarin.Forms application d’entreprise est de déterminer si la logique métier doit être placée dans les fichiers code-behind, ou de créer une séparation claire des préoccupations entre les contrôles d’interface utilisateur et leur logique, afin de rendre l’application plus gérable et testable. Pour plus d’informations, consultez [Model-View-ViewModel](~/xamarin-forms/enterprise-application-patterns/mvvm.md).
- Détermination de l’utilisation ou non d’un conteneur d’injection de dépendances. Les conteneurs d’injection de dépendances réduisent le couplage des dépendances entre les objets en fournissant une fonctionnalité permettant de construire des instances de classes avec leurs dépendances injectées, et de gérer leur durée de vie en fonction de la configuration du conteneur. Pour plus d’informations, consultez [injection de dépendances](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md).
- Choix entre les événements fournis par la plateforme et la communication faiblement couplée basée sur les messages entre les composants qui sont peu pratiques à lier par objet et les références de type. Pour plus d’informations, consultez Introduction à la [communication entre les composants faiblement couplés](~/xamarin-forms/enterprise-application-patterns/communicating-between-loosely-coupled-components.md).
- Choix du mode de navigation entre les pages, y compris l’appel de la navigation et l’emplacement de la logique de navigation. Pour plus d’informations, consultez [Navigation](~/xamarin-forms/enterprise-application-patterns/navigation.md).
- Détermination de la façon dont les entrées d’utilisateur sont validées pour l’exactitude. La décision doit comprendre comment valider les entrées d’utilisateur et comment informer l’utilisateur des erreurs de validation. Pour plus d’informations, consultez [validation](~/xamarin-forms/enterprise-application-patterns/validation.md).
- Choix de l’authentification et de la protection des ressources avec autorisation. Pour plus d’informations, consultez [authentification et autorisation](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md).
- Déterminer comment accéder aux données distantes à partir de services Web, notamment comment récupérer des données de manière fiable et comment mettre en cache des données. Pour plus d’informations, consultez [accès aux données distantes](~/xamarin-forms/enterprise-application-patterns/accessing-remote-data.md).
- Choix du mode de test de l’application. Pour plus d’informations, consultez [Test unitaire](~/xamarin-forms/enterprise-application-patterns/unit-testing.md).

Ce guide fournit des conseils sur ces problèmes et se concentre sur les modèles et l’architecture de base pour la création d’une application d’entreprise multiplateforme à l’aide de Xamarin.Forms . L’aide vise à produire du code adaptable, gérable et testable, en traitant Xamarin.Forms les scénarios courants de développement d’applications d’entreprise et en séparant les préoccupations de présentation, la logique de présentation et les entités grâce à la prise en charge du modèle MVVM (Model-View-ViewModel).

## <a name="sample-application"></a>Exemple d'application

Ce guide comprend un exemple d’application, eShopOnContainers, qui est un magasin en ligne qui comprend les fonctionnalités suivantes :

- Authentification et autorisation sur un service principal.
- Navigation dans un catalogue de maillots, de tasses à café et autres éléments marketing.
- Filtrage du catalogue.
- Tri des éléments à partir du catalogue.
- Affichage de l’historique des commandes de l’utilisateur.
- Configuration des paramètres.

### <a name="sample-application-architecture"></a>Exemple d’architecture d’application

La figure 1-1 fournit une vue d’ensemble de l’architecture de l’exemple d’application.

![](introduction-images/architecture.png "eShopOnContainers high-level architecture")

**Figure 1-1**: architecture de haut niveau eShopOnContainers

L’exemple d’application est fourni avec trois applications clientes :

- Une application MVC développée avec ASP.NET Core.
- Une application à page unique (SPA) développée avec l’angle 2 et la machine à écrire. Cette approche pour les applications Web évite d’effectuer un aller-retour sur le serveur avec chaque opération.
- Une application mobile développée avec Xamarin.Forms , qui prend en charge iOS, Android et la plateforme Windows universelle (UWP).

Pour plus d’informations sur les applications Web, consultez [conception et développement d’applications Web modernes avec ASP.net Core et Microsoft Azure](https://aka.ms/WebAppEbook).

L’exemple d’application comprend les services principaux suivants :

- Un microservice d’identité, qui utilise ASP.NET Core Identity et IdentityServer.
- Un microservice de catalogue, qui est un service de création, de lecture, de mise à jour, de suppression (CRUD) piloté par les données qui consomme une base de données SQL Server à l’aide d’EntityFramework Core.
- Un microservice de commande, qui est un service piloté par domaine qui utilise des modèles de conception pilotés par domaine.
- Un microservice de panier, qui est un service CRUD piloté par les données qui utilise le cache Redims.

Ces services principaux sont implémentés en tant que microservices à l’aide d’ASP.NET Core MVC et sont déployés en tant que conteneurs uniques au sein d’un seul hôte Dockr. Collectivement, ces services principaux sont appelés l’application de référence eShopOnContainers. Les applications clientes communiquent avec les services principaux via une interface Web REST. Pour plus d’informations sur les microservices et l’arrimeur, consultez [microservices en conteneur](~/xamarin-forms/enterprise-application-patterns/containerized-microservices.md).

Pour plus d’informations sur l’implémentation des services principaux, consultez [microservices .net : architecture pour les applications .net en conteneur](https://aka.ms/microservicesebook).

### <a name="mobile-app"></a>Application mobile

Ce guide se concentre sur la création d’applications d’entreprise multiplateforme à l’aide de Xamarin.Forms , et utilise l’application mobile eShopOnContainers comme exemple. La figure 1-2 montre les pages de l’application mobile eShopOnContainers qui fournissent les fonctionnalités décrites précédemment.

[![](introduction-images/screenshots.png "The eShopOnContainers mobile app")](introduction-images/screenshots-large.png#lightbox "The eShopOnContainers mobile app")

**Figure 1-2**: application mobile eShopOnContainers

L’application mobile consomme les services principaux fournis par l’application de référence eShopOnContainers. Toutefois, il peut être configuré pour consommer des données de services fictifs pour ceux qui souhaitent éviter de déployer les services principaux.

L’application mobile eShopOnContainers exerce les Xamarin.Forms fonctionnalités suivantes :

- XAML
- Contrôles
- Liaisons
- Convertisseurs
- Styles
- Animations
- Commandes
- comportements
- Déclencheurs
- Effects (Effets)
- Renderers personnalisés
- MessagingCenter
- Contrôles personnalisés

Pour plus d’informations sur cette fonctionnalité, consultez la [ Xamarin.Forms documentation](~/xamarin-forms/index.yml)et [création d’Mobile Apps Xamarin.Forms avec ](https://aka.ms/xamformsebook).

En outre, des tests unitaires sont fournis pour certaines des classes de l’application mobile eShopOnContainers.

#### <a name="mobile-app-solution"></a>Solution d’application mobile

La solution d’application mobile eShopOnContainers organise le code source et d’autres ressources en projets. Tous les projets utilisent des dossiers pour organiser le code source et d’autres ressources en catégories. Le tableau suivant présente les projets qui composent l’application mobile eShopOnContainers :

|Project|Description|
|--- |--- |
|eShopOnContainers. Core|Ce projet est le projet de bibliothèque de classes portable (PCL) qui contient le code partagé et l’interface utilisateur partagée.|
|eShopOnContainers. Droid|Ce projet contient le code spécifique à Android et constitue le point d’entrée de l’application Android.|
|eShopOnContainers. iOS|Ce projet contient du code spécifique à iOS et constitue le point d’entrée de l’application iOS.|
|eShopOnContainers. UWP|Ce projet contient le code spécifique plateforme Windows universelle (UWP) et est le point d’entrée de l’application Windows.|
|eShopOnContainers. TestRunner. Droid|Ce projet est le test Runner Android pour le projet eShopOnContainers. UnitTests.|
|eShopOnContainers. TestRunner. iOS|Ce projet est le test Runner iOS pour le projet eShopOnContainers. UnitTests.|
|eShopOnContainers. TestRunner. Windows|Ce projet est le plateforme Windows universelle Test Runner pour le projet eShopOnContainers. UnitTests.|
|eShopOnContainers. UnitTests|Ce projet contient des tests unitaires pour le projet eShopOnContainers. Core.|

Les classes de l’application mobile eShopOnContainers peuvent être réutilisées dans n’importe quelle Xamarin.Forms application avec peu ou pas de modifications.

##### <a name="eshoponcontainerscore-project"></a>Projet eShopOnContainers. Core

Le projet PCL eShopOnContainers. Core contient les dossiers suivants :

|Dossier|Description|
|--- |--- |
|Animations|Contient des classes qui permettent aux animations d’être consommées en XAML.|
|comportements|Contient les comportements exposés aux classes d’affichage.|
|Contrôles|Contient des contrôles personnalisés utilisés par l’application.|
|Convertisseurs|Contient des convertisseurs de valeurs qui appliquent une logique personnalisée à une liaison.|
|Effects (Effets)|Contient la `EntryLineColorEffect` classe, qui est utilisée pour modifier la couleur de bordure de `Entry` contrôles spécifiques.|
|Exceptions|Contient le personnalisé `ServiceAuthenticationException` .|
|Extensions|Contient des méthodes d’extension pour les `VisualElement` `IEnumerable` classes et.|
|Programmes d’assistance|Contient des classes d’assistance pour l’application.|
|Modèles|Contient les classes de modèle pour l’application.|
|Propriétés|Contient `AssemblyInfo.cs` un fichier de métadonnées d’assembly .net.|
|Services|Contient des interfaces et des classes qui implémentent des services fournis à l’application.|
|Déclencheurs|Contient le `BeginAnimation` déclencheur, qui est utilisé pour appeler une animation en XAML.|
|Validations|Contient les classes impliquées dans la validation de l’entrée de données.|
|ViewModels|Contient la logique d’application exposée aux pages.|
|Les vues|Contient les pages de l’application.|

##### <a name="platform-projects"></a>Projets de plateforme

Les projets de plateforme contiennent des implémentations d’effet, des implémentations de convertisseur personnalisées et d’autres ressources spécifiques à la plateforme.

## <a name="summary"></a>Résumé

Les plateformes et outils de développement d’applications mobiles multiplateforme de Xamarin fournissent une solution complète pour les applications clientes mobiles B2E, B2B et B2C, offrant ainsi la possibilité de partager du code sur toutes les plateformes cibles (iOS, Android et Windows) et de réduire le coût total de possession. Les applications peuvent partager l’interface utilisateur et le code de la logique de l’application, tout en conservant l’apparence native de la plateforme.

Les développeurs d’applications d’entreprise font face à plusieurs défis qui peuvent altérer l’architecture de l’application pendant le développement. Par conséquent, il est important de créer une application afin qu’elle puisse être modifiée ou étendue au fil du temps. La conception pour une telle adaptation peut être difficile, mais implique généralement le partitionnement d’une application en composants discrets et faiblement couplés qui peuvent être facilement intégrés ensemble dans une application.

## <a name="related-links"></a>Liens connexes

- [Télécharger le livre électronique (PDF de 2 Mo)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (exemple)](https://github.com/dotnet-architecture/eShopOnContainers)
