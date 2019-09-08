---
title: Création d’un CryptoObject
ms.prod: xamarin
ms.assetid: 4D159B80-FFF4-4136-A7F0-F8A5C6B86838
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 7328792e0d921beb09389d9a0400ea97766b2246
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756447"
---
# <a name="creating-a-cryptoobject"></a>Création d’un CryptoObject

L’intégrité des résultats de l’authentification par empreinte digitale est importante &ndash; pour une application. il s’agit de la façon dont l’application connaît l’identité de l’utilisateur. Il est théoriquement possible pour les logiciels malveillants tiers d’intercepter et de falsifier les résultats retournés par le scanneur d’empreintes digitales. Cette section décrit une technique permettant de préserver la validité des résultats de l’empreinte digitale. 

Est un wrapper autour des API de chiffrement Java et est utilisé `FingerprintManager` par pour protéger l’intégrité de la demande d’authentification. `FingerprintManager.CryptoObject` En règle générale `Javax.Crypto.Cipher` , un objet est le mécanisme de chiffrement des résultats du scanneur d’empreintes digitales. L' `Cipher` objet lui-même utilise une clé créée par l’application à l’aide des API du magasin de clés Android.

Pour comprendre comment ces classes fonctionnent ensemble, commençons par examiner le code suivant, qui montre comment créer un `CryptoObject`, puis expliquer plus en détail :

```csharp
public class CryptoObjectHelper
{
    // This can be key name you want. Should be unique for the app.
    static readonly string KEY_NAME = "com.xamarin.android.sample.fingerprint_authentication_key";

    // We always use this keystore on Android.
    static readonly string KEYSTORE_NAME = "AndroidKeyStore";

    // Should be no need to change these values.
    static readonly string KEY_ALGORITHM = KeyProperties.KeyAlgorithmAes;
    static readonly string BLOCK_MODE = KeyProperties.BlockModeCbc;
    static readonly string ENCRYPTION_PADDING = KeyProperties.EncryptionPaddingPkcs7;
    static readonly string TRANSFORMATION = KEY_ALGORITHM + "/" +
                                            BLOCK_MODE + "/" +
                                            ENCRYPTION_PADDING;
    readonly KeyStore _keystore;

    public CryptoObjectHelper()
    {
        _keystore = KeyStore.GetInstance(KEYSTORE_NAME);
        _keystore.Load(null);
    }

    public FingerprintManagerCompat.CryptoObject BuildCryptoObject()
    {
        Cipher cipher = CreateCipher();
        return new FingerprintManagerCompat.CryptoObject(cipher);
    }

    Cipher CreateCipher(bool retry = true)
    {
        IKey key = GetKey();
        Cipher cipher = Cipher.GetInstance(TRANSFORMATION);
        try
        {
            cipher.Init(CipherMode.EncryptMode | CipherMode.DecryptMode, key);
        } catch(KeyPermanentlyInvalidatedException e)
        {
            _keystore.DeleteEntry(KEY_NAME);
            if(retry)
            {
                CreateCipher(false);
            } else
            {
                throw new Exception("Could not create the cipher for fingerprint authentication.", e);
            }
        }
        return cipher;
    }

    IKey GetKey()
    {
        IKey secretKey;
        if(!_keystore.IsKeyEntry(KEY_NAME))
        {
            CreateKey();
        }

        secretKey = _keystore.GetKey(KEY_NAME, null);
        return secretKey;
    }

    void CreateKey()
    {
        KeyGenerator keyGen = KeyGenerator.GetInstance(KeyProperties.KeyAlgorithmAes, KEYSTORE_NAME);
        KeyGenParameterSpec keyGenSpec =
            new KeyGenParameterSpec.Builder(KEY_NAME, KeyStorePurpose.Encrypt | KeyStorePurpose.Decrypt)
                .SetBlockModes(BLOCK_MODE)
                .SetEncryptionPaddings(ENCRYPTION_PADDING)
                .SetUserAuthenticationRequired(true)
                .Build();
        keyGen.Init(keyGenSpec);
        keyGen.GenerateKey();
    }
}
```

L’exemple `Cipher` de code crée un pour chacun d' `CryptoObject`entre eux, à l’aide d’une clé créée par l’application. La clé est identifiée par `KEY_NAME` la variable qui a été définie au début de `CryptoObjectHelper` la classe. La méthode `GetKey` essaiera et récupérera la clé à l’aide des API du magasin de clés Android. Si la clé n’existe pas, la méthode `CreateKey` crée une nouvelle clé pour l’application.

Le chiffrement est instancié avec un appel à `Cipher.GetInstance`, en effectuant une _transformation_ (une valeur de chaîne qui indique au chiffrement comment chiffrer et déchiffrer les données). L’appel à `Cipher.Init` termine l’initialisation du chiffrement en fournissant une clé à partir de l’application. 

