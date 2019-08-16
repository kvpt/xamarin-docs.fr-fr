---
title: Opérations à virgule flottante dans Xamarin. iOS
description: Ce document décrit comment Xamarin. iOS gère les opérations à virgule flottante de précision 32 bits et 64 bits et décrit les impacts associés aux performances.
ms.prod: xamarin
ms.assetid: 003F25C1-B430-4339-9C95-7DF527EBC699
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 11/25/2015
ms.openlocfilehash: cd1bd0507f89f7b29bfcd3ef1ba0a3b1215632ce
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69527373"
---
# <a name="floating-point-operations-in-xamarinios"></a>Opérations à virgule flottante dans Xamarin. iOS

Xamarin. iOS effectue par défaut des opérations à virgule flottante 32 bits et 64 bits à l’aide de la précision 64 bits sur ARM.  

Bien que cette précision plus proche de ce que les développeurs attendent des opérations à C# virgule flottante dans sur le bureau, sur les appareils mobiles, l’impact sur les performances peut être significatif.

Il est possible de compiler votre code à virgule flottante 32 bits pour utiliser des opérations à virgule flottante 32 bits.  Pour ce faire, vous devez utiliser au moins Xamarin. iOS 8,10 et définir dans le panneau des options de build iOS sur la ligne d’entrée «arguments supplémentaires mTouch» la valeur suivante:

```
--aot-options=-O=float32
```

Cela permet d’informer les compilateurs statiques (compilateur statique intégré à mono ou LLVM) à effectuer des opérations à virgule flottante à l’aide de valeurs float 32 bits.
