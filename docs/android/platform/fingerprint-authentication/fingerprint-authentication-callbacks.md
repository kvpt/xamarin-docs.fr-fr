---
title: Réponse aux rappels d’authentification
ms.prod: xamarin
ms.assetid: 6533AFC9-1A1C-4897-A154-4D4ECFE27761
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/06/2017
ms.openlocfilehash: f1fc484931ba7a574ac660b4856f20b1cb1e08a3
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70119585"
---
# <a name="responding-to-authentication-callbacks"></a>Réponse aux rappels d’authentification

Le scanneur d’empreintes digitales s’exécute en arrière-plan sur son propre thread et, lorsqu’il est terminé, il signale les résultats de l' `FingerprintManager.AuthenticationCallback` analyse en appelant une méthode de sur le thread d’interface utilisateur. Une application Android doit fournir son propre gestionnaire qui étend cette classe abstraite, en implémentant toutes les méthodes suivantes:

- **`OnAuthenticationError(int errorCode, ICharSequence errString)`** &ndash; Appelé en cas d’erreur irrécupérable. Il n’y a rien d’autre qu’une application ou un utilisateur ne peut faire pour corriger la situation, sauf s’il est possible de réessayer.
- **`OnAuthenticationFailed()`** &ndash; Cette méthode est appelée lorsqu’une empreinte digitale a été détectée mais qu’elle n’est pas reconnue par l’appareil.
- **`OnAuthenticationHelp(int helpMsgId, ICharSequence helpString)`** &ndash; Appelée lorsqu’il y a une erreur récupérable, telle que le doigt en cours de balayage sur le scanneur.
- **`OnAuthenticationSucceeded(FingerprintManagerCompati.AuthenticationResult result)`** &ndash; Cette méthode est appelée lorsqu’une empreinte digitale a été reconnue.

Si un `CryptoObject` a été utilisé lors `Authenticate`de l’appel de, il `Cipher.DoFinal` est `OnAuthenticationSuccessful`recommandé d’appeler dans.
`DoFinal`lèvera une exception si le chiffrement a été falsifié ou mal initialisé, indiquant que le résultat du scanneur d’empreintes digitales a peut-être été falsifié en dehors de l’application.


> [!NOTE]
> Il est recommandé de conserver la classe de rappel relativement légère et exempte de logique spécifique à l’application. Les rappels doivent agir comme un «trafic COP» entre l’application Android et les résultats du scanneur d’empreintes digitales.

## <a name="a-sample-authentication-callback-handler"></a>Exemple de gestionnaire de rappel d’authentification

La classe suivante est un exemple d’implémentation minimale `FingerprintManager.AuthenticationCallback` : 

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

`OnAuthenticationSucceeded`vérifie si un `Cipher` a été fourni à `FingerprintManager` quand `Authentication` a été appelé. Si c’est le `DoFinal` cas, la méthode est appelée sur le chiffrement. Cela ferme le `Cipher`, en le restaurant à son état d’origine. En cas de problème avec le chiffrement, `DoFinal` lève une exception et la tentative d’authentification doit être considérée comme ayant échoué.

Les `OnAuthenticationError` rappels et `OnAuthenticationHelp` reçoivent chacun un entier indiquant le problème. La section suivante explique chaque code d’aide ou d’erreur possible. Les deux rappels servent des objectifs &ndash; similaires pour informer l’application de l’échec de l’authentification par empreinte digitale. Leur différence est de niveau de gravité. `OnAuthenticationHelp`est une erreur récupérable par l’utilisateur, telle que le balayage trop rapide de l’empreinte digitale; `OnAuthenticationError` est plus une erreur grave, telle qu’un scanneur d’empreintes digitales endommagé.

Notez que `OnAuthenticationError` est appelé lors de l’annulation de l’analyse par empreinte digitale `CancellationSignal.Cancel()` par le biais du message. Le `errMsgId` paramètre aura la valeur 5 (`FingerprintState.ErrorCanceled`). Selon les exigences, une implémentation du `AuthenticationCallbacks` peut traiter cette situation différemment des autres erreurs. 

