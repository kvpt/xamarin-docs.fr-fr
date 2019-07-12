---
title: Quelle version de Xamarin.Android ajoutait la prise en charge de Lollipop ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 63B6E10C-098D-4C82-9253-07CA62EA85A5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: db0cb221ee18998fbb98449ae03c442c84bcfcd7
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67829712"
---
# <a name="what-version-of-xamarinandroid-added-lollipop-support"></a>Quelle version de Xamarin.Android ajoutait la prise en charge de Lollipop ?

> [!NOTE]
> Ce guide a été écrit à l’origine pour la version préliminaire L Android.

-   [Xamarin.Android 4.17](https://developer.xamarin.com/releases/android/xamarin.android_4/xamarin.android_4.17/) prise en charge Android L version préliminaire.
-   [Xamarin.Android 4.20](https://developer.xamarin.com/releases/android/xamarin.android_4/xamarin.android_4.20/) prise en charge Android Lollipop.

Xamarin prend uniquement activement en charge la version stable actuelle des outils Xamarin. Les informations ci-dessous sont fournies « en tant que-est » pour les versions antérieures des outils. Pour plus d’informations sur les versions de Xamarin, consultez [ici](http://releases.xamarin.com/).

## <a name="missing-androidjar-for-api-level-21-in-android-l-preview"></a>« Manquant android.jar pour niveau 21 d’API » dans la version préliminaire L Android

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Le message d’erreur suivant (ou similaire) peuvent s’afficher :

```cmd
Error 1 Could not find android.jar for API Level 21.
```

Ce message signifie que la plateforme Android SDK pour API niveau 21 n’est pas installée. Installez-la dans le gestionnaire Android SDK (**Outils > Open Android SDK Manager...** ), ou changez votre projet Xamarin.Android pour cibler une version d’API qui est installée.

Il existe quelques solutions de contournement pour ce problème :

1. Modifier votre projet afin qu’il cible API 19 ou inférieure.

2. Renommez votre dossier android-21 à partir d’android-21 android-L. (Au mieux, cela doit uniquement servir comme un correctif temporaire et peut ne pas fonctionner très bien tout.)

   **%LOCALAPPDATA%\\Android\\android-sdk\\platforms\\android-21**

3. Rétrograder temporairement vers la version préliminaire de niveau d’API Android 21 « L » [1] :

    1.  Supprimer le **% LocalAppData%\\Android\\android-sdk\\plateformes\\android-21** 
    2.  Extraire [1] dans **C:\\utilisateurs\\&lt;nom d’utilisateur&gt;\\AppData\\Local\\Android\\android-sdk\\plateformes**  pour créer un **android-l** dossier.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Le message d’erreur suivant (ou similaire) peuvent s’afficher :

```bash
/Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets: 
Error: Could not find android.jar for API Level 21.**
```

Cela signifie que la plateforme Android SDK pour API niveau 21 n’est pas installée. Installez-la dans le gestionnaire Android SDK (Outils > Gestionnaire de SDK...), ou changez votre projet Xamarin.Android pour cibler une version d’API qui est installée.

Il existe quelques solutions de contournement pour ce problème :

1. Modifier votre projet afin qu’il cible API 19 ou inférieure.

2. Renommez votre dossier android-21 à partir d’android-21 android-L. (Au mieux, cela doit uniquement servir comme un correctif temporaire et peut ne pas fonctionner très bien tout.)

   **~/Library/Developer/Xamarin/android-sdk-macosx/android-21**

3. Rétrograder temporairement vers la version préliminaire de niveau d’API Android 21 « L » [1] :

    1.  Supprimer **/Users/username/Library/Developer/Xamarin/android-sdk-macosx/android-21**
    2.  Extraire [1] dans **/Users/username/Library/Developer/Xamarin/android-sdk-macosx** pour créer un **android-l** dossier.

-----


[1] - [https://dl-ssl.google.com/android/repository/android-L_r04.zip](https://dl-ssl.google.com/android/repository/android-L_r04.zip)
