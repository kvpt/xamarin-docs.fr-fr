---
title: Test sur des appareils Apple Watch
description: Ce document explique comment déployer une application Watchos générée avec Xamarin pour effectuer des tests sur un Apple Watch réel. Il aborde les appareils, les profils de provisionnement, les tests et fournit des conseils de dépannage.
ms.prod: xamarin
ms.assetid: A72A7D38-FAE8-4DD2-843D-54B74C5078D7
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: cbbf8194505e9caa09587471020026d495f9f99b
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84569694"
---
# <a name="testing-on-apple-watch-devices"></a>Test sur des appareils Apple Watch

Une fois que vous avez suivi les [étapes de déploiement](~/ios/watchos/deploy-test/index.md) pour créer des ID d’application et des groupes d’applications (si nécessaire), suivez les instructions de cette page pour effectuer les opérations suivantes :

- [Configurez vos appareils](#devices) dans le centre de développement Apple et
- [Créez des profils de provisionnement de développement](#profiles), puis
- [Déployez et testez](#testing) sur un Apple Watch.

<a name="devices"></a>

## <a name="devices"></a>Appareils

Tester des applications iOS sur un iPhone ou iPad réel a toujours requis l’inscription de l’appareil dans le centre de développement. La liste des appareils ressemble à ceci (cliquez sur le signe plus **+** pour ajouter un nouvel appareil) :

![](device-images/devices-sml.png "The device list looks like this")

Les espions ne sont pas différents : vous devez maintenant ajouter votre appareil Apple Watch avant de déployer des applications sur celui-ci. Recherchez le UDID du espion à l’aide de **Xcode** (**Windows > Devices** List). Lorsque le téléphone couplé est connecté, les informations du espion s’affichent également :

[![](device-images/xcode-devices-sml.png "Paired Watch Information")](device-images/xcode-devices.png#lightbox)

Lorsque vous connaissez le UDID du espion, ajoutez-le à la liste des appareils dans le centre de développement :

![](device-images/devices-watch-sml.png "The Watch's UDID in the device list")

Une fois l’appareil espion ajouté, assurez-vous qu’il est sélectionné dans tous les profils de développement nouveaux ou existants ou les profils de configuration ad hoc que vous créez :

![](device-images/devices-provisioning.png "Available device list")

N’oubliez pas que si vous modifiez un profil de provisionnement existant pour le téléchargement et la réinstallation !

<a name="profiles"></a>

## <a name="development-provisioning-profiles"></a>Profils de provisionnement de développement

Pour générer des tests sur votre appareil, vous devez créer un **profil de provisionnement de développement** pour chaque ID d’application dans votre solution.

Si vous avez un ID d’application générique, *un seul profil de provisionnement sera requis*; Toutefois, si vous disposez d’un ID d’application distinct pour chaque projet, vous aurez besoin d’un profil de configuration pour chaque ID d’application :

![](device-images/provisioningprofile-development.png "The Development Provisioning Profile")

Une fois que vous avez créé les trois profils, ceux-ci s’affichent dans la liste. N’oubliez pas de télécharger et d’installer chacun d’eux :

![](device-images/provisioningprofiles.png "The available Development Provisioning Profiles")

Vous pouvez vérifier le profil de provisionnement dans **les options du projet** en sélectionnant l’écran de signature de l' **offre groupée > iOS** , puis en sélectionnant la configuration de la **version** ou de l' **iPhone de débogage** .

La liste **profil de provisionnement** affiche tous les profils correspondants. vous devez voir les profils correspondants que vous avez créés dans cette liste déroulante :

![](device-images/options-selectprofile.png "The Provisioning Profile list")

<a name="testing"></a>

## <a name="testing-on-a-watch-device"></a>Test sur un appareil Watch

Une fois que vous avez configuré votre appareil, les ID d’application et les profils de provisionnement, vous êtes prêt à effectuer le test.

1. Assurez-vous que votre iPhone est branché et que la montre est déjà associée à l’iPhone.

2. Vérifiez que la configuration est définie sur **Release** ou **débogage**.

3. Vérifiez que l’appareil iPhone connecté est sélectionné dans la liste cible.

4. Cliquez avec le bouton droit sur le projet d’application iOS (pas sur l’espion ou l’extension) et choisissez **définir comme projet de démarrage**.

5. Cliquez sur le bouton **exécuter** (ou choisissez une option de **démarrage** dans le menu **exécuter** ).

6. La solution sera générée et l’application iOS sera déployée sur l’iPhone.
  Si l’approvisionnement de l’application iOS ou de l’extension Watch n’est pas défini correctement, le déploiement sur l’iPhone échouera.

7. Si le déploiement s’effectue correctement, l’iPhone tente automatiquement d’envoyer l’application Watch à la montre jumelée. L’icône de votre application s’affiche sur l’écran de surveillance avec un indicateur de progression de *l’installation* circulaire.

8. Si l’application Watch est correctement installée, l’icône reste sur l’écran Watch. Touchez-la pour commencer à tester votre application !

## <a name="troubleshooting"></a>Dépannage

Si une erreur se produit pendant le déploiement, utilisez la **vue > pad > journal** de l’appareil pour afficher plus d’informations sur l’erreur. Certaines erreurs et leurs causes sont répertoriées ci-dessous :

### <a name="error-mt3001-could-not-aot-the-assembly"></a>Erreur MT3001 : impossible d’AOA l’assembly

Cela peut se produire lors de la génération en mode débogage pour le déploiement sur un appareil Apple Watch.

Pour contourner ce problème *temporairement* , désactivez les **Builds incrémentielles** dans les options de projet de l’Extension Watch **> générer > fenêtre de génération Watchos** :

[![](device-images/disable-incremental-sml.png "The Incremental Builds checkbox")](device-images/disable-incremental.png#lightbox)

Ce problème sera résolu dans une version ultérieure, après quoi les builds incrémentielles pourront être réactivées pour tirer parti des temps de génération plus rapides.

### <a name="watch-app-fails-to-start-while-debugging-on-device"></a>L’application espion ne parvient pas à démarrer lors du débogage sur l’appareil

Lorsque vous tentez de déboguer une application espion sur un appareil physique, seule l’icône & le chargement du compteur s’affiche (et éventuellement le délai d’attente). Cela sera résolu dans une version ultérieure ; une solution de contournement consiste à exécuter une version RELEASE (qui n’autorise pas le débogage).

### <a name="invalid-application-executable-or-application-verification-failed"></a>L’exécutable de l’application ou la vérification de l’application n’est pas valide

```csharp
Failed to install [APPNAME]
Invalid executable/Application Verification Failed
```

![](device-images/invalid-application-executable.png "Invalid Application Executable alert")

Si ces messages s’affichent *sur l’écran Watch* après une tentative d’installation de l’application, il peut y avoir quelques problèmes :

- L’appareil espion lui-même n’a pas été ajouté en tant qu’appareil dans le centre de développement Apple. Suivez les instructions pour [configurer les appareils correctement](#devices).

- Les profils de provisionnement de développement utilisés pour les tests n’ont pas inclus l’appareil espion ; ou après l’ajout de la montre aux profils de provisionnement, ils n’ont pas été téléchargés et réinstallés. Suivez les instructions pour [configurer correctement les profils de provisionnement](#profiles).

- Si le **Journal de l’appareil iOS** contient, le fichier `The system version is lower than the minimum OS version specified for bundle...Have 8.2; need 8.3` **info. plist** de l’application espion a une valeur **MinimumOSVersion** incorrecte.
  Il doit s’agir de **8,2** -si vous avez installé Xcode 6,3, vous devrez peut-être modifier manuellement la source pour qu’elle soit définie sur 8,2.

- Les droits de l’application Watch **. plist** de manière incorrecte sont activés (par exemple, les groupes d’applications).

- L' **ID d’application** de l’application espion a incorrectement un droit activé (par exemple, des groupes d’applications) dans le centre de développement.

### <a name="install-never-finished"></a>Installation jamais terminée

```csharp
SPErrorGizmoInstallNeverFinishedErrorMessage
```

Cette erreur peut indiquer des clés inutiles (et non valides) dans le fichier **info. plist** de l’application Watch. Vous ne devez pas inclure de clés destinées à l’extension d’application ou de visionneuse iOS dans l’application Watch.

<!--eg. NSLocationAlwaysUsageDescription -->

### <a name="waiting-for-debugger-to-connect"></a>« en attente de la connexion du débogueur »

Si la fenêtre sortie de l' **application** est bloquée

```csharp
waiting for debugger to connect
```

Vérifiez si l’un des packages NuGet inclus dans votre projet a une dépendance vis-à-vis de **Microsoft. BCL. Build**. Elle est automatiquement ajoutée à certaines bibliothèques publiées par Microsoft, y compris les [bibliothèques clientes Microsoft http](https://www.nuget.org/packages/Microsoft.Net.Http/)populaires.

Le fichier **Microsoft. BCL. Build. targets** ajouté au fichier **. csproj** peut interférer avec l’empaquetage des extensions iOS au cours du déploiement. Vous pouvez suivre le [bogue](https://bugzilla.xamarin.com/show_bug.cgi?id=29912).
Une solution de contournement possible consiste à modifier le fichier. csproj et à déplacer manuellement le fichier **Microsoft. BCL. Build. targets** pour qu’il soit le dernier élément.
