---
title: Remoted iOS Simulator pour Windows
description: Le simulateur Remoted iOS pour Windows vous permet de tester vos applications sur un simulateur iOS affiché dans Windows aux côtés de Visual Studio 2019.
ms.prod: xamarin
ms.assetid: 63c50190-7e54-4140-a30d-1a0e577c47d7
author: davidortinau
ms.author: daortin
ms.date: 04/26/2019
ms.openlocfilehash: d5898f9c6ee30eb1f12bf6480b93a609e762e6ea
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "75886591"
---
# <a name="remoted-ios-simulator-for-windows"></a>Remoted iOS Simulator pour Windows

Le simulateur Remoted iOS pour Windows vous permet de tester vos applications sur un simulateur iOS affiché dans Windows aux côtés de Visual Studio 2019 et Visual Studio 2017.

[![simulateur iOS fonctionnant sur Windows](images/hero-sml.png "simulateur iOS fonctionnant sur Windows")](images/hero.png#lightbox)

## <a name="getting-started"></a>Prise en main

Le simulateur Remoted iOS pour Windows est installé automatiquement dans le cadre de Xamarin dans Visual Studio 2019 et Visual Studio 2017. Pour l’utiliser, suivez ces étapes :

1. [Paire Visual 2019 à un hôte Mac Build](~/ios/get-started/installation/windows/connecting-to-mac/index.md).
2. Dans Visual Studio, commencez à débogage d’un projet iOS ou TVOS. Le simulateur iOS Remoted pour Windows apparaîtra sur votre machine Windows.

Regardez [cette vidéo](deploy.md) pour un guide étape par étape.

## <a name="simulator-window"></a>Fenêtre de simulateur

La barre d’outils en haut de la fenêtre du simulateur contient un certain nombre de boutons utiles :

- **Accueil** - Simule le bouton d’accueil d’un appareil iOS.
- **Verrouillage** - Verrouille le simulateur (glisser pour déverrouiller).
- **Capture d’écran** - Enregistre une capture d’écran du simulateur (stocké dans **le simulateur\\Pictures-Xamarin-iOS**).
- [**Paramètres**](#settings) - Affiche clavier, emplacement et autres paramètres.
- [**Autres options**](#other-options) - Apporte diverses options de simulateur tels que la rotation, les gestes de secousse, et Touch ID.

    [![iOS simulateur cartes exemple](images/maps-app-sml.png "iOS simulateur cartes exemple")](images/maps-app.png#lightbox)

## <a name="settings"></a>Paramètres

En cliquant sur l’icône de l’équipement de la barre d’outils, vous ez la fenêtre **Paramètres** :

[![Paramètres de simulateur iOS](images/settings-sml.png "Paramètres de simulateur iOS")](images/settings.png#lightbox)

Ces paramètres vous permettent d’activer le clavier matériel, de choisir un emplacement que l’appareil doit signaler (les emplacements statiques et mobiles sont tous deux pris en charge), activer Touch ID et réinitialiser le contenu et les paramètres du simulateur.

## <a name="other-options"></a>Autres options

Le bouton ellipsis de la barre d’outils révèle d’autres options telles que la rotation, les gestes de secousse et le redémarrage. Ces mêmes options peuvent être considérées comme une liste en cliquant à droite n’importe où dans la fenêtre du simulateur :

[![Paramètres supplémentaires du simulateur iOS](images/more-sml.png "Paramètres supplémentaires du simulateur iOS")](images/more.png#lightbox)

## <a name="touchscreen-support"></a>Support tactile

La plupart des ordinateurs Windows modernes ont des écrans tactiles. Étant donné que le simulateur iOS Remoted pour Windows prend en charge les interactions tactiles, vous pouvez tester votre application avec les mêmes gestes tactiles de pincement, de balayage et de plusieurs doigts que vous utilisez avec les appareils iOS physiques.

De même, le simulateur iOS Remoted pour Windows traite Windows Stylus entrée comme entrée Apple Pencil.

## <a name="sound-handling"></a>Manipulation sonore

Les sons joués par le simulateur proviendront des haut-parleurs de l’hôte Mac.
Les sons iOS ne sont pas entendus sur l’ordinateur Windows.

## <a name="disabling-the-remoted-ios-simulator-for-windows"></a>Désactiver le simulateur iOS à distance pour Windows

Pour désactiver le simulateur iOS à distance pour Windows, naviguez vers **Des outils > Options > Xamarin > paramètres iOS** et décocher un simulateur à distance à **Windows**.

[![boîte à cocher pour utiliser le simulateur](images/options-sml.png "boîte à cocher pour utiliser le simulateur")](images/options.png#lightbox)

Avec cette option désactivée, débogage ouvre le simulateur iOS sur l’hôte connecté de construction Mac.

## <a name="troubleshooting"></a>Dépannage

Si vous rencontrez des problèmes avec le simulateur iOS Remoted, vous pouvez afficher les journaux dans ces endroits :

- **Mac** -`~/Library/Logs/Xamarin/Simulator.Server`
- **Fenêtres**`%LOCALAPPDATA%\Xamarin\Logs\Xamarin.Simulator`

Si vous [signalez un problème dans Visual Studio](https://docs.microsoft.com/visualstudio/ide/how-to-report-a-problem-with-visual-studio), attacher ces journaux peut être utile (il ya des options pour garder les téléchargements privés).
