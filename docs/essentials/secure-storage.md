---
title: 'Xamarin.Essentials : SecureStorage'
description: Ce document décrit la classe SecureStorage de Xamarin.Essentials qui permet de stocker en toute sécurité des paires clé/valeur simples. Il explique comment utiliser la classe et expose les caractéristiques de mise en œuvre de la plateforme et ses limitations.
ms.assetid: 78856C0D-76BB-406E-A880-D5A3987B7D64
author: jamesmontemagno
ms.author: jamont
ms.date: 04/02/2019
ms.custom: video
ms.openlocfilehash: 41d9efa66318f4c3f5315351d3c1f51b4e503521
ms.sourcegitcommit: 44c44ad60c5c880a39006493aedd2d7aa834a27e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550898"
---
# <a name="xamarinessentials-secure-storage"></a>Xamarin.Essentials : SecureStorage

La classe **SecureStorage** permet de stocker en toute sécurité des paires clé/valeur simples.

## <a name="get-started"></a>Bien démarrer

[!include[](~/essentials/includes/get-started.md)]

Pour accéder à la fonctionnalité **SecureStorage**, la configuration requise dépend de la plateforme :

# <a name="android"></a>[Android](#tab/android)

> [!TIP]
> La [sauvegarde automatique pour les applications](https://developer.android.com/guide/topics/data/autobackup) est une fonctionnalité d’Android 6.0 (niveau d’API 23) et des versions ultérieures qui sauvegarde les données d’application de l’utilisateur (préférences partagées, fichiers situés dans le stockage interne de l’application et autres fichiers spécifiques). Les données sont restaurées quand l’application est réinstallée ou installée sur un nouvel appareil. Ceci peut avoir une incidence sur `SecureStorage`, qui utilise les préférences de partage sauvegardées et non déchiffrables lors de la restauration. Xamarin.Essentials gère automatiquement ce cas de figure en supprimant la clé, qui peut ainsi être réinitialisée, mais vous pouvez aller encore plus loin en désactivant la sauvegarde automatique.

### <a name="enable-or-disable-backup"></a>Activer ou désactiver la sauvegarde
Vous pouvez choisir de désactiver la sauvegarde automatique pour la totalité de votre application en définissant le paramètre `android:allowBackup` sur false dans le fichier `AndroidManifest.xml`. Cette approche n’est recommandée que si vous envisagez de restaurer les données d’une autre façon.

```xml
<manifest ... >
    ...
    <application android:allowBackup="false" ... >
        ...
    </application>
</manifest>
```

### <a name="selective-backup"></a>Sauvegarde sélective
Il est possible de configurer la sauvegarde automatique de façon à ce que la sauvegarde de certains contenus soit désactivée. Vous pouvez créer un ensemble de règles personnalisées pour exclure les éléments `SecureStore` de la sauvegarde.

1. Définissez l’attribut `android:fullBackupContent` dans **AndroidManifest.xml** :

    ```xml
    <application ...
        android:fullBackupContent="@xml/auto_backup_rules">
    </application>
    ```

2. Créez un fichier XML nommé **auto_backup_rules.xml** dans le répertoire **ressources/xml** à l’aide de l’action de génération d’**AndroidResource**. Ensuite, définissez le contenu suivant, qui comporte toutes les préférences partagées à l’exception de `SecureStorage` :

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <full-backup-content>
        <include domain="sharedpref" path="."/>
        <exclude domain="sharedpref" path="${applicationId}.xamarinessentials.xml"/>
    </full-backup-content>
    ```

# <a name="ios"></a>[iOS](#tab/ios)

Si le travail de développement s’effectue sur le **simulateur iOS**, activez le droit **Keychain** et ajoutez un groupe d’accès au trousseau pour l’identificateur de bundle de l’application. 

Ouvrez **Entitlements.plist** dans le projet iOS, repérez le droit **Keychain** et activez-le. L’identificateur de l’application est automatiquement ajouté comme groupe.

Dans les propriétés du projet, sous **Signature du bundle iOS**, définissez le paramètre **Droits personnalisés** sur **Entitlements.plist**.

> [!TIP]
> En cas de déploiement sur un appareil iOS, ce droit n’est pas obligatoire et doit être supprimé.

# <a name="uwp"></a>[UWP](#tab/uwp)

Aucune configuration supplémentaire n’est requise.

-----

## <a name="using-secure-storage"></a>Utiliser SecureStorage

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

Pour enregistrer une valeur de _clé_ donnée dans SecureStorage :

```csharp
try
{
  await SecureStorage.SetAsync("oauth_token", "secret-oauth-token-value");
}
catch (Exception ex)
{
  // Possible that device doesn't support secure storage on device.
}
```

Pour récupérer une valeur de SecureStorage :

```csharp
try
{
  var oauthToken = await SecureStorage.GetAsync("oauth_token");
}
catch (Exception ex)
{
  // Possible that device doesn't support secure storage on device.
}
```

> [!NOTE]
> Si aucune valeur n’est associée à la clé demandée, `GetAsync` retourne `null`.

Pour supprimer une certaine clé, appelez :

```csharp
SecureStorage.Remove("oauth_token");
```

Pour supprimer toutes les clés, appelez :

```csharp
SecureStorage.RemoveAll();
```

## <a name="platform-implementation-specifics"></a>Caractéristiques de mise en œuvre de la plateforme

# <a name="android"></a>[Android](#tab/android)

Le [magasin de clés Android](https://developer.android.com/training/articles/keystore.html) est utilisé pour stocker la clé de chiffrement permettant de chiffrer la valeur avant de l’enregistrer dans les [Préférences partagées](https://developer.android.com/training/data-storage/shared-preferences.html) avec le nom de fichier **[VOTRE-ID-DE-PACKAGE-D-APPLICATION].xamarinessentials**.  La clé (qui n’est pas une clé de chiffrement, mais la _clé_ de la _valeur_) utilisée dans le fichier de préférences partagées est un _hachage MD5_ de la clé transmise aux API`SecureStorage`.

**Niveau d’API 23 et plus**

Dans les niveaux d’API récents, une clé **AES** est récupérée auprès du magasin de clés Android et utilisée avec un chiffrement **AES/GCM/NoPadding** permettant de chiffrer la valeur avant de la stocker dans le fichier de préférences partagées.

**Niveau d’API 22 et moins**

Dans les anciens niveaux d’API, le magasin de clés Android ne prend en charge que le stockage de clés **RSA**, utilisées avec un chiffrement **ECB/RSA/PKCS1Padding** pour chiffrer une clé **AES** (générée au hasard à l’exécution) et stockées dans le fichier de préférences partagées sous la clé _SecureStorageKey_, si elle n’a pas déjà été générée.

**SecureStorage** utilise l’API [Préférences](preferences.md) et suit la persistance des données décrite dans la documentation [Préférences](preferences.md#persistence). Si un appareil est mis à niveau du niveau d’API 22 (ou moins) au niveau d’API 23 (ou plus), ce type de chiffrement sera toujours utilisé, sauf si l’application est désinstallée ou si **RemoveAll** est appelé.

# <a name="ios"></a>[iOS](#tab/ios)

[KeyChain](xref:Security.SecKeyChain) permet de stocker en toute sécurité des valeurs sur des appareils iOS.  `SecRecord`, utilisé pour stocker la valeur, a une valeur `Service` définie sur **[VOTRE-ID-DE-BUNDLE-D-APPLICATIONS].xamarinessentials**.

Dans certains cas, les données KeyChain sont synchronisées avec iCloud, et il se peut que la désinstallation de l’application ne retire pas les valeurs sécurisées d’iCloud et autres appareils de l’utilisateur.

# <a name="uwp"></a>[UWP](#tab/uwp)

[DataProtectionProvider](https://docs.microsoft.com/uwp/api/windows.security.cryptography.dataprotection.dataprotectionprovider) permet de chiffrer de façon sécurisée des valeurs sur des appareils UWP.

Ces valeurs chiffrées sont stockées dans `ApplicationData.Current.LocalSettings`, à l’intérieur d’un conteneur, avec le nom **[VOTRE-ID-D-APPLICATION].xamarinessentials**.

**SecureStorage** utilise l’API [Préférences](preferences.md) et suit la persistance des données décrite dans la documentation [Préférences](preferences.md#persistence). Elle utilise également `LocalSettings` qui a une restriction selon laquelle le nom de chaque paramètre peut comporter 255 caractères au maximum. Chaque paramètre peut avoir une taille maximale de 8 Ko et chaque paramètre composite peut comporter jusqu’à 64 Ko d’octets.

-----

## <a name="limitations"></a>Limites

Cette API est destinée à stocker de petites quantités de texte.  Les performances risquent d’être lentes si vous essayez de l’utiliser pour stocker de grandes quantités de texte. 

## <a name="api"></a>API

- [Code source de SecureStorage](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/SecureStorage)
- [Documentation de l’API SecureStorage](xref:Xamarin.Essentials.SecureStorage)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Secure-Storage-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
