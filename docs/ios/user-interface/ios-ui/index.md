---
title: Interfaces utilisateur dans iOS
description: Ce document contient des liens vers des guides qui décrivent comment créer des interfaces utilisateur dans l’application Xamarin. iOS. Les repères liés couvrent l’API d’apparence, la création d’objets d’interface utilisateur, les options de disposition et bien plus encore.
ms.prod: xamarin
ms.assetid: 1BB46561-F503-491E-A27C-7878E7EBE00B
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/14/2017
ms.openlocfilehash: 954e3b8f612fd710dd178cfc296889c9da372183
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768308"
---
# <a name="user-interfaces-in-ios"></a>Interfaces utilisateur dans iOS

## <a name="appearance-apiintroduction-to-the-appearance-apimd"></a>[API d’apparence](introduction-to-the-appearance-api.md)

iOS permet à de nombreux attributs visuels des contrôles de l’interface utilisateur d’être à thème à l’aide des API UIAppearance.

## <a name="creating-user-interface-objectsiosuser-interfaceios-uicreating-ui-objectsmd"></a>[Création des objets de l’interface utilisateur](~/ios/user-interface/ios-ui/creating-ui-objects.md)

Apple regroupe des fonctionnalités connexes dans des « frameworks » qui correspondent à des espaces de noms Xamarin. iOS. `UIKit`est l’espace de noms qui contient tous les contrôles d’interface utilisateur pour iOS.

## <a name="layout-optionsiosuser-interfaceios-uilayout-optionsmd"></a>[Options de disposition](~/ios/user-interface/ios-ui/layout-options.md)

Il existe deux mécanismes différents pour contrôler la disposition lorsqu’une vue est redimensionnée ou pivotée : Redimensionnement automatique et mise en page automatique.

## <a name="providing-haptic-feedbackiosuser-interfaceios-uihaptic-feedbackmd"></a>[Activation du retour haptique](~/ios/user-interface/ios-ui/haptic-feedback.md)

Cet article aborde les nouveaux types de commentaires haptique disponibles dans iOS 10 et comment les implémenter dans Xamarin. iOS.

## <a name="working-with-the-ui-threadiosuser-interfaceios-uiui-threadmd"></a>[Utilisation du thread d’interface utilisateur](~/ios/user-interface/ios-ui/ui-thread.md)

Votre code doit uniquement apporter des modifications aux contrôles de l’interface utilisateur à partir du thread principal (ou de l’interface utilisateur). Les mises à jour de l’interface utilisateur qui se produisent sur un thread différent (par exemple, un rappel ou un thread d’arrière-plan) peuvent ne pas être rendues à l’écran ou peuvent même provoquer un blocage.
