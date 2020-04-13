---
title: Quelle version de Xamarin.Android ajoutait la prise en charge de Lollipop ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 63B6E10C-098D-4C82-9253-07CA62EA85A5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 9e36189c771ed0c91a6030fd0ab615ab9af4dd52
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73026713"
---
# <a name="what-version-of-xamarinandroid-added-lollipop-support"></a>Quelle version de Xamarin.Android ajoutait la prise en charge de Lollipop ?

> [!NOTE]
> Ce guide a été écrit à l’origine pour l’aperçu Android L.

- [Xamarin.Android 4.17](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_4/xamarin.android_4.17/index.md) a ajouté Android L Preview support.
- [Xamarin.Android 4.20](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_4/xamarin.android_4.20/index.md) a ajouté android Lollipop support.

Xamarin ne supporte activement que la libération stable actuelle des outils Xamarin. L’information ci-dessous est fournie "comme il est" pour les anciennes versions des outils. Pour les dernières informations sur les versions de Xamarin, veuillez consulter les [notes de sortie](https://docs.microsoft.com/xamarin/whats-new/#product-release-notes).

## <a name="missing-androidjar-for-api-level-21-in-android-l-preview"></a>"Missing android.jar for API Level 21" in Android L Preview

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Le message d’erreur suivant (ou similaire) peut apparaître :

```cmd
Error 1 Could not find android.jar for API Level 21.
```

Ce message signifie que la plate-forme Android SDK pour API Level 21 n’est pas installée. Installez-le dans l’Android SDK Manager **(Outils > Open Android SDK Manager...**), soit modifiez votre projet Xamarin.Android pour cibler une version API installée.

Il y a quelques solutions de contournement pour cette question :

1. Modifiez votre projet de façon à atteindre l’API 19 ou moins.

2. Renommer votre dossier Android-21 d’Android-21 à Android-L. (Au mieux, cela ne devrait être utilisé que comme une solution temporaire, et il pourrait ne pas fonctionner très bien du tout.)

   **%LOCALAPPDATA%\\\\Android android-sdk\\plates-formes\\android-21**

3. Temporairement rétrogradé à l’API Android Niveau 21 "L" aperçu [1]:

    1. Supprimer le **%LOCALAPPDATA%\\Android\\android-sdk\\plates-formes\\android-21** 
    2. Extrait [1] en **C :\\Les\\utilisateurs\\\\&lt;\\nom d’utilisateur\\&gt;\\AppData Local Android-sdk plates-formes** pour créer un dossier **Android-L.**

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Le message d’erreur suivant (ou similaire) peut apparaître :

```bash
/Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets: 
Error: Could not find android.jar for API Level 21.**
```

Cela signifie que la plate-forme Android SDK pour API Level 21 n’est pas installée. Installez-le dans l’Android SDK Manager (Tools > SDK Manager...), soit modifiez votre projet Xamarin.Android pour cibler une version API installée.

Il y a quelques solutions de contournement pour cette question :

1. Modifiez votre projet de façon à atteindre l’API 19 ou moins.

2. Renommer votre dossier Android-21 d’Android-21 à Android-L. (Au mieux, cela ne devrait être utilisé que comme une solution temporaire, et il pourrait ne pas fonctionner très bien du tout.)

   **/Bibliothèque/Développeur/Xamarin/android-sdk-macosx/android-21**

3. Temporairement rétrogradé à l’API Android Niveau 21 "L" aperçu [1]:

    1. Supprimer **/Utilisateurs/nom d’utilisateur/Bibliothèque/Développeur/Xamarin/android-sdk-macosx/android-21**
    2. Extrait [1] dans **/Users/username/Library/Developer/Xamarin/android-sdk-macosx** pour créer un dossier **Android-L.**

-----

[1] -[https://dl-ssl.google.com/android/repository/android-L_r04.zip](https://dl-ssl.google.com/android/repository/android-L_r04.zip)
