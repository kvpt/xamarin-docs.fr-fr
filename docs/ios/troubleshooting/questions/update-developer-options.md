---
title: Comment puis-je réactiver les options de développement après la mise à jour d’iOS ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F38BD21E-0C21-43FF-80A6-BB4A88DB88A5
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: e9ba068e9d68b6b8d6e90e927ec0252dd6e7637f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030907"
---
# <a name="how-can-i-reenable-developer-options-after-updating-ios"></a>Comment puis-je réactiver les options de développement après la mise à jour d’iOS ?

Un bogue iOS peut entraîner la disparition des options de développement après la mise à jour des versions d’iOS, ce qui a été observé lors du passage à iOS 8. x. Ces options peuvent être réactivées en procédant comme suit :

1. Connectez l’appareil iOS à votre ordinateur de développement Mac.
2. Démarrez Xcode.
3. Ouvrez **windows > appareils** & Sélectionnez votre appareil.
4. Attendez que Xcode termine de copier les symboles, puis vérifiez les paramètres de l’appareil. app pour les paramètres du développeur.
5. Redémarrez l’appareil.