Il est important de comprendre que dans certaines situations, Android peut invalider la clé : 

- Une nouvelle empreinte digitale a été inscrite auprès de l’appareil.
- Aucune empreinte digitale n’est inscrite auprès de l’appareil.
- L’utilisateur a désactivé le verrouillage de l’écran.
- L’utilisateur a modifié le verrou d’écran (le type de screenlock ou le code PIN/modèle utilisé).

Dans ce cas, `Cipher.Init` lèvera une. [`KeyPermanentlyInvalidatedException`](https://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html) L’exemple de code ci-dessus va intercepter cette exception, supprimer la clé, puis en créer une nouvelle.

La section suivante explique comment créer la clé et la stocker sur l’appareil.

## <a name="creating-a-secret-key"></a>Création d’une clé secrète

La `CryptoObjectHelper` classe utilise Android [`KeyGenerator`](xref:Javax.Crypto.KeyGenerator) pour créer une clé et la stocker sur l’appareil. La `KeyGenerator` classe peut créer la clé, mais nécessite des métadonnées sur le type de clé à créer. Ces informations sont fournies par une instance de la [`KeyGenParameterSpec`](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html) classe. 

Un `KeyGenerator` est instancié à l’aide `GetInstance` de la méthode de fabrique. L’exemple de code utilise le [_Advanced Encryption Standard_](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) (_AES_) comme algorithme de chiffrement. AES rompt les données dans des blocs d’une taille fixe et chiffrent chacun de ces blocs.

Ensuite, un `KeyGenParameterSpec` est créé à l' `KeyGenParameterSpec.Builder`aide de. `KeyGenParameterSpec.Builder` Encapsule les informations suivantes sur la clé qui doit être créée :

- Le nom de la clé.
- La clé doit être valide pour le chiffrement et le déchiffrement.
- Dans l’exemple de code `BLOCK_MODE` , le est défini sur le chaînage de`KeyProperties.BlockModeCbc` _blocs de chiffrement_ (), ce qui signifie que chaque bloc est XOR avec le bloc précédent (créant des dépendances entre chaque bloc). 
- Le `CryptoObjectHelper` utilise la norme_PKCS7_( [_Public Key Cryptography standard #7_](https://tools.ietf.org/html/rfc2315) ) pour générer les octets qui rempliront les blocs afin de garantir qu’ils ont tous la même taille.
- `SetUserAuthenticationRequired(true)`signifie que l’authentification de l’utilisateur est nécessaire pour pouvoir utiliser la clé.

Une fois `KeyGenParameterSpec` le créé, il est utilisé pour initialiser `KeyGenerator`, ce qui génère une clé et la stocke en toute sécurité sur l’appareil. 

## <a name="using-the-cryptoobjecthelper"></a>Utilisation de CryptoObjectHelper

Maintenant que l’exemple de code a encapsulé la plus grande partie de la logique `CryptoWrapper` pour créer `CryptoObjectHelper` un dans la classe, revenons au code à partir du début de `CryptoObjectHelper` ce guide et utilisez pour créer le chiffrement et démarrer un scanneur d’empreintes digitales : 

```csharp
protected void FingerPrintAuthenticationExample()
{
    const int flags = 0; /* always zero (0) */
    
    CryptoObjectHelper cryptoHelper = new CryptoObjectHelper();
    cancellationSignal = new Android.Support.V4.OS.CancellationSignal();
    
    // Using the Support Library classes for maximum reach
    FingerprintManagerCompat fingerPrintManager = FingerprintManagerCompat.From(this);
    
    // AuthCallbacks is a C# class defined elsewhere in code.
    FingerprintManagerCompat.AuthenticationCallback authenticationCallback = new MyAuthCallbackSample(this);

    // Here is where the CryptoObjectHelper builds the CryptoObject. 
    fingerprintManager.Authenticate(cryptohelper.BuildCryptoObject(), flags, cancellationSignal, authenticationCallback, null);
}
```

Maintenant que nous avons vu comment créer un `CryptoObject`, vous pouvez passer à la section Comment les `FingerprintManager.AuthenticationCallbacks` sont utilisés pour transférer les résultats du service de scanneur d’empreintes digitales à une application Android.

## <a name="related-links"></a>Liens associés

- [Cipher](xref:Javax.Crypto.Cipher)
- [FingerprintManager.CryptoObject](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat.CryptoObject](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [KeyGenerator](xref:Javax.Crypto.KeyGenerator)
- [KeyGenParameterSpec](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html)
- [KeyGenParameterSpec.Builder](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.Builder.html)
- [KeyPermanentlyInvalidatedException](https://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html)
- [KeyProperties](https://developer.android.com/reference/android/security/keystore/KeyProperties.html)
- [CHIFFRE](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)
- [RFC 2315-PCKS #7](https://tools.ietf.org/html/rfc2315)
