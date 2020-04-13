---
title: Déploiement et test des applications Xamarin.iOS
description: Ce document fournit des liens vers différents guides qui décrivent les rubriques relatives au déploiement et au test d’une application Xamarin.iOS. Par exemple, distribution d’applications, fichiers .ipa, provisionnement, déploiement sans fil, TestFlight et débogage.
ms.prod: xamarin
ms.assetid: 2DBF3BF9-79E7-4E24-AF26-E34C972B0169
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: f3f5e27e97b7b62ade66ea2dc50a79ac03d51f90
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73026453"
---
# <a name="deploying-and-testing-xamarinios-apps"></a>Déploiement et test des applications Xamarin.iOS

Cette section regroupe les rubriques qui expliquent comment tester et distribuer une application. Les présentes rubriques couvrent divers sujets, notamment les outils utilisés pour le débogage, le déploiement pour les testeurs et la publication d’applications sur l’App Store.

## <a name="app-distribution"></a>[Distribution d’applications](~/ios/deploy-test/app-distribution/index.md)

Cet article explique comment configurer, générer et publier une application Xamarin.iOS en vue de sa distribution par différents moyens, notamment :

- [Distribution De l’App Store](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)
- [Distribution interne (en entreprise)](~/ios/deploy-test/app-distribution/in-house-distribution.md)
- [Distribution ad hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)

## <a name="ipa-deployment"></a>[Déploiement IPA](~/ios/deploy-test/app-distribution/ipa-support.md)

Les déploiements en entreprise et ad hoc permettent aux développeurs de créer des paquets qui peuvent ensuite être distribués aux testeurs ou aux utilisateurs internes de l’entreprise. Ce document explique comment créer une application IPA pouvant être synchronisée avec un appareil iOS à l’aide d’iTunes.

## <a name="provisioning"></a>[Approvisionnement](provisioning/index.md)

Cet ensemble de guides aborde les bases de la signature de code et du provisionnement, par exemple, l’utilisation des listes de propriétés et le provisionnement d’une application pour les services d’application. 

## <a name="wireless-deployment"></a>[Déploiement sans fil](wireless-deployment.md)

 Xcode 9 a introduit une option de déploiement sur un appareil iOS ou Apple TV via un réseau, ce qui évite d’avoir à raccorder les appareils à chaque fois que vous désirez déployer et déboguer votre application. Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire.

## <a name="testflight"></a>[TestFlight](~/ios/deploy-test/testflight.md)

TestFlight est un outil racheté par Apple. Il constitue le principal moyen de tester les versions bêta de vos applications Xamarin.iOS. Cet article vous guide à travers toutes les étapes du processus TestFlight, depuis le chargement de votre application jusqu’à l’utilisation d’iTunes Connect.

## <a name="debugging-in-xamarinios"></a>[Débogage dans Xamarin.iOS](~/ios/deploy-test/debugging-in-xamarin-ios.md)

Les IDE Visual Studio et Visual Studio pour Mac incluent la prise en charge du débogage des applications Xamarin.iOS à la fois dans le simulateur iOS et sur les appareils iOS. Cet article explique comment utiliser le débogueur et configurer différentes options qu’il prend en charge.

## <a name="touchunit"></a>[Touch.Unit](~/ios/deploy-test/touch.unit.md)

Ce document décrit comment créer des tests unitaires pour vos projets Xamarin.iOS.
Les tests unitaires avec Xamarin.iOS s’effectuent à l’aide du framework Touch.Unit. Ce framework contient un outil Test Runner iOS, ainsi qu’une version modifiée du framework [NUnitLite](http://www.nunitlite.com/) qui fournit un ensemble d’API couramment utilisées pour l’écriture de tests unitaires.

## <a name="using-instruments-to-detect-native-leaks-using-markheap"></a>[Utilisation d’Instruments pour détecter les fuites de mémoire native à l’aide de MarkHeap](~/ios/deploy-test/using-instruments-to-detect-native-leaks-using-markheap.md)

Cet article décrit comment utiliser l’outil Instruments sur les appareils iOS et dans les applications Xamarin.iOS. Il explique également comment profiler les applications dans le simulateur.

## <a name="walkthrough---using-apples-instrument-tool"></a>[Procédure pas à pas : Utilisation de l’outil Instruments d’Apple](~/ios/deploy-test/walkthrough-apples-instrument.md)

Cet article explique pas à pas comment utiliser l’outil Instruments d’Apple pour diagnostiquer les problèmes de mémoire dans une application iOS conçue avec Xamarin. Il montre comment lancer Instruments, prendre des instantanés du tas et analyser la croissance de la mémoire. Il explique également comment utiliser Instruments pour afficher et identifier les lignes de code précises qui sont à l’origine du problème de mémoire.

## <a name="linking-on-ios"></a>[Liaison sur iOS](linker.md)

Explique comment fonctionne l’éditeur de liens pour produire le package d’application le plus petit possible, et comment modifier ses paramètres et son utilisation.

## <a name="xamarinios-performance"></a>[Performances des applications Xamarin.iOS](performance.md)

Il existe plusieurs techniques permettant d’accroître le niveau de performance des applications générées avec Xamarin.iOS. Utilisées conjointement, ces techniques peuvent considérablement réduire la charge de travail d’une UC, ainsi que la quantité de mémoire consommée par une application.

## <a name="mtouch"></a>[mtouch](mtouch.md)

Notes et informations sur mtouch.exe, l’outil de ligne de commande qui génère une application utilisable par iOS à partir d’un projet.

## <a name="ios-build-mechanics"></a>[Mécanique de construction iOS](ios-build-mechanics.md)

Ce guide explique comment planifier des applications et comment utiliser des méthodes permettant d’accélérer les builds pour toutes les configurations de build.
