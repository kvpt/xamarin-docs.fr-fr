---
title: Prise en main avec l’authentification par empreinte digitale
ms.prod: xamarin
ms.assetid: 7BACCB36-8E3E-4E5D-B8EF-56A639839FD2
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/17/2018
ms.openlocfilehash: c433d4d7920b024795e2e8344b452e25d8f58cf4
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510648"
---
# <a name="getting-started-with-fingerprint-authentication"></a>Prise en main avec l’authentification par empreinte digitale

Pour commencer, commençons par aborder la configuration d’un projet Xamarin. Android afin que l’application puisse utiliser l’authentification par empreinte digitale:

1. Mettez à jour **fichier AndroidManifest. xml** pour déclarer les autorisations requises par les API d’empreintes digitales.
2. Obtenez une référence à `FingerprintManager`.
3. Vérifiez que l’appareil est capable d’effectuer une numérisation par empreinte digitale.

## <a name="requesting-permissions-in-the-application-manifest"></a>Demande d’autorisations dans le manifeste de l’application

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Une application Android doit demander l' `USE_FINGERPRINT` autorisation dans le manifeste. La capture d’écran suivante montre comment ajouter cette autorisation à l’application dans Visual Studio:

[![Activation de\_l’option utiliser l’empreinte digitale dans l’écran du manifeste Android](get-started-images/fingerprint-01-vs.png)](get-started-images/fingerprint-01-vs.png#lightbox) 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Une application Android doit demander l' `USE_FINGERPRINT` autorisation dans le manifeste. La capture d’écran suivante montre comment ajouter cette autorisation à l’application dans Visual Studio pour Mac:

[![Activation de UseFingerprint dans l’écran de l’application Android](get-started-images/fingerprint-01-xs.png)](get-started-images/fingerprint-01-xs.png#lightbox) 

-----

## <a name="getting-an-instance-of-the-fingerprintmanager"></a>Obtention d’une instance de FingerprintManager

Ensuite, l’application doit recevoir une instance de la `FingerprintManager` `FingerprintManagerCompat` classe ou. Pour être compatible avec les versions antérieures d’Android, une application Android doit utiliser les API de compatibilité trouvées dans le package NuGet support Android v4. L’extrait de code suivant montre comment récupérer l’objet approprié à partir du système d’exploitation: 

```csharp
// Using the Android Support Library v4
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);

// Using API level 23:
FingerprintManager fingerprintManager = context.GetSystemService(Context.FingerprintService) as FingerprintManager;
```  

Dans l’extrait de code précédent `context` , est un `Android.Content.Context`Android. Il s’agit généralement de l’activité qui effectue l’authentification.

## <a name="checking-for-eligibility"></a>Vérification de l’éligibilité

Une application doit effectuer plusieurs vérifications pour s’assurer qu’il est possible d’utiliser l’authentification par empreinte digitale. Au total, l’application utilise cinq conditions pour vérifier l’éligibilité:  

**Niveau d’API 23** &ndash; Les API Fingerprint requièrent le niveau d’API 23 ou une version ultérieure. La `FingerprintManagerCompat` classe encapsulera la vérification au niveau de l’API. C’est la raison pour laquelle il est recommandé d’utiliser la bibliothèque de `FingerprintManagerCompat` **prise en charge Android v4** et de prendre en compte l’un de ces contrôles.

**Matériel** &ndash; Lorsque l’application démarre pour la première fois, elle doit vérifier la présence d’un scanneur d’empreintes digitales:

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.IsHardwareDetected)
{
    // Code omitted
}
```

L' **appareil est sécurisé** &ndash; L’appareil doit être sécurisé par l’utilisateur à l’aide d’un verrouillage d’écran. Si l’utilisateur n’a pas sécurisé l’appareil avec un verrouillage d’écran et que la sécurité est importante pour l’application, l’utilisateur doit être averti qu’un verrou d’écran doit être configuré. L’extrait de code suivant montre comment vérifier ce requiste:

```csharp
KeyguardManager keyguardManager = (KeyguardManager) GetSystemService(KeyguardService);
if (!keyguardManager.IsKeyguardSecure)
{
}
```

**Empreintes digitales inscrites** &ndash; L’utilisateur doit avoir au moins une empreinte digitale inscrite auprès du système d’exploitation. Cette vérification d’autorisation doit se produire avant chaque tentative d’authentification:

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.HasEnrolledFingerprints)
{
    // Can't use fingerprint authentication - notify the user that they need to
    // enroll at least one fingerprint with the device.
}
```

**Autorisations** &ndash; L’application doit demander l’autorisation à l’utilisateur avant d’utiliser l’application. Pour Android 5,0 et versions antérieures, l’utilisateur accorde l’autorisation comme condition d’installation de l’application. Android 6,0 a introduit un nouveau modèle d’autorisation qui vérifie les autorisations au moment de l’exécution. Cet extrait de code est un exemple de la façon de vérifier les autorisations sur Android 6,0:

```csharp
// The context is typically a reference to the current activity.
Android.Content.PM.Permission permissionResult = ContextCompat.CheckSelfPermission(context, Manifest.Permission.UseFingerprint);
if (permissionResult == Android.Content.PM.Permission.Granted)
{
    // Permission granted - go ahead and start the fingerprint scanner.
}
else
{
    // No permission. Go and ask for permissions and don't start the scanner. See
    // https://developer.android.com/training/permissions/requesting.html
}
```

La vérification de toutes ces conditions chaque fois que l’application offre des options d’authentification garantit que l’utilisateur obtient la meilleure expérience utilisateur. Les modifications ou les mises à niveau de leur appareil ou du système d’exploitation peuvent affecter la disponibilité de l’authentification par empreinte digitale. Si vous choisissez de mettre en cache les résultats de l’une de ces vérifications, assurez-vous de répondre aux scénarios de mise à niveau.

Pour plus d’informations sur la façon de demander des autorisations dans Android 6,0, consultez le guide Android [demandant des autorisations au moment](https://developer.android.com/training/permissions/requesting.html)de l’exécution.

## <a name="related-links"></a>Liens associés

- [Contexte](xref:Android.Content.Context)
- [KeyguardManager](xref:Android.App.KeyguardManager)
- [ContextCompat](https://developer.android.com/reference/android/support/v4/content/ContextCompat)
- [FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
- [Demande d’autorisations au moment de l’exécution](https://developer.android.com/training/permissions/requesting.html)
