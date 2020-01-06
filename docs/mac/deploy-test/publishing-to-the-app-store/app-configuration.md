---
title: Configuration d’application Mac
description: Ce document décrit comment configurer une application Xamarin.Mac en vue de sa publication. Il traite des paramètres d’application, de signature et de build.
ms.prod: xamarin
ms.assetid: fea66a34-1581-4cd6-b714-3fbff215a542
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 04/12/2017
ms.openlocfilehash: 7db7edd4d8143f4e456f59b97de51e3c1d202dc0
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489399"
---
# <a name="mac-app-configuration"></a>Configuration d’application Mac

## <a name="mac-app-configuration"></a>Configuration d’application Mac

Cliquez avec le bouton droit sur le projet d’application Mac dans Visual Studio pour Mac, puis choisissez **Options**.

### <a name="application-settings"></a>Paramètres d'application

Pour modifier les paramètres d’une application Xamarin.Mac, double-cliquez sur le fichier **Info.plist** dans le **Panneau Solutions** :

![Sélection du fichier info. plist](app-configuration-images/config04.png "Sélection du fichier info. plist")

Cette action affiche les options disponibles pour l’application :

 [![Modification du fichier info. plist](app-configuration-images/config01.png "Modification du fichier info. plist")](app-configuration-images/config01-large.png#lightbox)

L’exécution d’applications créées Mac avec Xamarin.Mac a la configuration requise suivante :

- Un ordinateur Mac exécutant Mac OS X 10.7 ou ultérieur.

### <a name="signing-settings"></a>Paramètres de signature

La section **Signature Mac** de la boîte de dialogue **Options du projet** permet au développeur de signer une application Xamarin.Mac à des fins de tests, pour une auto-publication ou pour une publication par le biais de l’Apple App Store :

[![Éditeur de signature Mac](app-configuration-images/config02.png "Fenêtre signature Mac")](app-configuration-images/config02-large.png#lightbox)

À partir de là, sélectionnez l’identité, le profil de provisionnement et tous les droits personnalisés utilisés pour signer l’application au moment de sa compilation. Le développeur peut éventuellement signer le programme d’installation utilisé pour installer l’application sur un autre Mac.

### <a name="build-settings"></a>Paramètres de build

La section **Build Mac** de la boîte de dialogue **Options du projet** permet au développeur de sélectionner l’architecture d’une application Xamarin.Mac, de contrôler la version de macOS que l’application prendra en charge et de créer éventuellement un paquet d’installation quand l’application sera compilée avec succès :

 [![Modification des paramètres de build](app-configuration-images/config03.png "Modification des paramètres de build")](app-configuration-images/config03-large.png#lightbox)

## <a name="related-links"></a>Liens connexes

- [Installation](/visualstudio/mac/installation/)
- [Exemple Hello, Mac](~/mac/get-started/hello-mac.md)
- [Distribuer vos applications sur le Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [ID de développeur et GateKeeper](https://developer.apple.com/resources/developer-id/)
