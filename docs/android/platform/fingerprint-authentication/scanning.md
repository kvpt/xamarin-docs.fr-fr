---
title: Numérisation pour les empreintes digitales
ms.prod: xamarin
ms.assetid: 1CDDC096-77E0-47B3-BE0B-8953E2DDACD3
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/23/2016
ms.openlocfilehash: 61edd0e4b532f18a8fc28502e5bb990703068776
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027492"
---
# <a name="scanning-for-fingerprints"></a>Numérisation pour les empreintes digitales

Maintenant que nous avons vu comment préparer une application Xamarin.Android pour utiliser `FingerprintManager.Authenticate` l’authentification des empreintes digitales, revenons à la méthode, et discuter de sa place dans l’authentification des empreintes digitales Android 6.0. Un bref aperçu du flux de travail pour l’authentification des empreintes digitales est décrit dans cette liste :

1. `FingerprintManager.Authenticate`Invoquer, `CryptoObject` en `FingerprintManager.AuthenticationCallback` passant un et une instance. Le `CryptoObject` est utilisé pour s’assurer que le résultat d’authentification des empreintes digitales n’a pas été altéré. 
2. Sous-classez la classe [FingerprintManager.AuthenticationCallback.](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html) Un exemple de cette classe `FingerprintManager` sera fourni au moment où l’authentification des empreintes digitales commence. Lorsque le scanner d’empreintes digitales est terminé, il invoque l’une des méthodes de rappel de cette classe.
3. Écrivez du code pour mettre à jour l’interface utilisateur pour faire savoir à l’utilisateur que l’appareil a lancé le scanner d’empreintes digitales et attend l’interaction de l’utilisateur. 
4. Lorsque le scanner d’empreintes digitales est fait, Android retournera `FingerprintManager.AuthenticationCallback` les résultats à l’application en invoquant une méthode sur l’instance qui a été fournie dans l’étape précédente.
5. L’application informera l’utilisateur des résultats de l’authentification des empreintes digitales et réagira aux résultats, le cas échéant. 

L’extrait de code suivant est un exemple d’une méthode dans une activité qui commencera à scanner les empreintes digitales :

```csharp
protected void FingerPrintAuthenticationExample()
{
    const int flags = 0; /* always zero (0) */

    // CryptoObjectHelper is described in the previous section.
    CryptoObjectHelper cryptoHelper = new CryptoObjectHelper();    
    
    // cancellationSignal can be used to manually stop the fingerprint scanner. 
    cancellationSignal = new Android.Support.V4.OS.CancellationSignal();
    
    FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(this);
    
    // AuthenticationCallback is a base class that will be covered later on in this guide.
    FingerprintManagerCompat.AuthenticationCallback authenticationCallback = new MyAuthCallbackSample(this);

    // Start the fingerprint scanner.
    fingerprintManager.Authenticate(cryptoHelper.BuildCryptoObject(), flags, cancellationSignal, authenticationCallback, null);
}
```

Discutons de chacun de ces `Authenticate` paramètres dans la méthode un peu plus en détail:

- Le premier paramètre est un objet _crypto_ que le scanner d’empreintes digitales utilisera pour aider à authentifier les résultats d’une analyse d’empreintes digitales. Cet objet `null`peut être , auquel cas l’application doit faire confiance aveuglément que rien n’a altéré les résultats de l’empreinte digitale. Il est recommandé `CryptoObject` d’être instantané et `FingerprintManager` fourni au plutôt que nul. [La création d’un CryptObject](~/android/platform/fingerprint-authentication/creating-a-cryptoobject.md) expliquera `CryptoObject` en détail `Cipher`comment instantané un basé sur un .
- Le deuxième paramètre est toujours nul. La documentation Android identifie cela comme un ensemble de drapeaux et est très probablement réservé pour une utilisation future. 
- Le troisième `cancellationSignal` paramètre est un objet utilisé pour éteindre le scanner d’empreintes digitales et annuler la demande en cours. Il s’agit [d’un Android AnnulationSignal](https://developer.android.com/reference/android/os/CancellationSignal.html), et non pas un type à partir du cadre .NET.
- Le quatrième paramètre est obligatoire et est `AuthenticationCallback` une classe qui sous-classe la classe abstraite. Les méthodes de cette classe seront invoquées `FingerprintManager` pour signaler aux clients quand le a terminé et quels sont les résultats. Comme il ya beaucoup à comprendre `AuthenticationCallback`sur la mise en œuvre de la , il sera couvert dans [sa propre section](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md).
- Le cinquième paramètre `Handler` est une instance facultative. Si `Handler` un objet est `FingerprintManager` fourni, `Looper` l’utilisera à partir de cet objet lors du traitement des messages à partir du matériel d’empreintes digitales. Typiquement, on n’a `Handler`pas besoin de fournir `Looper` un , l’fingerprintManager utilisera l’application.

## <a name="cancelling-a-fingerprint-scan"></a>Annulation d’une analyse d’empreintes digitales

Il peut être nécessaire pour l’utilisateur (ou l’application) d’annuler l’analyse d’empreintes digitales après qu’il a été lancé. Dans cette situation, [`IsCancelled`](https://developer.android.com/reference/android/os/CancellationSignal.html#isCanceled()) invoquez la méthode sur celle [`CancellationSignal`](https://developer.android.com/reference/android/os/CancellationSignal.html) qui a été fournie à `FingerprintManager.Authenticate` l’époque où elle a été invoquée pour commencer l’analyse des empreintes digitales.

Maintenant que nous `Authenticate` avons vu la méthode, examinons certains des paramètres les plus importants plus en détail. Tout d’abord, nous allons examiner [la réponse aux rappels d’authentification](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md), qui discutera de la façon de sous-classer [l’FingerprintManager.AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html), permettant à une application Android de réagir aux résultats fournis par le scanner d’empreintes digitales.

## <a name="related-links"></a>Liens connexes

- [AnnulationSignal](https://developer.android.com/reference/android/os/CancellationSignal.html)
- [FingerprintManager.AuthenticationCallback (en anglais)](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [FingerprintManager.CryptoObject](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat.CryptoObject](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [FingerprintManager (en anglais)](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
