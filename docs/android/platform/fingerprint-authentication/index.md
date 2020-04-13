---
title: Authentification par empreinte digitale
description: Ce guide discute de la façon d’ajouter l’authentification des empreintes digitales, introduite dans Android 6.0, à une application Xamarin.Android.
ms.prod: xamarin
ms.assetid: 6742D874-4988-4516-A946-D5C714B20A10
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 4a4b6ee7a123683a9d5a140c46c0b3542767ffa3
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027522"
---
# <a name="fingerprint-authentication"></a>Authentification par empreinte digitale

_Ce guide discute de la façon d’ajouter l’authentification des empreintes digitales, introduite dans Android 6.0, à une application Xamarin.Android._

## <a name="fingerprint-authentication-overview"></a>Aperçu de l’authentification des empreintes digitales

L’arrivée de scanners d’empreintes digitales sur les appareils Android offre aux applications une alternative à la méthode traditionnelle d’authentification utilisateur/mot de passe. L’utilisation d’empreintes digitales pour authentifier un utilisateur permet à une application d’intégrer une sécurité moins intrusive qu’un nom d’utilisateur et un mot de passe.

Les API FingerprintManager ciblent les appareils à l’adresse d’un scanner d’empreintes digitales et exécutent le niveau 23 de l’API (Android 6.0) ou plus. Les API se `Android.Hardware.Fingerprints` trouvent dans l’espace nom. L’Android Support Library v4 fournit des versions des API empreintes digitales destinées aux anciennes versions d’Android. Les API de compatibilité `Android.Support.v4.Hardware.Fingerprint` se trouvent dans l’espace nom, sont distribués via le [forfait Xamarin.Android.Support.v4 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.v4/).

Le [FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html) (et son homologue de la Bibliothèque de soutien, [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)) est la classe principale pour l’utilisation du matériel de numérisation des empreintes digitales. Cette classe est un emballage Android SDK autour du service de niveau système qui gère les interactions avec le matériel lui-même. Il est responsable du démarrage du scanner d’empreintes digitales et de la réponse aux commentaires du scanner. Cette classe a une interface assez simple avec seulement trois membres:

- **`Authenticate`**&ndash; Cette méthode va initialiser le scanner matériel et commencer le service en arrière-plan, en attendant que l’utilisateur de scanner leur empreinte digitale.
- **`EnrolledFingerprints`**&ndash; Cette propriété `true` sera de retour si l’utilisateur a enregistré une ou plusieurs empreintes digitales avec l’appareil.
- **`HardwareDetected`**&ndash; Cette propriété est utilisée pour déterminer si l’appareil prend en charge la numérisation des empreintes digitales.

La `FingerprintManager.Authenticate` méthode est utilisée par une application Android pour démarrer le scanner d’empreintes digitales. L’extrait suivant est un exemple de la façon de l’invoquer à l’aide des API de compatibilité de la Bibliothèque de soutien :

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

Ce guide discutera de `FingerprintManager` la façon d’utiliser les API pour améliorer une application Android avec l’authentification des empreintes digitales. Il couvrira la façon d’instantané et de créer un `CryptoObject` pour aider à sécuriser les résultats à partir du scanner d’empreintes digitales. Nous examinerons comment une application `FingerprintManager.AuthenticationCallback` devrait sous-classer et répondre aux commentaires du scanner d’empreintes digitales. Enfin, nous allons voir comment inscrire une empreinte digitale sur un appareil Android ou un émulateur et comment utiliser **une bad** pour simuler une analyse d’empreintes digitales.

## <a name="requirements"></a>Spécifications

L’authentification des empreintes digitales nécessite Android 6.0 (niveau API 23) ou plus et un appareil équipé d’un scanner d’empreintes digitales. 

Une empreinte digitale doit déjà être inscrite avec l’appareil pour chaque utilisateur qui doit être authentifié. Il s’agit de configurer un verrou d’écran qui utilise un mot de passe, un NIP, un motif de balayage ou une reconnaissance faciale. Il est possible de simuler une partie de la fonctionnalité d’authentification des empreintes digitales dans un émulateur Android.  Pour plus d’informations sur ces deux sujets, veuillez consulter la section [Inscription d’une empreinte digitale.](enrolling-fingerprint.md) 

## <a name="related-links"></a>Liens connexes

- [App échantillon de guide d’empreintes digitales](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fingerprintguide)
- [Échantillon de dialogue d’empreintes digitales](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)
- [Demande d’autorisations à Runtime](https://developer.android.com/training/permissions/requesting.html)
- [android.hardware.fingerprint](https://developer.android.com/reference/android/hardware/fingerprint/package-summary.html)
- [android.support.v4.hardware.fingerprint](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/package-summary.html)
- [Android.Content.Context (en)](xref:Android.Content.Context)
- [Empreintes digitales et paiements API (vidéo)](https://youtu.be/VOn7VrTRlA4)
