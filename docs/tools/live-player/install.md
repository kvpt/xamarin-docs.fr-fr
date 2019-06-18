---
title: Configuration de Xamarin Live Player Visual Studio
description: Ce document décrit l’utilisation de Xamarin Live Player pour apporter des modifications en direct à une application en cours d’exécution.
ms.prod: xamarin
ms.assetid: 5DDF9203-8826-4B04-93F5-B8D07EDE3873
author: lobrien
ms.author: laobri
ms.date: 06/13/2019
ms.openlocfilehash: a29a637526c2829b44ae89d505dac37a648dee77
ms.sourcegitcommit: 93b1e2255d59c8ca6674485938f26bd425740dd1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67157741"
---
# <a name="xamarin-live-player-visual-studio-configuration"></a>Configuration de Xamarin Live Player Visual Studio

![Fonctionnalité d’évaluation](~/media/shared/preview.png)

> [!WARNING]
> La version préliminaire Xamarin Live Player est terminée. L’application n’est plus disponible. Les instructions ci-dessous sont fournies pour les clients de continuer à utiliser la version préliminaire avec Visual Studio 2017.

> [!TIP]
> Vous pouvez utiliser la [Générateur d’aperçu XAML](~/xamarin-forms/xaml/xaml-previewer/index.md) dans Visual Studio 2019 ou Visual Studio pour Mac pour afficher vos conceptions de l’écran lorsque vous les modifiez.

# <a name="visual-studio-2017tabwindows"></a>[Visual Studio 2017](#tab/windows)

## <a name="using-xamarin-live-player"></a>À l’aide de Xamarin Live Player

Vous devez déjà disposer l’application Xamarin Live Player sur votre appareil. Il n’est plus disponible en téléchargement.

1. Ouvrez **Visual Studio 2017**.
2. Accédez à **Outils > Options...**  et sélectionnez le **Xamarin > autres** onglet.
3. Graduation **activer Xamarin Live Player**:

    ![Cochez la case Activer Xamarin Live Player dans la fenêtre Options](install-images/vs2017-options.png)

4. Créez ou ouvrez un projet Xamarin (ou un [exemple](~/tools/live-player/samples.md)).
5. Choisissez **Live Player** dans la liste des appareils :

    ![Liste des appareils inclut une option de Xamarin Live Player](install-images/devices-empty-windows.png)

    - Si vous avez déjà associé un appareil, il sera disponible en tant qu’option.
    - Sinon, vous allez être invité à coupler un appareil si nécessaire.

6. Appuyez sur la **exécuter** bouton, ou sélectionnez une de ces options à partir de la **exécuter** ou menu contextuel :

    - **Démarrer sans débogage** – vous pouvez modifier l’application et voir les modifications se produisent sur l’appareil (application redémarrage que des modifications sont apportées et le fichier enregistré).
    - **Démarrer le débogage** : vous pouvez définir des points d’arrêt et inspecter les variables, mais code ne peut pas être modifié.

    Vous pouvez également sélectionner **Outils > Xamarin Live Player > Live affichage de l’exécution**, qui vous permet de modifier l’application et voir les modifications se produisent sur l’appareil. La vue actuelle s’affiche (au lieu de l’écran principal de l’application).

7. Si un appareil est déjà associé et l’application Xamarin Live Player est en cours d’exécution sur l’appareil, il se peut que le code s’exécute immédiatement !

    Si aucun périphérique n’est associée, un code QR s’affiche avec des instructions sur la façon d’associer un appareil :

    ![Une fenêtre de dispositif de paire](install-images/manage-empty-windows.png)

    Si l’appareil ne peut pas être contacté pour l’appairage, une erreur peut apparaître.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

## <a name="using-xamarin-live-player"></a>À l’aide de Xamarin Live Player

Vous devez déjà disposer l’application Xamarin Live Player sur votre appareil. Il n’est plus disponible en téléchargement.

1. Ouvrez **Visual Studio pour Mac**.
2. Accédez à **Visual Studio > Préférences...**  et sélectionnez le **projets > Xamarin Live Player (version préliminaire)** onglet.
3. Graduation **activer Xamarin Live Player**:

    [![Cochez la case Activer Xamarin Live Player dans la fenêtre Options](install-images/vsmac-options-sml.png)](install-images/vsmac-options.png#lightbox)

4. Créez ou ouvrez un projet Xamarin (ou un [exemple](~/tools/live-player/samples.md)).
5. Choisissez **Live Player** dans la liste des appareils.

    ![Liste des appareils inclut une option de Xamarin Live Player](install-images/devices.png)

    - Si vous avez déjà associé un appareil, il sera disponible en tant qu’option.
    - Sinon, vous allez être invité à coupler un appareil si nécessaire.
    - Choisissez **appareils Xamarin Live Player...**  pour gérer les appareils que vous souhaitez utiliser avec Xamarin Live Player.

6. Appuyez sur la **exécuter** bouton, ou sélectionnez une de ces options à partir de la **exécuter** ou menu contextuel :

    ![Menu options d’exécution](install-images/run-menu.png)

    - **Démarrer sans débogage** – vous pouvez modifier l’application et voir les modifications se produisent sur l’appareil (application redémarrage que des modifications sont apportées et le fichier enregistré).
    - **Démarrer le débogage** : vous pouvez définir des points d’arrêt et inspecter les variables, mais code ne peut pas être modifié.
    - **Affichage de l’exécution de Live** – vous pouvez modifier l’application et voir les modifications se produisent sur l’appareil. La vue actuelle s’affiche (au lieu de l’écran principal de l’application).

7. Si un appareil est déjà associé et l’application Xamarin Live Player est en cours d’exécution sur l’appareil, il se peut que le code s’exécute immédiatement !

    Si aucun périphérique n’est associée, un code QR s’affiche avec des instructions sur la façon d’associer un appareil :

    ![Une fenêtre de dispositif de paire](install-images/manage-empty.png)

    Si l’appareil ne peut pas être contacté pour l’appairage, une erreur s’affiche :

    ![Impossible de se connecter à un message d’erreur de périphérique](install-images/error-cannot-connect.png)

-----

Si vous rencontrez des problèmes ou que vous ne pouvez pas vous connecter, consultez [résolution des problèmes et limitations](~/tools/live-player/troubleshooting.md).

## <a name="related-links"></a>Liens associés

- [Résolution des problèmes](~/tools/live-player/troubleshooting.md)
