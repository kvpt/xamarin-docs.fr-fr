---
title: Présentation du stockage de données dans les applications Xamarin. iOS
description: Ce document décrit les différentes méthodes de stockage des données dans une application Xamarin. iOS et fournit des informations spécifiques sur les avantages de SQLite.
ms.prod: xamarin
ms.assetid: B1994468-FD06-4FD9-96B3-FCEBB13A972A
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/11/2016
ms.openlocfilehash: eefe57abd4ebf4986411a1d717aebd131ebf408f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73008339"
---
# <a name="introduction-to-data-storage-in-xamarinios-apps"></a>Présentation du stockage de données dans les applications Xamarin. iOS

## <a name="when-to-use-a-database"></a>Quand utiliser une base de données

Tandis que les capacités de stockage et de traitement des appareils mobiles sont accrues, les téléphones et les tablettes se décalent toujours derrière leur ordinateur de bureau &amp; leurs homologues. Pour cette raison, il est judicieux de prendre un certain temps pour planifier l’architecture de stockage des données de votre application, plutôt que de supposer qu’une base de données est la réponse à tout moment. Il existe plusieurs options qui répondent à différentes exigences, telles que :

- **Préférences** – iOS offre un mécanisme intégré pour stocker des paires clé-valeur de données simples. Si vous stockez des paramètres utilisateur simples ou des petites portions de données (telles que des informations de personnalisation), utilisez les fonctionnalités natives de la plateforme pour stocker ce type d’informations. Pour iOS, vous pouvez également tirer parti de la synchronisation iCloud pour ces données, à la fois pour la sauvegarde et la synchronisation pour les utilisateurs avec plusieurs appareils.
- **Fichiers texte** : entrée de l’utilisateur ou caches du contenu téléchargé (par exemple, HTML) peut être stocké directement dans le système de fichiers. Utilisez une convention d’affectation de noms de fichiers appropriée pour vous aider à organiser les fichiers et à rechercher des données.
- **Fichiers de données sérialisés** : les objets peuvent être rendus persistants au format XML ou JSON sur le système de fichiers. Le .NET Framework comprend des bibliothèques qui facilitent la sérialisation et la désérialisation des objets. Utilisez les noms appropriés pour organiser les fichiers de données.
- **Base de** données – le moteur de base de données SQLite est disponible iOS et est utile pour stocker des données structurées que vous devez interroger, trier ou manipuler. Le stockage de base de données est adapté aux listes de données avec de nombreuses propriétés.
- **Fichiers image** : bien qu’il soit possible de stocker des données binaires dans la base de données sur un appareil mobile, il est recommandé de les stocker directement dans le système de fichiers. Si nécessaire, vous pouvez stocker les noms de fichiers dans une base de données pour associer l’image à d’autres données. Lors du traitement d’images volumineuses ou de nombreuses images, il est recommandé de planifier une stratégie de mise en cache qui supprime les fichiers dont vous n’avez plus besoin pour éviter de consommer tout l’espace de stockage de l’utilisateur.

Si une base de données est le mécanisme de stockage adapté à votre application, le reste de ce document explique comment utiliser SQLite sur la plateforme Xamarin.

## <a name="advantages-of-using-a-database"></a>Avantages de l’utilisation d’une base de données

L’utilisation d’une base de données SQL dans votre application mobile présente un certain nombre d’avantages :

- Les bases de données SQL permettent un stockage efficace des données structurées.
- Des données spécifiques peuvent être extraites avec des requêtes complexes.
- Les résultats de la requête peuvent être triés.
- Les résultats de la requête peuvent être agrégés.
- Les développeurs possédant des compétences de base de données existantes peuvent utiliser leurs connaissances pour concevoir la base de données et le code d’accès aux données.
- Le modèle de données du composant serveur d’une application connectée peut être réutilisé (en totalité ou en partie) dans l’application mobile.

## <a name="sqlite-database-engine"></a>SQLite Moteur de base de données

SQLite est un moteur de base de données Open source qui a été adopté par Apple pour sa plateforme mobile. Le moteur de base de données SQLite est intégré à iOS, donc aucun travail supplémentaire n’est nécessaire pour les développeurs. SQLite est bien adapté au développement mobile multiplateforme, car :

- Le moteur de base de données est petit, rapide et facile à portable.
- Une base de données est stockée dans un seul fichier, ce qui est facile à gérer sur les périphériques mobiles.
- Le format de fichier est facile à utiliser sur les plateformes : qu’il s’agisse de systèmes 32-ou 64 bits, et de systèmes Big-endian ou Little-endian.
- Il implémente la plupart des SQL92 standard.

Étant donné que SQLite est conçu pour être petit et rapide, il y a quelques inconvénients à son utilisation :

- Une syntaxe de jointure externe n’est pas prise en charge.
- Seules les tables Rename et ADDCOLUMN sont prises en charge. Vous ne pouvez pas effectuer d’autres modifications dans votre schéma.
- Les vues sont en lecture seule.

Vous pouvez en savoir plus sur SQLite sur le site Web- [SQLite.org](https://SQLite.org) . Toutefois, toutes les informations dont vous avez besoin pour utiliser SQLite avec Xamarin sont contenues dans ce document et les exemples associés. Le moteur de base de données SQLite est intégré à toutes les versions d’iOS.
Bien que cela ne soit pas abordé dans ce chapitre, SQLite est également disponible pour une utilisation sur les applications Windows Phone et Windows.

## <a name="windows-and-windows-phone"></a>Windows et Windows Phone

SQLite peut également être utilisé sur les plateformes Windows, bien que ces plateformes ne soient pas abordées dans ce document.
Pour en savoir plus, consultez l’étude de cas [Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) et consultez le [blog de Tim Heuer](https://timheuer.com/blog/archive/2012/06/28/seeding-your-metro-style-app-with-sqlite-database.aspx).

## <a name="related-links"></a>Liens associés

- [DataAccess de base (exemple)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avancé (exemple)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Recettes de données iOS](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Accès aux données Xamarin. Forms](~/xamarin-forms/data-cloud/data/databases.md)
