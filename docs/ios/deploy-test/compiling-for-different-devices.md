---
title: Compilation pour différents appareils dans Xamarin.iOS
description: Ce document décrit différentes options de configuration de build que vous pouvez utiliser afin de personnaliser une build Xamarin.iOS pour différents appareils.
ms.prod: xamarin
ms.assetid: 3B259248-887E-3E4F-E09C-7AD28C2A8CEE
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: aac558f52423132bef53f1754307e743c69f6f22
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121203"
---
# <a name="compiling-for-different-devices-in-xamarinios"></a>Compilation pour différents appareils dans Xamarin.iOS

Les propriétés build de votre fichier exécutable peuvent être configurées à partir de la page de propriétés **Build iOS** du projet, qui se trouve en cliquant avec le bouton de droite sur le nom du projet et en navigant jusqu’à **Options > Build iOS** dans Visual Studio pour Mac, et **Propriétés** dans Visual Studio :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)


[![](compiling-for-different-devices-images/image1.png "Page de propriétés de génération iOS des projets")](compiling-for-different-devices-images/image1.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](compiling-for-different-devices-images/image1a.png "Page de propriétés de génération iOS des projets")](compiling-for-different-devices-images/image1a.png#lightbox)

-----

Outre les options de configuration disponibles sur l’interface utilisateur, vous pouvez également transmettre votre propre ensemble d’options de lignes de commande à [l’outil Build Xamarin.iOS (mtouch)](~/ios/deploy-test/mtouch.md).

[http://iossupportmatrix.com/](http://iossupportmatrix.com/) est une ressource utile dont vous pouvez vous servir pour veiller à inclure tous les appareils, architectures et versions iOS requis.

 <a name="SDK_Options" />


## <a name="sdk-options"></a>Options du Kit de développement logiciel

Visual Studio pour Mac vous permet de configurer deux propriétés importantes liées au Kit de développement logiciel : la version du Kit de développement logiciel iOS utilisée pour générer votre logiciel et la Cible de déploiement (ou la version iOS minimale requise).

L’option **Version du Kit de développement logiciel** iOS permet d’utiliser différentes versions d’un Kit de développement logiciel Apple, cela dirige Xamarin.iOS vers les compilateurs, les éditeurs de liens et les bibliothèques, auxquels il doit faire référence au cours de votre build. 

Le paramètre **Cible de déploiement** permet de sélectionner la version minimale requise du système d’exploitation sur lequel votre application s’exécutera. Cela est défini dans le fichier Info.plist de votre projet. Vous devez sélectionner la version minimale qui a toutes les API dont vous avez besoin pour exécuter votre application.

En général, l’API Xamarin.iOS expose toutes les méthodes disponibles dans la version la plus récente du Kit de développement logiciel, et lorsque cela est nécessaire, nous fournissons des propriétés pratiques qui vous permettent de détecter si la fonctionnalité est disponible lors du runtime (par exemple,`UIDevice.UserInterfaceIdiom` et `UIDevice.IsMultitaskingSupported` fonctionnent toujours sur Xamarin.iOS, tout est exécuté dans les coulisses).

 <a name="Linking" />


## <a name="linking"></a>Liaison

Consultez notre page dédiée [Éditeur de liens](~/ios/deploy-test/linker.md) pour en savoir plus sur la façon dont l’éditeur de liens vous permet de réduire la taille de vos fichiers exécutables et pour savoir comment l’utiliser efficacement.

 <a name="Code_Generation_Engine" />


## <a name="code-generation-engine"></a>Moteur de génération de code

À partir de Xamarin.iOS 4.0, il existe deux serveurs principaux de génération de code sur Xamarin.iOS. Le moteur de génération de code Mono régulier et basé sur le compilateur d’optimisation LLVM. Chaque moteur a ses avantages et ses inconvénients.

En règle générale, au cours du processus de développement, vous utiliserez probablement le moteur de génération de code Mono car il vous permet une itération rapide. Pour les versions préliminaires et le déploiement de l’AppStore, vous devez basculer vers le moteur de génération de code LLVM.

Le moteur du serveur principal d’optimisation LLVM génère un code plus rapide et plus strict que le moteur Mono, pour une durée de compilation plus longue.

Vous pouvez activer ces moteurs à partir des options Build iOS dans Visual Studio pour Mac ou Visual Studio.

[![](compiling-for-different-devices-images/image2.png "Activation de LLVM")](compiling-for-different-devices-images/image2.png#lightbox)

[![](compiling-for-different-devices-images/image2a.png "Activation de LLVM")](compiling-for-different-devices-images/image2a.png#lightbox)

 <a name="ARMV7_and_ARMV7s_support" />


## <a name="architecture-support"></a>Prise en charge de l’architecture

<a name="armv6-discontinued" />

### <a name="armv6-xamarinios-discontinued-support-for-armv6-with-v810"></a>ARMv6 (prise en charge discontinue de Xamarin.iOS pour ARMv6 avec v8.10)

- iPhone (original), 3G
- iPod 1ère et 2e génération

### <a name="armv7"></a>ARMv7

- iPhone 3GS, 4, 4S
- iPad 1, 2, 3, Mini
- iPod 3e, 4e, 5e génération

### <a name="armv7s"></a>ARMv7s

- iPhone 5
- iPhone 5c
- iPad 4

Si vous ne ciblez que le processeur ARMv7s, le code généré sera légèrement plus rapide, mais il ne s’exécutera plus sur les systèmes ARMv7 ou ARMv6, sauf si vous compilez un fichier fat binaire qui contient plusieurs fichiers exécutables dans votre package.

### <a name="arm64-xamarinios-started-supporting-arm64-in-v86"></a>ARM64 (Xamarin.iOS a commencé à prendre en charge ARM64 dans v8.6)

- iPhone 5s
- iPhone SE
- iPhone 6, 6 Plus
- iPhone 6s, 6s Plus
- iPhone 7, 7 Plus
- iPhone 8, 8 Plus
- iPhone X
- iPad Air
- iPad Air 2
- iPad Mini 2, 3, 4
- iPad Pro (tous)

Notez que les builds soumis à l’App Store doivent contenir la prise en charge 64 bits, il s’agit d’une exigence définie par [Apple](https://developer.apple.com/news/?id=12172014b). En outre, iOS 11 prend uniquement en charge des applications 64 bits.

 <a name="ARM_Thumb_Support" />


### <a name="arm-thumb-2-support"></a>Prise en charge ARM Thumb-2

Thumb est un jeu d’instructions plus compact utilisé par les processeurs ARM. En activant la prise en charge Thumb, vous pouvez réduire la taille de votre fichier exécutable, pour un temps d’exécution plus lent. Thumb est pris en charge sur ARMv7 et ARMv7s.

 <a name="Conditional_framwork_useage" />


## <a name="conditional-framework-usage"></a>Utilisation de l’infrastructure conditionnelle

Si votre projet souhaite tirer parti de certaines des fonctionnalités des versions iOS les plus récentes, vous devrez peut-être vous appuyer sous certaines conditions sur certaines nouvelles infrastructures. Par exemple, si vous voulez utiliser iAd lors de l’exécution sur iOS 4.0 ou version ultérieure, mais toujours prendre en charge les appareils 3.x. Pour ce faire, vous devez informer Xamarin.iOS que vous devez le lier à l’infrastructure iAd « faiblement ». Les liaisons faibles assurent que l’infrastructure est uniquement chargée sur demande la première fois qu'une classe de l’infrastructure est requise.

Pour ce faire, vous devez suivre les étapes suivantes :

-  Ouvrez vos **Options de projet** et naviguez vers le volet **Build iOS**.
-  Ajoutez `'-gcc_flags "-weak_framework iAd"'` aux **Options supplémentaires** pour chaque configuration que vous souhaitez lier faiblement sur :


[![](compiling-for-different-devices-images/image3.png "Options supplémentaires")](compiling-for-different-devices-images/image3.png#lightbox)


En plus, vous devez empêcher votre utilisation des types de s’exécuter sur des versions antérieures d’iOS où elles ne peuvent pas exister. Il existe plusieurs méthodes pour y parvenir, l’une d’elles est l’analyse `UIDevice.CurrentDevice.SystemVersion`.



## <a name="related-links"></a>Liens associés

- [Éditeur de liens](~/ios/deploy-test/linker.md)
- [Externe - matrice de support iOS](http://iossupportmatrix.com/)