`OnAuthenticationFailed`est appelé lorsque l’empreinte digitale a été analysée avec succès, mais ne correspond à aucune empreinte digitale inscrite auprès de l’appareil. 

## <a name="help-codes-and-error-message-ids"></a>Codes d’aide et ID de message d’erreur 

Une liste et une description des codes d’erreur et des codes d’aide se trouvent dans la [documentation Android SDK](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html#FINGERPRINT_ACQUIRED_GOOD) de la classe FingerprintManager. Xamarin. Android représente ces valeurs avec l' `Android.Hardware.Fingerprints.FingerprintState` énumération:


- **`AcquiredGood`** &ndash; (valeur 0) l’image acquise était bonne.


- **`AcquiredImagerDirty`** &ndash; (valeur 3) l’image de l’empreinte digitale était trop bruyante en raison de la poussière suspectée ou détectée sur le capteur. Par exemple, il est raisonnable de retourner cette valeur après `AcquiredInsufficient` plusieurs ou une détection réelle des saletés sur le capteur (pixels bloqués, quantités, etc.). L’utilisateur doit agir pour nettoyer le capteur lorsque ce dernier est retourné.


- **`AcquiredInsufficient`** (valeur 2) l’image de l’empreinte digitale était trop bruyante pour être traité en raison d’une condition détectée (par exemple, une apparence sèche) ou d’un capteur éventuellement sale (consultez `AcquiredImagerDirty`. &ndash;



- **`AcquiredPartial`** &ndash; (valeur 1) seule une image d’empreinte digitale partielle a été détectée. Au cours de l’inscription, l’utilisateur doit être informé de ce qui doit se produire pour résoudre ce problème, par &ldquo;exemple, appuyez fermement sur le capteur.&rdquo;



- **`AcquiredTooFast`** &ndash; (valeur 5) l’image de l’empreinte digitale était incomplète en raison d’un mouvement rapide. Bien qu’elles soient essentiellement appropriées pour les capteurs de tableau linéaire, cela peut également se produire si le doigt a été déplacé pendant l’acquisition. L’utilisateur doit être invité à déplacer le doigt plus lentement (linéaire) ou à conserver le doigt sur le capteur plus longtemps.




- **`AcquiredToSlow`** &ndash; (valeur 4) l’image de l’empreinte digitale était illisible en raison d’un manque de mouvement. Cela est le plus approprié pour les capteurs de matrice linéaire qui nécessitent un mouvement de balayage.



- **`ErrorCanceled`** &ndash; (valeur 5) l’opération a été annulée, car le capteur d’empreintes digitales n’est pas disponible. Par exemple, cela peut se produire lorsque l’utilisateur est basculé, que l’appareil est verrouillé ou qu’une autre opération en attente l’empêche ou le désactive.



- **`ErrorHwUnavailable`** &ndash; (valeur 1) le matériel n’est pas disponible. Réessayez ultérieurement.




- **`ErrorLockout`** &ndash; (valeur 7) l’opération a été annulée, car l’API est verrouillée en raison d’un trop grand nombre de tentatives.




- **`ErrorNoSpace`** &ndash; (valeur 4) état d’erreur retourné pour les opérations telles que l’inscription; l’opération ne peut pas être effectuée car le stockage restant est insuffisant pour terminer l’opération.



- **`ErrorTimeout`** &ndash; (valeur 3) état d’erreur retourné lorsque la requête actuelle a été exécutée trop longtemps. Cela a pour but d’empêcher les programmes d’attendre indéfiniment le capteur d’empreintes digitales. Le délai d’attente est spécifique à la plateforme et au capteur, mais est généralement d’environ 30 secondes.



- **`ErrorUnableToProcess`** &ndash; (valeur 2) état d’erreur renvoyé lorsque le capteur n’a pas pu traiter l’image actuelle.



## <a name="related-links"></a>Liens associés

- [Cipher](https://docs.oracle.com/javase/7/docs/api/javax/crypto/Cipher.html)
- [AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [AuthenticationCallback](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.AuthenticationCallback.html)
