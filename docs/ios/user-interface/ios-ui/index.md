---
title: Interfaces utilisateur dans iOS
description: Ce document contient des liens vers des guides qui décrivent comment créer des interfaces utilisateur dans l’application Xamarin. iOS. Les repères liés couvrent l’API d’apparence, la création d’objets d’interface utilisateur, les options de disposition et bien plus encore.
ms.prod: xamarin
ms.assetid: 1BB46561-F503-491E-A27C-7878E7EBE00B
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/14/2017
ms.openlocfilehash: 325898b3c934e25ae1610a3437f787476dcd22cb
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73003339"
---
# <a name="user-interfaces-in-ios"></a>Interfaces utilisateur dans iOS

## <a name="appearance-apiintroduction-to-the-appearance-apimd"></a>[API d’apparence](introduction-to-the-appearance-api.md)

iOS permet à de nombreux attributs visuels des contrôles de l’interface utilisateur d’être à thème à l’aide des API UIAppearance.

## <a name="creating-user-interface-objectsiosuser-interfaceios-uicreating-ui-objectsmd"></a>[Création des objets de l’interface utilisateur](~/ios/user-interface/ios-ui/creating-ui-objects.md)

Apple regroupe des fonctionnalités connexes dans des « frameworks » qui correspondent à des espaces de noms Xamarin. iOS. `UIKit` est l’espace de noms qui contient tous les contrôles d’interface utilisateur pour iOS.

## <a name="layout-optionsiosuser-interfaceios-uilayout-optionsmd"></a>[Options de disposition](~/ios/user-interface/ios-ui/layout-options.md)

Il existe deux mécanismes différents pour contrôler la disposition lorsqu’une vue est redimensionnée ou pivotée : redimensionnement automatique et mise en forme automatique.

## <a name="providing-haptic-feedbackiosuser-interfaceios-uihaptic-feedbackmd"></a>[Activation du retour haptique](~/ios/user-interface/ios-ui/haptic-feedback.md)

Cet article aborde les nouveaux types de commentaires haptique disponibles dans iOS 10 et comment les implémenter dans Xamarin. iOS.

## <a name="working-with-the-ui-threadiosuser-interfaceios-uiui-threadmd"></a>[Utilisation du thread d’interface utilisateur](~/ios/user-interface/ios-ui/ui-thread.md)

Votre code doit uniquement apporter des modifications aux contrôles de l’interface utilisateur à partir du thread principal (ou de l’interface utilisateur). Les mises à jour de l’interface utilisateur qui se produisent sur un thread différent (par exemple, un rappel ou un thread d’arrière-plan) peuvent ne pas être rendues à l’écran ou peuvent même provoquer un blocage.
