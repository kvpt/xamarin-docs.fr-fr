---
title: Xamarin et la résolution des problèmes iOS 13
description: Cette section contient des conseils de dépannage pour la fonctionnalité de Xamarin iOS 13.
ms.prod: xamarin
ms.assetid: 00DE8C33-1407-45C0-A0C7-32AF1E490034
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/12/2019
ms.openlocfilehash: 99fd7e41e1521c6a9254d286bf989281658ccf24
ms.sourcegitcommit: 85c45dc28ab3625321c271804768d8e4fce62faf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67039786"
---
# <a name="troubleshooting-tips-for-ios-13-and-xamarinios"></a>Conseils de dépannage pour iOS 13 et Xamarin.iOS

![Fonctionnalité d’évaluation](~/media/shared/preview.png)

## <a name="updating-to-xcode-11-stops-the-simulator-from-launching"></a>La mise à jour à Xcode 11 arrête le simulateur de lancement

Après la mise à jour à **Xcode 11 version bêta 1** chaque fois qu’un simulateur est lancé l’exception suivante est levée, et le simulateur ne démarre pas. Cela se produit avec tous les simulateurs.

### <a name="exception"></a>Exception

`Foundation.ObjCException: NSInvalidArgumentException: -[SimDevice registerNotificationHandler:]: unrecognized selector sent to instance 0x7ffbf5d1e110`

### <a name="workaround"></a>Solution de contournement

Jusqu'à ce qu’un [corriger](https://github.com/xamarin/xamarin-macios/issues/6216), les étapes suivantes peuvent être suivies pour réinstaller l’ancienne infrastructure de simulateur pour permettre aux développeurs de continuer à fonctionner :

> [!NOTE]
> Ces étapes supposent que vous disposez de deux applications de Xcode :
> - **Xcode11-beta1.app** – la version bêta, ce qui ne fonctionne pas avec des simulateurs et Visual Studio pour Mac.
> - **Xcode102.app** – une version stable de Xcode 10. Vôtre peut également être appelé **Xcode.app**.
>
> Modifier les exemples de ligne de commande ci-dessous en fonction de votre configuration.

1. Assurez-vous d’avoir 11 Xcode sélectionnées via xcode-select :

   `sudo xcode-select -s /Applications/Xcode11-beta1.app/Contents/Developer/`

2. L’exécution, si nécessaire, le programme d’installation des outils pour la première fois.

    `/Applications/Xcode11-beta1.app/Contents/Developer/usr/bin/xcodebuild -runFirstLaunch`

3. Supprimer le framework suivant :

    `sudo rm -Rf  /Library/Developer/PrivateFrameworks/CoreSimulator.framework/Versions/*`

4. Revenez à l’ancienne version de Xcode

   `sudo xcode-select -s /Applications/Xcode102.app/Contents/Developer/`

5. Exécutez de nouveau le premier outil de lancement pour les _ancien_ version Xcode que vous venez de sélectionner

   `/Applications/Xcode102.app/Contents/Developer/usr/bin/xcodebuild -runFirstLaunch`

Après avoir suivi ces étapes, vous pourrez travailler à nouveau avec le simulateur iOS.