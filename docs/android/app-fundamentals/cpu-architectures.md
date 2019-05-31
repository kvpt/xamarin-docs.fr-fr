---
title: Architectures de processeur
description: Xamarin.Android prend en charge plusieurs architectures d’UC, notamment les périphériques 32 bits et 64 bits. Cet article explique comment cibler une application à un ou plusieurs architectures d’UC Android pris en charge.
ms.prod: xamarin
ms.assetid: D4BC889D-9164-49BB-9B7B-F6C4E4E109F1
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/30/2019
ms.openlocfilehash: 46e628700771864c6a4b99edea550af694bf3a62
ms.sourcegitcommit: dd73477b1bccbd7ca45c1fb4e794da6b36ca163d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66394683"
---
# <a name="cpu-architectures"></a>Architectures de processeur

_Xamarin.Android prend en charge plusieurs architectures d’UC, notamment les périphériques 32 bits et 64 bits. Cet article explique comment cibler une application à un ou plusieurs architectures d’UC Android pris en charge._

## <a name="cpu-architectures-overview"></a>Vue d’ensemble des Architectures de processeur

Lorsque vous préparez votre application pour la mise en production, vous devez spécifier les architectures de plateforme UC pris en charge de votre application. Un même APK peut contenir du code machine permettant la prise en charge de plusieurs architectures différentes. Chaque collection de code spécifique à l’architecture est associée un *Interface binaire d’Application* (ABI). Chaque ABI définit comment ce code machine est prévu pour interagir avec Android en cours d’exécution.
Pour plus d’informations sur la façon dont cela fonctionne, consultez [appareils multicœurs &amp; Xamarin.Android](~/android/deploy-test/multicore-devices.md).


## <a name="how-to-specify-supported-architectures"></a>Comment spécifier les Architectures prises en charge

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

En règle générale, vous sélectionnez explicitement une architecture (ou architectures) lorsque votre application est configurée pour **version**. Lorsque votre application est configurée pour **déboguer**, le **utiliser le Runtime partagé** et **utiliser Fast Deployment** options sont activées, lequel désactiver la sélection de l’architecture explicite.

Dans Visual Studio, cliquez sur votre projet sous le **l’Explorateur de solutions** et sélectionnez **propriétés**. Sous le **Options Android** page Vérification de la **propriétés de Packaging** section et vérifiez que **utiliser le Runtime partagé** est désactivé (désactivation de cette option vous permet de manière explicite Sélectionnez les ABI pour prendre en charge). Cliquez sur le **avancé** bouton et, sous **architectures prises en charge**, vérifiez les architectures que vous souhaitez prendre en charge :

[![En sélectionnant armeabi et armeabi-v7a](cpu-architectures-images/vs/01-abi-selections-sml.png)](cpu-architectures-images/vs/01-abi-selections.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

En règle générale, vous sélectionnez explicitement une architecture (ou architectures) lorsque votre application est configurée pour **version**. Lorsque votre application est configurée pour **déboguer**, le **utiliser le runtime Mono partagé** et **déploiement d’assembly rapide** options sont activées, ce qui empêche l’architecture explicite sélection.

Dans Visual Studio pour Mac, localisez votre projet dans le **Solution** de remplissage, cliquez sur l’icône d’engrenage en regard de votre projet et sélectionnez **Options**. Dans le **Options du projet** boîte de dialogue, cliquez sur **Build Android**. Cliquez sur le **général** onglet et vérifiez que **utiliser le runtime Mono partagé** est désactivé (désactivation de cette option vous permet de sélectionner explicitement les ABI pour prendre en charge). Cliquez sur le **avancé** onglet et sous **ABI prises en charge**, vérifiez l’ABI pour les architectures que vous souhaitez prendre en charge :

[![En sélectionnant armeabi et armeabi-v7a](cpu-architectures-images/xs/01-abi-selections-sml.png)](cpu-architectures-images/xs/01-abi-selections.png#lightbox)

-----


Xamarin.Android prend en charge les architectures suivantes :

-   **armeabi** &ndash; les UC ARM qui prennent en charge au moins le jeu d’instructions ARMv5TE. Notez que `armeabi` n’est pas thread-safe et ne doit pas être utilisé sur les appareils multiprocesseur.

> [!NOTE]
> En tant que de [Xamarin.Android 9.2](https://docs.microsoft.com/xamarin/android/release-notes/9/9.2#removal-of-support-for-armeabi-cpu-architecture), `armeabi` n’est plus pris en charge.

-   **armeabi-v7a** &ndash; les UC ARM avec les opérations à virgule flottante de matériel et de plusieurs appareils multiprocesseur (SMP). Notez que `armeabi-v7a` code machine ne s’exécutera pas sur les appareils ARMv5.

-   **arm64-v8a** &ndash; unités centrales, selon l’architecture de ARMv8 64 bits.

-   **x86** &ndash; processeurs qui prennent en charge présent (ou une IA-32 à le x86) jeu d’instructions. Ce jeu d’instructions est équivalent à celle de Pentium Pro, y compris les instructions MMX, SSE, SSE2 et SSE3.

-   **x86_64** processeurs qui prennent en charge la x86 64 bits (également appelés *x64* et *AMD64*) jeu d’instructions.

Par défaut est Xamarin.Android `armeabi-v7a` pour **version** génère. Ce paramètre offre des performances supérieures aux `armeabi`. Si vous ciblez une plateforme ARM de 64 bits (par exemple, le 9 Nexus), sélectionnez `arm64-v8a`. Si vous déployez votre application sur un x86 appareil, sélectionnez `x86`. Si l’appareil cible x86 utilise une architecture de processeur 64 bits, sélectionnez `x86_64`.

## <a name="targeting-multiple-platforms"></a>Ciblage de plateformes multiples

Pour cibler plusieurs architectures d’UC, vous pouvez sélectionner plusieurs ABI (aux dépens de taille du fichier APK). Vous pouvez utiliser la **générer un package (.apk) par ABI sélectionnée** option (décrit dans [définir les propriétés de Packaging](~/android/deploy-test/release-prep/index.md#Set_Packaging_Properties)) pour créer un APK distinct pour chaque architecture prise en charge.

Vous n’êtes pas obligé de sélectionner **arm64-v8a** ou **x86_64** pour cibler des appareils 64 bits ; prise en charge 64 bits n’est pas nécessaire pour exécuter votre application sur un matériel 64 bits. Par exemple, les appareils ARM 64 bits (tels que le [Nexus 9](http://www.google.com/nexus/9/)) peuvent exécuter des applications configurées pour `armeabi-v7a`. Le principal avantage de permettre la prise en charge 64 bits consiste à permettre à votre application d’adresser plus de mémoire.

> [!NOTE]
> Depuis août 2018, les nouvelles applications doivent cibler l’API de niveau 26, et à partir d’août 2019, les applications devront [fournir des versions 64 bits](https://android-developers.googleblog.com/2017/12/improving-app-security-and-performance.html) en plus des versions 32 bits.

## <a name="additional-information"></a>Informations supplémentaires

Dans certaines situations, vous devrez peut-être créer un APK distinct pour chaque architecture (pour réduire la taille de votre APK, soit parce que votre application a des bibliothèques partagées qui sont spécifiques à une architecture d’UC spécifique).
Pour plus d’informations sur cette approche, consultez [générer des fichiers Apk spécifiques ABI](~/android/deploy-test/building-apps/abi-specific-apks.md).
