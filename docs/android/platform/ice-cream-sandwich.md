---
title: Caractéristiques du sandwich glace
description: Cet article décrit plusieurs des nouvelles fonctionnalités disponibles pour les développeurs d’applications avec l’API Android 4-Ice Ice. Il couvre plusieurs nouvelles technologies d’interface utilisateur, puis examine une série de nouvelles fonctionnalités offertes par Android 4 pour le partage de données entre les applications et entre les appareils.
ms.prod: xamarin
ms.assetid: 78E18A62-C12F-A699-37FA-44B9F6B44273
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 8cbb7c9c3da92b1ea5140e6f85accf53dab0bf80
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70761464"
---
# <a name="ice-cream-sandwich-features"></a>Caractéristiques du sandwich glace

_Cet article décrit plusieurs des nouvelles fonctionnalités disponibles pour les développeurs d’applications avec l’API Android 4-Ice Ice. Il couvre plusieurs nouvelles technologies d’interface utilisateur, puis examine une série de nouvelles fonctionnalités offertes par Android 4 pour le partage de données entre les applications et entre les appareils._

## <a name="overview"></a>Présentation

Le système d’exploitation Android version 4,0 (niveau d’API 14) représente un retravail majeur du système d’exploitation Android et inclut un certain nombre de modifications et de mises à niveau importantes, notamment :

- **Interface utilisateur mise à jour** : plusieurs nouvelles fonctionnalités de l’interface utilisateur offrent aux développeurs plus de puissance et de flexibilité lorsqu’ils créent des interfaces utilisateur d’application. Ces nouvelles fonctionnalités sont notamment `GridLayout` : `PopupMenu` , `Switch` , widget et `TextureView` . 
- **Meilleure accélération matérielle** : le rendu 2D s’effectue désormais sur le GPU pour tous les contrôles Android. En outre, l’accélération matérielle est activée, par défaut, dans toutes les applications développées pour Android 4,0. 
- **Nouvelles API de données** : il existe un nouvel accès aux données qui n’étaient pas auparavant officiellement accessibles, telles que les données de calendrier et le profil utilisateur du propriétaire de l’appareil. 
- **Partage de données d’application** : le partage de données entre les applications et les appareils est désormais plus facile `ShareActionProvider` que jamais via des technologies telles que, ce qui facilite la création d’une action de partage à partir d’un barre d’action et d’un *faisceau Android* pour *le terrain proche Communications (NFC)* , qui en fait un composant logiciel enfichable pour partager des données entre les appareils à proximité l’un de l’autre. 

Dans cet article, nous allons explorer ces fonctionnalités et d’autres modifications apportées à l’API Android 4,0, et nous expliquerons comment utiliser chaque fonctionnalité avec Xamarin. Android.

## <a name="user-interface-features"></a>Fonctionnalités de l’interface utilisateur

Une variété de nouvelles technologies d’interface utilisateur sont disponibles avec Android 4, notamment :

- **[GridLayout](~/android/user-interface/layouts/grid-layout.md)** : prend en charge la disposition en grille 2D des contrôles. 
- **[Widget de basculement](~/android/user-interface/controls/switch.md)** : permet de basculer entre on ou OFF. 
- **[TextureView](~/android/user-interface/controls/texture-view.md)** : active le contenu vidéo et OpenGL dans une vue. 
- **[Barre de navigation](~/android/user-interface/controls/navigation-bar.md)** : contient des boutons virtuels pour l’arrière-plan, la page d’hébergement et l’exécution multitâche. 

En outre, d’autres éléments de l’interface utilisateur ont été améliorés, tels que le `<a href"/guides/android/user_interface/popup_menus">PopupMenu</a>`, qui est désormais plus facile à utiliser, et les onglets, qui ont une apparence plus soignée.

## <a name="sharing-features"></a>Partage des fonctionnalités

Android 4 comprend plusieurs nouvelles technologies qui nous permettent de partager des données entre les appareils et entre les applications. Il permet également d’accéder à différents types de données qui n’étaient pas auparavant disponibles, telles que des informations de calendrier et le profil utilisateur du propriétaire de l’appareil. Dans cette section, nous allons examiner toute une série de fonctionnalités offertes par Android 4, qui répondent aux questions suivantes :

- **[Android Beam](~/android/platform/android-beam.md)** : autorise le partage de données via NFC.
- **[ShareActionProvider](~/android/user-interface/controls/action-bar.md)** : crée un fournisseur qui permet aux développeurs de spécifier des actions de partage à partir de l’barre d’action. 
- **[Profil utilisateur](~/android/user-interface/user-profile.md)** : permet d’accéder aux données de profil du propriétaire de l’appareil. 
- **[API de calendrier](~/android/user-interface/controls/calendar.md)** : permet d’accéder aux données de calendrier à partir du fournisseur de calendrier. 

## <a name="x86-emulators"></a>Émulateurs x86

ICS ne prend pas encore en charge le développement avec un émulateur x86. les émulateurs x86 sont uniquement pris en charge avec Android 2.3.3, niveau d’API 10. Pour plus d’informations, consultez [configuration de l’émulateur x86](~/android/get-started/installation/android-emulator/index.md) .

## <a name="summary"></a>Récapitulatif

Cet article a présenté une série de nouvelles technologies désormais disponibles avec Android 4. Nous avons examiné les nouvelles fonctionnalités de l’interface utilisateur, telles que *GridLayout*, *PopupMenu*et le widget de *commutateur* . Nous avons également examiné la nouvelle prise en charge du contrôle de l’interface utilisateur du système, ainsi que l’utilisation de *TextureView*. Nous avons ensuite abordé une variété de nouvelles technologies de partage. Nous avons abordé la manière dont *Android Beam* vous permet de partager des informations entre des appareils qui utilisent *NFC*, décrit la nouvelle *API de calendrier*et vous a également montré comment utiliser le *ShareActionProvider*intégré.
Enfin, nous avons examiné comment utiliser le fournisseur *ContactsContract* pour accéder aux données de profil utilisateur.

## <a name="related-links"></a>Liens associés

- [Exemples de glace glace](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-ics-samples)
- [TextureViewDemo (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/textureviewdemo)
- [CalendarDemo (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/calendardemo)
- [Didacticiel sur la disposition des onglets](~/android/user-interface/layouts/tab-layout/index.md)
- [Sandwich glace](https://developer.android.com/about/versions/android-4.0-highlights.html)
- [Plateforme Android 4,0](https://developer.android.com/about/versions/android-4.0.html)
