---
title: Signature d’applications Xamarin.Mac avec un ID de développeur
description: Ce document décrit comment signer une application Xamarin.Mac avec un ID de développeur, afin qu’elle puisse être distribuée en dehors du Mac App Store. Il traite de la génération et des options de signature de code.
ms.prod: xamarin
ms.assetid: cf7b733b-e08f-4f56-a233-264b29ee4c97
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: f05e763df58e6fafc4d4ed1d4007979859670c28
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725460"
---
# <a name="signing-xamarinmac-apps-with-a-developer-id"></a>Signature d’applications Xamarin.Mac avec un ID de développeur

Si le développeur prévoit de distribuer une application directement aux utilisateurs macOS, Apple lui recommande d’en signer le code avec son ID de développeur afin que celle-ci puisse être installée sur les systèmes macOS où **GateKeeper** est activé. Si l’application n’a pas été signée, **GateKeeper** empêche les utilisateurs d’effectuer l’installation au moyen d’un message d’alerte (ils peuvent ignorer cette restriction en maintenant la touche CTRL enfoncée pendant le lancement).

Pour en savoir plus sur l’[ID de développeur et GateKeeper](https://developer.apple.com/developer-id/) et la [distribution en dehors du Mac App Store](https://developer.apple.com/library/content/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html), consultez le site web d’Apple.

## <a name="code-signing-options"></a>Options de signature du code

Pour générer une application en vue de la déployer directement pour les utilisateurs (et non par le biais du Mac App Store), définissez les **paramètres de signature** pour utiliser l’**ID de développeur**. Veillez à modifier la configuration **Release**.

 [![](signing-images/config02.png "The Mac Signing options")](signing-images/config02.png#lightbox)

## <a name="build"></a>Générer

Avant d’effectuer la génération, veillez à sélectionner la configuration appropriée et à sélectionner l’option permettant de créer un paquet d’installation dans les paramètres **Build Mac** :

[![](signing-images/config03.png "The build options")](signing-images/config03.png#lightbox)

Lors de la génération de l’application, le développeur est invité à utiliser les deux certificats :

 [![](signing-images/image57.png "Allowing keychain access")](signing-images/image57.png#lightbox)

 [![](signing-images/image58.png "Allowing keychain access")](signing-images/image58.png#lightbox)

Une fois l’application générée, le développeur peut cliquer avec le bouton droit sur le projet, puis choisir **Ouvrir le dossier conteneur** pour rechercher le fichier de paquet (dans le répertoire `bin/Release`). Ce fichier de paquet inclut un programme d’installation pour l’application qui permet de distribuer celle-ci à n’importe quel utilisateur macOS pour qu’il l’installe.

 [![](signing-images/image59.png "Selecting the app package in Finder")](signing-images/image59.png#lightbox)

## <a name="related-links"></a>Liens connexes

- [Installation](~//mac/get-started/installation.md)
- [Exemple Hello, Mac](~//mac/get-started/hello-mac.md)
- [Distribuer vos applications sur le Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [Guide sur les outils : Signature du code de votre application](https://developer.apple.com/library/mac/#documentation/ToolsLanguages/Conceptual/OSXWorkflowGuide/CodeSigning/CodeSigning.html)
- [ID de développeur et GateKeeper](https://developer.apple.com/developer-id/)
