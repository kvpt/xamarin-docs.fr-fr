---
title: Xamarin. Exigences relatives aux formulaires
description: Configuration requise pour la plateforme et le développement pour Xamarin. Forms.
ms.prod: xamarin
ms.assetid: eecaf6a5-567c-49b2-ac83-2a195596c5bf
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/16/2019
no-loc:
- Xamarin
- Xamarin.Forms
- Xamarin.Android
- Xamarin.Essentials
- Xamarin.iOS
- Xamarin.Mac
ms.openlocfilehash: d12daa358917399fc5fd1febf02d4f96a647f360
ms.sourcegitcommit: 6f09bc2b760e76a61a854f55d6a87c4f421ac6c8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2020
ms.locfileid: "75607852"
---
# <a name="opno-locxamarinforms-requirements"></a>Xamarin. Exigences relatives aux formulaires

_Configuration requise pour la plateforme et le développement pour Xamarin. Forms._

Reportez-vous à l’article [Installation](installation/index.md) pour obtenir une vue d’ensemble des pratiques d’installation et de configuration qui s’appliquent sur toutes les plateformes.

## <a name="target-platforms"></a>Plateformes cibles

Xamarin. Les applications de formulaires peuvent être écrites pour les systèmes d’exploitation suivants :

- iOS 9 ou version ultérieure
- Android 4.4 (API 19) ou version ultérieure ([plus d’informations](#android))
- Plateforme Windows universelle Windows 10 ([plus de détails](#windows10))

Toutefois, Android 5,0 (API 21) est recommandé comme API minimale. Cela garantit une compatibilité complète avec toutes les bibliothèques de support Android, tout en ciblant toujours la majorité des appareils Android.

Il est supposé que les développeurs se familiarisent avec [.NET standard](~/cross-platform/app-fundamentals/net-standard.md).

### <a name="additional-platform-support"></a>Prise en charge de plateforme supplémentaire

L’état de ces plateformes est disponible sur le [Xamarin. GitHub de formulaires](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support):

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

![Sélectionner la dernière version cible de .NET Framework](requirements-images/options-general-sml.png)

**Générer > Application Android**

![Sélectionner les versions d’Android minimale et cible pour votre application](requirements-images/options-android-sml.png)

-----

## <a name="development-system-requirements"></a>Configuration requise pour le développement

Xamarin. Les applications de formulaires peuvent être développées sur macOS et Windows. Toutefois, Windows et Visual Studio sont nécessaires pour produire des versions Windows de l’application.

## <a name="mac-system-requirements"></a>Configuration requise pour Mac

Vous pouvez utiliser Visual Studio pour Mac pour développer des Xamarin. Applications de formulaires sur macOS High Sierra (10,13) ou version ultérieure. Pour développer des applications iOS, nous vous recommandons d’utiliser la dernière version de Xcode, iOS et macOS. Pour des exigences de version spécifiques, reportez-vous aux notes de publication les plus récentes de [Xamarin. iOS](/xamarin/ios/release-notes/).

> [!NOTE]
> Les applications Windows ne peuvent pas être développées sur macOS.

## <a name="windows-system-requirements"></a>Configuration requise pour Windows

Xamarin. Les applications de formulaires pour iOS et Android peuvent être créées sur n’importe quelle installation Windows qui prend en charge le développement de Xamarin. Pour une prise en charge complète des fonctionnalités actuelles de la plateforme, utilisez la version la plus récente de Visual Studio. 

Un Mac en réseau est requis pour le développement iOS à l’aide de la dernière version de Xcode et de la version minimale de macOS spécifiée par Apple.

<a name="windows10" />

### <a name="universal-windows-platform-uwp"></a>Plateforme Windows universelle (UWP)

Développement de Xamarin. Les applications de formulaires pour UWP requièrent :

- Windows 10 (version la plus récente recommandée, configuration des créateurs de automne minimale)

- Visual Studio 2019 recommandé

- [SDK Windows 10](https://dev.windows.com/downloads/windows-10-sdk)

Vous pouvez [Ajouter une application plateforme Windows universelle (UWP)](~/xamarin-forms/platform/windows/installation/index.md) à un Xamarinexistant. Forms solution à tout moment.

## <a name="deprecated-platforms"></a>Plateformes déconseillées

Ces plateformes ne sont pas prises en charge lors de l’utilisation de Xamarin. Formulaires 3,0 ou plus récents :

- *Windows 8.1 / Windows Phone 8.1 WinRT*
- *Windows Phone 8 Silverlight*
