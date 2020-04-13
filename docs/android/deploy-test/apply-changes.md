---
title: Appliquer les modifications
description: Comment commencer à utiliser Apply Changes dans les projets Xamarin.Android.
ms.assetid: 38950B0C-8880-448F-B12E-08D5BFE87D0D
author: JonDouglas
ms.author: jodou
ms.date: 03/09/2020
ms.openlocfilehash: 03bace97908a53ac6112cd2600b20d429fe2f521
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "80215347"
---
# <a name="apply-changes"></a>Appliquer les modifications

Appliquez les modifications vous permet de pousser les modifications de ressources à votre application en cours d’exécution sans redémarrer votre application. Cela vous aide à contrôler la quantité de votre application est redémarrée lorsque vous souhaitez déployer et tester de petites modifications progressives tout en préservant l’état actuel de votre appareil ou de votre émulateur.

Apply Changes utilise des capacités dans la [implémentation Android JVMTI](https://docs.oracle.com/javase/8/docs/platform/jvmti/jvmti.html#bci) qui est pris en charge sur les appareils ou les émulateurs fonctionnant sous Android 8.0 (niveau API 26) ou plus.

## <a name="requirements"></a>Spécifications

La liste suivante indique les exigences d’utilisation des modifications d’application :

- **Visual Studio** - Sur Windows, mise à jour de Visual Studio 2019 version 16.5 ou plus tard. Sur macOS, mise à jour sur Visual Studio 2019 pour mac version 8.5 ou plus tard.
- **Xamarin.Android** - Xamarin.Android 10.2 ou plus tard doit être installé avec Visual Studio (Xamarin.Android est automatiquement installé dans le cadre du développement mobile avec la charge de travail **.NET** sur Windows et installé dans le cadre du **studio visuel pour Mac Installer**).
- **Android SDK** - Android API 28 ou plus doit être installé via le gestionnaire Android SDK.
- **Appareil cible ou émulateur** - Votre appareil ou émulateur doit fonctionner Android 8.0 (niveau API 26) ou plus.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="get-started"></a>Prise en main

Pour commencer avec Apply Changes, vous devrez vous assurer qu’un appareil ou un émulateur fonctionne sous Android 8.0 (niveau API 26) ou plus. Ensuite, exécutez votre application Android avec ou sans débogage.

Vous pouvez ensuite interagir avec Apply Changes avec les approches suivantes :

1. **Icône de barre d’outils.** Vous pouvez cliquer sur l’icône de la barre d’outils Apply Changes pour appliquer des modifications à votre appareil cible ou à votre émulateur.

    [![Appliquer les modifications - Icône de barre d’outils](apply-changes-images/Apply-Changes-Toolbar.png)](apply-changes-images/Apply-Changes-Toolbar.png#lightbox)

2. **Raccourci clavier.** Vous pouvez utiliser le raccourci du clavier **Shift et Alt F5** pour appliquer des modifications à votre appareil cible ou à votre émulateur.
3. **Menu Debug.** Vous pouvez utiliser l’élément de menu **Debug > Apply Changes** pour appliquer des modifications à votre appareil cible ou à votre émulateur.

    [![Appliquer les modifications - Menu Debug](apply-changes-images/Apply-Changes-Debug-Menu.png)](apply-changes-images/Apply-Changes-Debug-Menu.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

## <a name="get-started"></a>Prise en main

Pour commencer avec Apply Changes, vous devrez vous assurer qu’un appareil ou un émulateur fonctionne sous Android 8.0 (niveau API 26) ou plus. Ensuite, exécutez votre application Android avec ou sans débogage.

Vous pouvez ensuite interagir avec Apply Changes avec les approches suivantes :

1. **Raccourci clavier.** Vous pouvez utiliser le raccourci clavier **R** pour appliquer des modifications à votre appareil cible ou à votre émulateur.
2. **Exécuter le menu.** Vous pouvez utiliser **l’élément de** menu Run > Apply Changes pour appliquer des modifications à votre appareil cible ou à votre émulateur.

    [![Appliquer les modifications - Menu Debug](apply-changes-images/Apply-Changes-Debug-Menu-Mac.png)](apply-changes-images/Apply-Changes-Debug-Menu-Mac.png#lightbox)

-----

## <a name="limitations"></a>Limites

Les modifications suivantes nécessitent un redémarrage de l’application :

- Modification du code C.
- Ajout ou suppression d’une ressource.
- Modification de l’AndroidManifest.xml.
- Changer les bibliothèques autochtones (.donc les fichiers).

## <a name="related-links"></a>Liens connexes

- [Appliquer les modifications](https://developer.android.com/studio/run#apply-changes)
