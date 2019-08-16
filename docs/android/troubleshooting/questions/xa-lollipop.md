---
title: Quelle version de Xamarin.Android ajoutait la prise en charge de Lollipop ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 63B6E10C-098D-4C82-9253-07CA62EA85A5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 4fe1bd4dda9a54eb3a1692f07d1069adb39345cb
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69523291"
---
# <a name="what-version-of-xamarinandroid-added-lollipop-support"></a>Quelle version de Xamarin.Android ajoutait la prise en charge de Lollipop ?

> [!NOTE]
> Ce guide a été écrit à l’origine pour Android L preview.

- [Xamarin. android 4,17](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_4/xamarin.android_4.17/index.md) a ajouté la prise en charge d’Android L preview.
- [Xamarin. android 4,20](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_4/xamarin.android_4.20/index.md) a ajouté la prise en charge des Lollipop Android.

Xamarin prend uniquement en charge la version stable actuelle des outils Xamarin. Les informations ci-dessous sont fournies «en l’or» pour les versions antérieures des outils. Pour obtenir les informations les plus récentes sur les versions Xamarin, consultez [cette page](http://releases.xamarin.com/).

## <a name="missing-androidjar-for-api-level-21-in-android-l-preview"></a>«Android. jar manquant pour le niveau d’API 21» dans Android L preview

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Le message d’erreur suivant (ou similaire) peut s’afficher:

```cmd
Error 1 Could not find android.jar for API Level 21.
```

Ce message signifie que la plateforme Android SDK pour le niveau d’API 21 n’est pas installée. Vous pouvez l’installer dans le gestionnaire de Android SDK (**outils > ouvrir Android SDK Manager...** ) ou modifier votre projet Xamarin. Android pour cibler une version d’API installée.

Il existe quelques solutions de contournement pour ce problème:

1. Modifiez votre projet pour qu’il cible l’API 19 ou inférieure.

2. Renommez votre dossier Android-21 d’Android-21 à Android-L. (Au mieux, cela ne doit être utilisé qu’en tant que correctif temporaire et il peut ne pas fonctionner très bien.)

   **% LocalAppData%\\Android\\Android-plateformes\\SDK\\Android-21**

3. Revenir temporairement à la version préliminaire du niveau d’API Android 21 «L» (1):

    1. Supprimer les **plateformes\\\\\\AndroidAndroid-SDK%LocalAppData%Android-21\\** 
    2. Extraire [1] dans **C:\\utilisateurs\\&lt;nom_utilisateur&gt;\\AppDatalocalAndroid\\Android-SDK\\plateformes à créer\\\\** un dossier **Android-L** .

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Le message d’erreur suivant (ou similaire) peut s’afficher:

```bash
/Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets: 
Error: Could not find android.jar for API Level 21.**
```

Cela signifie que la plateforme Android SDK pour le niveau d’API 21 n’est pas installée. Vous pouvez l’installer dans le gestionnaire de Android SDK (outils > Gestionnaire du kit de développement logiciel (SDK)...) ou modifier votre projet Xamarin. Android pour cibler une version d’API installée.

Il existe quelques solutions de contournement pour ce problème:

1. Modifiez votre projet pour qu’il cible l’API 19 ou inférieure.

2. Renommez votre dossier Android-21 d’Android-21 à Android-L. (Au mieux, cela ne doit être utilisé qu’en tant que correctif temporaire et il peut ne pas fonctionner très bien.)

   **~/Library/Developer/Xamarin/android-sdk-macosx/android-21**

3. Revenir temporairement à la version préliminaire du niveau d’API Android 21 «L» (1):

    1. Supprimer **/Users/username/Library/Developer/Xamarin/Android-SDK-MacOSX/Android-21**
    2. Extrayez [1] dans **/Users/username/Library/Developer/Xamarin/Android-SDK-MacOSX** pour créer un dossier **Android-L** .

-----


[1] - [https://dl-ssl.google.com/android/repository/android-L_r04.zip](https://dl-ssl.google.com/android/repository/android-L_r04.zip)
