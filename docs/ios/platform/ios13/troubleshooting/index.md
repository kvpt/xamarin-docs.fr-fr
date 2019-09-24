---
title: Résolution des problèmes liés à Xamarin et iOS 13
description: Cette section contient des conseils de dépannage pour les fonctionnalités Xamarin relatives à iOS 13.
ms.prod: xamarin
ms.assetid: 00DE8C33-1407-45C0-A0C7-32AF1E490034
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/12/2019
ms.openlocfilehash: 9a12163b0300a8d523632ad38e1b66a8c44b7aaf
ms.sourcegitcommit: 09bc69d7119a04684c9e804c5cb113b8b1bb7dfc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71206315"
---
# <a name="troubleshooting-tips-for-ios-13-and-xamarinios"></a>Conseils de dépannage pour iOS 13 et Xamarin. iOS

## <a name="updating-to-xcode-11-stops-the-simulator-from-launching"></a>La mise à jour vers Xcode 11 arrête le lancement du simulateur

Après la mise à jour vers **Xcode 11 bêta 1** chaque fois qu’un simulateur est lancé, l’exception suivante est levée et le simulateur ne démarre pas. Cela se produit avec tous les simulateurs.

### <a name="exception"></a>Exception

`Foundation.ObjCException: NSInvalidArgumentException: -[SimDevice registerNotificationHandler:]: unrecognized selector sent to instance 0x7ffbf5d1e110`

### <a name="workaround"></a>Solution de contournement

Jusqu’à ce qu’il y ait un [correctif](https://github.com/xamarin/xamarin-macios/issues/6216), les étapes suivantes peuvent être suivies pour réinstaller l’ancien Framework de simulateur pour permettre aux développeurs de continuer à travailler :

> [!NOTE]
> Ces étapes supposent que vous avez deux applications Xcode :
>
> - **Xcode11-beta1. app** : version bêta qui ne fonctionne pas avec les simulateurs et les Visual Studio pour Mac.
> - **Xcode102. app** : version stable de Xcode 10. Vous pouvez également être appelé **Xcode. app**.
>
> Modifiez les exemples de ligne de commande ci-dessous en fonction de votre configuration.

1. Vérifiez que Xcode 11 est sélectionné par le biais de Xcode-Select :

   `sudo xcode-select -s /Applications/Xcode11-beta1.app/Contents/Developer/`

2. Exécutez, si nécessaire, les outils d’installation pour la première fois.

    `/Applications/Xcode11-beta1.app/Contents/Developer/usr/bin/xcodebuild -runFirstLaunch`

3. Supprimez l’infrastructure suivante :

    `sudo rm -Rf  /Library/Developer/PrivateFrameworks/CoreSimulator.framework/Versions/*`

4. Revenir à l’ancienne version de Xcode

   `sudo xcode-select -s /Applications/Xcode102.app/Contents/Developer/`

5. Exécutez à nouveau le premier outil de lancement de l' _ancienne_ version de Xcode que vous venez de sélectionner.

   `/Applications/Xcode102.app/Contents/Developer/usr/bin/xcodebuild -runFirstLaunch`

Après avoir effectué ces étapes, vous devriez pouvoir réutiliser le simulateur iOS.
