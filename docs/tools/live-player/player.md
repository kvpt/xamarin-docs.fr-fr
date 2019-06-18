---
title: Application Xamarin Live Player
description: Ce document décrit le Xamarin Live Player application, qui peut être utilisée pour afficher un aperçu des modifications du code en direct sur l’appareil. Il aborde le programme d’installation, des exemples, des journaux, des paramètres, la gestion des appareils et bien plus encore.
ms.prod: xamarin
ms.assetid: A7EB73C1-38D7-46C5-9AF6-4C571C168BE7
author: lobrien
ms.author: laobri
ms.date: 06/13/2019
ms.openlocfilehash: fce0eeae4ef5776842ea1b45c36163118042dc49
ms.sourcegitcommit: 93b1e2255d59c8ca6674485938f26bd425740dd1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67157726"
---
# <a name="xamarin-live-player-app"></a>Application Xamarin Live Player

![Fonctionnalité d’évaluation](~/media/shared/preview.png)

> [!WARNING]
> La version préliminaire Xamarin Live Player est terminée. L’application n’est plus disponible. Les instructions ci-dessous sont fournies pour les clients de continuer à utiliser la version préliminaire avec Visual Studio 2017.

> [!TIP]
> Vous pouvez utiliser la [Générateur d’aperçu XAML](~/xamarin-forms/xaml/xaml-previewer/index.md) dans Visual Studio 2019 ou Visual Studio pour Mac pour afficher vos conceptions de l’écran lorsque vous les modifiez.

Au démarrage, l’application Xamarin Live Player ressemble à ceci :

![Capture d’écran application de lecteur Android en direct](player-images/app-android-sml.png)

Quand vous appuyez sur **paire à Visual Studio**, utilisez l’appareil photo pour scanner le code-barres affichant sur votre ordinateur :

![Capture d’écran du scanneur de codes-barres Android](player-images/scan-android-sml.png)

Si la connexion est établie, le code doit s’exécuter sur l’appareil presque immédiatement (telles que la [exemple de calculatrice](https://developer.xamarin.com/samples/mobile/LivePlayer/BasicCalculator)) :

![Exemple d’application Calculatrice en cours d’exécution sur l’appareil](player-images/basic-calculator-sml.png)

## <a name="options"></a>Options

Appuyez sur le bouton informations **(i)** au bas de l’application pour faire apparaître le **Options** menu :

[![Capture d’écran du menu options](player-images/options-sml.png)](player-images/options.png#lightbox)

### <a name="logs"></a>Journaux

Afficher les journaux pour diagnostiquer les problèmes.

### <a name="settings"></a>Paramètres

- Activer/désactiver l’affichage des erreurs de compilation et d’exécution.
- Informations de version.
- Envoyer des commentaires.

[![Capture d’écran des paramètres](player-images/settings-sml.png)](player-images/settings.png#lightbox)

## <a name="managing-devices"></a>La gestion des appareils

Pour vous connecter à un appareil pour la première fois, suivez les instructions de [exigences et le programme d’installation](~/tools/live-player/install.md). Vous pouvez associer plusieurs appareils et les gérer via l’IDE.

# <a name="visual-studio-2017tabwindows"></a>[Visual Studio 2017](#tab/windows)

Dans Visual Studio, choisissez **Outils > Xamarin Live Player > Gérer les appareils...**

![Gérer la fenêtre d’appareils](player-images/manage-tools-menu-vs.png)

Cette fenêtre vous permet d’effectuer les opérations suivantes :

- Associer un nouvel appareil en analysant le code
- Vous pouvez également associer un appareil en tapant le code affiché sur l’écran
- Supprimez la liste des appareils existants

Vous pouvez également accéder à cette fenêtre à partir de la liste des appareils.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Dans Visual Studio pour Mac, choisissez **Outils > Gérer les appareils (Xamarin Live Player)...**

![Gérer la fenêtre d’appareils](player-images/manage-tools-menu.png)

Cette fenêtre vous permet d’effectuer les opérations suivantes :

- Associer un nouvel appareil en analysant le code
- Vous pouvez également associer un appareil en tapant le code affiché sur l’écran
- Supprimez la liste des appareils existants

![Gérer la fenêtre d’appareils](player-images/manage.png)

Vous pouvez également accéder à cette fenêtre à partir de la liste des appareils :

![Choisissez les appareils Xamarin Live Player à partir de la liste des appareils](player-images/manage-device-menu.png)

-----

Si vous rencontrez des problèmes, consultez [résolution des problèmes et limitations](~/tools/live-player/troubleshooting.md).

## <a name="related-links"></a>Liens associés

- [Résolution des problèmes](~/tools/live-player/troubleshooting.md)

