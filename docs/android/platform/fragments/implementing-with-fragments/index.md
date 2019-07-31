---
title: Implémentation de fragments-procédure pas à pas
description: Cet article explique comment utiliser des fragments pour développer des applications Xamarin. Android.
ms.topic: tutorial
ms.prod: xamarin
ms.assetid: A71E9D87-CB69-10AB-CE51-357A05C76BCD
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/26/2018
ms.openlocfilehash: e5a09c216f0def71efb1c3ddc0ed18672663bdfe
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68643615"
---
# <a name="implementing-fragments---walkthrough"></a>Implémentation de fragments-procédure pas à pas

_Les fragments sont des composants modulaires autonomes qui peuvent aider à gérer la complexité des applications Android qui ciblent des appareils avec une grande variété de tailles d’écran. Cet article explique comment créer et utiliser des fragments lors du développement d’applications Xamarin. Android._

## <a name="overview"></a>Présentation

Dans cette section, vous allez découvrir comment créer et utiliser des fragments dans une application Xamarin. Android. Cette application affiche les titres de plusieurs lectures par William Shakespeare dans une liste. Quand l’utilisateur appuie sur le titre d’une lecture, l’application affiche un guillemet de cette lecture dans une activité distincte:

[![Application en cours d’exécution sur un téléphone Android en mode portrait](./images/intro-screenshot-phone-sml.png)](./images/intro-screenshot-phone.png#lightbox)

Lorsque le téléphone passe en mode paysage, l’apparence de l’application change: la liste des lectures et les guillemets s’affichent dans la même activité. Quand une lecture est sélectionnée, le devis s’affiche dans la même activité:

[![Application en cours d’exécution sur un téléphone Android en mode paysage](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

Enfin, si l’application s’exécute sur une tablette:

[![Application s’exécutant sur une tablette Android](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)

Cet exemple d’application peut facilement s’adapter aux différents facteurs de forme et orientations avec des modifications de code minimes à l’aide de fragments et de [dispositions secondaires](/xamarin/android/app-fundamentals/resources-in-android/alternate-resources).

Les données de l’application existent dans deux tableaux de chaînes codés en dur dans l’application en C# tant que tableaux de chaînes. Chacun des tableaux servira de source de données pour un fragment.  Un groupe contiendra le nom d’un certain nombre de lectures par Shakespeare, et l’autre tableau contiendra un guillemet de cette lecture. Lorsque l’application démarre, elle affiche les noms de lecture dans un `ListFragment`. Lorsque l’utilisateur clique sur une lecture dans `ListFragment`le, l’application démarre une autre activité qui affiche la citation.

L’interface utilisateur de l’application se compose de deux dispositions: une pour portrait et une pour le mode paysage. Au moment de l’exécution, Android détermine la disposition à charger en fonction de l’orientation de l’appareil et fournit cette disposition à l’activité à afficher. Toute la logique pour répondre aux clics de l’utilisateur et afficher les données est contenue dans des fragments. Les activités de l’application existent uniquement en tant que conteneurs qui hébergeront les fragments.

Cette procédure pas à pas est divisée en deux guides. La [première partie](./walkthrough.md) sera axée sur les principales parties de l’application. Un ensemble unique de dispositions (optimisé pour le mode portrait) sera créé, ainsi que deux fragments et deux activités:

1. `MainActivity`&nbsp; Il s’agit de l’activité de démarrage de l’application.
1. `TitlesFragment`&nbsp; Ce fragment affiche une liste des titres des lectures écrites par William Shakespeare. Il sera hébergé par `MainActivity`.
1. `PlayQuoteActivity`démarre le enréponse`TitlesFragment`à l’utilisateur en sélectionnant une lecture dans. `PlayQuoteActivity` &nbsp; `TitlesFragment`
1. `PlayQuoteFragment`&nbsp; Ce fragment affichera un guillemet d’une lecture de William Shakespeare. Il sera hébergé par `PlayQuoteActivity`.

La [deuxième partie de cette procédure pas à pas](./walkthrough-landscape.md) abordera l’ajout d’une disposition alternative (optimisée pour le mode paysage) qui affichera les deux fragments à l’écran. En outre, certaines modifications de code mineures sont apportées au code afin que l’application adapte son comportement au nombre de fragments qui s’affichent simultanément à l’écran.

## <a name="related-links"></a>Liens associés

- [FragmentsWalkthrough (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fragmentswalkthrough)
- [Vue d’ensemble du concepteur](~/android/user-interface/android-designer/index.md)
- [Implémentation de fragments](https://developer.android.com/guide/topics/fundamentals/fragments.html)
- [Package de support](https://developer.android.com/sdk/compatibility-library.html)
