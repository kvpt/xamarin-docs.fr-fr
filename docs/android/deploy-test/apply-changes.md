---
title: Appliquer les modifications
description: Comment commencer à utiliser Apply changes dans les projets Xamarin. Android.
ms.assetid: 38950B0C-8880-448F-B12E-08D5BFE87D0D
author: JonDouglas
ms.author: jodou
ms.date: 03/09/2020
ms.openlocfilehash: 03bace97908a53ac6112cd2600b20d429fe2f521
ms.sourcegitcommit: ec112800a76089ab1db66fe24b8bbcc510e067b4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80215347"
---
# <a name="apply-changes"></a>Appliquer les modifications

Appliquer les modifications vous permet de transmettre les modifications de ressources à votre application en cours d’exécution sans redémarrer votre application. Cela vous permet de contrôler la quantité de votre application redémarrée lorsque vous souhaitez déployer et tester de petites modifications incrémentielles tout en préservant l’état actuel de votre appareil ou de votre émulateur.

Appliquer les modifications utilise les fonctionnalités de l' [implémentation Android JVMTI](https://docs.oracle.com/javase/8/docs/platform/jvmti/jvmti.html#bci) qui est prise en charge sur les appareils ou les émulateurs exécutant Android 8,0 (niveau d’API 26) ou ultérieur.

## <a name="requirements"></a>Spécifications

La liste suivante indique la configuration requise pour l’utilisation de Apply changes :

- **Visual Studio** -sur Windows, mettez à jour vers visual studio 2019 version 16,5 ou ultérieure. Sur macOS, mettez à jour vers Visual Studio 2019 pour Mac version 8,5 ou ultérieure.
- **Xamarin. Android** -Xamarin. Android 10,2 ou version ultérieure doit être installé avec Visual Studio (Xamarin. Android est installé automatiquement dans le cadre du **développement mobile avec** la charge de travail .net sur Windows et installé dans le cadre du **programme d’installation de Visual Studio pour Mac**).
- **Android SDK** -API Android 28 ou version ultérieure doit être installé via le gestionnaire de Android SDK.
- **Appareil cible ou émulateur** : votre appareil ou émulateur doit exécuter Android 8,0 (niveau d’API 26) ou supérieur.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="get-started"></a>Bien démarrer

Pour commencer à appliquer les modifications, vous devez vous assurer qu’un appareil ou un émulateur exécute Android 8,0 (niveau d’API 26) ou supérieur. Exécutez ensuite votre application Android avec ou sans débogage.

Vous pouvez ensuite interagir avec Apply changes avec les approches suivantes :

1. **Icône de barre d’outils.** Vous pouvez cliquer sur l’icône de la barre d’outils appliquer les modifications pour appliquer les modifications à votre appareil ou émulateur cible.

    [![appliquer les modifications-icône de la barre d’outils](apply-changes-images/Apply-Changes-Toolbar.png)](apply-changes-images/Apply-Changes-Toolbar.png#lightbox)

2. **Raccourci clavier.** Vous pouvez utiliser le raccourci clavier **Maj + Alt + F5** pour appliquer les modifications à votre appareil ou émulateur cible.
3. **Menu Déboguer.** Vous pouvez utiliser l’élément de menu **Déboguer > appliquer les modifications** pour appliquer les modifications à votre appareil ou émulateur cible.

    [![appliquer les modifications-menu Déboguer](apply-changes-images/Apply-Changes-Debug-Menu.png)](apply-changes-images/Apply-Changes-Debug-Menu.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

## <a name="get-started"></a>Bien démarrer

Pour commencer à appliquer les modifications, vous devez vous assurer qu’un appareil ou un émulateur exécute Android 8,0 (niveau d’API 26) ou supérieur. Exécutez ensuite votre application Android avec ou sans débogage.

Vous pouvez ensuite interagir avec Apply changes avec les approches suivantes :

1. **Raccourci clavier.** Vous pouvez utiliser le raccourci clavier **⌥ + ⇧ + R** pour appliquer les modifications à votre appareil ou émulateur cible.
2. **Menu exécuter.** Vous pouvez utiliser l’élément de menu **exécuter > appliquer les modifications** pour appliquer les modifications à votre appareil ou émulateur cible.

    [![appliquer les modifications-menu Déboguer](apply-changes-images/Apply-Changes-Debug-Menu-Mac.png)](apply-changes-images/Apply-Changes-Debug-Menu-Mac.png#lightbox)

-----

## <a name="limitations"></a>Limites

Les modifications suivantes requièrent un redémarrage de l’application :

- Modification C# du code.
- Ajout ou suppression d’une ressource.
- Modification de fichier AndroidManifest. Xml.
- Modification des bibliothèques natives (fichiers. so).

## <a name="related-links"></a>Liens connexes

- [Appliquer les modifications](https://developer.android.com/studio/run#apply-changes)
