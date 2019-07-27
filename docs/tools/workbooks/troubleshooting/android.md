---
title: Résolution des problèmes Xamarin Workbooks sur Android
description: Ce document fournit des conseils de dépannage pour l’utilisation de Xamarin Workbooks sur Android. Il aborde la prise en charge de l’émulateur, les classeurs qui ne sont pas chargés et d’autres rubriques.
ms.prod: xamarin
ms.assetid: F1BD293B-4EB7-4C18-A699-718AB2844DFB
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: 0d04b42a8d9f230c48bb09059296eb3740336dc6
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511835"
---
# <a name="troubleshooting-xamarin-workbooks-on-android"></a>Résolution des problèmes Xamarin Workbooks sur Android

## <a name="emulator-support"></a>Prise en charge des émulateurs

Pour exécuter un classeur Android, un émulateur Android doit être disponible. Les appareils Android physiques ne sont pas pris en charge.

Nous vous recommandons d’utiliser l’émulateur de Google avec HAXM si votre ordinateur le prend en charge.
Si Hyper-V doit être activé sur votre système, utilisez plutôt le Émulateur Android Visual Studio.

Vous devez disposer d’un émulateur qui exécute Android 5,0 ou une version ultérieure. Les émulateurs ARM ne sont pas pris en charge. Utiliser `x86` des `x86_64` appareils ou uniquement.

Veuillez lire [notre documentation sur la configuration des émulateurs Android][android-emu] si vous n’êtes pas familiarisé avec le processus.

> [!NOTE]
> Les classeurs 1,1 et antérieurs essaient (et échouent) d’utiliser les émulateurs ARM s’ils sont disponibles. Pour contourner ce contournement, lancez l’émulateur x86 de votre choix avant d’ouvrir ou de créer un classeur Android. Les classeurs préfèrent toujours se connecter à un émulateur en cours d’exécution, à condition qu’il soit compatible.

## <a name="workbooks-wont-load"></a>Les classeurs ne sont pas chargés

### <a name="workbook-window-spins-forever-never-loads-windows"></a>La fenêtre de classeur tourne indéfiniment, ne se charge jamais (Windows)

Tout d’abord, vérifiez que votre émulateur dispose d’un accès réseau entièrement opérationnel en testant n’importe quel site Web dans le navigateur Web de l’émulateur.

### <a name="visual-studio-android-emulator-cannot-connect-to-the-internet"></a>Émulateur Android Visual Studio ne peut pas se connecter à Internet

Si votre émulateur ne dispose pas d’un accès réseau, vous devrez peut-être suivre les étapes ci-dessous pour corriger votre commutateur réseau Hyper-V. Si vous basculez entre les réseaux Wi-Fi fréquemment, vous devrez peut-être répéter cette opération régulièrement:

1. **Assurez-vous que toutes les opérations de réseau critiques sont terminées, car cela peut temporairement déconnecter Windows d’Internet.**
1. Fermez les émulateurs.
1. Ouvrez `Hyper-V Manager`.
1. Sous `Actions`, ouvrez `Virtual Switch Manager...`.
1. Supprimez tous les commutateurs virtuels.
1. Cliquez sur `OK`.
1. Lancez VS Émulateur Android. Vous serez probablement invité à recréer le commutateur réseau virtuel.
1. Testez que le navigateur de Visual Émulateur Android peut accéder à Internet.

[android-emu]: ~/android/deploy-test/debugging/debug-on-emulator.md

## <a name="related-links"></a>Liens associés

- [Signalement des bogues](~/tools/workbooks/install.md#reporting-bugs)
