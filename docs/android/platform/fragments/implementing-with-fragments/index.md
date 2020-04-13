---
title: Mise en œuvre des fragments - Procédure pas à pas
description: Cet article se promène à travers la façon d’utiliser des fragments pour développer des applications Xamarin.Android.
ms.topic: tutorial
ms.prod: xamarin
ms.assetid: A71E9D87-CB69-10AB-CE51-357A05C76BCD
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/26/2018
ms.openlocfilehash: b601fc37cc75dcd43c3688de8d302f0a47a06b35
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027417"
---
# <a name="implementing-fragments---walkthrough"></a>Mise en œuvre de fragments - procédure pas à pas

_Les fragments sont des composants modulaires autonomes qui peuvent aider à répondre à la complexité des applications Android qui ciblent les appareils avec une variété de tailles d’écran. Cet article se promène à travers la façon de créer et d’utiliser des fragments lors du développement des applications Xamarin.Android._

## <a name="overview"></a>Vue d’ensemble

Dans cette section, vous allez parcourir comment créer et utiliser des fragments dans une application Xamarin.Android. Cette application affichera les titres de plusieurs pièces de William Shakespeare dans une liste. Lorsque l’utilisateur tape sur le titre d’une pièce de théâtre, l’application affiche une citation de ce jeu dans une activité distincte :

[![Application fonctionnant sur un téléphone Android en mode portrait](./images/intro-screenshot-phone-sml.png)](./images/intro-screenshot-phone.png#lightbox)

Lorsque le téléphone est tourné en mode paysage, l’apparence de l’application change : la liste des jeux et des citations apparaîtra dans la même activité. Lorsqu’une pièce est sélectionnée, la citation s’affiche dans la même activité :

[![Application fonctionnant sur un téléphone Android en mode paysage](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

Enfin, si l’application est en cours d’exécution sur une tablette:

[![Application fonctionnant sur une tablette Android](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)

Cette application d’échantillon peut facilement s’adapter aux différents facteurs de forme et orientations avec des modifications minimales de code en utilisant des fragments et [des mises en page alternatives.](/xamarin/android/app-fundamentals/resources-in-android/alternate-resources)

Les données de l’application existeront dans deux tableaux de cordes qui sont codés en dur dans l’application sous forme de tableaux de cordes C. Chacun des tableaux servira de source de données pour un fragment.  Un tableau tiendra le nom de certaines pièces de Shakespeare, et l’autre tableau tiendra une citation de cette pièce. Lorsque l’application démarre, elle affiche les `ListFragment`noms de jeu dans un . Lorsque l’utilisateur clique sur `ListFragment`un jeu dans le , l’application va démarrer une autre activité qui affichera la citation.

L’interface utilisateur de l’application se composera de deux mises en page, l’une pour le portrait et l’autre pour le mode paysage. Au moment de l’exécution, Android déterminera quelle mise en page à charger en fonction de l’orientation de l’appareil et fournira cette mise en page à l’activité à rendre. Toute la logique pour répondre aux clics des utilisateurs et afficher les données sera contenue dans des fragments. Les activités de l’application n’existent que sous forme de conteneurs qui hébergeront les fragments.

Cette procédure pas à pas sera décomposée en deux guides. La [première partie](./walkthrough.md) se concentrera sur les parties principales de l’application. Un seul ensemble de mises en page (optimisé pour le mode portrait) sera créé, ainsi que deux fragments et deux activités :

1. `MainActivity`&nbsp; Il s’agit de l’activité de démarrage pour l’application.
1. `TitlesFragment`&nbsp; Ce fragment affichera une liste de titres de pièces qui ont été écrites par William Shakespeare. Il sera accueilli `MainActivity`par .
1. `PlayQuoteActivity`commencera la `PlayQuoteActivity` réponse à l’utilisateur sélectionnant un jeu dans `TitlesFragment`. &nbsp; `TitlesFragment`
1. `PlayQuoteFragment`&nbsp; Ce fragment affichera une citation d’une pièce de William Shakespeare. Il sera accueilli `PlayQuoteActivity`par .

La [deuxième partie de cette procédure pas à pas](./walkthrough-landscape.md) portera sur l’ajout d’une disposition alternative (optimisée pour le mode paysage) qui affichera les deux fragments à l’écran. En outre, quelques modifications mineures du code seront apportées au code afin que l’application adapte son comportement au nombre de fragments qui sont affichés simultanément sur l’écran.

## <a name="related-links"></a>Liens connexes

- [FragmentsWalkthrough (échantillon)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fragmentswalkthrough)
- [Vue d'ensemble du concepteur](~/android/user-interface/android-designer/index.md)
- [Mise en œuvre des fragments](https://developer.android.com/guide/topics/fundamentals/fragments.html)
- [Forfait de soutien](https://developer.android.com/sdk/compatibility-library.html)
