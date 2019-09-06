---
title: Émettre du son avec AVAudioPlayer dans Xamarin. Mac
description: Ce document explique comment lire du son avec AVAudioPlayer dans une application Xamarin. Mac. Il aborde AVAudioPlayer à un niveau élevé et fournit des liens vers d’autres documents qui l’explorent plus en détail.
ms.prod: xamarin
ms.assetid: 4A683A94-F75D-4EAF-8497-E9443653250B
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 10/19/2016
ms.openlocfilehash: b4a5ead3e3c02fbdd2ae5486a6ac637defeb5abd
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70283301"
---
# <a name="playing-sound-with-avaudioplayer-in-xamarinmac"></a>Émettre du son avec AVAudioPlayer dans Xamarin. Mac

## <a name="about-the-avaudioplayer"></a>À propos de AVAudioPlayer

La `AVAudioPlayer` classe permet de lire des données audio à partir de la mémoire ou d’un fichier. Apple recommande d’utiliser cette classe pour lire de l’audio dans votre application, sauf si vous effectuez une diffusion réseau ou si vous avez besoin d’e/s audio à faible latence.

Vous pouvez utiliser la `AVAudioPlayer` classe pour effectuer les opérations suivantes :

- Lire des sons de n’importe quelle durée avec boucle facultative.
- Lisez plusieurs sons en même temps avec la synchronisation facultative.
- Contrôler le volume, la vitesse de lecture et le positionnement stéréo pour chaque émission de sons.
- Prise en charge des fonctionnalités telles que l’avance rapide ou le rembobinage.
- Obtenir des données de contrôle du niveau de lecture.

`AVAudioPlayer`prend en charge les sons dans tout format audio fourni par iOS, tvOS et macOS, tels que. AIF,. wav ou. mp3.

## <a name="playing-sounds-in-macos"></a>Émettre des sons dans macOS

Étant donné que macOS prend en charge les mêmes classes de boîte à outils audio que iOS, consultez notre fichier iOS [Play sound with AVAudioPlayer](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/sound/avaudioplayer) pour obtenir des détails complets sur la lecture de données audio dans une application Xamarin. Mac.

## <a name="related-links"></a>Liens associés

- [Référence AVAudioPlayer](https://developer.apple.com/documentation/avfoundation/avaudioplayer)
