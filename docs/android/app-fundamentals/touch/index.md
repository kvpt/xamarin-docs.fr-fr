---
title: Toucher et gestes dans Xamarin. Android
description: Les écrans tactiles sur la plupart des appareils actuels permettent aux utilisateurs d’interagir rapidement et efficacement avec les appareils de manière naturelle et intuitive. Cette interaction n’est pas limitée uniquement à la détection tactile simple. il est également possible d’utiliser des mouvements. Par exemple, le geste de pincement de zoom est un exemple très courant en dépinceant une partie de l’écran avec deux doigts. l’utilisateur peut effectuer un zoom avant ou arrière. Ce guide examine les entrées tactiles et les gestes dans Android.
ms.prod: xamarin
ms.assetid: 61874769-978A-4562-9B2A-7FFD45F58B38
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 0b11f5c290f07debb61d511bdd27ad1a7ad0b0b0
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69526082"
---
# <a name="touch-and-gestures-in-xamarinandroid"></a>Toucher et gestes dans Xamarin. Android

_Les écrans tactiles sur la plupart des appareils actuels permettent aux utilisateurs d’interagir rapidement et efficacement avec les appareils de manière naturelle et intuitive. Cette interaction n’est pas limitée uniquement à la détection tactile simple. il est également possible d’utiliser des mouvements. Par exemple, le geste de pincement de zoom est un exemple très courant en dépinceant une partie de l’écran avec deux doigts. l’utilisateur peut effectuer un zoom avant ou arrière. Ce guide examine les entrées tactiles et les gestes dans Android._

## <a name="touch-overview"></a>Vue d’ensemble tactile

iOS et Android sont similaires dans la manière dont ils gèrent les fonctions tactiles. Les deux peuvent prendre en charge des points de contact multipoint à plusieurs touches sur les gestes d’écran et complexes. Ce guide présente certaines des similarités des concepts, ainsi que les particularités de l’implémentation des fonctions tactiles et des gestes sur les deux plateformes.

Android utilise un `MotionEvent` objet pour encapsuler les données tactiles et les méthodes sur l’objet de vue pour écouter les touches.

En plus de capturer les données tactiles, iOS et Android offrent à la fois des moyens d’interpréter les modèles de touches en gestes. Ces détecteurs de mouvement peuvent à leur tour être utilisés pour interpréter des commandes spécifiques à l’application, telles que la rotation d’une image ou l’activation d’une page. Android fournit une poignée de mouvements pris en charge, ainsi que des ressources pour faciliter l’ajout de gestes personnalisés complexes.

Que vous travailliez sur Android ou iOS, le choix entre les touches de contact et les reconnaissances de mouvement peut être une opération confuse. Ce guide recommande que, en général, la préférence soit donnée aux détecteurs de mouvement. Les module de reconnaissance de mouvement sont implémentés en tant que classes discrètes, ce qui permet une plus grande séparation des préoccupations et une meilleure encapsulation. Cela facilite le partage de la logique entre différentes vues, ce qui réduit la quantité de code écrite.

Ce guide suit un format similaire pour chaque système d’exploitation: tout d’abord, les API tactiles de la plateforme sont introduites et expliquées, car il s’agit de la base sur laquelle les interactions tactiles sont créées. Ensuite, nous plongeons dans le monde des détecteurs de mouvement: tout d’abord en explorant certains mouvements courants et en terminant par la création de mouvements personnalisés pour les applications. Enfin, vous verrez comment suivre des doigts individuels à l’aide d’un suivi tactile de bas niveau pour créer un programme de peinture par doigt.

## <a name="sections"></a>Sections

- [Entrées tactiles dans Android](~/android/app-fundamentals/touch/android-touch-walkthrough.md)
- [Procédure pas à pas : Utilisation de Touch dans Android](~/android/app-fundamentals/touch/android-touch-walkthrough.md)
- [Suivi tactile multipoint](touch-tracking.md)

## <a name="summary"></a>Récapitulatif

Dans ce guide, nous avons examiné Touch dans Android. Pour les deux systèmes d’exploitation, nous avons appris à activer touch et à répondre aux événements tactiles. Ensuite, nous avons appris les gestes et certains des détecteurs de mouvement fournis par Android et iOS pour gérer certains des scénarios les plus courants. Nous avons examiné comment créer des mouvements personnalisés et les implémenter dans des applications. Une procédure pas à pas A montré les concepts et les API pour chaque système d’exploitation en action, et vous avez également vu comment suivre des doigts individuels.



## <a name="related-links"></a>Liens associés

- [Android touch Start (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-touch-start)
- [Android Touch (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-touch-final)
- [FingerPaint (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint)
