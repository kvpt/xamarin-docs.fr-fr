---
title: Désinstaller Xamarin
description: Ce document explique comment désinstaller Xamarin de Visual Studio sur Windows.
ms.prod: xamarin
ms.assetid: b83a85ec-842a-444c-8f82-c2464eda099b
author: conceptdev
ms.author: crdun
ms.date: 01/22/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1eae29e8531c9d401630f9d113b01eb3c2510689
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86939163"
---
# <a name="uninstall-xamarin-from-visual-studio"></a>Désinstaller Xamarin de Visual Studio

Ce guide explique comment supprimer Xamarin de Visual Studio sur Windows.

<a name="uninstallvs2017"></a>

## <a name="visual-studio-2019-and-visual-studio-2017"></a>Visual Studio 2019 et Visual Studio 2017

Pour désinstaller Xamarin de Visual Studio 2019 et Visual Studio 2017, utilisez le programme d’installation :

1. Utilisez le **menu Démarrer** pour ouvrir **Visual Studio Installer**.

2. Appuyez sur le bouton **Modifier** pour l’instance que vous souhaitez modifier.

    [![Appuyez sur le bouton modifier](uninstalling-xamarin-images/vs2017-02-sml.png)](uninstalling-xamarin-images/vs2017-02.png#lightbox)

3. Sous l’onglet **Charges de travail**, décochez l’option ** Développement mobile en .NET** (dans la section **Mobile et jeux**).

    [![Décochez l’option relative à la charge de travail Développement mobile](uninstalling-xamarin-images/vs2017-03-sml.png)](uninstalling-xamarin-images/vs2017-03.png#lightbox)

4. Cliquez sur le bouton **Modifier** situé en bas à droite de la fenêtre.

5. Visual Studio Installer va supprimer les composants qui ont été désélectionnés (Visual Studio 2017 doit être fermé pour que Visual Studio Installer puisse apporter les modifications nécessaires).

    [![Appuyez sur le bouton Modifier](uninstalling-xamarin-images/vs2017-04-sml.png)](uninstalling-xamarin-images/vs2017-04.png#lightbox)

Les composants Xamarin individuels (tels que le profileur ou les classeurs) peuvent être désinstallés en basculant vers l’onglet **composants individuels** à l’étape 3 et en dévérifiant des composants spécifiques :

[![Désinstallez les composants individuels](uninstalling-xamarin-images/vs2017-components-sml.png)](uninstalling-xamarin-images/vs2017-components.png#lightbox)

Pour désinstaller entièrement Visual Studio 2017, choisissez **Désinstaller** dans le menu en regard du bouton **Lancer** (représenté par trois barres superposées).

[![Désinstallez complètement Visual Studio](uninstalling-xamarin-images/vs2017-uninstall-sml.png)](uninstalling-xamarin-images/vs2017-uninstall.png#lightbox)

> [!IMPORTANT]
> Si vous avez installé deux instances (ou plus) de Visual Studio côte à côte (SxS), par exemple une Version et une Préversion, la désinstallation d’une instance peut supprimer certaines fonctionnalités Xamarin de la ou des autres instances de Visual Studio, notamment :
>
> - Xamarin Profiler
> - Xamarin Workbooks/Inspector
> - Xamarin Remote iOS Simulator
> - SDK Apple Bonjour
>
> Sous certaines conditions, la désinstallation de l’une des instances côte à côte peut entraîner la suppression de ces fonctionnalités. Cela peut alors dégrader les performances de la plateforme Xamarin sur les instances restantes de Visual Studio.
>
>Pour résoudre ce problème, exécutez l’option **Réparer** dans Visual Studio Installer, qui réinstallera les composants manquants.

<a name="uninstallvs2015"></a>

## <a name="visual-studio-2015-and-earlier"></a>Visual Studio 2015 et versions antérieures

Pour désinstaller intégralement Visual Studio 2015, utilisez [la réponse du support sur visualstudio.com](https://visualstudio.microsoft.com/vs/support/vs2015/uninstall-visual-studio-2015/).

Sur les ordinateurs Windows, Xamarin peut être désinstallé via le **Panneau de configuration**. Accédez à **Programmes et fonctionnalités** ou à **Programmes > Désinstaller un programme**, comme illustré ci-dessous :

 [![Accédez à Programmes et fonctionnalités ou à Programmes &gt; Désinstaller un programme, comme illustré ici](uninstalling-xamarin-images/image3.png)](uninstalling-xamarin-images/image3.png#lightbox)

Dans le Panneau de configuration, désinstallez les éléments de votre choix présents ici :

- Xamarin
- Xamarin pour Windows
- Xamarin.Android
- Xamarin.iOS
- Xamarin pour Visual Studio

Dans l’Explorateur, supprimez tous les fichiers restants des dossiers d’extension Xamarin Visual Studio (toutes les versions, y compris Fichiers programme et Fichiers programme (x86)) :

```
C:\Program Files*\Microsoft Visual Studio 1*.0\Common7\IDE\Extensions\Xamarin
```

Supprimez le répertoire de cache du composant MEF de Visual Studio, qui devrait se trouver à l’emplacement suivant :

```
%LOCALAPPDATA%\Microsoft\VisualStudio\1*.0\ComponentModelCache
```

Vérifiez dans le répertoire **VirtualStore** pour voir si Windows a stocké des fichiers de superposition dans les répertoires **Extensions\Xamarin** ou **ComponentModelCache** :

```
%LOCALAPPDATA%\VirtualStore
```

Ouvrez l’Éditeur du Registre (regedit) et recherchez la clé suivante :

```
HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\SharedDlls
```

Recherchez et supprimez toutes les entrées qui correspondent à ce modèle :

```
C:\Program Files*\Microsoft Visual Studio 1*.0\Common7\IDE\Extensions\Xamarin
```

Recherchez cette clé :

```
HKEY_CURRENT_USER\Software\Microsoft\VisualStudio\1*.0\ExtensionManager\PendingDeletions
```

Supprimez les entrées qui semblent éventuellement liées à Xamarin. Par exemple, tout élément contenant les termes `mono` ou `xamarin`.

Ouvrez une invite de commandes cmd.exe administrateur, puis exécutez les commandes `devenv /setup` et `devenv /updateconfiguration` pour chaque version installée de Visual Studio. Par exemple, pour Visual Studio 2015 :

```cmd
"%ProgramFiles(x86)%\Microsoft Visual Studio 14.0\Common7\IDE\devenv.exe" /setup
"%ProgramFiles(x86)%\Microsoft Visual Studio 14.0\Common7\IDE\devenv.exe" /updateconfiguration
```
