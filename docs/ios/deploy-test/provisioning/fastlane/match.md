---
title: 'fastlane pour iOS : match'
description: Ce document décrit la commande match de fastlane, qui sert à créer et à tenir à jour les certificats de signature de code et les profils de provisionnement pour le développement iOS.
ms.prod: xamarin
ms.assetid: C4A2A67E-0643-4CED-B1A9-79D65054F3CA
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 08fdcafe33e4b9b2aa6b5595c732718878be33f2
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028510"
---
# <a name="fastlane-for-ios---match"></a>fastlane pour iOS : match

En règle générale, le provisionnement des appareils est effectué par chaque membre d’une équipe de développement, à l’aide de l’outil Xcode ou à partir du portail des développeurs d’Apple. Ce processus comprend les étapes suivantes :

- Demande d’un certificat de développement
- Ajout d’un appareil au portail
- Création d’un ID d’application
- Création d’un profil de provisionnement
- Téléchargement des profils et certificats

Le guide [Provisionnement d’un appareil](~/ios/get-started/installation/device-provisioning/index.md) fournit plus d’informations sur les étapes à effectuer pour configurer, manuellement ou à l’aide de l’outil Xcode, un appareil pour le développement.

Ce guide explique comment utiliser les outils fastlane à la place de l’outil Xcode.

## <a name="installation"></a>Installation

Pour plus d’informations sur l’installation de fastlane, consultez le guide [fastlane](~/ios/deploy-test/provisioning/fastlane/index.md#Installation).

<a name="whatismatch" />

## <a name="what-is-match"></a>Présentation de match

match crée et gère les certificats de signature de code et les profils de provisionnement, et permet à tous les membres d’une équipe de développement iOS de partager une identité de signature de code unique.

Pour déployer une application sur l’App Store, tester les versions bêta ou installer l’application sur un appareil, chaque membre d’une équipe de développement utilise généralement sa propre identité de signature. Cette pratique risque d’engendrer des conflits entre les identités et les profils, et de vous contraindre à créer, changer et gérer manuellement les profils et les ID d’application.

Pour éviter ce problème, match crée et gère tous les profils et certificats à votre place, et les stocke dans un référentiel Git privé. Tous les développeurs d’une équipe peuvent alors utiliser ces informations d’identification. La sécurisation accrue des certificats est un autre avantage de match : en plus d’être stockés dans un référentiel Git privé, les certificats sont chiffrés avec une [phrase secrète](#passphrase). Grâce au stockage des artefacts de signature de code dans un référentiel, les administrateurs et agents de l’équipe peuvent effectuer les opérations de mise à jour et de rotation des certificats nécessaires, ce qui leur évite de perdre du temps à distribuer de nouveaux certificats à chaque développeur.

> [!IMPORTANT]
> match ne prend pas en charge les profils d’entreprise internes.

<a name="initializing" />

## <a name="initializing-your-project-with-match"></a>Initialisation de votre projet avec match

Si vous êtes administrateur de l’équipe, créez un référentiel Git privé par le biais de github.com ou de bitbucket.com, puis ajoutez à ce référentiel chaque membre de l’équipe en tant que contributeur.

Dans le terminal, basculez vers le répertoire du projet et exécutez :

```
fastlane match init
```

Lorsque vous y êtes invité, entrez l’URL du référentiel Git :

 [![](match-images/fastlane-image7.png "Enter the URL of the git repo")](match-images/fastlane-image7.png#lightbox)

Vous pouvez rechercher et copier l’URL en cliquant sur le bouton **Clone or download** sur github.com, comme illustré ci-dessous :

[![](match-images/fastlane-image6.png "The URL under the Clone or Download button on github.com")](match-images/fastlane-image6.png#lightbox)

L’initialisation du projet crée un matchfile, qui est un fichier texte que vous pouvez modifier pour passer des variables d’environnement à l’outil match. Un exemple de matchfile est illustré ci-dessous :

[![](match-images/fastlane-image8.png "A matchfile example")](match-images/fastlane-image8.png#lightbox)

<a name="running" />

## <a name="running-match"></a>Exécution de match

> [!NOTE]
> Si vous exécutez match pour la première fois, fastlane vous recommande d’effacer préalablement vos profils et certificats existants à l’aide de la [commande match nuke](#using).

En fonction du type d’environnement ciblé, utilisez l’une des commandes suivantes pour créer un certificat et un profil de provisionnement, et les stocker dans votre nouveau référentiel Git :

```
fastlane match appstore

fastlane match adhoc

fastlane match development
```

Ces commandes, en plus de créer des certificats et profils, ajoutent les éléments suivants (ou les mettent à jour s’ils existent déjà) dans votre référentiel Git :

- Un dossier Certificats
- Un dossier Profils
- Un fichier README contenant les instructions de base
- Une version de match

[![](match-images/fastlane-image9.png "The project structure in the git repo")](match-images/fastlane-image9.png#lightbox)

Les profils de provisionnement sont créés dans `~/Library/MobileDevice/Provisioning Profiles`. Les certificats et les clés privées sont créés directement dans votre trousseau.

<a name="using" />

### <a name="using-the-nuke-command"></a>Utilisation de la commande `nuke`

Si vous avez des certificats non nettoyés, vous pouvez utiliser `nuke` afin de révoquer des certificats et des profils pour chaque environnement, en utilisant les commandes suivantes :

```
fastlane match nuke
```

Pour révoquer la totalité des certificats et des profils de provisionnement d’un environnement spécifique, exécutez :

```
fastlane match nuke development
```

 or

```
fastlane match nuke distribution
```

fastlane vous demande de confirmer la suppression des fichiers indiqués avant de les supprimer.

<a name="passphrase" />

### <a name="passphrase"></a>Phrase secrète

Quand vous exécutez `match` pour la première fois, vous êtes invité à définir une phrase secrète pour le référentiel Git. Conservez bien ce mot de passe, car vous en aurez besoin si vous exécutez match sur une autre machine. Cette couche de sécurité supplémentaire chiffre chaque fichier avec OpenSSL. À chaque première exécution de `match` sur une nouvelle machine, vous devrez entrer votre phrase secrète, laquelle sera alors ajoutée au trousseau local.

Pour définir la phrase secrète de déchiffrement de vos profils à l’aide d’une variable d’environnement, utilisez `MATCH_PASSWORD`.

<a name="options" />

## <a name="additional-options"></a>Options supplémentaires

Vous pouvez utiliser les options suivantes pour bénéficier d’une assistance supplémentaire quand vous utilisez match :

- Utilisez l’indicateur `-–help` pour obtenir la liste de toutes les commandes disponibles :

    ```
    fastlane match cert --help
    ```

- Utilisez l’indicateur `-–verbose` pour afficher des commentaires plus détaillés dans la sortie :

    ```
    fastlane match --development --verbose
    ```

- Utilisez l’indicateur `--force_for_new_devices` pour forcer le renouvellement des profils de provisionnement en cas de changement du nombre d’appareils sur le portail des développeurs.

    ```
    fastlane match development --force_for_new_devices
    ```

## <a name="related-links"></a>Liens associés

- [fastlane - match](https://github.com/fastlane/fastlane/blob/master/match/README.md)
