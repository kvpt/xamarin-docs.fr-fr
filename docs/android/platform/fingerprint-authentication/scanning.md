---
title: Analyse des empreintes digitales
ms.prod: xamarin
ms.assetid: 1CDDC096-77E0-47B3-BE0B-8953E2DDACD3
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/23/2016
ms.openlocfilehash: 4ead912b55790caf3e2e1f22e149f5682e6bb697
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70761220"
---
# <a name="scanning-for-fingerprints"></a>Analyse des empreintes digitales

Maintenant que nous avons vu comment préparer une application Xamarin. Android pour utiliser l’authentification par empreinte digitale, revenons à `FingerprintManager.Authenticate` la méthode et discutons de sa place dans l’authentification par empreinte digitale Android 6,0. Une vue d’ensemble rapide du flux de travail pour l’authentification par empreinte digitale est décrite dans la liste suivante :

1. Appeler `FingerprintManager.Authenticate`, en passant `CryptoObject` une et `FingerprintManager.AuthenticationCallback` une instance de. `CryptoObject` Est utilisé pour s’assurer que le résultat de l’authentification par empreinte digitale n’a pas été falsifié. 
2. Sous-classez la classe [FingerprintManager. AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html) . Une instance de cette classe sera fournie lors du `FingerprintManager` démarrage de l’authentification par empreinte digitale. Lorsque le scanneur d’empreintes digitales est terminé, il appellera l’une des méthodes de rappel sur cette classe.
3. Écrivez du code pour mettre à jour l’interface utilisateur afin de permettre à l’utilisateur de savoir que l’appareil a démarré le scanneur d’empreintes digitales et qu’il attend une intervention de l’utilisateur. 
4. Lorsque le scanneur d’empreintes digitales est terminé, Android retourne les résultats à l’application en appelant une `FingerprintManager.AuthenticationCallback` méthode sur l’instance fournie à l’étape précédente.
5. L’application informe l’utilisateur des résultats de l’authentification par empreinte digitale et réagit aux résultats en fonction des besoins. 

L’extrait de code suivant est un exemple de méthode dans une activité qui commence à analyser les empreintes digitales :

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

Nous allons aborder chacun de ces paramètres dans la `Authenticate` méthode plus en détail :

- Le premier paramètre est un objet de _chiffrement_ qui sera utilisé par le scanneur d’empreintes digitales pour authentifier les résultats d’une analyse d’empreintes digitales. Cet objet peut être `null`, auquel cas l’application doit faire confiance à la non-falsification des résultats de l’empreinte digitale. Il est recommandé `CryptoObject` d’instancier et de fournir à la place `FingerprintManager` de la valeur null. La [création d’un CryptObject](~/android/platform/fingerprint-authentication/creating-a-cryptoobject.md) explique en détail comment instancier `CryptoObject` un basé sur `Cipher`un.
- Le deuxième paramètre est toujours égal à zéro. La documentation Android identifie cela comme un ensemble d’indicateurs et est probablement réservée à une utilisation ultérieure. 
- Le troisième paramètre, `cancellationSignal` est un objet utilisé pour désactiver le scanneur d’empreintes digitales et annuler la requête actuelle. Il s’agit d’un [CancellationSignal Android](https://developer.android.com/reference/android/os/CancellationSignal.html), et non d’un type de .NET Framework.
- Le quatrième paramètre est obligatoire et est une classe qui sous-classe la `AuthenticationCallback` classe abstraite. Les méthodes de cette classe sont appelées pour signaler aux clients lorsque le `FingerprintManager` est terminé et ce que sont les résultats. Comme il y a beaucoup à comprendre sur l' `AuthenticationCallback`implémentation de, elle sera traitée dans [sa section](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md).
- Le cinquième paramètre est une instance `Handler` facultative. Si un `Handler` objet est fourni, le `FingerprintManager` utilise le `Looper` de cet objet lors du traitement des messages à partir du matériel d’empreinte digitale. En règle générale, il n’est pas nécessaire `Handler`de fournir un, le FingerprintManager `Looper` utilise à partir de l’application.

## <a name="cancelling-a-fingerprint-scan"></a>Annulation d’une analyse d’empreinte digitale

Il peut être nécessaire que l’utilisateur (ou l’application) annule l’analyse d’empreintes digitales après son lancement. Dans ce cas, appelez la [`IsCancelled`](https://developer.android.com/reference/android/os/CancellationSignal.html#isCanceled()) méthode sur le [`CancellationSignal`](https://developer.android.com/reference/android/os/CancellationSignal.html) qui a été fourni `FingerprintManager.Authenticate` à lorsqu’il a été appelé pour démarrer l’analyse d’empreintes digitales.

Maintenant que nous avons vu la `Authenticate` méthode, examinons certains des paramètres les plus importants plus en détail. Tout d’abord, nous allons nous pencher sur les [rappels d’authentification](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md), qui expliquent comment sous-traiter [FingerprintManager. AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html), ce qui permet à une application Android de réagir aux résultats fournis par le scanneur d’empreintes digitales.

## <a name="related-links"></a>Liens associés

- [CancellationSignal](https://developer.android.com/reference/android/os/CancellationSignal.html)
- [FingerprintManager.AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [FingerprintManager.CryptoObject](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat.CryptoObject](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
