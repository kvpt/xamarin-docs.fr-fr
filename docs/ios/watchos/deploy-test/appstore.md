---
title: Déploiement d’applications Watchos sur l’App Store
description: Ce document explique comment déployer des applications Watchos générées avec Xamarin dans l’App Store. Il examine les profils de provisionnement de distribution et iTunes Connect, et fournit également des conseils de dépannage.
ms.prod: xamarin
ms.assetid: DBE16040-70D2-4F61-B5F3-C8D213DBC754
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 1581a58d9a6851ad880d2631660e261685260e40
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86932793"
---
# <a name="deploying-watchos-apps-to-the-app-store"></a>Déploiement d’applications Watchos sur l’App Store

> [!IMPORTANT]
> Veillez à consulter le [Guide de soumission du kit de surveillance d’Apple](https://developer.apple.com/app-store/watch/)et consultez la section [résolution](#troubleshooting) des problèmes pour tout problème que vous pouvez rencontrer.

- Vérifiez que vous disposez des éléments suivants :
  - [**Profils de provisionnement de distribution**](#provisioning) créés pour vos projets.
  - La **cible de déploiement** ( `MinimumOSVersion` ) de l’application parent iOS définie à **8,2** ou une version antérieure (8,3 n’est pas prise en charge).

- Dans [**iTunes Connect**](#iTunes_Connect):

  - Créez votre entrée d’application iOS (ou ajoutez une **nouvelle version** à une application existante).
  - Ajouter une icône de montre et des captures d’écran.

- Ensuite, dans [Visual Studio pour Mac](#xamarin_studio) (Visual Studio n’est pas pris en charge actuellement) :

  - Cliquez avec le bouton droit sur l’application iOS, puis choisissez **définir comme projet de démarrage**.
  - Passez à la configuration de l' **App Store** .
  - Utilisez la fonctionnalité d' **archivage** créer une archive d’application.

- Enfin, basculez vers [Xcode 6.2 +](#xcode)

  - Accédez à la **fenêtre > organisateur** , puis choisissez **Archives**.
  - Sélectionnez l’application et l’archive dans la liste.
  - Éventuellement **Valider...** Archive.
  - **Envoyer...** l’archive et suivez les étapes pour télécharger vers iTunes Connect pour la révision et l’approbation.

Lisez les conseils spécifiques relatifs à ces éléments ci-dessous. Si vous rencontrez des problèmes, consultez la section [résolution des](#troubleshooting) problèmes.

<a name="provisioning"></a>

## <a name="distribution-provisioning-profiles"></a>Profils de provisionnement de distribution

Pour générer le déploiement de l’App Store, vous devez créer un **profil de provisionnement de distribution** pour chaque ID d’application dans votre solution.

Si vous avez un ID d’application générique, *un seul profil de provisionnement sera requis*; Toutefois, si vous disposez d’un ID d’application distinct pour chaque projet, vous aurez besoin d’un profil de configuration pour chaque ID d’application :

![Profil de distribution de l’App Store](appstore-images/provisioningprofile-distribution-sml.png)

Une fois que vous avez créé les trois profils, ceux-ci s’affichent dans la liste. N’oubliez pas de télécharger et d’installer chacun d’eux (en double-cliquant dessus) :

![Liste des profils disponibles](appstore-images/provisioningprofiles-sml.png)

Vous pouvez vérifier le profil de provisionnement dans les **Options du projet** en sélectionnant l’écran de signature de l' **offre groupée > iOS** et en sélectionnant la configuration **AppStore | iPhone** .

La liste **profil de provisionnement** affiche tous les profils correspondants. vous devez voir les profils correspondants que vous avez créés dans cette liste déroulante.

![Boîte de dialogue de signature d’offre groupée iOS](appstore-images/options-selectprofile-sml.png)

<a name="iTunes_Connect"></a>

## <a name="itunes-connect"></a>iTunes Connect

Suivez la [Présentation de la distribution d’applications](~/ios/deploy-test/app-distribution/index.md), en particulier :

- [Configuration d’une application dans iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [Publication sur l’App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)

Quand vous configurez l’application dans iTunes Connect, n’oubliez pas d’ajouter l’icône de visionneuse et les captures d’écran :

![Icône de montre et captures d’écran dans iTunes Connect](appstore-images/itunesconnect-watch-sml.png)

Le fichier icône doit être 1024x1024 pixels et un masque circulaire lui sera appliqué lors de son affichage. L’icône ne doit pas avoir de canal alpha.

Au moins une capture d’écran est requise, jusqu’à cinq peut être envoyée.
Ils doivent être 312x390 pixels et démontrer votre application Watch en action.
Vous pouvez utiliser le simulateur Watch 42mm Watch pour prendre des captures d’écran à cette taille.

<a name="xamarin_studio"></a>

## <a name="visual-studio-for-mac"></a>Visual Studio pour Mac

1. Assurez-vous que l’application iOS est le projet de démarrage. Si ce n’est pas le cas, cliquez dessus avec le bouton droit pour le définir :

   ![Définition du projet de démarrage](appstore-images/xs-startup.png)

2. Choisissez la configuration de build **AppStore** :

   ![Configuration de build AppStore](appstore-images/xs-appstore.png)

3. Choisissez l’élément de menu **Build > Archive** pour démarrer le processus d’archivage :

   ![Menu Générer](appstore-images/xs-archive.png)

Vous pouvez également choisir l’élément de menu **afficher > les archives...** pour afficher les archives qui ont été créées précédemment.

  ![Vue Archives](appstore-images/xs-archives-sml.png)

<a name="xcode"></a>

## <a name="xcode"></a>Xcode

Xcode affiche automatiquement les archives créées dans Visual Studio pour Mac.

1. Démarrez Xcode et choisissez **fenêtre > organisateur**:

   ![Menu fenêtre](appstore-images/xc-organizer.png)

2. Basculez vers l’onglet **Archives** et sélectionnez l’archive qui a été créée avec Visual Studio pour Mac :

   ![Onglet Archives](appstore-images/xc-archives.png)

3. Si vous le souhaitez, **validez** l’archive..., puis choisissez **Envoyer...** pour télécharger l’application dans iTunes Connect.

4. Choisissez l’équipe de développement (si vous appartenez à plus d’un), puis confirmez la soumission :

   ![Section de l’équipe de développement](appstore-images/xc-submit1.png)

5. Reportez-vous à iTunes Connect pour voir le binaire chargé. Accédez à la page de configuration de votre application, puis choisissez **version préliminaire** dans le menu supérieur pour afficher la liste des **Builds** :

   [![Page de configuration des applications dans iTunes Connect](appstore-images/itc-prerelease-sml.png)](appstore-images/itc-prerelease.png#lightbox)

Vous pouvez ensuite soumettre l’application pour approbation sur la page **versions** . Pour plus d’informations, reportez-vous à la présentation de la [distribution d’applications iOS](~/ios/deploy-test/app-distribution/index.md) .

## <a name="troubleshooting"></a>Dépannage

Voici quelques-unes des erreurs que vous pouvez rencontrer lors de l’envoi à l’App Store, ainsi que les étapes à suivre pour les corriger.

### <a name="archive-menu-option-is-not-visible-in-visual-studio-for-mac"></a>L’option de menu archive n’est pas visible dans Visual Studio pour Mac

Suivez les [étapes ci-dessus](#xamarin_studio) pour configurer la solution pour l’archivage. Si vous ne pouvez pas définir correctement le projet de démarrage, assurez-vous que la configuration de build est définie sur Debug ou Release avant de tenter de modifier le projet de démarrage. Réaffectez ensuite la valeur **AppStore**à la configuration de Build.

### <a name="invalid-icon"></a>Icône Non valide

```csharp
Invalid Icon - The watch application '...watchkitextension.appex/WatchApp.app'
contains an icon file '...watchkitextension.appex/WatchApp.app/AppIcon27.5x27.5@2x.png'
with an alpha channel. Icons should not have an alpha channel.
```

Suivez les [instructions pour supprimer le canal alpha](~/ios/watchos/troubleshooting.md) de vos icônes.

### <a name="cfbundleversion-mismatch"></a>Incompatibilité valeur cfbundleversion

```csharp
CFBundleVersion Mismatch. The CFBundleVersion value '1' of watch application
'...watchkitextension.appex/WatchApp.app' does not match the CFBundleVersion
value '1.0' of its containing iOS application `YouriOS.app`.
```

Tous les projets de votre solution (l’application iOS, l’extension Watch et l’application Watch) doivent utiliser le même numéro de version. Modifiez chaque fichier **info. plist** afin que le numéro de version corresponde exactement à.

### <a name="missing-icons"></a>Icônes manquantes

```csharp
Missing Icons. No icons found for watch application '...watchkitextension.appex/WatchApp.app'.
Please make sure that its Info.plist file includes entries for CFBundleIconFiles.
```

Suivez les instructions fournies dans les [icônes utilisation](~/ios/watchos/app-fundamentals/icons.md) de pour ajouter toutes les images requises au projet d’application Watch.

### <a name="missing-icon"></a>Icône manquante

```csharp
Missing Icon. The watch application '...watchkitextension.appex/WatchApp.app'
is missing icon with name pattern '*44x44@2x.png' (Home Screen 42mm).
```

Vérifiez que vous disposez de la dernière version de Visual Studio pour Mac, et que votre **AppIcon. appiconset** contient un ensemble complet d’images. Si vous continuez à voir cette erreur, affichez la source de l' **Contents.jssur** pour confirmer qu’elle contient une entrée pour toutes les images requises. Une fois que vous avez vérifié que vous utilisez la dernière version de Xamarin, supprimez et recréez l' **AppIcon. appiconset**.

> [!IMPORTANT]
> Il existe un bogue connu dans la prise en charge de l’icône de surveillance de Visual Studio pour Mac : elle attend une image de pixel 88x88 pour l' **29x29@3x** image (qui doit être 87x87 pixels).

Vous ne pouvez pas résoudre ce problème dans Visual Studio pour Mac : modifiez la ressource d’image dans Xcode ou modifiez manuellement l' **Contents.jsdans** le fichier.

### <a name="invalid-watchkit-support"></a>Prise en charge WatchKit non valide

```csharp
Invalid WatchKit Support - The bundle contains an invalid implementation of WatchKit.
The app may have been built or signed with non-compliant or pre-release tools.
```

Ce message peut s’afficher lors de la validation et de l’envoi, ou dans un e-mail automatisé d’iTunes Connect après un chargement apparemment réussi.
<!--
Ensure you are using the latest version of Xcode and Xamarin's tools.
-->
> [!IMPORTANT]
> Vous devez **Archiver** votre application dans Visual Studio pour Mac puis basculer vers Xcode 6.2 + pour valider et charger iTunes Connect.

Utilisez le canal Xamarin stable et Xcode 6.2 +.

### <a name="invalid-provisioning-profile"></a>Profil d’approvisionnement non valide

```csharp
Invalid Provisioning Profile. The provisioning profile included in the bundle
...iOSWatchApp.watchkitapp [iOSWatchApp.app/PlugIns/...iOSWatchApp.watchkitextension.appex/WatchApp.app]
is invalid. [Missing code-signing certificate.]
```

Les **profils de provisionnement de distribution** doivent être fournis pour les trois projets dans une solution d’application Watch : l’application iOS, l’extension Watch et l’application Watch, soit explicitement (trois profils), soit via un profil générique unique. Vérifiez que les profils de provisionnement existent dans le centre de développement iOS et que vous les avez téléchargés et ajoutés à votre Mac.

### <a name="invalid-code-signing-entitlements"></a>Droits de signature de code non valides

```csharp
ITMS-90046: Invalid Code Signing Entitlements. Your application bundle's signature contains
code signing entitlements that are not supported on iOS. Specifically, value
'...watchkitextension' for key 'application-identifier' in '...watchkitextension'
is not supported. The value should be a string startign with your TEAMID, followed
by a dot '.' followed by the bundle identifier.
```

Vérifiez que vos profils de provisionnement sont correctement configurés sur le centre de développement Apple, et que vous les avez téléchargés et installés. Vérifiez également qu’ils sont définis dans la fenêtre Propriétés de Visual Studio pour Mac pour chaque projet.

### <a name="invalid-architecture"></a>Architecture non valide

```csharp
Invalid architecture: Apps that include an app extension
and framework must support arm64.
```

Vous pouvez uniquement ajouter des applications Watch [API unifiée (64 bits)](~/cross-platform/macios/unified/index.md) Xamarin. iOS.
Cliquez avec le bouton droit sur le projet d’application iOS, accédez à **Options > générer > version ios > onglet Avancé** et assurez-vous que les **architectures prises en charge** pour la configuration AppStore-iPhone incluent **ARM64** (par exemple, **ARMv7 + ARM64**).

### <a name="this-bundle-is-invalid"></a>Cette offre groupée n’est pas valide.

```csharp
ITMS-90068: This bundle is invalid. The value provided for the key
MinimumOSVersion '8.3' is not acceptable.
```

Le MinimumOSVersion doit être défini sur « 8,2 » ou une version antérieure pour votre application iOS parente.

### <a name="non-public-api-usage"></a>Utilisation d’API non publiques

```csharp
Your app contains non-public API usage.
Please review the errors, and resubmit your application.
```

Vérifiez que vous utilisez la dernière version des outils Xcode et Xamarin.
Votre code ne doit pas accéder aux API non publiques.

### <a name="build-error-mt5309"></a>Erreur de génération MT5309

```csharp
Error MT5309: Native linking error: clang: error: no such file or directory:
```

Cette erreur est probablement due au fait que vous avez renommé votre installation Xcode à partir de **Xcode. app**. Par exemple, cette erreur se produit si vous renommez votre installation de **Xcode 6.2. app**.

## <a name="related-links"></a>Liens associés

- [Guide d’envoi Apple WatchKit](https://developer.apple.com/app-store/watch/)
