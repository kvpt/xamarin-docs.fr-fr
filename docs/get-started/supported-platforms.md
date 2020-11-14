---
title: Xamarin.Forms Plateformes prises en charge
description: Configuration requise pour la plateforme et le développement pour Xamarin.Forms .
ms.prod: xamarin
ms.assetid: eecaf6a5-567c-49b2-ac83-2a195596c5bf
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/13/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: cfb5c7ce11ca2eab5aedfd1b6f862a750fcff6e3
ms.sourcegitcommit: bda07768b51e6d24f897ccae16e152dc7a83effa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94595331"
---
# <a name="no-locxamarinforms-supported-platforms"></a>Xamarin.Forms Plateformes prises en charge

Xamarin.Forms les applications peuvent être écrites pour les systèmes d’exploitation suivants :

- iOS 9 ou ultérieur.
- Android 4.4 (API 19) ou ultérieur ([plus d’informations](#android-platform-support)). Toutefois, Android 5.0 (API 21) est recommandé comme API minimale. Cela garantit une compatibilité complète avec toutes les bibliothèques de prise en charge Android, tout en ciblant toujours la majorité des appareils Android.
- Windows 10 plateforme Windows universelle, 10.0.16299.0 ou version ultérieure pour la prise en charge .NET Standard 2,0. Toutefois, la version 10.0.18362.0 ou ultérieure est recommandée.

Xamarin.Forms les applications pour iOS, Android et le plateforme Windows universelle (UWP) peuvent être créées dans Visual Studio. Toutefois, un Mac en réseau est nécessaire pour le développement iOS avec la dernière version de Xcode et la version minimale de macOS spécifiée par Apple. Pour plus d’informations, consultez [Configuration requise de Windows](~/cross-platform/get-started/requirements.md#windows-requirements).

Xamarin.Forms vous pouvez créer des applications pour iOS et Android dans Visual Studio pour Mac. Pour plus d’informations, consultez [Configuration requise de macOS](~/cross-platform/get-started/requirements.md#macos-requirements).

> [!NOTE]
> Le développement d’applications à l’aide Xamarin.Forms de nécessite une connaissance [.NET standard](~/cross-platform/app-fundamentals/net-standard.md).

## <a name="additional-platform-support"></a>Prise en charge de plateforme supplémentaire

Xamarin.Forms prend en charge des plateformes supplémentaires autres que iOS, Android et Windows :

- Samsung Tizen
- macOS 10,13 ou version ultérieure
- GTK#
- WPF

L’état de ces plateformes est disponible sur le [ Xamarin.Forms wiki de prise en charge de la plateforme GitHub](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support).

## <a name="android-platform-support"></a>Prise en charge des plateformes Android

Les dernières versions d’Android SDK Tools et de la plateforme d’API Android doivent être installées. Vous pouvez effectuer une mise à jour vers les versions les plus récentes à l’aide du [Gestionnaire Android SDK](~/android/get-started/installation/android-sdk.md).

De plus, la version cible/de compilation pour les projets Android **doit** avoir la valeur *Utiliser la dernière plateforme installée*. Toutefois, vous pouvez définir la version minimale à API 19, ce qui vous permet de continuer à prendre en charge les appareils utilisant Android 4.4 et les versions ultérieures. Ces valeurs sont définies dans les **Options du projet**  :

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

**Options du projet > Application > Propriétés de l’application**

![Options de build Android dans Visual Studio](requirements-images/options-android-vs-sml.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

**Générer > Général**

![Sélectionner le dernier framework cible](requirements-images/options-general-sml.png)

**Générer > Application Android**

![Sélectionner les versions d’Android minimale et cible pour votre application](requirements-images/options-android-sml.png)

-----

## <a name="deprecated-platforms"></a>Plateformes dépréciées

Ces plateformes ne sont pas prises en charge lors de l’utilisation Xamarin.Forms de 3,0 ou plus récente :

- *Windows 8.1 / Windows Phone 8.1 WinRT*
- *Windows Phone 8 Silverlight*
