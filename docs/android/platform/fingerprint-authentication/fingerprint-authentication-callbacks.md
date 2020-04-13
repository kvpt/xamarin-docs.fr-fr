---
title: Réponse aux rappels d’authentification
ms.prod: xamarin
ms.assetid: 6533AFC9-1A1C-4897-A154-4D4ECFE27761
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/06/2017
ms.openlocfilehash: 8dc06740355bd95828e1a1bd8d9d15a2ef37e6b2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027531"
---
# <a name="responding-to-authentication-callbacks"></a>Réponse aux rappels d’authentification

Le scanner d’empreintes digitales fonctionne en arrière-plan sur son propre thread, et quand `FingerprintManager.AuthenticationCallback` il est terminé, il signalera les résultats de l’analyse en invoquant une méthode de sur le fil d’interface utilisateur. Une application Android doit fournir son propre gestionnaire qui étend cette classe abstraite, mettant en œuvre toutes les méthodes suivantes:

- **`OnAuthenticationError(int errorCode, ICharSequence errString)`**&ndash; Appelé quand il ya une erreur irrécupérable. Il n’y a rien de plus qu’une application ou un utilisateur peut faire pour corriger la situation, sauf éventuellement essayer à nouveau.
- **`OnAuthenticationFailed()`**&ndash; Cette méthode est invoquée lorsqu’une empreinte digitale a été détectée mais non reconnue par l’appareil.
- **`OnAuthenticationHelp(int helpMsgId, ICharSequence helpString)`**&ndash; Appelé quand il ya une erreur récupérable, comme le doigt étant glissé à jeûner sur le scanner.
- **`OnAuthenticationSucceeded(FingerprintManagerCompati.AuthenticationResult result)`**&ndash; C’est ce qu’on appelle lorsqu’une empreinte digitale a été reconnue.

Si `CryptoObject` un a `Authenticate`été utilisé lors `Cipher.DoFinal` de `OnAuthenticationSuccessful`l’appel , il est recommandé d’appeler .
`DoFinal`jettera une exception si le chiffrement a été trafiqué ou mal initialisé, ce qui indique que le résultat du scanner d’empreintes digitales peut avoir été altéré à l’extérieur de l’application.

> [!NOTE]
> Il est recommandé de maintenir la classe de rappel relativement léger et exempt de logique spécifique à l’application. Les rappels doivent agir comme un «flic de la circulation» entre l’application Android et les résultats du scanner d’empreintes digitales.

## <a name="a-sample-authentication-callback-handler"></a>Un gestionnaire de rappel d’authentification d’échantillon

La classe suivante est un `FingerprintManager.AuthenticationCallback` exemple d’une mise en œuvre minimale : 

```csharp
class MyAuthCallbackSample : FingerprintManagerCompat.AuthenticationCallback
{
    // Can be any byte array, keep unique to application.
    static readonly byte[] SECRET_BYTES = {1, 2, 3, 4, 5, 6, 7, 8, 9};
    // The TAG can be any string, this one is for demonstration.
    static readonly string TAG = "X:" + typeof (SimpleAuthCallbacks).Name;

    public MyAuthCallbackSample()
    {
    }

    public override void OnAuthenticationSucceeded(FingerprintManagerCompat.AuthenticationResult result)
    {
        if (result.CryptoObject.Cipher != null) 
        {
            try
            {
                // Calling DoFinal on the Cipher ensures that the encryption worked.
                byte[] doFinalResult = result.CryptoObject.Cipher.DoFinal(SECRET_BYTES);
    
                // No errors occurred, trust the results.              
            }
            catch (BadPaddingException bpe)
            {
                // Can't really trust the results.
                Log.Error(TAG, "Failed to encrypt the data with the generated key." + bpe);
            }
            catch (IllegalBlockSizeException ibse)
            {
                // Can't really trust the results.
                Log.Error(TAG, "Failed to encrypt the data with the generated key." + ibse);
            }
        }
        else
        {
            // No cipher used, assume that everything went well and trust the results.
        }
    }

    public override void OnAuthenticationError(int errMsgId, ICharSequence errString)
    {
        // Report the error to the user. Note that if the user canceled the scan,
        // this method will be called and the errMsgId will be FingerprintState.ErrorCanceled.
    }

    public override void OnAuthenticationFailed()
    {
        // Tell the user that the fingerprint was not recognized.
    }

    public override void OnAuthenticationHelp(int helpMsgId, ICharSequence helpString)
    {
        // Notify the user that the scan failed and display the provided hint.
    }
}
```

`OnAuthenticationSucceeded`pour voir si `Cipher` un `FingerprintManager` a `Authentication` été fourni à quand a été invoqué. Si c’est le cas, la `DoFinal` méthode est appelée sur le chiffrement. Cela ferme `Cipher`le , le restaurer à son état d’origine. S’il y avait un problème `DoFinal` avec le chiffrement, alors jettera une exception et la tentative d’authentification devrait être considérée comme ayant échoué.

