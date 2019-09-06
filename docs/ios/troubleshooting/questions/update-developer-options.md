---
title: Comment puis-je réactiver les options de développement après la mise à jour d’iOS ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F38BD21E-0C21-43FF-80A6-BB4A88DB88A5
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: 18651485ad53ca5493a0a47368608214e5f7c595
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292075"
---
# <a name="how-can-i-reenable-developer-options-after-updating-ios"></a>Comment puis-je réactiver les options de développement après la mise à jour d’iOS ?

Un bogue iOS peut entraîner la disparition des options de développement après la mise à jour des versions d’iOS, ce qui a été observé lors du passage à iOS 8. x. Ces options peuvent être réactivées en procédant comme suit :

1. Connectez l’appareil iOS à votre ordinateur de développement Mac.
2. Démarrez Xcode.
3. Ouvrez **windows > appareils** & Sélectionnez votre appareil.
4. Attendez que Xcode termine de copier les symboles, puis vérifiez les paramètres de l’appareil. app pour les paramètres du développeur.
5. Redémarrez l’appareil.
