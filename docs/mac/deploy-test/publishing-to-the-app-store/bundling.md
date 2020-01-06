---
title: Regroupement pour le Mac App Store
description: Ce document décrit comment créer un bundle d’une application Xamarin.Mac en vue de sa publication dans le Mac App Store. Il traite de la génération et des options de signature de code.
ms.prod: xamarin
ms.assetid: 00a36d7c-937d-4657-bf6a-0de9684b8f94
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 7d2cd650dd22cd64d506d5d17a5ae0c34b44fc2b
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75487192"
---
# <a name="bundling-for-the-mac-app-store"></a>Regroupement pour le Mac App Store

Cette section décrit les concepts de base de la génération d’une application pour une mise en production dans le Mac App Store à l’aide de Visual Studio pour Mac. Suivant les fonctionnalités supplémentaires (telles que les accès iCloud et les notifications Push), des étapes de configuration supplémentaires qui n’entrent pas dans le cadre de cet article peuvent s’avérer nécessaires.

> [!NOTE]
> Avant de commencer cette section, le développeur doit avoir créé un profil de provisionnement de production pour effectuer des générations pour App Store Mac. Consultez les [instructions de profil](profiles.md) pour créer les profils d’approvisionnement requis.

## <a name="code-signing-options"></a>Options de signature du code

Définissez la **Configuration** sur **Release** avant de mettre à jour les options de signature du code et de packaging. Le développeur doit s’assurer qu’il utilise l’**identité** de sa société et le profil de provisionnement que nous avons créé ci-dessus lors de la signature de l’application en vue de sa mise en production dans l’App Store.

[![modification des options de signature du code](bundling-images/sign.png)](bundling-images/sign-large.png#lightbox)

Vérifiez que l’option permettant de créer un paquet d’installation a été cochée dans les paramètres **Build Mac** :

[![Modification des options de build](bundling-images/build.png "Modification des options de build")](bundling-images/build-large.png#lightbox)

## <a name="build"></a>Générer

Avant d’effectuer la génération, vérifiez que la configuration **Release** a été sélectionnée. Lorsque le développeur crée l’application, il est invité à _deux reprises_ (pour utiliser les certificats de l’application et du programme d’installation) :

![Autoriser l’application à utiliser le certificat apparaît deux fois](bundling-images/perms02.png)

Une fois l’application générée, le développeur peut cliquer avec le bouton droit sur le projet et choisir **afficher dans le Finder** pour trouver le fichier de package (dans le répertoire `bin/Release/AppStore` dans l’exemple ci-dessous).  Ce fichier de paquet inclut un programme d’installation pour l’application. Il peut être envoyé à Apple pour être ajouté dans le Mac App Store.

> [!div class="mx-imgBorder"]
> ![la sélection du package de build dans le Finder](bundling-images/path.png)

## <a name="related-links"></a>Liens connexes

- [Installation](/visualstudio/mac/installation/)
- [Exemple Hello, Mac](~/mac/get-started/hello-mac.md)
- [Distribuer vos applications sur le Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [ID de développeur et GateKeeper](https://developer.apple.com/resources/developer-id/)