Les `OnAuthenticationError` `OnAuthenticationHelp` rappels et les rappels reçoivent chacun un integer indiquant quel était le problème. La section suivante explique chacun des codes d’aide ou d’erreur possibles. Les deux rappels servent &ndash; des fins similaires pour informer l’application que l’authentification des empreintes digitales a échoué. La façon dont ils diffèrent est dans la sévérité. `OnAuthenticationHelp`est une erreur récupérable de l’utilisateur, comme glisser l’empreinte digitale trop rapidement; `OnAuthenticationError` est plus une erreur grave, comme un scanner d’empreintes digitales endommagé.

Notez `OnAuthenticationError` que sera invoqué lorsque l’analyse `CancellationSignal.Cancel()` d’empreintes digitales est annulée par le message. Le `errMsgId` paramètre aura la valeur`FingerprintState.ErrorCanceled`de 5 ( ). Selon les exigences, une `AuthenticationCallbacks` mise en œuvre de la peut traiter cette situation différemment des autres erreurs. 

`OnAuthenticationFailed`est invoqué lorsque l’empreinte digitale a été numérisée avec succès, mais ne correspondait à aucune empreinte digitale inscrite avec l’appareil. 

## <a name="help-codes-and-error-message-ids"></a>Codes d’aide et ids de message d’erreur 

Une liste et une description des codes d’erreur et des codes d’aide peuvent être trouvées dans la [documentation Android SDK](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html#FINGERPRINT_ACQUIRED_GOOD) pour la classe FingerprintManager. Xamarin.Android représente ces `Android.Hardware.Fingerprints.FingerprintState` valeurs avec l’enum:

- **`AcquiredGood`**&ndash; (valeur 0) L’image acquise était bonne.

- **`AcquiredImagerDirty`**&ndash; (valeur 3) L’image d’empreinte digitale était trop bruyante en raison de la saleté suspectée ou détectée sur le capteur. Par exemple, il est raisonnable de `AcquiredInsufficient` retourner cela après la détection multiple ou réelle de la saleté sur le capteur (pixels coincés, swaths, etc.). On s’attend à ce que l’utilisateur prenne des mesures pour nettoyer le capteur lorsque cela est retourné.

- **`AcquiredInsufficient`**&ndash; (valeur 2) L’image d’empreinte digitale était trop bruyante pour traiter en raison d’une condition détectée (c.-à-d. peau sèche) ou d’un capteur éventuellement sale (voir `AcquiredImagerDirty`.

- **`AcquiredPartial`**&ndash; (valeur 1) Seule une image partielle d’empreinte digitale a été détectée. Pendant l’inscription, l’utilisateur doit être informé de ce qui &ldquo;doit arriver pour résoudre ce problème, par exemple, appuyez fermement sur le capteur.&rdquo;

- **`AcquiredTooFast`**&ndash; (valeur 5) L’image d’empreinte digitale était incomplète en raison d’un mouvement rapide. Bien que la plupart du temps approprié pour les capteurs de tableau linéaire, cela pourrait également se produire si le doigt a été déplacé lors de l’acquisition. Il faut demander à l’utilisateur de déplacer le doigt plus lentement (linéaire) ou de laisser le doigt sur le capteur plus longtemps.

- **`AcquiredToSlow`**&ndash; (valeur 4) L’image d’empreinte digitale était illisible en raison d’un manque de mouvement. Ceci est plus approprié pour les capteurs de tableau linéaire qui nécessitent un mouvement de balayage.

- **`ErrorCanceled`**&ndash; (valeur 5) L’opération a été annulée parce que le capteur d’empreintes digitales n’est pas disponible. Par exemple, cela peut se produire lorsque l’utilisateur est commuté, l’appareil est verrouillé, ou une autre opération en cours l’empêche ou le désactive.

- **`ErrorHwUnavailable`**&ndash; (valeur 1) Le matériel n’est pas disponible. Réessayez plus tard.

- **`ErrorLockout`**&ndash; (valeur 7) L’opération a été annulée parce que l’API est verrouillée en raison de trop de tentatives.

- **`ErrorNoSpace`**&ndash; (valeur 4) État d’erreur retourné pour des opérations comme l’inscription; l’opération ne peut pas être terminée parce qu’il ne reste pas assez de stockage pour terminer l’opération.

- **`ErrorTimeout`**&ndash; (valeur 3) État d’erreur retourné lorsque la demande actuelle a été en cours d’exécution trop longtemps. Ceci vise à empêcher les programmes d’attendre le capteur d’empreintes digitales indéfiniment. Le délai d’attente est spécifique à la plate-forme et au capteur, mais il est généralement d’environ 30 secondes.

- **`ErrorUnableToProcess`**&ndash; (valeur 2) L’état d’erreur est revenu lorsque le capteur n’a pas été en mesure de traiter l’image actuelle.

## <a name="related-links"></a>Liens connexes

- [Cipher](https://docs.oracle.com/javase/7/docs/api/javax/crypto/Cipher.html)
- [AuthentificationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [AuthentificationCallback](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.AuthenticationCallback.html)
