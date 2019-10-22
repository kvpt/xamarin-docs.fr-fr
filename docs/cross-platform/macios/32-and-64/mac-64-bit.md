---
title: Mise à jour des applications unifiées Xamarin. Mac vers 64 bits
description: Ce guide explique comment mettre à jour vos applications Xamarin. Mac pour cibler 64 bits. Il fournit également des exemples des types d’erreurs qui peuvent se produire lors de l’exécution de cette modification.
ms.prod: xamarin
ms.assetid: C3810A74-539C-4FFB-B47F-68CA5F7BCDAD
author: conceptdev
ms.author: crdun
ms.date: 02/22/2018
ms.openlocfilehash: 5539bab417c5efc0064cd1753cb74c7524463ee5
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "70765921"
---
# <a name="updating-xamarinmac-unified-applications-to-64-bit"></a>Mise à jour des applications unifiées Xamarin. Mac vers 64 bits

Depuis le 2018 janvier, Apple exige que les nouveaux [envois Mac App Store ciblent 64 bits](https://developer.apple.com/news/?id=06282017a). Les applications déjà disponibles sur le Mac App Store doivent être mises à jour pour cibler 64 bits avant le 2018 juin.

Le **fichier**  > **nouveau modèle de** projet Xamarin. Mac crée des applications 64 bits par défaut. par conséquent, toutes les applications créées récemment sont déjà compatibles avec 64 bits et ne nécessitent aucune modification.

## <a name="targeting-64-bit"></a>Ciblage 64 bits

1. Ouvrez la fenêtre **options de projet** pour votre application Xamarin. Mac :

   ![Menu contextuel du projet](mac-64-bit-images/1-contextual_menu-vsmac.png "Menu contextuel du projet")

2. Sélectionnez **Build Mac** et définissez les **architectures prises en charge** sur **x86 \_64**:

   [![Définition des architectures prises en charge sur x86_64](mac-64-bit-images/2-project_options-vsmac.png "Définition des architectures prises en charge sur x86_64")](mac-64-bit-images/2-project_options-vsmac-large.png#lightbox)

3. Si votre application comporte des dépendances externes, telles que des références natives ou des projets de liaison, mettez-les à jour pour cibler 64 bits.

### <a name="errors"></a>Errors

La première fois que vous générez ou exécutez votre application avec la prise en charge de 64 bits, vous pouvez rencontrer des erreurs de liaison de Clang ou des problèmes d’exécution. Ces erreurs peuvent se produire si des dépendances tierces (par exemple, des références natives dans votre Xamarin. Mac ou des projets de liaisons, ou des infrastructures à l’ensemble du système chargées manuellement) n’ont pas été mises à jour à 64 bits.

> [!TIP]
> La conversion de votre projet en 64 bits est une modification majeure et peut révéler indirectement différentes erreurs de programmation. En particulier, il peut modifier la taille et l’alignement des structures de données, ce qui affecte les signatures p/Invoke et le code natif lié dans votre projet. Examinez tous les avertissements de build fournis et testez soigneusement votre application pour détecter les problèmes potentiels.

#### <a name="example-error-resulting-from-a-dynamically-linked-third-party-dependency-that-does-not-target-64-bit"></a>Exemple d’erreur résultant d’une dépendance tierce liée de manière dynamique qui ne cible pas 64 bits :

```console
ld : warning : ignoring file PATH/ThirdPartyLibrary.framework/ThirdPartyLibrary, 
file was built for i386 which is not the architecture being linked (x86_64): 
PATH/ThirdPartyLibrary.framework/ThirdPartyLibrary 
```

Cette erreur peut être suivie lors de l’exécution de `dlopen` retournant `IntPtr.Zero` au lieu d’un handle attendu.

#### <a name="example-error-resulting-from-a-statically-linked-third-party-dependency-that-does-not-target-64-bit"></a>Exemple d’erreur résultant d’une dépendance tierce liée de manière statique qui ne cible pas 64 bits :

```console
Undefined symbols for architecture x86_64:
  "_LibraryFunction", referenced from:
     -u command line option
ld: symbol(s) not found for architecture x86_64 
```

Pour générer et s’exécuter correctement, mettez à jour ces dépendances sur 64 bits et recompilez votre application.
