---
title: Présentation de ContentProvider
description: Le système d’exploitation Android utilise des fournisseurs de contenu pour faciliter l’accès aux données partagées, telles que les fichiers multimédias, les contacts et les informations de calendrier. Cet article présente la classe ContentProvider et fournit deux exemples montrant comment l’utiliser.
ms.prod: xamarin
ms.assetid: 6E1810AA-EB70-9AD0-1B32-D9418908CC97
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 1e62dc32e9764667cb8737167a49bcc9a4516f0f
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510628"
---
# <a name="intro-to-contentproviders"></a>Présentation de ContentProvider

_Le système d’exploitation Android utilise des fournisseurs de contenu pour faciliter l’accès aux données partagées, telles que les fichiers multimédias, les contacts et les informations de calendrier. Cet article présente la classe ContentProvider et fournit deux exemples montrant comment l’utiliser._


## <a name="content-providers-overview"></a>Vue d’ensemble des fournisseurs de contenu

Un *ContentProvider* encapsule un référentiel de données et fournit une API pour y accéder. Le fournisseur existe dans le cadre d’une application Android qui fournit généralement une interface utilisateur pour l’affichage et la gestion des données. L’avantage principal de l’utilisation d’un fournisseur de contenu est de permettre à d’autres applications d’accéder facilement aux données encapsulées à l’aide d’un objet client fournisseur (appelé *ContentResolver*). Ensemble, un fournisseur de contenu et un programme de résolution de contenu offrent une API inter-application cohérente pour l’accès aux données, qui est simple à générer et à consommer. Toutes les applications peuvent choisir d' `ContentProviders` utiliser pour gérer les données en interne et pour les exposer à d’autres applications.

Une `ContentProvider` est également requise pour que votre application fournisse des suggestions de recherche personnalisées, ou si vous souhaitez fournir la possibilité de copier des données complexes à partir de votre application pour les coller dans d’autres applications. Ce document montre comment accéder à et générer `ContentProviders` avec Xamarin. Android.

La structure de cette section est la suivante:

- **Fonctionnement** Vue d’ensemble de la `ContentProvider` conception de et de son fonctionnement. &ndash;

- **Utilisation d’un fournisseur de contenu** &ndash; Exemple d’accès à la liste des contacts.

- **Utilisation de ContentProvider pour partager des données** Écriture et utilisation d’un `ContentProvider` dans la même application. &ndash;

`ContentProviders`et les curseurs qui fonctionnent sur leurs données sont souvent utilisés pour remplir des ListView. Pour plus d’informations sur l’utilisation de ces classes, reportez-vous au [Guide des ListViews et](~/android/user-interface/layouts/list-view/index.md) des adaptateurs.

`ContentProviders`exposée par Android (ou d’autres applications) est un moyen simple d’inclure des données provenant d’autres sources dans votre application. Elles vous permettent d’accéder à des données, telles que la liste de contacts, les photos ou les événements de calendrier et de les présenter à partir de votre application, et de permettre à l’utilisateur d’interagir avec ces données.

Personnalisé `ContentProviders` sont un moyen pratique pour empaqueter vos données à utiliser dans votre propre application, ou pour une utilisation par d’autres applications (y compris des utilisations spéciales telles que la recherche personnalisée et le copier/coller).

Les rubriques de cette section fournissent des exemples simples de consommation et d’écriture `ContentProvider` de code.



## <a name="related-links"></a>Liens associés

- [Démonstration ContactsAdapter (exemple)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ContactsAdapterDemo/)
- [SimpleContentProvider (sample)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/SimpleContentProvider)
- [Guide des développeurs de fournisseurs de contenu](https://developer.android.com/guide/topics/providers/content-providers.html)
- [Référence de la classe ContentProvider](xref:Android.Content.ContentProvider)
- [Référence de la classe ContentResolver](xref:Android.Content.ContentResolver)
- [Référence de classe ListView](xref:Android.Widget.ListView)
- [Informations de référence sur la classe CursorAdapter](xref:Android.Widget.CursorAdapter)
- [Référence de la classe UriMatcher](xref:Android.Content.UriMatcher)
- [Android.Provider](xref:Android.Provider)
- [Référence de la classe ContactsContract](xref:Android.Provider.ContactsContract)
