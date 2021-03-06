---
title: Regroupement pour le Mac App Store
description: Ce document décrit comment créer un bundle d’une application Xamarin.Mac en vue de sa publication dans le Mac App Store. Il traite de la génération et des options de signature de code.
ms.prod: xamarin
ms.assetid: 00a36d7c-937d-4657-bf6a-0de9684b8f94
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: f4d38bb66a34257c1e0a27c5fbbfe16f59743e83
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "76725504"
---
# <a name="bundling-for-the-mac-app-store"></a>Regroupement pour le Mac App Store

Cette section décrit les concepts de base de la génération d’une application pour une mise en production dans le Mac App Store à l’aide de Visual Studio pour Mac. Suivant les fonctionnalités supplémentaires (telles que les accès iCloud et les notifications Push), des étapes de configuration supplémentaires qui n’entrent pas dans le cadre de cet article peuvent s’avérer nécessaires.

> [!NOTE]
> Avant de commencer cette section, le développeur doit avoir créé un profil de provisionnement de production pour effectuer des générations pour App Store Mac. Pour plus d’informations sur la création des profils de provisionnement nécessaires, consultez les [instructions sur les profils](profiles.md).

## <a name="code-signing-options"></a>Options de signature du code

Définissez la **Configuration** sur **Release** avant de mettre à jour les options de signature du code et de packaging. Le développeur doit s’assurer qu’il utilise l’**identité** de sa société et le profil de provisionnement que nous avons créé ci-dessus lors de la signature de l’application en vue de sa mise en production dans l’App Store.

[![Modifier les options de signature de code](bundling-images/sign.png)](bundling-images/sign-large.png#lightbox)

Vérifiez que l’option permettant de créer un paquet d’installation a été cochée dans les paramètres **Build Mac** :

[![Modification des options de build](bundling-images/build.png "Modification des options de build")](bundling-images/build-large.png#lightbox)

## <a name="build"></a>Build

Avant d’effectuer la génération, vérifiez que la configuration **Release** a été sélectionnée. Quand le développeur génère l’application, il est invité _deux fois_ (à utiliser les certificats de l’application et du programme d’installation) :

![Autorisation pour l’application à utiliser le certificat, apparaîtra à deux reprises.](bundling-images/perms02.png)

Une fois l’application générée, le développeur peut cliquer avec le bouton droit sur le projet, puis choisir **Afficher dans le Finder** pour rechercher le fichier de paquet (dans le répertoire `bin/Release/AppStore` dans l’exemple présenté ci-dessous).  Ce fichier de paquet inclut un programme d’installation pour l’application. Il peut être envoyé à Apple pour être ajouté dans le Mac App Store.

> [!div class="mx-imgBorder"]
> ![Sélection du paquet de build dans le Finder](bundling-images/path.png)

## <a name="related-links"></a>Liens connexes

- [Installation](/visualstudio/mac/installation/)
- [Bonjour, échantillon De Mac](~/mac/get-started/hello-mac.md)
- [Distribuer vos applications sur le Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [ID de développeur et GateKeeper](https://developer.apple.com/developer-id/)
