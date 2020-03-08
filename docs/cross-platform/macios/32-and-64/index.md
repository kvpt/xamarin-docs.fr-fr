---
title: Considérations sur les plateformes 32 bits et 64 bits
description: Ce document décrit différentes considérations à prendre en compte lorsque vous ciblez des architectures 32 bits et 64 bits pour une application Xamarin. iOS ou Xamarin. Mac.
ms.prod: xamarin
ms.assetid: F7126340-04B2-4A10-B14D-394E23527C1A
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: 5ba451de857444bc5b12b750ae479b62abdb75a3
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78910774"
---
# <a name="3264-bit-platform-considerations"></a>Considérations sur les plateformes 32 bits et 64 bits

Tandis que iOS et macOS ont pris en charge à la fois les applications 32 et 64 bits, Apple a progressivement supprimé la prise en charge de 32 bits.

Depuis iOS 11, les applications 32 bits ne sont plus lancées, et [toutes les soumissions à l’App Store doivent prendre en charge 64 bits](https://developer.apple.com/news/?id=06282017b).

Depuis le 2018 janvier, [les nouvelles applications soumises au Mac App Store doivent prendre en charge 64 bits](https://developer.apple.com/news/?id=06282017a), et les applications existantes doivent être mises à jour avant le 2018 juin.

Le API classique de Xamarin (`XamMac.dll` et `monotouch.dll`) prenait uniquement en charge les applications 32 bits. Toutefois, les nouvelles applications Xamarin. iOS et Xamarin. Mac utilisent le [API unifiée](~/cross-platform/macios/unified/index.md) (`Xamarin.iOS` et `Xamarin.Mac`) par défaut, et peuvent donc cibler à la fois 32 et 64 bits, si nécessaire.

## <a name="ios"></a>iOS

<a name="enable-64" />

### <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>Activation des versions 64 bits des applications Xamarin. iOS

> [!WARNING]
> Cette section est incluse pour des raisons historiques et pour vous aider à déplacer des projets Xamarin. iOS plus anciens vers le API unifiée et prendre en charge 64 bits. Tous les nouveaux projets Xamarin. iOS utilisent le API unifiée et ciblent 64 bits par défaut.

Pour les applications mobiles Xamarin. iOS qui ont été converties en API unifiée, les développeurs doivent mettre à jour manuellement les paramètres de build pour cibler 64 bits :

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

1. Dans la **panneau solutions**, double-cliquez sur le projet de l’application pour ouvrir la fenêtre **Options du projet** .
2. Sélectionnez **Build iOS**.
3. Pour le simulateur iPhone, dans la liste déroulante **architectures prises en charge** , sélectionnez **x86\_64** ou **i386 + x86\_64**:

   [![Définition des architectures prises en charge sur x86\_64 ou i386 + x86\_64](Images/Image01.png "Setting Supported architectures to x86\_64 or i386 + x86\_64")](Images/Image01-large.png#lightbox) 

4. Pour les appareils physiques, sélectionnez l’une des combinaisons **ARM64** disponibles :

   [![Définition des architectures prises en charge sur l’une des combinaisons ARM64](Images/Image02.png "Définition des architectures prises en charge sur l’une des combinaisons ARM64")](Images/Image02-large.png#lightbox)

5. Cliquez sur **OK**.
6. Optez pour une build propre.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le projet de l’application, puis sélectionnez **Propriétés**.
2. Sélectionnez **Build iOS**.
3. Pour le simulateur iPhone, définissez les **architectures prises en charge** sur **x86\_64** ou **i386 + x86\_64**: 

   [![Définition des architectures prises en charge pour x86_64 ou i386 + x86\_64](Images/VS02.png "Setting Supported architectures to x86_64 or i386 + x86\_64")](Images/VS02-large.png#lightbox)

4. Pour les appareils physiques, sélectionnez l’une des combinaisons **ARM64** disponibles :
    
   [![Définition des architectures prises en charge sur l’une des combinaisons ARM64](Images/VS01.png "Définition des architectures prises en charge sur l’une des combinaisons ARM64")](Images/VS01-large.png#lightbox)

5. Enregistrez vos modifications.
6. Optez pour une build propre.

-----

ARMv7s est pris en charge uniquement par le processeur A6 inclus dans l’iPhone 5 (ou version ultérieure). Le code ARMv7 est plus rapide et plus petit que le ARMv6, fonctionne uniquement avec l’iPhone 3GS et versions ultérieures, et est requis par Apple lors du ciblage de l’iPad ou d’une version iOS minimale de 5,0. ARMv6 fonctionne sur tous les appareils, mais n’est plus pris en charge par le compilateur fourni avec Xcode 4,5 et versions ultérieures. 

ARM64 est requis pour prendre en charge iOS 8 sur iPhone 6 ou d’autres périphériques 64 bits et sera requis par Apple lors de l’envoi de nouvelles applications ou de la mise à jour d’applications de sortie dans l’iTunes App Store.

Pour obtenir une vue d’ensemble complète des fonctionnalités des différents appareils iOS, consultez le document sur la [compatibilité des appareils](https://developer.apple.com/library/content/documentation/DeviceInformation/Reference/iOSDeviceCompatibility/DeviceCompatibilityMatrix/DeviceCompatibilityMatrix.html) Apple.

### <a name="64-bit-and-binary-size-increases"></a>64 bits et la taille binaire augmente

Pendant la transition d’Apple de 32 bits à 64 bits, les applications iOS doivent s’exécuter sur du matériel 32 bits et 64 bits. Pour cette raison, le API unifiée de Xamarin permet aux développeurs de cibler les deux.

Le ciblage des architectures 32 bits et 64 bits augmentera considérablement la taille d’une application. Toutefois, cela permettra à des appareils plus récents d’exécuter du code optimisé tout en prenant en charge les appareils plus anciens.

> [!IMPORTANT]
> Si vous recevez le message suivant lors de l’envoi d’une application iOS à l’App Store iTunes, _«Avertissement iTMS-9000 : prise en charge de 64 bits manquante. À partir du 1er février 2015, les nouvelles applications iOS téléchargées vers l’App Store doivent inclure la prise en charge de 64 bits et être générées avec le kit de développement logiciel (SDK) iOS 8, inclus dans Xcode 6 ou version ultérieure. Pour activer 64 bits dans votre projet, nous vous recommandons d’utiliser le paramètre de build Xcode par défaut « architectures standard » pour générer un seul fichier binaire avec le code 32 bits et 64 bits.»_ Vous devez faire basculer les architectures prises en charge vers l’une des combinaisons **ARM64** disponibles (comme indiqué ci-dessus), recompiler et renvoyer.

## <a name="mac"></a>Mac

> [!IMPORTANT]
> Depuis le 2018 janvier, toutes les nouvelles applications Mac soumises au Mac App Store doivent prendre en charge 64 bits. Les applications Mac App Store existantes et leurs mises à jour doivent prendre en charge 64 bits à partir du 2018 juin. Consultez [l’annonce d’Apple](https://developer.apple.com/news/?id=06282017a) et [un guide qui décrit comment mettre à jour vos applications Xamarin. Mac vers 64 bits](~/cross-platform/macios/32-and-64/mac-64-bit.md).

La plupart des ordinateurs Mac modernes prennent en charge les applications 32 bits et 64 bits.   MacOS 10,6 (Snow Leopard) était le dernier système d’exploitation à s’exécuter sur les systèmes 32 bits.   La plupart des Mac publiées depuis 2010 prennent en charge les deux systèmes.

Contrairement à iOS, un grand nombre des nouveaux frameworks introduits dans les versions récentes de macOS sont pris en charge uniquement en mode 64 bits (CloudKit, EventKit, GameController, LocalAuthentication, MediaLibrary, MultipeerConnectivity, NotificationCenter, GLKit, SpriteKit, social, et MapKit, entre autres).

Les API unifiée permettent aux développeurs de choisir le genre d’applications qu’ils souhaitent produire : 32 bits ou 64 bits.

les **applications 32 bits** s’exécutent sur des ordinateurs Mac 32 bits et 64 bits, disposent d’un espace d’adressage limité à 32 bits et requièrent que toutes les bibliothèques soient de 32 bits.

Vous utiliserez généralement ce mode si vous avez des dépendances 32 bits qui ne s’exécutent pas en mode 64 bits, si vous souhaitez un téléchargement plus petit, ou si le passage à 64 bits n’est pas un avantage en matière de performances.

Ce mode est limité car vous ne pouvez pas utiliser de nombreuses infrastructures disponibles dans macOS Mavericks et macOS Yosemite.

**les applications 64 bits** s’exécutent uniquement sur les appareils Mac 64 bits.

Pour Mac, il s’agit du mode de fonctionnement par défaut, car la plupart des Mac en cours d’utilisation prennent aujourd’hui en charge le mode 64 bits, et vous avez accès à l’ensemble complet des frameworks fournis par Apple.

### <a name="enabling-64-bit-builds-of-xamarinmac-apps"></a>Activation des versions 64 bits des applications Xamarin. Mac

Pour plus d’informations sur la création d’une application 64 bits à l’aide de Xamarin. Mac, consultez le guide [mise à jour des applications unifiées Xamarin. Mac à l’adresse 64 bits](~/cross-platform/macios/32-and-64/mac-64-bit.md) .

## <a name="related-links"></a>Liens connexes

- [Différences entre les API unifiée classiques et les différences](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
