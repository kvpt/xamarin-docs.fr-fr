---
title: Publication d’applications Xamarin.Mac dans le Mac App Store
description: Ce document décrit comment déployer une application Xamarin.Mac avec Visual Studio pour Mac. Il explique comment configurer un compte de développeur Mac, comment créer les certificats pour la signature de code et comment les utiliser pour générer des applications Mac distribuables directement ou par le biais du Mac App Store.
ms.prod: xamarin
ms.assetid: D26C5E54-EAD2-5487-264D-4263AEA1EBF2
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/14/2017
ms.openlocfilehash: 84a35389276fed031f6bea116c1e4f37a63469a7
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769867"
---
# <a name="publishing-xamarinmac-apps-to-the-mac-app-store"></a>Publication d’applications Xamarin.Mac dans le Mac App Store

## <a name="overview"></a>Présentation

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

### <a name="certificates-and-identifiersmacdeploy-testpublishing-to-the-app-storecertificates-identifiersmd"></a>[Certificats et identificateurs](~/mac/deploy-test/publishing-to-the-app-store/certificates-identifiers.md)

Ce guide présente en détail la création des certificats et des identificateurs nécessaires qui seront exigés pour publier une application Xamarin.Mac.

### <a name="create-provisioning-profilemacdeploy-testpublishing-to-the-app-storeprofilesmd"></a>[Créer un profil de provisionnement](~/mac/deploy-test/publishing-to-the-app-store/profiles.md)

Ce guide présente en détail la création des profils de provisionnement nécessaires qui seront exigés pour publier une application Xamarin.Mac.

### <a name="mac-app-configurationmacdeploy-testpublishing-to-the-app-storeapp-configurationmd"></a>[Configuration d’application Mac](~/mac/deploy-test/publishing-to-the-app-store/app-configuration.md)

Ce guide présente en détail la configuration d’une application Xamarin.Mac en vue de sa publication.

### <a name="sign-with-developer-idmacdeploy-testpublishing-to-the-app-storesigningmd"></a>[Signer avec l’ID de développeur](~/mac/deploy-test/publishing-to-the-app-store/signing.md)

Ce guide présente en détail la signature d’une application Xamarin.Mac avec un ID de développeur en vue de sa publication.

### <a name="bundle-for-mac-app-storemacdeploy-testpublishing-to-the-app-storebundlingmd"></a>[Bundle pour le Mac App Store](~/mac/deploy-test/publishing-to-the-app-store/bundling.md)

Ce guide présente en détail la création d’un bundle d’une application Xamarin.Mac en vue de sa publication dans le Mac App Store.

### <a name="upload-to-mac-app-storemacdeploy-testpublishing-to-the-app-storeuploadingmd"></a>[Charger dans le Mac App Store](~/mac/deploy-test/publishing-to-the-app-store/uploading.md)

Ce guide présente en détail le chargement d’une application Xamarin.Mac en vue de sa publication sur le Mac App Store.

## <a name="related-links"></a>Liens associés

- [Installation](/visualstudio/mac/installation/)
- [Exemple Hello, Mac](~/mac/get-started/hello-mac.md)
- [ID de développeur et GateKeeper](https://developer.apple.com/resources/developer-id/)
