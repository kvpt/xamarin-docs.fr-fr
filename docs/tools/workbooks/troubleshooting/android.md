---
title: Résolution des problèmes de classeurs de Xamarin sur Android
description: Ce document fournit des conseils de dépannage pour l’utilisation des classeurs de Xamarin sur Android. Il aborde la prise en charge de l’émulateur, les classeurs qui ne se chargement pas et les autres rubriques.
ms.prod: xamarin
ms.assetid: F1BD293B-4EB7-4C18-A699-718AB2844DFB
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: 08fa7f57f3fe44721bc00f0d59ed5df93300cf1e
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864035"
---
# <a name="troubleshooting-xamarin-workbooks-on-android"></a>Résolution des problèmes de classeurs de Xamarin sur Android

## <a name="emulator-support"></a>Prise en charge de l’émulateur

Pour exécuter un classeur Android, un émulateur Android doit être disponible pour utilisation. Appareils Android physiques ne sont pas pris en charge.

Nous vous recommandons d’émulateur de Google avec HAXM si votre ordinateur prend en charge.
Si vous devez disposer de Hyper-V est activé sur votre système, accédez à la place avec l’émulateur Android de Visual Studio.

Vous devez disposer d’un émulateur qui exécute Android 5.0 ou version ultérieure. Les émulateurs ARM ne sont pas pris en charge. Utilisez `x86` ou `x86_64` uniquement les appareils.

Veuillez lire [notre documentation sur la configuration des émulateurs Android][android-emu] si vous n’êtes pas familiarisé avec le processus.

> [!NOTE]
> Classeurs 1.1 ou antérieures seront essayez (et échouer !) pour utiliser des émulateurs ARM si elles sont disponibles. Pour contourner cet, émulateur le x86 de lancement de votre choix avant d’ouvrir ou de création d’un classeur Android. Classeurs préfère toujours se connecter à un émulateur en cours d’exécution, tant qu’il est compatible.

## <a name="workbooks-wont-load"></a>Ne chargement pas les classeurs

### <a name="workbook-window-spins-forever-never-loads-windows"></a>Fenêtre de classeur ne tourne indéfiniment, jamais charges (Windows)

Tout d’abord, vérifiez que votre émulateur entièrement-utilisation de l’accès réseau en testant un site Web dans le navigateur web de l’émulateur.

### <a name="visual-studio-android-emulator-cannot-connect-to-the-internet"></a>L’émulateur Android de Visual Studio ne peut pas se connecter à internet

Si votre émulateur n’a pas accès au réseau, vous devrez peut-être suivre ces étapes pour résoudre votre commutateur de réseau Hyper-V. Si vous basculez entre les réseaux Wi-Fi fréquemment, vous devrez peut-être répéter cette opération régulièrement :

1. **Assurez-vous que toutes les opérations réseau critiques sont terminées, comme cela peut déconnecter temporairement de Windows à partir d’internet.**
1. Fermez les émulateurs.
1. Ouvrez `Hyper-V Manager`.
1. Sous `Actions`, ouvrez `Virtual Switch Manager...`.
1. Supprimer tous les commutateurs virtuels.
1. Cliquez sur `OK`.
1. Lancez l’émulateur Android de Visual Studio. Vous devrez probablement recréer le commutateur réseau virtuel.
1. Test que navigateur de l’émulateur VS Android peut accéder à internet.

[android-emu]: https://developer.xamarin.com/guides/android/deployment,_testing,_and_metrics/debug-on-emulator/


## <a name="related-links"></a>Liens associés

- [Signalement des bogues](~/tools/workbooks/install.md#reporting-bugs)
