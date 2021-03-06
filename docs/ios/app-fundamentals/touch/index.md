---
title: Gestion des fonctions tactiles dans les applications Xamarin. iOS
description: Ce document contient des liens vers des guides qui décrivent comment utiliser les fonctions tactiles tactiles, tactiles tactiles, les gestes tactiles et la fonction tactile 3D dans une application Xamarin. iOS.
ms.prod: xamarin
ms.assetid: E3904713-6018-4755-A315-EB045DFB3500
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 01/23/2017
ms.openlocfilehash: db3e66920beb355e0b05df2118cd2645c602f0d5
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433918"
---
# <a name="handling-touch-in-xamarinios-apps"></a>Gestion des fonctions tactiles dans les applications Xamarin. iOS

Comme les autres plateformes mobiles, iOS offre plusieurs moyens de gérer les fonctions tactiles. Il peut prendre en charge la fonctionnalité multipoint (plusieurs points de contact sur l’écran) et des mouvements complexes. Ce guide présente certains des concepts, ainsi que les particularités de l’implémentation des fonctions tactiles et des gestes sur iOS.

iOS encapsule des données tactiles dans la `UITouch` classe, qui sont mises à la disposition des applications par le biais d’une série de `UIResponder` méthodes. Les applications peuvent substituer ces méthodes dans les sous-classes de `UIView` et `UIViewController` , qui héritent toutes les deux de `UIResponder` .

En plus de capturer des données tactiles, iOS offre des moyens d’interpréter les séquences de touches en gestes. Ces détecteurs de mouvement peuvent à leur tour être utilisés pour interpréter des commandes spécifiques à l’application, telles que la rotation d’une image ou l’activation d’une page. iOS fournit une collection complète de classes pour gérer les gestes courants avec un code ajouté minimal.

Le choix entre les fonctions tactiles et les détecteurs de mouvement peut être une opération confuse. Ce guide recommande que, en général, la préférence soit donnée aux détecteurs de mouvement. Les module de reconnaissance de mouvement sont implémentés en tant que classes discrètes, ce qui permet une plus grande séparation des préoccupations et une meilleure encapsulation. Cela permet de partager facilement la logique entre différentes vues, ce qui réduit la quantité de code écrite.

Toutefois, il peut arriver que vous deviez utiliser le traitement tactile de bas niveau et même suivre plusieurs doigts, par exemple pour créer un programme de peinture par doigt.

## <a name="sections"></a>Sections

- [Touch dans iOS](touch-in-ios.md)
- [Procédure pas à pas : Utilisation de Touch dans iOS](ios-touch-walkthrough.md)
- [Suivi tactile multipoint](touch-tracking.md)

Ce guide fait office d’introduction à Touch dans iOS. Pour plus d’informations sur l’utilisation de la fonction tactile 3D et des commentaires haptique dans iOS, qui ont été introduits dans iOS 9 et 10, consultez les guides spécifiques ci-dessous :

- [3D Touch](~/ios/platform/3d-touch.md)
- [Activation du retour haptique](~/ios/user-interface/ios-ui/haptic-feedback.md)

## <a name="related-links"></a>Liens associés

- [iOS Touch final (exemple)](/samples/xamarin/ios-samples/applicationfundamentals-touch-final)
- [FingerPaint (exemple)](/samples/xamarin/ios-samples/applicationfundamentals-fingerpaint)