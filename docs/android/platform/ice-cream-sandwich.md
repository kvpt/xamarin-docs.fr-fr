---
title: Caractéristiques de sandwich à la crème glacée
description: Cet article décrit plusieurs des nouvelles fonctionnalités disponibles pour les développeurs d’applications avec l’Android 4 API - Ice Cream Sandwich. Il couvre plusieurs nouvelles technologies d’interface utilisateur, puis examine une variété de nouvelles fonctionnalités qu’Android 4 offre pour le partage de données entre les applications et entre les appareils.
ms.prod: xamarin
ms.assetid: 78E18A62-C12F-A699-37FA-44B9F6B44273
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 4fbbe1bec317e66166d5218ef0ed54247aa4f6dd
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78291624"
---
# <a name="ice-cream-sandwich-features"></a>Caractéristiques de sandwich à la crème glacée

_Cet article décrit plusieurs des nouvelles fonctionnalités disponibles pour les développeurs d’applications avec l’Android 4 API - Ice Cream Sandwich. Il couvre plusieurs nouvelles technologies d’interface utilisateur, puis examine une variété de nouvelles fonctionnalités qu’Android 4 offre pour le partage de données entre les applications et entre les appareils._

## <a name="overview"></a>Vue d’ensemble

Android OS version 4.0 (API Niveau 14) représente une refonte majeure du système d’exploitation Android et comprend un certain nombre de changements et de mises à niveau importants, y compris:

- **Interface utilisateur mise à jour** - Plusieurs nouvelles fonctionnalités d’interface utilisateur donnent aux développeurs plus de puissance et de flexibilité lorsqu’ils créent des interfaces utilisateur d’application. Ces nouvelles fonctionnalités comprennent: `GridLayout` , , `PopupMenu` `Switch` widget, et `TextureView` .
- **Meilleure accélération matérielle** - rendu 2D a maintenant lieu sur le GPU pour tous les contrôles Android. En outre, l’accélération matérielle est allumée, par défaut, dans toutes les applications développées pour Android 4.0.
- **Nouvelles API de données** - Il y a un nouvel accès aux données qui n’étaient pas auparavant officiellement accessibles, telles que les données de calendrier et le profil utilisateur du propriétaire de l’appareil.
- **Partage de données d’application** - Partager des données entre `ShareActionProvider` les applications et les appareils est maintenant plus facile que jamais via des technologies telles que le , ce qui facilite la création d’une action de partage à partir d’une barre d’action, et *Android Beam* pour *les communications de champ proche (NFC)* , ce qui rend un clin d’œil pour partager des données à proximité les uns des autres.

Dans cet article, nous allons explorer ces fonctionnalités et d’autres changements qui ont été apportées à l’API Android 4.0, et nous allons expliquer comment utiliser chaque fonctionnalité avec Xamarin.Android.

## <a name="user-interface-features"></a>Caractéristiques de l’interface utilisateur

Une variété de nouvelles technologies d’interface utilisateur sont disponibles avec Android 4, y compris:

- **[GridLayout](~/android/user-interface/layouts/grid-layout.md)** - Prend en charge la mise en page des commandes du réseau 2D.
- **[Commutateur widget](~/android/user-interface/controls/switch.md)** - Permet de basculer entre ON ou OFF.
- **[TextureView](~/android/user-interface/controls/texture-view.md)** - Permet la vidéo et le contenu OpenGL dans une vue.
- **[Barre de navigation](~/android/user-interface/controls/navigation-bar.md)** contient des boutons virtuels pour le dos, la maison et le multitâche.

En outre, d’autres éléments d’interface utilisateur ont été améliorés, tels que le `<a href"/guides/android/user_interface/popup_menus">PopupMenu</a>`, qui est maintenant plus facile à travailler avec, et les onglets, qui ont une apparence plus polie.

## <a name="sharing-features"></a>Partage des fonctionnalités

Android 4 comprend plusieurs nouvelles technologies qui nous permettent de partager des données sur tous les appareils et sur toutes les applications. Il donne également accès à divers types de données qui n’étaient pas disponibles auparavant, telles que les informations de calendrier et le profil d’utilisateur du propriétaire de l’appareil. Dans cette section, nous allons examiner une variété de fonctionnalités offertes par Android 4 qui traitent de ces domaines, y compris:

- **[Android Beam](~/android/platform/android-beam.md)** - Permet le partage de données via NFC.
- **[ShareActionProvider](~/android/user-interface/controls/action-bar.md)** - Crée un fournisseur qui permet aux développeurs de spécifier les actions de partage à partir de la barre d’action.
- **[Profil utilisateur](~/android/user-interface/user-profile.md)** - Donne accès aux données de profil du propriétaire de l’appareil.
- **[L’API de calendrier](~/android/user-interface/controls/calendar.md)** - Donne accès aux données du calendrier du fournisseur de calendrier.

## <a name="x86-emulators"></a>x86 émulateurs

ICS ne prend pas encore en charge le développement avec un émulateur x86. les émulateurs x86 ne sont pris en charge qu’avec Android 2.3.3, niveau API 10. Voir [Configuring l’émulateur x86](~/android/get-started/installation/android-emulator/index.md) pour plus d’informations.

## <a name="summary"></a>Récapitulatif

Cet article a couvert une variété des nouvelles technologies qui sont maintenant disponibles avec Android 4. Nous avons passé en revue de nouvelles fonctionnalités d’interface utilisateur telles que le *GridLayout*, *PopupMenu*, et le widget *Switch.* Nous avons également examiné certains des nouveaux soutiens pour contrôler l’interface utilisateur du système, ainsi que la façon de travailler avec le *TextureView*. Ensuite, nous avons discuté d’une variété de nouvelles technologies de partage. Nous avons couvert la façon dont *Android Beam* vous permet de partager des informations sur tous les appareils qui utilisent *NFC*, discuté de la nouvelle *API Calendrier*, et a également montré comment utiliser le intégré dans *ShareActionProvider*.
Enfin, nous avons examiné comment utiliser le fournisseur *ContactsContract* pour accéder aux données du profil utilisateur.

## <a name="related-links"></a>Liens connexes

- [TextureViewDemo (échantillon)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/textureviewdemo)
- [CalendarDemo (échantillon)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/calendardemo)
- [Tutoriel de mise en page de l’onglet](~/android/user-interface/layouts/tab-layout/index.md)
- [Sandwich à la crème glacée](https://developer.android.com/about/versions/android-4.0-highlights.html)
- [Android 4.0 Plateforme](https://developer.android.com/about/versions/android-4.0.html)
