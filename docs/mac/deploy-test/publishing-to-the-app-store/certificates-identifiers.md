---
title: Certificats et identificateurs dans Xamarin.Mac
description: Ce guide vous explique comment créer les certificats et identificateurs nécessaires à la publication d’une application Xamarin. Mac.
ms.prod: xamarin
ms.assetid: 393d0066-7f6f-4ac3-a48d-4b5db65bc4cd
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 12/17/2019
ms.openlocfilehash: 2ea3516c1fb89c8c9b9cc3694d7c95ccd87e9d41
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489649"
---
# <a name="certificates-and-identifiers-in-xamarinmac"></a>Certificats et identificateurs dans Xamarin.Mac

_Ce guide présente en détail la création des certificats et des identificateurs nécessaires exigés pour publier une application Xamarin.Mac._

## <a name="setup"></a>Programme d'installation

Visitez l’[Apple Developer Member Center](https://developer.apple.com) pour configurer le Mac pour le développement. Cliquez sur le lien du **compte** et connectez-vous. Le menu principal est affiché ci-dessous :

> [!div class="mx-imgBorder"]
> [![l’Apple Developer Member Center](certificates-identifiers-images/devcenter01.png)](certificates-identifiers-images/devcenter01-large.png#lightbox)

Cliquez sur le bouton **certificats, identificateurs & profils** (ou sur le bouton plus près de l’en-tête **certificats** ) :

> [!div class="mx-imgBorder"]
> [![sélection de certificats, ID & profils](certificates-identifiers-images/devcenter02.png)](certificates-identifiers-images/devcenter02-large.png#lightbox)

Sélectionnez un type de certificat, puis cliquez sur **Continuer**:

> [!div class="mx-imgBorder"]
> [![sélectionnant le lien certificats](certificates-identifiers-images/devcenter03.png)](certificates-identifiers-images/devcenter03-large.png#lightbox)

À partir de là, vous pouvez télécharger les **certificats intermédiaires** (Worldwide Developer Relations Certificate Authority and Developer ID Certificate Authority) si nécessaire (dernier élément en bas de la page). Toutefois, ceux-ci doivent être automatiquement configurés par Xcode pour le développeur.

Le reste de cette section décrit les sections relatives aux développeurs Mac :

- **Inscrire l’ID d’application Mac** : le développeur doit suivre ces étapes pour chaque application qu’il écrit.
- **Inscrire les systèmes macOS** : cette opération est obligatoire uniquement lors de l’ajout d’ordinateurs sur lesquels effectuer les tests.
- **Créer des certificats** : obligatoire une seule fois lors de la configuration des certificats, et ultérieurement lors de leur renouvellement.
- **Créer un profil de provisionnement** : le développeur doit suivre ces étapes pour chaque nouvelle application écrite et lors de l’ajout de nouveaux systèmes.

## <a name="register-mac-app-id"></a>Inscrire l’ID d’application Mac

Vous devez inscrire un ID d’application pour chaque application. Suivez les étapes ci-dessous pour créer une entrée :

1. Appuyez sur « + » (signe plus) ou **Enregistrez un ID d’application**:

    > [!div class="mx-imgBorder"]
    > [![la prise en main des ID d’application](certificates-identifiers-images/appid01.png)](certificates-identifiers-images/appid01-large.png#lightbox)

1. Choisir des **ID d’application**

    > [!div class="mx-imgBorder"]
    > [![la prise en main des ID d’application](certificates-identifiers-images/appid02.png)](certificates-identifiers-images/appid02-large.png#lightbox)

1. Entrez une **Description**, puis sélectionnez les **app services** nécessaires à l’application : a. La plateforme doit être **MacOS** a. Choisissez une **Description** (utilisée uniquement dans ce portail) a. Entrez l' **ID de Bundle**, qui doit correspondre à votre fichier **info. plist** . Sélectionner les fonctionnalités requises par votre application

    > [!div class="mx-imgBorder"]
    > [![la saisie de la description et des services d’application](certificates-identifiers-images/appid03.png)](certificates-identifiers-images/appid03-large.png#lightbox)

    Appuyez sur **Continuer** pour passer en revue vos sélections.

1. Si les informations sont correctes, cliquez sur **inscrire** pour terminer l’installation :

    > [!div class="mx-imgBorder"]
    > [![vérifier les données entrées](certificates-identifiers-images/appid04.png)](certificates-identifiers-images/appid04-large.png#lightbox)

1. Vérifiez les informations, puis cliquez sur le bouton **Submit** :

    > [!div class="mx-imgBorder"]
    > ![de la vérification des informations](certificates-identifiers-images/appid05.png)

Certains **services d’application** peuvent nécessiter une configuration supplémentaire (par exemple, iCloud). Si tel est le cas, sélectionnez le nouvel ID d’application que vous venez de créer, puis cliquez sur le bouton **Modify** :

> [!div class="mx-imgBorder"]
> [![modification du nouvel ID d’application](certificates-identifiers-images/appid06.png)](certificates-identifiers-images/appid06-large.png#lightbox)

Pour configurer les services iCloud, par exemple, cliquez sur le bouton **modifier** :

> [!div class="mx-imgBorder"]
> [![la configuration des services iCloud](certificates-identifiers-images/appid07.png)](certificates-identifiers-images/appid07-large.png#lightbox)

## <a name="register-macos-devices"></a>Inscrire des appareils macOS

Pour créer un profil de provisionnement à des fins de test, les ordinateurs Mac des développeurs doivent être inscrits. Un maximum de 100 ordinateurs peuvent être inscrits pour le test.

1. Dans le Mac Developer Center, sélectionnez **All** dans la section **Devices**, puis cliquez sur le bouton **+**  :

    > [!div class="mx-imgBorder"]
    > [![de l’ajout d’un nouvel ordinateur](certificates-identifiers-images/device01.png)](certificates-identifiers-images/device01-large.png#lightbox)

1. Entrez un **nom** et l’**UUID** de l’ordinateur à ajouter, puis cliquez sur le bouton **Continue**. Examinez les informations, puis cliquez sur le bouton **Register** :

    > [!div class="mx-imgBorder"]
    > [![entrée des informations sur l’ordinateur](certificates-identifiers-images/device02.png)](certificates-identifiers-images/device02-large.png#lightbox)

1. Vérifiez et confirmez les données entrées :

    > [!div class="mx-imgBorder"]
    > [![entrée des informations sur l’ordinateur](certificates-identifiers-images/device03.png)](certificates-identifiers-images/device03-large.png#lightbox)

## <a name="create-certificates"></a>Créer des certificats

La section Certificates permet de créer différents types de certificats qui seront utilisés pour signer les applications Mac :

> [!div class="mx-imgBorder"]
> [![Création d’un certificat](certificates-identifiers-images/devcenter04.png)](certificates-identifiers-images/devcenter04-large.png#lightbox)

Il existe cinq types principaux de certificats relatifs au développement macOS :

- **Développement Mac** : facultatif pour le développement d’applications générales, mais requis si le développeur prévoit d’utiliser des fonctionnalités telles que icloud ou les notifications push. Le développeur doit disposer d’un certificat de développement avant de créer des profils de provisionnement qui l’autorisent à accéder à ces fonctionnalités.
- **Distribution d’applications Mac** : le développeur doit avoir un certificat pour son application et un autre certificat pour le programme d’installation.
- **Distribution du programme d’installation Mac** : le développeur doit avoir un certificat pour son application et un autre certificat pour le programme d’installation.
- **Programme d’installation** de l’ID de développeur : certificats pour le programme d’installation à distribuer en dehors de l’App Store Mac.
- **Application ID de développeur** : certificats pour l’application à distribuer en dehors de l’App Store Mac.

Les sections suivantes fournissent des exemples de création de certains de ces types de certificats.

### <a name="mac-development-certificate"></a>Certificat de développement Mac

Comme mentionné précédemment, le certificat de développement Mac n’est pas nécessaire, sauf si des fonctionnalités macOS comme iCloud ou les notifications push sont utilisées.

Effectuez les étapes suivantes pour créer un certificat de développement :

1. Sélectionnez la case d’option **Mac Development**, puis cliquez sur **Continue** :

    > [!div class="mx-imgBorder"]
    > [![ajout d’un certificat de développement](certificates-identifiers-images/certif02.png)](certificates-identifiers-images/certif02-large.png#lightbox)

1. Téléchargez une _demande de signature de certificat_. Le fichier de demande de certificat (`.certSigningRequest`d’extension) sera enregistré localement sur le Mac. Cliquez sur **choisir un fichier** pour sélectionner la demande de certificat, puis appuyez sur **Continuer**.

    > [!div class="mx-imgBorder"]
    > [![de charger un fichier de demande de certificat](certificates-identifiers-images/certif03.png)](certificates-identifiers-images/certif03-large.png#lightbox)

    Pour obtenir des instructions sur l’utilisation de l' **accès au trousseau** pour créer un fichier de demande de certificat, suivez le lien [en savoir plus >](https://help.apple.com/developer-account/#/devbfa00fef7) .

1. Appuyez sur **Télécharger** pour accéder au fichier de certificat, puis double-cliquez dessus pour l’installer :

    > [!div class="mx-imgBorder"]
    > [![Télécharger le fichier de certificat](certificates-identifiers-images/certif04.png)](certificates-identifiers-images/certif04-large.png#lightbox)

Comme mentionné précédemment, le certificat de développeur n’est pas toujours obligatoire, sauf si le développeur implémente des fonctionnalités macOS comme iCloud et les notifications Push. Il est également obligatoire pour créer un **profil de provisionnement de développement**, qui sera nécessaire pour tester les applications Mac App Store.

### <a name="mac-app-store-certificates"></a>Certificats Mac App Store

Pour libérer une application sur l’App Store, vous avez besoin de deux certificats :

- Certificat de **distribution d’application Mac** qui sera utilisé pour signer l’application ; les 
- Certificat de **distribution du programme d’installation Mac** , pour signer le programme d’installation.

> [!TIP]
> Soyez prudent quand vous nommez les demandes de certificat pour ces clés : utilisez des noms descriptifs qui contiennent le texte `Application` et `Installer` afin qu’ils puissent être distingués ultérieurement.

Tout d’abord, créez le certificat du programme d’installation :

1. Sélectionnez **distribution du programme d’installation Mac** comme type de certificat, puis cliquez sur le bouton **Continuer** :

    > [!div class="mx-imgBorder"]
    > [![de la création d’un certificat App Store](certificates-identifiers-images/certif05.png)](certificates-identifiers-images/certif05-large.png#lightbox)

1. La page suivante explique comment utiliser l’**accès au trousseau** pour générer un fichier de demande de certificat. Suivez les instructions spécifiées :

    > [!div class="mx-imgBorder"]
    > [![télécharger une demande de certificat](certificates-identifiers-images/certif06.png)](certificates-identifiers-images/certif06-large.png#lightbox)

    Pour obtenir des instructions sur l’utilisation de l' **accès au trousseau** pour créer un fichier de demande de certificat, suivez le lien [en savoir plus >](https://help.apple.com/developer-account/#/devbfa00fef7) . N’oubliez pas de choisir un nom de certificat qui reflète le _type_ de certificat (application ou programme d’installation).

1. Cliquez sur **Download** pour obtenir votre certificat, puis double-cliquez dessus pour l’installer dans le **trousseau** :

    > [!div class="mx-imgBorder"]
    > [![Télécharger le certificat de l’App Store](certificates-identifiers-images/certif07.png)](certificates-identifiers-images/certif07-large.png#lightbox)

**Suivez les mêmes étapes pour le certificat de distribution d’application Mac.**

![Certificat de distribution d’application Mac](certificates-identifiers-images/certif08.png)

### <a name="developer-id-certificates"></a>Certificats d’ID de développeur

Pour effectuer une auto-publication d’une application Xamarin. Mac (qui n’est pas publiée via l’Apple App Store), vous avez besoin de deux certificats :

- Certificat du programme d’installation de l' **ID de développeur** qui sera utilisé pour signer l’application ; les 
- Certificat d' **application d’ID de développeur** , pour signer le programme d’installation.

> [!TIP]
> Soyez prudent quand vous nommez les demandes de certificat pour ces clés : utilisez des noms descriptifs qui contiennent le texte `Application` et `Installer` afin qu’ils puissent être distingués ultérieurement.

Une fois que vous avez créé, téléchargé et installé des certificats, ils sont visibles dans le **trousseau d’accès**:

[Liste des certificats d’accès au trousseau](certificates-identifiers-images/certif09.png)

## <a name="related-links"></a>Liens connexes

- [Installation](/visualstudio/mac/installation/)
- [Exemple Hello, Mac](~/mac/get-started/hello-mac.md)
- [Distribuer vos applications sur le Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [ID de développeur et GateKeeper](https://developer.apple.com/resources/developer-id/)
