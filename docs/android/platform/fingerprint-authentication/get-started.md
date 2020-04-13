---
title: Démarrer avec l’authentification des empreintes digitales
ms.prod: xamarin
ms.assetid: 7BACCB36-8E3E-4E5D-B8EF-56A639839FD2
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/17/2018
ms.openlocfilehash: 746a096f93036e63b29bc917826259f88426cead
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020279"
---
# <a name="getting-started-with-fingerprint-authentication"></a>Démarrer avec l’authentification des empreintes digitales

Pour commencer, nous allons d’abord couvrir la façon de configurer un projet Xamarin.Android afin que l’application est en mesure d’utiliser l’authentification des empreintes digitales:

1. Mettez à jour **AndroidManifest.xml** pour déclarer les autorisations dont les API empreintes digitales ont besoin.
2. Obtenir une référence `FingerprintManager`à la .
3. Vérifiez que l’appareil est capable de scanner les empreintes digitales.

## <a name="requesting-permissions-in-the-application-manifest"></a>Demande d’autorisation dans le Manifeste de la demande

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Une application Android `USE_FINGERPRINT` doit demander la permission dans le manifeste. La capture d’écran suivante montre comment ajouter cette autorisation à l’application dans Visual Studio:

[![Activer\_USE FINGERPRINT dans l’écran Android Manifest](get-started-images/fingerprint-01-vs.png)](get-started-images/fingerprint-01-vs.png#lightbox) 

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Une application Android `USE_FINGERPRINT` doit demander la permission dans le manifeste. La capture d’écran suivante montre comment ajouter cette autorisation à l’application dans Visual Studio pour Mac:

[![Activer UseFingerprint dans l’écran d’application Android](get-started-images/fingerprint-01-xs.png)](get-started-images/fingerprint-01-xs.png#lightbox) 

-----

## <a name="getting-an-instance-of-the-fingerprintmanager"></a>Obtenir une instance de l’fingerprintManager

Ensuite, l’application doit obtenir `FingerprintManager` une `FingerprintManagerCompat` instance de la ou de la classe. Pour être compatible avec les anciennes versions d’Android, une application Android doit utiliser l’API de compatibilité trouvée dans le forfait Android Support v4 NuGet. L’extrait suivant montre comment obtenir l’objet approprié à partir du système d’exploitation : 

```csharp
// Using the Android Support Library v4
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);

// Using API level 23:
FingerprintManager fingerprintManager = context.GetSystemService(Context.FingerprintService) as FingerprintManager;
```  

Dans l’extrait précédent, `context` le est `Android.Content.Context`tout Android . Typiquement, c’est l’activité qui effectue l’authentification.

## <a name="checking-for-eligibility"></a>Vérification de l’admissibilité

Une application doit effectuer plusieurs vérifications pour s’assurer qu’il est possible d’utiliser l’authentification des empreintes digitales. Au total, il y a cinq conditions que la demande utilise pour vérifier l’admissibilité :  

**API niveau 23** &ndash; Les API d’empreintes digitales nécessitent un niveau d’API 23 ou plus. Le `FingerprintManagerCompat` cours enveloppera la vérification de niveau API pour vous. Pour cette raison, il est recommandé d’utiliser l’Android **Support Library v4** et `FingerprintManagerCompat`; cela expliquera l’un de ces contrôles.

**Matériel** &ndash; Lorsque l’application démarre pour la première fois, elle doit vérifier la présence d’un scanner d’empreintes digitales :

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.IsHardwareDetected)
{
    // Code omitted
}
```

**Appareil sécurisé** &ndash; L’utilisateur doit avoir l’appareil sécurisé avec un verrouillage d’écran. Si l’utilisateur n’a pas sécurisé l’appareil avec un verrou d’écran et la sécurité est importante pour l’application, alors l’utilisateur doit être informé qu’un verrouillage d’écran doit être configuré. L’extrait de code suivant montre comment vérifier cette pré-requis:

```csharp
KeyguardManager keyguardManager = (KeyguardManager) GetSystemService(KeyguardService);
if (!keyguardManager.IsKeyguardSecure)
{
}
```

**Empreintes digitales inscrites** &ndash; L’utilisateur doit avoir au moins une empreinte digitale enregistrée auprès du système d’exploitation. Cette vérification d’autorisation doit avoir lieu avant chaque tentative d’authentification :

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.HasEnrolledFingerprints)
{
    // Can't use fingerprint authentication - notify the user that they need to
    // enroll at least one fingerprint with the device.
}
```

**Autorisations** &ndash; L’application doit demander la permission de l’utilisateur avant d’utiliser l’application. Pour Android 5.0 et moins, l’utilisateur accorde l’autorisation comme condition d’installation de l’application. Android 6.0 a introduit un nouveau modèle d’autorisation qui vérifie les autorisations à l’heure de fonctionnement. Cet extrait de code est un exemple de la façon de vérifier les autorisations sur Android 6.0:

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

Vérifier toutes ces conditions chaque fois que l’application offre des options d’authentification s’assurera que l’utilisateur obtient la meilleure expérience utilisateur. Les modifications ou mises à niveau de leur appareil ou de leur système d’exploitation peuvent affecter la disponibilité de l’authentification des empreintes digitales. Si vous choisissez de mettre en cache les résultats de l’un de ces contrôles, assurez-vous de répondre aux scénarios de mise à niveau.

Pour plus d’informations sur la façon de demander des autorisations dans Android 6.0, consultez le guide Android [Demandant des autorisations à Run-Time](https://developer.android.com/training/permissions/requesting.html).

## <a name="related-links"></a>Liens connexes

- [Context](xref:Android.Content.Context)
- [KeyguardManager (KeyguardManager)](xref:Android.App.KeyguardManager)
- [ContexteCompat ContextCompat](https://developer.android.com/reference/android/support/v4/content/ContextCompat)
- [FingerprintManager (en anglais)](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
- [Demande d’autorisations à Run-Time](https://developer.android.com/training/permissions/requesting.html)
