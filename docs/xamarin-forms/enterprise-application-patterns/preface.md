---
title: Préface au développement d’applications d’entreprise
description: Ce chapitre fournit une préface aux modèles d’application d’entreprise à l’aide de Xamarin.Forms .
ms.prod: xamarin
ms.assetid: fbf32a44-1d33-4e16-a904-dc7ee5991e7c
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6ea63fc483025fc6f9b0c7f379b6dfdc6ca30de8
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84198087"
---
# <a name="preface-to-enterprise-app-development"></a>Préface au développement d’applications d’entreprise

Ce livre électronique fournit des conseils sur la création d’applications d’entreprise multiplateforme à l’aide de Xamarin.Forms . Xamarin.Formsest une boîte à outils d’interface utilisateur multiplateforme qui permet aux développeurs de créer facilement des dispositions d’interface utilisateur natives qui peuvent être partagées entre différentes plateformes, notamment iOS, Android et le plateforme Windows universelle (UWP). Il offre une solution complète pour les applications d’entreprise à employé (B2E), d’entreprise à entreprise (B2B) et d’entreprise à consommateur (B2C), offrant ainsi la possibilité de partager du code sur toutes les plateformes cibles et de réduire le coût total de possession (TCO).

Ce guide fournit des conseils architecturaux pour le développement d’applications d’entreprise adaptables, gérables et pouvant être testées Xamarin.Forms . Des conseils sont fournis sur la façon d’implémenter MVVM, l’injection de dépendances, la navigation, la validation et la gestion de la configuration, tout en maintenant un couplage faible. En outre, il existe également des conseils sur l’exécution de l’authentification et de l’autorisation avec IdentityServer, l’accès aux données à partir de microservices en conteneur et les tests unitaires.

Le guide est fourni avec le code source de l' [application mobile eShopOnContainers](https://github.com/dotnet-architecture/eShopOnContainers/tree/master/src/Mobile)et le code source de l' [application de référence eShopOnContainers](https://github.com/dotnet-architecture/eShopOnContainers). L’application mobile eShopOnContainers est une application d’entreprise multiplateforme développée à l’aide de Xamarin.Forms , qui se connecte à une série de microservices en conteneur connue sous le nom d’application de référence eShopOnContainers. Toutefois, l’application mobile eShopOnContainers peut être configurée pour consommer des données de services fictifs pour ceux qui souhaitent éviter de déployer les microservices en conteneur.

## <a name="whats-left-out-of-this-guides-scope"></a>Ce qui n’est pas dans la portée de ce guide

Ce guide est destiné aux lecteurs qui sont déjà familiarisés avec Xamarin.Forms . Pour obtenir une présentation détaillée de Xamarin.Forms , consultez la [ Xamarin.Forms documentation](~/xamarin-forms/index.yml)et [création d’Mobile Apps Xamarin.Forms avec ](https://aka.ms/xamformsebook).

Ce guide est complémentaire aux [microservices .net : architecture pour les applications .net en conteneur](https://aka.ms/microservicesebook), qui se concentre sur le développement et le déploiement de microservices en conteneur. Les autres guides qui méritent une telle lecture incluent l' [architecture et le développement d’applications Web modernes avec ASP.net Core et Microsoft Azure](https://aka.ms/WebAppEbook), le [cycle de vie des applications de l’arrimeur en conteneur avec la plateforme et les outils Microsoft](https://aka.ms/dockerlifecycleebook), ainsi que la [plateforme et les outils Microsoft pour le développement d’applications mobiles](https://aka.ms/MobAppDev/StndPDF).

## <a name="who-should-use-this-guide"></a>Qui doit utiliser ce guide

Ce guide est principalement destiné aux développeurs et aux architectes qui souhaitent apprendre à concevoir et à implémenter des applications d’entreprise multiplateforme à l’aide de Xamarin.Forms .

Un public secondaire est un décideur technique qui souhaite recevoir une vue d’ensemble de l’architecture et des technologies avant de décider de l’approche à sélectionner pour le développement d’applications d’entreprise multiplateforme à l’aide de Xamarin.Forms .

## <a name="how-to-use-this-guide"></a>Comment utiliser ce guide

Ce guide se concentre sur la création d’applications d’entreprise multiplateforme à l’aide de Xamarin.Forms . Par conséquent, elle doit être lue dans son intégralité pour fournir une base de compréhension de ces applications et de leurs considérations techniques. Le guide, ainsi que son exemple d’application, peuvent également servir de point de départ ou de référence pour la création d’une application d’entreprise. Utilisez l’exemple d’application associé comme modèle pour la nouvelle application ou pour voir comment organiser les composants d’une application. Reportez-vous ensuite à ce guide pour obtenir des conseils architecturaux.

N’hésitez pas à transmettre ce guide aux membres de l’équipe afin de garantir une compréhension commune du développement d’applications d’entreprise multiplateforme à l’aide de Xamarin.Forms . Faire en sorte que tout le monde travaille à partir d’un ensemble commun de terminologies et de principes sous-jacents permet de garantir une application cohérente des modèles et des pratiques d’architecture.

## <a name="related-links"></a>Liens connexes

- [Télécharger le livre électronique (PDF de 2 Mo)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (exemple)](https://github.com/dotnet-architecture/eShopOnContainers)
