---
title: Fonctionnement des fournisseurs de contenu
ms.prod: xamarin
ms.assetid: B9E2EF89-7EBE-45F5-1ED9-7D2C70BE792C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 9d8710b3b88b59871b88a1d42ec4f4bb3e515ff5
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756504"
---
# <a name="how-content-providers-work"></a>Fonctionnement des fournisseurs de contenu

Deux classes sont impliquées dans une `ContentProvider` interaction :

- **ContentProvider** &ndash; Implémente une API qui expose un jeu de données de manière standard. Les principales méthodes sont interroger, insérer, mettre à jour et supprimer.

- **ContentResolver** Proxy statique qui communique avec un `ContentProvider` pour accéder à ses données, soit à partir de la même application, soit à partir d’une autre application. &ndash;

Un fournisseur de contenu est normalement associé à une base de données SQLite, mais l’API signifie que la consommation de code n’a pas besoin de savoir quoi que ce soit sur le SQL sous-jacent. Les requêtes sont effectuées via un URI à l’aide de constantes pour référencer les noms de colonnes (afin de réduire les dépendances sur la structure de données sous-jacente) et un `ICursor` est retourné pour le code de consommation à itérer.

## <a name="consuming-a-contentprovider"></a>Consommation d’un ContentProvider

`ContentProviders`exposer leurs fonctionnalités par le biais d’un URI inscrit dans le **fichier AndroidManifest. xml** de l’application qui publie les données. Il existe une Convention dans laquelle l’URI et les colonnes de données exposées doivent être disponibles en tant que constantes pour faciliter la liaison avec les données. `ContentProviders` Les fonctionnalités intégrées d’Android fournissent des classes pratiques avec des constantes qui référencent la structure de [`Android.Providers`](xref:Android.Provider) données dans l’espace de noms.

### <a name="built-in-providers"></a>Fournisseurs intégrés

Android offre un accès à un large éventail de données système et utilisateur `ContentProviders`à l’aide des éléments suivants :

- *Navigateur* signets et historique du navigateur (nécessite `READ_HISTORY_BOOKMARKS` l’autorisation et `WRITE_HISTORY_BOOKMARKS`/ou). &ndash;

- *CallLog* &ndash; appels récents effectués ou reçus avec l’appareil.

- *Contacts* &ndash; informations détaillées de la liste de contacts de l’utilisateur, notamment les personnes, les téléphones, les photos & les groupes.

- *Mediastore* &ndash; contenu de l’appareil de l’utilisateur : audio (albums, artistes, genres, playlists), images (miniatures comprises) & vidéo.

- *Paramètres* &ndash; paramètres et préférences de l’appareil à l’ensemble du système.

- *UserDictionary* &ndash; contenu du dictionnaire défini par l’utilisateur utilisé pour l’entrée de texte prédictive.

- *Messagerie vocale* &ndash; historique des messages de messagerie vocale.

## <a name="classes-overview"></a>Vue d’ensemble des classes

Les classes principales utilisées lors de l’utilisation `ContentProvider` d’un sont indiquées ici :

[![Diagramme de classes de l’application de fournisseur de contenu et utilisation des interactions d’application](how-it-works-images/classdiagram1.png)](how-it-works-images/classdiagram1.png#lightbox)

Dans ce diagramme, `ContentProvider` implémente des requêtes et inscrit des URI que d’autres applications utilisent pour localiser des données. Le `ContentResolver` agit comme un « proxy » pour les `ContentProvider` méthodes de requête, d’insertion, de mise à jour et de suppression. Le `SQLiteOpenHelper` contient des données utilisées par `ContentProvider`le, mais il n’est pas directement exposé aux applications consommatrices.
Le `CursorAdapter` passe le curseur retourné `ContentResolver` par à afficher dans un `ListView`. Est `UriMatcher` une classe d’assistance qui analyse les URI lors du traitement des requêtes.

L’objectif de chaque classe est décrit ci-dessous :

- **ContentProvider** &ndash; Implémentez les méthodes de cette classe abstraite pour exposer les données. L’API est mise à la disposition d’autres classes et applications via l’attribut URI qui est ajouté à la définition de classe.

- **SQLiteOpenHelper** Aide à implémenter la Banque de SQLite qui est `ContentProvider`exposée par. &ndash;

- **UriMatcher** &ndash; Utilisez dans`UriMatcher` votre`ContentProvider` implémentation pour faciliter la gestion des URI utilisés pour interroger le contenu.

- **ContentResolver** Le code de consommation utilise `ContentResolver` un pour accéder `ContentProvider` à une instance. &ndash; Les deux classes s’occupent ensemble des problèmes de communication entre processus, ce qui permet de partager facilement les données entre les applications. La consommation de code ne crée `ContentProvider` jamais de classe explicite ; à la place, les données sont accessibles en créant un curseur basé sur un URI `ContentProvider` exposé par l’application.

- **CursorAdapter** &ndash; `ContentProvider`Utilisez `CursorAdapter` ou pourafficherles`SimpleCursorAdapter` données accessibles via un.

L' `ContentProvider` API permet aux consommateurs d’effectuer diverses opérations sur les données, telles que :

- Interroger des données pour retourner des listes ou des enregistrements individuels.
- Modifiez des enregistrements individuels.
- Ajoutez de nouveaux enregistrements.
- Supprimer des enregistrements.

Ce document contient un exemple qui utilise un fourni `ContentProvider`par le système, ainsi qu’un exemple simple en lecture seule qui implémente un personnalisé. `ContentProvider`
