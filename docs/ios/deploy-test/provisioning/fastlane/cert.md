---
title: 'fastlane pour iOS : cert'
description: 'Ce document décrit fastlane, un outil permettant d’automatiser de nombreuses parties du processus de provisionnement d’application iOS : demande de certificats, ajout d’un appareil au portail des développeurs Apple, création d’un ID d’application et bien plus encore.'
ms.prod: xamarin
ms.assetid: 900FA6FF-F3C9-4D35-993E-B0D88E6B1883
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 8236dc01f31141544bef88011d87a457f18072e8
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69526563"
---
# <a name="fastlane-for-ios--cert"></a>fastlane pour iOS : cert

> [!IMPORTANT]
> fastlane recommande l’utilisation de l’outil [`match`](~/ios/deploy-test/provisioning/fastlane/match.md) pour la génération et la gestion des certificats. Utilisez `cert` directement seulement si vous voulez avoir un contrôle total de la signature de code et si vous maîtrisez suffisamment ce sujet. Utilisez cette action pour télécharger l’identité de signature de code la plus récente.

## <a name="overview"></a>Vue d'ensemble

En règle générale, le provisionnement des appareils est effectué par chaque membre d’une équipe de développement, à l’aide de l’outil Xcode ou à partir du portail des développeurs d’Apple. Ce processus comprend les étapes suivantes :

- Demande d’un certificat de développement
- Ajout d’un appareil au portail
- Création d’un ID d’application
- Création d’un profil de provisionnement
- Téléchargement des profils et certificats

À chacune de ces étapes, vous devez définir certaines variables qui dépendent du type de l’application développée. Le guide [Provisionnement d’un appareil](~/ios/get-started/installation/device-provisioning/index.md) fournit plus d’informations sur les étapes à effectuer pour configurer, manuellement ou à l’aide de l’outil Xcode, un appareil pour le développement.

Ce guide explique comment utiliser les outils fastlane à la place de l’outil Xcode. Il contient les rubriques suivantes :

- [Présentation de cert](#whatiscert)
- [Utilisation de cert](#using)
- [Options supplémentaires](#options)

## <a name="installation"></a>Installation

Pour plus d’informations sur l’installation et la mise à jour de fastlane, consultez le guide [fastlane](~/ios/deploy-test/provisioning/fastlane/index.md#Installation).

<a name="whatiscert" />

## <a name="what-is-cert"></a>Présentation de cert

cert fournit une interface de terminal qui permet de créer des identités de signature de code (souvent désignées sous le terme de _certificat_ de développeur) pour les environnements de développement et de distribution.

<a name="using" />

## <a name="using-cert"></a>Utilisation de cert

Pour utiliser l’utilitaire cert, entrez la commande suivante dans l’interface CLI du terminal :

```
fastlane cert
```

Par défaut, cette commande crée un certificat de distribution. Pour créer un certificat de développement, ajoutez l’indicateur `--development` :

```
fastlane cert --development
```

cert vous invite à entrer votre ID Apple et votre mot de passe, comme ceci :

[![](cert-images/fastlane-image1.png "Invite cert pour entrer votre ID Apple et votre mot de passe")](cert-images/fastlane-image1.png#lightbox)

> [!IMPORTANT]
> La première fois que vous entrez votre mot de passe, celui-ci est enregistré dans le trousseau local macOS. Vous pouvez également utiliser des variables d’environnement pour stocker le nom d’utilisateur et le mot de passe, ou utiliser `export fastlane_DONT_STORE_PASSWORD=1` si vous ne souhaitez pas stocker votre mot de passe dans le trousseau. Pour plus d’informations sur la gestion des informations d’identification avec fastlane, consultez le [guide CredentialsManager](https://github.com/fastlane/fastlane/blob/master/credentials_manager/README.md) de fastlane.

Vous pouvez également passer l’ID Apple comme argument à l’aide de la commande suivante :

```
fastlane cert -u myemailadress@domain.com
```

Si votre ID Apple est associé à plusieurs équipes, les équipes en question sont répertoriées ici. Sélectionnez le numéro correspondant à l’équipe appropriée :

[![](cert-images/fastlane-image2.png "Sélectionner l’équipe que vous voulez utiliser")](cert-images/fastlane-image2.png#lightbox)

Vous pouvez également passer l’ID de l’équipe en spécifiant l’indicateur suivant :

```
fastlane cert -l 2TU993NY9J
```

fastlane vérifie ensuite si l’un des certificats de signature disponibles est installé sur votre machine locale et, le cas échéant, l’utilise.

S’il ne trouve pas de certificat de signature, cert va :

- Créer une clé privée et une demande de signature
- Générer, télécharger et installer le certificat
- Importer le certificat et la clé privée dans le trousseau

Si le nombre maximal d’identités de signature autorisées pour votre compte est atteint, une exception est levée. Pour pouvoir créer une autre identité de signature, vous devez alors révoquer manuellement l’un des certificats existants à partir du developer center, puis réessayer.

> [!NOTE]
> fastlane ne peut pas télécharger des identités de signature existantes du Centre de développement, si ces identités ne sont pas déjà dans votre trousseau. Cela est dû au fait que la clé privée est stockée uniquement sur votre ordinateur, ou dans une version exportée (*.p12) du certificat, mais jamais dans le developer center.

<a name="options" />

## <a name="additional-options"></a>Options supplémentaires

Vous pouvez utiliser les options suivantes pour bénéficier d’une assistance supplémentaire quand vous utilisez cert :

- Utilisez l’indicateur `-–help` pour obtenir la liste de toutes les commandes disponibles :

    ```
    fastlane cert --help
    ```

- Utilisez l’indicateur `-–verbose` pour afficher des commentaires plus détaillés dans la sortie

    ```
    fastlane cert --development --verbose
    ```

## <a name="related-links"></a>Liens associés

- [fastlane - cert](https://github.com/fastlane/fastlane/blob/master/cert/README.md)
