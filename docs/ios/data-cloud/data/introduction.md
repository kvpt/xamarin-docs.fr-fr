---
title: Introduction au stockage des données dans les applications Xamarin.iOS
description: Ce document décrit les différents moyens de stockage de données dans une application Xamarin.iOS et fournit des informations spécifiques sur les avantages de SQLite.
ms.prod: xamarin
ms.assetid: B1994468-FD06-4FD9-96B3-FCEBB13A972A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/11/2016
ms.openlocfilehash: 06fd77293302a3d6d5d44def9d0a83e3f1a57ede
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61217737"
---
# <a name="introduction-to-data-storage-in-xamarinios-apps"></a>Introduction au stockage des données dans les applications Xamarin.iOS

## <a name="when-to-use-a-database"></a>Quand utiliser une base de données

Tandis que les capacités de stockage et traitement des appareils mobiles augmente, téléphones et tablettes toujours rester derrière son bureau &amp; équivalents de l’ordinateur portable. Pour cette raison, il est important de prendre le temps de planifier l’architecture de stockage de données pour votre application plutôt que de simplement une base de données est la bonne solution tout le temps. Il existe un nombre de différentes options adaptées à des exigences différentes, telles que :

-  **Préférences** – iOS offre un mécanisme intégré pour stocker des paires clé-valeur simples de données. Si vous stockez des paramètres utilisateur simple ou petits morceaux de données (par exemple, les informations de personnalisation) vous devez ensuite utiliser les fonctionnalités natives de la plateforme pour le stockage de ce type d’information. Pour iOS, vous pouvez également tirer parti de la synchronisation iCloud pour ces données, à la fois pour la sauvegarde et de synchronisation pour les utilisateurs disposant de plusieurs appareils.
-  **Fichiers texte** – l’entrée d’utilisateur ou des caches de contenu (par ex téléchargement. HTML) peut être stockée directement sur le système de fichiers. Utiliser une convention d’affectation de noms de fichier appropriée pour vous aider à organiser les fichiers et de rechercher des données.
-  **Sérialiser des fichiers de données** – objets peuvent être conservés en tant que XML ou JSON sur le système de fichiers. Le .NET framework inclut des bibliothèques de sérialisation et désérialisation des objets facile. Utilisez les noms appropriés pour organiser les fichiers de données.
-  **Base de données** – moteur de base de données SQLite The est iOS disponible et est utile pour stocker des données semi-structurées dont vous avez besoin pour interroger, trier ou sinon manipuler. Stockage de base de données est adapté aux listes de données avec de nombreuses propriétés.
-  **Fichiers image** – bien qu’il soit possible de stocker des données binaires dans la base de données sur un appareil mobile, il est recommandé de les stocker directement dans le système de fichiers. Si nécessaire, vous pouvez stocker les noms de fichiers dans une base de données à associer l’image aux autres données. Lors du traitement de grandes images, ou un grand nombre d’images, il est conseillé de planifier une stratégie de mise en cache qui supprime les fichiers que vous n’avez plus besoin pour éviter de consommer l’espace de stockage de tous les l’utilisateur.


Si une base de données est le mécanisme de stockage approprié pour votre application, le reste de ce document explique comment utiliser SQLite sur la plateforme Xamarin.

## <a name="advantages-of-using-a-database"></a>Avantages de l’utilisation d’une base de données

Il existe un certain nombre d’avantages à l’utilisation d’une base de données SQL dans votre application mobile :

-  Bases de données SQL permettent un stockage efficace de données structurées.
-  Données spécifiques peuvent être extraits avec des requêtes complexes.
-  Résultats de la requête peuvent être triées.
-  Résultats de la requête peuvent être agrégées.
-  Les développeurs avec les compétences de base de données existantes peuvent utiliser leurs connaissances pour concevoir le code d’accès aux données et de la base de données.
-  Le modèle de données à partir du composant serveur d’une application connectée peut-être être réutilisée (en totalité ou en partie) dans l’application mobile.


## <a name="sqlite-database-engine"></a>Moteur de base de données SQLite

SQLite est un moteur de base de données open source qui a été adopté par Apple pour leur plateforme mobile. Le moteur de base de données SQLite est intégré pour iOS et il n’existe aucun travail supplémentaire pour les développeurs à en tirer parti. SQLite est bien adapté au développement mobile multiplateforme, car :

-  Le moteur de base de données est petit, rapidement et facilement portable.
-  Une base de données est stockée dans un seul fichier, ce qui est facile à gérer sur des appareils mobiles.
-  Le format de fichier est facile à utiliser sur les plateformes : si 32 ou 64 bits et les systèmes de big - endian ou little-endian.
-  Il implémente la plupart de la SQL92 standard.


Étant donné que SQLite est conçu pour être petit et rapide, il existe certains inconvénients sur son utilisation :

-  Certaines syntaxes de jointure OUTER n’est pas pris en charge.
-  Changement de nom de table uniquement et ADDCOLUMN sont pris en charge. Vous ne pouvez effectuer d’autres modifications à votre schéma.
-  Les vues sont en lecture seule.


Vous pouvez en savoir plus sur SQLite sur le site Web - [SQLite.org](http://SQLite.org) : Toutefois, toutes les informations que vous devez utiliser SQLite avec Xamarin sont contenues dans ce document et associé les exemples. Le moteur de base de données SQLite est intégré à toutes les versions d’e/s.
Bien que ne pas abordé dans ce chapitre, SQLite est également disponible pour une utilisation sur Windows Phone et les applications Windows.

## <a name="windows-and-windows-phone"></a>Windows et Windows Phone

SQLite peut également être utilisé sur les plateformes Windows, bien que ces plateformes ne sont pas traités dans ce document.
En savoir plus dans le [Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) et [Tasky Pro](http://docs.xamarin.com/guides/cross-platform/application_fundamentals/building_cross_platform_applications/case_study%3A_tasky) études de cas et passez en revue [blog de Tim Heuer](http://timheuer.com/blog/archive/2012/06/28/seeding-your-metro-style-app-with-sqlite-database.aspx).



## <a name="related-links"></a>Liens associés

- [DataAccess Basic (exemple)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avancé (exemple)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS recettes de données](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Accès aux données de Xamarin.Forms](~/xamarin-forms/app-fundamentals/databases.md)
