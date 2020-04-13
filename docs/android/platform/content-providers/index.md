---
title: Intro aux contentProviders
description: Le système d’exploitation Android utilise les fournisseurs de contenu pour faciliter l’accès aux données partagées telles que les fichiers multimédias, les contacts et les informations de calendrier. Cet article introduit la classe ContentProvider, et fournit deux exemples de la façon de l’utiliser.
ms.prod: xamarin
ms.assetid: 6E1810AA-EB70-9AD0-1B32-D9418908CC97
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 496e5c092c79f4f71bddaad30bea6acd1d58d375
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027543"
---
# <a name="intro-to-contentproviders"></a>Intro aux contentProviders

_Le système d’exploitation Android utilise les fournisseurs de contenu pour faciliter l’accès aux données partagées telles que les fichiers multimédias, les contacts et les informations de calendrier. Cet article introduit la classe ContentProvider, et fournit deux exemples de la façon de l’utiliser._

## <a name="content-providers-overview"></a>Aperçu des fournisseurs de contenu

Un *ContentProvider* résume un référentiel de données et fournit une API pour y accéder. Le fournisseur existe dans le cadre d’une application Android qui fournit généralement également une interface utilisateur pour l’affichage / gestion des données. L’avantage clé de l’utilisation d’un fournisseur de contenu est de permettre à d’autres applications d’accéder facilement aux données encapsulées à l’aide d’un objet client fournisseur (appelé *ContentResolver*). Ensemble, un fournisseur de contenu et un résolveur de contenu offrent une API inter-application cohérente pour l’accès aux données qui est simple à construire et à consommer. Toute application peut `ContentProviders` choisir d’utiliser pour gérer les données en interne et aussi pour les exposer à d’autres applications.

A `ContentProvider` est également nécessaire pour votre application de fournir des suggestions de recherche personnalisées, ou si vous voulez fournir la possibilité de copier des données complexes de votre application à coller dans d’autres applications. Ce document montre comment `ContentProviders` accéder et construire avec Xamarin.Android.

La structure de cette section est la suivante :

- **Comment il fonctionne** &ndash; Un `ContentProvider` aperçu de ce que l’est conçu pour et comment il fonctionne.

- **Consommation d’un fournisseur de contenu** &ndash; Un exemple accédant à la liste des contacts.

- **Utilisation de ContentProvider pour partager des données** &ndash; Rédaction et consommation d’un `ContentProvider` dans la même application.

`ContentProviders`et les curseurs qui fonctionnent sur leurs données sont souvent utilisés pour remplir ListViews. Consultez le [guide ListViews and Adapters](~/android/user-interface/layouts/list-view/index.md) pour plus d’informations sur la façon d’utiliser ces classes.

`ContentProviders`exposés par Android (ou d’autres applications) sont un moyen facile d’inclure des données provenant d’autres sources dans votre application. Ils vous permettent d’accéder et de présenter des données telles que la liste Contacts, des photos ou des événements de calendrier à partir de votre application, et permettent à l’utilisateur d’interagir avec ces données.

La `ContentProviders` coutume est un moyen pratique d’emballer vos données pour une utilisation à l’intérieur de votre propre application, ou pour une utilisation par d’autres applications (y compris des utilisations spéciales comme la recherche personnalisée et copier/coller).

Les sujets de cette section fournissent quelques `ContentProvider` exemples simples de la consommation et de la rédaction de code.

## <a name="related-links"></a>Liens connexes

- [ContactsAdapter Demo (échantillon)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-contactsadapterdemo)
- [SimpleContentProvider (échantillon)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-simplecontentprovider)
- [Guide des développeurs de fournisseurs de contenu](https://developer.android.com/guide/topics/providers/content-providers.html)
- [Référence de classe ContentProvider](xref:Android.Content.ContentProvider)
- [Référence de classe ContentResolver](xref:Android.Content.ContentResolver)
- [Référence de classe ListView](xref:Android.Widget.ListView)
- [Référence de classe CursorAdapter](xref:Android.Widget.CursorAdapter)
- [Référence de classe UriMatcher](xref:Android.Content.UriMatcher)
- [Android.Provider (en)](xref:Android.Provider)
- [ContactsContract Class Reference](xref:Android.Provider.ContactsContract)
