---
title: Changements d’architecture dans iOS 11
description: Ce document décrit la désapprobation des applications 32 bits dans iOS 11. Il explique comment mettre à jour des applications pour cibler des architectures 64 bits.
ms.prod: xamarin
ms.assetid: 55F62F3F-8570-402B-B7D9-2875F76CB946
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/13/2016
ms.openlocfilehash: 15cd6139cc83639146e6044d2b791d94ee30f2d9
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70286346"
---
# <a name="architecture-changes-in-ios-11"></a>Changements d’architecture dans iOS 11

L’une des modifications les plus importantes que vous devez connaître avec iOS 11 est la désapprobation de la prise en charge de 32 bits pour les applications, comme indiqué dans le communiqué de presse [d’Apple](https://developer.apple.com/news/?id=06282017b) . Toutes les nouvelles applications et mises à jour des applications existantes doivent prendre en charge 64 bits. les applications 32 bits ne **seront pas lancées** dans iOS 11.

Pour mettre à jour votre application dans Visual Studio pour Mac, procédez comme suit :

1. Cliquez avec le bouton droit sur le nom du projet pour ouvrir **Options du projet**.
2. Accédez à l’onglet **Build iOS** .
3. Définissez la liste déroulante **architectures prises en charge** sur **x86_64** pour **Déboguer | iphonesimulator dans** et **Release | iphonesimulator dans**:

    ![Liste déroulante modifier les architectures de simulateur](architecture-changes-images/image1.png)

4. Pour les appareils iOS, modifiez la configuration pour **Déboguer | iPhone** et définissez la liste déroulante **architectures prises en charge** sur **ARM64**:

    ![Liste déroulante modifier les architectures des appareils](architecture-changes-images/image2.png)

5. Modifiez la configuration en **Release | iPhone** et définissez la liste déroulante **architectures prises en charge** sur **ARM64**.

Pour plus d’informations sur les architectures 32 bits et 64 bits, consultez le guide des [considérations relatives à la plateforme 32/64 bits](~/cross-platform/macios/32-and-64/index.md#ios) .

## <a name="related-links"></a>Liens associés

- [Nouveautés d’iOS 11 (Apple)](https://developer.apple.com/ios/)
- [Page produit App Store mise à jour (Apple)](https://developer.apple.com/app-store/product-page/)
- [Mise à jour de votre application pour iOS 11 (WWDC) (vidéo)](https://developer.apple.com/videos/play/wwdc2017/204/)
