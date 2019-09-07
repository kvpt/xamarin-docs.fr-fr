---
title: 'Erreur d’exécution : l’assembly mscorlib.dll est introuvable ou ne peut pas être chargé'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 1027E16C-2C14-4BB5-AAAB-342F3E28E22E
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: a1c6c5357c62d0421376930ea04c4755fae59d04
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769355"
---
# <a name="runtime-error-the-assembly-mscorlibdll-was-not-found-or-could-not-be-loaded"></a>Erreur d’exécution : l’assembly mscorlib.dll est introuvable ou ne peut pas être chargé

```
<Warning>: The assembly mscorlib.dll was not found or could not be loaded.
<Warning>: It should have been installed in the `/Developer/MonoTouch/Source/monotouch/builds/install/target64/lib/mono/2.0/mscorlib.dll' directory.
<Warning>: Service exited with abnormal code: 1
```

Ce problème se produit lorsque les dossiers `.monotouch-64` *masqués* `.monotouch-32` et sont absents du pour la `.xcarchive` création de la signature/la disponibilité, déclenchant l’erreur d’exécution.
