---
title: Configuration d’application Mac
description: Ce document décrit comment configurer une application Xamarin.Mac en vue de sa publication. Il traite des paramètres d’application, de signature et de build.
ms.prod: xamarin
ms.assetid: fea66a34-1581-4cd6-b714-3fbff215a542
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 04/12/2017
ms.openlocfilehash: f008ac42bfffeda2a47ca30aa2991d91f990732f
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "76725036"
---
# <a name="mac-app-configuration"></a>Configuration d’application Mac

## <a name="mac-app-configuration"></a>Configuration d’application Mac

Cliquez avec le bouton droit sur le projet d’application Mac dans Visual Studio pour Mac, puis choisissez **Options**.

### <a name="application-settings"></a>Paramètres de l’application

Pour modifier les paramètres d’une application Xamarin.Mac, double-cliquez sur le fichier **Info.plist** dans le **Panneau Solutions** :

![Sélection du fichier Info.plist](app-configuration-images/config04.png "Sélection du fichier Info.plist")

Cette action affiche les options disponibles pour l’application :

 [![Modifier le fichier Info.plist](app-configuration-images/config01.png "Modification du fichier Info.plist")](app-configuration-images/config01-large.png#lightbox)

L’exécution d’applications créées Mac avec Xamarin.Mac a la configuration requise suivante :

- Un ordinateur Mac exécutant Mac OS X 10.7 ou ultérieur.

### <a name="signing-settings"></a>Paramètres de signature

La section **Mac Signing** du dialogue **Project Options** permet au développeur de signer une application Xamarin.Mac pour les tests, l’auto-libération ou la sortie via l’App Store d’Apple :

[![L’éditeur Signature Mac](app-configuration-images/config02.png "La fenêtre Signature Mac")](app-configuration-images/config02-large.png#lightbox)

À partir de là, sélectionnez l’identité, le profil de provisionnement et tous les droits personnalisés utilisés pour signer l’application au moment de sa compilation. Le développeur peut éventuellement signer le programme d’installation utilisé pour installer l’application sur un autre Mac.

### <a name="build-settings"></a>Paramètres de build

La section **Mac Build** du dialogue **Project Options** permet au développeur de sélectionner l’architecture d’une application Xamarin.Mac, de contrôler quelle version de macOS l’application prendra en charge et de créer un package d’installation lorsque l’application sera compilée avec succès :

 [![Modification des paramètres de build](app-configuration-images/config03.png "Modification des paramètres de build")](app-configuration-images/config03-large.png#lightbox)

## <a name="related-links"></a>Liens connexes

- [Installation](/visualstudio/mac/installation/)
- [Bonjour, échantillon De Mac](~/mac/get-started/hello-mac.md)
- [Distribuer vos applications sur le Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [ID de développeur et GateKeeper](https://developer.apple.com/developer-id/)
