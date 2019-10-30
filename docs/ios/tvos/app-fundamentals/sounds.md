---
title: Émettre du son dans tvOS avec AVAudioPlayer dans Xamarin
description: Cet article explique comment utiliser une classe d’assistance pour contrôler la lecture du son à l’aide d’un AVAudioPlayer dans une application Xamarin. iOS.
ms.prod: xamarin
ms.assetid: E0305572-DC64-48BB-BD97-0A5096E6CA04
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 4dddde8d4408df6a9b9d73c0a3efff62f563591a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030776"
---
# <a name="playing-sound-in-tvos-with-avaudioplayer-in-xamarin"></a>Émettre du son dans tvOS avec AVAudioPlayer dans Xamarin

## <a name="about-the-avaudioplayer"></a>À propos de AVAudioPlayer

Le `AVAudioPlayer` est utilisé pour lire les données audio à partir de la mémoire ou d’un fichier. Apple recommande d’utiliser cette classe pour lire de l’audio dans votre application, sauf si vous effectuez une diffusion réseau ou si vous avez besoin d’e/s audio à faible latence.

Vous pouvez utiliser la `AVAudioPlayer` pour effectuer les opérations suivantes :

- Lire des sons de n’importe quelle durée avec boucle facultative.
- Lisez plusieurs sons en même temps avec la synchronisation facultative.
- Contrôler le volume, la vitesse de lecture et le positionnement stéréo pour chaque émission de sons.
- Prise en charge des fonctionnalités telles que l’avance rapide ou le rembobinage.
- Obtenir des données de contrôle du niveau de lecture.

`AVAudioPlayer` prend en charge les sons au format audio fourni par iOS, tvOS et OS X, comme `.aif`, `.wav` ou `.mp3`.

## <a name="playing-sounds-in-tvos"></a>Émettre des sons dans tvOS

Étant donné que tvOS prend en charge les mêmes classes de boîte à outils audio que iOS, consultez notre fichier iOS [Play sound with AVAudioPlayer](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/sound/avaudioplayer) pour obtenir des détails complets sur la lecture de données audio dans une application Xamarin. tvOS.

## <a name="related-links"></a>Liens associés

- [Référence AVAudioPlayer](https://developer.apple.com/library/ios/documentation/AVFoundation/Reference/AVAudioPlayerClassReference/)
