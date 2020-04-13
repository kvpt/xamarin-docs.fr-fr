---
title: Fonctionnement des fournisseurs de contenu
ms.prod: xamarin
ms.assetid: B9E2EF89-7EBE-45F5-1ED9-7D2C70BE792C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: e61be6f0189eb825c15fd75764a16706e588ebc9
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020518"
---
# <a name="how-content-providers-work"></a>Fonctionnement des fournisseurs de contenu

Il y a deux `ContentProvider` classes impliquées dans une interaction :

- **ContentProvider** &ndash; implémente une API qui expose un ensemble de données de manière standard. Les principales méthodes sont La requête, l’insertion, la mise à jour et la suppression.

- **ContentResolver** &ndash; Un proxy statique qui `ContentProvider` communique avec un pour accéder à ses données, soit à partir de la même application ou d’une autre application.

Un fournisseur de contenu est normalement soutenu par une base de données SQLite, mais l’API signifie que la consommation de code n’a pas besoin de savoir quoi que ce soit sur le SQL sous-jacent. Les requêtes sont effectuées via un Uri en utilisant des constantes pour référencer `ICursor` les noms de colonnes (pour réduire les dépendances sur la structure de données sous-jacente), et un est retourné pour le code de consommation à itérer plus.

## <a name="consuming-a-contentprovider"></a>Consommer un contentProvider

`ContentProviders`exposer leur fonctionnalité à travers un Uri qui est enregistré dans **l’AndroidManifest.xml** de l’application qui publie les données. Il existe une convention où l’Uri et les colonnes de données qui sont exposées devraient être disponibles en constantes pour faciliter l’enregistrement des données. Android intégré `ContentProviders` tous fournir des classes de commodité avec des [`Android.Providers`](xref:Android.Provider) constantes qui font référence à la structure de données dans l’espace de nom.

### <a name="built-in-providers"></a>Fournisseurs intégrés

Android offre l’accès à un large `ContentProviders`éventail de données système et utilisateur en utilisant :

- *Signets de navigateur* &ndash; et historique `READ_HISTORY_BOOKMARKS` du `WRITE_HISTORY_BOOKMARKS`navigateur (nécessite la permission et/ou ).

- *CallLog* &ndash; appels récents effectués ou reçus avec l’appareil.

- *Communiquez avec* &ndash; des informations détaillées de la liste de contacts de l’utilisateur, y compris des personnes, des téléphones, des photos & des groupes.

- *Contenu MediaStore* &ndash; de l’appareil de l’utilisateur : audio (albums, artistes, genres, playlists), images (y compris vignettes) & vidéo.

- *Paramètres des* &ndash; paramètres et préférences de l’appareil à l’échelle du système.

- *Contenu utilisateurDictionnaire* &ndash; du dictionnaire défini par l’utilisateur utilisé pour l’entrée de texte prédictive.

- *Histoire vocale* &ndash; des messages vocaux.

## <a name="classes-overview"></a>Aperçu des classes

Les classes primaires utilisées `ContentProvider` lors du travail avec un sont montrés ici:

[![Diagramme de classe de l’application de fournisseur de contenu et des interactions d’application de consommation](how-it-works-images/classdiagram1.png)](how-it-works-images/classdiagram1.png#lightbox)

Dans ce diagramme, les `ContentProvider` impléments interrogent et enregistre URI que d’autres applications utilisent pour localiser les données. Le `ContentResolver` agit comme un «proxy» pour les `ContentProvider` méthodes (Requête, Insert, Update, et Supprimer). Les `SQLiteOpenHelper` données contiennent `ContentProvider`des données utilisées par le , mais il n’est pas directement exposé à la consommation d’applications.
Les `CursorAdapter` passes du curseur `ContentResolver` retourné par `ListView`le pour afficher dans un . Il `UriMatcher` s’agit d’une classe d’aide qui analyse les URI lors du traitement des requêtes.

Le but de chaque classe est décrit ci-dessous :

- **ContentProvider** &ndash; Implémentez les méthodes de cette classe abstraite pour exposer les données. L’API est mise à la disposition d’autres classes et applications via l’attribut Uri qui est ajouté à la définition de classe.

- **SQLiteOpenHelper** &ndash; aide à mettre en œuvre `ContentProvider`la datastore SQLite qui est exposée par le .

- **UriMatcher** &ndash; `UriMatcher` Utilisez `ContentProvider` dans votre implémentation pour aider à gérer Uris qui sont utilisés pour interroger le contenu.

- **ContentResolver** &ndash; Le code `ContentResolver` de `ContentProvider` consommation utilise un pour accéder à une instance. Ensemble, les deux classes s’occupent des problèmes de communication interprofessionnels, permettant de partager facilement les données entre les applications. Consommer du code `ContentProvider` ne crée jamais une version explicite de la classe; au lieu de cela, les données sont accessibles en créant `ContentProvider` un curseur basé sur un Uri exposé par l’application.

- **CursorAdapter** &ndash; `CursorAdapter` Utilisation `SimpleCursorAdapter` ou pour afficher `ContentProvider`les données consultées via un .

L’API `ContentProvider` permet aux consommateurs d’effectuer une variété d’opérations sur les données, telles que :

- Requête des données pour retourner les listes ou les enregistrements individuels.
- Modifier les enregistrements individuels.
- Ajoutez de nouveaux enregistrements.
- Supprimer les enregistrements.

Ce document contient un exemple qui `ContentProvider`utilise un système fourni, ainsi qu’un `ContentProvider`exemple simple de lecture seulement qui implémente une coutume .
