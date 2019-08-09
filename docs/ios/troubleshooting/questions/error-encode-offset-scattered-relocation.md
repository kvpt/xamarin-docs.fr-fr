---
title: 'Erreur de compilation: Impossible d’encoder l’offset X dans le réadressage éparpillé résultant'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 84158C42-FE79-415A-A44A-D48C9E5E6760
ms.technology: xamarin-ios
author: chamons
ms.author: chhamo
ms.date: 08/07/2019
ms.openlocfilehash: df974649c8c9f1d1fb4c13d6d801eb0f6a3d1e77
ms.sourcegitcommit: 2e5a6b8bcd1a073b54604f51538fd108e1c2a8e5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68876228"
---
# <a name="compile-error-can-not-encode-offset-x-in-resulting-scattered-relocation"></a>Erreur de compilation: Impossible d’encoder l’offset X dans le réadressage éparpillé résultant

```
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Xamarin\iOS\Xamarin.iOS.Common.targets(804,3): error GDC116A36: can not encode offset ‘0x1155504’ in resulting scattered relocation.
1> .long mono_aot_Xamarin_iOS_got - . + 12 (TaskId:141)
1> ^ (TaskId:141)
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Xamarin\iOS\Xamarin.iOS.Common.targets(804,3): error GDC116A36: can not encode offset ‘0x11555B8’ in resulting scattered relocation.
1> .long mono_aot_Xamarin_iOS_got - . + 16 (TaskId:141)
```

Ce problème se produit lors de la génération d’architectures 32 bits, telles que ARMv7, lorsque le fichier binaire final est trop grand pour le chaîne d’outils natif.

Pour résoudre ce qui suit:

- Déplacez la génération pour cette architecture dans le volet Build iOS des options du projet.
- Activer la liaison ou supprimer manuellement le code pour réduire la taille finale de l’exécutable
