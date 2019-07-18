---
title: Déploiement et test des applications Xamarin.iOS
description: Ce document fournit des liens vers différents guides qui décrivent les rubriques relatives au déploiement et au test d’une application Xamarin.iOS. Par exemple, distribution d’applications, fichiers .ipa, provisionnement, déploiement sans fil, TestFlight et débogage.
ms.prod: xamarin
ms.assetid: 2DBF3BF9-79E7-4E24-AF26-E34C972B0169
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 29a1134ebe25f0ce1f25f2c41bf28d4c60f8fa6a
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865794"
---
# <a name="deploying-and-testing-xamarinios-apps"></a>Déploiement et test des applications Xamarin.iOS

Cette section regroupe les rubriques qui expliquent comment tester et distribuer une application. Les présentes rubriques couvrent divers sujets, notamment les outils utilisés pour le débogage, le déploiement pour les testeurs et la publication d’applications sur l’App Store.

## <a name="app-distributioniosdeploy-testapp-distributionindexmd"></a>[Distribution d’une application](~/ios/deploy-test/app-distribution/index.md)

Cet article explique comment configurer, générer et publier une application Xamarin.iOS en vue de sa distribution par différents moyens, notamment :

- [Distribution sur l’App Store](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)
- [Distribution interne (en entreprise)](~/ios/deploy-test/app-distribution/in-house-distribution.md)
- [Distribution ad hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)

## <a name="ipa-deploymentiosdeploy-testapp-distributionipa-supportmd"></a>[Déploiement IPA](~/ios/deploy-test/app-distribution/ipa-support.md)

Les déploiements en entreprise et ad hoc permettent aux développeurs de créer des paquets qui peuvent ensuite être distribués aux testeurs ou aux utilisateurs internes de l’entreprise. Ce document explique comment créer une application IPA pouvant être synchronisée avec un appareil iOS à l’aide d’iTunes.

## <a name="provisioningprovisioningindexmd"></a>[Provisionnement](provisioning/index.md)

Cet ensemble de guides aborde les bases de la signature de code et du provisionnement, par exemple, l’utilisation des listes de propriétés et le provisionnement d’une application pour les services d’application. 

## <a name="wireless-deploymentwireless-deploymentmd"></a>[Déploiement sans fil](wireless-deployment.md)

 Xcode 9 a introduit une option de déploiement sur un appareil iOS ou sur Apple TV via un réseau, ce qui évite d’avoir à raccorder les appareils pour pouvoir déployer et déboguer une application. Cette fonctionnalité est actuellement en préversion.

## <a name="testflightiosdeploy-testtestflightmd"></a>[TestFlight](~/ios/deploy-test/testflight.md)

TestFlight est un outil racheté par Apple. Il constitue le principal moyen de tester les versions bêta de vos applications Xamarin.iOS. Cet article vous guide à travers toutes les étapes du processus TestFlight, depuis le chargement de votre application jusqu’à l’utilisation d’iTunes Connect.

## <a name="debugging-in-xamariniosiosdeploy-testdebugging-in-xamarin-iosmd"></a>[Débogage dans Xamarin.iOS](~/ios/deploy-test/debugging-in-xamarin-ios.md)

Les IDE Visual Studio et Visual Studio pour Mac incluent la prise en charge du débogage des applications Xamarin.iOS à la fois dans le simulateur iOS et sur les appareils iOS. Cet article explique comment utiliser le débogueur et configurer différentes options qu’il prend en charge.

## <a name="touchunitiosdeploy-testtouchunitmd"></a>[Touch.Unit](~/ios/deploy-test/touch.unit.md)

Ce document décrit comment créer des tests unitaires pour vos projets Xamarin.iOS.
Les tests unitaires avec Xamarin.iOS s’effectuent à l’aide du framework Touch.Unit. Ce framework contient un outil Test Runner iOS, ainsi qu’une version modifiée du framework [NUnitLite](http://www.nunitlite.com/) qui fournit un ensemble d’API couramment utilisées pour l’écriture de tests unitaires.

## <a name="using-instruments-to-detect-native-leaks-using-markheapiosdeploy-testusing-instruments-to-detect-native-leaks-using-markheapmd"></a>[Utilisation d’Instruments pour détecter les fuites de mémoire native à l’aide de MarkHeap](~/ios/deploy-test/using-instruments-to-detect-native-leaks-using-markheap.md)

Cet article décrit comment utiliser l’outil Instruments sur les appareils iOS et dans les applications Xamarin.iOS. Il explique également comment profiler les applications dans le simulateur.

## <a name="walkthrough---using-apples-instrument-tooliosdeploy-testwalkthrough-apples-instrumentmd"></a>[Procédure pas à pas : Utilisation de l’outil Instruments d’Apple](~/ios/deploy-test/walkthrough-apples-instrument.md)

Cet article explique pas à pas comment utiliser l’outil Instruments d’Apple pour diagnostiquer les problèmes de mémoire dans une application iOS conçue avec Xamarin. Il montre comment lancer Instruments, prendre des instantanés du tas et analyser la croissance de la mémoire. Il explique également comment utiliser Instruments pour afficher et identifier les lignes de code précises qui sont à l’origine du problème de mémoire.

## <a name="linking-on-ioslinkermd"></a>[Liaison sur iOS](linker.md)

Explique comment fonctionne l’éditeur de liens pour produire le package d’application le plus petit possible, et comment modifier ses paramètres et son utilisation.

## <a name="xamarinios-performanceperformancemd"></a>[Performances des applications Xamarin.iOS](performance.md)

Il existe plusieurs techniques permettant d’accroître les performances des applications conçues avec Xamarin.iOS. Ensemble, ces techniques peuvent considérablement réduire la charge de travail d’un processeur, de même que la quantité de mémoire consommée par une application.

## <a name="mtouchmtouchmd"></a>[mtouch](mtouch.md)

Notes et informations sur mtouch.exe, l’outil de ligne de commande qui génère une application utilisable par iOS à partir d’un projet.

## <a name="ios-build-mechanicsios-build-mechanicsmd"></a>[Mécanismes de génération d’iOS](ios-build-mechanics.md)

Ce guide explique comment planifier des applications et comment utiliser des méthodes permettant d’accélérer les builds pour toutes les configurations de build.
