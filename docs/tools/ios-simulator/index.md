---
title: Remoted iOS Simulator pour Windows
description: Le simulateur iOS distant pour Windows vous permet de tester vos applications sur un simulateur iOS affiché dans Windows à côté de Visual Studio 2019.
ms.prod: xamarin
ms.assetid: 63c50190-7e54-4140-a30d-1a0e577c47d7
author: conceptdev
ms.author: crdun
ms.date: 04/26/2019
ms.openlocfilehash: 3067493315c62c46f44fb94aa61a919e1449080b
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70289707"
---
# <a name="remoted-ios-simulator-for-windows"></a>Remoted iOS Simulator pour Windows

Le simulateur iOS distant pour Windows vous permet de tester vos applications sur un simulateur iOS affiché dans Windows en même temps que Visual Studio 2019 et Visual Studio 2017.

[![simulateur iOS exécuté sur Windows](images/hero-sml.png "simulateur iOS exécuté sur Windows")](images/hero.png#lightbox)

## <a name="getting-started"></a>Prise en main

Le simulateur iOS distant pour Windows est installé automatiquement dans le cadre de Xamarin dans Visual Studio 2019 et Visual Studio 2017. Pour l’utiliser, procédez comme suit :

1. [Couplez le Visual 2019 à un hôte de build Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).
2. Dans Visual Studio, démarrez le débogage d’un projet iOS ou tvOS. Le simulateur iOS distant pour Windows s’affiche sur votre ordinateur Windows.

Regardez [cette vidéo](deploy.md) pour un guide pas à pas.

## <a name="simulator-window"></a>Fenêtre du simulateur

La barre d’outils en haut de la fenêtre du simulateur contient un certain nombre de boutons utiles :

- **Page d’hébergement** : simule le bouton de démarrage sur un appareil iOS.
- **Lock** : verrouille le simulateur (balayer pour déverrouiller).
- **Capture** d’écran : enregistre une capture d’écran du simulateur (stocké dans **Pictures\Xamarin\iOS Simulator\\** ).
- [**Paramètres**](#settings) : affiche le clavier, l’emplacement et d’autres paramètres.
- [**Autres options**](#other-options) : affiche diverses options du simulateur, telles que la rotation, les gestes de tremblement et l’id tactile.

    [![exemple de Maps de simulateur iOS](images/maps-app-sml.png "exemple de Maps de simulateur iOS")](images/maps-app.png#lightbox)

## <a name="settings"></a>Paramètres

Cliquez sur l’icône d’engrenage de la barre d’outils pour ouvrir la fenêtre des **paramètres** :

[![paramètres du simulateur iOS](images/settings-sml.png "paramètres du simulateur iOS")](images/settings.png#lightbox)

Ces paramètres vous permettent d’activer le clavier matériel, de choisir un emplacement que l’appareil doit signaler (les emplacements statiques et mobiles sont tous deux pris en charge), d’activer Touch ID et de réinitialiser le contenu et les paramètres du simulateur.

## <a name="other-options"></a>Autres options

Le bouton de sélection de la barre d’outils révèle d’autres options telles que la rotation, les gestes de tremblement et le redémarrage. Ces mêmes options peuvent être affichées sous forme de liste en cliquant avec le bouton droit n’importe où dans la fenêtre du simulateur :

[![paramètres supplémentaires du simulateur iOS](images/more-sml.png "paramètres supplémentaires du simulateur iOS")](images/more.png#lightbox)

## <a name="touchscreen-support"></a>Prise en charge de l’écran tactile

La plupart des ordinateurs Windows modernes ont des écrans tactiles. Dans la mesure où le simulateur iOS distant pour Windows prend en charge les interactions tactiles, vous pouvez tester votre application avec les mêmes gestes de pincement, de balayage et de toucher à plusieurs doigts que vous utilisez avec des appareils iOS physiques.

De même, le simulateur iOS distant pour Windows traite l’entrée du stylet Windows comme une entrée Apple crayon.

## <a name="sound-handling"></a>Gestion des sons

Les sons joués par le simulateur proviennent des haut-parleurs Mac de l’hôte.
les sons iOS ne sont pas audibles sur l’ordinateur Windows.

## <a name="disabling-the-remoted-ios-simulator-for-windows"></a>Désactivation du simulateur iOS distant pour Windows

Pour désactiver le simulateur iOS distant pour Windows, accédez à **outils > Options > Xamarin > paramètres iOS** et décochez la case à cocher **simulateur distant vers Windows**.

[![case à cocher pour utiliser le simulateur](images/options-sml.png "case à cocher pour utiliser le simulateur")](images/options.png#lightbox)

Si cette option est désactivée, le débogage ouvre le simulateur iOS sur l’hôte de build Mac connecté.
