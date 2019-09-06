---
title: Création d’interfaces utilisateur à l’aide du concepteur iOS
description: Ce document explique comment utiliser l’Xamarin Designer pour iOS pour créer l’interface utilisateur d’une application avec des storyboards et des fichiers. XIB. Il contient des liens vers des documents qui traitent de la disponibilité de l’outil, de ses fonctionnalités de base, de ses contrôles concevables et fournissent des procédures pas à pas de son utilisation.
ms.prod: xamarin
ms.assetid: E35EFB69-EBBA-40E3-ADBE-CB8016F17127
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 05/31/2018
ms.openlocfilehash: 577c5602c1cbc331564c3034b3f0c11a4b97bc0c
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279842"
---
# <a name="building-user-interfaces-with-the-ios-designer"></a>Création d’interfaces utilisateur à l’aide du concepteur iOS

_Le Xamarin Designer pour iOS est un concepteur visuel pour les formats de Storyboard et de Interface Builder iOS entièrement intégrés à Visual Studio pour Mac et Visual Studio. Le concepteur iOS assure une compatibilité complète avec les formats de Storyboard et. XIB, afin que les fichiers puissent être modifiés dans Visual Studio pour Mac ou Visual Studio en plus de l’Interface Builder de Xcode. En outre, le Xamarin Designer pour iOS prend en charge des fonctionnalités avancées telles que les contrôles personnalisés qui s’affichent au moment du design dans l’éditeur._

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![Concepteur iOS dans Visual Studio pour Mac](images/designer-vsmac-sml.png "Le concepteur iOS")](images/designer-vsmac.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Concepteur iOS dans Visual Studio](images/designer-vs.png "Le concepteur iOS")](images/designer-vs.png#lightbox)

-----

## <a name="availability"></a>Disponibilité

Le Xamarin Designer pour iOS est disponible dans Visual Studio pour Mac et dans Visual Studio 2017 sur Windows.

Ces guides partent du principe que vous êtes familiarisé avec le contenu décrit dans les [guides de prise en main Xamarin. iOS](~/ios/get-started/index.md).

## <a name="ios-designer-basicsintroductionmd"></a>[Principes de base du concepteur iOS](introduction.md)

Ce guide couvre les fonctionnalités du concepteur Xamarin iOS. Il aborde les principes fondamentaux du concepteur, en expliquant comment utiliser le concepteur pour disposer les contrôles visuellement et comment modifier des propriétés.

## <a name="designable-controls-overviewios-designable-controls-overviewmd"></a>[Vue d’ensemble des contrôles concevables](ios-designable-controls-overview.md)

Ce guide se penche sur les contrôles personnalisés, sur la façon dont ils sont créés et sur les exigences qu’ils doivent respecter pour être rendu sur l’aire de conception. En outre, il montre comment déboguer les problèmes courants qui peuvent se produire lors de l’utilisation de contrôles concevables.

## <a name="walkthrough---using-custom-controls-with-ios-designerios-designable-controls-walkthroughmd"></a>[Procédure pas à pas : utilisation de contrôles personnalisés avec le concepteur iOS](ios-designable-controls-walkthrough.md)

Cet article fournit une procédure pas à pas qui montre comment créer un contrôle personnalisé et l’utiliser dans le concepteur iOS. Il montre comment rendre un contrôle disponible dans la boîte à outils du concepteur afin qu’il puisse être déplacé sur une vue. En outre, il montre comment implémenter un contrôle pour qu’il s’affiche correctement au moment de la conception et de l’exécution, et comment créer des propriétés qui peuvent être définies au moment de la conception.

## <a name="auto-layout-with-the-xamarin-ios-designerdesigner-auto-layoutmd"></a>[Disposition automatique avec le concepteur iOS Xamarin](designer-auto-layout.md)

Ce guide présente la disposition automatique iOS et le flux de travail des nouvelles contraintes disponibles dans le concepteur iOS.
