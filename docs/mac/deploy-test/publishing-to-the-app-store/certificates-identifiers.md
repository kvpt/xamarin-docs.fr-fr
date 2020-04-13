---
title: Certificats et identificateurs dans Xamarin.Mac
description: Ce guide présente en détail la création des certificats et des identificateurs nécessaires pour publier une application Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 393d0066-7f6f-4ac3-a48d-4b5db65bc4cd
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 12/17/2019
ms.openlocfilehash: 2b2bfe9925a99c2ba7f1366ea28d5c72e2e1da88
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "76725539"
---
# <a name="certificates-and-identifiers-in-xamarinmac"></a>Certificats et identificateurs dans Xamarin.Mac

_Ce guide présente en détail la création des certificats et des identificateurs nécessaires pour publier une application Xamarin.Mac._

## <a name="setup"></a>Programme d’installation

Visitez l’[Apple Developer Member Center](https://developer.apple.com) pour configurer le Mac pour le développement. Cliquez sur le lien **Account** et connectez-vous. Le menu principal est affiché ci-dessous :

> [!div class="mx-imgBorder"]
> [![Le Centre des membres du développeur Apple](certificates-identifiers-images/devcenter01.png)](certificates-identifiers-images/devcenter01-large.png#lightbox)

Cliquez sur le bouton **Certificates, Identifiers & Profiles** (Certificats, identificateurs et profils) (ou sur le bouton plus en regard de l’en-tête **Certificatse**) :

> [!div class="mx-imgBorder"]
> [![Sélection des certificats, des DIU & Profils](certificates-identifiers-images/devcenter02.png)](certificates-identifiers-images/devcenter02-large.png#lightbox)

Sélectionnez un type de certificat, puis cliquez sur **Continue** :

> [!div class="mx-imgBorder"]
> [![Sélection du lien Certificats](certificates-identifiers-images/devcenter03.png)](certificates-identifiers-images/devcenter03-large.png#lightbox)

À partir de là, vous pouvez télécharger les **certificats intermédiaires** (Autorité de certification des relations des développeurs dans le monde entier et Autorité de certification d’ID de développeur) si nécessaire (dernier élément en bas de la page). Toutefois, ceux-ci doivent être automatiquement configurés par Xcode pour le développeur.

Le reste de cette section décrit les sections qui concernent les développeurs Mac :

- **Inscrire l’ID d’application Mac** : le développeur doit suivre ces étapes pour chaque application qu’il écrit.
- **Inscrire les systèmes macOS** : cette opération est obligatoire uniquement lors de l’ajout d’ordinateurs sur lesquels effectuer les tests.
- **Créer des certificats** : opération nécessaire une seule fois lors de la configuration des certificats, et ultérieurement lors de leur renouvellement.
- **Créer un profil de provisionnement** : le développeur doit suivre ces étapes pour chaque nouvelle application écrite et lors de l’ajout de nouveaux systèmes.

## <a name="register-mac-app-id"></a>Inscrire l’ID d’application Mac

Vous devez inscrire un ID d’application pour chaque application. Effectuez les étapes suivantes pour créer une entrée :

1. Appuyez sur « + » (signe plus) ou sur **Register an App ID** (Inscrire un ID d’application) :

    > [!div class="mx-imgBorder"]
    > [![Commencer avec les ID App](certificates-identifiers-images/appid01.png)](certificates-identifiers-images/appid01-large.png#lightbox)

1. Choisissez **App IDs**.

    > [!div class="mx-imgBorder"]
    > [![Commencer avec les ID App](certificates-identifiers-images/appid02.png)](certificates-identifiers-images/appid02-large.png#lightbox)

1. Entrez une **Description** et sélectionnez les **App Services** dont l’application a besoin : a. La plateforme doit être **macOS** a. Choisissez une **Description** (utilisée uniquement dans ce portail) a. Entrez le **Bundle ID**, qui doit correspondre à votre fichier **Info.plist** a. Sélectionnez les fonctionnalités requises par votre application.

    > [!div class="mx-imgBorder"]
    > [![Saisie des services de description et d’application](certificates-identifiers-images/appid03.png)](certificates-identifiers-images/appid03-large.png#lightbox)

    Appuyez sur **Continue** pour passer en revue vos sélections.

1. Si les informations sont correctes, cliquez sur **Register** (Inscrire) pour terminer la configuration :

    > [!div class="mx-imgBorder"]
    > [![Examen des données saisies](certificates-identifiers-images/appid04.png)](certificates-identifiers-images/appid04-large.png#lightbox)

1. Vérifiez les informations, puis cliquez sur le bouton **Submit** :

    > [!div class="mx-imgBorder"]
    > ![Vérification des informations](certificates-identifiers-images/appid05.png)

Certains **services d’application** peuvent nécessiter une configuration supplémentaire (par exemple, iCloud). Si tel est le cas, sélectionnez le nouvel ID d’application que vous venez de créer, puis cliquez sur le bouton **Modify** :

> [!div class="mx-imgBorder"]
> [![Modification du nouvel ID App](certificates-identifiers-images/appid06.png)](certificates-identifiers-images/appid06-large.png#lightbox)

Pour configurer les services iCloud, par exemple, cliquez sur le bouton **Edit** (Modifier) :

> [!div class="mx-imgBorder"]
> [![Configurer les services iCloud](certificates-identifiers-images/appid07.png)](certificates-identifiers-images/appid07-large.png#lightbox)

## <a name="register-macos-devices"></a>Inscrire des appareils macOS

Pour créer un profil de provisionnement à des fins de test, les ordinateurs Mac des développeurs doivent être inscrits. Vous pouvez inscrire un maximum de 100 ordinateurs pour le test.

1. Dans le Mac Developer Center, sélectionnez **All** dans la section **Devices**, puis cliquez sur le bouton **+**  :

    > [!div class="mx-imgBorder"]
    > [![Ajout d’un nouvel ordinateur](certificates-identifiers-images/device01.png)](certificates-identifiers-images/device01-large.png#lightbox)

1. Entrez un **nom** et l’**UUID** de l’ordinateur à ajouter, puis cliquez sur le bouton **Continue**. Examinez les informations, puis cliquez sur le bouton **Register** :

    > [!div class="mx-imgBorder"]
    > [![Saisie des nouvelles informations informatiques](certificates-identifiers-images/device02.png)](certificates-identifiers-images/device02-large.png#lightbox)

1. Vérifiez et confirmez les données entrées :

    > [!div class="mx-imgBorder"]
    > [![Saisie des nouvelles informations informatiques](certificates-identifiers-images/device03.png)](certificates-identifiers-images/device03-large.png#lightbox)

## <a name="create-certificates"></a>Créer des certificats

La section Certificates permet de créer différents types de certificats qui seront utilisés pour signer les applications Mac :

> [!div class="mx-imgBorder"]
> [![Création d’un certificat](certificates-identifiers-images/devcenter04.png)](certificates-identifiers-images/devcenter04-large.png#lightbox)

Il existe cinq types principaux de certificats relatifs au développement macOS :

- **Mac Development** : facultatif pour le développement d’application général, mais obligatoire si le développeur prévoit d’utiliser des fonctionnalités comme iCloud ou les notifications Push. Le développeur doit disposer d’un certificat de développement avant de créer des profils de provisionnement qui l’autorisent à accéder à ces fonctionnalités.
- **Mac App Distribution** : le développeur doit disposer d’un certificat pour son application et d’un autre certificat pour le programme d’installation.
- **Mac Installer Distribution** : le développeur doit disposer d’un certificat pour son application et d’un autre certificat pour le programme d’installation.
- **Developer ID Installer** : certificats pour le programme d’installation en vue d’une distribution en dehors du Mac App Store.
- **Developer ID Application** : certificats pour l’application en vue d’une distribution en dehors du Mac App Store.

Les sections suivantes fournissent des exemples de création de certains de ces types de certificats.

### <a name="mac-development-certificate"></a>Certificat de développement Mac

Comme mentionné plus haut, le certificat Mac Development n’est nécessaire que si des fonctionnalités macOS comme iCloud ou les notifications Push sont utilisées.

Effectuez les étapes suivantes pour créer un certificat de développement :

1. Sélectionnez la case d’option **Mac Development**, puis cliquez sur **Continue** :

    > [!div class="mx-imgBorder"]
    > [![Ajout d’un certificat de développement](certificates-identifiers-images/certif02.png)](certificates-identifiers-images/certif02-large.png#lightbox)

1. Chargez une _requête de signature de certificat_. Le fichier de demande de certificat (extension `.certSigningRequest`) est enregistré localement sur le Mac. Cliquez sur **Choose file** pour sélectionner la demande de certificat, puis appuyez sur **Continue**.

    > [!div class="mx-imgBorder"]
    > [![Télécharger un fichier de demande de certificat](certificates-identifiers-images/certif03.png)](certificates-identifiers-images/certif03-large.png#lightbox)

    Pour obtenir des instructions sur l’utilisation de l’**accès au trousseau** pour créer un fichier de demande de certificat, suivez le lien [Learn more >](https://help.apple.com/developer-account/#/devbfa00fef7).

1. Appuyez sur **Download** (Télécharger) pour obtenir le fichier de certificat, puis double-cliquez dessus pour l’installer :

    > [!div class="mx-imgBorder"]
    > [![Télécharger le fichier de certificat](certificates-identifiers-images/certif04.png)](certificates-identifiers-images/certif04-large.png#lightbox)

Comme mentionné précédemment, le certificat de développeur n’est pas toujours obligatoire, sauf si le développeur implémente des fonctionnalités macOS comme iCloud et les notifications Push. Il est également obligatoire pour créer un **profil de provisionnement de développement**, qui sera nécessaire pour tester les applications Mac App Store.

### <a name="mac-app-store-certificates"></a>Certificats Mac App Store

Pour publier une application dans l’App Store, vous avez besoin de deux certificats :

- Le certificat **Mac App Distribution** qui sera utilisé pour signer l’application, et
- Le certificat **Mac Installer Distribution** pour signer le programme d’installation.

> [!TIP]
> Soyez prudent quand vous nommez les demandes de certificat pour ces clés : utilisez des noms descriptifs qui contiennent le texte `Application` et `Installer` afin qu’ils puissent être distingués ultérieurement.

Tout d’abord, créez le certificat du programme d’installation :

1. Sélectionnez **Mac Installer Distribution** comme type de certificat, puis cliquez sur le bouton **Continue** :

    > [!div class="mx-imgBorder"]
    > [![Création d’un certificat App Store](certificates-identifiers-images/certif05.png)](certificates-identifiers-images/certif05-large.png#lightbox)

1. La page suivante explique comment utiliser l’**accès au trousseau** pour générer un fichier de demande de certificat. Suivez les instructions spécifiées :

    > [!div class="mx-imgBorder"]
    > [![Télécharger une demande de certificat](certificates-identifiers-images/certif06.png)](certificates-identifiers-images/certif06-large.png#lightbox)

    Pour obtenir des instructions sur l’utilisation de l’**accès au trousseau** pour créer un fichier de demande de certificat, suivez le lien [Learn more >](https://help.apple.com/developer-account/#/devbfa00fef7). Pensez à choisir un nom de certificat qui reflète le _type_ de certificat (application ou programme d’installation).

1. Cliquez sur **Download** pour obtenir votre certificat, puis double-cliquez dessus pour l’installer dans le **trousseau** :

    > [!div class="mx-imgBorder"]
    > [![Télécharger le certificat App Store](certificates-identifiers-images/certif07.png)](certificates-identifiers-images/certif07-large.png#lightbox)

**Effectuez les mêmes étapes pour le certificat Mac App Distribution.**

![Certificat Mac App Distribution](certificates-identifiers-images/certif08.png)

### <a name="developer-id-certificates"></a>Certificats d’ID de développeur

Pour effectuer une auto-publication d’une application Xamarin.Mac (qui n’est pas publiée dans l’Apple App Store), vous avez besoin de deux certificats :

- Le certificat **Developer ID Installer** qui sera utilisé pour signer l’application, et
- Le certificat **Developer ID Application** pour signer le programme d’installation.

> [!TIP]
> Soyez prudent quand vous nommez les demandes de certificat pour ces clés : utilisez des noms descriptifs qui contiennent le texte `Application` et `Installer` afin qu’ils puissent être distingués ultérieurement.

Une fois que vous avez créé, téléchargé et installé des certificats, ils sont visibles dans **Keychain Access** :

[Liste des certificats d’accès au trousseau](certificates-identifiers-images/certif09.png)

## <a name="related-links"></a>Liens connexes

- [Installation](/visualstudio/mac/installation/)
- [Bonjour, échantillon De Mac](~/mac/get-started/hello-mac.md)
- [Distribuer vos applications sur le Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [ID de développeur et GateKeeper](https://developer.apple.com/developer-id/)
