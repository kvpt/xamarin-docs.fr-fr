---
title: Installation de Xamarin.iOS sur un système Windows
description: Ce document décrit comment configurer un ordinateur Windows, configurer un hôte de build Mac et appairer Windows au Mac pour le développement Xamarin.iOS.
zone_pivot_groups: platform-win
ms.prod: xamarin
ms.assetid: abf85d3e-a365-44a2-b1a4-6c572c7f76dd
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/16/2018
ms.openlocfilehash: 53f244521e4b916078bdcbaa02f1a79a919be0c4
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291309"
---
# <a name="installing-xamarinios-on-windows"></a>Installation de Xamarin.iOS sur un système Windows

_Cet article décrit comment configurer un ordinateur Windows et un hôte de build Mac pour le développement Xamarin.iOS._

::: zone pivot="windows"

## <a name="overview"></a>Présentation

Pour générer des applications Xamarin.iOS avec Visual Studio 2019 sur Windows, vous avez besoin des éléments suivants :

- Machine Windows sur laquelle Visual Studio 2019 est installé. Il peut s’agir d’une machine virtuelle ou physique.

  - [Configuration requise pour Windows](~/cross-platform/get-started/requirements.md#windows-requirements)

- Un Mac accessible via le réseau configuré avec les outils de génération d’Apple et Xamarin.iOS. Visual Studio 2019 accède à cette machine par le biais d’une connexion réseau pour utiliser les outils de génération d’Apple, qui sont nécessaires pour compiler des applications iOS natives.

  - [Configuration requise pour Mac](~/cross-platform/get-started/requirements.md#macos-requirements)

  > [!TIP]
  > Vous n’avez pas accès à un Mac ?
  >
  > Si vous n’avez pas accès à un Mac, vous pouvez utiliser [MacinCloud](https://www.macincloud.com/pages/visual-studio-mac.html) ou [MacStadium](https://www.macstadium.com/) ; les deux services fournissent du matériel Mac basé sur le cloud que vous pouvez utiliser pour générer des projets Xamarin.iOS.

## <a name="setup"></a>Installation

Afin de préparer la configuration pour le développement Xamarin.iOS dans Visual Studio 2019, effectuez les étapes suivantes :

1. Configurer Windows (installer Visual Studio 2019)

    Xamarin.iOS fonctionne avec les éditions Visual Studio 2019 Community, Professional et Enterprise, sur une machine virtuelle ou autonome.

    - Installez [Visual Studio 2019](~/get-started/installation/windows.md).

2. Configurer Mac (installer Xcode et Visual Studio pour Mac)

    Pour générer, déboguer et signer des applications iOS à distribuer, Visual Studio 2017 doit avoir un accès réseau à un hôte de build Mac configuré avec à la fois les outils de développement d’Apple (Xcode) et Xamarin.iOS.

    - [Téléchargez et installez Xcode à partir de Mac App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12).
    - [Installez Visual Studio pour Mac](https://docs.microsoft.com/visualstudio/mac/installation), qui installe également Xamarin.iOS.

    > [!NOTE]
    > Si vous préférez ne pas installer Visual Studio pour Mac, Visual Studio 2019 peut [configurer automatiquement](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes#automatic-macos-provisioning) l’hôte de build Mac avec le logiciel nécessaire pour générer des applications Xamarin.iOS.
    > Pour plus d’informations, consultez [Provisionnement Mac automatique](~/ios/get-started/installation/windows/connecting-to-mac/index.md#automatic-mac-provisioning).

3. Appairer avec un Mac (connecter Visual Studio 2019 au Mac)

    Pour que Visual Studio 2019 utilise les outils de génération iOS sur le Mac, les deux machines doivent être connectées par le biais d’un réseau.

    - [Lisez le guide Appairer avec un Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

::: zone-end
::: zone pivot="win-vs2017"

## <a name="overview"></a>Présentation

Pour générer des applications Xamarin.iOS avec Visual Studio 2017 sur Windows, vous avez besoin des éléments suivants :

- Machine Windows sur laquelle Visual Studio 2017 est installé. Il peut s’agir d’une machine virtuelle ou physique.
  - [Configuration requise pour Windows](~/cross-platform/get-started/requirements.md#windows-requirements)

- Un Mac accessible via le réseau configuré avec les outils de génération d’Apple et Xamarin.iOS. Visual Studio 2017 accède à cette machine via une connexion réseau pour utiliser les outils de génération d’Apple, qui sont nécessaires pour compiler des applications iOS natives.
  - [Configuration requise pour Mac](~/cross-platform/get-started/requirements.md#macos-requirements)

## <a name="setup"></a>Installation

Afin de préparer la configuration pour le développement Xamarin.iOS dans Visual Studio 2017, effectuez les étapes suivantes :

1. Configurer Windows (installer Visual Studio 2017)

    Xamarin.iOS fonctionne avec les éditions Visual Studio 2017 Community, Professional et Enterprise, sur une machine virtuelle ou autonome.

    - [Installez Visual Studio 2017](~/get-started/installation/windows.md).

2. Configurer Mac (installer Xcode et Visual Studio pour Mac)

    Pour générer, déboguer et signer des applications iOS à distribuer, Visual Studio 2017 doit avoir un accès réseau à un hôte de build Mac configuré avec à la fois les outils de développement d’Apple (Xcode) et Xamarin.iOS.

    - [Téléchargez et installez Xcode à partir de Mac App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12).
    - [Installez Visual Studio pour Mac](https://docs.microsoft.com/visualstudio/mac/installation), qui installe également Xamarin.iOS.

    > [!NOTE]
    > Si vous préférez ne pas installer Visual Studio pour Mac, depuis [Visual Studio 2017 version 15.6](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes#automatic-macos-provisioning), Visual Studio 2017 peut configurer automatiquement l’hôte de build Mac avec le logiciel nécessaire pour générer des applications Xamarin.iOS. Pour plus d’informations, consultez [Provisionnement Mac automatique](~/ios/get-started/installation/windows/connecting-to-mac/index.md#automatic-mac-provisioning).

3. Appairer avec un Mac (connecter Visual Studio 2017 au Mac)

    Pour que Visual Studio 2017 utilise les outils de génération iOS sur le Mac, les deux machines doivent être connectées via un réseau.

    - [Lisez le guide Appairer avec un Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

::: zone-end

## <a name="summary"></a>Récapitulatif

Cet article a décrit comment configurer un ordinateur Windows et son hôte de build Mac associé pour le développement Xamarin.iOS.

## <a name="next-steps"></a>Étapes suivantes

- [Introduction à Xamarin.iOS pour Visual Studio](introduction-to-xamarin-ios-for-visual-studio.md)
- [Configuration de Visual Studio pour le développement iOS](config-options.md)
- [Provisionnement des appareils](~/ios/get-started/installation/device-provisioning/index.md)
