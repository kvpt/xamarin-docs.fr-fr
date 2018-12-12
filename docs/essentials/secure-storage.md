---
title: 'Xamarin.Essentials : SecureStorage'
description: Ce document décrit la classe SecureStorage de Xamarin.Essentials qui permet de stocker en toute sécurité des paires clé/valeur simples. Il explique comment utiliser la classe et expose les caractéristiques de mise en œuvre de la plateforme et ses limitations.
ms.assetid: 78856C0D-76BB-406E-A880-D5A3987B7D64
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 7ba7fc6cabc2e3684476c216ca65d3824a35e8aa
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2018
ms.locfileid: "52898912"
---
# <a name="xamarinessentials-secure-storage"></a>Xamarin.Essentials : SecureStorage

La classe **SecureStorage** permet de stocker en toute sécurité des paires clé/valeur simples.

## <a name="get-started"></a>Prise en main

[!include[](~/essentials/includes/get-started.md)]

Pour accéder à la fonctionnalité **SecureStorage**, la configuration requise dépend de la plateforme :

# <a name="androidtabandroid"></a>[Android](#tab/android)

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

2. Créez un fichier XML nommé **auto_backup_rules.xml** dans le répertoire **ressources/xml**. Ensuite, définissez le contenu suivant, qui comporte toutes les préférences partagées à l’exception de `SecureStorage` :

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <full-backup-content>
        <include domain="sharedpref" path="."/>
        <exclude domain="sharedpref" path="${applicationId}.xamarinessentials.xml"/>
    </full-backup-content>
    ```

# <a name="iostabios"></a>[iOS](#tab/ios)

Si le travail de développement s’effectue sur le **simulateur iOS**, activez le droit **Keychain** et ajoutez un groupe d’accès au trousseau pour l’identificateur de bundle de l’application. 

Ouvrez **Entitlements.plist** dans le projet iOS, repérez le droit **Keychain** et activez-le. L’identificateur de l’application est automatiquement ajouté comme groupe.

Dans les propriétés du projet, sous **Signature du bundle iOS**, définissez le paramètre **Droits personnalisés** sur **Entitlements.plist**.

> [!TIP]
> En cas de déploiement sur un appareil iOS, ce droit n’est pas obligatoire et doit être supprimé.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

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

# <a name="androidtabandroid"></a>[Android](#tab/android)

Le [magasin de clés Android](https://developer.android.com/training/articles/keystore.html) est utilisé pour stocker la clé de chiffrement permettant de chiffrer la valeur avant de l’enregistrer dans les [Préférences partagées](https://developer.android.com/training/data-storage/shared-preferences.html) avec le nom de fichier **[VOTRE-ID-DE-PACKAGE-D-APPLICATION].xamarinessentials**.  La clé utilisée dans le fichier de préférences partagées est un _hachage MD5_ de la clé transmise aux API `SecureStorage`.

## <a name="api-level-23-and-higher"></a>Niveau d’API 23 et plus

Dans les niveaux d’API récents, une clé **AES** est récupérée auprès du magasin de clés Android et utilisée avec un chiffrement **AES/GCM/NoPadding** permettant de chiffrer la valeur avant de la stocker dans le fichier de préférences partagées.

## <a name="api-level-22-and-lower"></a>Niveau d’API 22 et moins

Dans les anciens niveaux d’API, le magasin de clés Android ne prend en charge que le stockage de clés **RSA**, utilisées avec un chiffrement **ECB/RSA/PKCS1Padding** pour chiffrer une clé **AES** (générée au hasard à l’exécution) et stockées dans le fichier de préférences partagées sous la clé _SecureStorageKey_, si elle n’a pas déjà été générée.

**SecureStorage** utilise l’API [Préférences](preferences.md) et suit la persistance des données décrite dans la documentation [Préférences](preferences.md#persistence). Si un appareil est mis à niveau du niveau d’API 22 (ou moins) au niveau d’API 23 (ou plus), ce type de chiffrement sera toujours utilisé, sauf si l’application est désinstallée ou si **RemoveAll** est appelé.

# <a name="iostabios"></a>[iOS](#tab/ios)

[KeyChain](https://developer.xamarin.com/api/type/Security.SecKeyChain/) permet de stocker en toute sécurité des valeurs sur des appareils iOS.  `SecRecord`, utilisé pour stocker la valeur, a une valeur `Service` définie sur **[VOTRE-ID-DE-BUNDLE-D-APPLICATIONS].xamarinessentials**.

Dans certains cas, les données KeyChain sont synchronisées avec iCloud, et il se peut que la désinstallation de l’application ne retire pas les valeurs sécurisées d’iCloud et autres appareils de l’utilisateur.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

[DataProtectionProvider](https://docs.microsoft.com/uwp/api/windows.security.cryptography.dataprotection.dataprotectionprovider) permet de chiffrer en toute sécurité des valeurs sur des appareils UWP.

Ces valeurs chiffrées sont stockées dans `ApplicationData.Current.LocalSettings`, à l’intérieur d’un conteneur, avec le nom **[VOTRE-ID-D-APPLICATION].xamarinessentials**.

**SecureStorage** utilise l’API [Préférences](preferences.md) et suit la persistance des données décrite dans la documentation [Préférences](preferences.md#persistence).

-----

## <a name="limitations"></a>Limitations

Cette API est destinée à stocker de petites quantités de texte.  Les performances risquent d’être lentes si vous essayez de l’utiliser pour stocker de grandes quantités de texte.

## <a name="api"></a>API

- [Code source de SecureStorage](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/SecureStorage)
- [Documentation de l’API SecureStorage](xref:Xamarin.Essentials.SecureStorage)
