---
title: Publication d’applications Xamarin.Mac dans le Mac App Store
description: Ce document décrit comment déployer une application Xamarin.Mac avec Visual Studio pour Mac. Il explique comment configurer un compte de développeur Mac, comment créer les certificats pour la signature de code et comment les utiliser pour générer des applications Mac distribuables directement ou par le biais du Mac App Store.
ms.prod: xamarin
ms.assetid: D26C5E54-EAD2-5487-264D-4263AEA1EBF2
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 1a14bcc15299360a17b4c9e7a363b6d6e6f2e69a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "76725023"
---
# <a name="publishing-xamarinmac-apps-to-the-mac-app-store"></a>Publication d’applications Xamarin.Mac dans le Mac App Store

## <a name="overview"></a>Vue d’ensemble

Les applications Xamarin.Mac peuvent être distribuées de deux manières différentes :

- **ID de développeur** : les applications signées avec un ID de développeur peuvent être distribuées en dehors de l’App Store mais sont reconnues par GateKeeper, et leur installation est autorisée.
- **Mac App Store** : pour être envoyées au Mac App Store, les applications doivent avoir un paquet d’installation ; de plus, l’application et le programme d’installation doivent tous les deux être signés.

Ce document explique comment utiliser Visual Studio pour Mac et Xcode pour définir un compte de développeur Apple et configurer un projet Xamarin.Mac pour chaque type de déploiement.

## <a name="mac-developer-program"></a>Programme pour les développeurs Mac

Quand le développeur rejoint le [programme pour les développeurs Mac](https://developer.apple.com/devcenter/mac/), il a le choix entre le rejoindre comme individu ou comme société, comme indiqué dans la capture d’écran ci-dessous :

[![Le portail des développeurs Apple](images/image1.png "Le portail des développeurs Apple")](images/image1-large.png#lightbox)

Choisissez le type d’inscription adapté à votre situation.

> [!NOTE]
> Les choix effectués ici affecteront l’affichage de certains écrans lors de la configuration d’un compte de développeur. Les descriptions et les captures d’écran présentées dans ce document sont effectuées du point de vue du compte de développeur d’individu (**Individual**). Dans une société (**Company**), certaines options sont disponibles seulement pour les utilisateurs **Team Admin** (Administrateur d’équipe).

### <a name="certificates-and-identifiers"></a>[Certificats et identificateurs](~/mac/deploy-test/publishing-to-the-app-store/certificates-identifiers.md)

Ce guide présente en détail la création des certificats et des identificateurs nécessaires qui seront exigés pour publier une application Xamarin.Mac.

### <a name="create-provisioning-profile"></a>[Créer un profil de provisionnement](~/mac/deploy-test/publishing-to-the-app-store/profiles.md)

Ce guide présente en détail la création des profils de provisionnement nécessaires qui seront exigés pour publier une application Xamarin.Mac.

### <a name="mac-app-configuration"></a>[Configuration d’application Mac](~/mac/deploy-test/publishing-to-the-app-store/app-configuration.md)

Ce guide présente en détail la configuration d’une application Xamarin.Mac en vue de sa publication.

### <a name="sign-with-developer-id"></a>[Signer avec l’ID de développeur](~/mac/deploy-test/publishing-to-the-app-store/signing.md)

Ce guide présente en détail la signature d’une application Xamarin.Mac avec un ID de développeur en vue de sa publication.

### <a name="bundle-for-mac-app-store"></a>[Bundle pour le Mac App Store](~/mac/deploy-test/publishing-to-the-app-store/bundling.md)

Ce guide présente en détail la création d’un bundle d’une application Xamarin.Mac en vue de sa publication dans le Mac App Store.

### <a name="upload-to-mac-app-store"></a>[Charger dans le Mac App Store](~/mac/deploy-test/publishing-to-the-app-store/uploading.md)

Ce guide présente en détail le chargement d’une application Xamarin.Mac en vue de sa publication sur le Mac App Store.

## <a name="related-links"></a>Liens connexes

- [Installation](/visualstudio/mac/installation/)
- [Bonjour, échantillon De Mac](~/mac/get-started/hello-mac.md)
- [ID de développeur et GateKeeper](https://developer.apple.com/developer-id/)
