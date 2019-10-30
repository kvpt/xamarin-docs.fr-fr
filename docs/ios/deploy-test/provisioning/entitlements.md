---
title: Utilisation des droits dans Xamarin.iOS
description: Les droits sont des fonctionnalités et des autorisations de sécurité particulières accordées aux applications qui présentent la configuration requise pour les utiliser.
ms.prod: xamarin
ms.assetid: 8A3961A2-02AB-4228-A41D-06CB4108D9D0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/13/2018
ms.openlocfilehash: 43bde3a31a79728548e72ea1d34977f1a131f282
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028543"
---
# <a name="working-with-entitlements-in-xamarinios"></a>Utilisation des droits dans Xamarin.iOS

_Les droits sont des fonctionnalités et des autorisations de sécurité particulières accordées aux applications qui présentent la configuration requise pour les utiliser._

Dans iOS, les applications s’exécutent dans un _bac à sable (sandbox)_ , qui fournit un ensemble de règles visant à limiter l’accès entre l’application et certaines ressources système ou données utilisateur. Utilisez des _droits_ pour demander au système d’augmenter le bac à sable afin d’ajouter des fonctionnalités à votre application.

Pour étendre les fonctionnalités de votre application, ajoutez les droits appropriés dans le fichier Entitlements.plist de l’application. Seules certaines fonctionnalités peuvent être étendues. Celles-ci sont répertoriées dans le guide [Utilisation des fonctionnalités](~/ios/deploy-test/provisioning/capabilities/index.md) et décrites [ci-dessous](#entitlement-key-reference). Les droits sont passés au système sous forme de paires clé-valeur. En règle générale, un seul droit est requis pour chaque fonctionnalité. Les clés et valeurs spécifiques sont décrites dans la section [Informations de référence sur les clés de droits](#entitlement-key-reference), plus loin dans ce guide.
Visual Studio pour Mac et Visual Studio offrent une interface simple pour l’ajout de droits dans une application Xamarin.iOS à l’aide de l’éditeur Entitlements.plist.
Ce guide présente l’éditeur Entitlements.plist et explique comment l’utiliser. Il fournit également des informations de référence sur l’ensemble des droits que vous pouvez ajouter à un projet iOS pour chaque fonctionnalité.

## <a name="entitlements-and-provisioning"></a>Droits et provisionnement

Le fichier Entitlements.plist sert à spécifier les droits et à signer le bundle d’applications.

Toutefois, un provisionnement supplémentaire est nécessaire pour garantir la signature correcte du code de l’application. Le profil de provisionnement utilisé doit contenir un ID d’application pour lequel la fonctionnalité requise est activée. Pour plus d’informations sur la procédure à suivre, consultez le guide [Utilisation des fonctionnalités](~/ios/deploy-test/provisioning/capabilities/index.md).

> [!IMPORTANT]
> Le fichier Entitlements.plist permet de renseigner les propriétés appropriées d’une application utilisant des fonctionnalités. Toutefois, il ne permet pas de générer un profil de provisionnement, car il n’est pas lié à un compte de développeur Apple. Vous devez générer un profil de provisionnement à partir du portail des développeurs pour pouvoir déployer et distribuer l’application.

## <a name="set-entitlements-in-a-xamarinios-project"></a>Définir des droits dans un projet Xamarin.iOS

En plus de sélectionner et configurer les services d’application nécessaires quand vous définissez l’ID d’application, vous devez configurer les droits dans le projet Xamarin.iOS en modifiant les fichiers **Info.plist** et  **Entitlements.plist**.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Pour configurer les droits dans Visual Studio pour Mac, effectuez les étapes suivantes :

1. Dans l’**Explorateur de solutions**, double-cliquez sur le fichier **Info.plist** pour l’ouvrir et le modifier.
2. Dans la section **Cible d’application iOS**, entrez le nom de l’application, puis entrez l’**identificateur de bundle** créé quand vous avez défini l’ID d’application :

    ![](entitlements-images/servicexs01.png "Enter a Bundle Identifier")

3. Enregistrez les modifications apportées au fichier **Info.plist**.
4. Dans l’**Explorateur de solutions**, double-cliquez sur le fichier **Entitlements.plist** pour l’ouvrir et le modifier :

    ![](entitlements-images/servicexs02.png "Editing the Entitlements")

5. Sélectionnez et configurez les droits requis pour l’application Xamarin.iOS afin qu’ils correspondent à la configuration définie au moment de la création de l’ID d’application.
6. Enregistrez les modifications apportées au fichier **Entitlements.plist**.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Pour configurer les droits dans Visual Studio, effectuez les étapes suivantes :

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le fichier **Info.plist**, puis sélectionnez **Ouvrir avec…** et **Éditeur de liste de propriétés** pour ouvrir le fichier à modifier.
2. Dans la section **Cible d’application iOS**, entrez le nom de l’application, puis entrez l’**identificateur de bundle** créé quand vous avez défini l’ID d’application :

    ![](entitlements-images/servicevs01.png "Setting the Bundle Identifier")

3. Enregistrez les modifications apportées au fichier **Info.plist**.
4. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le fichier **Entitlements.plist**, puis sélectionnez **Ouvrir avec…** et **Éditeur de liste de propriétés** pour ouvrir le fichier à modifier :

    ![](entitlements-images/servicevs02.png "Editing the Entitlements")

    Vous pouvez également double-cliquer sur le fichier **Entitlements.plist** pour l’ouvrir dans l’éditeur de source XML, puis définir les valeurs de propriété et de clé des droits, comme expliqué dans la section [Informations de référence sur les clés de droits](#entitlement-key-reference) ci-dessous.

5. Sélectionnez et configurez les droits requis pour l’application Xamarin.iOS afin qu’ils correspondent à la configuration définie au moment de la création de l’ID d’application.
6. Enregistrez les modifications apportées au fichier **Entitlements.plist**.

-----

## <a name="adding-a-new-entitlementsplist-file"></a>Ajout d’un nouveau fichier Entitlements.plist

Vous ajoutez les droits à une application à l’aide du fichier Entitlements.plist. Ce fichier est inclus dans les projets Xamarin.iOS par défaut, mais il n’est pas toujours présent dans les projets plus anciens.

Pour ajouter un fichier Entitlements.plist à votre projet Xamarin.iOS, effectuez les étapes suivantes :

1. Cliquez avec le bouton droit sur le fichier projet et sélectionnez **Ajouter > Nouveau fichier…**  :

    ![Menu contextuel d’ajout de fichiers](entitlements-images/image1.png)
2. Dans la boîte de dialogue Nouveau fichier, sélectionnez **iOS > Liste de propriétés** et nommez la liste « Entitlements » :

    ![Boîte de dialogue Nouveau fichier](entitlements-images/image2.png)

## <a name="entitlement-key-reference"></a>Informations de référence sur les clés de droits

Vous pouvez ajouter les clés de droits par le biais du panneau source de l’éditeur Entitlements.plist. Les clés requises sont normalement ajoutées quand vous utilisez l’éditeur Entitlements.plist, mais elles sont répertoriées ci-dessous pour référence.

### <a name="wallet"></a>Wallet

- **Description** : Anciennement appelée Passbook, Wallet est une application qui stocke et gère les passes. Ces passes peuvent être des cartes bancaires, des cartes de magasin, des cartes d’embarquement ou des billets.

  - **Identificateur du type de passe**
    - **Clés** : com.apple.developer.pass-type-identifiers
    - **Chaîne** : `$(TeamIdentifierPrefix)*`

- **Remarques** :
  - Avec cette valeur de chaîne, votre application accepte tous les types de passes. Si vous souhaitez que votre application accepte uniquement une partie des types de passes d’équipe, définissez la chaîne à cette valeur : `$(TeamIdentifierPrefix)pass.$(CFBundleIdentifier)`

  Où pass.$(CFBundleIdentifier) est l’ID de passe qui a été créé [plus haut](~/ios/platform/passkit.md)

<a name="icloud" />

### <a name="icloud"></a>iCloud

- **Description** : iCloud offre aux utilisateurs d’appareil iOS un moyen simple et pratique de stocker leur contenu et de le partager entre plusieurs appareils. Avec iCloud, les développeurs peuvent fournir quatre types de stockage différents aux utilisateurs : le stockage Key-value iCloud, le stockage UIDocument, CoreData et l’utilisation directe de CloudKit pour le stockage des fichiers et répertoires. Pour plus d’informations, consultez le guide Présentation d’iCloud.

  - **Documents iCloud et CloudKit**
    - **Clés** : com.apple.developer.ubiquity-container-identifiers
    - **Chaîne** : `$(TeamIdentifierPrefix)$(CFBundleIdentifier)`
  - **Stockage Key-value iCloud**
    - **Clé** : com.apple.developer.ubiquity-kvstore-identifier
    - **Chaîne** : `$(TeamIdentifierPrefix)$(CFBundleIdentifier)`

- **Remarques** :
  - Pour trouver la chaîne `$(TeamIdentifierPrefix)`, connectez-vous sur le site developer.apple.com et accédez à la section **Member Center > Your Account > Developer Account Summary**, dans laquelle est indiqué votre ID d’équipe (ou l’ID individuel du développeur). Il s’agit d’une chaîne composée de dix caractères (A93A5CM278, par exemple).
  - La chaîne `$(CFBundleIdentifier)` commence par `iCloud`. Elle est définie au moment de la création du conteneur iCloud (voir les étapes décrites dans le guide [Utilisation des fonctionnalités](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md)).
  - Les espaces réservés $`(TeamIdentifierPrefix)` et `$(CFBundleIdentifier)` peuvent être utilisés. Ils sont remplacés par les valeurs correspondantes au moment de la génération.

> [!IMPORTANT]
> Apple [fournit des outils](https://developer.apple.com/support/allowing-users-to-manage-data/) pour aider les développeurs à gérer correctement le Règlement général sur la protection des données (RGPD) de l’Union européenne.

### <a name="app-groups"></a>App Groups

- **Description** : Un App Group est un groupe qui permet à différentes applications (ou à une application et ses extensions) d’accéder à un emplacement partagé du stockage de fichiers.

  - **Clé** : com.apple.security.application-groups
  - **Chaîne** : group.$(CFBundleIdentifier)

<a name="apple-pay" />

### <a name="apple-pay"></a>Apple Pay

- **Description** : Apple Pay permet aux utilisateurs de payer des marchandises physiques à partir de leur appareil iOS.
  - **Clé** : com.apple.developer.in-app-payments
  - **Chaîne** : merchant.your.mechantid

### <a name="push-notifications"></a>Push Notifications

- **Clé** : aps-environment
- **Chaîne** : `development` ou `production`

### <a name="siri"></a>Siri

- **Description** : SiriKit permet à une application iOS de fournir des services accessibles à Siri et à l’application Maps sur un appareil iOS à l’aide des extensions d’application, ainsi que des nouveaux frameworks Intents et Intents UI. Pour plus d’informations, consultez le guide Présentation de SiriKit.
  - **Clé** : com.apple.developer.siri

### <a name="personal-vpn"></a>VPN personnel

- **Clé** : com.apple.developer.networking.vpn.api
- **Chaîne** : allow-vpn

### <a name="keychain-sharing"></a>Keychain Sharing

- **Description** : Keychain Sharing permet aux développeurs d’application de partager les mots de passe qui sont stockés dans le trousseau de l’appareil avec d’autres applications développées par la même équipe. Vous pouvez limiter l’accès en passant à la chaîne un identificateur de groupe d’accès au trousseau.
  - **Clé** : keychain-access-groups
  - **Chaîne** : $(AppIdentifierPrefix) $(CFBundleIdentifier)

### <a name="inter-app-audio"></a>Inter-App Audio

- **Description** : Inter-App Audio permet aux développeurs de diffuser des flux audio en continu entre les applications.
  - **Clé** : inter-app-audio
  - **Valeur booléenne** : YES

### <a name="associated-domains"></a>Associated Domains

- **Description** : Associated Domains permet d’associer et de passer les domaines qui doivent être traités comme des liens universels. Vous pouvez implémenter des liens universels pour autoriser les liens profonds entre votre application et un site web. Vous devez fournir une entrée à chaque domaine pris en charge par votre application, en commençant chaque entrée par `applinks:`
  - **Clé** : com.apple.developer.associated-domains
  - **Chaîne** : webcredentials:example.com

### <a name="data-protection"></a>Data Protection

- **Description** : Data Protection permet d’utiliser le matériel de chiffrement intégré pour stocker les données sensibles contenues dans votre application dans un format chiffré. Par défaut, le niveau de protection est défini sur une protection complète (les fichiers sont accessibles uniquement quand l’appareil est déverrouillé).
  - **Clé** : com.apple.developer.default-data-protection
  - **Chaîne** : NSFileProtectionComplete

### <a name="homekit"></a>HomeKit

- **Description** : Le framework HomeKit offre une plateforme pour installer, configurer et gérer les appareils domotiques compatibles à partir d’un appareil iOS. Pour plus d’informations sur l’utilisation d’HomeKit, consultez le guide Présentation d’HomeKit.
  - **Clé** : com.apple.developer.homekit
  - **Valeur booléenne** : YES

### <a name="healthkit"></a>HealthKit

- **Description** : Le framework HealthKit introduit dans iOS 8 fournit un stockage centralisé, coordonné et sécurisé pour les données de santé. Pour plus d’informations sur l’utilisation d’HealthKit, consultez le guide Présentation d’HealthKit.
  - **Clé** : com.apple.developer.healthkit
  - **Valeur booléenne** : YES

### <a name="wireless-accessory-configuration"></a>Configuration d’accessoires sans fil

- **Description** : Wireless Accessory Configuration permet à votre application de configurer des accessoires WiFi MFI
  - **Clé** : com.apple.external-accessory.wireless-configuration
  - **Valeur booléenne** : YES

### <a name="classkit"></a>ClassKit

- **Description** : ClassKit permet aux enseignants d’afficher la progression de l’étudiant sur les activités qui lui ont été affectées dans votre application.
  - **Clé** : com.apple.developer.ClassKit-environment
  - **Chaîne** : `development` ou `production`

## <a name="summary"></a>Récapitulatif

Ce guide a présenté les différents droits et a expliqué comment les utiliser dans Visual Studio pour Mac et Visual Studio. Il a également fourni des informations de référence sur les paires clé-valeur pour chaque fonctionnalité.
