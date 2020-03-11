---
title: Authentification par empreinte digitale
description: Ce guide explique comment ajouter l’authentification par empreinte digitale, introduite dans Android 6,0, à une application Xamarin. Android.
ms.prod: xamarin
ms.assetid: 6742D874-4988-4516-A946-D5C714B20A10
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 4a4b6ee7a123683a9d5a140c46c0b3542767ffa3
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "73027522"
---
# <a name="fingerprint-authentication"></a>Authentification par empreinte digitale

_Ce guide explique comment ajouter l’authentification par empreinte digitale, introduite dans Android 6,0, à une application Xamarin. Android._

## <a name="fingerprint-authentication-overview"></a>Vue d’ensemble de l’authentification par empreinte digitale

L’arrivée des scanneurs d’empreintes digitales sur les appareils Android offre aux applications une alternative à la méthode traditionnelle de nom d’utilisateur/mot de passe de l’authentification utilisateur. L’utilisation d’empreintes digitales pour authentifier un utilisateur permet à une application d’intégrer une sécurité moins intrusive qu’un nom d’utilisateur et un mot de passe.

Les API FingerprintManager ciblent les appareils avec un scanneur d’empreintes digitales et exécutent le niveau d’API 23 (Android 6,0) ou une version ultérieure. Les API se trouvent dans l’espace de noms `Android.Hardware.Fingerprints`. La bibliothèque de prise en charge Android fournit des versions des API d’empreintes digitales destinées aux versions antérieures d’Android. Les API de compatibilité se trouvent dans l’espace de noms `Android.Support.v4.Hardware.Fingerprint`, sont distribuées via le [package NuGet Xamarin. Android. support. v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/).

Le [FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html) (et son équivalent de la bibliothèque de prise en charge, [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)) est la classe principale pour l’utilisation du matériel de numérisation d’empreintes digitales. Cette classe est un wrapper Android SDK autour du service au niveau du système qui gère les interactions avec le matériel lui-même. Il est responsable du démarrage du scanneur d’empreintes digitales et de la réponse aux commentaires du scanneur. Cette classe a une interface relativement simple avec seulement trois membres :

- **`Authenticate`** &ndash; cette méthode initialise le scanneur matériel et démarre le service en arrière-plan, en attendant que l’utilisateur analyse son empreinte digitale.
- **`EnrolledFingerprints`** &ndash; cette propriété retourne `true` si l’utilisateur a inscrit une ou plusieurs empreintes digitales auprès de l’appareil.
- **`HardwareDetected`** &ndash; cette propriété est utilisée pour déterminer si l’appareil prend en charge l’analyse d’empreintes digitales.

La méthode `FingerprintManager.Authenticate` est utilisée par une application Android pour démarrer le scanneur d’empreintes digitales. L’extrait de code suivant montre comment l’appeler à l’aide des API de compatibilité de la bibliothèque de prise en charge :

```csharp
// context is any Android.Content.Context instance, typically the Activity 
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
fingerprintManager.Authenticate(FingerprintManager.CryptoObject crypto,
                                int flags,
                                CancellationSignal cancel,
                                FingerprintManagerCompat.AuthenticationCallback callback,
                                Handler handler
                               );
```

Ce guide explique comment utiliser les API `FingerprintManager` pour améliorer une application Android avec l’authentification par empreinte digitale. Il explique comment instancier et créer un `CryptoObject` pour sécuriser les résultats à partir du scanneur d’empreintes digitales. Nous allons examiner comment une application doit sous-créer `FingerprintManager.AuthenticationCallback` et répondre aux commentaires du scanneur d’empreintes digitales. Enfin, nous verrons comment inscrire une empreinte digitale sur un appareil ou un émulateur Android et comment utiliser **ADB** pour simuler une analyse d’empreintes digitales.

## <a name="requirements"></a>Configuration requise

L’authentification par empreinte digitale nécessite Android 6,0 (niveau d’API 23) ou supérieur et un appareil avec un scanneur d’empreintes digitales. 

Une empreinte digitale doit déjà être inscrite auprès de l’appareil pour chaque utilisateur qui doit être authentifié. Cela implique la configuration d’un verrouillage d’écran qui utilise un mot de passe, un code confidentiel, un modèle de balayage ou une reconnaissance faciale. Il est possible de simuler certaines fonctionnalités d’authentification par empreinte digitale dans une Émulateur Android.  Pour plus d’informations sur ces deux rubriques, consultez la section [inscription d’une empreinte digitale](enrolling-fingerprint.md) . 

## <a name="related-links"></a>Liens connexes

- [Exemple d’application Guide d’empreinte digitale](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fingerprintguide)
- [Exemple de boîte de dialogue empreinte digitale](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)
- [Demande d’autorisations au moment de l’exécution](https://developer.android.com/training/permissions/requesting.html)
- [Android. hardware. Fingerprint](https://developer.android.com/reference/android/hardware/fingerprint/package-summary.html)
- [Android. support. v4. hardware. Fingerprint](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/package-summary.html)
- [Android. Content. Context](xref:Android.Content.Context)
- [API d’empreinte digitale et de paiement (vidéo)](https://youtu.be/VOn7VrTRlA4)
