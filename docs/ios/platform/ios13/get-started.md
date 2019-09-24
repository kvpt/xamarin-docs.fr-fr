---
title: Prise en main d’iOS 13, iPados 13, tvOS 13 et Watchos 6
description: Ce document explique comment configurer pour créer des applications iOS 13, iPados 13, tvOS 13 et Watchos 6 avec Xamarin. Il explique comment télécharger Xcode 11 et Update Visual Studio pour Mac.
ms.prod: xamarin
ms.assetid: 97414545-85D2-433C-9246-63B6763F488A
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/01/2019
ms.openlocfilehash: 1f190ef2f99e71c8cf21680f9902caccf3454450
ms.sourcegitcommit: 09bc69d7119a04684c9e804c5cb113b8b1bb7dfc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71206327"
---
# <a name="get-started-with-ios-13"></a>Prise en main d’iOS 13

Ce document explique comment créer des applications Xamarin qui appellent des API fournies avec Xcode 11, pour iOS 13. L’utilisation de la préversion nécessite macOS 10.14.4 (Mojave) ou une version plus récente.

## <a name="download-and-install"></a>Télécharger et installer

1. **Installer Xcode 11** : les développeurs Apple inscrits peuvent télécharger et installer la dernière version de Xcode 11 à partir du [portail des développeurs Apple](https://developer.apple.com/download/) ou de l' **App Store**.

2. **Exécutez Xcode 11** : exécutez Xcode 11 avant de mettre à jour et d’exécuter Visual Studio pour Mac, car il installe certains outils requis par Xamarin.

3. Dans Visual Studio pour Mac, sélectionnez **Visual Studio > Rechercher les mises à jour...** , sélectionnez le canal **stable** , puis installez les mises à jour disponibles.

4. Facultatif **Installer iOS 13 sur vos appareils iOS** : pour tester des appareils d’applications qui utilisent des API introduites avec Xcode 11, les développeurs Apple inscrits peuvent [Télécharger](https://developer.apple.com/download) et installer le système d’exploitation sur leurs appareils. 

   > [!TIP]
   > Même si votre application n’utilise pas de nouvelles API, veillez à la créer avec les derniers SDK Xcode 11 et testez-la pour vous assurer que tout fonctionne comme prévu. Si une application n’appelle pas de nouvelles API, vous pouvez la recompiler avec ces nouveaux kits de développement logiciel (SDK) et la tester sur des appareils qui n’ont pas encore été mis à niveau vers le nouveau système d’exploitation.
   >
   > Avant de mettre à niveau vos appareils vers les versions les plus récentes du système d’exploitation à partir d’Apple pour tester vos applications Xamarin, veillez à :
   >
   > - Lisez les [notes de publication d’Apple](https://developer.apple.com/download/) pour les mises à jour du système d’exploitation.

## <a name="related-links"></a>Liens connexes

- [Télécharger Xcode](https://developer.apple.com/download/)
- [Notes de publication de Xamarin.iOS](/xamarin/ios/release-notes/13/13.0)
