---
title: Architectures de processeur
description: Xamarin. Android prend en charge plusieurs architectures d’UC, y compris les appareils 32 bits et 64 bits. Cet article explique comment cibler une application vers une ou plusieurs architectures de processeur compatibles Android.
ms.prod: xamarin
ms.assetid: D4BC889D-9164-49BB-9B7B-F6C4E4E109F1
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/30/2019
ms.openlocfilehash: 16e805488969aadb0d0b8aa5c892248b7fa403c9
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69521208"
---
# <a name="cpu-architectures"></a>Architectures de processeur

_Xamarin. Android prend en charge plusieurs architectures d’UC, y compris les appareils 32 bits et 64 bits. Cet article explique comment cibler une application vers une ou plusieurs architectures de processeur compatibles Android._

## <a name="cpu-architectures-overview"></a>Vue d’ensemble des architectures de processeur

Lorsque vous préparez votre application pour la version finale, vous devez spécifier les architectures d’UC de plateforme prises en charge par votre application. Un même APK peut contenir du code machine permettant la prise en charge de plusieurs architectures différentes. Chaque collection de code spécifique à l’architecture est associée à une *interface binaire d’application* (ABI). Chaque ABI définit la manière dont ce code machine est censé interagir avec Android au moment de l’exécution.
Pour plus d’informations sur le fonctionnement de ce processus, consultez la [page périphériques &amp; multicœurs Xamarin. Android](~/android/deploy-test/multicore-devices.md).


## <a name="how-to-specify-supported-architectures"></a>Comment spécifier des architectures prises en charge

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

En règle générale, vous sélectionnez explicitement une ou des architectures lorsque votre application est configurée pour la **mise en version**. Lorsque votre application est configuréepour le débogage, les options **utiliser le runtime partagé** et **utiliser le déploiement rapide** sont activées, ce qui désactive la sélection de l’architecture explicite.

Dans Visual Studio, cliquez avec le bouton droit sur votre projet sous le **Explorateur de solutions** , puis sélectionnez **Propriétés**. Dans la page **options Android** , vérifiez la section **Propriétés de Packaging** et vérifiez que l’option utiliser le **Runtime partagé** est désactivée (l’activation de cette option vous permet de sélectionner explicitement les Abi à prendre en charge). Cliquez sur le bouton **avancé** et, sous **architectures prises en charge**, vérifiez les architectures que vous souhaitez prendre en charge:

[![Sélection de ARMEABI et ARMEABI-V7A](cpu-architectures-images/vs/01-abi-selections-sml.png)](cpu-architectures-images/vs/01-abi-selections.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

En règle générale, vous sélectionnez explicitement une ou des architectures lorsque votre application est configurée pour la **mise en version**. Lorsque votre application est configuréepour le débogage, les options **utiliser le runtime mono partagé** et déploiement de l' **assembly rapide** sont activées, ce qui empêche la sélection de l’architecture explicite.

Dans Visual Studio pour Mac, localisez votre projet dans le panneau **solutions** , cliquez sur l’icône d’engrenage en regard de votre projet, puis sélectionnez **options**. Dans la boîte de dialogue **Options du projet** , cliquez sur **Build Android**. Cliquez sur l’onglet **général** et vérifiez que l’option **utiliser le runtime mono partagé** est désactivée (l’activation de cette option vous permet de sélectionner explicitement les Abi à prendre en charge). Cliquez sur l’onglet **avancé** et, sous **pris en charge Abi**, vérifiez les architectures Abi pour les architectures que vous souhaitez prendre en charge:

[![Sélection de ARMEABI et ARMEABI-V7A](cpu-architectures-images/xs/01-abi-selections-sml.png)](cpu-architectures-images/xs/01-abi-selections.png#lightbox)

-----


Xamarin.Android prend en charge les architectures suivantes :

- **ARMEABI** &ndash; Les processeurs ARM qui prennent en charge au moins le jeu d’instructions ARMv5TE. Notez que `armeabi` n’est pas thread-safe et ne doit pas être utilisé sur des appareils à plusieurs UC.

> [!NOTE]
> À partir de [Xamarin. Android 9,2](https://docs.microsoft.com/xamarin/android/release-notes/9/9.2#removal-of-support-for-armeabi-cpu-architecture), `armeabi` n’est plus pris en charge.

- **ARMEABI-V7A** &ndash; Les processeurs ARM avec des opérations en virgule flottante matérielles et des périphériques multiprocesseurs (SMP). Notez que `armeabi-v7a` le code machine ne s’exécutera pas sur les appareils ARMv5.

- **arm64-V8A** &ndash; Processeurs basés sur l’architecture ARMv8 bits 64.

- **x86** &ndash; Les processeurs qui prennent en charge le jeu d’instructions x86 (ou IA-32). Ce jeu d’instructions est équivalent à celui du Pentium Pro, y compris les instructions MMX, SSE, SSE2 et SSE3.

- **x86_64** Les processeurs qui prennent en charge le jeu d’instructions x86 64 bits (également appelé *x64* et *amd64*).

Xamarin. Android a pour valeur `armeabi-v7a` par défaut pour les versions **Release** . Ce paramètre offre des performances nettement supérieures `armeabi`à celles de. Si vous ciblez une plateforme ARM 64 bits (telle que la branche 9), sélectionnez `arm64-v8a`. Si vous déployez votre application sur un appareil x86, sélectionnez `x86`. Si l’appareil x86 cible utilise une architecture d’UC 64 bits, sélectionnez `x86_64`.

## <a name="targeting-multiple-platforms"></a>Ciblage de plusieurs plateformes

Pour cibler plusieurs architectures d’UC, vous pouvez sélectionner plusieurs ABI (au détriment d’une taille de fichier APK plus grande). Vous pouvez utiliser l’option **générer un package (. apk) par l’option Abi sélectionnée** (décrite dans [définir les propriétés](~/android/deploy-test/release-prep/index.md#Set_Packaging_Properties)d’empaquetage) pour créer un apk distinct pour chaque architecture prise en charge.

Vous n’avez pas besoin de sélectionner **arm64-V8A** ou **x86_64** pour cibler des appareils 64 bits; la prise en charge de 64 bits n’est pas nécessaire pour exécuter votre application sur du matériel 64 bits. Par exemple, les appareils ARM 64 bits (par exemple, [la branche 9](http://www.google.com/nexus/9/)) peuvent exécuter des applications `armeabi-v7a`configurées pour. Le principal avantage de l’activation de la prise en charge de 64 bits est de permettre à votre application de traiter davantage de mémoire.

> [!NOTE]
> Depuis août 2018, les nouvelles applications doivent cibler l’API de niveau 26, et à partir d’août 2019, les applications devront [fournir des versions 64 bits](https://android-developers.googleblog.com/2017/12/improving-app-security-and-performance.html) en plus des versions 32 bits.

## <a name="additional-information"></a>Informations supplémentaires

Dans certains cas, vous devrez peut-être créer un APK distinct pour chaque architecture (afin de réduire la taille de votre APK, ou parce que votre application a des bibliothèques partagées qui sont spécifiques à une architecture d’UC particulière).
Pour plus d’informations sur cette approche, consultez [générer des fichiers APK spécifiques à Abi](~/android/deploy-test/building-apps/abi-specific-apks.md).
