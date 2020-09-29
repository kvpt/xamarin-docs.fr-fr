---
title: Fragments
description: Android 3,0 a introduit des fragments, qui montrent comment prendre en charge des conceptions plus flexibles pour les différentes tailles d’écran disponibles sur les téléphones et les tablettes. Cet article explique comment utiliser des fragments pour développer des applications Xamarin. Android et comment prendre en charge des fragments sur des appareils pré-Android 3,0 (niveau d’API 11).
ms.prod: xamarin
ms.assetid: 1AFB4242-A337-F8E0-83D9-B8D850D7F384
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/15/2018
ms.openlocfilehash: 960f64c05bcc474a7e197c06990840d5e9b88caf
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453842"
---
# <a name="fragments"></a>Fragments

_Android 3,0 a introduit des fragments, qui montrent comment prendre en charge des conceptions plus flexibles pour les différentes tailles d’écran disponibles sur les téléphones et les tablettes. Cet article explique comment utiliser des fragments pour développer des applications Xamarin. Android et comment prendre en charge des fragments sur des appareils pré-Android 3,0 (niveau d’API 11)._

## <a name="fragments-overview"></a>Vue d’ensemble des fragments

Les tailles d’écran plus grandes trouvées sur la plupart des tablettes ont ajouté une couche de complexité supplémentaire au développement Android. une disposition conçue pour le petit écran ne fonctionne pas nécessairement aussi bien pour les écrans plus grands, et vice versa. Pour réduire le nombre de complications introduites, Android 3,0 a ajouté deux nouvelles fonctionnalités, *fragments* et *packages de prise en charge*.

Les fragments peuvent être considérés comme des modules d’interface utilisateur. Ils permettent au développeur de diviser l’interface utilisateur en parties isolées et réutilisables qui peuvent être exécutées dans des activités distinctes. Au moment de l’exécution, les activités elles-mêmes déterminent les fragments à utiliser.

Les packages de prise en charge ont été appelés *bibliothèques de compatibilité* et fragments autorisés à être utilisés sur les appareils qui exécutent des versions d’Android antérieures à Android 3,0 (niveau d’API 11).

Par exemple, l’image ci-dessous montre comment une application unique utilise des fragments sur différents facteurs de forme de périphérique.

[![Diagramme de l’utilisation des fragments dans les tablettes et les téléphones](images/00.png)](images/00.png#lightbox)

*Fragment a* contient une liste, tandis que *fragment B* contient les détails d’un élément sélectionné dans cette liste. Lorsque l’application est exécutée sur une tablette, elle peut afficher les deux fragments sur la même activité. Lorsque la même application est exécutée sur un téléphone (avec sa plus petite taille d’écran), les fragments sont hébergés dans deux activités distinctes. Les fragments A et B sont identiques sur les deux facteurs de forme, mais les activités qui les hébergent sont différentes.

Pour aider à coordonner une activité et gérer tous ces fragments, Android a introduit une nouvelle classe appelée *FragmentManager*. Chaque activité possède sa propre instance de `FragmentManager` pour l’ajout, la suppression et la recherche de fragments hébergés. Le diagramme suivant illustre la relation entre les fragments et les activités :

[![Diagramme illustrant les relations entre l’activité, le gestionnaire de fragment et les fragments](images/01.png)](images/01.png#lightbox)

À certains égards, les fragments peuvent être considérés comme des contrôles composites ou comme des mini-activités. Ils regroupent des éléments d’interface utilisateur dans des modules réutilisables qui peuvent ensuite être utilisés indépendamment par les développeurs dans les activités. Un fragment a une hiérarchie d’affichage, comme une activité, mais, contrairement à une activité, il peut être partagé entre les écrans. Les vues diffèrent des fragments dans les fragments qui ont leur propre cycle de vie. les vues ne le font pas.

Alors que l’activité est un hôte vers un ou plusieurs fragments, elle n’est pas directement consciente des fragments eux-mêmes. De même, les fragments ne sont pas directement informés des autres fragments de l’activité d’hébergement. Toutefois, les fragments et les activités prennent en compte le `FragmentManager` dans leur activité. À l’aide de `FragmentManager` , il est possible pour une activité ou un fragment d’obtenir une référence à une instance spécifique d’un fragment, puis d’appeler des méthodes sur cette instance. De cette façon, l’activité ou les fragments peuvent communiquer et interagir avec d’autres fragments.

Ce guide contient une couverture complète de l’utilisation des fragments, notamment :

- **Création de fragments** : comment créer un fragment et des méthodes de clé de base qui doivent être implémentés.
- **Gestion des fragments et transactions** : comment manipuler des fragments au moment de l’exécution.
- **Package de support Android** : comment utiliser les bibliothèques qui permettent l’utilisation de fragments sur des versions antérieures d’Android.

## <a name="requirements"></a>Configuration requise

Les fragments sont disponibles dans le Android SDK commençant par le niveau d’API 11 (Android 3,0), comme illustré dans la capture d’écran suivante :

[![Sélection du niveau d’API dans le gestionnaire de Android SDK](images/02.png)](images/02.png#lightbox)

Les fragments sont disponibles dans Xamarin. Android 4,0 et versions ultérieures. Une application Xamarin. Android doit cibler au moins le niveau d’API 11 (Android 3,0) ou une version ultérieure pour pouvoir utiliser des fragments. La version cible de .NET Framework peut être définie dans les propriétés du projet, comme indiqué ci-dessous :

[![Définition du niveau de l’API du Framework cible dans les options du projet](images/03-sml.png)](images/03.png#lightbox)

Il est possible d’utiliser des fragments dans les versions antérieures d’Android en utilisant le package de support Android et Xamarin. Android 4,2 ou une version ultérieure. La procédure à suivre est décrite plus en détail dans les documents de cette section.

## <a name="related-links"></a>Liens associés

- [Galerie d’abeilles (exemple)](/samples/xamarin/monodroid-samples/honeycombgallery)
- [Fragments](https://developer.android.com/guide/topics/fundamentals/fragments.html)
- [Package de support](https://developer.android.com/sdk/compatibility-library.html)