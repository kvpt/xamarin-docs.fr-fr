---
title: Modèles d’application d’entreprise utilisant Xamarin.Forms ebook
description: Ce livre électronique fournit des conseils architecturaux pour le développement d’applications d’entreprise adaptables, gérables et pouvant être testées Xamarin.Forms .
ms.prod: xamarin
ms.assetid: 28cfed6c-6175-4223-a8cc-798d40bf0832
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1d993afa7163beb0b24e496b0c876d903f2396e7
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86934288"
---
# <a name="enterprise-application-patterns-using-xamarinforms-ebook"></a>Modèles d’application d’entreprise utilisant Xamarin.Forms ebook

_Conseils architecturaux pour le développement d’applications d’entreprise adaptables, gérables et pouvant être testées Xamarin.Forms_

![Modèles d’application d’entreprise utilisant Xamarin.Forms ebook](images/cover-sml.png)

Ce livre électronique fournit des conseils sur la façon d’implémenter le modèle MVVM (Model-View-ViewModel), l’injection de dépendances, la navigation, la validation et la gestion de la configuration, tout en conservant un couplage faible. En outre, il existe également des conseils sur l’exécution de l’authentification et de l’autorisation avec IdentityServer, l’accès aux données à partir de microservices en conteneur et les tests unitaires.

## <a name="preface"></a>[Préface](preface.md)

Ce chapitre explique l’objectif et l’étendue du guide, ainsi que les personnes qui l’ont ciblé.

## <a name="introduction"></a>[Introduction](introduction.md)

Les développeurs d’applications d’entreprise font face à plusieurs défis qui peuvent altérer l’architecture de l’application pendant le développement. Par conséquent, il est important de créer une application afin qu’elle puisse être modifiée ou étendue au fil du temps. La conception pour une telle adaptation peut être difficile, mais implique généralement le partitionnement d’une application en composants discrets et faiblement couplés qui peuvent être facilement intégrés ensemble dans une application.

## <a name="mvvm"></a>[MVVM](mvvm.md)

Le modèle MVVM (Model-View-ViewModel) permet de séparer correctement la logique métier et de présentation d’une application à partir de son interface utilisateur. Maintenir une séparation nette entre la logique d’application et l’interface utilisateur permet de résoudre de nombreux problèmes de développement et peut rendre une application plus facile à tester, à gérer et à évoluer. Cela peut également améliorer les opportunités de réutilisation de code et permet aux développeurs et aux concepteurs d’interface utilisateur de collaborer plus facilement lors du développement de leurs parties respectives d’une application.

## <a name="dependency-injection"></a>[Injection de dépendances](dependency-injection.md)

L’injection de dépendances permet de découpler des types concrets du code qui dépend de ces types. Elle utilise généralement un conteneur qui contient une liste d’inscriptions et de mappages entre les interfaces et les types abstraits, ainsi que les types concrets qui implémentent ou étendent ces types.

Les conteneurs d’injection de dépendances réduisent le couplage entre les objets en fournissant une fonctionnalité permettant d’instancier des instances de classe et de gérer leur durée de vie en fonction de la configuration du conteneur. Lors de la création d’objets, le conteneur injecte toutes les dépendances que l’objet requiert dans celui-ci. Si ces dépendances n’ont pas encore été créées, le conteneur crée et résout d’abord leurs dépendances.

## <a name="communicating-between-loosely-coupled-components"></a>[Communication entre les composants faiblement couplés](communicating-between-loosely-coupled-components.md)

La Xamarin.Forms [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) classe implémente le modèle de publication-abonnement, ce qui permet la communication basée sur les messages entre les composants qui sont peu pratiques à lier par objet et les références de type. Ce mécanisme permet aux éditeurs et aux abonnés de communiquer sans avoir une référence entre eux, ce qui contribue à réduire les dépendances entre les composants, tout en permettant aux composants d’être développés et testés indépendamment.

## <a name="navigation"></a>[Navigation](navigation.md)

Xamarin.Formsprend en charge la navigation entre les pages, qui résulte généralement de l’interaction de l’utilisateur avec l’interface utilisateur, ou à partir de l’application elle-même, en raison des modifications d’État pilotées par la logique interne. Toutefois, la navigation peut être complexe à implémenter dans les applications qui utilisent le modèle MVVM.

