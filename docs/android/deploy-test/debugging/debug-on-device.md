---
title: Déboguer sur un appareil
description: Cet article explique comment déboguer une application Xamarin.Android sur un appareil Android physique.
ms.prod: xamarin
ms.assetid: 153D3746-A27F-198B-48FE-D219C0133A79
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: e2542a95ce7b62a017161da4fe206b83202c9085
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70754144"
---
# <a name="debug-on-device"></a>Déboguer sur un appareil

_Cet article explique comment déboguer une application Xamarin.Android sur un appareil Android physique._

## <a name="debug-on-device-overview"></a>Vue d’ensemble du débogage sur un appareil

Il est possible de déboguer une application Xamarin.Android sur un appareil Android à l’aide de Visual Studio pour Mac ou de Visual Studio. Avant de pouvoir déboguer une application sur un appareil, celui-ci doit être [configuré pour le développement](~/android/get-started/installation/set-up-device-for-development.md) et connecté à votre PC ou Mac.

## <a name="debug-application"></a>Déboguer l’application

Une fois qu’un appareil est connecté à votre ordinateur, le débogage d’une application Xamarin.Android est réalisé de la même façon que pour tout autre produit Xamarin ou application .NET. Vérifiez que la configuration **Debug** et l’appareil externe sont sélectionnés dans l’IDE. Les symboles de débogage nécessaires seront ainsi disponibles et l’IDE pourra se connecter à l’application en cours d’exécution : 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Configuration Debug sélectionnée](debug-on-device-images/image1-vs.png)

Ensuite, un point d'arrêt est défini dans le code :

![Point d’arrêt défini à la ligne de code](debug-on-device-images/image2-vs.png)

Une fois l’appareil sélectionné, Xamarin.Android se connecte à celui-ci, déploie l’application et l’exécute. Lorsque le point d’arrêt est atteint, le débogueur arrête l’application, ce qui permet de la déboguer de la même manière que toute autre application C# : 

![Point d’arrêt atteint](debug-on-device-images/image3-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

![Configuration Debug sélectionnée](debug-on-device-images/image1-xs.png)

Ensuite, un point d'arrêt est défini dans le code :

![Point d’arrêt défini à la ligne de code](debug-on-device-images/image2-xs.png)

Une fois l’appareil sélectionné, Xamarin.Android se connecte à celui-ci, déploie l’application et l’exécute. Lorsque le point d’arrêt est atteint, le débogueur arrête l’application, ce qui permet de la déboguer de la même manière que toute autre application C# : 

![Point d’arrêt atteint](debug-on-device-images/image3-xs.png)

-----

## <a name="summary"></a>Récapitulatif

Dans ce document, vous avez appris à déboguer une application Xamarin.Android en définissant un point d’arrêt et en sélectionnant l’appareil cible.

## <a name="related-links"></a>Liens associés

- [Configuration de l’appareil pour le développement](~/android/get-started/installation/set-up-device-for-development.md)
- [Définir l’attribut Debuggable](~/android/deploy-test/debuggable-attribute.md)
