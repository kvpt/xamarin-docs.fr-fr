---
title: Émettre du son dans tvOS avec AVAudioPlayer dans Xamarin
description: Cet article explique comment utiliser une classe d’assistance pour contrôler la lecture du son à l’aide d’un AVAudioPlayer dans une application Xamarin. iOS.
ms.prod: xamarin
ms.assetid: E0305572-DC64-48BB-BD97-0A5096E6CA04
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: 57892689eeb5eef9747e19fa167b8598569f3cd1
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769209"
---
# <a name="playing-sound-in-tvos-with-avaudioplayer-in-xamarin"></a>Émettre du son dans tvOS avec AVAudioPlayer dans Xamarin

## <a name="about-the-avaudioplayer"></a>À propos de AVAudioPlayer

Le `AVAudioPlayer` est utilisé pour lire les données audio à partir de la mémoire ou d’un fichier. Apple recommande d’utiliser cette classe pour lire de l’audio dans votre application, sauf si vous effectuez une diffusion réseau ou si vous avez besoin d’e/s audio à faible latence.

Vous pouvez utiliser le `AVAudioPlayer` pour effectuer les opérations suivantes :

- Lire des sons de n’importe quelle durée avec boucle facultative.
- Lisez plusieurs sons en même temps avec la synchronisation facultative.
- Contrôler le volume, la vitesse de lecture et le positionnement stéréo pour chaque émission de sons.
- Prise en charge des fonctionnalités telles que l’avance rapide ou le rembobinage.
- Obtenir des données de contrôle du niveau de lecture.

`AVAudioPlayer`prend en charge les sons dans n’importe quel format audio fourni par iOS, tvOS `.aif`et `.wav` OS `.mp3`X, tels que, ou.

## <a name="playing-sounds-in-tvos"></a>Émettre des sons dans tvOS

Étant donné que tvOS prend en charge les mêmes classes de boîte à outils audio que iOS, consultez notre fichier iOS [Play sound with AVAudioPlayer](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/sound/avaudioplayer) pour obtenir des détails complets sur la lecture de données audio dans une application Xamarin. tvOS.

## <a name="related-links"></a>Liens associés

- [Référence AVAudioPlayer](https://developer.apple.com/library/ios/documentation/AVFoundation/Reference/AVAudioPlayerClassReference/)
