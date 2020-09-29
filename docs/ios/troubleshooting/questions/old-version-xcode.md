---
title: Puis-je utiliser une version antérieure de Xcode ou Xamarin. iOS ?
description: Ce guide décrit les problèmes liés à l’utilisation de versions antérieures de Xamarin. iOS ou Xcode (par rapport à la version stable actuelle).
ms.prod: xamarin
ms.assetid: 27CF7EB7-9251-435F-BEA5-F20F8DD7DC17
ms.technology: xamarin-ios
author: chamons
ms.author: chhamo
ms.date: 04/16/2019
ms.openlocfilehash: 6c6a0491f7989f2f76afabc3412e38be74a06da4
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431583"
---
# <a name="can-i-use-an-older-version-of-xcode-or-xamarinios"></a>Puis-je utiliser une version antérieure de Xcode ou Xamarin. iOS ?

La documentation Xamarin suppose l’utilisation de la dernière version de Xamarin. iOS et Xcode, ce qui est recommandé. Toutefois, certains clients préfèrent utiliser les anciens Xamarin. iOS et/ou Xcode et souhaiteraient des détails sur les conséquences.

Les notes de publication contiennent l’avertissement suivant :

> [!WARNING]
> **Utilisation d’une version antérieure de Xcode**
>
> L’utilisation d’une version antérieure de Xcode (celle mentionnée dans les [conditions requises](/xamarin/ios/release-notes/12/12.8#requirements)ci-dessus) est souvent possible, mais certaines fonctionnalités peuvent ne pas être disponibles. Certaines limitations peuvent également nécessiter des solutions de contournement, par exemple :
>
> - Le Bureau d’enregistrement statique requiert des fichiers d’en-têtes Xcode pour générer des applications, entraînant [`MT0091`](../mtouch-errors.md#MT0091) [`MT4109`](../mtouch-errors.md#MT4109) des erreurs ou si des API sont manquantes. Dans la plupart des cas, l’activation de l’éditeur de liens managés vous aidera (en supprimant l’API).
> - Les builds Bitcode (pour tvOS et Watchos) peuvent échouer à l’envoi à l’App Store, sauf si un chaîne d’outils Xcode 9.0 + est utilisé.

## <a name="further-information"></a>Informations supplémentaires

Microsoft recommande vivement d’utiliser la dernière version de Xcode et Xamarin. iOS la plus récente lors du développement et de la soumission d’applications. Apple requiert l’utilisation de Xcode le plus récent pour soumettre des applications.

Notez que l’utilisation de la dernière version de Xcode n’empêche pas votre application de cibler des versions iOS plus anciennes. Les versions d’iOS que vous prenez en charge dépendent de votre entrée **info. plist** et des API que votre application utilise.

Il est possible d’installer plusieurs versions de Xcode côte à côte, avec des noms différents, tels que **Xcode101. app** et **Xcode102. app**. Si vous utilisez plusieurs versions, veillez à définir le Xcode actif dans [Visual Studio pour Mac paramètres](~/ios/troubleshooting/questions/ios-sdk.md) et à l’aide de l' [`xcode-select`](https://developer.apple.com/library/archive/technotes/tn2339/_index.html#//apple_ref/doc/uid/DTS40014588-CH1-HOW_DO_I_SELECT_THE_DEFAULT_VERSION_OF_XCODE_TO_USE_FOR_MY_COMMAND_LINE_TOOLS_) [outil en ligne de commande](https://developer.apple.com/library/archive/technotes/tn2339/_index.html#//apple_ref/doc/uid/DTS40014588-CH1-HOW_DO_I_SELECT_THE_DEFAULT_VERSION_OF_XCODE_TO_USE_FOR_MY_COMMAND_LINE_TOOLS_).

Toutefois, de rares circonstances peuvent nécessiter l’utilisation de composants plus anciens. Cette documentation décrit les défis généraux auxquels vous pouvez être confronté lors de l’utilisation de versions antérieures à la dernière version.

Chaque version d’Apple est cependant unique, et vous pouvez rencontrer d’autres pièges non documentés ici.

Ces défis sont parfois difficiles à résoudre. par conséquent, chaque fois que cela est possible, il est possible de configurer la dernière version de Xcode et la dernière version de Xamarin. iOS.

## <a name="use-of-an-old-xamarinios-with-an-old-xcode"></a>Utilisation d’un ancien Xamarin. iOS avec un ancien Xcode

La mise à jour de Xamarin. iOS et Xcode est possible, au moins pendant une certaine période. La limite est que, à un moment donné, Apple nécessite une version minimale de Xcode pour soumettre vos applications. À ce stade, vous devez mettre à jour tous vos composants (macOS, Xcode et Xamarin. iOS) vers les versions les plus récentes (ou la nouvelle version minimale de Xcode requise par Apple et la version Xamarin. iOS correspondante).

Il est généralement plus facile de mettre à jour progressivement et de suivre les petites modifications. Pour les grands projets dans lesquels les mises à jour peuvent être plus difficiles à suivre, le maintien d’une plage de travail connue peut être un bon compromis.

## <a name="use-of-new-xamarinios-with-older-xcode"></a>Utilisation de New Xamarin. iOS avec Xcode plus ancien

Xamarin. iOS en général prend en charge les anciennes versions de Xcode chaque fois que cela est possible. Voici quelques-uns des défis potentiels :

- La version plus récente de Xamarin. iOS peut prendre en charge certaines fonctionnalités et API non présentes dans le Xcode sélectionné. 
- Le **Bureau** d’enregistrement statique requiert des fichiers d’en-têtes Xcode pour générer des applications, entraînant [`MT0091`](~/ios/troubleshooting/mtouch-errors.md#MT0091) [`MT4109`](~/ios/troubleshooting/mtouch-errors.md#MT4109) des erreurs ou si des API sont manquantes.
  - Dans la plupart des cas, l’activation de l’éditeur de liens managés vous aidera (en supprimant les liaisons managées pour la nouvelle API) si elles ne sont pas utilisées.
- Les builds Bitcode (pour tvOS et Watchos) peuvent échouer à l’envoi à l’App Store, sauf si un chaîne d’outils Xcode 9.0 + est utilisé.

## <a name="use-of-new-xcode-with-older-xamarinios"></a>Utilisation de Xcode avec un ancien Xamarin. iOS

Ce cas d’usage est beaucoup plus difficile, car Xamarin. iOS ne peut pas prédire les exigences fluctuantes du nouveau Xcode. Les mises à jour de macOS peuvent également introduire des problèmes et sans correctifs de compatibilité de nombreuses parties de Xamarin. iOS pourraient être affectées. 

Il y a un certain nombre de domaines potentiels dans lesquels des problèmes peuvent se poser :

- Incompatibilités avec `mlaunch` :
  - La prise en charge du simulateur peut ne pas fonctionner correctement (ou du tout)
  - La prise en charge des appareils peut ne pas fonctionner correctement (ou du tout)
- Prise en charge inconnue de `mtouch` 
  - Aucune prise en charge pour les nouveaux frameworks
  - Aucune prise en charge des nouveaux droits
  - Aucune prise en charge des outils nouveaux ou mis à jour
    - Cela peut également affecter la signature du code

### <a name="new-appstore-submission-rules"></a>Nouvelles règles de soumission AppStore

Apple se réserve le droit de mettre à jour les règles de soumissions AppStore à tout moment. Ces modifications de règles sont parfois annoncées à l’avance. Certaines de ces modifications requièrent des modifications d’outils pour prendre en charge, ce qui nécessiterait un composant Xamarin. iOS mis à jour.

En plus des modifications apportées aux règles, Apple ajoute souvent des validations supplémentaires aux applications soumises ou renforce celles qui existent déjà. Certaines d’entre elles nécessitent des modifications dans nos outils (par exemple, un nouveau symbole de liste noire). Un grand nombre d’entre eux sont tout d’abord rencontrés par les clients qui envoient, car il n’y a aucune annonce (ou liste) des règles.

## <a name="summary"></a>Récapitulatif

Dans la mesure du possible, jouez en toute sécurité en suivant les conseils d’Apple et en développant et en soumettant avec la dernière version de Xcode publiée sur l’App Store.

À son tour, utilisez la dernière version de Xamarin. iOS. Cela permet de suivre les derniers correctifs susceptibles d’affecter les applications soumises et de se conformer aux modifications de règle les plus récentes.

Si ce n’est pas possible, envisagez d’utiliser une version antérieure de Xcode et Xamarin. iOS. Cela peut fonctionner pendant une période, mais à un moment donné, Apple insistera sur les outils plus récents afin de planifier en conséquence.