Ce chapitre présente une `NavigationService` classe, qui est utilisée pour effectuer la navigation dans le modèle de vue, en commençant par les modèles de vue. Le fait de placer une logique de navigation dans les classes de modèle de vue signifie que la logique peut être testée par le biais de tests automatisés. En outre, le modèle de vue peut ensuite implémenter une logique pour contrôler la navigation afin de s’assurer que certaines règles d’entreprise sont appliquées.

## <a name="validation"></a>[Validation](validation.md)

Toute application qui accepte les entrées d’utilisateurs doit s’assurer que l’entrée est valide. Sans validation, un utilisateur peut fournir des données qui provoquent l’échec de l’application. La validation applique des règles d’entreprise et empêche une personne malveillante d’injecter des données malveillantes.

Dans le contexte du modèle MVVM (Model-View-ViewModel), un modèle de vue ou un modèle est souvent requis pour effectuer la validation des données et signaler toutes les erreurs de validation à la vue afin que l’utilisateur puisse les corriger.

## <a name="configuration-management"></a>[Gestion de la configuration](configuration-management.md)

Les paramètres permettent de séparer les données qui configurent le comportement d’une application à partir du code, ce qui permet de modifier le comportement sans reconstruire l’application. Les paramètres de l’application sont les données créées et gérées par une application, et les paramètres utilisateur sont les paramètres personnalisables d’une application qui affectent le comportement de l’application et ne nécessitent pas de réajustement fréquent.

## <a name="containerized-microservices"></a>[Microservices conteneurisés](containerized-microservices.md)

Les microservices offrent une approche du développement et du déploiement d’applications adaptées aux exigences en termes d’agilité, de mise à l’échelle et de fiabilité des applications Cloud modernes. L’un des principaux avantages des microservices est qu’ils peuvent être mis à l’échelle de manière indépendante, ce qui signifie qu’il est possible de mettre à l’échelle une zone fonctionnelle spécifique nécessitant davantage de puissance de traitement ou de bande passante réseau pour prendre en charge la demande, sans mettre à l’échelle les zones de l’application qui ne subissent pas une demande accrue.

## <a name="authentication-and-authorization"></a>[Authentification et autorisation](authentication-and-authorization.md)

Il existe de nombreuses approches pour intégrer l’authentification et l’autorisation dans une Xamarin.Forms application qui communique avec une application web ASP.NET MVC. Ici, l’authentification et l’autorisation sont effectuées avec un microservice d’identité en conteneur qui utilise IdentityServer 4. IdentityServer est une infrastructure Open source OpenID Connect et OAuth 2,0 pour ASP.NET Core qui s’intègre avec ASP.NET Core identité pour exécuter l’authentification du jeton du porteur.

## <a name="accessing-remote-data"></a>[Accès aux données distantes](accessing-remote-data.md)

De nombreuses solutions Web modernes utilisent des services Web hébergés par des serveurs Web pour fournir des fonctionnalités aux applications clientes distantes. Les opérations qu’un service Web expose constituent une API Web, et les applications clientes doivent être en mesure d’utiliser l’API Web sans savoir comment les données ou opérations exposées par l’API sont implémentées.

## <a name="unit-testing"></a>[Tests unitaires](unit-testing.md)

Le test de modèles et de vues à partir d’applications MVVM est identique au test de toute autre classe, et les mêmes outils et techniques peuvent être utilisés. Toutefois, il existe des modèles qui sont typiques des classes de modèle de modèle et de vue, qui peuvent tirer parti de techniques de test unitaire spécifiques.

## <a name="feedback"></a>Commentaires

Ce projet contient un site de la Communauté, sur lequel vous pouvez poser des questions et fournir des commentaires. Le site de la communauté se trouve sur [GitHub](https://github.com/dotnet-architecture/eShopOnContainers). Vous pouvez également envoyer des commentaires sur le livre électronique à [dotnet-architecture-ebooks-feedback@service.microsoft.com](mailto:dotnet-architecture-ebooks-feedback@service.microsoft.com) .

## <a name="related-links"></a>Liens associés

- [Télécharger le livre électronique (PDF de 2 Mo)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (exemple)](https://github.com/dotnet-architecture/eShopOnContainers)
