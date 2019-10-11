---
title: Écran de démarrage Xamarin. Forms
description: Cet article explique comment créer un écran de démarrage pour une application Xamarin. Forms.
ms.prod: xamarin
ms.assetId: 338C8F60-90F2-4B3D-BB47-7F846AE8DC6C
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 10/1/2019
ms.openlocfilehash: 2624c3f35a9cfac122a0b36ea8c1684d30f57824
ms.sourcegitcommit: 5110d1279809a2af58d3d66cd14c78113bb51436
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035754"
---
# <a name="xamarinforms-splash-screen"></a>Écran de démarrage Xamarin. Forms

Les applications ont souvent un délai de démarrage pendant que l’application termine son processus d’initialisation. Les développeurs peuvent souhaiter offrir une expérience personnalisée, généralement appelée écran de démarrage, pendant le démarrage de l’application. Cet article explique comment créer des écrans de démarrage pour les applications Xamarin. Forms.

Xamarin. Forms est initialisé sur chaque plateforme une fois la séquence de démarrage Native terminée. Xamarin. Forms est initialisé :

- Dans la méthode `OnCreate` de la classe `MainActivity` sur Android.
- Dans la méthode `FinishedLaunching` de la classe `AppDelegate` sur iOS.
- Dans la méthode `OnLaunched` de la classe `App` sur UWP.

L’écran de démarrage doit être affiché dès que possible lorsque l’application est lancée, mais Xamarin. Forms n’est pas initialisé jusqu’à la fin de la séquence de démarrage, ce qui signifie que l’écran de démarrage doit être implémenté en dehors de Xamarin. Forms sur chaque plateforme. Les sections suivantes expliquent comment créer des écrans de démarrage sur chaque plateforme.

## <a name="xamarinforms-android-splash-screen"></a>Écran de démarrage Android Xamarin. Forms

La création d’un écran de démarrage sur Android requiert la création d’une @no__t de démarrage-0 comme `MainLauncher` avec un thème spécial. Dès que le @no__t de démarrage-0 est démarré, il lance le @no__t principal-1 avec le thème d’application normal.

Pour plus d’informations sur les écrans de démarrage sur Xamarin. Android, consultez l' [écran de démarrage Xamarin. Android](~/android/user-interface/splash-screen.md).

## <a name="xamarinforms-ios-splash-screen"></a>Écran de démarrage Xamarin. Forms iOS

Un écran de démarrage sur iOS est désigné sous le terme d’écran de lancement. La création d’un écran de lancement sur iOS requiert la création d’une table de montage séquentiel qui définit l’interface utilisateur de l’écran de lancement, puis la définition de la table de montage séquentiel comme écran de lancement dans le fichier **info. plist**.

Pour plus d’informations sur les écrans de lancement sur Xamarin. iOS, consultez [écran de lancement de Xamarin. iOS](~/ios/app-fundamentals/images-icons/launch-screens.md).

## <a name="xamarinforms-uwp-splash-screen"></a>Écran de démarrage UWP Xamarin. Forms

Sur UWP, **Package. appxmanifest** contient un onglet **ressources visuelles** avec un sous-menu d' **écran de démarrage** . Les graphiques de l’écran de démarrage peuvent être spécifiés dans ce menu :

[écran de démarrage ![Setting sur UWP](splashscreen-images/uwp-splashscreen-cropped.png)](splashscreen-images/uwp-splashscreen.png#lightbox)

## <a name="related-links"></a>Liens connexes

- [Écran de démarrage Xamarin. Android](~/android/user-interface/splash-screen.md)
- [Écran de lancement de Xamarin. iOS](~/ios/app-fundamentals/images-icons/launch-screens.md)
