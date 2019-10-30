---
title: fastlane pour iOS - sigh
description: Ce document décrit la commande sigh de fastlane, qui sert à créer, renouveler et réparer des profils de provisionnement pour toutes les configurations de build Xamarin.iOS.
ms.prod: xamarin
ms.assetid: CD17276F-2C8C-4A46-A54C-DD532EBD5720
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 9506d80ab10ce70938f95371a52bf28bda33d42d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028494"
---
# <a name="fastlane-for-ios-sigh"></a>fastlane pour iOS - sigh

> [!IMPORTANT]
> fastlane recommande l’utilisation de [`match`](~/ios/deploy-test/provisioning/fastlane/match.md) pour la génération et la gestion de vos profils d’approvisionnement. Utilisez sigh directement seulement si vous voulez avoir un contrôle total de la signature de code et si vous maîtrisez suffisamment ce sujet.

## <a name="overview"></a>Vue d'ensemble

En règle générale, le provisionnement des appareils est effectué par chaque membre d’une équipe de développement, à l’aide de l’outil Xcode ou à partir du portail des développeurs d’Apple. Ce processus comprend les étapes suivantes :

- Demande d’un certificat de développement
- Ajout d’un appareil au portail
- Création d’un ID d’application
- Création d’un profil de provisionnement
- Téléchargement des profils et certificats

À chacune de ces étapes, vous devez définir certaines variables qui dépendent du type de l’application développée. Le guide [Provisionnement d’un appareil](~/ios/get-started/installation/device-provisioning/index.md) fournit plus d’informations sur les étapes à effectuer pour configurer, manuellement ou à l’aide de l’outil Xcode, un appareil pour le développement.

Ce guide explique comment utiliser les outils fastlane à la place de l’outil Xcode. Il contient les rubriques suivantes :

- [Présentation de sigh](#whatissigh)
- [Création d’un ID d’application](#appid)
- [Ajout de nouveaux appareils](#newdevices)
- [Utilisation de sigh](#using)
- [Options supplémentaires](#options)

> [!NOTE]
> S’il existe déjà un ID d’application qui correspond à l’ID de l’offre groupée de votre application et si votre appareil figure déjà dans le portail des développeurs, vous pouvez ignorer les étapes [Création d’un ID d’application](#appid) et [Ajout d’un appareil](#newdevices). Dans ce cas, passez directement à [Utilisation de sigh](#using) pour démarrer.

## <a name="installation"></a>Installation

Pour plus d’informations sur l’installation de fastlane, consultez le guide [fastlane](~/ios/deploy-test/provisioning/fastlane/index.md#Installation).

<a name="whatissigh" />

## <a name="what-is-sigh"></a>Présentation de sigh

sigh fournit une interface de terminal qui permet de créer et renouveler des profils de provisionnement pour toutes les configurations : développement, distribution sur l’App Store, distribution ad hoc et distribution en entreprise. Il offre également un moyen simple de télécharger et réparer les profils de provisionnement.

<a name="appid" />

## <a name="creating-an-app-id"></a>Création d’un ID d’application

Vous pouvez créer un ID d’application avec la commande suivante :

```
fastlane produce -u your@appleid.com -a com.company.appname --skip_itc
```

`com.company.appname` correspond à l’ID de bundle de l’application, qui est indiqué dans le fichier Info.plist de votre application Xamarin.iOS, comme illustré ci-dessous :

[![](sigh-images/fastlane-image5.png "The Info.plist file of the Xamarin.iOS application")](sigh-images/fastlane-image5.png#lightbox)

L’ID d’application unique doit être une chaîne de style DNS inversé. Conservez bien ce nouvel ID, car vous en aurez besoin un plus tard pour utiliser sigh.

Si vous souhaitez créer l’application sur [iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md), supprimez l’indicateur `--skip_itc` de la commande ci-dessus.

<a name="newdevices" />

## <a name="adding-new-devices"></a>Ajout de nouveaux appareils

Pour ajouter un seul appareil dans le portail des développeurs à partir de la ligne de commande, entrez la commande suivante :

```bash
fastlane run register_device name:"Adam iPhone" udid:"abcdeg1234567"
```

Pour ajouter plusieurs appareils, utilisez la commande `register_devices` :

```bash
    register_devices(
        devices: {
            "iPhone 6" => "1234567890123456789012345678901234567890",
            "iPad Air 2" => "abcdefghijklmnopqrstvuwxyzabcdefghijklmn"
         }
    )
```

<a name="using" />

## <a name="using-sigh"></a>Utilisation de sigh

Pour lancer l’utilitaire sigh, entrez la commande suivante dans votre terminal :

```bash
fastlane sigh
```

Par défaut, cette commande crée un profil de provisionnement pour une [distribution sur l’App Store](~/ios/deploy-test/app-distribution/app-store-distribution/index.md). Pour configurer votre appareil pour le développement, passez l’indicateur `--development` :

```bash
fastlane sigh --development
```

Entrez votre nom d’utilisateur (Apple ID username) quand vous y êtes invité par fastlane. Vous êtes également invité à entrer le mot de passe si vous utilisez fastlane pour la première fois. Sinon, la variable d’environnement du mot de passe est tirée (pull) du trousseau.

Si votre ID Apple est associé à plusieurs équipes, les équipes en question sont répertoriées ici. Sélectionnez le numéro correspondant à l’équipe appropriée :

[![](sigh-images/fastlane-image2.png "Select the team that you wish to use")](sigh-images/fastlane-image2.png#lightbox)

Vous pouvez également passer l’ID de l’équipe à l’interface CLI de la manière suivante :

```bash
fastlane sigh -l 2TU993NY9J
```

Entrez l’[ID d’application](#appid) de votre application. N’oubliez pas que cet ID doit correspondre à l’ID de bundle figurant dans le fichier Info.plist de l’application.

Tous les appareils associés à votre compte seront ajoutés à votre profil de provisionnement.

fastlane crée, télécharge et installe votre profil de provisionnement à votre place.

Vous pouvez accéder au developer center pour afficher le nouveau profil de provisionnement, comme illustré ci-dessous :

[![](sigh-images/fastlane-image10.png "View the newly created provisioning profile")](sigh-images/fastlane-image10.png#lightbox)

sigh stocke les profils de provisionnement dans le dossier actif, par défaut. Pour changer de répertoire de sortie, modifiez `output_path` ou utilisez la commande suivante :

```bash
fastlane sigh -o "~/Library/MobileDevice/Provisioning Profiles"
```

<a name="options" />

## <a name="sigh-additional-options"></a>Options supplémentaires

Vous pouvez utiliser les options suivantes pour bénéficier d’une assistance supplémentaire quand vous utilisez sigh :

- Pour télécharger tous les profils de provisionnement, utilisez :

    ```bash
    fastlane sigh download_all
    ```

- Pour utiliser une identité de signature spécifique pour votre utilisation du profil de provisionnement :

    ```bash
    fastlane sigh -c "Amy cert"
    ```
    
    Où `Amy cert` est le nom de l’identité de signature de code.

## <a name="related-links"></a>Liens associés

- [fastlane - sigh](https://github.com/fastlane/fastlane/tree/master/sigh#readme)
