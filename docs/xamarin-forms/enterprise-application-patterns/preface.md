---
title: Préface au développement d’applications Enterprise
description: Ce chapitre fournit une préface aux modèles d’Application d’entreprise à l’aide de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: fbf32a44-1d33-4e16-a904-dc7ee5991e7c
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 82f1455ff5e5ac06b50664e1d4d533d4964b7a0e
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831130"
---
# <a name="preface-to-enterprise-app-development"></a>Préface au développement d’applications Enterprise

Ce livre électronique fournit des conseils sur la création d’applications d’entreprise d’inter-plateformes à l’aide de Xamarin.Forms. Xamarin.Forms est un kit de ressources de l’interface utilisateur multiplateforme qui permet aux développeurs de créer facilement des dispositions d’interfaces qui peuvent être partagées entre les plateformes, notamment iOS, Android et la plateforme universelle Windows (UWP) utilisateur natives. Il fournit une solution complète pour les activités entreprise-employé (B2E), Business to Business (B2B) et d’entreprise pour les applications consommateur (B2C), leur permettant de partager du code entre toutes les plateformes cibles et contribue à diminuer le coût total de possession (TCO).

Le guide fournit des conseils d’architecture pour le développement d’applications d’entreprise Xamarin.Forms adaptables, testables et facile à gérer. Conseils sur l’implémentation MVVM, l’injection de dépendances, la navigation, validation et gestion de la configuration, tout en conservant un couplage faible. En outre, il existe également des conseils sur l’exécution de l’authentification et autorisation avec IdentityServer, l’accès aux données à partir de microservices en conteneur et les tests unitaires.

Le guide est fourni avec le code source pour le [application mobile eShopOnContainers](https://github.com/dotnet-architecture/eShopOnContainers/tree/master/src/Mobile)et le code source pour le [application de référence eShopOnContainers](https://github.com/dotnet-architecture/eShopOnContainers). L’application mobile eShopOnContainers est une application multiplateforme entreprise développée à l’aide de Xamarin.Forms, qui se connecte à une série de microservices en conteneur appelé à l’application de référence eShopOnContainers. Toutefois, l’application mobile eShopOnContainers peut être configurée pour consommer des données à partir des services fictifs pour ceux qui souhaitent éviter de déployer les microservices en conteneur.

## <a name="whats-left-out-of-this-guides-scope"></a>Ce qui reste hors de portée de ce Guide

Ce guide est destiné aux lecteurs qui sont déjà familiarisés avec Xamarin.Forms. Pour obtenir une présentation détaillée Xamarin.Forms, consultez le [Xamarin.Forms documentation](~/xamarin-forms/index.yml), et [création d’applications mobiles avec Xamarin.Forms](https://aka.ms/xamebook).

Le guide est complémentaire à [.NET Microservices : Architecture pour les Applications .NET](https://aka.ms/microservicesebook), qui se concentre sur le développement et déploiement de microservices en conteneur. Incluent d’autres guides intéressant de lecture [création d’une architecture et développement d’Applications Web modernes avec ASP.NET Core et Microsoft Azure](https://aka.ms/WebAppEbook), [Containerized Docker Application Lifecycle with Microsoft Platform and Tools](https://aka.ms/dockerlifecycleebook), et [Microsoft Platform and Tools pour le développement d’applications mobiles](https://aka.ms/MobAppDev/StndPDF).

## <a name="who-should-use-this-guide"></a>Qui doit utiliser ce Guide

L’audience pour ce guide est principalement les développeurs et architectes qui souhaitent apprendre à concevoir et implémenter des applications d’entreprise d’inter-plateformes à l’aide de Xamarin.Forms.

Un public secondaire se compose des décideurs techniques qui souhaitent recevoir une vue d’ensemble de l’architecture et technologie avant de choisir quelle approche à sélectionner pour le développement d’applications inter-plateformes enterprise à l’aide de Xamarin.Forms.

## <a name="how-to-use-this-guide"></a>Comment utiliser ce Guide

Ce guide se concentre sur la création d’applications d’entreprise d’inter-plateformes à l’aide de Xamarin.Forms. Par conséquent, il doit être lu dans son intégralité pour fournir une base de présentation de ces applications et les considérations techniques. Le guide, ainsi que son exemple d’application, peut également servir d’un point de départ ou une référence pour la création d’une nouvelle application d’entreprise. Utiliser l’application exemple associé comme modèle pour la nouvelle application, ou pour voir comment organiser les composants d’une application. Ensuite, font référence à ce guide pour obtenir des conseils architecturaux.

N’hésitez pas à faire connaître ce guide pour les membres de l’équipe afin de garantir une compréhension commune inter-plateformes enterprise du développement d’applications à l’aide de Xamarin.Forms. Le fait que chacun fonctionne à partir d’un ensemble commun de terminologie et les principes sous-jacents afin de garantir une application cohérente des modèles architecturaux et des pratiques.


## <a name="related-links"></a>Liens associés

- [Téléchargez le livre électronique (PDF de 2 Mo)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
