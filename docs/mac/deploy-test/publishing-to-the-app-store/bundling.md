---
title: Regroupement pour le Mac App Store
description: Ce document décrit comment créer un bundle d’une application Xamarin.Mac en vue de sa publication dans le Mac App Store. Il traite de la génération et des options de signature de code.
ms.prod: xamarin
ms.assetid: 00a36d7c-937d-4657-bf6a-0de9684b8f94
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/14/2017
ms.openlocfilehash: 283a057723ae399ead65c0c3e40347dbbd1e189e
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70281180"
---
# <a name="bundling-for-the-mac-app-store"></a>Regroupement pour le Mac App Store

Cette section décrit les concepts de base de la génération d’une application pour une mise en production dans le Mac App Store à l’aide de Visual Studio pour Mac. Suivant les fonctionnalités supplémentaires (telles que les accès iCloud et les notifications Push), des étapes de configuration supplémentaires qui n’entrent pas dans le cadre de cet article peuvent s’avérer nécessaires.

> [!NOTE]
> Avant de commencer cette section, le développeur doit avoir créé un profil de provisionnement de production pour effectuer des générations pour App Store Mac. Consultez les instructions mentionnées précédemment dans ce document sur la création des profils de provisionnement nécessaires.

## <a name="code-signing-options"></a>Options de signature du code

Définissez la **Configuration** sur **Release** avant de mettre à jour les options de signature du code et de packaging. Le développeur doit s’assurer qu’il utilise l’**identité** de sa société et le profil de provisionnement que nous avons créé ci-dessus lors de la signature de l’application en vue de sa mise en production dans l’App Store.

 [![Modification des options de signature du code](bundling-images/config02.png "Modification des options de signature du code")](bundling-images/config02-large.png#lightbox)

Vérifiez que l’option permettant de créer un paquet d’installation a été cochée dans les paramètres **Build Mac** :

[![Modification des options de build](bundling-images/config03.png "Modification des options de build")](bundling-images/config03-large.png#lightbox)

## <a name="build"></a>Build

Avant d’effectuer la génération, vérifiez que la configuration **Release** a été sélectionnée. Quand le développeur génère l’application, il est invité à utiliser les deux certificats :

 ![Autorisation de l’application à utiliser le certificat](bundling-images/image62.png "Autorisation de l’application à utiliser le certificat")

 ![Autorisation de l’application à utiliser le certificat](bundling-images/image63.png "Autorisation de l’application à utiliser le certificat")

Une fois l’application générée, le développeur peut cliquer avec le bouton droit sur le projet, puis choisir **Ouvrir le dossier conteneur** pour rechercher le fichier de paquet (dans le répertoire `bin/x86/AppStore` dans l’exemple présenté ci-dessous).  Ce fichier de paquet inclut un programme d’installation pour l’application. Il peut être envoyé à Apple pour être ajouté dans le Mac App Store.

 ![Sélection du package de build dans le Finder](bundling-images/image64.png "Sélection du package de build dans le Finder")


## <a name="related-links"></a>Liens associés

- [Installation](/visualstudio/mac/installation/)
- [Exemple Hello, Mac](~/mac/get-started/hello-mac.md)
- [Distribuer vos applications sur le Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [ID de développeur et GateKeeper](https://developer.apple.com/resources/developer-id/)
