---
title: Configuration de Xamarin Live Player Visual Studio
description: Ce document explique comment utiliser l’Xamarin Live Player pour apporter des modifications dynamiques à une application en cours d’exécution.
ms.prod: xamarin
ms.assetid: 5DDF9203-8826-4B04-93F5-B8D07EDE3873
author: conceptdev
ms.author: crdun
ms.date: 06/13/2019
ms.openlocfilehash: 94f1d36bf97aab7eabb57e6f2712c9850b390ab1
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290486"
---
# <a name="xamarin-live-player-visual-studio-configuration"></a>Configuration de Xamarin Live Player Visual Studio

![Fonctionnalité d’évaluation](~/media/shared/preview.png)

> [!WARNING]
> L’aperçu de la Xamarin Live Player s’est terminé. L’application n’est plus disponible. Les instructions ci-dessous sont fournies aux clients qui continuent d’utiliser la version préliminaire de Visual Studio 2017.

> [!TIP]
> Vous pouvez utiliser le [Générateur d’aperçu XAML](~/xamarin-forms/xaml/xaml-previewer/index.md) dans Visual Studio 2019 ou Visual Studio pour Mac pour afficher les conceptions d’écran au fur et à mesure de leur modification.

# <a name="visual-studio-2017tabwindows"></a>[Visual Studio 2017](#tab/windows)

## <a name="using-xamarin-live-player"></a>Utilisation de Xamarin Live Player

Vous devez déjà disposer de l’application Xamarin Live Player sur votre appareil. Il n’est plus disponible pour le téléchargement.

1. Ouvrez **Visual Studio 2017**.
2. Accédez à **outils > options...** , puis sélectionnez l’onglet **Xamarin > autre** .
3. **Xamarin Live Player d’activation**de la graduation :

    ![Cochez la case Activer la Xamarin Live Player dans la fenêtre Options.](install-images/vs2017-options.png)

4. Créez ou ouvrez un projet Xamarin (ou un [exemple](~/tools/live-player/samples.md)).
5. Choisissez **Live Player** dans la liste des appareils :

    ![La liste des appareils comprend une option Xamarin Live Player](install-images/devices-empty-windows.png)

    - Si vous avez déjà couplé un appareil, celui-ci sera disponible en option.
    - Dans le cas contraire, vous serez invité à coupler un appareil à la demande.

6. Appuyez sur le bouton **exécuter** ou sélectionnez l’une des options suivantes dans le menu **exécuter** ou cliquez avec le bouton droit :

    - **Démarrer sans débogage** : vous pouvez modifier l’application et voir les modifications apportées à l’appareil (l’application est redémarrée à mesure que des modifications sont effectuées et que le fichier est enregistré).
    - **Démarrer le débogage** : vous pouvez définir des points d’arrêt et inspecter des variables, mais le code ne peut pas être modifié.

    Vous pouvez également sélectionner **outils > Xamarin Live Player > vue en cours exécuter en temps réel**, ce qui vous permet de modifier l’application et de voir les modifications apportées sur l’appareil. La vue actuelle est affichée (à la place de l’écran principal de l’application).

7. Si un appareil est déjà couplé et que l’application Xamarin Live Player s’exécute sur l’appareil, le code s’exécute immédiatement.

    Si aucun appareil n’est couplé, un code QR s’affiche avec des instructions sur la façon de coupler un appareil :

    ![Associer une fenêtre d’appareil](install-images/manage-empty-windows.png)

    Si l’appareil ne peut pas être contacté pour le jumelage, une erreur peut s’afficher.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

## <a name="using-xamarin-live-player"></a>Utilisation de Xamarin Live Player

Vous devez déjà disposer de l’application Xamarin Live Player sur votre appareil. Il n’est plus disponible pour le téléchargement.

1. Ouvrez **Visual Studio pour Mac**.
2. Accédez à **Visual Studio > préférences...** , puis sélectionnez l’onglet **projets > Xamarin Live Player (version préliminaire)** .
3. **Xamarin Live Player d’activation**de la graduation :

    [![Cochez la case Activer la Xamarin Live Player dans la fenêtre Options.](install-images/vsmac-options-sml.png)](install-images/vsmac-options.png#lightbox)

4. Créez ou ouvrez un projet Xamarin (ou un [exemple](~/tools/live-player/samples.md)).
5. Choisissez **Live Player** dans la liste des appareils.

    ![La liste des appareils comprend une option Xamarin Live Player](install-images/devices.png)

    - Si vous avez déjà couplé un appareil, celui-ci sera disponible en option.
    - Dans le cas contraire, vous serez invité à coupler un appareil à la demande.
    - Choisissez **Xamarin Live Player appareils...** pour gérer les appareils que vous souhaitez utiliser avec Xamarin Live Player.

6. Appuyez sur le bouton **exécuter** ou sélectionnez l’une des options suivantes dans le menu **exécuter** ou cliquez avec le bouton droit :

    ![Options du menu exécuter](install-images/run-menu.png)

    - **Démarrer sans débogage** : vous pouvez modifier l’application et voir les modifications apportées à l’appareil (l’application est redémarrée à mesure que des modifications sont effectuées et que le fichier est enregistré).
    - **Démarrer le débogage** : vous pouvez définir des points d’arrêt et inspecter des variables, mais le code ne peut pas être modifié.
    - **Affichage en temps réel** : vous pouvez modifier l’application et voir les modifications apportées sur l’appareil. La vue actuelle est affichée (à la place de l’écran principal de l’application).

7. Si un appareil est déjà couplé et que l’application Xamarin Live Player s’exécute sur l’appareil, le code s’exécute immédiatement.

    Si aucun appareil n’est couplé, un code QR s’affiche avec des instructions sur la façon de coupler un appareil :

    ![Associer une fenêtre d’appareil](install-images/manage-empty.png)

    Si l’appareil ne peut pas être contacté pour le jumelage, une erreur s’affiche :

    ![Impossible de se connecter au message d’erreur de l’appareil](install-images/error-cannot-connect.png)

-----

Si vous rencontrez des problèmes ou si vous ne pouvez pas vous connecter, consultez [limitations et résolution des](~/tools/live-player/troubleshooting.md)problèmes.

## <a name="related-links"></a>Liens associés

- [Dépannage](~/tools/live-player/troubleshooting.md)
