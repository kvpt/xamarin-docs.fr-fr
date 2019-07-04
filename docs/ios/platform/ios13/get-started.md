---
title: Bien démarrer avec iOS 13, iPadOS 13, 13 de tvOS et watchOS 6
description: Ce document décrit comment préparer le jusqu'à build 13, iPadOS 13, tvOS 13 applications iOS et watchOS 6 avec Xamarin. Il explique comment télécharger Xcode 11 et mettre à jour de Visual Studio pour Mac.
ms.prod: xamarin
ms.assetid: 97414545-85D2-433C-9246-63B6763F488A
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/01/2019
ms.openlocfilehash: 554cddf5717401f912cab38c78a6af17659a0cf7
ms.sourcegitcommit: 8ecfa339d0f3e7687977bfe4fc96448942690183
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67558683"
---
# <a name="get-started-with-ios-13"></a>Prise en main iOS 13

![Fonctionnalité d’évaluation](~/media/shared/preview.png)

Ce document décrit comment commencer à créer des applications Xamarin qui appellent les API publiées avec Xcode 11, pour iOS 13. À l’aide de la version d’évaluation nécessite macOS 10.14.4 (Mojave) ou une version ultérieure.

## <a name="download-and-install"></a>Téléchargez et installez

1. **Installer Xcode 11 beta** – aux développeurs d’Apple inscrits peuvent télécharger et installer la dernière version de Xcode 11 beta à partir de la [portail des développeurs Apple](https://developer.apple.com/download/) ou **App Store**.

2. **Exécuter Xcode 11 beta** – nécessite d’exécuter Xcode 11 avant la mise à jour et en cours d’exécution Visual Studio pour Mac, comme il installe certains des outils que Xamarin.

3. Dans Visual Studio pour Mac, sélectionnez **Visual Studio > Rechercher les mises à jour...** , sélectionnez le **Xcode 11 aperçus** de canal et installer les mises à jour disponibles.

4. Dans Visual Studio pour Mac, sélectionnez **Visual Studio > Préférences > projets > emplacements SDK > Apple** et sélectionnez **Xcode-beta.app**.

5. (Facultatif) Si l’évaluation de cette version préliminaire à l’aide _Xcode 11 version bêta 3_, vous devez activer la liaison. Cliquez sur votre projet, accédez à **Options > Build iOS > comportement de l’éditeur de liens** et définissez la valeur de comportement de l’éditeur de liens sur **lien les SDK Framework uniquement**. Cette solution de contournement ne sera pas nécessaire dans une prochaine version d’évaluation.

6. (Facultatif) **Installer iOS 13 sur vos appareils iOS** : appareil de test d’applications qui utilisent les API introduites avec 11 Xcode, Apple inscrit, les développeurs peuvent [télécharger](https://developer.apple.com/download) et installer le système d’exploitation sur leurs appareils. IOS étant en version bêta, veillez à installer sur votre appareil principal.

   > [!TIP]
   > Même si votre application n’utilise pas les nouvelles API, veillez à le générer avec les kits de développement logiciel plus récent Xcode 11 et testez-le pour vous assurer que tout fonctionne comme prévu. Si une application n’appelle pas les nouvelles API, vous pouvez le recompiler avec ces nouveaux kits de développement logiciel et le tester sur les appareils qui n’ont pas encore été mis à niveau vers le nouveau système d’exploitation.
   >
   > Avant la mise à niveau de vos appareils vers le dernier système d’exploitation à partir d’Apple pour tester vos applications Xamarin, veillez à :
   >
   > - Lecture [notes de publication d’Apple](https://developer.apple.com/download/) pour les mises à jour du système d’exploitation.

## <a name="related-links"></a>Liens connexes

- [Télécharger Xcode](https://developer.apple.com/download/)
- [Notes de publication de Xamarin.iOS version préliminaire](/xamarin/ios/release-notes/12/12.99)
