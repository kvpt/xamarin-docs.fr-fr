---
title: Création d’un CryptoObject
ms.prod: xamarin
ms.assetid: 4D159B80-FFF4-4136-A7F0-F8A5C6B86838
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 871058d1c128b37a0f2e77b43587139efb433de1
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "75487774"
---
# <a name="creating-a-cryptoobject"></a>Création d’un CryptoObject

L’intégrité des résultats d’authentification des empreintes digitales est importante pour une application, &ndash; c’est la façon dont l’application connaît l’identité de l’utilisateur. Il est théoriquement possible pour les logiciels malveillants tiers d’intercepter et de falsifier les résultats retournés par le scanner d’empreintes digitales. Cette section discutera d’une technique pour préserver la validité des résultats des empreintes digitales. 

Il `FingerprintManager.CryptoObject` s’agit d’un emballage autour des API `FingerprintManager` de cryptographie Java et est utilisé par le pour protéger l’intégrité de la demande d’authentification. Typiquement, `Javax.Crypto.Cipher` un objet est le mécanisme pour chiffrer les résultats du scanner d’empreintes digitales. L’objet `Cipher` lui-même utilisera une clé qui est créée par l’application à l’aide des API Android keystore.

Pour comprendre comment ces classes fonctionnent toutes ensemble, regardons d’abord le `CryptoObject`code suivant qui démontre comment créer un , puis expliquer plus en détail:

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
            cipher.Init(CipherMode.EncryptMode, key);
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

Le code de l’échantillon créera un nouveau `Cipher` pour chacun d’eux, `CryptoObject`à l’aide d’une clé qui a été créée par l’application. La clé est `KEY_NAME` identifiée par la variable qui `CryptoObjectHelper` a été définie au début de la classe. La `GetKey` méthode va essayer de récupérer la clé à l’aide des API Android Keystore. Si la clé n’existe `CreateKey` pas, alors la méthode créera une nouvelle clé pour l’application.

Le chiffrement est instantané avec `Cipher.GetInstance`un appel à , en prenant une _transformation_ (une valeur de chaîne qui indique au chiffrement comment chiffrer et déchiffrer les données). L’appel `Cipher.Init` à compléter l’initialisation du chiffrement en fournissant une clé de l’application. 

Il est important de se rendre compte qu’il ya des situations où Android peut invalider la clé: 

- Une nouvelle empreinte digitale a été inscrite avec l’appareil.
- Il n’y a pas d’empreintes digitales inscrites avec l’appareil.
- L’utilisateur a désactivé le verrou d’écran.
- L’utilisateur a changé la serrure de l’écran (le type de verrouillage ou le CODE/modèle utilisé).

Lorsque cela `Cipher.Init` se produit, jettera un [`KeyPermanentlyInvalidatedException`](https://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html). Le code d’échantillon ci-dessus emprisonnera cette exception, supprimera la clé, puis en créera une nouvelle.

La section suivante discutera de la façon de créer la clé et de la stocker sur l’appareil.

## <a name="creating-a-secret-key"></a>Création d’une clé secrète

La `CryptoObjectHelper` classe utilise [`KeyGenerator`](xref:Javax.Crypto.KeyGenerator) l’Android pour créer une clé et la stocker sur l’appareil. La `KeyGenerator` classe peut créer la clé, mais a besoin de quelques méta-données sur le type de clé à créer. Ces informations sont fournies par [`KeyGenParameterSpec`](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html) un exemple de la classe. 

A `KeyGenerator` est instantanée en `GetInstance` utilisant la méthode de l’usine. Le code d’échantillon utilise [_l’Advanced Encryption Standard_](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) (_AES_) comme algorithme de cryptage. AES décomposera les données en blocs de taille fixe et chiffrera chacun de ces blocs.

Ensuite, `KeyGenParameterSpec` a est `KeyGenParameterSpec.Builder`créé en utilisant le . Les `KeyGenParameterSpec.Builder` enveloppements des informations suivantes sur la clé qui doit être créée:

- Nom de la clé.
- La clé doit être valide pour le cryptage et le décryptage.
- Dans le code `BLOCK_MODE` de l’échantillon, le`KeyProperties.BlockModeCbc`est réglé sur _Cipher Block Chaining_ (), ce qui signifie que chaque bloc est XORed avec le bloc précédent (créant des dépendances entre chaque bloc). 
- L’utilisation `CryptoObjectHelper` [_de La norme de cryptographie à clé publique #7_](https://tools.ietf.org/html/rfc2315) (_PKCS7_) pour générer les octets qui seront pad out les blocs pour s’assurer qu’ils sont tous de la même taille.
- `SetUserAuthenticationRequired(true)`signifie que l’authentification de l’utilisateur est nécessaire avant que la clé puisse être utilisée.

Une `KeyGenParameterSpec` fois que le est créé, `KeyGenerator`il est utilisé pour initialiser le , qui générera une clé et le stocker en toute sécurité sur l’appareil. 

## <a name="using-the-cryptoobjecthelper"></a>Utilisation du CryptoObjectHelper

Maintenant que le code de l’échantillon a encapsulé une grande partie de la logique pour créer un `CryptoWrapper` dans la `CryptoObjectHelper` classe, nous allons revoir le code dès le début de ce guide et utiliser le `CryptoObjectHelper` pour créer le chiffrement et démarrer un scanner d’empreintes digitales: 

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

Maintenant que nous avons vu `CryptoObject`comment créer un , `FingerprintManager.AuthenticationCallbacks` permet de passer à voir comment les sont utilisés pour transférer les résultats du service de scanner d’empreintes digitales à une application Android.

## <a name="related-links"></a>Liens connexes

- [Cipher](xref:Javax.Crypto.Cipher)
- [FingerprintManager.CryptoObject](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat.CryptoObject](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [KeyGenerator KeyGenerator KeyGenerator KeyGen](xref:Javax.Crypto.KeyGenerator)
- [KeyGenParameterSpec](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html)
- [KeyGenParameterSpec.Builder (en)](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.Builder.html)
- [KeyPermanentlyInvalidatedException](https://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html)
- [KeyProperties (KeyProperties)](https://developer.android.com/reference/android/security/keystore/KeyProperties.html)
- [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)
- [RFC 2315 - PCKS #7](https://tools.ietf.org/html/rfc2315)
