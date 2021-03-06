---
title: Extensions Visual Studio manquantes après l’installation
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 066d36a3-e553-48d6-8769-c972274d7641
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: 976d0882c5875c1d3e1c8f0ea1732de08df8e07f
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "86996173"
---
# <a name="missing-visual-studio-extensions-after-installation"></a>Extensions Visual Studio manquantes après l’installation

## <a name="error-message-this-project-is-incompatible-with-the-current-edition-of-visual-studio"></a>Message d’erreur : ce projet est incompatible avec l’édition actuelle de Visual Studio

Assurez-vous que Visual Studio 2017 (Community, Professional ou Enterprise) ou une version plus récente est installé.

Consultez également la [Configuration requise pour Windows](~/cross-platform/get-started/requirements.md#windows-requirements).

## <a name="possible-fix-1-change-the-installation-to-make-sure-the-visual-studio-extensions-are-installed"></a>Correctif possible 1 : modifier l’installation pour vous assurer que les extensions Visual Studio sont installées

Dans certains cas, le programme d’installation Xamarin peut désactiver automatiquement les options d’installation des extensions Visual Studio. S’il s’agit de la cause du problème, installez les extensions Visual Studio manquantes à l’aide de la commande de **modification** du programme d’installation. Par exemple, pour installer les extensions pour Visual Studio 2013 :

1. Ouvrez le panneau **de configuration programmes et fonctionnalités** Windows.

2. Cliquez avec le bouton droit sur l’entrée **Xamarin** , puis sélectionnez **modifier**.

3. Cliquez sur **suivant**, puis sur **modifier**.

4. Assurez-vous que l’option **Xamarin pour Visual Studio 2013** est définie sur installer :

    ![Option d’installation de Xamarin pour Visual Studio 2013](missing-vs-extensions-images/installer.png)

5. Suivez les autres étapes de l’Assistant installation.

## <a name="possible-fix-2-ask-visual-studio-to-set-up-the-extensions-again"></a>Possibilité de corriger 2 : demander à Visual Studio de réinstaller les extensions

1. Vérifiez si les extensions Xamarin ont été copiées dans le dossier Extensions de Visual Studio :

    `C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\IDE\Extensions\Xamarin\Xamarin\3.1.228.0`

    Si les extensions sont correctement installées (pour la version 3.1.228), il y aura 60 éléments dans le dossier :

    ![Liste du contenu du dossier Xamarin\3.1.228.0 dans l’Explorateur](missing-vs-extensions-images/folder.png)

2. Une fois que vous avez confirmé que ce dossier semble correct, demandez à Visual Studio de réessayer de configurer les extensions :

    `"C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\IDE\devenv.exe" /setup`

## <a name="possible-fix-3-try-a-fresh-reinstall-of-xamarin"></a>Correctif logiciel possible 3 : essayez une nouvelle réinstallation de Xamarin

1. Dans le panneau de configuration Windows, désinstallez l’un des éléments suivants :

    * Xamarin

    * Xamarin pour Windows

    * Xamarin.Android

    * Xamarin.iOS

    * Xamarin pour Visual Studio

2. Dans l’Explorateur, supprimez tous les fichiers restants dans les dossiers d’extension Xamarin Visual Studio (toutes les versions, y compris les fichiers **programme** et les **fichiers programme (x86)**) :

    `C:\Program Files*\Microsoft Visual Studio 1*.0\Common7\IDE\Extensions\Xamarin`

3. Archivez également le répertoire « VirtualStore » pour voir s’il peut y avoir des copies de « superposition » de l’un des répertoires d’extension :

    `%LOCALAPPDATA%\VirtualStore`

4. Ouvrez l’éditeur du Registre (regedit).

5. Recherchez cette clé :

    _HKEY \_ local \_ MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\SharedDlls_

6. Recherchez et supprimez toutes les entrées qui correspondent à ce modèle :

    _C:\Program Files \* \Microsoft Visual Studio 1 \* . 0 \ Common7\IDE\Extensions\Xamarin_

7. Recherchez cette clé :

    `HKEY\_CURRENT\_USER\Software\Microsoft\VisualStudio\1\*.0\ExtensionManager\PendingDeletions`

8. Supprimez les entrées qui semblent éventuellement liées à Xamarin. Par exemple, voici un qui était utilisé pour provoquer des problèmes dans les versions antérieures de Xamarin :

    _Mono. VisualStudio. Shell, 1.0_

9. Redémarrage.

10. Réinstallez la version stable actuelle de Xamarin à partir de [VisualStudio.com](https://visualstudio.com/xamarin).

## <a name="possible-fix-4-repair-visual-studio-installation"></a>Correctif 4 possible : réparation de l’installation de Visual Studio

1. Ouvrez le panneau **de configuration programmes et fonctionnalités** Windows.

2. Cliquez avec le bouton droit sur l’entrée de Microsoft Visual Studio appropriée, puis sélectionnez **modifier** .

3. Cliquez sur le bouton **réparer** dans la boîte de dialogue Visual Studio qui s’ouvre.
