---
title: Application Xamarin Live Player
description: Ce document décrit l’application Xamarin Live Player, qui peut être utilisée pour afficher un aperçu des modifications du code en direct sur l’appareil. Il traite de l’installation, des exemples, des journaux, des paramètres, de la gestion des appareils et bien plus encore.
ms.prod: xamarin
ms.assetid: A7EB73C1-38D7-46C5-9AF6-4C571C168BE7
author: davidortinau
ms.author: daortin
ms.date: 06/13/2019
ms.openlocfilehash: f1d3adfad3b9d2a64990ff11f81bf3a1a39825bb
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029656"
---
# <a name="xamarin-live-player-app"></a>Application Xamarin Live Player

![Fonctionnalité d’évaluation](~/media/shared/preview.png)

> [!WARNING]
> L’aperçu de la Xamarin Live Player s’est terminé. L’application n’est plus disponible. Les instructions ci-dessous sont fournies aux clients qui continuent d’utiliser la version préliminaire de Visual Studio 2017.

> [!TIP]
> Vous pouvez utiliser le [Générateur d’aperçu XAML](~/xamarin-forms/xaml/xaml-previewer/index.md) dans Visual Studio 2019 ou Visual Studio pour Mac pour afficher les conceptions d’écran au fur et à mesure de leur modification.

Au démarrage, l’application Xamarin Live Player se présente comme suit :

![Capture d’écran de l’application Live Player Android](player-images/app-android-sml.png)

Quand vous appuyez sur **paire vers Visual Studio**, utilisez l’appareil photo pour scanner le code-barres sur votre ordinateur :

![Capture d’écran du scanneur de codes-barres Android](player-images/scan-android-sml.png)

Si la connexion réussit, le code doit s’exécuter presque immédiatement sur l’appareil (par exemple, l' [exemple Calculator](https://github.com/xamarin/mobile-samples/tree/master/LivePlayer/BasicCalculator):

![Exemple d’application de calculatrice s’exécutant sur un appareil](player-images/basic-calculator-sml.png)

## <a name="options"></a>Options

Appuyez sur le bouton informations **(i)** en bas de l’application pour afficher le menu **options** :

[Capture d’écran![du menu options](player-images/options-sml.png)](player-images/options.png#lightbox)

### <a name="logs"></a>Journaux

Affichez les journaux pour diagnostiquer les problèmes.

### <a name="settings"></a>Paramètres

- Afficher/masquer les erreurs de compilation et d’exécution.
- Informations sur la version.
- Envoyer des commentaires.

[Capture d’écran![des paramètres](player-images/settings-sml.png)](player-images/settings.png#lightbox)

## <a name="managing-devices"></a>Gestion des appareils

Pour connecter un appareil pour la première fois, suivez les instructions de la [& configuration requise](~/tools/live-player/install.md). Vous pouvez associer plusieurs appareils et les gérer par le biais de l’IDE.

# <a name="visual-studio-2017tabwindows"></a>[Visual Studio 2017](#tab/windows)

Dans Visual Studio, choisissez **outils > Xamarin Live Player > gérer les appareils...**

![Fenêtre gérer les appareils](player-images/manage-tools-menu-vs.png)

Cette fenêtre vous permet d’effectuer les opérations suivantes :

- Coupler un nouvel appareil en analysant le code
- Vous pouvez également coupler un appareil en tapant le code affiché à l’écran
- Supprimer les appareils existants de la liste

Vous pouvez également accéder à cette fenêtre à partir de la liste des appareils.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Dans Visual Studio pour Mac, choisissez **outils > (Xamarin Live Player) gérer les appareils...**

![Fenêtre gérer les appareils](player-images/manage-tools-menu.png)

Cette fenêtre vous permet d’effectuer les opérations suivantes :

- Coupler un nouvel appareil en analysant le code
- Vous pouvez également coupler un appareil en tapant le code affiché à l’écran
- Supprimer les appareils existants de la liste

![Fenêtre gérer les appareils](player-images/manage.png)

Vous pouvez également accéder à cette fenêtre à partir de la liste des appareils :

![Choisir des appareils Xamarin Live Player dans la liste des appareils](player-images/manage-device-menu.png)

-----

Si vous rencontrez des problèmes [, consultez Limitations et résolution des](~/tools/live-player/troubleshooting.md)problèmes.

## <a name="related-links"></a>Liens associés

- [Résolution des problèmes](~/tools/live-player/troubleshooting.md)
