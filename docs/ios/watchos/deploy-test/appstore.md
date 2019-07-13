---
title: Déploiement watchOS applications sur l’App Store
description: Ce document décrit comment déployer des applications watchOS générées avec Xamarin à l’App Store. Il examine les profils de provisionnement de distribution et d’iTunes Connect, et il fournit également des conseils de dépannage.
ms.prod: xamarin
ms.assetid: DBE16040-70D2-4F61-B5F3-C8D213DBC754
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 58e3593dc09c76439a3e128e51f354c169d7e72e
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865975"
---
# <a name="deploying-watchos-apps-to-the-app-store"></a>Déploiement watchOS applications sur l’App Store

> [!IMPORTANT]
> Veillez à consulter [Guide de soumission du Kit d’Apple Watch](https://developer.apple.com/app-store/watch/)et consultez le [dépannage](#troubleshooting) section pour les problèmes que vous pouvez avoir.

- Vérifiez que :
  - [**Profils de provisionnement de distribution** ](#provisioning) créé pour vos projets.
  - Le **cible de déploiement** (`MinimumOSVersion`) pour iOS parent la valeur application **8.2** ou une version antérieure (8.3 n’est pas pris en charge).

- Dans [ **iTunes Connect**](#iTunes_Connect):

  - Créer votre iOS entrée de l’application (ou ajouter un **nouvelle Version** à une application existante).
  - Ajouter des captures d’écran et icône espion.

- Ensuite, dans [Visual Studio pour Mac](#xamarin_studio) (Visual Studio n'est pas actuellement pris en charge) :

  - Avec le bouton droit de l’application iOS et choisissez **définir comme projet de démarrage**.
  - Remplacez par le **App Store** configuration.
  - Utilisez le **Archive** fonctionnalité de créer une archive d’application.

- Enfin, basculez vers [Xcode 6.2 +](#xcode)

  - Accédez à la **fenêtre > organisateur** et choisissez **Archives**.
  - Sélectionnez l’application et l’archive dans la liste.
  - (Facultatif) **Valider...**  l’archive.
  - **Envoyer...**  l’archive et suivez les étapes à charger dans iTunes connectent pour révision et approbation.

Lire des conseils spécifiques liés à ces éléments ci-dessous. Consultez le [dépannage](#troubleshooting) section si vous rencontrez des problèmes.

<a name="provisioning" />

## <a name="distribution-provisioning-profiles"></a>Profils de provisionnement de distribution

Pour générer pour le déploiement d’App Store vous devez créer un **profil de provisionnement de Distribution** pour chaque ID d’application dans votre solution.

Si vous avez un ID d’application, de caractère générique *qu’un seul profil de provisionnement devront*; Toutefois, si vous avez un ID d’application distinct pour chaque projet vous aurez besoin un profil de provisionnement pour chaque ID d’application :

![](appstore-images/provisioningprofile-distribution-sml.png "Le profil de Distribution d’applications Store")

Une fois que vous avez créé les trois profils, ceux-ci s’affichent dans la liste. N’oubliez pas de télécharger et installer chacune d’elles (en double-cliquant dessus) :

![](appstore-images/provisioningprofiles-sml.png "La liste des profils disponibles")

Vous pouvez vérifier le profil de provisionnement dans le **Options du projet** en sélectionnant le **Générer > signature du Bundle iOS** écran et en sélectionnant le **AppStore | iPhone** configuration.

Le **profil de provisionnement** liste affiche tous les profils correspondants : vous devez voir les profils correspondants que vous avez créée dans cette liste déroulante.

![](appstore-images/options-selectprofile-sml.png "La boîte de dialogue signature du Bundle iOS")

<a name="iTunes_Connect"/>

## <a name="itunes-connect"></a>iTunes Connect

Suivez le [présentation de la distribution application](~/ios/deploy-test/app-distribution/index.md), en particulier :

- [Configuration d’une application dans iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [Publication dans l’App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)

Lors de la configuration de l’application dans iTunes Connect, n’oubliez pas d’ajouter l’icône espion et les captures d’écran :

![](appstore-images/itunesconnect-watch-sml.png "L’icône espion et les captures d’écran dans iTunes Connect")

Le fichier icône doit être de 1 024 x 1 024 pixels et aura un masque circulaire appliqué lorsqu’elle est affichée. L’icône ne doit pas avoir un canal alpha.

Au moins une capture d’écran est nécessaire, jusqu'à cinq peut être envoyée.
Leur 312 x 390 pixels et illustrer votre application Watch en action.
Vous pouvez utiliser le simulateur de watch 42mm pour prendre des captures d’écran à cette taille.


<a name="xamarin_studio" />

## <a name="visual-studio-for-mac"></a>Visual Studio pour Mac

1. Assurez-vous que l’application iOS est le projet de démarrage. Si ce n’est pas le cas, avec le bouton droit pour la définir :

   ![](appstore-images/xs-startup.png "Définition du projet de démarrage")

2. Choisissez le **AppStore** configuration de build :

   ![](appstore-images/xs-appstore.png "La configuration de build App Store")

3. Choisissez le **Générer > Archiver** élément de menu pour démarrer le processus d’archivage :

   ![](appstore-images/xs-archive.png "Le menu Générer")

Vous pouvez également choisir le **vue > Archives...**  élément de menu pour afficher les archives qui ont été créés précédemment.

  ![](appstore-images/xs-archives-sml.png "La vue Archives")

<a name="xcode" />

## <a name="xcode"></a>Xcode

Xcode affiche automatiquement les archives créés dans Visual Studio pour Mac.

1. Démarrez Xcode et choisissez **fenêtre > organisateur**:

   ![](appstore-images/xc-organizer.png "Le menu Fenêtre")

2. Basculez vers le **Archives** onglet et sélectionnez l’archive a été créé avec Visual Studio pour Mac :

   ![](appstore-images/xc-archives.png "L’onglet Archives")

3. Si vous le souhaitez **valider...**  l’archive, puis choisissez **envoyer...**  pour charger l’application à iTunes Connect.

4. Choisissez l’équipe de développement (si vous appartenez à plusieurs), puis confirmez la soumission :

   ![](appstore-images/xc-submit1.png "La section d’équipe de développement")

5. Visitez iTunes Connect à nouveau pour voir le fichier binaire chargé. Accédez à la page de configuration de votre application et choisissez **la version préliminaire** dans le menu supérieur pour afficher le **génère** liste :

   [![](appstore-images/itc-prerelease-sml.png "La page de configuration des applications dans iTunes Connect")](appstore-images/itc-prerelease.png#lightbox)

Vous pouvez ensuite soumettre l’application de réception sur le **Versions** page. Reportez-vous à la [présentation de la distribution iOS application](~/ios/deploy-test/app-distribution/index.md) pour plus d’informations.


## <a name="troubleshooting"></a>Résolution de problèmes

Voici certaines erreurs que vous pouvez rencontrer lors de l’envoi à l’App Store et les étapes à que suivre pour les corriger.

### <a name="archive-menu-option-is-not-visible-in-visual-studio-for-mac"></a>Option de menu d’archive n’est pas visible dans Visual Studio pour Mac

Suivez le [étapes ci-dessus](#xamarin_studio) pour configurer la solution pour l’archivage. Si vous ne pouvez pas correctement le projet de démarrage, vérifiez que la configuration de build est tout d’abord définie sur Debug ou Release avant de modifier le projet de démarrage. Réglez la configuration de build au **AppStore**.

### <a name="invalid-icon"></a>Icône Non valide

```csharp
Invalid Icon - The watch application '...watchkitextension.appex/WatchApp.app'
contains an icon file '...watchkitextension.appex/WatchApp.app/AppIcon27.5x27.5@2x.png'
with an alpha channel. Icons should not have an alpha channel.
```

Suivez le [instructions pour supprimer le canal alpha](~/ios/watchos/troubleshooting.md) à partir de vos icônes.

### <a name="cfbundleversion-mismatch"></a>Incompatibilité de valeur CFBundleVersion

```csharp
CFBundleVersion Mismatch. The CFBundleVersion value '1' of watch application
'...watchkitextension.appex/WatchApp.app' does not match the CFBundleVersion
value '1.0' of its containing iOS application `YouriOS.app`.
```

Tous les projets de votre solution, l’application iOS, l’Extension Watch et l’application Watch - doivent utiliser le même numéro de version. Modifiez chacun **Info.plist** de fichiers afin que le numéro de version correspond à exactement.

### <a name="missing-icons"></a>Icônes manquantes

```csharp
Missing Icons. No icons found for watch application '...watchkitextension.appex/WatchApp.app'.
Please make sure that its Info.plist file includes entries for CFBundleIconFiles.
```

Suivez les instructions de la [fonctionne avec des icônes](~/ios/watchos/app-fundamentals/icons.md) pour ajouter toutes les images nécessaires au projet d’application de surveillance.

### <a name="missing-icon"></a>Icône manquante

```csharp
Missing Icon. The watch application '...watchkitextension.appex/WatchApp.app'
is missing icon with name pattern '*44x44@2x.png' (Home Screen 42mm).
```

Assurez-vous d’avoir la dernière version de Visual Studio pour Mac et que votre **AppIcon.appiconset** contient un ensemble complet des images. Si cette erreur persiste, affichez la source de la **Contents.json** pour confirmer qu’il contient une entrée pour toutes les images nécessaires. Vous pouvez également, une fois que vous avez vérifié que vous utilisez la dernière version de Xamarin, supprimez et recréez le **AppIcon.appiconset**.

> [!IMPORTANT]
> Il existe un bogue connu dans Visual Studio pour la prise en charge de Mac espion icône : elle attend une image de pixel de 88 x 88 pour la **29x29@3x** image (qui doit être 87 x 87 pixels).


Vous ne pouvez pas résoudre ce problème dans Visual Studio pour Mac - modifier la ressource image dans Xcode ou modifier manuellement le **Contents.json** fichier (pour correspondre à [cet exemple](https://github.com/xamarin/monotouch-samples/blob/master/WatchKit/WatchKitCatalog/WatchApp/Resources/Images.xcassets/AppIcons.appiconset/Contents.json#L126-L132)).



### <a name="invalid-watchkit-support"></a>Prise en charge de WatchKit non valide

```csharp
Invalid WatchKit Support - The bundle contains an invalid implementation of WatchKit.
The app may have been built or signed with non-compliant or pre-release tools.
```

Ce message peut apparaître au cours de validation et la soumission, ou dans un message électronique automatisé d’iTunes Connect après un chargement apparemment réussi.
<!--
Ensure you are using the latest version of Xcode and Xamarin's tools.
-->
> [!IMPORTANT]
> Vous devez **Archive** votre application dans Visual Studio pour Mac et basculez ensuite vers Xcode 6.2 + valider et charger dans iTunes Connect.


Utilisez le canal de Xamarin Stable et Xcode 6.2 +.



### <a name="invalid-provisioning-profile"></a>Profil d’approvisionnement non valide

```csharp
Invalid Provisioning Profile. The provisioning profile included in the bundle
...iOSWatchApp.watchkitapp [iOSWatchApp.app/PlugIns/...iOSWatchApp.watchkitextension.appex/WatchApp.app]
is invalid. [Missing code-signing certificate.]
```

**Profils de provisionnement de distribution** doit être fourni pour les trois projets dans une solution d’application espion : l’application iOS, l’Extension Watch et l’application Watch - soit explicitement (les trois profils) ou via un profil générique unique. Vérifiez que les profils de provisionnement existent dans le centre de développement iOS et que vous avez téléchargé et ajoutés à votre Mac.

### <a name="invalid-code-signing-entitlements"></a>Droits de signature de Code non valide

```csharp
ITMS-90046: Invalid Code Signing Entitlements. Your application bundle's signature contains
code signing entitlements that are not supported on iOS. Specifically, value
'...watchkitextension' for key 'application-identifier' in '...watchkitextension'
is not supported. The value should be a string startign with your TEAMID, followed
by a dot '.' followed by the bundle identifier.
```

Vérifiez que vos profils de provisionnement sont configurés correctement sur le centre de développement Apple, et que vous avez téléchargé et installé les. Vérifiez également qu’ils sont définis dans Visual Studio pour la fenêtre de propriétés du Mac pour chaque projet.

### <a name="invalid-architecture"></a>Architecture non valide

```csharp
Invalid architecture: Apps that include an app extension
and framework must support arm64.
```

Vous pouvez uniquement ajouter des applications cadran [API unifiée (64 bits)](~/cross-platform/macios/unified/index.md) les applications Xamarin.iOS.
Avec le bouton droit sur le projet d’application iOS, puis accédez à **Options > Build > Build iOS > onglet Avancé** et vérifiez que le **pris en charge les Architectures** pour l’App Store-iPhone configuration inclut **ARM64** (par exemple). **ARMv7 + ARM64**).

### <a name="this-bundle-is-invalid"></a>Cette offre n’est pas valide.

```csharp
ITMS-90068: This bundle is invalid. The value provided for the key
MinimumOSVersion '8.3' is not acceptable.
```

Votre application iOS de parent doit avoir la MinimumOSVersion définie à '8.2' ou une version antérieure.

### <a name="non-public-api-usage"></a>Utilisation de l’API non publics

```csharp
Your app contains non-public API usage.
Please review the errors, and resubmit your application.
```

Vérifiez que vous utilisez la dernière version des outils Xcode et Xamarin.
Votre code ne doit pas accéder aux toutes les API non public.

### <a name="build-error-mt5309"></a>Générer l’erreur MT5309

```csharp
Error MT5309: Native linking error: clang: error: no such file or directory:
```

Cette erreur est probablement le résultat de votre avoir renommé de votre installation de Xcode **Xcode.app**. Par exemple, cette erreur se produit si vous renommez votre installation vers **XCode 6.2.app**.



## <a name="related-links"></a>Liens associés

- [Guide de présentation WatchKit Apple](https://developer.apple.com/app-store/watch/)
