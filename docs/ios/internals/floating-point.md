---
title: Opérations à virgule flottante dans Xamarin. iOS
description: Ce document décrit comment Xamarin. iOS gère les opérations à virgule flottante de précision 32 bits et 64 bits et décrit les impacts associés aux performances.
ms.prod: xamarin
ms.assetid: 003F25C1-B430-4339-9C95-7DF527EBC699
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 11/25/2015
ms.openlocfilehash: 1ecb00fecaf14afb8c6d5c59297eb26821ed791a
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291927"
---
# <a name="floating-point-operations-in-xamarinios"></a>Opérations à virgule flottante dans Xamarin. iOS

Xamarin. iOS effectue par défaut des opérations à virgule flottante 32 bits et 64 bits à l’aide de la précision 64 bits sur ARM.  

Bien que cette précision plus proche de ce que les développeurs attendent des opérations à C# virgule flottante dans sur le bureau, sur les appareils mobiles, l’impact sur les performances peut être significatif.

Il est possible de compiler votre code à virgule flottante 32 bits pour utiliser des opérations à virgule flottante 32 bits.  Pour ce faire, vous devez utiliser au moins Xamarin. iOS 8,10 et définir dans le panneau des options de build iOS sur la ligne d’entrée « arguments supplémentaires mTouch » la valeur suivante :

```
--aot-options=-O=float32
```

Cela permet d’informer les compilateurs statiques (compilateur statique intégré à mono ou LLVM) à effectuer des opérations à virgule flottante à l’aide de valeurs float 32 bits.
