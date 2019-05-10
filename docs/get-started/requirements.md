---
title: Configuration requise pour Xamarin.Forms
description: Configuration requise pour la plateforme et le développement pour Xamarin.Forms.
ms.prod: xamarin
ms.assetid: eecaf6a5-567c-49b2-ac83-2a195596c5bf
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/01/2019
ms.openlocfilehash: 89afb106320ce77e86a66f2c78bd6e32de8c38f3
ms.sourcegitcommit: be9658de032f3893741261f16162a664952ce178
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2019
ms.locfileid: "64986986"
---
# <a name="xamarinforms-requirements"></a>Configuration requise pour Xamarin.Forms

_Configuration requise pour la plateforme et le développement pour Xamarin.Forms._

Reportez-vous à l’article [Installation](installation/index.md) pour obtenir une vue d’ensemble des pratiques d’installation et de configuration qui s’appliquent sur toutes les plateformes.

## <a name="target-platforms"></a>Plateformes cibles

Les applications Xamarin.Forms peuvent être écrites pour les systèmes d’exploitation suivants :

- iOS 8 ou ultérieur
- Android 5.0 (API 21) ou une version ultérieure ([plus de détails](#android))
- Plateforme Windows universelle Windows 10 ([plus de détails](#windows10))

Il est supposé que les développeurs connaissent [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md).

### <a name="additional-platform-support"></a>Prise en charge de plateforme supplémentaire

L’état de ces plateformes est disponible sur [Xamarin.Forms GitHub](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support) :

- Samsung Tizen
- macOS
- GTK#
- WPF

### <a name="android"></a>Android

Les dernières versions d’Android SDK Tools et de la plateforme d’API Android doivent être installées. Vous pouvez effectuer une mise à jour vers les versions les plus récentes à l’aide du [Gestionnaire Android SDK](~/android/get-started/installation/android-sdk.md).

De plus, la version cible/de compilation pour les projets Android **doit** avoir la valeur *Utiliser la dernière plateforme installée*. Toutefois, vous pouvez définir la version minimale à API 19, ce qui vous permet de continuer à prendre en charge les appareils utilisant Android 4.4 et les versions ultérieures. Ces valeurs sont définies dans les **Options du projet** :

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

**Options du projet > Application > Propriétés de l’application**

![Options de génération Android dans Visual Studio](requirements-images/options-android-vs-sml.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

**Générer > Général**

![Sélectionnez la dernière version du framework cible](requirements-images/options-general-sml.png)

**Générer > Application Android**

![Sélectionnez la valeur minimale et cibler des versions Android pour votre application](requirements-images/options-android-sml.png)

-----

## <a name="development-system-requirements"></a>Configuration requise pour le développement

Les applications Xamarin.Forms peuvent être développées sur macOS et Windows. Toutefois, Windows et Visual Studio sont nécessaires pour produire des versions Windows de l’application.

## <a name="mac-system-requirements"></a>Configuration requise pour Mac

Vous pouvez utiliser Visual Studio pour Mac pour développer des applications Xamarin.Forms sur macOS High Sierra (10.13) ou une version ultérieure. Pour développer des applications iOS, nous vous recommandons au moins le SDK iOS 10 et Xcode 9 est installé.

> [!NOTE]
>  Les applications Windows ne peuvent pas être développées sur macOS.

## <a name="windows-system-requirements"></a>Configuration requise pour Windows

Les applications Xamarin.Forms pour iOS et Android peuvent être générées sur toute installation Windows qui prend en charge le développement Xamarin. Visual Studio 2017 ou version plus récente doit être exécuté sur Windows 7 ou ultérieur. Un Mac en réseau est requis pour le développement iOS.

<a name="windows10" />

### <a name="universal-windows-platform-uwp"></a>Plateforme Windows universelle (UWP)

Le développement d’applications Xamarin.Forms pour UWP nécessite :

- Windows 10 (dernière version recommandée, Fall Creators Update minimale)

- Visual Studio 2019 recommandé (Visual Studio 2017 version 15.8 minimale)

- [SDK Windows 10](https://dev.windows.com/downloads/windows-10-sdk)

Vous pouvez [ajouter une application de plateforme Windows universelle (UWP)](~/xamarin-forms/platform/windows/installation/index.md) à une solution Xamarin.Forms existante à tout moment.

## <a name="deprecated-platforms"></a>Plates-formes déconseillées

Ces plateformes ne sont pas prises en charge lors de l’utilisation de Xamarin.Forms 3.0 ou version ultérieure :

- *Windows 8.1 / Windows Phone 8.1 WinRT*
- *Windows Phone 8 Silverlight*