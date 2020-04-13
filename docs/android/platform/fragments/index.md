---
title: Fragments
description: Android 3.0 introduit Fragments, montrant comment prendre en charge des conceptions plus flexibles pour les nombreuses tailles d’écran différentes trouvées sur les téléphones et les tablettes. Cet article couvrira la façon d’utiliser Fragments pour développer des applications Xamarin.Android, et aussi comment prendre en charge Fragments sur les appareils pré-Android 3.0 (niveau API 11).
ms.prod: xamarin
ms.assetid: 1AFB4242-A337-F8E0-83D9-B8D850D7F384
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/15/2018
ms.openlocfilehash: 5d243429fe4f61768568a634b205055c1ad94297
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020248"
---
# <a name="fragments"></a>Fragments

_Android 3.0 introduit Fragments, montrant comment prendre en charge des conceptions plus flexibles pour les nombreuses tailles d’écran différentes trouvées sur les téléphones et les tablettes. Cet article couvrira la façon d’utiliser Fragments pour développer des applications Xamarin.Android, et aussi comment prendre en charge Fragments sur les appareils pré-Android 3.0 (niveau API 11)._

## <a name="fragments-overview"></a>Aperçu des fragments

Les plus grandes tailles d’écran trouvées sur la plupart des tablettes ont ajouté une couche supplémentaire de complexité au développement Android- une mise en page conçue pour le petit écran ne fonctionne pas nécessairement aussi bien pour les grands écrans, et vice-versa. Pour réduire le nombre de complications que cela a introduit, Android 3.0 a ajouté deux nouvelles fonctionnalités, *Fragments* et *forfaits de soutien*.

Les fragments peuvent être considérés comme des modules d’interface utilisateur. Ils permettent au développeur de diviser l’interface utilisateur en pièces isolées et réutilisables qui peuvent être exécutées dans des activités distinctes. Au moment de l’exécution, les activités elles-mêmes décideront quels Fragments utiliser.

Les forfaits de support étaient à l’origine appelés *bibliothèques de compatibilité* et permettaient à Fragments d’être utilisé sur des appareils qui exécutent des versions d’Android avant Android 3.0 (niveau API 11).

Par exemple, l’image ci-dessous illustre comment une seule application utilise Fragments à travers différents facteurs de forme de périphérique.

[![Diagramme de la façon dont les fragments sont utilisés dans les tablettes et les combinés](images/00.png)](images/00.png#lightbox)

*Fragment A* contient une liste, tandis que *le fragment B* contient des détails pour un élément sélectionné dans cette liste. Lorsque l’application est exécutée sur une tablette, elle peut afficher les deux Fragments sur la même activité. Lorsque la même application est exécutée sur un combiné (avec sa taille d’écran plus petite), les Fragments sont hébergés dans deux activités distinctes. Le fragment A et le fragment B sont les mêmes sur les deux facteurs de forme, mais les activités qui les accueillent sont différentes.

Pour aider une activité à coordonner et à gérer tous ces fragments, Android a introduit une nouvelle classe appelée *FragmentManager*. Chaque activité a son `FragmentManager` propre exemple d’un pour l’ajout, la suppression et la recherche de fragments hébergés. Le diagramme suivant illustre la relation entre Fragments et Activités :

[![Diagramme illustrant les relations entre l’activité, le gestionnaire de fragments, et les fragments](images/01.png)](images/01.png#lightbox)

À certains égards, les fragments peuvent être considérés comme des commandes composites ou comme des mini-activités. Ils regroupent des pièces d’interface utilisateur en modules réutilisables qui peuvent ensuite être utilisés indépendamment par les développeurs dans les activités. Un fragment a une hiérarchie de vue, tout comme une activité, mais, contrairement à une activité, elle peut être partagée sur les écrans. Les vues diffèrent des Fragments dans celui Fragments ont leur propre cycle de vie ; vues ne le font pas.

Bien que l’activité soit l’hôte d’un ou plusieurs Fragments, elle n’est pas directement au courant des Fragments eux-mêmes. De même, Les fragments ne sont pas directement au courant d’autres fragments dans l’activité d’hébergement. Cependant, fragments et activités `FragmentManager` sont au courant de l’activité dans leur activité. En utilisant `FragmentManager`le , il est possible pour une activité ou un fragment d’obtenir une référence à un exemple spécifique d’un fragment, puis d’appeler des méthodes sur cette instance. De cette façon, l’activité ou les fragments peuvent communiquer et interagir avec d’autres fragments.

Ce guide contient une couverture complète sur la façon d’utiliser Fragments, y compris:

- **Création de fragments** - Comment créer un fragment de base et des méthodes clés qui doivent être mises en œuvre.
- **Gestion des fragments et transactions** - Comment manipuler les fragments à l’heure de fonctionnement.
- **Forfait de support Android** - Comment utiliser les bibliothèques qui permettent fragments d’être utilisé sur les anciennes versions d’Android.

## <a name="requirements"></a>Spécifications

Des fragments sont disponibles dans l’Android SDK en commençant par le niveau 11 de l’API (Android 3.0), comme le montre la capture d’écran suivante :

[![Sélection du niveau API dans le gestionnaire Android SDK](images/02.png)](images/02.png#lightbox)

Les fragments sont disponibles en Xamarin.Android 4.0 et plus. Une application Xamarin.Android doit cibler au moins le niveau 11 de l’API (Android 3.0) ou plus afin d’utiliser Fragments. Le cadre cible peut être défini dans les propriétés du projet comme indiqué ci-dessous :

[![Établir le niveau d’API cadre cible dans les options de projet](images/03-sml.png)](images/03.png#lightbox)

Il est possible d’utiliser Fragments dans les anciennes versions d’Android en utilisant le package de support Android et Xamarin.Android 4.2 ou plus. La façon de le faire est abordée plus en détail dans les documents de cette section.

## <a name="related-links"></a>Liens connexes

- [Honeycomb Gallery (échantillon)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/honeycombgallery)
- [Fragments](https://developer.android.com/guide/topics/fundamentals/fragments.html)
- [Forfait de soutien](https://developer.android.com/sdk/compatibility-library.html)
