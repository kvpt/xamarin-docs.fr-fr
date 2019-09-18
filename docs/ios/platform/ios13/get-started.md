---
title: Prise en main d’iOS 13, iPados 13, tvOS 13 et Watchos 6
description: Ce document explique comment configurer pour créer des applications iOS 13, iPados 13, tvOS 13 et Watchos 6 avec Xamarin. Il explique comment télécharger Xcode 11 et Update Visual Studio pour Mac.
ms.prod: xamarin
ms.assetid: 97414545-85D2-433C-9246-63B6763F488A
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/01/2019
ms.openlocfilehash: 9dc6f234c4bc14c3644d953eef0d2e0f397436e5
ms.sourcegitcommit: 61a35d0643eb3bf5adb8f8831da54771d8dde626
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033078"
---
# <a name="get-started-with-ios-13"></a>Prise en main d’iOS 13

![Fonctionnalité d’évaluation](~/media/shared/preview.png)

Ce document explique comment créer des applications Xamarin qui appellent des API fournies avec Xcode 11, pour iOS 13. L’utilisation de la préversion nécessite macOS 10.14.4 (Mojave) ou une version plus récente.

## <a name="download-and-install"></a>Télécharger et installer

1. **Installer Xcode 11 bêta** : les développeurs Apple inscrits peuvent télécharger et installer la dernière version de Xcode 11 bêta à partir du [portail des développeurs Apple](https://developer.apple.com/download/) ou de l' **App Store**.

2. **Exécutez Xcode 11 bêta** : exécutez Xcode 11 avant de mettre à jour et d’exécuter Visual Studio pour Mac, car il installe certains outils requis par Xamarin.

3. Dans Visual Studio pour Mac, sélectionnez **Visual Studio > Rechercher les mises à jour...** , sélectionnez le canal **aperçus Xcode 11** et installez les mises à jour disponibles. Si vous ne voyez pas ce canal, vérifiez que vous êtes connecté à votre compte à partir de **Visual Studio > compte...** .

4. Dans Visual Studio pour Mac, sélectionnez **Visual Studio > préférences > projets > emplacements du kit de développement logiciel (SDK) > Apple** , puis sélectionnez **Xcode-Beta. app**.

5. Facultatif Si vous évaluez cette version préliminaire à l’aide de _Xcode 11 bêta 3_, vous devez activer la liaison. Cliquez avec le bouton droit sur votre projet, accédez à **Options > Build iOS > comportement** de l’éditeur de liens et définissez la valeur de comportement de l’éditeur de liens sur lier les kits de développement logiciel ( **SDK) Framework uniquement**. Cette solution de contournement n’est pas nécessaire dans une prochaine version préliminaire.

6. Facultatif **Installer iOS 13 sur vos appareils iOS** : pour tester des appareils d’applications qui utilisent des API introduites avec Xcode 11, les développeurs Apple inscrits peuvent [Télécharger](https://developer.apple.com/download) et installer le système d’exploitation sur leurs appareils. Comme iOS est en version bêta, veillez à l’installer sur votre appareil principal.

   > [!TIP]
   > Même si votre application n’utilise pas de nouvelles API, veillez à la créer avec les derniers SDK Xcode 11 et testez-la pour vous assurer que tout fonctionne comme prévu. Si une application n’appelle pas de nouvelles API, vous pouvez la recompiler avec ces nouveaux kits de développement logiciel (SDK) et la tester sur des appareils qui n’ont pas encore été mis à niveau vers le nouveau système d’exploitation.
   >
   > Avant de mettre à niveau vos appareils vers les versions les plus récentes du système d’exploitation à partir d’Apple pour tester vos applications Xamarin, veillez à :
   >
   > - Lisez les [notes de publication d’Apple](https://developer.apple.com/download/) pour les mises à jour du système d’exploitation.

## <a name="related-links"></a>Liens connexes

- [Télécharger Xcode](https://developer.apple.com/download/)
- [Notes de publication de Xamarin. iOS preview](/xamarin/ios/release-notes/12/12.99)
