---
title: Fonctionnement des fournisseurs de contenu
ms.prod: xamarin
ms.assetid: B9E2EF89-7EBE-45F5-1ED9-7D2C70BE792C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: e61be6f0189eb825c15fd75764a16706e588ebc9
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "73020518"
---
# <a name="how-content-providers-work"></a>Fonctionnement des fournisseurs de contenu

Deux classes sont impliquées dans une interaction `ContentProvider` :

- **ContentProvider** &ndash; implémente une API qui expose un jeu de données de manière standard. Les principales méthodes sont interroger, insérer, mettre à jour et supprimer.

- **ContentResolver** &ndash; un proxy statique qui communique avec un `ContentProvider` pour accéder à ses données, soit à partir de la même application, soit à partir d’une autre application.

Un fournisseur de contenu est normalement associé à une base de données SQLite, mais l’API signifie que la consommation de code n’a pas besoin de savoir quoi que ce soit sur le SQL sous-jacent. Les requêtes sont effectuées via un URI à l’aide de constantes pour référencer les noms de colonnes (afin de réduire les dépendances sur la structure de données sous-jacente), et une `ICursor` est retournée pour le code de consommation à itérer.

## <a name="consuming-a-contentprovider"></a>Consommation d’un ContentProvider

`ContentProviders` exposer leurs fonctionnalités par le biais d’un URI inscrit dans le **fichier AndroidManifest. xml** de l’application qui publie les données. Il existe une Convention dans laquelle l’URI et les colonnes de données exposées doivent être disponibles en tant que constantes pour faciliter la liaison avec les données. Les `ContentProviders` intégrés d’Android offrent des classes pratiques avec des constantes qui référencent la structure de données dans l’espace de noms [`Android.Providers`](xref:Android.Provider) .

### <a name="built-in-providers"></a>Fournisseurs intégrés

Android offre un accès à un large éventail de données système et utilisateur à l’aide de `ContentProviders`:

- *Navigateur* &ndash; les signets et l’historique du navigateur (nécessite des autorisations `READ_HISTORY_BOOKMARKS` et/ou `WRITE_HISTORY_BOOKMARKS`).

- *CallLog* &ndash; appels récents effectués ou reçus avec l’appareil.

- Les *contacts* &ndash; des informations détaillées à partir de la liste de contacts de l’utilisateur, notamment des personnes, des téléphones, des photos & des groupes.

- *MediaStore* &ndash; contenu de l’appareil de l’utilisateur : audio (albums, artistes, genres, playlists), images (y compris les miniatures) & vidéo.

- *Paramètres* &ndash; paramètres et préférences des appareils à l’ensemble du système.

- *UserDictionary* &ndash; contenu du dictionnaire défini par l’utilisateur utilisé pour l’entrée de texte prédictive.

- *Messagerie vocale* &ndash; historique des messages vocaux.

## <a name="classes-overview"></a>Vue d’ensemble des classes

Les classes principales utilisées lors de l’utilisation d’un `ContentProvider` sont indiquées ici :

[![diagramme de classes de l’application de fournisseur de contenu et utilisation des interactions d’application](how-it-works-images/classdiagram1.png)](how-it-works-images/classdiagram1.png#lightbox)

Dans ce diagramme, le `ContentProvider` implémente des requêtes et inscrit des URI que d’autres applications utilisent pour localiser des données. Le `ContentResolver` agit comme un « proxy » pour les `ContentProvider` (méthodes de requête, d’insertion, de mise à jour et de suppression). Le `SQLiteOpenHelper` contient des données utilisées par le `ContentProvider`, mais il n’est pas directement exposé aux applications consommatrices.
Le `CursorAdapter` passe le curseur retourné par l' `ContentResolver` à afficher dans un `ListView`. Le `UriMatcher` est une classe d’assistance qui analyse les URI lors du traitement des requêtes.

L’objectif de chaque classe est décrit ci-dessous :

- **ContentProvider** &ndash; implémenter les méthodes de cette classe abstraite pour exposer les données. L’API est mise à la disposition d’autres classes et applications via l’attribut URI qui est ajouté à la définition de classe.

- **SQLiteOpenHelper** &ndash; aide à implémenter le magasin de banques de SQLite qui est exposé par le `ContentProvider`.

- **UriMatcher** &ndash; utilisez `UriMatcher` dans votre implémentation de `ContentProvider` pour faciliter la gestion des URI utilisés pour interroger le contenu.

- **ContentResolver** &ndash; le code de consommation utilise une `ContentResolver` pour accéder à une instance de `ContentProvider`. Les deux classes s’occupent ensemble des problèmes de communication entre processus, ce qui permet de partager facilement les données entre les applications. La consommation de code ne crée jamais une classe `ContentProvider` explicite ; au lieu de cela, les données sont accessibles en créant un curseur basé sur un URI exposé par l’application `ContentProvider`.

- **CursorAdapter** &ndash; utiliser `CursorAdapter` ou `SimpleCursorAdapter` pour afficher les données accessibles via un `ContentProvider`.

L’API `ContentProvider` permet aux consommateurs d’effectuer diverses opérations sur les données, telles que :

- Interroger des données pour retourner des listes ou des enregistrements individuels.
- Modifiez des enregistrements individuels.
- Ajoutez de nouveaux enregistrements.
- Supprimer des enregistrements.

Ce document contient un exemple qui utilise un `ContentProvider`fourni par le système, ainsi qu’un exemple simple en lecture seule qui implémente un `ContentProvider`personnalisé.
